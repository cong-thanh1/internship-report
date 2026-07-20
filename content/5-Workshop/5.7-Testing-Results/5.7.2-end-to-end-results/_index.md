---
title: "End-to-End Results"
date: 2026-07-19
weight: 2
chapter: false
pre: " <b> 5.7.2. </b> "
---

#### Deployed application

The SmartStudy landing page was available through the production AWS Amplify URL and provided access to the complete learning workflow.

![SmartStudy production landing page](/images/5.7-Testing-Results/5.7-Testing-Results/welcome-page.png)

*Figure 5.33: SmartStudy application available for production demonstration.*

#### Quiz workflow

After a document is ready, SmartStudy can generate a timed multiple-choice quiz. The learner selects answers, tracks progress, and submits the attempt from the web interface.

![SmartStudy quiz interface](/images/5.7-Testing-Results/5.7-Testing-Results/quiz-page.png)

*Figure 5.34: Generated quiz presented to the learner.*

#### Result review

The result interface shows the submitted choice, expected answer, correctness state, and an explanation for each question. This closes the workflow from source document to learning assessment.

![SmartStudy quiz result](/images/5.7-Testing-Results/5.7-Testing-Results/quiz-result.png)

*Figure 5.35: Per-question result and explanation interface.*

The final demonstration verified the following user-visible capabilities:

* Registration and sign-in.
* Document upload, processing state, search, and management.
* Full-document summary.
* AI study room and learning assistant.
* Quiz generation, submission, scoring, and result review.

#### Operating status and planned decommissioning

Development and final testing were completed on July 19, 2026. The SmartStudy production environment, including AWS Amplify and its supporting backend resources, remained online through July 30, 2026 for report evaluation and product demonstration.

Resource cleanup was therefore outside the workshop scope at the time of documentation. After the evaluation period, the team may disable the Amplify deployment and remove unnecessary CloudFormation stacks to prevent additional cost. This workshop does not claim that those resources were already deleted.
