---
title: "Preparation and Architecture Decisions"
date: 2026-07-19
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

#### Technical prerequisites

Before deploying SmartStudy, the development environment requires:

* A GitHub repository and a branch workflow for development, staging, and production.
* Node.js and npm for the React frontend, TypeScript backend, and AWS CDK project.
* Docker Engine with Docker Compose v2 for the local-first development environment.
* An AWS account with access to AWS Amplify, Amazon Cognito, Amazon API Gateway, AWS Lambda, Amazon S3, Amazon SQS, Amazon DynamoDB, Amazon CloudWatch, AWS IAM, and AWS CloudFormation.
* AWS CLI and AWS CDK configured for infrastructure deployment.
* A self-hosted local AI server running Ollama with Qwen 2.5 7B.

Sensitive values must be supplied through deployment environment configuration and must not be committed to GitHub. The project keeps a `.env.example` file to document required variable names while excluding actual credentials.

#### AWS Region

The SmartStudy staging and production environments are deployed in **US East (N. Virginia), `us-east-1`**. Using one Region keeps the AWS resources and CDK stacks consistent across both environments.

![AWS Region used by SmartStudy](/images/5-Workshop/5.2-Prerequisite/aws-region.png)

*Figure 5.3: The AWS Region selected for the SmartStudy deployment.*

#### Infrastructure as Code preparation

The `infra/` module contains the AWS CDK project. Its structure includes the CDK entry point, reusable stack definitions, tests, TypeScript configuration, and package dependencies. This approach allows staging and production resources to be deployed from the same infrastructure definitions while applying environment-specific names and configuration.

![SmartStudy AWS CDK infrastructure module](/images/5-Workshop/5.2-Prerequisite/github-cdk-infrastructure.png)

*Figure 5.4: AWS CDK project stored in the `infra` module.*

#### Architecture constraints and changes

The initial architecture was revised after validating service access and deployment constraints.

##### Custom domain

The team initially planned to register `smartstudylearning.com` through Amazon Route 53. Domain registration could not be completed, so the production application retained the default secure `amplifyapp.com` domain provided by AWS Amplify.

![Route 53 domain registration failure](/images/5-Workshop/5.2-Prerequisite/route53-domain-registration-failed.png)

*Figure 5.5: Route 53 could not complete the proposed domain registration.*

##### Managed AI service

Amazon Bedrock was evaluated as the original managed AI service. The project account received a `ValidationException` with the message `Operation not allowed`, so Bedrock was removed from the implemented architecture.

![Amazon Bedrock operation not allowed](/images/5-Workshop/5.2-Prerequisite/bedrock-operation-not-allowed.png)

*Figure 5.6: Amazon Bedrock access limitation encountered during evaluation.*

The final implementation uses **Ollama with Qwen 2.5 7B** on a self-hosted local AI server. The repository includes a dedicated `cloudflare-relay/` component that provides the relay layer between the deployed backend and the local AI service.

![Cloudflare AI relay module](/images/5-Workshop/5.2-Prerequisite/github-cloudflare-relay.png)

*Figure 5.7: Cloudflare relay module included in the SmartStudy repository.*

#### Final decisions

The following decisions define the implemented project scope:

* Use the default AWS Amplify domain instead of Route 53.
* Use Ollama and Qwen 2.5 7B instead of Amazon Bedrock.
* Use deployment environment configuration without AWS Secrets Manager.
* Use Amazon CloudWatch for application monitoring; AWS CloudTrail is outside the implemented scope.
* Maintain separate staging and production resources.
* Use AWS CDK and AWS CloudFormation for repeatable infrastructure deployment.

With these prerequisites and decisions established, the next section can proceed with the deployment of the core AWS infrastructure.
