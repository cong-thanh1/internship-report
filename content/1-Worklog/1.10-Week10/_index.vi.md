---
title: "Nhật ký tuần 10"
date: 2026-07-13
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

### Trọng tâm trong tuần

Tuần cuối tôi tách môi trường production khỏi staging, ghép toàn bộ chức năng và chạy lại quy trình như một người dùng thật. Công việc không chỉ là deploy mà còn gồm sửa lỗi tích hợp, bổ sung theo dõi và chuẩn bị bản demo.

### Công việc đã thực hiện

| Ngày | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 1 | **Hạ tầng production** <br> - Deploy Cognito, API Gateway, Lambda, S3, SQS/DLQ và DynamoDB bằng CDK <br> - Kiểm tra IAM và tách môi trường | 13/07/2026 | 14/07/2026 | Dự án AWS CDK |
| 2 | **Triển khai và bảo mật frontend** <br> - Kết nối `main` với Amplify <br> - Deploy frontend production <br> - Bật WAF <br> - Kiểm tra đăng ký và đăng nhập Cognito | 14/07/2026 | 15/07/2026 | Môi trường production |
| 3 | **Tích hợp tính năng** <br> - Hoàn thiện xử lý tài liệu và lịch sử hội thoại <br> - Tích hợp Ollama <br> - Hoàn thiện quiz, chấm điểm, giải thích và kiểm tra DLQ | 15/07/2026 | 17/07/2026 | Ứng dụng SmartStudy |
| 4 | **Giám sát và kiểm tra cuối** <br> - Xem log và metric CloudWatch <br> - Tạo alarm <br> - Sửa lỗi tích hợp và giao diện <br> - Chạy kiểm thử cuối và quay demo | 18/07/2026 | 19/07/2026 | CloudWatch và video demo |

### Vấn đề và cách xử lý

Khi chuyển từ staging sang production, tôi phải rà lại các giá trị Cognito, API endpoint và quyền theo đúng tài nguyên của từng môi trường; nếu dùng nhầm một giá trị, frontend vẫn tải được nhưng request sẽ lỗi ở bước xác thực hoặc gọi API. Tôi kiểm tra lần lượt đăng nhập, upload, trạng thái xử lý, hội thoại và quiz, đồng thời đối chiếu CloudWatch log ở mỗi bước. Các lỗi giao diện được sửa sau khi luồng backend ổn định để tránh khó xác định nguồn lỗi.

### Kết quả cuối tuần

Đến ngày 19/07/2026, SmartStudy đã chạy đầy đủ các luồng xác thực, quản lý tài liệu, học cùng AI, tạo quiz, chấm điểm và xem giải thích. Mô hình Ollama chạy trên máy chủ AI local tự quản lý được dùng làm dịch vụ AI của dự án. Production được giữ hoạt động đến ngày 30/07/2026 để phục vụ đánh giá và trình diễn; việc dọn tài nguyên không nằm trong phạm vi nhật ký này.
