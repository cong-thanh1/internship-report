---
title: "Xử lý tài liệu"
date: 2026-07-19
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

#### Tổng quan

SmartStudy xử lý tài liệu PDF theo cơ chế bất đồng bộ để các tác vụ trích xuất và chia nhỏ nội dung kéo dài không chặn API phục vụ người dùng. Ứng dụng duy trì cùng một workflow nghiệp vụ trong môi trường local và AWS thông qua các infrastructure adapter có thể thay thế.

```text
React frontend
      │ yêu cầu presigned upload URL
      ▼
API Gateway → API Lambda
      │
      ├── metadata → DynamoDB
      └── presigned URL → trình duyệt upload PDF → Amazon S3
                                            │ hoàn tất upload
                                            ▼
                                      Amazon SQS
                                            │
                                            ▼
                               Document Ingestion Lambda
                                 ├── trích xuất PDF
                                 ├── phát hiện heading
                                 ├── chia document chunk
                                 └── lưu kết quả vào DynamoDB
```

Workflow đã triển khai sử dụng document bucket, processing queue trên Amazon SQS, document-ingestion function và các bảng dữ liệu tài liệu.

#### Nội dung

1. [Upload và xử lý tài liệu](5.5.1-upload-processing/)
2. [Quản lý và tóm tắt tài liệu](5.5.2-document-management-summary/)
