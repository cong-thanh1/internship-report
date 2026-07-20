---
title: "Tổng quan SmartStudy"
date: 2026-07-19
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

#### Giới thiệu

SmartStudy AI là nền tảng học tập dựa trên tài liệu, giúp sinh viên tổ chức tài liệu và học tập hiệu quả hơn. Ứng dụng kết hợp giao diện web, backend serverless trên AWS, cơ chế xử lý tài liệu bất đồng bộ và máy chủ AI local tự quản lý.

Ứng dụng hoàn chỉnh hỗ trợ các luồng chức năng sau:

* Đăng ký và đăng nhập người dùng.
* Upload PDF và quản lý tài liệu.
* Trích xuất văn bản và chia nhỏ nội dung tài liệu.
* Tóm tắt tài liệu và hỏi đáp với AI.
* Trả lời dựa trên nội dung truy xuất từ tài liệu kèm citation.
* Tự động tạo quiz, nộp đáp án, chấm điểm và xem lại kết quả.

#### Phương pháp xây dựng dự án

SmartStudy áp dụng **kiến trúc local-first theo mô hình Ports & Adapters**. Logic nghiệp vụ được tách khỏi phần triển khai hạ tầng cụ thể, nhờ đó ứng dụng có thể sử dụng các dịch vụ local khi phát triển và chuyển sang các dịch vụ AWS khi triển khai.

Môi trường phát triển local được khởi chạy bằng Docker Compose, sử dụng MinIO cho object storage tương thích S3, PostgreSQL với pgvector cho dữ liệu quan hệ và vector, cùng Redis với BullMQ cho background job. Trong môi trường đã triển khai, ứng dụng sử dụng AWS Amplify, Amazon Cognito, Amazon API Gateway, AWS Lambda, Amazon S3, Amazon SQS, Amazon DynamoDB và Amazon CloudWatch. Khả năng AI được cung cấp bởi Ollama với Qwen 2.5 7B trên máy chủ AI local tự quản lý và kết nối thông qua thành phần Cloudflare relay của dự án.

#### Tổ chức repository

Dự án được quản lý trong một GitHub repository duy nhất, bao gồm frontend, backend, định nghĩa hạ tầng, tài liệu kỹ thuật, script triển khai và thành phần Cloudflare relay.

![GitHub repository của SmartStudy](/images/5-Workshop/5.1-Workshop-overview/github-repository-overview.png)

*Hình 5.1: Repository SmartStudy và các module triển khai chính.*

Cấu trúc source code chính gồm:

* `frontend/`: Ứng dụng web React.
* `backend/`: API và worker xử lý tài liệu bằng Node.js, TypeScript.
* `infra/`: Định nghĩa hạ tầng bằng AWS CDK.
* `cloudflare-relay/`: Thành phần relay kết nối tới dịch vụ AI local.
* `docs/`: Đặc tả kỹ thuật và quy ước phát triển.
* `scripts/` và `tools/`: Công cụ hỗ trợ thiết lập và triển khai dự án.
* `docker-compose.yml`: Hạ tầng cho môi trường phát triển local.

![Cấu trúc repository SmartStudy](/images/5-Workshop/5.1-Workshop-overview/repository-structure.png)

*Hình 5.2: Cấu trúc repository cốt lõi được mô tả trong dự án.*

#### Tổng quan triển khai

SmartStudy duy trì hai môi trường staging và production riêng biệt tại region AWS `us-east-1`. Các thay đổi được tích hợp và kiểm tra thông qua hệ thống branch trên GitHub trước khi merge vào `main`. AWS Amplify build và công bố frontend production, trong khi AWS CDK cùng AWS CloudFormation triển khai các tài nguyên backend.

Các phần tiếp theo sẽ trình bày quá trình chuẩn bị, triển khai, xử lý tài liệu, tích hợp AI, giám sát và kiểm thử end-to-end của SmartStudy.
