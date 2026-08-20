# 📊 10. Amazon CloudWatch

Amazon **CloudWatch** is AWS's monitoring and observability service.

It helps you monitor:

* EC2 instances
* Applications
* AWS services
* Logs
* Metrics
* Alarms
* Events
* Dashboards
* Application performance

---

# 📌 What is CloudWatch?

CloudWatch collects monitoring data from AWS resources and applications.

Simple architecture:

```text id="cw01"
AWS Resources
     |
     +---- EC2
     +---- RDS
     +---- Lambda
     +---- ALB
     +---- S3
     |
     ↓
CloudWatch
     |
     +---- Metrics
     +---- Logs
     +---- Alarms
     +---- Dashboards
     +---- Events
```

---

# 🧠 Why Do We Need CloudWatch?

Imagine you have an EC2 server.

Without monitoring:

```text id="cw02"
EC2
 |
 ?
 |
Is CPU high?
Is memory high?
Are applications failing?
Are logs showing errors?
```

With CloudWatch:

```text id="cw03"
EC2
 |
 ↓
CloudWatch
 |
 +---- CPU Metrics
 +---- Logs
 +---- Alarms
 +---- Dashboard
```

You can monitor your infrastructure and create automated responses.

---

# 🔥 Main Components

CloudWatch mainly consists of:

```text id="cw04"
CloudWatch
│
├── Metrics
├── Logs
├── Alarms
├── Dashboards
├── Events / EventBridge
├── Logs Insights
└── Synthetics
```

---

# 1. 📈 Metrics

A **metric** is a numerical measurement collected over time.

Examples:

```text id="cw05"
CPUUtilization
NetworkIn
NetworkOut
DiskReadOps
DiskWriteOps
RequestCount
Latency
```

Example:

```text id="cw06"
EC2 CPUUtilization

10%
20%
40%
70%
90%
```

You can use these metrics to understand resource behavior.

---

# EC2 Metrics

Common EC2 CloudWatch metrics:

```text id="cw07"
CPUUtilization
NetworkIn
NetworkOut
DiskReadOps
DiskWriteOps
StatusCheckFailed
```

Example:

```text id="cw08"
EC2
 |
 +---- CPU = 85%
 +---- NetworkIn = 2 GB
 +---- NetworkOut = 1 GB
 +---- StatusCheck = OK
```

---

# ⚠️ Important: Memory Monitoring

By default, standard EC2 CloudWatch metrics do **not** provide guest OS memory utilization.

For memory usage, you typically install and configure the **CloudWatch Agent**.

Example:

```text id="cw09"
EC2
 |
 ↓
CloudWatch Agent
 |
 ↓
Memory Metrics
 |
 ↓
CloudWatch
```

---

# 2. 📝 CloudWatch Logs

CloudWatch Logs stores log data from applications and AWS services.

Example:

```text id="cw10"
Application
    |
    ↓
Log File
    |
    ↓
CloudWatch Logs
```

Logs can contain:

```text id="cw11"
INFO Application started
INFO User logged in
WARNING High CPU
ERROR Database connection failed
```

---

# 📂 Log Group

A **Log Group** is a collection of related log streams.

Example:

```text id="cw12"
/aws/ec2/application
       |
       +---- Log Stream 1
       +---- Log Stream 2
       +---- Log Stream 3
```

---

# 📄 Log Stream

A log stream represents a sequence of log events from a particular source.

Example:

```text id="cw13"
/aws/ec2/application
       |
       +---- i-123456789
       |
       +---- i-987654321
```

---

# 3. 🚨 CloudWatch Alarms

CloudWatch Alarms monitor metrics and perform actions when configured thresholds are reached.

Example:

```text id="cw14"
EC2 CPU
   |
   ↓
CloudWatch
   |
   ↓
CPU > 80%
   |
   ↓
ALARM
```

Possible actions include:

* Sending notifications through SNS
* Triggering Auto Scaling actions
* Performing supported automated responses

---

# Example Alarm

```text id="cw15"
Metric:
CPUUtilization

Condition:
Greater than 80%

Period:
5 minutes

Action:
Send SNS notification
```

Architecture:

```text id="cw16"
EC2
 |
 | CPU = 85%
 ↓
CloudWatch Alarm
 |
 ↓
SNS
 |
 ↓
Email / Notification
```

---

# 4. 📊 CloudWatch Dashboards

Dashboards provide a visual view of metrics.

Example:

```text id="cw17"
+----------------------+----------------------+
| EC2 CPU              | Network              |
|                      |                      |
|       65%            |       2.5 GB         |
+----------------------+----------------------+
| ALB Requests         | ALB Latency           |
|                      |                      |
|       1200           |       80 ms           |
+----------------------+----------------------+
```

A DevOps engineer can create dashboards for:

* Infrastructure
* Applications
* Databases
* Load Balancers
* Auto Scaling

---

# 5. 🔍 CloudWatch Logs Insights

Logs Insights lets you query and analyze CloudWatch Logs.

Example query:

```text id="cw18"
fields @timestamp, @message
| filter @message like /ERROR/
| sort @timestamp desc
| limit 20
```

This can help identify application errors quickly.

---

# 6. ⚡ CloudWatch Events / EventBridge

AWS services can generate events when something happens.

Modern AWS event-driven architectures commonly use **Amazon EventBridge**.

Example:

```text id="cw19"
EC2 State Change
       |
       ↓
EventBridge
       |
       ↓
Lambda
       |
       ↓
Automation
```

Example use case:

```text id="cw20"
EC2 terminated
     ↓
EventBridge
     ↓
Lambda
     ↓
Send notification
```

---

# 7. 🧪 CloudWatch Synthetics

CloudWatch Synthetics can run automated checks against applications and endpoints.

Example:

```text id="cw21"
CloudWatch Synthetics
        |
        ↓
https://example.com
        |
        ↓
Check Response
        |
        +---- Healthy
        |
        +---- Failed
```

This can help monitor application availability from outside the application.

---

# 📡 CloudWatch Agent

The CloudWatch Agent can collect additional operating-system-level metrics and logs.

It can collect things such as:

```text id="cw22"
CPU
Memory
Disk
Swap
Processes
Log Files
```

Architecture:

```text id="cw23"
EC2
 |
 ↓
CloudWatch Agent
 |
 +---- Memory
 +---- Disk
 +---- Logs
 |
 ↓
CloudWatch
```

---

# 🛠️ Installing CloudWatch Agent on Ubuntu

Download/install the CloudWatch Agent using the current AWS-supported installation method for your architecture and operating system.

After installation, configuration is typically stored under:

```bash id="cw24"
/opt/aws/amazon-cloudwatch-agent/etc/
```

A configuration file can define metrics and log collection.

Example structure:

```json id="cw25"
{
  "metrics": {
    "metrics_collected": {
      "mem": {
        "measurement": [
          "mem_used_percent"
        ]
      },
      "disk": {
        "measurement": [
          "used_percent"
        ]
      }
    }
  }
}
```

Then start/restart the agent using its service or control command.

---

# 🧩 CloudWatch Namespaces

A namespace separates metrics from different AWS services.

Examples:

```text id="cw26"
AWS/EC2
AWS/RDS
AWS/Lambda
AWS/ApplicationELB
AWS/AutoScaling
```

Example:

```text id="cw27"
AWS/EC2
   |
   +---- CPUUtilization
   +---- NetworkIn
   +---- NetworkOut
```

---

# 📏 Dimensions

A dimension identifies the resource or characteristic associated with a metric.

Example:

```text id="cw28"
Metric:
CPUUtilization

Dimension:
InstanceId = i-123456789
```

Dimensions allow CloudWatch to distinguish metrics for different resources.

---

# ⏱️ Period

A period defines the time interval over which metric data is evaluated or aggregated.

Examples:

```text id="cw29"
1 minute
5 minutes
10 minutes
```

Example:

```text id="cw30"
CPU > 80%
for 5 minutes
```

---

# 🚦 Alarm States

A CloudWatch alarm can have states such as:

```text id="cw31"
OK
ALARM
INSUFFICIENT_DATA
```

### OK

Metric is within the configured threshold.

### ALARM

Configured threshold has been breached according to the alarm evaluation settings.

### INSUFFICIENT_DATA

There isn't enough data to determine the alarm state.

---

# 🔥 CloudWatch + Auto Scaling

CloudWatch is commonly used with Auto Scaling.

Example:

```text id="cw32"
EC2
 |
 ↓
CPU = 80%
 |
 ↓
CloudWatch
 |
 ↓
Alarm / Scaling Policy
 |
 ↓
Auto Scaling Group
 |
 ↓
Launch New EC2
```

Example:

```text id="cw33"
CPU > 70%
     ↓
Scale Out
     ↓
Add EC2
```

When demand decreases:

```text id="cw34"
CPU < Target
     ↓
Scale In
     ↓
Remove EC2
```

---

# 🔔 CloudWatch + SNS

CloudWatch Alarms can send notifications through SNS.

Architecture:

```text id="cw35"
EC2
 |
 ↓
CloudWatch Metric
 |
 ↓
CloudWatch Alarm
 |
 ↓
SNS Topic
 |
 ↓
Email / Other Subscribers
```

Example:

```text id="cw36"
CPU > 80%
     ↓
Alarm
     ↓
SNS
     ↓
DevOps Team
```

---

# ⚖️ CloudWatch + Load Balancer

CloudWatch can monitor Application Load Balancers.

Important metrics include:

```text id="cw37"
RequestCount
TargetResponseTime
HTTPCode_ELB_5XX_Count
HTTPCode_Target_5XX_Count
HealthyHostCount
UnHealthyHostCount
```

Example:

```text id="cw38"
ALB
 |
 ↓
CloudWatch
 |
 +---- Requests
 +---- Latency
 +---- 4xx
 +---- 5xx
 +---- Healthy Targets
```

---

# 🗄️ CloudWatch + RDS

RDS provides metrics such as:

```text id="cw39"
CPUUtilization
DatabaseConnections
FreeStorageSpace
ReadIOPS
WriteIOPS
ReadLatency
WriteLatency
```

Example:

```text id="cw40"
RDS
 |
 ↓
CloudWatch
 |
 ↓
CPU / Storage / Connections
```

---

# λ CloudWatch + Lambda

Lambda automatically integrates with CloudWatch Logs.

Example:

```text id="cw41"
Lambda
 |
 +---- Invocation
 +---- Duration
 +---- Errors
 +---- Throttles
 |
 ↓
CloudWatch
```

Lambda function logs can be viewed in CloudWatch Logs.

---

# 🛠️ AWS CLI Commands

## List Metrics

```bash id="cw42"
aws cloudwatch list-metrics
```

---

## Get Metric Statistics

```bash id="cw43"
aws cloudwatch get-metric-statistics \
  --namespace AWS/EC2 \
  --metric-name CPUUtilization \
  --dimensions Name=InstanceId,Value=<INSTANCE-ID> \
  --statistics Average \
  --period 300 \
  --start-time 2026-08-20T08:00:00Z \
  --end-time 2026-08-20T09:00:00Z
```

---

## List Alarms

```bash id="cw44"
aws cloudwatch describe-alarms
```

---

## List Log Groups

```bash id="cw45"
aws logs describe-log-groups
```

---

## List Log Streams

```bash id="cw46"
aws logs describe-log-streams \
  --log-group-name <LOG-GROUP>
```

---

## Get Log Events

```bash id="cw47"
aws logs get-log-events \
  --log-group-name <LOG-GROUP> \
  --log-stream-name <LOG-STREAM>
```

---

# 🌱 Terraform Example

## CloudWatch Log Group

```hcl id="cw48"
resource "aws_cloudwatch_log_group" "app" {
  name              = "/devops/application"
  retention_in_days = 7
}
```

---

# 🚨 CloudWatch Alarm

Example: EC2 CPU alarm.

```hcl id="cw49"
resource "aws_cloudwatch_metric_alarm" "cpu_high" {
  alarm_name          = "ec2-high-cpu"
  comparison_operator = "GreaterThanThreshold"

  evaluation_periods = 2
  metric_name       = "CPUUtilization"
  namespace         = "AWS/EC2"

  period          = 300
  statistic       = "Average"
  threshold       = 80
  alarm_description = "EC2 CPU utilization is high"

  dimensions = {
    InstanceId = aws_instance.web.id
  }
}
```

This monitors:

```text id="cw50"
EC2 CPU
   |
   ↓
Average
   |
   ↓
5 minutes
   |
   ↓
CPU > 80%
   |
   ↓
Alarm
```

---

# 📊 CloudWatch Monitoring Strategy

A basic DevOps monitoring strategy:

```text id="cw51"
Infrastructure
     |
     +---- CPU
     +---- Memory
     +---- Disk
     +---- Network
     |
     ↓
Application
     |
     +---- Errors
     +---- Latency
     +---- Requests
     |
     ↓
Logs
     |
     +---- Application Logs
     +---- System Logs
     |
     ↓
CloudWatch
     |
     +---- Dashboard
     +---- Alarms
     +---- Notifications
```

---

# 🧪 Troubleshooting Scenarios

## Scenario 1: EC2 CPU suddenly reaches 95%

Check:

```text id="cw52"
1. CloudWatch CPU metric
2. Application processes
3. System logs
4. Application logs
5. Recent deployments
6. Traffic increase
7. Auto Scaling configuration
```

Useful Linux command:

```bash id="cw53"
top
```

---

## Scenario 2: EC2 Memory is high

Remember:

> Standard EC2 CloudWatch metrics don't provide guest OS memory utilization automatically.

Check whether the CloudWatch Agent is installed and configured.

```text id="cw54"
EC2
 ↓
CloudWatch Agent
 ↓
Memory Metric
 ↓
CloudWatch
```

---

## Scenario 3: Application is returning many 500 errors

Check:

```text id="cw55"
ALB Metrics
     ↓
CloudWatch
     ↓
Target 5XX
     ↓
Application Logs
     ↓
CloudWatch Logs
```

Then investigate the backend application.

---

## Scenario 4: Disk is almost full

Use CloudWatch Agent for filesystem-level disk metrics.

On the server:

```bash id="cw56"
df -h
```

Then inspect:

```text id="cw57"
Disk Usage
 ↓
CloudWatch Agent
 ↓
CloudWatch Alarm
```

---

# 🏗️ Real-World DevOps Monitoring Architecture

```text id="cw58"
                       AWS Infrastructure
                              |
          +-------------------+-------------------+
          |                   |                   |
         EC2                 ALB                 RDS
          |                   |                   |
          +-------------------+-------------------+
                              |
                              ↓
                         CloudWatch
                              |
          +-------------------+-------------------+
          |                   |                   |
       Metrics              Logs                Alarms
          |                   |                   |
          ↓                   ↓                   ↓
      Dashboard         Logs Insights            SNS
                                                  |
                                                  ↓
                                             DevOps Team
```

---

# 🎯 Interview Questions

### Q1. What is CloudWatch?

CloudWatch is an AWS monitoring and observability service used to collect and analyze metrics, logs, and operational data.

### Q2. What is a CloudWatch Metric?

A metric is a numerical measurement collected over time.

Example:

```text
CPUUtilization = 75%
```

### Q3. What is a CloudWatch Alarm?

An alarm monitors a metric and changes state when configured conditions are met.

### Q4. What is CloudWatch Logs?

A service for collecting, storing, and analyzing log data.

### Q5. What is a Log Group?

A logical container for related CloudWatch log streams.

### Q6. What is a Log Stream?

A sequence of log events from a particular source.

### Q7. Does CloudWatch monitor EC2 memory by default?

No. Guest OS memory utilization generally requires the CloudWatch Agent or another monitoring solution.

### Q8. What is CloudWatch Agent?

An agent that can collect additional system-level metrics and logs from EC2 and other supported environments.

### Q9. What is a CloudWatch Dashboard?

A customizable visual display of metrics and monitoring information.

### Q10. What is CloudWatch Logs Insights?

A query and analysis tool for searching and analyzing CloudWatch Logs.

### Q11. How can CloudWatch trigger Auto Scaling?

CloudWatch metrics and alarms can be used with Auto Scaling policies to initiate scale-out or scale-in actions.

### Q12. How can CloudWatch send notifications?

CloudWatch Alarms can publish notifications to an SNS topic.

### Q13. What are the main alarm states?

```text id="cw59"
OK
ALARM
INSUFFICIENT_DATA
```

### Q14. What is a CloudWatch namespace?

A namespace groups metrics belonging to a particular AWS service or custom metric category.

Examples:

```text id="cw60"
AWS/EC2
AWS/RDS
AWS/Lambda
AWS/ApplicationELB
```

### Q15. CloudWatch vs CloudTrail?

```text id="cw61"
CloudWatch
→ Monitoring
→ Metrics
→ Logs
→ Alarms
→ Performance

CloudTrail
→ API Activity
→ Audit
→ Who did what
→ When it happened
```

---

# 🧩 Scenario-Based Interview Question

### Scenario

Your production EC2 server is experiencing high CPU.

The company wants:

```text id="cw62"
CPU > 80%
      ↓
CloudWatch Alarm
      ↓
SNS Notification
      ↓
DevOps Team
```

At the same time, the application should automatically scale.

Solution:

```text id="cw63"
                         EC2
                          |
                          ↓
                     CloudWatch
                          |
                +---------+---------+
                |                   |
                ↓                   ↓
              Alarm             Scaling Policy
                |                   |
                ↓                   ↓
               SNS                 ASG
                |                   |
                ↓                   ↓
             DevOps              New EC2
```

---

# 🧠 Quick Revision

```text id="cw64"
CloudWatch
│
├── Metrics
│
├── Logs
│   ├── Log Groups
│   └── Log Streams
│
├── Alarms
│
├── Dashboards
│
├── Logs Insights
│
├── CloudWatch Agent
│
├── Synthetics
│
└── Events / EventBridge
```

# ⭐ Interview Shortcut

```text id="cw65"
Metrics → Numerical Monitoring Data

Logs → Application/System Logs

Alarm → Detects Threshold Conditions

Dashboard → Visual Monitoring

Agent → Extra OS Metrics + Logs

Logs Insights → Query Logs

SNS → Notifications

ASG → Automatic Scaling

EventBridge → Event-Driven Automation
```

## 🔥 Real-World DevOps Flow

```text id="cw66"
Application
    |
    ↓
EC2 / ECS / EKS
    |
    +----------------+
    |                |
    ↓                ↓
 Metrics            Logs
    |                |
    +-------+--------+
            |
            ↓
       CloudWatch
            |
      +-----+------+
      |            |
      ↓            ↓
   Dashboard     Alarm
                    |
                    ↓
                   SNS
                    |
                    ↓
              DevOps Team
```

