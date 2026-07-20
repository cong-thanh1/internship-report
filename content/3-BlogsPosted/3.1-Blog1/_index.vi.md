---
title: "Triển khai Blazor WebAssembly trên S3 và CloudFront"
date: 2026-07-09
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

## Triển khai ứng dụng .NET Blazor WebAssembly trên AWS với Amazon S3 và Amazon CloudFront

Blazor WebAssembly (WASM) cho phép xây dựng ứng dụng web tương tác bằng C#. Sau khi biên dịch, ứng dụng gồm các tệp tĩnh như HTML, CSS, JavaScript và WASM. Vì vậy, ứng dụng có thể được lưu trên Amazon S3 và phân phối qua Amazon CloudFront mà không cần vận hành application server.

Trong mô hình này, Amazon S3 là origin private, CloudFront là CDN và điểm truy cập HTTPS công khai, còn Terraform được dùng để quản lý hạ tầng.

![Kiến trúc triển khai Blazor WebAssembly với Amazon S3 và Amazon CloudFront](/images/3-BlogPosted/WebAssembly.png)

### Các quyết định thiết kế chính

- **Sử dụng Origin Access Control thay cho Origin Access Identity:** OAC là cơ chế hiện đại thay thế OAI. CloudFront ký request bằng SigV4 và bucket policy chỉ cấp quyền cho ARN của CloudFront distribution cụ thể.
- **Tắt S3 static website hosting:** CloudFront là điểm truy cập duy nhất nên bucket không cần public website endpoint. Toàn bộ public access của bucket được chặn.
- **Dùng hai cache policy:** Các tệp có content hash trong `_framework/` được cache một năm. `index.html` không được cache để mỗi lần triển khai mới có hiệu lực ngay.
- **Không bật multithreading:** `WasmEnableThreads` được tắt. Nếu bật, ứng dụng cần thêm các header `Cross-Origin-Opener-Policy` và `Cross-Origin-Embedder-Policy`, làm phức tạp OAuth popup và cấu hình CloudFront. Với ứng dụng thiên về I/O, `async/await` là đủ.

### Điều kiện cần có

| Công cụ | Phiên bản | Mục đích |
| --- | --- | --- |
| .NET SDK | 10.0 trở lên | Build ứng dụng Blazor WASM |
| Terraform | 1.6 trở lên | Khởi tạo hạ tầng AWS |
| AWS CLI | v2 | Upload tệp và tạo cache invalidation |
| Git | Bất kỳ | Clone repository |

IAM user hoặc role cần các quyền sau:

```text
s3:CreateBucket, s3:PutObject, s3:DeleteObject, s3:ListBucket,
s3:PutBucketPolicy, s3:PutBucketVersioning, s3:PutBucketPublicAccessBlock

cloudfront:CreateDistribution, cloudfront:UpdateDistribution,
cloudfront:CreateInvalidation, cloudfront:CreateOriginAccessControl
```

Cấu hình AWS CLI trước khi bắt đầu:

```bash
aws configure
```

## Bước 1: Tạo ứng dụng Blazor WebAssembly

Tạo project Blazor WASM sử dụng .NET 10:

```bash
dotnet new blazorwasm -o src/BlazorApp --framework net10.0
```

Template mặc định có các trang Home, Counter và Weather để minh họa routing, tương tác và lấy dữ liệu qua HTTP. Không cần thay đổi mã nguồn để triển khai lên AWS.

Chạy thử ứng dụng trên máy local:

```bash
dotnet run --project src/BlazorApp
# Mở https://localhost:5XXX trong trình duyệt
```

## Bước 2: Khởi tạo hạ tầng bằng Terraform

Các tài nguyên AWS được định nghĩa trong thư mục `infra/`. Terraform tạo S3 bucket private có versioning, CloudFront distribution sử dụng OAC và hai cache policy. Nếu có custom domain, cấu hình có thể bổ sung ACM certificate và Route 53 record.

```text
infra/
├── main.tf           # S3 bucket, public access block, versioning, bucket policy
├── cloudfront.tf     # CloudFront, OAC, cache policy, ACM và Route 53 tùy chọn
├── variables.tf      # Biến đầu vào
├── outputs.tf        # Bucket, CloudFront domain và distribution ID
└── terraform.tfvars  # Giá trị cấu hình, không commit nếu có dữ liệu nhạy cảm
```

### Tạo S3 bucket private (`main.tf`)

```hcl
resource "aws_s3_bucket" "blazor_app" {
  bucket = var.bucket_name
  tags = {
    Environment = var.environment
    ManagedBy   = "terraform"
  }
}

resource "aws_s3_bucket_public_access_block" "blazor_app" {
  bucket                  = aws_s3_bucket.blazor_app.id
  block_public_acls       = true
  block_public_policy     = true
  ignore_public_acls      = true
  restrict_public_buckets = true
}

resource "aws_s3_bucket_versioning" "blazor_app" {
  bucket = aws_s3_bucket.blazor_app.id
  versioning_configuration { status = "Enabled" }
}

resource "aws_s3_bucket_policy" "blazor_app" {
  bucket     = aws_s3_bucket.blazor_app.id
  policy     = data.aws_iam_policy_document.s3_cloudfront_read.json
  depends_on = [aws_s3_bucket_public_access_block.blazor_app]
}

data "aws_iam_policy_document" "s3_cloudfront_read" {
  statement {
    actions   = ["s3:GetObject"]
    resources = ["${aws_s3_bucket.blazor_app.arn}/*"]
    principals {
      type        = "Service"
      identifiers = ["cloudfront.amazonaws.com"]
    }
    condition {
      test     = "StringEquals"
      variable = "aws:SourceArn"
      values   = [aws_cloudfront_distribution.blazor_app.arn]
    }
  }
}
```

Điều kiện `aws:SourceArn` giới hạn quyền đọc cho đúng CloudFront distribution thay vì cho phép mọi distribution thuộc CloudFront service.

### Tạo OAC và cache policy (`cloudfront.tf`)

```hcl
resource "aws_cloudfront_origin_access_control" "blazor_app" {
  name                              = "${var.bucket_name}-oac"
  origin_access_control_origin_type = "s3"
  signing_behavior                  = "always"
  signing_protocol                  = "sigv4"
}

resource "aws_cloudfront_cache_policy" "immutable_assets" {
  name        = "${var.bucket_name}-immutable-assets"
  min_ttl     = 31536000
  default_ttl = 31536000
  max_ttl     = 31536000

  parameters_in_cache_key_and_forwarded_to_origin {
    cookies_config      { cookie_behavior = "none" }
    headers_config      { header_behavior = "none" }
    query_strings_config { query_string_behavior = "none" }
    enable_accept_encoding_brotli = true
    enable_accept_encoding_gzip   = true
  }
}

resource "aws_cloudfront_cache_policy" "no_cache" {
  name        = "${var.bucket_name}-no-cache"
  min_ttl     = 0
  default_ttl = 0
  max_ttl     = 0

  parameters_in_cache_key_and_forwarded_to_origin {
    cookies_config      { cookie_behavior = "none" }
    headers_config      { header_behavior = "none" }
    query_strings_config { query_string_behavior = "none" }
    enable_accept_encoding_brotli = false
    enable_accept_encoding_gzip   = false
  }
}
```

Policy đầu tiên dành cho asset có tên chứa content hash và hỗ trợ Brotli/gzip. Policy thứ hai đặt TTL bằng 0 cho `index.html` và các đường dẫn không khớp cache behavior riêng.

### Tạo CloudFront distribution (`cloudfront.tf`)

```hcl
resource "aws_cloudfront_distribution" "blazor_app" {
  enabled             = true
  is_ipv6_enabled     = true
  default_root_object = "index.html"
  price_class         = var.cloudfront_price_class
  aliases             = var.custom_domain != "" ? [var.custom_domain] : []

  origin {
    domain_name              = aws_s3_bucket.blazor_app.bucket_regional_domain_name
    origin_id                = "s3-blazor-app"
    origin_access_control_id = aws_cloudfront_origin_access_control.blazor_app.id
  }

  default_cache_behavior {
    allowed_methods        = ["GET", "HEAD"]
    cached_methods         = ["GET", "HEAD"]
    target_origin_id       = "s3-blazor-app"
    cache_policy_id        = aws_cloudfront_cache_policy.no_cache.id
    compress               = true
    viewer_protocol_policy = "redirect-to-https"
  }

  ordered_cache_behavior {
    path_pattern           = "_framework/*"
    allowed_methods        = ["GET", "HEAD"]
    cached_methods         = ["GET", "HEAD"]
    target_origin_id       = "s3-blazor-app"
    cache_policy_id        = aws_cloudfront_cache_policy.immutable_assets.id
    compress               = true
    viewer_protocol_policy = "redirect-to-https"
  }

  custom_error_response {
    error_code            = 403
    response_code         = 200
    response_page_path    = "/index.html"
    error_caching_min_ttl = 0
  }

  custom_error_response {
    error_code            = 404
    response_code         = 200
    response_page_path    = "/index.html"
    error_caching_min_ttl = 0
  }

  viewer_certificate {
    cloudfront_default_certificate = var.custom_domain == ""
    acm_certificate_arn = var.custom_domain != "" ? aws_acm_certificate_validation.blazor_app[0].certificate_arn : null
    ssl_support_method  = var.custom_domain != "" ? "sni-only" : null
    minimum_protocol_version = var.custom_domain != "" ? "TLSv1.2_2021" : null
  }

  restrictions {
    geo_restriction { restriction_type = "none" }
  }
}
```

Hai custom error response chuyển lỗi 403/404 thành `/index.html` với HTTP 200. Blazor Router sau đó đọc URL và hiển thị component phù hợp, nhờ vậy deep link như `/counter` vẫn hoạt động.

### Khai báo biến và output

```hcl
# terraform.tfvars
aws_region             = "us-west-2"
bucket_name            = "my-blazor-wasm-app"
environment            = "prod"
cloudfront_price_class = "PriceClass_100"
custom_domain          = ""
route53_zone_id        = ""

output "cloudfront_distribution_id" {
  value = aws_cloudfront_distribution.blazor_app.id
}

output "app_url" {
  value = var.custom_domain != "" ? "https://${var.custom_domain}" : "https://${aws_cloudfront_distribution.blazor_app.domain_name}"
}
```

Khởi tạo và áp dụng hạ tầng:

```bash
cd infra
terraform init
terraform plan
terraform apply
```

Việc tạo CloudFront distribution thường mất khoảng 5–10 phút để cấu hình được truyền tới các edge location. Cần lưu `cloudfront_distribution_id` và `app_url` từ Terraform output.

## Bước 3: Triển khai ứng dụng

Script `deploy.sh` lần lượt build ứng dụng, upload lên S3 và tạo CloudFront invalidation.

```bash
./scripts/deploy.sh <bucket-name> <cloudfront-distribution-id>

dotnet publish src/BlazorApp/BlazorApp.csproj \
  -c Release \
  -o publish \
  --nologo
```

### Upload với MIME type và cache header phù hợp

```bash
BUCKET=$1

# Framework files không nén
aws s3 sync publish/wwwroot/_framework/ s3://$BUCKET/_framework/ \
  --delete --exclude "*.br" \
  --cache-control "max-age=31536000,immutable"

# Brotli WebAssembly
aws s3 sync publish/wwwroot/_framework/ s3://$BUCKET/_framework/ \
  --exclude "*" --include "*.wasm.br" \
  --content-encoding "br" --content-type "application/wasm" \
  --cache-control "max-age=31536000,immutable"

# Brotli JavaScript
aws s3 sync publish/wwwroot/_framework/ s3://$BUCKET/_framework/ \
  --exclude "*" --include "*.js.br" \
  --content-encoding "br" --content-type "application/javascript" \
  --cache-control "max-age=31536000,immutable"

# Dữ liệu ICU nén Brotli
aws s3 sync publish/wwwroot/_framework/ s3://$BUCKET/_framework/ \
  --exclude "*" --include "*.dat.br" \
  --content-encoding "br" --content-type "application/octet-stream" \
  --cache-control "max-age=31536000,immutable"

# Các static asset còn lại
aws s3 sync publish/wwwroot/ s3://$BUCKET/ \
  --delete --exclude "_framework/*" --exclude "index.html*" \
  --cache-control "max-age=31536000,immutable"

# index.html luôn phải mới
aws s3 cp publish/wwwroot/index.html s3://$BUCKET/index.html \
  --cache-control "no-cache, no-store, must-revalidate" \
  --content-type "text/html"
```

Các file `.br` cần được gán rõ `Content-Encoding` và `Content-Type`; AWS CLI không tự nhận diện chính xác tất cả các trường hợp này.

### Tạo cache invalidation

```bash
DIST_ID=$2

INVALIDATION_ID=$(aws cloudfront create-invalidation \
  --distribution-id $DIST_ID \
  --paths "/*" \
  --query 'Invalidation.Id' \
  --output text)

aws cloudfront wait invalidation-completed \
  --distribution-id $DIST_ID \
  --id $INVALIDATION_ID

echo "Deployed. Invalidation: $INVALIDATION_ID"
```

| Đường dẫn | Cache-Control | Lý do |
| --- | --- | --- |
| `_framework/*` | `max-age=31536000, immutable` | Tên tệp chứa content hash nên có thể cache một năm |
| `css/`, `lib/`, `images/` | `max-age=31536000, immutable` | Static asset; đổi tên tệp khi thay đổi nội dung |
| `index.html` | `no-cache, no-store, must-revalidate` | Luôn lấy bản mới để tham chiếu đúng deployment |

## Bước 4: Kiểm tra deployment

```bash
APP_URL="https://d1mw9a12s7eftc.cloudfront.net"

curl -sI "$APP_URL/index.html" | grep -i "cache-control"
# cache-control: no-cache, no-store, must-revalidate

curl -sI "$APP_URL/_framework/blazor.web.js" | grep -i "cache-control"
# cache-control: max-age=31536000,immutable

curl -sI "$APP_URL/_framework/dotnet.runtime.wasm" | grep -i "content-type"
# content-type: application/wasm

curl -sI "$APP_URL/counter" | grep -i "HTTP/"
# HTTP/2 200
```

Ngoài kiểm tra header, cần mở trực tiếp `https://<your-url>/counter` trong tab mới để xác nhận client-side routing hoạt động.

### Tùy chọn: sử dụng custom domain

```hcl
custom_domain   = "app.yourdomain.com"
route53_zone_id = "Z1234567890ABC"
```

Terraform sẽ tạo ACM certificate tại `us-east-1`, thêm DNS record xác thực, tạo Route 53 alias trỏ tới CloudFront và cập nhật distribution sử dụng certificate. Quá trình xác thực DNS có thể mất 5–30 phút.

## Dọn dẹp tài nguyên

S3 bucket phải được làm trống trước khi Terraform có thể xóa:

```bash
aws s3 rm s3://my-blazor-wasm-app --recursive

cd infra
terraform destroy

aws s3 ls | grep my-blazor-wasm-app
# Không trả về kết quả nếu bucket đã được xóa
```

Nếu versioning đã bật, cần xóa cả object versions và delete markers trước khi chạy `terraform destroy`.

## Kết luận

- `dotnet publish` tạo nội dung tĩnh có thể lưu trực tiếp trên S3.
- OAC giữ bucket ở trạng thái private và chỉ cho phép CloudFront distribution được chỉ định truy cập bằng request ký SigV4.
- `_framework/*` có thể cache một năm, còn `index.html` không được cache.
- Chuyển 403/404 về `index.html` giúp Blazor Router xử lý deep link.
- File `.br` cần `Content-Encoding` và `Content-Type` chính xác khi upload.

Mô hình tương tự có thể áp dụng cho React, Angular, Vue và Svelte bằng cách thay lệnh build tương ứng.

### Nguồn tham khảo

[Host a .NET Blazor WebAssembly App on Amazon S3 and Amazon CloudFront](https://aws.amazon.com/vi/blogs/dotnet/host-a-net-blazor-webassembly-app-on-amazon-s3-and-amazon-cloudfront/) — Shibu Thomas và Gopi Burla, .NET on AWS Blog, 09/07/2026.
