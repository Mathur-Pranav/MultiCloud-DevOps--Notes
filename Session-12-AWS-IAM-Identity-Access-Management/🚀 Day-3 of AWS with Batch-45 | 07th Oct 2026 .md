# AWS Day 3 - Session Summary

A comprehensive, student-friendly reference guide based on the Day 3 AWS Cloud & DevOps session by Vikas from CloudDevOpsHub.

---

## 1. Evolution and History of AWS

Understanding the historical context of Amazon Web Services (AWS) explains why modern cloud architecture looks the way it does today.

### 1.1 Key Milestones
- **2002 – Platform & Management Console Launched**: Amazon released the very first iteration of the AWS platform, opening initial developer interfaces.
- **2004 – Amazon SQS (Simple Queue Service)**: The first official public AWS service. SQS solved asynchronous message buffering and decoupled distributed architectures.
- **2006 – Amazon EC2 (Elastic Compute Cloud)**: The virtualization revolution. Users could rent virtual machines on demand over the internet with pay-as-you-go billing instead of buying physical servers.
- **2009 – Amazon VPC (Virtual Private Cloud)**: Addressed enterprise security concerns. Allowed customers to build isolated private virtual networks in the cloud.
- **2012 – First AWS re:Invent**: Enterprise adoption accelerated rapidly.
- **2016 – $10 Billion Revenue Milestone**: AWS established dominant global market leadership in cloud infrastructure.
- **2017+ – 200+ Fully Featured Services**: Expanded into serverless computing, managed databases, container orchestration, machine learning, and edge networking.

### 1.2 The "Do We Need to Learn All 200+ Services?" Reality Check
- AWS introduces new services and updates continuously.
- **Industry Reality**: No DevOps engineer or cloud architect needs to memorize or use all 200+ services.
- **The Core 10–20 Rule**: Mastering 10 to 15 core services spanning compute, storage, networking, database, security, and messaging enables you to design, build, and support 95%+ of production enterprise projects.

---

## 2. Why AWS Built 200+ Services: On-Premise to Cloud Evolution

Every AWS service is a direct, modernized cloud evolution of a traditional physical datacenter component.

| On-Premises / Legacy Component | Physical Reality / Pain Points | AWS Cloud Replacement Service | Cloud Advantage |
| :--- | :--- | :--- | :--- |
| **Physical Storage (Floppy / CD / DVD / Tape / NAS / SAN)** | Physical hardware degradation, capacity limits, manual drive swaps. | **Amazon S3** (Object), **Amazon EBS** (Block), **Amazon EFS** (Shared File) | Virtually infinite capacity, 99.999999999% (11 9's) durability, instant resizing. |
| **Physical Server Rooms & Racks** | Real-estate cost, power, cooling, hardware procurement lead time (weeks to months). | **Amazon EC2** | Provision virtual servers across the globe in under 60 seconds. |
| **Office Network Cables, LAN, WAN & Switches** | Tangled physical wiring, hardware switch failures, localized bottlenecks. | **Amazon VPC**, Subnets, Route Tables, Internet Gateways | Software-defined networking, isolated virtual private networks. |
| **Physical Security Guards at Datacenter Gates** | Badges, physical logbooks, vulnerable to human error. | **AWS IAM (Identity and Access Management)** | Granular JSON policies, Least Privilege, MFA, temporary STS credentials. |
| **Manual Application Notifications / Batch Delivery** | Fragmented bulk mail servers, vendor SMS gateways getting blocked or throttled. | **Amazon SNS (Simple Notification Service)** & **Amazon SQS** | High-throughput, managed pub/sub messaging and decoupled queues. |
| **Server Monitoring Screens & Audit Binders** | Manual log reviews, physical monitoring stations. | **Amazon CloudWatch** & **AWS CloudTrail** | Centralized metric dashboards, automated alarms, complete API audit trails. |

---

## 3. AWS Global Infrastructure

AWS global infrastructure powers high availability, fault tolerance, low latency, and regulatory compliance.

### 3.1 Regions
- A **Region** is a physical geographical location around the world (e.g., `ap-south-1` in Mumbai, `ap-south-2` in Hyderabad, `us-east-1` in N. Virginia).
- Each region is completely autonomous and isolated from other regions to achieve maximum fault tolerance and stability.
- **Key Decision Criteria for Choosing a Region**:
  1. **User Latency**: Proximity to your end users.
  2. **Legal & Compliance Requirements**: Local data residency laws (e.g., RBI guidelines in India, GDPR in Europe).
  3. **Service Availability**: Not all AWS services are available in every region on day one.
  4. **Cost**: Regional tax and operational differences result in varying service prices.

### 3.2 Availability Zones (AZs)
- An **Availability Zone (AZ)** consists of one or more discrete datacenters, each with redundant power, networking, and connectivity.
- Represented by a Region code followed by a letter (e.g., `ap-south-1a`, `ap-south-1b`).
- Every AWS Region has a minimum of **3 Availability Zones** separated by meaningful physical distance (miles apart) to protect against local flood, fire, or grid failures, yet connected via ultra-low-latency private fiber.

### 3.3 Edge Locations & Points of Presence (PoP)
- **Edge Locations** are specialized network endpoints situated in high-density metropolitan areas worldwide.
- **The "Blinkit / DMART Ready" Analogy**: A central warehouse (Region) holds all inventory. However, localized mini-hubs (Edge Locations) cache high-demand items 1–2 km from users to deliver orders within minutes.
- **Primary Uses**:
  - **Amazon CloudFront**: Caches static and dynamic content closer to end users for ultra-fast load times.
  - **AWS Shield & WAF**: Mitigates DDoS attacks at the perimeter before traffic hits your core servers.
  - **SSL/TLS Termination**: Handles protocol handshakes at the edge to reduce backend compute overhead.

---

## 4. Core AWS Services Overview (Top 12 Services)

| Service Name | Category | Primary Function | One-Line Real-World Description |
| :--- | :--- | :--- | :--- |
| **Amazon EC2** | Compute | Virtual Servers | Rented virtual machine instances running Linux or Windows in the cloud. |
| **Amazon S3** | Storage | Object Storage | Virtually infinite storage bucket for static files, backups, logs, and static websites. |
| **Amazon EBS** | Storage | Block Storage | High-performance virtual hard disks attached directly to EC2 (OS boot drives). |
| **Amazon RDS** | Database | Managed Relational DB | Automated MySQL, PostgreSQL, Oracle, and SQL Server with automated backups and Multi-AZ. |
| **Amazon DynamoDB** | Database | NoSQL Key-Value DB | Ultra-fast, single-digit millisecond NoSQL database for unstructured data. |
| **Amazon VPC** | Networking | Isolated Virtual Network | Your own private, secure datacenter boundary inside AWS cloud. |
| **AWS IAM** | Security | Identity & Access | Controls authentication and authorization (who can do what) inside AWS. |
| **AWS Lambda** | Serverless | Function as a Service | Run backend code in response to events without provisioning or managing any servers. |
| **Amazon API Gateway** | Networking / API | Front Door for APIs | Creates, publishes, maintains, monitors, and secures REST and WebSocket APIs at scale. |
| **Amazon CloudFront** | Networking | Global CDN | Distributes cached content to global Edge Locations for low-latency delivery. |
| **Amazon SQS** | Integration | Message Queuing | Fully managed message queue for decoupling distributed application services. |
| **Amazon SNS** | Integration | Pub/Sub Notifications | Publishes messages to subscriber endpoints (Email, SMS, Lambda, SQS, Slack, Webhooks). |
| **Amazon CloudWatch** | Monitoring | Observability & Alarms | Collects server metrics, application logs, and triggers automated alerting. |
| **AWS CloudTrail** | Governance | Audit & Compliance | Records and tracks all API calls and console activities (who did what, when, and from where). |
| **AWS CloudFormation** | DevOps / IaC | Infrastructure as Code | Declaratively provisions AWS resources using JSON/YAML templates. |
| **Amazon EKS** | Containers | Managed Kubernetes | Runs and scales standard Kubernetes container clusters on AWS infrastructure. |

---

## 5. Storage Deep Dive: Block vs. File vs. Object Storage

| Dimension | Block Storage (Amazon EBS) | File Storage (Amazon EFS) | Object Storage (Amazon S3) |
| :--- | :--- | :--- | :--- |
| **Data Format** | Raw volume blocks (unformatted disk). | Hierarchical file system (directories, files). | Flat address space (Key + Metadata + Value). |
| **Performance** | Extremely high IOPS, ultra-low latency. | Moderate to high, distributed throughput. | High throughput, higher initial request latency. |
| **Access Model** | Single EC2 instance attachment (per volume). | Concurrent read/write across thousands of EC2 instances. | Accessible via REST API / HTTP URLs from anywhere. |
| **Primary Use Cases** | OS Boot disks (Root volumes), transactional databases. | Shared application code, content repositories, user home dirs. | Backups, archives, images, video assets, static websites, logs. |
| **Cost** | Highest per GB cost. | Moderate per GB cost. | Lowest per GB cost with tiered lifecycle classes. |

---

## 6. Disaster Recovery (DR) Concepts: RTO and RPO

Disaster Recovery is a core topic in production engineering and advanced technical interviews.

### 6.1 Definitions
- **Disaster Recovery (DR)**: The set of policies, tools, and procedures enabling the recovery or continuation of vital technology infrastructure and systems following a natural or human-induced disaster (e.g., datacenter flood, power grid failure, regional outage, cyber incident).
- **Primary Site vs. Secondary (DR) Site**:
  - **Primary Site (DC)**: The live production environment handling daily client transactions.
  - **DR Site**: A secondary replica environment deployed in an alternate geographical region or availability zone, synchronized with the primary.

### 6.2 RTO vs. RPO Comparison

| Metric | Full Form | Meaning | Real-World Example |
| :--- | :--- | :--- | :--- |
| **RTO** | **Recovery Time Objective** | The maximum acceptable **duration of time** that an application can remain offline before normal business operations must be restored. | If a database crashes at 10:00 AM and RTO is 60 minutes, the DR site must be fully operational by 11:00 AM. |
| **RPO** | **Recovery Point Objective** | The maximum acceptable **amount of data loss** measured backward in time from the moment of disruption. | If database backups run every 3 hours and a crash occurs at 2:59 PM, up to 2 hours and 59 minutes of transactional data could be lost. |

### 6.3 DR Drills vs. Real-Time Disasters
- **DR Drill (Simulation)**: A planned exercise conducted quarterly or semi-annually where engineers simulate a datacenter disaster, switch DNS to the DR host, validate application health, and verify runbooks without waiting for an actual crisis.
- **Real-Time Disaster**: An unplanned catastrophic event (e.g., drone attack on an overseas datacenter, physical flooding, regional fiber cuts) requiring live activation of the failover mechanism.
- **Failover Mechanism**:
  1. Primary site detected unhealthy by health checks or monitoring.
  2. Database replication promoted: Standby database in DR region promoted to primary read/write master.
  3. Compute workloads scaled up in DR region.
  4. DNS routing switched: Route 53 or external DNS updated to point domain traffic to DR load balancers.

---

## 7. Hands-On Lab Walkthroughs

### 7.1 Lab 1: Provisioning Amazon Linux EC2 Instances & SSH Connectivity

#### Step 1: Launching the EC2 Instance
1. In the AWS Management Console, navigate to **EC2** > **Instances** > **Launch instances**.
2. **Name**: `Server-1` (and `Server-2` for the second node).
3. **AMI**: Select **Amazon Linux 2023 AMI** (Default username: `ec2-user`).
4. **Instance Type**: `t2.micro` or `t3.micro` (Free Tier eligible).
5. **Key Pair**:
   - Create a new Key Pair named `devops-key`.
   - **Key pair type**: `RSA`.
   - **Private key file format**:
     - `.ppk` for MobaXterm and PuTTY on Windows.
     - `.pem` for OpenSSH on Linux, macOS, and native Windows PowerShell/CMD.
6. **Network Settings**:
   - VPC: Default VPC.
   - Auto-assign Public IP: `Enable`.
   - Security Group: Create security group allowing:
     - SSH (Port 22) from `0.0.0.0/0` (or `My IP`).
     - HTTP (Port 80) from `0.0.0.0/0`.
7. **Storage**: Configure root volume to 8 GB or 30 GB GP3.
8. Click **Launch instance**.

#### Step 2: Connecting via MobaXterm (Windows)
1. Download and extract **MobaXterm Portable Edition**.
2. Click **Session** > **SSH**.
3. **Remote host**: Paste your EC2 instance's **Public IPv4 address**.
4. Check **Specify username**: Enter `ec2-user`.
5. Click **Advanced SSH settings**:
   - Check **Use private key**.
   - Browse and select your downloaded `devops-key.ppk` file.
6. Click **OK**.
7. Accept the host key fingerprint prompt to enter the terminal session.

#### Step 3: Connecting via Terminal / Command Prompt (macOS / Linux / Windows CMD)
If using a `.pem` file with OpenSSH:
```bash
# 1. Navigate to the folder containing your key
cd /path/to/key-folder

# 2. Restrict file permissions (Linux/macOS requirement)
chmod 400 devops-key.pem

# 3. Connect to the EC2 instance
ssh -i "devops-key.pem" ec2-user@<YOUR_EC2_PUBLIC_IP>
```

---

### 7.2 Lab 2: Installing and Configuring NGINX Web Server

Execute the following commands on both instances to configure distinct web responses:

#### On Server 1:
```bash
# Switch to root privileges
sudo -i

# Update system repositories
yum update -y

# Install NGINX web server
yum install nginx -y

# Start the NGINX service
systemctl start nginx

# Enable NGINX to start on system boot
systemctl enable nginx

# Verify service status
systemctl status nginx

# Navigate to the default web root directory
cd /usr/share/nginx/html

# Replace index.html with a custom identifier
echo "<h1>Welcome to Server 1 - Batch 45 DevOps Student</h1>" > index.html
```

#### On Server 2:
```bash
sudo -i
yum update -y
yum install nginx -y
systemctl start nginx
systemctl enable nginx

cd /usr/share/nginx/html
echo "<h1>Welcome to Server 2 - Production NGINX Backup</h1>" > index.html
```

#### Verification:
Open your browser and navigate to:
- `http://<SERVER_1_PUBLIC_IP>` -> Displays "Welcome to Server 1..."
- `http://<SERVER_2_PUBLIC_IP>` -> Displays "Welcome to Server 2..."

> [!IMPORTANT]
> If the page does not load, verify that the EC2 Security Group contains an inbound rule allowing **HTTP (Port 80)** from `0.0.0.0/0`.

---

### 7.3 Lab 3: Creating and Testing a Classic Load Balancer (ELB)

#### Step 1: Creating the Load Balancer
1. In the EC2 Console, go to **Load Balancers** under *Load Balancing*.
2. Click **Create Load Balancer**.
3. Select **Classic Load Balancer (Previous Generation)**.
4. **Basic Configuration**:
   - **Load Balancer name**: `batch45-web-elb`.
   - **Scheme**: `Internet-facing`.
   - **IP address type**: `IPv4`.
   - **VPC**: Default VPC.
5. **Listeners**:
   - Load Balancer Protocol: `HTTP` on Port `80`.
   - Instance Protocol: `HTTP` on Port `80`.
6. **Assign Security Groups**:
   - Select or create a security group allowing inbound traffic on Port 80 from `0.0.0.0/0`.
7. **Configure Health Check**:
   - **Ping Protocol**: `HTTP`.
   - **Ping Port**: `80`.
   - **Ping Path**: `/index.html` (or `/`).
   - **Response Timeout**: `5 seconds`.
   - **Interval**: `5 seconds` (frequency of heartbeats).
   - **Unhealthy threshold**: `2` (consecutive failed checks before marking unhealthy).
   - **Healthy threshold**: `2` (consecutive successful checks before marking healthy).
8. **Add EC2 Instances**:
   - Select both `Server-1` and `Server-2`.
9. Click **Review and Create** > **Create**.

#### Step 2: Validating Health Status
- Navigate to the newly created load balancer's **Instances** (or **Target**) tab.
- Initial Status: `OutOfService` (Instances are undergoing health checks).
- After 1–2 minutes: Status transitions to `InService`.

#### Step 3: Verifying Traffic Distribution
1. Copy the **DNS name** of the load balancer (e.g., `batch45-web-elb-123456789.ap-south-1.elb.amazonaws.com`).
2. Open a web browser and navigate to: `http://<LOAD_BALANCER_DNS_NAME>`
3. Hit Refresh multiple times:
   - Request 1 returns: *Welcome to Server 1 - Batch 45 DevOps Student*
   - Request 2 returns: *Welcome to Server 2 - Production NGINX Backup*
4. The load balancer distributes traffic evenly between both backend targets using the Round-Robin algorithm.

#### Step 4: Clean Up to Prevent Charges
To avoid unnecessary billing in free tier or sandbox accounts:
1. Select the Load Balancer > **Actions** > **Delete** > Confirm.
2. Navigate to **Instances** > Select `Server-1` and `Server-2` > **Instance state** > **Terminate instance** > Confirm.

---

## 8. Common Troubleshooting and Gotchas

1. **SSH "Permission Denied (publickey)"**:
   - Wrong username used (use `ec2-user` for Amazon Linux, `ubuntu` for Ubuntu, `centos` for CentOS).
   - Key pair does not match the key assigned during instance launch.
   - Permissions on `.pem` file are too permissive (run `chmod 400 <keyfile>`).
2. **Web Page Times Out on Port 80**:
   - Security Group missing inbound rule for HTTP (Port 80).
   - NGINX service not started (run `sudo systemctl start nginx`).
3. **ELB Status Remains "OutOfService"**:
   - Health check path points to a file that does not exist (e.g., `/index.html` missing in `/usr/share/nginx/html`).
   - The EC2 security group blocks incoming traffic from the Load Balancer's security group on port 80.
   - Target EC2 instance is stopped or unhealthy.
4. **Secure Key Management in Real-World Teams**:
   - Never share private keys over unencrypted communication channels.
   - Use password and secret managers (KeePass, 1Password, CyberArk, LastPass).
   - **Modern Best Practice**: Eliminate SSH keys entirely by managing EC2 instance access via **AWS Systems Manager (SSM) Session Manager**.

---

## 9. 10 Core Interview Questions with Answers

### Q1: What is AWS Global Infrastructure, and how do Regions, Availability Zones, and Edge Locations differ?
**Answer:**
- **Region**: A distinct physical geographical territory (e.g., `ap-south-1` Mumbai) hosting completely isolated datacenters. Chosen based on user latency, compliance/data residency, service availability, and cost.
- **Availability Zone (AZ)**: One or more physically separated datacenters within a Region, equipped with redundant power, cooling, and low-latency networking (e.g., `ap-south-1a`, `ap-south-1b`).
- **Edge Location**: A Point of Presence (PoP) connected to the AWS global network in major global cities, used by Amazon CloudFront (CDN) and AWS Shield/WAF to cache content and terminate traffic close to users.

### Q2: What are the primary differences between Amazon EBS, Amazon EFS, and Amazon S3?
**Answer:**
- **Amazon EBS**: Block-level storage attached to a single EC2 instance via dedicated network fabric. Ideal for OS root volumes, transaction logs, and databases requiring low-latency IOPS.
- **Amazon EFS**: Scalable, elastic Network File System (NFSv4) that can be mounted simultaneously by thousands of EC2 instances for shared application code and distributed workloads.
- **Amazon S3**: Serverless object storage service accessible over HTTP/REST APIs. Offers 11 9's durability, infinite scale, and lifecycle policies. Ideal for media files, data lakes, backups, and static website hosting.

### Q3: Explain RTO and RPO in the context of Disaster Recovery (DR).
**Answer:**
- **RTO (Recovery Time Objective)**: The targeted duration of time within which a business process or application must be restored after a disruption (how long you can afford to be down).
- **RPO (Recovery Point Objective)**: The maximum targeted period in which data might be lost due to an incident (how much data loss in time you can tolerate).
- *Example*: A 1-hour RTO means the application must recover within 60 minutes. A 15-minute RPO means data backup/synchronization must ensure no more than 15 minutes of new data is lost.

### Q4: How does Elastic Load Balancing (ELB) determine target health, and what is a "heartbeat"?
**Answer:**
- ELB performs periodic **health checks** (heartbeat requests) against registered target instances on a defined protocol, port, and health check path (e.g., `HTTP:80/index.html`).
- Key health check parameters include:
  - **Interval**: Frequency of requests (e.g., every 5 seconds).
  - **Timeout**: Wait time for a response before marking an individual attempt as failed.
  - **Unhealthy Threshold**: Number of consecutive failures before marking the target `OutOfService`.
  - **Healthy Threshold**: Number of consecutive successes before restoring traffic routing to the instance.

### Q5: What is the difference between `.pem` and `.ppk` key formats?
**Answer:**
- **`.pem` (Privacy Enhanced Mail)**: An OpenSSH-standard Base64-encoded certificate/private key container used natively by Linux, macOS terminals, Windows OpenSSH client, and automated CI/CD tools.
- **`.ppk` (PuTTY Private Key)**: A proprietary private key format developed specifically for the PuTTY suite (and supported directly by tools like MobaXterm).
- Tools like `PuTTYgen` can convert `.pem` keys into `.ppk` keys and vice-versa.

### Q6: Why does SSH fail with "Permissions 0644 are too open", and how do you fix it?
**Answer:**
- The OpenSSH client requires that private key files are readable **only by the owner** and inaccessible by others or group members.
- If permissions are too open, SSH rejects the key to protect against unauthorized access.
- **Fix**: Run `chmod 400 <private_key_file>` on Linux/macOS. On Windows, remove inherited permissions in the file properties security tab and grant read access exclusively to your user account.

### Q7: Why was Amazon VPC introduced in 2009 after EC2 launched in 2006?
**Answer:**
- In 2006, early EC2 instances ran in a flat, shared networking architecture called **EC2-Classic**, where instances were allocated public IP addresses and logical isolation was limited.
- Enterprises and financial organizations hesitated to migrate sensitive workloads due to security and compliance boundaries.
- In 2009, AWS launched **Amazon VPC (Virtual Private Cloud)**, giving customers private, software-defined networks with custom IP CIDR ranges, public/private subnets, network access control lists (NACLs), route tables, and direct VPN/Direct Connect integrations.

### Q8: What is the architectural difference between Amazon SQS and Amazon SNS?
**Answer:**
- **Amazon SQS (Pull / Polling model)**: A message queue service where consumers actively poll the queue, process messages, and delete them. Used for asynchronous decoupling and load smoothing.
- **Amazon SNS (Push / Fan-out model)**: A publish/subscribe messaging service where publishers send a message to a topic, and SNS instantly pushes copies to all subscribed endpoints (email, SMS, Lambda, SQS, webhooks).
- **Fan-Out Architecture**: Combining SNS with multiple SQS queues allows a single event notification to be pushed into separate queues for parallel processing by different backend services.

### Q9: What is an AMI, and what are the default usernames for standard Linux distributions on AWS?
**Answer:**
- An **AMI (Amazon Machine Image)** is a pre-configured template containing the OS, storage mapping, architecture, and installed packages needed to launch an EC2 instance.
- **Standard Default Usernames**:
  - Amazon Linux 2 / 2023: `ec2-user`
  - Ubuntu: `ubuntu`
  - CentOS: `centos`
  - Red Hat Enterprise Linux (RHEL): `ec2-user` or `root`
  - Debian: `admin`
  - Fedora: `fedora`

### Q10: What is the difference between AWS CloudWatch and AWS CloudTrail?
**Answer:**
- **Amazon CloudWatch**: An observability and monitoring service that collects system/application **metrics**, application **logs**, and configures automated alarms and auto-scaling triggers based on operational performance (e.g., CPU > 80%).
- **AWS CloudTrail**: A governance, compliance, and auditing service that records every **API call and administrative action** executed in the AWS account (who accessed what resource, when, from which IP, and via which credential).

---

## 10. 15 Scenario-Based Interview Questions with Answers

### Scenario 1: ELB Target Instances Stuck in "OutOfService"
- **Scenario**: You launch an Application/Classic Load Balancer and register two healthy EC2 web instances. When checking the load balancer console, both instances remain stuck in `OutOfService`. Browsing to the load balancer DNS returns HTTP 502 Bad Gateway or 504 Gateway Timeout. How do you troubleshoot and resolve this?
- **Answer**:
  1. **Direct IP Verification**: Access each EC2 instance directly via its Public IP on port 80 to verify if NGINX/Apache is running and serving pages.
  2. **Health Check Configuration**:
     - Check the configured Health Check ping path. If the health check is set to `/index.html` or `/healthz`, verify that the file actually exists and returns HTTP 200.
  3. **Security Group Rules**:
     - Verify that the EC2 instance's Security Group allows inbound traffic on port 80 originating from the **Load Balancer's Security Group**.
     - Verify that the Load Balancer's Security Group allows outbound traffic to the EC2 instances.
  4. **Target Response Timeout / Thresholds**:
     - Ensure the web server responds within the configured response timeout (default 5s). If the server is slow to respond, the health check will time out.

---

### Scenario 2: Zero-Downtime EBS Volume Expansion on a Production EC2 Instance
- **Scenario**: A critical production database running on an Amazon Linux EC2 instance has an 80 GB root EBS volume that is 98% full. Management requires increasing the disk capacity to 200 GB immediately with zero downtime and without rebooting the server. How do you accomplish this?
- **Answer**:
  1. **Elastic Volumes Modification (AWS Side)**:
     - Open EC2 Console > **Volumes** > Select the target EBS volume.
     - Click **Actions** > **Modify volume**.
     - Increase the size from 80 GB to 200 GB.
     - Click **Modify**. AWS applies the change dynamically while the instance is running.
  2. **File System Extension (OS Side)**:
     - SSH into the EC2 instance.
     - Run `lsblk` to verify the underlying block device has expanded to 200 GB while the partition remains 80 GB.
     - Grow the partition using `growpart`:
       ```bash
       sudo growpart /dev/xvda 1    # (or /dev/nvme0n1p1 on newer instance types)
       ```
     - Resize the filesystem based on format:
       - For **XFS**:
         ```bash
         sudo xfs_growfs -d /
         ```
       - For **EXT4**:
         ```bash
         sudo resize2fs /dev/xvda1
         ```
     - Run `df -h` to confirm the filesystem reflects the 200 GB capacity without rebooting.

---

### Scenario 3: Investigating HTTP 502 vs. 503 Errors on an Application Load Balancer
- **Scenario**: Users visiting your web platform experience intermittent HTTP 502 and 503 errors during peak hours. As the lead DevOps engineer, how do you distinguish the root causes between a 502 and a 503 error on an ALB?
- **Answer**:
  - **HTTP 502 (Bad Gateway)**:
    - *Meaning*: The load balancer connected to the backend EC2 target, but received an invalid response, connection reset, or unexpected closure.
    - *Common Causes*: Web server daemon crashed midway, backend keep-alive timeout is shorter than the ALB idle timeout, or target responded with malformed HTTP headers.
  - **HTTP 503 (Service Unavailable)**:
    - *Meaning*: The load balancer cannot route the request because there are no healthy targets available to process requests.
    - *Common Causes*: All registered targets in the target group failed their health checks, target group capacity is 0, or target queues are completely saturated.
  - *Troubleshooting Steps*:
    1. Inspect CloudWatch metrics: `HTTPCode_ELB_502_Count` vs `HTTPCode_ELB_503_Count`, `HealthyHostCount`, and `UnHealthyHostCount`.
    2. Enable and query **ALB Access Logs** in Amazon Athena to inspect `elb_status_code` vs `target_status_code`.

---

### Scenario 4: Designing a Multi-Region Disaster Recovery Strategy with Low RTO/RPO
- **Scenario**: A fintech startup requires a Disaster Recovery architecture between Mumbai (`ap-south-1`) and Hyderabad (`ap-south-2`). Their business SLA mandates an RTO of under 15 minutes and an RPO of under 1 minute. What architectural strategy satisfies this requirement?
- **Answer**:
  - **Disaster Recovery Strategy**: **Warm Standby** (or **Pilot Light** with cross-region replication).
  - **Database Layer (RPO < 1 min)**:
    - Deploy Amazon Aurora Global Database or Amazon RDS PostgreSQL with a **Cross-Region Read Replica** in Hyderabad. Aurora Global Database provides storage-level replication with typical latencies of under 1 second.
  - **Application/Compute Layer**:
    - Deploy core microservices in Mumbai with an active Auto Scaling Group.
    - In Hyderabad, maintain a minimal footprint (Warm Standby) with pre-provisioned infrastructure managed via Terraform or CloudFormation.
  - **DNS & Failover Orchestration (RTO < 15 min)**:
    - Use **Amazon Route 53** with Failover routing and Route 53 Health Checks.
    - When Mumbai is detected unhealthy, Route 53 automatically switches DNS traffic to the Hyderabad Application Load Balancer.
    - An automated AWS Lambda script promotes the Hyderabad read replica to standalone primary writer within 2–5 minutes.

---

### Scenario 5: NGINX Web Server Returns "Connection Timed Out" Despite Instance Being "Running"
- **Scenario**: An intern creates an EC2 instance, installs NGINX, and starts the service. In the AWS console, the instance shows `Running` with `2/2 checks passed`. However, browsing to `http://<Public_IP>` results in a `Connection Timed Out` error in the browser. What are the 4 main places to inspect?
- **Answer**:
  1. **Security Group Inbound Rules**: Confirm that the Security Group attached to the instance explicitly allows inbound traffic on **Port 80 (HTTP)** from `0.0.0.0/0`.
  2. **Subnet Route Table & Internet Gateway**:
     - Check the subnet's Route Table. It must have a route `0.0.0.0/0` targeting an attached **Internet Gateway (IGW)** (`igw-xxxx`). Without an IGW route, public traffic cannot reach the subnet.
  3. **Network ACLs (NACL)**:
     - Check the subnet's Network ACL. Ensure inbound rule allows port 80 and outbound rule allows ephemeral ports (`1024-65535`).
  4. **Internal OS Firewall**:
     - SSH into the instance and verify that local Linux firewalls (`iptables`, `firewalld`, or `ufw`) are not blocking port 80.

---

### Scenario 6: High Availability Across Multiple Availability Zones vs. Single AZ Failure
- **Scenario**: A company hosts a containerized web application on EC2 instances inside a single Availability Zone (`ap-south-1a`). During a thunderstorm, a transformer fails at the `ap-south-1a` datacenter, taking the entire zone offline for 3 hours. How should the architecture be re-engineered to survive future single-zone outages automatically?
- **Answer**:
  1. **Multi-AZ Subnet Distribution**:
     - Provision subnets across at least 3 distinct Availability Zones (`ap-south-1a`, `ap-south-1b`, `ap-south-1c`) within the custom VPC.
  2. **Multi-AZ Auto Scaling Group**:
     - Attach the Auto Scaling Group (ASG) to subnets across all 3 Availability Zones. Set minimum capacity to 3 instances (1 per zone).
  3. **Multi-AZ Load Balancer**:
     - Deploy an Application Load Balancer spanning all three Availability Zones. The ALB routes traffic only to healthy instances in operational zones.
  4. **Multi-AZ Database**:
     - Migrate any single-instance database to **Amazon RDS Multi-AZ**. In the event of an AZ outage, RDS triggers automatic, synchronous failover to the standby replica in another zone within 60–120 seconds.

---

### Scenario 7: Securing EC2 SSH Access Without Managing or Distributing Key Pairs
- **Scenario**: Your engineering team has grown to 50 developers. Managing `.pem` or `.ppk` files has become an administrative and security challenge—keys are frequently shared over Slack, downloaded onto personal laptops, and former employees still possess active keys. How can you eliminate SSH keys entirely while maintaining secure shell access?
- **Answer**:
  - **Solution: AWS Systems Manager (SSM) Session Manager**.
  - **Implementation**:
    1. Attach the AWS Managed IAM Policy `AmazonSSMManagedInstanceCore` to an IAM Role.
    2. Attach this IAM Role to all EC2 instances as an **Instance Profile**.
    3. Ensure the EC2 instances have outbound internet connectivity (or VPC Endpoints for SSM) and run the pre-installed AWS SSM Agent.
    4. Remove inbound Port 22 rules from all EC2 Security Groups entirely.
    5. Engineers log in via AWS Management Console (**Connect > Session Manager**) or terminal using the AWS CLI:
       ```bash
       aws ssm start-session --target <instance-id>
       ```
  - **Benefits**:
    - Zero open inbound ports (no bastion host needed).
    - No SSH key pairs to generate, rotate, or store.
    - All session keystrokes and commands are logged to Amazon CloudWatch and S3 for compliance auditing.

---

### Scenario 8: Asynchronous Order Processing Architecture Using SQS and SNS
- **Scenario**: During festive sales, an e-commerce platform receives 20,000 checkout requests per minute. Direct synchronous calls to inventory, billing, email, and shipping microservices cause backend timeouts and dropped transactions. How do you refactor this using SQS and SNS?
- **Answer**:
  - **Architecture: Pub/Sub Fan-Out Pattern**.
  1. **Order Service (Publisher)**:
     - When a customer clicks "Place Order", the frontend API writes an `OrderPlaced` event to an **Amazon SNS Topic** and immediately returns HTTP 200 to the shopper.
  2. **Decoupled Queues (Subscribers)**:
     - Create 4 independent **Amazon SQS Queues**:
       - `Inventory-Queue`
       - `Billing-Queue`
       - `Email-Notification-Queue`
       - `Shipping-Queue`
     - Subscribe all 4 queues to the `OrderPlaced` SNS Topic.
  3. **Independent Workers**:
     - Each downstream microservice polls its respective SQS queue at its own processing rate.
     - If the billing service slows down, messages buffer safely in `Billing-Queue` without affecting inventory or email confirmations.
  4. **Dead Letter Queues (DLQ)**:
     - Configure a DLQ on each SQS queue to capture corrupted or failed messages after 5 retry attempts for engineering review.

---

### Scenario 9: Auto Scaling Group Continually Launching and Terminating Instances (Flapping)
- **Scenario**: An Auto Scaling Group (ASG) repeatedly launches an EC2 instance, waits 3 minutes, terminates it, launches a replacement, and repeats this cycle continuously. What is this issue called, and how do you resolve it?
- **Answer**:
  - **Issue**: **ASG Instance Flapping / Thrashing**.
  - **Root Cause**:
    - The ASG health check type is set to **ELB**, but newly launched instances take longer to install packages and start the application than the configured **Health Check Grace Period**.
    - The ELB health checks the instance before NGINX or the application server finishes booting, marks it `Unhealthy`, and the ASG terminates the instance before it ever becomes ready.
  - **Resolution**:
    1. **Increase Health Check Grace Period**: Navigate to ASG settings and increase the grace period from the default 300 seconds to 600 seconds, giving user-data boot scripts sufficient time to complete.
    2. **Pre-bake AMIs (Golden AMI)**: Instead of installing packages via `yum update` on every boot, use EC2 Image Builder or Packer to create a pre-baked Golden AMI containing NGINX and application dependencies pre-installed.
    3. **Inspect CloudWatch / EC2 Console System Logs**: Check `/var/log/cloud-init-output.log` to identify failures in startup scripts.

---

### Scenario 10: Optimizing Disaster Recovery Drills to Prevent Unexpected Outages
- **Scenario**: During an annual scheduled DR drill, your team switches DNS to the secondary region. However, users experience database authentication errors and SSL certificate warnings for 45 minutes because configuration files and credentials were out of date in the DR site. How do you optimize future DR drills?
- **Answer**:
  1. **Automate Infrastructure as Code (IaC)**: Deploy and manage both Primary and DR environments using Terraform or AWS CloudFormation, ensuring identical configurations, security groups, and network topologies.
  2. **Centralized Secret Management**: Store database passwords and API tokens in **AWS Secrets Manager** with multi-region secret replication. Applications in both regions dynamically fetch secrets without hardcoded configurations.
  3. **Automated SSL/TLS Certificates**: Issue public certificates via **AWS Certificate Manager (ACM)** in both regions covering all primary and wildcard domain names.
  4. **Standard Operating Procedures (Runbooks)**: Create detailed, step-by-step Runbooks in AWS Systems Manager Incident Manager.
  5. **Increase Drill Frequency**: Conduct smaller automated failover drills quarterly rather than once a year.

---

### Scenario 11: Cross-Zone Load Balancing and Uneven Traffic Distribution
- **Scenario**: You have 10 EC2 instances behind an Application Load Balancer: 8 instances are deployed in `ap-south-1a`, and 2 instances are deployed in `ap-south-1b`. You observe that the 2 instances in `ap-south-1b` run at 95% CPU, while the 8 instances in `ap-south-1a` run at 15% CPU. Why is this happening, and how do you fix it?
- **Answer**:
  - **Root Cause**: **Cross-Zone Load Balancing** is disabled.
  - When Cross-Zone Load Balancing is disabled, the ALB distributes traffic 50/50 between the two Availability Zones. The 50% traffic routed to `ap-south-1a` is divided among 8 instances (6.25% per instance), while the 50% routed to `ap-south-1b` is divided between only 2 instances (25% per instance).
  - **Resolution**:
    1. Open the EC2 Console > Target Groups > Select your Target Group > **Attributes** > Click **Edit**.
    2. Enable **Cross-Zone Load Balancing**.
    3. Once enabled, the ALB balances traffic across all registered instances regardless of the Availability Zone they reside in (10% traffic per instance).
    4. Rebalance your Auto Scaling Group to maintain equal instance distribution across all AZs.

---

### Scenario 12: Zero-Downtime Application Updates for EC2 Instances Behind an ELB
- **Scenario**: You have 4 production EC2 instances running behind an Application Load Balancer. You need to deploy a new version of the web application (`v2.0`) without dropping a single active customer session or incurring maintenance downtime. How do you perform this rolling update?
- **Answer**:
  1. **Connection Draining (Deregistration Delay)**:
     - Verify that **Deregistration Delay** (Connection Draining) is enabled on the ALB Target Group (default: 300 seconds).
  2. **Rolling Deployment Steps**:
     - Take Instance 1 out of service by deregistering it from the Target Group.
     - The ALB stops sending new requests to Instance 1 while allowing in-flight requests to complete cleanly up to the deregistration delay.
     - SSH into Instance 1 (or run an automated Ansible/SSM script), deploy the new code version, restart the web service, and verify local health on `localhost`.
     - Re-register Instance 1 into the Target Group.
     - Wait for the ALB health checks to mark Instance 1 `InService`.
     - Repeat the exact same sequence for Instance 2, Instance 3, and Instance 4 sequentially.

---

### Scenario 13: Emergency Incident: Primary Datacenter Outage & Automated Route 53 Failover
- **Scenario**: At 3:00 AM, the primary datacenter hosting your production workload suffers a major power failure. Your monitoring alerts wake you up. How should Route 53 be configured so client traffic fails over to a secondary disaster recovery site automatically without human intervention?
- **Answer**:
  1. **Route 53 Health Checks**:
     - Create a Route 53 Health Check monitoring the Primary ALB endpoint (e.g., `https://primary.example.com/healthz`).
     - Configure parameters: Request interval: 10s, Failure threshold: 3.
  2. **Route 53 Failover Routing Policy**:
     - In your public hosted zone (`example.com`), create an **Alias record** for `api.example.com`:
       - **Primary Record**: Points to the Primary ALB in Mumbai. Routing policy: **Failover**, Failover record type: **Primary**, Health check ID: attached to the Primary Health Check.
       - **Secondary Record**: Points to the DR ALB in Hyderabad. Routing policy: **Failover**, Failover record type: **Secondary**.
  3. **Automatic Failover Execution**:
     - When the primary datacenter goes down, 3 consecutive health check probes fail within 30 seconds.
     - Route 53 automatically updates global DNS answers to resolve `api.example.com` to the secondary DR ALB without requiring engineer intervention.

---

### Scenario 14: Accidental Root/EBS Volume Deletion Prevention on Critical Production EC2 Instances
- **Scenario**: A junior cloud engineer accidentally terminates a production database EC2 instance. Upon termination, the attached 500 GB root EBS volume containing critical transactional data is permanently deleted from the account. How do you configure AWS to protect against this?
- **Answer**:
  1. **Enable Termination Protection on EC2**:
     - In the EC2 Console, select the instance > **Actions** > **Instance settings** > **Change termination protection** > Check **Enable**.
     - This prevents console or CLI termination until termination protection is explicitly disabled.
  2. **Disable "Delete on Termination" for EBS Volumes**:
     - For the root EBS volume, modify the attribute `DeleteOnTermination` to `false`.
     - When the EC2 instance is terminated, the underlying EBS volume remains preserved in the AWS account as an independent, detached storage volume.
  3. **Automated EBS Snapshots with AWS Backup**:
     - Configure **AWS Backup** or Amazon Data Lifecycle Manager (DLM) to take hourly/daily automated snapshots of critical volumes with retention policies and cross-region replication.
  4. **IAM Service Control Policies (SCPs)**:
     - Implement an explicit `Deny` on `ec2:TerminateInstances` and `ec2:DeleteVolume` for non-admin IAM roles on production tags.

---

### Scenario 15: Auditing Unauthorized Resource Launches and Infrastructure Changes
- **Scenario**: At month-end, the finance team flags an unexpected $5,000 bill increase for 10 expensive GPU instances (`g5.4xlarge`) launched in the Frankfurt region (`eu-central-1`). The DevOps team states they never launched these instances. How do you investigate who created them, when, and from where?
- **Answer**:
  1. **Query AWS CloudTrail Event History**:
     - Navigate to **AWS CloudTrail** in the `eu-central-1` (Frankfurt) region.
     - In **Event history**, filter by **Event name**: `RunInstances`.
  2. **Analyze Event Record Details**:
     - The JSON event record contains:
       - `userIdentity`: Identifies the exact IAM user, IAM role, or Root account that initiated the API request.
       - `eventTime`: Exact UTC timestamp when the instances were launched.
       - `sourceIPAddress`: The external IP address used by the requester.
       - `userAgent`: The client tool used (e.g., AWS Console, AWS CLI, Terraform, Python boto3).
  3. **Remediation & Root Cause Analysis**:
     - Immediately terminate the unauthorized instances.
     - If credentials were leaked, revoke active STS sessions, delete access keys, or rotate passwords.
     - Review AWS GuardDuty findings to verify if the account was compromised via credential exposure on GitHub or malware.
     - Configure **AWS Budgets** and **CloudWatch Billing Alarms** with SNS alerts to notify teams immediately when daily charges deviate from standard baselines.
