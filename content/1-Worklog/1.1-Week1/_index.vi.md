---
title: "Nhật ký tuần 1"
date: 2026-05-11
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

### Trọng tâm trong tuần

Tuần đầu tiên tôi làm quen với chương trình FCAJ, đọc nội quy và chuẩn bị tài khoản AWS để dùng xuyên suốt kỳ thực tập. Phần kỹ thuật tập trung vào bảo vệ tài khoản root, tạo tài khoản quản trị riêng và thiết lập cảnh báo chi phí.

### Công việc đã thực hiện

| Ngày | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 1 | Làm quen với các thành viên FCAJ; đọc nội quy và lộ trình thực tập | 11/05/2026 | 11/05/2026 | [Chương trình FCAJ](https://github.com/AWS-First-Cloud-Journey/FCJ-2023) |
| 2 | **000001 - Tạo tài khoản AWS** <br> - Tạo tài khoản AWS <br> - Bật MFA cho tài khoản root <br> - Tạo IAM user và group quản trị <br> - Tìm hiểu quy trình xác minh tài khoản | 11/05/2026 | 11/05/2026 | [000001 - Tạo tài khoản AWS](https://000001.awsstudygroup.com/vi/) |
| 3 | **000007 - Làm quen với AWS Budgets** <br> - Tạo Cost, Usage, Reservation và Savings Plans Budget | 14/05/2026 | 17/05/2026 | [000007 - AWS Budgets](https://000007.awsstudygroup.com/vi/) |

Tài liệu sử dụng: [000001 - Tạo tài khoản AWS](https://000001.awsstudygroup.com/vi/) và [000007 - AWS Budgets](https://000007.awsstudygroup.com/vi/).

### Ghi nhận sau khi thực hiện

Tôi tách tài khoản dùng hằng ngày khỏi root thay vì tiếp tục thao tác trực tiếp bằng root. MFA được bật ngay từ đầu, sau đó tôi kiểm tra lại việc đăng nhập và quyền của tài khoản quản trị. Với AWS Budgets, phần dễ nhầm là mỗi loại budget theo dõi một đối tượng khác nhau; việc tự tạo lần lượt từng loại giúp tôi phân biệt rõ theo dõi chi phí với theo dõi mức sử dụng.

### Kết quả cuối tuần

Môi trường AWS ban đầu đã sẵn sàng cho các bài lab tiếp theo. Quan trọng hơn, tôi hình thành được thói quen kiểm tra quyền truy cập và chi phí trước khi bắt đầu tạo tài nguyên.
