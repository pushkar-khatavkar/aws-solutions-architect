## Module 01: Introduction

**Core idea:** AWS architectures are built on the Well-Architected Framework's five pillars and the Region/AZ global infrastructure hierarchy.

**Must-know services:** **AWS Well-Architected Tool**, **Amazon CloudFront**, **Amazon Route 53**, **AWS GovCloud (US)**, **AWS Local Zones**

**Key facts:**
- Five pillars: **Security**, **Reliability**, **Cost Optimization**, **Operational Excellence**, **Performance Efficiency**.
- Hierarchy: Region → **≥2 AZs** → data center (one data center belongs to exactly **one AZ**).
- AWS never auto-replicates data across regions — **customer's responsibility**.
- Deploy core apps **N+1** across data centers to avoid outages.
- Memorize all **six cloud advantages**, not just cost savings.

---

## Module 02: The Simplest Architectures

**Core idea:** Amazon S3 object storage, with the right storage class and access controls, is the simplest durable foundation for cloud architectures.

**Must-know services:** **Amazon S3**, **Amazon S3 Glacier**, **Amazon S3 Intelligent-Tiering**, **AWS Snowball**, **AWS DataSync**, **AWS Transfer Family**, **AWS Snowmobile**

**Key facts:**
- S3 durability is **99.999999999%** (11 nines); object storage only (full re-upload to change).
- **Block Public Access** is ON by default — must be explicitly disabled for public access.
- Max object size **5 TB**; console upload capped at **160 GB** — use multipart/CLI above that.
- Glacier retrieval: **Expedited** (1–5 min), **Standard** (3–5 hrs), **Bulk** (5–12 hrs) — never instant.
- Data transfer scales by volume: CLI/API → **DataSync** → **Transfer Family** → **Snowball** → **Snowmobile**.

---

## Module 03: Adding a Compute Layer

**Core idea:** EC2 compute choices (AMI, storage, instance family, pricing model, placement) are cost/performance/durability trade-offs matched to the workload.

**Must-know services:** **Amazon EC2**, **Amazon EBS**, **Amazon EFS**, **Amazon FSx**, **EC2 Instance Store**, **AWS Compute Optimizer**

**Key facts:**
- **EBS** = single-instance, persistent block storage; **EFS/FSx** = shared, multi-instance file systems.
- **Instance store** is ephemeral — wiped on stop/terminate, never for durable data.
- **Spot Instances** get a **2-minute** interruption warning; only for fault-tolerant workloads.
- Standard **Reserved Instances** (~75% discount, least flexible) vs. **Savings Plans** (flexible, cross-family).
- **Dedicated Host** = specific physical server (BYOL); **Dedicated Instance** = isolated but non-specific hardware.
- Placement groups: **Cluster** (lowest latency, 1 AZ), **Spread** (max **7/AZ**), **Partition** (big distributed systems).

---

## Module 04: Adding a Database Layer

**Core idea:** Pick relational vs. non-relational based on schema/query needs, and prefer managed services (RDS/DynamoDB) over self-hosted databases.

**Must-know services:** **Amazon RDS**, **Amazon Aurora**, **Amazon DynamoDB**, **Amazon Redshift**, **AWS Database Migration Service**, **AWS Schema Conversion Tool**

**Key facts:**
- Relational = fixed schema, joins, vertical scaling; non-relational = dynamic schema, horizontal scaling.
- DynamoDB default read is **eventually consistent** (**0.5 RCU**); strongly consistent costs **1 RCU**.
- **AWS DMS** does NOT convert schema — pair with **Schema Conversion Tool** for heterogeneous migrations.
- DMS requires source or target to be in **EC2/RDS** — no direct on-prem-to-on-prem migration.
- Aurora: up to **5x MySQL** / **3x PostgreSQL** throughput, **64TB** max storage, 6-way replication across 3 AZs.

---

## Module 05: Networking in AWS Part 1

**Core idea:** Amazon VPC provides the isolated network foundation, secured in layers by stateful security groups and stateless network ACLs.

**Must-know services:** **Amazon VPC**, **Amazon EC2**, **Internet Gateway**, **NAT Gateway**, **Security Groups**, **Network ACLs**, **Elastic IP Addresses**

**Key facts:**
- Security groups = **stateful** (instance-level); network ACLs = **stateless** (subnet-level) — don't mix up.
- Each subnet lives in exactly **one AZ**; VPC CIDR range is **/16 to /28**.
- AWS reserves **5 IPs** per subnet; default VPC and Elastic IP limits are **5 per Region**.
- Private subnets reach internet only via **NAT gateway** in a public subnet.
- Design subnets by internet accessibility (public/private), not by application tier.

---

## Module 06: Networking in AWS Part 2

**Core idea:** Connect VPCs and on-premises networks privately, then distribute and route traffic across AZs/Regions for high availability.

**Must-know services:** **AWS Direct Connect**, **AWS Transit Gateway**, **VPC Peering**, **VPC Endpoints**, **Elastic Load Balancing**, **Amazon Route 53**, **AWS Global Accelerator**

**Key facts:**
- **VPC Peering** = simple, non-transitive, 1:1; **Transit Gateway** = hub-and-spoke, scales to **5,000 VPCs**.
- **Gateway endpoints** only support **S3** and **DynamoDB**; everything else uses **Interface Endpoints** (PrivateLink).
- **ALB** = Layer 7 (HTTP/HTTPS); **NLB** = Layer 4 (TCP/TLS/UDP, static IP); **CLB** = legacy.
- Default HA guidance: start with **≥2 Availability Zones**.
- **Global Accelerator** gives near-instant failover vs. Route 53's DNS TTL-limited failover.

---

## Module 07: AWS Identity and Access Management (IAM)

**Core idea:** Grant least-privilege access via IAM roles/policies rather than long-term credentials, and govern multiple accounts with AWS Organizations.

**Must-know services:** **AWS IAM**, **AWS STS**, **Amazon Cognito**, **AWS Organizations**, **AWS Landing Zone**, **AWS CloudTrail**

**Key facts:**
- Policy evaluation order: **explicit deny** > explicit allow > implicit deny (default).
- Root user access **cannot be restricted** — secure it, use an IAM admin user daily instead.
- Check both **identity-based** and **resource-based** policies when access is denied.
- Roles grant **temporary credentials** via assumption — no long-term credential sharing.
- A **Service Control Policy** (Organizations) caps what IAM policies can ever allow in an account.

---

## Module 08: Elasticity, High Availability, and Monitoring

**Core idea:** Monitoring drives elastic, auto-scaling architectures that expand/contract compute and database capacity to match demand.

**Must-know services:** **Amazon CloudWatch**, **AWS CloudTrail**, **Amazon EC2 Auto Scaling**, **Amazon Aurora**, **Amazon DynamoDB**, **Amazon VPC Flow Logs**

**Key facts:**
- High availability = **fault tolerance** + **recoverability** + **scalability**.
- Elasticity types: **time-based**, **volume-based**, **predictive-based**.
- Aurora supports up to **15 read replicas**; **Aurora Serverless** scales with zero downtime.
- DynamoDB **Adaptive Capacity** fixes hot partitions, NOT bad partition key design.
- DynamoDB: switch provisioned→on-demand only **once per day**; on-demand→provisioned anytime.

---

## Module 09: Automation

**Core idea:** Replace manual infrastructure changes with Infrastructure as Code and managed automation tools, trading control for convenience.

**Must-know services:** **AWS CloudFormation**, **AWS Systems Manager**, **AWS OpsWorks**, **AWS Elastic Beanstalk**

**Key facts:**
- CloudFormation templates can be **JSON or YAML** — functionally equivalent.
- **Change sets** preview updates; **DeletionPolicy** protects/backs up resources on delete — don't confuse.
- Convenience/control spectrum: **EC2** → **CloudFormation** → **OpsWorks** → **Elastic Beanstalk**.
- **Quick Starts** are AWS/partner best-practice templates, not custom solutions.
- **Elastic Beanstalk** still exposes everything provisioned underneath — not a black box.

---

## Module 10: Caching

**Core idea:** Cache static, frequently-accessed, staleness-tolerant data at the edge, session, or database layer to cut latency, load, and cost.

**Must-know services:** **Amazon CloudFront**, **Amazon DynamoDB Accelerator (DAX)**, **Amazon ElastiCache**, **AWS WAF**, **Amazon Route 53**

**Key facts:**
- Cache invalidation: **TTL** (cheap) > renaming objects > explicit invalidation (expensive, last resort).
- **Write-through** = always fresh, double-write cost; **lazy loading** = efficient, stale/cache-miss risk.
- **Memcached** = multi-threaded, no persistence; **Redis** = rich data types, Multi-AZ failover, backup/restore.
- **DAX** is DynamoDB-only (API-compatible); **ElastiCache** is general-purpose in-memory cache.
- Only cache static or staleness-tolerant data — never unique or rapidly changing data.

---

## Module 11: Building Decoupled Architectures

**Core idea:** Insert managed messaging intermediaries (queues, topics) between tiers so failure or scaling in one doesn't cascade to others.

**Must-know services:** **Amazon SQS**, **Amazon SNS**, **Amazon MQ**, **AWS KMS**, **AWS Lambda**

**Key facts:**
- **SNS** = push/pub-sub/no persistence/one-to-many; **SQS** = poll/point-to-point/persistent.
- **FIFO queues**: exact-once, ordered, capped at **300 msg/s** (**3,000/s** batched); **Standard** = near-unlimited.
- **Visibility timeout** prevents duplicate processing of an in-flight message.
- Use a **dead-letter queue** to capture repeatedly failing messages.
- Choose **Amazon MQ** for lift-and-shift of existing broker protocols; **SQS/SNS** for cloud-native apps.

---

## Module 12: Microservices and Serverless Architectures

**Core idea:** Build independent, API-communicating microservices on containers or serverless compute instead of a monolith.

**Must-know services:** **AWS Lambda**, **Amazon ECS**, **AWS Fargate**, **Amazon API Gateway**, **AWS Step Functions**, **Amazon EKS**

**Key facts:**
- ECS **EC2 launch type** = you manage cluster; **Fargate** = no server management.
- Containers skip the hypervisor — start in milliseconds, smaller than VM images.
- **Lambda** execution capped at **15 minutes**; max **5 layers**; **250 MB** unzipped package limit.
- **Step Functions** uses JSON **Amazon States Language** to build retry/parallel/choice state machines.
- Put **SQS** between app and database to prevent lost orders from deadlocks/race conditions.

---

## Module 13: RTO/RPO and Backup Recovery Setup

**Core idea:** Choose a disaster recovery strategy by balancing cost against your required RPO (data loss) and RTO (downtime).

**Must-know services:** **Amazon S3**, **AWS Backup**, **Amazon EBS**, **AWS Storage Gateway**, **Amazon Route 53**, **AWS Direct Connect**

**Key facts:**
- **RPO** = max acceptable data loss (backup frequency); **RTO** = max acceptable downtime.
- Four DR strategies by cost/speed: **Backup & Restore** < **Pilot Light** < **Warm Standby** < **Multi-Site Active-Active**.
- **Pilot Light** = data replication only; **Warm Standby** = scaled-down but fully functional, always on.
- **Storage Gateway** has exactly three types: **File**, **Volume**, **Tape** — one interface per instance.
- DynamoDB point-in-time recovery covers up to **35 days**.

---

## Module 14: Optimizations and Review

**Core idea:** Critique any architecture against four questions: right resources, resiliency, loose coupling, and scalability.

**Must-know services:** **Amazon EC2**, **Amazon RDS**, **AWS Auto Scaling**, **Amazon SQS**, **AWS Well-Architected Tool**

**Key facts:**
- The **4 review questions**: right resources, resilient, loosely coupled, scalable.
- **"Replace, then investigate"** — swap failed resources first, root-cause after.
- Right-sizing needs roughly **3 months** of usage data.
- Since **2017**, **Spot Instances** have no bidding; diversify pools to cut interruption risk.
- Spot Instances can save up to **90%** off On-Demand pricing.

---

## Module 15: Course Wrap Up

**Core idea:** Use AWS Training and Certification resources to continue building and validating AWS skills after the course.

**Must-know services:** **AWS Certification**, **AWS Training**

**Key facts:**
- **AWS Certification** validates AWS skills and knowledge.
- **AWS Training** builds skills to design, develop, deploy, and manage AWS applications.
- **Self-Paced Labs** offer hands-on practice.
- No new architectural content — administrative/closing module only.

---

## Appendix: Cloud Economics, Shared Responsibility, and Security Deep Dive

**Core idea:** AWS secures "of the cloud" infrastructure while the customer secures "in the cloud" data, access, and configuration.

**Must-know services:** **AWS Shield**, **AWS WAF**, **AWS Key Management Service (AWS KMS)**, **AWS CloudTrail**, **AWS IAM**

**Key facts:**
- **Shared Responsibility**: AWS = "of the cloud" (infrastructure); customer = "in the cloud" (OS, IAM, data encryption).
- **Shield Standard** is free/automatic (**L3/L4**); **Shield Advanced** adds the **DDoS Response Team** (**L7**).
- **KMS envelope encryption**: master key (never leaves KMS) encrypts data keys, which encrypt the data.
- **WAF** operates at **Layer 7**, rule-based (OWASP Top 10, rate limiting, allow/deny lists).
- KMS auto-rotates keys **annually** — imported key material must be rotated manually.
- Recall the **six cloud economics advantages**: expense, scale, capacity, speed, focus, global.
