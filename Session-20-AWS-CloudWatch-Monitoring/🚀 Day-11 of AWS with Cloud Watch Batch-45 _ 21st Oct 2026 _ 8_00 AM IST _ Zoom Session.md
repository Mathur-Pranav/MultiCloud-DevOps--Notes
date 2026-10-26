## Key Outcomes

The session introduced **Amazon CloudWatch** as AWS's fully managed monitoring service, covering its core purpose, architecture, and hands-on configuration. Students learned why centralized log monitoring is essential, how to configure CloudWatch alarms with **SNS (Simple Notification Service)** for email alerting, how to create billing alarms, and how to build dashboards for multi-server visibility. The session concluded with a Q&A covering resume preparation, CloudWatch vs. CloudTrail distinctions, and billing management strategies.

---

## Decisions Made

- All hands-on practice will be performed in the **Mumbai region** only; creating resources in multiple regions is discouraged to avoid confusion and unexpected billing. 
- **Enhanced/detailed CloudWatch monitoring** must be manually enabled per service (EC2, RDS, etc.) if granular metrics are needed — basic monitoring is on by default but provides less detail. 
- The standard **threshold for CPU alerts** used across most companies is **80%**; alerts should trigger only after the issue persists for an average of **5–15 minutes** to avoid false positives from brief spikes. 
- **Auto-scaling actions** triggered by CloudWatch alarms (e.g., reboot/terminate EC2) exist as options but should **not** be used without manual verification and approval in real-world scenarios. 
- **Friday's session** will be a reverse Knowledge Transfer (KT): students prepare and present topics related to AWS to the group. 

---

## Core Concepts Covered

### Why Monitoring Is Required

- If a server goes down and is terminated, its **local logs are deleted** — without a centralized store, root cause analysis becomes impossible. 
- Managing **100+ servers** individually via SSH to check logs is not feasible; a centralized system is mandatory. 
- **Attackers** who compromise an environment often delete local logs to remove their footprint — centralized logging captures activity before deletion occurs. 
- Storing logs **locally for extended periods** (e.g., 1 year per client SLA) requires very large machines, increases cost, and can slow down the server itself. 
- **SLA compliance**: monitoring enables teams to maintain agreed service uptime thresholds (e.g., 80–90% CPU alert → action taken before SLA breach). 

### How CloudWatch Works

- CloudWatch **collects logs and metrics** from all AWS services (200+) into a centralized location. 
- Data flow: AWS services generate logs → logs stream to CloudWatch → CloudWatch creates **graphs, dashboards, and panels** → thresholds trigger **alarms** → alarms notify via **SNS**. 
- Four core functions of CloudWatch: **Collect → Monitor → Act → Analyze**. 
- CloudWatch is **100% managed by AWS**, eliminating the overhead of maintaining a separate monitoring server (e.g., open-source tools like ELK, Zabbix, Splunk, Grafana, AppDynamics can go down themselves). 
- Real-world example shared: a company moved from self-hosted observability servers to CloudWatch after their own monitoring server crashed, removing the "uptime overhead" of managing the monitoring tool itself. 

### Metrics vs. Logs

- **Metrics**: numeric measurements (CPU %, memory %, network throughput) — visualized as graphs. 
- **Logs**: detailed event records — application activity, errors, messages, and events happening inside a service. 

### CloudWatch vs. CloudTrail

- **CloudWatch**: used for monitoring resources and applications — dashboards, alarms, metrics. 
- **CloudTrail**: auditing and governance service that **records all API activity** — tracks who made changes, for security and compliance purposes. 

---

## Hands-On Demonstrations

### Enabling Enhanced Monitoring on EC2

- Navigate to **EC2 → Launch Instance → Advanced Details → Detailed CloudWatch Monitoring** → enable → extra charges apply. 
- Basic monitoring provides standard metrics; enhanced monitoring provides higher-frequency data collection (as granular as **1-second intervals** vs. standard 30-second or 5-minute intervals). 

### Enabling Monitoring on RDS

- RDS offers **Standard mode** (7-day free metric retention) and **Advanced mode** (up to 24 months, additional charges). 
- Enhanced monitoring for RDS collects data every **8 seconds** by default, configurable down to 1 second. 

### Viewing Metrics from EC2 Instance

- Inside an EC2 instance: **Monitoring tab** → panels auto-generated for CPU, network in/out, disk, credit usage. 
- Click the **three-dot menu → "View in Metrics"** to navigate directly into CloudWatch metrics for that instance. 
- Initial data may take **3–5 minutes** to appear after instance launch. 

### Creating a CPU Alarm

1. From the metrics graph, click the **bell icon** → enters CloudWatch Alarm creation. 
2. Set **threshold**: e.g., CPU utilization ≥ **80%** — a red threshold line appears on the graph. 
3. Set **period**: 5-minute average (standard); alert triggers only if the condition persists for that duration. 
4. Configure **SNS topic**: create a new topic (e.g., `Batch45-CPU-80`) with an **email endpoint**. 
5. After topic creation, AWS sends a **confirmation email** — the subscription must be confirmed before alerts are received. 
6. Once confirmed, the SNS subscription status changes from **"Pending"** to **"Confirmed"** in the SNS console. 

### How Many Alerts Fire? (Class Discussion)

- A brief spike above 80% that immediately drops = **no alert** (does not sustain for the averaging period). 
- A sustained breach above 80% = alerts fire based on the configured period; if the issue persists, alerts continue every 5–15 minutes. 
- Best practice: **3 alerts** for a sustained breach spanning multiple periods; spikes that resolve quickly generate 0 alerts. 

### Creating a Billing Alarm

- Navigate to **CloudWatch → Metrics → Billing → Estimated Charges**. 
- Set currency to **INR or USD** depending on account configuration; set threshold (e.g., ₹250 or $1). 
- Create a new SNS topic with email endpoint → confirm subscription → alarm created. 
- Billing data updates approximately every **6 hours** by default. 
- **SNS charges**: approximately **₹1 per email** notification sent. 

### Dashboards

- From EC2 monitoring tab: **three-dot menu → "Add to Dashboard"** → create or select a dashboard (e.g., `Batch45-Amrut-Dashboard`). 
- All panels (CPU, network, disk) from a machine are added to the single dashboard view. 
- For **multiple servers on one dashboard**: each server appears as a **different color line** on the same panel — no need for separate dashboards per server. 
- For **100 machines**: one dashboard, one alarm per metric type (e.g., one CPU alarm, one memory alarm) — alarms can reference multiple instance IDs. 
- Dashboards can be scoped **per project** (prod vs. dev vs. QA). 

### SNS (Simple Notification Service)

- Fully managed AWS messaging service using a **publisher-subscriber (PubSub)** model. 
- A **topic** is the message channel; **subscriptions** are the endpoints (email, SMS, Teams, etc.) that receive notifications. 
- Subscribers must **opt in** (confirm subscription) — analogous to joining a WhatsApp/email group. 
- Equivalent to **GCP Pub/Sub** in Google Cloud. 
- **NOC (Network Operations Center)** teams are the typical recipients of monitoring alerts — L1 teams monitoring 24/7 for incoming alerts. 

---

## Billing & Cost Management

- Students checked their AWS bills live; findings included:
    - Most students: **$0–$0.09/month**
    - One student (Gopal): **~₹270**
    - One student (Manoj): **~$13**
    - One student (Nithish): **$24** (left Load Balancer + RDS running for 1–2 days) 
- **Key chargeable services**: EC2 compute time, RDS, Load Balancer, ELB — all billed per hour of use. 
- **Non-chargeable items**: Security Groups (free regardless of quantity). 
- **How to read bills**: AWS Billing Dashboard → Bills → filter by month → view cost by service and region. 
- **Free Tier**: some services have free usage limits (e.g., AWS Glue: 40 requests/day free). 

### If Bill Is Unexpectedly High

- Go to **AWS Support → Create a billing support case** → explain you are a student learning AWS, request bill reversal. 
- AWS may automatically reduce or zero out the bill; allow **2–3 days** for processing. 
- Students with free-tier/POC accounts should check all regions for forgotten resources and delete them. 
- For accounts with free credits (e.g., AWS Education Builder): contact AWS support to confirm whether charges are deducted from credits. 

---

## Pending Confirmation

- Whether **INR billing alarms** can be set directly or only USD alarms are available in certain account configurations — observed inconsistency during demo. 
- Nithish's **$24 bill**: needs to verify with AWS Support whether it will be covered by free credits or requires payment. 
- Shubhendu's account: confirm whether **POC account** or main account, and whether to upgrade for additional learning capabilities. 

---

## Open Questions

- **How to add multiple VMs to a single alarm**: use **tags** (dev/QA/prod) or add individual **instance IDs** directly in the alarm configuration's add/edit option. 
- **Can CloudWatch automatically trigger an alert when CPU > 80%?** Yes — CloudWatch triggers the alarm → SNS delivers the notification → team takes manual action (or Lambda can automate further). 
- **How to test if the alerting setup is working**: create a low-threshold test alarm (e.g., 20% CPU) or artificially generate load to push CPU above the configured threshold. 
- **Custom/imported dashboard panels (like Grafana)**: CloudWatch supports customization but has fewer import options than Grafana, which was purpose-built for panel imports. 
- **Mono-repo to multiple Azure DevOps repositories**: must clone locally, then `git init` + `git add` + `git commit` + push each folder individually to its own remote repository. 

---

## Action Items

- **All students**: Delete any resources created during today's session (EC2 instances, alarms, SNS topics) to avoid unnecessary charges; recreate as needed for practice. 
- **All students**: Check all AWS regions for forgotten running resources and delete them. 
- **Students with high bills**: Raise an AWS Support billing case requesting reversal, citing student/learning status. 
- **Nithish**: Contact AWS Support to clarify whether the $24 October bill is covered by free credits. 
- **All students**: Prepare a topic related to AWS for **Friday's reverse KT session** — include a short practical demo if possible; submit before 8:00 AM Monday. 
- **Vikas**: Conduct upcoming sessions on **AWS Lambda** (next class), **Prometheus + Grafana** (manual install and configuration), and **cost optimization tips and tricks**. 
- **Vikas**: Deliver a dedicated session on **resume points and networking** (committed during session). 

---

## Upcoming Sessions

|  Session   |                            Topic                             |
|------------|--------------------------------------------------------------|
| Next class | AWS Lambda — automations and integrations with CloudWatch    |
| Upcoming   | Prometheus + Grafana — manual installation and configuration |
| Upcoming   | Cost optimization tips and tricks                            |
| Upcoming   | Resume points and networking discussion                      |
| Friday     | Reverse KT — students present AWS topics to the group        |



# AWS CloudWatch Interview Preparation
## Batch-45 — Day 11 | 21 October 2026

This guide is based on the uploaded session notes for **Amazon CloudWatch**. It contains 20 interview questions with answers and 20 scenario-based questions with suggested answers.

> Note: Answers follow the session notes. Where a detail may depend on AWS configuration or service changes, verify it in the AWS console before using it in production.

---

# Part 1: 20 Interview Questions and Answers

### 1. What is Amazon CloudWatch?
**Answer:** Amazon CloudWatch is a fully managed AWS monitoring service. It collects metrics and logs from AWS resources and applications, helps visualize them through graphs and dashboards, and can trigger alarms when configured conditions are met.

### 2. What are the four core functions of CloudWatch covered in the session?
**Answer:** **Collect, Monitor, Act, and Analyze.** CloudWatch collects monitoring data, helps teams monitor it, can trigger configured actions or alarms, and provides graphs and dashboards for analysis.

### 3. Why is centralized logging important?
**Answer:** Centralized logging keeps logs in a shared location instead of relying only on individual servers. If a server is terminated or compromised and its local logs are deleted, centrally stored logs can still help with troubleshooting, security investigation, and root-cause analysis.

### 4. What is the difference between a metric and a log?
**Answer:** A **metric** is a numeric measurement, such as CPU utilization or network throughput, usually viewed as a graph. A **log** is a detailed record of events, messages, application activity, or errors.

### 5. What is the difference between CloudWatch and CloudTrail?
**Answer:** CloudWatch is mainly used to monitor resources and applications through metrics, logs, dashboards, and alarms. CloudTrail records AWS API activity and helps answer auditing questions such as who performed an action or changed a resource.

### 6. What is a CloudWatch alarm?
**Answer:** A CloudWatch alarm evaluates a metric against a configured condition or threshold. For example, it can enter an alarm state when average EC2 CPU utilization remains at or above 80% for the configured evaluation period.

### 7. How can CloudWatch send email alerts?
**Answer:** Configure a CloudWatch alarm to publish a notification to an Amazon SNS topic, then add an email subscription to that topic. The recipient must confirm the subscription before email notifications can be received.

### 8. What is Amazon SNS, and how does it fit into CloudWatch alerting?
**Answer:** Amazon Simple Notification Service (SNS) is a managed publish-subscribe messaging service. A topic acts as the message channel, and subscriptions define the endpoints that receive messages. CloudWatch alarms can publish notifications to an SNS topic.

### 9. Why must an SNS email subscription be confirmed?
**Answer:** Confirmation verifies that the email recipient wants to receive messages from the topic. Until confirmation, the subscription remains pending and the recipient may not receive alarm notifications.

### 10. What is the difference between basic and detailed EC2 monitoring in the session notes?
**Answer:** Basic monitoring is enabled by default and provides standard metric collection. Detailed monitoring provides more frequent metric data and must be enabled where needed; additional charges may apply. Check the current EC2 settings and pricing for the exact collection interval and cost.

### 11. How do you create a CPU utilization alarm for an EC2 instance?
**Answer:** Open the instance's Monitoring tab or locate its metric in CloudWatch, start alarm creation, select CPU utilization, set a threshold such as 80%, configure the period and evaluation settings, and select an SNS notification topic. Confirm the SNS email subscription and test the alert.

### 12. Why should an alarm evaluate a condition over time instead of alerting on every brief spike?
**Answer:** A short CPU spike may be normal and may resolve without action. Requiring a condition to persist for a configured period can reduce noisy or false-positive alerts. The right period depends on the application and operational requirements.

### 13. What is a CloudWatch dashboard?
**Answer:** A dashboard is a customizable view of monitoring widgets such as CPU and network graphs. It can bring metrics from multiple resources into one place, making it easier to monitor a project or environment.

### 14. Can multiple EC2 instances be monitored on one dashboard?
**Answer:** Yes. Add metric widgets for multiple instances to a shared dashboard. Metrics can be shown together for comparison, or separated into widgets based on operational needs.

### 15. How can you monitor many servers without opening an SSH session to each one?
**Answer:** Use centralized CloudWatch metrics, logs, dashboards, and alarms. This gives the operations team a shared view of resource health and helps scale monitoring across many servers.

### 16. What is a CloudWatch billing alarm?
**Answer:** A billing alarm monitors estimated charges using the available billing metrics and sends a notification when the configured threshold is crossed. In the session, the workflow used CloudWatch billing metrics and an SNS email topic. Billing data may be delayed, so the alarm is not a real-time spending control.

### 17. How often does billing data update according to the session notes?
**Answer:** The notes say estimated billing data updates approximately every six hours by default. Treat this as an approximate interval, not a guarantee, and check the AWS Billing console for current behavior.

### 18. What should you check if CloudWatch metrics do not appear immediately after launching an EC2 instance?
**Answer:** Allow a few minutes for initial data to appear, as discussed in the session. Confirm that you are viewing the correct instance, region, metric, and time range, and verify that the required monitoring configuration is enabled.

### 19. Why should automatic actions such as rebooting or terminating an instance be used carefully?
**Answer:** An alarm-driven action can affect production availability or cause data loss. The session recommendation is to use manual verification and approval before taking disruptive actions in real-world environments.

### 20. How do you investigate an unexpectedly high AWS bill after a lab?
**Answer:** Open Billing and Cost Management, review the bill by service and region, and look for resources such as EC2 instances, RDS databases, and load balancers that may still be running. Check all regions, stop or delete unneeded resources, and contact AWS Support through a billing case if the charge appears unexpected. Do not assume a reversal or credit is guaranteed.

---

# Part 2: 20 Scenario-Based Questions and Answers

### Scenario 1: EC2 CPU stays above 80%
**Question:** A production EC2 instance has average CPU utilization above 80% for several evaluation periods. How would you configure alerting?

**Suggested answer:**
1. Select the instance's CPU utilization metric in CloudWatch.
2. Create an alarm with a threshold appropriate to the workload, using 80% as the session example.
3. Configure the evaluation period so a brief spike does not trigger unnecessary alerts.
4. Send alarm notifications to an SNS topic with a confirmed email subscription.
5. Investigate the process, workload, traffic, and application logs before deciding on remediation.

### Scenario 2: CPU spikes briefly and returns to normal
**Question:** CPU reaches 90% for a few seconds but quickly drops. The team does not want an alert for every spike. What would you do?

**Suggested answer:** Configure the alarm's period and evaluation settings so the condition must persist long enough to matter. Test the behavior against expected traffic patterns. Avoid using a threshold alone without considering duration and evaluation rules.

### Scenario 3: SNS subscription remains pending
**Question:** The CloudWatch alarm changes state, but no email arrives. The SNS subscription status is `Pending`. What is the likely cause and fix?

**Suggested answer:** The email recipient has not confirmed the SNS subscription. Find the SNS confirmation email, follow its confirmation link, and verify that the subscription status becomes `Confirmed`. Then test the alarm notification path.

### Scenario 4: EC2 instance is terminated and its logs are gone
**Question:** An EC2 instance was terminated during an incident, and its local logs are no longer available. How could the team avoid this problem in the future?

**Suggested answer:** Configure centralized log collection so important logs are sent to CloudWatch Logs or another approved central logging destination. Define suitable retention and access controls, and verify that logs are actually arriving before relying on them during an incident.

### Scenario 5: The company manages 100 EC2 instances
**Question:** Engineers currently SSH into each of 100 servers to check CPU and network health. How would you improve the process?

**Suggested answer:** Use CloudWatch metrics and a shared dashboard to view instances centrally. Create useful alarms for key metric types and group views by environment or project. Use logs for detailed troubleshooting rather than manually checking every machine.

### Scenario 6: You need to know who changed a security setting
**Question:** A security group rule changed unexpectedly. Should you start with CloudWatch or CloudTrail?

**Suggested answer:** Start with CloudTrail because the main question is who performed the AWS API action and when. CloudWatch is useful for resource monitoring and alarms, but it is not the primary API audit trail.

### Scenario 7: A CloudWatch graph is empty just after launch
**Question:** An engineer launches an EC2 instance and sees no metric data immediately. What should they check?

**Suggested answer:** Wait a few minutes for initial data, then confirm the correct AWS region, instance, metric, and graph time range. Check monitoring settings and whether the metric is expected to be available for that resource and configuration.

### Scenario 8: The team needs more granular EC2 metrics
**Question:** Standard monitoring does not provide enough detail for troubleshooting short-lived performance spikes. What is the next step?

**Suggested answer:** Review and enable detailed monitoring for the EC2 instance if the additional granularity is needed. Confirm the current collection interval, pricing, and metric availability in AWS documentation and the console before enabling it in production.

### Scenario 9: A team wants one dashboard for dev, QA, and production
**Question:** Different teams want visibility into their own environments without losing a shared operational view. How would you organize dashboards?

**Suggested answer:** Create dashboards or clearly separated widgets for dev, QA, and production. Use consistent naming and include the most useful metrics for each environment. Make sure access permissions match the sensitivity of each environment.

### Scenario 10: Multiple instances must be included in an alarm
**Question:** You want a CPU alarm that covers several EC2 instances. What approach could you take?

**Suggested answer:** Configure the alarm using the supported metric or metric-math approach for the desired instances. The session discussed adding instance IDs individually or organizing resources with tags. Verify the chosen alarm's semantics: an aggregate alarm and separate per-instance alarms answer different operational questions.

### Scenario 11: A billing alarm does not notify the team immediately
**Question:** AWS usage increases, but the billing alarm email arrives later than expected. Why might this happen?

**Suggested answer:** Billing metrics are delayed and are not real-time spending controls. The session notes mention an approximate six-hour update interval. Review the billing metric, threshold, SNS confirmation, and notification setup, and use additional cost-management controls where needed.

### Scenario 12: A student sees an unexpected AWS charge
**Question:** A student notices a bill after an AWS lab. What steps should they take?

**Suggested answer:**
1. Open Billing and Cost Management and inspect costs by service and region.
2. Identify running or retained resources such as EC2, RDS, and load balancers.
3. Check all regions for forgotten resources and remove anything no longer needed.
4. Review free credits and account terms.
5. If the charge appears unexpected, open an AWS Support billing case and explain the learning-lab context. A reversal is not guaranteed.

### Scenario 13: A load balancer and RDS were left running
**Question:** A learner receives a higher bill after leaving a load balancer and RDS database running for two days. How should they respond?

**Suggested answer:** Review the bill by service and region to confirm the charges, then stop or delete resources that are no longer required, after checking whether anything depends on them. For future labs, use a cleanup checklist and verify resource deletion after each exercise.

### Scenario 14: An attacker may delete local logs
**Question:** The security team suspects an attacker could remove logs from a compromised host. What monitoring design would help?

**Suggested answer:** Send relevant logs to a centralized destination such as CloudWatch Logs, with appropriate permissions, retention, and access controls. Validate delivery and restrict who can delete or modify central logs. Centralization improves investigation but should be combined with broader security controls.

### Scenario 15: The monitoring server itself becomes unavailable
**Question:** A company runs its own monitoring server, and that server crashes during an incident. What lesson does the CloudWatch session highlight?

**Suggested answer:** A self-hosted monitoring platform introduces extra infrastructure that the team must keep available. A managed service such as CloudWatch reduces the need to operate a separate monitoring server, although teams still need to configure and validate monitoring correctly.

### Scenario 16: The team wants to reboot an instance automatically
**Question:** A CloudWatch alarm detects high CPU, and someone proposes automatically rebooting the EC2 instance. What should you consider?

**Suggested answer:** Do not enable a disruptive action without review. First determine whether high CPU actually requires a reboot; it may be caused by legitimate traffic or a process that needs investigation. Follow the team's approval process and test remediation safely before automating it.

### Scenario 17: You need to test the notification setup
**Question:** You have configured a CPU alarm and SNS email subscription but have not received a test alert. How can you validate the setup?

**Suggested answer:** Confirm that the SNS subscription is `Confirmed`, inspect the alarm's metric, threshold, period, and current state, and check its notification action. Use a safe test alarm or generate controlled load in a non-production environment. Verify each step rather than assuming the alarm has fired.

### Scenario 18: An application error appears, but CPU is normal
**Question:** Users report application errors, but the CPU dashboard looks healthy. What should you examine?

**Suggested answer:** Metrics such as CPU do not show every application problem. Inspect application logs and relevant service logs in the centralized logging view, correlate errors with timestamps, and add appropriate log-based metrics or alarms if the problem is recurring.

### Scenario 19: A company needs to keep logs for one year
**Question:** A client SLA requires long-term log retention. Is keeping all logs only on each EC2 server a good approach?

**Suggested answer:** It is risky and can consume local disk space and affect the server. Centralize logs and define a retention policy that meets the SLA, while checking storage costs, access controls, and the applicable compliance requirements.

### Scenario 20: The alarm fires repeatedly during a busy period
**Question:** A CPU alarm repeatedly notifies the NOC team during a legitimate traffic peak. How would you reduce alert noise without hiding real incidents?

**Suggested answer:** Review whether the threshold and evaluation periods match normal workload patterns. Consider sustained-breach rules, suitable notification behavior, and separate alarms for different workloads or environments. Use historical metrics to tune the alarm, and do not simply raise the threshold without understanding the risk.

---

## Quick Revision: Key Points

- **CloudWatch:** Resource and application monitoring using metrics, logs, dashboards, and alarms.
- **CloudTrail:** AWS API activity auditing.
- **Metric:** Numeric measurement; **log:** detailed event record.
- **SNS:** Delivers notifications from a topic to confirmed subscribers.
- **Alarm design:** Choose a meaningful threshold, period, and evaluation behavior.
- **Centralized logs:** Help preserve evidence and support troubleshooting when a host is unavailable.
- **Dashboards:** Bring key metrics from multiple resources into a shared view.
- **Billing:** Check costs by service and region, and look for forgotten resources in all regions.
- **Production safety:** Review and approve disruptive automated actions before enabling them.
- **Lab cleanup:** Remove unneeded EC2, RDS, load balancer, alarm, and SNS resources, and verify whether any charges may continue.

## Source

Prepared from the uploaded notes: **“Day-11 of AWS with CloudWatch — Batch-45 — 21st Oct 2026.”**
