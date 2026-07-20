---
title: "Kết quả xác thực"
date: 2026-07-19
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---

#### Giao diện đăng nhập

Trang giới thiệu SmartStudy đã triển khai cung cấp hộp thoại đăng nhập cho người dùng hiện có. Form nhận địa chỉ email, mật khẩu và gửi thông tin qua luồng xác thực Cognito.

![Hộp thoại đăng nhập SmartStudy](/images/5-Workshop/5.4-Frontend-Authentication/login-page.png)

*Hình 5.22: Giao diện đăng nhập của ứng dụng SmartStudy đã triển khai.*

#### Giao diện đăng ký

Người dùng mới có thể tạo tài khoản SmartStudy bằng tên, địa chỉ email và mật khẩu. Giao diện hiển thị độ dài mật khẩu tối thiểu trước khi gửi yêu cầu đăng ký.

![Hộp thoại đăng ký SmartStudy](/images/5-Workshop/5.4-Frontend-Authentication/signup-page.png)

*Hình 5.23: Giao diện tạo tài khoản được kết nối với luồng Cognito.*

Hai giao diện trên, cùng với Cognito User Pool và Pre Sign-up Lambda đã triển khai, xác minh luồng xác thực từ frontend tới dịch vụ định danh AWS.
