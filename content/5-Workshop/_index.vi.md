---
title: "Workshop"
date: 2026-07-19
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Xây dựng và triển khai SmartStudy AI

#### Tổng quan

Workshop mô tả hệ thống SmartStudy đã triển khai, từ cách tổ chức source code đến tài nguyên AWS và kết quả ứng dụng. SmartStudy áp dụng kiến trúc local-first theo Ports & Adapters, đồng thời sử dụng các dịch vụ serverless AWS cho frontend, xác thực, API, lưu trữ, xử lý bất đồng bộ, dữ liệu ứng dụng và monitoring.

Kiến trúc triển khai không sử dụng custom VPC, subnet, NAT Gateway, Internet Gateway, Route 53 domain, Amazon Bedrock, AWS Secrets Manager hoặc AWS CloudTrail. Khả năng AI được cung cấp bởi Ollama với Qwen 2.5 7B trên máy chủ AI local tự quản lý thông qua thành phần Cloudflare relay.

#### Nội dung hiện tại

1. [Tổng quan SmartStudy](5.1-Workshop-overview/)
2. [Chuẩn bị và quyết định kiến trúc](5.2-Prerequiste/)
3. [Triển khai và xác minh hạ tầng AWS cốt lõi](5.3-Infrastructure/)
4. [Triển khai frontend và cấu hình xác thực](5.4-Frontend-Authentication/)
5. [Upload, xử lý và quản lý tài liệu](5.5-Document-Processing/)
6. [Tích hợp Ollama và workflow học với AI](5.6-AI-Integration/)
7. [Xác minh monitoring và kết quả dự án](5.7-Testing-Results/)

Các phần cuối mô tả tích hợp AI, monitoring, kết quả end-to-end và trạng thái vận hành trong giai đoạn đánh giá.
