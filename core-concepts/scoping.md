# Scoping the System

Before you design any architecture, you must properly scope the problem by clarifying constraints, traffic, and user expectations. Making assumptions here leads to over-engineering or under-provisioning.

## Estimating Load: Users vs RPS

When gathering requirements, it is a common pitfall to solely focus on the total number of expected users. While user count is helpful, it is rarely the most critical metric for system design. Instead, you must determine the **Requests Per Second (RPS)**. 

For example, a service like a URL shortener might not have millions of registered, logged-in users, but its core redirect functionality experiences massive traffic volumes whenever a popular link is clicked. Understanding specific traffic patterns and request volume spikes allows you to adequately scale the system's throughput to handle the *actual load*, rather than over-provisioning based on raw user accounts.

## Projected Growth Timeline

When discussing expected scale, product teams tend to provide their maximum expected user count for the product's entire lifetime. However, this peak metric may not be reached until years after the initial launch. 

Designing a Day-1 architecture equipped to handle a Year-5 peak load results in massively over-scaled systems, accumulating unnecessary cloud infrastructure costs early on. It is crucial to explicitly ask for the timeline and expected growth patterns. This knowledge empowers you to design elastic systems that can scale up gracefully alongside actual traffic growth, and seamlessly scale down during seasons or hours of low engagement.
