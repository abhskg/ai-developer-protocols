---
name: code-walkthrough
description: >-
  Provides a comprehensive, architectural walkthrough of a specific file, function, list of files, or an entire directory/folder. 
  Activates when the user requests an explanation, walkthrough, purpose analysis, or system context mapping for source code codeblocks or folders.
---

# Global Code Walkthrough & Directory Mapping Skill

This skill instructs the agent to inspect source code elements—ranging from single functions to entire recursive directory structures—and construct an operational, structural, and architectural map.

All output is written to markdown files inside a `.walkthrough/` folder rather than dumped into the chat window. Only a short summary/confirmation message is sent to the chat.

---

## 🛠️ Execution Pipeline

### Step 1: Target Identification, Scope Ingestion & Framework Detection
* Determine if the target is a **file**, **function**, or **directory**.
* If a **directory/folder** is targeted:
  1. **Run Phase 0** of `references/directory-traversal.md`: scan the project root for manifest files (`package.json`, `pubspec.yaml`, `pom.xml`, `Cargo.toml`, `go.mod`, `*.csproj`, etc.) to identify active ecosystems.
  2. **Activate the matching exclusion blocks** from Phase 1 of `references/directory-traversal.md` (universal + ecosystem-specific + generated-file heuristics).
  3. Only then begin recursive traversal, skipping all excluded paths.
* Never read or index files that match the exclusion lists — treat them as if they do not exist.

### Step 2: Determine Output Path

Compute the `.walkthrough/` output path using the following rules. The `.walkthrough/` root is always placed at the **workspace root** (or the root of the project being analyzed).

#### Single File Target
* Output file: `.walkthrough/<relative-path-to-file>.md`
* Example: source file `src/auth/login.ts` → `.walkthrough/src/auth/login.ts.md`
* Append `.md` to the full original filename — do **not** strip the original extension.

#### Directory Target
* Mirror the full directory hierarchy inside `.walkthrough/`.
* For each file analyzed, create `.walkthrough/<relative-path-to-file>.md`.
* Generate a directory-level **index** file at `.walkthrough/<relative-path-to-dir>/index.md` containing:
  - High-level folder purpose
  - Component interaction topology (Mermaid diagram)
  - Table of contents linking to each file's walkthrough markdown file

#### Function / Code Block Target
* Output file: `.walkthrough/<relative-path-to-file>/<function-name>.md`

> **Rule**: Always preserve the original folder hierarchy inside `.walkthrough/`. Never flatten paths.

### Step 3: Contextual & Dependency Mapping
* Read the codebase files matching the **inclusion criteria** (i.e., everything not excluded in Step 1).
* If analyzing a directory, apply the **Phase 3 priority sort** from `references/directory-traversal.md` to read manifest/entrypoint files first, building context before reading deeper modules.
* Construct an internal dependency map showing how the included files interact.
* Consult `references/architectural-matrix.md` to map design trade-offs for high-impact files.

### Step 3.5: Function-Level Extraction
For **every non-excluded source file** that will receive a walkthrough:
* Enumerate **all functions, methods, classes, and exported symbols** defined in the file.
* For each unit, capture:
  - **Signature** — the full declaration/prototype as it appears in source.
  - **Purpose** — a concise description of what it does.
  - **Parameters** — name, type, and description for every parameter.
  - **Return value** — type and meaning of the return (or `void`/`None`/`Future<void>` etc.).
  - **Usage example** — a minimal realistic call-site snippet (inline code block, language-tagged).
  - **Side effects & dependencies** — external calls, state mutations, I/O, throws/exceptions.
* If a file contains **no exported or meaningful symbols** (e.g., pure config JSON, plain CSS variables) skip this step for that file and note "No callable symbols" in section 5.

### Step 4: Progressive Output Synthesis & File Writing
* Generate the overarching structural summary and write it to the index file (for directories).
* Run individual file walkthroughs conditionally based on file relevance (skip boilerplate/generated files as defined in `references/directory-traversal.md`).
* Format all diagrams using the rules in `references/diagram-standards.md`.
* **Write each walkthrough as a separate `.md` file** to the path computed in Step 2.
* Create intermediate directories as needed.

---

## 📋 Markdown File Content Specification

### Directory Index File: `.walkthrough/<dir>/index.md`

```markdown
# 📁 Directory Walkthrough: `[Folder Path]`

> **Scope Summary**: Analyzed `[X]` files across `[Y]` subdirectories. Detected ecosystems: `[e.g., Node/TypeScript, Python]`. Excluded: `[list key ignored folders, e.g., node_modules/, .next/, __pycache__/]`.

## 1. High-Level Folder Purpose
*A macro-level summary of what this directory/module layer accomplishes as a cohesive unit.*

## 2. Directory Component Interaction Topology

[Mermaid Sequence or Flowchart diagram showing file interactions]

## 3. File Index

| File | Purpose |
|------|---------|
| [filename.ext.md](./filename.ext.md) | One-line description |
```

### Single File Walkthrough: `.walkthrough/<path>/file.ext.md`

```markdown
# 📄 File Walkthrough: `[Relative Path/To/File.ext]`

## 1. Core Purpose
[State mutations or data transformations happening here.]

## 2. Strategic Rationale
- **Tactical Role**: [e.g., Anti-Corruption Layer / Reactive State Conduit]
- **Systemic Necessity**: [Why the application requires this file in this module layer.]

## 3. Ecosystem Role & Telemetry
- 📥 **Inbound Triggers**: `[Components, hooks, or events that call this file]`
- ⚙️ **Internal State**: `[State mutations, cache updates, or local storage persistence details]`
- 📤 **Outbound Vectors**: `[Where data or control is passed next]`

## 4. Architectural Trade-Offs *(if applicable)*
> Consult `references/architectural-matrix.md` to populate this section.
- *Chosen Pattern*: `[e.g., Local-first drift syncing via repository design]`
- *Alternative Bypass*: `[Why this implementation was chosen over direct alternatives]`

## 5. Functions & API Inventory

> **Required**: Document EVERY function, method, class, and exported symbol in this file.
> If the file contains no callable symbols, write: *No callable symbols — pure configuration/data file.*

---

### `FunctionOrMethodName`

**Signature**
```[language]
[Full declaration exactly as it appears in source]
```

**Purpose**  
[One or two sentences describing what this function does and why it exists.]

**Parameters**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `paramName` | `Type` | ✅ / ❌ | What this parameter controls or represents. |

**Return Value**

| Type | Description |
|------|-------------|
| `ReturnType` | What the returned value represents; `void` / `Future<void>` / `None` if no return. |

**Usage Example**
```[language]
// Minimal realistic call-site snippet
[example call]
```

**Side Effects & Dependencies**
- [External API calls, database writes, state mutations, throws/exceptions, platform channels, etc.]
- [None] if purely functional with no side effects.

---

*Repeat the block above for each function / method / class / exported symbol in the file.*

## 6. Local Behavioral Topology
> Triggered conditionally based on the rules in `references/file-breakdown-engine.md`.
[If triggered, insert the custom Mermaid diagram here. If skipped, omit this subsection entirely.]
```

### Function / Code Block Walkthrough: `.walkthrough/<path>/<function-name>.md`

```markdown
# 🔧 Function Walkthrough: `[FunctionName]` in `[File]`

## Signature
[function signature]

## Purpose
[What this function does.]

## Parameters & Return Values
| Param | Type | Description |
|-------|------|-------------|

## Internal Flow
[Mermaid flowchart or step-by-step breakdown]

## Side Effects & Dependencies
[External calls, state mutations, etc.]
```

---

## 💬 Chat Response (After Writing Files)

After all `.md` files have been written to disk, send a **short** message in the chat:

```
✅ Walkthrough written to `.walkthrough/`:

- `.walkthrough/src/auth/login.ts.md` — Auth login handler
- `.walkthrough/src/auth/index.md` — Auth module overview

Use the links above to navigate the walkthrough.
```

**Do NOT reproduce the full walkthrough content in the chat window.**

---

## ⚠️ Rules & Constraints

1. **Never dump full walkthrough content into the chat.** The chat response is always a short summary + clickable file links only.
2. **Always mirror the source folder hierarchy** inside `.walkthrough/`. Do not flatten paths.
3. **The `.walkthrough/` root** is placed at the workspace root (or the root of the project being analyzed).
4. **File naming**: Append `.md` to the original filename (e.g., `login.ts` → `login.ts.md`). Do not strip the original extension.
5. **Directory index files** are named `index.md` and placed inside the mirrored directory folder.
6. **Overwrite** existing walkthrough files if re-running on the same target.
7. **Exclusion is framework-aware**: Before traversal, detect active ecosystems via Phase 0 of `references/directory-traversal.md`, then apply universal + ecosystem-specific + generated-file exclusions (Phases 1a–1c). Never traverse `node_modules/`, `build/`, `dist/`, `target/`, `.gradle/`, `__pycache__/`, `vendor/`, `.next/`, `DerivedData/`, `.terraform/`, or any other build/dependency artifact folder.