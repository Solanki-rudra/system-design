# Scoping the System

Before you design any architecture, you must properly scope the problem by clarifying constraints, traffic, and user expectations. Making assumptions here leads to over-engineering or under-provisioning.

## Estimating Load: Users vs RPS

When gathering requirements, it is a common pitfall to solely focus on the total number of expected users. While user count is helpful, it is rarely the most critical metric for system design. Instead, you must determine the **Requests Per Second (RPS)**. 

For example, a service like a URL shortener might not have millions of registered, logged-in users, but its core redirect functionality experiences massive traffic volumes whenever a popular link is clicked. Understanding specific traffic patterns and request volume spikes allows you to adequately scale the system's throughput to handle the *actual load*, rather than over-provisioning based on raw user accounts.

## Projected Growth Timeline

When discussing expected scale, product teams tend to provide their maximum expected user count for the product's entire lifetime. However, this peak metric may not be reached until years after the initial launch. 

Designing a Day-1 architecture equipped to handle a Year-5 peak load results in massively over-scaled systems, accumulating unnecessary cloud infrastructure costs early on. It is crucial to explicitly ask for the timeline and expected growth patterns. This knowledge empowers you to design elastic systems that can scale up gracefully alongside actual traffic growth, and seamlessly scale down during seasons or hours of low engagement.

## Defining Data Constraints & Limits

Scoping isn't just about traffic volume; it is also about the physical size of the data entering your system. 

**Q: Why is setting a maximum data size (e.g., maximum URL size) important during the design phase?**  
A: Setting strict data size limits is critical for several reasons:
1. **Preventing Abuse:** Without a limit, malicious users could submit gigabytes of garbage data in a single request, potentially taking down the system.
2. **Storage and Database Constraints:** Different databases have different limitations. Relational databases might have stricter row-size limits, whereas object storage can handle larger blobs. Knowing the max size dictates your primary database choice.
3. **Predictability:** Knowing both load (RPS) and max data size allows you to accurately forecast storage capacity requirements over time.

**Q: Why should both inputs (e.g., long URLs) and outputs (e.g., short URLs) have explicit size limits in a system?**  
A: Every component touching the network or database needs boundaries:
- **Inputs (Long URLs):** Need limits (e.g., 3 kilobytes) due to backend database storage constraints and to prevent payload abuse.
- **Outputs (Short URLs):** Need limits (e.g., 1 kilobyte or 1000 characters) to maintain their core functional purpose (being short and shareable) and to guarantee highly efficient index storage in high-velocity caches/databases.
