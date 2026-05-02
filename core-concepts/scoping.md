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

## Understand the User Flow Before Touching the Architecture

One of the most common interview mistakes is jumping straight to boxes and arrows before understanding *how a user actually experiences the system*. The temptation is to start drawing architecture immediately because it feels productive, but without anchoring that diagram to a concrete user journey, you risk two critical errors:

1. **Overdesigning the wrong part:** You might build an elaborate real-time notification system for an action that happens once per session, while completely ignoring the critical upload path that happens millions of times per day.
2. **Missing entire components:** Without walking through the user flow step by step, entire services (like a notification service, a status polling mechanism, or a cleanup job for failed uploads) simply never occur to you.

### How to Apply This in Practice

Before drawing a single box, narrate the user's journey out loud:

> "A user opens the app and clicks Upload. They select a file. What happens? The file goes somewhere. Who confirms it arrived? What does the user see while it's processing? How do they know when it's done? What happens if they close the tab mid-upload?"

Each of these questions reveals a distinct architectural component or decision. The answers define your bottlenecks before you write a line of design. This is domain-driven scoping — understanding the *problem space* before the *solution space*.

**Q: Why is understanding the user flow the first step in system design, and what happens when you skip it?**
A: The user flow acts as a requirements discovery mechanism. By tracing what a user does step by step — from triggering an action to seeing a result — you surface every component the system must contain and every failure mode it must handle. Skipping this step causes two failure modes: you over-engineer components that don't matter (e.g., complex caching for data that changes once a day), and you completely miss components that are essential (e.g., the notification service that tells the user their upload completed). The user flow forces you to ask the right questions before committing to architecture.
