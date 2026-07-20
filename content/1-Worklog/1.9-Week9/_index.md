---
title: "Week 9 Worklog"
date: 2026-07-06
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

### Focus for the week

The objective for Week 9 was to have a working staging environment instead of testing each component separately. I defined the infrastructure with AWS CDK and prioritized authentication, the API, document upload, and asynchronous processing.

### Work completed

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 1 | **Infrastructure as Code and Authentication** <br> - Bootstrap CDK <br> - Define staging resources and IAM roles <br> - Create Cognito and Pre Sign-up Lambda | 06/07/2026 | 07/07/2026 | AWS CDK project |
| 2 | **Backend and Data Layer** <br> - Create API Gateway, Lambda, S3, and DynamoDB resources <br> - Connect the API to the data layer | 08/07/2026 | 09/07/2026 | SmartStudy backend |
| 3 | **Asynchronous Document Processing** <br> - Create SQS and DLQ <br> - Create Document Ingestion Lambda <br> - Connect S3, SQS, Lambda, and DynamoDB | 10/07/2026 | 11/07/2026 | Document ingestion flow |
| 4 | **Staging Deployment** <br> - Deploy the staging branch with Amplify <br> - Configure Cognito and API values <br> - Test the Ollama endpoint | 11/07/2026 | 12/07/2026 | Staging environment |

### Issues and resolution

Integration exposed more configuration problems than local testing, mainly in frontend environment values, IAM permissions between services, and asynchronous document states. I followed Lambda logs and queue messages through each stage. I also added the dead-letter queue so failed messages could be inspected separately instead of being retried without a clear explanation.

### End-of-week result

The staging environment connected Cognito, API Gateway, Lambda, S3, SQS, DynamoDB, and Amplify. Document processing ran asynchronously with a defined failure path, leaving the system ready for a separate production deployment and final testing.
