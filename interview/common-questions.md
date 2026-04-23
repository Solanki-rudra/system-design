# Common Interview Questions

This document compiles common architectural scenarios, conceptual trade-offs, and behavioral questions frequently encountered in system design interviews.

## Communication Protocols & Networking

**Q: What is a key characteristic of HTTP that makes web programming challenging for developers coming from stateful programming environments?**

**A:** HTTP is inherently stateless. This means every transaction or request over the internet is treated as entirely brand new. The server doesn't remember previous interactions inherently. To maintain a session (like keeping a user logged in), the client must explicitly provide context and metadata (such as authentication tokens or session cookies) with each request.

---

**Q: What are the main advantages of WebSockets over HTTP for certain use cases?**

**A:** WebSockets provide continuous, *bidirectional* communication over a persistent connection. Because they reuse the connection, they are stateful (can remember previous context within the session) and offer very low latency. This is because they bypass the need to repeatedly negotiate handshakes for every individual message, unlike standard HTTP request-response cycles. They are ideal for real-time applications like chat or live trading.

---

**Q: Why is gRPC typically not used for browser-to-backend communication, and where does it shine?**

**A:** Browsers do not natively support gRPC; they require an intermediary library or proxy (like gRPC-Web) to translate the calls. Furthermore, gRPC relies strictly on HTTP/2 and uses Protocol Buffers—a binary serialization format that is highly efficient but not human-readable. Because of these constraints, gRPC is much better suited for high-throughput, low-latency *service-to-service* (backend-to-backend) communication within the data center.

---

**Q: What is a primary structural advantage of GraphQL over REST when dealing with highly fragmented data sources?**

**A:** GraphQL allows clients to fetch highly nested or related data from multiple disparate sources (e.g., a news feed, ad servers, and user image galleries) using a *single request* to what appears as one unified endpoint. REST, in contrast, often requires multiple separate round-trips to distinct dedicated endpoints to gather the same combined dataset, which can lead to over-fetching or under-fetching.

---

**Q: What is the main difference between Server-Sent Events (SSE) and WebSockets in terms of communication direction?**

**A:** Server-Sent Events are designed for *one-way* communication, strictly from the server down to the client. This makes them ideal for scenarios where the server pushes continuous updates (like a live news feed or a scrolling stock ticker) and doesn't need the client to respond over that stream. WebSockets, conversely, offer fully *bidirectional* communication, allowing continuous message streams back and forth simultaneously.

---

**Q: What communication technology is ideally suited for a stock trading dashboard showing live price updates?**

**A:** **Server-Sent Events (SSE)** are highly recommended for this scenario. They provide an efficient, one-directional, real-time data push from the server down to the client. Since a stock dashboard primarily *consumes* updates and rarely needs to send high-frequency responses back to the backend, SSE is perfectly matched to the requirement without the complexity of WebSockets.

---

**Q: In building a collaborative document editor like Google Docs, which communication technology should be used and why?**

**A:** **WebSockets** should be the primary choice. Collaborative document editing relies heavily on concurrent, high-frequency, *bidirectional* communication. As users type, those changes must instantly flow up to the server, and edits from other users must instantly flow down. WebSockets provide the continuous, stateful, and low-latency connection required to make that user experience seamless.

---

**Q: What is a significant downside of using WebSockets on mobile devices, and how can it be mitigated?**

**A:** WebSockets consume **battery life quickly** on mobile devices. Because a WebSocket connection is persistent, it constantly exchanges "heartbeat" pings with the backend to prevent the connection from actively timing out, which prevents the device's network radio from entering a low-power state. If extreme real-time bidirectional latency isn't strictly necessary, standard HTTP polling can serve as a better, battery-friendly middle ground.

---

**Q: Why is standard REST typically recommended over GraphQL for core APIs in secure environments like banking systems?**

**A:** Banking infrastructure requires maximum reliability, predictability, and broad compatibility across multiple client types (e.g., mobile apps, diverse third-party integrations). **REST** relies on standard, well-tested HTTP methods and allows servers to strictly define and lock down exactly what data each endpoint returns. This eliminates risks associated with GraphQL, such as unpredictable server load triggered by highly complex, deeply nested query requests from external clients. Furthermore, REST natively leverages built-in network-level caching, making it rock solid and extremely scalable.

---

**Q: For a food delivery application aiming to show a driver's live location, what factors dictate the choice between HTTP polling and WebSockets?**

**A:** The decision relies on **update frequency** and **directionality requirements**. 
*   If you only need updates every few seconds, standard **HTTP polling** is more than sufficient and much easier to scale. 
*   If immediate, sub-second continuous tracking is required, **WebSockets** or persistent connections are more appropriate. 
*   *Optimization Strategy*: Given directionality, since the driver is only *writing* their location and the customer is only *reading* it, a highly optimal design decouples these flows: the driver's app safely writes updates using standard HTTP to the server, while the customer's phone *receives* that live stream utilizing **Server-Sent Events (SSE)**.

---

## API Design & Architecture Principles

**Q: Multiple Choice: When is GraphQL typically cited as a potential API solution, and what is the underlying architectural recommendation?**
1. It is required when handling more than two endpoints.
2. It eliminates the need for proper API design planning.
3. It should be used by default for all modern API designs.
**4. It provides one unified endpoint but may not merit the added backend complexity.**

**A:** The correct answer is **4**. While GraphQL's defining feature is that it exposes a single unified endpoint (preventing the frontend from needing to make dozens of separate REST requests), this comes at a steep backend cost. You must maintain complex type schemas, specialized data resolvers, and custom caching strategies. If your application's data needs are relatively simple (e.g., basic functional requirements needing just a few straightforward REST endpoints), introducing GraphQL is essentially an *over-engineering* anti-pattern.

---

**Q: Why is explicitly listing out all API endpoints a critical step after gathering system requirements?**
**A:** API design should be a **literal translation of business requirements into code**. Explicitly listing out all endpoints helps visualize the actual technical scope of the system. In many cases, it reveals that seemingly overwhelmingly complex functional requirements actually boil down to just two or three predictable endpoints, drastically simplifying how you mentally reason about the architecture.
