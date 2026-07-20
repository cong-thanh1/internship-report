---
title: "Triển khai bằng AWS Amplify"
date: 2026-07-19
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

#### Lưu trữ frontend

AWS Amplify Hosting build và công bố frontend SmartStudy từ GitHub repository đã kết nối. Branch `main` được cấu hình làm production branch và đã được triển khai thành công trên domain bảo mật mặc định của Amplify.

![Ứng dụng SmartStudy trên AWS Amplify](/images/5-Workshop/5.4-Frontend-Authentication/amplify-production.png)

*Hình 5.20: Branch production của SmartStudy được triển khai trên AWS Amplify.*

Trang tổng quan Amplify xác nhận:

* Ứng dụng SmartStudy được kết nối với GitHub.
* Hai branch được cấu hình cho các môi trường riêng.
* `main` được xác định là production branch.
* Bản production đang hoạt động trên URL `amplifyapp.com`.
* Firewall protection được bật cho ứng dụng được lưu trữ.

Dự án sử dụng domain do Amplify cung cấp vì quá trình đăng ký custom domain bằng Route 53 không thể hoàn tất. Phương án này vẫn cung cấp truy cập HTTPS mà không cần bổ sung VPC hoặc hạ tầng mạng.
