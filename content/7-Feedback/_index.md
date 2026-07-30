---
title: "Sharing and Feedback"
date: 2024-01-01
weight: 7
chapter: false
pre: " <b> 7. </b> "
---

### Overall evaluation

**1. Working environment**

What stood out most about the program was being able to choose my own topic and own it from beginning to end. Nobody handed me a list of tasks; I had to decide what to build, in what order, and live with the consequences when I decided wrong.

That is both the strength and the difficulty. A strength, because it forces real understanding rather than following instructions. A difficulty, because nobody stops you when you head the wrong way — the mistake only surfaces when the system fails to run, and by then it has usually cost a few days.

**2. Support from mentor / team admin**

The admin team answered my questions throughout the process of learning AWS. For someone meeting an ecosystem that broad for the first time, having answers at the right moment kept me from committing to several wrong directions early on.

**3. Relevance of the work to my academic major**

The development half sat squarely within my field: database design, APIs, asynchronous processing, testing. The infrastructure half was almost entirely new. At university, networking is taught as theory; the moment you have to decide which subnet goes where and which security group references which, that same knowledge turns into something else entirely.

That gap was the most valuable thing I got. Without the program I would have had no reason to build a real system on AWS with my own money and time.

**4. Learning and skill development opportunities**

Things I learned that I can point to concretely:

* Designing a private network on AWS: subnets, route tables, and why a genuinely private subnet must have no route to the Internet.
* Security groups that reference one another instead of opening IP ranges — this changed how I think about network permissions altogether.
* VPC Endpoints and their real cost, including the fact that they are not cheaper than a NAT Gateway as I first assumed.
* Deploying containers on ECS Fargate, and why deployment order matters as much as configuration.
* Federated authentication with OIDC instead of static access keys — for me the most valuable security lesson of the program.
* How to write technical documentation someone else can reproduce, rather than documentation that proves I did the work.

**5. Culture and team spirit**

Requiring everyone to publish a weekly worklog and blog creates a healthy kind of pressure: knowing the work will be read changes how carefully it is recorded. Reading other people's write-ups was also how I gauged where I stood.

**6. Internship policies and benefits**

The material at [cloudjourney.awsstudygroup.com](https://cloudjourney.awsstudygroup.com/) is a significant benefit, particularly because it exists in Vietnamese. For anyone meeting AWS for the first time, reading a new concept in their first language before checking it against the original English documentation saves a great deal of time.

### Additional questions

**What did I find most satisfying during the internship?**

The moment I typed my own domain into a browser and the system answered correctly — after passing through Route 53, CloudFront, the ALB, and finally a container I had packaged myself. Until then I had only ever run applications on my own machine.

**What should the program improve for future cohorts?**

There should be a session, or at least a document, on **estimating cost before building**. I only realised that eight VPC Endpoints cost more than one NAT Gateway after building all eight. For anyone paying for their own account, that is something to know beforehand, not afterwards.

**Would I recommend it to a friend?**

Yes, with one caveat: this program only pays off if the participant picks a genuinely demanding topic. Choose something small and safe, and six weeks will pass without much being learned.

### Suggestions and expectations

* **Add a VPC Endpoint workshop.** While looking for reference material I noticed that the cloudjourney catalogue covers VPC, VPC Peering, and Transit Gateway, but has no dedicated lab on VPC Endpoints — even though they are mandatory for anyone running containers in a private subnet, and were the cause of the hardest failure I had to diagnose.
* **Add a resource cleanup checklist.** Many services keep charging when idle. A standard checklist would spare learners a surprise bill after the program ends.
* **Encourage Infrastructure as Code earlier.** Had it been suggested at the start, I would have built the infrastructure with CloudFormation instead of by hand through the console.
* **On continuing:** I intend to keep developing this system after the internship, starting by fixing the single-Availability-Zone weakness and moving the whole infrastructure into code.
