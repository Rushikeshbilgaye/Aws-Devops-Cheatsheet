# 15. AWS CloudFormation

## 📌 Overview

**AWS CloudFormation** is an AWS **Infrastructure as Code (IaC)** service that allows you to define and provision AWS infrastructure using template files.

Instead of manually creating:

```text
VPC
Subnets
EC2
Security Groups
IAM Roles
Load Balancer
RDS
```

through the AWS Console, you define them in a template and CloudFormation creates them for you.

### Simple Architecture

```text
Developer
    │
    ▼
CloudFormation Template
(YAML / JSON)
    │
    ▼
CloudFormation
    │
    ├──► VPC
    ├──► EC2
    ├──► S3
    ├──► IAM
    ├──► RDS
    └──► Load Balancer
```

---

# 🎯 Why CloudFormation?

Without Infrastructure as Code:

```text
Engineer
   │
   ├── Create VPC
   ├── Create Subnet
   ├── Create EC2
   ├── Create Security Group
   └── Configure everything manually
```

This can lead to:

* Human errors
* Configuration differences
* Difficult reproduction
* Poor documentation
* Slow deployments

With CloudFormation:

```text
CloudFormation Template
          │
          ▼
      AWS Stack
          │
     ┌────┼────┐
     ▼    ▼    ▼
    VPC   EC2  S3
```

Infrastructure becomes **repeatable and version-controlled**.

---

# 🧱 Infrastructure as Code

CloudFormation is an **Infrastructure as Code** tool.

Example:

```yaml id="x6v1ri"
Resources:
  MyBucket:
    Type: AWS::S3::Bucket
```

CloudFormation reads this definition and creates the S3 bucket.

---

# 📄 CloudFormation Templates

CloudFormation templates are commonly written in:

```text
YAML
JSON
```

YAML is generally easier to read.

Example:

```yaml id="6tdcsm"
AWSTemplateFormatVersion: '2010-09-09'

Description: Simple S3 bucket

Resources:
  MyBucket:
    Type: AWS::S3::Bucket
```

---

# 🏗️ CloudFormation Architecture

```text
                    Template
                 YAML / JSON
                       │
                       ▼
                CloudFormation
                       │
                       ▼
                     Stack
                       │
        ┌──────────────┼──────────────┐
        ▼              ▼              ▼
       VPC            EC2             S3
        │              │              │
        └──────────────┼──────────────┘
                       ▼
                  AWS Resources
```

---

# 📦 Important CloudFormation Concepts

## 1. Template

A template is the file that defines your infrastructure.

Example:

```text
template.yaml
```

---

## 2. Stack

A **stack** is a collection of AWS resources created and managed together from a CloudFormation template.

```text id="8m9j6n"
CloudFormation Stack
│
├── VPC
├── Subnet
├── Security Group
├── EC2
└── IAM Role
```

---

## 3. Resources

Resources are the AWS infrastructure components created by the stack.

Example:

```yaml id="u4o4j3"
Resources:
  MyBucket:
    Type: AWS::S3::Bucket
```

The resource type is:

```text id="l2s6w8"
AWS::S3::Bucket
```

---

## 4. Parameters

Parameters allow users to provide values when creating or updating a stack.

Example:

```yaml id="xw6w4z"
Parameters:
  Environment:
    Type: String
    Default: dev
```

You can use:

```text
dev
test
prod
```

without changing the template.

---

# 🔗 References

CloudFormation can reference parameters and other values.

Example:

```yaml id="v9g5z4"
Parameters:
  BucketName:
    Type: String

Resources:
  MyBucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: !Ref BucketName
```

`!Ref` retrieves the value of a parameter or resource reference.

---

# 🧮 Intrinsic Functions

CloudFormation provides intrinsic functions for dynamic template behavior.

Common functions:

```text
!Ref
!GetAtt
!Sub
!Join
!Select
!Split
!If
!Equals
!FindInMap
```

---

# 🔗 `!Ref`

Used to reference a parameter or resource.

Example:

```yaml id="k7d7i4"
Parameters:
  Environment:
    Type: String
    Default: dev

Resources:
  MyBucket:
    Type: AWS::S3::Bucket
    Tags:
      - Key: Environment
        Value: !Ref Environment
```

---

# 📌 `!GetAtt`

Retrieves an attribute from a resource.

Example:

```yaml id="g0h8y5"
Outputs:
  BucketArn:
    Value: !GetAtt MyBucket.Arn
```

---

# 🔤 `!Sub`

Used for string substitution.

Example:

```yaml id="w6r2xy"
Outputs:
  Message:
    Value: !Sub "Environment: ${AWS::Region}"
```

---

# 🔗 `!Join`

Combines strings.

Example:

```yaml id="1qk9tw"
Value: !Join
  - "-"
  - - dev
    - web
    - server
```

Result:

```text
dev-web-server
```

---

# 🗺️ Mappings

Mappings provide static key-value mappings.

Example:

```yaml id="2g8x3r"
Mappings:
  RegionMap:
    ap-south-1:
      Environment: Mumbai
    us-east-1:
      Environment: Virginia
```

Mappings are useful when configuration depends on a known set of values.

---

# 📤 Outputs

Outputs expose useful information after a stack is created.

Example:

```yaml id="2d0a7c"
Outputs:
  BucketName:
    Description: S3 Bucket Name
    Value: !Ref MyBucket
```

You can output:

* Resource names
* Resource IDs
* ARNs
* URLs
* IP addresses

---

# 🔄 Dependencies

CloudFormation automatically determines many resource dependencies.

Example:

```text id="3l7qz9"
VPC
 │
 ▼
Subnet
 │
 ▼
EC2
```

CloudFormation understands that the dependent resource must be created after the resource it depends on.

You can also explicitly define dependencies using:

```yaml id="w1q5vy"
DependsOn:
```

---

# 🔁 CloudFormation Stack Lifecycle

Typical lifecycle:

```text id="r7w8d2"
Template
   │
   ▼
Create Stack
   │
   ▼
CloudFormation
   │
   ▼
Create Resources
   │
   ▼
CREATE_COMPLETE
```

When updating:

```text id="8n6r0k"
Updated Template
      │
      ▼
Update Stack
      │
      ▼
Modify Resources
      │
      ▼
UPDATE_COMPLETE
```

---

# 🗑️ Stack Deletion

When a stack is deleted, CloudFormation generally deletes the resources managed by that stack.

```text id="n3k8cv"
Delete Stack
     │
     ▼
CloudFormation
     │
     ├──► Delete EC2
     ├──► Delete Security Group
     ├──► Delete Subnet
     └──► Delete VPC
```

⚠️ Be careful with stack deletion because it can delete infrastructure and potentially cause data loss.

Some resources can be configured with deletion policies such as:

```yaml id="q3p8lx"
DeletionPolicy: Retain
```

---

# 🛡️ DeletionPolicy

CloudFormation supports resource deletion behavior.

Common options include:

```text
Delete
Retain
Snapshot
```

Example:

```yaml id="p9q7h2"
Resources:
  MyBucket:
    Type: AWS::S3::Bucket
    DeletionPolicy: Retain
```

This can protect important resources from being automatically deleted when a stack is deleted.

---

# 🔄 UpdatePolicy

Some AWS resources support update policies that control how updates occur.

This is especially relevant to resources such as Auto Scaling groups.

---

# 🧪 Change Sets

A **Change Set** allows you to preview how a stack update will modify resources before executing it.

```text id="v7q8w1"
Updated Template
      │
      ▼
  Change Set
      │
      ▼
Review Changes
      │
      ▼
Execute
```

This is useful in production environments.

---

# 🚨 Drift Detection

**CloudFormation Drift Detection** identifies differences between the configuration defined in the CloudFormation template and the actual configuration of resources.

Example:

```text id="y1v9cz"
CloudFormation Template
        │
        │ Expected
        ▼
      EC2
        ▲
        │ Actual
        │
Someone manually changed
the Security Group
```

CloudFormation can detect this drift.

---

# 🏷️ Stack Tags

You can apply tags to stacks and resources.

Example:

```yaml id="d5e7yt"
Tags:
  - Key: Environment
    Value: Production
  - Key: Project
    Value: DevOps
```

Useful for:

* Cost allocation
* Resource organization
* Automation
* Environment identification

---

# 🔐 IAM and CloudFormation

CloudFormation requires permissions to create and manage resources.

Example:

```text id="m7x2r9"
CloudFormation
      │
      ▼
IAM Permissions
      │
      ├──► EC2
      ├──► S3
      ├──► VPC
      └──► IAM
```

For some deployments, CloudFormation can use a service role.

The role should follow **least privilege**.

---

# 📋 Basic CloudFormation Template

Here is a simple EC2 example:

```yaml id="v4q8sn"
AWSTemplateFormatVersion: '2010-09-09'

Description: Simple EC2 instance

Parameters:
  InstanceType:
    Type: String
    Default: t3.micro

Resources:

  WebServer:
    Type: AWS::EC2::Instance

    Properties:
      InstanceType: !Ref InstanceType

      ImageId: ami-xxxxxxxxxxxxxxxxx

      Tags:
        - Key: Name
          Value: CloudFormation-WebServer

Outputs:

  InstanceId:
    Description: EC2 Instance ID
    Value: !Ref WebServer
```

⚠️ The AMI ID must be valid for the AWS Region where you deploy.

---

# 🪣 CloudFormation S3 Example

```yaml id="f5m9t2"
AWSTemplateFormatVersion: '2010-09-09'

Description: Create an S3 bucket

Resources:

  MyBucket:
    Type: AWS::S3::Bucket

    Properties:
      VersioningConfiguration:
        Status: Enabled

Outputs:

  BucketName:
    Value: !Ref MyBucket

  BucketArn:
    Value: !GetAtt MyBucket.Arn
```

---

# 🛠️ AWS CLI Commands

## Validate Template

```bash id="k9v4yq"
aws cloudformation validate-template \
  --template-body file://template.yaml
```

---

## Create Stack

```bash id="m4s8dx"
aws cloudformation create-stack \
  --stack-name my-stack \
  --template-body file://template.yaml
```

---

## Create Stack with Parameters

```bash id="2k7z9p"
aws cloudformation create-stack \
  --stack-name my-stack \
  --template-body file://template.yaml \
  --parameters \
  ParameterKey=Environment,ParameterValue=dev
```

---

## List Stacks

```bash id="h6q1tw"
aws cloudformation list-stacks
```

---

## Describe Stack

```bash id="8w3y5c"
aws cloudformation describe-stacks \
  --stack-name my-stack
```

---

## Update Stack

```bash id="4v1q6n"
aws cloudformation update-stack \
  --stack-name my-stack \
  --template-body file://template.yaml
```

---

## Delete Stack

```bash id="f9k3r7"
aws cloudformation delete-stack \
  --stack-name my-stack
```

---

## List Stack Resources

```bash id="q5d8w1"
aws cloudformation list-stack-resources \
  --stack-name my-stack
```

---

# 📊 Check Stack Events

Stack events are extremely useful when troubleshooting deployment failures.

```bash id="x1m7q4"
aws cloudformation describe-stack-events \
  --stack-name my-stack
```

Example:

```text id="s8k2vb"
CREATE_IN_PROGRESS
        │
        ▼
CREATE_FAILED
        │
        ▼
Check Stack Events
```

---

# 🚨 Troubleshooting CloudFormation

## Problem 1: Stack Creation Failed

Check:

```bash id="n5p7x2"
aws cloudformation describe-stack-events \
  --stack-name my-stack
```

Look for:

```text
CREATE_FAILED
UPDATE_FAILED
DELETE_FAILED
```

---

## Problem 2: Insufficient Permissions

Possible error:

```text id="9p1w4f"
AccessDenied
```

Check the IAM permissions being used by CloudFormation.

---

## Problem 3: Invalid AMI

Example:

```text id="m8v2q1"
The image id 'ami-xxxx' does not exist
```

Possible cause:

The AMI does not exist in the selected AWS Region.

---

## Problem 4: Resource Already Exists

Some resources require globally or account-unique names.

For example:

```text id="y8k1w3"
S3 bucket name already exists
```

Choose a unique name or allow CloudFormation to generate a physical name where appropriate.

---

# 🚀 CloudFormation in DevOps

CloudFormation can be integrated into CI/CD pipelines.

```text id="n8p4q0"
Developer
    │
    ▼
GitHub
    │
    ▼
Jenkins
    │
    ├── Validate Template
    │
    ├── Create Change Set
    │
    ├── Review
    │
    └── Deploy
           │
           ▼
     CloudFormation
           │
           ▼
       AWS Resources
```

Example Jenkins commands:

```bash id="c4q6m8"
aws cloudformation validate-template \
  --template-body file://template.yaml
```

Then:

```bash id="z9r2x7"
aws cloudformation create-stack \
  --stack-name devops-stack \
  --template-body file://template.yaml
```

---

# 🏗️ Real-World DevOps Project

A CloudFormation template can create a complete application infrastructure:

```text id="h7n3y1"
CloudFormation
      │
      ├── VPC
      │
      ├── Public Subnets
      │
      ├── Private Subnets
      │
      ├── Internet Gateway
      │
      ├── NAT Gateway
      │
      ├── Security Groups
      │
      ├── Application Load Balancer
      │
      ├── Auto Scaling Group
      │
      ├── EC2 Instances
      │
      └── RDS
```

One template can reproduce the environment consistently.

---

# ⚖️ CloudFormation vs Terraform

| Feature               | CloudFormation                 | Terraform            |
| --------------------- | ------------------------------ | -------------------- |
| Developer             | AWS                            | HashiCorp            |
| Cloud support         | AWS-focused                    | Multi-cloud          |
| Language              | YAML / JSON                    | HCL                  |
| AWS integration       | Native                         | Excellent            |
| State                 | Managed by CloudFormation      | Terraform state      |
| Multi-cloud           | Limited                        | ✅                    |
| Modules               | Nested stacks/modules patterns | Strong module system |
| AWS-specific features | Usually available quickly      | Provider-dependent   |
| DevOps popularity     | High                           | Very high            |

### Easy way to remember

```text id="r3w5j9"
CloudFormation → AWS-native IaC

Terraform → Multi-cloud IaC
```

---

# 🔥 CloudFormation vs Manual AWS Console

```text
Manual
  │
  ├── Click
  ├── Configure
  ├── Repeat
  └── Risk of mistakes

CloudFormation
  │
  ├── Write template
  ├── Version control
  ├── Deploy
  └── Reproduce
```

---

# 🎯 Interview Questions

### 1. What is AWS CloudFormation?

CloudFormation is AWS's Infrastructure as Code service for defining and provisioning AWS resources using templates.

### 2. What is a CloudFormation stack?

A stack is a collection of AWS resources managed as a single unit.

### 3. Which languages can CloudFormation templates use?

Commonly:

```text
YAML
JSON
```

### 4. What is the difference between Parameters and Outputs?

```text
Parameters → Input values

Outputs → Values exposed after deployment
```

### 5. What is `!Ref`?

`!Ref` retrieves a parameter value or a resource's reference value.

### 6. What is `!GetAtt`?

`!GetAtt` retrieves a specific attribute of a CloudFormation resource.

### 7. What is a Change Set?

A Change Set previews the proposed changes to a stack before the changes are executed.

### 8. What is drift detection?

Drift detection identifies differences between the template-defined configuration and the actual resource configuration.

### 9. How do you troubleshoot a failed CloudFormation stack?

Check stack events:

```bash id="g5c2r8"
aws cloudformation describe-stack-events \
  --stack-name my-stack
```

### 10. CloudFormation vs Terraform?

```text id="j7n4w2"
CloudFormation → AWS-native

Terraform → Multi-cloud
```

---

# 🧠 Quick Revision

```text id="x6t9p3"
CloudFormation
│
├── Infrastructure as Code
├── YAML / JSON
├── Template
├── Stack
├── Resources
├── Parameters
├── Mappings
├── Conditions
├── Outputs
├── Intrinsic Functions
├── Change Sets
├── Drift Detection
├── DeletionPolicy
└── Stack Events
```

## ⭐ Must-Remember Commands

```bash
# Validate
aws cloudformation validate-template \
  --template-body file://template.yaml

# Create
aws cloudformation create-stack \
  --stack-name my-stack \
  --template-body file://template.yaml

# List
aws cloudformation list-stacks

# Describe
aws cloudformation describe-stacks \
  --stack-name my-stack

# Stack events
aws cloudformation describe-stack-events \
  --stack-name my-stack

# Resources
aws cloudformation list-stack-resources \
  --stack-name my-stack

# Update
aws cloudformation update-stack \
  --stack-name my-stack \
  --template-body file://template.yaml

# Delete
aws cloudformation delete-stack \
  --stack-name my-stack
```

> **CloudFormation = AWS Infrastructure as Code.**

> **Template → Stack → AWS Resources**

> **For DevOps interviews, focus on Templates, Stacks, Parameters, Outputs, Intrinsic Functions, Change Sets, Drift Detection, Stack Events, and CloudFormation vs Terraform.**
