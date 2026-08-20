# AWS Lambda

## 📌 What is AWS Lambda?

**AWS Lambda** is a serverless compute service that lets you run code without provisioning or managing servers.

You upload your code, configure the required permissions and triggers, and Lambda runs your function when an event occurs. AWS manages the underlying infrastructure and automatically scales the function based on incoming requests.

### Simple Example

```text
User
  ↓
API Gateway
  ↓
AWS Lambda
  ↓
DynamoDB
```

A user sends an API request → API Gateway invokes Lambda → Lambda processes the request → Lambda stores or retrieves data from DynamoDB.

---

# 1. Why Lambda?

With traditional servers:

```text
Launch EC2
   ↓
Install OS
   ↓
Install Runtime
   ↓
Deploy Application
   ↓
Configure Scaling
   ↓
Monitor Server
   ↓
Patch Server
```

With Lambda:

```text
Write Code
   ↓
Upload Function
   ↓
Configure Trigger
   ↓
Lambda Runs Code
```

AWS manages the servers, operating system, infrastructure and scaling.

---

# 2. Key Features

* Serverless execution
* Automatic scaling
* Event-driven execution
* Pay-per-use pricing
* Multiple runtime options
* Integration with many AWS services
* IAM-based security
* CloudWatch logging and monitoring
* Supports ZIP deployment packages and container images

---

# 3. Lambda Architecture

```text
                    ┌─────────────────┐
                    │     Event       │
                    │   / Trigger     │
                    └────────┬────────┘
                             │
                             ▼
                    ┌─────────────────┐
                    │  AWS Lambda     │
                    │                 │
                    │  Function Code  │
                    └────────┬────────┘
                             │
              ┌──────────────┼──────────────┐
              ▼              ▼              ▼
          ┌────────┐     ┌────────┐     ┌──────────┐
          │  S3    │     │ DynamoDB│    │   SQS    │
          └────────┘     └────────┘     └──────────┘
                             │
                             ▼
                       CloudWatch
                          Logs
```

---

# 4. Important Lambda Components

## Function

The Lambda function contains your application code.

Example:

```python
def lambda_handler(event, context):
    return {
        "statusCode": 200,
        "body": "Hello from Lambda"
    }
```

---

## Handler

The **handler** is the entry point of your Lambda function.

For Python:

```text
lambda_function.lambda_handler
```

Where:

```text
lambda_function → Python file
lambda_handler  → Function name
```

Example:

```python
def lambda_handler(event, context):
    print("Lambda executed")
```

Lambda invokes the handler when the function runs.

---

# 5. Event

An event contains information that triggers the Lambda function.

Example:

```json
{
  "name": "Rushikesh",
  "action": "login"
}
```

The Lambda function can process this event.

```python
def lambda_handler(event, context):

    name = event["name"]

    return {
        "statusCode": 200,
        "body": f"Hello {name}"
    }
```

---

# 6. Context

The `context` object provides information about the current Lambda invocation.

Example:

```python
def lambda_handler(event, context):

    print(context.function_name)
    print(context.aws_request_id)

    return "Success"
```

Common information includes:

```text
Function name
Function version
Request ID
Remaining execution time
Memory limit
```

---

# 7. Lambda Runtimes

Lambda supports several programming languages through managed runtimes.

Common choices include:

```text
Python
Node.js
Java
Go
.NET
Ruby
```

You can also deploy Lambda functions using container images.

---

# 8. Lambda Invocation Types

There are two important invocation models.

## Synchronous

The caller waits for the Lambda response.

```text
Client
  ↓
Lambda
  ↓
Response
```

Examples:

```text
API Gateway
AWS SDK
CLI
Application
```

---

## Asynchronous

The caller sends the event and doesn't wait for the function's response.

```text
Event Source
     ↓
Lambda
     ↓
Function executes
```

Common examples include event-driven workloads from AWS services.

---

# 9. Lambda Triggers

A trigger is an event source that invokes a Lambda function.

Common triggers:

```text
API Gateway
S3
EventBridge
SNS
SQS
CloudWatch Events
DynamoDB Streams
Kinesis
ALB
```

Example:

```text
S3 Bucket
   ↓
Object Uploaded
   ↓
Lambda Trigger
   ↓
Lambda Function
```

Lambda can have multiple triggers. Queue and stream services such as SQS and Kinesis use event source mappings to poll the source and invoke Lambda with batches of records.

---

# 10. Lambda + S3

A common DevOps/serverless use case:

```text
User uploads image
       ↓
      S3
       ↓
    Lambda
       ↓
Process Image
```

Example use cases:

* Resize images
* Generate thumbnails
* Process uploaded files
* Validate files
* Move objects between buckets

---

# 11. Lambda + API Gateway

A very common serverless architecture:

```text
Client
  ↓
API Gateway
  ↓
Lambda
  ↓
DynamoDB
```

Example:

```text
POST /users
      ↓
API Gateway
      ↓
Lambda
      ↓
DynamoDB
```

This architecture can be used to build REST APIs without managing EC2 servers.

---

# 12. Lambda + DynamoDB

Example:

```text
API Gateway
     ↓
Lambda
     ↓
DynamoDB
```

Lambda can:

```text
Create item
Read item
Update item
Delete item
```

The Lambda execution role must contain the required DynamoDB permissions.

---

# 13. Lambda + SQS

Lambda can process messages from an SQS queue.

```text
Application
    ↓
   SQS
    ↓
 Lambda
    ↓
Process Message
```

Example:

```text
Order Application
       ↓
     SQS Queue
       ↓
     Lambda
       ↓
Process Order
```

SQS integration uses an event source mapping, which polls the queue and sends batches of messages to Lambda.

---

# 14. Lambda Execution Role

Every Lambda function requires an **execution role**.

The execution role is an IAM role that Lambda assumes when executing the function.

Example:

```text
Lambda
   ↓
Assumes IAM Execution Role
   ↓
AWS Resources
```

The role determines what AWS resources the Lambda function can access.

---

# 15. Example IAM Permission

Suppose Lambda needs to read from S3.

The execution role might contain:

```json
{
  "Effect": "Allow",
  "Action": [
    "s3:GetObject"
  ],
  "Resource": "arn:aws:s3:::my-bucket/*"
}
```

For DynamoDB:

```json
{
  "Effect": "Allow",
  "Action": [
    "dynamodb:GetItem",
    "dynamodb:PutItem"
  ],
  "Resource": "arn:aws:dynamodb:ap-south-1:123456789012:table/users"
}
```

Follow the **principle of least privilege**: give Lambda only the permissions it actually needs.

---

# 16. Lambda Permissions vs Execution Role

This is a very common interview question.

### Execution Role

Controls:

> What can my Lambda function access?

Example:

```text
Lambda → S3
Lambda → DynamoDB
Lambda → SQS
```

These permissions are defined in the Lambda execution role.

### Resource-Based Policy

Controls:

> Who can invoke my Lambda function?

Example:

```text
API Gateway → Lambda
S3 → Lambda
SNS → Lambda
```

Lambda supports resource-based policies for allowing AWS services or other accounts to invoke a function.

---

# 17. Lambda Environment Variables

Environment variables allow you to store configuration values outside your code.

Example:

```text
DB_HOST=database.example.com
DB_NAME=users
ENVIRONMENT=production
```

Python:

```python
import os

environment = os.environ["ENVIRONMENT"]

print(environment)
```

Do not store secrets directly in environment variables when a dedicated secret-management solution is more appropriate.

---

# 18. Lambda Layers

A **Lambda Layer** contains reusable code or dependencies.

Example:

```text
Lambda Function
      +
Lambda Layer
      ↓
Shared Libraries
```

Useful for:

* Common libraries
* Shared utilities
* Dependencies
* Custom runtimes

Example:

```text
Function A ──┐
Function B ──┼── Lambda Layer
Function C ──┘
```

---

# 19. Lambda Versions

Lambda allows you to publish versions of a function.

Example:

```text
$LATEST
   ↓
Version 1
Version 2
Version 3
```

`$LATEST` represents the latest unpublished version.

Published versions provide an immutable version of the function code and configuration.

---

# 20. Lambda Aliases

An alias is a pointer to a specific Lambda version.

Example:

```text
production → Version 10

staging → Version 11
```

This is useful for deployment strategies such as:

```text
Blue/Green Deployment
Canary Deployment
```

Example:

```text
Production
    ↓
Alias
    ↓
Version 10
```

---

# 21. Lambda Concurrency

Concurrency is the number of Lambda executions running at the same time.

Example:

```text
100 requests
     ↓
100 concurrent executions
```

Important concepts:

```text
Reserved Concurrency
Provisioned Concurrency
Unreserved Concurrency
```

### Reserved Concurrency

Reserves concurrency for a function and can also limit its maximum concurrent executions.

### Provisioned Concurrency

Keeps execution environments initialized to help reduce cold-start latency.

---

# 22. Lambda Cold Start

A **cold start** occurs when Lambda needs to initialize a new execution environment before running your function.

```text
Request
   ↓
Create Environment
   ↓
Initialize Runtime
   ↓
Load Code
   ↓
Execute Function
```

A warm invocation can reuse an existing execution environment.

```text
Request
   ↓
Existing Environment
   ↓
Execute Function
```

Lambda manages execution environments and may reuse an existing environment for later invocations.

---

# 23. Lambda Timeout

Timeout defines the maximum amount of time a function can execute.

Example:

```text
Timeout = 30 seconds
```

If the function doesn't finish within the configured timeout, Lambda terminates the invocation.

For regular Lambda function invocations, AWS currently documents a maximum invocation duration of 15 minutes.

---

# 24. Lambda Memory

You configure memory for a Lambda function.

Example:

```text
128 MB
256 MB
512 MB
1024 MB
2048 MB
```

Increasing memory also changes the compute resources available to the function.

---

# 25. Lambda Logging

Lambda integrates with **Amazon CloudWatch Logs**.

Example:

```python
def lambda_handler(event, context):

    print("Function started")

    return "Success"
```

The output can be viewed in CloudWatch Logs.

Architecture:

```text
Lambda
   ↓
CloudWatch Logs
   ↓
Log Group
   ↓
Log Stream
```

The basic Lambda execution role normally includes permissions to write logs to CloudWatch Logs.

---

# 26. Lambda Monitoring

Use CloudWatch to monitor:

```text
Invocations
Errors
Duration
Throttles
ConcurrentExecutions
```

For troubleshooting:

```text
Lambda
  ↓
CloudWatch Metrics
  ↓
CloudWatch Logs
```

---

# 27. Lambda Deployment Package

Lambda code can be deployed using:

### ZIP package

```text
function.zip
 ├── lambda_function.py
 ├── dependencies
 └── configuration
```

### Container image

```text
Dockerfile
     ↓
Docker Image
     ↓
Lambda
```

Lambda supports both ZIP archives and container images as deployment package types.

---

# 28. Lambda with Docker

Example Dockerfile:

```dockerfile
FROM public.ecr.aws/lambda/python:3.12

COPY app.py ${LAMBDA_TASK_ROOT}

CMD ["app.lambda_handler"]
```

Build:

```bash
docker build -t lambda-app .
```

Tag:

```bash
docker tag lambda-app:latest <account-id>.dkr.ecr.ap-south-1.amazonaws.com/lambda-app:latest
```

Push:

```bash
docker push <account-id>.dkr.ecr.ap-south-1.amazonaws.com/lambda-app:latest
```

Then deploy the container image to Lambda.

---

# 29. Lambda CLI Commands

List functions:

```bash
aws lambda list-functions
```

Create function:

```bash
aws lambda create-function \
  --function-name my-function \
  --runtime python3.12 \
  --role arn:aws:iam::<account-id>:role/lambda-role \
  --handler lambda_function.lambda_handler \
  --zip-file fileb://function.zip
```

Invoke function:

```bash
aws lambda invoke \
  --function-name my-function \
  response.json
```

Read response:

```bash
cat response.json
```

Update function code:

```bash
aws lambda update-function-code \
  --function-name my-function \
  --zip-file fileb://function.zip
```

Delete function:

```bash
aws lambda delete-function \
  --function-name my-function
```

---

# 30. Simple Lambda Example

## Python

```python
def lambda_handler(event, context):

    name = event.get("name", "World")

    return {
        "statusCode": 200,
        "body": f"Hello {name}"
    }
```

Test event:

```json
{
  "name": "Rushikesh"
}
```

Expected response:

```json
{
  "statusCode": 200,
  "body": "Hello Rushikesh"
}
```

---

# 31. Lambda + API Gateway Project

A good beginner project:

```text
                  ┌──────────────┐
                  │    Client    │
                  └──────┬───────┘
                         │
                         ▼
                  ┌──────────────┐
                  │ API Gateway  │
                  └──────┬───────┘
                         │
                         ▼
                  ┌──────────────┐
                  │    Lambda    │
                  └──────┬───────┘
                         │
                         ▼
                  ┌──────────────┐
                  │  DynamoDB    │
                  └──────────────┘
```

Project flow:

```text
POST /users
     ↓
API Gateway
     ↓
Lambda
     ↓
DynamoDB
     ↓
Response
```

This is an excellent project for understanding serverless architecture.

---

# 32. Lambda + S3 Project

```text
User
 ↓
Upload File
 ↓
S3 Bucket
 ↓
Lambda Trigger
 ↓
Lambda Function
 ↓
Process File
 ↓
Store Result
```

Example:

```text
Upload image
     ↓
S3
     ↓
Lambda
     ↓
Resize image
     ↓
S3/output/
```

---

# 33. Lambda + SQS Project

```text
Application
    ↓
SQS Queue
    ↓
Lambda
    ↓
Process Message
    ↓
Database
```

Useful for asynchronous processing.

Example:

```text
Order Service
     ↓
SQS
     ↓
Lambda
     ↓
Payment Processing
```

---

# 34. Lambda Best Practices

### 1. Keep functions small

Use one function for one logical responsibility.

### 2. Follow least privilege

Only grant required IAM permissions.

### 3. Reuse connections

Initialize reusable clients outside the handler when appropriate.

```python
import boto3

s3 = boto3.client("s3")

def lambda_handler(event, context):
    ...
```

### 4. Use environment variables

Keep configuration outside application code.

### 5. Monitor with CloudWatch

Always monitor:

```text
Errors
Duration
Throttles
Invocations
```

### 6. Avoid unnecessary cold-start overhead

Keep deployment packages and initialization work efficient.

### 7. Use separate IAM roles where practical

Avoid giving multiple unrelated functions unnecessarily broad permissions.

---

# 35. Lambda Limitations to Remember

Important interview points:

```text
Maximum regular invocation duration → 15 minutes
```

Lambda is designed primarily for event-driven workloads rather than traditional always-running server processes.

For long-running workflows, consider services such as:

```text
Step Functions
ECS
EKS
EC2
```

---

# 36. Lambda vs EC2

| Feature                   | Lambda                 | EC2                      |
| ------------------------- | ---------------------- | ------------------------ |
| Server Management         | AWS manages            | You manage               |
| Scaling                   | Automatic              | Configure/manage         |
| Billing                   | Pay for execution      | Pay for instance usage   |
| Infrastructure            | Serverless             | Virtual server           |
| Startup                   | Can have cold starts   | Instance remains running |
| Maximum regular execution | 15 minutes             | No Lambda-style limit    |
| OS Management             | AWS                    | User                     |
| Best For                  | Event-driven workloads | Long-running workloads   |

---

# 37. Lambda vs ECS

| Feature           | Lambda                 | ECS                        |
| ----------------- | ---------------------- | -------------------------- |
| Infrastructure    | Serverless             | Container platform         |
| Deployment        | ZIP/Container          | Container                  |
| Scaling           | Automatic              | Configure service scaling  |
| Long-running app  | Not ideal              | Excellent                  |
| Server Management | Minimal                | Depends on launch type     |
| Best Use          | Event-driven functions | Containerized applications |

---

# 38. Common Interview Questions

### Q1. What is AWS Lambda?

AWS Lambda is a serverless compute service that runs code in response to events without requiring you to manage servers.

### Q2. What is a Lambda trigger?

A trigger is an event source that invokes a Lambda function.

Examples:

```text
S3
API Gateway
SQS
SNS
EventBridge
DynamoDB Streams
```

### Q3. What is a Lambda execution role?

It is an IAM role that Lambda assumes when executing a function. It defines what AWS resources the function can access.

### Q4. What is a Lambda handler?

The handler is the entry point of the Lambda function.

Example:

```python
def lambda_handler(event, context):
```

### Q5. What is a cold start?

A cold start happens when Lambda creates and initializes a new execution environment before executing a function.

### Q6. What is Lambda concurrency?

Concurrency represents the number of Lambda function invocations executing simultaneously.

### Q7. What is the difference between execution role and resource-based policy?

```text
Execution Role
→ What Lambda can access

Resource-Based Policy
→ Who can invoke Lambda
```

### Q8. Can Lambda access a private RDS database?

Yes. Lambda can be configured to access resources in a VPC, provided networking, security groups, routing and permissions are configured correctly.

### Q9. Can Lambda run Docker containers?

Yes. Lambda supports container images as a deployment package type.

### Q10. How do you troubleshoot a failed Lambda?

Check:

```text
1. CloudWatch Logs
2. Lambda metrics
3. IAM execution role
4. Trigger configuration
5. Environment variables
6. Timeout
7. Memory
8. VPC networking
9. Security groups
10. Event payload
```

---

# 39. Important Commands Cheat Sheet

```bash
# List Lambda functions
aws lambda list-functions

# Invoke function
aws lambda invoke \
  --function-name my-function \
  response.json

# Update function code
aws lambda update-function-code \
  --function-name my-function \
  --zip-file fileb://function.zip

# Get function configuration
aws lambda get-function-configuration \
  --function-name my-function

# Publish version
aws lambda publish-version \
  --function-name my-function

# List versions
aws lambda list-versions-by-function \
  --function-name my-function

# Delete function
aws lambda delete-function \
  --function-name my-function
```

---

# 40. DevOps Perspective

As a DevOps engineer, focus on:

```text
Lambda
 ├── IAM
 ├── CloudWatch
 ├── API Gateway
 ├── S3
 ├── SQS
 ├── SNS
 ├── DynamoDB
 ├── EventBridge
 ├── Terraform
 ├── AWS CLI
 ├── Docker
 ├── CI/CD
 └── Monitoring
```

A practical CI/CD workflow:

```text
Developer
   ↓
GitHub
   ↓
Jenkins
   ↓
Build/Test
   ↓
Terraform / AWS CLI
   ↓
Lambda Deployment
   ↓
CloudWatch
   ↓
Monitoring
```

---

# 41. Terraform Lambda Example

```hcl
resource "aws_lambda_function" "example" {
  function_name = "hello-lambda"

  filename         = "function.zip"
  source_code_hash = filebase64sha256("function.zip")

  role    = aws_iam_role.lambda_role.arn
  handler = "lambda_function.lambda_handler"
  runtime = "python3.12"
}
```

IAM role:

```hcl
resource "aws_iam_role" "lambda_role" {
  name = "lambda-execution-role"

  assume_role_policy = jsonencode({
    Version = "2012-10-17"

    Statement = [{
      Effect = "Allow"

      Principal = {
        Service = "lambda.amazonaws.com"
      }

      Action = "sts:AssumeRole"
    }]
  })
}
```

The Lambda service principal must be trusted by the execution role so Lambda can assume it.

---

# 42. Real-World Serverless Architecture

```text
                    Internet
                       │
                       ▼
                ┌──────────────┐
                │ API Gateway  │
                └──────┬───────┘
                       │
                       ▼
                ┌──────────────┐
                │    Lambda    │
                └──────┬───────┘
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
      ┌────────┐   ┌────────┐   ┌────────┐
      │DynamoDB│   │  S3    │   │  SQS   │
      └────────┘   └────────┘   └────────┘
                       │
                       ▼
                 ┌───────────┐
                 │CloudWatch │
                 └───────────┘
```

---

# 43. ⭐ Interview Quick Revision

Remember these points:

```text
Lambda = Serverless Compute

Function = Code that Lambda executes

Handler = Entry point

Event = Input to Lambda

Trigger = Invokes Lambda

Execution Role = Lambda → AWS resources

Resource Policy = Other services/accounts → Lambda

CloudWatch = Logs + Monitoring

Cold Start = New execution environment initialization

Concurrency = Simultaneous executions

Layer = Shared dependencies/code

Version = Published immutable function version

Alias = Pointer to Lambda version

S3 = Object events

SQS = Queue processing

API Gateway = HTTP/API frontend

DynamoDB = Serverless database
```

---

# 44. 🚀 Mini Project

## Serverless User Registration API

Build:

```text
GitHub
   ↓
Jenkins
   ↓
Terraform
   ↓
API Gateway
   ↓
Lambda
   ↓
DynamoDB
   ↓
CloudWatch
```

### Features

```text
POST /users
GET /users
GET /users/{id}
DELETE /users/{id}
```

### Technologies

```text
AWS Lambda
API Gateway
DynamoDB
IAM
CloudWatch
Terraform
GitHub
Jenkins
```

This project combines **AWS + Serverless + Terraform + CI/CD**, making it especially useful for DevOps interview preparation.

---

# 45. 🎯 What You Should Know for Interviews

Before moving to the next topic, make sure you can explain:

* What Lambda is
* Lambda architecture
* Handler
* Event
* Trigger
* Execution role
* Resource-based policy
* Lambda layers
* Environment variables
* Versions and aliases
* Cold starts
* Concurrency
* Timeout
* Memory
* CloudWatch logs
* Lambda + S3
* Lambda + API Gateway
* Lambda + DynamoDB
* Lambda + SQS
* Lambda + Docker
* Lambda with Terraform
* Lambda CI/CD
* Lambda troubleshooting

---

## 🔗 Official AWS Documentation

* [AWS Lambda — How Lambda works](https://docs.aws.amazon.com/lambda/latest/dg/concepts-basics.html?utm_source=chatgpt.com)
* [Lambda execution roles](https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html?utm_source=chatgpt.com)
* [Lambda permissions](https://docs.aws.amazon.com/lambda/latest/dg/lambda-permissions.html?utm_source=chatgpt.com)
* [AWS Lambda](https://aws.amazon.com/lambda/?utm_source=chatgpt.com)

---

