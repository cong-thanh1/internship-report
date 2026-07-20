---
title: "Nhật ký tuần 2"
date: 2026-05-18
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Trọng tâm trong tuần

Sau phần thiết lập tài khoản, tôi chuyển sang mạng trên AWS. Mục tiêu của tuần là tự dựng một VPC, hiểu đường đi của traffic và kết nối tới EC2 bằng Session Manager thay cho SSH trực tiếp.

### Công việc đã thực hiện

| Ngày | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 1 | **000003 - Tạo VPC** <br> - Tìm hiểu các thành phần và tường lửa VPC <br> - Tạo VPC theo bài lab <br> - Tìm hiểu luồng Site-to-Site VPN | 18/05/2026 | 21/05/2026 | [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023) |
| 2 | **000058 - Systems Manager Session Manager** <br> - Chuẩn bị IAM role và SSM Agent <br> - Kết nối EC2 <br> - Kiểm tra session log <br> - Cấu hình port forwarding | 18/05/2026 | 24/05/2026 | [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023) |

Tài liệu sử dụng: các bài `000003 - Create a VPC` và `000058 - Systems Manager Session Manager` trong [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023).

### Ghi nhận sau khi thực hiện

Phần mất thời gian nhất là đối chiếu route table với subnet và rule của Security Group/NACL khi kết nối chưa đi đúng như dự kiến. Tôi kiểm tra lần lượt từng lớp thay vì thay đổi nhiều cấu hình cùng lúc. Với Session Manager, tôi nhận ra EC2 không chỉ cần SSM Agent mà còn phải có IAM role và đường kết nối phù hợp tới dịch vụ SSM.

### Kết quả cuối tuần

Tôi dựng được VPC theo bài thực hành và quản trị EC2 qua Session Manager. Bài lab giúp tôi nhìn rõ hơn mối liên hệ giữa định tuyến, tường lửa và quyền IAM trong một kết nối thực tế.
