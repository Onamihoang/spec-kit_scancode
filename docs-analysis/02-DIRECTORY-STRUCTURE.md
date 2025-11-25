# Spec Kit - Cấu Trúc Thư Mục Chi Tiết

## 1. Tổng Quan Cấu Trúc

```
spec-kit/
│
├── 📁 src/                          # SOURCE CODE CHÍNH
│   └── 📁 specify_cli/
│       └── 📄 __init__.py           # CLI implementation (1357 lines)
│
├── 📁 templates/                    # TEMPLATES CHO AI AGENTS
│   ├── 📁 commands/                 # Slash command definitions
│   │   ├── 📄 specify.md            # /speckit.specify
│   │   ├── 📄 plan.md               # /speckit.plan
│   │   ├── 📄 tasks.md              # /speckit.tasks
│   │   ├── 📄 implement.md          # /speckit.implement
│   │   ├── 📄 clarify.md            # /speckit.clarify
│   │   ├── 📄 analyze.md            # /speckit.analyze
│   │   ├── 📄 checklist.md          # /speckit.checklist
│   │   ├── 📄 constitution.md       # /speckit.constitution
│   │   └── 📄 taskstoissues.md      # /speckit.taskstoissues
│   │
│   ├── 📄 spec-template.md          # Feature spec template
│   ├── 📄 plan-template.md          # Implementation plan template
│   ├── 📄 tasks-template.md         # Task breakdown template
│   ├── 📄 agent-file-template.md    # Agent config template
│   ├── 📄 checklist-template.md     # Quality checklist template
│   └── 📄 vscode-settings.json      # VSCode settings
│
├── 📁 scripts/                      # AUTOMATION SCRIPTS
│   ├── 📁 bash/                     # POSIX shell scripts
│   │   ├── 📄 common.sh             # Shared functions
│   │   ├── 📄 check-prerequisites.sh
│   │   ├── 📄 create-new-feature.sh
│   │   ├── 📄 setup-plan.sh
│   │   └── 📄 update-agent-context.sh
│   │
│   └── 📁 powershell/               # Windows PowerShell
│       ├── 📄 common.ps1
│       ├── 📄 check-prerequisites.ps1
│       ├── 📄 create-new-feature.ps1
│       ├── 📄 setup-plan.ps1
│       └── 📄 update-agent-context.ps1
│
├── 📁 docs/                         # DOCUMENTATION (DocFX)
│   ├── 📄 docfx.json                # DocFX config
│   ├── 📄 index.md                  # Docs homepage
│   ├── 📄 toc.yml                   # Table of contents
│   ├── 📄 installation.md
│   ├── 📄 quickstart.md
│   ├── 📄 upgrade.md
│   ├── 📄 local-development.md
│   └── 📄 README.md
│
├── 📁 memory/                       # PROJECT MEMORY
│   └── 📄 constitution.md           # Constitution template
│
├── 📁 media/                        # MEDIA ASSETS
│   └── (images, logos)
│
├── 📁 .github/                      # GITHUB CONFIGURATION
│   ├── 📁 workflows/                # CI/CD pipelines
│   │   ├── 📄 release.yml           # Automated releases
│   │   ├── 📄 docs.yml              # Documentation deployment
│   │   ├── 📄 lint.yml              # Code linting
│   │   └── 📁 scripts/              # Workflow helpers
│   ├── 📄 CODEOWNERS
│   └── 📁 (issue/PR templates)
│
├── 📁 .devcontainer/                # DEV CONTAINER
│   └── 📄 devcontainer.json         # Docker dev environment
│
├── 📄 README.md                     # Main documentation (31KB)
├── 📄 spec-driven.md                # SDD methodology (25KB)
├── 📄 AGENTS.md                     # Agent integration guide (14KB)
├── 📄 CHANGELOG.md                  # Version history (8KB)
├── 📄 CONTRIBUTING.md               # Contribution guidelines (7KB)
├── 📄 pyproject.toml                # Python project config
├── 📄 LICENSE                       # MIT License
└── 📄 (other config files)
```

---

## 2. Chi Tiết Từng Thư Mục

### 2.1 📁 src/specify_cli/

**Mục đích**: Chứa toàn bộ source code của Specify CLI

```python
# __init__.py Structure (1357 lines)

# Lines 1-57: Imports & SSL setup
# Lines 59-123: GitHub API rate-limit handling
# Lines 126-217: AGENT_CONFIG (15 AI agents)
# Lines 219-231: Constants (BANNER, SCRIPT_TYPE_CHOICES)
# Lines 233-316: StepTracker class (progress tracking)
# Lines 318-411: Interactive selection (arrow keys)
# Lines 413-501: Utility functions (run_command, check_tool)
# Lines 503-556: Git management
# Lines 558-623: JSON merging for VSCode settings
# Lines 625-886: Template download & extraction
# Lines 889-931: Script permission management
# Lines 933-1229: Main CLI commands (init, check, version)
```

**Key Classes/Functions:**

| Component | Line | Purpose |
|-----------|------|---------|
| `StepTracker` | 233 | Progress tracking with Rich tree |
| `select_with_arrows()` | 338 | Interactive menu selection |
| `download_template_from_github()` | 625 | GitHub release download |
| `download_and_extract_template()` | 739 | ZIP extraction & merge |
| `init` command | 933 | Main initialization logic |

---

### 2.2 📁 templates/commands/

**Mục đích**: Định nghĩa slash commands cho AI agents

```yaml
# Mỗi file có cấu trúc YAML frontmatter + Markdown body

---
description: <mô tả command>
handoffs:                    # Chuyển tiếp sang command khác
  - label: <tên action>
    agent: speckit.<command>
    prompt: <prompt template>
scripts:                     # Scripts để chạy
  sh: scripts/bash/<script>.sh
  ps: scripts/powershell/<script>.ps1
---

## User Input
$ARGUMENTS

## Outline
<workflow steps>

## Guidelines
<best practices>
```

**Command Flow:**

```
                    ┌──────────────────┐
                    │  constitution    │
                    └────────┬─────────┘
                             │
                    ┌────────▼─────────┐
     ┌──────────────│     specify      │──────────────┐
     │              └────────┬─────────┘              │
     │                       │                        │
┌────▼────┐         ┌────────▼─────────┐        ┌────▼────┐
│ clarify │         │      plan        │        │checklist│
└─────────┘         └────────┬─────────┘        └─────────┘
                             │
                    ┌────────▼─────────┐
                    │      tasks       │
                    └────────┬─────────┘
                             │
     ┌──────────────┬────────▼─────────┬──────────────┐
     │              │                  │              │
┌────▼────┐   ┌─────▼─────┐   ┌────────▼──────┐ ┌────▼────┐
│ analyze │   │ implement │   │ taskstoissues │ │checklist│
└─────────┘   └───────────┘   └───────────────┘ └─────────┘
```

---

### 2.3 📁 scripts/

**Mục đích**: Automation scripts cross-platform

#### bash/ scripts:

| Script | Function | Key Features |
|--------|----------|--------------|
| `common.sh` | Shared utilities | Path resolution, JSON parsing |
| `check-prerequisites.sh` | Tool verification | Check required tools installed |
| `create-new-feature.sh` | Feature setup | Branch creation, spec initialization |
| `setup-plan.sh` | Plan preparation | Copy templates, setup directories |
| `update-agent-context.sh` | Agent config | Update agent-specific files |

#### powershell/ scripts:

Tương tự bash/ nhưng cho Windows PowerShell:
- `common.ps1`
- `check-prerequisites.ps1`
- `create-new-feature.ps1`
- `setup-plan.ps1`
- `update-agent-context.ps1`

---

### 2.4 📁 .github/workflows/

**Mục đích**: CI/CD automation

```yaml
# release.yml - Automated release workflow
Triggers: Manual dispatch
Actions:
  1. Version increment (semantic)
  2. Build multi-agent template ZIPs
  3. Create GitHub release
  4. Upload assets (30 variants: 15 agents × 2 script types)

# docs.yml - Documentation deployment
Triggers: Push to main
Actions:
  1. Build DocFX site
  2. Deploy to GitHub Pages

# lint.yml - Code quality
Triggers: PR, Push
Actions:
  1. Markdown linting
  2. Python linting
```

---

### 2.5 📁 memory/

**Mục đích**: Project governance và principles

```markdown
# constitution.md - Template for project constitution

## The Nine Articles:
1. Library-First Principle
2. CLI Interface Mandate
3. Single Source of Truth
4. Explicit Over Implicit
5. Composition Over Inheritance
6. Configuration Over Convention
7. Simplicity Principle
8. Anti-Abstraction Principle
9. Constitution Amendments
```

---

## 3. File Organization Pattern

### 3.1 Naming Conventions

```
# Commands: kebab-case với prefix
templates/commands/speckit.specify.md    ❌ (không dùng dot)
templates/commands/specify.md            ✅

# Scripts: kebab-case
scripts/bash/create-new-feature.sh       ✅

# Templates: kebab-case với suffix -template
templates/spec-template.md               ✅

# Configs: lowercase với extension
pyproject.toml                           ✅
.markdownlint-cli2.jsonc                 ✅
```

### 3.2 Generated Project Structure

Khi chạy `specify init my-project`, tạo ra:

```
my-project/
├── .specify/
│   ├── templates/
│   │   ├── spec-template.md
│   │   ├── plan-template.md
│   │   ├── tasks-template.md
│   │   └── checklist-template.md
│   └── scripts/
│       ├── bash/
│       └── powershell/
│
├── .claude/                    # (nếu chọn Claude)
│   └── commands/
│       ├── speckit.specify.md
│       ├── speckit.plan.md
│       └── ... (8 more)
│
├── memory/
│   └── constitution.md
│
├── specs/                      # Feature specifications
│   └── (empty - created per feature)
│
├── .vscode/
│   └── settings.json           # Merged settings
│
└── .gitignore
```

---

## 4. Dependency Graph

```
┌─────────────────────────────────────────────────────────────────┐
│                         SPEC KIT                                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌─────────────────┐                                            │
│  │   specify-cli   │◄─────────────────────────────────┐         │
│  │   (__init__.py) │                                  │         │
│  └────────┬────────┘                                  │         │
│           │                                           │         │
│           │ uses                                      │         │
│           ▼                                           │         │
│  ┌─────────────────┐    ┌─────────────────┐   ┌──────┴──────┐  │
│  │    templates/   │    │    scripts/     │   │ GitHub API  │  │
│  │    commands/    │    │   bash + ps     │   │  (releases) │  │
│  └────────┬────────┘    └────────┬────────┘   └─────────────┘  │
│           │                      │                              │
│           │ defines              │ automates                    │
│           ▼                      ▼                              │
│  ┌─────────────────────────────────────────┐                   │
│  │           AI Agent Commands             │                   │
│  │  (Claude, Copilot, Gemini, Cursor...)   │                   │
│  └─────────────────────────────────────────┘                   │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 5. File Statistics

| Category | Count | Total Size |
|----------|-------|------------|
| Python files | 1 | ~50KB |
| Markdown files | 25+ | ~150KB |
| Shell scripts | 10 | ~30KB |
| YAML configs | 5 | ~10KB |
| JSON configs | 3 | ~5KB |
| **Total** | ~50 files | ~7.5MB (incl. media) |
