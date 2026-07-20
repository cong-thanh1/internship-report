---
title: "AI Integration"
date: 2026-07-19
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

#### Overview

SmartStudy uses Ollama with Qwen 2.5 7B as its AI provider. The model runs on a self-hosted local AI server rather than Amazon Bedrock. A dedicated Cloudflare relay component connects the deployed backend workflow to the local AI service.

```text
SmartStudy frontend
        │
        ▼
API Gateway → API Lambda
                    │ AI request with retrieved context
                    ▼
             Cloudflare relay
                    │
                    ▼
        Self-hosted local AI server
             Ollama + Qwen 2.5 7B
                    │ generated response
                    └──────────────► SmartStudy
```

The AI layer is used for document-grounded study assistance, summaries, quiz generation, and answer explanations. Document storage and retrieval remain separate from the model host.

#### Content

1. [Ollama and Cloudflare relay](5.6.1-ollama-cloudflare-relay/)
2. [AI study room](5.6.2-ai-study-room/)
