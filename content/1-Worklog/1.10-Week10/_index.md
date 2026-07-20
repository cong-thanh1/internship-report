---
title: "Week 10 Worklog"
date: 2026-07-13
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

### Focus for the week

In the final week, I separated production from staging, connected the full feature set, and ran the application as an end user would. The work included deployment, integration fixes, monitoring, and preparation for the final demonstration.

### Work completed

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 1 | **Production Infrastructure** <br> - Deploy Cognito, API Gateway, Lambda, S3, SQS/DLQ, and DynamoDB with CDK <br> - Review IAM access and environment separation | 13/07/2026 | 14/07/2026 | AWS CDK project |
| 2 | **Frontend Delivery and Security** <br> - Connect `main` to Amplify <br> - Deploy the production frontend <br> - Enable WAF <br> - Test Cognito registration and sign-in | 14/07/2026 | 15/07/2026 | Production environment |
| 3 | **Feature Integration** <br> - Complete document processing and conversation history <br> - Integrate Ollama <br> - Complete quiz, scoring, explanations, and DLQ testing | 15/07/2026 | 17/07/2026 | SmartStudy application |
| 4 | **Monitoring and Final Validation** <br> - Review CloudWatch logs and metrics <br> - Create alarms <br> - Fix integration and UI issues <br> - Run the final test and record the demo | 18/07/2026 | 19/07/2026 | CloudWatch and final demo |

### Issues and resolution

Moving from staging to production required another check of the Cognito values, API endpoint, and permissions for the resources in each environment. A wrong value could allow the frontend to load while authentication or API requests still failed. I tested sign-in, upload, processing status, conversations, and quizzes in order and checked the corresponding CloudWatch logs at each stage. I fixed interface issues after the backend flow was stable so the source of each problem remained clear.

### End-of-week result

By July 19, 2026, SmartStudy supported authentication, document management, AI-assisted study, quiz generation, scoring, and result explanations. The project used an Ollama model running on a self-managed local AI server. Production remained online through July 30 for evaluation and demonstration; resource cleanup was outside the scope of this worklog.
