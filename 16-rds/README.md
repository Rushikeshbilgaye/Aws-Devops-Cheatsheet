# 16. Amazon RDS

## 📌 Overview

**Amazon RDS (Relational Database Service)** is a managed AWS service that makes it easier to set up, operate, and scale relational databases.

Instead of manually managing a database server:

```text
EC2
 │
 ├── Install MySQL
 ├── Configure Database
 ├── Manage Backups
 ├── Apply Patches
 └── Monitor Server
```

RDS handles many of these operational tasks for you:

```text
Amazon RDS
 │
 ├── Automated Backups
 ├── Patching
 ├── Monitoring
 ├── High Availability
 ├── Scaling
 └── Recovery
```

---

# 🗄️ Supported Database Engines

Amazon RDS supports several relational database engines, including:

```text
MySQL
PostgreSQL
MariaDB
Oracle
Microsoft SQL Server
Amazon Aurora
```

### Important

**Amazon Aurora** is AWS's cloud-optimized relational database engine compatible with MySQL and PostgreSQL.

---

# 🏗️ RDS Architecture

A typical architecture looks like:

```text
                         Internet
                            │
                            ▼
                     Application Load
                         Balancer
                            │
                            ▼
                         EC2 / ECS
                            │
                            ▼
                    ┌───────────────┐
                    │      RDS      │
                    │   Database    │
                    └───────────────┘
```

For production:

```text
                  Application
                       │
                       ▼
                Private Subnet
                       │
                       ▼
              ┌─────────────────┐
              │   RDS Primary    │
              └────────┬────────┘
                       │
                 Synchronous
                   standby
                       │
                       ▼
              ┌─────────────────┐
              │   RDS Standby    │
              └─────────────────┘
```

---

# 🎯 Why Use RDS?

Without RDS:

```text
Engineer
   │
   ▼
EC2
   │
   ├── Install DB
   ├── Configure DB
   ├── Patch DB
   ├── Backup DB
   ├── Monitor DB
   └── Handle failures
```

With RDS:

```text
Engineer
   │
   ▼
Amazon RDS
   │
   ├── AWS manages infrastructure
   ├── Automated backups
   ├── Patching
   ├── Monitoring
   └── High availability options
```

RDS lets teams focus more on the application and database rather than the underlying server administration.

---

# 🔑 Important RDS Concepts

## 1. DB Instance

A DB instance is the compute environment running the database engine.

Example:

```text
RDS
 │
 └── DB Instance
       │
       └── MySQL
```

---

# 2. DB Instance Class

The instance class determines the compute and memory resources available to the database.

Example:

```text
db.t3.micro
db.t4g.micro
db.m7g.large
```

Choose the instance class based on:

* CPU requirements
* Memory requirements
* Workload
* Cost
* Database engine compatibility

---

# 3. Storage

RDS uses storage volumes for database data.

Common options include:

```text
General Purpose SSD
Provisioned IOPS SSD
Magnetic / legacy options where supported
```

For modern workloads, SSD-based storage is commonly used.

---

# 4. Storage Autoscaling

RDS can automatically increase allocated storage when configured appropriately.

Example:

```text
Initial Storage
      │
      ▼
     20 GB
      │
      ▼
Storage Usage Increasing
      │
      ▼
Storage Autoscaling
      │
      ▼
     30 GB
```

This helps prevent the database from running out of storage.

---

# 🔐 RDS Security

RDS should normally be placed in **private subnets** when it doesn't need direct internet access.

Typical architecture:

```text
Internet
   │
   ▼
ALB
   │
   ▼
Private Application Subnet
   │
   ▼
Private Database Subnet
   │
   ▼
RDS
```

---

# 🛡️ Security Groups

RDS uses security groups to control network access.

Example:

```text
Application Security Group
            │
            │ TCP 3306
            ▼
       RDS Security Group
```

For MySQL:

```text
Port: 3306
```

For PostgreSQL:

```text
Port: 5432
```

### Important

Do **not** normally allow:

```text
0.0.0.0/0
```

to your database port.

Instead, allow access from the application's security group.

Example:

```text
Web/App SG
    │
    │ 3306
    ▼
RDS SG
```

---

# 🌐 DB Subnet Group

A **DB subnet group** defines the subnets where RDS can place database instances.

For production, use subnets in multiple Availability Zones.

Example:

```text
VPC
│
├── AZ-a
│   └── Private DB Subnet
│
└── AZ-b
    └── Private DB Subnet
```

RDS can then use these subnets for high-availability deployments.

---

# 🚀 Multi-AZ

**Multi-AZ** provides high availability by maintaining a standby database instance in another Availability Zone.

Architecture:

```text
                Application
                     │
                     ▼
                RDS Primary
                     │
            Synchronous Replication
                     │
                     ▼
                RDS Standby
                  AZ-B
```

If the primary database becomes unavailable, RDS can fail over to the standby.

### Important

**Multi-AZ is primarily for high availability and failover, not for read scaling.**

---

# 📖 Read Replicas

Read Replicas are used to scale read-heavy workloads.

Architecture:

```text
                 Application
                    │
            ┌───────┴───────┐
            ▼               ▼
         Primary         Read Replica
            │               │
            └──── Replication
```

Applications can send read traffic to replicas.

Example:

```text
Write requests
      │
      ▼
Primary DB

Read requests
      │
      ├──► Primary
      │
      └──► Read Replica
```

---

# ⚖️ Multi-AZ vs Read Replica

| Feature      | Multi-AZ                                          | Read Replica            |
| ------------ | ------------------------------------------------- | ----------------------- |
| Main purpose | High availability                                 | Read scaling            |
| Standby      | Yes                                               | No                      |
| Read traffic | Not designed for scaling                          | Yes                     |
| Failover     | Automatic failover support                        | Not the primary purpose |
| Typical use  | Production HA                                     | Read-heavy applications |
| Replication  | Synchronous for supported Multi-AZ configurations | Asynchronous            |

### Easy way to remember

```text
Multi-AZ     → Availability
Read Replica → Read Scaling
```

---

# 💾 Automated Backups

RDS supports automated backups.

These can include:

* Database snapshots
* Transaction logs
* Point-in-time recovery

Example:

```text
Monday
  │
  ▼
Backup
  │
  ▼
Tuesday
  │
  ▼
Backup
  │
  ▼
Wednesday
  │
  ▼
Failure
  │
  ▼
Point-in-Time Recovery
```

---

# 📸 DB Snapshots

A DB snapshot is a point-in-time backup of an RDS database.

Example:

```text
RDS
 │
 ▼
DB Snapshot
 │
 ▼
Stored Backup
```

Snapshots can be used to restore a database or create another database instance.

---

# 🔄 Point-in-Time Recovery

Point-in-time recovery allows you to restore a database to a specific point in time within the available backup retention period.

Example:

```text
10:00 ───── 11:00 ───── 12:00 ───── 13:00
             │
             ▼
       Accidental deletion

Restore to:
11:55
```

This is useful for recovering from accidental changes or data corruption.

---

# 🔐 Encryption

RDS supports encryption at rest using **AWS KMS**.

Encryption can protect:

* Database storage
* Automated backups
* Read replicas
* Snapshots

Architecture:

```text
Application
     │
     ▼
    RDS
     │
     ▼
AWS KMS Encryption
```

For production databases, encryption should be carefully planned from the beginning because changing encryption configuration later may require migration or restoration workflows.

---

# 🔒 Encryption in Transit

Applications can connect to RDS using encrypted connections such as TLS/SSL when supported by the database engine.

Example:

```text
Application
     │
     │ TLS/SSL
     ▼
    RDS
```

---

# 👤 Authentication

RDS supports database authentication using normal database credentials.

Some engines also support IAM-based database authentication.

Example:

```text
Application
     │
     ▼
IAM Authentication
     │
     ▼
RDS
```

IAM database authentication can reduce the need to distribute long-lived database passwords in supported configurations.

---

# 📊 RDS Monitoring

RDS integrates with **Amazon CloudWatch**.

Common metrics include:

```text
CPUUtilization
DatabaseConnections
FreeStorageSpace
FreeableMemory
ReadIOPS
WriteIOPS
ReadLatency
WriteLatency
```

Example:

```text
RDS
 │
 ▼
CloudWatch
 │
 ├── CPU
 ├── Memory
 ├── Storage
 ├── Connections
 └── IOPS
```

---

# 🔎 Enhanced Monitoring

RDS Enhanced Monitoring provides more detailed operating-system-level metrics for supported configurations.

It can provide information such as:

```text
CPU
Memory
Processes
Disk activity
```

This is useful for deeper troubleshooting.

---

# 📜 RDS Events

RDS generates events for important database operations.

Examples:

```text
DB instance reboot
Failover
Backup
Maintenance
Configuration changes
```

These events can be monitored through AWS services such as EventBridge and SNS.

---

# 🛠️ AWS CLI Commands

## List RDS Instances

```bash
aws rds describe-db-instances
```

---

## Describe a Specific Database

```bash
aws rds describe-db-instances \
  --db-instance-identifier mydb
```

---

## Create DB Instance

Example:

```bash
aws rds create-db-instance \
  --db-instance-identifier mydb \
  --db-instance-class db.t3.micro \
  --engine mysql \
  --allocated-storage 20 \
  --master-username admin \
  --manage-master-user-password
```

The exact options available depend on the engine and RDS configuration.

---

## Stop DB Instance

```bash
aws rds stop-db-instance \
  --db-instance-identifier mydb
```

Not every RDS configuration supports stopping, and stopped databases can be automatically restarted after a period of time.

---

## Start DB Instance

```bash
aws rds start-db-instance \
  --db-instance-identifier mydb
```

---

## Reboot DB Instance

```bash
aws rds reboot-db-instance \
  --db-instance-identifier mydb
```

---

# 📸 Create Snapshot

```bash
aws rds create-db-snapshot \
  --db-instance-identifier mydb \
  --db-snapshot-identifier mydb-snapshot
```

---

# 📋 List Snapshots

```bash
aws rds describe-db-snapshots
```

---

# 🔄 Restore Snapshot

```bash
aws rds restore-db-instance-from-db-snapshot \
  --db-instance-identifier mydb-restore \
  --db-snapshot-identifier mydb-snapshot
```

---

# 🗑️ Delete Database

```bash
aws rds delete-db-instance \
  --db-instance-identifier mydb \
  --skip-final-snapshot
```

⚠️ This can cause data loss. In production, carefully consider backups and a final snapshot before deletion.

---

# 🧰 RDS Parameter Groups

A **DB parameter group** controls database engine configuration.

Examples include settings related to:

```text
Memory
Connections
Logging
Performance
Engine behavior
```

Architecture:

```text
RDS
 │
 ▼
DB Parameter Group
 │
 ▼
Database Configuration
```

---

# 🌐 RDS Option Groups

Some database engines support **option groups** that enable additional engine-specific features.

```text
RDS
 │
 └── Option Group
       │
       └── Engine-specific options
```

---

# 🔧 Maintenance

RDS handles many maintenance tasks, including supported database engine and infrastructure maintenance.

You can configure maintenance windows so that disruptive maintenance is scheduled during an appropriate period.

Example:

```text
Maintenance Window
        │
        ▼
Sunday 02:00 - 03:00
```

---

# 📈 Scaling RDS

RDS can scale in different ways.

## Vertical Scaling

Increase the DB instance class:

```text
db.t3.micro
     │
     ▼
db.t3.small
     │
     ▼
db.t3.medium
```

More CPU and memory become available.

---

## Storage Scaling

Increase storage capacity:

```text
20 GB
 │
 ▼
50 GB
 │
 ▼
100 GB
```

Storage autoscaling can also be configured.

---

## Read Scaling

Use Read Replicas:

```text
                Primary
                   │
        ┌──────────┼──────────┐
        ▼          ▼          ▼
      Read       Read       Read
     Replica    Replica    Replica
```

---

# 🚀 RDS in Three-Tier Architecture

A common DevOps architecture:

```text
                  Internet
                     │
                     ▼
             Application Load
                Balancer
                     │
                     ▼
             Application Tier
                     │
                     ▼
              Private Subnet
                     │
                     ▼
                 Amazon RDS
```

Example:

```text
┌─────────────────────────────────────┐
│                VPC                  │
│                                     │
│  Public Subnet                      │
│  ┌───────────────────────────────┐  │
│  │ Application Load Balancer     │  │
│  └───────────────┬───────────────┘  │
│                  │                  │
│  Private App Subnet                 │
│  ┌───────────────▼───────────────┐  │
│  │ EC2 / ECS Application         │  │
│  └───────────────┬───────────────┘  │
│                  │                  │
│  Private DB Subnet                  │
│  ┌───────────────▼───────────────┐  │
│  │          Amazon RDS           │  │
│  └───────────────────────────────┘  │
│                                     │
└─────────────────────────────────────┘
```

---

# 🔥 RDS + Secrets Manager

Database credentials should not be hard-coded into application code.

Better architecture:

```text
Application
    │
    ▼
Secrets Manager
    │
    ▼
Database Credentials
    │
    ▼
RDS
```

This is especially useful for production applications.

---

# 🔥 RDS + CloudWatch + SNS

A monitoring architecture:

```text
RDS
 │
 ▼
CloudWatch
 │
 ▼
Alarm
 │
 ▼
SNS
 │
 ▼
Email / Notification
```

Example:

```text
CPU > 80%
   │
   ▼
CloudWatch Alarm
   │
   ▼
SNS
   │
   ▼
DevOps Team
```

---

# 🔐 RDS Security Best Practices

## 1. Use Private Subnets

Do not expose the database directly to the internet unless there is a specific architectural requirement.

---

## 2. Restrict Security Groups

Allow database traffic only from the application tier.

Example:

```text
Application SG
      │
      │ TCP 3306
      ▼
Database SG
```

---

## 3. Enable Encryption

Use KMS encryption for sensitive workloads.

---

## 4. Enable Automated Backups

Configure an appropriate backup retention period.

---

## 5. Use Multi-AZ for Production HA

For workloads requiring high availability, use a suitable Multi-AZ configuration.

---

## 6. Protect Credentials

Use:

```text
Secrets Manager
or
Parameter Store
```

instead of storing passwords in Git.

---

## 7. Monitor Database Health

Use CloudWatch metrics and RDS events.

---

# 💼 Real-World DevOps Scenario

### Scenario

You have an application running on EC2 and a MySQL database on RDS.

```text
User
 │
 ▼
ALB
 │
 ▼
EC2
 │
 │ TCP 3306
 ▼
RDS MySQL
```

The application cannot connect to the database.

### Troubleshooting Steps

#### Step 1: Check RDS status

```bash
aws rds describe-db-instances \
  --db-instance-identifier mydb
```

Check:

```text
DBInstanceStatus
```

---

#### Step 2: Check Security Groups

Verify:

```text
EC2/App SG
      │
      ▼
RDS SG
      │
      └── TCP 3306 allowed
```

---

#### Step 3: Check Subnets

Verify that the RDS DB subnet group uses appropriate subnets.

---

#### Step 4: Check DNS / Endpoint

Retrieve the RDS endpoint:

```bash
aws rds describe-db-instances \
  --db-instance-identifier mydb \
  --query 'DBInstances[0].Endpoint.Address'
```

---

#### Step 5: Test Connectivity

From the application server:

```bash
nc -zv <RDS-ENDPOINT> 3306
```

For PostgreSQL:

```bash
nc -zv <RDS-ENDPOINT> 5432
```

---

# ⚖️ RDS vs EC2 Database

| Feature            | RDS                 | Database on EC2     |
| ------------------ | ------------------- | ------------------- |
| AWS managed        | ✅                   | ❌                   |
| Automated backups  | ✅                   | Manual/configurable |
| Patching           | AWS-managed aspects | You manage          |
| OS access          | ❌                   | ✅                   |
| Database control   | Managed             | Full control        |
| High availability  | Built-in options    | You design it       |
| Maintenance        | Reduced             | More responsibility |
| Operational effort | Lower               | Higher              |

### Easy way to remember

```text
RDS → Managed Database

EC2 Database → You manage the server + database
```

---

# ⚖️ RDS vs DynamoDB

| Feature       | RDS                              | DynamoDB                                |
| ------------- | -------------------------------- | --------------------------------------- |
| Type          | Relational                       | NoSQL                                   |
| Schema        | Structured                       | Flexible                                |
| SQL           | ✅                                | ❌ Traditional SQL                       |
| Joins         | ✅                                | ❌                                       |
| Transactions  | ✅                                | ✅                                       |
| Scaling model | Primarily instance/storage based | Serverless-style managed scaling        |
| Best for      | Relational applications          | High-scale key-value/document workloads |

---

# 🎯 Interview Questions

### 1. What is Amazon RDS?

Amazon RDS is a managed relational database service that simplifies database deployment, operation, backup, patching, and scaling.

### 2. Which database engines does RDS support?

Common engines include:

```text
MySQL
PostgreSQL
MariaDB
Oracle
SQL Server
Aurora
```

### 3. What is Multi-AZ?

Multi-AZ provides high availability by maintaining a standby database in another Availability Zone and supporting automatic failover.

### 4. Does Multi-AZ improve read performance?

**No.**

Multi-AZ is primarily designed for **high availability and failover**.

### 5. What is a Read Replica?

A Read Replica is a replicated database instance used primarily to scale read workloads.

### 6. Multi-AZ vs Read Replica?

```text
Multi-AZ     → High Availability
Read Replica → Read Scaling
```

### 7. What is a DB subnet group?

It defines the subnets in which RDS can place database resources.

### 8. How do you secure an RDS database?

Use:

```text
Private subnets
Security groups
Encryption
IAM where supported
Secrets Manager
Backups
Least privilege
```

### 9. How do you troubleshoot an application that cannot connect to RDS?

Check:

```text
1. RDS status
2. Security groups
3. Network ACLs
4. Route tables
5. DB subnet group
6. RDS endpoint
7. DNS resolution
8. Database port
9. Credentials
10. CloudWatch metrics/events
```

### 10. RDS vs database on EC2?

```text
RDS → Managed service

EC2 → You manage OS + database
```

---

# 🧠 Quick Revision

```text
Amazon RDS
│
├── Managed Relational Database
│
├── MySQL
├── PostgreSQL
├── MariaDB
├── Oracle
├── SQL Server
└── Aurora
│
├── Multi-AZ
│
├── Read Replicas
│
├── Automated Backups
│
├── DB Snapshots
│
├── Point-in-Time Recovery
│
├── Encryption
│
├── Security Groups
│
├── DB Subnet Groups
│
├── Parameter Groups
│
├── CloudWatch
│
└── RDS Events
```

## ⭐ Must-Remember

```text
Multi-AZ
    ↓
High Availability

Read Replica
    ↓
Read Scaling

Snapshot
    ↓
Point-in-Time Backup

CloudWatch
    ↓
Monitoring

Security Group
    ↓
Network Access Control

Secrets Manager
    ↓
Database Credentials
```

> **RDS = Managed Relational Database**

> **Multi-AZ = High Availability**

> **Read Replica = Read Scaling**

> **For DevOps interviews, focus heavily on Multi-AZ vs Read Replica, backups, snapshots, security groups, DB subnet groups, encryption, monitoring, and RDS troubleshooting.**
