# 🖥️ AWS EC2 — Elastic Compute Cloud

Amazon EC2 (Elastic Compute Cloud) provides **resizable virtual servers in the AWS Cloud**.

EC2 is one of the most important AWS services for DevOps engineers because it is commonly used for:

* Application servers
* Web servers
* Jenkins
* Docker
* Kubernetes nodes
* Monitoring servers
* CI/CD infrastructure
* Backend applications

---

# 📌 What is EC2?

**EC2 = Elastic Compute Cloud**

It allows you to create virtual machines called **instances**.

Basic architecture:

```text
                    AWS Cloud
                        │
                       EC2
                        │
              ┌─────────┴─────────┐
              │                   │
          Web Server          Application
              │                   │
              └─────────┬─────────┘
                        │
                       EBS
```

---

# 🏗️ EC2 Components

When launching an EC2 instance, you typically choose:

```text
EC2 Instance
│
├── AMI
├── Instance Type
├── Key Pair
├── VPC
├── Subnet
├── Security Group
├── Storage
├── IAM Role
└── User Data
```

---

# 1️⃣ AMI

AMI = **Amazon Machine Image**

An AMI is a template used to launch an EC2 instance.

It contains things such as:

* Operating system
* Software configuration
* Application configuration
* Root volume information

Examples:

```text
Ubuntu
Amazon Linux
Red Hat
Windows Server
```

Architecture:

```text
AMI
 │
 ├── OS
 ├── Packages
 └── Configuration
      │
      ↓
   EC2 Instance
```

### Interview Question

**What is an AMI?**

> An AMI is a template containing the software configuration required to launch an EC2 instance.

---

# 2️⃣ Instance Types

Instance type determines the compute resources available to an EC2 instance.

It controls:

```text
CPU
Memory
Network
Storage capabilities
```

Example:

```text
t3.micro
t3.small
t3.medium
m5.large
c5.large
r5.large
```

---

# 🧠 Instance Type Categories

## General Purpose

Examples:

```text
t3
t4g
m5
m6i
```

Used for:

* Web servers
* Development environments
* Small applications
* General workloads

---

## Compute Optimized

Examples:

```text
c5
c6i
```

Used for:

* CPU-intensive workloads
* Batch processing
* High-performance applications

---

## Memory Optimized

Examples:

```text
r5
r6i
x2
```

Used for:

* Large databases
* In-memory applications
* Caching

---

## Storage Optimized

Examples:

```text
i3
i4i
d3
```

Used for:

* High-speed local storage
* Data processing
* Large datasets

---

# 3️⃣ Key Pair

A key pair is used to securely connect to an EC2 instance.

For Linux:

```text
Private Key
    ↓
.pem
    ↓
SSH
    ↓
EC2
```

Example:

```bash
ssh -i my-key.pem ubuntu@PUBLIC-IP
```

### Important

Never share your private key.

Set proper permissions:

```bash
chmod 400 my-key.pem
```

---

# 4️⃣ Security Groups

A Security Group acts as a **virtual firewall for EC2 instances**.

It controls:

```text
Inbound Traffic
Outbound Traffic
```

Example:

```text
Internet
   │
   ├── 80  → HTTP
   ├── 443 → HTTPS
   └── 22  → SSH
            │
            ↓
           EC2
```

---

# 🔐 Common Security Group Ports

| Port | Protocol | Purpose        |
| ---: | -------- | -------------- |
|   22 | TCP      | SSH            |
|   80 | TCP      | HTTP           |
|  443 | TCP      | HTTPS          |
| 8080 | TCP      | Jenkins        |
| 3000 | TCP      | Node.js        |
| 3306 | TCP      | MySQL          |
| 5432 | TCP      | PostgreSQL     |
| 6443 | TCP      | Kubernetes API |

### Security Best Practice

Avoid:

```text
0.0.0.0/0 → SSH port 22
```

when possible.

Prefer:

```text
Your IP → Port 22
```

---

# 5️⃣ EBS

EBS = **Elastic Block Store**

EBS provides persistent block storage for EC2.

```text
EC2
 │
 └── EBS Volume
       │
       └── Data
```

Common EBS types:

```text
gp3
gp2
io1
io2
st1
sc1
```

For most general workloads, **gp3** is commonly used.

---

# 6️⃣ Public IP vs Private IP

## Private IP

Used for communication inside the VPC.

Example:

```text
10.0.1.10
```

## Public IP

Used for communication over the internet.

Example:

```text
13.x.x.x
```

Architecture:

```text
Internet
   │
Public IP
   │
  EC2
   │
Private IP
   │
VPC Resources
```

---

# 7️⃣ Elastic IP

An Elastic IP is a **static public IPv4 address** that can be associated with an EC2 instance or network interface.

Without a persistent public IP:

```text
Stop EC2
   ↓
Start EC2
   ↓
Public IP may change
```

With Elastic IP:

```text
Stop EC2
   ↓
Start EC2
   ↓
Elastic IP remains associated
```

### Best Practice

Don't allocate Elastic IPs unnecessarily. AWS may charge for public IPv4 addresses, including unused/idle ones.

---

# 8️⃣ User Data

User Data allows you to execute commands automatically when an EC2 instance starts.

Example:

```bash
#!/bin/bash

apt update -y
apt install nginx -y
systemctl enable nginx
systemctl start nginx
```

Architecture:

```text
Launch EC2
    ↓
User Data
    ↓
Install Software
    ↓
Configure Server
    ↓
Application Ready
```

---

# 9️⃣ IAM Role with EC2

Instead of storing AWS access keys on an EC2 instance, attach an IAM role.

```text
EC2
 │
 ↓
IAM Role
 │
 ↓
AWS Services
```

Example:

```text
EC2
 ↓
IAM Role
 ↓
S3
```

Then the application can access S3 according to the role's permissions.

### Interview Question

**Why should you use IAM roles instead of access keys on EC2?**

> IAM roles provide temporary credentials and avoid storing long-term AWS credentials on the server.

---

# 🔟 EC2 Lifecycle

An EC2 instance can have different states:

```text
Pending
   ↓
Running
   ↓
Stopping
   ↓
Stopped
   ↓
Starting
   ↓
Running
   ↓
Terminating
   ↓
Terminated
```

---

# 🟢 Stop vs Terminate

## Stop

Stops the instance.

```text
EC2
 ↓
Stopped
```

The instance can generally be started again.

EBS volumes can persist depending on their delete-on-termination configuration.

---

## Terminate

Deletes the instance.

```text
EC2
 ↓
Terminated
```

The instance cannot be restarted.

The root EBS volume is commonly configured to be deleted on termination, but this depends on its configuration.

---

# 💰 EC2 Pricing Models

Common EC2 purchasing options include:

### On-Demand

Pay for compute capacity without a long-term commitment.

Good for:

* Testing
* Short-term workloads
* Unpredictable workloads

---

### Reserved Instances

Commit to a term in exchange for discounted pricing compared with equivalent On-Demand usage.

Good for:

* Predictable workloads
* Long-running applications

---

### Savings Plans

Commit to a consistent amount of usage/spend for a term in exchange for discounted rates.

---

### Spot Instances

Use spare AWS capacity at potentially large discounts.

However:

> Spot Instances can be interrupted when AWS needs the capacity back.

Good for:

* Batch processing
* Fault-tolerant workloads
* Data processing
* Flexible workloads

---

# 🌐 EC2 Networking

EC2 instances run inside a VPC.

Basic architecture:

```text
AWS
│
└── VPC
    │
    ├── Public Subnet
    │    │
    │    └── EC2
    │
    └── Private Subnet
         │
         └── EC2
```

---

# 🌍 Public EC2 Architecture

```text
Internet
   │
Internet Gateway
   │
Public Subnet
   │
Security Group
   │
EC2
```

A public EC2 instance generally needs:

```text
Public IPv4 / public addressing
+
Route to Internet Gateway
+
Security Group allowing required traffic
```

---

# 🔒 Private EC2 Architecture

```text
Internet
   │
   ↓
Load Balancer
   │
   ↓
Private EC2
   │
   ↓
Database
```

Private EC2 instances don't need public IP addresses to serve application traffic when accessed through a load balancer.

---

# 🚀 EC2 Launch Example

### Step 1 — Choose AMI

Example:

```text
Ubuntu Server
```

### Step 2 — Choose Instance Type

Example:

```text
t3.micro
```

### Step 3 — Select Key Pair

```text
devops-key
```

### Step 4 — Configure Network

```text
VPC
Subnet
Public IP
```

### Step 5 — Configure Security Group

Example:

```text
SSH   → 22
HTTP  → 80
HTTPS → 443
```

### Step 6 — Configure Storage

Example:

```text
20 GB gp3
```

### Step 7 — Add User Data

```bash
#!/bin/bash

apt update -y
apt install nginx -y
systemctl enable nginx
systemctl start nginx
```

### Step 8 — Launch

```text
Launch Instance
      ↓
EC2 Running
      ↓
Nginx Installed
      ↓
Web Server Ready
```

---

# 🧑‍💻 Important AWS CLI Commands

## List EC2 Instances

```bash
aws ec2 describe-instances
```

---

## Start Instance

```bash
aws ec2 start-instances \
  --instance-ids i-1234567890abcdef0
```

---

## Stop Instance

```bash
aws ec2 stop-instances \
  --instance-ids i-1234567890abcdef0
```

---

## Reboot Instance

```bash
aws ec2 reboot-instances \
  --instance-ids i-1234567890abcdef0
```

---

## Terminate Instance

```bash
aws ec2 terminate-instances \
  --instance-ids i-1234567890abcdef0
```

---

## Describe Security Groups

```bash
aws ec2 describe-security-groups
```

---

## Describe Volumes

```bash
aws ec2 describe-volumes
```

---

# 🔧 SSH into Ubuntu EC2

First, make the key private:

```bash
chmod 400 my-key.pem
```

Then:

```bash
ssh -i my-key.pem ubuntu@PUBLIC-IP
```

For Amazon Linux:

```bash
ssh -i my-key.pem ec2-user@PUBLIC-IP
```

---

# 🛠️ Troubleshooting EC2 SSH

If SSH doesn't work, check:

```text
1. EC2 instance is running
2. Correct public IP
3. Correct username
4. Correct private key
5. Security Group allows TCP 22
6. Network ACL isn't blocking traffic
7. Route table has proper connectivity
8. Instance has internet connectivity
```

Example:

```text
SSH
 │
 ↓
Port 22
 │
 ↓
Security Group
 │
 ↓
EC2
```

---

# 🌐 Troubleshooting HTTP

If Nginx isn't accessible:

```text
Browser
   ↓
Public IP
   ↓
Security Group
   ↓
Port 80
   ↓
Nginx
```

Check:

```bash
sudo systemctl status nginx
```

Check port:

```bash
sudo ss -tulpn | grep :80
```

Test locally:

```bash
curl localhost
```

---

# 🧪 EC2 Practical Tasks

## 🟢 Beginner Tasks

### Task 1 — Launch EC2

Create an Ubuntu EC2 instance.

Configure:

```text
AMI       → Ubuntu
Instance  → Appropriate general-purpose type
Storage   → 20 GB gp3
SSH       → Port 22
HTTP      → Port 80
```

Connect using SSH.

---

### Task 2 — Install Nginx

Run:

```bash
sudo apt update
sudo apt install nginx -y
```

Start:

```bash
sudo systemctl start nginx
```

Enable at boot:

```bash
sudo systemctl enable nginx
```

Check:

```bash
sudo systemctl status nginx
```

---

### Task 3 — Host a Web Page

Create:

```bash
sudo nano /var/www/html/index.html
```

Example:

```html
<h1>Hello from AWS EC2</h1>
<p>My first AWS web server.</p>
```

Then open:

```text
http://PUBLIC-IP
```

---

# 🟡 Intermediate Tasks

## Task 4 — EC2 + IAM Role + S3

Create:

```text
EC2
 ↓
IAM Role
 ↓
S3 Read Access
```

From EC2:

```bash
aws s3 ls
```

Verify that the instance can access S3 without manually configuring access keys.

---

## Task 5 — EC2 + Custom User Data

Create an EC2 instance that automatically installs:

```text
Nginx
Docker
Git
```

using User Data.

---

## Task 6 — Private EC2

Create:

```text
Public Subnet
    │
    └── Load Balancer
            │
            ↓
      Private Subnet
            │
            └── EC2
```

The application server should not have a public IP.

---

# 🔴 DevOps Scenario

### Scenario

You launched an Ubuntu EC2 instance and installed Nginx.

The service shows:

```text
active (running)
```

But when you open:

```text
http://PUBLIC-IP
```

the website doesn't load.

### Troubleshooting

Check the following:

### 1. Nginx

```bash
sudo systemctl status nginx
```

### 2. Local Test

```bash
curl localhost
```

If this works, Nginx is running.

### 3. Security Group

Verify:

```text
Inbound
TCP 80
Source: appropriate client/network
```

### 4. Network

Check:

```text
EC2
 ↓
Subnet
 ↓
Route Table
 ↓
Internet Gateway
```

### 5. Public Address

Verify the instance has a reachable public IPv4 address or another appropriate public path.

---

# 🎯 EC2 Interview Questions

### 1. What is EC2?

EC2 is AWS's service for providing resizable virtual compute capacity.

---

### 2. What is an AMI?

An AMI is a template used to launch EC2 instances.

---

### 3. What is a Security Group?

A Security Group is a virtual firewall associated with resources such as EC2 network interfaces that controls inbound and outbound traffic.

---

### 4. What is the difference between Security Group and NACL?

| Security Group                       | NACL                                      |
| ------------------------------------ | ----------------------------------------- |
| Instance/network-interface level     | Subnet level                              |
| Stateful                             | Stateless                                 |
| Supports allow rules                 | Supports allow and deny rules             |
| Return traffic automatically allowed | Return traffic must be explicitly allowed |

---

### 5. What is User Data?

User Data allows scripts to run automatically during the initial boot process of an EC2 instance.

---

### 6. What is an IAM role used for with EC2?

To provide AWS permissions to applications running on EC2 without embedding long-term credentials.

---

### 7. What is the difference between Stop and Terminate?

**Stop** shuts down the instance while preserving it for later restart.

**Terminate** permanently removes the EC2 instance.

---

### 8. What happens to an EC2 public IP after stopping and starting?

A public IPv4 address assigned automatically can change after stop/start. An Elastic IP provides a persistent public IPv4 address while associated.

---

### 9. What is a Spot Instance?

A Spot Instance uses spare EC2 capacity at a discounted price but can be interrupted.

---

### 10. How do you troubleshoot an EC2 server that is unreachable?

Check:

```text
Instance state
Security Group
Network ACL
Route table
Internet Gateway
Public IP
SSH key
Username
OS firewall
Service status
```

---

# 🚀 EC2 + DevOps

EC2 is heavily used in DevOps environments.

Typical architecture:

```text
                   GitHub
                      │
                      ↓
                   Jenkins
                      │
                      ↓
                    Docker
                      │
                      ↓
                AWS EC2 Server
                      │
                      ↓
                   Nginx
                      │
                      ↓
                 Application
```

A common real-world project:

```text
GitHub
   ↓
Jenkins
   ↓
Docker Build
   ↓
Docker Image
   ↓
Docker Hub
   ↓
AWS EC2
   ↓
Docker Container
   ↓
Application
```

---

# ⚡ EC2 Quick Cheat Sheet

```text
EC2
│
├── AMI
│
├── Instance Type
│
├── Key Pair
│
├── Security Group
│
├── EBS
│
├── VPC
│
├── Subnet
│
├── Public/Private IP
│
├── Elastic IP
│
├── IAM Role
│
├── User Data
│
└── Instance Lifecycle
```

### 🔥 Golden Rules

```text
1. Use Security Groups carefully.
2. Never expose unnecessary ports.
3. Avoid 0.0.0.0/0 for SSH when possible.
4. Use IAM roles instead of hard-coded AWS credentials.
5. Use User Data for initial configuration.
6. Use private subnets for backend servers when appropriate.
7. Use Load Balancers for scalable web applications.
8. Use Auto Scaling for automatically adjusting capacity.
9. Monitor EC2 with CloudWatch.
10. Use EBS for persistent block storage.
```

---

# 📚 Important EC2 Topics for DevOps Interviews

```text
✅ EC2 Basics
✅ AMI
✅ Instance Types
✅ Key Pairs
✅ Security Groups
✅ EBS
✅ Public vs Private IP
✅ Elastic IP
✅ User Data
✅ IAM Roles
✅ EC2 Lifecycle
✅ Stop vs Terminate
✅ On-Demand
✅ Reserved Instances
✅ Savings Plans
✅ Spot Instances
✅ EC2 Networking
✅ SSH Troubleshooting
✅ EC2 + Nginx
✅ EC2 + Docker
✅ EC2 + Jenkins
✅ EC2 + IAM
✅ EC2 + CloudWatch
```
