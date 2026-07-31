---
title: "Blog 3"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# WHEN A PUBLIC LOAD BALANCER WON'T "TELL" YOU ITS PRIVATE IP — AND HOW TO BUILD YOUR OWN INTERNAL DNS ZONE FOR ALB/NLB

Today I'm sharing a networking problem that's genuinely annoying for anyone running large-scale infrastructure: an internet-facing Load Balancer only ever gives you its public IP, while what you actually need is the private IP. This post is based on an AWS article by Gonzalo Escarrá, a Solutions Architect specializing in Networking.

## Where's the problem?

ELB launched back in 2009 and is still the foundational service behind almost every architecture on AWS. Today, ALB and NLB support two main deployment types:

* **internet-facing** → placed in a public subnet
* **internal** → placed in a non-public subnet

Whichever type you choose, you get a randomly generated DNS name, in the form:

```
nlb-1234567890abcdef0.elb.us-east-1.amazonaws.com
```

The key issue: for an internet-facing LB, the AWS-managed DNS name **ONLY** resolves to public IPs. There is no record at all pointing to the load balancer's internal address.

Normally that's not a problem. But it becomes one in two very real situations:

* You use a third-party device/gateway to inspect ingress traffic, and the rules on that device are DNS-based. The device sits between the ALB/NLB and the Internet Gateway, but when it queries DNS it gets back the public IP — not what it needs for internal routing.
* You want to reach that same internet-facing LB over AWS Direct Connect, using a private VIF or a transit VIF. Traffic travels over a private path, but DNS still only returns the public IP.

In short: you need **"split DNS"** — the same name, but resolving to the internal IP from inside the VPC.

## The idea behind the solution

Build your own Private Hosted Zones in Route 53 to hold internal records for your internet-facing load balancers, along with automation to keep those records continuously up to date.

Why automation? Because the IP of the ENI a load balancer uses can change: the LB gets recreated, deleted, or scales out to add more ENIs. Updating records by hand simply isn't feasible.

## Overall architecture

The solution assumes you're running a multi-account environment (using AWS Organizations), with the following flow:

CloudTrail (in the workload account) → an EventBridge rule catches the load balancer's lifecycle events → forwards them to a custom event bus in the shared-services account → a Lambda function creates / updates / deletes records in the Route 53 Private Hosted Zone → DynamoDB stores state (mapping ARN ↔ CNAME ↔ ENI) so it doesn't need to make repeated cross-account API calls.

## What needs to be deployed?

**In the shared-services account:**

* 2 Private Hosted Zones per Region:
  * For ALB: **internal.region.elb.amazonaws.com**
  * For NLB: **internal.elb.region.amazonaws.com**
* 2 Lambda functions:
  * **r53-scavenger**: runs on a schedule, scans and loads the private IPs of existing ALBs/NLBs into the private hosted zone
  * **r53-updater**: runs on create / update / scale / delete events, keeping records accurate
* 1 DynamoDB table to track changes
* 1 custom EventBridge bus to receive events from member accounts

**In the workload accounts (where the load balancers live):**

* 1 IAM role for the Lambda to assume, used for the initial record discovery
* 1 EventBridge rule to catch CloudTrail events

First run: use AWS Step Functions to manually trigger the **r53-scavenger** workflow once, loading the names and IPs of all existing load balancers into the hosted zone. After that, every change is handled automatically by **r53-updater**.

The whole thing can be deployed using the CloudFormation template AWS provides in the original article.

## The interesting detail: why do you need 2 EventBridge rules?

This is the part I found most worth learning from. The reason: Elastic Load Balancing operations log to CloudTrail from **two** different sources depending on the action.

**Rule 1** — catches load balancer create/delete events, source is **aws.elasticloadbalancing**:

```json
{
  "source": ["aws.elasticloadbalancing"],
  "detail": {
    "eventName": ["CreateLoadBalancer", "DeleteLoadBalancer"]
  }
}
```

Note: right at CREATE time, we don't yet know which IP the ENI will get — we only know the LB was just created. So the initial record is written with a placeholder value.

**Rule 2** — catches network interface create/delete events, source is **aws.ec2**. But EC2 creates ENIs constantly, so it needs additional filtering on **sourceIPAddress** and **userAgent** to pick up only the ENIs actually created by ELB:

```json
{
  "source": ["aws.ec2"],
  "detail": {
    "eventSource": ["ec2.amazonaws.com"],
    "eventName": ["CreateNetworkInterface", "DeleteNetworkInterface"],
    "sourceIPAddress": ["elasticloadbalancing.amazonaws.com"],
    "userAgent": ["elasticloadbalancing.amazonaws.com"]
  }
}
```

Both rules forward events to the custom bus in the central account. The bus's resource policy is configured to allow **PutEvents** only from accounts within the same Organization (via the **aws:PrincipalOrgID** condition). If needed, this can be extended for cross-organization use or specific accounts.

The **r53-updater** Lambda then uses a cross-account role to call **DescribeLoadBalancer** and **DescribeNetworkInterfaces** to build the record. When an LB is created, the DynamoDB table stores both **cname** (the public version) and **cnameint** (the internal version). When the LB scales, the Route 53 record gets updated with the IPs of the new ENIs.

## A few things to keep in mind

* The solution relies on AWS Organizations, since it uses the organization ID as the principal for permissions on the EventBridge bus.
* This design operates within a single Region, with one pair of hosted zones.

## Results

After deploying this, you can retrieve the private IP an internet-facing ALB/NLB is currently using with a single, simple DNS query. As a result:

* Internal firewalls/appliances can look it up far more easily
* You can use split DNS so the same name resolves to that same load balancer, but over the internal path

#### Post image

![img](/images/3-BlogsPosted/Screenshot_20260731_205503.png)

> This is a great example of combining CloudTrail, EventBridge, and Lambda to automate a piece of networking that AWS doesn't provide out of the box — private DNS for your own public load balancer.
