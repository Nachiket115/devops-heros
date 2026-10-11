# AWS EC2 (Elastic Compute Cloud) — Compute Guide

## 1. What is Amazon EC2?
Amazon Elastic Compute Cloud (Amazon EC2) provides scalable virtual computing capacity in the cloud. It eliminates the need to invest in hardware upfront, allowing you to build and run applications on virtual servers known as **instances**.

EC2 is **regional**, meaning instances are deployed within specific Availability Zones (AZs) inside an AWS Region.

```text
               +---------------------------------------+
               |         AWS Region (e.g., ap-south-1) |
               |                                       |
               |  +---------------------------------+  |
               |  |  Availability Zone (AZ 1a)      |  |
               |  |                                 |  |
               |  |  +---------------------------+  |  |
               |  |  |     Amazon EC2 Instance   |  |  |
               |  |  |  - AMI: Ubuntu 24.04 LTS  |  |  |
               |  |  |  - Type: t2.micro / t3    |  |  |
               |  |  |  - Root EBS Volume (20GB) |  |  |
               |  |  |  - Key Pair: id_rsa.pem   |  |  |
               |  |  |  - Security Group (Firewall)| |
               |  |  +-------------+-------------+  |  |
               |  +----------------|----------------+  |
               +-------------------|-------------------+
                                   v
                             Internet Gateway
```

---

## 2. Core Concepts & Components

### A. AMI (Amazon Machine Image)
* An AMI is a template that contains the software configuration (operating system, application server, and applications) required to launch your instance.
* Types of AMIs:
  * **AWS Provided**: Amazon Linux 2023, Ubuntu, Red Hat, Windows Server.
  * **Community AMIs**: Custom images shared by the AWS community.
  * **AWS Marketplace**: Commercial appliances (e.g., Cisco firewalls, WordPress stacks).
  * **Custom AMIs**: Golden images created by snapshotting an existing EC2 instance.

### B. Instance Types
EC2 offers diverse instance families optimized for different workloads:

| Family | Purpose | Examples | Ideal For |
|---|---|---|---|
| **General Purpose** | Balanced compute, memory, and networking | `t2.micro`, `t3.medium`, `m5.large` | Web servers, dev/test environments |
| **Compute Optimized** | High performance processors | `c5.large`, `c6g.xlarge` | Batch processing, media transcoding, gaming |
| **Memory Optimized** | Fast performance for large in-memory data | `r5.large`, `x1e.xlarge` | High-performance databases, Redis/Memcached |
| **Storage Optimized** | High sequential read/write to huge datasets | `i3.large`, `d2.xlarge` | Data warehousing, Kafka clusters, Elasticsearch |
| **Accelerated Computing** | Hardware accelerators (GPUs) | `p3.2xlarge`, `g4dn.xlarge` | Machine learning, 3D rendering |

### C. Key Pairs
* EC2 uses public-key cryptography to encrypt and decrypt login credentials.
* AWS stores the **public key** on the instance; you download and store the **private key** (`.pem` file).
* Connecting via SSH (Linux):
  ```bash
  chmod 400 my-key.pem
  ssh -i my-key.pem ubuntu@<ec2-public-ip>
  ```

### D. Security Groups
* A Security Group acts as a **virtual stateful firewall** controlling inbound and outbound network traffic to your instance.
* Rules are **allow-only** by default (all inbound traffic is denied unless explicitly permitted).
* **Stateful**: If you send a request from your instance, the response traffic is automatically allowed regardless of inbound rules.

Example rules:
* Inbound: Port 22 (SSH) from `Your-IP/32`
* Inbound: Port 80 (HTTP) and Port 443 (HTTPS) from `0.0.0.0/0`
* Outbound: All traffic allowed (`0.0.0.0/0`)

### E. EBS (Elastic Block Store)
* EBS provides persistent, high-performance block-level storage volumes for EC2.
* Attached to an EC2 instance like a physical hard drive.
* EBS volumes persist independently from the running life of an instance (if `DeleteOnTermination` is set to false).
* Types of EBS volumes:
  * **General Purpose SSD (`gp2`/`gp3`)**: Balanced price and performance.
  * **Provisioned IOPS SSD (`io1`/`io2`)**: Mission-critical low-latency databases.
  * **Throughput Optimized HDD (`st1`)**: Frequently accessed, throughput-intensive workloads.
  * **Cold HDD (`sc1`)**: Lowest cost for infrequent access.

### F. Public vs Private IP Addresses
* **Public IP**: Reachable over the public internet. By default, dynamically assigned upon instance startup and released upon instance stop.
* **Elastic IP (EIP)**: A static, public IPv4 address allocated to your AWS account that persists across instance stops and restarts.
* **Private IP**: Internal IP address assigned within your VPC subnet. Retained for the lifetime of the instance, used for communication between resources within the same VPC.

---

## 3. EC2 Instance Lifecycle

```text
[Launch] ──> [Pending] ──> [Running]
                              │
               ┌──────────────┼──────────────┐
               v              v              v
           [Stopped]     [Rebooting]    [Terminated]
           (Can start)   (Same instance)(Permanently deleted)
```

1. **Pending**: Instance is being provisioned with hardware and networking.
2. **Running**: Instance is active and accessible via SSH/RDP.
3. **Stopping / Stopped**: Instance compute is released (you stop paying for compute, but continue paying for attached EBS volumes).
4. **Terminated**: Instance is permanently deleted; ephemeral storage is destroyed.

---

## 4. Common EC2 Use Cases
* **Web & Application Hosting**: Deploying full-stack web applications, microservices, and API servers behind an Application Load Balancer.
* **Batch Processing & Background Workers**: Running Celery or asynchronous tasks that scale dynamically using EC2 Auto Scaling Groups.
* **Self-Managed Databases**: Running custom database engines or legacy enterprise software not supported by managed database offerings.
