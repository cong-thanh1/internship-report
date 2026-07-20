---
title: "Amazon Cognito Authentication"
date: 2026-07-19
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

#### User pools

Amazon Cognito provides the authentication layer for the deployed SmartStudy application. Two single-Region User Pools were created in `us-east-1`, corresponding to the staging and production environments.

![SmartStudy Cognito User Pools](/images/5-Workshop/5.4-Frontend-Authentication/cognito-user-pools.png)

*Figure 5.21: Separate Amazon Cognito User Pools for SmartStudy environments.*

The authentication workflow is:

1. The user submits registration or sign-in information through the React frontend.
2. Amazon Cognito validates the request and manages the user identity.
3. The Pre Sign-up Lambda can apply project-specific registration logic.
4. After successful authentication, Cognito issues tokens to the client.
5. The frontend includes the access token when calling protected backend operations.

Separate User Pools prevent staging users and configuration from affecting production authentication. Passwords and authentication credentials are handled by Cognito and are not stored directly by the frontend.
