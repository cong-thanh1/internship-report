---
title: "Proposal"
date: 2026-06-22
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# SmartStudy AI
## A Serverless Learning Assistant for Document-Based Study

### 1. Executive Summary

SmartStudy AI is a web-based learning platform that helps students learn from their own PDF materials. Users can create an account, upload and manage documents, ask questions grounded in document content, generate practice quizzes, submit answers, and review scores and explanations.

The application uses a serverless AWS architecture for web hosting, authentication, APIs, storage, asynchronous processing, persistence, and monitoring. Because Amazon Bedrock was unavailable to the project account, the AI workload was moved to a Qwen 2.5 7B model running with Ollama on a self-hosted local AI server. A Cloudflare relay component appears in the project repository to support connectivity to the local AI service; its detailed implementation will be confirmed during final technical documentation.

### 2. Problem Statement

Students often work with long and fragmented learning materials. Reading, summarizing, creating revision questions, and tracking learning results manually require considerable time. General-purpose chatbots may also answer beyond the supplied materials, making it difficult to verify whether a response is grounded in the learner's document.

SmartStudy addresses these problems by providing one workspace for:

* Uploading and organizing PDF learning materials.
* Asking questions based on an uploaded document.
* Receiving responses with references to relevant document content.
* Automatically generating practice quizzes.
* Recording conversations, attempts, scores, and explanations.

### 3. Proposed Solution

SmartStudy provides the following main workflows:

1. A user registers or signs in through Amazon Cognito.
2. The web application requests backend operations through Amazon API Gateway.
3. Documents are stored in Amazon S3 and submitted to an Amazon SQS queue for asynchronous processing.
4. AWS Lambda processes documents and stores metadata, chunks, conversations, quizzes, and attempts in Amazon DynamoDB.
5. AI requests are forwarded to the locally hosted Ollama model through the project's relay mechanism.
6. Amazon CloudWatch collects operational logs, metrics, and alarms.

### 4. Preliminary Solution Architecture

#### Initial proposed architecture

The following diagram records the architecture proposed before implementation. It is retained to show the original design direction; some services shown in it were later removed or replaced because of service availability and operational constraints.

![Initial proposed architecture of SmartStudy AI](/internship-report/images/2-Proposal/initial-proposed-architecture.jpg)

*Figure 1: Initial proposed architecture before implementation changes. This diagram does not represent the final deployed system.*

#### Revised architecture

![Final deployed architecture of SmartStudy AI](/internship-report/images/2-Proposal/final-deployed-architecture.png)

*Figure 2: Final deployed architecture, including the AWS serverless workload and the self-hosted Ollama AI environment.*

The project maintains separate staging and production resources in the `us-east-1` Region. The GitHub staging branch is used for integration work, while approved changes are merged into the main branch and deployed to production through AWS Amplify.

### 5. Services and Technologies

#### AWS services

* **AWS Amplify Hosting:** Builds and deploys the web frontend from GitHub.
* **Amazon Cognito:** Manages user registration, authentication, and tokens.
* **Amazon API Gateway:** Exposes the backend HTTP API.
* **AWS Lambda:** Handles API operations, document ingestion, and pre-sign-up logic.
* **Amazon S3:** Stores uploaded PDF documents and AWS CDK assets.
* **Amazon SQS:** Decouples document upload from asynchronous document processing.
* **Amazon DynamoDB:** Stores documents, document chunks, conversations, messages, quizzes, exams, attempts, summaries, and AI job state.
* **Amazon CloudWatch:** Provides logs, metrics, and alarms for Lambda and SQS.
* **AWS IAM:** Controls access between users and AWS services.
* **AWS CDK and AWS CloudFormation:** Define and deploy repeatable staging and production infrastructure.

#### Supporting technologies

* **GitHub:** Source control, branch collaboration, and the source for Amplify continuous deployment.
* **Ollama:** Serves the language model from a self-hosted local AI server.
* **Qwen 2.5 7B:** Local language model used by the AI workflow.
* **Cloudflare relay:** Provides a relay layer associated with access to the locally hosted AI service. The exact relay mechanism remains to be verified from the deployment configuration.

### 6. Architecture Changes and Constraints

| Initial proposal | Implemented approach | Reason |
| --- | --- | --- |
| Route 53 custom domain | Default `amplifyapp.com` domain | The team could not complete the domain purchase. |
| Amazon Bedrock | Ollama with Qwen 2.5 7B on a self-hosted local AI server | The required Bedrock operation was not allowed for the project account. |
| AWS Secrets Manager | Deployment environment configuration | Secrets Manager was not used in the final implementation. |
| AWS CloudTrail | Amazon CloudWatch monitoring | CloudTrail was not included in the implemented scope. |
| Direct/synchronous document processing | Amazon SQS processing queues | Asynchronous processing isolates long-running ingestion from the user-facing API. |
| Single environment | Separate staging and production resources | Separation reduces the risk of testing changes against production data and services. |

### 7. Implementation Plan

* **Planning:** Define the problem, core features, user flows, and initial AWS architecture.
* **Architecture revision:** Validate service availability and replace unavailable components.
* **Application development:** Implement the frontend, backend APIs, document processing, AI study room, and quiz workflows.
* **Infrastructure deployment:** Provision staging and production resources through AWS CDK and CloudFormation.
* **Integration:** Connect GitHub to Amplify and integrate Cognito, API Gateway, Lambda, S3, SQS, DynamoDB, and the local AI service.
* **Validation:** Test registration, upload, processing, question answering, quiz generation, scoring, and result review.

### 8. Risks and Mitigation

| Risk | Impact | Mitigation |
| --- | --- | --- |
| The local AI server or Ollama service is unavailable | AI study and quiz functions may become unavailable | Keep the server available during demonstrations, monitor connectivity, and document a future managed-AI migration path. |
| Relay or Internet connectivity is unstable | AI requests may be delayed or fail | Apply timeouts, error handling, and clear retry feedback. |
| Uploaded document processing fails | Documents may remain unavailable for study | Use SQS, processing-status tracking, application error handling, and CloudWatch monitoring. |
| Staging changes affect production | Service disruption or data inconsistency | Maintain separate staging and production resources and deploy through reviewed GitHub merges. |
| AWS usage exceeds expectations | Unexpected project cost | Monitor resource usage and retain only the resources required for evaluation. |

### 9. Expected Outcomes

* A deployed SmartStudy web application accessible through AWS Amplify.
* Secure user registration and sign-in through Amazon Cognito.
* Reliable PDF upload and asynchronous document ingestion.
* Document-grounded AI question answering and quiz generation.
* Persistent learning history and result review.
* Repeatable AWS infrastructure for staging and production.
* A foundation for replacing the local Ollama service with a managed AI platform in a future version.

This proposal is based on the deployed AWS resources, the completed application demonstration, and the available GitHub repository overview. The detailed Cloudflare-to-Ollama connection will be refined after the runtime configuration of the local AI server is verified.
