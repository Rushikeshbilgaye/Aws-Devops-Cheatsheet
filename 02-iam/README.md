# 🔐 AWS IAM — Identity and Access Management

AWS IAM (Identity and Access Management) is a service used to securely control **who can access AWS resources and what actions they can perform**.

---

## 📌 What is IAM?

**IAM = Identity and Access Management**

IAM allows you to:

* 👤 Create users
* 👥 Create groups
* 🎭 Create roles
* 📜 Create policies
* 🔑 Manage access permissions
* 🔒 Implement least-privilege access
* 🛡️ Secure AWS resources

IAM is a **global AWS service**.

---

# 🏗️ IAM Core Components

IAM mainly consists of:

```text
                    AWS IAM
                       │
        ┌──────────────┼──────────────┐
        │              │              │
      Users          Groups          Roles
        │              │              │
        └──────────────┼──────────────┘
                       │
                    Policies
                       │
                  Permissions
```

---

# 1️⃣ IAM Users

An IAM user represents a person or application that needs access to AWS.

Example:

```text
Developer
   ↓
IAM User
   ↓
AWS Resources
```

A user can have:

* Username
* Password
* Access Key ID
* Secret Access Key
* Permissions

### Create IAM User

AWS Console:

```text
IAM
→ Users
→ Create user
```

AWS CLI:

```bash
aws iam create-user --user-name dev-user
```

---

# 2️⃣ IAM Groups

A group is a collection of IAM users.

Instead of assigning permissions individually:

```text
User 1 → Permission
User 2 → Permission
User 3 → Permission
```

Create a group:

```text
Developers
    │
    ├── User 1
    ├── User 2
    └── User 3
```

Then attach permissions to the group.

### Create Group

```bash
aws iam create-group --group-name Developers
```

Add user to group:

```bash
aws iam add-user-to-group \
  --user-name dev-user \
  --group-name Developers
```

---

# 3️⃣ IAM Roles

An IAM role provides temporary permissions to an AWS service, user, or application.

Common example:

```text
EC2
 │
 │ Assume Role
 ↓
IAM Role
 │
 ↓
S3
```

Instead of storing AWS credentials inside an EC2 server, attach an IAM role to EC2.

### Common Use Cases

IAM roles are commonly used with:

* EC2
* Lambda
* ECS
* EKS
* CloudFormation
* Cross-account access

### Important Interview Point

> **IAM users normally represent identities, while IAM roles provide temporary credentials and permissions that can be assumed.**

---

# 4️⃣ IAM Policies

Policies define **what actions are allowed or denied**.

Policies are written in JSON.

Example:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-bucket/*"
    }
  ]
}
```

This policy allows:

```text
s3:GetObject
```

on objects inside:

```text
my-bucket
```

---

# 📜 IAM Policy Structure

A policy generally contains:

```text
Version
Statement
    │
    ├── Effect
    ├── Action
    ├── Resource
    └── Condition
```

### Effect

Can be:

```text
Allow
Deny
```

Example:

```json
"Effect": "Allow"
```

---

## Action

Defines what operation can be performed.

Examples:

```text
s3:GetObject
s3:PutObject
ec2:StartInstances
ec2:StopInstances
```

---

## Resource

Defines which AWS resource the policy applies to.

Example:

```json
"Resource": "arn:aws:s3:::my-bucket/*"
```

---

## Condition

Adds additional restrictions.

Example:

```json
"Condition": {
  "StringEquals": {
    "aws:RequestedRegion": "ap-south-1"
  }
}
```

---

# 5️⃣ Managed Policies

AWS provides two types of managed policies.

### AWS Managed Policies

Created and maintained by AWS.

Examples:

```text
AmazonS3ReadOnlyAccess
AmazonEC2FullAccess
AdministratorAccess
```

### Customer Managed Policies

Created and managed by you.

Example:

```text
DevOpsS3ReadOnlyPolicy
```

---

# 6️⃣ Inline Policies

An inline policy is directly embedded into:

* User
* Group
* Role

Example:

```text
IAM Role
   │
   └── Inline Policy
```

Inline policies are tightly associated with one identity.

---

# 🔑 IAM Access Keys

Access keys are mainly used for programmatic access.

They consist of:

```text
Access Key ID
Secret Access Key
```

Example:

```text
AWS CLI
   ↓
Access Key
   ↓
AWS API
```

Configure credentials:

```bash
aws configure
```

You will be asked for:

```text
AWS Access Key ID
AWS Secret Access Key
Default region
Output format
```

### ⚠️ Security Rule

Never commit AWS access keys to GitHub.

❌ Don't do:

```bash
git add .env
```

if `.env` contains AWS credentials.

Use:

```text
IAM Roles
Environment Variables
AWS Secrets Manager
AWS Systems Manager Parameter Store
```

where appropriate.

---

# 🛡️ Root User vs IAM User

| Feature               | Root User | IAM User               |
| --------------------- | --------- | ---------------------- |
| Created automatically | Yes       | No                     |
| Full account access   | Yes       | Depends on permissions |
| Daily usage           | ❌ Avoid   | ✅ Preferred            |
| MFA                   | ✅ Enable  | ✅ Enable               |
| Access keys           | Avoid     | Only when required     |

### Interview Answer

> The AWS root user has complete access to the AWS account. It should not be used for daily operations. IAM users, groups, and roles should be used instead.

---

# 🔐 MFA

MFA = **Multi-Factor Authentication**

It adds another authentication factor.

Example:

```text
Password
   +
MFA Code
   ↓
AWS Account
```

MFA should especially be enabled for the root user.

---

# 🧠 Principle of Least Privilege

Give users and applications **only the permissions they actually need**.

Bad:

```text
Developer
   ↓
AdministratorAccess
```

Better:

```text
Developer
   ↓
Only required permissions
   ↓
Specific AWS resources
```

Example:

Instead of:

```text
s3:*
```

use:

```text
s3:GetObject
```

when read-only access is sufficient.

---

# ⚖️ IAM Policy Evaluation Logic

A simplified evaluation model:

```text
Explicit Deny
     ↓
   DENIED
```

If there is no explicit deny:

```text
Explicit Allow
     ↓
   ALLOWED
```

If there is neither:

```text
Implicit Deny
     ↓
   DENIED
```

### Important Rule

> **Explicit Deny overrides Allow.**

---

# 🔄 IAM Authentication vs Authorization

### Authentication

Answers:

> **Who are you?**

Example:

```text
Username + Password
```

### Authorization

Answers:

> **What are you allowed to do?**

Example:

```text
Allow s3:GetObject
```

---

# 🏷️ IAM ARN

ARN = **Amazon Resource Name**

It uniquely identifies AWS resources.

Example:

```text
arn:aws:s3:::my-bucket
```

Example EC2 ARN:

```text
arn:aws:ec2:ap-south-1:123456789012:instance/i-1234567890abcdef0
```

General format:

```text
arn:
  partition:
  service:
  region:
  account-id:
  resource
```

---

# 🔗 Trust Policy vs Permission Policy

This is a very important interview topic.

## Permission Policy

Defines:

> What can this identity do?

Example:

```text
Role → S3 Read Access
```

## Trust Policy

Defines:

> Who can assume this role?

Example:

```text
EC2
 ↓
AssumeRole
 ↓
IAM Role
```

The role's trust policy can allow the EC2 service to assume the role.

---

# 🖥️ IAM Role with EC2

Recommended architecture:

```text
             EC2
              │
              │ Assume Role
              ↓
          IAM Role
              │
              ↓
        S3 Permissions
              │
              ↓
          S3 Bucket
```

Avoid:

```text
EC2
 │
 ├── Access Key
 └── Secret Key
```

---

# 🧑‍💻 Useful AWS CLI Commands

### List users

```bash
aws iam list-users
```

### List groups

```bash
aws iam list-groups
```

### List roles

```bash
aws iam list-roles
```

### Get user

```bash
aws iam get-user
```

### Create user

```bash
aws iam create-user --user-name dev-user
```

### Delete user

```bash
aws iam delete-user --user-name dev-user
```

### List attached user policies

```bash
aws iam list-attached-user-policies \
  --user-name dev-user
```

### List role policies

```bash
aws iam list-role-policies \
  --role-name EC2-S3-Role
```

---

# 🧪 IAM Practical Tasks

## 🟢 Beginner

### Task 1 — Create IAM User

Create:

```text
dev-user
```

Give the user appropriate read-only permissions.

---

### Task 2 — Create IAM Group

Create:

```text
Developers
```

Add:

```text
dev-user
```

Attach an appropriate managed policy.

---

### Task 3 — Create S3 Read-Only Policy

Create a customer-managed policy that allows:

```text
s3:ListBucket
s3:GetObject
```

for a specific bucket.

---

# 🟡 Intermediate

### Task 4 — EC2 IAM Role

Create:

```text
EC2-S3-Role
```

Allow EC2 to assume the role.

Give the role permission to read objects from a specific S3 bucket.

Attach the role to an EC2 instance.

Test:

```bash
aws s3 ls
```

from the EC2 instance.

---

### Task 5 — Least Privilege

Create a policy that allows a developer to:

```text
Start EC2 instances
Stop EC2 instances
```

but does not allow:

```text
TerminateInstances
```

---

### Task 6 — MFA

Enable MFA for an IAM user and verify that authentication requires the additional factor.

---

# 🔴 Interview Scenario

### Scenario

A developer needs to access files from an S3 bucket using an EC2 instance.

The developer currently wants to store:

```text
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
```

inside the EC2 application configuration.

### ❌ Bad Approach

Store permanent credentials on the server.

### ✅ Better Approach

Create an IAM role:

```text
EC2
 ↓
IAM Role
 ↓
S3 Read Permissions
 ↓
S3 Bucket
```

Attach the IAM role to the EC2 instance.

### Why?

* No hard-coded credentials
* Temporary credentials
* Easier credential rotation
* Better security
* Follows least privilege

---

# 🎯 IAM Interview Questions

### 1. What is IAM?

IAM is an AWS service used to manage identities and control access to AWS resources.

### 2. Is IAM regional?

No. IAM is a **global service**.

### 3. What are the main IAM components?

```text
Users
Groups
Roles
Policies
```

### 4. What is an IAM role?

A role is an identity with permissions that can be assumed by trusted entities such as AWS services, users, or applications.

### 5. What is the difference between a user and a role?

A user represents a persistent AWS identity, while a role is designed to be assumed and generally provides temporary credentials.

### 6. What is least privilege?

Giving an identity only the permissions required to perform its job.

### 7. What overrides an Allow?

An **explicit Deny**.

### 8. What is a trust policy?

A trust policy specifies **who or what is allowed to assume a role**.

### 9. Should you use the root user for daily tasks?

No.

### 10. Why use IAM roles with EC2?

To avoid storing long-term AWS credentials on the EC2 instance.

---

# 🚀 DevOps Important IAM Topics

For a DevOps interview, focus strongly on:

```text
✅ IAM Users
✅ IAM Groups
✅ IAM Roles
✅ IAM Policies
✅ Managed vs Inline Policies
✅ Trust Policies
✅ Permission Policies
✅ Least Privilege
✅ MFA
✅ Access Keys
✅ AWS CLI Authentication
✅ EC2 IAM Roles
✅ Cross-Account Roles
✅ Policy Evaluation
✅ Explicit Deny vs Allow
✅ ARN
```

---

# ⚡ Quick IAM Cheat Sheet

```text
IAM
│
├── Users
│
├── Groups
│
├── Roles
│
├── Policies
│   ├── AWS Managed
│   ├── Customer Managed
│   └── Inline
│
├── Authentication
│   ├── Password
│   ├── Access Keys
│   └── MFA
│
└── Authorization
    └── Permissions
```

### Golden Rules

```text
1. Don't use root for daily operations.
2. Enable MFA.
3. Follow least privilege.
4. Don't hard-code AWS credentials.
5. Prefer IAM roles for AWS services.
6. Explicit Deny overrides Allow.
7. Keep policies as specific as possible.
8. Use roles instead of long-term credentials whenever possible.
```

---

## 📁 Recommended Folder Structure

```text
aws-cheat-sheet/
│
├── 01-cloud-basics/
│   └── README.md
│
├── 02-iam/
│   └── README.md
│
├── 03-ec2/
│   └── README.md
│
├── 04-ebs/
│   └── README.md
│
├── 05-s3/
│   └── README.md
│
└── ...
```


