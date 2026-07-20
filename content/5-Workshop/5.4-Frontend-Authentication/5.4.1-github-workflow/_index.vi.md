---
title: "Quy trình branch trên GitHub"
date: 2026-07-19
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

#### Quy trình repository

SmartStudy sử dụng GitHub để quản lý source code và phối hợp phát triển. Branch mặc định `main` đại diện cho source sẵn sàng production, trong khi `staging` được dùng để tích hợp và kiểm tra thay đổi trước khi đưa lên production. Branch `develop` cùng các feature branch hoặc bug-fix branch hỗ trợ quá trình phát triển.

Trang tổng quan repository cho thấy bản cập nhật production cuối được merge từ `staging` vào `main` thông qua pull request `#158`.

![Branch main của repository SmartStudy](/images/5-Workshop/5.4-Frontend-Authentication/github-repository-main.png)

*Hình 5.18: Repository SmartStudy sau khi merge thay đổi từ staging vào main.*

#### Kiểm tra branch

Trang branch xác định `main` là default branch và hiển thị check thành công cho `main`, `staging`, `develop`. Quy trình này tách công việc tích hợp khỏi quá trình phân phối production.

![Các branch GitHub của SmartStudy](/images/5-Workshop/5.4-Frontend-Authentication/github-branches.png)

*Hình 5.19: Default, staging, develop và các branch hỗ trợ với check thành công.*

Quy trình production được tóm tắt như sau:

```text
Feature hoặc bug-fix branch
          ▼
       develop
          ▼
       staging ── tích hợp và kiểm tra
          ▼
         main ── AWS Amplify triển khai production
```
