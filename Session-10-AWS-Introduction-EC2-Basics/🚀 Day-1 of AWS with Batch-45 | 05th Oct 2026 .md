## Key Outcomes

This was the first day of an AWS training session focused on foundational concepts rather than completing specific services. The instructor (Vikas) covered AWS IAM (Identity and Access Management) in depth — including root users, IAM users, groups, roles, and policies — with live demonstrations of user creation and access key generation. Core cloud concepts including elasticity, horizontal vs. vertical scaling, auto-scaling, and high availability were also taught. The session concluded with a live AWS account creation walkthrough and an extended Q&A.

---

## Decisions Made

- **Concept-first approach** adopted for Day 1: practicals will deepen from Day 2 onward; students should not expect to "complete" EC2 or IAM in a single session. 
- **AWS account creation** deferred to end of session for those without accounts; existing accounts should be reused, not replaced. 
- **MFA intentionally not taught** to avoid students locking themselves out if they change phones — Vikas explicitly took responsibility for this pedagogical choice. 
- **Vertical scaling cannot be used with auto-scaling** because the instance must be stopped/restarted to resize — horizontal scaling is the only auto-scaling-compatible approach. 
- **Paid AWS accounts recommended** for students whose free tier has expired rather than creating new accounts with different identities. 

---

## Core Concepts Taught

### IAM — Identity and Access Management

**Root User vs. IAM User**

- **Root user**: The first account created when an AWS account is set up; has full, unrestricted access to all AWS services, billing, and management console — equivalent to a "super user" or "owner account." 
- **IAM user**: A secondary identity created by the root user; access is limited by assigned roles and policies. Used by human employees or automated tools to interact with AWS without exposing root credentials. 
- Best practice: No one in a company should log in as root; IAM users with restricted policies should be used instead. 

**IAM User Creation (Live Demo)**

- Three users were created live: **Rahul Suman** (human user with console access), **Abhijit** (human user added to a group), and **Terraform** (non-human/tool user without console access). 
- Human users require **console access enabled** with a username and password; tool-based users (e.g., Terraform) do **not** need console access. 
- For tool users, authentication is done via **Access Key + Secret Key** — the equivalent of a username and password for CLI/API access. 
- Access keys are generated under the user's security credentials: `CLI` option selected → terms confirmed → access key and secret key generated. 

**Groups**

- Groups allow multiple IAM users to share the same permissions; a group named **Batch 45** was created with **Administrator Access** policy. 
- Abhijit was added to the group; Rahul was not added to any group and therefore had zero access despite being able to log in. 
- Practical benefit: If 50 users are hired, they can all be added to one group and inherit the same policies automatically. 

**Roles and Policies**

- **Policy**: A document defining a set of permissions (allowed or denied actions); applicable at the organization, group, or user level. AWS provides 1,600+ built-in policies. 
    - Example: A policy might allow EC2 instance creation and stopping, but deny deletion. 
- **Role**: A temporary identity that can be assumed by a user or service to gain specific permissions. AWS provides 100+ default roles. 
    - Analogy: Policy = office-wide rules applicable to all (e.g., attendance); Role = job-specific responsibilities (e.g., Kubernetes admin only manages Kubernetes). 
- **Key distinction**: Roles are **temporary**; policies **define** what actions are allowed or denied. 

**Credential Reports / Audit**

- IAM provides downloadable **credential reports** showing all users in an account, their password status, access key usage, last login, and rotation schedules — used for security audits. 

**IAM Capabilities Summary**

- Authenticate and authorize human users, applications, CLI tools, SDKs, third-party tools (e.g., Jenkins, Terraform, Ansible), and APIs. 
- Supports AD groups and SSO integration. 
- Account alias (e.g., `CloudDevOpsHub`) can be set so IAM users log in via a named URL rather than a numeric account ID. 

---

### Elasticity, Scaling, and High Availability

**Elasticity**

- The ability of compute resources (CPU, memory) to increase or decrease based on demand — the foundational concept behind AWS services prefixed with "E" (EC2, EBS, ECS, EKS, EFS, ECR, ELB, etc.). 

**Horizontal Scaling (Scale Out/In)**

- Adding or removing **additional server instances** from a pool to handle load changes. 
- Example: Flipkart's Big Billion Sale — traffic increases 100x, so 100 VMs are spun up; when traffic drops, VMs are released. 
- **Best for**: Web servers and stateless applications — if one server fails, others continue handling requests. 
- Supports **auto-scaling** because new instances can be added/removed without downtime. 

**Vertical Scaling (Scale Up/Down)**

- Increasing or decreasing the **size/power of a single existing instance** (e.g., upgrading from 2 GB RAM to 10 GB RAM on the same VM). 
- **Best for**: Databases — splitting a database across multiple servers creates data consistency issues (half the data on one server, half on another). 
- **Limitation**: Requires the instance to be **stopped** before resizing — incompatible with auto-scaling. 
- Cannot be done "on the fly" in a live environment. 

**Auto-Scaling**

- Automatically increases instances when load exceeds a threshold and releases them when load drops. 
- **Cooling period**: After traffic decreases, a ~60-second observation window ("cool down") occurs before instances are released to avoid premature scale-in. 
- Scale terminology: **Scale out** (add instances), **Scale in** (remove instances), **Scale up** (increase instance size), **Scale down** (decrease instance size). 
- Auto-scaling applies only to **horizontal scaling**; vertical scaling requires manual intervention and a restart. 

**High Availability (HA)**

- Deploying **identical sets of servers in two or more geographic regions** so that if one region goes down, the other handles all traffic seamlessly — users experience zero downtime. 
- Example: Servers in both India and the UK; if UK goes down entirely, India continues serving users. 
- Involves two full server sets in sync, meaning approximately **2x the cost** but ensuring continuous availability. 
- Related concept: **DC (Data Center) and DR (Disaster Recovery)** — maintaining a primary and a backup data center. 

---

## AWS Account Creation Walkthrough

- Link shared: `aws.amazon.com/console` — students instructed to bookmark it. 
- **Free tier**: $100 credit for first 3 months; another $100 if used within that period (not $200 in one shot). 
- **Steps covered**:
    1. Click "Create Free Account" → provide valid email (Gmail sign-in supported). 
    2. Set a strong, memorable password. 
    3. Provide personal/KYC information (PAN card or DigiLocker verification). 
    4. Payment info (credit/debit card with international transactions enabled, or UPI) — required but not charged for free tier usage. 
    5. Phone number OTP verification → account created. 
- **Live demo** (Junaid/Mohammad): Account created without credit card; EC2 instance launched in two clicks to verify the account works. 
- **Key notes**:
    - Students with accounts created 5–6 months ago should use the **same account**. 
    - Students whose free tier expired (3–4 years ago) should use a **different mobile number** for a new account. 
    - Family members' accounts can be used as an alternative. 
    - IAM users, VPCs, and groups incur **no billing** — only compute resources (VMs, storage, etc.) generate charges. 

---

## Q&A Highlights

- **Q: Why use IAM users instead of root?** → Root cannot be shared; IAM users allow role-based, limited access per team member or tool. 
- **Q: How does Terraform authenticate to AWS?** → Terraform uses CLI, not the console; it authenticates via **access key + secret key** (not username/password). 
- **Q: Is auto-scaling a part of elasticity?** → They are related but separate concepts: elasticity is the property (increase/decrease); auto-scaling is the mechanism that implements it automatically based on conditions. 
- **Q: Which scaling is more powerful — horizontal or vertical?** → Neither is universally superior; choice depends on use case. Databases → vertical; web/app servers with variable traffic → horizontal. 
- **Q: Can a former employee still access an EC2 instance with their old key pair?** → Yes, as long as the instance is still running and the public key was not rotated or removed from the instance. 
- **Q: For high availability, how many subnets are needed?** → Depends on project requirements; 2 or 4 subnets are both acceptable answers in an interview — justify based on architecture needs. 
- **Q: Do IAM users incur billing?** → No. Users are just information records; billing applies only to compute resources consuming CPU, memory, and storage. 
- **Q: Can vertical scaling be done on the fly?** → No — the instance must be stopped first. 
- **Q: When to choose RDS Postgres vs. Aurora Postgres?** → Vikas confirmed this will be covered in an upcoming session. 

---

## Pending Confirmation

- RDS Postgres vs. Aurora Postgres comparison — to be covered in a future session. 
- Cross-account access via STS (Security Token Service) — briefly mentioned but deferred; noted as more relevant to DevOps architect roles than standard DevOps engineers. 
- GCP billing issue (Tarun): Machine ran for 3–4 days generating unexpected charges; advised to contact **GCP Billing Support** via `console.cloud.google.com → Billing → Get Billing Support` and raise a ticket explaining student status. 

---

## Action Items

- **All students**: Create an IAM user with your own name under your AWS account; add to a group with Administrator Access. 
- **All students**: Delete any running VMs/instances on AWS or GCP after practicals to avoid billing. 
- **All students**: Bookmark `aws.amazon.com/console` for daily login. 
- **Students without AWS accounts**: Create accounts following the walkthrough; use the blog link shared in Zoom chat. 
- **Students with expired free tier**: Use a different mobile number or a family member's account; consider using a paid account (~₹500/month). 
- **All students**: Write one-line summaries of today's concepts in the WhatsApp group. 
- **All students**: Post on LinkedIn regularly to improve AI-driven ranking on the leaderboard; screenshot leaderboard before Monday reset. 
- **Tarun**: Contact GCP Billing Support to dispute unexpected charges; do not mislead other students about cloud provider billing differences. 

---

## Prerequisites Reminder

- **Linux must be completed** before progressing — all Linux videos, messages, and troubleshooting content should be reviewed. 
- Students who are behind should complete pending recordings at their own pace; fast learners should continue with AWS; slow learners should strengthen the Linux foundation first. 



# AWS Day 1 – Interview Questions and Basic Scenarios
## Batch-45 | 5 October 2026

Based on the Day-1 AWS training notes covering IAM, cloud elasticity, scaling, high availability, and AWS account basics.

---

# Part 1: 20 Interview Questions and Answers

### 1. What is AWS IAM?
**Answer:** AWS Identity and Access Management (IAM) helps control who can access AWS resources and what actions they can perform. It supports authentication and authorization for users, applications, CLI tools, SDKs, and APIs.

### 2. What is the difference between the AWS root user and an IAM user?
**Answer:** The root user is the identity created when the AWS account is first set up and has unrestricted access to the account, including billing and account management. An IAM user is a separate identity whose permissions can be limited using policies. The root account should not be used for routine work.

### 3. Why should teams avoid using the root user for daily tasks?
**Answer:** The root user has broad, unrestricted access and is not suitable for sharing among team members. IAM identities allow teams to give each person or tool only the permissions needed for their work.

### 4. What is an IAM policy?
**Answer:** An IAM policy is a permissions document that specifies which actions are allowed or denied on AWS resources. For example, a policy could allow a user to start and stop EC2 instances but deny deleting them.

### 5. What is an IAM group?
**Answer:** An IAM group is a way to manage permissions for multiple IAM users together. Users in a group can receive the permissions assigned to that group, making access management easier as a team grows.

### 6. What is the difference between an IAM user and an IAM group?
**Answer:** An IAM user represents an identity, such as an employee or a tool account. An IAM group is a collection of IAM users used to apply common permissions. A group is not a login identity.

### 7. What is an IAM role?
**Answer:** An IAM role is an identity with a set of permissions that can be assumed when needed. It is commonly used to provide temporary access to a user or AWS service. The training notes distinguish roles as temporary identities and policies as documents defining permissions.

### 8. What is the difference between a role and a policy?
**Answer:** A policy defines which actions are allowed or denied. A role is an identity that can be assumed and has permissions associated with it. A role can use policies to determine what the assumed identity can do.

### 9. How can a human user and an automation tool authenticate differently?
**Answer:** A human user may use AWS Management Console access with a username and password. A tool such as Terraform commonly uses programmatic access through an AWS authentication method rather than signing in to the console.

### 10. What are an access key ID and secret access key used for?
**Answer:** They are credentials used for programmatic AWS access through tools, SDKs, or APIs. The secret access key must be protected and should never be committed to source control or shared publicly.

### 11. Does Terraform need AWS Management Console access to manage AWS resources?
**Answer:** No. Terraform typically calls AWS APIs using configured credentials and permissions. It does not need a human-style console login. The training notes demonstrate access-key-based authentication; in production, prefer short-lived credentials or an IAM role when available.

### 12. What is the purpose of an IAM credential report?
**Answer:** It provides an account-level view of IAM users and credential information, such as password status, access-key status and usage, and last activity information where available. It helps administrators review credentials during security audits.

### 13. What is an IAM account alias?
**Answer:** An account alias provides a human-readable name for the AWS account sign-in URL instead of relying only on the account ID. It makes the IAM sign-in URL easier to recognize and share with authorized users.

### 14. What is elasticity in cloud computing?
**Answer:** Elasticity is the ability to increase or decrease computing resources in response to demand. For example, an online store may need more capacity during a sale and less capacity when traffic returns to normal.

### 15. What is horizontal scaling?
**Answer:** Horizontal scaling means adding or removing instances or servers. Adding instances is called scale out; removing them is called scale in. It is useful for applications that can distribute work across multiple instances.

### 16. What is vertical scaling?
**Answer:** Vertical scaling means increasing or decreasing the capacity of one machine, such as moving to an instance type with more CPU or memory. Depending on the instance and change, this may require stopping the instance and can cause downtime.

### 17. What is the difference between horizontal and vertical scaling?
**Answer:** Horizontal scaling changes the number of instances, while vertical scaling changes the size of an individual instance. Horizontal scaling is often useful for web applications with changing traffic; vertical scaling can be useful when a workload needs a larger individual server.

### 18. What is Auto Scaling, and how does it relate to elasticity?
**Answer:** Auto Scaling automatically adjusts capacity based on configured policies or conditions. Elasticity is the ability to change capacity; Auto Scaling is one mechanism used to achieve it. AWS Auto Scaling behavior depends on the service and configuration.

### 19. What is high availability (HA)?
**Answer:** High availability means designing a system to remain accessible when a component or location fails. A common approach is to distribute redundant resources across multiple Availability Zones and configure health checks and traffic routing. Multi-Region designs may be used when regional failure protection is required.

### 20. What is the difference between high availability and disaster recovery?
**Answer:** High availability aims to reduce interruptions during component or zone failures. Disaster recovery focuses on restoring service after a major disruption, using planned recovery processes and backup or standby resources. The required design depends on recovery-time and recovery-point targets.

---

# Part 2: 20 Basic Scenario-Based Questions and Answers

### 1. A company owner asks all developers to use the AWS root account for daily work. What would you recommend?
**Answer:** Recommend using separate IAM identities with only the permissions each developer needs. Keep root access restricted to account-level tasks that require it.

### 2. Rahul can sign in to the AWS console but cannot launch an EC2 instance. What could be wrong?
**Answer:** Being able to sign in only proves authentication succeeded. Rahul may not have a policy allowing the required EC2 actions, or an explicit deny or other permission boundary may block the request. Review his user and group permissions.

### 3. A company hires 50 engineers who need the same read-only AWS access. How can access management be simplified?
**Answer:** Create an appropriate IAM group, attach a least-privilege read-only policy, and add the engineers' IAM users to the group. Review access regularly as responsibilities change.

### 4. Abhijit is added to a group with the required permissions, but Rahul is not added to that group. Why might their access differ?
**Answer:** Abhijit receives the permissions assigned to the group, while Rahul does not inherit those group permissions. Check any other policies or permission controls that may also affect their access.

### 5. Terraform must create an EC2 instance, but the team created only a console username and password. What is missing?
**Answer:** Terraform needs programmatic AWS authentication and sufficient API permissions. Configure a supported credential method, preferably short-lived role-based credentials where possible, rather than relying on console credentials.

### 6. A developer accidentally commits an AWS secret access key to a public Git repository. What should the team do first?
**Answer:** Treat the key as compromised. Disable or deactivate it immediately, investigate its use, rotate credentials, remove the exposed secret from the repository where possible, and move secrets into a secure credential store. Deleting the visible commit alone is not enough.

### 7. The security team wants to identify IAM users with old or unused credentials. Which IAM feature can help?
**Answer:** Generate and review the IAM credential report, then investigate old passwords and access keys. Confirm actual usage before disabling credentials so that production automation is not unexpectedly broken.

### 8. A new employee needs to manage EC2 instances but must not delete them. What should you configure?
**Answer:** Create or assign a policy that allows only the required EC2 actions and explicitly excludes or denies deletion as appropriate. Test the policy and keep permissions limited to the required resources.

### 9. A team wants an easy-to-recognize sign-in URL for its IAM users. What can it configure?
**Answer:** Set an AWS account alias. Share the resulting sign-in URL only with authorized users and continue enforcing appropriate authentication and access controls.

### 10. An online store experiences a large traffic increase during a sale. What scaling approach is a good starting point?
**Answer:** For a stateless web tier, use horizontal scaling with multiple instances behind a load balancer and configure Auto Scaling based on suitable metrics. Test capacity and health checks before the sale.

### 11. Traffic drops after a sale. What should happen to the extra application instances?
**Answer:** A properly configured Auto Scaling group can scale in by removing excess instances after its conditions and cooldown or warm-up behavior permit it. Use termination protections and lifecycle handling where needed so requests are not interrupted.

### 12. A single server is running out of memory, and the application cannot easily be distributed across multiple servers. What option could help?
**Answer:** Consider vertical scaling by moving to an instance with more memory. Check compatibility and plan for any required stop/start operation and downtime before resizing.

### 13. A team expects traffic to change automatically throughout the day. Should it rely only on manual vertical resizing?
**Answer:** Usually not. For a horizontally scalable application, configure Auto Scaling to add or remove instances based on demand. Manual vertical resizing is not a substitute for horizontal Auto Scaling.

### 14. An application runs on only one server. If that server fails, the website becomes unavailable. What is a basic improvement?
**Answer:** Deploy multiple application instances across separate Availability Zones, put them behind a load balancer, and configure health checks. Confirm that the application and its data layer can also tolerate failures.

### 15. A company wants its service to survive a complete AWS Region outage. Is deploying only across two Availability Zones in one Region enough?
**Answer:** No. Availability Zones help with many data-center-level failures, but a complete Region outage may affect all of them. Consider a multi-Region disaster-recovery or high-availability design and test the failover process.

### 16. A database needs more memory because its workload has grown. Which scaling option might be suitable?
**Answer:** Vertical scaling may be suitable if the database benefits from a larger instance. Evaluate database-specific options, downtime, failover support, and whether read replicas or other architecture changes are also needed.

### 17. A student finishes an EC2 practical and leaves the instance running. How can unexpected costs be reduced?
**Answer:** Stop or terminate resources that are no longer needed, depending on whether they must be kept. Also check attached storage, public IPv4 addresses, snapshots, and other billable resources because stopping an instance does not necessarily remove every charge.

### 18. A student assumes creating IAM users and groups will itself create a compute bill. Is that correct?
**Answer:** IAM users and groups do not normally incur direct charges. However, resources created by those identities—such as EC2 instances, storage, or other billable services—can incur charges.

### 19. A former employee still has an old SSH private key for an EC2 instance. Does removing their IAM user automatically block SSH access?
**Answer:** Not necessarily. EC2 SSH key-based operating-system access is separate from IAM console permissions in a typical setup. Remove or rotate the relevant authorized key, review other access paths, and follow the organization's offboarding process.

### 20. A team wants to deploy identical application stacks in India and the UK so one can serve users if the other location fails. What should it plan?
**Answer:** This is a multi-Region design. Plan data replication, traffic routing or failover, health checks, consistency, recovery objectives, security, and the higher cost of running redundant capacity. Test the failover instead of assuming it will happen seamlessly.

---

## Quick Revision Table

| Topic | Remember |
|---|---|
| Root user | Unrestricted account-level identity; avoid routine use |
| IAM user | Identity for a person or workload |
| IAM group | Applies common permissions to multiple users |
| IAM policy | Defines allowed and denied actions |
| IAM role | Assumable identity, commonly used for temporary access |
| Access keys | Programmatic credentials; keep secrets protected |
| Credential report | Helps audit IAM credential status and usage |
| Elasticity | Capacity can increase or decrease with demand |
| Horizontal scaling | Add/remove instances; scale out/in |
| Vertical scaling | Increase/decrease one instance's size; scale up/down |
| Auto Scaling | Automatically adjusts capacity according to configuration |
| High availability | Reduces service interruption through redundancy |
| Disaster recovery | Restores service after a major disruption |
| Cost hygiene | Clean up unused resources and check related billable items |

## Practical Revision Tasks

1. Create an IAM user for a lab exercise and assign only the permissions needed.
2. Create a group and compare the effective access of a user inside and outside that group.
3. Review an IAM credential report and identify credentials that need investigation.
4. Explain horizontal versus vertical scaling using an online-shopping example.
5. Draw a basic highly available web architecture with instances in two Availability Zones and a load balancer.
6. After every lab, review the billing console and clean up resources that are no longer required.

> **Note:** These questions are based on the uploaded Day-1 session notes. A few answers add practical AWS security and architecture context where needed, and clarify that exact behavior depends on service configuration.
