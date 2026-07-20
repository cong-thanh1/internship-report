---
title: "Week 4 Worklog"
date: 2026-06-01
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Focus for the week

This week moved into compute services. I started with routine EC2 operations, then combined a Launch Template, Target Group, Load Balancer, and Auto Scaling Group into one deployment. I finished by trying Lightsail and comparing its setup with EC2.

### Work completed

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 1 | **000004 - Basic EC2 Operations** <br> - Create an EC2 instance <br> - Install an application <br> - Create a snapshot | 01/06/2026 | 02/06/2026 | [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023) |
| 2 | **000006 - Deploy Auto Scaling Group** <br> - Create a Launch Template, Target Group, Load Balancer, and Auto Scaling Group <br> - Verify instance health | 03/06/2026 | 05/06/2026 | [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023) |
| 3 | **000045 - Getting Started with Amazon Lightsail** <br> - Deploy the application <br> - Test Lightsail Load Balancer and RDS <br> - Review migration to EC2 | 06/06/2026 | 07/06/2026 | [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023) |

References: `000004`, `000006`, and `000045` in the [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023).

### Notes from the work

Target Group health checks required the most attention in the Auto Scaling exercise. An instance could be running and still show as unhealthy when the application was listening on the wrong port or the health-check path returned an invalid response. Checking the Security Group, application port, and health-check path separately resolved the issue.

### End-of-week result

I understood how the individual Auto Scaling components fit together instead of treating EC2 as a standalone server. Lightsail was quicker for a simple deployment, while EC2 provided more control over the underlying infrastructure.
