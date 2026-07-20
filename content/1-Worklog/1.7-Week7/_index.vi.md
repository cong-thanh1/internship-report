---
title: "Nhật ký tuần 7"
date: 2026-06-22
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Trọng tâm trong tuần

Từ tuần 7, tôi bắt đầu dự án SmartStudy AI. Ý tưởng ban đầu là một ứng dụng hỗ trợ học từ tài liệu cá nhân: người dùng tải tài liệu lên, hỏi đáp theo nội dung, tạo bài luyện tập và xem lại kết quả.

### Công việc đã thực hiện

| Ngày | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 1 | **Lên ý tưởng dự án** <br> - Xác định người dùng và vấn đề <br> - Xác định các luồng tài liệu, hỏi đáp, quiz và kết quả <br> - Phác thảo user flow | 22/06/2026 | 24/06/2026 | Kế hoạch dự án |
| 2 | **Thiết kế kiến trúc AWS** <br> - Tách frontend, xác thực, API, lưu trữ, xử lý tài liệu và AI/RAG <br> - Phác thảo sơ đồ kiến trúc | 25/06/2026 | 26/06/2026 | Thiết kế kiến trúc AWS |
| 3 | **Chuẩn bị phạm vi và đầu việc** <br> - Chia hệ thống thành các module <br> - Xem xét rủi ro, chi phí và mức ưu tiên <br> - Chuẩn bị backlog | 27/06/2026 | 28/06/2026 | Project backlog |

### Quyết định và điều chỉnh

Ở bản phác thảo đầu tiên, tôi cân nhắc Amazon Bedrock và vector storage cho phần AI/RAG. Tuy nhiên, đây mới là phương án kiến trúc, chưa phải cấu hình đã triển khai. Tôi giữ ranh giới chức năng ở mức đủ cho một bản chạy hoàn chỉnh, tránh mở rộng thêm các tính năng quản lý lớp học khi những luồng cốt lõi chưa được kiểm chứng.

### Kết quả cuối tuần

SmartStudy AI đã có phạm vi tương đối rõ, sơ đồ kiến trúc đầu tiên và danh sách công việc theo module. Đây là đầu vào để tuần 8 bắt đầu dựng giao diện, API và luồng xử lý tài liệu.
