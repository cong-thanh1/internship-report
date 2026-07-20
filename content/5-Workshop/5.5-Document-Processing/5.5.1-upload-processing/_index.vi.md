---
title: "Upload và xử lý tài liệu"
date: 2026-07-19
weight: 1
chapter: false
pre: " <b> 5.5.1. </b> "
---

#### Luồng presigned upload

Quá trình upload tài liệu sử dụng ba bước đã xác thực:

1. Frontend gọi `POST /api/v1/documents/upload-url` với tên tài liệu, content type `application/pdf` và kích thước file. File PDF được giới hạn tối đa 50 MiB.
2. Trình duyệt upload file trực tiếp vào presigned object-storage URL với các header do API trả về.
3. Frontend gọi `POST /api/v1/documents/{documentId}/complete`. API kiểm tra metadata object, chuyển trạng thái tài liệu sang `processing` và gửi processing job.

Upload trực tiếp từ trình duyệt tới storage giúp API function không phải mang PDF payload và giữ cho luồng upload hiệu quả.

#### Xử lý bất đồng bộ

Trong môi trường AWS đã triển khai, Amazon SQS chuyển document-processing job tới Document Ingestion Lambda. Luồng xử lý:

* Tải PDF từ Amazon S3.
* Trích xuất text bằng `pdf-parse`.
* Phát hiện heading chương cơ bản.
* Chia text với giới hạn mặc định 700 token mỗi chunk và overlap 80 token.
* Gọi embedding component đã cấu hình.
* Lưu metadata cùng document chunk trong DynamoDB.
* Chuyển tài liệu xử lý thành công sang `ready`; tài liệu không có text có thể trích xuất được đánh dấu `failed`.

![Đặc tả upload và xử lý tài liệu](/images/5-Workshop/5.5-Document-Processing/upload-processing-flow.png)

*Hình 5.24: Luồng presigned upload, gửi queue, trích xuất PDF và chia chunk.*

Nếu xử lý thất bại, ứng dụng ghi nhận trạng thái lỗi để kiểm tra và CloudWatch cung cấp log vận hành liên quan.
