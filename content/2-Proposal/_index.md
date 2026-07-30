---
title: "Proposal"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# VideoPlatformServer
## A Video Sharing Platform with Semantic Search, Deployed on AWS ECS Fargate

### 1. Executive Summary
VideoPlatformServer is a personal project built during the internship to gain hands-on experience with production-grade microservice architecture on AWS. The system consists of two independent services communicating over bidirectional gRPC: **api_service** (NestJS 11 + Apollo GraphQL) handles authentication, video management, and upload/transcoding; **search_service** (Python FastAPI) handles hybrid search that combines keyword matching with semantic vector similarity via Qdrant. The entire stack runs on AWS ECS Fargate behind CloudFront and an Application Load Balancer, backed by RDS PostgreSQL, ElastiCache (Valkey), and Amazon MQ (RabbitMQ) for asynchronous inter-service messaging, with automated CI/CD through GitHub Actions.

### 2. Problem Statement
*Current Problem*
Most personal or learning-oriented AWS projects stop at simple serverless setups (Lambda + API Gateway + S3), missing the harder problems found in real production systems: asynchronous large-file processing, inter-service communication over gRPC and message queues, semantic search backed by a vector database, and running containers consistently across multiple environments (local Docker Compose, staging, and AWS production).

*The Solution*
VideoPlatformServer addresses these problems with two clearly separated services. **api_service** handles the video upload flow via S3 presigned URLs, then enqueues an asynchronous transcoding job with BullMQ (running on Redis/ElastiCache Valkey) that converts the video into MPEG-DASH using FFmpeg. When video metadata changes, **api_service** publishes a message through RabbitMQ (Amazon MQ) so that **search_service** can re-index it; **search_service** fetches the full metadata over gRPC, generates embeddings, and upserts them into the Qdrant vector database to power hybrid search (combining keyword scores with vector similarity). Everything is containerized and deployed on ECS Fargate, fronted by a CDN (CloudFront) and a custom domain (Route 53 + ACM).

*Benefits and Value*
The project delivers practical skills in microservice design, asynchronous messaging, vector/AI search, and AWS production operations (IAM/OIDC, VPC networking, container orchestration, CI/CD) — well beyond the scope of a simple serverless demo. The finished system doubles as a portfolio project and provides a foundation for future extensions such as recommendations or live streaming after the internship.

### 3. Solution Architecture
Users reach the platform through a custom domain (Route 53) → CloudFront (CDN, TLS) → Application Load Balancer, which routes by path (**/graphql/\*** to **api_service**, **/api/\*** to **search_service**) → an ECS Fargate cluster running three services: **api-service**, **search-service**, and **qdrant** (a self-hosted vector database persisting data on EFS). The two application services communicate over bidirectional gRPC and both connect to RDS PostgreSQL (relational metadata), ElastiCache Valkey (sessions, BullMQ queues, caching), and Amazon MQ (asynchronous metadata synchronization). Raw and transcoded video files are stored in S3 and served through CloudFront. All compute runs in private subnets and reaches AWS services through VPC Endpoints rather than a NAT Gateway, for cost efficiency.

![Solution Architecture](/images/2-Proposal/solution_architecture.png)

*AWS Services Used*
- *Amazon ECS (Fargate/Fargate Spot)*: Runs containers for **api-service**, **search-service**, and **qdrant**.
- *Amazon ECR*: Stores container images with automatic vulnerability scanning (scan-on-push).
- *Application Load Balancer + Amazon CloudFront*: HTTP/GraphQL routing and CDN delivery for video.
- *Amazon Route 53 + AWS Certificate Manager*: Custom domain and TLS certificates.
- *Amazon RDS for PostgreSQL*: Relational database shared by both services.
- *Amazon ElastiCache (Valkey)*: Sessions, BullMQ job queues, and caching.
- *Amazon MQ (RabbitMQ)*: Asynchronous inter-service messaging.
- *Amazon EFS*: Persistent storage for the Qdrant vector database.
- *Amazon S3*: Stores raw uploads and transcoded MPEG-DASH artifacts.
- *VPC Endpoints*: Let ECS tasks reach ECR/CloudWatch/SSM/Amazon MQ without a NAT Gateway.
- *AWS Systems Manager Parameter Store*: Manages environment variables and secrets as SecureString parameters.
- *AWS IAM (OIDC)*: GitHub Actions authenticates to AWS via a federated role, with no static access keys.
- *Amazon CloudWatch + AWS Backup*: Centralized logging and automated EFS backups.

*Component Design*
- *api_service (NestJS + GraphQL)*: User authentication, video management, presigned upload URL generation, transcoding job enqueueing, GraphQL API, and a gRPC server for metadata.
- *search_service (FastAPI)*: Consumes RabbitMQ messages, fetches metadata over gRPC, generates embeddings, upserts into Qdrant, and exposes a REST hybrid-search API.
- *Qdrant*: Self-hosted vector database on Fargate storing video embeddings, with data persisted through an EFS mount.
- *Transcoding worker*: A BullMQ worker inside **api_service** that downloads raw video from S3, runs FFmpeg to produce DASH output, and uploads the results back to S3.

### 4. Technical Implementation
*Implementation Phases*
The project has two parts — building the application (two services plus the database schema) and setting up and deploying the AWS infrastructure — each going through 3 phases:
1. *Development and Testing*: Implement both services, write tests, containerize with Docker, and set up CI/CD (GitHub Actions).
2. *Research, Architecture Design, and Cost Estimation*: Research AWS infrastructure options and choose the services that optimize cost the most.
3. *Build AWS Infrastructure and Deploy*: Provision the chosen services and wire them together, deploy, and fine-tune.

*Technical Requirements*
- *api_service*: NestJS 11, Prisma ORM, GraphQL (Apollo), BullMQ, FFmpeg, AWS SDK (S3 presigned URLs), and a gRPC server/client.
- *search_service*: Python FastAPI, gRPC, an embedding library (fastembed), the Qdrant client, and a RabbitMQ consumer/publisher.
- *Infrastructure*: Multi-stage Docker builds for both services, ECS task definitions (Fargate), and GitHub Actions using an OIDC role to build/push to ECR and deploy to ECS without static AWS keys.

### 5. Budget Estimation
The estimate below is based on public AWS pricing for the **ap-southeast-1** region at the time of writing, reflecting the intended architecture (before AWS Free Tier credits; actual costs vary with traffic).

*Infrastructure Costs (estimated monthly)*
- Amazon ECS Fargate/Fargate Spot (3 tasks: api-service 2 vCPU/5GB, search-service 4 vCPU/9GB, qdrant 0.25 vCPU/2GB): ~$56
- VPC Interface Endpoints: ~$20
- Amazon MQ (mq.m7g.medium, single-instance): ~$50
- Amazon ElastiCache for Valkey (2× cache.t4g.micro, one per service): ~$30
- Amazon RDS for PostgreSQL (db.t3.micro, 20GB gp3): ~$17
- Amazon CloudFront (CDN): ~$3
- Amazon EFS + AWS Backup: ~$2
- Amazon S3 (storage + requests): ~$1
- Amazon Route 53 (2 hosted zones): ~$1
- Amazon ECR + Amazon CloudWatch Logs: ~$12
- AWS Certificate Manager, SSM Parameter Store, IAM/OIDC: free

*Total*: ~$182/month

### 6. Risk Assessment
*Risk Matrix*
- Synchronization failures between the two services over gRPC/RabbitMQ: High impact, medium probability.
- Amazon MQ cost can run high if not managed carefully, potentially causing budget overrun.
- RDS/ElastiCache running single-AZ (no Multi-AZ): High impact if an AZ is lost, low probability.
- Exceeding the personal budget during the internship: Medium impact, medium probability.

*Mitigation Strategies*
- Inter-service sync: Use a dead-letter queue for RabbitMQ, bounded retries, and correlation IDs in logs for debugging.
- Availability: Consider enabling Multi-AZ for RDS if the budget allows after reviewing actual costs.
- Budget: Enable AWS Budget alerts and prefer Fargate Spot for tasks that tolerate brief interruptions.

*Contingency Plans*
- Automatic rollback via the ECS deployment circuit breaker when a new deployment fails health checks.
- Retain the previous task definition revision for manual rollback if needed.

### 7. Expected Outcomes
*Technical Improvements*: A complete microservice system running on AWS, with automated CI/CD.
*Long-term Value*: A reusable foundation of microservice architecture and AWS production operations skills for future projects, plus a technical portfolio piece demonstrating real system design capability.
