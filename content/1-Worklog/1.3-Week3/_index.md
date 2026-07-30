---
title: "Week 3 Worklog"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Week 3 Objectives:

* Build **search_service** in Python with FastAPI, offering hybrid keyword plus semantic search.
* Set up bidirectional communication between the two services over gRPC.
* Synchronise video metadata asynchronously through RabbitMQ.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Bootstrap **search_service** in a layered architecture: **app**, **core**, **domain**, **infrastructure** <br> - Build the dependency-injection container and centralised configuration | 07/06/2026 | 07/06/2026 | |
| 3 | - Integrate Qdrant as the vector database <br> - Generate embeddings for video titles and descriptions <br> - **semantic_worker** handling background indexing | 07/07/2026 | 07/08/2026 | |
| 4 | - Define **proto/video.proto** <br>&emsp; + **VideoMetaDataService** — served by **api_service** on port 50051 <br>&emsp; + **DeleteVideoService** — served by **search_service** on port 50052 <br> - Generate stubs for both TypeScript and Python | 07/08/2026 | 07/09/2026 | |
| 5 | - Metadata synchronisation over RabbitMQ using routing keys **video.metadata.trans** and **video.metadata.res** <br> - Write the **consumer** and the **dlq_consumer** for failed messages | 07/09/2026 | 07/10/2026 | |
| 6 | - Finish the **/api/v1/search** and **/api/v1/health** endpoints <br> - **Practice:** integration-test both services running side by side under Docker Compose | 07/10/2026 | 07/10/2026 | |

### Week 3 Achievements:

* **search_service** runs on a clean layered architecture where business logic does not depend directly on FastAPI or Qdrant, so swapping infrastructure later will not touch the core.
* Search returns results that combine keyword matching with vector similarity, so a query describing the content still finds the video even when no word matches the title.
* Bidirectional gRPC works: **api_service** supplies metadata to **search_service**, and **search_service** calls back when a video must be removed from the index. The shared **.proto** file is the single source of truth for both languages.
* Metadata is synchronised asynchronously through RabbitMQ with a dead-letter queue, so failed messages are retained for handling instead of vanishing silently.
* End of the product development phase: both services run side by side locally and serve the whole flow from upload through search.
* Lesson learned: using both gRPC and a message queue in one system only makes sense when their purposes are kept apart — gRPC for calls that need an answer now, the queue for work that tolerates delay but must not be lost.
