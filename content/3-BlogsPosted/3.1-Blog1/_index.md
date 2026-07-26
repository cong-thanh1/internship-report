---
title: "Hosting Blazor WebAssembly on S3 and CloudFront"
date: 2026-07-09
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

## Deploying a .NET Blazor WebAssembly Application on AWS with Amazon S3 and Amazon CloudFront

Blazor WebAssembly (WASM) makes it possible to build interactive web applications in C#. After compilation, the application consists of static files such as HTML, CSS, JavaScript, and WASM. Therefore, it can be stored on Amazon S3 and distributed through Amazon CloudFront without operating an application server.

In this model, Amazon S3 is the private origin, CloudFront is the CDN and public HTTPS entry point, and Terraform is used to manage the infrastructure.

![Architecture for hosting Blazor WebAssembly with Amazon S3 and Amazon CloudFront](/images/3-BlogPosted/WebAssembly.png)

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

The IAM user or role needs the following permissions:

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

Create a Blazor WASM project using .NET 10:

```bash
dotnet new blazorwasm -o src/BlazorApp --framework net10.0
```

The default template provides Home, Counter, and Weather pages that demonstrate routing, interactivity, and HTTP data fetching. No code changes are required for AWS hosting.

Run the application locally:

```bash
dotnet run --project src/BlazorApp
# Open https://localhost:5XXX in a browser
```

## Step 2: Provision the infrastructure with Terraform

The AWS resources are defined in the `infra/` directory. Terraform creates a versioned private S3 bucket, a CloudFront distribution using OAC, and two cache policies. If a custom domain is used, the configuration can also include an ACM certificate and a Route 53 record.

```text
infra/
├── main.tf           # S3 bucket, public access block, versioning, bucket policy
├── cloudfront.tf     # CloudFront, OAC, cache policies, optional ACM and Route 53
├── variables.tf      # Input variables
├── outputs.tf        # Bucket name, CloudFront domain, distribution ID
└── terraform.tfvars  # Deployment values; do not commit secrets
```

### Create the private S3 bucket (`main.tf`)

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

The `aws:SourceArn` condition restricts read access to the specified CloudFront distribution instead of allowing every distribution that belongs to the CloudFront service.

### Create OAC and cache policies (`cloudfront.tf`)

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

The first policy is used for assets whose names contain content hashes and supports Brotli/gzip. The second policy sets the TTL to 0 for `index.html` and paths that do not match a dedicated cache behavior.

### Create the CloudFront distribution (`cloudfront.tf`)

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

The two custom error responses map 403/404 errors to `/index.html` with an HTTP 200 response. The Blazor Router then reads the URL and displays the appropriate component, allowing deep links such as `/counter` to work.

### Declare variables and outputs

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

Initialize and apply the infrastructure:

```bash
cd infra
terraform init
terraform plan
terraform apply
```

CloudFront distribution creation usually takes 5–10 minutes. Save the `cloudfront_distribution_id` and `app_url` outputs for deployment.

## Step 3: Deploy the application

The `deploy.sh` script builds the application, uploads it to S3, and creates a CloudFront invalidation.

```bash
./scripts/deploy.sh <bucket-name> <cloudfront-distribution-id>

dotnet publish src/BlazorApp/BlazorApp.csproj \
  -c Release \
  -o publish \
  --nologo
```

### Upload with the appropriate MIME types and cache headers

```bash
BUCKET=$1

# Uncompressed framework files
aws s3 sync publish/wwwroot/_framework/ s3://$BUCKET/_framework/ \
  --delete --exclude "*.br" \
  --cache-control "max-age=31536000,immutable"

# Brotli-compressed WebAssembly
aws s3 sync publish/wwwroot/_framework/ s3://$BUCKET/_framework/ \
  --exclude "*" --include "*.wasm.br" \
  --content-encoding "br" --content-type "application/wasm" \
  --cache-control "max-age=31536000,immutable"

# Brotli-compressed JavaScript
aws s3 sync publish/wwwroot/_framework/ s3://$BUCKET/_framework/ \
  --exclude "*" --include "*.js.br" \
  --content-encoding "br" --content-type "application/javascript" \
  --cache-control "max-age=31536000,immutable"

# Brotli-compressed ICU data
aws s3 sync publish/wwwroot/_framework/ s3://$BUCKET/_framework/ \
  --exclude "*" --include "*.dat.br" \
  --content-encoding "br" --content-type "application/octet-stream" \
  --cache-control "max-age=31536000,immutable"

# Other static assets
aws s3 sync publish/wwwroot/ s3://$BUCKET/ \
  --delete --exclude "_framework/*" --exclude "index.html*" \
  --cache-control "max-age=31536000,immutable"

# index.html must always remain fresh
aws s3 cp publish/wwwroot/index.html s3://$BUCKET/index.html \
  --cache-control "no-cache, no-store, must-revalidate" \
  --content-type "text/html"
```

The `.br` files must be assigned explicit `Content-Encoding` and `Content-Type` values because the AWS CLI does not correctly detect every case.

### Create a cache invalidation

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

| Path | Cache-Control | Reason |
| --- | --- | --- |
| `_framework/*` | `max-age=31536000, immutable` | Content-hashed names are safe to cache for one year |
| `css/`, `lib/`, `images/` | `max-age=31536000, immutable` | Static assets are versioned through their file names |
| `index.html` | `no-cache, no-store, must-revalidate` | It must always refer to the latest deployment |

## Step 4: Verify the deployment

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

In addition to checking the headers, open `https://<your-url>/counter` directly in a new browser tab to confirm that client-side routing works.

### Optional: use a custom domain

```hcl
custom_domain   = "app.yourdomain.com"
route53_zone_id = "Z1234567890ABC"
```

Terraform provisions an ACM certificate in `us-east-1`, DNS validation records, a Route 53 alias, and the CloudFront certificate configuration. DNS validation can take 5–30 minutes.

## Clean up resources

The S3 bucket must be emptied before Terraform can delete it:

```bash
aws s3 rm s3://my-blazor-wasm-app --recursive

cd infra
terraform destroy

aws s3 ls | grep my-blazor-wasm-app
# No output is returned if the bucket has been deleted
```

If versioning is enabled, all object versions and delete markers must also be removed before running `terraform destroy`.

## Conclusion

- `dotnet publish` creates static content that can be stored directly on S3.
- OAC keeps the bucket private and only allows the specified CloudFront distribution to access it through SigV4-signed requests.
- `_framework/*` can be cached for one year, while `index.html` is not cached.
- Mapping 403/404 responses to `index.html` allows the Blazor Router to handle deep links.
- `.br` files require the correct `Content-Encoding` and `Content-Type` values when uploaded.

The same model can be applied to React, Angular, Vue, and Svelte by replacing the corresponding build command.

### Reference

[Host a .NET Blazor WebAssembly App on Amazon S3 and Amazon CloudFront](https://aws.amazon.com/blogs/dotnet/host-a-net-blazor-webassembly-app-on-amazon-s3-and-amazon-cloudfront/) — Shibu Thomas and Gopi Burla, .NET on AWS Blog, July 9, 2026.
