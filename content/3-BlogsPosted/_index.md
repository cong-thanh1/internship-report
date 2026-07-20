---
title: "Translated Blog"
date: 2026-07-09
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

This section summarizes a technical article about deploying a .NET Blazor WebAssembly application on AWS. The architecture does not require a continuously running application server, while still addressing practical web-hosting requirements such as HTTPS, global content delivery, origin protection, and caching.

### [Hosting a .NET Blazor WebAssembly App on Amazon S3 and Amazon CloudFront](3.1-blog1/)

The article explains how to publish a Blazor WebAssembly application as static files, store them in a private S3 bucket, and distribute them through CloudFront. Its main topics include Origin Access Control, cache rules for different file groups, client-side routing, and the deployment workflow using Terraform and the AWS CLI.

Source article: [.NET on AWS Blog](https://aws.amazon.com/blogs/dotnet/host-a-net-blazor-webassembly-app-on-amazon-s3-and-amazon-cloudfront/)
