---
title: "Deploy with AWS CDK"
date: 2026-07-19
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

#### Infrastructure source

The `infra/` directory contains the AWS CDK application used to provision SmartStudy. The module includes:

* `bin/`: CDK application entry point.
* `lib/`: reusable stack and resource definitions.
* `test/`: infrastructure tests.
* `cdk.json`: CDK application configuration.
* `package.json`: infrastructure dependencies and project scripts.
* `tsconfig.json`: TypeScript compiler configuration.

![AWS CDK infrastructure source](/images/5-Workshop/5.2-Prerequisite/github-cdk-infrastructure.png)

*Figure 5.8: AWS CDK project in the SmartStudy repository.*

#### Deployment workflow

The infrastructure deployment follows this controlled sequence:

1. Install the infrastructure project dependencies.
2. Configure the target AWS account, `us-east-1` Region, and environment-specific values.
3. Validate and synthesize the CDK application into a CloudFormation template.
4. Deploy the staging foundation stack.
5. Validate the staging application and service integration.
6. Deploy the production foundation stack from the same CDK definitions.

The exact commands are maintained by the project scripts. Sensitive configuration is supplied through the deployment environment and is not committed to the repository.

#### CloudFormation deployment result

AWS CloudFormation shows three active stacks:

* `CDKToolkit`: resources required to deploy CDK applications in the account.
* `SmartStudy-staging-Foundation`: staging infrastructure.
* `SmartStudy-production-Foundation`: production infrastructure.

Both SmartStudy stacks reached `UPDATE_COMPLETE`, while the CDK bootstrap stack reached `CREATE_COMPLETE`.

![CloudFormation deployment stacks](/images/5-Workshop/5.3-Infrastructure/cloudformation-stacks.png)

*Figure 5.9: Successful CDK bootstrap, staging, and production CloudFormation stacks.*

These stack states confirm that the infrastructure definitions were accepted and deployed. The next step is to verify the resulting resources in each AWS service.
