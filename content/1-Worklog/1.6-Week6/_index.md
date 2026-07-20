---
title: "Week 6 Worklog"
date: 2026-06-15
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Focus for the week

This week I worked more closely with IAM. My goal was to understand how permissions are assigned to people and AWS services, and to become comfortable reading policies instead of only attaching permissions through the Console.

### Work completed

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 1 | **AWS Security and IAM** <br> - Create IAM users and groups <br> - Attach managed policies <br> - Test permissions in the Console | 15/06/2026 | 17/06/2026 | [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023) |
| 2 | **IAM Policies and Roles** <br> - Review policy structure <br> - Compare managed policy types <br> - Create roles for AWS services | 18/06/2026 | 19/06/2026 | [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023) |
| 3 | **Account Security Review** <br> - Review MFA and root protection <br> - Review access keys and least privilege | 20/06/2026 | 21/06/2026 | [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023) |

Reference: Session 5, AWS Security and IAM, in the [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023).

### Notes from the work

When a permission did not work, I learned to inspect the principal, action, resource, and effect separately. This was more useful than temporarily granting broad access to see whether an error disappeared. I also became clearer on the difference between an IAM user and a service role: a workload can receive temporary permissions through a role without storing a permanent access key in the source code.

### End-of-week result

I could organize users into groups, read a basic policy, and create a role for an AWS service. I later applied the least-privilege approach when granting access among Lambda, S3, SQS, and DynamoDB in SmartStudy.
