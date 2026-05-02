# Caching Fundamentals

Caching is a technique used to store a copy of frequently accessed data in a temporary, high-speed storage layer so that future requests for that data can be served faster than querying the primary data store.

## Cache vs. Database

While both store data, their design purposes are fundamentally different:

| Feature | Cache | Database |
| :--- | :--- | :--- |
| **Primary Purpose** | Ultra-fast access | Persistent, structured storage |
| **Data Structure** | Typically Key-Value store | Relational tables, documents, graphs, etc. |
| **Query Complexity**| Very low (simple GET/SET by key) | High (supports complex JOINs, filtering, aggregations) |
| **Location** | As close to the consumer/application as possible | Further down the stack |
| **Storage Medium** | Ephemeral (RAM) | Persistent (Disk/SSD) |

## Core Benefits of Caching

Implementing a caching layer introduces three massive architectural advantages:

1.  **Reduced Latency:** By serving data from fast memory rather than slow disk or complex computations, response times drop significantly. This directly improves the user experience.
2.  **Decreased System Load:** Serving a request from the cache acts as a "shield" for your primary database. It eliminates the need to make multiple network hops and perform heavy database queries, freeing up the database to handle crucial write operations.
3.  **Lower Costs:** Database reads can be computationally expensive (and financially expensive in cloud environments). Serving frequently accessed data from a cheap, fast cache drastically lowers the resource consumption of your primary datastore.

## Content Delivery Networks (CDNs)

A CDN is a specialized, globally distributed form of caching designed specifically to serve static assets (like images, CSS, JavaScript, and videos).

*   **How it works:** CDNs physically store copies of your static content on proxy servers located in data centers and Internet Service Providers (ISPs) all around the world.
*   **The Advantage:** When a user requests a file, the CDN routes that request to the geographically closest server (the "edge"). By drastically reducing the physical distance the data must travel (fewer network hops), CDNs significantly reduce latency and vastly improve page load speeds for global users.

## The Fundamental Tension: Performance vs. Freshness

A cache is always a secondary representation of data, not the primary source of truth. This creates an inherent architectural tension between **performance** (speed of access) and **data freshness** (how up-to-date the data is compared to the primary database). Every caching strategy is essentially an exercise in managing this trade-off.

## Cache Eviction vs. Cache Invalidation

While often used interchangeably, these are two distinct mechanisms for managing stale or excess data:

*   **Cache Invalidation:** The logical process of marking data as "stale," "expired," or no longer accurate. The data might still physically reside in memory, but the system is instructed not to use it.
*   **Cache Eviction:** The physical process of removing specific data from the cache because the cache has reached its maximum capacity (it is full) and needs to free up space for new, incoming data.

### Time To Live (TTL)

**TTL (Time To Live)** is a common cache invalidation mechanism. When an item is stored in the cache, it is assigned a TTL value (e.g., 60 seconds, 24 hours). Once that specific time frame elapses, the cached data is automatically considered invalid and is typically erased or overwritten, regardless of how frequently it was accessed during that period.

## Cache Eviction Policies

When a cache fills up, it must decide which existing data to remove using an eviction strategy. Common policies include:

*   **LRU (Least Recently Used):** Evicts the items that have not been accessed for the longest time. 
    *   *Implementation Note:* This is most commonly implemented using a **Doubly Linked List** combined with a Hash Map, allowing for highly efficient $O(1)$ tracking and removal of the least recently used items.
*   **LFU (Least Frequently Used):** Evicts the items that have been requested the fewest number of times overall, tracking the absolute frequency rather than just the recency.
*   **FIFO (First In, First Out):** A simple queue approach where the oldest data added to the cache is the first to be removed, regardless of how often or recently it was accessed.

---

## Caching Strategies: How Data Gets Into the Cache

Knowing *when* and *how* data is written to the cache is as important as knowing *when* to evict it. These are called **caching strategies** and they live in the application layer (not the cache layer itself). For detailed Mermaid diagrams and trade-off analysis of each strategy, see [`patterns/caching-patterns.md`](../patterns/caching-patterns.md).

**Quick summary of the four main strategies:**

| Strategy | When Cache is Populated | Consistency | Best For |
|:---|:---|:---|:---|
| **Cache-Aside** | On cache miss — app reads from DB, then writes to cache | Eventual | General purpose; most common |
| **Write-Through** | On every write — app writes to cache AND DB synchronously | Strong | Freshness critical, read-heavy |
| **Write-Behind** | On write — app writes to cache only; async DB flush later | Eventual | High write throughput (analytics, logging) |
| **Refresh-Ahead** | Background process proactively refreshes before TTL expires | Near-real-time | Predictable, frequently accessed data |

---

## Cache Warming

A **cold cache** is a freshly deployed or restarted cache with no data. On startup, all requests will be cache misses, sending a sudden spike of load to the database. This is known as **cache cold start**.

**Mitigation strategies:**
*   **Lazy warming (Cache-Aside):** Accept the cold start penalty. The cache will gradually warm up as requests come in and data gets populated.
*   **Pre-warming:** Before deploying, run a script that pre-populates the cache with the most commonly accessed keys. Requires knowing your hot data patterns in advance.
*   **Gradual rollout:** Deploy with traffic slowly ramping up (e.g., canary deployment), giving the cache time to warm before it receives full production traffic.

---

## When NOT to Cache

Caching is not a universal solution. Avoid caching when:
- **Data changes every request:** Caching a value that changes every millisecond provides no benefit and wastes memory.
- **Data is user-specific and low-volume:** If every user has a unique result that nobody else can reuse, the cache hit rate will be near 0%.
- **Consistency is non-negotiable:** Financial balances, inventory counts, and medical records must always reflect the latest write. Caching these risks showing dangerously stale values.
- **The bottleneck is compute, not data retrieval:** If your slowness comes from complex calculations, not database reads, a cache helps nothing. Consider instead memoization or pre-computation.
