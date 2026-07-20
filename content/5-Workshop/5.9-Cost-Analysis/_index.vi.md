---
title: "Phân tích chi phí"
date: 2026-07-20
weight: 9
chapter: false
pre: " <b> 5.9. </b> "
---

# Phân tích chi phí vận hành

Chi phí được tổng hợp từ AWS Cost Explorer cho các dịch vụ thuộc kiến trúc SmartStudy tại Region US East (N. Virginia). Số liệu phản ánh giai đoạn phát triển, kiểm thử và demo với lưu lượng sử dụng thấp.

## Chi phí theo dịch vụ

| Dịch vụ | Tổng chi phí ghi nhận (USD) | Số ngày hoạt động |
| --- | ---: | ---: |
| AWS Lambda | $0.0000419621 | 8 ngày |
| Amazon S3 | $0.0000003024 | 13 ngày |
| AWS Amplify | ≈ $0.0000000001 | 8 ngày |
| Amazon CloudWatch | $0.00 | 19 ngày |
| Amazon SQS | $0.00 | 8 ngày |
| Amazon Cognito | $0.00 | 6 ngày |
| Amazon API Gateway | $0.00 | 8 ngày |
| AWS CloudFormation | $0.00 | 8 ngày |
| Amazon DynamoDB | ≈ $0.00 | 8 ngày |

Tổng chi phí dương trực tiếp được ghi nhận trong giai đoạn này là khoảng **$0.0000422646**, tương đương **$0.00 khi làm tròn đến cent**.

Các giá trị điều chỉnh rất nhỏ hoặc mang giá trị âm trong dữ liệu billing không được xem là chi phí vận hành của một dịch vụ và được loại khỏi bảng để tránh gây hiểu nhầm. Những dịch vụ không thuộc kiến trúc triển khai cuối cùng của SmartStudy cũng không được đưa vào phép tính.

## Nhận xét

Chi phí thực tế rất thấp do hệ thống chỉ phục vụ quá trình phát triển và demo với số lượng người dùng, request và tài liệu hạn chế. Nhiều dịch vụ không ghi nhận chi phí trong khoảng thời gian đo.

Kết quả trên chỉ phản ánh môi trường demo và không đại diện cho chi phí production khi số lượng người dùng, tài liệu, request API, thời gian lưu trữ và khối lượng log tăng. Chi phí của máy chủ AI local chạy Ollama cũng không nằm trong AWS Cost Explorer, vì vậy không được tính vào bảng chi phí AWS.

AWS promotional credits được áp dụng ở cấp tài khoản. Credit giúp giảm số tiền thực tế phải thanh toán nhưng không làm thay đổi lượng tài nguyên mà SmartStudy đã sử dụng.
