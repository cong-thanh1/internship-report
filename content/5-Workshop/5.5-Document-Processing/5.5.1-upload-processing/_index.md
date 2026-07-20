---
title: "Upload and Process Documents"
date: 2026-07-19
weight: 1
chapter: false
pre: " <b> 5.5.1. </b> "
---

#### Presigned upload workflow

Document upload uses an authenticated three-step process:

1. The frontend calls `POST /api/v1/documents/upload-url` with the document title, `application/pdf` content type, and file size. PDF files are limited to 50 MiB.
2. The browser uploads the file directly to the presigned object-storage URL with the headers returned by the API.
3. The frontend calls `POST /api/v1/documents/{documentId}/complete`. The API validates object metadata, changes the document state to `processing`, and submits a processing job.

Direct browser-to-storage upload prevents the API function from carrying the PDF payload and keeps the upload workflow efficient.

#### Asynchronous ingestion

In the deployed AWS environment, Amazon SQS carries document-processing jobs to the Document Ingestion Lambda. The ingestion workflow:

* Downloads the PDF from Amazon S3.
* Extracts text with `pdf-parse`.
* Detects basic chapter headings.
* Splits text using a default maximum of 700 tokens per chunk and an 80-token overlap.
* Calls the configured embedding component.
* Stores document metadata and chunks in DynamoDB.
* Changes a successfully processed document to `ready`; documents without extractable text are marked `failed`.

![Document upload and processing specification](/images/5-Workshop/5.5-Document-Processing/upload-processing-flow.png)

*Figure 5.24: Presigned upload, queue submission, PDF extraction, and chunking workflow.*

If processing fails, the application records the failure state for investigation and CloudWatch provides the relevant operational logs.
