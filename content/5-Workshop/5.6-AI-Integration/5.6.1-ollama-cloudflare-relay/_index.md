---
title: "Ollama and Cloudflare Relay"
date: 2026-07-19
weight: 1
chapter: false
pre: " <b> 5.6.1. </b> "
---

#### Local AI service

The implemented AI service uses Ollama to serve Qwen 2.5 7B from a self-hosted local AI server. This approach was selected after the project account could not perform the required Amazon Bedrock operation.

The AI host is operationally independent from the AWS data layer. AWS continues to handle authentication, APIs, document storage, queues, application records, and monitoring, while the local server handles model inference.

#### Relay component

The repository contains a standalone `cloudflare-relay/` TypeScript module with its own source, tests, package configuration, and Wrangler configuration. It provides the relay layer associated with reaching the local model from the deployed workflow.

![Cloudflare relay source module](/images/5-Workshop/5.6-AI-Integration/cloudflare-relay.png)

*Figure 5.29: Dedicated Cloudflare relay module in the SmartStudy repository.*

The published workshop does not expose the relay URL, tokens, or local server address. Availability of AI-dependent features requires both the relay and the local Ollama service to remain reachable.
