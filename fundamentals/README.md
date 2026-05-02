# Fundamentals

Welcome to the **Fundamentals** section! This directory contains the foundational concepts you need to understand before diving into large-scale system architecture. These building blocks appear in every system design interview and every real production system.

## 📂 File Directory

Read the files in the following recommended order:

1. [`what-is-system.md`](./what-is-system.md) — **Start here.** Defines what a system is, explains system boundaries (inputs, outputs, scope), and introduces the key insight that a system's identity comes from its tradeoffs — not its tech stack.
2. [`distributed-systems.md`](./distributed-systems.md) — Explains what happens when one server is no longer enough. Covers vertical scaling (Scaling Up) vs. horizontal scaling (Scaling Out), and why modern systems default to horizontal.
3. [`cap-theorem.md`](./cap-theorem.md) — Deep dive into the CAP Theorem (Consistency, Availability, Partition Tolerance). Covers CP vs AP system choices with real-world use cases (banking vs. social feeds). Includes a Mermaid decision diagram and a Q&A section.
4. [`caching.md`](./caching.md) — Explains what caching is, why it dramatically improves performance, the performance vs. freshness tradeoff, cache eviction policies (LRU, LFU, FIFO), TTL, and how CDNs are a form of distributed caching. Read before `patterns/caching-patterns.md`.
5. [`load-balancing.md`](./load-balancing.md) — Covers how load balancers distribute traffic, reverse proxies (Nginx, HAProxy), the three SSL/TLS termination strategies, the bottleneck shift phenomenon, and the security risks of logging unencrypted data.
6. [`communication-protocols.md`](./communication-protocols.md) — Explains how clients and servers communicate: HTTP/HTTPS request-response, WebSockets for real-time bi-directional streams, Server-Sent Events (SSE) for server push notifications, and how TLS encrypts data in transit.
7. [`databases.md`](./databases.md) — Comprehensive coverage of SQL vs NoSQL, database replication strategies (Primary-Replica, Primary-Primary, Transactional, Snapshot), partitioning, sharding, and the scaling sequence from a single DB to a fully sharded cluster.
8. [`authentication.md`](./authentication.md) — Deep-dive on authentication vs. authorization, session-based vs. token-based (JWT) auth, the OAuth 2.0 flow, microservice security patterns, and common attack vectors (CSRF, XSS, token theft).

## 💡 Key Insights

- You cannot understand massive system architectures (like Netflix or Uber) without first grasping these foundational building blocks.
- Real-world distributed systems are all about managing the tradeoffs between these basic components.
- **Order matters:** Understanding caching before databases, and databases before authentication, makes each subsequent file dramatically easier to absorb.
- Every component here will appear in at least one of the system designs in `system-designs/`. Use this directory as your reference when a concept comes up.

## 🚀 How to Use

1. Read the files in the numbered order listed above — each builds on the previous.
2. Ensure you are comfortable with the CAP theorem tradeoff before progressing to `core-concepts/`.
3. When reviewing `system-designs/`, return to these files to reinforce specific concepts (e.g., revisit `databases.md` when studying the video streaming design).
