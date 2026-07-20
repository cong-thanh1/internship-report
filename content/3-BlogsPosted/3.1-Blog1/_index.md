---
title: "Hosting Blazor WebAssembly on S3 and CloudFront"
date: 2026-07-09
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

## Hosting a .NET Blazor WebAssembly App on Amazon S3 and Amazon CloudFront

### Overview

Blazor WebAssembly makes it possible to build an interactive web interface in C# and run it in the browser. After `dotnet publish`, the application consists of static HTML, CSS, JavaScript, and WebAssembly files. Since no application server is required to serve these files, Amazon S3 and Amazon CloudFront provide a suitable hosting model with little server administration.

The article goes beyond uploading a website to S3. It addresses several issues that appear when a Single Page Application (SPA) is deployed in practice: protecting the bucket, avoiding stale deployments, supporting direct access to client-side routes, and assigning the correct MIME headers to compressed files.

![Architecture for hosting Blazor WebAssembly with Amazon S3 and Amazon CloudFront](/images/3-BlogPosted/WebAssembly.png)

### Architecture

The request flow contains three main components:

1. The **Blazor WebAssembly** application is published as static content.
2. **Amazon S3** stores the build output in a private bucket with public access blocked.
3. **Amazon CloudFront** provides the public HTTPS endpoint, retrieves content from S3, and caches it at edge locations.

CloudFront reaches the bucket through **Origin Access Control (OAC)**. It signs S3 requests with SigV4, while the bucket policy grants object access only to the specified CloudFront distribution. Users therefore cannot bypass CloudFront and read the S3 objects directly, and S3 static website hosting does not need to be enabled.

For an optional custom domain, Amazon Route 53 can provide the alias record and AWS Certificate Manager can issue the TLS certificate used by CloudFront.

### Key design decisions

- **Origin Access Control (OAC) instead of Origin Access Identity (OAI):** OAC is the modern replacement for OAI. It uses SigV4 request signing, and the bucket policy restricts access to one specific CloudFront distribution ARN.
- **S3 static website hosting disabled:** CloudFront is the sole entry point, so the bucket blocks all public access.
- **Two cache policies:** Content-hashed files under `_framework/` are cached for one year. `index.html` is never cached, allowing new deployments to be picked up immediately.
- **No multithreading:** `WasmEnableThreads` remains disabled. Enabling it requires `Cross-Origin-Opener-Policy` and `Cross-Origin-Embedder-Policy`, which complicate OAuth pop-ups and CloudFront configuration. `async/await` is sufficient for an I/O-bound application.

### Prerequisites

| Tool | Version | Purpose |
| --- | --- | --- |
| .NET SDK | 10.0+ | Build the Blazor WASM application |
| Terraform | 1.6+ | Provision AWS infrastructure |
| AWS CLI | v2 | Upload files and create cache invalidations |
| Git | Any | Clone the repository |

The IAM user or role also needs the required S3 and CloudFront permissions:

```text
s3:CreateBucket, s3:PutObject, s3:DeleteObject, s3:ListBucket,
s3:PutBucketPolicy, s3:PutBucketVersioning, s3:PutBucketPublicAccessBlock

cloudfront:CreateDistribution, cloudfront:UpdateDistribution,
cloudfront:CreateInvalidation, cloudfront:CreateOriginAccessControl
```

Configure the AWS CLI before proceeding:

```bash
aws configure
```

## Step 1: Create the Blazor WebAssembly application

```bash
dotnet new blazorwasm -o src/BlazorApp --framework net10.0
dotnet run --project src/BlazorApp
# Open https://localhost:5XXX in a browser
```

The default template provides Home, Counter, and Weather pages that demonstrate routing, interactivity, and HTTP data fetching. No code changes are required for AWS hosting.

## Step 2: Provision the infrastructure with Terraform

```text
infra/
├── main.tf           # S3 bucket, public access block, versioning, bucket policy
├── cloudfront.tf     # CloudFront, OAC, cache policies, optional ACM and Route 53
├── variables.tf      # Input variables
├── outputs.tf        # Bucket name, CloudFront domain, distribution ID
└── terraform.tfvars  # Deployment values; do not commit secrets
```

#### S3 bucket (`main.tf`)

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

The `aws:SourceArn` condition grants access to exactly one distribution rather than every principal using the CloudFront service.

#### Origin Access Control and cache policies (`cloudfront.tf`)

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
    cookies_config       { cookie_behavior = "none" }
    headers_config       { header_behavior = "none" }
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
    cookies_config       { cookie_behavior = "none" }
    headers_config       { header_behavior = "none" }
    query_strings_config { query_string_behavior = "none" }
    enable_accept_encoding_brotli = false
    enable_accept_encoding_gzip   = false
  }
}
```

#### CloudFront distribution (`cloudfront.tf`)

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

#### Variables, outputs, and infrastructure deployment

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

```bash
cd infra
terraform init
terraform plan
terraform apply
```

CloudFront distribution creation usually takes 5–10 minutes. Save the `cloudfront_distribution_id` and `app_url` outputs for deployment.

## Step 3: Deploy the application

```bash
./scripts/deploy.sh <bucket-name> <cloudfront-distribution-id>

dotnet publish src/BlazorApp/BlazorApp.csproj \
  -c Release -o publish --nologo
```

The upload script must process pre-compressed files separately:

```bash
BUCKET=$1

aws s3 sync publish/wwwroot/_framework/ s3://$BUCKET/_framework/ \
  --delete --exclude "*.br" \
  --cache-control "max-age=31536000,immutable"

aws s3 sync publish/wwwroot/_framework/ s3://$BUCKET/_framework/ \
  --exclude "*" --include "*.wasm.br" \
  --content-encoding "br" --content-type "application/wasm" \
  --cache-control "max-age=31536000,immutable"

aws s3 sync publish/wwwroot/_framework/ s3://$BUCKET/_framework/ \
  --exclude "*" --include "*.js.br" \
  --content-encoding "br" --content-type "application/javascript" \
  --cache-control "max-age=31536000,immutable"

aws s3 sync publish/wwwroot/_framework/ s3://$BUCKET/_framework/ \
  --exclude "*" --include "*.dat.br" \
  --content-encoding "br" --content-type "application/octet-stream" \
  --cache-control "max-age=31536000,immutable"

aws s3 sync publish/wwwroot/ s3://$BUCKET/ \
  --delete --exclude "_framework/*" --exclude "index.html*" \
  --cache-control "max-age=31536000,immutable"

aws s3 cp publish/wwwroot/index.html s3://$BUCKET/index.html \
  --cache-control "no-cache, no-store, must-revalidate" \
  --content-type "text/html"
```

Create and wait for the CloudFront invalidation:

```bash
DIST_ID=$2
INVALIDATION_ID=$(aws cloudfront create-invalidation \
  --distribution-id $DIST_ID --paths "/*" \
  --query 'Invalidation.Id' --output text)

aws cloudfront wait invalidation-completed \
  --distribution-id $DIST_ID --id $INVALIDATION_ID
```

| Path | Cache-Control | Reason |
| --- | --- | --- |
| `_framework/*` | `max-age=31536000, immutable` | Content-hashed names are safe to cache for one year |
| `css/`, `lib/`, `images/` | `max-age=31536000, immutable` | Static assets are versioned through their file names |
| `index.html` | `no-cache, no-store, must-revalidate` | It must always refer to the latest deployment |

## Step 4: Verify the deployment

```bash
APP_URL="https://d1mw9a12s7eftc.cloudfront.net"
curl -sI "$APP_URL/index.html" | grep -i "cache-control"
curl -sI "$APP_URL/_framework/blazor.web.js" | grep -i "cache-control"
curl -sI "$APP_URL/_framework/dotnet.runtime.wasm" | grep -i "content-type"
curl -sI "$APP_URL/counter" | grep -i "HTTP/"
```

Open `https://<your-url>/counter` in a fresh browser tab to confirm that client-side routing works.

#### Optional custom domain

```hcl
custom_domain   = "app.yourdomain.com"
route53_zone_id = "Z1234567890ABC"
```

Terraform provisions an ACM certificate in `us-east-1`, DNS validation records, a Route 53 alias, and the CloudFront certificate configuration. DNS validation can take 5–30 minutes.

### Cleanup

```bash
aws s3 rm s3://my-blazor-wasm-app --recursive
cd infra
terraform destroy
aws s3 ls | grep my-blazor-wasm-app
```

When versioning is enabled, delete all object versions and delete markers before running `terraform destroy`.

### Main technical points

#### 1. Cache behavior should match the file type

Files under `_framework/*` contain content hashes in their names. A content change produces a new file name, which makes a one-year `max-age=31536000, immutable` cache safe for these assets.

`index.html` is different because it must point to the current deployment. Caching it for too long can leave a browser referring to old bundles. The deployment therefore assigns `no-cache, no-store, must-revalidate` to this file.

#### 2. SPA deep links require CloudFront handling

When a user opens a route such as `/counter` directly, S3 looks for an object at that path. It does not exist, so the private bucket returns 403; a missing path may also produce 404.

CloudFront Custom Error Responses map both errors to `/index.html` with an HTTP 200 response. Once the application loads, the Blazor Router reads the URL and renders the correct component. Direct navigation and browser refreshes on nested routes then work as expected.

#### 3. Brotli files need explicit content headers

The publish output includes `.br` files for WebAssembly, JavaScript, and runtime data. S3 does not infer every required header correctly. The deployment script uploads these groups separately and assigns `Content-Encoding: br` together with the appropriate `Content-Type`, such as `application/wasm` for WebAssembly.

Incorrect headers can allow the object to download while still preventing the browser from decoding or executing it properly.

#### 4. Infrastructure and application deployment are automated separately

The article uses **Terraform** to provision the S3 bucket, bucket policy, CloudFront distribution, OAC, and cache policies. A deployment script then performs three steps:

1. Publish the application in Release mode with `dotnet publish`.
2. Synchronize `publish/wwwroot` to S3 with the required headers.
3. Create a CloudFront invalidation so the new deployment reaches users.

Separating infrastructure provisioning from application deployment keeps AWS resource changes controlled while allowing the application to be released repeatedly.

### Conclusion

Static hosting involves more than placing files in a public bucket. A suitable deployment keeps S3 private, exposes the content through CloudFront, and configures caching and routing around the behavior of an SPA.

The same architecture can support React, Angular, Vue, or Svelte by replacing the build step. In each case, cache headers, MIME types, and deep-link behavior still need to be tested instead of applying one configuration to every object.

### Reference

[Host a .NET Blazor WebAssembly App on Amazon S3 and Amazon CloudFront](https://aws.amazon.com/blogs/dotnet/host-a-net-blazor-webassembly-app-on-amazon-s3-and-amazon-cloudfront/) — Shibu Thomas and Gopi Burla, .NET on AWS Blog, July 9, 2026.
