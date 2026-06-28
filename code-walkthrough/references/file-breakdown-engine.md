# File Decomposition & Behavioral Profiling Engine

When executing the `Detailed File Breakdowns` loop for any individual file, the agent must avoid superficial descriptions. Use the following structured prompts and analysis vectors to extract deep architectural meaning.

### 1. Semantic Purpose Extraction (The "What")
* **Anti-Pattern to Avoid**: Do not simply restate the class, function, or file name in prose.
* **Analysis Rule**: Read the primary exports, public interfaces, or main execution blocks. Define the file's purpose by answering: *What system state does this file mutate, what data does it transform, or what interface does it expose to the rest of the application?*

### 2. Strategic Rationale Engineering (The "Why")
Force the agent to deduce the engineering intent behind the file's placement. Categorize the file into one of the following tactical roles:

| Detected Code Characteristics | Deduced Strategic Rationale |
| :--- | :--- |
| Contains third-party SDK calls or external API payloads | **Anti-Corruption Layer**: Isolates external data contracts from core business logic. |
| Implements streams, listeners, or state emitters | **Reactive State Management**: Ensures deterministic UI updates and uncouples presentation from logic. |
| Pure functions with primitive inputs and outputs | **Domain Utility Layer**: Centralizes stateless deterministic calculations to maximize test coverage. |
| Implements interfaces or abstract classes | **Inversion of Control**: Allows decoupled testing and runtime flexibility through dependency injection. |

### 3. Ecosystem Role & Data Flow Mapping
Map how data flows through this specific file using a telemetry-focused mental model:
* **Inbound Vectors**: Identify what initializes or passes data to this file (e.g., HTTP request routers, parent UI components, dependency injection containers).
* **Internal Mutations**: Identify if this file holds local state, mutates global state, or acts as a stateless conduit.
* **Outbound Vectors**: Document where the data goes next (e.g., writes to a local database, triggers network requests, pushes events to a message broker).

### 4. File-Level Diagram Triggers
Do not generate a diagram for every single file (this causes token bloat). Apply strict conditional logic to determine if a file gets an inline Mermaid diagram:

* 🟢 **TRIGGER DIAGRAM IF**:
  * The file acts as an orchestrator/coordinator calling $\ge 3$ external modules.
  * The file implements a complex state machine (e.g., auth lifecycle, file upload chunking).
  * The code uses non-trivial conditional branching logic (nested loops, heavy pattern matching).
* 🔴 **SKIP DIAGRAM IF**:
  * The file is primarily data structures, type definitions, DTOs, or models.
  * The file contains standard boilerplate configuration.
  * The file execution flow is strictly linear and under 50 lines of code.

### 5. Managing Complexity & Edge Cases
* **Spaghetti Code Guardrail**: If a single file exceeds 500 lines of code or mixes multiple concerns (e.g., business logic mixed directly with UI rendering), the agent must append a distinct `⚠️ Architectural Debt Warning` block, calling out the violation of the Single Responsibility Principle and suggesting a refactoring pathway.