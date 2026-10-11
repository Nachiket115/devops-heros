# AWS S3 (Simple Storage Service) — Storage Guide

## 1. What is Amazon S3?
Amazon Simple Storage Service (Amazon S3) is an object storage service offering industry-leading scalability, data availability, security, and performance. S3 stores data as **objects** within **buckets**.

It provides **99.999999999% (11 9's) of data durability** by automatically replicating data across multiple physically separated Availability Zones within an AWS Region.

```text
               +---------------------------------------+
               |         Amazon S3 Service             |
               +-------------------+-------------------+
                                   |
         +-------------------------+-------------------------+
         |                                                   |
         v                                                   v
+-------------------------------+           +-------------------------------+
|   Bucket: devops-assets       |           |   Bucket: app-backups         |
|   (Region: ap-south-1)        |           |   (Region: us-east-1)         |
+---------------+---------------+           +---------------+---------------+
                |                                           |
    +-----------+-----------+                   +-----------+-----------+
    |                       |                   |                       |
    v                       v                   v                       v
[Object: photo.jpg]   [Object: index.html]  [Object: db.tar.gz]   [Object: logs.csv]
(Key, Value, Version) (Public or Private)   (Encrypted: SSE-S3)   (Glacier Lifecycle)
```

---

## 2. Core S3 Concepts

### A. Buckets
* A container for objects stored in Amazon S3.
* **Globally Unique Name**: Bucket names must be unique across all existing bucket names in Amazon S3 worldwide (like domain names).
* Bucket naming rules: 3 to 63 characters long, lowercase letters, numbers, and hyphens only.
* Created in a specific AWS Region.

### B. Objects
* Fundamental entities stored in S3.
* An object consists of:
  * **Key**: The unique name/path assigned to an object (e.g., `images/2026/banner.png`).
  * **Value**: The actual content data (up to 5 TB per single object).
  * **Version ID**: Distinguishes versions of the same key when versioning is enabled.
  * **Metadata**: Key-value pairs describing the object (Content-Type, custom tags).

### C. Storage Classes
AWS provides multiple storage classes tailored for different access patterns and cost budgets:

| Storage Class | Designed For | Durability | Retrieval Fee | Minimum Storage Duration |
|---|---|---|---|---|
| **S3 Standard** | Frequently accessed data | 99.999999999% | None | None |
| **S3 Intelligent-Tiering** | Unknown or changing access patterns | 99.999999999% | None | None (small monitoring fee) |
| **S3 Standard-IA** | Infrequently accessed data, rapid access when needed | 99.999999999% | Yes | 30 days |
| **S3 One Zone-IA** | Re-creatable, infrequently accessed data stored in 1 AZ | 99.999999999% (Single AZ) | Yes | 30 days |
| **S3 Glacier Flexible** | Long-term cold archival data (retrieval: mins to hours) | 99.999999999% | Yes | 90 days |
| **S3 Glacier Deep Archive**| Longest-term archival (lowest cost, retrieval: 12-48 hours)| 99.999999999% | Yes | 180 days |

### D. Versioning
* Keeps multiple variants of an object in the same bucket.
* Protects against unintended user actions and application failures (e.g., accidental overwrite or deletion).
* When you delete an object with versioning enabled, S3 inserts a **Delete Marker** rather than permanently removing the underlying file.
* Once enabled on a bucket, versioning cannot be disabled—only **suspended**.

### E. Lifecycle Policies
* Automated rules that manage objects throughout their lifetime:
  1. **Transition Actions**: Automatically move objects to cheaper storage classes (e.g., Standard -> Standard-IA after 30 days -> Glacier after 90 days).
  2. **Expiration Actions**: Automatically delete objects after a specified number of days (e.g., delete log files after 365 days).

### F. Encryption
S3 supports data encryption both in transit and at rest:
* **In Transit**: Enforced via HTTPS (TLS) connections.
* **At Rest**:
  * **SSE-S3**: Keys managed by Amazon S3 (AES-256) — enabled by default on all new buckets.
  * **SSE-KMS**: Keys managed by AWS Key Management Service (provides audit trails and granular key access control).
  * **SSE-C**: Server-side encryption with customer-provided keys.
  * **Client-Side Encryption**: Encrypting data before uploading to S3.

### G. Bucket Policies
* A resource-based IAM policy attached directly to the S3 bucket.
* Grants fine-grained permissions to other AWS accounts, IAM users, or anonymous internet users.

Example: Enforcing HTTPS only:
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "EnforceSSLRequestsOnly",
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:*",
      "Resource": [
        "arn:aws:s3:::my-secure-bucket",
        "arn:aws:s3:::my-secure-bucket/*"
      ],
      "Condition": {
        "Bool": {
          "aws:SecureTransport": "false"
        }
      }
    }
  ]
}
```

---

## 3. Common S3 Use Cases
* **Static Website Hosting**: Hosting static websites (HTML, CSS, JS) directly from an S3 bucket connected to Amazon CloudFront.
* **Backup & Disaster Recovery**: Storing automated database snapshots, virtual machine images, and long-term compliance archives.
* **Data Lakes & Big Data Analytics**: Serving as the primary data lake foundation for analytics engines like AWS Athena, EMR, and Redshift Spectrum.
