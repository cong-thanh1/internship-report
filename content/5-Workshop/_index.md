---
title: "Workshop"
date: 2026-07-19
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Build and Deploy SmartStudy AI

#### Overview

This workshop documents the implemented SmartStudy platform from source organization to deployed AWS resources and application results. SmartStudy follows a local-first Ports & Adapters design and uses serverless AWS services for its deployed frontend, authentication, APIs, storage, asynchronous processing, persistence, and monitoring.

The implemented architecture does not use a custom VPC, subnet, NAT Gateway, Internet Gateway, Route 53 domain, Amazon Bedrock, AWS Secrets Manager, or AWS CloudTrail. AI capabilities are provided by Ollama with Qwen 2.5 7B on a self-hosted local AI server through a Cloudflare relay component.

#### Current content

1. [SmartStudy overview](5.1-Workshop-overview/)
2. [Preparation and architecture decisions](5.2-Prerequiste/)
3. [Deploy and verify core AWS infrastructure](5.3-Infrastructure/)
4. [Deploy the frontend and configure authentication](5.4-Frontend-Authentication/)
5. [Upload, process, and manage documents](5.5-Document-Processing/)
6. [Integrate Ollama and the AI study workflow](5.6-AI-Integration/)
7. [Validate monitoring and project results](5.7-Testing-Results/)

The final sections document AI integration, monitoring, end-to-end results, and the operating status through the evaluation period.
