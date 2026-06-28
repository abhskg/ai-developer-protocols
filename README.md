# AI Developer Protocols 🚀

A curated suite of custom agent skills, execution pipelines, and reference guidelines that configure and extend the behavior of the **Gemini / Antigravity AI coding assistant**.

These skills enable the agent to execute structured workflows, adhere to strict guardrails, and pair program with you more effectively.

---

## 🛠️ Included Protocols & Skills

| Skill | Description | Directory |
| :--- | :--- | :--- |
| **`code-walkthrough`** | Generates detailed architectural walkthroughs, data flows, and per-function API inventories for directories or single source files. | [`code-walkthrough/`](file:///c:/Users/abhas/.gemini/config/skills/code-walkthrough) |
| **`competitive-programming`** | Mentors developer on LeetCode practice using a Socratic method and handles automatic scaffolding of solution templates and tests. | [`competitive-programming/`](file:///c:/Users/abhas/.gemini/config/skills/competitive-programming) |
| **`git-commit-generator`** | Safely analyzes workspace differentials and generates standard Conventional Commit messages for manual developer review. | [`git-commit-generator/`](file:///c:/Users/abhas/.gemini/config/skills/git-commit-generator) |

---

## 📁 Repository Structure

```text
.
├── code-walkthrough/
│   ├── SKILL.md                          # Single file & directory traversal instructions
│   └── references/
│       ├── architectural-matrix.md       # Archetypes & patterns
│       ├── diagram-standards.md          # Mermaid flowcharts & state diagrams spec
│       ├── directory-traversal.md        # Ecosystem exclusion & sorting policies
│       └── file-breakdown-engine.md      # Rules for function/API inventory logging
├── competitive-programming/
│   └── SKILL.md                          # Socratic mentorship & boilerplate scaffolding
└── git-commit-generator/
    ├── SKILL.md                          # Conventional Commit agent constraints
    └── references/
        ├── conventional-commits-spec.md  # Format specifications
        └── git-context-protocol.md       # Token-safe repo inspection flow
```

---

## ⚙️ How It Works

Custom skills are automatically discovered and loaded by the AI assistant from standard customization paths:

1. **Global Configuration:** Located at `C:\Users\<Username>\.gemini\config\skills` (or as configured on your local system).
2. **Project-Scoped Configuration:** Located in the `.agents/skills` folder at the root of a specific workspace repository.

Each skill is defined by:
* A `SKILL.md` file starting with YAML frontmatter containing `name` and `description`.
* Optional supporting resources inside `references/` or `scripts/` directories to break down complex protocols without polluting the main system prompt.

> [!NOTE]
> When the assistant detects keywords or actions matching a skill's description, it automatically activates the skill to govern its execution.

---

## ➕ Adding a New Skill

To add a new skill to this repository:

1. Create a new subdirectory: `mkdir -p <skill-name>`
2. Create a `SKILL.md` file:
   ```markdown
   ---
   name: Your Skill Name
   description: When this skill should trigger and what it accomplishes.
   ---
   
   # Core Instructions & Workflows
   ...
   ```
3. *(Optional)* Add detailed execution protocols or specs inside a `references/` subdirectory to keep the main `SKILL.md` concise.
