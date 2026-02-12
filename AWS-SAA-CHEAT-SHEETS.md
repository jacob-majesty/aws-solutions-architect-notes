# EXAM DAY MEMORY TRICKS & MENTAL SHORTCUTS
## Stop Memorizing. Start Recognizing Patterns.

You don't need to memorize everything. AWS asks the SAME questions in different clothes. Here are the **mental hooks** to hang your knowledge on.

---

## 🎯 THE GOLDEN TRIANGLE
**Every AWS question tests ONE of these three conflicts:**

1. **Security vs Convenience** → Choose Security
2. **Cost vs Performance** → Read the question: "cost-optimized" or "high-performance"?
3. **Managed vs Custom** → Choose Managed (AWS does it for you)

---

## 🧠 DOMAIN 1: SECURITY - QUICK HITS

### The "Who Can Do What" Hierarchy
```
SCP → DENY (Organization)  ← WINS OVER EVERYTHING
↓
Permission Boundary → CAN'T EXCEED
↓
Identity Policy → ALLOW/DENY (IAM Users/Roles)
↓
Resource Policy → ALLOW/DENY (Bucket, Role trust)
```

**Memory Trick**: **S**anta **P**lays **B**etter **I**n **R**everse  
(SCP → Permission → Identity → Resource)

---

### Encryption - 3 Second Decision Tree

```
Q: Who manages keys?
├── AWS → SSE-S3 (Default, can't audit per object)
├── Customer → SSE-KMS (Audit, rotate, cross-account)
└── You bring keys → SSE-C (Compliance, you manage)

Q: Data location?
├── In transit → TLS/SSL (ACM, ELB listener)
├── At rest → KMS (S3, EBS, RDS, etc.)
└── In use → Nitro Enclaves (rarely tested)
```

**Memory Trick**: **3 S's** - S3 uses SSE, SQS uses HTTPS, Secrets Manager uses KMS

---

### VPC Endpoints - WHEN to use WHAT

| You need... | Gateway Endpoint | Interface Endpoint |
|-------------|------------------|---------------------|
| S3 or DynamoDB | ✅ FREE | ❌ Don't (pay hourly) |
| Everything else (EC2, API, etc.) | ❌ | ✅ Private IP, hourly cost |

**Memory Trick**: **S3 = FREEway** (Gateway is free like a highway)  
**Others = TAXI** (Interface endpoint costs per hour like a cab)

---

### The "I AM" Rule for IAM

**I** = Identity (WHO - User, Role, Group)  
**A** = Action (WHAT - s3:GetObject)  
**M** = Resource (WHERE - arn:aws:s3:::bucket/*)  

**If a question mentions "least privilege" and ANY of these 3 is missing → WRONG**

---

### Security Gotchas in 10 Words

| Scenario | Keyword | Answer |
|----------|---------|--------|
| EC2 needs AWS access | No keys in code | IAM Role |
| Cross-account access | No passwords | IAM Role (STS AssumeRole) |
| Temporary URL | No AWS account | Pre-signed URL |
| Mobile users | Millions, no AWS account | Cognito Identity Pools |
| Database credentials | Rotate automatically | Secrets Manager |
| Configuration | Not secrets, no rotation | Parameter Store |
| PII in S3 | Find sensitive data | Macie |
| Threats | Unusual behavior | GuardDuty |
| Vulnerabilities | EC2, ECR | Inspector |
| S3 private + CloudFront | Only CloudFront can read | OAC/OAI |
| S3 public website | Custom domain + HTTPS | CloudFront + ACM (us-east-1) |

---

## 🧠 DOMAIN 2: RESILIENT - PATTERN RECOGNITION

### The AZ vs Region Decision Tree

```
Problem:
├── Instance fails → Auto Recovery (same AZ)
├── AZ fails → Multi-AZ (RDS, EC2 ASG, ELB)
├── Region fails → Multi-Region (Global tables, CRR, Aurora Global)
└── Application fails → Health checks + Auto Scaling
```

**Memory Trick**: **A-Z = A**Z, **R-EGION = R**emote

---

### RDS - ONE SIMPLE RULE

| Need this? | Use this | Gotcha |
|------------|----------|--------|
| High Availability | Multi-AZ | Standby, no read scaling |
| Read scaling | Read Replicas | Async, can be cross-region |
| Both HA + read scaling | Multi-AZ + Read Replicas | More expensive |
| Disaster Recovery | Cross-region Read Replica | Manual promote |
| Managed DR, low RPO | Aurora Global Database | Auto failover |

**Memory Trick**: **HA = 2 copies, RR = 2+ copies, Global = 2+ regions**

---

### Auto Scaling - 3 Questions

1. **When?** Predictable → Scheduled | Unpredictable → Dynamic
2. **What metric?** CPU, Network, RequestCountPerTarget, SQS queue depth
3. **How much?** Target tracking = "Keep CPU at 50%" | Step scaling = "Add 2 at 80%, 4 at 90%"

**Memory Trick**: **TARGET** = Set it and forget it | **STEP** = If this, then that

---

### Disaster Recovery - SPEED = MONEY

| Strategy | RTO | RPO | Cost | Setup |
|----------|-----|-----|------|-------|
| Backup & Restore | Hours | Hours | $ | S3 backups |
| Pilot Light | < 1 hour | Minutes | $$ | Core services running |
| Warm Standby | Minutes | Seconds | $$$ | Scaled-down prod |
| Multi-site | Real-time | Real-time | $$$$ | Full prod both regions |

**Memory Trick**: **B**ackup **P**ilot **W**arm **M**ulti = **Big Pizza Warms Me**

---

### SQS, SNS, EventBridge - WHO TALKS TO WHOM

| Service | Style | Use Case |
|---------|-------|----------|
| SQS | 1:1, pull | Decouple, buffer, DLQ |
| SNS | 1:many, push | Fanout, notifications |
| EventBridge | Many:many, rules | Event-driven, scheduled |

**Memory Trick**:  
**Q** = Queue (One drink, one straw)  
**N** = Notification (One announcement, many listeners)  
**E** = Event (Anything can happen, rules decide who cares)

---

## 🧠 DOMAIN 3: HIGH-PERFORMING - INSTINCT CHECKS

### Storage Performance - INSTINCT

**EBS vs Instance Store**  
- EBS = Persistent network drive (gp3, io2)  
- Instance Store = Ephemeral, on host, super fast, LOST on stop

**If question says:**
- "Critical data, can't lose" → EBS
- "Cache, temporary, max performance" → Instance Store
- "Shared, POSIX, Linux" → EFS
- "Shared, Windows, AD" → FSx
- "High-performance computing, ML" → FSx for Lustre

---

### Database - READ THE LAST SENTENCE

**Question ends with:**
- "... changing application or needs to modify" → ElastiCache
- "...microsecond latency" → DAX (DynamoDB) or MemoryDB (Redis)
- "...global, low RPO" → Aurora Global or DynamoDB Global Tables
- "...serverless, unpredictable" → Aurora Serverless or DynamoDB On-demand

---

### CloudFront vs Global Accelerator

| Feature | CloudFront | Global Accelerator |
|---------|-----------|---------------------|
| Content | Static, dynamic | Any TCP/UDP |
| Protocol | HTTP/HTTPS | TCP/UDP |
| IP addresses | Changing | Static (BYOIP/EIP) |
| Caching | ✅ | ❌ |
| Edge locations | 450+ | ~100 |

**Memory Trick**:  
**C**loudFront = **C**ontent (cacheable)  
**G**lobal Accelerator = **G**eneric (any protocol, static IP)

---

## 🧠 DOMAIN 4: COST-OPTIMIZED - PRICE TAGS

### Compute - The Price Pyramid

```
TOP (Most expensive)
    On-Demand (No commitment)
    Reserved (1-3 year commitment)
    Savings Plan (Flexible commitment)
    Spot (Interruptible)
BOTTOM (Cheapest)
```

**If workload is:**
- Steady state → Reserved / Savings Plan
- Interruptible, stateless → Spot
- Unpredictable → On-Demand
- Need to run immediately → On-Demand

---

### S3 Storage Classes - TEMPERATURE

**HOT** → S3 Standard (Frequent access)  
**WARM** → S3 Standard-IA (Infrequent, quick access)  
**COOL** → S3 Glacier Instant (Rare, instant)  
**COLD** → S3 Glacier Flexible (Archival, minutes)  
**FROZEN** → S3 Glacier Deep Archive (7+ years, hours)

**Memory Trick**: **Hot → Warm → Cool → Cold → Frozen** (Like a drink)

---

### NAT Gateway - THE COST TRAP

**NAT Gateway = $ per hour + $ per GB**  
**NAT Instance = $ per hour (EC2) + no per GB**

**If question says "cost-optimized" and mentions occasional outbound internet → NAT Instance**  
**If question says "managed, high availability" → NAT Gateway**

---

## 🃏 THE 12 DEADLY GOTCHAS - MEMORIZE THESE

1. **RDS Read Replica** → Does NOT auto-failover, does NOT help writes
2. **Multi-AZ RDS** → Does NOT scale reads, DOES auto-failover
3. **S3 Pre-signed URL** → Object level, time-limited, NO IAM required
4. **S3 Block Public Access** → Overrides bucket policies, account level wins
5. **IMDSv2** → Prevents SSRF, hop limit = 1
6. **CloudFront + ACM** → Certificate MUST be in us-east-1
7. **VPC Gateway Endpoint** → S3 and DynamoDB ONLY, FREE
8. **VPC Interface Endpoint** → Everything else, HOURLY COST
9. **EBS vs Instance Store** → EBS persists, Instance Store ephemeral
10. **SQS FIFO** → Exactly-once, 300 TPS (3000 with batching)
11. **Aurora** → 6 copies across 3 AZs, storage auto-scales
12. **Lambda + VPC** → ENI attachments = cold start penalty

---

## ✍️ EXAM DAY - 5 MENTAL SCRIPTS

### Script 1: "I don't know the service"
1. Is it managed? → Choose AWS managed over custom
2. Is it secure? → Encryption, IAM roles, private connectivity
3. Is it cost-effective? → Spot, Reserved, Lifecycle policies

### Script 2: "Two answers seem correct"
1. Which is MORE managed? (RDS vs EC2 database → RDS)
2. Which is MORE secure? (IAM role vs Access key → Role)
3. Which is MORE automated? (Target tracking vs Step scaling → Target)

### Script 3: "They're asking about hybrid"
1. Storage Gateway for on-prem to cloud
2. Direct Connect for dedicated private connection
3. VPN for encrypted tunnel over internet
4. DataSync for large data transfers

### Script 4: "I see a number (RTO/RPO)"
1. RTO = Time to recover (lower = more expensive)
2. RPO = Data loss (lower = more replication)
3. Match the strategy to the number

### Script 5: "I'm completely stuck"
1. Eliminate the obviously WRONG answers (usually 2)
2. Look for the keyword that matches my mnemonic
3. Choose the answer with MORE AWS services (not custom)
4. Trust my gut - first instinct is usually right

---

## 🎵 FINAL CHEAT SHEET - 30 SECOND MENTAL SCAN

**Before you click "Next", ask:**

- **IAM Role?** (EC2, Lambda, cross-account)
- **Multi-AZ?** (HA) or **Read Replica?** (Scaling)
- **VPC Endpoint?** (Private S3/DDB access)
- **Pre-signed URL?** (Temporary access, no AWS account)
- **CloudFront?** (Global, HTTPS, custom domain)
- **Secrets Manager?** (Auto-rotation) or **Parameter Store?** (No rotation)
- **Spot?** (Cheap, interruptible) or **Reserved?** (Steady state)
- **S3 Lifecycle?** (Cost optimization)
- **DLQ?** (Failed messages)
- **SCP?** (Cannot be overridden, org-level)

---
