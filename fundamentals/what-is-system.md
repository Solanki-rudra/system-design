# What is a System?

## Definition
At its core, a system is a collection of clearly defined, interconnected components that work together to achieve a specific overarching goal or solve a specific problem. In software engineering, this usually means taking data in, processing or storing it, and returning a predictable result.

## Core Characteristics
Every system, no matter how massively distributed or complex, can be conceptually broken down into three fundamental attributes:
1. **Input**: The information, requests, or events feeding into the system. *(e.g., A user clicking "Purchase", an IoT device sending a temperature reading.)*
2. **Output**: The result produced by the system after processing the input. *(e.g., An order confirmation email is sent, an alert is triggered on a dashboard.)*
3. **Boundaries**: The invisible line that separates your system from its environment. It defines exactly what your system is responsible for managing, and what it relies on external external providers for.

## Why Boundaries Matter
Defining boundaries is arguably the most important step before designing any architecture:
- **Prevents Scope Creep**: It answers the question, "Are we building an entire E-commerce platform, or just the inventory management service?"
- **Keeps the Problem Finite**: You cannot build or solve everything at once. Boundaries let you focus on the immediate requirements.
- **Identifies Dependencies**: It forces you to clarify what your system *consumes* or interacts with (like a 3rd party Stripe payment gateway API) versus what it completely *owns* (like the user profiles database).

## Key Insight
**The same underlying components do not equal the same system.** Two companies might both use React, Node.js, Redis, and PostgreSQL. However, one might be building a low-latency, high-frequency trading platform, while the other is building a read-heavy blog platform. A system's identity is defined by *how* its components interact and the engineering tradeoffs made to solve a specific problem, not just its tech stack.

## Interview Tip
**Always define your system boundaries early.** Before drawing any boxes on the whiteboard, establish exactly what is "in scope" for the 45-minute interview. 

For example, if an interviewer asks you to "Design YouTube":
- You should ask: *"Are we designing the entire platform encompassing video encoding algorithms, the comments section, and the recommendation feed? Or are we strictly scoping this to the core system of uploading and streaming videos?"*
- Clarifying this immediately prevents you from wasting 20 minutes designing an AI Recommendation Engine when the interviewer only cared about how you store large video files.