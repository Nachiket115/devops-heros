# AWS Managed Databases: DynamoDB & RDS Guide

## Part 1: Amazon DynamoDB (NoSQL Key-Value & Document DB)

### 1. What is DynamoDB?
Amazon DynamoDB is a fully managed, serverless, key-value and document NoSQL database designed for fast and predictable performance with seamless scalability. It delivers single-digit millisecond latency at any scale.

```text
+-------------------------------------------------------------------------+
|                        DynamoDB Table: Users                            |
+-------------------------------------------------------------------------+
| Partition Key (PK) | Sort Key (SK)     | Attributes (Schema-less)       |
| [UserID (String)]  | [CreatedAt (Num)] | [Email, Name, Role, Preferences]|
+--------------------+-------------------+--------------------------------+
| "usr_101"          | 1718000001        | "alice@example.com", "Alice"   |
| "usr_101"          | 1718000050        | "login_event", "192.168.1.1"   |
| "usr_102"          | 1718000200        | "bob@example.com", "Bob"       |
+-------------------------------------------------------------------------+
```

### 2. Core DynamoDB Concepts
* **Tables**: A collection of data items (equivalent to a table in relational databases).
* **Items**: A collection of attributes (equivalent to a row/record). Maximum item size is **400 KB**.
* **Attributes**: A fundamental data element (equivalent to a column/field). Attributes are schema-less—different items can have different attributes.
* **Primary Keys**:
  1. **Simple Primary Key (Partition Key / Hash Key)**: A single attribute determining the physical storage partition.
  2. **Composite Primary Key (Partition Key + Sort Key / Range Key)**: Items can share the same Partition Key, but must have unique Sort Keys. Enables range queries (`begins_with`, `between`, `<`, `>`).
* **Secondary Indexes**:
  * **Global Secondary Index (GSI)**: An index with a partition key and sort key that can be different from those on the table.
  * **Local Secondary Index (LSI)**: An index that has the same partition key as the table, but a different sort key.

### 3. DynamoDB Use Cases
* **User Session Stores & Profiles**: High-concurrency authentication and session states.
* **Gaming Leaderboards & State**: Low-latency global player stats and state syncing.
* **E-Commerce Shopping Carts**: Microservices requiring high write throughput and millisecond response times.

---

## Part 2: Amazon RDS (Relational Database Service)

### 1. What is Amazon RDS?
Amazon Relational Database Service (Amazon RDS) is a managed relational database service that makes it easy to set up, operate, and scale traditional SQL databases in the cloud. It automates time-consuming administrative tasks such as hardware provisioning, database setup, patching, and backups.

```text
               +---------------------------------------+
               |        Amazon RDS Architecture        |
               +-------------------+-------------------+
                                   |
         +-------------------------+-------------------------+
         |                                                   |
         v                                                   v
+-------------------------------+           +-------------------------------+
|     Primary DB Instance       |           |     Standby DB Instance       |
|  (AZ 1a - Read/Write Traffic) | ────────> |    (AZ 1b - Multi-AZ Sync)    |
+---------------+---------------+ (Sync Rep)+---------------+---------------+
                |                                           |
                | (Async Replication)                       | (Automated Failover)
                v                                           v
+-------------------------------+           +-------------------------------+
|      Read Replica 1           |           |      Read Replica 2           |
|   (Read-Only Scaling)         |           |   (Read-Only Analytics)       |
+-------------------------------+           +-------------------------------+
```

### 2. Core RDS Concepts

#### A. Supported Database Engines
* Amazon Aurora (MySQL & PostgreSQL compatible, cloud-native high performance)
* PostgreSQL
* MySQL
* MariaDB
* Oracle Database
* Microsoft SQL Server

#### B. DB Instances & Classes
* A DB instance is an isolated database environment in the cloud.
* Sized by DB instance classes: Standard (`db.m6g`), Memory Optimized (`db.r6g`), and Burstable (`db.t4g`).

#### C. High Availability (Multi-AZ Deployments)
* RDS automatically provisions and maintains a synchronous standby replica in a different Availability Zone.
* The primary DB synchronously replicates transactions to the standby.
* In the event of infrastructure failure, maintenance, or AZ outage, RDS performs an **automatic failover** with zero manual intervention and no endpoint URL change.

#### D. Read Replicas (Read Scaling)
* Uses asynchronous replication to offload read-heavy database workloads from the primary DB instance.
* Supports up to 15 read replicas per primary DB.
* Can be deployed across multiple AZs or cross-region.

#### E. Backups & Recovery
* **Automated Backups**: Continuous transaction log archiving and daily storage snapshots (retained 1 to 35 days). Enables **Point-in-Time Recovery (PITR)** to any second within the retention window.
* **Manual DB Snapshots**: User-initiated backups stored indefinitely in Amazon S3, even after deleting the DB instance.

#### F. Security
* **Network Isolation**: Placed inside private subnets within a VPC; protected by DB Security Groups.
* **Encryption**: At-rest encryption using AWS KMS (AES-256) for tables, automated backups, and snapshots; in-transit encryption using SSL/TLS.

### 3. RDS Use Cases
* **Enterprise ERP, CRM, and Financial Systems**: Workloads requiring ACID compliance, complex SQL queries, and foreign key constraints.
* **E-Commerce Product Catalogs**: Relational data models with complex joins and transactional guarantees.

---

## Part 3: Comparison: DynamoDB vs RDS

| Criteria | Amazon DynamoDB | Amazon RDS |
|---|---|---|
| **Data Model** | NoSQL (Key-Value / Document) | Relational SQL (Tables, rows, columns, foreign keys) |
| **Schema** | Schema-less / Dynamic | Fixed, structured schema |
| **Scaling** | Horizontal (Auto-scales partitions indefinitely) | Vertical (Compute scaling) & Horizontal (Read Replicas) |
| **Latency** | Single-digit milliseconds | Milliseconds (depends on query complexity & joins) |
| **High Availability** | Built-in multi-AZ redundancy by default | Optional Multi-AZ configuration |
| **Pricing Model** | On-demand (per request) or Provisioned capacity | Hourly per DB instance size + storage |
