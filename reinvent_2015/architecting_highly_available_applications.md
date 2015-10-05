# AWS Re:Invent 2015

://events-aws.qwiklab.com/?ref_=7
re:Invent 2015 - Bootcamp - Architecting Highly Available Applications on AWS  In Session

"Everything fails all of the time"

Design for Availability
Assume everything fails and work backwards
Avoid single point of failure
Implement elasticity
Design loosely coupled architecture

What is HA?
Availability: percentage of time that an application does work
Loss of availabilty:

- app is offline, unreachable or partially available.
- app is slow to use
- planned and unplanned

HA is:
Fault tolerance
built in redundancy
fault tolerance

Scalability
accommodate growth without changing design
scalability doesn’t guarantee availability

**Disaster recovery**

HA & DR
HA is the other end of the DR continuum
Don’t define RTO/RPO, design for continuous availability
Goal: application never goes down
  Elimiinate single points of failure
  Design self-healing applications
  Use graceful degradation
Traditional IT says that HA is very expensive and is only suitable for mission critical applications

AWS offers multiple services, isolated and redundant datacenter.t psetd s

Inherently fault tolerant:
PaaS

Fault tolerant if done correctly:
Iaas

Always do fault tolerance services
Where possible, use platform services instead of rolling your own
Service availability is built in.

Examples:
  - SES outobound email
  - SQS: queues
  -DynamoDB: NoSQL

Some services must be designed for availailability.
Fundamental AWS services has flexible deployment options, which means that you can deply them in non-fault tolerant modes.
  - fault tolerance is desireable for production environments
  - not suitable for development environments
Connectivity for the VPC must also be designed for availability.

Traditional architecture relies on duplicates to achieve scale, as opposed to the inherent strengths of AWS.

Single region, multi-AZ
  - on prem, many customers limited to a single DC
  - Production apps should use multiple AZs. They have network diversity, geo-dispersion and incorporate built-in redundancy.

Multi-region is for:
  - geographic expansion
  - DR
  - good for independent stacks that have no dependency on other regions

Multi-region for a single app can be very complex
  - consider latency
  - database sync

**Reserving Capacity**

AWS does not offer unlimited capacity. Reserved instancess can ensure capacity and are usefule is capacity is constrained.
Always pay attention to you service limites. Anticipate and adjust AWS resource limits ahead of time.

**Automation**

AWS is a virtuall programmable data center, a key differentiator. Treat infrastructure as code. HA cannot exist without automation. Manually intervened services is not HA.


**Chaos Monkey**

Failures happen when you don't expect it Test on your
timeline, not chance's timeline.  See Netflix's 'Principles of Chaos':
http://www.principlesofchaos.org/

```
Advances in large-scale, distributed software systems are changing the game for
software engineering.  As an industry, we are quick to adopt practices that
increase flexibility of development and velocity of deployment.  An urgent
question follows on the heels of these benefits: How much confidence we can
have in the complex systems that we put into production? - See more at:
http://www.principlesofchaos.org/#sthash.bVQfTNVL.dpuf
```

**Summary**

Be skeptical when designing for availability
Everything fails all the time
Design for faiulre
Design loosely coupled applications
Implement elasticity
Take advantage of AWS infrastructure that is inherently fault-tolerant
Follow best practices for adding HA to other services
Architect and optimize your applications for AWS
Automate and test your HA implementation

**Optimizing Edge Services**

Think about using S3 for websites, which can host the downloads of digital
assets. Use S3 for static sites, which can be used for basic websites as
disaster recovery options.

**Highly Availability DNS**

DNS is a tier-0 service, where availability is number 1.
Design principles:
  - reliable
  - fast
  - integrated with AWS
  - easy to use
  - cost effective
  - flexible

**Cloudfront**

Use CDNs for content distribution, which allows you to distribute content to end users with low-latency and high data transfer speeds.
  - Push requests to CDN, not your servers.

Optimizations made for Cloudfront:
  - TCP/IP optimizatinos
  - Keep-alive connections to reduce RTT
  - collapsed forwarding
  - device detecion
  - SSL termination at the edge
  - POST/PUT upload optimizations
  - latency based routing

Error codes
Can create custom error codes


**Core Web Application Services**
Elimiinate SPOF
Enable elasticity
Handle traffic spikes
Add resiliency

**Types of elasticity**
On / off
fast growth
variable peaks
predictable peaks

**Modern 3 Tier Web Application**
Web, application, and database servers.

Web tier foundation components:
Elastic load balancing
Cloudwatch
Auto-scaling

ELB:
  - scales smoothyl based on traffic
  - scaling takes from 1-7 minutes
  - for spiky traffic, pre-warm the ELB
  - submit a request to AWS, or incease traffic no more than 50% over 5 minute interval.
Use external and internal ELBs
External: web tier use only private IP addresses
Internal: loadbalance between multiple internal tiers. Must have subnet
availablity to acoommodate ELB scaling (/27 or greater with at least 8 free IP
addresses.)

Elasticity via Auto Scaling
scale in and out, rebalance across AZs, add remote from ELB is applicable.

**Auto Scaling**

Types of scaling:
  - manual
  - by schedule
  - by policy
  - auto-rebalance

Auto scaling strategies must include proper bootstrapping of provisioned EC2 instances
AMI management
Software to install or configure (including rebooting)  Discovery or registration of new instances
A low-touch, highly dynamic approach is necessary to meet high availability SLAs

Lifecycle Management

Initialize and decommission your instances cleanly

Pending state
- After launch but before it is attached to the ASG
- Configure software, create, format, and attach EBS volumes, connect the instance to message queues

Terminating state
- Detached from the ASG but before it has been terminated
- Do any additional work to fully decommission the instance
 - capture a final snapshot of any work in progress,
 - move log files to long-term storage
 - hold malfunctioning instances off to the side for debugging
