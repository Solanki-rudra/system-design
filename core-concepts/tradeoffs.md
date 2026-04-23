# System Trade-Offs

When designing a system, there is rarely a perfect choice. Every major architectural decision involves a trade-off.

## Performance: Latency vs Throughput

A classic trade-off in distributed systems is choosing whether to optimize for speed (Latency) or volume (Throughput). 

**Q: In the context of system performance, what is the difference between latency and throughput, and when is each more important?**
A: **Latency** is how quickly a system responds to a request, while **throughput** is the amount of data a system can handle over a given time. 
- *Latency* is more important for applications like shopping carts where users expect immediate feedback and will abandon slow-loading pages. 
- *Throughput* is more important for applications handling massive data volumes, such as video streaming, real-time mapping, autonomous cars, or AI models processing millions of data points.

## Data Consistency: Strict vs Eventual

Determining exactly how consistent your data needs to be is one of the most critical tradeoffs you will make. It directly dictates what kind of database you use and how you handle replication.

- **Strict Consistency (Critical):** In a stock market trading platform, money is constantly at stake. Transactions *must* depend on perfectly accurate, globally synchronized stock prices. In this scenario, consistency must be tightly prioritized over absolute availability. A user would rather see an error than make a trade based on an outdated price.
- **Eventual Consistency (Acceptable):** In contrast, an application like a news homepage or a social media feed can comfortably tolerate slightly outdated data. If a user sees a news headline or a 'like' count that is a few minutes delayed, it does not significantly impact their core user experience. Here, keeping the system highly available and responsive is far more important than strict, blocking synchronization.

## Performance vs Security

Security measures inherently introduce latency into a system. Every safeguard, validation step, or encryption cycle requires computational overhead. 

Consider the requirement of **HIPAA compliance** for handling medical data. HIPAA mandates that all sensitive data must be encrypted at rest and in transit. This means the database CPU must execute rigorous encryption and decryption cycles for every single read and write request, fundamentally slowing down the baseline performance. Furthermore, when these encrypted databases need to synchronize or communicate with external microservices, they are forced to perform extensive security handshakes, compounding network latency and architectural complexity. While legally and ethically vital, these safeguards stand as a direct tradeoff against raw system speed.

**Q: What is the tradeoff when implementing end-to-end encryption for transaction data in a microservices architecture?**
A: Normally, HTTPS traffic is decrypted at the API gateway or edge to make processing faster within the internal network and avoid computational overhead. However, if strict data protection requires data to be encrypted in-transit throughout the *entire* system until it reaches the database (end-to-end encryption), every single microservice must decrypt the data to process it. This is computationally expensive, significantly increasing latency and impacting overall system performance.

## Simplicity vs. Complexity (Over-engineering)

A guiding principle in system design should always be: **focus on the simplest, cheapest thing that solves the problem.** It is a common pitfall to automatically reach for the most rigorous or seemingly state-of-the-art architectural patterns when a simpler approach would suffice.

### Endpoint Granularity: Combined vs. Separate
When designing an API, you frequently face the tradeoff of whether to create separate endpoints for different operations or to lump them together into a single, unified endpoint:
*   **The Expense Tradeoff:** The decision often hinges on the complexity and computational expense of the underlying operation. For example, if an operation involves an expensive, heavy calculation (like aggregating and comparing multiple historical stock ticker arrays), it almost always warrants its own dedicated endpoint. Lumping it together with an otherwise lightweight operation (like fetching a user's display name) forces the entire system to wait on the slowest component, driving up aggregate latency and wasting compute resources if the client only needed the display name.

### The GraphQL Trap
**Q: When designing a system's API, when might you explicitly avoid using a sophisticated solution like GraphQL?**
**A:** You should avoid GraphQL when the API is simple enough that it does not merit the added structural complexity. If a system only needs a few straightforward endpoints to execute basic CRUD operations, a simple REST-based HTTP approach is far cheaper, easier to maintain, and natively supports edge-caching without the heavy backend resolvers, schemas, and performance tuning that GraphQL inherently requires.
