---
title: "Triển khai frontend và xác thực"
date: 2026-07-19
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

#### Tổng quan

SmartStudy phân phối frontend React bằng AWS Amplify và xác thực người dùng với Amazon Cognito. Quá trình triển khai không sử dụng custom VPC, subnet, NAT Gateway hoặc Internet Gateway. Amplify, Cognito, API Gateway và Lambda được sử dụng dưới dạng các dịch vụ managed theo Region.

Luồng triển khai và xác thực:

```text
GitHub branch
      │ push / merge
      ▼
AWS Amplify Hosting
      │
      ├── Amazon Cognito: đăng ký, đăng nhập và token
      └── Amazon API Gateway: yêu cầu backend đã xác thực
```

#### Nội dung

1. [Quy trình branch trên GitHub](5.4.1-github-workflow/)
2. [Triển khai bằng AWS Amplify](5.4.2-amplify-deployment/)
3. [Xác thực bằng Amazon Cognito](5.4.3-cognito-authentication/)
4. [Kết quả xác thực](5.4.4-authentication-result/)
