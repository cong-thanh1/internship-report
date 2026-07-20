---
title: "Week 2 Worklog"
date: 2026-05-18
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Focus for the week

After setting up the account, I moved on to networking. The goal was to build a VPC, understand how traffic moves through it, and connect to EC2 through Session Manager instead of exposing SSH access.

### Work completed

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 1 | **000003 - Create a VPC** <br> - Review VPC components and firewalls <br> - Create the lab VPC <br> - Review the Site-to-Site VPN flow | 18/05/2026 | 21/05/2026 | [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023) |
| 2 | **000058 - Systems Manager Session Manager** <br> - Prepare the IAM role and SSM Agent <br> - Connect to EC2 <br> - Review session logs <br> - Configure port forwarding | 18/05/2026 | 24/05/2026 | [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023) |

References: `000003 - Create a VPC` and `000058 - Systems Manager Session Manager` in the [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023).

### Notes from the work

The part that took the most checking was matching the route table to the correct subnet and then reviewing the Security Group and NACL rules when traffic did not follow the expected path. I checked one layer at a time instead of changing several rules together. The Session Manager lab also showed that having SSM Agent installed is not enough: the instance still needs the right IAM role and connectivity to the SSM service.

### End-of-week result

I completed the VPC lab and managed the EC2 instance through Session Manager. This gave me a more practical understanding of how routing, firewall rules, and IAM permissions all contribute to a working connection.
