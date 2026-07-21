---
title: "Building an AI Gateway to Amazon Bedrock with Amazon API Gateway"
date: 2025-11-19
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

Enterprises building generative AI applications need more than model access. They also need centralized authorization, quotas, tenant isolation, cost controls, API lifecycle management, and protection against abusive traffic. This reference architecture places Amazon API Gateway in front of Amazon Bedrock to provide those controls while preserving the familiar AWS SDK experience for client applications.

![Reference architecture of the AI gateway](/images/3-BlogPosted/blog3.png)

*Figure 1: Reference architecture of the AI gateway.*

## Architecture overview

The gateway uses fully managed AWS services and remains transparent to clients. Applications can continue using SDKs such as Boto3 to call Amazon Bedrock Runtime or Knowledge Bases, but send requests to the API Gateway endpoint instead of calling Bedrock directly.

The solution contains five main components:

1. **Amazon Route 53 (optional)** maps a company-specific domain to the gateway.
2. **Amazon API Gateway** receives requests and provides authorization, throttling, API lifecycle management, canary releases, response streaming, and integration with AWS WAF.
3. **Lambda authorizer** validates each request. The Dynatrace implementation validates JWTs, but the authorizer can use custom logic, Amazon Cognito user pools, or another API Gateway authorization mechanism.
4. **Lambda integration** acts as a dynamic request forwarder. It preserves headers, body, action, and parameters, signs the outgoing request with AWS Signature Version 4, and forwards it to the appropriate Bedrock endpoint.
5. **Amazon Bedrock** provides foundation models, model inference, Knowledge Bases, and other AI capabilities.

When a client calls the gateway, the integration function captures the original request, applies SigV4 authentication with its AWS credentials, and forwards the request to the correct Bedrock service. Because the forwarder does not contain logic for individual Bedrock operations, it can support current and future APIs with fewer gateway code changes.

## Deploying with AWS CloudFormation

The walkthrough initially deploys a private gateway with authorization disabled so that the core request path can be tested first. On the CloudFormation **Quick create stack** page, the key parameters are:

| Parameter | Value for initial testing | Purpose |
| --- | --- | --- |
| `EndpointType` | `PRIVATE` | Restrict access to the VPC |
| `EnableAuthorizer` | `false` | Test the gateway before adding authentication |
| `CustomDomain` | Empty | Use the default API Gateway domain |
| `HostedZoneId` | Empty | Not required without a custom domain |

After acknowledging that CloudFormation can create IAM resources, create the stack and wait for `CREATE_COMPLETE`. Save the `GatewayUrl`, `VpcId`, and `ApiId` values from the stack outputs.

## Testing the private gateway

A private API endpoint cannot be called from the public internet. The article therefore creates an AWS CloudShell VPC environment in the deployed VPC, using an available subnet and the default VPC security group.

A reusable Boto3 client factory keeps the normal SDK interface while changing the endpoint and leaving request signing to the integration Lambda:

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

The `aws-endpoint-prefix` header tells the integration function which Bedrock service should receive the request. Client-side SigV4 signing is disabled because authentication and signing are handled inside the gateway.

### Streaming model inference

Create a Bedrock Runtime client with the gateway URL and call `converse_stream()` as usual:

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

API Gateway response streaming delivers model output to the caller as it is generated instead of waiting for the entire response.

### Querying a Knowledge Base

The same client factory can create a `bedrock-agent-runtime` client and call `retrieve()` against an existing Knowledge Base. This demonstrates that one gateway can transparently route requests to multiple Bedrock service endpoints without exposing separate public entry points.

## Enabling authorization

After basic testing, replace the placeholder Lambda authorizer with the required validation logic. A typical enterprise implementation validates a JWT and returns an API Gateway IAM policy. The default behavior should deny the request when validation fails or an exception occurs.

Update the CloudFormation stack and set `EnableAuthorizer` to `true`. After the stack update finishes, create a new API Gateway deployment for stage `v1`; configuration changes do not become active at the stage until they are deployed. The client can then pass a token through the factory's `jwt_token` argument, which adds an `Authorization: Bearer <token>` header.

## Enhancement options

- **Rate limiting and throttling:** Use usage plans and API keys to control request volume and reduce noisy-neighbor problems in multi-tenant systems.
- **Private or edge-optimized endpoints:** Select an endpoint type that matches internal access or global performance requirements.
- **Lifecycle management and canary releases:** Maintain multiple API versions and introduce changes gradually with stages and canary deployments.
- **AWS WAF integration:** Add rules that help protect the API from common exploits and unwanted traffic.
- **Prompt and response caching:** Cache suitable repeated requests to reduce latency and model invocation costs.
- **Content filtering:** Add organization-specific checks for PII or other sensitive data in the integration layer, alongside Amazon Bedrock Guardrails.

## Key takeaways

The AI gateway pattern separates model consumption from governance. Client applications retain the standard AWS SDK programming model, while API Gateway and Lambda centralize authorization, quotas, routing, request signing, streaming, and operational controls.

Its dynamic forwarding design also reduces maintenance: the gateway preserves the original API operation instead of implementing every Bedrock feature separately. Organizations can therefore adopt new Bedrock capabilities while keeping a stable, governed access layer.

---

**Source and credit:** Thomas Natschläger, Philipp Ushiromiya, Simone Pomata, and Mohan Gowda Purushothama, [Building an AI gateway to Amazon Bedrock with Amazon API Gateway – AWS Architecture Blog](https://aws.amazon.com/blogs/architecture/building-an-ai-gateway-to-amazon-bedrock-with-amazon-api-gateway/), November 19, 2025.
