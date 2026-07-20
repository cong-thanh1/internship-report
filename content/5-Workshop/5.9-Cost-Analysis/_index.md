---
title: "Cost Analysis"
date: 2026-07-20
weight: 9
chapter: false
pre: " <b> 5.9. </b> "
---

# Operational Cost Analysis

The cost data was collected from AWS Cost Explorer for services included in the SmartStudy architecture in the US East (N. Virginia) Region. It represents the development, testing, and demonstration period with low usage volume.

## Cost by service

| Service | Recorded cost (USD) | Active period |
| --- | ---: | ---: |
| AWS Lambda | $0.0000419621 | 8 days |
| Amazon S3 | $0.0000003024 | 13 days |
| AWS Amplify | ≈ $0.0000000001 | 8 days |
| Amazon CloudWatch | $0.00 | 19 days |
| Amazon SQS | $0.00 | 8 days |
| Amazon Cognito | $0.00 | 6 days |
| Amazon API Gateway | $0.00 | 8 days |
| AWS CloudFormation | $0.00 | 8 days |
| Amazon DynamoDB | ≈ $0.00 | 8 days |

The total directly recorded positive cost for this period was approximately **$0.0000422646**, equivalent to **$0.00 when rounded to the nearest cent**.

Very small billing adjustments and negative values are not treated as operational service costs and were excluded from the table to avoid confusion. Services that are not part of the final SmartStudy architecture were also excluded from the calculation.

## Assessment

The recorded cost was extremely low because the system was used only for development and demonstration with a limited number of users, requests, and documents. Most services recorded no charge during the measured period.

These results represent the demonstration environment and should not be considered a production cost forecast. Costs will change as the number of users, documents, API requests, storage duration, and log volume increase. The self-hosted local AI server running Ollama is outside AWS Cost Explorer and is therefore not included in the AWS cost table.

AWS promotional credits are applied at the account level. Credits reduce the amount payable but do not change the amount of AWS resources consumed by SmartStudy.
