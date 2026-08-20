# 13. AWS CLI

## 📌 Overview

**AWS CLI (Command Line Interface)** is a command-line tool that allows you to interact with AWS services using commands instead of the AWS Management Console.

It is widely used by **DevOps engineers, system administrators, developers, and automation tools**.

### Simple Example

Instead of opening the AWS Console to list S3 buckets:

```bash
aws s3 ls
```

Instead of manually launching resources, AWS CLI can be used inside:

```text
Jenkins
Terraform
Shell Scripts
GitHub Actions
CI/CD Pipelines
Automation Scripts
```

---

# 🏗️ AWS CLI Architecture

```text
Developer / DevOps Engineer
          │
          ▼
       AWS CLI
          │
          ▼
     AWS API
          │
    ┌─────┼─────┐
    ▼     ▼     ▼
   EC2   S3    IAM
```

AWS CLI sends requests to AWS APIs.

---

# 💻 Installing AWS CLI

## Ubuntu / Linux

Download and install AWS CLI v2:

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
```

Install `unzip`:

```bash
sudo apt update
sudo apt install unzip -y
```

Extract:

```bash
unzip awscliv2.zip
```

Install:

```bash
sudo ./aws/install
```

Check the installation:

```bash
aws --version
```

Example:

```text
aws-cli/2.x.x Python/3.x Linux/x86_64
```

---

# ⚙️ Configure AWS CLI

Run:

```bash
aws configure
```

You will be asked for:

```text
AWS Access Key ID:
AWS Secret Access Key:
Default region name:
Default output format:
```

Example:

```text
AWS Access Key ID: AKIAxxxxxxxxxxxx
AWS Secret Access Key: xxxxxxxxxxxxx
Default region name: ap-south-1
Default output format: json
```

---

# 🔐 AWS Credentials

AWS CLI can authenticate using several methods.

Common options include:

1. IAM role
2. Environment variables
3. AWS CLI configuration
4. AWS IAM Identity Center
5. Instance profiles on EC2

### Important

**Never hard-code AWS access keys inside GitHub repositories.**

Bad:

```bash
aws configure
```

with credentials committed into a repository.

Never commit files containing secrets.

---

# 📁 AWS CLI Configuration Files

AWS CLI commonly uses:

```text
~/.aws/credentials
~/.aws/config
```

Example configuration:

```text
~/.aws/
├── credentials
└── config
```

---

# 🌍 AWS Regions

Specify a region:

```bash
aws ec2 describe-instances --region ap-south-1
```

You can configure a default region:

```bash
aws configure set region ap-south-1
```

Check the current configuration:

```bash
aws configure list
```

---

# 👤 IAM Commands

## List IAM Users

```bash
aws iam list-users
```

---

## Get IAM User

```bash
aws iam get-user
```

---

## List Roles

```bash
aws iam list-roles
```

---

## List Policies

```bash
aws iam list-policies
```

---

# 🖥️ EC2 Commands

## List Instances

```bash
aws ec2 describe-instances
```

---

## List Running Instances

```bash
aws ec2 describe-instances \
  --filters Name=instance-state-name,Values=running
```

---

## Start Instance

```bash
aws ec2 start-instances \
  --instance-ids i-0123456789abcdef0
```

---

## Stop Instance

```bash
aws ec2 stop-instances \
  --instance-ids i-0123456789abcdef0
```

---

## Reboot Instance

```bash
aws ec2 reboot-instances \
  --instance-ids i-0123456789abcdef0
```

---

## Terminate Instance

```bash
aws ec2 terminate-instances \
  --instance-ids i-0123456789abcdef0
```

⚠️ **Be careful:** terminating an EC2 instance can be destructive.

---

# 🪣 S3 Commands

AWS CLI provides a high-level `s3` command for common S3 operations.

## List Buckets

```bash
aws s3 ls
```

---

## Create Bucket

For example:

```bash
aws s3 mb s3://my-demo-bucket-12345 \
  --region ap-south-1
```

---

## List Bucket Contents

```bash
aws s3 ls s3://my-demo-bucket-12345
```

---

## Upload File

```bash
aws s3 cp index.html \
  s3://my-demo-bucket-12345/
```

---

## Download File

```bash
aws s3 cp \
  s3://my-demo-bucket-12345/index.html .
```

---

## Copy Directory

```bash
aws s3 cp ./website \
  s3://my-demo-bucket-12345/ \
  --recursive
```

---

## Sync Directory

```bash
aws s3 sync ./website \
  s3://my-demo-bucket-12345/
```

---

## Delete Object

```bash
aws s3 rm \
  s3://my-demo-bucket-12345/index.html
```

---

# 🔍 `aws s3` vs `aws s3api`

AWS CLI provides both:

```text
aws s3
aws s3api
```

### `aws s3`

High-level commands:

```bash
aws s3 cp
aws s3 sync
aws s3 ls
aws s3 mb
```

### `aws s3api`

Low-level API commands:

```bash
aws s3api create-bucket
aws s3api put-bucket-versioning
aws s3api get-bucket-policy
```

### Easy way to remember

```text
aws s3
   ↓
High-level / easier commands

aws s3api
   ↓
Low-level API operations
```

---

# 🌐 VPC Commands

## List VPCs

```bash
aws ec2 describe-vpcs
```

---

## List Subnets

```bash
aws ec2 describe-subnets
```

---

## List Security Groups

```bash
aws ec2 describe-security-groups
```

---

## List Route Tables

```bash
aws ec2 describe-route-tables
```

---

## List Internet Gateways

```bash
aws ec2 describe-internet-gateways
```

---

# 🔑 Security Group Commands

List security groups:

```bash
aws ec2 describe-security-groups
```

Authorize HTTP:

```bash
aws ec2 authorize-security-group-ingress \
  --group-id sg-0123456789abcdef0 \
  --protocol tcp \
  --port 80 \
  --cidr 0.0.0.0/0
```

⚠️ In production, avoid unnecessarily exposing services to `0.0.0.0/0`.

---

# 🔐 AWS STS

AWS STS helps work with temporary security credentials and identity information.

## Check Current Identity

```bash
aws sts get-caller-identity
```

Example output:

```json
{
    "UserId": "AIDAXXXXXXXXX",
    "Account": "123456789012",
    "Arn": "arn:aws:iam::123456789012:user/devops"
}
```

This is one of the **most useful AWS CLI troubleshooting commands**.

---

# 📊 CloudWatch Commands

## List Alarms

```bash
aws cloudwatch describe-alarms
```

---

## Get Metric Statistics

```bash
aws cloudwatch get-metric-statistics
```

The command requires parameters such as:

```text
Namespace
MetricName
Dimensions
StartTime
EndTime
Period
Statistics
```

---

# 🔔 SNS Commands

## List Topics

```bash
aws sns list-topics
```

---

## Create Topic

```bash
aws sns create-topic \
  --name devops-alerts
```

---

## Publish Message

```bash
aws sns publish \
  --topic-arn arn:aws:sns:ap-south-1:123456789012:devops-alerts \
  --message "Deployment completed successfully"
```

---

# 📦 SQS Commands

## List Queues

```bash
aws sqs list-queues
```

---

## Send Message

```bash
aws sqs send-message \
  --queue-url <QUEUE_URL> \
  --message-body "Hello from AWS CLI"
```

---

## Receive Message

```bash
aws sqs receive-message \
  --queue-url <QUEUE_URL>
```

---

# 🕵️ CloudTrail Commands

## List Trails

```bash
aws cloudtrail list-trails
```

---

## Lookup Events

```bash
aws cloudtrail lookup-events
```

---

## Search for EC2 Termination

```bash
aws cloudtrail lookup-events \
  --lookup-attributes \
  AttributeKey=EventName,AttributeValue=TerminateInstances
```

---

# 🧰 Useful Global AWS CLI Options

Many AWS CLI commands support common options.

## Region

```bash
--region ap-south-1
```

---

## Profile

```bash
--profile dev
```

---

## Output

```bash
--output json
```

Other common formats:

```text
json
text
table
yaml
yaml-stream
```

Example:

```bash
aws ec2 describe-instances \
  --output table
```

---

# 👤 AWS CLI Profiles

You can configure multiple AWS accounts or environments.

Example:

```bash
aws configure --profile dev
```

```bash
aws configure --profile prod
```

Then use:

```bash
aws s3 ls --profile dev
```

or:

```bash
aws ec2 describe-instances --profile prod
```

### Example

```text
AWS CLI
 │
 ├── dev profile
 │      └── Development Account
 │
 └── prod profile
        └── Production Account
```

This is useful when working with multiple AWS environments.

---

# 🔎 Filtering AWS CLI Output

AWS CLI supports filtering using:

```text
--filters
--query
```

## Example

Find running EC2 instances:

```bash
aws ec2 describe-instances \
  --filters Name=instance-state-name,Values=running
```

---

# 🧮 JMESPath `--query`

The `--query` option allows you to extract specific information from AWS CLI output.

Example:

```bash
aws ec2 describe-instances \
  --query 'Reservations[*].Instances[*].InstanceId'
```

Get public IP addresses:

```bash
aws ec2 describe-instances \
  --query 'Reservations[*].Instances[*].PublicIpAddress'
```

Get instance ID and state:

```bash
aws ec2 describe-instances \
  --query 'Reservations[*].Instances[*].[InstanceId,State.Name]'
```

---

# 📋 Output Formats

JSON:

```bash
aws ec2 describe-instances \
  --output json
```

Table:

```bash
aws ec2 describe-instances \
  --output table
```

Text:

```bash
aws ec2 describe-instances \
  --output text
```

Example:

```text
JSON → Best for scripts/APIs
Table → Easy for humans
Text → Simple shell processing
```

---

# 🤖 AWS CLI in DevOps

AWS CLI is heavily used in automation.

### Jenkins Example

```text
GitHub
   │
   ▼
Jenkins
   │
   ├── Build
   ├── Test
   ├── Docker Build
   │
   ▼
AWS CLI
   │
   ├── S3
   ├── EC2
   ├── ECR
   └── ECS
```

Example Jenkins shell command:

```bash
aws s3 sync ./build s3://my-website-bucket/
```

---

# 🚀 AWS CLI in CI/CD

A typical deployment can look like:

```text
Developer
    │
    ▼
GitHub
    │
    ▼
Jenkins
    │
    ▼
Build Application
    │
    ▼
Run Tests
    │
    ▼
AWS CLI
    │
    ▼
AWS Environment
```

Example:

```bash
aws ecr get-login-password --region ap-south-1 \
  | docker login \
  --username AWS \
  --password-stdin <ACCOUNT_ID>.dkr.ecr.ap-south-1.amazonaws.com
```

Then:

```bash
docker push <ECR_REPOSITORY>:latest
```

---

# 🔐 AWS CLI Security Best Practices

## 1. Never hard-code credentials

Never put:

```text
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
```

inside source code.

---

## 2. Use IAM Roles

For EC2:

```text
EC2
 │
 ▼
IAM Role
 │
 ▼
Temporary Credentials
 │
 ▼
AWS CLI
```

This is generally preferable to storing long-lived access keys on the server.

---

## 3. Follow Least Privilege

Give only the permissions required.

Example:

```text
Jenkins
   │
   ▼
IAM Role
   │
   ├── ECR permissions
   ├── S3 permissions
   └── EC2 permissions
```

Avoid giving:

```text
AdministratorAccess
```

unless there is a genuine requirement.

---

## 4. Protect Credentials

Avoid:

```bash
echo $AWS_SECRET_ACCESS_KEY
```

in CI/CD logs.

Use a proper secret-management mechanism when credentials are actually required.

---

# 🛠️ Troubleshooting

## Check AWS CLI Installation

```bash
aws --version
```

---

## Check Configuration

```bash
aws configure list
```

---

## Check Current AWS Identity

```bash
aws sts get-caller-identity
```

---

## Check Region

```bash
aws configure get region
```

---

## Check S3 Access

```bash
aws s3 ls
```

---

# ❌ Common Errors

## 1. Unable to locate credentials

```text
Unable to locate credentials
```

Possible causes:

* AWS CLI isn't configured
* No IAM role is attached
* Environment variables aren't available

Check:

```bash
aws configure list
```

Then:

```bash
aws sts get-caller-identity
```

---

## 2. AccessDenied

```text
An error occurred (AccessDenied)
```

Possible cause:

```text
IAM policy does not allow the requested action
```

Check the IAM permissions attached to the user or role.

---

## 3. Invalid Region

Example:

```text
Could not connect to the endpoint
```

Check:

```bash
aws configure get region
```

Set the correct region:

```bash
aws configure set region ap-south-1
```

---

# 💼 Real-World DevOps Scenario

### Scenario

Your Jenkins server needs to upload a build artifact to S3.

Architecture:

```text
GitHub
   │
   ▼
Jenkins
   │
   ▼
Build
   │
   ▼
AWS CLI
   │
   ▼
S3 Bucket
```

Jenkins can execute:

```bash
aws s3 sync ./build s3://my-application-bucket/
```

For production, prefer an IAM role or another secure credential mechanism rather than embedding access keys in the pipeline.

---

# 🎯 Interview Questions

### 1. What is AWS CLI?

AWS CLI is a command-line tool used to interact with AWS services through commands.

### 2. How do you check which AWS account you're using?

```bash
aws sts get-caller-identity
```

### 3. How do you configure AWS CLI?

```bash
aws configure
```

### 4. Where does AWS CLI store configuration?

Common locations:

```text
~/.aws/credentials
~/.aws/config
```

### 5. How do you use multiple AWS accounts?

Use named profiles:

```bash
aws configure --profile dev
aws configure --profile prod
```

Then:

```bash
aws s3 ls --profile dev
```

### 6. How do you list EC2 instances?

```bash
aws ec2 describe-instances
```

### 7. How do you list S3 buckets?

```bash
aws s3 ls
```

### 8. What is the difference between `aws s3` and `aws s3api`?

```text
aws s3
    ↓
High-level S3 commands

aws s3api
    ↓
Low-level S3 API commands
```

### 9. How do you filter AWS CLI output?

Use:

```bash
--filters
```

and:

```bash
--query
```

### 10. How would you securely use AWS CLI from Jenkins?

Prefer an **IAM role or securely managed temporary credentials**, apply least privilege, and never hard-code AWS secrets in the Jenkinsfile or Git repository.

---

# 🧠 Quick Revision

```text
AWS CLI
│
├── aws configure
├── aws sts get-caller-identity
├── aws ec2
├── aws s3
├── aws iam
├── aws vpc / ec2
├── aws cloudwatch
├── aws sns
├── aws sqs
├── aws cloudtrail
├── --region
├── --profile
├── --query
├── --filters
└── --output
```

## ⭐ Must-Remember Commands

```bash
# Check AWS identity
aws sts get-caller-identity

# Check configuration
aws configure list

# List S3 buckets
aws s3 ls

# List EC2 instances
aws ec2 describe-instances

# List IAM users
aws iam list-users

# List VPCs
aws ec2 describe-vpcs

# List CloudTrail trails
aws cloudtrail list-trails

# List SNS topics
aws sns list-topics

# List SQS queues
aws sqs list-queues
```

> **AWS CLI = AWS automation from the command line.**

> **For DevOps, master `aws sts`, `ec2`, `s3`, `iam`, `ecr`, `cloudwatch`, `sns`, `sqs`, and `cloudtrail`.**
