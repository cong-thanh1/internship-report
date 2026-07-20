---
title: "Nhật ký tuần 8"
date: 2026-06-29
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Trọng tâm trong tuần

Tuần 8 chuyển bản thiết kế thành khung ứng dụng. Tôi làm song song ba phần nhưng bám theo cùng một user flow để tránh frontend và backend định nghĩa dữ liệu lệch nhau.

### Công việc đã thực hiện

| Ngày | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 1 | **Hoàn thiện kiến trúc AWS** <br> - Chỉnh sơ đồ kiến trúc <br> - Xác định luồng upload và xử lý <br> - Ánh xạ dịch vụ AWS vào các thành phần | 29/06/2026 | 01/07/2026 | Thiết kế kiến trúc AWS |
| 2 | **Triển khai backend** <br> - Thiết kế endpoint tài liệu, hội thoại, quiz và kết quả <br> - Xác định dữ liệu lưu trữ và điểm tích hợp AI | 02/07/2026 | 03/07/2026 | Backend tasks |
| 3 | **Triển khai frontend** <br> - Dựng màn hình upload, phòng học, quiz và kết quả <br> - Đối chiếu request/response với API | 04/07/2026 | 05/07/2026 | Frontend tasks |

### Ghi nhận sau khi thực hiện

Khi ghép các màn hình với API, một số tên trường và trạng thái xử lý ban đầu chưa thống nhất. Tôi rà lại theo hành trình của một tài liệu từ lúc upload đến khi sẵn sàng để hỏi đáp, rồi chốt lại cấu trúc dữ liệu dùng chung. Việc này giúp giảm phần sửa giao diện khi backend chuyển sang triển khai thật.

### Kết quả cuối tuần

Cuối tuần, dự án đã có kiến trúc chi tiết hơn, khung API và các màn hình chính. Chức năng chưa được xem là hoàn tất ở giai đoạn này; kết quả quan trọng là các phần đã đủ rõ để đưa lên môi trường staging trong tuần 9.
