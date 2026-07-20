---
title: "Ollama và Cloudflare Relay"
date: 2026-07-19
weight: 1
chapter: false
pre: " <b> 5.6.1. </b> "
---

#### Dịch vụ AI local

Dịch vụ AI đã triển khai sử dụng Ollama để cung cấp Qwen 2.5 7B từ máy chủ AI local tự quản lý. Phương án này được lựa chọn sau khi tài khoản dự án không thể thực hiện operation cần thiết trên Amazon Bedrock.

AI host hoạt động độc lập với tầng dữ liệu AWS. AWS tiếp tục xử lý xác thực, API, lưu tài liệu, queue, dữ liệu ứng dụng và monitoring; máy chủ local đảm nhiệm model inference.

#### Thành phần relay

Repository có module TypeScript `cloudflare-relay/` độc lập với source, test, package configuration và Wrangler configuration riêng. Module cung cấp lớp relay liên quan đến việc truy cập model local từ workflow đã triển khai.

![Module source Cloudflare relay](/images/5-Workshop/5.6-AI-Integration/cloudflare-relay.png)

*Hình 5.29: Module Cloudflare relay riêng trong repository SmartStudy.*

Workshop công khai không hiển thị relay URL, token hoặc địa chỉ máy chủ local. Các tính năng phụ thuộc AI yêu cầu cả relay và dịch vụ Ollama local duy trì trạng thái có thể truy cập.
