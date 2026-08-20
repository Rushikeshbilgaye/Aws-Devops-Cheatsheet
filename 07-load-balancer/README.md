# ⚖️ 07. AWS Load Balancer

An **AWS Elastic Load Balancer (ELB)** distributes incoming application traffic across multiple targets such as EC2 instances, containers, and IP addresses.

The main purpose of a load balancer is to provide:

* High availability
* Fault tolerance
* Scalability
* Better application performance
* Health checking
* Traffic distribution

---

# 📌 What is a Load Balancer?

Without a load balancer:

```text
              Users
                |
                |
             EC2-1
                |
           Application
```

If EC2-1 goes down:

```text
              Users
                |
                X
             EC2-1
              DOWN
```

With a load balancer:

```text
                  Users
                    |
                    |
             Load Balancer
              /    |    \
             /     |     \
          EC2-1  EC2-2  EC2-3
           |       |       |
         App     App     App
```

If one instance fails:

```text
                  Users
                    |
                    |
             Load Balancer
              /         \
             /           \
          EC2-1        EC2-2
          DOWN          |
                       App
```

The load balancer detects the unhealthy instance and stops sending traffic to it.

---

# 🧠 AWS Elastic Load Balancing

AWS provides several load balancer types.

```text
Elastic Load Balancing
        |
        +-----------------------+
        |                       |
   Application LB          Network LB
        |                       |
      Layer 7               Layer 4
```

Other specialized types include:

* Gateway Load Balancer
* Classic Load Balancer

---

# 🔥 Types of AWS Load Balancers

## 1. Application Load Balancer (ALB)

ALB operates at:

```text
Layer 7
Application Layer
```

It understands HTTP and HTTPS traffic.

Commonly used for:

* Web applications
* Microservices
* REST APIs
* Containerized applications

Example:

```text
             Internet
                |
                |
               ALB
          /      |      \
         /       |       \
      EC2-1    EC2-2    EC2-3
```

---

# 2. Network Load Balancer (NLB)

NLB operates primarily at:

```text
Layer 4
Transport Layer
```

It is designed for very high-performance TCP/UDP/TLS traffic.

Common use cases:

* TCP applications
* UDP applications
* High-throughput workloads
* Low-latency applications
* Static IP requirements

Architecture:

```text
              Users
                |
                |
               NLB
             /     \
            /       \
         EC2-1     EC2-2
```

---

# 3. Gateway Load Balancer (GWLB)

Gateway Load Balancer is designed for deploying and scaling network virtual appliances.

Examples:

* Firewalls
* Intrusion detection systems
* Intrusion prevention systems
* Security appliances

Architecture:

```text
             Traffic
                |
                |
               GWLB
             /      \
        Security   Security
        Appliance  Appliance
```

---

# 4. Classic Load Balancer

Classic Load Balancer is an older AWS load-balancing option.

For modern applications, AWS generally recommends using:

```text
ALB
NLB
GWLB
```

instead of starting new deployments with Classic Load Balancer.

---

# ⭐ ALB vs NLB vs GWLB

| Feature               | ALB                              | NLB       | GWLB      |
| --------------------- | -------------------------------- | --------- | --------- |
| Layer                 | 7                                | 4         | 3/4       |
| HTTP/HTTPS            | ✅                                | ✅         | ❌         |
| TCP                   | Limited/use appropriate listener | ✅         | ✅         |
| UDP                   | ❌                                | ✅         | ❌         |
| Path-based routing    | ✅                                | ❌         | ❌         |
| Host-based routing    | ✅                                | ❌         | ❌         |
| Web applications      | ✅                                | Possible  | ❌         |
| Network appliances    | ❌                                | ❌         | ✅         |
| Very high performance | Good                             | Excellent | Excellent |

---

# 🏗️ Application Load Balancer Architecture

A typical architecture:

```text
                         Internet
                            |
                            |
                       ALB : 80/443
                            |
              +-------------+-------------+
              |             |             |
              ↓             ↓             ↓
           EC2-1          EC2-2         EC2-3
           AZ-1           AZ-2          AZ-3
              |             |             |
              +-------------+-------------+
                            |
                         Database
```

The ALB can distribute requests across multiple Availability Zones.

---

# 🎯 Target Groups

A **Target Group** contains the targets that receive traffic from the load balancer.

Targets can include:

* EC2 instances
* IP addresses
* Containers
* Lambda functions for ALB

Example:

```text
                 ALB
                  |
             Target Group
            /      |      \
           /       |       \
        EC2-1    EC2-2    EC2-3
```

---

# ❤️ Health Checks

The load balancer continuously checks whether targets are healthy.

Example:

```text
ALB
 |
 +---- EC2-1 → Healthy
 |
 +---- EC2-2 → Healthy
 |
 +---- EC2-3 → Unhealthy
```

Traffic is sent to:

```text
EC2-1
EC2-2
```

Traffic is stopped for:

```text
EC2-3
```

Example health check:

```text
Protocol: HTTP
Port: 80
Path: /
```

The load balancer requests:

```text
http://EC2-IP/
```

If the target responds successfully according to the configured health-check settings, it is considered healthy.

---

# 🔀 ALB Listener

A listener checks for incoming connection requests.

Example:

```text
ALB
 |
 +--- Listener : 80
 |
 +--- Listener : 443
```

Example:

```text
HTTP : 80
     |
     ↓
Target Group
```

HTTPS:

```text
HTTPS : 443
      |
      ↓
Target Group
```

---

# 🚦 ALB Listener Rules

ALB supports advanced routing rules.

## Path-Based Routing

Example:

```text
example.com/
      |
      ↓
Frontend Target Group

example.com/api
      |
      ↓
Backend Target Group

example.com/images
      |
      ↓
Image Target Group
```

---

## Host-Based Routing

Example:

```text
app.example.com
        |
        ↓
Frontend Target Group

api.example.com
        |
        ↓
Backend Target Group
```

---

# 🔄 Load Balancing Algorithms

The load balancer decides how traffic is distributed among targets.

Common concepts include:

### Round Robin

Requests are distributed sequentially.

```text
Request 1 → EC2-1
Request 2 → EC2-2
Request 3 → EC2-3
Request 4 → EC2-1
```

### Least Outstanding Requests

Traffic can be directed based on the number of outstanding requests for applicable ALB routing configurations.

---

# 🔐 HTTPS and SSL/TLS

You can configure HTTPS on an ALB.

Typical architecture:

```text
User
 |
 | HTTPS : 443
 ↓
ALB
 |
 | HTTP : 80
 ↓
EC2
```

The ALB can terminate TLS.

AWS Certificate Manager (**ACM**) can be used to manage certificates.

---

# 🛡️ Load Balancer Security Group

For an internet-facing ALB, you can configure:

```text
Inbound

HTTP   80    0.0.0.0/0
HTTPS  443   0.0.0.0/0
```

The EC2 instances can then allow traffic from the **load balancer's security group** rather than allowing the entire internet.

Example:

```text
Internet
   |
   | 80/443
   ↓
ALB Security Group
   |
   | Application traffic
   ↓
EC2 Security Group
```

### Best Practice

Do not unnecessarily configure:

```text
EC2
Port 80
Source: 0.0.0.0/0
```

Instead, allow the application instances to receive traffic from the load balancer security group.

---

# 🌍 Internet-Facing vs Internal Load Balancer

## Internet-Facing Load Balancer

Used when clients access the application from the internet.

```text
Internet
   |
   ↓
Internet-Facing ALB
   |
   ↓
EC2
```

---

## Internal Load Balancer

Used for private applications inside a VPC.

```text
Frontend
   |
   ↓
Internal ALB
   |
   ↓
Backend
```

Example:

```text
Public ALB
     |
     ↓
Frontend
     |
     ↓
Internal ALB
     |
     ↓
Backend
```

---

# 🏢 Multi-AZ Architecture

For high availability, place targets across multiple Availability Zones.

```text
                    ALB
                     |
          +----------+----------+
          |                     |
        AZ-1                  AZ-2
          |                     |
       EC2-1                  EC2-2
          |                     |
       Healthy                Healthy
```

If AZ-1 has a problem:

```text
                    ALB
                     |
                     |
                   AZ-2
                     |
                   EC2-2
```

The application can continue serving traffic from healthy targets.

---

# ⚙️ AWS CLI Examples

## List Load Balancers

```bash
aws elbv2 describe-load-balancers
```

---

## List Target Groups

```bash
aws elbv2 describe-target-groups
```

---

## List Target Health

```bash
aws elbv2 describe-target-health \
  --target-group-arn <TARGET-GROUP-ARN>
```

---

## List Listeners

```bash
aws elbv2 describe-listeners \
  --load-balancer-arn <LOAD-BALANCER-ARN>
```

---

# 🌱 Terraform Example

Basic Application Load Balancer:

```hcl
resource "aws_lb" "app" {
  name               = "devops-alb"
  internal           = false
  load_balancer_type = "application"

  security_groups = [aws_security_group.alb.id]

  subnets = [
    aws_subnet.public_1.id,
    aws_subnet.public_2.id
  ]

  tags = {
    Name = "devops-alb"
  }
}
```

---

## Target Group

```hcl
resource "aws_lb_target_group" "app" {
  name     = "devops-target-group"
  port     = 80
  protocol = "HTTP"
  vpc_id   = aws_vpc.main.id

  health_check {
    path = "/"
  }
}
```

---

## Register EC2 Instance

```hcl
resource "aws_lb_target_group_attachment" "app" {
  target_group_arn = aws_lb_target_group.app.arn
  target_id        = aws_instance.web.id
  port             = 80
}
```

---

## ALB Listener

```hcl
resource "aws_lb_listener" "http" {
  load_balancer_arn = aws_lb.app.arn
  port              = 80
  protocol          = "HTTP"

  default_action {
    type             = "forward"

    target_group_arn = aws_lb_target_group.app.arn
  }
}
```

---

# 🧪 Troubleshooting Load Balancer

If the ALB returns:

```text
502 Bad Gateway
```

Check:

```text
1. Target health
2. Application running on target port
3. Security Group
4. Target Group port
5. Listener configuration
6. Application response
```

---

## Targets Showing "Unhealthy"

Check:

```bash
aws elbv2 describe-target-health \
  --target-group-arn <TARGET-GROUP-ARN>
```

Then verify the application directly:

```bash
curl http://<EC2-PRIVATE-IP>/
```

Also check:

```bash
sudo systemctl status nginx
```

or:

```bash
sudo systemctl status apache2
```

---

# 🔥 Common Load Balancer Problems

### Problem 1: ALB DNS works but page doesn't load

Check:

```text
Internet Gateway
Security Group
Listener
Target Group
Target Health
EC2 application
```

---

### Problem 2: Target is unhealthy

Check:

```text
Health check path
Health check port
Application port
EC2 Security Group
Application status
```

---

### Problem 3: 502 error

Usually investigate:

```text
ALB → Target Group → Application
```

Make sure the backend application is listening on the expected port.

---

### Problem 4: Connection timeout

Check:

```text
Route table
Security Group
NACL
Internet Gateway
Public accessibility
```

---

# 🎯 Interview Questions

### Q1. What is a Load Balancer?

A load balancer distributes incoming traffic across multiple healthy targets.

### Q2. What is an ALB?

An Application Load Balancer operates at Layer 7 and is designed for HTTP/HTTPS applications.

### Q3. What is an NLB?

A Network Load Balancer is primarily a Layer 4 load balancer designed for high-performance TCP/UDP/TLS workloads.

### Q4. What is a Target Group?

A target group contains the backend targets that receive traffic from a load balancer.

### Q5. What is a Listener?

A listener checks for connection requests on a configured protocol and port and forwards traffic according to its rules.

### Q6. What is a Health Check?

A health check determines whether a target is healthy enough to receive traffic.

### Q7. What is path-based routing?

Routing traffic to different target groups based on the URL path.

Example:

```text
/app  → App Target Group
/api  → API Target Group
```

### Q8. What is host-based routing?

Routing traffic based on the hostname.

```text
app.example.com → App
api.example.com → API
```

### Q9. Why use multiple Availability Zones?

To improve availability and fault tolerance.

### Q10. Can a load balancer send traffic to an unhealthy instance?

Normally, no. Once a target fails its configured health checks, the load balancer stops routing new traffic to that target.

---

# 🧠 Quick Revision

```text
Load Balancer
│
├── ALB
│   └── Layer 7
│
├── NLB
│   └── Layer 4
│
├── GWLB
│   └── Network Appliances
│
├── Listener
│
├── Target Group
│
├── Health Check
│
├── Routing Rules
│   ├── Path-Based
│   └── Host-Based
│
└── Multi-AZ
```

## ⭐ Interview Shortcut

Remember:

```text
ALB → HTTP/HTTPS → Layer 7

NLB → TCP/UDP/TLS → Layer 4

GWLB → Security Appliances

Target Group → Backend Targets

Listener → Accepts Traffic

Health Check → Checks Backend

Multi-AZ → High Availability
```

### 🔥 Real-World DevOps Flow

```text
GitHub
   ↓
Jenkins
   ↓
Docker Build
   ↓
Docker Image
   ↓
EC2 / ECS / EKS
   ↓
Target Group
   ↓
Application Load Balancer
   ↓
Route 53
   ↓
Users
```

