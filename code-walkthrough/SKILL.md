---
name: code-walkthrough
description: >-
  Provides a comprehensive, architectural walkthrough of a specific file, function, list of files, or an entire directory/folder. 
  Activates when the user requests an explanation, walkthrough, purpose analysis, or system context mapping for source code codeblocks or folders.
---

# Global Code Walkthrough & Directory Mapping Skill

This skill instructs the agent to inspect source code elements—ranging from single functions to entire recursive directory structures—and construct an operational, structural, and architectural map.

## 🛠️ Execution Pipeline

### Step 1: Target Identification & Scope Ingestion
* Determine if the target is a file, function, or directory.
* If a **directory/folder** is targeted, activate the processing rules outlined in `references/directory-traversal.md` to recursively map the directory tree without blowing past context limits.

### Step 2: Contextual & Dependency Mapping
* Read the codebase files matching the inclusion criteria.
* If analyzing a directory, construct an internal dependency map showing how files within this directory interact before generating individual file breakdowns.
* Consult `references/architectural-matrix.md` to map design trade-offs for high-impact files.

### Step 3: Progressive Output Synthesis
* Generate an overarching structural summary for the directory.
* Run individual file walkthroughs conditionally based on file relevance (skip boilerplate/generated files as defined in standard configurations).
* Format all diagrams using the rules in `references/diagram-standards.md`.

---

## 📋 Output Specification

The final response rendered to the user must strictly adhere to the following clean, scannable Markdown structure:

### 📁 Directory Architecture Walkthrough: `[Insert Folder Path]`

> **Scope Summary**: Analyzed `[X]` files across `[Y]` subdirectories dynamically filtering out ignored assets.

#### 1. High-Level Folder Purpose
*A macro-level summary of what this directory/module layer accomplishes as a cohesive unit within the larger codebase.*

#### 2. Directory Component Interaction Topology
*A system map showcasing how key files inside this directory pass data or depend on one another.*

[Insert a Mermaid Sequence or Flowchart diagram showing file interactions here]

---

### 🧩 Detailed File Breakdowns

*Loop through every relevant file discovered in the recursive pass. Evaluate each target against the processing rules defined in `references/file-breakdown-engine.md`:*

#### 📄 File: `[Relative Path/To/File.ext]`

#### 1. Core Purpose
[Execute Section 1 of the breakdown engine: Identify the state mutations or data transformations happening here.]

#### 2. Strategic Rationale
* **Tactical Role**: [e.g., Anti-Corruption Layer / Reactive State Conduit]
* **Systemic Necessity**: [Why the application requires this file to exist in this module layer.]

#### 3. Ecosystem Role & Telemetry
* 📥 **Inbound Triggers**: `[List components, hooks, or events that call this file]`
* ⚙️ **Internal State**: `[State mutations, cache updates, or local storage persistence details]`
* 📤 **Outbound Vectors**: `[Where data or control is passed next]`

#### 4. Architectural Trade-Offs (If Applicable)
> Consult `references/architectural-matrix.md` to populate this section.
* *Chosen Pattern*: `[e.g., Local-first drift syncing via repository design]`
* *Alternative Bypass*: `[Why this implementation was chosen over direct remote API polling]`

#### 5. Local Behavioral Topology
> Triggered conditionally based on the rules in `references/file-breakdown-engine.md`.
[If triggered, insert the custom Mermaid diagram here. If skipped, omit this subsection entirely.]