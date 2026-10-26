## Key Outcomes

The session covered AWS foundational concepts including service history, global infrastructure, and core service categories, followed by hands-on EC2 instance creation, NGINX web server deployment, and classic load balancer (ELB) configuration. Students connected to Linux instances via MobaXterm, deployed NGINX on two separate servers, and successfully demonstrated round-robin load distribution. A job opportunity was also shared with the batch at the end of the session.

---

## Decisions Made

- **Top 10–15 core AWS services** are sufficient for real-time project work; learning all 200+ services is neither required nor practical. 
- **Classic Load Balancer** was chosen for the practical demonstration to introduce ELB concepts before progressing to Application and Network load balancers in future sessions. 
- **30 GB EBS storage** recommended over 8 GB for learning instances to accommodate future Docker and Jenkins installations. 
- **RSA key algorithm** preferred over ED25519 for key pair generation due to wider popularity and compatibility. 
- **PPK key format** used with MobaXterm for Linux server connectivity; PEM used for Windows server scenarios. 
- **MobaXterm** recommended over PuTTY for session management — supports session saving, grouping, and multi-server connectivity. 
- Static website hosting on **S3** confirmed as a valid use case; a portfolio/static site practical planned for the next class. 

---

## AWS History & Service Overview

### Timeline of Key AWS Milestones

- **2002:** AWS console launched ([console.aws.amazon.com](https://console.aws.amazon.com)). 
- **2004:** First AWS service — **SQS (Simple Queue Service)** — introduced for queue-based processing (FIFO/LIFO). 
- **2006:** **EC2 (Elastic Compute Cloud)** launched, enabling virtual machine access over the internet via virtualization. 
- **2009:** **VPC (Virtual Private Cloud)** introduced after three years of development, addressing security concerns about isolated network environments. 
- **2012:** Notable customer events milestone.
- **2016:** AWS reached **$10 billion revenue**, establishing itself as the leading cloud provider. 
- **2017+:** AWS reached **200+ services**. 

### Why 200+ Services Exist

- Each service was built to solve a specific real-world problem or fulfill a small, distinct requirement. 
- Services mirror and replace on-premises components: physical storage (HDD/SSD) → EBS/EFS/S3; server rooms → cloud compute; security guards → IAM; physical networking switches → VPC. 
- Evolution analogy used: floppy → CD → DVD → pen drive → cloud storage mirrors DST/SAN/NAS → EBS/S3. 

---

## Core AWS Services — Quick Reference

|   Category    |    Service     |                        Purpose                        |
|---------------|----------------|-------------------------------------------------------|
| Compute       | EC2            | Rented virtual machines                               |
| Storage       | S3             | Infinite object storage (logs, backups, static sites) |
| Storage       | EBS            | High-performance block storage (OS, databases)        |
| Storage       | EFS            | Shared file storage                                   |
| Database      | RDS            | Managed relational SQL database                       |
| Database      | DynamoDB       | NoSQL key-value store for unstructured data           |
| Networking    | VPC            | Private isolated network                              |
| Identity      | IAM            | User/role/policy management                           |
| Serverless    | Lambda         | Run code without managing infrastructure              |
| API           | API Gateway    | Front door for all APIs                               |
| CDN           | CloudFront     | Content delivery network via edge locations           |
| Messaging     | SQS            | Queue service for distributed processing              |
| Notifications | SNS            | Pub/sub notification (email, SMS, Slack, Telegram)    |
| Monitoring    | CloudWatch     | Metrics, logs, and alerting in one place              |
| Auditing      | CloudTrail     | Tracks who did what in the AWS account                |
| IaC           | CloudFormation | AWS infrastructure-as-code (equivalent to Terraform)  |
| Containers    | EKS            | Managed Kubernetes service                            |


# AWS Day-3 – 20 Interview Questions and Answers + 20 Basic Scenarios
## Batch-45 | 7 October 2026

**Source:** Day-3 AWS training session notes. Topics include AWS history and core services, EC2, EBS, SSH connectivity, NGINX, Elastic Load Balancing, and S3 static website hosting.

---

# Part 1: 20 Interview Questions and Answers

### 1. What is AWS?
**Answer:** Amazon Web Services (AWS) is a cloud platform that provides services such as compute, storage, databases, networking, identity management, monitoring, and messaging. Teams can use these services without building and maintaining all the physical infrastructure themselves.

### 2. What is Amazon EC2?
**Answer:** Amazon Elastic Compute Cloud (EC2) provides virtual servers in the cloud. You can choose an instance type, configure storage and networking, and install software such as NGINX or Jenkins.

### 3. What is Amazon S3 used for?
**Answer:** Amazon Simple Storage Service (S3) is object storage. Common uses include storing backups, logs, files, and static website assets such as HTML, CSS, JavaScript, and images.

### 4. What is the difference between S3 and EBS?
**Answer:** S3 stores objects in buckets and is commonly used for files, backups, logs, and static content. EBS provides block storage volumes that can be attached to EC2 instances, for example as an operating-system or application data disk.

### 5. What is Amazon EFS?
**Answer:** Amazon Elastic File System (EFS) is a managed file storage service that can be shared by multiple supported compute instances. It is useful when multiple servers need access to the same file system.

### 6. What is Amazon VPC?
**Answer:** Amazon Virtual Private Cloud (VPC) provides a logically isolated network for AWS resources. Within a VPC, you configure networking components such as subnets, route tables, and security controls.

### 7. What is IAM used for in AWS?
**Answer:** Identity and Access Management (IAM) manages identities and permissions. It helps control who or what can access AWS services and which actions they can perform.

### 8. What is Amazon RDS?
**Answer:** Amazon Relational Database Service (RDS) is a managed service for relational databases. It helps reduce the operational work of managing database infrastructure.

### 9. What is Amazon DynamoDB?
**Answer:** DynamoDB is a managed NoSQL database service. It is commonly used for key-value and document data models rather than traditional relational SQL schemas.

### 10. What is AWS Lambda?
**Answer:** AWS Lambda runs code in response to configured events without requiring you to manage the underlying servers directly. It is commonly used for event-driven and serverless workloads.

### 11. What is Amazon API Gateway?
**Answer:** API Gateway is a managed service for creating, publishing, securing, monitoring, and managing APIs. It can act as an entry point for applications that call backend services or Lambda functions.

### 12. What is Amazon CloudFront?
**Answer:** CloudFront is a content delivery network (CDN) that delivers content through a global network of edge locations. It can reduce latency for users accessing static or cacheable content.

### 13. What is the difference between Amazon SQS and Amazon SNS?
**Answer:** SQS is a message queue that helps decouple application components by allowing messages to be processed asynchronously. SNS is a publish/subscribe notification service that distributes messages to subscribed endpoints or services.

### 14. What is the difference between Amazon CloudWatch and AWS CloudTrail?
**Answer:** CloudWatch is used for monitoring metrics, logs, alarms, and operational health. CloudTrail records AWS account activity and API events, helping answer who performed an action and when.

### 15. What is AWS CloudFormation?
**Answer:** CloudFormation is an infrastructure-as-code service for defining and managing AWS resources through templates. It serves a similar broad purpose to Terraform, although the tools have different workflows and provider support.

### 16. What is Amazon EKS?
**Answer:** Amazon Elastic Kubernetes Service (EKS) is a managed Kubernetes service used to run Kubernetes workloads on AWS.

### 17. What is Elastic Load Balancing?
**Answer:** Elastic Load Balancing distributes incoming traffic across configured targets, such as EC2 instances. It helps spread requests across servers and can route traffic away from unhealthy targets when health checks are configured.

### 18. What is a Classic Load Balancer?
**Answer:** Classic Load Balancer (CLB) is an earlier Elastic Load Balancing option that distributes traffic at basic transport and application layers. It was used in the session to demonstrate load-balancing fundamentals before covering Application Load Balancers and Network Load Balancers. For new designs, evaluate the newer load balancer types against the workload requirements.

### 19. What is round-robin load distribution?
**Answer:** Round-robin is a simple distribution approach in which successive requests are assigned across available servers in turn. In a real load-balancer setup, the observed result can depend on connections, health checks, client behavior, and the selected balancing algorithm.

### 20. How did the session demonstrate a working load balancer?
**Answer:** NGINX was installed on two separate Linux EC2 instances. Students connected to the instances using MobaXterm, configured a Classic Load Balancer with both servers as targets, and observed requests being distributed between the servers.

---

# Part 2: 20 Basic Scenario-Based Questions and Answers

### 1. Your team needs a virtual Linux server to host a test application. Which AWS service would you use?
**Answer:** Use Amazon EC2. Choose an appropriate instance type, configure networking and security, select storage, and install the required software.

### 2. You need to store website images, downloadable files, and backup archives. Which service fits this need?
**Answer:** Amazon S3 is a suitable choice for object storage. Organize the files in buckets and configure access permissions carefully.

### 3. An EC2 instance needs a separate disk for application data. Which storage service should you consider?
**Answer:** Consider an EBS volume and attach it to the EC2 instance, subject to the instance and volume configuration. Format and mount the volume before using it as a file system.

### 4. Two application servers must read and write the same shared files. Which service from the session could help?
**Answer:** Amazon EFS may be appropriate because it provides shared file storage for supported compute instances. Confirm the application’s file-locking, performance, and access requirements.

### 5. A company wants its AWS resources inside an isolated virtual network. Which service should it start with?
**Answer:** Create or use an Amazon VPC, then plan subnets, routing, and security controls according to which resources need private or public connectivity.

### 6. A developer can sign in to AWS but should only be allowed to manage specific resources. What should the administrator configure?
**Answer:** Use IAM identities and policies with least-privilege permissions. Avoid giving broad administrative access when the developer only needs a limited set of actions.

### 7. Your application requires a managed relational SQL database. Which service from the notes is relevant?
**Answer:** Amazon RDS is a good starting point. Select a supported database engine and plan connectivity, credentials, backups, and availability requirements.

### 8. A new application stores user session information as key-value data and does not need a relational schema. Which service might fit?
**Answer:** Amazon DynamoDB may be suitable. Model the partition and sort keys around the application’s access patterns.

### 9. A small function should run when a configured event occurs, without managing a virtual server. Which service should you consider?
**Answer:** AWS Lambda. Configure the event source, execution role, code, and monitoring needed for the function.

### 10. A team wants to expose backend functionality through managed HTTP APIs. Which service could act as the API front door?
**Answer:** Amazon API Gateway. Configure the routes, authorization, integrations, and throttling appropriate for the API.

### 11. Users in different countries report slow downloads of static website assets. What AWS service could improve content delivery?
**Answer:** Amazon CloudFront can cache and deliver suitable content through edge locations closer to users. Configure the origin and caching behavior appropriately.

### 12. One application component needs to send work to another component for processing later. Should it use SQS or SNS?
**Answer:** Amazon SQS is a natural fit for queue-based asynchronous processing. The producer sends messages to a queue, and consumers process them independently.

### 13. A system needs to publish an alert to several subscribers, such as email endpoints or other services. Which service is relevant?
**Answer:** Amazon SNS supports publish/subscribe messaging and can distribute notifications to configured subscribers.

### 14. An administrator wants to see application metrics and create an alarm when CPU usage is high. Which service should be checked?
**Answer:** Amazon CloudWatch. Configure the relevant metrics, alarm threshold, evaluation period, and notification action.

### 15. A security auditor asks who made an API call that changed an AWS resource. Which service should be checked?
**Answer:** AWS CloudTrail records account activity and API events. Search the relevant event history or trail data for the action, identity, and timestamp.

### 16. A team wants to recreate AWS infrastructure consistently from a template. Which service from the session can help?
**Answer:** AWS CloudFormation can define AWS resources as infrastructure as code. Store templates in version control and review changes before applying them.

### 17. An organization wants to run Kubernetes workloads on AWS without managing its own Kubernetes control plane. Which service should it consider?
**Answer:** Amazon EKS is a managed Kubernetes service. The team still needs to plan worker compute, networking, permissions, and cluster operations.

### 18. You have two EC2 instances running NGINX, but users must access one common endpoint. What should you configure?
**Answer:** Configure an Elastic Load Balancer, register both instances as targets, configure a listener and suitable health checks, and ensure security groups allow the required traffic.

### 19. The load balancer sends traffic to a server where NGINX has stopped. What should you check?
**Answer:** Check the target’s health status and health-check configuration, confirm NGINX is running and listening on the expected port, and verify the instance security group and network path. The load balancer should avoid routing to targets marked unhealthy once health checks detect the failure.

### 20. You want to host a simple portfolio site containing HTML, CSS, JavaScript, and images. What option was discussed in the session?
**Answer:** Amazon S3 static website hosting was identified as a valid use case. Configure the site files and access appropriately. For HTTPS and a production-style setup, consider CloudFront in front of S3 and follow the recommended origin-access pattern.

---

# Quick Revision Table

| Service / Concept | Main purpose |
|---|---|
| EC2 | Virtual servers |
| S3 | Object storage; files, backups, static content |
| EBS | Block storage for EC2 |
| EFS | Shared file storage |
| RDS | Managed relational database |
| DynamoDB | Managed NoSQL database |
| VPC | Isolated virtual network |
| IAM | Identities and permissions |
| Lambda | Run code in response to events |
| API Gateway | Create and manage APIs |
| CloudFront | CDN and edge content delivery |
| SQS | Queue-based asynchronous messaging |
| SNS | Publish/subscribe notifications |
| CloudWatch | Metrics, logs, alarms |
| CloudTrail | AWS account/API activity history |
| CloudFormation | AWS infrastructure as code |
| EKS | Managed Kubernetes |
| Elastic Load Balancing | Distributes traffic across targets |
| NGINX | Web server used in the practical |
| MobaXterm | SSH client and session manager used in the practical |

## Hands-on Revision Checklist

- [ ] Launch a Linux EC2 instance.
- [ ] Connect to it using MobaXterm and the appropriate private-key format.
- [ ] Install and start NGINX.
- [ ] Repeat the NGINX setup on a second EC2 instance.
- [ ] Configure a load balancer with both instances as targets.
- [ ] Verify that both targets are healthy and observe responses from the two servers.
- [ ] Review the difference between S3, EBS, and EFS.
- [ ] Clean up lab resources that are no longer needed and review possible ongoing charges.

> **Note:** Questions are grounded in the uploaded Day-3 session notes. Where the notes give only a service name or short purpose, the answer provides a brief practical explanation to make the questions useful for interview preparation.


