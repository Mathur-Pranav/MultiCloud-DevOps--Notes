# AWS Day 4 - Session Summary: EC2 Ecosystem & Advanced Operations

A student-friendly reference guide based on the Day 4 AWS Cloud & DevOps session by Vikas from CloudDevOpsHub.

---

## 1. Mindset Shift: EC2 is an Ecosystem, Not Just a VM

A major interview and practical takeaway from this session is redefining how we describe Amazon EC2:

- **Common Student Mistake**: Describing EC2 merely as "a virtual machine in the cloud."
- **Production / Interview Reality**: EC2 is an entire **cloud ecosystem** comprising:
  - **Compute**: Instances, instance types, dedicated hosts, spot instances, savings plans.
  - **Images**: Amazon Machine Images (AMIs), catalog, AWS Marketplace.
  - **Storage**: Elastic Block Store (EBS) volumes, snapshots, Data Lifecycle Manager.
  - **Networking & Interfaces**: Elastic Network Interfaces (ENI), Elastic IP addresses (EIP).
  - **Security**: Security Groups (firewalls at the instance level), key pairs.
  - **Load Distribution**: Elastic Load Balancers (ALB, NLB, GWLB, CLB) and Target Groups.
  - **Elasticity & High Availability**: Auto Scaling Groups (ASG) and Launch Templates.

---

## 2. EC2 Dashboard & Global Infrastructure Navigation

### 2.1 EC2 Dashboard
- Central management console showing real-time operational status of all EC2 resources in the currently selected region.
- Displays counts of running instances, dedicated hosts, volumes, snapshots, load balancers, security groups, and placement groups.

### 2.2 AWS Global View
- A centralized, global view across all AWS regions.
- Instead of manually switching regions (e.g., from Mumbai `ap-south-1` to N. Virginia `us-east-1` or Frankfurt `eu-central-1`), Global View aggregates resources across every region in a single table.
- **Resource Billing Distinction**:
  - **Free Resources (No hourly charge)**: VPCs, Subnets, Security Groups, Route Tables. You do not need to delete these to avoid bills.
  - **Chargeable Resources**: Running EC2 instances, provisioned EBS volumes, unattached/idle Elastic IPs, and active Load Balancers.

### 2.3 Events Tab
- Displays AWS-initiated activities scheduled for your infrastructure.
- Includes scheduled maintenance, underlying hardware retirement, host reboots, regional upgrades, and failover notices.

### 2.4 Instance Lifecycle States
- **Running**: Active, compute charges apply.
- **Stopped**: Compute charges stop; EBS storage volume charges continue.
- **Terminated**: Instance is deleted; it remains visible in the AWS console for approximately **15 to 20 minutes** before disappearing automatically.

---

## 3. Amazon Machine Image (AMI) Deep Dive

### 3.1 What is an AMI?
An **Amazon Machine Image (AMI)** is a pre-configured operating system image packaged with necessary customizations, installed software, dependencies, and configuration settings required to launch virtual instances.

### 3.2 Key AMI Concepts
- **AMI ID**: A unique identifier (e.g., `ami-0abcdef1234567890`) assigned to each AMI within a region. It serves as the primary reference when automating infrastructure deployments (via scripts, Terraform, or CloudFormation).
- **Architecture**: Modern workloads almost exclusively run on **64-bit (x86)** or **64-bit (Arm / AWS Graviton)** architectures. 32-bit is deprecated in production.
- **Selection Criteria**:
  - Client and application requirements.
  - OS distribution (Amazon Linux 2023, Ubuntu, RHEL, Windows Server).
  - Always prefer **Verified / Official AMIs** from AWS or trusted Marketplace vendors for production security.

### 3.3 AMI Catalog & AWS Marketplace
- **AMI Catalog**: A pre-organized directory within AWS featuring Quick Start AMIs, Community AMIs, and private AMIs.
- **AWS Marketplace**: A commercial repository with over 5,000+ pre-built AMIs provided by third-party vendors (e.g., CIS hardened benchmarks, pre-installed security agents, ready-to-run enterprise tools).

### 3.4 Step-by-Step Hands-On: Creating a Custom AMI
1. Select an existing running EC2 instance.
2. Click **Actions** > **Image and templates** > **Create image**.
3. **Configure Image Parameters**:
   - **Image name**: Enter a descriptive name (e.g., `Production-Web-AMI`).
   - **Image description**: Detail the installed packages (e.g., `Amazon Linux 2023 with NGINX pre-configured`).
   - **No reboot checkbox**:
     - *Checked*: The instance will NOT reboot during creation. Faster, but risks data inconsistency if applications are writing data during the snapshot process.
     - *Unchecked (Default & Recommended)*: The instance reboots briefly to flush file system buffers and ensure snapshot consistency.
   - **Storage volume settings**: Review or expand the root volume size if required.
4. Click **Create image**.
5. Navigate to **AMIs** in the left menu. Status will transition from `Pending` to `Available`.

### 3.5 Real-World Production Use Case: The "Golden AMI"
- **Scenario**: A company onboarded 100 developers or needs to spin up 100 application servers.
- **Bad Approach**: Launch 100 raw base Linux instances and manually install software (Git, Docker, NGINX, security agents) on every single server.
- **Best Practice (Golden AMI)**:
  1. Configure one base instance with all enterprise-required packages, security tools, and configurations.
  2. Create a custom AMI (Golden AMI).
  3. Launch all 100 instances from this custom AMI in minutes with zero manual setup.

### 3.6 AMI vs. Launch Template
- **AMI**: The packaged disk image containing the operating system, file system, installed packages, and application code.
- **Launch Template**: A configuration blueprint specifying *how* an instance should launch (instance type, AMI ID, key pair, security groups, subnet, tags, user data script, storage mappings).

---

## 4. EC2 Instance Purchasing & Pricing Models

AWS offers multiple purchasing options to optimize operational costs:

| Model | How It Works | Cost Savings | Primary Use Case |
| :--- | :--- | :--- | :--- |
| **On-Demand** | Pay strictly per second/hour for active compute with no long-term commitment. | Baseline pricing (0% discount) | Unpredictable, short-term workloads, development, testing. |
| **Spot Instances** | Bid on spare, unused AWS datacenter capacity. AWS can reclaim instances with a 2-minute notice if on-demand demand spikes. | Up to 70% – 90% discount | Batch processing, background worker jobs, stateless architectures, non-critical rendering. |
| **Savings Plans** | Flexible discount model committing to a consistent dollar spend per hour (e.g., $10/hour) for a 1 or 3-year term across compute services. | Up to 66% – 72% discount | Dynamic, evolving workloads across EC2, AWS Lambda, and AWS Fargate. |
| **Reserved Instances (RI)** | Commit to specific instance configurations (family, region) for a 1-year or 3-year period. Payment options: All Upfront, Partial Upfront, or No Upfront (monthly installments). | Up to 60% – 72% discount | Steady-state, 24/7 predictable production workloads (core databases, main web tiers). |
| **Dedicated Hosts** | Physical server dedicated completely for a single organization's use. | High fixed server cost | Strict regulatory compliance, hardware isolation, or Bring-Your-Own-License (BYOL) software (Windows, Oracle). |
| **Capacity Reservations** | Reserve compute capacity in a specific Availability Zone ahead of time for any duration without commitment discounts unless paired with Savings Plans. | Standard On-Demand rates unless combined with RI/Savings Plans | High-traffic seasonal events (e.g., Big Billion Days, Diwali sales, major product launches) ensuring servers can definitely launch. |

### Real-World Production Sizing Baseline
In corporate production environments, standard baseline compute instances often start around:
- **RAM**: 16 GB+
- **CPU**: 4 to 16 vCPU Cores
- **Storage**: 512 GB+ EBS GP3 volume

---

## 5. Storage: EBS Volumes, Snapshots & Data Lifecycle Manager

### 5.1 Elastic Block Store (EBS) Volumes
- A persistent, block-level storage volume directly attached to an EC2 instance.
- Operates like a virtual hard disk (e.g., `C:` drive or `/dev/xvda`).
- **Availability Zone Restriction**: An EBS volume can only be attached to an EC2 instance residing in the **exact same Availability Zone** (e.g., a volume created in `ap-south-1a` cannot attach directly to an instance in `ap-south-1b`).

### 5.2 Snapshots
- Point-in-time backups of EBS volumes stored incrementally in Amazon S3.
- Only blocks that have changed since the previous snapshot are saved, reducing storage consumption.

### 5.3 The 5 Core Rules of Volumes, Snapshots, and AMIs
Vikas highlighted the core lifecycle relationships that frequently appear in technical interviews:

1. **From an EC2 instance**: You create an **AMI** (captures OS, installed software, configuration, and root drive).
2. **From an EBS volume**: You create a **Snapshot** (captures data block-by-block).
3. **From an EBS snapshot**: You can restore/create a new **EBS Volume**.
4. **From an EBS snapshot**: You can also register and create an **AMI**.
5. **Deciding between Volume vs. AMI from Snapshot**:
   - If you only need data or an extra storage disk to attach to an existing VM, convert the snapshot to an **EBS Volume**.
   - If you need a bootable operating system to launch new instances, convert the snapshot to an **AMI**.

### 5.4 Step-by-Step Hands-On: Snapshot Creation, Volume Recovery, and Attachment
1. **Take Snapshot**:
   - Navigate to **Volumes** > Select the attached EBS volume.
   - Click **Actions** > **Create snapshot**.
   - Name: `backup-snapshot-1` > Click **Create snapshot**.
   - Under **Snapshots**, status changes from `Pending` to `Completed`.
2. **Restore Volume from Snapshot**:
   - Select the completed snapshot.
   - Click **Actions** > **Create volume from snapshot**.
   - Specify the target **Availability Zone** (must match your target EC2 instance's AZ).
   - Click **Create volume**.
3. **Attach Volume to EC2**:
   - Go to **Volumes** > Select the restored volume (status: `Available`).
   - Click **Actions** > **Attach volume**.
   - Select the target running instance ID and assign the device name (e.g., `/dev/sdf`).
   - Click **Attach volume**. Status changes to `In-use`.

### 5.5 Amazon Data Lifecycle Manager (DLM)
- Built-in AWS service that automates the creation, retention, and deletion of EBS volume snapshots.
- **Configuration Workflow**:
  - Define target resource tags (e.g., `Environment = Production`).
  - Set schedule (e.g., take snapshot daily at 02:00 UTC).
  - Set retention policy (e.g., retain for 30 days, then delete automatically).
  - Prevents snapshot accumulation and unexpected storage bills.

---

## 6. Network Security: Security Groups (SG)

### 6.1 Fundamentals
- A virtual, stateful firewall operating at the EC2 instance level.
- Controls inbound (ingress) and outbound (egress) network traffic.

### 6.2 Inbound vs. Outbound Rules
- **Inbound Rules**: Must explicitly specify allowed protocol, port range, and source IP/CIDR:
  - SSH: Port 22
  - HTTP: Port 80
  - HTTPS: Port 443
  - Custom Applications (e.g., Jenkins on Port 8080)
- **Outbound Rules**: By default, AWS allows all outbound traffic (`0.0.0.0/0`).

### 6.3 Stateful Behavior
- Security groups are **stateful**: If an inbound request is permitted on a port, the return outbound response traffic is automatically allowed, regardless of outbound rules.

### 6.4 Security Group vs. Network ACL (NACL)

| Feature | Security Group (SG) | Network ACL (NACL) |
| :--- | :--- | :--- |
| **Level** | Instance level (Virtual NIC) | Subnet level |
| **Rule Types** | **ALLOW only** (implicit deny for anything not listed) | **ALLOW and DENY** rules supported |
| **State** | **Stateful** (return traffic automatically permitted) | **Stateless** (return traffic must be explicitly permitted) |
| **Blacklisting IPs** | Cannot explicitly block a single IP address | Can explicitly **DENY** specific malicious IPs via rule ordering |

---

## 7. Elastic IP Addresses (EIP)

### 7.1 What is an Elastic IP?
An **Elastic IP address** is a static, fixed public IPv4 address allocated to your AWS account that does not change when an EC2 instance is stopped and started.

### 7.2 The Dynamic IP Problem
- Standard EC2 public IPv4 addresses are dynamic and ephemeral.
- If you stop and restart an EC2 instance, AWS releases the old public IP and assigns a completely new public IP.
- This breaks hardcoded client configurations, DNS entries, and external whitelists.
- Associating an Elastic IP ensures the server maintains the exact same public IP throughout its lifecycle.

### 7.3 Step-by-Step Hands-On: Allocating, Associating, and Releasing EIP
1. In the EC2 console left menu, navigate to **Network & Security** > **Elastic IPs**.
2. Click **Allocate Elastic IP address** > Confirm allocation.
3. Select the allocated Elastic IP > **Actions** > **Associate Elastic IP address**.
4. Choose **Instance** > Select the target EC2 instance > Click **Associate**.
5. The instance public IP and public DNS now reflect the static Elastic IP.
6. **Critical Cleanup Step**:
   - Click **Actions** > **Disassociate Elastic IP address**.
   - Click **Actions** > **Release Elastic IP address**.

### 7.4 The Billing Trap of Elastic IPs
- AWS charges for public IPv4 addresses (~$0.005 per hour / ~$3.60 per month).
- **Idle Elastic IP Charge**: If you allocate an Elastic IP and leave it unattached (or attached to a stopped instance), AWS charges you for wasting IPv4 address space.
- Always **Release** Elastic IPs immediately after practice.

### 7.5 Production Use Cases
- Fixed VPN endpoints where client software connects to a constant IP.
- Third-party partner whitelists that only accept requests from a verified, static IP.
- NAT Gateways in custom VPCs.

---

## 8. Key Pairs Management & Lost Key Scenarios

### 8.1 Key Pair Basics
- Public-private key cryptography for authenticating SSH sessions to EC2 instances.
- Formats:
  - `.pem`: OpenSSH format for Linux, macOS, and native Windows PowerShell.
  - `.ppk`: PuTTY / MobaXterm format.
- **One-Time Download**: The private key is only downloadable once when created. AWS never stores your private key and cannot re-generate it for you.
- Key pairs themselves are free of cost.

### 8.2 Scenario: "What if you lose your private SSH key?"
Since AWS cannot regenerate the key, use one of the following recovery procedures:

1. **AWS Systems Manager (SSM) Session Manager**:
   - If the EC2 instance has an IAM Role with `AmazonSSMManagedInstanceCore` attached and the SSM Agent is running, connect directly via the AWS console with zero SSH keys needed.
2. **EC2 Instance Connect**:
   - Connect directly through the browser console using short-lived keys if supported by the AMI and security group.
3. **EBS Volume Swap Recovery Method**:
   - Stop the original instance (Instance A).
   - Detach its root EBS volume.
   - Attach the volume to a healthy helper instance (Instance B) as a secondary drive.
   - Mount the drive on Instance B and edit the `~/.ssh/authorized_keys` file to inject a new public key.
   - Unmount, detach, and re-attach the volume back to Instance A as the root volume (`/dev/xvda`).
   - Start Instance A and connect using the new private key.
4. **AMI Recreation Method**:
   - Stop the instance > Create an AMI from it.
   - Launch a new instance from that AMI, selecting a brand-new Key Pair during launch.

---

## 9. Elastic Load Balancing (ELB) & Target Groups

### 9.1 The Four Types of Elastic Load Balancers

```
+--------------------------------------------------------------------------+
|                       ELASTIC LOAD BALANCING (ELB)                       |
+--------------------------------------------------------------------------+
| 1. Application Load Balancer (ALB)  - Layer 7 (HTTP/HTTPS)               |
| 2. Network Load Balancer (NLB)      - Layer 4 (TCP/UDP/TLS)              |
| 3. Gateway Load Balancer (GWLB)     - Layer 3/4 (Firewall / IDS / IPS)   |
| 4. Classic Load Balancer (CLB)      - Layer 4 & 7 (Legacy Architecture)  |
+--------------------------------------------------------------------------+
```

1. **Application Load Balancer (ALB) - Layer 7 (Application)**:
   - Evaluates HTTP and HTTPS application layer protocols.
   - Supports **Path-based routing** (e.g., `/products` routed to one server group, `/orders` to another).
   - Supports **Host-based routing**, container routing, and WebSocket traffic.
2. **Network Load Balancer (NLB) - Layer 4 (Transport)**:
   - Operates on raw TCP, UDP, and TLS connections.
   - Ultra-high throughput and ultra-low latency (millisecond response times).
   - Supports assigning static Elastic IPs directly to the load balancer endpoints.
3. **Gateway Load Balancer (GWLB) - Layer 3/4**:
   - Deploys, scales, and manages fleets of third-party virtual security appliances (e.g., enterprise firewalls, Intrusion Detection/Prevention Systems).
4. **Classic Load Balancer (CLB) - Legacy (Layer 4 / Layer 7)**:
   - Previous generation load balancer supporting both Layer 4 and Layer 7 protocols.
   - Common in legacy architectures, but modern AWS deployments use ALB or NLB.

### 9.2 Path-Based Routing vs. Port-Based Routing
- **Path-Based Routing (ALB Feature)**: Routes traffic based on the URL path requested by the client:
  - `example.com/products` -> Routed to Product Microservice Target Group.
  - `example.com/courses` -> Routed to Course Platform Target Group.
  - `example.com/orders` -> Routed to Order Processing Target Group.
- **Port-Based Routing**: Routes traffic based on the incoming destination port (e.g., Port 80, Port 443, Port 8080).

### 9.3 Target Groups
- A logical grouping of backend targets (EC2 instances, IP addresses, or Lambda functions) registered to receive traffic forwarded by load balancer listener rules.
- Manages **health checks** independently for that specific tier (e.g., Web Server Target Group vs. App Server Target Group).

### 9.4 Trust Store
- A feature in Application Load Balancer listener configurations used to store trusted Certificate Authority (CA) certificates.
- Enables **mutual TLS (mTLS)** authentication to verify client certificates before routing requests.

---

## 10. Auto Scaling Groups (ASG) & Elasticity

### 10.1 What is Auto Scaling?
An **Auto Scaling Group (ASG)** automatically adjusts the number of EC2 instances up or down to match application load, ensuring high availability, fault tolerance, and cost optimization.

### 10.2 Core Capacity Settings
- **Minimum Capacity**: The lowest number of instances that must always be running (e.g., `2`).
- **Desired Capacity**: The normal baseline number of instances needed for standard operational traffic.
- **Maximum Capacity**: The upper ceiling limit of instances that can be launched during peak traffic spikes (e.g., `10`).

### 10.3 Horizontal Scaling vs. Vertical Scaling
- **Horizontal Scaling (Scale Out / Scale In)**: Adding or removing instances (e.g., going from 2 servers to 6 servers during a sale). This is what Auto Scaling does.
- **Vertical Scaling (Scale Up / Scale Down)**: Resizing the instance compute capacity (e.g., stopping an instance to upgrade from `t2.micro` to `m5.large`). Requires downtime.

### 10.4 Auto Scaling Group vs. Load Balancer

| Dimension | Load Balancer (ELB) | Auto Scaling Group (ASG) |
| :--- | :--- | :--- |
| **Primary Job** | **Distributes** incoming traffic across healthy target servers. | **Manages capacity** by adding/removing servers based on load. |
| **Health Checks** | Marks unhealthy targets `OutOfService` and redirects traffic elsewhere. | Terminates unhealthy instances and launches healthy replacements. |
| **Integration** | Routes traffic to targets registered in the **Target Group**. | Automatically **registers** newly scaled instances into the Target Group. |

### 10.5 Interview Scenario: ASG Instance Flapping / Thrashing
- **Problem**: Auto Scaling Group continuously launches an instance, waits a few minutes, terminates it, and launches another one in an endless loop.
- **Root Causes & Solutions**:
  1. **Health Check Grace Period**: The grace period is too short (e.g., set to 60 seconds while the web server or application startup script takes 180 seconds to boot). Fix: Increase the Health Check Grace Period to 300+ seconds.
  2. **Health Check Path Mismatch**: Health check looks for `/index.html`, but the application serves on `/` or returns a 404.
  3. **Metric Thresholds**: Scale policies should be configured using **5-minute rolling averages** rather than instant spikes to prevent reacting to momentary nanosecond CPU spikes.

---

## 11. Live Troubleshooting Cases & Practical Gotchas

During the class session, several real-world student errors were diagnosed and resolved:

### 11.1 Problem 1: ELB Displays "OutOfService" / Backend Returns 502/504
- **Diagnosed Causes**:
  1. **Availability Zone Mismatch**: The Load Balancer was configured in subnets for `ap-south-1a`, while the EC2 instances were deployed in `ap-south-1b`. Load balancers must have subnets enabled across all AZs where target instances reside.
  2. **Security Group Isolation**: The EC2 instance security group blocked incoming traffic on port 80 from the load balancer. The EC2 security group must allow port 80 from the load balancer security group (or `0.0.0.0/0`).
  3. **Case Sensitivity in URLs**: Linux file paths and HTTP requests are case-sensitive. Requesting `/index.html` when the file was named with capital letters fails the health check.

### 11.2 Problem 2: SSH Connection Timeout / Connection Refused
- **Diagnosed Causes**:
  1. The EC2 instance security group was missing an inbound rule for **SSH (Port 22)** from the client IP (or `0.0.0.0/0`).
  2. Selecting "Custom" source IP without providing an address instead of selecting "Anywhere IPv4" (`0.0.0.0/0`).
  3. The subnet route table lacked an active route to an Internet Gateway (`0.0.0.0/0` -> `igw-xxxx`).

### 11.3 Web Server vs. Application Server
- **Web Server (e.g., NGINX, Apache, IIS)**: Front-facing web server handling static assets (HTML, CSS, JS, images), SSL/TLS offloading, compression, and reverse proxy routing.
- **Application Server (e.g., Tomcat, Node.js, Gunicorn, ASP.NET Core)**: Backend server executing dynamic business logic, processing application transactions, and connecting directly to databases.

---

## 12. End-of-Session Resource Cleanup Checklist

To prevent recurring charges on personal or free-tier AWS accounts, perform the following cleanup steps:

1. **EC2 Instances**: Select instances > **Instance state** > **Terminate instance**.
2. **AMIs**: Go to **AMIs** > Select custom AMI > **Actions** > **Deregister AMI**.
3. **Snapshots**: Go to **Snapshots** > Select snapshot > **Actions** > **Delete snapshot**.
4. **Volumes**: Go to **Volumes** > Ensure detached volumes are deleted.
5. **Elastic IPs**: Go to **Elastic IPs** > Select IP > **Actions** > **Disassociate** > **Actions** > **Release Elastic IP**.
6. **Load Balancers**: Select Load Balancer > **Actions** > **Delete load balancer**.
7. **Target Groups**: Select Target Group > **Actions** > **Delete**.
8. *Note*: VPCs, Subnets, Security Groups, and Route Tables are free and do not incur ongoing costs.

---

## 13. 10 Core Interview Questions with Answers

### Q1: Why should EC2 be described as an "ecosystem" rather than merely a virtual machine?
**Answer:**
In modern enterprise environments, calling EC2 just a virtual machine demonstrates entry-level thinking. EC2 is a complete cloud computing ecosystem composed of tightly coupled subsystems:
- **Compute**: Sizing families (General Purpose, Compute/Memory/Storage Optimized), purchasing options (On-Demand, Spot, Reserved, Dedicated Hosts).
- **Images (AMI)**: Bootable OS templates customized with dependencies and security baselines.
- **Block Storage (EBS)**: Detachable, persistent, high-IOPS virtual disks and point-in-time S3 snapshots.
- **Networking & Interfaces (ENI / EIP)**: Virtual network interface controllers and persistent static public IPv4 addresses.
- **Security**: Stateful virtual firewalls (Security Groups) and asymmetric key pairs.
- **Traffic Routing & Elasticity**: Elastic Load Balancers (ALB, NLB, GWLB), Target Groups, and Auto Scaling Groups (ASG) for dynamic horizontal elasticity.

---

### Q2: What is an Amazon Machine Image (AMI), and how does a "Golden AMI" benefit enterprise operations?
**Answer:**
- **AMI (Amazon Machine Image)**: A pre-configured template containing the operating system, storage volume mappings, architecture specifications (x86 or ARM64), and pre-installed software required to instantiate an EC2 server.
- **The "Golden AMI" Strategy**:
  - Rather than provisioning vanilla OS instances and sequentially running slow package installation scripts (`yum update`, Docker, NGINX, monitoring agents) across 50–100 new servers, DevOps engineers build and validate one standardized, hardened "Golden AMI".
  - All new production instances and auto-scaling groups launch from this Golden AMI, ensuring 100% configuration consistency, faster launch times (<60 seconds), and zero external repository dependency during scale-out.

---

### Q3: What are the differences between Spot Instances, Reserved Instances (RI), and Savings Plans?
**Answer:**
- **Spot Instances**: Spare compute capacity offered at up to 70%–90% discounts. AWS can terminate or reclaim the instance with a 2-minute warning when on-demand capacity is required. Best for fault-tolerant, stateless batch jobs, CI/CD runners, and background rendering.
- **Reserved Instances (RI)**: A 1-year or 3-year contractual commitment to a specific instance family in a specific region. Offers up to 60%–72% discount. Payment options include All Upfront, Partial Upfront, or No Upfront (monthly installments). Best for steady-state 24/7 databases and core servers.
- **Savings Plans**: A commitment to a consistent dollar spend per hour (e.g., $10/hour) for 1 or 3 years. Provides up to 66%–72% discount with greater flexibility—automatically applying savings across instance families, operating systems, AWS Fargate, and AWS Lambda regardless of region.

---

### Q4: Explain the 5 core lifecycle rules connecting EC2 Instances, EBS Volumes, Snapshots, and AMIs.
**Answer:**
1. **EC2 Instance -> AMI**: Creating an image from an EC2 instance captures the root operating system, installed applications, configuration, and data volumes.
2. **EBS Volume -> Snapshot**: Taking a snapshot of an EBS volume generates a point-in-time, incremental backup stored durably in Amazon S3.
3. **Snapshot -> EBS Volume**: An EBS snapshot can be restored directly into a new, independent EBS volume in any Availability Zone within the region.
4. **Snapshot -> AMI**: A root EBS snapshot can be registered as an AMI to launch brand-new EC2 instances.
5. **Deciding between Volume vs. AMI Restoration**:
   - Restore as an **EBS Volume** when you need a raw secondary data disk to attach to an existing running server.
   - Restore as an **AMI** when you need a bootable drive to launch new virtual instances.

---

### Q5: How do Security Groups differ from Network Access Control Lists (NACLs)? Why can't you blacklist an IP in a Security Group?
**Answer:**
- **Security Groups (SG)**:
  - Operate at the **instance/NIC level**.
  - **Stateful**: If inbound traffic is allowed, outbound return traffic is automatically permitted regardless of outbound rules.
  - Support **ALLOW rules only**. There is an implicit "Deny All" for any traffic not explicitly permitted. Because you cannot write an explicit DENY rule, you cannot selectively blacklist an IP address in a Security Group.
- **Network ACLs (NACL)**:
  - Operate at the **subnet boundary**.
  - **Stateless**: Inbound and outbound traffic must be evaluated and permitted independently.
  - Support both **ALLOW and DENY rules** evaluated in numerical rule order. To blacklist a malicious IP address, you place an explicit DENY rule with a low rule number (e.g., Rule 50: DENY `203.0.113.50/32`) in the subnet's NACL.

---

### Q6: What is an Elastic IP address, and why does AWS charge for idle or unattached Elastic IPs?
**Answer:**
- An **Elastic IP (EIP)** is a static, persistent public IPv4 address allocated to an AWS account that remains unchanged even when an EC2 instance is stopped and restarted.
- **Why it is needed**: Standard public IPs assigned to EC2 instances are dynamic and released when an instance stops. An Elastic IP prevents DNS configuration breakage and connection failure.
- **The Idle EIP Charge**: Public IPv4 address space is globally scarce. AWS charges approximately $0.005 per hour for Elastic IPs that are allocated but not associated with a running instance (or attached to a stopped instance) to discourage address hoarding. Engineers must disassociate and **release** unused Elastic IPs back to the AWS pool.

---

### Q7: If you lose the private SSH key (`.pem` / `.ppk`) for an EC2 instance, can AWS regenerate it? How do you recover access?
**Answer:**
- **Can AWS regenerate it?**: **No.** AWS never stores the private key on its servers; it only holds the corresponding public key. Once lost, the private key cannot be retrieved or downloaded again from the AWS console.
- **Recovery Methods**:
  1. **AWS Systems Manager (SSM) Session Manager**: Connect directly via browser/CLI with zero SSH keys required (if the instance has the `AmazonSSMManagedInstanceCore` IAM policy attached and the SSM agent running).
  2. **EBS Volume Swap Recovery**: Stop the locked instance (A), detach its root volume, attach it as a secondary data disk to a temporary helper instance (B), mount the drive, append a newly generated SSH public key into `~/.ssh/authorized_keys`, unmount and detach the volume, reattach it back to instance A as `/dev/xvda`, and boot instance A.
  3. **AMI Recreation**: Stop instance A, create an AMI from it, and launch a replacement instance while specifying a new, freshly downloaded Key Pair.

---

### Q8: Compare the four types of Elastic Load Balancers (ALB, NLB, GWLB, CLB).
**Answer:**
- **Application Load Balancer (ALB)**: Layer 7 (HTTP/HTTPS). Inspects application-layer data to support path-based routing (`/products`, `/users`), host-based routing, redirect rules, and WebSocket connections. Ideal for web apps and microservices.
- **Network Load Balancer (NLB)**: Layer 4 (TCP/UDP/TLS). Capable of handling millions of requests per second with ultra-low latency. Supports assigning static/Elastic IPs to load balancer endpoints. Ideal for gaming, streaming, financial exchanges, and non-HTTP protocols.
- **Gateway Load Balancer (GWLB)**: Layer 3/4. Designed to deploy, scale, and manage fleets of third-party virtual security appliances (firewalls, Intrusion Detection/Prevention Systems).
- **Classic Load Balancer (CLB)**: Legacy, previous-generation load balancer operating across Layer 4 and Layer 7. Supports basic HTTP/HTTPS/TCP load balancing but lacks advanced routing rules and target group flexibility.

---

### Q9: What is Path-Based Routing on an Application Load Balancer, and how do Target Groups enable it?
**Answer:**
- **Path-Based Routing**: An ALB feature where incoming HTTP requests are forwarded to different backend target pools based on the URL path requested by the client.
  - `https://example.com/api/*` -> Forwarded to Backend API Target Group.
  - `https://example.com/images/*` -> Forwarded to Static Media Target Group.
  - `https://example.com/*` -> Forwarded to Main Web Frontend Target Group.
- **Target Groups**: Logical groups of backend servers, private IPs, or Lambda functions registered to receive traffic from load balancer rules. Each Target Group executes its own independent health checks, allowing different microservice clusters to scale and monitor health autonomously behind a single load balancer.

---

### Q10: What is the architectural difference between an Elastic Load Balancer (ELB) and an Auto Scaling Group (ASG)?
**Answer:**
- **Elastic Load Balancer (ELB)**:
  - Role: **Traffic Distributor**.
  - Distributes incoming user requests evenly across an existing pool of backend servers.
  - Performs health checks and temporarily stops forwarding traffic to instances marked `OutOfService`.
- **Auto Scaling Group (ASG)**:
  - Role: **Capacity & Fleet Manager**.
  - Dynamically adds (scales out) or terminates (scales in) EC2 instances to maintain desired operational capacity based on metrics (CPU, network, queue depth).
  - Automatically replaces failed or terminated instances with fresh ones.
- **Collaboration**: ASG integrates directly with ELB Target Groups. When ASG scales out, newly launched instances are automatically registered into the ELB Target Group; when ASG scales in, it deregisters instances smoothly using connection draining before terminating them.

---

## 14. 10 Scenario-Based Interview Questions with Answers

### Scenario 1: Load Balancer Targets Remain "OutOfService" Due to Availability Zone Mismatch
- **Scenario**: You configure a Classic or Application Load Balancer and register two healthy EC2 web servers. Both servers serve web pages when accessed directly via their public IPs, but the load balancer reports both targets as `OutOfService`. Browsing the load balancer DNS returns HTTP 502/504 errors. You notice the load balancer was created in subnet `ap-south-1a`, but both EC2 instances reside in `ap-south-1b`. How do you resolve this?
- **Answer**:
  1. **Root Cause**: An Elastic Load Balancer cannot forward traffic to backend target instances located in an Availability Zone where the load balancer has no enabled subnets.
  2. **Resolution**:
     - Navigate to **EC2** > **Load Balancers** > Select the load balancer.
     - Go to the **Network Mapping** / **Subnets** configuration tab.
     - Click **Edit subnets** and add the subnet corresponding to `ap-south-1b` (where the instances reside).
     - Alternatively, ensure instances are distributed across both `ap-south-1a` and `ap-south-1b` for high availability.
     - Once the load balancer has active nodes in `ap-south-1b`, health check probes succeed and targets transition to `InService`.

---

### Scenario 2: Auto Scaling Group Instance Flapping in an Endless Launch-Terminate Loop
- **Scenario**: A newly deployed Auto Scaling Group launches an EC2 instance, waits approximately 90 seconds, marks it unhealthy, terminates it, launches a replacement, and repeats this cycle indefinitely. Your web application requires 3 minutes to download dependencies and boot NGINX. What is this issue called, and how do you resolve it?
- **Answer**:
  - **Issue**: **ASG Instance Flapping / Thrashing**.
  - **Root Cause**: The **Health Check Grace Period** is set too short (e.g., the default or customized 60–120 seconds). The load balancer begins health checks before the application startup script completes booting, marks the instance `Unhealthy`, and the ASG immediately terminates it.
  - **Resolution**:
    1. **Increase Health Check Grace Period**: Open the Auto Scaling Group settings and extend the grace period to 300–600 seconds, allowing ample time for package installation and service startup.
    2. **Pre-bake Dependencies into a Golden AMI**: Instead of running extensive startup scripts on boot, build an AMI with NGINX and application runtimes pre-installed. The instance will boot in under 30 seconds and pass health checks immediately.

---

### Scenario 3: Investigating Unexplained Charges on an Inactive AWS Account
- **Scenario**: A junior engineer created a test EC2 instance, attached an Elastic IP, took two manual snapshots, and terminated the EC2 instance at the end of the day. At the end of the month, the finance team flags ongoing charges for this account. What caused these charges despite the instance being terminated?
- **Answer**:
  1. **Idle Elastic IP Charges**: When the EC2 instance was terminated, the Elastic IP became unassociated and remained idle in the account. AWS charges ~$0.005/hour for unattached Elastic IPs.
     - *Fix*: Navigate to **Elastic IPs** > Select the IP > Click **Actions** > **Release Elastic IP address**.
  2. **EBS Snapshots Storage**: Terminating an EC2 instance does NOT delete standalone snapshots created from its volumes. Snapshots continue to incur standard S3 per-GB storage fees each month.
     - *Fix*: Navigate to **Snapshots** > Select the old snapshots > Click **Actions** > **Delete snapshot**.
  3. **Best Practice**: Use **Amazon Data Lifecycle Manager (DLM)** with auto-expiry retention policies to automatically clean up orphaned snapshots.

---

### Scenario 4: Urgent Configuration Recovery on a Production Server with Lost SSH Key
- **Scenario**: A critical production EC2 instance running an internal API service requires an emergency configuration fix. The DevOps engineer who created the instance has left the company, and the private key (`.pem`) is lost. You cannot afford extended service downtime. How do you regain administrative shell access safely?
- **Answer**:
  - **Preferred Zero-Downtime Approach (AWS Systems Manager)**:
    - If the EC2 instance has an IAM instance profile with `AmazonSSMManagedInstanceCore` and outbound internet connectivity, open the EC2 console, select the instance, and click **Connect** > **Session Manager** to open an interactive bash shell with zero downtime and no SSH keys.
  - **Alternative Volume Swap Approach (Requires Brief Scheduled Downtime)**:
    1. Stop the production instance (Instance A).
    2. Detach its root volume (`/dev/xvda`).
    3. Attach the volume to a running rescue instance (Instance B) as `/dev/xvdf`.
    4. SSH into Instance B, mount the partition (`mount /dev/xvdf1 /mnt`), and append a new, authorized public SSH key into `/mnt/home/ec2-user/.ssh/authorized_keys`.
    5. Unmount (`umount /mnt`), detach the volume from Instance B, and reattach it to Instance A as the root volume (`/dev/xvda`).
    6. Start Instance A and log in using the newly matched private key.

---

### Scenario 5: Eliminating Massive Scale-Out Latency Using a Golden AMI
- **Scenario**: During flash-sale traffic spikes, your Auto Scaling Group takes 12 minutes to scale out from 4 to 20 instances because each new instance executes a 50-line User Data bash script running `yum update`, installing Python, compiling dependencies, and configuring NGINX. During these 12 minutes, existing servers experience 100% CPU utilization and drop user requests. How do you re-architect this?
- **Answer**:
  1. **Adopt the Golden AMI Pattern**:
     - Launch a temporary staging instance and execute the User Data script once to install and verify all system packages, Python runtimes, and NGINX configurations.
     - Create a custom AMI (Golden AMI) from this staging instance and test that services auto-start on boot via `systemctl enable nginx`.
  2. **Update ASG Launch Template**:
     - Create a new version of the ASG Launch Template referencing the newly created Golden AMI.
     - Strip the heavy installation steps from User Data, leaving only environment variable exports or secret fetches.
  3. **Result**: Newly launched scale-out instances boot up and pass load balancer health checks in under 45 seconds, absorbing traffic surges before existing nodes saturate.

---

### Scenario 6: Consolidating Multiple Web Services Behind a Single Load Balancer
- **Scenario**: Your engineering organization runs two distinct applications: a marketing website served by static NGINX servers, and a student portal application served by Node.js app servers. To save costs and simplify domain management, management wants both services accessible under `portal.example.com` (`/` routes to NGINX, `/courses/*` routes to Node.js) using a single load balancer. How do you design this?
- **Answer**:
  1. **Deploy an Application Load Balancer (ALB)** (Layer 7).
  2. **Create Two Target Groups**:
     - `TG-Nginx-Web`: Registers the static NGINX EC2 instances (Health check path: `/index.html`).
     - `TG-Node-App`: Registers the Node.js application EC2 instances (Health check path: `/courses/healthz`).
  3. **Configure ALB Listener Rules**:
     - **Rule 1 (Path-Based Routing)**: IF `Path is /courses*` -> THEN `Forward to TG-Node-App`.
     - **Default Rule**: IF no other rules match (`/*`) -> THEN `Forward to TG-Nginx-Web`.
  4. Both application fleets run securely in private subnets, while the ALB handles SSL termination and URL path routing through a single public DNS endpoint.

---

### Scenario 7: Blocking a Malicious Brute-Force Attacker on a Public Web Server
- **Scenario**: Your production web server has a Security Group allowing inbound HTTP (Port 80) and HTTPS (Port 443) from `0.0.0.0/0`. Monitoring logs reveal a single IP address (`198.51.100.25`) sending 5,000 automated brute-force requests per minute. You need to immediately block this specific IP address while keeping the web service available to all other legitimate global users. How do you achieve this?
- **Answer**:
  1. **Limitation of Security Groups**: Security Groups only support **ALLOW** rules with implicit deny. You cannot create a rule to deny a specific IP address in a Security Group.
  2. **Resolution via Network ACL (NACL)**:
     - Identify the subnet hosting the EC2 instance and open its attached **Network ACL**.
     - Add an inbound rule with a low rule number (evaluated first):
       - **Rule Number**: `50`
       - **Type**: `All Traffic` (or `HTTP/HTTPS`)
       - **Source**: `198.51.100.25/32`
       - **Action**: **DENY**
     - Ensure the default catch-all rule (`Rule 100`: `ALLOW 0.0.0.0/0`) remains in place below it.
  3. The subnet router drops all packets originating from `198.51.100.25` at the network boundary before they ever reach the EC2 instance.

---

### Scenario 8: Cross-Zone EBS Database Recovery Following a Datacenter Failure
- **Scenario**: A standalone MySQL database runs on an EC2 instance in Availability Zone `ap-south-1a` with an attached EBS data volume. A localized transformer fire shuts down datacenter `ap-south-1a`. Management directs you to bring the database back online in `ap-south-1b` immediately using the latest hourly snapshot. Why can't you directly attach the EBS volume to an instance in `ap-south-1b`, and what are the exact recovery steps?
- **Answer**:
  1. **EBS AZ Boundary Constraint**: EBS volumes are physically bound to the specific Availability Zone in which they are provisioned. A volume created in `ap-south-1a` cannot attach directly to an EC2 instance in `ap-south-1b`.
  2. **Recovery Steps via Snapshot**:
     - Open the EC2 Console > **Snapshots**.
     - Locate the latest automated snapshot of the database volume.
     - Click **Actions** > **Create volume from snapshot**.
     - In the configuration modal, set the **Availability Zone** to `ap-south-1b`.
     - Click **Create volume**.
     - Launch a replacement EC2 instance in `ap-south-1b` (or select an existing standby instance).
     - Attach the newly restored volume as a block device (e.g., `/dev/xvdf`) and mount it to restore database operations.

---

### Scenario 9: Diagnosing "Connection Timed Out" on a Running NGINX Web Server
- **Scenario**: A student launches an Amazon Linux 2023 EC2 instance, SSHes in, installs NGINX, and verifies it is running using `systemctl status nginx`. Running `curl http://localhost` from inside the terminal returns the NGINX welcome page. However, entering `http://<EC2_Public_IP>` in a laptop web browser results in `ERR_CONNECTION_TIMED_OUT`. How do you systematically diagnose this?
- **Answer**:
  1. **Inspect Security Group Inbound Rules**:
     - Check the instance's Security Group. Confirm there is an explicit inbound rule for **HTTP (Port 80)** with source `0.0.0.0/0`. If only SSH (Port 22) is open, web traffic will time out.
  2. **Inspect Subnet Route Table**:
     - Navigate to the subnet's Route Table. Ensure there is an active route for `0.0.0.0/0` pointing to an **Internet Gateway (IGW)** (`igw-xxxx`). Without an IGW, public internet packets cannot route into the subnet.
  3. **Verify Browser Protocol**:
     - Ensure the browser is requesting `http://` (Port 80) and not auto-redirecting to `https://` (Port 443), unless SSL certificates and Port 443 are configured.
  4. **Check Network ACLs (NACL)**:
     - Verify that the subnet NACL allows inbound traffic on port 80 and outbound traffic on ephemeral ports (`1024–65535`).

---

### Scenario 10: Preventing Auto Scaling Flapping Caused by Momentary Metric Spikes
- **Scenario**: An Auto Scaling Group is configured to add 2 EC2 instances whenever CPU utilization exceeds 70%. During peak operational hours, periodic background cron jobs create sudden 10-second CPU spikes to 85%, triggering ASG to scale out. Five minutes later, CPU drops back to 30%, triggering scale-in. This cycle repeats hourly, causing unnecessary instance turnover and billing waste. How do you stabilize this?
- **Answer**:
  1. **Implement CloudWatch Metric Averaging**:
     - Configure the CloudWatch Alarm to evaluate the **Average** CPU utilization over a **5-minute evaluation period** (or require 3 consecutive evaluation periods of 1 minute) before triggering a scaling alarm, rather than reacting to a single instantaneous spike.
  2. **Configure ASG Cooldown Periods**:
     - Set a **Default Cooldown Period** (e.g., 300 seconds). After a scaling action completes, the cooldown prevents the ASG from launching or terminating additional instances until metrics have stabilized.
  3. **Utilize Target Tracking Scaling**:
     - Replace simple step scaling policies with **Target Tracking Scaling Policies** (e.g., maintain average CPU at 60%). Target tracking uses proportional control algorithms to smooth out capacity changes and avoid flapping.
