# Requirements in System Design

## 1. Functional Requirements (What the system should do)
These describe the specific behaviors, features, and core use cases of the system. Without these, the system does not solve the user's core problem.
- **Features**: What explicit abilities does the product have? *(e.g., Users can upload photos, Users can like posts.)*
- **User actions**: How do users interact with the system? *(e.g., Search functionality, feed generation, user registration.)*
- **Use cases**: The specific flows the system must support.

*Example (For a URL Shortener)*: 
- Given a long URL, return a short URL. 
- When a user clicks a short URL, redirect them to the original long URL.

## 2. Non-Functional Requirements (How the system should behave)
These dictate the quality, scale, constraints, and operational attributes of the system. In system design, non-functional requirements almost exclusively drive your architectural tradeoffs.

Rather than specific features, NFRs target overall **System Quality Attributes** (like Availability, Latency, or Security). 
*(See [`functional-vs-nonfunctional.md`](./functional-vs-nonfunctional.md) for a deep dive into the 5 key pillars of system quality).*

## Key Insight
**Bad requirements = bad system.** An architecture perfectly built for the wrong problem is still a failed system. Clarifying requirements sets the boundaries and tells you whether you need a simple monolithic database or a complex, globally distributed architecture.

## Interview Tip
Spend the first 10–15 minutes of the interview **ONLY** on gathering requirements and scoping the problem. Do not start drawing architecture boxes too early. If you make assumptions without verifying them with the interviewer, you risk solving the wrong problem entirely.