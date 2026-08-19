# ☁️ Cloud Basics Cheat Sheet

> A quick-reference guide to Cloud Computing fundamentals for DevOps engineers and interview preparation.

---

## 📌 Table of Contents

* [What is Cloud Computing?](#-what-is-cloud-computing)
* [Why Cloud Computing?](#-why-cloud-computing)
* [Traditional IT vs Cloud](#-traditional-it-vs-cloud)
* [Cloud Service Models](#-cloud-service-models)
* [Cloud Deployment Models](#-cloud-deployment-models)
* [Key Cloud Characteristics](#-key-cloud-characteristics)
* [AWS Global Infrastructure](#-aws-global-infrastructure)
* [AWS Regions](#-aws-regions)
* [Availability Zones](#-availability-zones)
* [Edge Locations](#-edge-locations)
* [Scalability vs Elasticity](#-scalability-vs-elasticity)
* [High Availability](#-high-availability)
* [Fault Tolerance](#-fault-tolerance)
* [Disaster Recovery](#-disaster-recovery)
* [CapEx vs OpEx](#-capex-vs-opex)
* [Important AWS Services](#-important-aws-services)
* [DevOps Perspective](#-devops-perspective)
* [Interview Questions](#-interview-questions)
* [Scenario-Based Questions](#-scenario-based-questions)
* [Quick Revision](#-quick-revision)

---

# ☁️ What is Cloud Computing?

Cloud computing is the **on-demand delivery of IT resources over the internet**.

Instead of purchasing and maintaining physical servers, organizations can use cloud resources such as:

* Compute
* Storage
* Databases
* Networking
* Security
* Monitoring
* Messaging
* Serverless services

with a **pay-as-you-go** pricing model.

### Simple Example

Traditional approach:

```text
Company
   ↓
Buy Physical Server
   ↓
Install OS
   ↓
Configure Network
   ↓
Install Application
   ↓
Maintain Hardware
```

Cloud approach:

```text
Company
   ↓
AWS
   ↓
Launch EC2
   ↓
Configure Application
   ↓
Users Access Application
```

---

# 🚀 Why Cloud Computing?

### Major benefits

| Benefit           | Explanation                                              |
| ----------------- | -------------------------------------------------------- |
| On-demand         | Resources can be created when required                   |
| Scalability       | Increase or decrease resources                           |
| Elasticity        | Automatically adjust resources based on demand           |
| High Availability | Applications can remain available during failures        |
| Global Reach      | Deploy applications in different geographical locations  |
| Pay-as-you-go     | Pay for resources used                                   |
| Automation        | Infrastructure can be automated                          |
| Security          | Cloud providers offer multiple security mechanisms       |
| Disaster Recovery | Backup and recovery can be designed using cloud services |

---

# 🏢 Traditional IT vs Cloud

| Traditional IT                                | Cloud                                      |
| --------------------------------------------- | ------------------------------------------ |
| Buy physical servers                          | Rent cloud resources                       |
| Large upfront investment                      | Pay-as-you-go                              |
| Manual scaling                                | Easy/automatic scaling                     |
| Hardware maintenance                          | Cloud provider manages infrastructure      |
| Limited geographical reach                    | Global infrastructure                      |
| Long provisioning time                        | Resources can be provisioned quickly       |
| Hardware failures are customer responsibility | Provider manages underlying infrastructure |

---

# 🧩 Cloud Service Models

There are three major cloud service models:

```text
                 Cloud Computing
                       │
          ┌────────────┼────────────┐
          ↓            ↓            ↓
         IaaS         PaaS         SaaS
```

## 1. IaaS — Infrastructure as a Service

Provides infrastructure such as:

* Virtual machines
* Storage
* Networking
* Load balancers

### AWS Examples

* EC2
* EBS
* VPC

### Example

```text
You manage:
Application
OS
Runtime
Configuration

AWS manages:
Physical Server
Networking Hardware
Data Center
```

---

## 2. PaaS — Platform as a Service

Provides a managed platform where developers can deploy applications without managing the underlying infrastructure.

Examples:

* AWS Elastic Beanstalk
* AWS App Runner

The cloud provider manages much of the infrastructure and platform layer.

---

## 3. SaaS — Software as a Service

A complete software application delivered over the internet.

Examples:

* Gmail
* Microsoft 365
* Salesforce

Users generally don't manage the underlying infrastructure.

---

# 🌍 Cloud Deployment Models

## 1. Public Cloud

Infrastructure is provided by a cloud provider and shared among multiple customers using isolated environments.

Examples:

* AWS
* Microsoft Azure
* Google Cloud

---

## 2. Private Cloud

Cloud infrastructure dedicated to a single organization.

Example:

```text
Company
   ↓
Private Cloud
   ↓
Internal Applications
```

---

## 3. Hybrid Cloud

Combination of public and private cloud.

```text
Private Cloud
      │
      │
      ↓
   Hybrid
      ↑
      │
Public Cloud
```

Example:

A company keeps sensitive systems in a private environment while running scalable web applications on AWS.

---

# 🌎 AWS Global Infrastructure

AWS infrastructure is organized into several important components.

```text
AWS Global Infrastructure
          │
    ┌─────┴─────┐
    ↓           ↓
 Regions    Edge Locations
    │
    ↓
Availability Zones
```

---

# 🌍 AWS Regions

An AWS **Region** is a separate geographical area containing multiple Availability Zones.

Examples:

* Mumbai
* Singapore
* Frankfurt
* London
* US East

### Why use multiple regions?

For:

* Disaster recovery
* Global applications
* Lower latency
* Regulatory requirements
* Business continuity

---

# 🏢 Availability Zones

An Availability Zone (AZ) is one or more discrete data centers within an AWS Region with independent infrastructure.

Example:

```text
Mumbai Region
│
├── AZ-a
├── AZ-b
└── AZ-c
```

For high availability, deploy workloads across multiple AZs.

```text
             Load Balancer
                  │
          ┌───────┴───────┐
          ↓               ↓
        AZ-a             AZ-b
          │               │
        EC2             EC2
```

---

# 📍 Edge Locations

Edge Locations are locations used by AWS services such as **CloudFront** to deliver content closer to users.

Example:

```text
User
 ↓
Nearest Edge Location
 ↓
CloudFront
 ↓
Origin
```

This helps reduce latency for content delivery.

---

# 📈 Scalability vs Elasticity

These two concepts are commonly asked in interviews.

## Scalability

The ability to handle increasing workload by increasing resources.

### Vertical Scaling

Increase resources of an existing server.

```text
2 CPU
 ↓
8 CPU
```

### Horizontal Scaling

Add more servers.

```text
EC2
 ↓
EC2 + EC2
 ↓
EC2 + EC2 + EC2
```

---

## Elasticity

The ability to automatically add or remove resources according to workload.

Example:

```text
Low Traffic
    ↓
2 EC2

High Traffic
    ↓
5 EC2

Low Traffic
    ↓
2 EC2
```

AWS Auto Scaling is commonly used to implement this behavior.

---

# 🟢 High Availability

High Availability means designing systems to remain available even when some components fail.

Example:

```text
             Load Balancer
                  │
          ┌───────┴───────┐
          ↓               ↓
        AZ-a             AZ-b
          │               │
        EC2             EC2
```

If one Availability Zone experiences a problem, traffic can potentially be served from another AZ.

---

# 🛡️ Fault Tolerance

Fault tolerance means a system can continue operating despite failures.

Example:

```text
Application
   │
   ├── Server 1
   ├── Server 2
   └── Server 3
```

If Server 1 fails, the remaining servers continue serving requests.

---

# 🔄 Disaster Recovery

Disaster Recovery (DR) is the process of recovering systems and data after a major failure.

Common DR strategies include:

```text
Backup & Restore
      ↓
Pilot Light
      ↓
Warm Standby
      ↓
Multi-Site / Active-Active
```

### Important terms

**RTO — Recovery Time Objective**

How quickly the system should be restored.

**RPO — Recovery Point Objective**

How much data loss is acceptable.

Example:

```text
RPO = 1 hour

Maximum acceptable data loss ≈ 1 hour
```

---

# 💰 CapEx vs OpEx

## CapEx — Capital Expenditure

Money spent upfront on physical infrastructure.

Example:

```text
Buy:
Servers
Storage
Networking
Data Center
```

---

## OpEx — Operational Expenditure

Pay for resources as operational expenses.

Example:

```text
AWS
 ↓
Use EC2
 ↓
Pay for usage
```

Cloud computing generally shifts organizations toward an **OpEx-oriented consumption model**.

---

# 🧰 Important AWS Services

| Requirement            | AWS Service            |
| ---------------------- | ---------------------- |
| Virtual Server         | EC2                    |
| Object Storage         | S3                     |
| Block Storage          | EBS                    |
| Virtual Network        | VPC                    |
| Identity & Access      | IAM                    |
| Load Balancing         | Elastic Load Balancing |
| Auto Scaling           | EC2 Auto Scaling       |
| DNS                    | Route 53               |
| Monitoring             | CloudWatch             |
| Auditing               | CloudTrail             |
| Messaging              | SNS / SQS              |
| Database               | RDS                    |
| Serverless Compute     | Lambda                 |
| Infrastructure as Code | CloudFormation         |

---

# 👨‍💻 Cloud from a DevOps Perspective

As a DevOps engineer, you should understand how cloud services work together.

Example production architecture:

```text
                    Users
                      │
                      ↓
                  Route 53
                      │
                      ↓
              Application Load
                 Balancer
                      │
             ┌────────┴────────┐
             ↓                 ↓
           EC2               EC2
             │                 │
             └────────┬────────┘
                      ↓
                     RDS
```

Monitoring:

```text
EC2 ──────────┐
ALB ──────────┤
RDS ──────────┼──→ CloudWatch
              │
              └──→ SNS → Notification
```

Security:

```text
IAM
 │
 ├── EC2
 ├── S3
 ├── RDS
 └── Lambda
```

---

# 🔐 Shared Responsibility Model

One of the most important AWS concepts.

AWS is responsible for **security of the cloud**.

Customers are responsible for **security in the cloud**, depending on the service.

```text
              AWS
               │
       Security OF Cloud
               │
     ┌─────────┴─────────┐
     │                   │
Data Centers          Hardware
Networking            Physical Security
```

Customer responsibilities can include:

```text
Customer
   │
   ├── IAM permissions
   ├── Data
   ├── OS configuration
   ├── Security Groups
   ├── Application security
   └── Encryption configuration
```

The exact responsibility varies by AWS service.

---

# 📌 Important Cloud Terms

| Term            | Meaning                                          |
| --------------- | ------------------------------------------------ |
| Region          | Geographical AWS location                        |
| AZ              | Isolated infrastructure location inside a Region |
| Scalability     | Ability to handle increased workload             |
| Elasticity      | Automatically adjust resources                   |
| Availability    | System remains accessible                        |
| Fault Tolerance | System continues despite failures                |
| RTO             | Target recovery time                             |
| RPO             | Acceptable data-loss window                      |
| CapEx           | Upfront infrastructure spending                  |
| OpEx            | Operational/usage-based spending                 |
| IaaS            | Infrastructure as a Service                      |
| PaaS            | Platform as a Service                            |
| SaaS            | Software as a Service                            |

---

# 🎯 Interview Questions

### Q1. What is cloud computing?

Cloud computing is the on-demand delivery of computing resources such as compute, storage, networking, and databases over the internet, typically using a pay-as-you-go model.

---

### Q2. What are the three cloud service models?

**IaaS, PaaS, and SaaS.**

---

### Q3. What is the difference between a Region and an Availability Zone?

A **Region** is a geographical AWS area containing multiple Availability Zones.

An **Availability Zone** is an isolated infrastructure location within a Region.

---

### Q4. Why do we deploy applications across multiple Availability Zones?

To improve availability and resilience against failures affecting a single AZ.

---

### Q5. What is scalability?

Scalability is the ability of a system to handle increasing workload by increasing available resources.

---

### Q6. What is elasticity?

Elasticity is the ability to automatically increase or decrease resources according to demand.

---

### Q7. What is the difference between vertical and horizontal scaling?

**Vertical scaling:** increase resources of an existing machine.

**Horizontal scaling:** add more machines.

---

### Q8. What is high availability?

High availability is designing a system so that it remains accessible despite failures of individual components.

---

### Q9. What is RTO?

Recovery Time Objective defines the target amount of time within which a system should be restored after a disruption.

---

### Q10. What is RPO?

Recovery Point Objective defines the maximum acceptable amount of data loss measured in time.

---

### Q11. What is the AWS Shared Responsibility Model?

AWS manages security **of** the cloud infrastructure, while customers manage security **in** the cloud according to the service being used.

---

# 🔥 Scenario-Based Questions

## Scenario 1

**Your application is running on a single EC2 instance. The instance fails and your website becomes unavailable. How would you improve the architecture?**

### Answer

I would:

1. Deploy EC2 instances across multiple Availability Zones.
2. Place an Application Load Balancer in front of them.
3. Configure an Auto Scaling Group.
4. Configure health checks.
5. Monitor the infrastructure using CloudWatch.

Architecture:

```text
             Route 53
                 │
                 ↓
              ALB
           ┌─────┴─────┐
           ↓           ↓
         AZ-a         AZ-b
           │           │
         EC2          EC2
```

---

## Scenario 2

**Traffic suddenly increases from 1,000 users to 100,000 users. What cloud concept helps your application handle this?**

### Answer

I would use **scalability and elasticity**.

For an AWS application, I could use:

```text
ALB
 ↓
Auto Scaling Group
 ↓
Multiple EC2 Instances
```

The Auto Scaling Group can add instances when demand increases and remove unnecessary instances when demand decreases.

---

## Scenario 3

**Your company wants to serve users from India, Europe, and the US with low latency. What would you consider?**

### Answer

I would consider:

* Deploying workloads in appropriate AWS Regions.
* Using Amazon CloudFront for content delivery.
* Using Route 53 for DNS-based routing.
* Selecting infrastructure locations based on latency, compliance, availability, and cost requirements.

---

# 🧪 Hands-On Tasks

### Beginner

* [ ] Create an AWS account
* [ ] Explore the AWS Management Console
* [ ] Identify AWS Regions
* [ ] Identify Availability Zones
* [ ] Launch an EC2 instance
* [ ] Create an S3 bucket
* [ ] Explore the VPC console
* [ ] Explore IAM

### Intermediate

* [ ] Create an EC2 instance in a VPC
* [ ] Create public and private subnets
* [ ] Configure a Security Group
* [ ] Deploy EC2 instances in two AZs
* [ ] Configure an Application Load Balancer
* [ ] Configure Auto Scaling

### DevOps

* [ ] Deploy a web application on EC2
* [ ] Put an ALB in front of the application
* [ ] Configure Auto Scaling
* [ ] Monitor EC2 using CloudWatch
* [ ] Create an SNS alert
* [ ] Create the infrastructure using Terraform
* [ ] Create a CI/CD pipeline using Jenkins

---

# ⚡ Quick Revision

```text
Cloud Computing
│
├── IaaS
│   └── EC2 / EBS / VPC
│
├── PaaS
│   └── Elastic Beanstalk
│
├── SaaS
│   └── Complete applications
│
├── Global Infrastructure
│   ├── Region
│   ├── Availability Zone
│   └── Edge Location
│
├── Architecture
│   ├── Scalability
│   ├── Elasticity
│   ├── High Availability
│   └── Fault Tolerance
│
├── Disaster Recovery
│   ├── RTO
│   └── RPO
│
└── Cost
    ├── CapEx
    └── OpEx
```

---

# 💡 Interview Tip

Don't answer AWS interview questions with definitions only.

Use this pattern:

```text
Definition
    ↓
Why is it needed?
    ↓
AWS service/example
    ↓
Real-world scenario
```

### Example

**Question:** What is Auto Scaling?

**Strong answer:**

> Auto Scaling automatically adjusts the number of compute resources based on application demand. In AWS, I can use an EC2 Auto Scaling Group with an Application Load Balancer to distribute traffic across instances and maintain application availability. For example, during a traffic spike, the Auto Scaling Group can launch additional EC2 instances and terminate them when demand decreases.
