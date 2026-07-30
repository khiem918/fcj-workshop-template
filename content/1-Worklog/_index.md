---
title: "Worklog"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1. </b> "
---

The internship ran for **6 weeks**, from **22 June 2026** to **31 July 2026**, around a single product: **VideoPlatformServer** — a video sharing platform with semantic search, built and then deployed on AWS.

The six weeks split into two halves of equal length:

* **Weeks 1 – 3: building the product.** Developing two services from scratch — **api_service** on NestJS 11 with GraphQL, and **search_service** on Python FastAPI — along with the full business flow: authentication, upload, asynchronous transcoding to MPEG-DASH, and hybrid search combining keywords with vectors.
* **Weeks 4 – 6: infrastructure and deployment.** Designing the private network on AWS, building the managed data tier, deploying all three services onto ECS Fargate, opening the path to the Internet through ALB and CloudFront, and finally automating deployment with GitHub Actions over OIDC.

The split was deliberate: the first three weeks produce a system that works locally, so the last three can focus entirely on the infrastructure problem instead of debugging code and configuration at the same time.

**Week 1:** [Data modelling and scaffolding api_service](1.1-week1/)

**Week 2:** [S3 uploads and asynchronous MPEG-DASH transcoding](1.2-week2/)

**Week 3:** [search_service, semantic search, and inter-service communication](1.3-week3/)

**Week 4:** [AWS foundation: IAM, VPC, security groups, and VPC endpoints](1.4-week4/)

**Week 5:** [The managed data tier and deployment to ECS Fargate](1.5-week5/)

**Week 6:** [ALB, CloudFront, and automated CI/CD with GitHub Actions](1.6-week6/)
