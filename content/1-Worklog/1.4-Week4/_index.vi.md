---
title: "Nhật ký tuần 4"
date: 2026-06-01
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Trọng tâm trong tuần

Nội dung tuần này chuyển sang nhóm dịch vụ compute. Tôi bắt đầu từ thao tác cơ bản với EC2, sau đó ghép Launch Template, Target Group, Load Balancer và Auto Scaling Group thành một luồng triển khai hoàn chỉnh. Cuối tuần tôi thử Lightsail để so sánh với EC2.

### Công việc đã thực hiện

| Ngày | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 1 | **000004 - Thao tác EC2 cơ bản** <br> - Tạo EC2 instance <br> - Cài đặt ứng dụng <br> - Tạo snapshot | 01/06/2026 | 02/06/2026 | [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023) |
| 2 | **000006 - Triển khai Auto Scaling Group** <br> - Tạo Launch Template, Target Group, Load Balancer và Auto Scaling Group <br> - Kiểm tra instance health | 03/06/2026 | 05/06/2026 | [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023) |
| 3 | **000045 - Làm quen với Amazon Lightsail** <br> - Triển khai ứng dụng <br> - Thử Lightsail Load Balancer và RDS <br> - Tìm hiểu chuyển sang EC2 | 06/06/2026 | 07/06/2026 | [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023) |

Tài liệu sử dụng: các bài `000004`, `000006` và `000045` trong [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023).

### Ghi nhận sau khi thực hiện

Ở bài Auto Scaling, health check của Target Group là bước tôi phải chú ý nhiều nhất. Instance có thể đang chạy nhưng vẫn bị đánh dấu unhealthy nếu ứng dụng chưa lắng nghe đúng cổng hoặc đường dẫn kiểm tra không trả về kết quả hợp lệ. Sau khi đối chiếu security group, port và health check path, hệ thống mới hoạt động ổn định.

### Kết quả cuối tuần

Tôi hiểu được vai trò riêng của từng thành phần trong mô hình mở rộng tự động, thay vì chỉ biết tạo EC2 đơn lẻ. Lightsail phù hợp cho bài toán cần triển khai nhanh và ít cấu hình; EC2 linh hoạt hơn khi cần kiểm soát hạ tầng chi tiết.
