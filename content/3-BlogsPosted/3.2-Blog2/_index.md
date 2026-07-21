---
title: "Building Python Applications with SQLAlchemy and Amazon Aurora DSQL"
date: 2026-06-08
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---


When a Python application needs a scalable database, SQLAlchemy combined with Amazon Aurora DSQL is a compelling option. SQLAlchemy provides a familiar ORM for defining models, constructing queries, and manipulating data. Aurora DSQL is a distributed, serverless, PostgreSQL-compatible database that automatically scales with traffic and uses AWS IAM authentication instead of long-lived database passwords.

![AWS article about SQLAlchemy and Amazon Aurora DSQL](/images/3-BlogPosted/blog.jpg)

*Figure 1: Technical article about integrating SQLAlchemy with Amazon Aurora DSQL.*

## Solution overview

The original article demonstrates these concepts with a veterinary clinic CLI application. It uses SQLAlchemy 2.x to manage models such as `owner`, `pet`, `veterinarian`, and `specialty`, and performs CRUD operations and eager loading.

The main connection flow is:

```text
Python CLI Application
        │ SQLAlchemy 2.x ORM
        ▼
Aurora DSQL Python Connector + psycopg3
        │ IAM authentication
        ▼
Amazon Aurora DSQL
```

The Aurora DSQL Python Connector generates and refreshes short-lived IAM tokens, so the application does not need to store a static database password in source code or configuration files.

## Three important adaptations

### 1. Use UUID primary keys

Aurora DSQL is a distributed system. UUID generation does not require nodes to coordinate around a shared sequential counter, allowing inserts to scale more effectively and avoiding a centralized ID-generation bottleneck.

SQLAlchemy can ask Aurora DSQL to generate the UUID during insertion:

```python
id: Mapped[UUID] = mapped_column(
    Uuid,
    primary_key=True,
    server_default=text("gen_random_uuid()"),
)
```

Aurora DSQL also supports sequences and identity columns for specific cases, but UUIDs are the recommended fit for distributed workloads.

### 2. Manage relationships at the application layer

Aurora DSQL does not currently support foreign key constraints, so `ForeignKey()` cannot be used directly in column definitions. A reference is stored as a regular UUID column:

```python
owner_id: Mapped[UUID | None] = mapped_column(Uuid, nullable=True)
```

Without `ForeignKey()`, SQLAlchemy cannot automatically infer join conditions. Relationships must be declared explicitly with `relationship()`, `primaryjoin`, and `foreign()`:

```python
Owner.pets = relationship(
    "Pet",
    primaryjoin=Owner.id == foreign(Pet.owner_id),
)
```

The `foreign()` annotation tells SQLAlchemy which column is the referencing side. Referential integrity must therefore be enforced by the application instead of a database constraint.

### 3. Enable AUTOCOMMIT for the SQLAlchemy engine

Aurora DSQL does not support `SAVEPOINT`, while SQLAlchemy and psycopg may use it during connection initialization or transaction management. Configure the engine with `isolation_level="AUTOCOMMIT"` and create connector connections with `autocommit=True`:

```python
engine = create_engine(
    "postgresql+psycopg://",
    creator=lambda: dsql.connect(
        host=host,
        user=user,
        autocommit=True,
    ),
    isolation_level="AUTOCOMMIT",
    pool_pre_ping=True,
    pool_recycle=3300,
)
```

`pool_pre_ping` validates a connection before use, while `pool_recycle=3300` refreshes connections before Aurora DSQL's one-hour connection limit.

## AWS IAM authentication

Aurora DSQL uses time-limited IAM tokens instead of long-lived database passwords. An application needs `dsql:DbConnect` for a regular user or `dsql:DbConnectAdmin` for administrative tasks. Runtime policies should be restricted to the specific cluster ARN following the principle of least privilege.

The connector manages token generation and refresh, while SQLAlchemy continues to provide sessions, ORM queries, and connection pooling in the familiar PostgreSQL development model.

## CRUD operations and eager loading

After the models and engine are configured, the application can perform create, read, update, and delete operations through SQLAlchemy sessions. Relationships defined at the application layer can still use `joinedload()` to eager-load associated records in one query.

The absence of foreign key constraints does not mean the models have no relationships. The relationships still exist in the ORM, but the application is responsible for validating referenced identifiers and implementing related update or deletion rules.

## Handling write conflicts

Aurora DSQL uses optimistic concurrency control. When transactions conflict over the same data, or a session has stale schema information, the database can return `SQLSTATE 40001`. In psycopg3 this is exposed as `SerializationFailure` and is safe to retry.

A production application should use exponential backoff with jitter:

```python
for attempt in range(max_retries + 1):
    try:
        return operation()
    except psycopg.errors.SerializationFailure:
        if attempt == max_retries:
            raise
        backoff = base_delay * (2 ** attempt)
        time.sleep(backoff + random.uniform(0, backoff))
```

Backoff increases the delay after each failure, while jitter adds randomness so concurrent requests do not retry at the same moment and immediately conflict again.

## Key takeaways

Three patterns are essential when using SQLAlchemy with Aurora DSQL:

1. Use UUID primary keys for distributed workloads.
2. Use application-level relationships instead of foreign key constraints.
3. Configure the engine in AUTOCOMMIT mode to avoid unsupported SAVEPOINT operations.

Production applications should also apply least-privilege IAM permissions, recycle connections before expiration, and retry transaction conflicts. These patterns are useful not only for SQLAlchemy but also for other Python ORMs working with Aurora DSQL.

---

**Source and credit:** Dipen Patel, [Building Python applications with SQLAlchemy and Aurora DSQL – AWS Database Blog](https://aws.amazon.com/blogs/database/building-python-applications-with-sqlalchemy-and-aurora-dsql/), June 8, 2026.
