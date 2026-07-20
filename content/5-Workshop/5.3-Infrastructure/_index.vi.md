---
title: "Hạ tầng AWS cốt lõi"
date: 2026-07-19
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

#### Tổng quan

SmartStudy sử dụng Infrastructure as Code thay vì tạo thủ công từng tài nguyên trên AWS Management Console. Hạ tầng được định nghĩa bằng TypeScript với AWS CDK, tổng hợp thành AWS CloudFormation template và triển khai thành hai stack staging, production riêng biệt.

```text
AWS CDK source
      │ tổng hợp và triển khai
      ▼
AWS CloudFormation
      ├── SmartStudy-staging-Foundation
      └── SmartStudy-production-Foundation
             ├── Amazon API Gateway
             ├── AWS Lambda
             ├── Amazon S3
             ├── Processing queue trên Amazon SQS
             ├── Amazon DynamoDB
             └── Tài nguyên IAM và monitoring hỗ trợ
```

Do quá trình triển khai được tự động hóa, workshop mô tả workflow CDK có thể tái lập và xác minh các tài nguyên do CloudFormation tạo ra. Workshop không dựng lại các màn hình wizard trên Console hoặc trình bày danh sách tài nguyên cuối như các bước tạo thủ công.

#### Nội dung

1. [Triển khai hạ tầng bằng AWS CDK và CloudFormation](5.3.1-deploy-with-cdk/)
2. [Xác minh tài nguyên AWS sau triển khai](5.3.2-verify-resources/)
3. [Rà soát IAM role và policy](5.3.3-iam-roles-policies/)

Bằng chứng triển khai được tổ chức theo ba lớp: định nghĩa CDK trong source code, CloudFormation stack thành công và các tài nguyên AWS kết quả ở staging, production.
