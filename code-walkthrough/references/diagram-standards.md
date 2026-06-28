# Visual Topology & Diagram Standards

To ensure readable, renderable, and highly useful visual mappings, the agent must dynamically select the best Mermaid diagram type based on the context of the source code target:

### 1. Flowcharts (`graph TD` or `graph LR`)
* **When to use**: Complex branching logic, functional calculations, validation checks, or multi-step algorithms inside a single file/function.
* **Style**: Keep text inside nodes brief. Use standard shapes (diamonds for decisions, rectangles for actions).

### 2. Sequence Diagrams (`sequenceDiagram`)
* **When to use**: Multi-module coordination, async API fetches, middleware chains, or network operations spanning multiple files.
* **Style**: Explicitly show actors (e.g., Client, Controller, Database) and use clear activation markers (`activate`/`deactivate`).

### 3. State Diagrams (`stateDiagram-v2`)
* **When to use**: State machines, lifecycle hooks (e.g., Component Mount-to-Unmount, Connection Retries), or authentication flows.

### ⚠️ Execution Constraints
* Do not generate Class Diagrams unless specifically requested, as they tend to become bloated and unreadable for large source files.
* Ensure all Mermaid blocks are properly enclosed in triple backticks with the `mermaid` language identifier.