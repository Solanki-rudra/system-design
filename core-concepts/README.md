# Core Concepts

Welcome to the **Core Concepts** section! This directory focuses on the rules, principles, and evaluation metrics used in system design interviews and architecture planning.

## 📂 File Directory

- [`requirements.md`](./requirements.md) - The entry point for interview strategy. Explains how to gather Functional and Non-Functional requirements and properly scope a problem.
- [`functional-vs-nonfunctional.md`](./functional-vs-nonfunctional.md) - A deep dive into System Quality Attributes (Reliability, Observability, Security, Scalability, Performance).
- [`scoping.md`](./scoping.md) - Guides on clarifying constraints, estimating load (Users vs RPS), and understanding growth timelines.
- [`estimations.md`](./estimations.md) - Techniques for back-of-the-envelope calculations, including latency numbers every engineer should know and storage/bandwidth capacity planning.
- [`tradeoffs.md`](./tradeoffs.md) - Analyses of classic architectural dilemmas, such as Latency vs Throughput or Strict vs Eventual Data Consistency.

## 💡 Key Insights
- **Bad requirements = bad system.** Without scoping constraints first, you risk over-engineering a simple solution or under-provisioning a massive one.
- **Numbers drive design.** Mastering back-of-the-envelope math is essential to justify why you chose a specific database or caching strategy.
- Every architectural decision you make will eventually boil down to the performance and accuracy tradeoffs defined in these files.

## 🚀 How to Use
1. Read [`requirements.md`](./requirements.md) first to understand the interview flow.
2. Click through the non-functional requirements and tradeoff files to understand how to leverage system quality attributes.
3. Use [`estimations.md`](./estimations.md) to validate your architectural choices with concrete data.
4. Bring these scoping strategies into every practice interview you do!
