# System Designs

Welcome to the **System Designs** section! This directory holds detailed, end-to-end architectures and step-by-step explanations of popular, real-world systems. Each design shows how fundamentals and patterns combine to solve real engineering problems at scale.

## 📂 File Directory

- [`url-shortener.md`](./url-shortener.md) — A thorough breakdown of designing a URL Shortening service (like TinyURL or Bitly). Covers hashing strategies (MD5, Base62), redirect latency, read-heavy optimization with caching, custom alias support, and link expiration cleanup. A great **first design** to study.
- [`todo-app.md`](./todo-app.md) — Designing a scalable task management application. Covers database schema design, CRUD API structure, the scaling sequence from a single DB to read replicas, caching with Redis, and worked back-of-the-envelope storage estimation.
- [`banking-system.md`](./banking-system.md) — Designing a high-consistency financial transaction system. Covers ACID transactions, CP system design (CAP theorem applied to finance), idempotency keys for safe payment retries, double-entry bookkeeping, and audit logging patterns.
- [`video-streaming.md`](./video-streaming.md) — The most complex design in this folder. Covers the full video upload pipeline (chunked upload → object storage → async transcoding → CDN delivery), worker pool architecture, stateless service design, DAG-based processing flows, adaptive bitrate streaming, and storage partitioning strategies. Multiple Mermaid diagrams included.

## 📊 Design Complexity Progression

Study designs in this recommended order — from simplest scope to most complex:

| Order | Design | Key Concepts Practiced |
|:----:|:---|:---|
| 1st | `url-shortener.md` | Hashing, caching, redirect optimization, TTL |
| 2nd | `todo-app.md` | Schema design, storage estimation, read replicas, caching |
| 3rd | `banking-system.md` | Consistency, ACID, idempotency, CP trade-offs |
| 4th | `video-streaming.md` | Async workers, CDN, object storage, DAG pipelines, back pressure |

## 💡 Key Insights

- Studying specific case studies bridges the gap between abstract theory and real-world implementation.
- Every popular system design combines basic load balancers, caching, and specific database choices uniquely suited to its core requirements.
- **Always try to design the system yourself before reading the file.** Open `practice/template.md`, set a 45-minute timer, and attempt the design. Then compare your solution to the reference here.
- Notice how the same components (Load Balancer, Cache, DB, Queue) appear in every design — only the *configuration* and *tradeoffs* differ.

## 🚀 How to Use

1. Follow the complexity progression table above — start with `url-shortener.md` and work up to `video-streaming.md`.
2. Before reading each design, attempt it yourself using `practice/template.md`.
3. After reading, identify which `fundamentals/` concepts and `patterns/` were applied in that design.
4. Use `interview/common-questions.md` to reinforce the Q&A for each specific system type.
