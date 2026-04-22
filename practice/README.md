# Practice Mock Interviews

This folder is your dedicated sandbox for raw, unpolished system design practice. The goal is not to write a perfect, textbook architecture document, but to simulate the exact pressure, pacing, and structure of an actual whiteboard interview.

## 🚀 How to use this folder

1. **Pick a Prompt:** Choose a system to design. *(e.g., "Design a URL Shortener", "Design Twitter", "Design WhatsApp")*
2. **Copy the Template:** Duplicate `template.md` and name it after your prompt *(e.g., `twitter-design.md`)*.
3. **Start the Clock:** Set a timer strictly for **45 minutes**. Time management is arguably the most common reason developers fail system design rounds.
4. **Think Out Loud (On Paper):** Don't just hold ideas in your head—write them down. Use the template to force yourself through the exact interview phases: Requirements, API Design, High-Level Architecture, and Deep Dives.
5. **Review and Reflect:** When the timer goes off, **stop designing**. Transition to reviewing your own work. Spend 10-15 minutes filling out the self-evaluation section. Did you forget a Load Balancer? Did you spend 20 minutes on APIs and run out of time? Identifying these flaws is how you improve.

## 💡 Key Insight
**Knowledge is useless without application.** You can read all the core concepts and memorize CAP theorem definitions perfectly, but until you practice pulling those concepts together under a time limit, you will struggle in a real interview. Building the muscle memory of *structuring* your answer is just as important as knowing the technical facts.

## 🎯 Recommended Practice Prompts
If you don't know where to start, try tackling these industry-standard systems:
- **Design a URL Shortener** (Focus: Database Scaling, Sharding, fast Caching)
- **Design a Twitter/Instagram Feed** (Focus: Heavy reads, Fan-out architecture, Eventual consistency)
- **Design WhatsApp/Discord** (Focus: Real-time WebSockets, Message Queues, Low latency)
- **Design a Web Crawler** (Focus: Distributed background processing, Fault tolerance)

## 🎤 Interview Tip
When practicing in this folder, literally **speak out loud** as you type or write your Mermaid diagrams. Getting completely comfortable vocalizing your engineering tradeoffs (*"I chose a NoSQL database here because..."*) will make you significantly more confident and eloquent when sitting across from an actual engineering manager.
