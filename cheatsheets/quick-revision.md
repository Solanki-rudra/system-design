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