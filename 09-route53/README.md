# 🌐 09. Amazon Route 53

Amazon **Route 53** is AWS's highly available and scalable **DNS (Domain Name System)** service.

It translates domain names into IP addresses and can also perform:

* Domain registration
* DNS management
* Health checks
* Traffic routing
* Failover
* Load balancing

---

# 📌 What is DNS?

DNS converts a human-readable domain name into an IP address.

For example:

```text
User
  |
  | www.example.com
  ↓
DNS
  |
  | 203.0.113.10
  ↓
Web Server
```

Instead of remembering:

```text
203.0.113.10
```

users can access:

```text
www.example.com
```

---

# 🧠 What is Route 53?

Route 53 is AWS's managed DNS service.

Example:

```text
                    User
                      |
                      ↓
              www.example.com
                      |
                      ↓
                  Route 53
                      |
              +-------+-------+
              |               |
              ↓               ↓
             ALB             EC2
```

A very common AWS architecture is:

```text
User
 ↓
Route 53
 ↓
Application Load Balancer
 ↓
Target Group
 ↓
EC2 / ECS / EKS
```

---

# 🔥 Why is it called Route 53?

The name comes from:

```text
Route
+
53
```

Port **53** is the standard DNS port.

DNS primarily uses:

```text
UDP 53
```

and can also use:

```text
TCP 53
```

---

# 🧩 Route 53 Main Features

Route 53 provides three major capabilities:

```text
Route 53
│
├── Domain Registration
│
├── DNS Resolution
│
└── Health Checks
```

---

# 1. Domain Registration

You can register domains through Route 53.

Example:

```text
mycompany.com
myapp.in
example.net
```

Route 53 can manage the DNS records for registered domains.

---

# 2. DNS Management

Route 53 stores DNS records in a **Hosted Zone**.

Example:

```text
example.com
     |
     +── www
     +── api
     +── mail
```

---

# 3. Health Checks

Route 53 can monitor endpoints and use health information for DNS routing decisions.

Example:

```text
Route 53
   |
   +---- Server 1 → Healthy
   |
   +---- Server 2 → Unhealthy
```

Route 53 can route traffic according to the configured routing policy and health checks.

---

# 🏠 Hosted Zones

A **Hosted Zone** is a container for DNS records for a domain.

Example:

```text
example.com
     |
     +── A Record
     +── AAAA Record
     +── CNAME
     +── MX
     +── TXT
```

There are two types:

```text
Hosted Zone
│
├── Public Hosted Zone
│
└── Private Hosted Zone
```

---

# 🌍 Public Hosted Zone

Used for domains accessible from the internet.

Example:

```text
example.com
     |
     ↓
Public Hosted Zone
     |
     ↓
Internet
```

---

# 🔒 Private Hosted Zone

Used for DNS resolution inside one or more associated VPCs.

Example:

```text
VPC
 |
 +── Private Hosted Zone
        |
        +── db.internal
        +── api.internal
```

Private hosted zones are useful for internal applications and services.

---

# 📝 DNS Record Types

Route 53 supports many DNS record types.

The most important ones for DevOps interviews are:

```text
A
AAAA
CNAME
Alias
MX
TXT
NS
SOA
```

---

# 1. A Record

Maps a domain name to an IPv4 address.

Example:

```text
example.com
     |
     ↓
203.0.113.10
```

Configuration:

```text
Type: A
Name: example.com
Value: 203.0.113.10
```

---

# 2. AAAA Record

Maps a domain name to an IPv6 address.

Example:

```text
example.com
     |
     ↓
IPv6 Address
```

---

# 3. CNAME Record

Maps one domain name to another domain name.

Example:

```text
www.example.com
        |
        ↓
example.com
```

### Important

A CNAME points to another **domain name**, not directly to an IP address.

---

# 4. Alias Record

An AWS-specific feature that allows DNS routing to supported AWS resources.

Example:

```text
example.com
     |
     ↓
Route 53 Alias
     |
     ↓
ALB
```

Common targets include:

* Application Load Balancer
* Network Load Balancer
* CloudFront
* S3 website endpoints
* Other supported AWS resources

---

# ⭐ CNAME vs Alias

| Feature                 | CNAME                  | Alias               |
| ----------------------- | ---------------------- | ------------------- |
| AWS-specific            | ❌                      | ✅                   |
| Points to domain        | ✅                      | ✅                   |
| Points to AWS resources | Limited                | ✅                   |
| Root domain support     | ❌                      | ✅                   |
| Extra DNS query         | Can require resolution | AWS-native behavior |

### Interview Tip

For an AWS resource such as an ALB, an **Alias record** is commonly used.

---

# 5. MX Record

Used for email routing.

Example:

```text
example.com
     |
     ↓
MX Record
     |
     ↓
Mail Server
```

---

# 6. TXT Record

Stores text information.

Common uses:

* Domain verification
* SPF
* DKIM-related records
* Security policies

Example:

```text
example.com
     |
     ↓
TXT
     |
     ↓
"verification=value"
```

---

# 7. NS Record

**Name Server (NS)** records identify the authoritative DNS servers for a domain.

Example:

```text
example.com
     |
     ↓
NS Records
     |
     +── ns-123.awsdns...
     +── ns-456.awsdns...
```

---

# 8. SOA Record

**Start of Authority (SOA)** contains authoritative information about a DNS zone.

It includes information such as:

* Primary name server
* Administrative contact
* Serial number
* DNS timing parameters

---

# 🚦 Route 53 Routing Policies

Route 53 supports several routing policies.

Important ones:

```text
Simple
Weighted
Latency-based
Failover
Geolocation
Geoproximity
IP-based
Multivalue Answer
```

---

# 1. Simple Routing

Simple routing returns a single resource or set of values.

Example:

```text
example.com
     |
     ↓
Web Server
```

Useful for basic DNS configurations.

---

# 2. Weighted Routing

Weighted routing distributes traffic according to assigned weights.

Example:

```text
Server A → Weight 80
Server B → Weight 20
```

Traffic is approximately:

```text
80% → Server A
20% → Server B
```

Useful for:

* Blue/green deployments
* Testing
* Gradual migrations

---

# 3. Latency-Based Routing

Routes users to the AWS region that provides the lowest network latency according to Route 53's measurements.

Example:

```text
India User
    |
    ↓
Route 53
    |
    ↓
Mumbai Region

US User
    |
    ↓
Route 53
    |
    ↓
US Region
```

Useful for improving user experience across geographic regions.

---

# 4. Failover Routing

Used for primary/secondary architecture.

Example:

```text
                 Route 53
                    |
              +-----+-----+
              |           |
          Primary      Secondary
           ALB            ALB
              |
           Healthy
```

If the primary becomes unhealthy:

```text
                 Route 53
                    |
                    ↓
                Secondary
                    |
                    ↓
                   ALB
```

---

# 5. Geolocation Routing

Routes users based on their geographic location.

Example:

```text
India Users
    ↓
India Server

US Users
    ↓
US Server

Europe Users
    ↓
Europe Server
```

---

# 6. Geoproximity Routing

Routes traffic based on the geographic location of users and resources.

It can also use **bias** to shift more traffic toward or away from a location.

---

# 7. IP-Based Routing

Routes traffic based on the client's IP address and configured CIDR ranges.

Useful when you have specific network-based routing requirements.

---

# 8. Multivalue Answer Routing

Returns multiple healthy resource values.

Example:

```text
Route 53
   |
   +── Server A
   +── Server B
   +── Server C
```

It can improve availability, but it is **not a replacement for a dedicated load balancer**.

---

# 🎯 Routing Policy Comparison

| Policy       | Main Use                 |
| ------------ | ------------------------ |
| Simple       | Basic DNS                |
| Weighted     | Traffic percentage       |
| Latency      | Lowest latency           |
| Failover     | Primary/secondary        |
| Geolocation  | User location            |
| Geoproximity | Geographic distance      |
| IP-based     | Client IP ranges         |
| Multivalue   | Multiple healthy answers |

---

# 🔄 Route 53 + ALB

One of the most common DevOps architectures:

```text
                    User
                     |
                     ↓
              example.com
                     |
                     ↓
                  Route 53
                     |
                     ↓
                    ALB
                     |
                Target Group
                     |
             +-------+-------+
             |       |       |
            EC2     EC2     EC2
```

---

# 🔐 Route 53 + HTTPS

A typical production setup:

```text
User
 |
 | HTTPS
 ↓
Route 53
 |
 ↓
ALB
 |
 ↓
EC2
```

Route 53 handles DNS.

The ALB can handle TLS termination using an ACM certificate.

---

# 🌱 Terraform Example

## Route 53 Hosted Zone

```hcl
resource "aws_route53_zone" "main" {
  name = "example.com"
}
```

---

## A Record

```hcl
resource "aws_route53_record" "www" {
  zone_id = aws_route53_zone.main.zone_id

  name = "www.example.com"
  type = "A"

  ttl = 300

  records = [
    "203.0.113.10"
  ]
}
```

---

# ⭐ Route 53 Alias to ALB

For an ALB, an Alias record is commonly preferred.

```hcl
resource "aws_route53_record" "app" {
  zone_id = aws_route53_zone.main.zone_id

  name = "example.com"
  type = "A"

  alias {
    name                   = aws_lb.app.dns_name
    zone_id                = aws_lb.app.zone_id
    evaluate_target_health = true
  }
}
```

Architecture:

```text
example.com
     |
     ↓
Route 53
     |
   Alias
     |
     ↓
ALB
```

---

# 🛠️ AWS CLI Commands

## List Hosted Zones

```bash
aws route53 list-hosted-zones
```

---

## List DNS Records

```bash
aws route53 list-resource-record-sets \
  --hosted-zone-id <HOSTED-ZONE-ID>
```

---

## List Health Checks

```bash
aws route53 list-health-checks
```

---

## Get Hosted Zone

```bash
aws route53 get-hosted-zone \
  --id <HOSTED-ZONE-ID>
```

---

# 🧪 DNS Troubleshooting

If your domain isn't working, check the following.

```text
1. Domain registration
2. Name servers
3. Hosted Zone
4. DNS records
5. Record type
6. Record value
7. TTL
8. Load Balancer
9. Security Groups
10. Application health
```

---

# 🔥 Common Problem: Domain Not Resolving

Check:

```bash
nslookup example.com
```

or:

```bash
dig example.com
```

Check whether the response contains the expected record.

Example:

```text
example.com
     |
     ↓
Route 53
     |
     ↓
ALB DNS Name
```

---

# 🔥 Common Problem: Route 53 Works but Website Doesn't

If DNS resolves correctly but the website doesn't open:

```text
DNS
 ↓
Working
 ↓
ALB
 ↓
Check Listener
 ↓
Target Group
 ↓
Check Target Health
 ↓
EC2
 ↓
Check Application
```

Remember:

> Route 53 resolves the domain. It does not automatically make the application itself healthy.

---

# 🧠 TTL

**TTL (Time To Live)** controls how long DNS resolvers can cache a DNS response.

Example:

```text
TTL = 300 seconds
```

This means the response can be cached for approximately:

```text
5 minutes
```

Lower TTL:

```text
Faster DNS changes
More DNS queries
```

Higher TTL:

```text
Longer caching
Fewer DNS queries
```

---

# 🌍 Domain Resolution Flow

When a user enters:

```text
https://www.example.com
```

The simplified flow is:

```text
Browser
   |
   ↓
DNS Resolver
   |
   ↓
Route 53
   |
   ↓
DNS Record
   |
   ↓
ALB IP/Endpoint
   |
   ↓
Application
```

---

# 🏢 Production Architecture

A common AWS production setup:

```text
                         Users
                           |
                           ↓
                        Route 53
                           |
                    example.com
                           |
                           ↓
                  Application Load Balancer
                           |
                     Target Group
                           |
             +-------------+-------------+
             |             |             |
            EC2           EC2           EC2
             |             |             |
             +-------------+-------------+
                           |
                          RDS
```

With Auto Scaling:

```text
                         Route 53
                            |
                            ↓
                           ALB
                            |
                      Target Group
                            |
               +------------+------------+
               |            |            |
              EC2          EC2          EC2
               \            |            /
                +-----------+-----------+
                            |
                           ASG
                            |
                       CloudWatch
```

---

# 🎯 Interview Questions

### Q1. What is Route 53?

Route 53 is AWS's managed DNS service that provides DNS resolution, domain registration, health checks, and traffic routing.

### Q2. What is a Hosted Zone?

A hosted zone is a container for DNS records for a domain.

### Q3. What is an A record?

An A record maps a domain name to an IPv4 address.

### Q4. What is an AAAA record?

An AAAA record maps a domain name to an IPv6 address.

### Q5. What is a CNAME?

A CNAME maps one domain name to another domain name.

### Q6. What is an Alias record?

An Alias record is an AWS DNS feature that can route traffic to supported AWS resources such as an ALB.

### Q7. CNAME vs Alias?

CNAME points to another DNS name, while Alias can point to supported AWS resources and can be used at the zone apex.

### Q8. What is a Public Hosted Zone?

It provides DNS records that can be resolved publicly on the internet.

### Q9. What is a Private Hosted Zone?

It provides DNS resolution within associated VPCs.

### Q10. What is Weighted Routing?

It distributes DNS traffic according to configured weights.

### Q11. What is Latency-Based Routing?

It routes users toward the AWS region expected to provide the lowest latency.

### Q12. What is Failover Routing?

It routes traffic between primary and secondary resources based on health and configured failover settings.

### Q13. What is TTL?

TTL specifies how long DNS information can be cached.

### Q14. Does Route 53 act as a Load Balancer?

Route 53 provides DNS-based traffic routing, but it is not a replacement for an application/network load balancer.

### Q15. How would you connect Route 53 to an ALB?

Create an appropriate Route 53 record, commonly an **A/AAAA Alias** pointing to the ALB.

---

# 🧩 Scenario-Based Interview Question

### Scenario

You have deployed an application on:

```text
EC2
 +
Application Load Balancer
 +
Auto Scaling
```

Users currently access it using the ALB DNS name:

```text
my-alb-123.ap-south-1.elb.amazonaws.com
```

You want users to access:

```text
www.mycompany.com
```

### Solution

Use Route 53:

```text
User
  |
  ↓
www.mycompany.com
  |
  ↓
Route 53
  |
  ↓
Alias Record
  |
  ↓
Application Load Balancer
  |
  ↓
Target Group
  |
  ↓
EC2
```

---

# 🧠 Quick Revision

```text
Route 53
│
├── DNS
│
├── Domain Registration
│
├── Hosted Zones
│   ├── Public
│   └── Private
│
├── DNS Records
│   ├── A
│   ├── AAAA
│   ├── CNAME
│   ├── Alias
│   ├── MX
│   ├── TXT
│   ├── NS
│   └── SOA
│
├── Health Checks
│
└── Routing Policies
    ├── Simple
    ├── Weighted
    ├── Latency
    ├── Failover
    ├── Geolocation
    ├── Geoproximity
    ├── IP-based
    └── Multivalue
```

# ⭐ Interview Shortcut

```text
Route 53 = DNS

A Record = IPv4

AAAA = IPv6

CNAME = Domain → Domain

Alias = Domain → AWS Resource

Hosted Zone = DNS Records Container

Public Hosted Zone = Internet DNS

Private Hosted Zone = VPC DNS

Weighted = Percentage

Latency = Lowest Latency

Failover = Primary / Secondary

Geolocation = User Location

TTL = DNS Cache Duration
```

## 🔥 Real-World DevOps Flow

```text
GitHub
   ↓
Jenkins
   ↓
Docker
   ↓
EC2 / ECS / EKS
   ↓
Load Balancer
   ↓
Route 53
   ↓
www.example.com
   ↓
Users
```

