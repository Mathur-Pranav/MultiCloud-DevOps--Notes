# Networking Fundamentals for DevOps and AWS VPC

A practical, beginner-friendly guide to networking concepts used in DevOps, cloud infrastructure, and AWS.

> **Who is this for?** Beginners learning Linux, AWS, DevOps, Docker, Kubernetes, and cloud networking.
>
> **Goal:** Understand the core networking terms, IP addresses, CIDR, subnets, routing, and how these concepts fit into an AWS VPC.

---

## Table of Contents

1. [What Is Networking?](#1-what-is-networking)
2. [How Communication Works](#2-how-communication-works)
3. [Important Networking Terms](#3-important-networking-terms)
4. [IP Address Fundamentals](#4-ip-address-fundamentals)
5. [Public IP vs Private IP](#5-public-ip-vs-private-ip)
6. [Ports and Protocols](#6-ports-and-protocols)
7. [Subnet Mask and CIDR](#7-subnet-mask-and-cidr)
8. [CIDR Calculation Examples](#8-cidr-calculation-examples)
9. [Subnet Fundamentals](#9-subnet-fundamentals)
10. [Routing and Gateways](#10-routing-and-gateways)
11. [DNS, DHCP, NAT, and ARP](#11-dns-dhcp-nat-and-arp)
12. [Networking Commands for DevOps](#12-networking-commands-for-devops)
13. [AWS VPC Fundamentals](#13-aws-vpc-fundamentals)
14. [AWS Subnets: Public and Private](#14-aws-subnets-public-and-private)
15. [AWS Route Tables and Gateways](#15-aws-route-tables-and-gateways)
16. [Security Groups vs Network ACLs](#16-security-groups-vs-network-acls)
17. [Sample AWS VPC Design](#17-sample-aws-vpc-design)
18. [Common Networking Troubleshooting Scenarios](#18-common-networking-troubleshooting-scenarios)
19. [Quick Revision Cheat Sheet](#19-quick-revision-cheat-sheet)
20. [Practice Questions](#20-practice-questions)

---

## 1. What Is Networking?

A **computer network** is a group of devices connected so they can exchange data.

Examples:
- Your laptop connects to a website over the internet.
- A DevOps engineer connects to an EC2 instance using SSH.
- A Kubernetes pod calls another service.
- An application running on EC2 connects to an RDS database.

Networking makes communication between these systems possible.

### Why networking matters in DevOps

Networking is involved in:
- Connecting users to applications.
- Allowing CI/CD tools to reach servers.
- Connecting containers and Kubernetes services.
- Exposing applications through load balancers.
- Connecting application servers to databases.
- Securing access with firewalls and network rules.
- Troubleshooting timeouts, DNS failures, and connection errors.

---

## 2. How Communication Works

Suppose you open `https://example.com`.

At a simplified level:

1. Your computer asks DNS to find the IP address for `example.com`.
2. Your computer establishes a connection to the destination.
3. Network devices route packets toward the destination.
4. The server receives the request and sends a response.
5. Your browser displays the result.

A connection can fail at several points: DNS resolution, routing, firewall rules, the application process, or the return path.

### Packet

A **packet** is a unit of data sent across a network. Networking protocols add information such as source and destination addresses so devices can deliver it.

### Client and server

- **Client:** Starts a request, such as a browser or command-line tool.
- **Server:** Receives requests and provides a service, such as Nginx or an API.

---

## 3. Important Networking Terms

| Term | Simple meaning | Example |
|---|---|---|
| Network | Connected devices that communicate | Office network |
| Node/host | A device connected to a network | Laptop, EC2 instance |
| IP address | Logical address used to identify an interface | `192.168.1.10` |
| MAC address | Link-layer address used on a local network | `00:1A:2B:3C:4D:5E` |
| Packet | Unit of network data | IP packet |
| Router | Forwards traffic between networks | Home router |
| Switch | Connects devices within a local network | Office Ethernet switch |
| Gateway | A path or device used to reach another network | Default gateway |
| Route | Rule that says where traffic should go | `0.0.0.0/0` via a gateway |
| Firewall | Controls allowed and denied traffic | Security group |
| Bandwidth | Maximum data-transfer capacity | 1 Gbps |
| Latency | Time taken for data to travel | 25 ms |
| Packet loss | Packets that fail to reach their destination | 2% packet loss |
| Throughput | Actual data transferred per unit of time | 80 Mbps |
| MTU | Maximum packet size supported on a link | Often 1500 bytes on Ethernet |
| CIDR | Notation for an IP range and prefix length | `10.0.0.0/16` |
| Subnet | A smaller logical IP network | `10.0.1.0/24` |
| VLAN | Logical separation on a Layer 2 network | VLAN 10 |
| VPN | Encrypted connection across an untrusted network | Site-to-site VPN |

**Bandwidth vs throughput:** Bandwidth is the capacity; throughput is what is actually achieved.

**Latency vs bandwidth:** Latency is delay; bandwidth is capacity. A connection can have high bandwidth and still feel slow if latency is high.

---

## 4. IP Address Fundamentals

An **IP address** identifies a network interface so IP traffic can be addressed and routed.

There are two main IP versions:

### IPv4

IPv4 uses 32 bits and is commonly written as four decimal numbers separated by dots.

Example: `192.168.1.10`

Each part is an octet and ranges from `0` to `255`.

### IPv6

IPv6 uses 128 bits and is written in hexadecimal groups separated by colons.

Example: `2001:db8::10`

IPv6 provides a much larger address space than IPv4.

### IPv4 address structure

An IPv4 address has 32 bits. The prefix length tells us how many bits identify the network. The remaining bits are used for addresses within that network.

Example: `192.168.1.10/24`
- IP address: `192.168.1.10`
- Prefix: `/24`
- Network: `192.168.1.0`
- Traditional subnet mask: `255.255.255.0`

---

## 5. Public IP vs Private IP

### Private IPv4 ranges

These ranges are reserved for private networks and are not directly routed across the public internet:

| Range | CIDR |
|---|---|
| `10.0.0.0` – `10.255.255.255` | `10.0.0.0/8` |
| `172.16.0.0` – `172.31.255.255` | `172.16.0.0/12` |
| `192.168.0.0` – `192.168.255.255` | `192.168.0.0/16` |

Examples of private IPs:
- `10.0.1.25`
- `172.16.10.5`
- `192.168.1.100`

A **public IP** is globally routable, subject to routing and security controls. A private IP is normally used for internal communication.

Important:
- A public IP alone does not guarantee connectivity. Routes and firewall rules must also allow traffic.
- Private IPs can communicate across connected networks through routing, VPNs, or other private connectivity.
- NAT can allow private-addressed systems to initiate internet connections without assigning each system a public IPv4 address.

---

## 6. Ports and Protocols

An IP address identifies a host/interface; a **port** identifies a service endpoint on that host.

Examples of commonly used TCP ports:

| Port | Service | Typical use |
|---:|---|---|
| 22 | SSH | Linux server administration |
| 25 | SMTP | Email transfer |
| 53 | DNS | Name resolution (DNS can use UDP and TCP) |
| 80 | HTTP | Web traffic |
| 123 | NTP | Time synchronization (usually UDP) |
| 443 | HTTPS | Encrypted web traffic |
| 3306 | MySQL | Database connections |
| 5432 | PostgreSQL | Database connections |
| 6379 | Redis | Cache/data store |
| 8080 | Alternative HTTP | Application or proxy traffic |
| 9090 | Common Prometheus port | Metrics interface (default configuration) |
| 3000 | Common development/web port | Often used by frontend tools or Grafana in some setups |

Ports are conventions, not guarantees: applications can be configured to listen on different ports.

### TCP vs UDP

**TCP**
- Connection-oriented.
- Provides ordered delivery and retransmission.
- Common for HTTPS, SSH, and database connections.

**UDP**
- Connectionless.
- Has lower protocol overhead but does not itself guarantee delivery or order.
- Common for DNS queries, voice/video traffic, and telemetry.

### HTTP vs HTTPS

- **HTTP:** Web protocol without transport encryption by itself.
- **HTTPS:** HTTP protected by TLS encryption and server authentication.

---

## 7. Subnet Mask and CIDR

**CIDR** means *Classless Inter-Domain Routing*. CIDR notation represents an IP network using an address followed by a prefix length.

Examples:
- `10.0.0.0/8`
- `172.16.0.0/16`
- `192.168.1.0/24`
- `192.168.1.10/32`

The number after `/` tells us how many of the 32 IPv4 bits are network bits.

### Common IPv4 prefix sizes

| CIDR | Subnet mask | Total IPv4 addresses |
|---|---|---:|
| `/32` | `255.255.255.255` | 1 |
| `/30` | `255.255.255.252` | 4 |
| `/29` | `255.255.255.248` | 8 |
| `/28` | `255.255.255.240` | 16 |
| `/27` | `255.255.255.224` | 32 |
| `/26` | `255.255.255.192` | 64 |
| `/25` | `255.255.255.128` | 128 |
| `/24` | `255.255.255.0` | 256 |
| `/23` | `255.255.254.0` | 512 |
| `/22` | `255.255.252.0` | 1,024 |
| `/20` | `255.255.240.0` | 4,096 |
| `/16` | `255.255.0.0` | 65,536 |
| `/8` | `255.0.0.0` | 16,777,216 |

For a conventional IPv4 subnet, the number of addresses is:

`2^(32 - prefix length)`

For many traditional IPv4 LAN subnets, two addresses are reserved for network and broadcast, leaving `total addresses - 2` host addresses. This rule does **not** directly describe AWS subnet usable-address capacity; AWS reserves five IPv4 addresses in each subnet.

### Easy rule

- Smaller prefix number = larger network.
- Larger prefix number = smaller network.

For example:
- `/16` is larger than `/24`.
- `/24` contains 256 total IPv4 addresses.
- `/26` contains 64 total IPv4 addresses.

---

## 8. CIDR Calculation Examples

### Example 1: `192.168.1.0/24`

- Prefix length: 24
- Host bits: `32 - 24 = 8`
- Total addresses: `2^8 = 256`
- Address range: `192.168.1.0` to `192.168.1.255`
- Traditional usable host range: `192.168.1.1` to `192.168.1.254`

### Example 2: `10.0.0.0/16`

- Prefix length: 16
- Host bits: `32 - 16 = 16`
- Total addresses: `2^16 = 65,536`
- Address range: `10.0.0.0` to `10.0.255.255`
- Traditional usable host range: `10.0.0.1` to `10.0.255.254`

### Example 3: `10.0.1.0/24` inside `10.0.0.0/16`

The `/16` network covers `10.0.0.0` through `10.0.255.255`. The `/24` network `10.0.1.0/24` is one smaller network inside it.

This is useful when dividing a cloud network into subnets.

### Example 4: Divide `10.0.0.0/16` into `/24` subnets

A `/16` contains 65,536 addresses. Each `/24` contains 256 addresses.

`65,536 / 256 = 256` subnets.

Examples:
- `10.0.0.0/24`
- `10.0.1.0/24`
- `10.0.2.0/24`
- ...
- `10.0.255.0/24`

### Example 5: What does `/32` mean?

`10.0.1.25/32` identifies exactly one IPv4 address. `/32` routes are often used to represent a single host.

### CIDR practice tip

Use a subnet calculator when planning production networks, and verify that:
- The network address is aligned to the prefix.
- Subnet ranges do not overlap.
- There is enough address capacity for growth.
- Connected networks do not use conflicting CIDR ranges.

---

## 9. Subnet Fundamentals

A **subnet** is a smaller IP network carved out of a larger address range.

Why create subnets?
- Separate public-facing resources from internal resources.
- Divide workloads by environment or function.
- Control routing and security boundaries.
- Place resources across multiple Availability Zones for resilience.
- Plan IP address capacity.

Example VPC CIDR: `10.0.0.0/16`

Possible subnet plan:
- `10.0.1.0/24` — public subnet in Availability Zone A
- `10.0.2.0/24` — public subnet in Availability Zone B
- `10.0.11.0/24` — private application subnet in AZ A
- `10.0.12.0/24` — private application subnet in AZ B
- `10.0.21.0/24` — private database subnet in AZ A
- `10.0.22.0/24` — private database subnet in AZ B

Each subnet must fit completely inside the VPC CIDR and must not overlap another subnet in the same VPC.

### AWS subnet address reservation

For an IPv4 subnet in AWS, AWS reserves the first four IP addresses and the last IP address of the subnet. For example, in `10.0.1.0/24`, these are:
- `10.0.1.0` — network address
- `10.0.1.1` — reserved by AWS for the VPC router
- `10.0.1.2` — reserved by AWS for DNS
- `10.0.1.3` — reserved by AWS for future use
- `10.0.1.255` — reserved as the subnet's last address

So a `/24` AWS IPv4 subnet has **251 usable IPv4 addresses**, not 254.

---

## 10. Routing and Gateways

A **route table** contains rules that determine where network traffic is sent.

A route normally has:
- **Destination:** The network or IP range being reached.
- **Target/next hop:** Where traffic should be forwarded.

Example:

| Destination | Target | Meaning |
|---|---|---|
| `10.0.0.0/16` | Local | Traffic within the VPC |
| `0.0.0.0/0` | Internet/NAT gateway | Default route for IPv4 traffic, depending on the table |

`0.0.0.0/0` means all IPv4 destinations. It is called a **default route**.

### Default gateway

A default gateway is the next hop used when there is no more specific route for a destination. In AWS, route tables specify targets such as an internet gateway, NAT gateway, transit gateway, virtual private gateway, or network interface, depending on the design.

### Longest prefix match

When multiple routes match a destination, routing generally selects the most specific matching prefix.

For example, a route to `10.0.1.0/24` is more specific than a route to `10.0.0.0/16`.

---

## 11. DNS, DHCP, NAT, and ARP

### DNS — Domain Name System

DNS translates names such as `example.com` into IP addresses and can provide other records.

Useful commands:
```bash
nslookup example.com
dig example.com
```

### DHCP — Dynamic Host Configuration Protocol

DHCP automatically provides network configuration such as an IP address, subnet mask, default gateway, and DNS servers. Cloud platforms may manage this configuration for instances.

### NAT — Network Address Translation

NAT translates network addresses. A common cloud use case is allowing private instances to initiate outbound internet connections without giving each instance a public IPv4 address.

NAT does not automatically make private instances reachable from the internet.

### ARP — Address Resolution Protocol

On IPv4 local networks, ARP helps map an IP address to a link-layer (MAC) address. IPv6 uses Neighbor Discovery rather than ARP.

### ICMP and ping

ICMP supports network diagnostics and error reporting. `ping` commonly uses ICMP Echo Request and Echo Reply messages. A failed ping does not always mean a host is down; a firewall may block ICMP while TCP services still work.

---

## 12. Networking Commands for DevOps

### Check IP addresses

```bash
ip addr
ip -br addr
```

### Check routes

```bash
ip route
```

### Test DNS resolution

```bash
nslookup example.com
dig example.com
getent hosts example.com
```

### Test connectivity

```bash
ping -c 4 8.8.8.8
curl -I https://example.com
curl -v https://example.com
```

### Check listening ports and processes

```bash
ss -tulnp
ss -lnt
```

### Check a specific TCP port

```bash
nc -vz example.com 443
```

### Trace the route

```bash
traceroute example.com
tracepath example.com
```

Install the appropriate package if a command is missing. Some networks block ping or traceroute, so interpret results together with route, DNS, and port checks.

### A practical troubleshooting sequence

When an application cannot connect to a service, check in this order:

1. Is the application/service running?
2. Is it listening on the expected IP and port?
3. Does the hostname resolve to the expected IP?
4. Is the destination IP correct?
5. Is there a valid route in both directions?
6. Are host firewalls, cloud security groups, and network ACLs allowing the traffic?
7. Is the service healthy and accepting requests?
8. Check logs for the exact error and timestamp.

---

## 13. AWS VPC Fundamentals

**Amazon VPC (Virtual Private Cloud)** lets you create a logically isolated virtual network in AWS.

Inside a VPC, you can launch resources such as EC2 instances and connect services such as load balancers, databases, and Kubernetes clusters.

Core VPC components:

| Component | Purpose |
|---|---|
| VPC | Your isolated virtual network |
| CIDR block | IP address range allocated to the VPC |
| Subnet | IP range within a VPC, associated with one Availability Zone |
| Route table | Controls where subnet traffic is sent |
| Internet Gateway (IGW) | Enables internet connectivity for resources with appropriate routes and addressing |
| NAT Gateway | Allows private-subnet resources to initiate outbound connections |
| Security Group | Stateful virtual firewall for supported resources |
| Network ACL (NACL) | Stateless subnet-level traffic filter |
| VPC endpoint | Private connectivity to supported AWS services |
| VPC peering | Private routing between two VPCs, subject to non-overlapping CIDRs and route configuration |
| Transit Gateway | Hub for connecting multiple VPCs and networks |
| VPN connection | Encrypted connectivity between networks |
| VPC Flow Logs | Records information about IP traffic to and from network interfaces, subnets, or VPCs |

### VPC CIDR planning

A VPC IPv4 CIDR is commonly chosen from private ranges, such as `10.0.0.0/16`. Choose a range that:
- Is large enough for planned subnets and future growth.
- Does not overlap with on-premises or connected VPC networks.
- Can be divided cleanly into subnet ranges.

AWS VPC CIDR rules and supported sizes can vary by address type and feature; check current AWS documentation before finalizing production CIDR plans.

---

## 14. AWS Subnets: Public and Private

A subnet is considered **public** when its associated route table has a route to an Internet Gateway. For an EC2 instance to communicate directly over the public internet, it also needs suitable public addressing and security rules.

A **private subnet** does not have a direct route to an Internet Gateway. It can still access other VPC resources and may have outbound internet access through a NAT Gateway or another network path.

### Typical placement

**Public subnet**
- Internet-facing Application Load Balancer
- Bastion host, if needed
- NAT Gateway (for IPv4 outbound access from private subnets)

**Private application subnet**
- Application servers
- Internal services
- Worker nodes, depending on architecture

**Private database subnet**
- RDS database instances
- Other data services that should not be directly exposed to the public internet

Do not rely only on the subnet name. The route table, addressing, and security controls determine actual connectivity.

---

## 15. AWS Route Tables and Gateways

### Internet Gateway (IGW)

An IGW attaches to a VPC. A route such as `0.0.0.0/0 -> IGW` allows internet-bound IPv4 traffic to use the IGW. The resource still needs appropriate public addressing and security rules.

### NAT Gateway

A NAT Gateway is commonly placed in a public subnet and associated with an Elastic IP for IPv4 internet access. A private subnet's route table can use `0.0.0.0/0 -> NAT Gateway`.

Important:
- NAT Gateway enables outbound-initiated connectivity; it does not provide unsolicited inbound internet access to private instances.
- For high availability, production designs often use a NAT Gateway per Availability Zone and route each private subnet to the NAT Gateway in the same AZ, considering cost and architecture.
- IPv6 egress has different options, such as an egress-only internet gateway, depending on the use case.

### Example route tables

**Public subnet route table**

| Destination | Target |
|---|---|
| `10.0.0.0/16` | local |
| `0.0.0.0/0` | Internet Gateway |

**Private application subnet route table (IPv4 outbound internet via NAT)**

| Destination | Target |
|---|---|
| `10.0.0.0/16` | local |
| `0.0.0.0/0` | NAT Gateway |

A private subnet does not require a default route to a NAT Gateway if it does not need that outbound path. Use VPC endpoints where appropriate to reach supported AWS services privately.

---

## 16. Security Groups vs Network ACLs

| Feature | Security Group | Network ACL |
|---|---|---|
| Scope | Resource/network interface level | Subnet level |
| State | Stateful | Stateless |
| Rules | Allow rules | Allow and deny rules |
| Return traffic | Automatically allowed for established tracked connections | Must be allowed by rules in both directions |
| Rule evaluation | All applicable allow rules are considered | Rules evaluated in ascending rule-number order |
| Common use | Control access to EC2, load balancers, and other supported resources | Additional subnet-level traffic filtering |

### Example security group rules for a web application

| Type | Port | Source | Reason |
|---|---:|---|---|
| SSH | 22 | Trusted admin IP only | Server administration, if SSH is required |
| HTTP | 80 | `0.0.0.0/0` | Public HTTP traffic, if required |
| HTTPS | 443 | `0.0.0.0/0` | Public encrypted web traffic |
| Application port | 8080 | Application load balancer security group | Only the load balancer should reach the app |
| MySQL | 3306 | Application security group | Only the application tier should reach the database |

Avoid exposing SSH or database ports to `0.0.0.0/0`. Use the narrowest source range or security group reference that meets the requirement.

---

## 17. Sample AWS VPC Design

Example CIDR plan for a simple two-AZ application:

**VPC:** `10.0.0.0/16`

| Subnet | CIDR | Purpose |
|---|---|---|
| Public AZ-A | `10.0.1.0/24` | Public load balancer / NAT Gateway |
| Public AZ-B | `10.0.2.0/24` | Public load balancer / NAT Gateway |
| App private AZ-A | `10.0.11.0/24` | Application instances |
| App private AZ-B | `10.0.12.0/24` | Application instances |
| DB private AZ-A | `10.0.21.0/24` | Database subnet |
| DB private AZ-B | `10.0.22.0/24` | Database subnet |

### Request flow

```text
User / Internet
      |
      v
Internet Gateway
      |
      v
Public Load Balancer
      |
      v
Private Application Instances
      |
      v
Private RDS Database
```

The exact route and security configuration must permit each required connection. The database should normally accept traffic only from the application tier, not directly from the internet.

### Design checklist

- [ ] VPC CIDR is planned and does not overlap connected networks.
- [ ] Subnet ranges fit inside the VPC and do not overlap.
- [ ] Subnets are spread across Availability Zones for resilience where needed.
- [ ] Route tables are associated with the correct subnets.
- [ ] Internet-facing resources have appropriate public routing and addressing.
- [ ] Private resources do not have unintended direct internet exposure.
- [ ] Security groups allow only required traffic.
- [ ] Network ACLs are understood and do not block required flows.
- [ ] VPC Flow Logs and application/system logs are enabled where appropriate.
- [ ] Address capacity and expected growth have been considered.

---

## 18. Common Networking Troubleshooting Scenarios

### Scenario 1: Cannot SSH into an EC2 instance

Check:
1. The instance is running and has a reachable public IP or an alternative private access path.
2. The subnet route table has the expected route.
3. The security group allows TCP 22 from your trusted public IP.
4. The network ACL allows inbound and return traffic.
5. The operating system firewall allows SSH.
6. The SSH service is running and the key/user are correct.

### Scenario 2: EC2 in a private subnet cannot download packages

Check:
1. Is outbound access actually required?
2. Does the private route table have a route to a NAT Gateway or another approved egress path?
3. Is the NAT Gateway in a public subnet with a route to an IGW?
4. Does the NAT Gateway have the needed public addressing?
5. Do security groups, NACLs, DNS, and OS settings allow the connection?
6. Could a VPC endpoint provide private access to the needed AWS service?

### Scenario 3: Application cannot connect to RDS

Check:
1. RDS endpoint hostname resolves correctly.
2. RDS is available and listening on the expected database port.
3. The RDS security group allows the database port from the application security group.
4. Routes exist between the app and database subnets.
5. Network ACLs and host/application configuration do not block traffic.
6. Database credentials, TLS settings, and database permissions are correct.

### Scenario 4: Website resolves but does not load

DNS may be working, but the TCP/TLS/application path can still fail. Check:
- The resolved IP address.
- Port 80 or 443 connectivity.
- Load balancer target health.
- TLS certificate and hostname.
- Listener and target-group configuration.
- Security groups, routes, and application logs.

### Scenario 5: Two VPCs cannot communicate

Check:
- VPC peering, Transit Gateway, VPN, or other connectivity is configured.
- CIDR ranges do not overlap where the chosen connectivity method requires non-overlapping ranges.
- Routes exist in both directions.
- Security groups and NACLs allow traffic.
- DNS resolution settings are configured if using private hostnames.

### Scenario 6: Ping fails, but the website works

ICMP may be blocked while TCP port 443 is allowed. Test the actual application port with `curl` or `nc`; do not use ping alone to determine service availability.

### Scenario 7: Connection times out vs connection refused

- **Timeout:** Often suggests dropped traffic, missing routes, a firewall, or an unreachable destination.
- **Connection refused:** Often means the destination was reached but no process is accepting connections on that port, or the destination actively rejected the connection.

These are clues, not absolute rules. Check logs and the network path to confirm.

---

## 19. Quick Revision Cheat Sheet

| Concept | Remember |
|---|---|
| IP address | Logical address for network communication |
| MAC address | Link-layer address on a local network |
| CIDR `/24` | 256 total IPv4 addresses |
| CIDR `/16` | 65,536 total IPv4 addresses |
| `0.0.0.0/0` | All IPv4 destinations |
| `/32` | One IPv4 address |
| Private IPv4 | `10/8`, `172.16/12`, `192.168/16` |
| DNS | Resolves names to records such as IP addresses |
| DHCP | Automatically provides IP configuration |
| NAT | Translates addresses; often used for outbound IPv4 access |
| TCP | Reliable, ordered byte stream |
| UDP | Connectionless datagrams |
| Port 22 | Common SSH port |
| Port 80 | Common HTTP port |
| Port 443 | Common HTTPS port |
| VPC | Isolated virtual network in AWS |
| Public subnet | Route table has a route to an IGW |
| Private subnet | No direct route to an IGW |
| Internet Gateway | VPC component for internet connectivity |
| NAT Gateway | Common outbound IPv4 path for private subnets |
| Security Group | Stateful resource-level firewall |
| Network ACL | Stateless subnet-level filter |
| VPC Flow Logs | IP traffic metadata for network troubleshooting |

---

## 20. Practice Questions

Try to answer these without looking at the notes.

1. What is the difference between an IP address and a port?
2. What is the difference between public and private IPv4 addresses?
3. What does `10.0.0.0/16` mean?
4. How many total IPv4 addresses are in a `/24`?
5. Why does AWS reserve five IPv4 addresses in every subnet?
6. What is the difference between a VPC and a subnet?
7. What makes an AWS subnet public?
8. Can a public subnet's EC2 instance be reached from the internet without a public IP? Explain the normal IPv4 case.
9. What is the role of an Internet Gateway?
10. What is the role of a NAT Gateway?
11. What is the difference between a security group and a network ACL?
12. Why must return traffic be considered when configuring a network ACL?
13. What does the default route `0.0.0.0/0` represent?
14. What is DNS and how can you troubleshoot a DNS problem in Linux?
15. What is the difference between latency and bandwidth?
16. Which command can show listening TCP and UDP sockets on Linux?
17. Why might ping fail while HTTPS works?
18. What should you check if an application cannot connect to RDS?
19. Why should connected VPC CIDR ranges be planned carefully?
20. How would you troubleshoot an EC2 instance that cannot access the internet?

---

## Further Learning

Recommended topics to learn next:
- OSI and TCP/IP models
- DNS records and troubleshooting
- Linux firewall basics (`iptables`, `nftables`, `ufw`)
- AWS VPC route tables, endpoints, peering, and Transit Gateway
- Load balancers and health checks
- Docker bridge networking and port mapping
- Kubernetes Services, Ingress, and NetworkPolicy
- TLS certificates and HTTPS troubleshooting
- VPC Flow Logs and packet-level diagnostics

**Note:** This guide is an educational introduction. Verify AWS service behavior, quotas, and design details against the current AWS documentation before using a network design in production.
