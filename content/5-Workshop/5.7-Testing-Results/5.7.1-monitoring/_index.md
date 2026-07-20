---
title: "CloudWatch Monitoring"
date: 2026-07-19
weight: 1
chapter: false
pre: " <b> 5.7.1. </b> "
---

#### Operational monitoring

Amazon CloudWatch monitors the AWS components involved in application processing. The monitoring overview groups alarms by service and shows coverage for AWS Lambda and Amazon SQS.

![SmartStudy CloudWatch monitoring](/images/5.7-Testing-Results/5.7-Testing-Results/cloudwatch-monitoring.png)

*Figure 5.32: CloudWatch alarm overview for Lambda and SQS resources.*

The captured state shows no active alarm. SQS alarms are in `OK`, while some Lambda alarms report insufficient data during a period without enough invocation samples. The screenshot verifies metric and alarm configuration at a high level; it does not claim individual log-event contents.

CloudWatch provides the operational basis for detecting processing errors and queue conditions while the staging and production environments are active.
