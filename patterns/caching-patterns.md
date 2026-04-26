# Caching Patterns & Strategies

When introducing a cache, you must decide *how* and *when* data is written to and read from it. These decisions are known as caching strategies.

## Cache-Aside (Lazy Loading)

Cache-Aside is the most common and generally recommended caching strategy. It is "lazy" because data is only loaded into the cache when it is actually requested.

```mermaid
sequenceDiagram
    participant App as Application
    participant Cache as Cache Layer
    participant DB as Database
    
    App->>Cache: 1. Request Data (Key)
    alt Cache Hit
        Cache-->>App: Return Data
    else Cache Miss
        Cache-->>App: Not Found
        App->>DB: 2. Query Data
        DB-->>App: Return Data
        App->>Cache: 3. Write Data to Cache
    end
```

**Workflow:**
1.  **Check Cache:** The application first checks the cache for the requested data.
2.  **Cache Hit / Miss:** If found (hit), the data is returned immediately. If not found (miss), the application queries the primary database.
3.  **Update Cache:** The application returns the data from the database to the user, and then writes that same data into the cache for future requests.

**Advantages:**
*   Only heavily requested data occupies valuable cache space.
*   If the cache cluster fails, the system can still function (albeit slower) by falling back to the database.

## Write-Behind (Write-Back) Caching

In a Write-Behind strategy, the application treats the cache as the primary data store for writes.

```mermaid
sequenceDiagram
    participant App as Application
    participant Cache as Cache Layer
    participant DB as Database
    
    App->>Cache: 1. Write Data
    Cache-->>App: 2. Acknowledge Write (Success)
    Note over Cache,DB: ... Time Passes ...
    Cache->>DB: 3. Asynchronously Bulk Write
```

**Workflow:**
1.  **Write to Cache:** The application writes the update directly to the cache.
2.  **Immediate Return:** The cache immediately confirms the write to the application, allowing the user to proceed instantly.
3.  **Async Database Sync:** Behind the scenes, the cache asynchronously flushes those writes to the primary database at a later time (often in batches).

**Trade-offs:**
*   **Advantage:** Extremely high write performance and availability. The application never waits for slow database disk writes.
*   **Disadvantage (Consistency Risk):** If the cache server crashes before it has successfully synchronized its data with the main database, any acknowledged but un-synced writes are permanently lost. Consistency is strictly eventually consistent.

## Refresh-Ahead

Refresh-ahead is a proactive cache invalidation and loading strategy where the system is designed to automatically update the cache periodically *before* the data actually expires or is requested. 

```mermaid
sequenceDiagram
    participant App as Application
    participant Cache as Cache Layer
    participant DB as Database
    
    loop Every X seconds (Background Process)
        Cache->>DB: 1. Fetch Latest Data
        DB-->>Cache: 2. Return Data
        Cache->>Cache: 3. Update Cache Entry
    end
    
    App->>Cache: 4. Request Data
    Cache-->>App: 5. Return Fresh Data Instantly
```

**Workflow:**
1.  **Background Sync:** A background worker or daemon process continuously monitors cache entries.
2.  **Proactive Fetch:** Just before an item's TTL expires, the process reaches out to the database to fetch the most up-to-date value.
3.  **Silent Update:** The cache is updated seamlessly in the background. When the application reads from the cache, it always receives extremely fast, relatively fresh data without ever triggering a slow database fetch on a cache miss.

**Trade-offs:**
*   **Advantage:** Zero read latency for the application because cache misses are virtually eliminated for frequently accessed items.
*   **Disadvantage:** Can waste significant network bandwidth and database resources by continuously refreshing data that might not ever be requested again by the application.
