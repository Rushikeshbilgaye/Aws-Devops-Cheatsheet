# 🪣 AWS S3 — Simple Storage Service

Amazon S3 (Simple Storage Service) is a highly durable **object storage service** used to store and retrieve data from anywhere.

S3 is commonly used for:

* Static websites
* Backups
* Log storage
* Application files
* Images and videos
* Data lakes
* Terraform state
* CI/CD artifacts
* Disaster recovery

---

# 📌 What is S3?

**S3 = Simple Storage Service**

S3 stores data as **objects inside buckets**.

Basic architecture:

```text
AWS Account
    │
    ↓
  S3 Bucket
    │
    ├── file1.txt
    ├── image.png
    ├── backup.zip
    └── application.log
```

---

# 🏗️ S3 Structure

```text
S3
│
└── Bucket
     │
     ├── Object
     │    ├── Data
     │    ├── Key
     │    └── Metadata
     │
     ├── Object
     └── Object
```

### Important Terms

| Term     | Meaning                                 |
| -------- | --------------------------------------- |
| Bucket   | Container for objects                   |
| Object   | Actual stored data                      |
| Key      | Object's unique name/path within bucket |
| Metadata | Information about the object            |
| Region   | AWS Region where bucket is created      |

---

# 1️⃣ S3 Bucket

A bucket is a container used to store objects.

Example:

```text
my-devops-bucket
```

Inside:

```text
my-devops-bucket
│
├── index.html
├── images/
│   ├── logo.png
│   └── banner.jpg
│
├── backups/
│   └── backup.zip
│
└── logs/
    └── application.log
```

---

# 📝 Bucket Naming Rules

S3 bucket names must follow AWS naming requirements.

A bucket name should generally:

* Be globally unique
* Use lowercase letters
* Use numbers when appropriate
* Use hyphens
* Not contain spaces

Example:

```text
my-company-devops-2026
```

Because bucket names are globally unique, this may already be taken:

```text
my-bucket
```

Prefer something unique:

```text
rushikesh-devops-project-2026
```

---

# 2️⃣ S3 Object

An object is the actual file stored in S3.

Example:

```text
index.html
```

The object consists of:

```text
Object
│
├── Data
├── Key
└── Metadata
```

---

# 3️⃣ S3 Key

The key identifies an object inside a bucket.

Example:

```text
images/logo.png
```

Bucket:

```text
my-bucket
```

Object key:

```text
images/logo.png
```

Full logical path:

```text
s3://my-bucket/images/logo.png
```

---

# 4️⃣ S3 URI

S3 URI format:

```text
s3://bucket-name/object-key
```

Example:

```text
s3://my-devops-bucket/index.html
```

---

# 5️⃣ S3 Storage Classes

S3 provides different storage classes for different access patterns and cost requirements.

Important classes include:

```text
S3 Standard
S3 Intelligent-Tiering
S3 Standard-IA
S3 One Zone-IA
S3 Glacier Instant Retrieval
S3 Glacier Flexible Retrieval
S3 Glacier Deep Archive
```

---

# 🟢 S3 Standard

Designed for frequently accessed data.

Good for:

```text
Web applications
Images
Application assets
Frequently accessed files
```

Characteristics:

```text
High availability
High durability
Frequent access
```

---

# 🟡 S3 Intelligent-Tiering

Automatically moves objects between access tiers based on changing access patterns.

Good when:

```text
Access patterns are unpredictable
```

Example:

```text
Frequently accessed
        ↓
Less frequently accessed
        ↓
Automatically optimized
```

---

# 🟠 S3 Standard-IA

IA = **Infrequent Access**

Good for data that:

* Must remain available
* Is accessed less frequently
* Needs lower storage cost than Standard

Example:

```text
Backups
Old reports
Long-term application files
```

---

# 🔵 S3 One Zone-IA

Stores data in a single Availability Zone.

Useful for:

```text
Re-creatable data
Secondary copies
Less critical data
```

Not ideal for data requiring multi-AZ resilience.

---

# 🧊 S3 Glacier

Glacier storage classes are designed for archival workloads.

Examples:

```text
S3 Glacier Instant Retrieval
S3 Glacier Flexible Retrieval
S3 Glacier Deep Archive
```

Good for:

```text
Long-term backups
Compliance archives
Historical data
```

---

# 📊 S3 Storage Class Comparison

| Storage Class              | Typical Use                             |
| -------------------------- | --------------------------------------- |
| Standard                   | Frequently accessed data                |
| Intelligent-Tiering        | Unknown/changing access patterns        |
| Standard-IA                | Infrequently accessed data              |
| One Zone-IA                | Re-creatable infrequently accessed data |
| Glacier Instant Retrieval  | Archive needing fast retrieval          |
| Glacier Flexible Retrieval | Archive with flexible retrieval         |
| Glacier Deep Archive       | Long-term archival                      |

---

# 6️⃣ S3 Durability vs Availability

This is a **very important interview topic**.

### Durability

Durability means:

> How likely the stored data is to remain intact.

### Availability

Availability means:

> How often the service/data is accessible when requested.

Remember:

```text
Durability = Data protection
Availability = Accessibility
```

Do not confuse the two.

---

# 7️⃣ S3 Versioning

Versioning keeps multiple versions of an object.

Without versioning:

```text
file.txt
   ↓
Overwrite
   ↓
Old version replaced
```

With versioning:

```text
file.txt
│
├── Version 1
├── Version 2
└── Version 3
```

This helps protect against:

* Accidental deletion
* Accidental overwrites
* Application mistakes

---

# 🔐 Enable Versioning

AWS CLI:

```bash
aws s3api put-bucket-versioning \
  --bucket my-devops-bucket \
  --versioning-configuration Status=Enabled
```

Check:

```bash
aws s3api get-bucket-versioning \
  --bucket my-devops-bucket
```

---

# 8️⃣ S3 Lifecycle Rules

Lifecycle rules automatically transition or delete objects based on age or other criteria.

Example:

```text
Day 0
 ↓
S3 Standard
 ↓
Day 30
 ↓
Standard-IA
 ↓
Day 90
 ↓
Glacier
 ↓
Day 365
 ↓
Delete
```

Useful for:

* Backups
* Logs
* Old application data
* Cost optimization

---

# 🧠 Lifecycle Example

Suppose application logs are stored in:

```text
logs/
```

Policy:

```text
30 days → Standard-IA
90 days → Glacier
365 days → Delete
```

This reduces storage cost automatically.

---

# 9️⃣ S3 Bucket Policy

A bucket policy is a resource-based policy attached to an S3 bucket.

Example use:

```text
Allow specific AWS account
Allow specific role
Allow specific actions
Deny insecure requests
```

Basic structure:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::123456789012:role/MyRole"
      },
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-bucket/*"
    }
  ]
}
```

---

# 🔥 IAM Policy vs S3 Bucket Policy

Important interview question.

### IAM Policy

Attached to an identity such as:

```text
User
Role
Group
```

It defines what that identity can do.

### Bucket Policy

Attached directly to:

```text
S3 Bucket
```

It defines access to that bucket's resources.

Simple:

```text
IAM Policy
     ↓
Identity

Bucket Policy
     ↓
S3 Bucket
```

---

# 🔟 S3 Block Public Access

S3 provides **Block Public Access** settings to help prevent unintended public exposure.

For private application data:

```text
Block Public Access
        ↓
       ON
```

This is an important security control.

### Best Practice

Keep buckets private unless public access is genuinely required.

---

# 🔐 S3 Encryption

S3 supports encryption for data at rest.

Common approaches include:

```text
SSE-S3
SSE-KMS
DSSE-KMS
Client-Side Encryption
```

---

# 🟢 SSE-S3

S3 manages the encryption keys.

```text
Object
  ↓
S3 Encryption
  ↓
Encrypted Data
```

---

# 🔵 SSE-KMS

Uses AWS KMS keys.

```text
S3
 │
 ↓
AWS KMS
 │
 ↓
Encryption
```

Useful when you need more control over:

* Key policies
* Permissions
* Auditing
* Key management

---

# 🔒 Encryption in Transit

Use HTTPS when accessing S3.

Example:

```text
HTTPS
  ↓
S3
```

A bucket policy can be used to deny requests that are not using secure transport.

---

# 1️⃣1️⃣ S3 Access Control

S3 access can involve:

```text
IAM Policies
Bucket Policies
Block Public Access
ACLs
Access Points
```

### Important

For most modern S3 use cases, **IAM policies and bucket policies** are preferred over relying on ACLs.

---

# 1️⃣2️⃣ S3 Access Point

An S3 Access Point provides a dedicated access endpoint and policy for a bucket.

Example:

```text
Application A
     │
     ↓
Access Point A
     │
     ↓
   Bucket

Application B
     │
     ↓
Access Point B
     │
     ↓
   Bucket
```

Useful for managing access for different applications or teams.

---

# 1️⃣3️⃣ S3 Replication

S3 can replicate objects between buckets.

Types include:

```text
Same-Region Replication
Cross-Region Replication
```

Architecture:

```text
Bucket A
   │
   │ Replication
   ↓
Bucket B
```

Cross-Region Replication can be useful for:

* Disaster recovery
* Compliance
* Geographic requirements
* Lower-latency access in another Region

---

# 🌎 Cross-Region Replication

Example:

```text
Mumbai
ap-south-1
   │
   ↓
S3 Bucket
   │
   │ CRR
   ↓
Singapore
ap-southeast-1
   │
   ↓
Backup Bucket
```

---

# 1️⃣4️⃣ S3 Static Website Hosting

S3 can host static websites.

Example:

```text
index.html
style.css
script.js
```

Architecture:

```text
User
 │
 ↓
S3
 │
 ├── index.html
 ├── style.css
 └── script.js
```

For production websites, S3 is often combined with:

```text
CloudFront
+
Route 53
+
ACM
```

Architecture:

```text
User
  │
  ↓
Route 53
  │
  ↓
CloudFront
  │
  ↓
S3
```

---

# 1️⃣5️⃣ S3 CLI Commands

## List Buckets

```bash
aws s3 ls
```

---

## Create Bucket

Example for a Region-aware CLI command:

```bash
aws s3api create-bucket \
  --bucket my-devops-bucket-2026 \
  --region ap-south-1 \
  --create-bucket-configuration LocationConstraint=ap-south-1
```

---

## List Bucket Contents

```bash
aws s3 ls s3://my-devops-bucket
```

---

## Upload File

```bash
aws s3 cp index.html s3://my-devops-bucket/
```

---

## Download File

```bash
aws s3 cp s3://my-devops-bucket/index.html .
```

---

## Upload Directory

```bash
aws s3 cp ./website s3://my-devops-bucket/website/ --recursive
```

---

## Download Directory

```bash
aws s3 cp s3://my-devops-bucket/website/ ./website/ --recursive
```

---

## Sync Directory

```bash
aws s3 sync ./website s3://my-devops-bucket/
```

---

## Sync from S3

```bash
aws s3 sync s3://my-devops-bucket ./backup
```

---

## Delete Object

```bash
aws s3 rm s3://my-devops-bucket/index.html
```

---

## Delete Directory

```bash
aws s3 rm s3://my-devops-bucket/logs/ --recursive
```

---

# 🧑‍💻 Useful S3 API Commands

### Get Bucket Versioning

```bash
aws s3api get-bucket-versioning \
  --bucket my-devops-bucket
```

### Get Bucket Encryption

```bash
aws s3api get-bucket-encryption \
  --bucket my-devops-bucket
```

### Get Bucket Location

```bash
aws s3api get-bucket-location \
  --bucket my-devops-bucket
```

### Get Bucket Policy

```bash
aws s3api get-bucket-policy \
  --bucket my-devops-bucket
```

---

# 🛡️ Securing an S3 Bucket

A good baseline:

```text
S3 Bucket
│
├── Block Public Access
│
├── Encryption
│
├── Versioning
│
├── IAM Policies
│
├── Bucket Policy
│
├── CloudTrail / Logging where appropriate
│
└── Lifecycle Rules
```

---

# 🔥 S3 Security Best Practices

```text
1. Keep buckets private by default.
2. Enable Block Public Access unless public access is required.
3. Use IAM roles instead of long-term access keys.
4. Enable encryption.
5. Use least-privilege policies.
6. Enable versioning for important data.
7. Use lifecycle policies for cost optimization.
8. Monitor access and changes.
9. Avoid storing secrets directly in S3 objects.
10. Use HTTPS for data in transit.
```

---

# 🧪 S3 Practical Tasks

# 🟢 Beginner

## Task 1 — Create S3 Bucket

Create a bucket in:

```text
ap-south-1
```

Example:

```text
my-devops-s3-bucket-2026
```

Verify:

```bash
aws s3 ls
```

---

## Task 2 — Upload and Download

Create:

```bash
echo "Hello S3" > test.txt
```

Upload:

```bash
aws s3 cp test.txt s3://my-devops-s3-bucket-2026/
```

Download:

```bash
aws s3 cp \
  s3://my-devops-s3-bucket-2026/test.txt \
  downloaded.txt
```

Verify:

```bash
cat downloaded.txt
```

---

## Task 3 — Create Folder Structure

Upload:

```text
website/
├── index.html
├── css/
└── images/
```

Command:

```bash
aws s3 sync ./website \
  s3://my-devops-s3-bucket-2026/website/
```

---

# 🟡 Intermediate

## Task 4 — Enable Versioning

Enable versioning:

```bash
aws s3api put-bucket-versioning \
  --bucket my-devops-s3-bucket-2026 \
  --versioning-configuration Status=Enabled
```

Upload:

```text
version 1
```

Modify the file and upload again:

```text
version 2
```

Verify object versions.

---

## Task 5 — Lifecycle Policy

Create a lifecycle policy such as:

```text
30 days
   ↓
Standard-IA

90 days
   ↓
Glacier

365 days
   ↓
Delete
```

Use this for old application logs or backups.

---

## Task 6 — S3 + IAM Role

Create:

```text
EC2
 ↓
IAM Role
 ↓
S3 Read Access
 ↓
S3 Bucket
```

From EC2:

```bash
aws s3 ls s3://my-devops-s3-bucket-2026
```

Do not store AWS access keys on the EC2 instance.

---

# 🔴 DevOps Scenario 1

### Scenario

Your application running on EC2 needs to upload backup files to S3.

A developer suggests putting:

```text
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
```

inside the application configuration.

### ❌ Don't do this

Long-term credentials on an EC2 server create unnecessary security risk.

### ✅ Better Architecture

```text
EC2
 │
 ↓
IAM Role
 │
 ↓
S3 Permissions
 │
 ↓
S3 Bucket
```

Give the role only the required permissions, such as:

```text
s3:PutObject
```

for the required bucket/path.

---

# 🔴 DevOps Scenario 2

### Scenario

A company stores application logs in S3.

Current situation:

```text
All logs
   ↓
S3 Standard
```

Logs older than 90 days are rarely accessed.

### Solution

Use lifecycle rules:

```text
New Logs
   ↓
S3 Standard
   ↓
After 30 days
   ↓
Standard-IA
   ↓
After 90 days
   ↓
Glacier
```

This can reduce storage costs.

---

# 🔴 DevOps Scenario 3

### Scenario

A production S3 bucket contains important files.

A developer accidentally deletes:

```text
production-config.json
```

How can you recover it?

### Solution

Enable:

```text
S3 Versioning
```

Then previous object versions can be restored according to the bucket's versioning state and retention strategy.

---

# 🔴 DevOps Scenario 4

### Scenario

An S3 bucket containing company data accidentally becomes publicly accessible.

What should you check?

```text
1. Block Public Access
2. Bucket Policy
3. IAM Policies
4. Object Ownership / ACL configuration
5. Access Points
6. CloudTrail / access logs where configured
```

Then remove unnecessary public access and apply least privilege.

---

# 🎯 S3 Interview Questions

### 1. What is S3?

S3 is AWS object storage used to store and retrieve data as objects inside buckets.

---

### 2. What is an S3 bucket?

A bucket is a container for S3 objects.

---

### 3. What is an S3 object?

An object is the data stored in S3 together with its key and metadata.

---

### 4. What is an S3 key?

The key is the unique name used to identify an object within a bucket.

---

### 5. Is S3 block storage?

No.

S3 is **object storage**.

---

### 6. What is S3 versioning?

Versioning maintains multiple versions of objects and helps recover from accidental overwrites or deletions.

---

### 7. What is an S3 lifecycle policy?

A lifecycle policy automatically transitions or deletes objects according to defined rules.

---

### 8. What is the difference between S3 Standard and Glacier?

S3 Standard is designed for frequently accessed data, while Glacier storage classes are designed primarily for archival workloads.

---

### 9. What is an S3 bucket policy?

A resource-based policy attached to an S3 bucket that controls access to bucket resources.

---

### 10. What is S3 Block Public Access?

A set of account/bucket-level controls designed to prevent public access to S3 resources.

---

### 11. What is S3 replication?

Replication automatically copies eligible objects from one S3 bucket to another bucket.

---

### 12. What is CRR?

CRR = **Cross-Region Replication**.

It replicates objects between S3 buckets in different AWS Regions.

---

### 13. Can S3 be encrypted?

Yes. S3 supports encryption at rest, including SSE-S3 and SSE-KMS.

---

### 14. What is the difference between EBS and S3?

```text
EBS → Block Storage
S3  → Object Storage
```

---

### 15. Can EC2 access S3 without access keys?

Yes.

Use an **IAM role attached to the EC2 instance**.

---

# 🚀 S3 + DevOps

S3 is frequently used in DevOps workflows.

## Terraform State

A Terraform backend can use S3 for remote state storage.

```text
Developer
    │
    ↓
Terraform
    │
    ↓
S3 Backend
    │
    ↓
terraform.tfstate
```

For team environments, state locking/coordination should also be configured using the current supported Terraform backend mechanisms.

---

# Jenkins + S3

A CI/CD pipeline can upload build artifacts:

```text
GitHub
   ↓
Jenkins
   ↓
Build
   ↓
Test
   ↓
Artifact
   ↓
S3
```

Example artifacts:

```text
application.zip
build.tar.gz
reports/
```

---

# Backup Architecture

```text
EC2
 │
 ↓
Backup
 │
 ↓
S3
 │
 ├── Versioning
 ├── Encryption
 └── Lifecycle
```

---

# Static Website Architecture

```text
                 User
                   │
                   ↓
                Route 53
                   │
                   ↓
               CloudFront
                   │
                   ↓
                 S3
                   │
        ┌──────────┼──────────┐
        │          │          │
     index.html   CSS       Images
```

---

# ⚡ S3 Quick Cheat Sheet

```text
S3
│
├── Bucket
│
├── Object
│
├── Key
│
├── Storage Classes
│   ├── Standard
│   ├── Intelligent-Tiering
│   ├── Standard-IA
│   ├── One Zone-IA
│   ├── Glacier Instant Retrieval
│   ├── Glacier Flexible Retrieval
│   └── Glacier Deep Archive
│
├── Versioning
│
├── Lifecycle
│
├── Encryption
│
├── Bucket Policies
│
├── Block Public Access
│
├── Replication
│   ├── SRR
│   └── CRR
│
└── Static Website Hosting
```

---

# 🔥 Golden Rules

```text
1. S3 = Object Storage.
2. Bucket names must be globally unique.
3. Keep buckets private by default.
4. Enable Block Public Access unless public access is required.
5. Use IAM roles for EC2-to-S3 access.
6. Enable versioning for important data.
7. Use lifecycle rules to optimize costs.
8. Encrypt sensitive data.
9. Use replication for appropriate DR/compliance requirements.
10. Use S3 for objects, EBS for block storage, and EFS for shared file storage.
```

---

# 📚 Important S3 Topics for DevOps Interviews

```text
✅ S3 Basics
✅ Buckets
✅ Objects
✅ Keys
✅ Storage Classes
✅ Durability vs Availability
✅ Versioning
✅ Lifecycle Policies
✅ Bucket Policies
✅ IAM Policies
✅ Block Public Access
✅ S3 Encryption
✅ SSE-S3
✅ SSE-KMS
✅ S3 Replication
✅ CRR
✅ Static Website Hosting
✅ S3 CLI
✅ S3 + IAM Role
✅ S3 + EC2
✅ S3 + Jenkins
✅ S3 + Terraform
✅ S3 Backup
✅ S3 Security
```

