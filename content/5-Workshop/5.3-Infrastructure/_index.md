---
title: "Core AWS Infrastructure"
date: 2026-07-19
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

#### Overview

SmartStudy uses Infrastructure as Code instead of creating each AWS resource manually in the AWS Management Console. The infrastructure is defined in TypeScript with AWS CDK, synthesized into AWS CloudFormation templates, and deployed as separate staging and production stacks.

```text
AWS CDK source
      │ synthesize and deploy
      ▼
AWS CloudFormation
      ├── SmartStudy-staging-Foundation
      └── SmartStudy-production-Foundation
             ├── Amazon API Gateway
             ├── AWS Lambda
             ├── Amazon S3
             ├── Amazon SQS processing queues
             ├── Amazon DynamoDB
             └── Supporting IAM and monitoring resources
```

Because deployment is automated, this workshop documents the reproducible CDK workflow and verifies the resources created by CloudFormation. It does not recreate console wizards or present the final resource lists as manual creation steps.

#### Content

1. [Deploy infrastructure with AWS CDK and CloudFormation](5.3.1-deploy-with-cdk/)
2. [Verify deployed AWS resources](5.3.2-verify-resources/)
3. [Review IAM roles and policies](5.3.3-iam-roles-policies/)

The deployment evidence is organized into three layers: the CDK source definition, successful CloudFormation stacks, and the resulting AWS resources in staging and production.
