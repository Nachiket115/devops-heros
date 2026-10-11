# AWS IAM (Identity and Access Management) — Governance Guide

## 1. What is AWS IAM?
AWS Identity and Access Management (IAM) is a web service that helps you securely control access to AWS resources. IAM enables you to manage who is authenticated (signed in) and authorized (has permissions) to use resources.

IAM is **global**, meaning IAM entities (users, groups, roles, policies) exist globally across all AWS regions.

```text
               +---------------------------------------+
               |        AWS Account / Root User        |
               +-------------------+-------------------+
                                   |
         +-------------------------+-------------------------+
         |                         |                         |
         v                         v                         v
+-----------------+       +-----------------+       +-----------------+
|    IAM Users    |       |   IAM Groups    |       |    IAM Roles    |
| (Human / Admin) |       | (Devs / Admins) |       | (EC2 / Lambda)  |
+--------+--------+       +--------+--------+       +--------+--------+
         |                         |                         |
         +-------------------------+-------------------------+
                                   |
                                   v
                        +--------------------+
                        |    IAM Policies    |
                        | (JSON Permissions) |
                        +--------------------+
```

---

## 2. Core IAM Components

### A. Users
* An IAM User represents a person or application that interacts with AWS.
* Each user has credentials:
  * **Console Password** for AWS Management Console access.
  * **Access Keys (Access Key ID & Secret Access Key)** for programmatic access via AWS CLI, SDKs, or Terraform.
* Best practice: Do not use the AWS Root Account for daily tasks; create dedicated IAM users with limited administrative privileges.

### B. Groups
* An IAM Group is a collection of IAM users.
* Groups allow you to attach permissions to multiple users at once.
* A user can belong to multiple groups.
* Groups cannot contain other groups (no nesting), and groups cannot be identified as a `Principal` in policies.
* Example: `Developers`, `DevOps-Engineers`, `Security-Auditors`.

### C. Roles
* An IAM Role is an identity that you can assume to obtain temporary security credentials.
* Unlike a user, a role is not associated with a password or long-term access keys.
* Used by:
  * AWS services (e.g., an EC2 instance assuming a role to access an S3 bucket).
  * Cross-account access (allowing users in Account A to access resources in Account B).
  * Federated users (corporate Active Directory or Google identity via SAML/OIDC).

### D. Policies
* An IAM Policy is a JSON document that explicitly defines permissions.
* Policies state what actions are allowed or denied, on which resources, and under what conditions.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowS3ReadAccess",
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::my-company-data-bucket",
        "arn:aws:s3:::my-company-data-bucket/*"
      ]
    }
  ]
}
```

* Policy Elements:
  * `Version`: The policy language version (always `"2012-10-17"`).
  * `Statement`: Array of individual permission rules.
  * `Effect`: `"Allow"` or `"Deny"`.
  * `Action`: The specific API operations (e.g., `"s3:GetObject"`, `"ec2:DescribeInstances"`).
  * `Resource`: The Amazon Resource Name (ARN) identifying the target object.
  * `Condition` (optional): Criteria under which the policy is valid (e.g., IP range, MFA required).

### E. Permissions & Policy Evaluation
1. **Explicit Deny wins**: If any policy denies an action, access is denied immediately.
2. **Default Deny**: If no policy allows an action, access is denied.
3. **Explicit Allow**: Access is granted only when an explicit Allow exists and no explicit Deny overrides it.

---

## 3. Principle of Least Privilege
The **Principle of Least Privilege (PoLP)** states that users, applications, and services should be granted only the minimum permissions necessary to perform their required tasks, and nothing more.

* Why it matters:
  * Limits blast radius if credentials are leaked or compromised.
  * Prevents accidental deletion of production infrastructure.
  * Ensures regulatory and compliance standards (SOC 2, ISO 27001).

---

## 4. IAM Best Practices
1. **Lock away the AWS root user access keys** and enable MFA on the root account.
2. **Enable Multi-Factor Authentication (MFA)** for all IAM users, especially privileged administrators.
3. **Use IAM Roles for applications running on AWS** (e.g., EC2 instance profiles) rather than hardcoding static access keys.
4. **Use IAM Groups to assign permissions** instead of attaching policies directly to individual users.
5. **Rotate credentials regularly** and remove unused users or access keys.
6. **Use AWS IAM Access Analyzer** and CloudTrail to inspect unused permissions and refine policies toward least privilege.
7. **Use strong password policies** (minimum length, complexity, expiration).

---

## 5. Common Use Cases
* **CI/CD Pipeline Access**: Creating an IAM Role with OIDC integration for GitHub Actions to deploy infrastructure without storing static AWS access keys in repository secrets.
* **EC2 Instance S3 Access**: Attaching an IAM Role to an EC2 instance so backup scripts can run `aws s3 cp` securely without hardcoded credentials.
* **Separation of Duties**: Providing read-only access to developers in production environments, while granting full infrastructure deployment rights only to automated CI/CD roles.
