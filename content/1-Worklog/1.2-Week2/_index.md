---
title: "Week 2 Worklog"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Week 2 Objectives:

* Complete the path a video takes, from upload to playback.
* Move transcoding off the request path so uploads are not blocked.
* Convert videos to multi-resolution MPEG-DASH.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - **s3** module: issue presigned URLs so the client uploads straight to S3 <br> - Separate the **public/** and **private/** prefixes by **VideoVisibility** | 06/29/2026 | 06/29/2026 | |
| 3 | - Build the job queue with BullMQ on Redis <br> - Configure retry count and backoff for transcode jobs | 06/30/2026 | 06/30/2026 | |
| 4 | - **FfmpegService**: transcode to MPEG-DASH <br>&emsp; + Multiple resolutions driven by the **Resolution** enum <br>&emsp; + Generate the manifest and segments <br> - Clean up temporary files afterwards | 07/01/2026 | 07/02/2026 | |
| 5 | - **VideoProcessingHandler**: advance **ProcessingStatus** at each step <br> - Define dedicated exceptions for transcode failures <br> - Upload the DASH output to S3 | 07/02/2026 | 07/03/2026 | |
| 6 | - Write tests for **ffmpeg.service**, **video-processing.queue**, **video-processing.handler** <br> - **Practice:** measure transcode time across a range of file sizes | 07/03/2026 | 07/03/2026 | |

### Week 2 Achievements:

* Uploads no longer pass through the application server: the client fetches a presigned URL and sends the file directly to S3, so **api_service** never holds a large file in memory.
* Transcoding runs in the background through BullMQ. The API returns as soon as the job is queued, and progress is reflected in the **VideoProcessing** table via the **ProcessingStatus** enum.
* Videos are converted to multi-resolution MPEG-DASH, letting the player pick a bitrate that matches the viewer's connection.
* Failed jobs retry with backoff instead of failing permanently, and transcode errors are wrapped in dedicated exceptions so system faults are distinguishable from bad input files.
* Measured real transcode times against file size — these numbers are exactly what drove the CPU/memory sizing of the ECS tasks in week 5.
* Lesson learned: transcoding is the most CPU-hungry work in the system, so moving it off the request-serving process is a decision to make early, not an optimisation to add later.
