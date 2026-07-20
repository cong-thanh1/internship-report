---
title: "Week 3 Worklog"
date: 2026-05-25
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Focus for the week

Week 3 extended the networking work from one VPC to several VPCs. I set up both VPC Peering and Transit Gateway, then compared the routing required by the two approaches.

### Work completed

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 1 | **000019 - Set Up VPC Peering** <br> - Complete the preparation steps <br> - Update Network ACLs <br> - Create a peering connection <br> - Configure route tables <br> - Enable Cross-Peer DNS | 25/05/2026 | 28/05/2026 | [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023) |
| 2 | **000020 - Set Up Transit Gateway** <br> - Set up the infrastructure <br> - Create the Transit Gateway and attachments <br> - Create the TGW route table <br> - Update VPC routes and verify connectivity | 29/05/2026 | 31/05/2026 | [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023) |

References: `000019 - Set Up VPC Peering` and `000020 - Set Up Transit Gateway` in the [FCJ Workshop](https://github.com/AWS-First-Cloud-Journey/FCJ-2023).

### Notes from the work

A peering connection could be active while the instances still could not reach each other. I had to check the routes in both directions, the NACLs, and the Security Group rules before the connection worked. That was the main lesson from the lab: creating the connection is only one step, and every part of the traffic path has to agree. Transit Gateway introduced separate attachment and routing steps, but the structure was easier to follow as the number of VPCs increased.

### End-of-week result

I completed both connection models and understood the lack of transitive routing in VPC Peering. I would use peering for a small number of direct VPC connections and consider Transit Gateway when centralized connectivity becomes more important.
