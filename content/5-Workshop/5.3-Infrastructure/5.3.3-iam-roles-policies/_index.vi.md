---
title: "IAM Role và Policy"
date: 2026-07-19
weight: 3
chapter: false
pre: " <b> 5.3.3. </b> "
---

#### Phân tách IAM role

SmartStudy sử dụng AWS IAM role để tách quyền triển khai hạ tầng khỏi quyền thực thi của ứng dụng. AWS CDK tạo các bootstrap role phục vụ CloudFormation execution, deployment, asset publishing và context lookup. Các SmartStudy stack tạo thêm execution role cho Lambda function ở từng môi trường.

Danh sách role xác nhận sự tồn tại của:

* CDK CloudFormation execution role và deployment role.
* CDK file-publishing, image-publishing và lookup role.
* API Lambda execution role cho staging và production.
* Document Ingestion Lambda execution role cho staging và production.
* Pre Sign-up Lambda execution role cho staging và production.

Dịch vụ Lambda được liệt kê là trusted entity của các execution role thuộc SmartStudy, trong khi AWS CloudFormation được CDK CloudFormation execution role tin cậy.

![IAM role sử dụng khi triển khai và vận hành](/images/5-Workshop/5.3-Infrastructure/iam-roles.png)

*Hình 5.16: CDK deployment role và Lambda execution role của SmartStudy.*

#### Customer-managed policy

Danh sách IAM policy có các customer-managed policy liên kết với API Lambda role của staging và production. Danh sách cũng có Lambda basic-execution policy hỗ trợ ghi log khi runtime. Một số policy do CDK sinh sử dụng tên overflow policy khi phần quyền không thể nằm hoàn toàn trong inline policy chính.

![Các customer-managed IAM policy](/images/5-Workshop/5.3-Infrastructure/iam-policies.png)

*Hình 5.17: Customer-managed policy, bao gồm policy được sinh cho API function của SmartStudy.*

Các ảnh trên xác minh việc phân tách role và policy ở mức tổng quan; chúng không thể hiện hoặc khẳng định toàn bộ IAM action riêng lẻ. Quyền chi tiết tiếp tục được định nghĩa trong source AWS CDK và cần tuân theo nguyên tắc least privilege: mỗi function chỉ nhận quyền cần thiết cho workflow của chính nó.

#### Kết quả bảo mật

Cấu trúc IAM cung cấp:

* Ranh giới tin cậy riêng cho deployment và runtime.
* Lambda execution role riêng theo môi trường.
* Trust relationship dựa trên AWS service thay vì credential ứng dụng dài hạn.
* Khởi tạo role và policy có thể lặp lại bằng AWS CDK, CloudFormation.

AWS account identifier trong ảnh minh chứng đã được che trước khi công bố.
