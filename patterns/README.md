# Patterns

Welcome to the **Patterns** section! This directory covers reusable architectural patterns — battle-tested solutions to problems that recur across different system types. Mastering these patterns gives you a toolkit for rapidly designing complex systems without reinventing the wheel.

## 📂 File Directory

Read in the following order for maximum benefit:

1. [`caching-patterns.md`](./caching-patterns.md) — Covers the four primary caching strategies: **Cache-Aside (Lazy Loading)**, **Write-Through**, **Write-Behind (Write-Back)**, and **Refresh-Ahead**. Each includes a Mermaid sequence diagram, workflow description, and trade-off analysis. Read `fundamentals/caching.md` first.
2. [`read-heavy-vs-write-heavy.md`](./read-heavy-vs-write-heavy.md) — Explains how to identify whether a system is read-heavy or write-heavy, and how that single insight shapes your entire architecture (caching, replication, database choice, queue usage). Includes real-world examples and CQRS pattern introduction.
3. [`system-flow.md`](./system-flow.md) — The most advanced file in this folder. Covers the full **Asynchronous Workflow Architecture** — why it exists, its four core components (Service, Message Broker, Queue, Worker Pool), the back-pressure safety mechanism, ideal use cases, independent scaling strategies, and the complexity trade-offs. Includes three Mermaid diagrams.

## 💡 Key Insights

- **Patterns are not prescriptions.** Every pattern has a context where it excels and a context where it fails. The goal is to understand the *trade-off*, not just memorize the diagram.
- **Caching patterns and async workflows appear in almost every real system design.** A video platform uses both: Cache-Aside for metadata reads and async workers for transcoding.
- **Read/write ratio is the single most important question to ask first.** It immediately narrows down which patterns apply and which databases to consider.
- Once you understand these patterns, you'll recognize them in `system-designs/video-streaming.md`, `system-designs/url-shortener.md`, and more.

## 🔗 Cross-References

| Pattern | Where it Appears in System Designs |
|:---|:---|
| Cache-Aside | `url-shortener.md` (metadata caching), `todo-app.md` |
| Write-Behind | `video-streaming.md` (view count aggregation) |
| Async Workflow + Worker Pool | `video-streaming.md` (transcoding pipeline) |
| Back Pressure | `video-streaming.md` (upload queue throttling) |
| Read Replicas (Read-Heavy) | `banking-system.md`, `todo-app.md` |

## 🚀 How to Use

1. Read `caching-patterns.md` first — it applies to nearly every system you'll design.
2. Identify whether your practice problem is read-heavy or write-heavy using `read-heavy-vs-write-heavy.md`.
3. If a design involves background jobs (video processing, email sending, report generation), use `system-flow.md` to design the async pipeline.
4. Always ask: *"Which pattern from this folder is relevant here?"* before jumping to architecture boxes.
