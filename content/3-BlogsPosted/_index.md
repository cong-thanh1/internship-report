---
title: "Translated Blog"
date: 2026-07-09
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

This section summarizes three technical articles translated during the internship. The topics include deploying static web applications, developing applications with distributed databases, and building a secure access layer for generative AI services on AWS.

### [Hosting a .NET Blazor WebAssembly App on Amazon S3 and Amazon CloudFront](3.1-blog1/)

The article explains how to publish a Blazor WebAssembly application as static files, store them in a private Amazon S3 bucket, and distribute them through Amazon CloudFront. Its main topics include Origin Access Control (OAC), cache strategies for different file groups, client-side routing, and the deployment workflow using Terraform and the AWS CLI.

Source article: [.NET on AWS Blog](https://aws.amazon.com/vi/blogs/dotnet/host-a-net-blazor-webassembly-app-on-amazon-s3-and-amazon-cloudfront/)

### [Building Python Applications with SQLAlchemy and Amazon Aurora DSQL](3.2-blog2/)

Amazon Aurora DSQL is a distributed, serverless, PostgreSQL-compatible database that automatically scales with application traffic and authenticates through AWS IAM. When used with SQLAlchemy, developers can continue using the familiar Python ORM workflow while adapting several database design and connection patterns for distributed systems.

The article focuses on three essential adaptations:

- Using UUIDs as primary keys for scalable distributed inserts.
- Defining application-level relationships with `relationship()`, `primaryjoin`, and `foreign()` instead of foreign key constraints.
- Configuring the SQLAlchemy engine in AUTOCOMMIT mode to avoid unsupported SAVEPOINT operations.

The article also covers the Aurora DSQL Python Connector, IAM authentication, CRUD operations, eager loading, connection lifecycle management, and retry mechanisms using exponential backoff with jitter to handle optimistic concurrency conflicts.

Source article: [AWS Database Blog](https://aws.amazon.com/blogs/database/building-python-applications-with-sqlalchemy-and-aurora-dsql/)

### [Building an AI Gateway to Amazon Bedrock with Amazon API Gateway](3.3-blog3/)

The article introduces a managed access layer for Amazon Bedrock using Amazon API Gateway, a Lambda authorizer, and a Lambda integration that dynamically forwards model requests. It covers deploying a private gateway with AWS CloudFormation, testing within a CloudShell VPC environment, streaming model responses, enabling JWT authentication, and extending the gateway with throttling, AWS WAF, caching, and content filtering.

Source article: [AWS Architecture Blog](https://aws.amazon.com/vi/blogs/architecture/building-an-ai-gateway-to-amazon-bedrock-with-amazon-api-gateway/)