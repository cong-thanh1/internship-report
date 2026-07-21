---
title: "Xây dựng AI gateway cho Amazon Bedrock với Amazon API Gateway"
date: 2025-11-19
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

Khi xây dựng ứng dụng generative AI, doanh nghiệp không chỉ cần quyền truy cập model mà còn phải quản lý tập trung việc xác thực, hạn mức sử dụng, cô lập tenant, chi phí, vòng đời API và lưu lượng bất thường. Kiến trúc tham chiếu này đặt Amazon API Gateway phía trước Amazon Bedrock để cung cấp các cơ chế kiểm soát đó, đồng thời giữ nguyên trải nghiệm sử dụng AWS SDK quen thuộc cho ứng dụng client.

![Kiến trúc tham chiếu của AI gateway](/images/3-BlogPosted/blog3.png)

*Hình 1: Kiến trúc tham chiếu của AI gateway.*

## Tổng quan kiến trúc

Gateway sử dụng các dịch vụ AWS được quản lý hoàn toàn và hoạt động trong suốt đối với client. Ứng dụng vẫn có thể dùng SDK như Boto3 để gọi Amazon Bedrock Runtime hoặc Knowledge Bases, nhưng gửi request tới endpoint của API Gateway thay vì gọi trực tiếp Bedrock.

Giải pháp gồm năm thành phần chính:

1. **Amazon Route 53 (tùy chọn)** ánh xạ domain riêng của doanh nghiệp tới gateway.
2. **Amazon API Gateway** tiếp nhận request và cung cấp authorization, throttling, quản lý vòng đời API, canary release, response streaming và tích hợp AWS WAF.
3. **Lambda authorizer** xác thực từng request. Bản triển khai của Dynatrace kiểm tra JWT, nhưng có thể thay bằng logic riêng, Amazon Cognito user pool hoặc cơ chế authorization khác của API Gateway.
4. **Lambda integration** đóng vai trò bộ chuyển tiếp request động. Function giữ nguyên header, body, action và parameter, ký request đi bằng AWS Signature Version 4 rồi chuyển tới endpoint Bedrock phù hợp.
5. **Amazon Bedrock** cung cấp foundation model, model inference, Knowledge Bases và các năng lực AI khác.

Khi client gọi gateway, integration function thu thập request gốc, áp dụng xác thực SigV4 bằng AWS credentials của function và chuyển request tới đúng dịch vụ Bedrock. Do bộ chuyển tiếp không chứa logic riêng cho từng operation của Bedrock, gateway có thể hỗ trợ API hiện tại và tương lai với ít thay đổi mã nguồn hơn.

## Triển khai bằng AWS CloudFormation

Hướng dẫn ban đầu triển khai một private gateway và tắt authorization để kiểm tra luồng request cốt lõi trước. Tại trang **Quick create stack** của CloudFormation, các parameter quan trọng gồm:

| Parameter | Giá trị kiểm thử ban đầu | Mục đích |
| --- | --- | --- |
| `EndpointType` | `PRIVATE` | Giới hạn truy cập trong VPC |
| `EnableAuthorizer` | `false` | Kiểm thử gateway trước khi thêm xác thực |
| `CustomDomain` | Để trống | Dùng domain mặc định của API Gateway |
| `HostedZoneId` | Để trống | Không cần khi chưa dùng custom domain |

Sau khi xác nhận CloudFormation được phép tạo IAM resource, tạo stack và chờ trạng thái `CREATE_COMPLETE`. Lưu các output `GatewayUrl`, `VpcId` và `ApiId` để sử dụng ở bước kiểm thử.

## Kiểm thử private gateway

Private API endpoint không thể được gọi trực tiếp từ Internet. Vì vậy, bài viết tạo một AWS CloudShell VPC environment trong VPC vừa triển khai, sử dụng một subnet khả dụng và default VPC security group.

Một Boto3 client factory dùng lại giao diện SDK thông thường, nhưng thay endpoint và giao việc ký request cho integration Lambda:

```python
import boto3
from botocore import UNSIGNED
from botocore.config import Config

def create_client(service_name, endpoint_url, jwt_token=None):
    client = boto3.client(
        service_name,
        endpoint_url=endpoint_url,
        config=Config(signature_version=UNSIGNED),
        region_name="",
    )

    def add_headers(model, params, **kwargs):
        params["headers"]["aws-endpoint-prefix"] = \
            model.service_model.endpoint_prefix
        if jwt_token:
            params["headers"]["Authorization"] = f"Bearer {jwt_token}"

    client.meta.events.register("before-call.*.*", add_headers)
    return client
```

Header `aws-endpoint-prefix` cho integration function biết dịch vụ Bedrock nào sẽ nhận request. Việc ký SigV4 ở client được tắt vì gateway chịu trách nhiệm xác thực và ký request trước khi chuyển tiếp.

### Streaming kết quả model

Tạo Bedrock Runtime client với gateway URL rồi gọi `converse_stream()` như bình thường:

```python
bedrock = create_client("bedrock-runtime", gateway_url)

response = bedrock.converse_stream(
    modelId="global.anthropic.claude-haiku-4-5-20251001-v1:0",
    messages=[{
        "role": "user",
        "content": [{"text": "Who invented the airplane?"}],
    }],
)

for event in response["stream"]:
    if "contentBlockDelta" in event:
        print(event["contentBlockDelta"]["delta"].get("text", ""),
              end="", flush=True)
```

API Gateway response streaming gửi output của model tới client ngay khi được tạo, thay vì chờ toàn bộ response hoàn tất.

### Truy vấn Knowledge Base

Client factory tương tự có thể tạo client `bedrock-agent-runtime` và gọi `retrieve()` với một Knowledge Base hiện có. Điều này cho thấy một gateway có thể chuyển tiếp trong suốt tới nhiều endpoint dịch vụ Bedrock mà không cần công khai nhiều entry point riêng biệt.

## Bật authorization

Sau khi kiểm thử luồng cơ bản, thay đoạn code placeholder trong Lambda authorizer bằng logic xác thực cần thiết. Một triển khai doanh nghiệp điển hình sẽ kiểm tra JWT và trả về IAM policy cho API Gateway. Hành vi mặc định nên từ chối request khi token không hợp lệ hoặc khi xảy ra exception.

Cập nhật CloudFormation stack và chuyển `EnableAuthorizer` thành `true`. Khi stack cập nhật xong, cần tạo API Gateway deployment mới cho stage `v1`; thay đổi cấu hình chưa có hiệu lực tại stage cho tới khi được deploy. Sau đó client truyền token qua tham số `jwt_token` của factory để thêm header `Authorization: Bearer <token>`.

## Các hướng mở rộng

- **Rate limiting và throttling:** Dùng usage plan và API key để kiểm soát lưu lượng, hạn chế vấn đề noisy neighbor trong hệ thống multi-tenant.
- **Private hoặc edge-optimized endpoint:** Chọn loại endpoint phù hợp với truy cập nội bộ hoặc yêu cầu hiệu năng toàn cầu.
- **Quản lý vòng đời và canary release:** Duy trì nhiều phiên bản API và phát hành thay đổi dần dần bằng stage và canary deployment.
- **Tích hợp AWS WAF:** Bổ sung rule giúp bảo vệ API trước các kiểu khai thác phổ biến và lưu lượng không mong muốn.
- **Cache prompt và response:** Cache các request lặp lại phù hợp để giảm độ trễ và chi phí gọi model.
- **Lọc nội dung:** Bổ sung kiểm tra riêng của tổ chức đối với PII hoặc dữ liệu nhạy cảm tại integration layer, kết hợp với Amazon Bedrock Guardrails.

## Những điểm cần ghi nhớ

Pattern AI gateway tách việc sử dụng model khỏi lớp quản trị. Ứng dụng client vẫn giữ mô hình lập trình bằng AWS SDK, trong khi API Gateway và Lambda tập trung hóa authorization, quota, routing, ký request, streaming và các cơ chế kiểm soát vận hành.

Thiết kế chuyển tiếp động còn giúp giảm công sức bảo trì: gateway giữ nguyên API operation gốc thay vì triển khai riêng từng tính năng Bedrock. Nhờ đó, tổ chức có thể tiếp nhận các năng lực Bedrock mới mà vẫn duy trì một lớp truy cập ổn định và được quản trị tập trung.

---

**Nguồn và credit:** Thomas Natschläger, Philipp Ushiromiya, Simone Pomata và Mohan Gowda Purushothama, [Building an AI gateway to Amazon Bedrock with Amazon API Gateway – AWS Architecture Blog](https://aws.amazon.com/vi/blogs/architecture/building-an-ai-gateway-to-amazon-bedrock-with-amazon-api-gateway/), ngày 19/11/2025.
