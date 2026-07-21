---
title: "Xây dựng ứng dụng Python với SQLAlchemy và Amazon Aurora DSQL"
date: 2026-06-08
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---


Khi một ứng dụng Python cần cơ sở dữ liệu có khả năng mở rộng, SQLAlchemy kết hợp với Amazon Aurora DSQL là một lựa chọn đáng chú ý. SQLAlchemy cung cấp mô hình ORM quen thuộc để định nghĩa model, xây dựng truy vấn và thao tác dữ liệu. Aurora DSQL là cơ sở dữ liệu phân tán, serverless, tương thích PostgreSQL, tự động mở rộng theo lưu lượng và sử dụng AWS IAM để xác thực thay vì lưu mật khẩu dài hạn trong ứng dụng.

![Bài viết AWS về SQLAlchemy và Amazon Aurora DSQL](/images/3-BlogPosted/blog.jpg)

*Hình 1: Bài viết kỹ thuật về cách tích hợp SQLAlchemy với Amazon Aurora DSQL.*

## Tổng quan giải pháp

Bài viết gốc minh họa bằng một ứng dụng CLI quản lý phòng khám thú y. Ứng dụng sử dụng SQLAlchemy 2.x để quản lý các model như `owner`, `pet`, `veterinarian` và `specialty`, đồng thời thực hiện các thao tác CRUD và eager loading.

Luồng kết nối chính gồm:

```text
Python CLI Application
        │ SQLAlchemy 2.x ORM
        ▼
Aurora DSQL Python Connector + psycopg3
        │ IAM authentication
        ▼
Amazon Aurora DSQL
```

Aurora DSQL Python Connector chịu trách nhiệm tạo và làm mới IAM token ngắn hạn. Nhờ đó, ứng dụng không phải lưu mật khẩu database cố định trong source code hoặc file cấu hình.

## Ba điều chỉnh quan trọng

### 1. Sử dụng UUID làm primary key

Aurora DSQL là một hệ thống phân tán. UUID không yêu cầu các node phối hợp để cấp giá trị tuần tự, nhờ đó thao tác insert có thể mở rộng tốt hơn và tránh điểm nghẽn từ bộ đếm ID dùng chung.

SQLAlchemy có thể yêu cầu Aurora DSQL tự tạo UUID khi insert:

```python
id: Mapped[UUID] = mapped_column(
    Uuid,
    primary_key=True,
    server_default=text("gen_random_uuid()"),
)
```

Aurora DSQL vẫn hỗ trợ sequence và identity column trong một số trường hợp, nhưng UUID là lựa chọn phù hợp hơn cho workload phân tán.

### 2. Quản lý quan hệ ở tầng ứng dụng

Aurora DSQL hiện không hỗ trợ foreign key constraint, vì vậy không thể dùng `ForeignKey()` trực tiếp trong khai báo column. Cột tham chiếu vẫn được lưu như một UUID thông thường:

```python
owner_id: Mapped[UUID | None] = mapped_column(Uuid, nullable=True)
```

Do không có `ForeignKey()`, SQLAlchemy không thể tự suy ra điều kiện join. Quan hệ phải được khai báo rõ bằng `relationship()`, `primaryjoin` và `foreign()`:

```python
Owner.pets = relationship(
    "Pet",
    primaryjoin=Owner.id == foreign(Pet.owner_id),
)
```

`foreign()` cho SQLAlchemy biết column nào đóng vai trò tham chiếu. Tính toàn vẹn quan hệ vì thế cần được kiểm tra ở tầng ứng dụng thay vì dựa vào database constraint.

### 3. Bật AUTOCOMMIT cho SQLAlchemy engine

Aurora DSQL không hỗ trợ `SAVEPOINT`, trong khi SQLAlchemy và psycopg có thể sử dụng cơ chế này trong quá trình khởi tạo hoặc quản lý transaction. Engine cần được cấu hình với `isolation_level="AUTOCOMMIT"`, đồng thời connection của connector sử dụng `autocommit=True`:

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

`pool_pre_ping` giúp kiểm tra connection trước khi sử dụng. `pool_recycle=3300` làm mới connection trước giới hạn một giờ của Aurora DSQL.

## Xác thực bằng AWS IAM

Aurora DSQL sử dụng token IAM có thời hạn thay cho mật khẩu database dài hạn. Ứng dụng cần quyền `dsql:DbConnect` cho user thông thường hoặc `dsql:DbConnectAdmin` khi thực hiện tác vụ quản trị. Policy runtime nên được giới hạn vào ARN của cluster cụ thể theo nguyên tắc đặc quyền tối thiểu.

Connector xử lý việc tạo và làm mới token, còn SQLAlchemy tiếp tục cung cấp Session, ORM query và connection pooling như trong một ứng dụng PostgreSQL quen thuộc.

## CRUD và eager loading

Sau khi cấu hình model và engine, ứng dụng có thể thực hiện các thao tác create, read, update và delete bằng SQLAlchemy Session. Những quan hệ khai báo ở tầng ứng dụng vẫn có thể dùng `joinedload()` để eager-load dữ liệu liên quan trong cùng một truy vấn.

Điều quan trọng là việc không có foreign key constraint không đồng nghĩa với không có quan hệ giữa các model. Quan hệ vẫn tồn tại trong ORM, nhưng ứng dụng chịu trách nhiệm bảo đảm ID tham chiếu hợp lệ và xử lý quy tắc xóa hoặc cập nhật dữ liệu liên quan.

## Xử lý xung đột ghi

Aurora DSQL sử dụng optimistic concurrency control. Khi hai transaction cập nhật cùng dữ liệu hoặc session sử dụng schema cache không còn hợp lệ, database có thể trả về `SQLSTATE 40001`. Trong psycopg3, lỗi này xuất hiện dưới dạng `SerializationFailure` và có thể retry an toàn.

Ứng dụng nên áp dụng exponential backoff kết hợp jitter:

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

Backoff làm tăng thời gian chờ sau mỗi lần thất bại, còn jitter thêm độ trễ ngẫu nhiên để nhiều request không retry đồng thời và tiếp tục xung đột.

## Những điểm cần ghi nhớ

Để SQLAlchemy hoạt động tốt với Aurora DSQL, cần ghi nhớ ba pattern chính:

1. Dùng UUID làm primary key cho workload phân tán.
2. Dùng application-level relationship thay cho foreign key constraint.
3. Cấu hình engine ở chế độ AUTOCOMMIT để tránh thao tác SAVEPOINT không được hỗ trợ.

Ngoài ra, ứng dụng production nên sử dụng IAM theo nguyên tắc đặc quyền tối thiểu, tái tạo connection trước khi hết hạn và bổ sung retry logic cho transaction conflict. Những pattern này không chỉ áp dụng cho SQLAlchemy mà còn hữu ích với nhiều Python ORM khác khi làm việc với Aurora DSQL.

---

**Nguồn và credit:** Dipen Patel, [Building Python applications with SQLAlchemy and Aurora DSQL – AWS Database Blog](https://aws.amazon.com/blogs/database/building-python-applications-with-sqlalchemy-and-aurora-dsql/), ngày 08/06/2026.
