# AWS Re:Invent 2015

http://events-aws.qwiklab.com/?ref_=7
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


#### AWS Storage Options
- Pick the right storage for the workload  Amazon EC2
 - Amazon EC2 Local Instance Store (Ephemeral volumes)
  - Magnetic and SSD
  - Amazon EBS (Elastic Block Storage)
  - Magnetic, General Purpose (SSD) or PIOPS (SSD)
 - Amazon EFS: NAS  Object Storage
 - Amazon S3 (Simple Storage Service)
 - Amazon Glacier (Archival/Cold Storage)

Each has a unique combination of performance, durability, cost and interface

**EBS Snapshots**
EBS is designed for 99.999 availablility, but is only designed for a single AZ.
Increase availability by snapshotting and replcation. Take snapshots regularly.
Snapshots are stored durably in S3, which allows you to generate new volumes to
any AZ in a region.

To obtain consistent snapshots:
- Quiese file systems OR unmount filesystem.
- Use OS specific tools.
- Reduce snapshot overheard through using a replica, or during off-hours. Recent improvements to the EBS subsystem have decreased the amount of overhead of snapshots on IO performance.

**Replication**

Increase your availability by replicating your volumes to another AZ.

#### EBS Performance

**GP2 Volumes**

SSD-backed, single digit millisecond latencies. General workload capabilities with no EBS I/O charges. Your default.
- Each volume up to 1TB gets an IO credit of 5.4 million IOPS
 - 30mins x 60sec/min x 3000iops= 5,400,000 IOPS
- Burstable up to 3000 IOPS for 30 minutes
 - Once IOPS are depleted, it’s replenished at the baseline rate
- Sustained performance until IOPS are depleted
- Volumes greater than 1TB will always have a minimum of 3000 IOPS

**PIOPS Volumes**
- Use PIOPS for consistent IO performance  Up to 20,000 16KB IOPS/volume
 - Stripe multiple volumes for >20000 IOPS
 - Recommended for high performance workloads
- Use with EBS Optimized instance types
 - http://amzn.to/1LP2wSL
- Dive Deep
 -  Maximizing EC2 and Elastic Block Store Disk Performance  http://bit.ly/1J8yiKj


#### Instance Storage

- Storage local to Amazon EC2 instance
- Temporary, volatile block storage
- Not all instances have instance storage: e.g. T2, M4, C4  Must be configured
	at launch of instance
- Suitable for sequential I/O
- Reduces network & EBS contention
- Instance Store backed AMIs
- Root/boot volume is on instance store  Ideal for stateless servers
- Good for establishing the provenance of data security? The data doesn't leave
	machine and traverse the network.

Ranges from 0 GB to 48 TB of storage. Can be used for transient or replicated
data. Swap, caches, temp tables, intermediate data, non-persistent data SSD
Instance storage ideal for high performance workloads. Up to 365,000 4 KB
random read IOPS and up to 315,000 4 KB random first write IOPS

Instance storage is neither Highly Available nor Fault Tolerant. Data is lost
if instance is stopped or terminated. Back up and/or replicate important data.
Enable EC2 Termination Protection. Use with data that doesn’t need to be
retained across instance stop/start

**Pre-warming**

For new volumes, EBS wipes blocks clean. For restored volumes, EBS instantiates
blocks from its snapshot. To obtain best performance, pre-warm the volume •
http://amzn.to/1evnVDn. Recommended for high performance workloads Pre-warming
applies to EBS or Instance volumes For new volumes or restores from snapshots
￼￼￼￼￼￼￼
Pre-warm new Volumes: Write to all the blocks before first use. Large volumes
can take hours to write. Restored Volumes: Read all blocks
```
Linux: dd if=/dev/md0 of=/dev/null
Windows: NTFS full format
```

#### Amazon Elastic File System (EFS)
Fully managed file system for EC2 instances. Provides standard file system
semantics. Works with standard operating system APIs Sharable across thousands
of instances. Elastically grows to petabyte scale. Delivers performance for a
wide variety of workloads. Highly available and durable. NFS v4–based
- File system grows/shrinks automatically.
- No need for storage provisioning.
- Pay for the storage that you use.

#### Amazon S3

Durable storage for any object. Buckets act like drives with folder structures
within. Granular access control, server side encryption, multipart uploads,
object versioning, object lifecyle, access logging, web content hosting,
notifications.

**Cross region replication**

Increases availability of your S3 objects. Every object uploaded to a S3 bucket
is automatically replicated to a bucket in a different AWS region. Replicates
new objects. Enable versioning and cross-region replication. Ideal for
compliance/regulatory reasons where data must be replicated 100s of miles
apart.

**Performance Considerations

Use CloudFront for faster access to S3 objects. CloudFront caches requested
objects closer to end users. At higher usage, the price for CloudFront data
transfer is lower than the price for Amazon S3 data transfer.

**Naming conventions**

Important if PUT req/s > 100 or GET req/s > 300. Not necessary for occasional
bursts of 100-800 req/s  Avoid dates or ordered sequence. Add randomness to Amazon S3 keys. Can store objects as a hash of their name.

Add the original name as metadata:
`deadmau5_mix.mp3”  0aa316fb000eae52921aab1b4697424958a53ad9`

Prepend keyname with short hash
`0aa3-deadmau5_mix.mp3`

Epoch time (reverse)
`5321354831-deadmau5_mix.mp3`

**Performance Considerations**

Ensure that you're using multi-part uploads:
- Improved throughput
- Quick recovery from any network issues
- SDKs/CLI and other tools provide multi-part upload

**AMIs and EBS Snapshots**

AMIs & snapshots are specific to a region. For HA/DR purposes, ensure your
custom AMIs & snapshots are in the regions that you intend to use. Both can be
copied across regions:
- Geographic expansion
- HA/DR
- Migration
Avoid/reduce AMI copy by dynamically bootstrapping. Also, the snapshot/AMI copy is incremental

## Database High Availability

AWS offers highly available managed databases (RDS, Aurora, DynamoDB), as well as self-managed instances. Use EBS GP2 Volumes for low/medium workloads. EBS PIOPS provides up to 20,000 IOPS/volume for high performance workloads:
- Volumes can be logically striped for even higher IOPS. Separate data from logs and swap;
- Use separate EBS volumes not just separate partition
- Use EBS Optimized instances with EBS PIOPS volumes
Ephemeral disk (aka instance storage) for temporary data:
- Reduces EBS I/O consumption
- Only if TempDB isn’t storing critical values

**HA Considerations**

Each DB has features for HA, like mirroring, replication, etc., which mitigate the impact of individual system failure
- SQL Server mirroring, SQL Server 2012 AlwaysOn Availability Groups  MySQL async replication
- Oracle Data Guard
- Oracle RAC (unavailable, co-locate at AWS Partner and use DirectConnect to link with AWS)
Create AMIs of your Amazon EC2 database instance to preserve any complex configuration changes you’ve made for subsequent launches.

**NoSQL Databases**

Can process large amounts of data with high availability. Depends on the NoSQL
solution, configuration and architecture. Broad category with different
implementations & data models. Distributed FT model is usually a common feature
with NoSQL databases

Does your app need transaction support, ACID compliance, joins, SQL? Try
refactoring database hotspots to NoSQL solutions. NoSQL DBs can offer increases
in flexibity, availability, scalability and performance.

### Dataase as a Service

#### Amazon RDS

Simple and fast to deploy, scale. AWS handles patching, backups, replication. Predictable performance. GP2 SSD storage is suitable for a broad range of database workloads
- Provides baseline of 3 IOPS/GB and ability to burst to 3,000 IOPS
PIOPS SSD storage is suitable for I/O-intensive database workloads. Provides flexibility to provision I/O ranging from 1,000 to 30,000 IOPS. Magnetic storage may be used for small database workloads where data is accessed less frequently Backups are automatic if configured.

**Scaling Databases**

Whether self-managed or RDS, you can scale vertically for CPU/RAM/disk-IO.
- For read heavy databases, cache heavily with in memory caching. Create read-only databases to relieve pressure on master database
- For write intensive databases, consider:
 - PIOPS
 - Sharding
 - NoSQL options if applicable

Scaling read servers: Optimize master for OLTP and read-only instances for
table scans. Resize read-only instances as needed to boost reporting
performance. Use short-term read-only instances to save cost during monthly
reporting. Promote to standalone server Eventually consistent reads

Add in caching: small, frequently-accessed items are candidates for read caching. Reduce server-side latency to microseconds. Eliminate “hot spot” performance barriers. Offload heavy read activity from database

Database sharding: creates a share-nothing architecture, and splits large partitionable tables across multiple, small database servers. Will likely require periodic rebalancing.
Challenges:
- key management
- queue aggregation
- cross-shard queries

**HA RDS**
Use multi-AZ deployment to enhance fault tolerance and availability for production workloads. Allows or Automatic failover in case of
- Loss of availability in primary AZ
- Loss of connectivity to primary
- Host or storage failure on primary  Vertical scaling
- Software patching
- Rebooting of primary
RDS uses DNS to transfer traffic to your backup instances if there are any issues. Can also copy database snapshots to another AWS region, for use as a warm standby for DR. Brings data close to your customers.

#### Amazon Aurora

A relational database using a service oriented approach. Allows for the storage to scale out and becom mult-tenant. Backwards compatible with MySQL 5.6. Can create a databse in minutes. 
- Instance on-volume snapshots
- Continuous, incremental off-volume snapshots to S3.
- Storage scaling up to 64TB
- Automatic restripinng mirror repair, hot spot maagement, ecryption.

Highly available by default. 6-wayreplicationacross 3 AZs. 4 of 6 write quorum. Automatic fallback to 3 of 4 if an AZ is unavailable  3 of 6 read quorum. SSD, scale-out, multi-tenant storage  Seamless storage scalability. Up to 64TB database size

Log-structured storage
Many small segments, each with their own redo logs
log pages used to generate data pages.
Eliminates chatter between database and storage.

**Self Healing and fault tolerant**
Lose two copies or an AZ failure without read or write availability impact Lose three copies without read availability impact
Automatic detection, replication, and repair

#### Amazon DynamoDB

SSD-backed service with consistent latency in single digit milliseconds that doesn't increase as stroage grows.
Durability
- Synchronous replication for high durability
- A write is only acknowledged (committed) once it exists in at least two physical data centers
- All writes occur to disk, not memory
- Backup to other AWS regions
Availability
- Regional service
- Spans multiple AZ’s
- All data is continuously replicated to multiple AZ’s

**Provisioning Throughput**

Can provision throughput needed
Provision the throughput needed for each table
 Set at table creation
 Example: My table needs 1,000 writes/second and 5,000 reads/seconds of capacity
Increase / decrease any time via API call
Pay for throughput and storage (not instances)
 Reserved capacity available to optimize for cost
From a HA perspective, this feature ensures DynamoDB can serve requests
during periods of extremely high demand
Benefits
 Simpler capacity planning
 Don’t have to think in terms of servers and disk IOPS  Won’t be locked in your peak for month or year

## High Availability Design Patterns

#### Multi-Server Pattern

#### Multi-Datacenter Pattern

#### High Availability Database Pattern

#### EC2 Auto Recovery

Able to detect when the instances is impaired via Cloudwatch monitoring. Gives
you a replacement instance with the same instance ID, metadata, IP address. In
memory data is lost. Only works with certain instance types, and instance
storage cannot be used.

#### Floating IP Pattern

#### Floating Interface Pattern

#### State Sharing

#### Scheduled Scale out

#### Job Observer Pattern

#### Bootstrap Instance

#### High Availability NAT

#### HA NAT - Squid Proxy

## VPC and Direct Connect Availability Patterns

#### Connectivity Options
VPN connectivity connects dual redundant tunnels between your on-premises equipment and AWS.
- Redundant IPsec tunnels
- Supports BGP and static routing
- Redundant customer gateways
AWS Direct Connect establishes a private network connection between your network and one of the AWS Regions.

# Building Loosely Coupled Applications on AWS

#### Loose Coupling
Degree of dependence among components. Direct knowledge of one component of
dependent components. Reduce coupling as much as possible Components can be
built & tested independently Limit component failure impact. Core principle of
Service Oriented Architecture

The looser they are coupled, the bigger they scale, and the more fault tolerant they get.


https://d0.awsstatic.com/whitepapers/architecture/AWS_Well-Architected_Framework.pdf
