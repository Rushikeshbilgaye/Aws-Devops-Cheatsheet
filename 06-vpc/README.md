# 🌐 06. Amazon VPC

Amazon **VPC (Virtual Private Cloud)** allows you to create a logically isolated network inside AWS where you can launch and manage AWS resources such as EC2 instances, RDS databases, and load balancers.

---

## 📌 What is VPC?

A VPC is your **private network inside AWS**.

You control:

* IP address range
* Subnets
* Route tables
* Internet connectivity
* Security
* Network traffic

### Simple Architecture

```text
                         Internet
                            |
                     Internet Gateway
                            |
                    +----------------+
                    |      VPC       |
                    |  10.0.0.0/16   |
                    +----------------+
                       /          \
                      /            \
             Public Subnet      Private Subnet
              10.0.1.0/24        10.0.2.0/24
                   |                  |
                  EC2               Database
                + Nginx             RDS
```

---

# 🧠 Important VPC Components

## 1. VPC

The main virtual network.

Example:

```text
VPC CIDR = 10.0.0.0/16
```

This provides:

```text
10.0.0.0 - 10.0.255.255
```

---

## 2. CIDR

CIDR defines the IP address range of your network.

Example:

```text
10.0.0.0/16
```

Common examples:

```text
10.0.0.0/16
10.0.1.0/24
192.168.0.0/16
```

### Example

```text
VPC
10.0.0.0/16

        |
        +--- Public Subnet
        |    10.0.1.0/24
        |
        +--- Private Subnet
             10.0.2.0/24
```

---

# 3. Subnet

A subnet is a smaller network inside a VPC.

There are two commonly used types:

### Public Subnet

A subnet whose route table has a route to an **Internet Gateway**.

Example:

```text
10.0.1.0/24
```

Usually contains:

* Web servers
* Load Balancers
* Bastion hosts

### Private Subnet

A subnet without a direct route to the Internet Gateway.

Example:

```text
10.0.2.0/24
```

Usually contains:

* Application servers
* Databases
* Internal services

---

# 4. Internet Gateway

An **Internet Gateway (IGW)** allows resources in a VPC to communicate with the internet.

Architecture:

```text
Internet
   |
   |
Internet Gateway
   |
   |
VPC
   |
Public Subnet
   |
EC2
```

For an EC2 instance to be publicly accessible, you generally need:

```text
Internet Gateway
       +
Route to IGW
       +
Public IP
       +
Security Group allowing traffic
```

---

# 5. Route Table

A route table determines where network traffic goes.

Example public route table:

```text
Destination        Target

10.0.0.0/16        local
0.0.0.0/0          Internet Gateway
```

Meaning:

```text
VPC traffic
    |
    +--> local

Internet traffic
    |
    +--> Internet Gateway
```

---

# 6. NAT Gateway

A **NAT Gateway** allows resources in private subnets to access the internet for outbound connections.

Example:

```text
Private EC2
    |
    |
Private Route Table
    |
    |
NAT Gateway
    |
    |
Internet Gateway
    |
    |
Internet
```

### Important

NAT Gateway generally provides:

```text
Private subnet → Internet
```

It does **not** provide normal unsolicited inbound internet access to private instances.

---

# 7. Elastic IP

An Elastic IP is a static public IPv4 address.

It can be associated with resources such as:

* NAT Gateway
* EC2

Example:

```text
NAT Gateway
     |
Elastic IP
     |
Internet
```

---

# 8. Security Group

A Security Group acts as a **stateful virtual firewall** for resources such as EC2 instances.

Example:

```text
Inbound Rules

SSH     22      My IP
HTTP    80      0.0.0.0/0
HTTPS   443     0.0.0.0/0
```

### Important Characteristics

* Stateful
* Supports allow rules
* No explicit deny rules
* Associated with network interfaces/resources

Example:

```text
Internet
   |
Port 80
   |
Security Group
   |
EC2
```

---

# 9. Network ACL

A **Network ACL (NACL)** controls traffic at the subnet level.

Characteristics:

* Stateless
* Supports allow and deny rules
* Rules are evaluated by rule number
* Applies to subnet traffic

Example:

```text
Internet
   |
   |
NACL
   |
Subnet
   |
EC2
```

---

# 🔥 Security Group vs NACL

| Feature         | Security Group        | NACL                          |
| --------------- | --------------------- | ----------------------------- |
| Level           | Instance/ENI          | Subnet                        |
| Stateful        | Yes                   | No                            |
| Allow rules     | Yes                   | Yes                           |
| Deny rules      | No                    | Yes                           |
| Rule evaluation | All applicable rules  | Lowest-numbered matching rule |
| Return traffic  | Automatically allowed | Must be explicitly allowed    |

### Interview Tip

> **Security Group = Instance-level firewall**

> **NACL = Subnet-level firewall**

---

# 10. VPC Peering

VPC Peering allows two VPCs to communicate privately.

Example:

```text
VPC A
10.0.0.0/16
   |
   |
VPC Peering
   |
   |
VPC B
10.1.0.0/16
```

The VPCs must have **non-overlapping CIDR ranges**.

---

# 11. VPC Endpoints

VPC Endpoints allow private connectivity from a VPC to supported AWS services without requiring traffic to traverse the public internet.

Common example:

```text
Private EC2
    |
    |
VPC Endpoint
    |
    |
Amazon S3
```

Two important endpoint types:

### Gateway Endpoint

Commonly used for:

* S3
* DynamoDB

### Interface Endpoint

Uses Elastic Network Interfaces and private IP addresses.

Used for many AWS services through **AWS PrivateLink**.

---

# 🏗️ Typical 3-Tier VPC Architecture

A common production architecture looks like this:

```text
                       Internet
                           |
                    Internet Gateway
                           |
                  +------------------+
                  |      VPC         |
                  |   10.0.0.0/16    |
                  +------------------+
                     /            \
                    /              \
             Public Subnets     Private Subnets
                  |                  |
             Load Balancer      Application
                  |              Servers
                  |                  |
                  +----------------+
                           |
                        Database
                           |
                          RDS
```

Usually:

```text
Internet
   ↓
Load Balancer
   ↓
Application Servers
   ↓
Database
```

---

# 🔄 Public vs Private Subnet

## Public Subnet

```text
Route Table
     |
0.0.0.0/0
     |
Internet Gateway
```

Used for resources that need direct internet connectivity.

---

## Private Subnet

```text
Route Table
     |
0.0.0.0/0
     |
NAT Gateway
     |
Internet Gateway
```

Used for resources that should not accept direct inbound internet traffic.

---

# 🛠️ Useful AWS CLI Commands

### List VPCs

```bash
aws ec2 describe-vpcs
```

### List Subnets

```bash
aws ec2 describe-subnets
```

### List Route Tables

```bash
aws ec2 describe-route-tables
```

### List Security Groups

```bash
aws ec2 describe-security-groups
```

### List Internet Gateways

```bash
aws ec2 describe-internet-gateways
```

### List NAT Gateways

```bash
aws ec2 describe-nat-gateways
```

---

# 🏗️ Basic VPC Creation Using AWS CLI

### Create VPC

```bash
aws ec2 create-vpc \
  --cidr-block 10.0.0.0/16
```

### Create Subnet

```bash
aws ec2 create-subnet \
  --vpc-id <VPC-ID> \
  --cidr-block 10.0.1.0/24
```

### Create Internet Gateway

```bash
aws ec2 create-internet-gateway
```

### Attach Internet Gateway

```bash
aws ec2 attach-internet-gateway \
  --vpc-id <VPC-ID> \
  --internet-gateway-id <IGW-ID>
```

---

# 🌱 Terraform Example

A simple VPC can be created using Terraform:

```hcl
provider "aws" {
  region = "ap-south-1"
}

resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"

  tags = {
    Name = "devops-vpc"
  }
}

resource "aws_subnet" "public" {
  vpc_id     = aws_vpc.main.id
  cidr_block = "10.0.1.0/24"

  tags = {
    Name = "public-subnet"
  }
}
```

Run:

```bash
terraform init
terraform plan
terraform apply
```

---

# 🧪 VPC Troubleshooting Checklist

If an EC2 instance cannot access the internet, check:

```text
1. Does the subnet have a route to an Internet Gateway?
2. Is the Internet Gateway attached to the VPC?
3. Does the instance have a public IPv4 address?
4. Does the Security Group allow the required traffic?
5. Does the NACL allow inbound and outbound traffic?
6. Is the route table associated with the correct subnet?
7. If private subnet → Is NAT Gateway configured?
8. Are the subnet and route table in the expected Availability Zone?
```

---

# 🎯 Interview Questions

### Q1. What is a VPC?

A VPC is a logically isolated virtual network in AWS where you can launch AWS resources.

### Q2. What is a subnet?

A subnet is a smaller IP network inside a VPC.

### Q3. Difference between public and private subnet?

A public subnet has a route to an Internet Gateway, while a private subnet does not have a direct route to the Internet Gateway.

### Q4. What is an Internet Gateway?

It provides internet connectivity between a VPC and the internet.

### Q5. What is a NAT Gateway?

It allows resources in private subnets to make outbound connections to the internet.

### Q6. Security Group vs NACL?

Security Groups are stateful and operate at the resource/network-interface level. NACLs are stateless and operate at the subnet level.

### Q7. What is a route table?

A route table contains rules that determine where network traffic should be sent.

### Q8. Can two VPCs have the same CIDR?

They can exist independently, but overlapping CIDRs cause problems when you need private routing between them, such as with VPC peering.

### Q9. Why use private subnets?

To keep application/database resources away from direct inbound internet access.

### Q10. What is a VPC Endpoint?

It provides private connectivity from a VPC to supported AWS services without requiring traffic to use the public internet.

---

# 🧠 Quick Revision

```text
VPC
│
├── CIDR
│
├── Subnets
│   ├── Public
│   └── Private
│
├── Route Tables
│
├── Internet Gateway
│
├── NAT Gateway
│
├── Security Groups
│
├── Network ACL
│
├── VPC Peering
│
└── VPC Endpoints
```

### ⭐ Remember This

```text
VPC
 ↓
Subnet
 ↓
Route Table
 ↓
IGW / NAT Gateway
 ↓
Internet
```

**DevOps interview focus:**

```text
VPC
CIDR
Subnet
Route Table
Internet Gateway
NAT Gateway
Security Group
NACL
VPC Peering
VPC Endpoint
Public vs Private Subnet
3-Tier Architecture
```
