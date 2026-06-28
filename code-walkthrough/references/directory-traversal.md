# Directory Traversal & Recursion Guidelines

When a folder/directory prompt triggers this skill, the agent must adhere to the following file-system discovery protocol:

---

## Phase 0 — Framework Detection (Run Before Any Traversal)

Before listing any files, scan the project root for manifest/config files to identify the active ecosystem(s). This determines which ecosystem-specific exclusion set to activate in Phase 1.

| Detected File / Pattern | Ecosystem Activated |
|---|---|
| `package.json`, `yarn.lock`, `pnpm-lock.yaml` | Node / JavaScript / TypeScript |
| `next.config.*`, `vite.config.*`, `nuxt.config.*` | Next.js / Vite / Nuxt |
| `requirements.txt`, `Pipfile`, `pyproject.toml`, `setup.py` | Python |
| `pom.xml`, `build.gradle`, `build.gradle.kts`, `gradlew` | Java / Kotlin / Android |
| `go.mod`, `go.sum` | Go |
| `Cargo.toml`, `Cargo.lock` | Rust |
| `*.csproj`, `*.sln`, `global.json`, `NuGet.config` | .NET / C# |
| `composer.json`, `composer.lock` | PHP |
| `Gemfile`, `Gemfile.lock`, `Rakefile` | Ruby / Rails |
| `pubspec.yaml`, `pubspec.lock` | Dart / Flutter |
| `Podfile`, `Package.swift`, `*.xcodeproj`, `*.xcworkspace` | Swift / iOS / macOS |
| `CMakeLists.txt`, `conanfile.txt` | C / C++ (CMake / Conan) |
| `mix.exs` | Elixir |
| `*.tf`, `*.tfvars` | Terraform |
| `chart.yaml`, `values.yaml` | Helm / Kubernetes |
| `Dockerfile`, `docker-compose.yml` | Docker (always active) |

> Multiple ecosystems can be active simultaneously (e.g., a monorepo with `package.json` + `pom.xml`).

---

## Phase 1 — Hard Exclusion Filters

### 1a. Universal Exclusions (Always Applied)

These are **always skipped**, regardless of ecosystem:

**Version Control & IDE Metadata**
- `.git/`, `.svn/`, `.hg/`
- `.idea/`, `.vscode/`, `.cursor/`, `.zed/`, `.eclipse/`
- `*.suo`, `*.user`, `.DS_Store`, `Thumbs.db`

**Environment & Secrets**
- `.env`, `.env.*`, `*.pem`, `*.key`, `*.cert`, `*.p12`, `*.pfx`

**Log & Temp Files**
- `*.log`, `*.tmp`, `*.temp`, `*.cache`, `*.bak`, `tmp/`, `temp/`, `logs/`

**Binary & Media Assets**
- `*.png`, `*.jpg`, `*.jpeg`, `*.gif`, `*.webp`, `*.ico`, `*.svg` *(binary)*
- `*.mp4`, `*.mov`, `*.avi`, `*.mp3`, `*.wav`
- `*.pdf`, `*.docx`, `*.xlsx`, `*.zip`, `*.tar.gz`, `*.7z`
- `*.exe`, `*.dll`, `*.so`, `*.dylib`, `*.bin`, `*.wasm`

**Lockfiles (Skip Content, but Detect for Framework ID)**
- `package-lock.json`, `yarn.lock`, `pnpm-lock.yaml`
- `Gemfile.lock`, `Cargo.lock`, `Pipfile.lock`, `composer.lock`
- `pubspec.lock`, `go.sum`, `poetry.lock`

---

### 1b. Ecosystem-Specific Exclusions

Activate the relevant block(s) based on Phase 0 detection:

#### 🟨 Node / JavaScript / TypeScript
- `node_modules/`
- `.next/`, `.nuxt/`, `.output/`, `.svelte-kit/`
- `dist/`, `build/`, `out/`, `.cache/`, `.parcel-cache/`
- `coverage/`, `.nyc_output/`
- `storybook-static/`
- `*.min.js`, `*.min.css`, `*.bundle.js`, `*.chunk.js`
- `.turbo/`, `.vercel/`, `.netlify/`

#### 🐍 Python
- `__pycache__/`, `*.pyc`, `*.pyo`, `*.pyd`
- `.venv/`, `venv/`, `env/`, `.env/`
- `*.egg-info/`, `dist/`, `build/`, `site-packages/`
- `.mypy_cache/`, `.pytest_cache/`, `.ruff_cache/`
- `htmlcov/`, `.coverage`
- `*.ipynb_checkpoints/`

#### ☕ Java / Kotlin / Android
- `.gradle/`, `gradle/`, `build/`
- `out/`, `bin/`, `target/`
- `*.class`, `*.jar`, `*.war`, `*.aar`, `*.apk`, `*.aab`
- `.android/`, `.cxx/`, `captures/`
- `generated/`, `generatedJava/`

#### 🦀 Rust
- `target/`
- `*.rlib`, `*.rmeta`, `*.d`

#### 🐹 Go
- `vendor/`
- `bin/`, `pkg/`, `dist/`

#### 🔷 .NET / C#
- `bin/`, `obj/`
- `*.dll`, `*.exe`, `*.pdb`, `*.nupkg`
- `.vs/`, `TestResults/`
- `packages/` *(NuGet restore)*

#### 🐘 PHP
- `vendor/`
- `bootstrap/cache/`, `storage/framework/cache/`, `storage/logs/`
- `public/hot`, `public/storage`

#### 💎 Ruby / Rails
- `vendor/`, `.bundle/`
- `tmp/`, `log/`
- `public/assets/`, `public/packs/`
- `coverage/`

#### 🎯 Dart / Flutter
- `.dart_tool/`
- `build/`
- `.flutter-plugins`, `.flutter-plugins-dependencies`
- `*.g.dart`, `*.freezed.dart`, `*.gr.dart` *(generated)*

#### 🍎 Swift / iOS / macOS
- `Pods/`, `.build/`
- `DerivedData/`, `*.xcuserdata/`
- `*.ipa`, `*.app`, `*.dSYM`
- `Carthage/`

#### ⚙️ C / C++ (CMake)
- `build/`, `cmake-build-*/`, `CMakeFiles/`
- `*.o`, `*.obj`, `*.a`, `*.lib`, `*.out`
- `vcpkg_installed/`, `conan/`

#### 🏗️ Terraform / Infra
- `.terraform/`
- `*.tfstate`, `*.tfstate.backup`
- `.terragrunt-cache/`

#### 🐳 Docker
- *(No directory exclusion beyond universal — Dockerfile and compose files are analyzed)*

---

### 1c. Generated Source File Heuristics

Even if a file has a source extension (`.ts`, `.dart`, `.java`, etc.), skip it if **any** of the following patterns match:

- Filename ends in `.g.dart`, `.freezed.dart`, `.gr.dart`, `.generated.ts`, `.d.ts` *(auto-generated declaration files only)*
- File header contains `// GENERATED CODE`, `// DO NOT EDIT`, `/* auto-generated */`, `# This file is auto-generated`
- File lives inside a folder named `generated/`, `gen/`, `__generated__/`, `codegen/`

> **Exception**: Always read `*.d.ts` files that are hand-authored (e.g., ambient module declarations), identified by the absence of a `// Generated` header.

---

## Phase 2 — Traversal Depth Guardrails

Count only **non-excluded** files when applying tier thresholds:

| Tier | File Count | Strategy |
|---|---|---|
| **Tier 1** | ≤ 10 files | Full deep-dive on every file using the complete SKILL.md template |
| **Tier 2** | 11–30 files | Group cohesive files (models, widgets, utils) into aggregate summaries; full deep-dive for controllers, services, routers, and business logic |
| **Tier 3** | > 30 files | Output Directory Interaction Topology + high-level file table first; prompt user to specify sub-modules for deep-dive |

---

## Phase 3 — File Discovery Priority Sorting

When scanning a folder, read files in this order to build context efficiently:

1. **Manifests & Config** — `package.json`, `pubspec.yaml`, `pom.xml`, `go.mod`, `Cargo.toml`, `pyproject.toml`, `README.md`, `*.config.*`
2. **Entrypoints** — `index.ts`, `main.dart`, `main.go`, `main.rs`, `Program.cs`, `app.py`, `routes.py`, `App.tsx`
3. **Orchestrators / Controllers** — files matching `*controller*`, `*service*`, `*bloc*`, `*provider*`, `*router*`, `*middleware*`
4. **Data Layers** — `*repository*`, `*api*`, `*dao*`, `*store*`, `*db*`
5. **Presentation / Models** — `*view*`, `*model*`, `*component*`, `*widget*`, `*screen*`, `*page*`
6. **Tests** — `*.test.*`, `*.spec.*`, `*_test.*` *(low priority — summarize, do not deep-dive unless explicitly requested)*