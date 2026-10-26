## Key Outcomes

The session focused on designing and implementing an AWS RDS architecture using MySQL, covering the full workflow from architecture diagramming to live database creation, connectivity via a jump host (EC2), and running SQL queries. Students created a VPC-based architecture with public and private subnets, deployed an EC2 jump server, connected to a private MySQL RDS instance via endpoint, and executed core SQL commands. The session concluded with guidance on how to frame this hands-on work as professional database administration experience for resumes and interviews.

---

## Decisions Made

- **[draw.io](https://draw.io)** selected as the recommended tool for creating real-time cloud architecture diagrams, based on participant experience. 
- **MySQL 8.4** chosen as the RDS engine version for the session; version 8.x confirmed as the standard production answer. 
- **Free Tier** template selected for learning; **Sandbox** offered as an alternative for students without free tier access. 
- **Auto-generated password** used via AWS self-managed credential option; students instructed to copy credentials immediately as they cannot be recovered later. 
- **Automated connectivity** (not manual) selected when linking EC2 to RDS, to avoid manual subnet, routing, and NAT gateway configuration errors. 
- **Existing security group** should be selected (not "create new") during automated RDS-EC2 connectivity setup. 
- **20 GB storage** allocated for the EC2 jump server to support query execution and software installation. 
- **Ubuntu 24.04 LTS** (referred to as 26.04 in session) selected as the default OS for the EC2 instance. 
- **PPK key pair** created (named "RDS key") for third-party tool (PuTTY) connectivity. 

---

## Architecture Design

### VPC Architecture ([draw.io](https://draw.io))

- **VPC** as the outer boundary containing two subnets: 
    - **Public Subnet**: Hosts the EC2 jump server (Jump Host Server), connected to the internet via an **Internet Gateway (IGW)**
    - **Private Subnet**: Hosts the MySQL RDS instance — not directly internet-accessible
- **NAT Gateway**: Required in the public subnet to enable private subnet outbound connectivity; automatically configured when using AWS automated setup. 
- **Client/User** sits outside the VPC and connects via internet → IGW → EC2 → RDS (port **3306**). 
- Architecture analogy: public subnet = hall/dining area; private subnet = private bedroom — same concept of segmenting a house. 

### Why a Jump Host?

- RDS is in a private subnet and cannot be accessed directly from the internet. 
- The EC2 instance acts as a **mediator** — users SSH into the jump server first, then connect to RDS from there. 
- Named **Jump Host Server** / **Bastion Server** in architecture. 

### Production-Level Architecture Elements Discussed

- **Load Balancer** (Classic/ALB) in the public subnet distributes traffic to EC2/application servers. 
- **Auto Scaling Group** increases EC2 instances when traffic grows. 
- **RDS Standby** (secondary database) for disaster recovery — data synced/replicated in real time. 
- **S3 Bucket** stores application logs, database dumps, and audit data (not row/column data — that stays in RDS). 
- **CloudWatch** receives all RDS and EC2 logs for monitoring; basic monitoring enabled by default on AWS services. 
- **IAM** for access control; **CloudTrail** for auditing. 
- **Snapshot backups** stored separately; point-in-time recovery available. 

---

## RDS Configuration Walkthrough

### Database Creation Steps

1. Navigate to AWS Console → **RDS** → **Databases** → **Create Database** → **Full Configuration**. 
2. Engine: **MySQL 8.4**; Template: **Free Tier** (or Sandbox). 
3. Master username: **admin** (used by ~95% of companies). 
4. Password: **Auto-generated** by AWS; copy immediately from "View Credential Details" pop-up. 
5. Storage: **20 GB** (production standard cited as **512 GB** with auto-scaling enabled). 
6. **Connect to EC2 instance**: Select "Connect to EC2 Compute Resource" → choose the jump server instance → set to **Automated** setup. 
7. Public access: **Blocked** — only private connections allowed. 
8. Monitoring: **CloudWatch** integration enabled; logs sent automatically. 

### RDS Tiers Explained

|    Tier    |       Instances       |               Use Case                |
|------------|-----------------------|---------------------------------------|
| Free Tier  | 1 (read + write)      | Learning/testing                      |
| Dev/Test   | 2 (primary + standby) | Development with basic DR             |
| Production | 3+ (multi-AZ)         | High availability, negligible failure |

### Amazon Aurora vs Standard RDS

- **Aurora** = AWS enterprise-managed MySQL/PostgreSQL; 5× throughput vs standard MySQL. 
- Up to **128 TB** auto-scaling on SSD storage. 
- **6-way replication**, up to **15 read replicas**. 
- Automatic **failover detection** — hardware failures handled transparently; no manual intervention. 
- Multi-AZ: primary + multiple standbys; if one AZ fails, traffic shifts automatically. 
- Higher cost than standard RDS; not recommended unless enterprise features are needed. 

---

## EC2 Jump Server Setup

### Instance Configuration

- **Name**: Jump Host Server 
- **OS**: Ubuntu 24.04 LTS (64-bit) 
- **Key pair**: PPK format (RDS key) for PuTTY connectivity 
- **Storage**: 20 GB 
- **Traffic**: Allow SSH (port 22) inbound 

### Connection Commands (run in EC2 terminal)

```bash
# Step 1: Become root user
sudo -i

# Step 2: Update package list
sudo apt update

# Step 3: Upgrade installed packages
sudo apt upgrade -y

# Step 4: Install MySQL client
sudo apt install mysql-client

# Step 5: Connect to RDS
mysql -h <RDS_ENDPOINT> -u admin -p
# Do NOT add a space after -p; enter password at prompt
```


# AWS RDS Interview Preparation Cheatbook
## Batch-45 | Day 8 — AWS RDS (Part 2)

This cheatbook is based on the Day-8 AWS RDS session notes. It covers the architecture built in the session: a private MySQL RDS database, an EC2 jump host in a public subnet, security groups, CloudWatch monitoring, backups, and production architecture concepts.

> **Interview tip:** Explain the reason behind each design choice, not just the steps you clicked in the AWS Console.

---

# Part 1: 20 Interview Questions and Answers

### 1. What is Amazon RDS?
**Answer:** Amazon Relational Database Service (RDS) is a managed service for relational databases. AWS manages many routine tasks such as infrastructure provisioning, backups, and supported maintenance activities, while we manage database configuration, access, schema, and data.

### 2. Which database engine and version did we use in the lab?
**Answer:** We used MySQL 8.4. In an interview, mention that the selected engine/version should match the application's compatibility and support requirements.

### 3. Why did we place RDS in a private subnet?
**Answer:** A private subnet reduces direct exposure to the internet. Applications or administrators reach the database through approved network paths, such as an application server or a jump host, rather than connecting directly from the public internet.

### 4. What is an EC2 jump host or bastion server?
**Answer:** It is a controlled server used as an entry point to reach resources in a private network. In our lab, we SSH into the EC2 jump host and use the MySQL client on it to connect to the private RDS endpoint.

### 5. How does a client connect to the lab's private RDS database?
**Answer:** The administrator connects to the jump host over SSH. From that host, the MySQL client connects to the RDS endpoint on TCP port 3306. Security group rules must allow the intended traffic.

### 6. What is an RDS endpoint?
**Answer:** It is the DNS name used by clients to connect to an RDS database. Use the endpoint shown in the RDS console instead of hard-coding an IP address, because the underlying address can change.

### 7. Which port does MySQL use by default?
**Answer:** MySQL typically uses TCP port 3306. The RDS security group should allow this port only from trusted sources, such as the jump host's security group or the application tier.

### 8. What is the role of a security group?
**Answer:** A security group acts as a virtual firewall for AWS resources. It controls inbound and outbound traffic. For this design, SSH access is allowed to the jump host from an approved administrator IP, and MySQL access is allowed to RDS only from the required source.

### 9. Why should RDS public access be disabled for this design?
**Answer:** The database is intended to be reached privately through the VPC. Disabling public access reduces exposure and supports a least-privilege network design. Private access still requires correct routing, DNS, and security group rules.

### 10. What is the purpose of a VPC?
**Answer:** A Virtual Private Cloud provides a logically isolated network in AWS. It lets us define IP ranges, subnets, route tables, gateways, and network security controls for resources such as EC2 and RDS.

### 11. What is the difference between a public and a private subnet?
**Answer:** A public subnet has a route to an Internet Gateway for internet connectivity, while a private subnet does not have a direct route for inbound internet access. A private subnet may use a NAT Gateway for outbound internet access when required.

### 12. What is the role of an Internet Gateway?
**Answer:** An Internet Gateway enables communication between a VPC and the internet when routing and resource addressing/security settings permit it. In the lab architecture, it provides the internet path to the public jump host.

### 13. Why might a NAT Gateway be used?
**Answer:** A NAT Gateway lets resources in private subnets initiate outbound IPv4 connections, for example to download updates, without accepting unsolicited inbound internet connections. It is not required merely for private EC2-to-RDS traffic inside the VPC.

### 14. What is Multi-AZ deployment in RDS?
**Answer:** Multi-AZ is an availability feature that maintains a standby in another Availability Zone for supported RDS configurations. AWS can fail over to the standby when the primary has a qualifying failure. It is mainly for availability, not for scaling read traffic.

### 15. What is the difference between a Multi-AZ standby and a read replica?
**Answer:** A Multi-AZ standby primarily supports availability and failover. A read replica is primarily used to scale read workloads and can be used in supported recovery or promotion scenarios. Their replication behavior and capabilities depend on the database engine and configuration.

### 16. What are RDS backups and snapshots used for?
**Answer:** Automated backups support point-in-time recovery within the configured retention period. Manual snapshots provide a saved recovery point that can be retained independently. Backup and restore requirements should be tested, not just configured.

### 17. How can we monitor RDS?
**Answer:** Use Amazon CloudWatch metrics and alarms for indicators such as CPU utilization, free storage, database connections, and read/write performance. RDS can also publish supported database logs to CloudWatch Logs when configured.

### 18. What is AWS CloudTrail used for in this architecture?
**Answer:** CloudTrail records AWS API activity, helping teams audit actions such as changes to RDS settings, security groups, or IAM policies. It complements database metrics and database logs rather than replacing them.

### 19. Where should database backups and application logs be stored?
**Answer:** RDS backups and snapshots should use the appropriate RDS backup features and retention controls. S3 can store exported data, database dumps, application logs, and audit files where appropriate. Live relational rows and columns remain in the database, not in an S3 bucket.

### 20. What is Amazon Aurora, and when might we choose it over standard RDS for MySQL?
**Answer:** Amazon Aurora is an AWS relational database engine compatible with MySQL and PostgreSQL. It offers a different storage and availability architecture and may suit demanding workloads, but cost, compatibility, performance needs, and operational requirements should be evaluated before choosing it.

---

# Part 2: 20 Scenario-Based Questions and Answers

### Scenario 1: You cannot connect to RDS from your laptop. What do you check?
**Answer:**
1. Confirm that RDS is intentionally private and public access is disabled.
2. Connect to the approved jump host over SSH.
3. From the jump host, test the RDS endpoint and port 3306.
4. Check the RDS security group source rules, VPC routing, DNS resolution, and database status.
5. Confirm the endpoint, username, password, and database engine/client compatibility.

**Interview explanation:** A private database should not be made public just to fix a connectivity issue.

### Scenario 2: SSH to the jump host fails.
**Answer:** Verify that the EC2 instance is running, the correct public IP/DNS is being used, the route to an Internet Gateway exists, and the security group permits TCP 22 from the administrator's current public IP. Also check the key pair, username (for Ubuntu images, commonly `ubuntu`), network ACLs, and local firewall.

### Scenario 3: SSH works, but the MySQL connection times out.
**Answer:** Check whether the RDS instance is available, whether the endpoint resolves, and whether TCP 3306 is allowed from the jump host to RDS. The RDS security group should normally reference the jump host's security group as the source. Confirm VPC/subnet routing and network ACLs too.

### Scenario 4: The MySQL client reports “Access denied”.
**Answer:** This usually indicates an authentication or database privilege issue rather than a network timeout. Verify the username and password, whether the account is allowed to connect from that host, and whether the account has the required privileges. Reset credentials through an approved process if needed; do not share passwords in chat or source control.

### Scenario 5: The RDS endpoint does not resolve on the jump host.
**Answer:** Check that the endpoint was copied correctly, the EC2 and RDS resources have the expected VPC connectivity, and VPC DNS support/hostnames are configured appropriately. Test name resolution and confirm the database instance is available.

### Scenario 6: Your application needs database access. Should you open port 3306 to `0.0.0.0/0`?
**Answer:** No. Restrict port 3306 to the application's security group or another narrowly defined trusted source. Opening the database to every IPv4 address creates unnecessary exposure.

### Scenario 7: The database is running out of storage.
**Answer:** Check free storage and storage growth trends in CloudWatch. Confirm whether storage autoscaling is enabled and configured with an appropriate maximum, then plan a supported storage increase if necessary. Investigate large tables, indexes, logs, and retention policies before the system reaches a critical state.

### Scenario 8: CPU utilization remains high on RDS.
**Answer:** Review CloudWatch metrics, Performance Insights if enabled, slow-query logs where supported, connection counts, and query execution plans. Look for expensive queries, missing or ineffective indexes, connection storms, and undersized instance capacity. Optimize first, then scale resources if the workload requires it.

### Scenario 9: Database connections suddenly reach the limit.
**Answer:** Inspect connection metrics and application behavior. Check for connection leaks, too many application workers, and missing connection pooling. Set sensible connection limits and timeouts, and consider a managed connection-pooling option where appropriate. Increase database capacity only after understanding the cause.

### Scenario 10: An Availability Zone fails. How do you reduce database downtime?
**Answer:** Use a suitable Multi-AZ RDS deployment and verify that the application reconnects and handles endpoint failover correctly. Monitor failover events and test the recovery procedure. A Multi-AZ standby improves availability, but it does not remove the need for application retries and recovery testing.

### Scenario 11: A developer accidentally deletes important rows.
**Answer:** Stop further harmful writes if appropriate, preserve evidence, and assess the recovery point needed. Restore to a new RDS instance using point-in-time recovery or a suitable snapshot, validate the data, and plan a controlled way to recover the missing records. Avoid overwriting the current database before validating the restored copy.

### Scenario 12: You need a copy of production data for testing.
**Answer:** Restore a snapshot or create an appropriate database copy in an isolated non-production environment. Apply data masking or anonymization for sensitive data, use least-privilege access, and avoid connecting test workloads to production by mistake.

### Scenario 13: A password was committed to a public Git repository.
**Answer:** Treat it as compromised. Rotate the database credential immediately, update the approved secret store and dependent applications, review logs for suspicious activity, and remove the secret from the repository history where appropriate. Deleting the visible line alone does not make the old credential safe.

### Scenario 14: The database must be accessed by several administrators.
**Answer:** Avoid sharing a single administrator password. Use individually attributable access where supported, IAM and identity controls where applicable, a controlled access path such as a bastion or managed session service, and audit logs. Grant each person only the permissions needed for their role.

### Scenario 15: You need to patch or upgrade the MySQL engine.
**Answer:** Check the supported upgrade path and application compatibility, review release notes, and test the change on a restored copy or staging environment. Take/verify backups, schedule a maintenance window, monitor the upgrade, and prepare a documented rollback or restore plan. Follow the engine's supported upgrade process.

### Scenario 16: You need to serve many read-heavy requests.
**Answer:** First inspect query performance, indexes, caching opportunities, and connection management. If the workload still needs more read capacity, consider read replicas or an architecture suited to the engine and consistency requirements. Multi-AZ standby capacity is not a substitute for read scaling.

### Scenario 17: Your organization requires database activity auditing.
**Answer:** Identify which events must be audited. Use CloudTrail for AWS control-plane/API actions and configure supported RDS database logs or engine-specific auditing for database activity. Send the relevant logs to a protected central destination, set retention and access controls, and verify that the required events are actually captured.

### Scenario 18: Your private EC2 instance needs package updates but has no internet route.
**Answer:** If outbound internet access is required, configure an appropriate egress path such as a NAT Gateway in a public subnet with correct route tables. Consider VPC endpoints for supported AWS services to avoid unnecessary internet egress. Do not add a public IP to a private resource as the default fix.

### Scenario 19: Your monthly AWS bill increased after the RDS lab.
**Answer:** Review the RDS instance, storage, backup/snapshot retention, Multi-AZ configuration, NAT Gateway hours/data processing, EC2 uptime, and data transfer charges. Stop or delete lab resources that are no longer needed, while preserving required data and backups. Set budgets and billing alerts; verify the current Free Tier or account-specific eligibility instead of assuming every resource is free.

### Scenario 20: You are asked to explain this project in an interview.
**Answer:** Give a clear architecture walkthrough:
1. Designed a VPC with public and private subnets.
2. Deployed an Ubuntu EC2 jump host in the public subnet.
3. Created a private MySQL RDS instance and disabled public access.
4. Configured security groups so SSH was restricted and MySQL access came from the approved jump host.
5. Installed the MySQL client and connected using the RDS endpoint on port 3306.
6. Ran SQL commands and discussed monitoring, backups, Multi-AZ availability, IAM, and CloudTrail.

**Sample interview summary:** “I built a VPC-based AWS RDS lab with a private MySQL database and an EC2 bastion host. I configured controlled network access, connected using the RDS endpoint and MySQL client, and reviewed production considerations such as monitoring, backups, availability, and auditing.”

---

# Quick Revision: Commands Used in the Lab

```bash
# Update package lists
sudo apt update

# Upgrade installed packages
sudo apt upgrade -y

# Install MySQL client
sudo apt install mysql-client

# Connect to the private RDS endpoint
mysql -h <RDS_ENDPOINT> -u admin -p
```

Replace `<RDS_ENDPOINT>` with the endpoint shown in the RDS console. Enter the password when prompted. Do not put a space between `-p` and the password prompt, and do not store credentials in shell history or Git.

## Quick Checklist Before an Interview

- [ ] Explain why RDS is private.
- [ ] Explain the jump host / bastion pattern.
- [ ] Describe VPC, public/private subnets, IGW, and NAT Gateway.
- [ ] Explain security group rules for SSH (22) and MySQL (3306).
- [ ] Distinguish Multi-AZ availability from read replicas.
- [ ] Explain snapshots and point-in-time recovery.
- [ ] Name useful CloudWatch metrics and alarms.
- [ ] Explain the difference between CloudTrail API auditing and database logs.
- [ ] Discuss secrets handling, least privilege, and cost control.
- [ ] Walk through the project confidently from architecture to SQL connection.

---

*Prepared for CloudDevOpsHub — Multi-Cloud & DevOps with AI | Batch-45*

