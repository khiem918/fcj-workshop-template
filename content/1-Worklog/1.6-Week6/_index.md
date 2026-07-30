---
title: "Week 6 Worklog"
date: 2024-01-01
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Week 6 Objectives:

* Expose the system to the Internet through ALB and CloudFront on a custom domain.
* Automate deployment with GitHub Actions using OIDC, storing no access keys.
* Test the whole system and finish the report documentation.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Create two **IP addresses** target groups with per-service health checks <br> - Build an Application Load Balancer listening on HTTPS across both public subnets <br> - Add a listener rule routing the search path to **search-service** | 07/27/2026 | 07/27/2026 | <https://000006.awsstudygroup.com/en/> |
| 3 | - Attach the ECS services to the target groups and set the health check grace period <br> - Create the Origin Access Control and a CloudFront distribution with two origins <br> - Update the bucket policy and create the Route 53 alias record | 07/28/2026 | 07/28/2026 | <https://000082.awsstudygroup.com/en/> |
| 4 | - Register GitHub as an OpenID Connect identity provider in IAM <br> - Create **GitHubActionsDeployRole** with a trust policy scoped to one repository and branch <br> - Declare the repository variables the workflow needs | 07/29/2026 | 07/29/2026 | <https://000044.awsstudygroup.com/en/> <br> <https://000048.awsstudygroup.com/en/> |
| 5 | - Write the deployment workflow: build images → run migration → render task definition → update services → wait for stability <br> - Enable the deployment circuit breaker for automatic rollback | 07/30/2026 | 07/30/2026 | <https://000017.awsstudygroup.com/en/> <br> <https://000023.awsstudygroup.com/en/> |
| 6 | - End-to-end testing: DNS, ALB, CloudFront, upload, transcode, search <br> - Review the architecture against the AWS Well-Architected Framework <br> - Finish the workshop and the internship report | 07/31/2026 | 07/31/2026 | <https://000034.awsstudygroup.com/en/> <br> <https://000013.awsstudygroup.com/en/> |

### Week 6 Achievements:

* The system is reachable from the Internet on a custom domain with end-to-end HTTPS. The full chain is browser → Route 53 → CloudFront → ALB → ECS task, with video files served straight from S3 through CloudFront.
* The S3 bucket stays completely private. Origin Access Control lets CloudFront read from it without granting any public permission.
* Deployment is fully automated: every push to **main** builds, migrates, and rolls both services forward. No access key is stored in GitHub — the credentials are temporary and exist only for the duration of the job.
* Migration runs first and blocks the deploy step, so a failed migration leaves the old version running against the old schema instead of putting new code in front of a schema it does not understand.
* The circuit breaker combined with **wait-for-service-stability** means a broken image both fails the workflow and rolls the service back automatically.
* Reviewed the design against the Well-Architected Framework and recorded the gaps honestly: RDS runs single-AZ and all tasks currently sit in one Availability Zone — acceptable at internship scale, but it must be fixed before real use.
* Biggest lesson of the internship: the hard part was not writing the code, it was describing exactly which component may talk to which — and keeping that description true after every deployment.
