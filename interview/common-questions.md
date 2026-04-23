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

---

## Distributed Systems & Scaling

**Q: What are the two main types of scaling discussed for distributed systems, and how do they fundamentally differ?**
**A:** The two main types are **Vertical Scaling** (scaling *up*) and **Horizontal Scaling** (scaling *out*). Vertical scaling is achieved by adding more raw physical power (CPU, RAM, GPU, Disk) to an existing server. Horizontal scaling is achieved by provisioning additional standard machines and distributing the load across them.

**Q: What is a key advantage of vertical scaling compared to horizontal scaling?**
**A:** The primary advantage of vertical scaling is **absolute simplicity**. It typically requires zero architectural modifications and no code changes. You simply upgrade the hardware or instance size, and your application runs faster without the overhead of learning or managing distributed orchestration.

**Q: What is the primary limitation and resiliency risk associated with vertical scaling?**
**A:** The main limitation is the **strict physical hardware limit**—there is only so much computational power you can pack into a single machine. The glaring resiliency risk is the **Single Point of Failure (SPOF)**. By putting all your eggs in one massive server basket, a single hardware failure instantly takes down a massive portion (or all) of your available capacity.

**Q: What is a significant trade-off introduced by horizontal scaling?**
**A:** While horizontal scaling provides infinite capacity and high fault-tolerance, it introduces massive **architectural complexity and operational overhead**. Because the system is now distributed across many discrete machines, you must introduce and manage sophisticated orchestration tools, load balancers, and distributed messaging systems (like Apache ZooKeeper or Kafka) to ensure state remains synchronized continuously.

**Q: What are the key advantages of horizontal scaling compared to vertical scaling?**
**A:** Horizontal scaling is natively easier to dynamically auto-scale up or down to perfectly match traffic demand. Critically, it provides vastly superior **high availability**; if a server crashes, it's far safer and less impactful to lose 1 server out of an array of 100 than to lose 1 server out of a tiny pool of 5 massive machines. 

**Q: Why do modern distributed systems typically default to horizontal scaling from day one rather than starting vertically and transitioning later?**
**A:** Defaulting to horizontal scaling strictly prevents catastrophic **code refactoring** down the line. If you build a vertical monolith, internal components communicate via simple, instantaneous in-memory function calls. When you are eventually forced to transition to a horizontal architecture, those components are split across different physical machines. Suddenly, instant in-memory calls become network API calls subject to latency, timeouts, and partial network failures, requiring an immensely expensive total architectural rewrite.

---

## Load Balancing & Network Traffic

**Q: What is the central challenge that arises the moment you scale from one server to multiple backend servers, and how is it solved?**
**A:** The main challenge is **traffic distribution and discovery**—specifically, how does the client application know which exact server IP to connect to, what happens if that server goes offline, and how are DNS updates handled. The solution is placing a **Load Balancer** directly between the clients and the backend servers. The load balancer provides a single public entry point, actively monitors backend server health, and intelligently routes traffic across the active server pool.

**Q: In system design, what is the technical distinction between a dedicated Load Balancer and a Reverse Proxy, and which is generally preferred for web architectures?**
**A:** A dedicated load balancer (like AWS ELB) focuses purely on distributing immense volumes of raw network traffic. A **Reverse Proxy** (like Nginx or HAProxy) absolutely acts as a load balancer but also offers deep application-level flexibility. Reverse proxies can handle HTTPS termination (decrypting traffic at the edge), route-based routing (sending `/images` to one server and `/api` to another), and edge caching. For most modern web applications, a reverse proxy acting as a load balancer is the preferred, highly flexible choice.

**Q: When horizontally scaling an application footprint from one server to three servers behind a load balancer, what unexpected bottleneck frequently emerges?**
**A:** The **Database Tier**. When operating on a single application server, the server's limited CPU or RAM usually acts as an artificial mask over downstream limits. When you horizontally scale the application compute servers, you successfully un-bottleneck the traffic, instantly pushing the full, massive volume of requests directly downstream. Engineers frequently discover that the database is now the *true* crippling performance constraint of the entire system.

---

## Architecture Diagramming & Conventions

**Q: In a system architecture diagram, what component should universally be placed at the edge to distribute incoming traffic?**
**A:** A **Load Balancer** (or Reverse Proxy) should always be placed at the edge of the architecture immediately receiving traffic from the internet client, specifically to intelligently route that external traffic across your cluster of internal servers.

**Q: Are load balancers strictly placed at the "edge" confronting incoming internet traffic?**
**A:** No. While edge placement is standard for handling client requests, Load Balancers frequently sit deeply internal to an architecture (e.g., positioned between application servers and a cluster of multiple database nodes) to handle distinct, internal network load routing.

**Q: How can you quickly and cleanly represent a massive server cluster (e.g., 50 instances) on a whiteboard without drawing every single machine?**
**A:** **The Container Convention**: You draw a single large "Container" or "Cluster" boundary box, and sketch just 2 or 3 small server nodes inside it (using implicit numbering or ellipses). This serves as universal shorthand indicating a horizontally scaled pool of servers without needlessly cluttering the whiteboard diagram.

**Q: When sketching a read-heavy application handling 100,000+ users, what must you add to the diagram to protect system performance?**
**A:** You must draw an **In-Memory Cache** (like Redis) positioned explicitly *in front* of the Database. The application servers must be drawn routing to the Cache first to check for data, falling back to read from the Database only on a cache-miss.

**Q: What simple labeling practice separates an average architecture diagram from an elite system design communication tool?**
**A:** **Labeling the *Purpose***, not just the component name. Instead of merely labeling a generic box "Load Balancer" or "Web Server," you should explicitly clarify what it is actively doing (e.g., *"Load Balancer (TLS Termination & Routing)"* or *"Web Server (Core Business Logic)"*). This instantly explains the system's runtime flow without requiring verbal explanation.
