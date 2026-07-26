---
title: "Blog đã dịch"
date: 2026-07-09
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

Phần này tổng hợp ba bài viết kỹ thuật được dịch trong quá trình thực tập, bao gồm triển khai ứng dụng web tĩnh, phát triển ứng dụng với cơ sở dữ liệu phân tán và xây dựng lớp truy cập an toàn cho dịch vụ AI tạo sinh trên AWS.

### [Triển khai ứng dụng .NET Blazor WebAssembly trên Amazon S3 và Amazon CloudFront](3.1-blog1/)

Bài viết trình bày cách publish ứng dụng Blazor WebAssembly thành các tệp tĩnh, lưu chúng trong một S3 bucket private và phân phối tới người dùng qua CloudFront. Các nội dung chính gồm Origin Access Control, chiến lược cache cho từng nhóm tệp, cách xử lý client-side routing và quy trình triển khai bằng Terraform kết hợp AWS CLI.

Nguồn bài viết: [.NET on AWS Blog](https://aws.amazon.com/vi/blogs/dotnet/host-a-net-blazor-webassembly-app-on-amazon-s3-and-amazon-cloudfront/)

### [Xây dựng ứng dụng Python với SQLAlchemy và Amazon Aurora DSQL](3.2-blog2/)

Amazon Aurora DSQL là cơ sở dữ liệu phân tán, serverless, tương thích PostgreSQL, có khả năng tự động mở rộng theo lưu lượng ứng dụng và xác thực bằng AWS IAM. Khi kết hợp với SQLAlchemy, developer vẫn sử dụng được quy trình ORM quen thuộc của Python nhưng cần điều chỉnh một số pattern thiết kế và kết nối cho môi trường phân tán.

Bài viết tập trung vào ba điều chỉnh quan trọng:

- Sử dụng UUID làm primary key để hỗ trợ insert phân tán có khả năng mở rộng.
- Khai báo quan hệ ở tầng ứng dụng bằng `relationship()`, `primaryjoin` và `foreign()` thay cho foreign key constraint.
- Cấu hình SQLAlchemy engine ở chế độ AUTOCOMMIT để tránh thao tác SAVEPOINT không được Aurora DSQL hỗ trợ.

Bài viết cũng đề cập Aurora DSQL Python Connector, xác thực IAM, thao tác CRUD, eager loading, quản lý vòng đời connection và retry với exponential backoff cùng jitter khi xảy ra xung đột optimistic concurrency.

Nguồn bài viết: [AWS Database Blog](https://aws.amazon.com/blogs/database/building-python-applications-with-sqlalchemy-and-aurora-dsql/)

### [Xây dựng AI gateway cho Amazon Bedrock với Amazon API Gateway](3.3-blog3/)

Bài viết giới thiệu lớp truy cập được quản trị cho Amazon Bedrock bằng API Gateway, Lambda authorizer và Lambda integration chuyển tiếp request động. Nội dung gồm triển khai private gateway bằng CloudFormation, kiểm thử trong CloudShell VPC environment, streaming kết quả model, bật xác thực JWT và mở rộng gateway với throttling, AWS WAF, caching cùng content filtering.

Nguồn bài viết: [AWS Architecture Blog](https://aws.amazon.com/vi/blogs/architecture/building-an-ai-gateway-to-amazon-bedrock-with-amazon-api-gateway/)
