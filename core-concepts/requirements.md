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

## 3. The Grey Area
Sometimes, a requirement heavily influences both functional user behavior and architectural non-functional constraints.

**Q: Is "URL expiration" typically considered a functional or non-functional requirement in a URL shortener system, and why?**  
A: URL expiration bridges both categories:
- **Functional:** It explicitly dictates feature behavior (a user clicking an expired link experiences a 404 or a 'Link Expired' page instead of a redirect).
- **Non-Functional:** It fundamentally alters system storage, maintenance, and predictability. If URLs *don't* expire, the system's database will grow infinitely, requiring massive storage scaling. If they *do* expire, the architecture must include an automated cleanup/garbage-collection process (e.g., a background cron job) to purge stale data and free up capacity.

## 4. Translating Requirements to APIs

A critical rule of thumb in system design is that **API design should be a literal translation of your business requirements into code.**

*   If you can successfully articulate and convert your functional and non-functional requirements into a tangible list of API endpoints, you have essentially solved the core of a simple application.
*   **The Power of Visualization:** Explicitly listing out all endpoints helps visualize the actual scope of the system. You will frequently find that seemingly overwhelmingly complex functional requirements actually boil down to just two or three predictable endpoints, drastically simplifying how you mentally reason about the architecture.

## Key Insight
**Bad requirements = bad system.** An architecture perfectly built for the wrong problem is still a failed system. Clarifying requirements sets the boundaries and tells you whether you need a simple monolithic database or a complex, globally distributed architecture.

## Interview Tip
Spend the first 10–15 minutes of the interview **ONLY** on gathering requirements and scoping the problem. Do not start drawing architecture boxes too early. If you make assumptions without verifying them with the interviewer, you risk solving the wrong problem entirely.