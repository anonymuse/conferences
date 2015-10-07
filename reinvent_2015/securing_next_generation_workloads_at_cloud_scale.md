Securing Next-Generation Workloads at Cloud Scale
https://events-aws.qwiklab.com/classrooms/4376

# AWS Cloudformation Primer

Cloudformation (CFN) allows you to define a “template” which is composed of
different “resources” and then provision that template into repeatable, live,
“stacks”. CFN adds value by providing a single service interface and
abstracting the underlying API semantics and implementation mechanics.  For
example: update vs replace (of EBS storage type, etc.).  CFn templates can hook
into external configuration management frameworks such as Chef/Puppet/etc.

Infrastructure is code

- We already know how to manage “code” lifecycle.
- Let CFn perform state changes and govern who calls CFn.

Split ownership templates

- Merge code from Sec, Dev and Ops
- Baseline and build management

Can be implemented as a security control for:

- System testing
- Configuration baseline
- State transition engine
- Inventory and documentation

**Structured deployment with split ownership**

Create skeleton: infrastructure team
Define resources: devops
Execute: security

#### Customer-side security logic

Synchronize AWS and your business landscape
- Trigger actions in external systems such as the opening of firewall ports.
- Tag AWS resources with organizational billing-codes for chargeback.
Enforce a customer business workflow
- Force dev/test/prod code promotion workflow.
- Apply resource/software quotas against corporate policy.
By-definition, this is client-side logic (from the AWS perspective).
- Jenkins is a great tool for this purpose

#### AWS Service Catalog
Product Catalog

- Create custom catalogs containing products incorporating standard CloudFormation architectures and configurations
Centralized Control
- Provides IT with central management over portfolio of services that end users can browse and launch

UI layer on top of CloudFormation Templates

- Follows familiar constructs of parameters, resources and output.

User interaction is governed through IAM policies.

- IAM policies control user “product” discovery, usage rights (quotas).
- Stacks can be deployed via a Service IAM Roles.
 - User is not privileged to deploy the AWS resources natively.
- Administrators can impose conditions of stack parameters.

# Applied Governance

Building processes to segment and encapsulate application packages, consisting
of:

- deployment mechanisms for software artifacts
- Configuration building blocks
- Managing segmentation and security dependencies

Deployment mechanisms for software artifacts allow you to deploy code. This is
compromised of software artifacts and deployment services.

**Configuration Building Blocks**

Cloudformation templates: definitions for infrastructure as code
Task definition: application constraints
Application Specification File: what controls must be run in order to deploy
your application. This can be serivce interaction or other validation tests.kkkG

Use configuration building blocks (CBB) to enforce security. This allows you to
remove accidental conflicts because you're not making any changese to the
application software, as your maintaining these interactions as separate
layers. Also, make security processes continuous and automatic. Make sure that
you encapsulate software artifacts and implement controls one level up.

Control changes to this framework via IAM.

# Scaling Trust Decisions

Autonomous security

There are design time versus run time decisions.

Autonomous Security is a flow framework of some sort
Configuration is EC2::LaunchConfiguration
Resources is EC2::Instance

This allows for

- External ownership
- Intelligent decision flow
`Inspect > Decide > Apple > Validate > Action > Log`
- This model can be enforced for compliance
- Creates a security inventory for audits

In order to scale trust:

- Use combination of image prep and run time decisions. You should still be
	compiling and checksumming images.
- Tie to authentication on-demand for secure access of automated processes
- Combine with Continuous scanning
 - Code artifacts
 - Resources (AWS Config)

What is the root of trust for Amazon EC2 instances? Translated in "W's":

- Who[isthatinstance]?
- What[isit’sconfiguration]?
- Where[istheinstanceprovisioned]?
- When[wasitlaunched]?
- Why[whatpurposedoesalloftheaboveserve]?

**Security Flow Best Practices**

Avoid Pull patterns

- Requires an available source/path from target system
- Own/compromise the target and you own the access process

Push only allows outgoing traffic

- Instances have no path back to source
- Instances have no knowledge on the process/flow

Ephemeral/on-demand scripting on target

- Store in memory
- Create scripts on target on-demand at execution time
- Temporary credentials for first run

Keep source store on-instance (worker node) or S3 using dedicated VPC with VPC-E for S3 policy

### Tooling

**Triggering**

In an AutoScaling Group, you can:

Use Lifecycle hooks
Run automatic repair of non approved instances
SNS for integration with Workers/Lambda
Use least privileges on ASG instances • UseIAMInstanceRoles
Unique credentials per instance

**Worker**

Flow engine for Ruby/Java
Allow synchronous or asynchronous flows
Deciders/Workers can be EC2 instances of Lambda.
Can be integrated into Elastic Beanstalk

- Use internal or external source for validation tasks/tests • Checksumfilesbeforeuse
- UseLambda/ElasticBeanstalkworkers

**Real-life Trust Applications**

Agent deployment/validation
Integration with Configuration Management (Chef/Puppet)
Unique per-instance configuration Cross-instance network authentication
Joining an Active Directory (more on that later).

**Intrusion Detection Lifecycle Policy Workflow**

- Take anEBS Snapshot from instance.
- Restore EBS Snapshot on a isolated high-assurance system.
- Validate OS state against signature DB.
 - On success: retire instance
 - On failure: call a human

 # Embedded IAM
 Creating dynamic policy via AWS SDK

IAM enables control of who can do what to your AWS resources. Accept or deny
any operation based on customer- configurable parameters. API calls captured in
AWS CloudTrail and correlated to AWS Config configuration items.

**Application Authentication**

IAM authentication is relatively trivial. Several mechanism availabile in IAM
user credentials, instances profiles, Cognitio, and SAML.

IAM user credentials are outdated. Use EC2 Instance Profiles. The instance profile is:
- a container for an IAM role
- allows you to tie and IAM role to an instance at startup
- API calls on the instance inherit these credentials
- must restart instance to pick up a new role

Can use Amazon Cognito as an external identity provider for authentication
via OpenID. SAML federation is available.

**AWS Service Roles**
A role that you create and authorize an AWS service to assume on your behalf.
Useful for maintaining control and visibility of your AWS resources.
For example:
 Amazon EC2 (Making API calls from instances).
 AWS Directory Service (Console access from DS users).  AWS Lambda (Function access to other AWS resources).

**AWS Security Token Service**
AWS Security Token Service
Request temporary, limited-privilege credentials for authenticated users.
Assume a pre-defined baseline role.
Overlay dynamic policy during token creation. Least privilege between role and policy.



