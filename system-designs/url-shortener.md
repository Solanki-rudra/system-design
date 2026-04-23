# URL Shortener

Designing a URL shortener (like bit.ly) is a classic system design problem that touches on heavy read traffic, hashing algorithms, and strict data constraints.

## 1. Requirements & Scoping

### Functional Requirements
- **Core Functionality**: Given a long URL, return a shorter alias.
- **Redirection**: When a user clicks a short URL, redirect them to the original long URL.
- **Customization**: Optional custom aliases.

**Q: Is "URL expiration" typically considered a functional or non-functional requirement in a URL shortener system, and why?**  
A: URL expiration bridges both. It's **functional** because it changes feature behavior (expired links redirect to an error page). It's **non-functional** because it directly impacts system storage and maintenance. Without expiration, data grows indefinitely; with expiration, the system requires a cleanup mechanism (like a cron job) to purge records and reclaim database capacity.

### Non-Functional Requirements (NFRs)
- **High Availability**: The system strictly cannot go down, or millions of external links break.
- **Read-Heavy**: The read-to-write ratio is typically at least 100:1.

**Q: What are the two key performance metrics typically considered as non-functional requirements for a URL shortener service?**  
A: To successfully handle the massive scale of a URL shortener, you must baseline against:
1. **RPS (Requests Per Second):** Usually exceptionally high (e.g., handling 1 million RPS during a viral event).
2. **Maximum Latency:** Redirects must be functionally instantaneous to prevent ruining user experiences (e.g., ensuring 99th percentile latency is strictly under 500 milliseconds, though preferably much faster).

---

## 2. Defining Data Constraints

Setting size constraints is critical to prevent garbage data from crashing the application and to optimize the database.

**Q: Why should both long and short URLs have size limits in a URL shortener system?**  
A: Both must be capped for different architectural reasons:
- **Long URLs (The Input):** Need limits (e.g., max 3 kilobytes) to stay within backend database column restrictions, accommodate relational vs. object storage limits, and prevent malicious users from abusing payload sizes.
- **Short URLs (The Output):** Need limits (e.g., 1 kilobyte or 1000 characters) to ensure they efficiently fit into high-speed memory caches (like Redis) and to preserve their functional purpose of actually being short.

---

## 3. High-Level Design

```mermaid
flowchart LR
    Client([Client]) --> API[API Gateway]
    API --> Server[App Server]
    Server --> DB[(Database)]
    Server --> Cache[(Redis Cache)]
```

---

## 4. Key Components & Deep Dives

### ID Generation Strategies
Generating the short hash is the most common tradeoff discussed in this interview.
- **Random Hashing**: Creates unpredictable hashes but risks active collisions that must be handled.
- **Counter-Based (Base62 Encoding)**: Uses a sequential unique integer mapped to a Base62 string (a-z, A-Z, 0-9). Eliminates collisions and guarantees uniqueness, but makes URLs predictable.

### Suggested Tech Stack
- **Database**: Key-value stores (like DynamoDB or Cassandra) are often preferred over standard SQL databases due to the massive read-scaling requirements and the simple data structure.
- **Cache**: Redis or Memcached placed immediately before the database.

---

## 5. Scaling Strategy
- **Layered Caching**: Add caching for the top 20% most popular links (Pareto Principle).
- **Edge Deployment**: Use a CDN to cache the most popular redirects geographically closer to users to reduce latency.
- **Database Sharding**: Partition the database based on the first character of the hash to distribute the massive read load.