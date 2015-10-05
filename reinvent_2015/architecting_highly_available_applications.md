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


**Chaos Monkey** Failures happen when you don't expect it Test on your
timeline, not chance's timeline.  See Netflix's 'Principles of Chaos':
http://www.principlesofchaos.org/

```
Advances in large-scale, distributed
software systems are changing the game for software engineering.  As an
industry, we are quick to adopt practices that increase flexibility of
development and velocity of deployment.  An urgent question follows on the
heels of these benefits: How much confidence we can have in the complex systems
that we put into production? - See more at:
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




