---
title: "GitHub Branch Workflow"
date: 2026-07-19
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

#### Repository workflow

SmartStudy uses GitHub as its source-control and collaboration platform. The default `main` branch represents the production-ready code, while `staging` is used to integrate and validate changes before production. A `develop` branch and short-lived feature or bug-fix branches support ongoing development.

The repository overview shows that the final production update was merged from `staging` into `main` through pull request `#158`.

![SmartStudy repository main branch](/images/5-Workshop/5.4-Frontend-Authentication/github-repository-main.png)

*Figure 5.18: SmartStudy repository after merging staging changes into main.*

#### Branch validation

The branch overview identifies `main` as the default branch and shows successful checks for `main`, `staging`, and `develop`. This workflow separates integration work from production delivery.

![SmartStudy GitHub branches](/images/5-Workshop/5.4-Frontend-Authentication/github-branches.png)

*Figure 5.19: Default, staging, develop, and supporting branches with successful checks.*

The production workflow is summarized as:

```text
Feature or bug-fix branch
          ▼
       develop
          ▼
       staging ── validation and checks
          ▼
         main ── AWS Amplify production deployment
```
