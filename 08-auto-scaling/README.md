# 📈 08. AWS Auto Scaling

AWS **Auto Scaling** automatically adjusts the number of compute resources running your application based on demand.

It helps applications maintain:

* High availability
* Scalability
* Fault tolerance
* Performance
* Cost efficiency

---

# 📌 What is Auto Scaling?

Imagine your application normally needs 2 EC2 instances:

```text
                    Users
                      |
                      ↓
                Load Balancer
                  /       \
                 ↓         ↓
              EC2-1      EC2-2
```

During heavy traffic, 2 instances may not be enough.

Auto Scaling can automatically launch more:

```text
                    Users
                      |
                      ↓
                Load Balancer
              /      |      |      \
             ↓       ↓      ↓       ↓
          EC2-1   EC2-2   EC2-3   EC2-4
```

When traffic decreases:

```text
                    Users
                      |
                      ↓
                Load Balancer
                  /       \
                 ↓         ↓
              EC2-1      EC2-2
```

Unused instances can be terminated.

---

# 🧠 What is an Auto Scaling Group?

An **Auto Scaling Group (ASG)** is a collection of EC2 instances managed automatically by AWS.

Example:

```text
Auto Scaling Group

Minimum = 2
Desired = 2
Maximum = 5

       +-------------------+
       |       ASG         |
       |                   |
       | EC2-1             |
       | EC2-2             |
       +-------------------+
```

If demand increases:

```text
2 EC2
 ↓
3 EC2
 ↓
4 EC2
 ↓
5 EC2
```

If demand decreases:

```text
5 EC2
 ↓
4 EC2
 ↓
3 EC2
 ↓
2 EC2
```

---

# ⭐ Important ASG Settings

Three important values:

```text
Minimum Capacity
Desired Capacity
Maximum Capacity
```

Example:

```text
Minimum = 2
Desired = 2
Maximum = 5
```

This means:

* ASG should normally maintain at least 2 instances.
* Desired capacity is initially 2.
* ASG can scale up to 5 instances.

---

# 🏗️ Auto Scaling Architecture

A common architecture:

```text
                         Users
                           |
                           ↓
                    Application LB
                           |
              +------------+------------+
              |            |            |
              ↓            ↓            ↓
            EC2-1        EC2-2        EC2-3
              \            |            /
               \           |           /
                +----------+----------+
                           |
                     Auto Scaling Group
```

The ASG manages the EC2 instances.

---

# 🔥 Auto Scaling + Load Balancer

Auto Scaling is commonly combined with an Application Load Balancer.

```text
                    Internet
                       |
                       ↓
                    ALB
                       |
              Target Group
                       |
        +--------------+--------------+
        |              |              |
       EC2            EC2            EC2
        |              |              |
        +--------------+--------------+
                       |
                      ASG
```

### Why combine them?

The Load Balancer:

```text
Distributes traffic
```

The Auto Scaling Group:

```text
Adds/removes instances
```

Together:

```text
ALB → Traffic Distribution
ASG → Capacity Management
```

---

# 🚀 Launch Template

A **Launch Template** defines how new EC2 instances should be created.

It can contain:

* AMI ID
* Instance type
* Key pair
* Security groups
* Storage
* IAM role
* User data
* Network configuration

Example:

```text
Launch Template
       |
       +-- AMI
       +-- Instance Type
       +-- Security Group
       +-- Key Pair
       +-- User Data
       |
       ↓
Auto Scaling Group
       |
       ↓
EC2 Instances
```

---

# 🆚 Launch Template vs Launch Configuration

| Feature           | Launch Template | Launch Configuration |
| ----------------- | --------------- | -------------------- |
| Modern            | ✅               | Older                |
| Versioning        | ✅               | ❌                    |
| Recommended       | ✅               | ❌                    |
| Advanced features | More            | Limited              |

### Interview Tip

> Prefer **Launch Templates** for modern Auto Scaling configurations.

---

# 📊 Scaling Policies

Scaling policies determine when the ASG should add or remove instances.

Common approaches include:

1. Target Tracking
2. Step Scaling
3. Simple Scaling
4. Scheduled Scaling
5. Predictive Scaling

---

# 1. Target Tracking Scaling

Target Tracking attempts to maintain a target metric value.

Example:

```text
Target CPU = 50%
```

If CPU becomes:

```text
70%
```

ASG can launch additional instances.

If CPU drops:

```text
20%
```

ASG can scale in, subject to the configured limits and policy behavior.

Architecture:

```text
CPU > Target
   |
   ↓
Scale Out
   |
   ↓
More EC2 Instances
```

---

# 2. Step Scaling

Step scaling uses different scaling actions based on how much a metric crosses thresholds.

Example:

```text
CPU 50% → Add 1 instance

CPU 70% → Add 2 instances

CPU 90% → Add 3 instances
```

---

# 3. Simple Scaling

Simple scaling uses a single scaling adjustment after a CloudWatch alarm is triggered.

Example:

```text
CPU > 70%
    ↓
Add 1 EC2
```

For many modern workloads, target tracking or step scaling is preferred.

---

# 4. Scheduled Scaling

Scheduled scaling changes capacity at a known time.

Example:

```text
09:00 AM
   ↓
Scale to 5 instances

06:00 PM
   ↓
Scale to 2 instances
```

Useful for applications with predictable traffic patterns.

---

# 5. Predictive Scaling

Predictive scaling uses historical patterns and forecasting to anticipate future demand.

Example:

```text
Historical Traffic
       ↓
Forecast
       ↓
Expected High Traffic
       ↓
Scale Before Demand
```

---

# 📈 Scale Out vs Scale In

## Scale Out

Increase the number of instances.

```text
2 EC2
 ↓
3 EC2
 ↓
4 EC2
```

Used when demand increases.

---

## Scale In

Decrease the number of instances.

```text
4 EC2
 ↓
3 EC2
 ↓
2 EC2
```

Used when demand decreases.

---

# 🧠 Vertical vs Horizontal Scaling

## Vertical Scaling

Increase the size of an instance.

```text
t3.micro
   ↓
t3.medium
   ↓
t3.large
```

This is called:

```text
Scale Up
```

---

## Horizontal Scaling

Increase the number of instances.

```text
1 EC2
 ↓
2 EC2
 ↓
4 EC2
```

This is called:

```text
Scale Out
```

### DevOps Interview Tip

Auto Scaling Groups primarily provide **horizontal scaling**.

---

# ❤️ Health Checks

Auto Scaling can use health checks to determine whether instances are healthy.

Example:

```text
ASG
 |
 +--- EC2-1 → Healthy
 |
 +--- EC2-2 → Unhealthy
 |
 +--- EC2-3 → Healthy
```

If an instance becomes unhealthy, the ASG can terminate it and launch a replacement to maintain the desired capacity.

---

# 🔄 ASG Lifecycle

Example:

```text
Launch Template
       |
       ↓
Auto Scaling Group
       |
       ↓
Launch EC2
       |
       ↓
Register with Target Group
       |
       ↓
Health Check
       |
       ↓
Receive Traffic
```

When an instance fails:

```text
EC2 Unhealthy
      |
      ↓
ASG Detects Failure
      |
      ↓
Terminate / Replace
      |
      ↓
Launch New EC2
      |
      ↓
Health Check
      |
      ↓
Traffic
```

---

# 🌐 Multi-AZ Auto Scaling

For high availability, distribute instances across multiple Availability Zones.

```text
                 Auto Scaling Group
                         |
             +-----------+-----------+
             |                       |
            AZ-1                    AZ-2
             |                       |
          EC2-1                    EC2-2
          EC2-3                    EC2-4
```

If one Availability Zone experiences a problem, instances in another AZ can continue serving traffic.

---

# 🔐 Security Groups with ASG

The EC2 instances launched by the ASG should use an appropriate Security Group.

Example:

```text
Internet
   |
   ↓
ALB Security Group
   |
   ↓
EC2 Security Group
```

Recommended pattern:

```text
ALB SG
  ↓
EC2 SG
```

Instead of:

```text
Internet
  ↓
EC2 SG
```

This keeps the application instances behind the load balancer.

---

# 🛠️ AWS CLI

## Describe Auto Scaling Groups

```bash
aws autoscaling describe-auto-scaling-groups
```

---

## Describe Launch Templates

```bash
aws ec2 describe-launch-templates
```

---

## Describe Scaling Policies

```bash
aws autoscaling describe-policies
```

---

## Set Desired Capacity

```bash
aws autoscaling set-desired-capacity \
  --auto-scaling-group-name my-asg \
  --desired-capacity 3
```

---

## Update Minimum and Maximum Capacity

```bash
aws autoscaling update-auto-scaling-group \
  --auto-scaling-group-name my-asg \
  --min-size 2 \
  --max-size 5
```

---

# 🌱 Terraform Example

## Launch Template

```hcl
resource "aws_launch_template" "web" {
  name_prefix   = "web-server-"
  image_id      = "ami-xxxxxxxx"
  instance_type = "t3.micro"

  vpc_security_group_ids = [
    aws_security_group.web.id
  ]

  user_data = base64encode(<<-EOF
    #!/bin/bash
    apt update -y
    apt install nginx -y
    systemctl enable nginx
    systemctl start nginx

    echo "Hello from Auto Scaling" > /var/www/html/index.html
  EOF
  )
}
```

---

# Auto Scaling Group

```hcl
resource "aws_autoscaling_group" "web" {
  name = "web-asg"

  min_size         = 2
  desired_capacity = 2
  max_size         = 5

  vpc_zone_identifier = [
    aws_subnet.public_1.id,
    aws_subnet.public_2.id
  ]

  launch_template {
    id      = aws_launch_template.web.id
    version = "$Latest"
  }
}
```

---

# 🎯 Target Tracking Example

```hcl
resource "aws_autoscaling_policy" "cpu" {
  name                   = "cpu-target-tracking"
  policy_type            = "TargetTrackingScaling"
  autoscaling_group_name = aws_autoscaling_group.web.name

  target_tracking_configuration {
    predefined_metric_specification {
      predefined_metric_type = "ASGAverageCPUUtilization"
    }

    target_value = 50.0
  }
}
```

This tells the ASG to try to maintain average CPU utilization around:

```text
50%
```

---

# 🔥 Real-World Architecture

A common production-style architecture:

```text
                           Users
                             |
                             ↓
                         Route 53
                             |
                             ↓
                    Application LB
                             |
                       Target Group
                             |
             +---------------+---------------+
             |               |               |
            EC2             EC2             EC2
             |               |               |
             +---------------+---------------+
                             |
                    Auto Scaling Group
                             |
                     CloudWatch Metrics
                             |
                     Scaling Policy
```

Traffic increases:

```text
Users
  ↓
ALB
  ↓
CPU increases
  ↓
CloudWatch
  ↓
Scaling Policy
  ↓
ASG
  ↓
Launch EC2
```

Traffic decreases:

```text
Users decrease
      ↓
CPU decreases
      ↓
CloudWatch
      ↓
Scaling Policy
      ↓
ASG
      ↓
Terminate unnecessary EC2
```

---

# 🧪 Troubleshooting Auto Scaling

## Problem 1: ASG is not launching instances

Check:

```text
1. Launch Template
2. AMI ID
3. Instance type
4. IAM permissions
5. Subnet/AZ configuration
6. Security Group
7. Availability Zone capacity
8. ASG minimum/desired/maximum values
```

---

## Problem 2: Instances launch but immediately terminate

Check:

```text
1. EC2 health status
2. ASG health checks
3. Load Balancer health checks
4. Application startup
5. User Data script
6. Security Group
7. Target Group configuration
```

---

## Problem 3: Instances are healthy but users cannot access the application

Check:

```text
Internet
   ↓
Load Balancer
   ↓
Listener
   ↓
Target Group
   ↓
EC2
   ↓
Application
```

Verify every layer.

---

# 🧩 Scenario-Based Interview Question

### Scenario

Your company hosts an e-commerce application on EC2.

Normally:

```text
2 EC2 instances
```

During a sale:

```text
CPU → 85%
```

The company wants to automatically add instances.

### Solution

Use:

```text
Application Load Balancer
          +
Auto Scaling Group
          +
Launch Template
          +
CloudWatch
          +
Target Tracking Policy
```

Architecture:

```text
                    Users
                      |
                      ↓
                     ALB
                      |
                Target Group
                      |
             +--------+--------+
             |                 |
            EC2               EC2
             |                 |
             +--------+--------+
                      |
                     ASG
                      |
                 CPU Target
                    50%
                      |
                 CloudWatch
                      |
                CPU = 85%
                      |
                      ↓
                  Scale Out
                      |
                      ↓
                 New EC2
```

---

# 🎯 Interview Questions

### Q1. What is Auto Scaling?

Auto Scaling automatically adjusts the number of compute resources based on application demand.

### Q2. What is an Auto Scaling Group?

An ASG manages a group of EC2 instances and maintains the configured minimum, desired, and maximum capacity.

### Q3. What are minimum, desired, and maximum capacities?

```text
Minimum  → Lowest number of instances
Desired   → Normal target number
Maximum   → Highest allowed number
```

### Q4. What is a Launch Template?

A Launch Template defines the configuration AWS should use when launching EC2 instances.

### Q5. What is scale out?

Adding more instances.

### Q6. What is scale in?

Removing instances.

### Q7. What is target tracking?

A scaling policy that attempts to maintain a target value for a selected metric.

Example:

```text
CPU Target = 50%
```

### Q8. Why use Auto Scaling with a Load Balancer?

The Load Balancer distributes traffic while the Auto Scaling Group dynamically adjusts the number of backend instances.

### Q9. Why use multiple Availability Zones?

For high availability and fault tolerance.

### Q10. What happens if an ASG instance becomes unhealthy?

The ASG can terminate the unhealthy instance and launch a replacement to maintain desired capacity.

---

# 🧠 Quick Revision

```text
Auto Scaling
│
├── Auto Scaling Group
│
├── Launch Template
│
├── Minimum Capacity
│
├── Desired Capacity
│
├── Maximum Capacity
│
├── Scaling Policies
│   ├── Target Tracking
│   ├── Step Scaling
│   ├── Simple Scaling
│   ├── Scheduled Scaling
│   └── Predictive Scaling
│
├── Health Checks
│
├── Scale Out
│
└── Scale In
```

## ⭐ Interview Shortcut

```text
Launch Template
       ↓
Auto Scaling Group
       ↓
EC2 Instances
       ↓
Target Group
       ↓
Load Balancer
       ↓
Users
```

Remember:

```text
ALB = Distributes Traffic

ASG = Manages Capacity

Launch Template = Defines EC2 Configuration

CloudWatch = Provides Metrics/Alarms

Scaling Policy = Decides Scaling Behavior

Scale Out = Add Instances

Scale In = Remove Instances

Multi-AZ = High Availability
```

