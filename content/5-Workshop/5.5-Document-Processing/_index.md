---
title: "Document Processing"
date: 2026-07-19
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

#### Overview

SmartStudy processes uploaded PDF documents asynchronously so that long-running extraction and chunking tasks do not block the user-facing API. The application follows the same business workflow in local and AWS environments through replaceable infrastructure adapters.

```text
React frontend
      │ request a presigned upload URL
      ▼
API Gateway → API Lambda
      │
      ├── metadata → DynamoDB
      └── presigned URL → browser uploads PDF → Amazon S3
                                      │ upload completed
                                      ▼
                                Amazon SQS
                                      │
                                      ▼
                         Document Ingestion Lambda
                           ├── extract PDF text
                           ├── detect headings
                           ├── split document chunks
                           └── persist results in DynamoDB
```

The deployed workflow uses document storage, an Amazon SQS processing queue, a document-ingestion function, and document-related tables.

#### Content

1. [Upload and process documents](5.5.1-upload-processing/)
2. [Manage and summarize documents](5.5.2-document-management-summary/)
