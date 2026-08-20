# 11. SNS & SQS

## 📌 Overview

**Amazon SNS (Simple Notification Service)** and **Amazon SQS (Simple Queue Service)** are messaging services used to build **decoupled, reliable, and scalable applications**.

* **SNS** → Pushes messages to multiple subscribers.
* **SQS** → Stores messages in a queue until a consumer processes them.

### Simple Architecture

```text
                    ┌──────────────┐
                    │   Producer   │
                    └──────┬───────┘
                           │
                           ▼
                    ┌──────────────┐
                    │     SNS      │
                    │    Topic     │
                    └──────┬───────┘
                           │
             ┌─────────────┼─────────────┐
             ▼             ▼             ▼
          Email          SQS          Lambda
                        Queue
                          │
                          ▼
                      Consumer
```

---

# 🔔 Amazon SNS

## What is SNS?

Amazon SNS is a **fully managed pub/sub messaging service**.

SNS allows one message to be delivered to **multiple subscribers**.

### Example

An application publishes:

```text
"Server CPU usage is above 90%"
```

SNS can send this notification to:

* Email
* SMS
* Lambda
* SQS
* HTTP/HTTPS endpoint
* Other AWS services

---

# 🏗️ SNS Components

### 1. Topic

A topic is a communication channel.

```text
Application
     │
     ▼
 SNS Topic
     │
 ┌───┼────┬──────┐
 ▼   ▼    ▼      ▼
Email SQS Lambda HTTP
```

### 2. Publisher

The publisher sends messages to an SNS topic.

Example:

```text
Application → SNS Topic
```

### 3. Subscriber

A subscriber receives messages published to the topic.

Examples:

```text
SNS → Email
SNS → SQS
SNS → Lambda
SNS → HTTP
```

---

# 🔄 SNS Message Flow

```text
Publisher
    │
    ▼
SNS Topic
    │
    ├──► Email
    │
    ├──► SQS Queue
    │
    ├──► Lambda
    │
    └──► HTTP Endpoint
```

---

# 📦 Amazon SQS

## What is SQS?

Amazon SQS is a **fully managed message queuing service**.

It allows applications to communicate asynchronously.

Instead of one application directly calling another application:

```text
Application A ─────► Application B
```

we can use SQS:

```text
Application A
      │
      ▼
   SQS Queue
      │
      ▼
Application B
```

This provides **decoupling** between applications.

---

# 🏗️ SQS Components

### Producer

The producer sends messages to the queue.

```text
Producer → SQS
```

### Queue

The queue temporarily stores messages.

```text
┌───────────────────────────┐
│          SQS Queue        │
│                           │
│ Message 1                 │
│ Message 2                 │
│ Message 3                 │
└───────────────────────────┘
```

### Consumer

The consumer retrieves and processes messages.

```text
SQS → Consumer
```

---

# 🔀 Types of SQS Queues

## 1. Standard Queue

Provides:

* Very high throughput
* At-least-once message delivery
* Best-effort ordering

Use when:

```text
High scalability
+
Ordering is not critical
```

Example:

```text
Web Application
      │
      ▼
 Standard SQS
      │
      ▼
 Multiple Workers
```

---

## 2. FIFO Queue

FIFO means:

**First In, First Out**

Provides:

* Message ordering
* Exactly-once processing support
* Lower throughput than Standard queues

Example:

```text
Message 1
   ↓
Message 2
   ↓
Message 3

Processing:

Message 1 → Message 2 → Message 3
```

Use FIFO when **message ordering is important**.

Examples:

* Banking transactions
* Order processing
* Payment processing

---

# ⏱️ Visibility Timeout

Visibility Timeout prevents multiple consumers from processing the same message simultaneously.

Example:

```text
SQS
 │
 ▼
Message A
 │
 ▼
Consumer 1 receives message
 │
 └── Message becomes invisible
```

If the consumer successfully processes the message:

```text
Delete Message
```

If processing fails and the visibility timeout expires:

```text
Message becomes visible again
```

### Example

```text
Visibility Timeout = 30 seconds

Consumer receives message
        │
        ▼
Message hidden for 30 sec
        │
   ┌────┴─────┐
   │          │
Success     Failure
   │          │
Delete       Message
message      visible again
```

---

# ☠️ Dead-Letter Queue (DLQ)

A Dead-Letter Queue stores messages that repeatedly fail processing.

Example:

```text
Main Queue
    │
    ▼
Consumer
    │
    │ Processing fails
    ▼
Retry
    │
    ▼
Retry
    │
    ▼
Retry limit reached
    │
    ▼
Dead-Letter Queue
```

DLQs are extremely useful for **troubleshooting failed messages**.

---

# ⏳ Message Retention

SQS stores messages for a configurable retention period.

Messages can remain in the queue even if the consumer is temporarily unavailable.

This improves reliability.

---

# 📊 SNS vs SQS

| Feature         | SNS                     | SQS                       |
| --------------- | ----------------------- | ------------------------- |
| Type            | Pub/Sub                 | Queue                     |
| Main purpose    | Notification            | Message storage           |
| Consumers       | Multiple subscribers    | Consumers pull messages   |
| Message storage | No long-term queue      | Yes                       |
| Ordering        | Not the primary purpose | FIFO supports ordering    |
| Retry handling  | Subscriber-dependent    | Built into queue behavior |
| DLQ             | Supported               | Commonly used             |
| Common use      | Notifications           | Decoupling applications   |

---

# 🔥 SNS + SQS Together

SNS and SQS are commonly used together.

Example:

```text
                  Application
                       │
                       ▼
                  SNS Topic
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
       SQS Queue    SQS Queue    Lambda
          │            │
          ▼            ▼
      Worker 1      Worker 2
```

### Why use both?

SNS provides **fan-out**.

SQS provides **reliable message buffering**.

Example:

```text
Order Created
     │
     ▼
 SNS Topic
     │
     ├────► SQS → Payment Service
     │
     ├────► SQS → Inventory Service
     │
     └────► SQS → Notification Service
```

One event can therefore reach multiple independent systems.

---

# 🛠️ AWS CLI Commands

## Create SNS Topic

```bash
aws sns create-topic --name my-topic
```

---

## List SNS Topics

```bash
aws sns list-topics
```

---

## Publish Message

```bash
aws sns publish \
  --topic-arn arn:aws:sns:ap-south-1:123456789012:my-topic \
  --message "Hello from SNS"
```

---

## Create SQS Queue

```bash
aws sqs create-queue --queue-name my-queue
```

---

## List Queues

```bash
aws sqs list-queues
```

---

## Send Message

```bash
aws sqs send-message \
  --queue-url <QUEUE_URL> \
  --message-body "Hello from SQS"
```

---

## Receive Message

```bash
aws sqs receive-message \
  --queue-url <QUEUE_URL>
```

---

## Delete Message

After successfully processing a message:

```bash
aws sqs delete-message \
  --queue-url <QUEUE_URL> \
  --receipt-handle <RECEIPT_HANDLE>
```

---

# 🔐 Security

SNS and SQS use **AWS IAM** for access control.

Important security practices:

* Follow least privilege
* Restrict who can publish
* Restrict who can consume
* Use encryption
* Use HTTPS endpoints
* Monitor API activity using CloudTrail
* Use resource-based policies where appropriate

---

# 📈 Monitoring

Use **Amazon CloudWatch** to monitor SNS and SQS.

Important SQS metrics include:

* `ApproximateNumberOfMessagesVisible`
* `ApproximateNumberOfMessagesNotVisible`
* `NumberOfMessagesSent`
* `NumberOfMessagesReceived`
* `NumberOfMessagesDeleted`

Important SNS metrics include:

* Number of messages published
* Number of messages delivered
* Number of failed deliveries

---

# 💼 DevOps Real-World Example

Suppose an e-commerce application receives an order.

```text
User
 │
 ▼
Web Application
 │
 ▼
SNS Topic
 │
 ├─────────────► SQS Payment Queue
 │                    │
 │                    ▼
 │              Payment Service
 │
 ├─────────────► SQS Inventory Queue
 │                    │
 │                    ▼
 │              Inventory Service
 │
 └─────────────► SQS Notification Queue
                      │
                      ▼
                Notification Service
```

Benefits:

* Services are decoupled
* Applications can scale independently
* Messages aren't immediately lost if a consumer is unavailable
* Failed messages can be moved to a DLQ
* SNS provides fan-out

---

# 🎯 Interview Questions

### 1. What is SNS?

SNS is a managed **pub/sub messaging service** used to send notifications or messages to multiple subscribers.

### 2. What is SQS?

SQS is a managed **message queuing service** used to decouple applications and reliably store messages until they are processed.

### 3. Difference between SNS and SQS?

```text
SNS → Push
SQS → Queue/Pull
```

SNS distributes messages to subscribers, while SQS stores messages for consumers.

### 4. What is an SQS visibility timeout?

It is the period during which a received message remains invisible to other consumers while the current consumer processes it.

### 5. What happens if a consumer fails?

After the visibility timeout expires, the message can become visible again for another processing attempt.

### 6. What is a Dead-Letter Queue?

A DLQ stores messages that cannot be successfully processed after the configured number of receive attempts.

### 7. Standard vs FIFO SQS?

```text
Standard → High throughput, best-effort ordering
FIFO     → Ordered processing, exactly-once processing support
```

### 8. Can SNS send messages to SQS?

**Yes.**

SNS can publish messages to one or more SQS queues.

### 9. Why use SNS and SQS together?

SNS provides **fan-out**, while SQS provides **durable buffering and decoupling**.

### 10. Which service should you use for application notifications?

Usually **SNS**.

For example:

```text
CloudWatch Alarm
      │
      ▼
     SNS
      │
      ├──► Email
      ├──► SMS
      └──► Lambda
```

---

# 🧠 Quick Revision

```text
SNS
│
├── Pub/Sub
├── Push model
├── Topic
├── Multiple subscribers
└── Fan-out

SQS
│
├── Message Queue
├── Pull model
├── Standard
├── FIFO
├── Visibility Timeout
└── Dead-Letter Queue

SNS + SQS
│
└── Fan-out + Reliable Buffering
```

## ⭐ Remember

> **SNS = Send/Notify**

> **SQS = Store/Queue**

> **SNS + SQS = Decouple + Scale + Reliable Messaging**
