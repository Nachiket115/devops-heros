# AWS VPC (Virtual Private Cloud) — Networking Guide

## 1. What is Amazon VPC?
Amazon Virtual Private Cloud (Amazon VPC) enables you to launch AWS resources into a logically isolated, dedicated virtual network that you define. You have complete control over your virtual networking environment, including selection of your own IP address range, creation of subnets, and configuration of route tables and network gateways.

VPC is **regional**, spanning all Availability Zones within that Region.

```text
               +-------------------------------------------------------------+
               |                VPC (CIDR: 10.0.0.0/16)                      |
               |                                                             |
               |   Internet Gateway (igw-xxxx) <──── Internet (0.0.0.0/0)    |
               |                   │                                         |
               |    +──────────────┴────────────────────────────+            |
               |    │ Public Subnet (10.0.1.0/24)               │            |
               |    │ Route Table: 0.0.0.0/0 ──> Internet Gateway│            |
               |    │ [ Public EC2 / ALB ]                      │            |
               |    │ [ NAT Gateway (eip-xxxx) ] ───────────────┼────────┐   |
               |    +───────────────────────────────────────────+        │   |
               |                                                         │   |
               |    +───────────────────────────────────────────+        │   |
               |    │ Private Subnet (10.0.2.0/24)              │        │   |
               |    │ Route Table: 0.0.0.0/0 ──> NAT Gateway <──┼────────┘   |
               |    │ [ Backend App EC2 ]                       │            |
               |    │ [ RDS Database / DynamoDB Endpoint ]      │            |
               |    +───────────────────────────────────────────+            |
               +-------------------------------------------------------------+
```

---

## 2. Core VPC Networking Components

### A. CIDR (Classless Inter-Domain Routing)
* Defines the IP address range allocated to the VPC.
* Expressed as `Base-IP / Subnet-Mask` (e.g., `10.0.0.0/16` gives 65,536 private IPv4 addresses).
* Recommended private ranges (RFC 1918): `10.0.0.0/16`, `172.31.0.0/16`, `192.168.0.0/16`.

### B. Subnets
* A segment of your VPC's IP address range allocated to a specific Availability Zone.
* Subnets cannot span multiple Availability Zones.
* In every subnet, AWS reserves **5 IP addresses**:
  * `.0`: Network address.
  * `.1`: VPC Router.
  * `.2`: Amazon DNS server.
  * `.3`: Future use.
  * `.255`: Network broadcast address.

### C. Public vs Private Subnets
* **Public Subnet**: Has a direct route in its route table pointing to an **Internet Gateway (`igw-xxxx`)**. Instances launched here can receive public IPs and communicate directly with the internet.
* **Private Subnet**: Does **not** have a direct route to an Internet Gateway. Protected from inbound internet traffic. To download patches or reach the internet, instances in a private subnet must route outbound traffic through a **NAT Gateway**.

### D. Route Tables
* A set of rules (called routes) that determine where network traffic from your subnet or gateway is directed.
* Every subnet must be associated with exactly one route table (if none is explicitly assigned, the VPC Main Route Table is used).
* Route example:
  * Destination `10.0.0.0/16` -> Target `local` (Internal VPC traffic)
  * Destination `0.0.0.0/0` -> Target `igw-0123456789` (Public Internet)

### E. Internet Gateway (IGW)
* A horizontally scaled, redundant, highly available VPC component that allows communication between instances in your VPC and the internet.
* Performs Network Address Translation (1:1 NAT) for instances that have public IPv4 addresses.

### F. NAT Gateway (Network Address Translation)
* Enables instances in a **private subnet** to connect to external services outside the VPC, but prevents external internet services from initiating inbound connections to those private instances.
* Must be placed in a **public subnet** and requires an **Elastic IP (EIP)**.
* Fully managed by AWS with automatic scaling.

### G. Security Groups vs Network ACLs (NACLs)

| Feature | Security Group (SG) | Network ACL (NACL) |
|---|---|---|
| **Level** | Instance level (applies to ENI) | Subnet level (applies to boundary) |
| **Rules** | Allow rules only (implicit deny) | Allow and Deny rules (processed by rule number) |
| **State** | **Stateful** (Return traffic automatically allowed) | **Stateless** (Return traffic must be explicitly allowed) |
| **Evaluation** | Evaluates all rules before deciding | Evaluates rules in ascending numerical order |

---

## 3. Best Practices for VPC Architecture
1. **Multi-AZ Redundancy**: Deploy at least two public and two private subnets across multiple Availability Zones for high availability.
2. **Database Isolation**: Place all database engines (RDS, Redis, databases) strictly inside private subnets with no public IPs.
3. **Use Least Privilege Security Groups**: Restrict inbound rules by specific port, protocol, and source CIDR / Security Group ID.
4. **VPC Flow Logs**: Enable VPC Flow Logs to capture IP traffic flows for security auditing and network troubleshooting.
