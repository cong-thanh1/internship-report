---
title: "Triển khai bằng AWS CDK"
date: 2026-07-19
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

#### Source code hạ tầng

Thư mục `infra/` chứa ứng dụng AWS CDK dùng để khởi tạo hạ tầng SmartStudy. Module bao gồm:

* `bin/`: CDK application entry point.
* `lib/`: Định nghĩa stack và tài nguyên có thể tái sử dụng.
* `test/`: Test dành cho hạ tầng.
* `cdk.json`: Cấu hình ứng dụng CDK.
* `package.json`: Dependency và script của dự án hạ tầng.
* `tsconfig.json`: Cấu hình TypeScript compiler.

![Source hạ tầng AWS CDK](/images/5-Workshop/5.2-Prerequisite/github-cdk-infrastructure.png)

*Hình 5.8: Dự án AWS CDK trong repository SmartStudy.*

#### Quy trình triển khai

Hạ tầng được triển khai theo trình tự có kiểm soát:

1. Cài đặt dependency của dự án hạ tầng.
2. Cấu hình AWS account, Region `us-east-1` và các giá trị riêng theo môi trường.
3. Kiểm tra và tổng hợp ứng dụng CDK thành CloudFormation template.
4. Triển khai foundation stack cho staging.
5. Kiểm tra ứng dụng staging và việc tích hợp dịch vụ.
6. Triển khai foundation stack cho production từ cùng bộ định nghĩa CDK.

Các lệnh chính xác được duy trì trong script của dự án. Cấu hình nhạy cảm được cung cấp qua môi trường triển khai và không được commit vào repository.

#### Kết quả triển khai CloudFormation

AWS CloudFormation hiển thị ba stack đang hoạt động:

* `CDKToolkit`: Tài nguyên cần thiết để triển khai ứng dụng CDK trong AWS account.
* `SmartStudy-staging-Foundation`: Hạ tầng staging.
* `SmartStudy-production-Foundation`: Hạ tầng production.

Hai stack SmartStudy đạt trạng thái `UPDATE_COMPLETE`, trong khi CDK bootstrap stack đạt `CREATE_COMPLETE`.

![Các CloudFormation stack đã triển khai](/images/5-Workshop/5.3-Infrastructure/cloudformation-stacks.png)

*Hình 5.9: CDK bootstrap, staging và production CloudFormation stack triển khai thành công.*

Các trạng thái trên xác nhận định nghĩa hạ tầng đã được CloudFormation chấp nhận và triển khai. Bước tiếp theo là xác minh tài nguyên kết quả trong từng dịch vụ AWS.
