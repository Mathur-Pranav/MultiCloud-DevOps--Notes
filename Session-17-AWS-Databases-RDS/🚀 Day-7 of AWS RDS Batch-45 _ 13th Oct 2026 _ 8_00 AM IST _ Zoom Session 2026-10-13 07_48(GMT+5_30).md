## Key Outcomes

The session introduced AWS RDS as a fully managed PaaS relational database service, covering why databases must remain private, the differences between relational and non-relational databases, and practical creation of Postgres and MySQL RDS instances. Students explored key database engines (MySQL, Oracle, MongoDB, Cassandra), their use cases, and best practices for security, backups, and monitoring. Two live RDS databases were created and deleted during the session to demonstrate the full provisioning workflow. 

---

## Decisions Made

- **Recording method changed to local laptop** for this week; all automations will fail as a result, so students must check recording links directly from the **LMS** rather than relying on automatic sync. 
- **MySQL chosen as the primary practical database** for the batch; Postgres was also demonstrated live. 
- **Databases must always be kept private**, never public-facing, due to SQL injection risks, bot attacks, and compliance requirements (HIPAA, PCI DSS, SOX). 
- **Auto-generated passwords** recommended during RDS setup; password is visible only once and must be copied immediately. 
- **Delete protection should be enabled in production** but was left unchecked during the demo for ease of deletion. 

---

## Topics Covered

### Why Databases Are Required

- Store data in an **organized, structured, and persistent** way. 
- Enable **fast searching, sorting, and retrieval** beyond human memory capacity. 
- Support **multi-user access** and collaborative productivity. 
- Power **data-driven decisions**: over 90% of business decisions are based on data; companies use purchase history, behavior, and patterns to drive automation and personalization. 
- Prevent **major business loss** from data loss; databases are described as "company gold." 

### Types of Databases

Three major categories were identified: 

**Relational Databases (SQL)**

- Data stored in **rows and columns** (tables); relationships exist between tables. 
- Terminology: tables = *relations*, columns = *attributes*, rows = *tuples* — use these terms in interviews. 
- Examples: **MySQL, Oracle, PostgreSQL, MariaDB, SQL Server**. 
- Best for: predefined schemas, transactional systems (accounting, e-commerce, inventory). 

**Non-Relational Databases (NoSQL)**

- No fixed schema; data stored as **documents (JSON), key-value pairs, graphs, or column families**. 
- Examples: **MongoDB, Cassandra, Redis**. 
- Best for: unstructured or evolving data, applications where schema is unknown upfront. 

**Time-Series Databases**

- Data changes **every second** (e.g., stock market, monitoring metrics, IoT sensor readings). 
- Most popular example: **Prometheus** — to be covered in upcoming practicals. 
- Standard relational databases are not scalable enough for this use case. 

---

### Database Deep Dives

**MySQL**

- Open-source, free, one of the most widely used databases globally. 
- Supports all major platforms (Linux, Windows, Mac) and all major programming languages (Node.js, PHP, Python, Java, Ruby, etc.). 
- Comes with backup/restore utilities: `mysqldump` and `mysqlrestore`. 
- **Default port: 3306** — critical interview fact. 
- Supports multiple storage engines (e.g., InnoDB). 
- Can be installed locally for testing; also available as RDS managed service. 
- A **Bastion/Jump Host EC2 instance** with MySQL client installed is used to connect to a private RDS instance. 

**Oracle**

- **Not free; enterprise-licensed** — standard and enterprise editions available. 
- Leader in **banking and enterprise** domains due to critical support SLAs. 
- Provides advanced features: **encryption, wallet, Data Guard, RRAC, replication**. 
- Oracle team available for direct support when issues arise. 
- Key difference from MySQL: Oracle supports multiple databases under one instance; MySQL uses a single instance with multiple schemas. 

**MongoDB**

- Open-source, released from **2018** as community edition. 
- Stores data in **JSON format** (document-based). 
- Best choice when: schema is undefined, schema is constantly evolving, or data is unstructured. 
- Use cases: gaming session storage, e-commerce (products with different attributes), leaderboards. 
- Provides **auto-scaling and sharding** (replication-like feature). 
- Queries are simple and highly efficient. 

**Cassandra**

- Developed by **Apache**; designed for **high-volume read/write operations**. 
- Best for **IoT devices** — different vendors produce different schemas, and Cassandra handles heterogeneous data from multiple sources in a single database. 
- Real-world use case shared: used at Wipro for **Saudi project sensor data** — temperature and other sensors from different countries/vendors, all maintained centrally. 
- Supports **horizontal scaling** by adding nodes; peer-to-peer architecture. 
- Free to use; handles unstructured/documented data. 

---

### Why Databases Cannot Be Public

- Public databases expose systems to **SQL injection, bots, and unauthorized data harvesting**. 
- Compliance frameworks (**HIPAA, PCI DSS, SOX**) prohibit direct internet-facing databases. 
- All traffic must be **encrypted**; activity must be logged and auditable. 

**Best Practices Discussed:** 

- Always place database in a **private subnet** within a VPC
- Connect via a **Jump Host / Bastion Server / Proxy Server** (not directly from the internet) 
- **Restrict security groups** — apply least privilege
- Enable **enhanced monitoring** (basic + advanced)
- Store passwords securely in **vaults**
- Enable **encryption** (encryption algorithms)
- Maintain **automated backups**
- Deploy across **multiple Availability Zones**

---

### Architecture Overview

The session presented a target architecture: 

- **Internet → NAT Gateway → AWS VPC**
- VPC contains: public subnets (with Internet Gateway) + private subnets
- **EC2 instance** (Jump Host) sits in the public subnet
- **RDS** sits in the private subnet — accessible only via EC2
- Security groups act as firewalls; route tables manage internal routing

**Jump Host aliases used in industry:** Jump Server, Jump Box, Bastion Host, Proxy Server, DMZ (zone distinction noted — RDP is a separate protocol). 

---

### Traditional vs. Managed Database (RDS)

**Self-managed (on-premises or EC2)** requires manual handling of: 

- Power, hardware, OS installation and patching
- Database software installation and patching
- High availability, scaling, backup, DBA team management

**AWS RDS (PaaS)** automates all of the above with one click: 

- Provisioning, resizing, multi-AZ deployment
- Read replicas, automated minor version updates
- Performance monitoring, enterprise-level security
- Backup and recovery, patching
- Result: **DBA roles are being reduced** as cloud engineers handle everything via GUI

---

### AWS RDS — Practical Walkthrough

**Supported Engines in RDS (7 options):** 

- Aurora (MySQL-compatible), Aurora (PostgreSQL-compatible), MySQL, PostgreSQL, MariaDB, Oracle, Microsoft SQL Server, IBM DB2

**Aurora vs. Standard RDS — Key Differences:** 

- Aurora automatically increases storage; standard RDS requires manual storage increases
- Aurora is **log-structured**: every write is stored in **3 Availability Zones = 6 copies**
- Aurora is **5x faster** than standard MySQL; supports up to **128 TB** database size 
- Data replication syncs in **10 milliseconds** vs. typical ~3 seconds in standard setups 
- Aurora = enterprise-level MySQL/Postgres; standard RDS = single-instance managed service

**PostgreSQL — Key Facts:** 

- One of the most advanced open-source relational databases; ~35 years old
- Object-oriented programming concepts integrated into PL/SQL
- Stores data in page format for fast retrieval
- Current version in RDS: **18.3**

**Live Demo — Creating a Postgres RDS Instance (steps):** 

1. Navigate to RDS → Create Database → **Full Configuration**
2. Select engine: **PostgreSQL**
3. Template: **Free Tier**
4. Database identifier: `database-1`
5. Enable **auto-generate password**
6. Authentication: **Password + IAM database authentication**
7. Instance size: default (learning purpose)
8. **Public access: No** (always private)
9. Click **Create Database** — ready in 2–4 minutes
10. View credentials once (copy immediately — shown only one time) 

**Live Demo — Creating a MySQL RDS Instance (additional config explored):** 

- Engine: MySQL, Version: **8.x**
- **Storage auto-scaling**: enable with one click; scales from 20 GB to 1,000 GB automatically 
- Auto-scaling type: **vertical scaling** (same instance, increased size) 
- **Port: 3306** (default; not changed in real-time environments) 
- **Logging options**: Audit log, Error log, General log, IAM log, Slow query log — all enableable with one click 
- **Backup retention**: recommended **30–60 days**, max 90 days; logs can be exported to CloudWatch 
- **Backup window**: schedule during **non-business hours** (e.g., Saturday/Sunday, 5 AM UTC) 
- **Maintenance window**: separate from backup window — do not overlap; max 2-hour window 
- **Delete protection**: enable in production to prevent accidental deletion 
- **Endpoint**: acts as hostname/IP address for application connection strings 

**Deleting an RDS Instance:** 

- Select database → Actions → Delete
- Choose whether to take a final snapshot (not needed for test instances)
- Confirm acknowledgment → deletion is **permanent and unrecoverable**

---

### Backup & Restore from S3

- RDS snapshots are stored in S3 by default (AWS-managed backend). 
- To restore an on-premises Oracle/MySQL database from S3: use **dump files** (`mysqldump` / Oracle equivalent), download from S3, then run restore command on target. 
- S3 storage class for backups: **Standard** by default; Glacier for cost-saving archival. 
- Option in RDS console: **"Restore from S3"** — available as a direct button. 
- Postgres backups in production confirmed by attendee to go directly to S3. 

---

## Pending Confirmation

- Students who created RDS or other AWS resources (Terraform-provisioned infra, Aurora instances) must **run** **`terraform destroy`** and manually delete databases to avoid ongoing billing charges. 
- Students encountering **403 errors** (invalid security token) on S3 static website or GitBash operations should raise a **support ticket** — likely an account verification issue taking up to 24 hours. 
- **Route 53 availability** on free/POC accounts — one student reported an error; Vikas suggested checking the AWS console directly and raising a ticket if blocked. 
- **ECS overview** requested by a student — deferred to the Kubernetes session; ECS to be covered alongside Kubernetes. 
- **ELB (classic load balancer)** topic noted as pending from a previous session. 

---

## Open Questions

- **Can Cassandra be used for live traffic?** — Confirmed yes; it is designed for real-time, high-volume read/write. 
- **IAM user billing** — IAM users themselves do not incur charges; charges apply only when compute resources (CPU, memory, storage) are consumed. 
- **NTLM vs. Kerberos authentication** — raised by a student in context of banking applications; Kerberos uses cryptographic keys for encryption and handshake; NTLM is Windows-specific. Both are authentication protocols usable beyond Windows. 
- **Database pipeline in JD** — clarified as CI/CD pipeline deploying an application that interacts with a database, not a database-specific pipeline concept. 
- **Sandbox vs. Free Tier** — confirmed as the same; POC accounts labeled "Sandbox" allow create/destroy experimentation. 

---

## Action Items

- **All students:** Check LMS directly for recording links this week — automation sync is broken due to local recording method. 
- **All students:** Delete any RDS instances, Aurora databases, or Terraform-provisioned infrastructure created during or after the session to stop billing. 
- **All students:** Practice speaking 5 key points about each database (MySQL, Oracle, MongoDB, Cassandra, RDS) for interview readiness. 
- **Students with 403/token errors:** Raise a support ticket via the LMS support system. 
- **Vikas:** Add RDS to student resumes after 2 practicals are completed. 
- **Vikas:** Schedule **reverse critique / extra sessions** where students present and explain topics instead of a standard class — Linux session confirmed as one candidate. 
- **Vikas:** Cover Kubernetes and ECS together in an upcoming session. 


---

# Part 1: 20 AWS RDS Interview Questions and Answers

### 1. What is Amazon RDS?
**Answer:** Amazon Relational Database Service (RDS) is a managed AWS service for setting up, operating, and scaling relational databases. AWS manages many routine tasks such as infrastructure provisioning, backups, patching, and failure detection, while the user manages database configuration, data, access, and application queries.

### 2. What does PaaS mean, and how does RDS fit into it?
**Answer:** PaaS stands for Platform as a Service. The cloud provider manages much of the underlying platform, so the customer can focus on using the service. With RDS, AWS manages the underlying infrastructure and many database maintenance tasks instead of requiring the team to install and maintain the database on a virtual machine.

### 3. What is the difference between a relational and a non-relational database?
**Answer:** A relational database organizes data into tables with rows, columns, and a defined schema, and commonly uses SQL. A non-relational database may use document, key-value, graph, or other data models and can be more flexible for changing data structures.

### 4. Name some database engines that can be used with Amazon RDS.
**Answer:** Amazon RDS supports engines including MySQL, PostgreSQL, MariaDB, Oracle, and Microsoft SQL Server. Amazon Aurora is an AWS-designed relational database compatible with MySQL and PostgreSQL. Always confirm current engine and version availability in the AWS Region you plan to use.

### 5. What is the default port for MySQL?
**Answer:** The default MySQL port is **3306**. For RDS connectivity, the database security group must allow the required inbound traffic from the application's security group or another approved source.

### 6. What is an RDS endpoint?
**Answer:** An RDS endpoint is a DNS hostname used by an application or database client to connect to the database. Applications should use the endpoint rather than hard-coding an IP address, because the underlying IP can change during maintenance or failover.

### 7. Should an application database be publicly accessible?
**Answer:** Normally, no. For a production application, set **Public access to No** and place the database in private subnets. Allow access only from trusted application resources or an approved administrative path, such as a bastion host or VPN. Public accessibility alone does not automatically grant access, but it increases exposure and should be avoided unless there is a specific, controlled need.

### 8. What is a DB subnet group?
**Answer:** A DB subnet group is a collection of subnets in a VPC that RDS can use for a database deployment. For production deployments, it should generally include private subnets in multiple Availability Zones to support resilient architectures.

### 9. What is a bastion host or jump host?
**Answer:** A bastion host is a controlled server used as an entry point for administrators to reach private resources. For example, an engineer may connect to a secured bastion host and then connect to a private RDS database. Access to the bastion should be restricted and monitored; alternatives such as VPN or AWS Systems Manager may also be suitable.

### 10. What is the difference between vertical and horizontal scaling?
**Answer:** Vertical scaling means changing the database instance size to provide more CPU, memory, or other capacity. Horizontal scaling means adding capacity across multiple database instances or replicas, depending on the database engine and architecture. Read replicas can help with read-heavy workloads, but they do not automatically increase write capacity.

### 11. What is Multi-AZ in Amazon RDS?
**Answer:** Multi-AZ is an availability feature that maintains a standby in another Availability Zone for supported RDS deployments. It is primarily designed for high availability and failover. It should not be confused with a read replica, which is commonly used to scale read traffic.

### 12. What is a read replica?
**Answer:** A read replica is a copy of a source database that can serve read queries. It can reduce read load on the primary database. Replication is generally asynchronous, so a replica may lag behind the source. A read replica is not a substitute for backups.

### 13. What is Amazon Aurora?
**Answer:** Amazon Aurora is an AWS relational database engine compatible with MySQL and PostgreSQL. It separates compute from a distributed storage layer and is designed for performance and availability. Exact performance, storage limits, and feature support depend on the Aurora version and configuration, so verify current AWS documentation rather than relying on a fixed performance multiplier.

### 14. What is the purpose of automated backups in RDS?
**Answer:** Automated backups support point-in-time recovery within the configured backup retention period. RDS takes backups and retains transaction logs to allow recovery to a supported time. Teams should confirm retention settings and test recovery procedures rather than assuming that backups alone guarantee recoverability.

### 15. What is the difference between an automated backup and a manual snapshot?
**Answer:** Automated backups are managed by RDS according to the retention settings and support point-in-time recovery. A manual DB snapshot is initiated by the user and remains until the user deletes it. Snapshots are useful before major changes, migrations, or planned maintenance.

### 16. What is IAM database authentication?
**Answer:** IAM database authentication allows supported database engines and configurations to authenticate using AWS IAM-generated authentication tokens instead of relying only on a long-lived database password. It requires the relevant RDS configuration, IAM permissions, and compatible client support. It is not supported by every RDS engine or configuration.

### 17. What is the difference between a database password and IAM database authentication?
**Answer:** Traditional password authentication uses a database username and password managed by the database. IAM database authentication uses a short-lived token generated with AWS credentials and IAM permissions. The right choice depends on engine support, application requirements, operational complexity, and security policy.

### 18. What are maintenance windows in RDS?
**Answer:** A maintenance window is a selected time period during which RDS can apply certain planned maintenance activities. Teams often choose a low-traffic period to reduce impact. Some maintenance actions may cause downtime or a restart, so review the pending maintenance details and plan accordingly.

### 19. How can you troubleshoot an application that cannot connect to RDS?
**Answer:** Check the RDS status and endpoint, VPC and subnet routing, security group rules, network ACLs, DNS resolution, port number, database credentials, TLS settings if required, and whether the client is on an allowed network path. For MySQL, confirm that the client is reaching port 3306.

### 20. Why should you run `terraform destroy` after a temporary RDS lab?
**Answer:** `terraform destroy` removes resources managed by the relevant Terraform configuration, helping prevent continued charges for temporary resources. Before running it, verify the workspace, account, region, and plan. It can delete data and infrastructure, so never run it against production or shared resources without explicit approval and a reviewed plan.

---

# Part 2: 20 AWS RDS Scenario-Based Questions and Answers

### 1. Scenario: Your application running on EC2 cannot connect to a private RDS MySQL database. What do you check?
**Answer:**
1. Confirm the RDS instance is available and copy the correct endpoint.
2. Confirm the EC2 instance and RDS are in reachable VPC/network paths.
3. Check the RDS security group inbound rule: allow TCP 3306 from the EC2 application's security group.
4. Check route tables and network ACLs if applicable.
5. Verify credentials and test DNS resolution.

Avoid opening port 3306 to `0.0.0.0/0`.

### 2. Scenario: A developer asks you to make the production database publicly accessible for easy access from a laptop. What do you recommend?
**Answer:** Keep the database private. Use a VPN, a secured bastion host, AWS Systems Manager where appropriate, or another approved access path. Restrict access to known sources and use least-privilege permissions. Do not expose the database to the internet simply for convenience.

### 3. Scenario: You need to create a MySQL RDS instance for a lab. Which port should you use?
**Answer:** Use TCP **3306**, the default MySQL port, unless the database has been deliberately configured otherwise. Ensure the security group allows access only from the required lab client or application security group.

### 4. Scenario: The application is using an IP address to connect to RDS, and connectivity breaks after a database failover. How would you improve the setup?
**Answer:** Configure the application to use the RDS DNS endpoint rather than a hard-coded IP address. Review connection retry and pooling behaviour as well, because failover can interrupt existing connections while DNS and clients reconnect.

### 5. Scenario: Your e-commerce database is experiencing high CPU because of increased traffic. What steps would you take?
**Answer:** Review CloudWatch metrics, slow queries, query plans, indexes, connection counts, and application behaviour first. If the instance is undersized, consider vertical scaling. If read traffic is the main issue, evaluate read replicas and application changes. Do not scale blindly without identifying the bottleneck.

### 6. Scenario: Your database must remain available if an Availability Zone has a problem. What RDS feature should you consider?
**Answer:** Consider a supported Multi-AZ deployment. It is designed to improve availability through standby infrastructure and failover. Confirm the engine's deployment options and test the application's ability to reconnect after failover.

### 7. Scenario: Reports are putting heavy read load on the production database. What can you do?
**Answer:** First optimize the report queries and schedule heavy jobs sensibly. If appropriate, use a read replica for read-only reporting traffic. Monitor replication lag and ensure reports can tolerate slightly stale data if replication is asynchronous.

### 8. Scenario: A team accidentally deletes important rows from a database. How can you recover the data?
**Answer:** Identify the required recovery time and point. Use point-in-time recovery from automated backups when available, or restore a suitable snapshot. Restore to a separate database where possible, validate the data, and then plan a controlled data recovery or application cutover. Avoid experimenting directly on the only production copy.

### 9. Scenario: Before applying a schema migration, the team wants a recovery option. What should you do?
**Answer:** Confirm that recent automated backups are healthy and take a manual snapshot if appropriate. Test the migration in a non-production environment, define a rollback plan, and schedule the change during an approved window. A snapshot is a recovery aid, not a replacement for a tested migration plan.

### 10. Scenario: A Terraform lab created an RDS database, and the student has finished the exercise. How do you avoid unnecessary charges?
**Answer:** Review the Terraform workspace and run `terraform plan -destroy` or otherwise review the planned destruction before running `terraform destroy`. Confirm that the resources belong to the lab and that no required data will be lost. Also check for related resources such as snapshots, storage, or other billable components.

### 11. Scenario: The AWS Console returns an account verification or 403 error while creating an RDS resource. What should you do?
**Answer:** Read the full error message and check whether the account has restrictions or requires verification. Follow the guidance in the error and raise an AWS Support case if needed. Do not try to bypass account controls. Verify that the selected Region and requested configuration are supported.

### 12. Scenario: A database connection works from a bastion host but not from the application EC2 instance. What does this suggest?
**Answer:** The database may be reachable from the bastion, but the application path may have different security group rules, routing, DNS, or credentials. Check whether the RDS security group allows inbound traffic from the application's security group, not just the bastion's. Test connectivity from the application host.

### 13. Scenario: Your team wants to stop managing database OS patching and routine infrastructure tasks. What service would you consider?
**Answer:** Consider Amazon RDS, which manages the underlying infrastructure and many routine database operations. The team still remains responsible for database design, query performance, access control, application compatibility, backup policy choices, and recovery testing.

### 14. Scenario: The database needs more CPU and memory, but the data volume is not the main issue. What scaling approach may help?
**Answer:** Vertical scaling by modifying the DB instance class may help. Check CPU, memory-related indicators, connections, and query performance before resizing. Plan for any expected interruption and test the change in a lower environment when practical.

### 15. Scenario: An application team wants to store frequently changing JSON documents with a flexible schema. Is a relational database always the best fit?
**Answer:** Not necessarily. A document-oriented database such as MongoDB may fit a flexible document model, depending on access patterns and consistency requirements. Choose the database based on query patterns, transactions, scale, data relationships, and operational needs rather than choosing solely by popularity.

### 16. Scenario: A monitoring platform stores measurements that change every few seconds. Which data model might be suitable?
**Answer:** A time-series database or a purpose-built monitoring system may be suitable. Prometheus, for example, is commonly used for metrics and time-series monitoring data. Consider retention, query patterns, cardinality, and whether the data is operational metrics or transactional application data.

### 17. Scenario: Your application requires passwordless-style database access using AWS identity permissions. What RDS option might you evaluate?
**Answer:** Evaluate IAM database authentication if the chosen RDS engine and configuration support it. Configure the required IAM permissions and database settings, and ensure the client can generate and use authentication tokens. Do not assume all database engines support it.

### 18. Scenario: RDS maintenance is planned during your busiest business hours. What should you do?
**Answer:** Review the maintenance details and operational impact, then choose or request an appropriate maintenance window where supported. Communicate with stakeholders, confirm backups, check application reconnection behaviour, and monitor the database during and after maintenance. Some urgent maintenance may have constraints that limit scheduling choices.

### 19. Scenario: A junior engineer opens the RDS security group to all IP addresses to fix a connection error. What is the immediate response?
**Answer:** Remove the broad inbound rule and replace it with the minimum required access, ideally from the application's security group or a known administrative network. Check logs and exposure duration, follow the organization's incident process if the database may have been exposed, and rotate credentials if there is evidence they may be compromised.

### 20. Scenario: You are asked to choose between RDS Multi-AZ and a read replica. How do you explain the difference?
**Answer:** Multi-AZ is primarily for availability and failover. A read replica is primarily for serving read traffic and reducing load on the source database. They solve different problems, and a production architecture may use both if the engine and workload require them.

---

# Quick Revision Checklist

- RDS is a managed relational database service; AWS handles many infrastructure and maintenance tasks.
- MySQL default port: **3306**.
- Use the RDS **endpoint hostname**, not a hard-coded IP address.
- Keep production databases private wherever possible.
- Permit only required network sources through security groups.
- Use Multi-AZ for high availability and read replicas for read scaling.
- Understand automated backups, point-in-time recovery, and manual snapshots.
- Test restores and failover behaviour; do not rely on configuration alone.
- Schedule planned maintenance during a suitable low-traffic window where possible.
- Review Terraform plans carefully before destroying resources.
- Check current AWS documentation for engine-specific features, limits, and pricing.

## Suggested Practice Lab

1. Create a MySQL or PostgreSQL RDS instance in a lab VPC.
2. Set **Public access: No** and use private subnets.
3. Configure the database security group to allow only the required client security group.
4. Connect using the RDS endpoint from an approved host.
5. Review backup retention, maintenance settings, and CloudWatch metrics.
6. Write down the steps to restore a snapshot in a test environment.
7. If the lab was created with Terraform, review the destroy plan and clean up safely.

**Important:** AWS features, engine support, quotas, and pricing can change. Verify current AWS documentation and your account's configuration before applying these steps in production.

