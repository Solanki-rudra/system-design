# System Design Approach

## 1. Clarify Requirements (10–15 min)
- Users?
- Scale?
- Read vs Write?
- Constraints?

## 2. Define Core Entities

## 3. High-Level Design
Draw the core architecture:

```mermaid
flowchart LR
    Client([Client]) --> LB{Load Balancer}
    LB --> Server[App Server]
    Server --> DB[(Database)]
    Server -.-> Cache[(Cache)]
```

## 4. Deep Dive
- DB schema
- APIs
- Scaling

## 5. Tradeoffs
- CAP
- Cost vs performance

## 6. Bottlenecks + Improvements