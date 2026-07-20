---
title: "Nhật ký tuần 6"
date: 2026-06-15
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Trọng tâm trong tuần

Tuần này tôi học sâu hơn về IAM. Mục tiêu là hiểu quyền được cấp như thế nào cho người dùng và dịch vụ, đồng thời tập viết và đọc policy thay vì chỉ gắn quyền có sẵn trên Console.

### Công việc đã thực hiện

| Ngày | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 1 | **AWS Security và IAM** <br> - Tạo IAM user và group <br> - Gắn managed policy <br> - Kiểm tra quyền trên Console | 15/06/2026 | 17/06/2026 | [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023) |
| 2 | **IAM Policy và Role** <br> - Đọc cấu trúc policy <br> - So sánh các loại managed policy <br> - Tạo role cho dịch vụ AWS | 18/06/2026 | 19/06/2026 | [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023) |
| 3 | **Rà soát bảo mật tài khoản** <br> - Kiểm tra MFA và bảo vệ root <br> - Kiểm tra access key và least privilege | 20/06/2026 | 21/06/2026 | [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023) |

Tài liệu sử dụng: Session 5 về AWS Security và IAM trong [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023).

### Ghi nhận sau khi thực hiện

Khi kiểm tra một quyền không hoạt động, tôi học cách xem riêng principal, action, resource và effect trong policy. Cách này dễ tìm nguyên nhân hơn việc gắn thêm quyền rộng để thử. Tôi cũng phân biệt rõ hơn role của dịch vụ với IAM user: workload nhận quyền tạm thời qua role, không cần lưu access key cố định trong mã nguồn.

### Kết quả cuối tuần

Tôi có thể tổ chức user theo group, đọc một policy cơ bản và tạo role cho dịch vụ. Nội dung least privilege được áp dụng lại ở giai đoạn làm SmartStudy, đặc biệt khi cấp quyền giữa Lambda, S3, SQS và DynamoDB.
