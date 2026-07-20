---
title: "SmartStudy Overview"
date: 2026-07-19
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

#### Introduction

SmartStudy AI is a document-based learning platform that helps students organize learning materials and study more effectively. The application combines a web interface, a serverless AWS backend, asynchronous document processing, and a self-hosted local AI server.

The completed application supports the following workflows:

* User registration and sign-in.
* PDF upload and document management.
* Text extraction and document chunking.
* Document summaries and AI-assisted question answering.
* Responses grounded in retrieved document content with citations.
* Automatic quiz generation, answer submission, scoring, and result review.

#### Project approach

SmartStudy follows a **local-first Ports & Adapters architecture**. Business logic is separated from infrastructure-specific implementations so that the application can use local services during development and AWS services during deployment.

The local development environment is started with Docker Compose and uses MinIO for S3-compatible object storage, PostgreSQL with pgvector for relational and vector data, and Redis with BullMQ for background jobs. In the deployed environment, the application uses AWS Amplify, Amazon Cognito, Amazon API Gateway, AWS Lambda, Amazon S3, Amazon SQS, Amazon DynamoDB, and Amazon CloudWatch. The AI capability is provided by Ollama with Qwen 2.5 7B on a self-hosted local AI server and is connected through the project's Cloudflare relay component.

#### Repository organization

The project is maintained in a single GitHub repository containing the frontend, backend, infrastructure definitions, documentation, deployment scripts, and the Cloudflare relay component.

![SmartStudy GitHub repository](/images/5-Workshop/5.1-Workshop-overview/github-repository-overview.png)

*Figure 5.1: SmartStudy repository and its main implementation modules.*

The main source structure includes:

* `frontend/`: React web application.
* `backend/`: Node.js and TypeScript API and document-processing worker.
* `infra/`: AWS CDK infrastructure definitions.
* `cloudflare-relay/`: Relay component used to connect to the local AI service.
* `docs/`: Technical specifications and development conventions.
* `scripts/` and `tools/`: Setup, deployment, and project-support utilities.
* `docker-compose.yml`: Local development infrastructure.

![SmartStudy repository structure](/images/5-Workshop/5.1-Workshop-overview/repository-structure.png)

*Figure 5.2: Core repository structure documented by the project.*

#### Deployment overview

SmartStudy maintains separate staging and production environments in the AWS `us-east-1` Region. Changes are integrated and validated through GitHub branches before being merged into `main`. AWS Amplify builds and publishes the production frontend, while AWS CDK and AWS CloudFormation provision the backend resources.

The following sections describe the preparation, deployment, document-processing workflow, AI integration, monitoring, and end-to-end validation of SmartStudy.
