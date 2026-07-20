---
title: "Week 8 Worklog"
date: 2026-06-29
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Focus for the week

Week 8 turned the design into an application skeleton. I worked on the architecture, backend, and frontend in parallel, but kept them aligned to the same user flow so that the two sides would not define incompatible data.

### Work completed

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 1 | **Complete the AWS Architecture** <br> - Refine the diagram <br> - Define upload and processing flows <br> - Map AWS services to components | 29/06/2026 | 01/07/2026 | AWS architecture design |
| 2 | **Backend Implementation** <br> - Design document, conversation, quiz, and result endpoints <br> - Define stored data and AI integration points | 02/07/2026 | 03/07/2026 | Backend tasks |
| 3 | **Frontend Implementation** <br> - Build upload, study room, quiz, and result screens <br> - Match requests and responses with the API | 04/07/2026 | 05/07/2026 | Frontend tasks |

### Notes from the work

While matching the screens to the API, I found that several field names and processing states were inconsistent. I traced one document from upload through the point where it became available for questions, then revised the shared data structure. Doing this before the full backend deployment reduced the amount of interface rework later.

### End-of-week result

The project finished the week with a more detailed architecture, an API skeleton, and its main screens. The features were not yet treated as complete; the useful result was that the components were defined well enough to deploy and integrate in staging during Week 9.
