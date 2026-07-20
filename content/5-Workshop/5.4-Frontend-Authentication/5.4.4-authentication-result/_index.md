---
title: "Authentication Result"
date: 2026-07-19
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---

#### Sign-in interface

The deployed SmartStudy landing page provides a sign-in dialog for returning users. The form collects an email address and password and submits them through the Cognito authentication workflow.

![SmartStudy sign-in dialog](/images/5-Workshop/5.4-Frontend-Authentication/login-page.png)

*Figure 5.22: Sign-in interface of the deployed SmartStudy application.*

#### Registration interface

New users can create a SmartStudy account with a name, email address, and password. The interface communicates the minimum password length before registration is submitted.

![SmartStudy registration dialog](/images/5-Workshop/5.4-Frontend-Authentication/signup-page.png)

*Figure 5.23: Account-registration interface connected to the Cognito workflow.*

The presence of both interfaces, together with the deployed Cognito User Pools and Pre Sign-up Lambda functions, verifies the authentication flow from the frontend to the AWS identity service.
