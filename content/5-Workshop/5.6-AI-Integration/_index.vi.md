---
title: "Tích hợp AI"
date: 2026-07-19
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

#### Tổng quan

SmartStudy sử dụng Ollama với Qwen 2.5 7B làm AI provider. Model chạy trên máy chủ AI local tự quản lý thay vì Amazon Bedrock. Thành phần Cloudflare relay riêng kết nối workflow backend đã triển khai tới dịch vụ AI local.

```text
SmartStudy frontend
        │
        ▼
API Gateway → API Lambda
                    │ AI request kèm context truy xuất
                    ▼
             Cloudflare relay
                    │
                    ▼
        Máy chủ AI local tự quản lý
             Ollama + Qwen 2.5 7B
                    │ generated response
                    └──────────────► SmartStudy
```

Lớp AI được sử dụng cho trợ lý học tập dựa trên tài liệu, tóm tắt, tạo quiz và giải thích đáp án. Việc lưu trữ, truy xuất tài liệu được tách khỏi máy chủ model.

#### Nội dung

1. [Ollama và Cloudflare relay](5.6.1-ollama-cloudflare-relay/)
2. [Phòng học AI](5.6.2-ai-study-room/)
