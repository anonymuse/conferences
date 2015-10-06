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
