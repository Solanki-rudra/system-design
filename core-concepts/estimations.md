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
