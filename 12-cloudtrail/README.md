# 12. AWS CloudTrail

## 📌 Overview

**AWS CloudTrail** is an AWS service that records and tracks **API activity and account actions** in your AWS environment.

It helps answer questions like:

* Who created an EC2 instance?
* Who deleted an S3 bucket?
* When was an IAM policy changed?
* Which IP address performed an API call?
* What AWS service made a particular request?

### Simple Architecture

```text
User / AWS Service / Application
              │
              ▼
        AWS API Request
              │
              ▼
         AWS CloudTrail
              │
        ┌─────┴─────┐
        ▼           ▼
   CloudWatch       S3
        │
        ▼
    Monitoring
    & Alerts
```

---

# 🔍 What is CloudTrail?

CloudTrail provides **governance, compliance, operational auditing, and security auditing** of your AWS account.

It records API activity such as:

```text
CreateInstance
TerminateInstances
CreateBucket
DeleteBucket
CreateUser
AttachRolePolicy
PutObject
```

CloudTrail records information about the request, including:

* Who made the request
* What action was performed
* When it happened
* Which AWS service was involved
* Source IP address
* Region
* Request parameters
* Response information

---

# 🎯 Why Do We Need CloudTrail?

Imagine an EC2 instance suddenly gets terminated.

Without CloudTrail:

```text
EC2 Instance
     │
     ▼
Terminated ❌
     │
     ▼
Who did it?
Unknown
```

With CloudTrail:

```text
EC2 Instance
     │
     ▼
TerminateInstances API
     │
     ▼
CloudTrail Event
     │
     ├── User
     ├── Time
     ├── IP Address
     └── Request Details
```

You can investigate what happened.

---

# 🏗️ CloudTrail Architecture

```text
                   AWS Account
                       │
       ┌───────────────┼────────────────┐
       ▼               ▼                ▼
      IAM             EC2              S3
       │               │                │
       └───────────────┼────────────────┘
                       ▼
                  CloudTrail
                       │
             ┌─────────┴─────────┐
             ▼                   ▼
        CloudWatch               S3
             │
             ▼
       Alerts / Monitoring
```

---

# 📦 CloudTrail Events

CloudTrail records different types of events.

## 1. Management Events

Management events record control-plane operations.

Examples:

```text
CreateInstance
TerminateInstances
CreateUser
DeleteUser
CreateRole
PutRolePolicy
CreateBucket
DeleteBucket
```

They are useful for tracking changes to AWS resources.

---

## 2. Data Events

Data events track operations performed on resources.

Examples:

```text
S3 Object Access
Lambda Function Invocation
```

For S3:

```text
GetObject
PutObject
DeleteObject
```

Data events can generate a large number of events, so configure them carefully.

---

## 3. Insights Events

CloudTrail Insights helps identify **unusual API activity**.

Example:

```text
Normal API activity
       │
       ▼
100 API calls/hour

Suddenly:

       ▼
5000 API calls/hour
       │
       ▼
CloudTrail Insights
       │
       ▼
Potential unusual activity
```

This can help detect unusual operational or security behavior.

---

# 📊 Management vs Data Events

| Feature          | Management Events      | Data Events                     |
| ---------------- | ---------------------- | ------------------------------- |
| Purpose          | Track resource changes | Track resource-level operations |
| Example          | Create EC2             | S3 GetObject                    |
| Default          | Commonly enabled       | Usually configured selectively  |
| Volume           | Lower                  | Can be very high                |
| Example services | IAM, EC2, VPC          | S3, Lambda                      |

---

# 🪣 CloudTrail and S3

CloudTrail logs can be delivered to an **Amazon S3 bucket**.

Example:

```text
AWS Account
     │
     ▼
CloudTrail
     │
     ▼
S3 Bucket
     │
     ▼
Log Storage
```

This is useful for:

* Long-term auditing
* Compliance
* Security investigations
* Historical analysis

### Recommended practice

Use a dedicated S3 bucket for CloudTrail logs and protect it using appropriate access controls.

---

# ☁️ CloudTrail and CloudWatch

CloudTrail can integrate with **CloudWatch Logs**.

Example:

```text
CloudTrail
    │
    ▼
CloudWatch Logs
    │
    ▼
Metric Filter
    │
    ▼
CloudWatch Alarm
    │
    ▼
SNS Notification
    │
    ▼
Email / Notification
```

### Real-world example

Detect root user activity:

```text
Root Account Activity
        │
        ▼
    CloudTrail
        │
        ▼
 CloudWatch Logs
        │
        ▼
      Alarm
        │
        ▼
       SNS
        │
        ▼
      Email
```

---

# 🧾 CloudTrail Event Structure

A CloudTrail event contains information similar to:

```json
{
  "eventName": "TerminateInstances",
  "eventSource": "ec2.amazonaws.com",
  "awsRegion": "ap-south-1",
  "sourceIPAddress": "203.0.113.10",
  "userIdentity": {
    "type": "IAMUser"
  }
}
```

Important fields include:

```text
eventTime
eventName
eventSource
awsRegion
sourceIPAddress
userIdentity
requestParameters
responseElements
```

---

# 👤 User Identity

CloudTrail helps identify the identity responsible for an API request.

Possible identity types include:

* IAM user
* IAM role
* AWS account/root user
* AWS service
* Federated user

Example:

```text
Who deleted the EC2 instance?

        │
        ▼
    CloudTrail
        │
        ▼
userIdentity
        │
        ▼
IAM Role: JenkinsRole
```

This is extremely useful during troubleshooting.

---

# 🌍 Source IP Address

CloudTrail records the source IP address associated with many API requests.

Example:

```text
User
 │
 ▼
AWS API
 │
 ▼
CloudTrail
 │
 └──► Source IP: x.x.x.x
```

This can help during security investigations.

---

# 🔐 CloudTrail Security Best Practices

## 1. Enable CloudTrail

Ensure API activity is being recorded for the AWS environment.

---

## 2. Centralize Logs

For multiple AWS accounts, consider sending logs to a centralized logging account/bucket.

```text
Account A ──┐
Account B ──┼──► Central S3 Bucket
Account C ──┘
```

---

## 3. Protect the S3 Bucket

Use:

* Least-privilege IAM
* S3 Block Public Access
* Encryption
* Bucket policies
* Versioning where appropriate

---

## 4. Enable Log File Validation

CloudTrail log file validation can help detect whether log files have been modified after delivery.

---

## 5. Monitor Critical API Calls

Examples:

```text
DeleteBucket
TerminateInstances
CreateUser
DeleteUser
PutBucketPolicy
AttachRolePolicy
CreateAccessKey
```

---

# 🛠️ AWS CLI Commands

## Check CloudTrail Trails

```bash
aws cloudtrail describe-trails
```

---

## List Trails

```bash
aws cloudtrail list-trails
```

---

## Get Trail Status

```bash
aws cloudtrail get-trail-status \
  --name my-trail
```

---

## Start Logging

```bash
aws cloudtrail start-logging \
  --name my-trail
```

---

## Stop Logging

```bash
aws cloudtrail stop-logging \
  --name my-trail
```

---

## Lookup Events

```bash
aws cloudtrail lookup-events
```

---

## Lookup Specific Events

```bash
aws cloudtrail lookup-events \
  --lookup-attributes AttributeKey=EventName,AttributeValue=TerminateInstances
```

---

# 🔎 Troubleshooting Example

### Problem

An EC2 instance was unexpectedly terminated.

### Step 1: Check CloudTrail

```bash
aws cloudtrail lookup-events \
  --lookup-attributes AttributeKey=EventName,AttributeValue=TerminateInstances
```

### Step 2: Check the event

Look for:

```text
eventTime
userIdentity
sourceIPAddress
requestParameters
```

### Step 3: Identify the actor

Example:

```text
User Identity:
JenkinsRole

Source IP:
10.x.x.x

Action:
TerminateInstances
```

### Step 4: Investigate Jenkins

If the action came from Jenkins, check:

```text
Jenkins Job
     │
     ▼
AWS CLI / Terraform
     │
     ▼
TerminateInstances
```

This is a very common DevOps troubleshooting scenario.

---

# 💼 DevOps Real-World Example

Suppose your company has multiple AWS accounts:

```text
              AWS Organization
                     │
       ┌─────────────┼─────────────┐
       ▼             ▼             ▼
   Dev Account   Test Account   Prod Account
       │             │             │
       ▼             ▼             ▼
    CloudTrail    CloudTrail    CloudTrail
       │             │             │
       └─────────────┼─────────────┘
                     ▼
             Central Log Storage
                     │
                     ▼
                 Security Team
```

CloudTrail provides an audit trail across the environment.

---

# 🚨 CloudTrail + CloudWatch + SNS

A powerful security monitoring architecture:

```text
                 AWS API Activity
                        │
                        ▼
                   CloudTrail
                        │
                        ▼
                 CloudWatch Logs
                        │
                        ▼
                  Metric Filter
                        │
                        ▼
                 CloudWatch Alarm
                        │
                        ▼
                       SNS
                        │
              ┌─────────┴─────────┐
              ▼                   ▼
            Email              Operations
```

Example:

```text
IAM Policy Changed
       │
       ▼
CloudTrail records event
       │
       ▼
CloudWatch detects event
       │
       ▼
Alarm triggered
       │
       ▼
SNS notification
```

---

# ⚖️ CloudTrail vs CloudWatch

| Feature           | CloudTrail            | CloudWatch                       |
| ----------------- | --------------------- | -------------------------------- |
| Main purpose      | API auditing          | Monitoring                       |
| Tracks API calls  | ✅                     | Can consume related logs/metrics |
| Metrics           | Limited event metrics | ✅                                |
| Logs              | API activity          | Application/service/system logs  |
| Alarms            | Via integrations      | ✅                                |
| Security auditing | ✅                     | Supporting role                  |
| Troubleshooting   | API activity          | Performance & logs               |

### Easy way to remember

```text
CloudTrail → WHO did WHAT?
CloudWatch → HOW is the system performing?
```

---

# 🎯 Interview Questions

### 1. What is AWS CloudTrail?

CloudTrail is an AWS service that records API activity and account actions for auditing, security, and troubleshooting.

### 2. Why is CloudTrail important?

It helps identify:

* Who performed an action
* What action was performed
* When it happened
* Where the request originated
* Which AWS resource was affected

### 3. What are CloudTrail management events?

They record control-plane operations such as creating, modifying, and deleting AWS resources.

### 4. What are data events?

They record resource-level operations such as S3 object access and Lambda function invocations.

### 5. Where can CloudTrail logs be stored?

CloudTrail can deliver logs to destinations such as:

* Amazon S3
* CloudWatch Logs

### 6. Can CloudTrail monitor S3 object-level activity?

**Yes**, using S3 data events.

### 7. How can you find who terminated an EC2 instance?

Use CloudTrail and search for the `TerminateInstances` event.

Check:

```text
userIdentity
eventTime
sourceIPAddress
requestParameters
```

### 8. What is CloudTrail Insights?

CloudTrail Insights helps identify unusual patterns in API activity.

### 9. CloudTrail vs CloudWatch?

```text
CloudTrail  → API auditing
CloudWatch  → Monitoring and observability
```

### 10. How would you monitor unauthorized AWS changes?

A common architecture is:

```text
CloudTrail
   ↓
CloudWatch Logs
   ↓
Metric Filter
   ↓
CloudWatch Alarm
   ↓
SNS
   ↓
Security Team
```

---

# 🧠 Quick Revision

```text
AWS CloudTrail
│
├── API Auditing
├── Management Events
├── Data Events
├── Insights
├── User Identity
├── Source IP
├── Event Time
├── S3 Log Storage
├── CloudWatch Integration
└── Security Investigation
```

## ⭐ Remember

> **CloudTrail = Audit Trail**

> **CloudTrail answers: "WHO did WHAT, WHEN, and from WHERE?"**

> **CloudWatch answers: "HOW is my system performing?"**
