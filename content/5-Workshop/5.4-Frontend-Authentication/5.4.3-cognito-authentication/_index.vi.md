---
title: "Xác thực bằng Amazon Cognito"
date: 2026-07-19
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

#### User Pool

Amazon Cognito cung cấp lớp xác thực cho ứng dụng SmartStudy đã triển khai. Hai User Pool loại Single-Region được tạo tại `us-east-1`, tương ứng với môi trường staging và production.

![Cognito User Pool của SmartStudy](/images/5-Workshop/5.4-Frontend-Authentication/cognito-user-pools.png)

*Hình 5.21: Amazon Cognito User Pool riêng cho các môi trường SmartStudy.*

Luồng xác thực:

1. Người dùng gửi thông tin đăng ký hoặc đăng nhập từ frontend React.
2. Amazon Cognito xác thực yêu cầu và quản lý danh tính người dùng.
3. Pre Sign-up Lambda có thể áp dụng logic đăng ký riêng của dự án.
4. Sau khi xác thực thành công, Cognito cấp token cho client.
5. Frontend gửi access token khi gọi các thao tác backend được bảo vệ.

Hai User Pool riêng giúp người dùng và cấu hình staging không ảnh hưởng đến xác thực production. Mật khẩu và credential xác thực được Cognito xử lý, không được frontend lưu trực tiếp.
