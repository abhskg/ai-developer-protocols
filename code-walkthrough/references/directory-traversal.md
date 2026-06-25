# Directory Traversal & Recursion Guidelines

When a folder/directory prompt triggers this skill, the agent must adhere to the following file-system discovery protocol:

### 1. Hard Exclusion Filters
Always skip the following directories and file types during recursive passes unless explicitly targeted by name:
* **Dependency Folders**: `node_modules/`, `vendor/`, `.gradle/`, `Pods/`, `target/`
* **Version Control & IDEs**: `.git/`, `.idea/`, `.vscode/`, `.cursor/`
* **Build Outputs**: `build/`, `dist/`, `out/`, `*.pyc`
* **Static/Binary Assets**: `*.png`, `*.jpg`, `*.svg`, `*.pdf`, `*.mp4`, `*.lock`

### 2. Traversal Depth Guardrails
To optimize system tokens and response times, use a tier-based analysis approach:
* **Tier 1 (Shallow Folders, ≤ 10 files)**: Parse all relevant source files comprehensively using the full `SKILL.md` template.
* **Tier 2 (Medium Folders, 11–30 files)**: Group highly cohesive files (e.g., models, UI widgets) into aggregated summaries, and only provide full technical deep-dives for core controllers, routers, services, or business logic engines.
* **Tier 3 (Large Folders, > 30 files)**: First output the Directory Interaction Topology and a high-level table of files. Ask the user to specify which sub-modules require deep-dive exploration.

### 3. File Discovery Priority Sorting
When scanning a folder, read files in this priority order to build context efficiently:
1. Entrypoints/Config (`index.ts`, `main.dart`, `routes.py`, `README.md`)
2. Orchestrators/Controllers (`*controller*`, `*service*`, `*bloc*`, `*provider*`)
3. Data Layers (`*repository*`, `*api*`, `*dao*`)
4. Presentation/Models (`*view*`, `*model*`, `*component*`)