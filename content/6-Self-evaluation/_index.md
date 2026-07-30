---
title: "Self-Assessment"
date: 2024-01-01
weight: 6
chapter: false
pre: " <b> 6. </b> "
---

Over the six weeks of the **First Cloud AI Journey** program, from **22 June 2026** to **31 July 2026**, I carried one project all the way through, from the first line of code to a system running live on the Internet: **VideoPlatformServer** — a video sharing platform with semantic search.

In the first three weeks I built the product: two services written in two different languages, communicating bidirectionally over **gRPC** and **RabbitMQ**, with asynchronous video transcoding to **MPEG-DASH** and hybrid search combining keywords with vectors. In the last three weeks I moved all of it onto AWS: designing a private network with no NAT Gateway, building the managed data tier, deploying three services onto **ECS Fargate**, opening the path to the Internet through **ALB** and **CloudFront**, and automating deployment with **GitHub Actions** over **OIDC**.

What I gained was not a list of services used, but an understanding of why they have to fit together in that particular order. One concrete example: it took me a long time to work out why ECS tasks authenticated to ECR successfully yet stalled while pulling the image — the S3 gateway endpoint was missing, because image layers live in S3 rather than in ECR. The failure looked like an ordinary network timeout. It taught me that in infrastructure, error messages usually point at the symptom rather than the cause.

In terms of work ethic, I held to the six-week plan I set out, finished both the development and the deployment phases, and documented the entire process as a bilingual workshop that someone else could follow from scratch.

To reflect objectively on my internship, I would like to assess myself against the following criteria:

| No. | Criteria | Description | Good | Fair | Average |
| --- | --- | --- | --- | --- | --- |
| 1 | **Professional knowledge & skills** | Understanding of the field, applying knowledge in practice, proficiency with tools, work quality | ✅ | ☐ | ☐ |
| 2 | **Ability to learn** | Ability to absorb new knowledge and learn quickly | ✅ | ☐ | ☐ |
| 3 | **Proactiveness** | Taking initiative, seeking out tasks without waiting for instructions | ✅ | ☐ | ☐ |
| 4 | **Sense of responsibility** | Completing tasks on time and ensuring quality | ✅ | ☐ | ☐ |
| 5 | **Discipline** | Adhering to schedules, rules, and work processes | ☐ | ✅ | ☐ |
| 6 | **Progressive mindset** | Willingness to receive feedback and improve oneself | ✅ | ☐ | ☐ |
| 7 | **Communication** | Presenting ideas and reporting work clearly | ☐ | ✅ | ☐ |
| 8 | **Teamwork** | Working effectively with colleagues and participating in teams | ☐ | ✅ | ☐ |
| 9 | **Professional conduct** | Respecting colleagues, partners, and the work environment | ✅ | ☐ | ☐ |
| 10 | **Problem-solving skills** | Identifying problems, proposing solutions, and showing creativity | ✅ | ☐ | ☐ |
| 11 | **Contribution to project/team** | Work effectiveness, innovative ideas, recognition from the team | ☐ | ✅ | ☐ |
| 12 | **Overall** | General evaluation of the entire internship period | ✅ | ☐ | ☐ |

### Strengths

* **Covered the full lifecycle of a system.** From data schema design, writing code, containerisation, and network build-out through to automated deployment — without stopping short anywhere.
* **Chose solutions for reasons, not by habit.** Using VPC Endpoints instead of a NAT Gateway was about isolating the private subnet, not saving money; I worked out that with eight endpoints the approach is actually more expensive, and chose it deliberately anyway.
* **Diagnosed infrastructure failures whose error messages pointed at the wrong place**, rather than trying things at random until something worked.
* **Documented everything.** The whole process is written up as a bilingual workshop that can be reproduced from nothing.

### Needs improvement

* **No Infrastructure as Code.** The entire infrastructure was built by hand through the console. The system works, but it cannot be recreated with one command and there is no change history. Given another attempt, I would use CloudFormation or CDK from the first week of the infrastructure phase.
* **Availability was traded for speed.** RDS runs single-AZ and every ECS task sits in one Availability Zone, even though I had already created a subnet in a second one. It was a conscious choice to stay on schedule, but it is a genuine weakness and I record it as such rather than hiding it.
* **Cost estimation came too late.** I only worked out the VPC Endpoint costs properly after building all eight. That comparison belonged in the design step, not the review step.
* **Testing stops at the application layer.** The code has unit tests, but the deployment has no automated verification beyond the load balancer health checks.
* **Little practice in teamwork and communication.** Because this was an individual project, I almost never had to defend a technical decision to someone else — a skill I need to seek out opportunities to practise.
* **Time discipline.** Some parts were done in concentrated bursts at the end of a week rather than spread evenly, leaving the workload very uneven across days.
