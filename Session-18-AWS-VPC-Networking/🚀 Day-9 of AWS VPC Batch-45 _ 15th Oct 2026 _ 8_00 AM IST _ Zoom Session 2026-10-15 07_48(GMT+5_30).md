## Key Outcomes

The session provided a comprehensive, hands-on introduction to Amazon VPC, covering its architecture, core components, and underlying networking concepts. Participants created VPCs, subnets, Internet Gateways, NAT Gateways, and VPC peering connections live in the AWS console. Key networking fundamentals — OSI model layers, TCP/UDP, IPv4/IPv6, proxy types, and CIDR notation — were discussed as foundational context before diving into AWS-specific implementation. 

---

## Decisions Made

- **Production environment protection:** Mumbai region designated as production; all practice and experimentation must be done in **N. Virginia** to avoid accidental disruption. 
- **VPC components are chargeable; VPCs themselves are not:** The default VPC incurs no cost, but components like **Elastic IPs** and **NAT Gateways** do — participants advised to use the default VPC for learning where possible. 
- **CIDR /16 chosen as the standard VPC size** for real-world projects, yielding 65,536 available IP addresses (2^16); subnets further divided using /20 (4,096 IPs) as a practical partition size. 
- **Manual vs. automated VPC creation:** Both approaches demonstrated — step-by-step manual creation and the "VPC and more" wizard that auto-generates subnets, route tables, NAT gateways, and endpoints in one click. 
- **VPC peering requires non-overlapping CIDRs:** A failed peering attempt between two VPCs with the same CIDR range illustrated that overlapping IP spaces prevent peering; migration or IP reassignment is the only resolution. 
- **Transitive routing is not supported in VPC peering:** If VPC-A peers with VPC-B and VPC-B peers with VPC-C, VPC-A cannot reach VPC-C through VPC-B; direct peering or Transit Gateway is required. 
- **One-directional peering acceptance:** A peering connection accepted by one side does not automatically allow reverse communication — a separate peering must be created for bidirectional access. 

---

## Core Concepts Covered

### OSI Model & Networking Fundamentals

- **Seven OSI layers** reviewed with practical relevance: Application (Layer 7) and Transport (Layer 4) are most critical for DevOps practitioners. 
    - **Layer 7 (Application):** Handles request/response cycles — e.g., a browser requesting a website.
    - **Layer 6 (Presentation):** Encryption/decryption, TLS/SSL.
    - **Layer 5 (Session):** Establishes time-bound connections (e.g., a Zoom session).
    - **Layer 4 (Transport):** TCP/UDP data transfer.
    - **Layer 3 (Network):** Large file transfers broken into packets.
    - **Layer 2 (Data Link):** Ethernet/LAN connections between routers.
    - **Layer 1 (Physical):** Physical cable/hardware connections. 

### IPv4 vs. IPv6

- **IPv4:** 32-bit, written in decimal (e.g., `192.168.1.16`), range 0–255 per octet; address space is nearly exhausted. 
- **IPv6:** 128-bit, written in hexadecimal; created to solve IPv4 exhaustion — analogous to mobile number series expanding from 9→8→7→6 as each series fills up. 
- Most laptops carry **both IPv4 and IPv6** simultaneously, confirmed by checking Wi-Fi network settings. 
- IPv6 in production is **very rare** currently; most company servers still run IPv4. 

### TCP vs. UDP

- **TCP (Transmission Control Protocol):**
    - Connection-oriented; uses **three-way handshake** (SYN → SYN-ACK → ACK). 
    - Guarantees delivery with acknowledgements; gracefully shuts down after transmission.
    - Used for dynamic websites, persistent connections, and larger DNS queries. 
- **UDP (User Datagram Protocol):**
    - Stateless; **send-and-forget** — no acknowledgement packets.
    - Used for video streaming, heavy media transfers, and standard DNS queries where speed matters more than guaranteed delivery. 

### Proxy, Peer-to-Peer, Broadcast, Unicast

- **Forward Proxy:** Sits on the client side; used to restrict outbound access (e.g., blocking YouTube in a corporate network). 
- **Reverse Proxy:** Sits on the server side; controls and filters incoming traffic — **NGINX** is a common reverse proxy implementation. 
- **Peer-to-Peer:** Direct one-to-one connection between two hosts without intermediary. 
- **Broadcast:** One message sent to all recipients simultaneously. 
- **Unicast:** One-to-one targeted communication. 

### CIDR Notation & Subnet Math

- **Formula:** Available IPs = 2^(32 − prefix length)
    - `/16` → 2^16 = **65,536 IPs**
    - `/20` → 2^12 = **4,096 IPs**
    - `/24` → 2^8 = **256 IPs** 
- CIDR website `cidr.xyz` demonstrated live to visualize IP counts per prefix. 
- **IP address classes** (private ranges):
    - Class A: `10.x.x.x` — default CIDR /8
    - Class B: `172.16.x.x` — default CIDR /16
    - Class C: `192.168.x.x` — default CIDR /24 

---

## VPC Architecture & Components

### VPC (Virtual Private Cloud)

- A **private, isolated network** within AWS where you define IP ranges, security rules, and resource placement. 
- Analogy used: purchasing a plot of land and deciding how to partition it into public areas (hall, garden, parking) and private areas (bedrooms). 
- Default VPC exists in every AWS account and region; up to **5 VPCs** per region; multiple VPCs supported for different projects or environments. 

### Subnets

- **Partitions** of the VPC's IP space into smaller logical units. 
- **Public Subnet:** Resources accessible from the internet (e.g., EC2 application servers).
- **Private Subnet:** Resources with no direct internet access (e.g., RDS databases). 
- AWS allows **one public subnet per Availability Zone**; selecting 2 AZs yields 2 public subnets automatically. 
- Subnet size example: a /16 VPC split into /20 subnets gives ~4,096 IPs per subnet, with the remainder available for additional partitions. 

### Internet Gateway (IGW)

- Enables **internet connectivity** for the VPC; required for any public-facing resources. 
- Created independently and then **attached to a VPC** via Actions → Attach to VPC. 
- **One IGW per VPC** — a single IGW cannot be shared across multiple VPCs. 
- **Egress-Only Internet Gateway:** Used exclusively for **IPv6 outbound** connections; does not allow inbound IPv6 traffic. 

### NAT Gateway

- Allows resources in **private subnets to initiate outbound connections** to the internet (e.g., downloading OS patches for RDS) without exposing them to inbound internet traffic. 
- Must be placed in the **public subnet**; private subnet route tables point to it for internet-bound traffic. 
- Two types: 
    - **Public NAT Gateway:** Private subnet → internet.
    - **Private NAT Gateway:** Private subnet → other VPCs or on-premises networks.
- **Zonal vs. Regional scope:** Zonal NAT Gateway serves a single AZ; Regional serves the entire region — selection based on architecture requirements. 

### Route Tables

- Define **which traffic goes where** within the VPC — equivalent to **IP tables in Linux**. 
- Each subnet is associated with a route table that specifies allowed paths (e.g., private subnet → NAT Gateway → internet). 
- Linux IP tables historically used for: (1) firewall/traffic filtering, and (2) manual traffic redirection between VMs during maintenance — AWS route tables serve the analogous routing function. 

### VPC Endpoints

- Enable **private connectivity between VPC resources and AWS services** (e.g., S3) without traversing the public internet or requiring a NAT Gateway. 
- Use case: RDS backups exported directly to S3 privately via an **S3 Gateway Endpoint**. 
- Reduces cost and latency compared to routing through NAT Gateway for AWS service communication. 
- **Chargeable** component — treat all VPC components as billable. 

### DNS Settings in VPC

- **DNS Hostnames:** Assigns human-readable hostnames to VPC resources in addition to IP addresses. 
- **DNS Resolution:** Maps hostnames to IP addresses in DNS records, enabling name-based access to resources. 
- Both settings must be enabled together; disabling resolution means hostnames exist but cannot be resolved to IPs. 

---

## VPC Peering

### How It Works

- Connects **two VPCs** so resources can communicate using private IP addresses — works across accounts and regions. 
- **Requester** sends a peering request; **Acceptor** must explicitly accept it — pending until accepted. 
- File transfer between peered VPCs uses **SCP (Secure Copy Protocol)** over SSH for private IP communication. 

### Key Constraints

- **Overlapping CIDR blocks prevent peering** — if both VPCs use the same IP range (e.g., `10.0.0.0/16`), the peering request fails; resolution requires migrating resources to a new IP range. 
- **No transitive routing:** VPC-A ↔ VPC-B and VPC-B ↔ VPC-C does not allow VPC-A ↔ VPC-C; each pair needs its own peering connection. 
- **Peering is unidirectional by acceptance:** OM→Default peering being active does not mean Default→OM traffic is automatically allowed without a separate accepted connection. 

### Cross-Account Peering (Live Demo)

- Vikas created a peering request from his account targeting a participant's (Sunil's) VPC in a different account and region. 
- First attempt failed due to **CIDR overlap**; second attempt with a non-overlapping CIDR succeeded and was accepted by Sunil. 

---

## Database Access via Jump Host

- **RDS lives in the private subnet** — not directly accessible from the internet or developer laptops. 
- Standard access pattern: developer → **Jump Host (Bastion)** → RDS endpoint using tools like **MySQL Workbench** or **phpMyAdmin**. 
- For non-technical stakeholders needing read-only access: create a **read-only database user** and provide credentials via the jump host or a Windows Jump Host with GUI tools installed. 
- Alternative for fully private setups: **Windows Jump Host** with database GUI software installed, accessible via VDI in client environments. 
- Exposing RDS publicly is technically possible with strict security group restrictions (source IP whitelisting), but is **not a best practice** for production. 

---

## Cleanup Procedure

Participants instructed to delete resources in the correct dependency order to avoid errors: 

1. Delete **NAT Gateway** first (Actions → Delete NAT Gateway).
2. Wait for NAT Gateway deletion to complete (~30 seconds).
3. Delete **VPC Peering Connections** (Actions → Delete Peering).
4. Delete the **VPC** (Actions → Delete VPC).
5. Check for and release any **Elastic IPs** — these are chargeable even when unattached. 
- Default VPC: **do not delete**; no charges incurred for the VPC itself.

---

## Interview Questions Highlighted

|    Topic     |                      Question / Concept                       |
|--------------|---------------------------------------------------------------|
| VPC          | What is a VPC? Define in one line.                            |
| IGW          | What is an Internet Gateway and when do you need one?         |
| NAT Gateway  | What does NAT Gateway enable for private subnets?             |
| CIDR         | How do you calculate available IPs from a CIDR prefix?        |
| IPv4 vs IPv6 | Differences and why IPv6 was introduced?                      |
| TCP vs UDP   | Three-way handshake, use cases for each?                      |
| Proxy        | Forward proxy vs. reverse proxy — use cases?                  |
| VPC Peering  | Does it work across accounts and regions? Transitive routing? |
| VPC Endpoint | When would you use a VPC S3 endpoint instead of NAT Gateway?  |
| Route Table  | Linux equivalent? (IP tables)                                 |
| Subnets      | Definition and purpose of public vs. private subnets?         |

---

## Pending Confirmation

- **Monday vs. Tuesday next class:** Participants debated; final decision leaned toward **Monday**, but confirmation pending from batch coordinator. 
- **Vault/secrets management:** Will be covered in the **Kubernetes secrets session**, not this module. 
- **Route 53 / DNS practical session:** Requested by participants; Vikas indicated domain registration via GoDaddy would be needed as a prerequisite. 
- **VPC Lattice and Transit Gateway:** Overview requested; Vikas acknowledged the topic but deferred detailed coverage to a future session. 
- **Session 11 Q&A format:** Participant (Shubham) requested a structured question-and-answer document from the session; Vikas confirmed the AI-generated notes follow discussion content and committed to maintaining the existing format. 

---

## Action Items

- **All participants:** Switch AWS practice region to **N. Virginia** (not Mumbai) before any hands-on work. 
- **All participants:** Delete any VPC components created during the session (NAT Gateway → Peering → VPC → Elastic IPs) to avoid charges. 
- **All participants:** Read the VPC definitions and CIDR formulas shared via WhatsApp; spend time on the linked resources before the next class. 
- **All participants:** Record a **1–2 minute self-introduction video** and share in the WhatsApp group — daily practice required to build communication skills for interviews. 
- **Interested participants (Pune-based or remote):** Apply to the **Happiest Minds AWS DevOps Engineer** opening (8+ years experience, AWS CDK with TypeScript required) — send CV to Vikas for forwarding. 
- **Vikas:** Share full job description details in the **Batch 45 WhatsApp group**. 
- **Shubham:** Submit the auto-scaling + load balancer integration point as a structured note/PR to the shared repository for Vikas to verify and publish.

# AWS VPC Interview Questions & Answers
## Day 9 — Batch 45 | 15 October 2026

This guide is based on the AWS VPC and networking topics covered in the session notes: VPCs, subnets, CIDR, route tables, Internet Gateways, NAT Gateways, VPC endpoints, DNS, VPC peering, jump hosts, TCP/UDP, IPv4/IPv6, proxies, and cleanup.

> **Practice-region reminder:** The session notes designate **N. Virginia (`us-east-1`) for practice** and Mumbai as the production region. Confirm your own environment before making changes. NAT Gateways, Elastic IPs, and other networking components can incur charges.

---

# Part 1: 20 Interview Questions and Answers
## Basic to Advanced

### 1. What is an Amazon VPC?

**Answer:** Amazon Virtual Private Cloud (VPC) is a logically isolated network in AWS where we define an IP address range and deploy resources such as EC2 instances and RDS databases. We control the network design using subnets, route tables, gateways, and security controls.

**Example:** We can place a web server in a public subnet and a database in a private subnet inside the same VPC.

### 2. What is the difference between a VPC and a subnet?

**Answer:** A VPC is the overall virtual network. A subnet is a smaller IP range carved out of the VPC CIDR and associated with a single Availability Zone.

**Example:** A VPC with `10.0.0.0/16` could contain subnets such as `10.0.1.0/24` and `10.0.2.0/24`, provided the subnet ranges do not overlap.

### 3. What is CIDR notation, and how do you calculate IP addresses?

**Answer:** CIDR (Classless Inter-Domain Routing) represents an IP range using a prefix length. For IPv4, the total number of addresses is `2^(32 − prefix length)`.

| CIDR | Total IPv4 addresses |
|---|---:|
| `/16` | 65,536 |
| `/20` | 4,096 |
| `/24` | 256 |

AWS reserves five IPv4 addresses in each subnet, so the number available for resources is lower than the total. For example, a `/24` subnet has 251 assignable IPv4 addresses in AWS.

### 4. What is the difference between a public subnet and a private subnet?

**Answer:** The distinction is based mainly on routing, not just on the resource's public IP address. A public subnet has a route to an Internet Gateway. A private subnet does not have a direct route to an Internet Gateway.

**Example:** A public-facing load balancer may be placed in public subnets, while application servers and RDS databases can be placed in private subnets. A private subnet may still have outbound access through a NAT Gateway.

### 5. What is an Internet Gateway (IGW)?

**Answer:** An Internet Gateway is a horizontally scaled, redundant VPC component that allows communication between a VPC and the internet when the routing and addressing requirements are met. It must be attached to the VPC, and the relevant subnet route table needs a route such as `0.0.0.0/0 → IGW`.

**Important:** A route to an IGW alone does not make an EC2 instance reachable from the internet. The instance also needs suitable addressing and security rules.

### 6. What is a NAT Gateway, and why do we use it?

**Answer:** A NAT Gateway lets resources in private subnets initiate connections to external IPv4 destinations without allowing unsolicited inbound connections from those destinations.

**Example:** Private EC2 instances can download operating-system updates through a public NAT Gateway placed in a public subnet. The private subnet's route table sends internet-bound traffic to the NAT Gateway.

**Cost note:** NAT Gateways incur charges, so unused gateways should be removed after practice.

### 7. What is the difference between an Internet Gateway and a NAT Gateway?

**Answer:**

- **Internet Gateway:** Provides the VPC's internet connectivity path for resources with suitable routes, addresses, and security rules.
- **NAT Gateway:** Allows private-subnet resources to initiate outbound IPv4 connections without being directly reachable through that NAT path from the internet.
- A public NAT Gateway is placed in a public subnet and needs a route to an Internet Gateway for internet access.

### 8. What is a route table in AWS VPC?

**Answer:** A route table contains rules that determine the next hop for network traffic based on its destination IP range. Each subnet is associated with a route table, either explicitly or through the VPC's main route table.

**Example:**
- `10.0.0.0/16 → local` allows routing within the VPC.
- `0.0.0.0/0 → IGW` provides a default internet route for a public subnet.
- `0.0.0.0/0 → NAT Gateway` provides outbound internet routing for an appropriate private subnet.

AWS route tables perform routing; they are not equivalent to Linux `iptables`, which is primarily used for packet filtering and network address translation on a host.

### 9. What is a VPC endpoint? Why might you use an S3 Gateway Endpoint?

**Answer:** A VPC endpoint provides a private path from a VPC to supported AWS services without requiring traffic to traverse the public internet. An S3 Gateway Endpoint can route supported S3 traffic through route tables, often avoiding NAT Gateway data-processing charges for that traffic.

**Example:** A private EC2 instance can access an S3 bucket through an S3 Gateway Endpoint without needing a NAT Gateway solely for S3 access. Endpoint policies, IAM policies, bucket policies, and routes must still permit the request.

### 10. What are VPC DNS resolution and DNS hostnames?

**Answer:** DNS resolution lets resources resolve DNS names to IP addresses. DNS hostnames allows AWS to assign DNS hostnames to supported resources. These are separate VPC attributes and should be configured appropriately for the intended design.

**Example:** If an application connects to an RDS endpoint by DNS name, DNS resolution must work and the network path and security rules must also allow the connection.

### 11. What is VPC peering?

**Answer:** VPC peering connects two VPCs so that resources can communicate using private IP addresses, subject to routes and security controls. It can connect VPCs in the same or different AWS accounts and Regions.

To establish connectivity, the requester creates a peering request, the accepter accepts it, and the relevant route tables are updated on both sides.

### 12. Why can two VPCs with overlapping CIDR ranges not be peered?

**Answer:** Overlapping IPv4 CIDR ranges make routing ambiguous because the same destination address range could exist in both VPCs. VPC peering does not support this overlap.

**Example:** If both VPCs use `10.0.0.0/16`, redesign one VPC with a non-overlapping range and migrate or readdress resources as needed before peering.

### 13. Does VPC peering support transitive routing?

**Answer:** No. VPC peering is non-transitive.

If VPC-A peers with VPC-B and VPC-B peers with VPC-C, VPC-A cannot use VPC-B as a transit path to reach VPC-C. Create a direct peering connection where suitable, or consider AWS Transit Gateway for a larger hub-and-spoke design.

### 14. Does accepting a VPC peering request automatically enable traffic in both directions?

**Answer:** Accepting the request activates the peering connection, but it does not automatically configure end-to-end communication. Routes must be configured on both sides, and security groups and network ACLs must allow the traffic.

A single peering connection can support traffic initiated in either direction when routing and security controls allow it; a second peering connection is not normally required just to make the connection bidirectional.

### 15. How would you securely connect to an RDS database in a private subnet?

**Answer:** Keep the database private and connect through an approved access path, such as a bastion/jump host, VPN, or managed session access. Permit the database port in the RDS security group only from the required application or jump-host security group.

**Example:** For MySQL, allow TCP `3306` from the jump host's security group to the RDS security group. Avoid exposing RDS publicly as a default design choice.

### 16. What is the difference between TCP and UDP?

**Answer:** TCP is connection-oriented and provides reliable, ordered delivery using acknowledgements and retransmissions. Its connection setup commonly uses a three-way handshake: SYN, SYN-ACK, ACK. UDP is connectionless and does not itself guarantee delivery, ordering, or retransmission.

**Examples:** HTTPS commonly uses TCP; DNS commonly uses UDP for standard queries, but can also use TCP. Real-time media often uses UDP when lower latency is preferred.

### 17. Explain the OSI model and identify the layers most relevant to DevOps troubleshooting.

**Answer:** The OSI model has seven conceptual layers:

1. **Layer 7 — Application:** HTTP, DNS and application protocols.
2. **Layer 6 — Presentation:** Data representation and encryption concepts.
3. **Layer 5 — Session:** Session management.
4. **Layer 4 — Transport:** TCP/UDP and ports.
5. **Layer 3 — Network:** IP addressing and routing.
6. **Layer 2 — Data Link:** Ethernet and MAC addressing.
7. **Layer 1 — Physical:** Physical links and hardware.

DevOps troubleshooting often focuses on Layers 3, 4 and 7: confirm the destination IP and route, test port reachability, and then inspect the application response.

### 18. What is the difference between a forward proxy and a reverse proxy?

**Answer:** A forward proxy represents clients when they access external services. It can be used for outbound access controls and auditing. A reverse proxy sits in front of servers and receives client requests before forwarding them to backend services.

**Example:** A corporate proxy can restrict users' outbound web access. NGINX can act as a reverse proxy in front of application servers.

### 19. What is an egress-only Internet Gateway?

**Answer:** An egress-only Internet Gateway enables outbound-only internet connectivity for IPv6 traffic from a VPC. It allows instances to initiate outbound connections while preventing the internet from initiating connections back through that gateway. Security rules and routing still need to be configured correctly.

### 20. How would you design a highly available VPC for a web application and database?

**Answer:** A common design uses at least two Availability Zones:

- Public subnets for an internet-facing load balancer.
- Private application subnets for application instances.
- Private database subnets for RDS, configured with appropriate subnet groups and security controls.
- Route tables separated by subnet purpose.
- NAT Gateway capacity and routes planned per Availability Zone where required for availability.
- VPC endpoints for suitable AWS service traffic, such as S3.
- Security groups that allow only required flows, plus monitoring and logging.

The final design should consider availability, security, traffic flow, and cost rather than simply placing everything in public subnets.

---

# Part 2: 20 Scenario-Based Questions and Answers

### Scenario 1. An EC2 instance in a public subnet cannot be reached over SSH from your laptop. What would you check?

**Answer:**
1. Confirm the instance is running and has a reachable public IPv4 address or another approved access path.
2. Check that the subnet route table has `0.0.0.0/0 → Internet Gateway`.
3. Confirm the IGW is attached to the correct VPC.
4. Check the security group allows TCP `22` from your current public IP, not from the whole internet unless there is a justified need.
5. Check network ACL inbound and outbound rules, the OS firewall, and whether SSH is running.
6. Verify you are using the correct key and username.

### Scenario 2. An EC2 instance in a private subnet cannot download OS updates. How would you troubleshoot it?

**Answer:**
- Check whether the subnet route table sends the required IPv4 internet-bound traffic to a NAT Gateway.
- Confirm the NAT Gateway is available and located in a public subnet.
- Verify that the NAT Gateway's subnet route table has a default route to an attached IGW.
- Confirm the private instance's security group and network ACL permit the required outbound and return traffic.
- Check DNS resolution and the destination repository.
- Review NAT Gateway metrics and VPC Flow Logs if enabled.

### Scenario 3. Your AWS bill unexpectedly increased after a VPC lab. What would you inspect first?

**Answer:**
1. Check the billing and cost-management views for NAT Gateway hours and data processing.
2. Look for allocated Elastic IPs, especially addresses that are not attached to a resource.
3. Review other resources created during the lab.
4. Delete unused resources in the correct dependency order.
5. Verify the resources are actually deleted and check the bill again after usage data updates.

For the lab in the session notes, the cleanup sequence was NAT Gateway, peering connections, VPC, then checking and releasing unused Elastic IPs. Do not delete a production resource or the default VPC without authorization.

### Scenario 4. VPC peering fails because both VPCs use `10.0.0.0/16`. What is the fix?

**Answer:** The CIDR ranges overlap, so the VPCs cannot be peered. Plan a non-overlapping IP range for one VPC, then migrate or readdress resources as necessary. Re-create or update the network design and configure routes and security rules after the CIDRs no longer overlap.

### Scenario 5. VPC-A can reach VPC-B, and VPC-B can reach VPC-C, but VPC-A cannot reach VPC-C. Why?

**Answer:** VPC peering does not support transitive routing. VPC-A cannot use VPC-B as a router to reach VPC-C. Create a direct A-to-C peering connection if appropriate, or use Transit Gateway for a multi-VPC network architecture. Update the route tables and security rules for the selected design.

### Scenario 6. A peering connection shows “Active,” but an EC2 instance in one VPC cannot connect to an instance in the other. What would you check?

**Answer:**
- Ensure both VPCs have non-overlapping CIDRs.
- Add routes to the peer VPC CIDR through the peering connection in both VPCs' relevant route tables.
- Check security groups and network ACLs.
- Confirm the destination service is listening on the expected port.
- Verify OS firewalls, DNS behavior, and the target private IP.
- Use connectivity tests and VPC Flow Logs where available.

An active peering connection alone does not guarantee application connectivity.

### Scenario 7. A private EC2 instance needs to upload files to S3, but the team wants to avoid NAT Gateway charges for that traffic. What would you do?

**Answer:** Configure an S3 Gateway Endpoint and associate it with the relevant route tables. Then check the endpoint policy, IAM permissions, S3 bucket policy, and application configuration. This can keep supported S3 traffic on the AWS network path and avoid using a NAT Gateway solely for that traffic.

### Scenario 8. An application can connect to an RDS instance by IP address but fails when using the RDS endpoint hostname. What is a likely cause?

**Answer:** Investigate DNS first. Confirm VPC DNS resolution is enabled and that the client can resolve the RDS endpoint. Also check resolver configuration and whether the client is using the expected VPC/network. If DNS resolves correctly, investigate connectivity and database authentication separately.

### Scenario 9. A developer wants to connect directly from a home laptop to a private RDS database. What architecture would you recommend?

**Answer:** Keep RDS private. Use an approved access route such as VPN, a managed session connection to a bastion host, or a Windows/Linux jump host with database tools. Allow the database port only from the required security group or private network. Use individual database accounts and least-privilege permissions; use read-only access when that is all the developer needs.

### Scenario 10. A web server has a public IP and a route to the Internet Gateway, but the website is still unreachable. What might be wrong?

**Answer:** Check the full traffic path:
- Security group permits inbound traffic on the application port, such as TCP `80` or `443`.
- Network ACLs permit inbound requests and outbound response traffic.
- The web server process is running and listening on the expected interface and port.
- The OS firewall allows traffic.
- The correct public IP, DNS record, and application configuration are being used.
- The route table is associated with the intended subnet and the IGW is attached.

### Scenario 11. Your VPC uses `10.0.0.0/16`. You need several subnets and want to use `/20` for each. How many addresses are in each `/20`, and what should you consider?

**Answer:** A `/20` contains `2^(32−20) = 4,096` total IPv4 addresses. AWS reserves five IPv4 addresses per subnet, so 4,091 are assignable. Plan non-overlapping subnet CIDRs, allow room for future growth, and distribute subnets across Availability Zones according to the application design.

### Scenario 12. You created a NAT Gateway in a private subnet, but private instances still cannot access the internet. What is the design issue?

**Answer:** A public NAT Gateway should be placed in a public subnet. That subnet needs a default route to an attached Internet Gateway, and the private subnet route table should send appropriate internet-bound traffic to the NAT Gateway. Verify the NAT Gateway type and routing design, then correct the placement/routes as needed.

### Scenario 13. Two private-subnet applications need to communicate, but the connection times out. How would you troubleshoot it?

**Answer:**
1. Confirm both applications have the correct private IPs and are in networks with a valid route.
2. Check the destination service is running and listening on the expected port.
3. Check source and destination security groups.
4. Check network ACLs in both subnets, including return traffic.
5. Check host-level firewalls and application logs.
6. Use VPC Flow Logs or connectivity tools to narrow down where traffic is blocked.

### Scenario 14. An application team asks to make the RDS database publicly accessible because connecting through the jump host is inconvenient. What would you advise?

**Answer:** Recommend keeping RDS private and improving the approved access path instead, for example through VPN or managed session access to a jump host. Public accessibility increases exposure and should not be the default solution. If a public design is explicitly approved, it requires a documented risk review and tightly restricted network access, but private connectivity is generally preferred.

### Scenario 15. A private subnet needs outbound IPv6 internet connectivity, but inbound connections from the internet must be blocked. Which component is suitable?

**Answer:** Use an egress-only Internet Gateway for IPv6, with the appropriate IPv6 default route in the subnet route table. Also configure security groups and network ACLs. An egress-only Internet Gateway is for IPv6; it is not a replacement for a NAT Gateway used for IPv4 internet egress.

### Scenario 16. A user reports that HTTPS requests are failing, while ping to the server's IP address works. What would you check?

**Answer:** Ping only demonstrates that ICMP traffic may be passing; it does not prove HTTPS works. Check TCP port `443`, security group and network ACL rules, the server listener, TLS certificate and handshake, reverse proxy configuration, application health, and logs. Use a tool such as `curl -v https://hostname` and test DNS separately.

### Scenario 17. The company has 20 VPCs that need connectivity with each other and with on-premises networks. Is individual VPC peering always the best option?

**Answer:** Not necessarily. A large mesh of individual peering connections can become difficult to manage, and peering does not provide transitive routing. Evaluate AWS Transit Gateway for centralized connectivity and routing across many VPCs and on-premises networks. Choose based on routing needs, scale, security boundaries, operational complexity, and cost.

### Scenario 18. A security team wants to stop employees from accessing certain external websites. Would you use a forward proxy or reverse proxy?

**Answer:** A forward proxy is the closer fit because it controls or mediates clients' outbound requests to external services. A reverse proxy is typically used to receive inbound requests in front of servers. In a real environment, enforce policy through an approved secure web gateway/proxy and supporting network controls.

### Scenario 19. After a lab, deleting the VPC fails or AWS reports that dependencies remain. How would you proceed?

**Answer:**
- Inspect the VPC's resources and dependencies.
- Remove the NAT Gateway and wait until deletion completes.
- Delete VPC peering connections associated with the VPC.
- Check for other dependent resources, such as network interfaces, endpoints, or gateways, and remove them only if safe.
- Delete the non-default VPC once dependencies are gone.
- Check for and release unused Elastic IPs to avoid ongoing charges.

Never run cleanup commands against production resources without confirming the account, Region, and resource IDs.

### Scenario 20. Your application works from one Availability Zone but fails when traffic is routed to another AZ. What would you investigate?

**Answer:**
- Confirm the load balancer and application resources are deployed and healthy in both AZs.
- Check each subnet's route-table association and routes.
- Verify security groups, network ACLs, and application listener settings.
- Check database connectivity, DNS, and any AZ-specific dependencies.
- If private instances need outbound internet access, verify their NAT routing and availability design.
- Review load balancer target health and application logs.

For resilient production designs, distribute appropriate resources across multiple AZs and avoid relying on a single-AZ network component when the availability requirements do not allow it.

---

# Quick Revision Checklist

- [ ] Explain VPC, subnet, public subnet, and private subnet.
- [ ] Calculate IPv4 address counts for `/16`, `/20`, and `/24`.
- [ ] Explain routes to an Internet Gateway versus a NAT Gateway.
- [ ] Describe when an S3 Gateway Endpoint is useful.
- [ ] Explain DNS resolution versus DNS hostnames.
- [ ] Explain VPC peering, CIDR overlap, and non-transitive routing.
- [ ] Troubleshoot security groups, network ACLs, routes, DNS, and listeners.
- [ ] Describe secure access to RDS in a private subnet.
- [ ] Compare TCP and UDP, and forward versus reverse proxies.
- [ ] Clean up lab resources carefully and check for billable components.

## Important Lab Safety Notes

1. Use the practice Region specified by your instructor; the session notes say to use **N. Virginia** for practice and protect the Mumbai production environment.
2. NAT Gateways and Elastic IPs can incur charges. Verify resources are no longer needed before deleting them.
3. Do not delete the default VPC as part of routine lab cleanup.
4. Never expose a production database or SSH port broadly to the internet.
5. Always check the AWS account and Region before changing or deleting networking resources.

