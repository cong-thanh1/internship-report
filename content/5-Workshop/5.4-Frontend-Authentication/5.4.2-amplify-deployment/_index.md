---
title: "AWS Amplify Deployment"
date: 2026-07-19
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

#### Frontend hosting

AWS Amplify Hosting builds and publishes the SmartStudy frontend from the connected GitHub repository. The `main` branch is configured as the production branch and was successfully deployed to the default secure Amplify domain.

![SmartStudy application on AWS Amplify](/images/5-Workshop/5.4-Frontend-Authentication/amplify-production.png)

*Figure 5.20: SmartStudy production branch deployed on AWS Amplify.*

The Amplify overview confirms:

* The SmartStudy application is connected to GitHub.
* Two branches are configured for separate environments.
* `main` is identified as the production branch.
* The production deployment is active on an `amplifyapp.com` URL.
* Firewall protection is enabled for the hosted application.

The project uses the Amplify-provided domain because the planned Route 53 custom-domain registration could not be completed. This maintains HTTPS access without adding VPC or networking infrastructure.
