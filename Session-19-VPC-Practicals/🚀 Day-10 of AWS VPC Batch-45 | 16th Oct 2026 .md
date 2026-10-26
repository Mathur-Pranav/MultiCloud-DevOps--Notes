## Key Outcomes

The session delivered an extensive, practical exploration of AWS Network Security and Enterprise High Availability / Disaster Recovery (DR) architectures. Building directly on the foundational VPC concepts established in the previous session, the discussion focused on how production architectures protect multi-tier environments and maintain business continuity under failure conditions. Students conducted a deep-dive comparison between AWS Security Groups (instance-level, stateful firewalls) and Network Access Control Lists (NACLs, subnet-level, stateless firewalls), analyzing rule evaluation orders, precedence, and real-time IP blacklisting.

The session dissected the industry-standard 3-Tier Web Application Architecture (Web, Application, and Database tiers), demonstrating how to enforce strict defense-in-depth through Security Group referencing/chaining without hard-coding IP addresses. Inter-VPC communication was explored through VPC Peering, highlighting CIDR constraints and non-transitive routing limitations. Crucially, the session dismantled common industry misconceptions by contrasting High Availability (Multi-AZ intra-region synchronous failover) with Disaster Recovery (Cross-Region asynchronous replication). Students evaluated the four primary AWS Disaster Recovery strategies—Backup & Restore, Pilot Light, Warm Standby, and Multi-Region Active-Active—benchmarking their cost, complexity, Recovery Point Objective (RPO), and Recovery Time Objective (RTO).

---

## Decisions Made

- **Standardized session documentation:** Reaffirmed the structured Zoom AI Companion format (Key Outcomes, Decisions Made, Topics Covered, Open Questions, Action Items, 20 Interview Q&A, and 20 Scenario Q&A) as the required blueprint for all repository session notes.
- **Clear separation between HA and DR:** Mandated that students never present Multi-AZ as a Disaster Recovery solution in interviews; Multi-AZ is strictly intra-region High Availability, whereas Disaster Recovery requires a separate AWS Region.
- **NACL rule numbering best practice:** Adopted the industry standard of numbering NACL rules in increments of 10 or 100 (e.g., 100, 110, 120) to permit non-disruptive rule insertions between existing rules without requiring complete renumbering.
- **Security Group chaining standard:** In multi-tier application architectures, security groups must reference parent security group IDs (e.g., App SG allows traffic only from Web SG; DB SG allows traffic only from App SG) rather than static CIDR blocks.
- **Weekend hands-on homework:** Students must build two distinct VPCs with non-overlapping CIDR blocks, configure a VPC Peering connection, update route tables, and demonstrate file transfer between EC2 instances in each VPC over private IPs.
- **1000 technical questions milestone:** Reemphasized the ongoing initiative to compile and master 1000 core Cloud & DevOps interview questions, with dedicated review sessions scheduled for the upcoming weekend.

---

## Topics Covered

### AWS Network Security: Security Groups vs. Network ACLs (NACLs)

Understanding the distinction between Security Groups and Network ACLs is critical for designing secure cloud perimeters:

| Architectural Dimension | Security Group (SG) | Network Access Control List (NACL) |
| :--- | :--- | :--- |
| **Operating Level** | Instance / Elastic Network Interface (ENI) level | Subnet boundary level |
| **State Nature** | **Stateful**: Outbound response traffic is automatically permitted if inbound request is allowed (and vice versa) | **Stateless**: Inbound and outbound traffic are evaluated completely independently |
| **Supported Rule Types** | **ALLOW rules only** (cannot explicitly deny an IP) | **ALLOW and DENY rules** |
| **Rule Evaluation** | All rules are evaluated before permitting traffic | Evaluated in strict **numerical order** (lowest number first); first match applies |
| **Default Configuration** | Blocks all inbound traffic; allows all outbound traffic | Default NACL allows all inbound & outbound; Custom NACL blocks all by default |
| **Return Traffic Requirement** | Automatically handled by connection tracking | Requires explicit outbound rules on **ephemeral ports** (1024–65535) |

---

### Deep Dive into Network ACLs (NACLs)

- **Subnet-Level Firewall:** A NACL guards the entrance and exit of an entire subnet. Every packet entering or leaving a subnet must traverse the associated NACL.
- **Subnet Association Rule:** A subnet can be associated with **exactly one NACL** at a time. However, a single NACL can be associated with multiple subnets.
- **Rule Processing & Precedence:**
  - Rules are processed sequentially in ascending numerical order (e.g., Rule 10, Rule 20, Rule 100).
  - As soon as a packet matches a rule's condition, the action (**ALLOW** or **DENY**) is executed immediately, and no further rules are evaluated.
  - **The Default Asterisk (`*`) Rule:** Every NACL includes an unmodifiable default rule denoted by an asterisk (`*`) at the bottom. Any packet that does not match an explicit numbered rule hits the asterisk rule and is dropped (**DENY**).
- **Practical IP Whitelisting & Blacklisting:**
  - To allow only one specific IP while denying all others: place an **ALLOW** rule with a lower rule number (e.g., Rule 100: ALLOW `203.0.113.10/32`) and a broad **DENY** rule at a higher number (e.g., Rule 200: DENY `0.0.0.0/0`).
  - To block a specific malicious bot/attacker: place a **DENY** rule at a low rule number (e.g., Rule 50: DENY `198.51.100.4/32`) before the broad allow rules.
- **The Stateless Return Traffic Challenge (Ephemeral Ports):**
  - When an EC2 client initiates an outbound web request (e.g., `curl https://google.com`), the operating system assigns a dynamic client port known as an **ephemeral port** (typically TCP ports 1024–65535).
  - Because NACLs are stateless, return packets from external servers will be blocked unless the NACL's inbound rules explicitly allow return traffic on ephemeral ports (TCP 1024–65535).

---

### 3-Tier Enterprise Web Application Security Architecture

The session mapped out the industry-standard defense-in-depth architecture across subnets and security tiers:

1. **Web Tier (Presentation Tier):**
   - Deployed in **Public Subnets** across multiple Availability Zones.
   - Hosted on Internet-facing Application Load Balancers (ALBs) or Web EC2 instances (Nginx/Apache).
   - **Web Security Group:** Inbound: TCP port 80 (HTTP) and TCP port 443 (HTTPS) allowed from `0.0.0.0/0`. Outbound: Port 8080/custom app port allowed to App Security Group.
2. **Application Tier (Business Logic Tier):**
   - Deployed in **Private Subnets** across multiple Availability Zones (completely isolated from the public internet).
   - Hosted on internal EC2 instances running microservices (Node.js, Java Spring Boot, Python FastAPI).
   - **App Security Group:** Inbound: TCP port 8080 allowed **strictly from Web Security Group ID** (`sg-web-xxxx`). Outbound: TCP port 3306 allowed to DB Security Group.
3. **Database Tier (Data Persistence Tier):**
   - Deployed in separate, dedicated **Private Subnets** across multiple Availability Zones.
   - Hosted on managed Amazon RDS instances (MySQL / PostgreSQL).
   - **Database Security Group:** Inbound: TCP port 3306 (MySQL) or 5432 (PostgreSQL) allowed **strictly from App Security Group ID** (`sg-app-xxxx`). Outbound: Blocked or restricted.

**Security Group Chaining Advantage:** Referencing security group IDs eliminates reliance on private IP addresses. If auto-scaling spins up 50 new web or app servers, security group rules dynamically allow traffic without manual IP adjustments.

---

### Inter-VPC Connectivity: VPC Peering Architecture

- **What is VPC Peering?** A private network connection between two VPCs that routes traffic using private IPv4/IPv6 addresses, keeping all communication on the private AWS global network backbone without traversing the public internet.
- **CIDR Restriction:** Peered VPCs **must have non-overlapping CIDR blocks** (e.g., VPC A: `10.0.0.0/16` and VPC B: `10.1.0.0/16`). Peering between identical or overlapping address spaces is strictly prohibited.
- **Non-Transitive Routing:**
  - VPC Peering relationships are non-transitive: If VPC A is peered with VPC B, and VPC B is peered with VPC C, **VPC A cannot communicate with VPC C through VPC B**.
  - To enable communication between A and C, you must either establish a direct peering connection between A and C, or deploy an **AWS Transit Gateway** as a central hub.
- **Routing Configuration Requirement:** Establishing a peering connection requires three distinct steps:
  1. Creating and accepting the peering request.
  2. Adding routes in VPC A's route table pointing VPC B's CIDR to the peering ID (`pcx-xxxx`).
  3. Adding routes in VPC B's route table pointing VPC A's CIDR to the peering ID (`pcx-xxxx`).

---

### High Availability (Multi-AZ) vs. Disaster Recovery (DR)

Candidates frequently confuse High Availability with Disaster Recovery in technical interviews:

- **High Availability (HA) — Intra-Region:**
  - Designed to protect workloads against component, server, or entire **data center / Availability Zone** failures.
  - Implemented across multiple Availability Zones within the **same AWS Region** (e.g., `us-east-1a` and `us-east-1b`).
  - Uses **synchronous replication** for RDS Multi-AZ. When a write occurs on the primary, it is synchronously committed to the standby instance in the secondary AZ before acknowledging success.
  - Automatic DNS failover occurs in approximately 60–120 seconds.
  - **Limitation:** If a catastrophic regional disaster strikes (e.g., regional power grid failure, massive natural disaster, fiber cuts), the entire region and all its AZs can fail simultaneously.
- **Disaster Recovery (DR) — Inter-Region (Cross-Region):**
  - Designed to maintain business survival against catastrophic regional outages.
  - Implemented across **two or more distinct AWS Regions** (e.g., Primary in Mumbai `ap-south-1`, DR in Virginia `us-east-1`).
  - Uses **asynchronous cross-region replication** (e.g., RDS Cross-Region Read Replicas, S3 Cross-Region Replication).
  - Managed via global routing services such as **Amazon Route 53** with DNS health checks and automated failover routing policies.

---

### AWS Disaster Recovery (DR) Strategies & RTO / RPO

The session evaluated the four standardized Disaster Recovery strategies defined by AWS, balancing cost against recovery objectives:

- **Recovery Point Objective (RPO):** The maximum acceptable amount of data loss measured backward in time from the point of failure (e.g., "We can tolerate losing up to 15 minutes of transaction data").
- **Recovery Time Objective (RTO):** The maximum acceptable duration of system downtime to restore operations (e.g., "The application must be fully operational within 1 hour").

**The 4 Disaster Recovery Strategies:**
1. **Backup and Restore:**
   - Strategy: Periodic snapshots and backups are copied to a secondary AWS Region. If the primary fails, infrastructure is provisioned from scratch using Infrastructure as Code (Terraform/CloudFormation) and data is restored from backups.
   - Cost: **Lowest**.
   - RTO / RPO: **Highest** (RTO measured in hours/days; RPO depends on backup frequency).
2. **Pilot Light:**
   - Strategy: Core database and persistent data are continuously replicated to the secondary region. Core minimal servers (the "pilot light") are kept in a stopped or minimal standby state. Upon disaster, compute tiers are scaled up and provisioned to handle live traffic.
   - Cost: **Low to Moderate**.
   - RTO / RPO: **Moderate** (RTO in 10s of minutes; RPO minimal due to continuous data sync).
3. **Warm Standby:**
   - Strategy: A fully functional, scaled-down version of the complete production environment is always running in the secondary region. It handles minimal traffic or health checks. Upon failure, it immediately scales out to 100% capacity.
   - Cost: **Moderate to High**.
   - RTO / RPO: **Low** (RTO in minutes; RPO minimal).
4. **Multi-Region Active-Active:**
   - Strategy: Full production environments run concurrently in two or more AWS regions. Route 53 distributes live traffic globally based on latency, geolocation, or weighted policies. If one region suffers an outage, traffic is automatically routed to the surviving region.
   - Cost: **Highest** (effectively doubling infrastructure and data transfer costs).
   - RTO / RPO: **Near Zero** (instantaneous or sub-minute failover; near-zero data loss).

---

### Database Replication: Multi-AZ Standby vs. Read Replicas

- **RDS Multi-AZ Standby (HA):**
  - Sole purpose is automated failover and high availability.
  - Synchronous physical block-level replication.
  - The standby instance **cannot serve read queries or write queries**; it is completely passive until promoted during a failover.
- **RDS Read Replicas (Scalability & DR):**
  - Primary purpose is scaling read-heavy application workloads.
  - Asynchronous logical replication.
  - The replica **actively serves read requests** (via a dedicated reader endpoint).
  - Can be deployed in the same Availability Zone, cross-AZ, or **Cross-Region** (serving as a foundational building block for cross-region Disaster Recovery).
  - A Read Replica can be promoted to a standalone read/write database in the event of primary database corruption or regional failover.
- **Connection Routing in Practice:**
  - Modern applications use database proxies (e.g., **AWS RDS Proxy**, ProxySQL for MySQL, or PgBouncer/PgPool for PostgreSQL) to automatically route `SELECT` queries to Read Replicas while directing `INSERT`/`UPDATE`/`DELETE` queries to the primary instance.

---

## Pending Confirmation

- **VPC Peering lab completion:** Students must complete the assigned weekend lab: establishing a VPC Peering connection between two custom VPCs and testing private data transmission between EC2 instances.
- **Security group chaining validation:** Students should verify their 3-tier configurations to confirm that no database or backend application security groups contain `0.0.0.0/0` inbound rules.
- **Weekend interview question review:** Batch schedule to be finalized for reviewing the 1000 technical interview question bank and conducting mock peer evaluations.

---

## Open Questions

- **If a NACL has a DENY rule and an ALLOW rule, which one takes precedence?**  
  *Clarification:* NACL rules are evaluated strictly in ascending numerical order. Precedence is determined solely by the **rule number**, not the action type. If Rule 100 allows an IP and Rule 200 denies all traffic, the IP matching Rule 100 is permitted immediately, and Rule 200 is never evaluated for that packet.
- **Why do custom NACLs block all traffic by default while Default NACLs allow all traffic?**  
  *Clarification:* The AWS Default NACL is designed for immediate usability, containing rules (Rule 100) that allow all inbound and outbound traffic. Newly created Custom NACLs enforce zero-trust security by default, containing only the asterisk (`*`) rule that drops all inbound and outbound traffic until explicit allow rules are added.
- **What happens if an outbound web request is made through a NACL without ephemeral port rules?**  
  *Clarification:* The outbound request leaves the subnet successfully (assuming outbound port 80/443 is allowed). However, when the external server sends back the response on the client's temporary ephemeral port (TCP 1024–65535), the stateless NACL drops the returning packets unless an inbound rule permits TCP 1024–65535.
- **Can an RDS Multi-AZ standby replica be used to offload reporting queries?**  
  *Clarification:* No. In standard Amazon RDS Multi-AZ, the standby replica is passive and inaccessible. To offload read/reporting queries, you must provision an **RDS Read Replica**, which provides an active reader endpoint.
- **Can VPC Peering route traffic transitively through a middle VPC?**  
  *Clarification:* No. AWS VPC Peering does not support transitive routing (edge-to-edge routing). To achieve hub-and-spoke multi-VPC routing without establishing a full mesh of individual peering connections, use **AWS Transit Gateway**.
- **Is Multi-AZ considered Disaster Recovery?**  
  *Clarification:* Absolutely not. Multi-AZ is intra-region High Availability designed to withstand single data center / AZ failures. Disaster Recovery protects against regional catastrophe and requires deploying data and services across distinct AWS Regions.

---

## Action Items

- **All students:** Complete the weekend homework: create two separate custom VPCs with distinct CIDRs (`10.0.0.0/16` and `10.1.0.0/16`), establish VPC Peering, update route tables, and transfer a test file via `scp` or `curl` between private EC2 instances.
- **All students:** Practice constructing a 3-tier Security Group architecture by referencing Security Group IDs in inbound rules rather than hard-coding IP addresses.
- **All students:** Record and submit a 1-minute video explaining the difference between High Availability (Multi-AZ) and Disaster Recovery (DR).
- **All students:** Review the top 20 Network Security and DR interview questions to ensure point-to-point, concise answers.
- **Vikas:** Coordinate the weekend session focused on the 1000 technical interview questions review and student project validations.
- **Vikas:** Review and merge student GitHub pull requests for Day 8, Day 9, and Day 10 session summaries.

---

# Part 1: 20 AWS Network Security & Architecture Interview Questions and Answers

### 1. What is the fundamental difference between an AWS Security Group and a Network ACL?
**Answer:** A Security Group operates at the instance/ENI level, is **stateful** (return traffic is automatically allowed), and supports only **ALLOW** rules. A Network ACL (NACL) operates at the subnet boundary, is **stateless** (inbound and outbound rules must be configured separately), supports both **ALLOW and DENY** rules, and evaluates rules in strict numerical order.

### 2. What does it mean that Security Groups are "stateful"?
**Answer:** "Stateful" means the firewall automatically tracks the state of active network connections. If an inbound request is permitted by an inbound rule (e.g., port 443), the corresponding outbound response traffic is automatically allowed to leave the instance, regardless of outbound security group rules.

### 3. What does it mean that Network ACLs are "stateless"?
**Answer:** "Stateless" means the firewall evaluates each packet independently without connection tracking. If inbound traffic is allowed on port 80, the return response packets must be explicitly permitted by an outbound NACL rule (typically on ephemeral ports 1024–65535); otherwise, return traffic is dropped.

### 4. How are Network ACL rules evaluated when processing network traffic?
**Answer:** NACL rules are processed in ascending numerical order (e.g., 100, 110, 200). When a packet matches a rule, the action (ALLOW or DENY) is applied immediately, and evaluation stops. Any packet that does not match an explicit rule hits the default asterisk (`*`) rule and is denied.

### 5. Why should you number NACL rules in increments of 10 or 100?
**Answer:** Numbering rules in increments (such as 100, 110, 120) leaves room to insert new rules between existing rules later (e.g., adding rule 105 to block a specific IP) without having to delete and recreate subsequent rules.

### 6. What are ephemeral ports, and why are they critical in NACL configuration?
**Answer:** Ephemeral ports are short-lived, dynamic transport ports (typically TCP 1024–65535) chosen automatically by a client operating system when initiating outbound network connections. Because NACLs are stateless, return traffic from external servers targeting these client ports will be dropped unless inbound NACL rules explicitly allow ephemeral ports.

### 7. What is Security Group chaining (referencing)?
**Answer:** Security Group chaining is the practice of specifying another Security Group ID (e.g., `sg-web-xxxx`) as the source or destination in a security group rule instead of an IP CIDR block. It ensures that only instances associated with the referenced security group can communicate with the target, providing seamless scaling without managing individual IP addresses.

### 8. How do you design a secure 3-tier application architecture using Security Groups?
**Answer:** 
- **Web Tier:** Allows inbound HTTP (80) and HTTPS (443) from `0.0.0.0/0`.
- **Application Tier:** Allows inbound custom application port (e.g., 8080) strictly from the **Web Security Group ID**.
- **Database Tier:** Allows inbound database port (e.g., 3306 for MySQL) strictly from the **Application Security Group ID**.

### 9. What is VPC Peering, and what is its primary CIDR constraint?
**Answer:** VPC Peering is a private networking connection between two VPCs enabling direct communication over private IP addresses via the AWS global network backbone. Its primary constraint is that the two VPCs **must have non-overlapping CIDR blocks**.

### 10. Does AWS VPC Peering support transitive routing?
**Answer:** No. VPC Peering does not support transitive routing. If VPC A is peered with VPC B, and VPC B is peered with VPC C, traffic from VPC A cannot route through VPC B to reach VPC C. A direct peering connection or an AWS Transit Gateway is required.

### 11. What is the difference between High Availability (HA) and Disaster Recovery (DR)?
**Answer:** High Availability (such as Multi-AZ) is deployed within a **single AWS Region** across multiple Availability Zones using synchronous replication to protect against hardware or data center failures with automatic failover. Disaster Recovery spans **multiple AWS Regions** using asynchronous replication to ensure business continuity in the event of an entire regional catastrophe.

### 12. What do RTO and RPO stand for in Disaster Recovery planning?
**Answer:**
- **RTO (Recovery Time Objective):** The maximum acceptable duration of system downtime to restore application availability following a disaster.
- **RPO (Recovery Point Objective):** The maximum acceptable amount of data loss measured in time between the last data backup/sync and the failure event.

### 13. Name the four AWS Disaster Recovery strategies from lowest to highest cost.
**Answer:**
1. **Backup and Restore** (lowest cost, highest RTO/RPO).
2. **Pilot Light** (minimal core data replicated, compute scaled up upon disaster).
3. **Warm Standby** (scaled-down full environment continuously running).
4. **Multi-Region Active-Active** (highest cost, near-zero RTO/RPO, full traffic active in multiple regions).

### 14. Can an RDS Multi-AZ standby instance process read traffic?
**Answer:** No. In standard RDS Multi-AZ, the standby instance is purely passive for high availability and automated failover. It cannot serve read or write queries. To serve read traffic, you must create an **RDS Read Replica**.

### 15. What are the key differences between RDS Multi-AZ and RDS Read Replicas?
**Answer:**
- **Multi-AZ:** Synchronous physical replication, intra-region across AZs, passive standby, automatic DNS failover, zero read scaling.
- **Read Replica:** Asynchronous logical replication, same-region or cross-region, active reader endpoint, manual promotion to primary, used for read scaling and Disaster Recovery.

### 16. How does AWS Route 53 support Disaster Recovery architectures?
**Answer:** Route 53 provides **DNS Health Checks** and **Failover Routing Policies**. When health checks detect that the primary region's endpoints are unresponsive, Route 53 automatically updates DNS records to route global user traffic to the secondary Disaster Recovery region.

### 17. How can you immediately block a single malicious IP address from attacking an EC2 instance?
**Answer:** Add an inbound rule to the **Network ACL (NACL)** associated with the instance's subnet with a low rule number (e.g., Rule 50), specifying the malicious IP (e.g., `203.0.113.45/32`) and setting the action to **DENY**. Security Groups cannot be used because they do not support deny rules.

### 18. What is the role of an AWS RDS Proxy in database architecture?
**Answer:** RDS Proxy maintains a managed pool of database connections, reducing CPU and memory overhead on the database caused by opening and closing connections. It accelerates application failover times by up to 66% and can route queries efficiently between reader and writer endpoints.

### 19. What is AWS Transit Gateway, and when is it preferred over VPC Peering?
**Answer:** AWS Transit Gateway is a centralized cloud router that connects hundreds or thousands of VPCs and on-premises networks in a hub-and-spoke topology. It is preferred over VPC Peering when managing complex multi-VPC environments, as it eliminates the need to manage complex, non-transitive point-to-point peering meshes ($N(N-1)/2$ connections).

### 20. If an EC2 instance has multiple Security Groups attached, how does AWS evaluate them?
**Answer:** AWS evaluates **all rules across all attached Security Groups** cumulatively. If any rule across any attached security group allows the traffic, the packet is permitted.

---

# Part 2: 20 AWS Network Security & Architecture Scenario-Based Questions and Answers

### 1. Scenario: A developer can connect to a public EC2 web server over SSH, but after creating a custom NACL, outbound `curl https://aws.amazon.com` hangs and times out. What is wrong?
**Answer:** The custom NACL allows outbound traffic on port 443, but because NACLs are stateless, it lacks an inbound rule allowing return traffic on **ephemeral ports (TCP 1024–65535)**. Add an inbound NACL rule allowing TCP ports 1024–65535 from `0.0.0.0/0`.

### 2. Scenario: Your web server in a public subnet needs to communicate with an application server in a private subnet. How should you configure the Application Security Group?
**Answer:** Configure the Application Security Group's inbound rule to allow the application's port (e.g., TCP 8080) with the source set to the **Web Security Group ID** (`sg-web-xxxx`). Do not use the public or private IP CIDR of the web server.

### 3. Scenario: A security team demands that an IP range (`198.51.100.0/24`) be completely blocked from communicating with your database subnet. How do you implement this?
**Answer:** Open the Network ACL associated with the database subnet. Create an inbound rule with a low rule number (e.g., Rule 10) specifying Source: `198.51.100.0/24`, Port: ALL, Action: **DENY**. Ensure this rule number is lower than any existing ALLOW rules.

### 4. Scenario: Two teams attempt to peer VPC A (`172.16.0.0/16`) with VPC B (`172.16.10.0/24`). The peering request fails to establish active communication. Why?
**Answer:** The two VPC CIDR blocks overlap (`172.16.10.0/24` is a subset of `172.16.0.0/16`). AWS strictly prohibits VPC Peering between overlapping address spaces to prevent routing conflicts. One VPC must be recreated with a distinct CIDR block.

### 5. Scenario: You established a VPC Peering connection between VPC A and VPC B, but EC2 instances in VPC A cannot ping instances in VPC B. What steps must you verify?
**Answer:**
1. Verify that the VPC Peering connection status is **Active** (the request was accepted).
2. Check VPC A's route table: ensure there is a route for VPC B's CIDR pointing to the Peering Connection ID (`pcx-xxxx`).
3. Check VPC B's route table: ensure there is a route for VPC A's CIDR pointing to `pcx-xxxx`.
4. Verify Security Groups on both instances to ensure ICMP/traffic is permitted from the peer VPC CIDR or security group.

### 6. Scenario: An enterprise requires an RTO of under 10 minutes and an RPO of under 5 minutes for a business-critical application, but wants to minimize idle compute costs. Which DR strategy fits best?
**Answer:** The **Pilot Light** strategy. The database is continuously replicated asynchronously to the secondary region (achieving near-zero RPO). A minimal core infrastructure or automation scripts (Terraform/CloudFormation) remain on standby. Upon disaster, Auto Scaling groups rapidly launch the compute instances within 5–10 minutes (achieving RTO < 10 mins) without paying for full idle compute running continuously.

### 7. Scenario: A primary database in an RDS Multi-AZ deployment experiences a hardware failure. How does RDS handle the failover, and what application change is required?
**Answer:** RDS automatically detects the failure and promotes the synchronous standby replica in the secondary AZ to primary status in approximately 60–120 seconds. It updates the DB instance's DNS endpoint to resolve to the new primary instance's IP. **Zero application configuration changes are required**, provided the application connects via the RDS DNS endpoint and implements basic connection retry logic.

### 8. Scenario: An e-commerce platform experiences heavy slow-downs during reporting batch jobs run against its primary RDS PostgreSQL database. How should this be resolved without impacting writes?
**Answer:** Provision an **RDS Read Replica** in the same region. Direct the reporting queries to the Read Replica's dedicated **reader endpoint**. This offloads read traffic completely from the primary database, ensuring transactional write throughput remains unaffected.

### 9. Scenario: A company needs to deploy a global web service where users in Europe are served from Frankfurt and users in the US are served from Ohio, with automatic regional failover if one region goes down. How do you design this?
**Answer:** Deploy a **Multi-Region Active-Active** architecture. Host the web application and cross-region replicated databases in both `eu-central-1` and `us-east-2`. Configure Amazon Route 53 with **Geolocation or Latency-based Routing** backed by **Health Checks**; if Frankfurt becomes unhealthy, Route 53 automatically redirects European traffic to Ohio.

### 10. Scenario: An engineer accidentally configured an inbound rule in a Security Group allowing port 22 from `0.0.0.0/0`, and another rule allowing port 22 only from `10.0.0.5/32`. Which rule wins?
**Answer:** Both rules apply simultaneously. Because Security Groups are permissive (ALLOW only), the broader rule (`0.0.0.0/0`) allows SSH access from any IP address globally. The restrictive rule does not override the permissive rule. The `0.0.0.0/0` rule must be deleted to enforce least privilege.

### 11. Scenario: You have three VPCs: VPC-A, VPC-B, and VPC-C. VPC-A is peered to VPC-B, and VPC-B is peered to VPC-C. Why cannot VPC-A communicate with VPC-C?
**Answer:** VPC Peering is non-transitive. Traffic cannot hop through an intermediate VPC. To enable communication, you must create a dedicated VPC Peering connection directly between VPC-A and VPC-C, or attach all three VPCs to an AWS Transit Gateway.

### 12. Scenario: A company wants to run a zero-downtime database upgrade on a production RDS MySQL database. How can Read Replicas assist?
**Answer:** Provision an RDS Read Replica, allow replication to sync, and perform the database upgrade on the Read Replica. Once verified, promote the Read Replica to primary and point the application endpoint to the new database, minimizing downtime to the brief cutover window.

### 13. Scenario: A financial institution cannot tolerate any data loss (RPO = 0) and requires sub-second failover for its primary database across data center failures. What architecture must be deployed?
**Answer:** Deploy Amazon RDS in a **Multi-AZ Deployment** (or Amazon Aurora Multi-AZ with multiple read/write replicas). Synchronous replication guarantees that transactions are written to storage in multiple AZs before completing, ensuring RPO = 0 and automated failover in the event of an AZ failure.

### 14. Scenario: A junior admin created a custom NACL and associated it with a public subnet, but instances inside lost all internet access immediately. Why?
**Answer:** Custom NACLs default to a single asterisk (`*`) rule that blocks all inbound and outbound traffic (**DENY**). The admin must explicitly add an inbound rule allowing port 80/443 and ephemeral ports, and an outbound rule allowing `0.0.0.0/0`.

### 15. Scenario: An application running on an EC2 instance in a private subnet connects to an external third-party API. Where should outbound traffic be inspected and filtered by domain name?
**Answer:** Deploy an **AWS Network Firewall** or a forward proxy (e.g., Squid) in the public subnet. Route the private subnet's outbound traffic through the firewall before reaching the NAT Gateway, applying domain-filtering rules (e.g., allowing only `api.partner.com`).

### 16. Scenario: A security audit discovers that developers are connecting directly to private RDS instances using database clients from their laptops. How do you re-architect access securely?
**Answer:** Remove public access and revoke direct laptop IP access in the RDS Security Group. Deploy an **EC2 Bastion Host** (or configure **AWS Systems Manager Session Manager / EC2 Instance Connect Endpoint**) in a public subnet. Update the RDS Security Group to accept traffic on port 3306/5432 strictly from the Bastion Host's security group ID.

### 17. Scenario: A company maintains an active-passive disaster recovery setup. How does Route 53 detect that the primary region is down and redirect users?
**Answer:** Route 53 uses **Health Checks** configured against the primary region's Application Load Balancer endpoint. If the endpoint fails consecutive health checks (e.g., returning 5xx status or timing out), Route 53 triggers a failover routing policy, routing DNS queries to the secondary region's IP/endpoint.

### 18. Scenario: An engineer wants to allow traffic from one specific subnet in VPC-A to an RDS database in VPC-B over a peering connection. How should the RDS Security Group be configured?
**Answer:** In the RDS Security Group in VPC-B, add an inbound rule permitting TCP port 3306 from the specific CIDR block of the source subnet in VPC-A (e.g., `10.0.1.0/24`). (Note: Security Group referencing across VPC Peering is supported only within the same region).

### 19. Scenario: During a mock Disaster Recovery drill, a team promotes an RDS Cross-Region Read Replica to primary. What happens to its replication from the original primary?
**Answer:** Promoting a Read Replica permanently breaks the replication link between the original primary and the replica. The replica becomes an independent read/write database instance. Once the drill is over or the primary region recovers, replication must be manually re-established in reverse if required.

### 20. Scenario: An application microservice in a containerized cluster must talk to 15 different backend services across different VPCs without maintaining dozens of peering connections. What AWS service should be used?
**Answer:** Deploy an **AWS Transit Gateway** (or **AWS PrivateLink / VPC Endpoints** for service-to-service access). Attach each VPC to the Transit Gateway and manage central routing tables to facilitate scalable any-to-any or hub-and-spoke connectivity.

---

# Quick Revision Checklist

- **Security Groups:** Stateful, instance/ENI level, allow rules only, supports SG referencing.
- **Network ACLs:** Stateless, subnet level, allow & deny rules, evaluated in ascending numerical order.
- **NACL Asterisk Rule:** Default unnumbered rule (`*`) that denies all unmatched traffic.
- **Ephemeral Ports:** Dynamic client ports (TCP 1024–65535) required in inbound NACL rules for stateless return traffic.
- **3-Tier SG Chaining:** Web SG allows public HTTP/HTTPS $\rightarrow$ App SG allows traffic only from Web SG $\rightarrow$ DB SG allows traffic only from App SG.
- **VPC Peering:** Direct private connection between VPCs; requires non-overlapping CIDRs; strictly non-transitive.
- **High Availability (HA):** Intra-region (Multi-AZ), synchronous replication, automatic failover in 1–2 minutes.
- **Disaster Recovery (DR):** Inter-region (Cross-Region), asynchronous replication, mitigates total regional loss.
- **RDS Multi-AZ Standby:** Inactive/passive standby for failover only; cannot serve read queries.
- **RDS Read Replica:** Active reader endpoint for scaling read performance and powering Cross-Region DR.
- **DR Strategies:** Backup & Restore (lowest cost, highest RTO/RPO) $\rightarrow$ Pilot Light $\rightarrow$ Warm Standby $\rightarrow$ Multi-Region Active-Active (highest cost, near-zero RTO/RPO).

---

## Suggested Practice Lab

1. **VPC Peering Lab:**
   - Create two custom VPCs: `VPC-A` (`10.0.0.0/16`) and `VPC-B` (`10.1.0.0/16`).
   - Create a public subnet and launch an EC2 instance in each VPC.
   - Create a **VPC Peering Connection** between `VPC-A` and `VPC-B` and accept the request.
   - Update Route Tables: add `10.1.0.0/16` targeting `pcx-xxxx` in `VPC-A`, and `10.0.0.0/16` targeting `pcx-xxxx` in `VPC-B`.
   - Ping and SSH/SCP between the instances over their private IP addresses.
2. **NACL Testing Lab:**
   - Associate a custom NACL with an EC2 instance's subnet.
   - Add an inbound ALLOW rule for SSH (port 22) and outbound ALLOW for all traffic. Observe what happens to web browsing.
   - Add inbound ALLOW for ephemeral ports (TCP 1024–65535) and observe web traffic restore.
   - Add a high-priority DENY rule for your specific IP on port 22 and verify immediate connection termination.
3. **Lab Cleanup:**
   - Terminate EC2 instances, delete the VPC Peering connection, delete custom NACLs, and delete the custom VPCs to avoid unwanted charges.

**Important:** AWS networking features, quotas, and service pricing are subject to updates. Always verify current AWS documentation and account configuration when designing production environments.
