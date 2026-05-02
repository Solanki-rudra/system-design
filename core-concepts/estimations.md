# Estimations (Back-of-the-Envelope)

Estimations, often referred to as "back-of-the-envelope calculations," are a critical preliminary step in system design.

## The Purpose of Estimations

The primary goal of estimating is **direction, not precision**. You aren't trying to calculate exact byte counts; you are trying to determine if your architectural plan is in the right "ballpark." 

Estimations serve two critical functions:
1.  **Sanity Checking:** Validating that your proposed design can mathematically handle the expected load.
2.  **Identifying Bottlenecks:** Helping you discover the most critical limitation of your system (e.g., "Oh, we don't have a storage problem, we have a massive read throughput problem").

## Key Metrics to Estimate

When starting a design, you should quickly estimate the metrics most likely to bottleneck the system:

1.  **Number of Users:** Active daily/monthly users.
2.  **Throughput (RPS):** Requests Per Second for both reading and writing data.
3.  **Storage Capacity:** How much data is generated and stored over time (usually calculated per month or over 5 years).

## Database Sizing: Gigabytes vs. Terabytes

A common area of confusion is when a single relational database hits its limits and requires sharding. Understanding the scale of data sizes is crucial here.

*   **1 Kilobyte (KB)** = 1,000 bytes
*   **1 Megabyte (MB)** = 1,000 KB (1 million bytes)
*   **1 Gigabyte (GB)** = 1,000 MB (1 billion bytes)
*   **1 Terabyte (TB)** = 1,000 GB (1 trillion bytes)

**The Rule of Thumb for Sharding:**
*   **Under 500 GB:** A single, standard relational database (like PostgreSQL) can handle this comfortably with plenty of margin. You are completely safe with a single instance.
*   **Approaching 1 TB (1,000 GB):** At this scale, single databases begin to struggle operationally. Routine tasks like backing up data, rebuilding indexes, or migrating tables become incredibly slow. You should start *thinking* about partitioning or sharding.
*   **Multiple Terabytes (e.g., 10 TB+):** You absolutely must architect a distributed, sharded database system. 

## Storage Calculation Example: A To-Do App

**Scenario:** Calculate the monthly storage required for a To-Do application.

**Assumptions:**
*   **Users:** 100,000 active users.
*   **Usage:** Each user creates 3 tasks per day.
*   **Size:** A single task (text + metadata) is about 500 bytes.
*   **Time:** 30 days in a month.

**The Math Step-by-Step:**
1.  **Total Tasks Per Day:** 
    `100,000 users × 3 tasks/user = 300,000 tasks/day`
2.  **Total Tasks Per Month:** 
    `300,000 tasks/day × 30 days = 9,000,000 tasks/month`
3.  **Total Bytes Per Month:** 
    `9,000,000 tasks × 500 bytes/task = 4,500,000,000 bytes`

**Converting Bytes to Readable Units:**
*   4,500,000,000 bytes ÷ 1,000 = 4,500,000 **KB**
*   4,500,000 KB ÷ 1,000 = 4,500 **MB**
*   4,500 MB ÷ 1,000 = **4.5 GB per month**

**Conclusion:** 4.5 GB of storage per month is extremely small. In a year, that's only ~54 GB. This proves that a To-Do app with 100,000 users will never require database sharding for storage reasons; a single standard database will easily suffice for a decade.

## Storage Calculation Example: A Video Upload Service

**Scenario:** Calculate the daily storage requirement for a basic video upload service.

**Assumptions:**
*   **Users:** 1,000 active users.
*   **Usage:** Each user uploads 1 video per day.
*   **Size:** Average raw video file is 1 GB.

**The Math Step-by-Step:**
1.  **Total Uploads Per Day:**
    `1,000 users × 1 upload/user = 1,000 uploads/day`
2.  **Total Raw Storage Per Day:**
    `1,000 uploads × 1 GB/upload = 1,000 GB/day = 1 TB/day`

**Conclusion:** 1 TB of new raw data *per day* is a fundamentally different architectural problem than 4.5 GB per month for a Todo app. This immediately signals:
- **Object Storage** (like AWS S3) is mandatory — relational databases cannot store binary blobs at this scale cost-effectively.
- **Database sharding** may be needed for metadata within weeks, not years.
- **Worker scaling** for transcoding must account for processing hundreds of GB daily.

> The critical lesson: the same estimation methodology applied to different data types can shift the architecture by orders of magnitude. Always estimate *before* committing to a design.

---

## Data Access Speeds (Latency Hierarchy)

When making design choices, you don't need to memorize exact microsecond latencies (e.g., L1 cache is 0.5ns). You only need to understand the relative *hierarchy* of speeds.

From fastest to slowest:
1.  **CPU Cache (L1/L2)**
2.  **RAM (Memory)** 
3.  **SSD (Solid State Disk)**
4.  **HDD (Magnetic Hard Drive)**
5.  **Network (Internet)**

**The Architectural Takeaway:**
*   Caches (like Redis) store data in **RAM**.
*   Databases (like PostgreSQL) permanently store data on **Disk** (SSD/HDD).
*   Because RAM is mathematically orders of magnitude faster than Disk, retrieving data from a Cache will always be drastically faster than retrieving it from a Database.

---

## RPS (Requests Per Second) Estimation Walkthrough

RPS is the most important throughput metric in system design. Here is how to estimate it from scratch:

**Formula:**
```
RPS = (DAU × avg_requests_per_user_per_day) ÷ 86,400
```
*(86,400 = number of seconds in a day)*

**Example: Estimating RPS for a Social Media Feed**

**Assumptions:**
*   **Daily Active Users (DAU):** 10,000,000 (10 million)
*   **Avg requests per user per day:** 50 (scrolling, loading feed, refreshing)
*   **Read/Write ratio:** 90:10 (mostly reads)

**The Math:**
1.  **Total requests per day:**
    `10,000,000 users × 50 req/user = 500,000,000 requests/day`
2.  **Average RPS (all requests):**
    `500,000,000 ÷ 86,400 ≈ 5,800 RPS`
3.  **Read RPS (90%):**
    `5,800 × 0.90 ≈ 5,200 read RPS`
4.  **Write RPS (10%):**
    `5,800 × 0.10 ≈ 580 write RPS`

**Conclusion:** ~5,200 read RPS immediately signals:
- A caching layer (Redis) is **mandatory** — the database alone cannot handle 5,200 reads/sec efficiently.
- Read replicas are needed to distribute the read load.
- The write path at ~580 RPS is manageable with a single primary DB initially.

> **Peak Traffic Rule of Thumb:** Real traffic is never evenly distributed. Peak traffic is typically **2–3× the average**. Always design for peak, not average: `Peak RPS ≈ 5,200 × 3 = 15,600 read RPS`.

---

## Quick Reference: Estimation Cheatsheet

| Metric | Common Scale | Implication |
|:---|:---|:---|
| < 1,000 RPS | Small/Medium | Single server, single DB likely sufficient |
| 1,000 – 10,000 RPS | Medium/Large | Load balancer + caching layer needed |
| 10,000 – 100,000 RPS | Large | Horizontal scaling, read replicas, CDN |
| > 100,000 RPS | Web-scale | Sharding, global distribution, dedicated infra |
| < 100 GB storage | Small | Single DB, no sharding needed |
| 100 GB – 1 TB storage | Medium | Start considering partitioning |
| > 1 TB storage | Large | Sharding or object storage required |
| > 10 TB storage | Massive | Distributed object storage (S3), data lakes |

### Useful Constants to Memorize
| Unit | Value |
|:---|:---|
| Seconds per day | 86,400 |
| Seconds per month | ~2,600,000 (~2.6M) |
| Seconds per year | ~31,500,000 (~31.5M) |
| 1 KB | 1,000 bytes |
| 1 MB | 1,000,000 bytes |
| 1 GB | 1,000,000,000 bytes |
| 1 TB | 1,000,000,000,000 bytes |
