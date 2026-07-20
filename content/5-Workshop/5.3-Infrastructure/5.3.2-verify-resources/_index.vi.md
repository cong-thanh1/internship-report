---
title: "Xác minh tài nguyên sau triển khai"
date: 2026-07-19
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---

#### Phương pháp xác minh

Sau khi các CloudFormation stack đạt trạng thái thành công, nhóm kiểm tra các tài nguyên kết quả trên AWS Management Console. Những ảnh sau thể hiện trạng thái cuối sau triển khai, không phải các bước tạo tài nguyên thủ công.

#### Amazon API Gateway

Hai HTTP API loại Regional được khởi tạo tương ứng với môi trường staging và production của SmartStudy. Đây là các backend entry point công khai được frontend đã triển khai sử dụng.

![Các HTTP API của SmartStudy](/images/5-Workshop/5.3-Infrastructure/api-gateway.png)

*Hình 5.10: HTTP API Regional cho staging và production.*

#### AWS Lambda

Sáu Lambda function sử dụng Node.js 22.x được triển khai:

* Một API function cho mỗi môi trường.
* Một Document Ingestion function cho mỗi môi trường.
* Một Pre Sign-up function cho mỗi môi trường.

API function xử lý yêu cầu ứng dụng, ingestion function xử lý tài liệu đã upload và pre-sign-up function hỗ trợ luồng đăng ký của Cognito.

![Các Lambda function của SmartStudy](/images/5-Workshop/5.3-Infrastructure/lambda-functions.png)

*Hình 5.11: Lambda function được triển khai cho staging và production.*

#### Amazon S3

AWS account có một CDK asset bucket và hai document bucket của SmartStudy. Document bucket staging và production lưu tài liệu học tập riêng biệt theo từng môi trường.

![Các S3 bucket của SmartStudy](/images/5-Workshop/5.3-Infrastructure/s3-buckets.png)

*Hình 5.12: CDK asset bucket và document bucket riêng theo môi trường.*

#### Amazon SQS

Các Amazon SQS queue đã triển khai hỗ trợ xử lý tài liệu bất đồng bộ và tách yêu cầu upload của người dùng khỏi tác vụ tiếp nhận tài liệu kéo dài.

![Các SQS queue của SmartStudy](/images/5-Workshop/5.3-Infrastructure/sqs-queues.png)

*Hình 5.13: Các Amazon SQS queue hỗ trợ xử lý tài liệu.*

#### Amazon DynamoDB

SmartStudy sử dụng chín nhóm bảng logic cho mỗi môi trường:

* AI jobs.
* Attempts.
* Conversations.
* Conversation messages.
* Document chunks.
* Documents.
* Exams.
* Quizzes.
* Summaries.

Tài nguyên production và staging sử dụng tên riêng để dữ liệu, thao tác kiểm thử không ảnh hưởng tới ứng dụng production.

![Các bảng DynamoDB của SmartStudy - phần một](/images/5-Workshop/5.3-Infrastructure/dynamodb-tables-1.png)

*Hình 5.14: Các bảng production và nhóm bảng staging đầu tiên.*

![Các bảng DynamoDB của SmartStudy - phần hai](/images/5-Workshop/5.3-Infrastructure/dynamodb-tables-2.png)

*Hình 5.15: Các bảng DynamoDB staging còn lại.*

#### Kết quả xác minh

Danh sách dịch vụ phù hợp với các tài nguyên staging và production dự kiến từ SmartStudy foundation stack. Các luồng tích hợp chi tiết—xác thực, xử lý tài liệu, truy cập AI và monitoring—sẽ được trình bày riêng trong các phần Workshop tiếp theo.
