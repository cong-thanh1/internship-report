---
title: "Verify Deployed Resources"
date: 2026-07-19
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---

#### Verification approach

After the CloudFormation stacks reached a successful state, the resulting resources were verified in the AWS Management Console. The following screenshots show the final deployed state; they are not manual resource-creation steps.

#### Amazon API Gateway

Two Regional HTTP APIs were provisioned, corresponding to the SmartStudy staging and production environments. These APIs provide the public backend entry point used by the deployed frontend.

![SmartStudy HTTP APIs](/images/5-Workshop/5.3-Infrastructure/api-gateway.png)

*Figure 5.10: Regional HTTP APIs created for staging and production.*

#### AWS Lambda

Six Node.js 22.x Lambda functions were deployed:

* An API function for each environment.
* A Document Ingestion function for each environment.
* A Pre Sign-up function for each environment.

The API functions handle application requests, the ingestion functions process uploaded documents, and the pre-sign-up functions support the Cognito registration workflow.

![SmartStudy Lambda functions](/images/5-Workshop/5.3-Infrastructure/lambda-functions.png)

*Figure 5.11: Lambda functions deployed for staging and production.*

#### Amazon S3

The account contains a CDK asset bucket and two SmartStudy document buckets. The staging and production document buckets keep uploaded learning materials separated by environment.

![SmartStudy S3 buckets](/images/5-Workshop/5.3-Infrastructure/s3-buckets.png)

*Figure 5.12: CDK asset bucket and environment-specific document buckets.*

#### Amazon SQS

The deployed Amazon SQS queues support asynchronous document processing and decouple the user-facing upload request from the longer document-ingestion workload.

![SmartStudy SQS queues](/images/5-Workshop/5.3-Infrastructure/sqs-queues.png)

*Figure 5.13: Amazon SQS queues supporting document processing.*

#### Amazon DynamoDB

SmartStudy uses nine logical tables per environment:

* AI jobs.
* Attempts.
* Conversations.
* Conversation messages.
* Document chunks.
* Documents.
* Exams.
* Quizzes.
* Summaries.

The production and staging resources use distinct names so that test data and operations do not affect the production application.

![SmartStudy DynamoDB tables - first view](/images/5-Workshop/5.3-Infrastructure/dynamodb-tables-1.png)

*Figure 5.14: Production tables and the first group of staging tables.*

![SmartStudy DynamoDB tables - second view](/images/5-Workshop/5.3-Infrastructure/dynamodb-tables-2.png)

*Figure 5.15: Remaining staging DynamoDB tables.*

#### Verification result

The service inventories match the staging and production resources expected from the SmartStudy foundation stacks. Detailed integration flows—authentication, document processing, AI access, and monitoring—are presented separately in the following workshop sections.
