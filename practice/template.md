# System Design Practice: [System Name]

**Date:** YYYY-MM-DD
**Time Spent:** [Target: 45 minutes max]
**Problem Source:** *(e.g., "common-questions.md — Video Streaming", "Leetcode Discuss", "Interviewing.io")*

---

## ⏱️ Time Budget (Stick to this!)

| Phase | Allotted Time | Actual Time |
|:---|:---:|:---:|
| 1. Requirements & Scoping | 5–10 min | |
| 2. Core Entities & API Design | 5 min | |
| 3. Back-of-the-Envelope Estimation | 5 min | |
| 4. High-Level Architecture | 10–15 min | |
| 5. Deep Dive & Bottlenecks | 10–15 min | |
| 6. Tradeoffs & Wrap-up | 3–5 min | |

---

## 1. Requirements & Scoping (5-10 mins)
*Tip: Treat this step as a conversation to define boundaries. Do not design the whole world, just what is explicitly in scope.*

### Functional Requirements
*What specific features and user behaviors must the system support to solve the problem?*
- [ ] 
- [ ] 
- [ ] *(Are there any explicitly out-of-scope features to ignore? e.g., Auth, Payments)*

### Non-Functional Requirements
*What are the constraints and quality attributes? These heavily dictate your architecture.*
- [ ] **Scale:** Expected Daily Active Users (DAU), Read/Write Queries Per Second (QPS).
- [ ] **Data Volume:** How much data is generated or stored per second/day/year?
- [ ] **Latency / Performance:** Is speed critical? *(e.g., Must respond in < 200ms)*
- [ ] **CAP Tradeoff:** In a network partition, do we prioritize Consistency (CP) or Availability (AP)?
- [ ] **Read/Write Ratio:** Is this system read-heavy (→ caching, replicas) or write-heavy (→ queues, sharding)?
- [ ] **Durability:** Can we tolerate data loss? *(e.g., analytics vs banking)*

---

## 2. Core Entities & API Design (5 mins)
*Tip: Defining APIs early solidifies the system boundaries and data interactions before you get stuck drawing boxes.*

### Core Entities / Data Schema
*What are the main nouns? Will this map better to a SQL or NoSQL database?*
- **User:** `{ id, username, email, created_at }`
- **[Entity]:** `{ id, user_id, payload, status, created_at }`

### APIs
*What are the core endpoints external clients will hit? Derive these directly from the Functional Requirements.*
- `POST /v1/example` → Request: `{}`, Response: `{ id, status }`
- `GET /v1/example/{id}` → Response: `{ id, data, created_at }`
- `DELETE /v1/example/{id}` → Response: `{ success: true }`

*API Naming Tip: Use nouns for resources (`/users`, `/videos`), verbs for actions (`/videos/{id}/publish`).*

---

## 3. Back-of-the-Envelope Estimation (5 mins)
*Tip: This validates your architectural choices. A storage problem needs a different solution than a throughput problem.*

| Metric | Calculation | Result |
|:---|:---|:---|
| Daily Active Users (DAU) | Provided or assumed | |
| Write RPS | DAU × avg writes/day ÷ 86,400 | |
| Read RPS | Write RPS × read:write ratio | |
| Storage per item | Estimated payload size (bytes) | |
| Daily new storage | Write RPS × 86,400 × item size | |
| 5-year storage | Daily storage × 365 × 5 | |

**Conclusion:** *(e.g., "5TB over 5 years → sharding required" or "12GB/year → single DB sufficient")*

---

## 4. High-Level Design (10-15 mins)
*Tip: Draw the "Happy Path" first. Get the data from the client to the database. Narrate your choices out loud as if explaining to an interviewer.*

```mermaid
flowchart TD
    Client([Client Application]) --> LB{Load Balancer}
    LB --> API[API Gateway / App Servers]
    API --> Cache[(Redis Cache)]
    API --> PrimaryDB[(Primary Database)]
    PrimaryDB -.-> ReplicaDB[(Read Replicas)]
```

*Label every component with its purpose, not just its type. E.g., "Load Balancer (TLS Termination & Routing)"*

---

## 5. Deep Dives, Tradeoffs & Bottlenecks (10-15 mins)
*Tip: Identify Single Points of Failure (SPOF) and justify your technology decisions. Remember, there are no perfect architectures, only tradeoffs.*

### A. Data Storage Justification
- **SQL vs NoSQL:** *Why choose this? (e.g., "I chose Postgres because we need strict ACID compliance for transactions.")*
- **Schema Design:** *Sketch the key tables or document structures with their important fields.*
- **Indexing Strategy:** *Which fields need indexes? (e.g., "Index on `user_id` for fast user-specific queries.")*

### B. Scaling & Optimization
- **Caching:** *What specific data is being cached? Which strategy: Cache-Aside, Write-Through, Write-Behind? What TTL?*
- **CDN:** *Are there static assets (images, videos) that should be served from a CDN edge?*
- **Asynchronous Processing:** *Do we need Message Queues (Kafka/RabbitMQ/SQS) to offload heavy background tasks?*
- **Partitioning/Sharding:** *If a single hard drive fills up, how do we split the data? (e.g., Shard by UserID, Geographic region).*
- **Read Replicas:** *How many? Are reads routed to replicas, or only the primary?*

### C. Bottlenecks / SPOFs

| Component | Single Point of Failure? | Mitigation Strategy |
|:---|:---:|:---|
| Load Balancer | Yes (if single LB) | Active-passive LB pair or cloud-managed LB |
| Primary Database | Yes | Read replicas + automated failover |
| Cache Layer | No (cache miss → DB fallback) | Cluster mode (Redis Cluster) |
| Message Queue | Yes (if single broker) | Replicated Kafka partitions |
| App Servers | No | Stateless + auto-scaling group |

*Key Questions to Answer:*
- *What happens if the primary database goes offline?*
- *What is the biggest bottleneck if traffic suddenly spikes 10x?*
- *What happens if a worker crashes mid-job? Is the work lost?*

### D. Architecture Decision Log
*Document the key decisions you made and the tradeoffs accepted:*

| Decision | Chosen Option | Alternative Considered | Reason for Choice |
|:---|:---|:---|:---|
| Database type | PostgreSQL | MongoDB | Need ACID + relational schema |
| Caching strategy | Cache-Aside | Write-Through | Lazy loading better for read patterns |
| Job processing | Async Queue | Synchronous | Transcoding takes too long for sync |

---

## 6. Tradeoffs & Wrap-up (3-5 mins)

### Final Architecture Summary
*One paragraph: What does this system do, how does it scale, and what is the key tradeoff you accepted?*

> *(e.g., "This URL shortener uses a horizontally-scaled app tier behind an AWS ELB, with Redis for redirect caching and PostgreSQL for URL storage. The key tradeoff is eventual consistency: new short URLs may take up to 5 seconds to propagate to read replicas, which is acceptable since the requirement is for the system to stay available (AP), not for instant global consistency.")*

---

## 7. Self-Evaluation & Reflection

### Key Insight
*Practice is only useful if you identify your weak points. Time management is usually the hardest part.*

### What went well?
- 

### What did I miss or struggle with?
- *(e.g., "I spent 20 minutes on the exact DB schema and had to rush the caching strategy.")*
- *(e.g., "I forgot to discuss the async job failure/retry mechanism.")*

### Gaps to fill — Action items to study:
- 

### Score yourself (honest self-assessment):
- [ ] Clarified requirements before drawing? *(5 min rule)*
- [ ] Defined APIs from requirements? 
- [ ] Did back-of-the-envelope estimation?
- [ ] Identified at least one SPOF and its mitigation?
- [ ] Discussed at least one caching strategy?
- [ ] Explained the CAP tradeoff for this system?
- [ ] Stayed within 45 minutes total?
