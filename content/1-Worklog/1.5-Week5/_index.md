---
title: "Week 5 Worklog"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Week 5 Objectives:

* Build the managed data tier on AWS: database, cache, message broker, and storage.
* Package both services as container images and push them to ECR.
* Deploy three services onto ECS Fargate.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Create a **db.t3.micro** RDS PostgreSQL instance in the private subnet <br> - Two ElastiCache Valkey clusters, one per service <br> - Amazon MQ running RabbitMQ on **mq.m7g.medium** | 07/20/2026 | 07/20/2026 | <https://000005.awsstudygroup.com/en/> |
| 3 | - Create the S3 bucket with all public access blocked <br> - EFS with an access point for Qdrant's persistent vectors <br> - Store sensitive configuration in SSM Parameter Store as **SecureString**, encrypted with KMS | 07/21/2026 | 07/21/2026 | <https://000057.awsstudygroup.com/en/> <br> <https://000033.awsstudygroup.com/en/> <br> <https://000031.awsstudygroup.com/en/> |
| 4 | - Create two ECR repositories with scan-on-push enabled <br> - Write multi-stage Dockerfiles for both services <br> - Build and push the images to ECR <br> - Author the task definitions and **ecsTaskExecutionRole** | 07/22/2026 | 07/22/2026 | <https://000015.awsstudygroup.com/en/> |
| 5 | - Create the ECS cluster and deploy in order: <br>&emsp; + **qdrant** first, with EFS attached <br>&emsp; + then **api-service** and **search-service** <br> - Set up internal service discovery through Cloud Map | 07/23/2026 | 07/24/2026 | <https://000016.awsstudygroup.com/en/> <br> <https://000067.awsstudygroup.com/en/> |
| 6 | - Run **prisma migrate deploy** as a one-off ECS task <br> - Use ECS Exec to diagnose running tasks <br> - **Troubleshooting:** tasks authenticate to ECR successfully but stall while pulling the image | 07/24/2026 | 07/24/2026 | <https://000058.awsstudygroup.com/en/> |

### Week 5 Achievements:

* The whole data tier sits in the private subnet and accepts connections only from the ECS task security groups; nothing is exposed to the Internet.
* No sensitive value remains in an image or a static environment variable: configuration is read from Parameter Store at task startup, and the task role may decrypt only its own parameter branch.
* Three services run reliably on Fargate. Qdrant was deployed first because the other two depend on it, and its vectors live on EFS so a restarted task does not lose the index.
* Database migrations run as a separate task rather than during container startup, which stops several simultaneously starting tasks from migrating on top of each other.
* **Most instructive incident:** tasks failed to pull their image even though ECR authentication succeeded. The cause was the missing S3 gateway endpoint — image layers live in S3, not in ECR. The symptom looked like a plain network timeout, which is why it took a while to trace.
* Lesson learned: deployment order matters as much as configuration. Starting with the component that depends on nothing means every failure has exactly one plausible cause.
