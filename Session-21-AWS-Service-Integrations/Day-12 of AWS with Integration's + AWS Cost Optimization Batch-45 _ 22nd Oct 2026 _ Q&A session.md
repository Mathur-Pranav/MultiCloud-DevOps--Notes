## Key Outcomes

The session provided a comprehensive introduction to **AWS Lambda** and serverless computing, covering core concepts, pricing, use cases, and hands-on implementation. Students built a functional **image resizing pipeline** using Lambda, S3, SNS, and CloudWatch — all provisioned via **Terraform**. Cost optimization strategies using AWS billing tools were also explored, connecting Lambda's event-driven model to real-world cost savings. 

---

## Decisions Made

- **Lambda** chosen as the primary serverless compute service to teach, due to its event-driven model, cost efficiency, and 200-service integration capability. 
- **Python** selected as the primary runtime language for Lambda functions, identified as the most popular supported language. 
- **Terraform** used for infrastructure provisioning rather than manual console setup, reinforcing DevOps automation principles. 
- **Mumbai / US-East-1** regions used for deployments, consistent with prior class guidance. 
- Students directed to **destroy resources** after the practical to avoid unnecessary charges. 
- Students who had not completed the previous Terraform static website practical were advised they could not proceed with this dependent practical. 

---

## Core Concepts Taught

### What is AWS Lambda?

- **Serverless compute service** that runs code without provisioning or managing underlying infrastructure. 
- The term "serverless" means no physical server setup is required by the user; AWS manages backend servers transparently. 
- Launched in **2014** — the first major serverless computing service globally. 
- Analogy used: Lambda is like a **5-star hotel** — everything is ready-made, but you only book it for an occasion, not 24/7. 

### Key Lambda Facts

- **Pay-per-use:** Billing in **milliseconds** — charged only when code runs, never for idle time. 
- **Free tier:** Up to **1 million requests per month** at no cost. 
- **Auto-scaling:** Scales from **0 to 10,000+ concurrent executions** automatically, with no configuration required. 
- Supports **20+ programming languages**; Python is the most commonly used. 
- Integrates with **200 AWS services**. 
- Runs over **1 trillion executions per month** globally (used by Netflix, Airbnb, Coca-Cola, etc.). 
- Available in **30 regions** across **100 data centers**. 
- **15-minute execution limit** per invocation — not suitable for long-running heavy workloads. 

### When to Use Lambda vs. EC2

- **EC2** is appropriate for continuous, 24/7 workloads; you pay for the full uptime regardless of usage. 
- **Lambda** is optimal for **occasional, event-triggered tasks** — e.g., processing resumes once per day, resizing images on upload. 
- Key interview point: Lambda is for **cost optimization** when a server is only needed for seconds or minutes, not round-the-clock. 
- Migration from EC2 to Lambda is possible when the use case is identified as occasional; Python code logic remains largely the same, but Lambda-optimized configuration is required. 

### How Lambda is Triggered

- Lambda does not run on its own — it requires a **trigger** (event source). 
- Triggers can be configured from **200 AWS services**, including S3, CloudWatch, EventBridge, Alexa, CloudFront, and more. 
- Example trigger types from S3: **PUT, COPY, DELETE, permanent delete** — any S3 event can invoke Lambda. 
- Scheduling is also possible via **CloudWatch / EventBridge** (e.g., run at 8 AM daily). 
- Manual invocation via the AWS console is also available. 

---

## Cost Optimization Module

### Understanding AWS Billing

- Students were walked through the **AWS Billing console** (root user login required). 
- Instructor's own bill reviewed: EC2 charges of **$0.21**, total bill of **$18**, attributed to running EC2, VPC, and Kubernetes resources. 
- First step in cost optimization: **understand your bill** — identify which services are consuming cost. 

### AWS Cost Management Tools (6 Key Tools)

1. **AWS Cost Explorer** — Visualizes and analyzes cost by service and time period; supports forecasting. 
2. **AWS Budgets** — Set spending thresholds; alerts triggered when budget is approached or exceeded. 
    - Analogy: Like setting a ₹2 crore construction budget and getting alerted when spending hits ₹70 lakhs. 
3. **AWS Cost Anomaly Detection** — Detects unexpected spending spikes using ML pattern recognition; sends alerts via SNS/email. 
4. **AWS Compute Optimizer** — Analyzes resource utilization and recommends **right-sizing** (e.g., flags oversized EC2 instances). 
5. **Cost Optimization Hub** — Centralized dashboard aggregating recommendations across all accounts. 
6. **AWS Trusted Advisor** — Reviews architecture for unnecessary or wasteful resource usage; recommends decommissioning unused services. 

### Cost Optimization Principles

- **Discover waste:** Identify idle or oversized resources (e.g., a dedicated GPU that's never used). 
- **Right-sizing:** Match resource size to actual usage — if CPU/memory consumption is 60–80%, consider upsizing; if barely used, downsize. 
- **Pricing models:** Choose between **on-demand, savings plans, reserved instances, or spot instances** based on workload duration. 
- **Networking costs:** NAT Gateway data transfer is **heavily chargeable** — avoid unnecessary cross-region or private-to-private transfers through NAT. 
- **Storage optimization:** Databases, storage, and networking components can all be optimized. 
- **Governance:** Cost decisions should align with audit and governance requirements in enterprise environments. 

---

## Hands-On Project: Image Resizing Pipeline

### Architecture Overview

The pipeline demonstrates Lambda's event-driven model with the following flow: 

**User uploads image → S3 (non-resized bucket) → Lambda triggered → Image resized using Pillow (Python) → Output saved to S3 (resized bucket) → CloudWatch logs event → SNS sends email notification**

### Components Used

|             Service             |                       Role                        |
|---------------------------------|---------------------------------------------------|
| **S3 (Bucket 1 — non-resized)** | Receives original uploaded images                 |
| **S3 (Bucket 2 — resized)**     | Stores processed, reduced-size images             |
| **AWS Lambda (Python)**         | Executes image resizing logic on trigger          |
| **Pillow (Python library)**     | Python image processing library for resizing      |
| **SNS**                         | Sends email notification upon task completion     |
| **CloudWatch**                  | Captures logs, duration, memory usage, and errors |
| **Terraform**                   | Provisions all infrastructure as code             |
| **IAM**                         | Role and policy for Lambda execution permissions  |

### Why Two S3 Buckets?

- **Bucket 1** holds original (large) images and triggers the Lambda function. 
- **Bucket 2** stores the resized output — keeps processed files separate from originals. 
- Storage cost is the same whether files are in one or two buckets; the separation is architectural, not cost-driven. 

### Terraform Provisioning (9 Resources Created)

The `terraform apply` command automatically created: 

1. IAM Role for Lambda
2. IAM Policy attachment
3. Lambda function (Python runtime)
4. Lambda trigger permission
5. S3 Bucket 1 (non-resized)
6. S3 Bucket 2 (resized)
7. S3 event notification integration
8. SNS Topic
9. SNS Subscription

### Practical Steps Followed

1. **Clone repo** from GitHub using `git clone`. 
2. **Create** **`terraform.tfvars`** file with personal values: access key, secret key, name, email address. 
3. Run `terraform init` → backend configured locally. 
4. Run `terraform validate` → syntax check passed. 
5. Run `terraform plan` → preview of 9 resources to be created. 
6. Run `terraform apply` → type `yes` to confirm; all resources created. 
7. **Upload a PNG image** to the non-resized bucket via AWS console. 
8. **Verify output:** Resized image appears in the second bucket (396KB → 223KB confirmed). 
9. **Check CloudWatch logs** for execution details: duration, memory used, billing milliseconds. 
10. **Confirm SNS subscription** via email to receive notifications. 
11. Run `terraform destroy` to clean up all resources. 

### Observed Results

- Image successfully reduced from **396KB to 223KB** after Lambda execution. 
- CloudWatch logs showed: request ID, **bill duration**, memory consumed (~133MB used), initialization time (~569ms). 
- Lambda charges applied only for the execution duration — not for idle time. 

---

## GitHub Diagram Feature (Bonus Tip)

- Replace `github.com` with `gitdiagram.com` in any GitHub repo URL to auto-generate a **visual architecture diagram** of the codebase. 
- Also supports `explain me` and backend file exploration directly in the browser. 
- Works for any GitHub repository. 

---

## Interview Talking Points

### How to Describe This Project in an Interview

Instructor modeled the following narrative structure: 
> 
> *"In my organization, we had an occasional image processing requirement. Instead of keeping a 24/7 EC2 instance running, we migrated the workload to AWS Lambda. We automated the entire infrastructure using Terraform — provisioning two S3 buckets, a Lambda function, SNS topic, and IAM roles. When a user uploads an image to the source bucket, Lambda triggers automatically, resizes the image using Python's Pillow library, saves it to a destination bucket, and sends an email notification via SNS. This resulted in approximately 60–70% cost savings by eliminating idle server charges."*

### Key Interview Phrases

- "We used Lambda for **occasional, event-driven processing** rather than maintaining 24/7 infrastructure." 
- "Lambda **auto-scales from 0 to 10,000+ concurrent executions** without any configuration." 
- "Billing is in **milliseconds** — we only pay when the function runs." 
- "We integrated Lambda with **S3 triggers, SNS notifications, and CloudWatch logging**." 
- "This was a **cost optimization initiative** — we eliminated idle EC2 costs." 
- For cost tools: reference **Cost Explorer, Budgets, Anomaly Detection, Compute Optimizer, Cost Optimization Hub, Trusted Advisor**. 

---

## Troubleshooting & Issues Encountered

- **Capital letters in bucket names:** S3 bucket names must be **lowercase** — students using uppercase characters in their name variable received errors. 
- **403 IAM permission error:** Lambda function creation failed for some students due to insufficient IAM permissions on their access key user — resolved by attaching **AdministratorAccess** policy. 
- **Region restriction (US-East-1):** One student (Mandhir) received a 403 API error in US-East-1 and resolved it by switching regions. 
- **SNS subscription not confirmed:** Students who did not click **"Confirm Subscription"** in their email did not receive notifications — subscription remains in "pending confirmation" state until confirmed. 
- **Wrong email in** **[variables.tf](https://variables.tf)****:** Some students had incorrect email addresses in their Terraform variables file, causing notification failures. 
- **Terraform not set up:** Students who skipped the previous Terraform practical could not complete this dependent exercise. 
- **File format for Lambda trigger:** Only image files (PNG, JPG) trigger the resize function correctly; uploading Word or video files will cause Lambda to fail — logs in CloudWatch will show the error. 

---

## Pending Confirmation

- Recording of the previous **mock interview session** to be uploaded — instructor confirmed it would be available on the same link. 
- Students who did not complete the **static website Terraform practical** need to watch the recording and complete it before attempting this project. 
- Students experiencing **SNS pending confirmation** need to locate the AWS notification email (check spam) and click the confirmation link before re-uploading an image. 

---

## Action Items

- **All students:** Clone the provided GitHub repo, configure `terraform.tfvars` with personal credentials, and complete the image resizing pipeline practical independently. 
- **All students:** Run `terraform destroy` after completing the practical to avoid charges. 
- **All students:** Record a **2-minute spoken summary** of the project in the WhatsApp group to practice interview articulation. 
- **Students missing previous practical:** Watch the Terraform static website recording and complete it as a prerequisite. 
- **Abhinav / students with SNS issues:** Search inbox/spam for "AWS Notification," confirm the subscription link, then re-upload an image to test the full flow. 
- **Sunil:** Attach **AdministratorAccess** to the IAM user being used for Terraform; recreate access key if deleted. 
- **Rajesh:** Join the correct WhatsApp group (Batch 45) via the number shared in session. 

---

## Open Questions

- **Lambda for infra teams (Shankar):** Use case clarified — Lambda can automate backup/restore triggered by tickets or schedules, eliminating the need for a 24/7 server to run cron jobs. 
- **Migrating EC2 workloads to Lambda:** Feasible when the workload is occasional; Python code logic stays the same, but Lambda-specific configuration and testing are required. 
- **SMS notifications via SNS:** Possible but requires OTP verification and subscription approval; organizational-level restrictions apply. 
- **Non-billable AWS services:** Students directed to check `aws.amazon.com/free` and AWS documentation for the full free-tier list; IAM and S3 (within limits) are free, SNS charges ~₹1 per notification. 
- **Monthly AWS project costs:** Vary widely by project — can range from $1 to $10,000+/month; students advised to state their specific project cost confidently in interviews. 
- **Uploading large images via front-end:** Same upload mechanism applies; Lambda handles the processing regardless of how the file reaches S3.


# AWS Lambda Interview Questions, Lambda Scenarios, and AWS Cost Optimization Scenarios
## Batch-45 | Day-12: AWS Integrations + Cost Optimization | 22 October 2026

> Based on the Day-12 session notes. Answers use the concepts and project architecture covered in the session, with practical interview-ready explanations. Cost savings and prices are workload-dependent; validate estimates against actual AWS usage and current pricing.

---

# Part 1: 20 AWS Lambda Interview Questions and Answers

### 1. What is AWS Lambda?
**Answer:** AWS Lambda is a serverless compute service that runs code in response to events. AWS manages the underlying servers, so the team does not need to provision or maintain a server for each function. It is especially useful for short, event-driven tasks such as image processing, scheduled jobs, and automation.

### 2. What does “serverless” mean in AWS Lambda?
**Answer:** Serverless does not mean that servers do not exist. It means AWS manages the servers and much of the infrastructure operation for us. We focus on code, configuration, permissions, and integrations.

### 3. How is Lambda pricing different from EC2 pricing?
**Answer:** Lambda charges are mainly based on requests and execution duration, with memory configuration also affecting compute cost. EC2 generally charges for the instance while it is running. Lambda can be economical for intermittent workloads because there is no continuously running application server to pay for. Other services used by the solution can still incur charges.

### 4. When would you choose Lambda instead of EC2?
**Answer:** I would consider Lambda for short, event-driven or scheduled jobs that do not need to run continuously. EC2 may be a better fit for applications that need a continuously running process, specialized operating-system control, or workloads that exceed Lambda's execution limits.

### 5. How is an AWS Lambda function triggered?
**Answer:** A Lambda function can be invoked by supported AWS services or applications, including S3 events, EventBridge schedules, API Gateway, and other event sources. It can also be invoked manually or through the AWS SDK/CLI, subject to permissions.

### 6. Explain the S3-to-Lambda integration.
**Answer:** An S3 event notification can invoke Lambda when an object is created or another configured event occurs. In the session project, uploading an image to the source S3 bucket triggers the Python function, which resizes the image and saves the result to a separate destination bucket.

### 7. Why did the image-resizing project use two S3 buckets?
**Answer:** One bucket stores original images and the other stores resized outputs. This separates source files from processed files and makes the flow easier to manage. The separation is architectural; using two buckets does not automatically reduce storage cost.

### 8. What is the role of IAM in Lambda?
**Answer:** IAM controls what a Lambda function and the person or automation deploying it are allowed to do. The function's execution role should grant only the permissions it needs, such as reading from the source bucket, writing to the destination bucket, and publishing to an SNS topic if required.

### 9. What is Amazon CloudWatch used for with Lambda?
**Answer:** CloudWatch collects Lambda logs and metrics. It helps us inspect execution details, duration, errors, memory usage, initialization time, and other operational signals. We use these logs to troubleshoot failures and understand performance and cost drivers.

### 10. How does Amazon SNS fit into the image-resizing project?
**Answer:** SNS sends a notification when the workflow reaches the configured notification step. The session used an email subscription. The recipient must confirm the subscription before email notifications can be delivered.

### 11. Why use Terraform with Lambda?
**Answer:** Terraform defines infrastructure as code, making the deployment repeatable and reviewable. In the session, Terraform created the Lambda function, IAM resources, two S3 buckets, S3 event integration, SNS topic, and subscription-related resources.

### 12. What are the main steps to deploy the session's image-resizing pipeline?
**Answer:** Configure the required variables securely, run `terraform init`, `terraform validate`, and `terraform plan`, review the plan, then run `terraform apply`. Upload a supported image, verify the output bucket, inspect CloudWatch logs, confirm the SNS email subscription, and destroy test resources when finished.

### 13. What is the maximum execution time of a Lambda invocation?
**Answer:** The session notes describe a maximum of 15 minutes per invocation. Lambda is therefore not the right choice for every long-running workload. Check the current AWS documentation and the chosen invocation model when designing a production solution.

### 14. How does Lambda scale?
**Answer:** Lambda can increase concurrent executions in response to demand, subject to account and function concurrency quotas and event-source limits. Production designs should consider concurrency limits, downstream capacity, retries, and throttling rather than assuming capacity is unlimited.

### 15. What happens if the Lambda function receives an unsupported file?
**Answer:** The function may fail if its code expects an image but receives a Word document, video, or another unsupported format. The session's function was designed for PNG/JPG inputs. Validate file type and size before processing, handle errors safely, and use CloudWatch logs to diagnose failures.

### 16. Why might an S3 upload fail to trigger Lambda?
**Answer:** Possible causes include a missing or incorrect S3 event notification, missing Lambda invoke permission, a region or configuration mismatch, an event filter that excludes the object, or uploading to the wrong bucket. Check the S3 notification configuration, Lambda resource policy, and CloudWatch logs/metrics.

### 17. What does “SNS subscription pending confirmation” mean?
**Answer:** For an email subscription, SNS sends a confirmation email. Until the recipient clicks the confirmation link, the subscription remains pending and notifications are not delivered to that email address. Check spam/junk folders and verify the configured email address.

### 18. How would you troubleshoot a Lambda 403 or access-denied error?
**Answer:** Identify which principal is denied: the deployment identity, Lambda execution role, or service-to-service invocation. Review the relevant IAM identity policy, resource policy, bucket policy, permissions boundary, and organization-level controls. Grant only the missing, required permissions rather than defaulting to broad administrator access.

### 19. How would you migrate an occasional EC2 task to Lambda?
**Answer:** First confirm the task is short-running, event-driven, and compatible with Lambda's runtime and limits. Separate the business logic from server-specific assumptions, package dependencies, configure triggers and IAM permissions, add logging/error handling, test the function, and compare the full cost—including S3, logs, notifications, and data transfer—before retiring EC2.

### 20. How would you explain the image-resizing project in an interview?
**Answer:** “I built an event-driven image-processing pipeline using S3, Lambda, SNS, CloudWatch, IAM, and Terraform. When an image was uploaded to the source bucket, an S3 event invoked a Python Lambda function that resized the image with Pillow and saved it to a destination bucket. CloudWatch captured execution logs, and SNS sent an email notification. Terraform provisioned the infrastructure, and I destroyed the test resources after validation to avoid ongoing charges.”

---

# Part 2: 20 AWS Lambda Scenario-Based Questions and Answers

### 1. Scenario: A user uploads a PNG to S3. You need to resize it automatically. How would you design it?
**Answer:** Configure an S3 object-created event on the source bucket to invoke Lambda. The Python function reads the image, resizes it using Pillow, and writes the output to a separate destination bucket. Add an execution role with least-privilege S3 permissions, CloudWatch logging, and error handling.

### 2. Scenario: The resized image triggers Lambda again and creates a loop. How do you fix it?
**Answer:** Use separate source and destination buckets, as in the session project. If one bucket must be used, configure event filters for a dedicated input prefix and write outputs to a different prefix that the trigger ignores. Avoid allowing generated outputs to match the same trigger.

### 3. Scenario: Images are uploaded, but no resized files appear. What do you check?
**Answer:** Confirm the upload went to the correct source bucket and region, verify the S3 event notification and Lambda invoke permission, inspect Lambda metrics and CloudWatch logs, and check the function's execution role for source-read and destination-write permissions.

### 4. Scenario: Lambda runs, but it fails with `AccessDenied` when reading the source image.
**Answer:** Check the Lambda execution role and the source bucket policy. Ensure the role has the required `s3:GetObject` permission for the correct bucket and object ARN. Also review any permissions boundary, SCP, or explicit deny. Do not add broad permissions without identifying the denied action.

### 5. Scenario: The function resizes an image but cannot save it to the output bucket.
**Answer:** Verify the destination bucket name, region, encryption settings, and network/configuration assumptions. Ensure the execution role has `s3:PutObject` on the correct destination object ARN and that the bucket policy permits the action.

### 6. Scenario: SNS does not send the completion email.
**Answer:** Check that the SNS topic ARN is correct, the function has permission to publish, and the email subscription is confirmed. Verify the address in Terraform variables and check inbox/spam for the confirmation message. Review CloudWatch logs for publish errors.

### 7. Scenario: CloudWatch shows a timeout while processing large images.
**Answer:** Measure execution duration and image dimensions. Optimize the image-processing logic, avoid unnecessary copies, and select suitable memory and timeout settings. If processing is too long or the workload is not compatible with Lambda's limits, use a different architecture such as a queue-backed worker service.

### 8. Scenario: A Lambda function is invoked thousands of times in a short period.
**Answer:** Check concurrency, throttling, duration, and downstream S3/database/API capacity. Set appropriate reserved concurrency if protecting downstream systems is necessary, and use queues such as SQS when buffering and controlled processing rates are useful. Configure retry and failure handling to avoid losing events.

### 9. Scenario: A function works in the console but fails after Terraform deployment.
**Answer:** Compare the deployed runtime, handler, environment variables, package/dependencies, IAM role, region, and trigger configuration with the console test. Review the Terraform plan and CloudWatch logs. Make the fix in Terraform and redeploy so the configuration remains reproducible.

### 10. Scenario: `terraform apply` fails because an S3 bucket name contains uppercase letters.
**Answer:** S3 bucket names must follow S3 naming rules and use lowercase letters. Update the bucket-name variable to a globally unique, valid lowercase name, run `terraform plan`, and apply again.

### 11. Scenario: Terraform cannot create a Lambda function due to a 403 error.
**Answer:** Identify the exact denied API action and the identity used by Terraform. Check the deployment user's IAM policy, permission boundaries, organization SCPs, and region restrictions. Add the minimum required permissions and retry. Avoid using `AdministratorAccess` as a permanent fix.

### 12. Scenario: Students deploy the pipeline in a region where a required setup fails.
**Answer:** Check that the required services and configuration are supported in that region, and confirm the provider region in Terraform matches the intended deployment. If appropriate, test in another approved region and review any data residency or governance requirements first.

### 13. Scenario: A Word document is uploaded to the image input bucket.
**Answer:** The image-processing code may fail because the file is not a supported image. Add file-extension and content validation, reject unsupported formats gracefully, and log the reason. Do not assume the extension alone proves the content type.

### 14. Scenario: The same S3 event appears to be processed more than once.
**Answer:** Design the function to be idempotent. Use deterministic output keys, detect whether the object version or event has already been processed, and avoid duplicate side effects. Event-driven systems can deliver duplicate notifications, so do not assume every event is delivered exactly once.

### 15. Scenario: You need a Lambda function to run every day at 8 AM.
**Answer:** Create an EventBridge scheduled rule or schedule to invoke the function at the required time. Confirm the schedule's time-zone behavior and configure the correct IAM permission. Add monitoring and failure notifications. If the requirement is local time, explicitly account for the schedule's time-zone configuration.

### 16. Scenario: Your team runs a backup script on an EC2 instance every night, but the script takes only two minutes.
**Answer:** Assess whether the script can run safely as a Lambda function triggered by EventBridge. Move secrets to an appropriate secrets service, configure IAM access to the backup target, and add logging and alerts. Check the execution-time, storage, networking, and dependency limits before migrating.

### 17. Scenario: Lambda's memory is set very high, but the function uses little memory and runs briefly.
**Answer:** Use CloudWatch metrics and repeatable tests to measure duration and memory use. Test suitable memory settings and compare total billed compute cost and performance. Do not automatically choose the lowest memory: additional memory can provide more CPU and sometimes reduce total cost.

### 18. Scenario: Lambda errors rise after a new code deployment.
**Answer:** Compare errors, duration, and initialization metrics before and after deployment. Inspect logs and dependency/package changes, test with representative events, and roll back to a known-good version if needed. Use versions and aliases or a controlled deployment strategy to reduce release risk.

### 19. Scenario: A developer asks to put AWS access keys directly in `terraform.tfvars` and commit the file.
**Answer:** Do not commit secrets to source control. Use an approved credential chain or secret-management approach, exclude local secret files from Git, rotate any exposed keys, and restrict access to state files because Terraform state can contain sensitive values. Prefer short-lived credentials where possible.

### 20. Scenario: The project works, and the class is finished. What should you do to prevent unwanted charges?
**Answer:** Run `terraform destroy` from the correct workspace and directory after confirming that the resources are safe to remove. Review the plan before approving destruction, verify resources were deleted, and check for leftovers such as logs, objects, snapshots, or resources created outside Terraform.

---

# Part 3: 20 AWS Cost Optimization Scenario-Based Questions and Answers

### 1. Scenario: Your AWS bill is higher than expected, but you do not know which service caused it. What is your first step?
**Answer:** Open AWS Cost Explorer and group costs by service, region, usage type, and time period. Compare the current period with previous periods, then inspect the detailed billing data for the largest increases. Start with evidence from the bill rather than deleting resources blindly.

### 2. Scenario: You want to be notified when monthly AWS spending approaches ₹10,000.
**Answer:** Create an AWS Budget with a monthly cost budget of ₹10,000 and configure alerts at useful thresholds, such as 80% and 100%. Send notifications through email or SNS. Remember that budget alerts are not necessarily real-time and do not automatically stop resources unless you build a separate control.

### 3. Scenario: Your AWS bill suddenly jumps overnight.
**Answer:** Use Cost Anomaly Detection to identify unusual spending patterns and configure alerts to the responsible team. Then use Cost Explorer and service-level metrics to investigate the source—for example, an unexpected compute workload, data transfer, or storage growth. Treat the alert as a starting point for investigation, not a root-cause diagnosis.

### 4. Scenario: Several EC2 instances have low CPU utilization for weeks.
**Answer:** Use CloudWatch metrics and AWS Compute Optimizer recommendations to review CPU, memory where available, network, and workload patterns. Right-size instances or stop non-production instances outside working hours where safe. Validate performance and availability requirements before making changes.

### 5. Scenario: A development EC2 instance is used only from 9 AM to 6 PM on weekdays.
**Answer:** Automate start/stop scheduling outside working hours if the workload can be stopped safely. Check dependencies, local data, and whether the instance must be available for background jobs. Calculate savings from the hours avoided, while accounting for storage and other resources that continue to incur charges.

### 6. Scenario: An EC2 workload runs 24/7 with stable, predictable usage.
**Answer:** Compare On-Demand pricing with Savings Plans or Reserved Instances, as appropriate for the service and commitment model. Use historical usage and a forecast before committing. Keep flexible or variable workloads on a suitable on-demand model when commitment would add risk.

### 7. Scenario: A batch job can be interrupted and restarted without losing work.
**Answer:** Evaluate Spot Instances for eligible compute workloads because they can be cheaper than On-Demand capacity but may be interrupted. Build checkpointing, retries, and interruption handling. Do not use Spot for workloads that cannot tolerate interruption without a fallback plan.

### 8. Scenario: Your team launched a GPU instance for testing, but it is mostly idle.
**Answer:** Confirm whether the GPU is actually needed, then stop or terminate the instance when unused, or choose a smaller/appropriate instance for the task. Check attached storage and other resources that may continue to cost money after stopping the instance.

### 9. Scenario: Your Lambda image-resizing function is used only a few times per hour, but the team keeps an EC2 server running 24/7 for the same task.
**Answer:** Evaluate migrating the task to Lambda if it fits Lambda's duration, runtime, and resource limits. Compare full solution costs, including requests, duration, memory, S3, CloudWatch, SNS, and data transfer. The session's architecture illustrates how event-driven processing can avoid paying for an idle application server.

### 10. Scenario: A Lambda function runs very frequently and takes longer than expected.
**Answer:** Use CloudWatch to inspect invocation count, duration, errors, and memory use. Optimize code and dependencies, tune memory based on measured performance, and consider batching or event filtering where appropriate. Estimate cost using actual request and duration patterns rather than assuming Lambda is always cheaper than EC2.

### 11. Scenario: Your bill includes unexpected NAT Gateway charges.
**Answer:** Review NAT Gateway hourly charges and data processing, route tables, traffic volume, and data paths. Where secure and appropriate, reduce unnecessary NAT traffic, use suitable VPC endpoints for supported AWS services, and avoid needless cross-AZ or cross-region data movement. Validate architecture and security before changing routes.

### 12. Scenario: An application transfers large amounts of data between AWS regions.
**Answer:** Identify why cross-region transfer is required and measure the data volume and transfer charges. Keep data and compute close when latency, resilience, and compliance allow; reduce duplicate transfers and cache or compress data where appropriate. Do not remove cross-region replication if it is a required recovery or compliance control.

### 13. Scenario: An S3 bucket stores years of old logs and images that are rarely accessed.
**Answer:** Review access patterns and retention requirements. Apply S3 Lifecycle policies to transition eligible objects to lower-cost storage classes or expire data that is no longer required. Check retrieval fees, minimum storage-duration charges, and restore times before moving data.

### 14. Scenario: Your application keeps multiple copies of the same processed images.
**Answer:** Inspect object keys, versions, lifecycle settings, and application behavior. Remove duplicates only after confirming they are not needed, and set lifecycle or retention policies where suitable. In the session project, separate source and output buckets helped organize the workflow, but two buckets alone do not reduce storage costs.

### 15. Scenario: You have unused EBS volumes left after terminating EC2 instances.
**Answer:** Use inventory and billing data to find unattached volumes, snapshots, and other orphaned resources. Confirm ownership and backup requirements before deleting them. Track resources with tags so teams can identify the owner and purpose before cleanup.

### 16. Scenario: Your database instance is much larger than its actual workload requires.
**Answer:** Review database metrics, performance requirements, storage, connections, and peak periods. Test a smaller supported instance or suitable database configuration in a safe environment, then right-size with a rollback plan. Do not optimize cost at the expense of required availability or latency.

### 17. Scenario: The team cannot tell which project or department owns the AWS spend.
**Answer:** Establish a tagging standard for application, environment, owner, and cost center. Apply tags consistently and use Cost Explorer and cost allocation reporting to break down spending. Define ownership for shared services where a single resource supports multiple teams.

### 18. Scenario: Leadership wants one dashboard of savings opportunities across multiple AWS accounts.
**Answer:** Use Cost Optimization Hub to consolidate eligible recommendations, with the appropriate account and organization setup. Use AWS Trusted Advisor and Compute Optimizer as complementary sources for resource and architecture recommendations. Assign owners, prioritize by estimated impact and effort, and track implementation rather than just collecting recommendations.

### 19. Scenario: You receive a cost alert, but no one acts on it.
**Answer:** Route budget and anomaly alerts to a monitored team channel or email distribution list, define an owner and response time, and document an escalation path. For important workloads, build a review process that connects the alert to Cost Explorer, resource metrics, and a remediation ticket. Alerts are useful only when someone investigates and acts.

### 20. Scenario: Students finish the Terraform lab but leave resources running.
**Answer:** Make cleanup part of the lab checklist. Run `terraform destroy` after validating the output, inspect the plan before approving, and verify the resources were removed. Check for leftover S3 objects, CloudWatch log groups, snapshots, or manually created resources. Set a budget alert for the lab account and never assume all resources are free.

---

# Quick Revision: Cost Optimization Tools from the Session

| Tool | What it helps you do |
|---|---|
| AWS Cost Explorer | Analyze historical costs by service/time and forecast spending |
| AWS Budgets | Set spending thresholds and receive alerts |
| AWS Cost Anomaly Detection | Identify unusual spending patterns and alert the team |
| AWS Compute Optimizer | Get resource right-sizing recommendations |
| Cost Optimization Hub | View cost-saving recommendations in one place |
| AWS Trusted Advisor | Find potential waste and review architecture/resource recommendations |

# Quick Revision: Image-Resizing Project Flow

1. User uploads a PNG/JPG to the source S3 bucket.
2. S3 sends the configured object-created event to Lambda.
3. Python Lambda uses Pillow to resize the image.
4. Lambda writes the processed image to the destination S3 bucket.
5. CloudWatch records logs and execution metrics.
6. SNS sends an email notification after the configured step; the subscription must be confirmed.
7. Terraform manages the infrastructure, and `terraform destroy` removes lab resources when no longer needed.

# Interview Reminders

- Explain the **problem**, **architecture**, **your contribution**, **security**, **monitoring**, and **measurable result**.
- The session notes observed an image changing from **396 KB to 223 KB**. This is a result from the lab, not a guarantee for every image.
- The session's interview example estimated **60–70% cost savings** by avoiding an idle EC2 server. Present this as an example estimate, not a universal or independently verified saving. In a real project, show the before/after bill and workload assumptions.
- AWS prices, free-tier eligibility, service limits, and quotas can change. Verify current details in AWS documentation and the Pricing Calculator before making production estimates.
- Never commit AWS access keys or secrets to Git. Use least-privilege IAM and an approved credential-management method.

