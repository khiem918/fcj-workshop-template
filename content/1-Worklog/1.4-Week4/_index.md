---
title: "Week 4 Worklog"
date: 2024-01-01
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Week 4 Objectives:

* Move into the infrastructure phase: secure the AWS account and build the network foundation.
* Design a VPC whose private subnet has no route to the Internet.
* Prepare the domain name and certificates for the system.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Set up the AWS account: enable MFA, create an IAM user instead of using root <br> - Write a least-privilege policy for deployment <br> - Install and configure the AWS CLI <br> - Estimate cost with the AWS Pricing Calculator | 07/13/2026 | 07/13/2026 | <https://000002.awsstudygroup.com/en/> <br> <https://000048.awsstudygroup.com/en/> <br> <https://000011.awsstudygroup.com/en/> <br> <https://000007.awsstudygroup.com/en/> |
| 3 | - Create the **10.0.0.0/16** VPC <br> - Two public subnets across two Availability Zones plus one private subnet <br> - Internet Gateway and the two matching route tables | 07/14/2026 | 07/14/2026 | <https://000003.awsstudygroup.com/en/> |
| 4 | - Design nine security groups that reference each other rather than CIDR ranges: <br>&emsp; + **alb-sg**, **ecs-tasks-sg**, **qdrant-sg** <br>&emsp; + **grpc-api-sg**, **grpc-search-sg** <br>&emsp; + **rds-sg**, two **redis-sg**, **rabbitmq-sg** | 07/15/2026 | 07/15/2026 | <https://000003.awsstudygroup.com/en/> |
| 5 | - Create eight interface VPC endpoints and one S3 gateway endpoint <br> - Compare the cost of endpoints against a NAT Gateway <br> - Enable Private DNS on every interface endpoint | 07/16/2026 | 07/17/2026 | <https://000003.awsstudygroup.com/en/> <br> <https://000034.awsstudygroup.com/en/> |
| 6 | - Create the Route 53 hosted zone and DNS records for the domain <br> - Request ACM certificates in both **ap-southeast-1** and **us-east-1** <br> - Create a private hosted zone for internal service discovery | 07/17/2026 | 07/17/2026 | <https://000010.awsstudygroup.com/en/> |

### Week 4 Achievements:

* The AWS account was secured from the start: root is not used for daily work, and the deployment permissions are scoped to exactly what is needed.
* The network foundation is complete, with a private subnet that genuinely has no route out — no NAT Gateway, and every AWS service call travels through a VPC endpoint and stays inside the private network.
* Nine security groups reference each other instead of opening IP ranges, so the rules stay correct when a resource changes address.
* Understood why pulling one ECR image needs three endpoints: **ecr.api** for authentication, **ecr.dkr** for the registry protocol, and S3 for the layers themselves.
* Domain and certificates are ready, including the CloudFront certificate that must live in **us-east-1** — an easily missed detail that only surfaces when the distribution is created, which is too late.
* Lesson learned: VPC endpoints are not necessarily cheaper than a NAT Gateway once the endpoint count grows. What they buy is isolation, and that — not price — is the reason to choose them.
