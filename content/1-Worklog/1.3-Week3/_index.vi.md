---
title: "Nhật ký tuần 3"
date: 2026-05-25
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Trọng tâm trong tuần

Tuần 3 tiếp tục phần networking nhưng mở rộng từ một VPC sang nhiều VPC. Tôi thực hành hai cách kết nối là VPC Peering và Transit Gateway, sau đó so sánh cách định tuyến của từng mô hình.

### Công việc đã thực hiện

| Ngày | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 1 | **000019 - Thiết lập VPC Peering** <br> - Chuẩn bị tài nguyên <br> - Cập nhật Network ACL <br> - Tạo peering connection <br> - Cấu hình route table <br> - Bật Cross-Peer DNS | 25/05/2026 | 28/05/2026 | [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023) |
| 2 | **000020 - Thiết lập Transit Gateway** <br> - Chuẩn bị hạ tầng <br> - Tạo Transit Gateway và attachments <br> - Tạo TGW route table <br> - Cập nhật VPC route và kiểm tra kết nối | 29/05/2026 | 31/05/2026 | [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023) |

Tài liệu sử dụng: các bài `000019 - Set Up VPC Peering` và `000020 - Set Up Transit Gateway` trong [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023).

### Ghi nhận sau khi thực hiện

Khi peering đã ở trạng thái active nhưng hai máy vẫn chưa liên lạc được, tôi phải kiểm tra thêm route ở cả hai chiều, NACL và rule của Security Group. Đây là điểm tôi ghi nhớ rõ nhất trong tuần: tạo connection mới chỉ là một phần, traffic chỉ chạy khi toàn bộ đường đi được cấu hình đồng bộ. Transit Gateway có thêm bước attachment và route table riêng nhưng dễ quản lý hơn khi số lượng VPC tăng.

### Kết quả cuối tuần

Tôi hoàn thành cả hai mô hình kết nối và hiểu giới hạn không hỗ trợ định tuyến bắc cầu của VPC Peering. Từ bài lab, tôi có cơ sở chọn peering cho kết nối đơn giản giữa ít VPC và cân nhắc Transit Gateway khi hệ thống lớn hơn.
