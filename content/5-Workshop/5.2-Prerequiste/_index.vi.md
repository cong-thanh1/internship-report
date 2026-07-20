---
title: "Chuẩn bị và quyết định kiến trúc"
date: 2026-07-19
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

#### Điều kiện kỹ thuật

Trước khi triển khai SmartStudy, môi trường phát triển cần có:

* GitHub repository và quy trình branch cho development, staging và production.
* Node.js và npm cho frontend React, backend TypeScript và dự án AWS CDK.
* Docker Engine với Docker Compose v2 cho môi trường phát triển local-first.
* Tài khoản AWS có quyền sử dụng AWS Amplify, Amazon Cognito, Amazon API Gateway, AWS Lambda, Amazon S3, Amazon SQS, Amazon DynamoDB, Amazon CloudWatch, AWS IAM và AWS CloudFormation.
* AWS CLI và AWS CDK được cấu hình để triển khai hạ tầng.
* Máy chủ AI local tự quản lý chạy Ollama với Qwen 2.5 7B.

Các giá trị nhạy cảm phải được cung cấp qua cấu hình biến môi trường khi triển khai và không được commit lên GitHub. Dự án duy trì file `.env.example` để mô tả tên biến cần thiết nhưng không chứa credential thật.

#### AWS Region

Hai môi trường staging và production của SmartStudy được triển khai tại **US East (N. Virginia), `us-east-1`**. Việc sử dụng cùng một Region giúp tài nguyên AWS và CDK stack nhất quán giữa hai môi trường.

![AWS Region sử dụng cho SmartStudy](/images/5-Workshop/5.2-Prerequisite/aws-region.png)

*Hình 5.3: AWS Region được lựa chọn để triển khai SmartStudy.*

#### Chuẩn bị Infrastructure as Code

Module `infra/` chứa dự án AWS CDK. Cấu trúc này bao gồm CDK entry point, định nghĩa stack có thể tái sử dụng, test, cấu hình TypeScript và package dependency. Nhờ đó, tài nguyên staging và production có thể được triển khai từ cùng một bộ định nghĩa hạ tầng nhưng sử dụng tên và cấu hình riêng theo môi trường.

![Module hạ tầng AWS CDK của SmartStudy](/images/5-Workshop/5.2-Prerequisite/github-cdk-infrastructure.png)

*Hình 5.4: Dự án AWS CDK được lưu trong module `infra`.*

#### Giới hạn và thay đổi kiến trúc

Kiến trúc ban đầu được điều chỉnh sau khi nhóm kiểm tra quyền sử dụng dịch vụ và các giới hạn triển khai thực tế.

##### Custom domain

Nhóm ban đầu dự kiến đăng ký domain `smartstudylearning.com` thông qua Amazon Route 53. Quá trình đăng ký không thể hoàn tất, vì vậy ứng dụng production sử dụng domain HTTPS mặc định `amplifyapp.com` do AWS Amplify cung cấp.

![Đăng ký domain Route 53 thất bại](/images/5-Workshop/5.2-Prerequisite/route53-domain-registration-failed.png)

*Hình 5.5: Route 53 không thể hoàn tất đăng ký domain dự kiến.*

##### Dịch vụ AI managed

Amazon Bedrock được đánh giá trong phương án kiến trúc AI ban đầu. Tài khoản dự án nhận lỗi `ValidationException` với thông báo `Operation not allowed`, vì vậy Bedrock được loại khỏi kiến trúc triển khai.

![Amazon Bedrock operation not allowed](/images/5-Workshop/5.2-Prerequisite/bedrock-operation-not-allowed.png)

*Hình 5.6: Giới hạn truy cập Amazon Bedrock trong quá trình đánh giá.*

Phương án cuối sử dụng **Ollama với Qwen 2.5 7B** trên máy chủ AI local tự quản lý. Repository có module `cloudflare-relay/` riêng, cung cấp lớp relay giữa backend đã triển khai và dịch vụ AI local.

![Module Cloudflare AI relay](/images/5-Workshop/5.2-Prerequisite/github-cloudflare-relay.png)

*Hình 5.7: Module Cloudflare relay trong repository SmartStudy.*

#### Các quyết định cuối cùng

Phạm vi triển khai được xác định theo các quyết định sau:

* Sử dụng domain mặc định của AWS Amplify thay cho Route 53.
* Sử dụng Ollama và Qwen 2.5 7B thay cho Amazon Bedrock.
* Sử dụng cấu hình biến môi trường khi triển khai, không dùng AWS Secrets Manager.
* Sử dụng Amazon CloudWatch để giám sát ứng dụng; AWS CloudTrail không nằm trong phạm vi triển khai.
* Duy trì tài nguyên staging và production riêng biệt.
* Sử dụng AWS CDK và AWS CloudFormation để triển khai hạ tầng có thể lặp lại.

Sau khi hoàn tất các điều kiện và quyết định trên, phần tiếp theo có thể triển khai các tài nguyên AWS cốt lõi của hệ thống.
