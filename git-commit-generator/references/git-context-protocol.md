# Git Context Discovery & Telemetry Protocol

This reference module codifies exactly how the AI Agent interacts with the host shell environment to extract repository status. It ensures the agent gathers maximum context with minimum token consumption, gracefully handling empty states, massive diffs, and mixed staging areas.

---

## 🧭 Operational Pipeline Overview

The agent must execute discovery commands in strict hierarchical phases. Do not run heavy diff extraction commands before assessing file-change telemetry.

```text
  [Phase 1: Status Baseline]  --> Inspect file changes & tracking states
               │
               ▼
  [Phase 2: Strategy Select]  --> Route based on Staged vs. Unstaged content
               │
               ▼
  [Phase 3: Diff Extraction]  --> Run token-safe diff checks
               │
               ▼
  [Phase 4: Metadata Ingest]  --> Check active branch or ticket contexts
```
