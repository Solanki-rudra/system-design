# System Design Quick Revision

## 1. Always Ask (Requirements & Scope)
Before designing anything, systematically clarify the boundaries and constraints:
- **Users?**: Who is using this? How many active users are there? (e.g., DAU/MAU)
- **Scale?**: What is the expected traffic? (Requests per second, data storage volume)
- **Read vs Write?**: What is the read-to-write ratio? (Heavy read = caching; Heavy write = queues/sharding)
- **Constraints?**: Do we have latency requirements? High availability guarantees?

## 2. Always Include (Core Building Blocks)
Most high-level architectures utilize these standard components:
- **Client**: Web, Mobile, or API consumers making the requests.
- **Load Balancer**: Distributes incoming network traffic evenly across your servers to avoid single points of failure.
- **Server/API Gateway**: Handles the core business logic, routing, and access control.
- **Cache**: Fast, in-memory data store (e.g., Redis) to reduce load on the database for frequent, read-heavy operations.
- **Database**: Persistent storage. (RDBMS for structured/relational data vs NoSQL for flexible schema/high scale).

## 3. Golden Rules
- **Start simple, scale later**: Don't overengineer right out of the gate. Solve the core problem first with a basic architecture.
- **Talk tradeoffs**: There is no "perfect" system. Be ready to explain *why* you chose a component and what you sacrifice by doing so (Cost vs Performance).
- **Identify Bottlenecks**: Always look for Single Points of Failure (SPOF) and discuss how to add redundancy.

## 4. CAP Theorem Refresher
A distributed system can only guarantee two out of three:
- **Consistency**: Every read receives the most recent write.
- **Availability**: Every request receives a (non-error) response.
- **Partition Tolerance**: The system continues to operate despite network failures.
*(Note: Because networks drop packets, P is mandatory. You are usually trading off between C and A).*

## Key Insight
A successful system design interview is a collaborative conversation, not a test with a single correct answer. Your thought process and communication matter as much as your technical knowledge.

## Interview Tip
When drawing your architecture, narrate your decisions out loud. For example: "I am placing a Load Balancer here to ensure our web servers don't get overwhelmed and to provide high availability."

---

## 5. Database Types Quick Reference

| Type | Examples | Best For | Avoid When |
|:---|:---|:---|:---|
| **Relational (SQL)** | PostgreSQL, MySQL | Structured data, ACID transactions, complex JOINs | Unstructured data, massive horizontal scale |
| **Document** | MongoDB, Firestore | Flexible schema, nested objects, user profiles | Complex relationships between entities |
| **Key-Value** | Redis, DynamoDB | Caching, sessions, simple lookups | Complex queries or relational data |
| **Wide-Column** | Cassandra, HBase | Write-heavy, time-series, high availability | Ad-hoc queries, transactions |
| **Graph** | Neo4j, Amazon Neptune | Social networks, fraud detection, recommendations | Non-relational data, simple use cases |
| **Search** | Elasticsearch, Solr | Full-text search, log analysis, autocomplete | Primary data store (always use as secondary) |
| **Time-Series** | InfluxDB, TimescaleDB | IoT metrics, monitoring, analytics | General-purpose storage |

---

## 6. Scaling Decision Tree

```
Is traffic too high for one server?
├── YES → Add Load Balancer + Horizontal Scaling
│         ↓
│    Is the database the bottleneck?
│    ├── YES (reads too slow) → Add Cache (Redis) + Read Replicas
│    └── YES (data too large) → Partition → Shard
│
└── NO → Can you squeeze more from one server?
         └── YES → Vertical Scaling (buy bigger server)
```

**Scaling Sequence (most systems follow this order):**
1. Single server
2. Add DB server (separate app + DB)
3. Add Load Balancer + app server pool
4. Add Cache (Redis/Memcached)
5. Add DB Read Replicas
6. Add CDN for static assets
7. Partitioning / Sharding
8. Microservices decomposition

---

## 7. Common Failure Modes & Mitigations

| Failure | Root Cause | Mitigation |
|:---|:---|:---|
| **Database overload** | Too many reads on primary | Add caching + read replicas |
| **Single Point of Failure** | One server handles all traffic | Redundancy + failover |
| **Cache stampede** | Cache expires → all requests hit DB simultaneously | Staggered TTL + locking |
| **Queue buildup** | Workers too slow for producers | Scale workers + back pressure |
| **Thundering herd** | Many clients retry at the same time | Exponential backoff with jitter |
| **Data inconsistency** | Replica lag or cache staleness | Tune consistency level; use strong consistency for critical paths |
| **Memory leak** | Unbounded cache growth | Set maxmemory policy on Redis; use TTL on all cache entries |

---

## 8. Numbers Every Engineer Should Know

| Operation | Approximate Latency |
|:---|:---|
| L1 cache read | ~1 ns |
| RAM read | ~100 ns |
| SSD sequential read | ~100 µs |
| HDD sequential read | ~10 ms |
| Network round-trip (same region) | ~1 ms |
| Network round-trip (cross-continent) | ~150 ms |
| Redis GET | ~0.1 ms |
| PostgreSQL indexed query | ~1–5 ms |
| PostgreSQL full table scan (1M rows) | ~1–10 s |

**Memory Size Reference:**
- 1 character (ASCII) = 1 byte
- 1 UUID = 36 bytes
- 1 tweet (280 chars) ≈ 300 bytes
- 1 user profile record ≈ 1–2 KB
- 1 photo (compressed) ≈ 200 KB – 2 MB
- 1 minute of HD video ≈ 100–300 MB

---

## 9. Common Interview Phrases

- *"Given the read-heavy nature of this system, I'd prioritize caching and read replicas over heavy write optimization."*
- *"In the event of a network partition, I'd prefer Availability (AP) here because users can tolerate slightly stale data."*
- *"This is a good candidate for asynchronous processing — the user doesn't need to wait for the result in real-time."*
- *"My first concern with this design is the single database server — it's a SPOF. I'd add a replica with automated failover."*
- *"I'd start with a monolith and extract services only when a specific component becomes the bottleneck."*