# Communication Protocols in System Design

Understanding how different components of a system communicate is foundational to designing efficient, scalable, and responsive application architectures. Below is a deep dive into the primary protocols and paradigms used in modern web and distributed systems.

## 1. HTTP (HyperText Transfer Protocol)

The backbone of web communication.

*   **Stateless Nature**: HTTP is inherently stateless. Every transaction or request is entirely independent and brand new to the server. The server does not remember previous interactions inherently.
*   **The Challenge**: For modern web applications that require state (e.g., user sessions, shopping carts), developers must explicitly provide context and metadata (such as cookies, session IDs, or JWTs) with every request to maintain continuity.

## 2. WebSockets

When you need real-time, bidirectional communication between a client and a server.

*   **Bidirectional & Persistent**: Unlike HTTP's request-response model, WebSockets establish a persistent connection. Both the client and the server can send messages to each other at any time.
*   **Stateful Connection**: The connection stays open and "remembers" the context, making it stateful.
*   **Low Latency**: Because the connection remains open, it avoids the overhead of establishing a new handshake for every message. This makes it ideal for low-latency applications like chat apps, multiplayer games, or live collaborative editors (like Google Docs).
*   **Mobile Considerations**: WebSockets can heavily drain battery life on mobile devices because they constantly ping the backend to keep the connection alive (heartbeats). Standard HTTP polling can sometimes be a better middle-ground for mobile applications if updates are infrequent.

### Stateful vs. Stateless: The Scaling Implications of WebSockets

The persistent nature of a WebSocket connection introduces a fundamental architectural constraint: **statefulness**. Understanding this distinction is essential for reasoning about system scalability.

*   **Stateless Architecture (e.g., HTTP/REST):** Every request is entirely self-contained and independent. The server retains absolutely no memory of previous interactions. Any server in a horizontally scaled pool can handle any incoming request because no prior context is required. This means you can freely add or remove servers behind a load balancer without disrupting any client—scaling is essentially frictionless.

*   **Stateful Architecture (e.g., WebSockets):** The server *must* maintain an active, in-memory record of each open connection and its associated context. The connection is "pinned" to a specific server instance. This creates a tight coupling between the client and the particular machine it connected to.

**Why Statefulness Cripples Horizontal Scaling:**

When you need to scale a stateful system (adding new servers or decommissioning old ones), you face a brutal challenge: **connection migration**. Every open WebSocket connection is a live, in-memory session bound to a specific server. You cannot simply kill that server; you must gracefully migrate every one of those active connections to a new server without the user noticing a disruption. This is operationally expensive and architecturally complex, often requiring sticky sessions, connection draining strategies, or distributed session stores.

In contrast, stateless services can be scaled elastically with zero migration overhead—spin up a new instance, point the load balancer at it, and it immediately starts serving traffic.

```mermaid
graph LR
    subgraph Stateless["Stateless (HTTP/REST)"]
        C1[Client] -->|Any Request| LB1[Load Balancer]
        LB1 --> S1[Server A]
        LB1 --> S2[Server B]
        LB1 --> S3[Server C - NEW]
        style S3 fill:#2d6a4f,stroke:#40916c,color:#fff
    end

    subgraph Stateful["Stateful (WebSockets)"]
        C2[Client] -.->|Pinned Connection| S4[Server X]
        S4 -. "Migration<br/>Required" .-> S5[Server Y - NEW]
        style S5 fill:#9d0208,stroke:#d00000,color:#fff
    end
```

> **Key Takeaway:** This is precisely why Server-Sent Events (SSE) are often preferred over WebSockets when bidirectional communication isn't strictly required. SSE operates over standard HTTP, preserving the stateless, easily scalable nature of the architecture while still enabling real-time server-to-client data push.

## 3. Server-Sent Events (SSE)

An alternative to WebSockets for specific use cases where communication is heavily one-sided.

*   **One-Way Communication**: Server-Sent Events provide a unidirectional channel from the server to the client.
*   **Use Cases**: Perfect for scenarios where the client only needs to receive continuous updates without sending much back—such as live news feeds, stock tickers, or social media timelines.
*   **Versus WebSockets**: If you only need to *push* events to the client and don't care about the client talking back over the same channel, SSE is simpler to implement and runs over standard HTTP, avoiding the complexity of WebSocket connection management.

## 4. gRPC (gRPC Remote Procedure Calls)

A high-performance, open-source universal RPC framework.

*   **Service-to-Service Focus**: Generally, gRPC is the preferred choice for internal, service-to-service communication (backend-to-backend) rather than browser-to-backend.
*   **Why Not Browsers?**: Browsers do not support gRPC natively. Using it requires an additional library or proxy (like gRPC-Web) to interpret the traffic. 
*   **Under the Hood**: It operates exclusively over HTTP/2 and uses Protocol Buffers (Protobufs) instead of JSON. Protobufs are binary, strongly-typed, and highly compressed, making them exceptionally efficient but not human-readable (unlike JSON over standard HTTP).

## 5. GraphQL vs. REST

Modern approaches to structuring API endpoints.

| Feature | REST | GraphQL |
| :--- | :--- | :--- |
| **Data Fetching** | Typically requires multiple requests to separate, distinct endpoints to gather related data. | Can fetch data from multiple underlying sources in a single query. |
| **Efficiency** | Often suffers from over-fetching or under-fetching of data. | Client requests exactly the data it needs, no more, no less. |
| **Use Case Advantage** | Simple, standard architecture leveraging HTTP verbs effectively. | Excellent for complex dashboards (e.g., fetching a user profile, recent news feeds, pictures, and ads simultaneously) under what appears to the client as a single unified endpoint. |

### Why REST is Preferred for Critical APIs (e.g., Banking)

While GraphQL offers excellent flexibility for dynamic frontends, **REST** remains the gold standard for robust, high-stakes infrastructure like banking systems:
*   **Predictable Load**: In GraphQL, clients construct the queries, posing a risk of deeply nested queries maliciously or accidentally overloading the server. REST endpoints return strictly predefined payloads.
*   **Reliability & Simplicity**: REST relies on standard HTTP methods (GET, POST) and status codes. Its simplicity means a lower learning curve, rock-solid reliability, and broad compatibility for third-party integrations.
*   **Native Caching**: REST natively leverages HTTP caching at the network level (CDNs, reverse proxies). GraphQL caching is notoriously complex since queries typically hit a single `POST` endpoint.

## Communication Protocols Taxonomy

```mermaid
graph TD
    A[Communication Approaches]
    A --> B[Traditional Client-Server]
    A --> C[Real-Time / Event-Driven]
    A --> D[Internal / Microservices]
    
    B --> E[HTTP / REST]
    B --> F[GraphQL]
    
    C --> G[WebSockets <br/> Bidirectional]
    C --> H[Server-Sent Events <br/> Unidirectional Server -> Client]
    
    D --> I[gRPC <br/> HTTP/2 + Protobufs]
```

## Protocol Decision Tree

```mermaid
flowchart TD
    M1[Is this internal service-to-service communication?] --> |Yes| gRPC{Consider gRPC}
    M1 --> |No| M2[Do you need real-time updates?]
    M2 --> |Yes| M3[Do you need bi-directional communication?]
    M2 --> |No| M4[Do you have complex data from many sources?]
    M3 --> |Yes| WbSckt{WebSockets}
    M3 --> |No| SSE{Server-Sent Events}
    M4 --> |Yes| GQL{GraphQL}
    M4 --> |No| REST{REST}
```
