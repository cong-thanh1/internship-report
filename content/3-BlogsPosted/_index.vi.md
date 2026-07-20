---
title: "Blog đã dịch"
date: 2026-07-09
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

Phần này tổng hợp nội dung bài viết kỹ thuật về cách triển khai ứng dụng .NET Blazor WebAssembly trên AWS. Kiến trúc không cần máy chủ ứng dụng chạy thường xuyên nhưng vẫn đáp ứng các yêu cầu như HTTPS, phân phối nội dung toàn cầu, bảo vệ origin và cache.

### [Triển khai ứng dụng .NET Blazor WebAssembly trên Amazon S3 và Amazon CloudFront](3.1-blog1/)

Bài viết trình bày cách publish ứng dụng Blazor WebAssembly thành các tệp tĩnh, lưu chúng trong một S3 bucket private và phân phối tới người dùng qua CloudFront. Các nội dung chính gồm Origin Access Control, chiến lược cache cho từng nhóm tệp, cách xử lý client-side routing và quy trình triển khai bằng Terraform kết hợp AWS CLI.

Nguồn bài viết: [.NET on AWS Blog](https://aws.amazon.com/vi/blogs/dotnet/host-a-net-blazor-webassembly-app-on-amazon-s3-and-amazon-cloudfront/)
