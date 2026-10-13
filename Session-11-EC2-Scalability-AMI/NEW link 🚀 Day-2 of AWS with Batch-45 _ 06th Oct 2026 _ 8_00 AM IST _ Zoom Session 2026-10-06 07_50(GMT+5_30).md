## Key Outcomes

Day 2 of the AWS training covered foundational cloud concepts (elasticity, scaling, high availability, CDN/edge locations) and moved into hands-on EC2 practice. Students created both Linux and Windows virtual machines, connected to them via browser console and RDP respectively, and learned instance lifecycle management including termination behavior. The session established Mumbai as the primary production region for learning purposes and clarified key differences between POC accounts and standard AWS accounts.

---

## Decisions Made

- **Mumbai selected as the primary AWS region** for all class practicals; region selection should be based on latency ping results to data centers, not the instructor's physical location 
- **Root account to be used** for POC accounts since IAM user creation is restricted in the new POC account type 
- **Amazon Linux 2023** selected as the default OS for Linux VMs; **Windows Server 2025 Base** selected for Windows VMs (Data Center Edition used in real enterprise environments) 
- **GP3 SSD** confirmed as the standard EBS volume type used in real-time production environments 
- **Key pair (PEM format, RSA algorithm)** required when connecting to Windows VMs via external RDP tools; not needed when using the AWS browser console 
- Every VM must be **terminated after each practice session** to avoid unnecessary billing 

---

## Core Concepts Covered

### Elasticity

- **Elasticity** = ability to increase or decrease compute resources; all AWS services starting with "E" (EC2, EBS, ECR, ECS, EFS, EKS) are elastic services 
- Elasticity is a **concept**; auto-scaling is the **feature** that implements it 

### Scaling Types

- **Vertical scaling**: same machine gets a larger size (e.g., upgrading a laptop's RAM); scale-down requires restart, cannot be done automatically 
- **Horizontal scaling**: additional separate resources added to handle load; auto scale-down is possible by terminating excess instances 
- Horizontal scaling uses **identical-sized replicas** created via AMI + Launch Templates 

### Auto-Scaling

- Resources automatically increase or decrease based on configurable conditions: **CPU, memory, or traffic** 
- Real-world example: Flipkart running 100 servers normally scales up by 10% when traffic increases by 10% 
- Configuration options include minimum, desired, and maximum instance counts with trigger thresholds 

### High Availability (HA)

- Running the same application stack across **two different regions** (e.g., Mumbai + UAE) so that if one data center goes down, the other serves traffic 
- Cost is **double** since full hardware resources are duplicated 
- **Database sync layer required** between HA zones; web/middleware servers do not need sync 
- HA is part of horizontal scaling conceptually — more data centers added 

### Disaster Recovery (DR)

- DR is **separate from HA**: kept in a third zone, **passive** (not actively serving traffic) 
- DR is activated only when both primary and HA regions go down 
- **Active-Active**: both instances serving traffic simultaneously; **Active-Passive**: one serves traffic, one is on standby 

### CDN / Edge Locations

- **CDN (Content Delivery Network)** = delivers content from edge locations near the end user rather than the origin data center 
- AWS CDN service is **CloudFront**; 900+ edge locations globally — no need to memorize count 
- Analogy: like Blinkit's dark stores near your neighborhood delivering goods in 10 minutes instead of from a central warehouse 
- CDN is for **front-end content delivery** (caching/distribution), not load balancing 
- Edge locations ≠ full data centers; they serve cached content to reduce latency 

---

## AWS Account Types: POC vs. Standard

- New AWS accounts are now **POC (Proof of Concept) accounts** with limited access 
- POC accounts: DigiLocker verification mandatory, 200 free credits provided, regions limited (only one region visible/usable) 
- POC account has a **[settings.aws.amazon.com](https://settings.aws.amazon.com)** portal (new) with Projects, Teams, Billing, Support tabs; IAM users created via Teams section 
- **Do not mention POC account in interviews** — state that you worked on a paid/production account 
- Standard console access: **[console.aws.amazon.com/console](https://console.aws.amazon.com/console)** 
- Students on POC accounts should use the root account for all practicals 

---

## AMI (Amazon Machine Image)

- AMI = **customized operating system image** provided by Amazon, optimized for AWS hardware 
- Includes pre-installed software for: faster boot times, monitoring agents, Active Directory integration, application optimization 
- Each AMI has a **unique AMI ID**; ID changes if the region changes 
- AMI ID changes per region — same image in Mumbai vs. UAE will have different IDs 
- **Always select verified AMIs** (green tick) in production and interviews; avoid unverified community AMIs 
- 5,163+ AMIs available in the marketplace including third-party and community images 
- Custom AMIs can be created and shared (used as "golden images" for replication) 
- AMI is the basis for horizontal scaling replicas — template contains the AMI, which is used to spin up identical instances 

---

## EC2 Instance Creation Walkthrough

### Linux VM (Amazon Linux 2023)

Key configuration steps covered: 

- **Name**: descriptive label for the instance
- **AMI**: Amazon Linux 2023 (free tier eligible, kernel version 6)
- **Instance type**: t3.micro (2 vCPU, 1 GB RAM) — free tier
- **Key pair**: skipped for browser-based console connection
- **Network**: left as default (networking covered in a separate dedicated class)
- **Storage (EBS)**: 30 GB root volume (C drive equivalent); additional EBS volumes can be added as D drive equivalent via "Add New Volume" 
- **Traffic**: HTTP and HTTPS allowed

### Windows VM (Windows Server 2025 Base)

- Select **Windows Server 2025 Base** AMI (Data Center Edition used in real enterprise) 
- Instance type t3.micro has only 1 GB RAM — slow for Windows but free tier; 2 GB RAM minimum recommended 
- **Key pair required** (PEM format, RSA algorithm) to decrypt the administrator password for RDP login 
- Minimum **30 GB storage** for Windows 

---

## Connecting to Instances

### Linux VM — Browser Console

1. Select instance → click **Connect**
2. Choose **EC2 Instance Connect** option
3. Click **Connect** — lands directly in terminal 
- Total clicks: ~4 from VM creation to connected terminal 

### Windows VM — RDP (6-Step Process)

1. Install/open RDP client (pre-installed on Windows; Mac users download **Windows App** from Microsoft's official website) 
2. Download the **RDP shortcut file** from the Connect tab 
3. Open RDP client and run the shortcut file 
4. **Retrieve password**: upload the downloaded PEM key file → click **Decrypt** to generate the password 
5. Enter **username** (`Administrator` by default) and the decrypted password 
6. Accept the certificate prompt (first-time connection) → logged in 

**Important notes:**

- Windows Home Edition **does not support RDP** — Windows Professional required 
- Public IP = External IP = same thing; used as the hostname in RDP 
- If public IP is not generated, check **Network Settings → Auto-assign Public IP** is enabled 
- Mac users: install **Windows App** from Microsoft's official site (not App Store) 

---

## Instance Lifecycle & Termination Behavior

- **Terminate = Delete** in AWS terminology — both are identical 
- After termination, instance remains **visible for 10–15 minutes intentionally** — AWS keeps it so accidental deletions can be identified and configuration info retrieved 
- Information retrievable post-termination: instance name, CPU/memory specs, key pair used, network configuration 
- **VM itself cannot be recovered** after termination — only metadata/configuration info is accessible 
- Data can be recovered if **snapshots/backups** were taken before termination 
- Resources are fully released after the grace period — no billing continues 
- Reboot option available from right-click menu on instance without needing to log in 

---

## EBS (Elastic Block Storage)

- EBS = **virtual external hard drive** attached to an EC2 instance 
- Root volume (C drive) = minimum 8 GB, class uses 30 GB 
- Additional EBS volumes added as separate drives (D drive equivalent) via "Add New Volume" 
- Volume types: **GP3 SSD** is the standard in real-time use; magnetic (HDD) types are being deprecated 
- In Linux terminology, attaching EBS = **mounting** a volume to a mount point (directory) 
- EBS described as a "virtual raw hard drive" — accurate technical characterization 

---

## Region Selection Logic

- Use **latency ping tool** (link shared in Zoom chat) to ping all AWS data centers and select the one with lowest response time 
- Share the ping link with clients and have them test from their location over 24 hours; use the average result 
- **Never select a region purely based on your own location** — base it on where end users are 
- Avoid placing HA/DR in the same country to protect against country-level outages (e.g., AWS India contract termination scenario) 
- CDN edge locations handle geographic distribution; full data centers only needed where primary user base is concentrated 

---

## Real-World Windows Admin Context (Gangadhar's Experience)

Gangadhar (10 years Windows Admin) shared how Windows Server is used in production: 

- **Splunk** installed on Windows Server for log monitoring and alerting
- **Windows patching** managed via **BigFix** (third-party patching tool) — not built-in Windows Update
- Patching workflow: identify unpatched servers → raise change management ticket → coordinate with application team for downtime window → inject KB articles → reboot multiple times → confirm services are up 
- **Disk space management**: C drive space monitored and cleaned before patching
- Database servers: DB team informed to bring down and restart instances during maintenance 
- AWS **CloudWatch** handles automatic monitoring of Windows servers in the cloud 
- VDI environments use the same RDP/remote connectivity principles; only the client software differs (e.g., Citrix vs. standard RDP) 

---

## IAM, Roles & Policies (Brief Coverage)

- **Policy**: set of permissions applicable broadly (e.g., all users can start/stop EC2 but not delete) 
- **Role**: permissions scoped to a department or function (e.g., IT team vs. marketing team gets different access) 
- Analogy: company ID card = policy (everyone gets one); department access card = role (specific to your team) 
- Best practice: use **IAM users** rather than root for daily work; root acceptable in POC accounts where IAM is restricted 

---

## Pending Confirmation

- Students with **account verification issues** (KYC/DigiLocker not completing): raise a support ticket with AWS if issue persists beyond 2 days 
- Students on POC accounts **not seeing region selector**: expected behavior — limited to one assigned region, no impact on VM creation practicals 
- Public IP not appearing on instances: verify **Auto-assign Public IP** is enabled in network settings during instance creation 

---

## Action Items

- **All students**: Create and terminate at least one Linux and one Windows EC2 instance daily for practice 
- **All students**: Connect to Linux VM via EC2 Instance Connect and to Windows VM via RDP using the 6-step process 
- **Patel**: Write a LinkedIn article with step-by-step screenshots for connecting to Windows VM from Mac using the Windows App 
- **Students with verification issues**: Raise AWS support ticket if account access not resolved within 2 days 
- **New students / those missing Day 1**: Watch the YouTube playlist (Batch 45) for Linux and foundational content before next class 
- **All students**: Review recordings for any missed content; recording link available in the same Zoom join link after ~10–15 minutes post-session 

---

## Upcoming Session (Day 3)

- **Linux configuration** deep dive
- Create **Apache / Nginx web server** on EC2
- Connect using **MobaXterm** or other third-party SSH tools
- **Snapshots and backups** of EBS volumes
- Networking fundamentals scheduled for **Day 7**

- # AWS Day-2 — 20 Basic to Advanced Q&A + 20 Scenario-Based Q&A

Based on the AWS Day-2 training notes covering elasticity, scaling, HA, DR, CDN, AWS account types, AMI, EC2, EBS, region selection, Windows/Linux connectivity, IAM, and instance lifecycle.

---

# Part 1: Basic to Advanced Q&A

## 1. What is Elasticity in AWS?

**Answer:**  
Elasticity is the ability to increase or decrease compute resources according to the workload. For example, when traffic increases, more resources can be added, and when traffic decreases, resources can be reduced.

Elasticity is a **concept**, while Auto Scaling is a **feature/mechanism** used to implement elasticity.

---

## 2. What is the difference between Vertical Scaling and Horizontal Scaling?

**Answer:**  
**Vertical scaling** means increasing the capacity of the same machine. For example, changing an EC2 instance to a larger instance with more CPU or RAM.

**Horizontal scaling** means adding more separate machines/instances to handle additional load.

A simple example:

- Vertical: 1 EC2 → larger EC2
- Horizontal: 1 EC2 → 3 identical EC2 instances

---

## 3. What is Auto Scaling?

**Answer:**  
Auto Scaling automatically increases or decreases resources based on configured conditions such as CPU utilization, memory usage, or traffic.

It normally uses minimum, desired, and maximum instance counts along with trigger thresholds.

---

## 4. Why is horizontal scaling commonly used for application replicas?

**Answer:**  
Horizontal scaling allows additional instances to be added when demand increases and excess instances to be removed when demand decreases.

Identical instances can be created using an **AMI and Launch Template**, which helps maintain a consistent application environment.

---

## 5. What is High Availability (HA)?

**Answer:**  
High Availability means designing an application so that it can continue serving users even if one infrastructure location becomes unavailable.

The training example uses application stacks across two different regions, such as Mumbai and UAE, so another region can serve traffic if the primary region goes down.

---

## 6. What is the difference between Active-Active and Active-Passive?

**Answer:**  

**Active-Active:** Both environments are running and serving traffic at the same time.

**Active-Passive:** One environment actively serves traffic while the other remains on standby and can be activated when required.

---

## 7. How is Disaster Recovery different from High Availability?

**Answer:**  
HA and DR solve different availability problems.

**HA** keeps another environment available to continue serving traffic when a primary environment fails.

**DR** is a separate recovery environment, described in the training as a passive third zone. It is activated when both the primary and HA environments are unavailable.

---

## 8. Why is database synchronization important in an HA architecture?

**Answer:**  
If an application is running across multiple locations, the database data must remain synchronized so that users do not receive inconsistent information.

The training specifically highlights the need for a **database sync layer** between HA zones. Web and middleware servers do not require the same type of data synchronization.

---

## 9. What is a CDN and why is it used?

**Answer:**  
A **Content Delivery Network (CDN)** delivers content from edge locations closer to end users instead of always serving it from the origin data center.

AWS provides CDN functionality through **CloudFront**.

The main benefit discussed in the training is reduced latency for front-end content delivery.

---

## 10. What is the difference between an Edge Location and a Data Center?

**Answer:**  
An edge location is used mainly to cache and distribute content closer to users.

It is not the same as a complete data center running the full application infrastructure.

For example, CloudFront can use edge locations to deliver cached front-end content quickly to users.

---

## 11. What is an AMI in AWS?

**Answer:**  
AMI stands for **Amazon Machine Image**.

It is a customized operating system image used as a template for launching EC2 instances. It can contain the operating system and pre-installed software or configuration.

AMI is also important for creating identical EC2 replicas during horizontal scaling.

---

## 12. Why can the AMI ID be different in different AWS regions?

**Answer:**  
AMI IDs are region-specific.

For example, an AMI available in Mumbai can have a different AMI ID from the corresponding image in UAE.

Therefore, when creating infrastructure in another region, you should verify the AMI available in that region instead of assuming the same AMI ID will work.

---

## 13. What is a Golden Image?

**Answer:**  
A Golden Image is a standardized, preconfigured image that can be used to create consistent servers.

In AWS, a custom AMI can act as a Golden Image. It can be shared and reused to create identical EC2 instances.

This is useful for horizontal scaling and standardizing production environments.

---

## 14. What is EC2?

**Answer:**  
Amazon EC2 provides virtual machines/compute instances in AWS.

In the training, students created both:

- Amazon Linux 2023 EC2 instances
- Windows Server 2025 Base EC2 instances

These instances can be configured with a selected AMI, instance type, storage, networking, and connectivity options.

---

## 15. What is EBS?

**Answer:**  
EBS stands for **Elastic Block Store**.

It provides persistent block storage that can be attached to EC2 instances.

A simple way to understand it is as a **virtual external hard drive** for an EC2 instance.

The training uses GP3 SSD as the standard EBS volume type for real-time production examples.

---

## 16. What is the difference between an EC2 instance and an EBS volume?

**Answer:**  
An **EC2 instance** provides compute resources such as CPU and memory.

An **EBS volume** provides block storage.

For example:

- EC2 = the virtual computer
- EBS = the virtual disk attached to that computer

The root EBS volume can be treated like the main system drive, while additional EBS volumes can be attached as separate drives.

---

## 17. How do you connect to a Linux EC2 instance using the browser console?

**Answer:**  
The training process is:

1. Select the EC2 instance.
2. Click **Connect**.
3. Choose **EC2 Instance Connect**.
4. Click **Connect**.

This opens a terminal directly in the browser.

For this browser-based method, the training notes specify that a key pair can be skipped.

---

## 18. Why is a key pair required for Windows EC2 RDP access?

**Answer:**  
For the Windows RDP process covered in the training, the PEM key is used to decrypt the Windows Administrator password.

The basic flow is:

**PEM key → Decrypt password → Administrator credentials → RDP login**

The key pair is not required when using the browser-based Linux EC2 Instance Connect method described in the training.

---

## 19. What happens when an EC2 instance is terminated?

**Answer:**  
In AWS terminology, **Terminate means Delete** for the EC2 instance.

After termination, the instance may remain visible for around 10–15 minutes so that configuration information can still be identified.

The actual VM cannot be recovered after termination. However, data may be recoverable if snapshots or backups were created before termination.

---

## 20. How should you select an AWS region for a production workload?

**Answer:**  
The region should not be selected only because it is geographically close to the engineer.

The training recommends using latency/ping results from the actual user/client location. Ideally, clients can test AWS data centers over a period such as 24 hours and use average latency results.

The region should therefore be selected based on factors such as:

- User location
- Network latency
- Application requirements
- HA/DR requirements
- Geographic outage risk

---

# Part 2: 20 Scenario-Based Q&A

## Scenario 1: Traffic suddenly increases

**Question:**  
Your application normally handles traffic using 10 EC2 instances, but traffic suddenly increases significantly. What AWS concept would you use?

**Answer:**  
Use **horizontal scaling with Auto Scaling**. Configure minimum, desired, and maximum instance counts and define suitable triggers such as CPU, memory, or traffic.

New identical instances can be launched using an AMI and Launch Template.

---

## Scenario 2: One EC2 needs more CPU and RAM

**Question:**  
Your application is running on one EC2 instance and the workload has increased. Instead of adding more servers, you want to make the existing machine larger. What type of scaling is this?

**Answer:**  
This is **vertical scaling**.

You increase the capacity of the same machine by moving it to a larger instance size.

The training notes also mention that scale-down through vertical scaling requires a restart and cannot be done automatically in the same way as horizontal scaling.

---

## Scenario 3: One region goes down

**Question:**  
Your application is running in Mumbai, but the Mumbai data center becomes unavailable. You already have the same application stack running in UAE. What architecture concept does this represent?

**Answer:**  
This represents **High Availability across regions**.

The second region can serve users when the primary region becomes unavailable.

The architecture has higher cost because resources are duplicated.

---

## Scenario 4: Both primary and HA environments fail

**Question:**  
Your primary environment and HA environment are both unavailable. You have a third environment that is normally passive. What is this environment used for?

**Answer:**  
It is the **Disaster Recovery (DR)** environment.

The training describes DR as a separate passive zone that is activated when both the primary and HA environments go down.

---

## Scenario 5: Users from another location experience high latency

**Question:**  
Your application is hosted in Mumbai, but most users are located in another geographic region and are experiencing high latency. What should you investigate first?

**Answer:**  
Check network latency between the users and AWS regions using a latency/ping tool.

Region selection should be based on the actual user location and average latency rather than only the engineer's physical location.

For front-end content, a CDN such as CloudFront can also help by serving cached content from nearby edge locations.

---

## Scenario 6: Static website content is slow

**Question:**  
Your web application has static front-end content that takes a long time to load for users in different geographic locations. Would you use EC2 Auto Scaling or a CDN?

**Answer:**  
For front-end content delivery, use a **CDN such as CloudFront**.

A CDN caches/distributes content through edge locations closer to users, reducing latency.

Auto Scaling is a compute scaling mechanism and is not a replacement for CDN content delivery.

---

## Scenario 7: Need identical EC2 servers

**Question:**  
Your company needs 20 EC2 instances with exactly the same operating system, software, and configuration. How can you approach this?

**Answer:**  
Create a customized **AMI** containing the required configuration and use it as the template for launching identical EC2 instances.

This approach can also be combined with a Launch Template for horizontal scaling.

---

## Scenario 8: Same AMI ID doesn't work in another region

**Question:**  
You launched an EC2 instance using an AMI in Mumbai. You move your deployment to UAE and try to use the same AMI ID, but it is not available. Why?

**Answer:**  
AMI IDs are **region-specific**.

You need to select or copy/use the appropriate AMI available in the target region.

The same operating system image can have different AMI IDs in different AWS regions.

---

## Scenario 9: Linux EC2 connection without downloading a key

**Question:**  
A student needs to connect to an Amazon Linux EC2 instance quickly from a browser and does not want to configure an external SSH client. What method was demonstrated?

**Answer:**  
Use **EC2 Instance Connect** from the EC2 Connect option.

The training process is:

**Instance → Connect → EC2 Instance Connect → Connect**

---

## Scenario 10: Windows RDP password cannot be retrieved

**Question:**  
You created a Windows EC2 instance and want to connect using RDP, but you cannot decrypt the Administrator password. What should you check?

**Answer:**  
Check that you have the correct **PEM key file** associated with the instance.

The Windows connection process uses the PEM key to decrypt the Administrator password before RDP login.

---

## Scenario 11: Windows EC2 has no public IP

**Question:**  
You created a Windows EC2 instance but no public IP is visible, so RDP cannot connect using the public address. What should you check?

**Answer:**  
Check the instance's network settings and verify that **Auto-assign Public IP** is enabled during instance creation.

The public IP is used as the hostname/address for the RDP connection described in the training.

---

## Scenario 12: Mac user needs to connect to Windows EC2

**Question:**  
A student is using a Mac and needs to connect to a Windows EC2 instance through RDP. What client was recommended in the training?

**Answer:**  
Use **Windows App** from Microsoft's official website.

The user can download the RDP shortcut file from the AWS Connect section and open it through the RDP client.

---

## Scenario 13: EC2 instance was terminated accidentally

**Question:**  
An EC2 instance was terminated accidentally. Can you simply start the same VM again?

**Answer:**  
No. **Terminate means delete**, and the VM itself cannot be recovered after termination.

However, some configuration metadata may remain visible temporarily, and data can potentially be recovered if snapshots or backups were created before termination.

---

## Scenario 14: Student keeps EC2 instances running after practice

**Question:**  
Students finish their daily EC2 practice but leave all instances running. What operational problem can this create?

**Answer:**  
Running resources can result in unnecessary AWS charges.

The training action item is to **terminate the Linux and Windows practice instances after each session** to avoid unnecessary billing.

---

## Scenario 15: Need a second disk on EC2

**Question:**  
A Windows EC2 server has a root disk and the application requires an additional disk similar to a D: drive. What AWS resource should you use?

**Answer:**  
Create and attach an additional **EBS volume**.

The training describes the root volume as similar to the C: drive and additional EBS volumes as separate drives such as a D: drive.

---

## Scenario 16: Linux server needs an additional storage volume

**Question:**  
You attach an EBS volume to a Linux EC2 instance. What Linux concept is used to make the storage available through a directory?

**Answer:**  
The EBS volume needs to be **mounted** to a mount point/directory.

In Linux terminology, attaching an EBS volume and mounting it are related but distinct steps.

---

## Scenario 17: Production team wants a standardized image

**Question:**  
A company wants every production EC2 server to start with the same OS and pre-installed monitoring/application software. What would you recommend?

**Answer:**  
Create a **custom AMI / Golden Image** containing the required operating system and software configuration.

Use that image when launching new instances so that production servers remain consistent.

---

## Scenario 18: Choosing a region only because the engineer is in Mumbai

**Question:**  
An engineer in Mumbai says, "We are in Mumbai, so production should automatically be hosted in Mumbai." Is that a good region-selection approach?

**Answer:**  
Not by itself.

The training recommends selecting a region based on **user/client location and latency**, not simply the engineer's physical location.

If most customers are elsewhere, another AWS region may provide better latency.

---

## Scenario 19: HA and DR are placed in the same country

**Question:**  
A company keeps its primary, HA, and DR environments in the same country. What risk should the architecture team consider?

**Answer:**  
A country-level outage or major regional event could affect multiple environments at the same time.

The training recommends avoiding placing HA/DR in the same country when possible to provide protection against country-level outages.

---

## Scenario 20: EC2 works but users still experience slow front-end delivery

**Question:**  
Your EC2 infrastructure is healthy and Auto Scaling is working, but users in distant geographic locations still experience slow delivery of front-end content. What component should you consider?

**Answer:**  
Consider using **CloudFront/CDN**.

Auto Scaling handles compute capacity, while a CDN improves front-end content delivery by caching and distributing content through edge locations closer to users.

---

# Quick Interview Revision

| Topic | Key Point |
|---|---|
| Elasticity | Increase/decrease resources based on demand |
| Auto Scaling | Feature used to implement automatic scaling |
| Vertical Scaling | Make the same machine bigger |
| Horizontal Scaling | Add more machines |
| HA | Keep application available through another environment |
| DR | Separate recovery environment, generally passive |
| Active-Active | Both environments serve traffic |
| Active-Passive | One active, one standby |
| CDN | Faster front-end content delivery |
| CloudFront | AWS CDN service |
| Edge Location | Location for cached/distributed content |
| AMI | Image/template for EC2 |
| Golden Image | Standardized reusable custom AMI |
| EC2 | AWS virtual compute instance |
| EBS | Persistent block storage for EC2 |
| GP3 | Standard SSD volume used in the training |
| EC2 Instance Connect | Browser-based Linux connection |
| RDP | Remote connection method for Windows |
| Key Pair | Used in Windows process to decrypt Administrator password |
| Terminate | Delete the EC2 instance |
| Region Selection | Based on users, latency, and architecture requirements |
| IAM | Controls permissions and access |

---

# Important Interview Note

The training notes specifically instruct students on POC accounts **not to describe the account as a POC account during interviews** and instead describe their experience in terms of paid/production AWS account usage.

For technical interviews, focus on explaining the architecture, services, configuration, troubleshooting approach, and practical work you actually performed.

