# 💾 AWS EBS — Elastic Block Store

Amazon EBS (Elastic Block Store) provides **persistent block storage volumes for EC2 instances**.

EBS is commonly used for:

* Operating system disks
* Application data
* Databases
* Logs
* Docker data
* Jenkins data
* Persistent application storage

---

# 📌 What is EBS?

**EBS = Elastic Block Store**

Think of EBS as a **virtual hard disk attached to an EC2 instance**.

```text
                 AWS Cloud
                     │
                    EC2
                     │
              ┌──────┴──────┐
              │             │
           OS Disk       EBS Volume
              │             │
              │          Application
              │             Data
              └─────────────┘
```

Unlike instance store, EBS storage is designed to persist independently of the instance's lifecycle.

---

# 🧠 EBS vs EC2

A very common interview question:

| EC2                     | EBS                        |
| ----------------------- | -------------------------- |
| Compute service         | Storage service            |
| Provides virtual server | Provides block storage     |
| Runs applications       | Stores data                |
| Has CPU and RAM         | Provides storage capacity  |
| Can attach EBS volumes  | EBS can be attached to EC2 |

Simple way to remember:

```text
EC2 = Computer
EBS = Hard Disk
```

---

# 🏗️ EBS Architecture

```text
                 Availability Zone
                        │
                 ┌──────┴──────┐
                 │             │
                EC2           EBS
                 │             │
                 └──────┬──────┘
                        │
                    Block Storage
```

### Important

An EBS volume is associated with a specific **Availability Zone**.

For example:

```text
ap-south-1a
   │
   ├── EC2
   └── EBS
```

You generally cannot directly attach that EBS volume to an EC2 instance in:

```text
ap-south-1b
```

Instead, you can create a snapshot and restore it as a new volume in another Availability Zone.

---

# 1️⃣ EBS Volume

An EBS volume is a virtual block device.

Example:

```text
EC2
 │
 ├── Root Volume
 │
 └── Data Volume
```

You can use EBS volumes for:

```text
/           → Operating System
/data       → Application Data
/var/lib   → Application Storage
```

---

# 2️⃣ Root Volume

The root volume contains the operating system.

Example:

```text
EC2
 │
 └── /dev/sda1
       │
       └── Ubuntu
```

For Ubuntu, you may see devices such as:

```text
/dev/sda1
```

or NVMe device names such as:

```text
/dev/nvme0n1
```

depending on the instance and AMI.

---

# 3️⃣ Additional EBS Volume

You can attach additional EBS storage.

Example:

```text
EC2
 │
 ├── Root Volume
 │
 └── Data Volume
       │
       └── /data
```

This is useful when you want application data separated from the operating system.

---

# 📦 EBS Volume Types

Important EBS volume families include:

```text
General Purpose SSD
Provisioned IOPS SSD
Throughput Optimized HDD
Cold HDD
```

---

# 🟢 1. gp3

**gp3 = General Purpose SSD**

A common default choice for many workloads.

Good for:

* Web servers
* Applications
* Development
* Jenkins
* General databases
* CI/CD workloads

Benefits:

* SSD-based
* Configurable IOPS
* Configurable throughput
* Good price/performance

Example:

```text
100 GB gp3
```

---

# 🟢 2. gp2

**gp2 = General Purpose SSD**

Older general-purpose SSD volume type.

Performance is tied more closely to volume size than gp3.

For many new workloads, **gp3 is generally preferred**.

---

# 🔵 3. io2

**io2 = Provisioned IOPS SSD**

Designed for workloads requiring:

* High IOPS
* High durability
* Consistent performance

Common examples:

```text
High-performance databases
Critical applications
IO-intensive workloads
```

---

# 🟡 4. st1

**st1 = Throughput Optimized HDD**

Designed for workloads where throughput matters more than low latency.

Examples:

```text
Big data
Log processing
Data warehouses
Large sequential workloads
```

---

# 🟠 5. sc1

**sc1 = Cold HDD**

Designed for workloads with infrequent access where low cost is more important than performance.

---

# 📊 EBS Volume Comparison

| Type | Storage | Best For                        |
| ---- | ------- | ------------------------------- |
| gp3  | SSD     | General workloads               |
| gp2  | SSD     | Older general-purpose workloads |
| io2  | SSD     | High IOPS / critical workloads  |
| st1  | HDD     | High-throughput workloads       |
| sc1  | HDD     | Infrequently accessed data      |

### Interview Tip

For a general-purpose application:

```text
gp3
```

is usually a strong starting point.

---

# 4️⃣ EBS IOPS

IOPS = **Input/Output Operations Per Second**

It measures how many input/output operations a storage volume can perform per second.

Example:

```text
10,000 IOPS
```

means the volume can support up to a specified level of I/O operations per second, subject to the volume and instance limits.

---

# 5️⃣ EBS Throughput

Throughput represents how much data can be transferred per second.

Usually measured in:

```text
MB/s
```

Example:

```text
250 MB/s
```

---

# ⚖️ IOPS vs Throughput

### IOPS

Important for:

```text
Small random I/O
Databases
Transaction-heavy workloads
```

### Throughput

Important for:

```text
Large sequential I/O
Big data
Log processing
Data processing
```

Simple example:

```text
Database
   ↓
High IOPS

Large file processing
   ↓
High Throughput
```

---

# 6️⃣ EBS Snapshots

An EBS snapshot is a **point-in-time backup of an EBS volume**.

Architecture:

```text
EBS Volume
    │
    ↓
 Snapshot
    │
    ├── Backup
    └── Restore
```

Example:

```text
EC2
 │
 └── EBS
      │
      ↓
   Snapshot
      │
      ↓
New EBS Volume
```

---

# 🔄 Snapshot Use Cases

Snapshots are useful for:

* Backup
* Disaster recovery
* Creating new volumes
* Migrating data
* Creating AMIs
* Copying data between Regions

---

# 🌎 Cross-Region EBS Snapshot

EBS volumes are AZ-specific.

Snapshots can be copied to another AWS Region.

Example:

```text
Mumbai Region
ap-south-1
     │
     ↓
 EBS Snapshot
     │
     ↓
Copy Snapshot
     │
     ↓
Singapore Region
ap-southeast-1
```

Then create a new EBS volume from the copied snapshot.

---

# 7️⃣ EBS Encryption

EBS supports encryption.

Encryption helps protect:

```text
Data at rest
Data stored in snapshots
Data stored in volumes
```

AWS KMS keys can be used for EBS encryption.

Architecture:

```text
EC2
 │
EBS
 │
Encryption
 │
AWS KMS
```

### Best Practice

Use encryption for EBS volumes whenever appropriate, especially for sensitive workloads.

---

# 8️⃣ Attach EBS Volume

Suppose you have:

```text
EC2
```

and:

```text
100 GB EBS Volume
```

You can attach it to the EC2 instance.

```text
Before:

EC2


After:

EC2
 │
 ├── Root Volume
 │
 └── 100 GB EBS
```

---

# 9️⃣ Mount EBS Volume on Ubuntu

After attaching an EBS volume, identify disks:

```bash
lsblk
```

Example:

```text
NAME        SIZE
nvme0n1      20G
├─nvme0n1p1  20G
nvme1n1     100G
```

Here:

```text
nvme1n1
```

could be the newly attached EBS volume.

---

# 🔧 Format the Volume

For a new volume:

```bash
sudo mkfs -t ext4 /dev/nvme1n1
```

⚠️ Be careful with the device name. Formatting the wrong device can destroy data.

---

# 📁 Create Mount Point

```bash
sudo mkdir /data
```

Mount:

```bash
sudo mount /dev/nvme1n1 /data
```

Verify:

```bash
df -h
```

---

# 🔄 Make Mount Persistent

If you mount manually:

```bash
sudo mount /dev/nvme1n1 /data
```

the mount may not automatically return after a reboot.

Use `/etc/fstab`.

First get the UUID:

```bash
sudo blkid /dev/nvme1n1
```

Example:

```text
UUID="abc123..."
```

Edit:

```bash
sudo nano /etc/fstab
```

Add:

```text
UUID=abc123... /data ext4 defaults,nofail 0 2
```

Then test:

```bash
sudo mount -a
```

If there are no errors, verify:

```bash
df -h
```

---

# 🔟 EBS Resize

Suppose you have:

```text
20 GB EBS
```

and need:

```text
50 GB
```

You can modify the EBS volume size.

```text
20 GB
 ↓
Modify Volume
 ↓
50 GB
```

After increasing the volume, you may also need to expand the partition and filesystem inside the operating system.

For an ext4 filesystem, one common command is:

```bash
sudo resize2fs /dev/nvme1n1
```

The exact command depends on the device and filesystem.

---

# 🛡️ EBS Backup Strategy

A simple backup architecture:

```text
EC2
 │
 EBS
 │
 ↓
Snapshot
 │
 ├── Backup
 ├── Disaster Recovery
 └── Restore
```

For production environments, consider using AWS Backup and appropriate snapshot lifecycle policies rather than relying only on manual snapshots.

---

# 🔥 EBS vs Instance Store

This is an important interview question.

| EBS                             | Instance Store                                                              |
| ------------------------------- | --------------------------------------------------------------------------- |
| Persistent block storage        | Temporary local storage                                                     |
| Can survive instance stop/start | Data can be lost when instance is stopped/terminated depending on lifecycle |
| Can be snapshotted              | No EBS-style snapshots                                                      |
| Network-attached storage        | Physically attached to host                                                 |
| Good for persistent data        | Good for temporary/high-speed data                                          |

Simple:

```text
EBS
↓
Persistent

Instance Store
↓
Temporary
```

---

# 🔥 EBS vs S3

Another important interview question.

| EBS                                   | S3                                 |
| ------------------------------------- | ---------------------------------- |
| Block storage                         | Object storage                     |
| Usually attached to EC2               | Accessed through APIs/HTTP         |
| Designed for filesystem-style access  | Designed for objects               |
| Low-latency storage for EC2 workloads | Highly durable object storage      |
| AZ-specific volumes                   | Regional service                   |
| Can be used as OS/data disk           | Cannot be used as an EC2 boot disk |

Simple:

```text
EBS = Hard Disk

S3 = Object Storage
```

---

# 🔥 EBS vs EFS

| EBS                          | EFS                                      |
| ---------------------------- | ---------------------------------------- |
| Block storage                | File storage                             |
| Typically attached to one AZ | Regional file system                     |
| Usually used with EC2        | Can be mounted by multiple EC2 instances |
| Great for OS/data disks      | Great for shared files                   |
| Filesystem created on volume | Managed network filesystem               |

Example:

```text
EBS:

EC2 ─── EBS


EFS:

EC2 ──┐
      ├── EFS
EC2 ──┤
      │
EC2 ──┘
```

---

# 🧑‍💻 Important AWS CLI Commands

## List EBS Volumes

```bash
aws ec2 describe-volumes
```

---

## Create EBS Volume

Example:

```bash
aws ec2 create-volume \
  --availability-zone ap-south-1a \
  --size 20 \
  --volume-type gp3
```

---

## Attach Volume

```bash
aws ec2 attach-volume \
  --volume-id vol-1234567890abcdef0 \
  --instance-id i-1234567890abcdef0 \
  --device /dev/sdf
```

---

## Detach Volume

```bash
aws ec2 detach-volume \
  --volume-id vol-1234567890abcdef0
```

---

## Modify Volume

```bash
aws ec2 modify-volume \
  --volume-id vol-1234567890abcdef0 \
  --size 50
```

---

## Create Snapshot

```bash
aws ec2 create-snapshot \
  --volume-id vol-1234567890abcdef0 \
  --description "EBS backup"
```

---

## List Snapshots

```bash
aws ec2 describe-snapshots \
  --owner-ids self
```

---

# 🧪 EBS Practical Tasks

## 🟢 Beginner

### Task 1 — Create EBS Volume

Create:

```text
20 GB
gp3
```

in the same Availability Zone as your EC2 instance.

Attach it to EC2.

---

### Task 2 — Mount EBS

On Ubuntu:

```bash
lsblk
```

Format:

```bash
sudo mkfs -t ext4 /dev/nvme1n1
```

Create directory:

```bash
sudo mkdir /data
```

Mount:

```bash
sudo mount /dev/nvme1n1 /data
```

Verify:

```bash
df -h
```

---

### Task 3 — Store Data

Create:

```bash
echo "Hello from EBS" | sudo tee /data/test.txt
```

Verify:

```bash
cat /data/test.txt
```

---

# 🟡 Intermediate

## Task 4 — Persistent Mount

Configure `/etc/fstab` so `/data` is automatically mounted after reboot.

Test:

```bash
sudo reboot
```

After reconnecting:

```bash
df -h
```

---

## Task 5 — Snapshot and Restore

Create:

```text
EBS Volume
    ↓
Snapshot
    ↓
New Volume
    ↓
Attach to EC2
```

Verify that your data exists on the restored volume.

---

## Task 6 — Resize EBS

Start with:

```text
20 GB
```

Increase to:

```text
40 GB
```

Then expand the filesystem inside the operating system.

Verify:

```bash
df -h
```

---

# 🔴 DevOps Scenario

### Scenario

Your EC2 server has:

```text
20 GB EBS
```

The application stores logs under:

```text
/var/log
```

After several weeks, the disk is almost full.

You need to increase storage without rebuilding the server.

### Solution

```text
Current EBS
20 GB
  ↓
Modify Volume
  ↓
50 GB
  ↓
Expand Partition/Filesystem
  ↓
Verify
```

Check disk:

```bash
df -h
```

Check block devices:

```bash
lsblk
```

Then expand the appropriate partition/filesystem.

### Important Interview Point

> Increasing the EBS volume size does not automatically mean the filesystem inside the operating system has already been expanded.

---

# 🔴 Production Scenario

### Scenario

An EC2 instance accidentally terminates and the application data disappears.

How can you prevent this?

### Better Architecture

```text
Application
    │
    ↓
EBS
    │
    ↓
Snapshots
    │
    ↓
Backup / DR
```

Use:

```text
EBS Snapshots
AWS Backup
Snapshot lifecycle policies
Cross-Region copies when required
```

Also make sure critical application data is not unnecessarily tied to a single EC2 instance.

---

# 🎯 EBS Interview Questions

### 1. What is EBS?

EBS is a persistent block storage service designed primarily for use with EC2.

---

### 2. What does EBS stand for?

**Elastic Block Store.**

---

### 3. Is EBS persistent?

Yes. EBS is persistent block storage, unlike instance store.

---

### 4. What is gp3?

gp3 is a general-purpose SSD EBS volume type that allows storage, IOPS, and throughput to be configured independently within its supported limits.

---

### 5. What is IOPS?

IOPS means **Input/Output Operations Per Second**.

---

### 6. What is EBS throughput?

Throughput is the amount of data that can be transferred per second, commonly measured in MB/s.

---

### 7. What is an EBS snapshot?

A snapshot is a point-in-time backup of an EBS volume.

---

### 8. Can an EBS volume be attached across Availability Zones?

No. An EBS volume is tied to a specific Availability Zone.

To use the data in another AZ, create a snapshot and restore it there.

---

### 9. Can EBS be encrypted?

Yes. EBS volumes and snapshots can be encrypted using AWS encryption capabilities, including AWS KMS keys.

---

### 10. What is the difference between EBS and S3?

EBS is block storage used like a disk, while S3 is object storage accessed through APIs.

---

### 11. What happens to EBS when EC2 is terminated?

It depends on the volume's **DeleteOnTermination** setting.

---

### 12. Can you increase EBS volume size?

Yes. EBS volumes can generally be increased, and the operating system partition/filesystem may also need to be expanded.

---

# 🚀 EBS + DevOps

EBS is heavily used in DevOps infrastructure.

### Jenkins Example

```text
EC2
 │
 ├── Jenkins
 │
 └── EBS
       │
       ├── Jenkins Home
       ├── Build Data
       └── Plugins
```

### Docker Example

```text
EC2
 │
 Docker
 │
 EBS
 │
 Persistent Docker Data
```

### Database Example

```text
EC2
 │
Database
 │
EBS
 │
Persistent Data
```

---

# ⚡ EBS Quick Cheat Sheet

```text
EBS
│
├── Block Storage
│
├── Persistent
│
├── AZ-specific
│
├── Volume Types
│   ├── gp3
│   ├── gp2
│   ├── io2
│   ├── st1
│   └── sc1
│
├── Snapshots
│
├── Encryption
│
├── IOPS
│
├── Throughput
│
├── Resize
│
└── Attach / Detach
```

### 🔥 Golden Rules

```text
1. EBS = Block Storage.
2. EBS is primarily used with EC2.
3. EBS volumes are AZ-specific.
4. Use gp3 for many general-purpose workloads.
5. Use snapshots for backups.
6. Encrypt sensitive EBS data.
7. Increasing a volume may require filesystem expansion.
8. Check DeleteOnTermination before terminating production instances.
9. Use EFS when multiple instances need shared file storage.
10. Use S3 for object storage rather than EBS.
```

---

# 📚 Important EBS Topics for DevOps Interviews

```text
✅ EBS Basics
✅ EBS Volume Types
✅ gp3 vs gp2
✅ gp3 vs io2
✅ IOPS
✅ Throughput
✅ EBS Snapshots
✅ EBS Encryption
✅ Attach / Detach
✅ Mounting EBS on Linux
✅ /etc/fstab
✅ EBS Resize
✅ EBS vs Instance Store
✅ EBS vs S3
✅ EBS vs EFS
✅ DeleteOnTermination
✅ EBS Backup
✅ Cross-Region Snapshot
✅ EC2 + EBS Troubleshooting
```

