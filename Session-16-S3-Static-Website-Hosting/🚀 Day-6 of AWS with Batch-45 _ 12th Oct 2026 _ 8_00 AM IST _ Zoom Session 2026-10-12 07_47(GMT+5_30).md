## Key Outcomes

The session covered AWS S3 storage classes with real-time use cases, S3 lifecycle policy configuration, and hands-on automation of a static website deployment using Terraform with CloudFront (CDN) and Origin Access Control (OAC). Students successfully deployed a portfolio website to AWS S3 via Terraform automation, with CDN integration providing HTTPS and edge-location caching. Key conceptual distinctions between CDN, OAC, load balancers, and S3 static hosting were discussed and clarified through Q&A.

---

## Decisions Made

- **Recording format changed:** Vikas switched to recording locally on his laptop for this session and asked students to compare quality with previous recordings and provide feedback by evening. 
- **Certificates require full names:** All students must update their full names in the system so certificates (workshop/weekend) are generated correctly. 
- **S3 static hosting is for static content only:** Confirmed that S3 static website hosting cannot process backend requests or payment gateways — a student's sweet-shop website was cited as an example of an incorrect use case. 
- **Region cannot be changed after bucket creation:** Bucket region is fixed (physical quantity); only objects can be copied/moved to another region. 
- **Bucket name is globally unique (with nuance):** Globally unique by default, but with the newer account-namespace option, the same name can exist within a different account — both answers are valid in interviews. 
- **UI deletion limit:** Cannot delete more than 1 lakh (100,000) objects from the S3 console UI; CLI must be used. The AWS multi-object deletion API allows only 1,000 objects per operation, making synchronous UI deletion impractical at scale. 
- **Lifecycle rules automate storage class transitions:** The word "lifecycle rules/policies" is the correct term for automating data movement between storage classes. 
- **Terraform destroy to clean up:** Students instructed to run `terraform destroy` after the practical to avoid unnecessary charges. 

---

## S3 Storage Classes — Real-Time Use Cases

Five primary storage classes were covered (Reduced Redundancy and others explicitly excluded): 

- **Standard**
    - For frequently accessed, hot data — websites, portfolios, recently generated data, log files. 
    - Default class when uploading; data accessible with lowest latency. 
- **Intelligent Tiering**
    - Adds monitoring on top of Standard; AWS charges a small monitoring fee but automatically moves data based on access patterns. 
    - Analogy used: just as a trainer understands student attendance patterns (weekend vs. evening batches), Intelligent Tiering learns data access patterns and adjusts storage accordingly. 
- **Standard-One Zone**
    - Data stored in only one availability zone (vs. three or more for other classes); approximately one-third cost reduction. 
    - Suitable when slight slowness is acceptable and data loss risk in one zone is tolerable.
- **Glacier (Instant Retrieval)**
    - For archival data accessed quarterly or less; retrieval in seconds to minutes. 
    - **Instant Retrieval:** data available in seconds/couple of minutes.
    - **Standard Glacier:** retrieval takes 2+ hours. 
- **Glacier Deep Archive**
    - For data accessed less than once a year; lowest cost, highest retrieval latency. 

---

## Banking Scenario — Lifecycle Policy Design

A practical scenario was posed: store banking/UPI transaction data with tiered retention: 

|             Phase             |    Duration    | Recommended Storage Class |
|-------------------------------|----------------|---------------------------|
| Active/online access          | 0–90 days      | **Standard**              |
| Monitoring-based optimization | 90 days–1 year | **Intelligent Tiering**   |
| Archival, infrequent access   | 1–3 years      | **Glacier**               |
| Deep archival                 | 3–5 years      | **Glacier Deep Archive**  |

- Banking data is **never deleted** (regulatory requirement); log files, by contrast, can be deleted after a defined period. 
- Maximum real-time data retention cited as **3 years** active, then archival. 
- Transitions between classes incur a **per-request fee**, but this is far preferable to manual management. 

---

## S3 Lifecycle Rules — Practical Configuration

Vikas demonstrated creating a lifecycle rule named **"banking-policy"** in the S3 Management tab: 

- Scope: applied to all objects in the bucket (no filter).
- **Transition – Current Version:** moves live objects between storage classes at defined day thresholds (e.g., Day 90 → Intelligent Tiering, Day 180 → One Zone or Glacier Instant Retrieval, Day 365 → Glacier Deep Archive). 
- **Transition – Non-Current Version:** handles older versions of objects (relevant when versioning is enabled).
- **Expire / Permanently Delete:** optional rule to delete objects after a set period (use-case dependent). 
- Multiple rules can be created for different object sets within the same bucket.

---

## S3 Bucket Configuration — Key Concepts

### Bucket Types

- **General Purpose:** standard option used for most workloads.
- **Directory Bucket:** new option optimized for low-latency use cases (single-zone storage); not recommended when availability is a priority. 

### Versioning

- Enabled in production environments; each re-upload of a file creates a new version while retaining the old one. 
- If a file is uploaded 10 times, 10 versions are maintained. 

### Encryption

Three SSE options available: 

- **SSE-S3:** default, auto-enabled, AWS-managed keys.
- **SSE-KMS:** customer-managed keys via AWS Key Management Service (customizable).
- **DSSE-KMS:** dual-layer encryption.
- Encryption analogy: like Netflix/Udemy videos that appear as a black screen when screen-recorded — the file is unusable without the decryption key. 

### Object Lock

- When enabled, objects **cannot be permanently deleted**; should be disabled unless data immutability is required. 

### Bucket Tagging

- Tags used for project/environment tracking (e.g., Environment = Production, Project = Batch 45). 

### Access Logs & Metrics

- All bucket activity can be logged object-wise; metrics show bucket size, object count, and usage trends over time. 

### Object Operations (UI)

- Objects can be uploaded directly to a specific storage class (not just Standard). 
- Actions available: copy URL, URI, download, open, delete, move, rename, change storage class.
- Folders/directories can be created inside buckets; drag-and-drop supported. 

---

## Key S3 Interview Points

- **Bucket name in URL:** Cannot be hidden — it is part of the DNS-compliant URL. 
- **Bucket rename:** Not possible after creation.
- **Region change:** Not possible after bucket creation; only objects can be migrated. 
- **100 buckets** is the default limit per account — more than enough for most use cases (analogous to not needing 100 hard drives). 
- **Deleting >1 lakh objects via UI:** Not supported; use CLI command `aws s3 rm` (mirrors Linux `rm`, `cp`, `mv` syntax). 
- **Static website hosting:** Supported on S3; HTTPS/SSL certificates cannot be added directly to S3 — CloudFront is required for HTTPS. 
- **Replication:** Available for copying data across buckets or regions; applied at the object level in real-time environments. 

---

## Terraform Automation — Portfolio Deployment on AWS

### What Was Automated

The session demonstrated deploying a static website (portfolio) to AWS using a pre-written Terraform codebase, covering: 

- S3 bucket creation
- ACL and public access configuration
- Static website hosting configuration
- CloudFront distribution (CDN) with OAC (Origin Access Control)
- Auto-generated HTTPS URL via CloudFront

### Tools Required

1. **Terraform CLI** — binary download (~32 MB); no installer needed. 
2. **AWS CLI** — MSI installer for Windows (~47 MB), script-based for Mac. 
3. **IAM User with Access Key** — a dedicated user (e.g., "terraform-45") created with admin/full-access policy; access key and secret key generated and configured. 
4. **Git** — to clone the Terraform code repository. 

### Setup Steps

1. Download and place Terraform binary in a `bin` folder. 
2. Install AWS CLI; verify with `aws` command in terminal. 
3. Create IAM user → generate access key → run `aws configure` (provide Access Key ID, Secret Key, region, output format). 
    - Mac users: set output format to `json` to avoid "unknown output type" error. 
4. Clone the Terraform repo into a local project folder. 
5. Copy Terraform binary into the cloned project folder (workaround if environment variables not set). 

### Terraform Commands

- `terraform init` — initializes the working directory, downloads provider plugins. 
- `terraform plan` — previews what will be created; checks account-level access but cannot validate global conflicts. 
- `terraform apply` — executes the infrastructure creation; prompts for confirmation. 
- `terraform destroy` — tears down all created resources. 
- `terraform output` — displays outputs such as the website URL after apply. 

### Errors Encountered & Resolutions

- **409 Conflict (name already exists):** CDN/CloudFront distribution name must be globally unique; resolved by changing the variable value in the Terraform variables file. 
- **403 Access Denied (account not verified):** Vikas's account was blocked by AWS for CloudFront creation; an AWS support ticket was raised. Students with the same error should raise a support case — resolution can take 2–15 days depending on account tier. 
- **Empty directory error:** Terraform must be run from within the cloned code directory, not an empty folder. 
- **Permission (403/4xx):** Indicates the IAM user lacks sufficient permissions; full-access policy must be attached. 

### Successful Output

- Students (e.g., Narendra) successfully deployed the portfolio; the CloudFront URL with HTTPS was auto-generated. 
- The CDN URL format is auto-generated by AWS (random string), not customizable without additional configuration. 

---

## CDN, OAC, and Architecture Concepts

### Architecture: Manual vs. Automated

- **Manual (previous session):** S3 bucket → ACL enabled → public access enabled → static website URL generated → users access via HTTP. 
- **Automated (this session):** S3 bucket (private) → OAC configured → CloudFront distribution → edge locations → users access via HTTPS. 

### CloudFront (CDN)

- **400+ Points of Presence (edge locations)** globally; content is cached at the nearest edge location to the user. 
- First request goes to the origin (S3/data center); subsequent requests served from cached edge location. 
- Syncing/cache invalidation: changes to S3 may take ~30 minutes to propagate to edge locations; forced invalidation requires additional configuration. 
- CDN ≠ Load Balancer: Load balancer distributes traffic within a region; CDN distributes cached content across global edge locations. 

### Origin Access Control (OAC)

- A **CloudFront security feature** that restricts direct S3 URL access; forces all traffic through the CDN network rather than exposing raw S3 object URLs. 
- Prevents exposure of S3 directory structure in URLs. 
- OAC is distinct from HTTPS/SSL — SSL is enforced by CloudFront on every request regardless of OAC. 
- When OAC is not used (manual setup), S3 static URL is publicly accessible directly, which is less secure. 

### Why OAC + CDN Together

- CDN provides **performance** (caching, low latency, global distribution).
- OAC provides **security** (restricts direct S3 access, enforces CDN as the only entry point).
- Together they represent production-grade best practices for static website hosting. 

---

## Homework & Practice

- **Architecture diagram:** Students to draw the architecture of the static website deployment (S3 + CDN + OAC) independently, optionally using AI tools, and present in the next class. 
- **Environment variable setup:** Students to configure Terraform binary path as a system environment variable (Windows: Advanced System Settings → Environment Variables → Edit PATH). 
- **Recording practice:** Vikas strongly encouraged students to record a 1-minute daily video explaining what they learned (any language) to build communication skills for interviews. 
- **LinkedIn/WhatsApp group:** Post a one-line session summary using keywords in the WhatsApp group as a habit-building exercise. 
- **Next session topic:** Databases — students encouraged to read about databases and the difference between object storage (S3) and databases before the next class. 

---

## Pending Confirmation

- **Vikas's CloudFront 403 error:** AWS support ticket raised; awaiting account verification/unblocking (estimated 2–15 days). 
- **Students with 403 CloudFront errors:** Raise AWS support tickets individually; the error is account-specific, not a code issue. 
- **Recording upload:** This session was recorded locally; link will differ from usual — students must check LMS directly and allow up to 90 minutes for upload. 
- **Mandhir to explain OAC/CDN code implementation:** Scheduled for the next class (9–10 minute slot) to walk through how OAC policies are applied in the Terraform/website code. 

---

## Action Items

- **All students:** Update full name in the system for certificate generation. 
- **All students:** Run `terraform destroy` to clean up resources created during the session. 
- **All students:** Set up Terraform binary as a system environment variable (PATH) on their machines. 
- **All students:** Prepare architecture diagram (S3 + CDN + OAC) for next class. 
- **Students with 403 CloudFront error:** Raise an AWS support ticket with account ID and error details. 
- **All students:** Read about databases (difference between S3 object storage and databases) before the next session. 
- **Mandhir:** Present a 10-minute explanation of OAC/CDN code implementation at the start of the next class. 
- **All students:** Practice daily 1-minute recording of session learnings. 


# AWS Day-6 Interview Preparation
## Batch-45 | 12 October 2026

This question bank is based on the Day-6 session topics: Amazon S3 storage classes and lifecycle policies, static website hosting with S3 and CloudFront, Origin Access Control (OAC), Terraform, AWS CLI, and common troubleshooting cases.

---

# Part 1: 20 Interview Questions with Answers

### 1. What is Amazon S3?
**Answer:** Amazon Simple Storage Service (S3) is an object storage service used to store and retrieve files such as images, videos, documents, backups, logs, and static website assets. Data is stored as objects inside buckets.

### 2. What is an S3 bucket?
**Answer:** An S3 bucket is a container for objects. Bucket names must be globally unique within the applicable AWS partition because they are used in S3 URLs.

### 3. What is an S3 storage class?
**Answer:** A storage class determines how S3 stores data and helps balance storage cost, access needs, retrieval charges, and availability requirements.

### 4. When would you use S3 Standard?
**Answer:** Use S3 Standard for data accessed frequently, such as active website assets, frequently used files, and recent logs.

### 5. What is S3 Intelligent-Tiering?
**Answer:** S3 Intelligent-Tiering monitors access patterns and can move eligible objects between access tiers to reduce storage costs when access patterns are unpredictable. Some monitoring and retrieval-related charges may apply depending on the tier and use case.

### 6. What is S3 One Zone-IA?
**Answer:** S3 One Zone-IA is intended for infrequently accessed data that can be recreated if lost. It stores data in one Availability Zone, so it has less resilience to the loss of that zone than multi-AZ storage classes.

### 7. When should you use S3 Glacier storage classes?
**Answer:** Use S3 Glacier storage classes for archival data that is accessed infrequently and where retrieval time and retrieval cost are acceptable. Choose the specific Glacier class based on retention, access frequency, and restore-time requirements.

### 8. What is S3 Glacier Deep Archive?
**Answer:** S3 Glacier Deep Archive is designed for long-term archival data that is rarely accessed. It generally offers very low storage cost, but retrieval can take many hours and has associated costs.

### 9. What is an S3 Lifecycle Policy?
**Answer:** A Lifecycle Policy automates actions on objects, such as transitioning them to lower-cost storage classes after a specified number of days or expiring them when they are no longer needed.

### 10. Can an S3 bucket's AWS Region be changed after creation?
**Answer:** No. You cannot change the Region of an existing bucket. Create a new bucket in the target Region and copy or replicate the objects, then update applications and DNS/configuration as needed.

### 11. What is Infrastructure as Code (IaC)?
**Answer:** IaC means defining infrastructure in configuration files so it can be created, reviewed, version-controlled, and updated consistently instead of being configured manually each time.

### 12. Why use Terraform for AWS infrastructure?
**Answer:** Terraform makes infrastructure repeatable and reviewable. It helps teams provision resources from code, track configuration in version control, plan changes before applying them, and reduce manual configuration differences.

### 13. What does `terraform init` do?
**Answer:** It initializes a Terraform working directory, downloads required providers and modules, and prepares the backend and working environment.

### 14. What does `terraform plan` do?
**Answer:** It compares the desired configuration with the known current state and proposes changes Terraform intends to make. Review the plan before applying it.

### 15. What does `terraform apply` do?
**Answer:** It executes the planned infrastructure changes, subject to approval and configuration. It can create, update, or destroy resources depending on the plan.

### 16. What is Amazon CloudFront?
**Answer:** CloudFront is AWS's content delivery network (CDN). It caches and delivers content through edge locations to reduce latency for users and can provide HTTPS delivery.

### 17. What is Origin Access Control (OAC)?
**Answer:** OAC lets a CloudFront distribution securely access a supported AWS origin, such as a private S3 bucket. The bucket policy can allow the intended CloudFront distribution while blocking direct public access.

### 18. Why put CloudFront in front of a private S3 bucket?
**Answer:** CloudFront provides edge caching and HTTPS delivery while OAC allows the bucket to remain private. Users access the site through CloudFront instead of accessing S3 objects directly.

### 19. Why can an S3 bucket name creation fail?
**Answer:** The name may already be taken, may not meet S3 naming rules, or the request may lack the required permissions. Choose a compliant unique name and check the exact AWS error.

### 20. What can an HTTP 403 or a Terraform error 409 mean in this workflow?
**Answer:** A 403 means the request is forbidden; common causes include missing IAM permissions, an incorrect bucket policy/OAC configuration, or an AWS account restriction. A 409 indicates a conflict, but the exact meaning depends on the API and resource. Read the full error before deciding whether the name, resource state, or another condition caused it.

---

# Part 2: 20 Scenario-Based Questions with Answers

### 1. Scenario: Your website images and CSS files are accessed by users all day. Which S3 storage class would you choose?
**Answer:** Start with S3 Standard because the files are frequently accessed. Review actual access patterns and costs before changing the class.

### 2. Scenario: You store logs whose access pattern is unpredictable. You want S3 to optimize storage tiers automatically. What would you consider?
**Answer:** Consider S3 Intelligent-Tiering. Confirm the eligible object sizes, monitoring charges, and access-tier behavior against the workload before enabling it.

### 3. Scenario: Your organization must retain old audit files for several years, and they are rarely accessed. What is your approach?
**Answer:** Use a lifecycle rule to transition eligible objects to an appropriate Glacier archival class. If access is extremely rare and long restore times are acceptable, evaluate Glacier Deep Archive. Confirm compliance and retrieval requirements first.

### 4. Scenario: A team wants to move objects from Standard to Glacier after 90 days and later to Deep Archive. How can you automate it?
**Answer:** Configure an S3 Lifecycle Policy with transition rules based on object age. Validate the eligible transitions, minimum storage-duration rules, and expected retrieval needs before applying it.

### 5. Scenario: A backup can be recreated from another source, and minimizing storage cost is more important than surviving an Availability Zone outage. Which class might fit?
**Answer:** S3 One Zone-IA may fit if the data is infrequently accessed and reproducible. Confirm the risk is acceptable and that the workload meets the class's requirements.

### 6. Scenario: Your company wants a static website hosted on S3, but the S3 bucket must not be public. What architecture would you use?
**Answer:** Store website assets in a private S3 bucket, create a CloudFront distribution with OAC, and configure the bucket policy to allow access from that distribution. Use CloudFront as the public HTTPS entry point.

### 7. Scenario: The CloudFront URL returns `403 AccessDenied` for files in S3. What would you check?
**Answer:** Check that OAC is attached to the correct origin, the bucket policy allows the correct CloudFront distribution, the origin path and object key are correct, and the relevant IAM/resource policies are valid. Also check whether the requested object exists and review CloudFront and S3 error details.

### 8. Scenario: The S3 object URL is accessible directly, even though users should go through CloudFront. What would you do?
**Answer:** Block public access as appropriate, remove unintended public bucket/object permissions, and configure a bucket policy that grants access to the intended CloudFront distribution through OAC. Test both the CloudFront URL and direct S3 URL after the change.

### 9. Scenario: You deployed a website using Terraform. A teammate wants to create the same infrastructure in a second environment. What benefit does IaC provide?
**Answer:** Keep reusable Terraform configuration and supply environment-specific variables. Initialize, review `terraform plan`, and apply the approved plan in the second environment using an appropriate state/backend setup.

### 10. Scenario: You run `terraform plan` and see resources scheduled for deletion that you did not expect. What should you do?
**Answer:** Do not apply the plan. Review the configuration, variables, state, provider settings, and any recent changes. Use a saved plan and peer review if appropriate, and apply only after the destructive changes are understood and approved.

### 11. Scenario: `terraform init` fails because a provider or module cannot be downloaded. How would you troubleshoot?
**Answer:** Check network/proxy access, provider and module source addresses, version constraints, registry availability, and Terraform configuration syntax. Review the complete error, correct the cause, and rerun `terraform init`.

### 12. Scenario: `terraform apply` reports that a resource already exists or a name conflicts. What do you do?
**Answer:** Read the exact AWS API error and identify the conflicting resource. Check whether the resource already exists, whether the name must be unique, and whether the existing resource should be imported into Terraform state or a different name should be used. Do not blindly delete existing resources.

### 13. Scenario: Creating a CloudFront distribution fails with HTTP 403 and the message says the AWS account must be verified. Is changing the Terraform code enough?
**Answer:** Not necessarily. If AWS explicitly requires account verification, contact AWS Support and include the error details. Also confirm IAM permissions, but do not assume a code change can remove an account-level restriction.

### 14. Scenario: An S3 bucket is in the wrong Region for a new application. Can you edit the bucket to move it?
**Answer:** No. Create a bucket in the required Region, copy or replicate the data, validate access and encryption settings, update application references, and switch traffic after testing. Keep the old bucket until migration is verified.

### 15. Scenario: You need to delete a bucket containing a very large number of objects. What is a safe approach?
**Answer:** First confirm retention, backup, and deletion approval. List and remove objects using an appropriate AWS CLI or automated process, account for versioned objects and delete markers if versioning is enabled, verify the bucket is empty, and then delete it. Console limitations can vary, so use the exact error and current AWS guidance rather than relying only on an object-count threshold.

### 16. Scenario: Users in different countries report slow loading of static website assets. How can CloudFront help?
**Answer:** CloudFront can cache eligible content at edge locations closer to users. Review cache policies, TTLs, compression, origin latency, and cache-hit ratio. Invalidate cached files or use versioned filenames when content changes.

### 17. Scenario: You update `index.html` in S3, but users still see the old page through CloudFront. What should you check?
**Answer:** Check the CloudFront cache policy and TTL. If needed, create an invalidation for the changed path or deploy versioned asset names. Also verify the updated file reached the intended S3 bucket and origin.

### 18. Scenario: Terraform reports access denied while creating or updating an S3 bucket. What should you investigate?
**Answer:** Identify the principal Terraform is using, then check its IAM permissions, permission boundaries, service control policies, session policies, and any relevant bucket policy or encryption-key policy. Confirm the AWS account and Region are correct.

### 19. Scenario: A team is manually creating S3 and CloudFront resources for every environment, and configurations keep drifting. How would you improve the process?
**Answer:** Define the resources in Terraform, store code in version control, separate reusable modules from environment-specific values, use a suitable remote state and locking setup, and review plans through a controlled deployment pipeline.

### 20. Scenario: You need to explain the full request flow for a secure static website built with S3, CloudFront, OAC, and Terraform. What would you say?
**Answer:** Terraform provisions the S3 bucket, CloudFront distribution, OAC, and related policies. A user requests the website over HTTPS from CloudFront. CloudFront serves a cached response when available; otherwise it requests the object from the private S3 origin using OAC-authorized access, caches the response according to its cache policy, and returns it to the user.

---

## Quick Revision Checklist

- [ ] Explain S3 Standard, Intelligent-Tiering, One Zone-IA, Glacier, and Deep Archive.
- [ ] Explain S3 Lifecycle Policies and object transitions.
- [ ] Explain why an S3 bucket's Region cannot be changed.
- [ ] Explain Terraform `init`, `plan`, and `apply`.
- [ ] Draw the private S3 + CloudFront + OAC architecture.
- [ ] Troubleshoot 403 errors using the full error message and policies.
- [ ] Troubleshoot resource conflicts without deleting resources blindly.
- [ ] Explain caching, TTL, and CloudFront invalidation.
- [ ] Practice explaining the architecture in one minute.

**Interview tip:** In scenario questions, explain the checks you would perform and why. Avoid assuming every 403 or 409 has the same cause; use the exact AWS error message and relevant logs.
