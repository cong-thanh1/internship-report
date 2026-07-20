---
title: "Nhật ký tuần 9"
date: 2026-07-06
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

### Trọng tâm trong tuần

Mục tiêu tuần 9 là có một môi trường staging chạy được thay vì tiếp tục kiểm tra từng phần riêng lẻ. Tôi dùng AWS CDK để định nghĩa hạ tầng và ưu tiên hoàn thiện luồng xác thực, API, upload tài liệu và xử lý bất đồng bộ.

### Công việc đã thực hiện

| Ngày | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 1 | **Infrastructure as Code và xác thực** <br> - Bootstrap CDK <br> - Định nghĩa tài nguyên staging và IAM role <br> - Tạo Cognito và Pre Sign-up Lambda | 06/07/2026 | 07/07/2026 | Dự án AWS CDK |
| 2 | **Backend và tầng dữ liệu** <br> - Tạo API Gateway, Lambda, S3 và DynamoDB <br> - Kết nối API với tầng dữ liệu | 08/07/2026 | 09/07/2026 | SmartStudy backend |
| 3 | **Xử lý tài liệu bất đồng bộ** <br> - Tạo SQS và DLQ <br> - Tạo Document Ingestion Lambda <br> - Kết nối S3, SQS, Lambda và DynamoDB | 10/07/2026 | 11/07/2026 | Luồng xử lý tài liệu |
| 4 | **Triển khai staging** <br> - Deploy nhánh staging bằng Amplify <br> - Cấu hình Cognito và API <br> - Kiểm tra Ollama endpoint | 11/07/2026 | 12/07/2026 | Môi trường staging |

### Vấn đề và cách xử lý

Phần tích hợp phát sinh nhiều lỗi cấu hình hơn lúc chạy local, chủ yếu ở biến môi trường frontend, quyền IAM giữa các dịch vụ và trạng thái bất đồng bộ của tài liệu. Tôi dùng log của Lambda và message trong queue để lần theo từng bước, sau đó bổ sung dead-letter queue để tách các message xử lý thất bại thay vì để chúng lặp lại không rõ nguyên nhân.

### Kết quả cuối tuần

Môi trường staging đã kết nối Cognito, API Gateway, Lambda, S3, SQS, DynamoDB và Amplify. Luồng xử lý tài liệu chạy bất đồng bộ và đã có đường xử lý lỗi; hệ thống sẵn sàng để tách production và hoàn thiện kiểm thử ở tuần cuối.
