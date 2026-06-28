# Architectural Evaluation Framework

When executing step 4 of the code walkthrough, evaluate the code implementation against standard software engineering trade-offs. Look for markers belonging to these common paradigms:

| Pattern / Strategy | Common Discarded Alternative | Primary Driver for Selection |
| :--- | :--- | :--- |
| **Declarative UI State** | Imperative UI Mutations | Reduced side effects, single source of truth. |
| **Dependency Injection** | Hardcoded Singletons | Testability, decoupled module lifetimes. |
| **Repository Pattern** | Direct Inline DB Queries | High data-layer abstraction, easily swappable caching layers. |
| **Async Streams/WebSockets** | HTTP Polling | Lower network overhead, real-time fidelity. |

### Probing Rules
1. **Boilerplate Exemption**: If the file contains only basic models, type definitions, or standard configurations, skip the alternative comparison entirely.
2. **Code Smells**: If the current approach seems subpar, gently point it out in the Trade-Offs section as a prospective optimization path instead of inventing a historic design justification.