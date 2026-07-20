---
title: "Week 5 Worklog"
date: 2026-06-08
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Focus for the week

Week 5 covered storage. Rather than memorizing service names, I grouped the services into object, block, file, and hybrid storage and then matched them to practical use cases.

### Work completed

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 1 | **Amazon S3** <br> - Create and configure a bucket <br> - Upload and organize objects <br> - Review versioning, lifecycle rules, and access settings | 08/06/2026 | 10/06/2026 | [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023) |
| 2 | **Block and File Storage** <br> - Review EBS and snapshots <br> - Review EFS <br> - Compare block and shared file storage | 11/06/2026 | 12/06/2026 | [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023) |
| 3 | **Advanced and Hybrid Storage** <br> - Review Amazon FSx and AWS Storage Gateway <br> - Compare hybrid storage scenarios | 13/06/2026 | 14/06/2026 | [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023) |

Reference: Session 4, AWS Storage Services, in the [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023).

### Notes from the work

EBS and EFS were easy to confuse at first because both can be used by applications running on EC2. I compared them using three questions: what type of data is being stored, how many machines need access, and whether the data must be shared. For S3, I also noted that versioning and lifecycle configuration affect both recovery options and storage cost.

### End-of-week result

By the end of the week, I could explain a choice between S3, EBS, EFS, FSx, and Storage Gateway based on access patterns, data sharing, and the deployment environment rather than the service name alone.
