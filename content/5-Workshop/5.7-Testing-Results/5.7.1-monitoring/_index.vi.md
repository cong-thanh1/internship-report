---
title: "Monitoring bằng CloudWatch"
date: 2026-07-19
weight: 1
chapter: false
pre: " <b> 5.7.1. </b> "
---

#### Giám sát vận hành

Amazon CloudWatch giám sát các thành phần AWS tham gia xử lý ứng dụng. Trang tổng quan nhóm alarm theo dịch vụ và thể hiện phạm vi theo dõi AWS Lambda, Amazon SQS.

![CloudWatch monitoring của SmartStudy](/images/5.7-Testing-Results/5.7-Testing-Results/cloudwatch-monitoring.png)

*Hình 5.32: Tổng quan CloudWatch alarm cho tài nguyên Lambda và SQS.*

Trạng thái được chụp không có alarm đang kích hoạt. Alarm SQS ở trạng thái `OK`, trong khi một số alarm Lambda hiển thị insufficient data trong thời gian chưa có đủ mẫu invocation. Ảnh xác minh cấu hình metric và alarm ở mức tổng quan, không khẳng định nội dung từng log event.

CloudWatch cung cấp nền tảng vận hành để phát hiện lỗi xử lý và tình trạng queue trong thời gian staging, production hoạt động.
