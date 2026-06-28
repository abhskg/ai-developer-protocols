---
name: git-commit-generator
description: >-
  Automatically runs git commands to fetch diffs and status context, analyzes the working directory changes, 
  and generates a perfectly formatted Conventional Commit message in the chat window. 
  Activates when the user asks to "generate a commit message", "commit changes", or "write a git log entry".
---

# Global Git Commit Generator Skill

This skill allows the agent to safely inspect local repository differentials and formulate a professional, context-aware commit message.

## 🛑 ABSOLUTE GUARDRAIL
* **CRITICAL POLICY**: The agent is strictly **FORBIDDEN** from executing `git commit` or any state-mutating git commands. 
* The sole objective is to collect context, process the logic, and output the text block inside the chat interface for the human developer to review, modify, and execute manually.

## 🛠️ Execution Pipeline

### Step 1: Workspace Context Gathering
* Execute the multi-stage background command sequencing defined in `references/git-context-protocol.md`.
* Safely ingest either the staged diff caching layer or the active working tree modifications.

### Step 2: Context Analysis & Categorization
* Analyze the generated file stats and codebase modifications.
* Map the intent of the changes to a standard type using the matrix inside `references/conventional-commits-spec.md`.

### Step 3: Message Synthesis
* Construct a multi-line commit message block utilizing the imperative mood.
* Present the raw output cleanly inside a dedicated codeblock for effortless copying.

---

## 📋 Output Specification

The final output rendered to the user must use the following clean format:

### 💾 Suggested Commit Message

```text
[Type]([Optional Scope]): [Short Imperative Summary under 50 chars]

[Optional descriptive paragraph outlining what was changed and why it was necessary]

[Optional footer tracking breaking changes or issue identifiers]