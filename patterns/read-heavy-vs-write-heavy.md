# Read-Heavy vs. Write-Heavy Systems

Understanding the ratio of reads to writes in your system is fundamental to shaping your architecture, choosing your database, and designing your caching strategy. 

## Distinguishing the Ratio

```mermaid
pie title Typical Traffic Ratios
    "Reads (e.g., viewing data)" : 80
    "Writes (e.g., updating data)" : 20
```
*Note: The exact ratio varies heavily by domain, but most applications are strongly read-heavy with typical ratios ranging from 10:1 to 1000:1 (reads to writes).*

**Q: Why is it important to distinguish between read and write transactions when determining non-functional requirements?**
A: Different systems have vastly different operational profiles. For example, banking applications or social media feeds are typically **read-heavy** (users check their balances/transaction history far more frequently than they transfer money or make payments). 
Understanding this ratio is crucial because it helps determine:
1. **Appropriate System Architecture**: e.g., separating read and write database nodes.
2. **Caching Strategies**: Heavy read traffic often demands layered caching to protect the primary database and speed up responses.
3. **Resource Allocation**: You can scale out read replicas to handle high read loads horizontally without paying for expensive write-capable master nodes.

## System Profiles

### Read-Heavy Systems
- **Examples**: 
  - **Social media** (where users mostly scroll, e.g., Twitter feeds or YouTube).
  - **Package managers** like `npm` (where most users download packages rather than publish).
  - **Data warehouses** and Banking apps (checking balances).
- **Optimization Strategy**: 
  - Substantial caching layers.
  - Database read-replicas.
  - Content Delivery Networks (CDNs) for static assets.

### Write-Heavy Systems
- **Examples**: 
  - **Logging systems** and metric collection.
  - **Eventing systems**.
  - IoT telemetry ingestion and click-tracking.
- **Optimization Strategy**:
  - Write-optimized databases (e.g., Cassandra, Time-series databases).
  - Message queues or event streams (Kafka) to buffer high-velocity write bursts.
  - Batch processing instead of single real-time writes when possible.

---

## Mixed Systems: CQRS (Command Query Responsibility Segregation)

Many real-world systems are neither purely read-heavy nor purely write-heavy — different parts of the system have radically different operational profiles. For example, a social network's feed-reading path is extremely read-heavy, but its notification-writing path is extremely write-heavy.

**CQRS** is an architectural pattern that explicitly separates the *read model* from the *write model*, allowing each to be optimized independently.

```mermaid
flowchart LR
    Client([Client])
    
    subgraph Write Side
        W[Command Handler] --> PrimaryDB[(Primary DB\nWrite-Optimized)]
        PrimaryDB --> EventBus[Event Bus / Queue]
    end
    
    subgraph Read Side
        EventBus --> Projector[Read Model Projector]
        Projector --> ReadDB[(Read Store\ne.g., Redis / Elasticsearch)]
        ReadDB --> Q[Query Handler]
    end
    
    Client -->|Commands\ne.g., POST /post| W
    Client -->|Queries\ne.g., GET /feed| Q
```

**How CQRS Works:**
1.  **Commands (Writes):** Mutations go to the write model — a normalized, ACID-compliant database optimized for correctness.
2.  **Events:** The write model emits events (e.g., "PostCreated", "UserFollowed") to an event bus.
3.  **Projections:** A projector service consumes events and maintains a denormalized *read model* optimized for the specific queries clients need (e.g., a pre-aggregated feed).
4.  **Queries (Reads):** Reads hit the read model directly — no joins, no complex aggregations at query time. Reads are fast because the work is pre-computed.

**Trade-offs:**
*   **Advantage:** Extreme scalability for both reads and writes independently. The read model can be a Redis cache, Elasticsearch index, or a denormalized replica — whatever serves the query pattern best.
*   **Disadvantage:** Eventual consistency between write and read models. After writing, the read model may be stale for a brief period while the event propagates. This is acceptable for most social features (e.g., a feed showing a post 1 second late is fine) but unacceptable for financial systems (e.g., a balance update must be immediately reflected).

**When to use CQRS:**
- The read and write models have fundamentally different optimization requirements.
- Query complexity is high (many aggregations, joins) on a write-optimized schema.
- The system has very high read throughput requirements that the write DB cannot handle.

---

## Architecture Selection Guide

Use this flowchart to choose your optimization strategy:

```mermaid
flowchart TD
    A{What is the read/write ratio?} -->|Mostly reads\n10:1 or higher| B[Read-Heavy Strategy]
    A -->|Mostly writes\nor near 1:1| C[Write-Heavy Strategy]
    A -->|Mixed — different\nparts have different profiles| D[Consider CQRS]
    
    B --> B1[Add Redis caching layer]
    B1 --> B2[Add read replicas]
    B2 --> B3[Add CDN for static assets]
    
    C --> C1[Use write-optimized DB\ne.g., Cassandra, TimescaleDB]
    C1 --> C2[Add message queue\nto buffer bursts]
    C2 --> C3[Use batch writes\ninstead of per-row writes]
    
    D --> D1[Separate write DB\nfrom read store]
    D1 --> D2[Emit events on write]
    D2 --> D3[Build denormalized\nread projections]
```
