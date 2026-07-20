---
title: "Manage and Summarize Documents"
date: 2026-07-19
weight: 2
chapter: false
pre: " <b> 5.5.2. </b> "
---

#### Document dashboard

After authentication, the SmartStudy dashboard displays the user's document library and processing state. The interface provides document statistics, search, upload access, and navigation to the study room and practice features.

![SmartStudy document dashboard](/images/5-Workshop/5.5-Document-Processing/document-dashboard.png)

*Figure 5.25: SmartStudy dashboard with ready documents in the personal library.*

#### Document management API

Protected document operations use a Bearer access token:

* `GET /api/v1/documents` lists documents and supports search, status, page, and limit parameters.
* `GET /api/v1/documents/{documentId}` returns metadata, page count, and detected chapters.
* `DELETE /api/v1/documents/{documentId}` removes the stored object and soft-deletes the document record.

![Document management API specification](/images/5-Workshop/5.5-Document-Processing/document-management-api.png)

*Figure 5.26: Authenticated API operations for document management.*

#### Summary workflow

SmartStudy supports both full-document and chapter summaries. A summary request can specify `scope: "full"` or `scope: "chapter"`; chapter requests also provide a chapter reference from the document metadata. Generated results are cached so that later requests can reuse an existing summary unless `forceRefresh` is requested.

![Document summary API specification](/images/5-Workshop/5.5-Document-Processing/summary-api.png)

*Figure 5.27: Summary endpoints, scopes, and cache behavior.*

The web interface presents the generated summary as key points followed by a concise explanation, allowing the learner to review the document before entering the AI study or quiz workflow.

![SmartStudy document summary result](/images/5-Workshop/5.5-Document-Processing/summary-result.png)

*Figure 5.28: Full-document summary displayed in SmartStudy.*

Together, the dashboard, management API, and summary result verify the document lifecycle from ready state to learning use.
