---
title: "Frontend Deployment and Authentication"
date: 2026-07-19
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

#### Overview

SmartStudy delivers its React frontend through AWS Amplify and authenticates users with Amazon Cognito. The deployment does not use a custom VPC, subnet, NAT Gateway, or Internet Gateway. Amplify, Cognito, API Gateway, and Lambda are used as managed regional services.

The delivery and authentication flow is:

```text
GitHub branch
      │ push / merge
      ▼
AWS Amplify Hosting
      │
      ├── Amazon Cognito: registration, sign-in, and tokens
      └── Amazon API Gateway: authenticated backend requests
```

#### Content

1. [GitHub branch workflow](5.4.1-github-workflow/)
2. [AWS Amplify deployment](5.4.2-amplify-deployment/)
3. [Amazon Cognito authentication](5.4.3-cognito-authentication/)
4. [Authentication result](5.4.4-authentication-result/)
