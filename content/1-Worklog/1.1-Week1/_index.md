---
title: "Week 1 Worklog"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

### Week 1 Objectives:

* Define the scope of **VideoPlatformServer** and model the data for the whole system.
* Scaffold **api_service** on NestJS 11 with a code-first GraphQL setup.
* Complete user authentication and session management.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Gather requirements and lock the product scope: upload → transcode → playback → search <br> - Draw the data flow and set the boundary between the two services | 06/22/2026 | 06/22/2026 | |
| 3 | - Bootstrap the NestJS 11 project <br> - Configure **ConfigModule** with schema validation for environment variables <br> - Enable code-first GraphQL, auto-generating **schema.gql** | 06/23/2026 | 06/23/2026 | |
| 4 | - Design the Prisma schema for PostgreSQL: <br>&emsp; + **User**, **Video**, **VideoInformation** <br>&emsp; + **Comment**, **LikeVideo**, **Subscribe**, **WatchHistory** <br>&emsp; + Status enums: **UploadVideoStatus**, **ProcessingStatus**, **VideoVisibility** <br> - Run the first migration | 06/24/2026 | 06/25/2026 | |
| 5 | - **auth** module: verify Firebase ID tokens <br> - **SessionService** storing sessions in Redis <br> - Guard and **@CurrentUser** decorator for resolvers | 06/25/2026 | 06/26/2026 | |
| 6 | - Write unit tests for **auth.service**, **auth.resolver**, **session.service** <br> - **Practice:** stand up the local environment with Docker Compose (PostgreSQL, Redis, RabbitMQ) | 06/26/2026 | 06/26/2026 | |

### Week 1 Achievements:

* Settled the product scope and split the system into two services with clear responsibilities: **api_service** owns business logic and relational data, **search_service** owns semantic search.
* Finished the data schema with 11 tables and 8 status enums. Splitting **Video** from **VideoInformation** keeps frequently-changing metadata from locking the main table.
* **api_service** runs locally and generates its GraphQL schema from code, so no schema file has to be maintained by hand.
* Authentication works end to end: Firebase-issued tokens are verified server-side and sessions live in Redis, so they can be revoked without waiting for token expiry.
* Every dependency (PostgreSQL, Redis, RabbitMQ) comes up with a single **docker compose up**, which cut setup time for the following weeks.
* Lesson learned: designing the status enums up front mattered more than expected — the entire week 2 transcode flow is driven by them.
