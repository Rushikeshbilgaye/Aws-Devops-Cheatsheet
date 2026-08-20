# 14. AWS Systems Manager

## 📌 Overview

**AWS Systems Manager (SSM)** is an AWS service that helps you **manage, configure, automate, monitor, and troubleshoot AWS resources**, especially EC2 instances.

It allows DevOps engineers to manage servers without always needing direct SSH access.

### Simple Architecture

```text
                    AWS Systems Manager
                           │
          ┌────────────────┼────────────────┐
          ▼                ▼                ▼
      EC2 Instance      Automation      Parameter Store
          │
          ▼
      SSM Agent
          │
          ▼
      AWS Systems Manager
```

---

# 🎯 Why Use Systems Manager?

Traditional server management:

```text
Developer
    │
    ▼
SSH
    │
    ▼
EC2
```

Systems Manager:

```text
Developer
    │
    ▼
Systems Manager
    │
    ▼
EC2
```

Benefits:

* No need to expose SSH publicly in many use cases
* Centralized server management
* Remote command execution
* Session Manager
* Patch management
* Parameter storage
* Automation
* Inventory management
* Run Command
* Secure operational access

---

# 🧩 Important Systems Manager Features

AWS Systems Manager contains several important capabilities.

```text
Systems Manager
│
├── Session Manager
├── Run Command
├── Automation
├── Parameter Store
├── Patch Manager
├── Fleet Manager
├── State Manager
└── Inventory
```

For DevOps interviews, focus heavily on:

```text
Session Manager
Run Command
Parameter Store
Automation
Patch Manager
```

---

# 🤖 SSM Agent

The **SSM Agent** is software that runs on managed EC2 instances.

It allows Systems Manager to communicate with the instance.

```text
AWS Systems Manager
        │
        ▼
    SSM Agent
        │
        ▼
    EC2 Instance
```

The agent can perform tasks such as:

* Run commands
* Start sessions
* Apply patches
* Collect inventory
* Execute automation tasks

Many current AWS-provided AMIs include SSM Agent, but you should still verify that the agent is installed and running on the operating system you are using.

---

# 🔐 IAM Role for EC2

For an EC2 instance to communicate with Systems Manager, it generally needs an IAM role with appropriate Systems Manager permissions.

A common AWS managed policy is:

```text
AmazonSSMManagedInstanceCore
```

Architecture:

```text
EC2
 │
 ▼
IAM Role
 │
 └── AmazonSSMManagedInstanceCore
          │
          ▼
   Systems Manager
```

### Important

The IAM role should follow **least privilege**.

Do not attach `AdministratorAccess` just to make SSM work.

---

# 🌐 Network Requirements

The EC2 instance needs network connectivity to Systems Manager endpoints.

Depending on the architecture, this can be achieved through:

### Option 1: Internet/NAT connectivity

```text
Private EC2
    │
    ▼
NAT Gateway
    │
    ▼
AWS Systems Manager
```

### Option 2: VPC Endpoints

For private environments, VPC endpoints can provide private connectivity to required AWS services.

```text
Private EC2
    │
    ▼
VPC Endpoint
    │
    ▼
Systems Manager
```

Common Systems Manager-related endpoints include:

```text
ssm
ssmmessages
ec2messages
```

The exact endpoint requirements can vary by Region and current Systems Manager architecture, so verify AWS documentation when designing a production environment.

---

# 🖥️ Session Manager

## What is Session Manager?

**Session Manager** allows you to connect to an EC2 instance through Systems Manager without opening inbound SSH access.

Traditional approach:

```text
Laptop
   │
   │ SSH :22
   ▼
Security Group
   │
   ▼
EC2
```

Session Manager:

```text
Laptop
   │
   ▼
AWS Systems Manager
   │
   ▼
SSM Agent
   │
   ▼
EC2
```

---

# ⭐ Benefits of Session Manager

* No SSH key required
* No inbound port 22 required for the session itself
* Centralized access control through IAM
* Can work with private EC2 instances when network prerequisites are satisfied
* Session activity can be logged
* Better auditing and access management

---

# 🛠️ Start Session Using AWS CLI

```bash
aws ssm start-session \
  --target i-0123456789abcdef0
```

Example:

```bash
aws ssm start-session \
  --target i-0abc123456789def0
```

After connecting:

```text
sh-5.2$
```

You can execute commands on the instance.

---

# 🔥 Session Manager vs SSH

| Feature              | SSH                   | Session Manager                 |
| -------------------- | --------------------- | ------------------------------- |
| Port 22              | Usually required      | Not required for the session    |
| SSH key              | Usually required      | Not required                    |
| IAM integration      | Limited               | Strong                          |
| Centralized auditing | Additional setup      | Supported                       |
| Private instances    | Requires network path | Can work with VPC endpoints/NAT |
| Access control       | OS-based              | IAM + OS permissions            |
| AWS-native           | ❌                     | ✅                               |

### Interview Answer

> Session Manager allows secure access to managed EC2 instances without requiring direct inbound SSH access.

---

# 📢 Run Command

## What is Run Command?

**Run Command** allows you to execute commands remotely on one or more managed instances.

Example:

```text
Systems Manager
       │
       ▼
   Run Command
       │
   ┌───┼────┐
   ▼   ▼    ▼
 EC2 EC2   EC2
```

You can run commands such as:

```bash
sudo apt update
```

or:

```bash
sudo systemctl restart nginx
```

---

# 🛠️ Run Command Using CLI

Example:

```bash
aws ssm send-command \
  --instance-ids i-0123456789abcdef0 \
  --document-name "AWS-RunShellScript" \
  --parameters 'commands=["sudo systemctl restart nginx"]'
```

For multiple instances:

```bash
aws ssm send-command \
  --instance-ids i-11111111111111111 i-22222222222222222 \
  --document-name "AWS-RunShellScript" \
  --parameters 'commands=["uptime"]'
```

---

# 📄 SSM Documents

Systems Manager uses **SSM Documents** to define actions and automation steps.

Examples:

```text
AWS-RunShellScript
AWS-RunPowerShellScript
AWS-ConfigureAWSPackage
```

For Linux:

```bash
AWS-RunShellScript
```

For Windows:

```text
AWS-RunPowerShellScript
```

---

# 🔑 Parameter Store

## What is Parameter Store?

**AWS Systems Manager Parameter Store** provides secure, hierarchical storage for configuration values and secrets.

Examples:

```text
/database/host
/database/port
/application/environment
/application/api-url
```

Values can be:

* String
* StringList
* SecureString

---

# 🔐 SecureString

Sensitive values can be stored as:

```text
SecureString
```

Example:

```text
/database/password
```

Architecture:

```text
Application
     │
     ▼
Parameter Store
     │
     ▼
SecureString
     │
     ▼
AWS KMS encryption
```

Access should be controlled using IAM.

---

# 🛠️ Parameter Store CLI

## Create Parameter

```bash
aws ssm put-parameter \
  --name "/app/environment" \
  --value "production" \
  --type "String"
```

---

## Create Secure Parameter

```bash
aws ssm put-parameter \
  --name "/app/db-password" \
  --value "MySecretPassword" \
  --type "SecureString"
```

⚠️ Do not put real production secrets directly into shell history, scripts, or Git repositories.

---

## Get Parameter

```bash
aws ssm get-parameter \
  --name "/app/environment"
```

---

## Get Secure Parameter

```bash
aws ssm get-parameter \
  --name "/app/db-password" \
  --with-decryption
```

---

## List Parameters

```bash
aws ssm describe-parameters
```

---

# 🌳 Parameter Hierarchy

Parameter Store supports hierarchical names.

Example:

```text
/app/
│
├── dev/
│   ├── database-url
│   └── api-key
│
├── test/
│   ├── database-url
│   └── api-key
│
└── prod/
    ├── database-url
    └── api-key
```

This makes configuration management easier across environments.

---

# 🤖 Automation

Systems Manager Automation allows you to automate operational tasks.

Example:

```text
CloudWatch Alarm
      │
      ▼
Systems Manager Automation
      │
      ▼
Restart EC2
```

Possible tasks:

* Restart instances
* Create AMIs
* Modify resources
* Apply patches
* Execute operational workflows

---

# 🩹 Patch Manager

**Patch Manager** helps automate operating-system patching.

Example:

```text
Systems Manager
      │
      ▼
Patch Manager
      │
 ┌────┼────┐
 ▼    ▼    ▼
EC2  EC2  EC2
 │    │    │
 ▼    ▼    ▼
Patches
```

It can help manage:

* Security patches
* Operating system updates
* Patch baselines
* Maintenance operations

---

# 📋 Inventory

Systems Manager Inventory collects information about managed instances.

Examples:

```text
Operating System
Installed Applications
Network Configuration
Instance Details
```

Example:

```text
EC2 Instance
     │
     ▼
SSM Agent
     │
     ▼
Inventory
     │
     ├── OS
     ├── Applications
     └── Configuration
```

---

# 📊 Fleet Manager

Fleet Manager provides a centralized interface for managing server fleets.

It can help administrators inspect:

* Files
* Processes
* Users
* Logs
* Performance information
* Instance details

---

# 🔄 State Manager

State Manager helps maintain instances in a desired configuration.

Example:

```text
Desired State:
Nginx installed
Nginx running
Configuration applied
```

State Manager can help keep managed instances aligned with that configuration.

---

# 🚀 DevOps Use Case

Suppose you have 20 EC2 instances running Nginx.

Without SSM:

```text
Engineer
   │
   ├── SSH → EC2-1
   ├── SSH → EC2-2
   ├── SSH → EC2-3
   ├── SSH → EC2-4
   └── ...
```

With Systems Manager:

```text
Engineer
    │
    ▼
Systems Manager
    │
    ▼
Run Command
    │
 ┌──┼─────────────┐
 ▼  ▼             ▼
EC2 EC2           EC2
```

One command can be executed across multiple managed instances.

---

# 💼 Real-World DevOps Example

### Scenario

You need to restart Nginx on 50 production servers.

Instead of manually connecting to each server:

```bash
ssh user@server
sudo systemctl restart nginx
```

Use Systems Manager Run Command.

```text
Jenkins / Engineer
        │
        ▼
Systems Manager
        │
        ▼
Run Command
        │
 ┌──────┼──────────┐
 ▼      ▼          ▼
EC2-1  EC2-2 ... EC2-50
```

Command:

```bash
sudo systemctl restart nginx
```

This is faster and easier to audit.

---

# 🔥 Systems Manager + Jenkins

Systems Manager can be used in CI/CD pipelines.

Example:

```text
GitHub
   │
   ▼
Jenkins
   │
   ├── Build
   ├── Test
   └── Deploy
         │
         ▼
   AWS Systems Manager
         │
         ▼
      EC2 Fleet
```

Jenkins can invoke Systems Manager commands using the AWS CLI or AWS SDK.

Example:

```bash
aws ssm send-command \
  --instance-ids i-0123456789abcdef0 \
  --document-name "AWS-RunShellScript" \
  --parameters 'commands=["sudo systemctl restart myapp"]'
```

---

# 🔐 Security Best Practices

## 1. Use IAM Roles

Attach an appropriate IAM role to EC2 instances.

```text
EC2
 │
 ▼
IAM Role
 │
 ▼
SSM Permissions
```

---

## 2. Avoid Public SSH

Where practical, use Session Manager instead of exposing SSH to the internet.

---

## 3. Use Least Privilege

Only allow required Systems Manager actions.

---

## 4. Encrypt Sensitive Parameters

Use:

```text
SecureString
```

for sensitive configuration values.

---

## 5. Control Parameter Access

Use IAM policies to control who can read or modify parameters.

---

## 6. Monitor Activity

Use CloudTrail to audit Systems Manager API activity.

```text
Systems Manager
      │
      ▼
CloudTrail
      │
      ▼
Audit Logs
```

---

# 🔎 Troubleshooting SSM

If an EC2 instance does not appear as a managed instance, check the following.

### 1. Is SSM Agent running?

On Ubuntu:

```bash
sudo systemctl status snap.amazon-ssm-agent.amazon-ssm-agent
```

On systems where the agent is installed as a standard systemd service:

```bash
sudo systemctl status amazon-ssm-agent
```

---

### 2. Does EC2 have the correct IAM role?

Check that the instance has an appropriate role containing:

```text
AmazonSSMManagedInstanceCore
```

---

### 3. Does the instance have network connectivity?

The instance needs access to the required Systems Manager endpoints.

Check:

```text
Internet/NAT
or
VPC Endpoints
```

---

### 4. Is the instance running?

Verify:

```bash
aws ec2 describe-instances
```

---

### 5. Check SSM managed instances

```bash
aws ssm describe-instance-information
```

---

# 🛠️ Useful AWS CLI Commands

## List Managed Instances

```bash
aws ssm describe-instance-information
```

---

## Start Session

```bash
aws ssm start-session \
  --target i-0123456789abcdef0
```

---

## Send Command

```bash
aws ssm send-command \
  --instance-ids i-0123456789abcdef0 \
  --document-name "AWS-RunShellScript" \
  --parameters 'commands=["uptime"]'
```

---

## List Commands

```bash
aws ssm list-commands
```

---

## List Command Invocations

```bash
aws ssm list-command-invocations
```

---

## Get Parameter

```bash
aws ssm get-parameter \
  --name "/app/environment"
```

---

## Get Secure Parameter

```bash
aws ssm get-parameter \
  --name "/app/db-password" \
  --with-decryption
```

---

# ⚖️ Session Manager vs Run Command

| Feature            | Session Manager                  | Run Command              |
| ------------------ | -------------------------------- | ------------------------ |
| Interactive shell  | ✅                                | ❌                        |
| Execute commands   | ✅                                | ✅                        |
| Multiple instances | Possible, but different use case | ✅                        |
| SSH required       | ❌                                | ❌                        |
| IAM integration    | ✅                                | ✅                        |
| Automation         | Limited                          | Strong                   |
| Best for           | Interactive troubleshooting      | Remote command execution |

### Easy way to remember

```text
Session Manager → "I want to log into the server."

Run Command     → "I want to execute this command on servers."
```

---

# ⚖️ Parameter Store vs Secrets Manager

| Feature                | Parameter Store | Secrets Manager                       |
| ---------------------- | --------------- | ------------------------------------- |
| Configuration storage  | ✅               | ✅                                     |
| Secure values          | ✅               | ✅                                     |
| SecureString           | ✅               | N/A                                   |
| Secret rotation        | More limited    | Strong built-in rotation capabilities |
| Simple configuration   | Excellent       | Usually more than needed              |
| AWS-native integration | ✅               | ✅                                     |

### Example

Use Parameter Store for:

```text
/app/environment = production
/app/api-url = https://api.example.com
```

Use Secrets Manager when you need advanced secret-management capabilities such as managed rotation.

---

# 🎯 Interview Questions

### 1. What is AWS Systems Manager?

AWS Systems Manager is a managed service for operating and automating AWS resources, especially EC2 instances.

### 2. What is Session Manager?

Session Manager provides secure interactive access to managed instances without requiring direct inbound SSH access.

### 3. Does Session Manager require port 22?

**No.**

Session Manager does not require inbound SSH port 22 for the session.

### 4. What is SSM Agent?

SSM Agent is software running on managed instances that allows Systems Manager to perform management operations.

### 5. What IAM policy is commonly used for EC2 Systems Manager access?

```text
AmazonSSMManagedInstanceCore
```

### 6. What is Run Command?

Run Command allows you to execute commands remotely on one or more managed instances.

### 7. What is Parameter Store?

Parameter Store provides hierarchical storage for configuration data and sensitive parameters.

### 8. What is SecureString?

`SecureString` is a Parameter Store parameter type intended for sensitive values and uses encryption.

### 9. What is Patch Manager?

Patch Manager helps automate and manage operating-system patching across managed instances.

### 10. How would you troubleshoot an EC2 instance missing from Systems Manager?

Check:

```text
1. EC2 is running
2. SSM Agent is installed/running
3. IAM role is attached
4. Required SSM permissions exist
5. Network connectivity exists
6. Systems Manager endpoints are reachable
7. Region is correct
```

---

# 🧠 Quick Revision

```text
AWS Systems Manager
│
├── Session Manager
│     └── Secure shell access
│
├── Run Command
│     └── Remote command execution
│
├── Parameter Store
│     └── Configuration + SecureString
│
├── Automation
│     └── Automated operational tasks
│
├── Patch Manager
│     └── OS patching
│
├── Inventory
│     └── Instance information
│
├── Fleet Manager
│     └── Fleet management
│
└── State Manager
      └── Desired configuration
```

## ⭐ Must-Remember Commands

```bash
# Check managed instances
aws ssm describe-instance-information

# Start Session Manager session
aws ssm start-session --target <INSTANCE_ID>

# Execute command
aws ssm send-command \
  --instance-ids <INSTANCE_ID> \
  --document-name "AWS-RunShellScript" \
  --parameters 'commands=["uptime"]'

# Get parameter
aws ssm get-parameter \
  --name "/app/environment"

# Get encrypted parameter
aws ssm get-parameter \
  --name "/app/db-password" \
  --with-decryption
```

> **Systems Manager = Manage your AWS servers without manually SSH-ing into every machine.**

> **Session Manager = Interactive access.**

> **Run Command = Execute commands.**

> **Parameter Store = Store configuration and parameters.**

> **Patch Manager = Manage OS patches.**
