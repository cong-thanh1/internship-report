---
title: "Nhật ký tuần 5"
date: 2026-06-08
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Trọng tâm trong tuần

Tuần 5 tập trung vào lưu trữ. Thay vì chỉ ghi nhớ tên dịch vụ, tôi phân loại theo bốn kiểu: object, block, file và hybrid storage, rồi đặt từng dịch vụ vào tình huống sử dụng phù hợp.

### Công việc đã thực hiện

| Ngày | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 1 | **Amazon S3** <br> - Tạo và cấu hình bucket <br> - Upload và sắp xếp object <br> - Tìm hiểu versioning, lifecycle và quyền truy cập | 08/06/2026 | 10/06/2026 | [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023) |
| 2 | **Block và File Storage** <br> - Tìm hiểu EBS và snapshot <br> - Tìm hiểu EFS <br> - So sánh block storage với shared file storage | 11/06/2026 | 12/06/2026 | [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023) |
| 3 | **Advanced và Hybrid Storage** <br> - Tìm hiểu Amazon FSx và AWS Storage Gateway <br> - So sánh các tình huống hybrid storage | 13/06/2026 | 14/06/2026 | [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023) |

Tài liệu sử dụng: Session 4 về AWS Storage Services trong [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023).

### Ghi nhận sau khi thực hiện

Điểm dễ nhầm ban đầu là EBS và EFS đều có thể được ứng dụng trên EC2 sử dụng, nhưng cách truy cập và bài toán giải quyết khác nhau. Tôi tự đối chiếu theo ba tiêu chí: kiểu dữ liệu, số lượng máy cần truy cập và yêu cầu chia sẻ. Với S3, tôi cũng lưu ý versioning và lifecycle ảnh hưởng trực tiếp đến khả năng khôi phục lẫn chi phí lưu trữ.

### Kết quả cuối tuần

Tôi không còn chọn dịch vụ lưu trữ chỉ dựa trên tên gọi. Sau tuần này, tôi có thể giải thích lựa chọn giữa S3, EBS, EFS, FSx và Storage Gateway dựa trên nhu cầu truy cập, chia sẻ dữ liệu và môi trường triển khai.
