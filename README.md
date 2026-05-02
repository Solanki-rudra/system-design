# System Design Learning Repository

Welcome to the comprehensive System Design learning repository! This project is organized into structured modules to help you master large-scale architecture, from fundamental principles to advanced design patterns and interview strategies.

## 📂 Repository Structure

Navigate through the folders below to find notes, templates, and full system architectures.

### 1. [`fundamentals/`](./fundamentals/)
Contains the foundational building blocks you need to understand before diving into large-scale system design. These concepts underpin every architecture decision you'll ever make.
* **Topics:** What is a system & system boundaries, vertical vs. horizontal scaling, CAP Theorem (Consistency vs. Availability), caching fundamentals & eviction policies, load balancing & SSL/TLS termination, HTTP/WebSockets/SSE communication protocols, SQL vs. NoSQL database replication & sharding, and authentication/authorization (JWT, OAuth 2.0).

### 2. [`core-concepts/`](./core-concepts/)
Focuses on the core principles and evaluation metrics used in system design interviews and architecture planning. This is the methodology layer — it teaches you *how to think*, not just what components exist.
* **Topics:** Functional vs. Non-functional requirements gathering, back-of-the-envelope estimation (RPS, storage sizing), proper problem scoping & constraint definition, the critical importance of tracing the user flow, and deep dives into architectural tradeoffs (Latency vs. Throughput, Strict vs. Eventual Consistency, Performance vs. Security, and more).

### 3. [`patterns/`](./patterns/)
Covers reusable architectural patterns — battle-tested solutions to problems that recur across different system types. Mastering these gives you a toolkit to rapidly compose complex systems.
* **Topics:** Caching strategies (Cache-Aside, Write-Through, Write-Behind, Refresh-Ahead) with Mermaid sequence diagrams, Read-Heavy vs. Write-Heavy system optimization (including CQRS), and Asynchronous Workflow Architecture (Message Brokers, Worker Pools, Back Pressure, Dead-Letter Queues).

### 4. [`system-designs/`](./system-designs/)
Detailed, end-to-end architectures and step-by-step explanations of popular real-world systems. Each design bridges abstract theory and real-world engineering by showing how fundamentals and patterns combine at scale.
* **Examples:** URL Shortener (TinyURL/Bitly), Scalable Todo App, Banking & Transaction System, and Video Streaming Platform (YouTube/Netflix-style upload pipeline with async transcoding and CDN delivery).

### 5. [`interview/`](./interview/)
Resources tailored specifically for helping you succeed in high-pressure system design interviews. Bridges the gap between technical knowledge and effective communication.
* **Contents:** A systematic 5-phase approach template for structuring 45-minute whiteboard sessions (Clarification → Entities & APIs → High-Level Design → Deep Dive → Tradeoffs), and a comprehensive Q&A bank (`common-questions.md`) covering the most frequently asked topics organized by theme.

### 6. [`practice/`](./practice/)
A dedicated workspace for practicing system design problems using a structured, interview-realistic format.
* **Contents:** A richly detailed `template.md` that includes a time budget tracker, back-of-the-envelope estimation table, SPOF analysis, architecture decision log, and self-scoring checklist — ready to copy for each new problem you attempt.

### 7. [`cheatsheets/`](./cheatsheets/)
Quick reference guides to rapidly brush up on system design topics right before an interview or exam.
* **Contents:** Dense bulleted summaries of CAP theorem, caching, scaling heuristics, database types quick-reference table, common failure modes, latency numbers every engineer should know, and diagram drawing conventions (component labeling, CDN placement, microservice architecture patterns).


---

## 🗺️ Suggested Learning Path

| Phase | Focus Area | Goal |
| :--- | :--- | :--- |
| **Phase 1** | Fundamentals | Understand the "what" and "how" of distributed components. |
| **Phase 2** | Patterns | Learn to compose components into proven architectural solutions. |
| **Phase 3** | Core Concepts | Practice scoping and estimating scale requirements. |
| **Phase 4** | System Designs | Analyze complex systems by deconstructing them into patterns. |
| **Phase 5** | Interview Prep | Simulate interviews using the templates and cheat sheets. |

---

## 🚀 How to Use

1. **Build the Foundation**: Start with `fundamentals/` to get comfortable with the vocabulary (e.g., latency, throughput, partition tolerance).
2. **Master the Patterns**: Move to `patterns/` to learn how to solve recurring problems like service communication and state management.
3. **Refine Your Process**: Read `core-concepts/` to learn how to turn an abstract prompt into a concrete system requirement.
4. **Deconstruct Reality**: Study `system-designs/` to see how senior engineers design for millions of daily active users.
5. **Practice Iteratively**: Use the `practice/` templates to draft your own solutions before checking them against standard architectures.
6. **Last-Minute Review**: Use the `cheatsheets/` and `interview/` sections to polish your communication and recall key technical constraints right before your interviews.