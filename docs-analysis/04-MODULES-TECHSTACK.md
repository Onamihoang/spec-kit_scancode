# Spec Kit - Chi Tiết Modules & Tech Stack

## 1. Tech Stack Overview

### 1.1 Core Technologies

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           SPEC KIT TECH STACK                                │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  LANGUAGE & RUNTIME                                                          │
│  ═══════════════════                                                         │
│  ┌─────────────┐                                                            │
│  │  Python     │  Version: 3.11+                                            │
│  │  3.11+      │  Type Hints: Yes                                           │
│  └─────────────┘  Single-file Architecture                                  │
│                                                                              │
│  CLI FRAMEWORK                                                               │
│  ═════════════                                                               │
│  ┌─────────────┐                                                            │
│  │   Typer     │  Click-based CLI with auto-completion                      │
│  │             │  Type-safe command definitions                             │
│  └─────────────┘  Subcommands: init, check, version                         │
│                                                                              │
│  UI/OUTPUT                                                                   │
│  ═════════                                                                   │
│  ┌─────────────┐                                                            │
│  │    Rich     │  Beautiful terminal formatting                             │
│  │             │  Progress bars, tables, trees, panels                      │
│  └─────────────┘  Live display updates                                      │
│                                                                              │
│  HTTP CLIENT                                                                 │
│  ═══════════                                                                 │
│  ┌─────────────┐                                                            │
│  │   httpx     │  Async-capable HTTP client                                 │
│  │  [socks]    │  SOCKS proxy support                                       │
│  └─────────────┘  Streaming downloads                                       │
│                                                                              │
│  SECURITY                                                                    │
│  ════════                                                                    │
│  ┌─────────────┐                                                            │
│  │ truststore  │  Cross-platform SSL/TLS trust store                        │
│  │  >=0.10.4   │  System certificate support                                │
│  └─────────────┘                                                            │
│                                                                              │
│  BUILD SYSTEM                                                                │
│  ════════════                                                                │
│  ┌─────────────┐                                                            │
│  │ hatchling   │  Modern Python packaging                                   │
│  │             │  PEP 517/518 compliant                                     │
│  └─────────────┘                                                            │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 1.2 Dependencies (pyproject.toml)

```toml
[project]
name = "specify-cli"
requires-python = ">=3.11"
dependencies = [
    "typer",           # CLI framework
    "rich",            # Terminal UI
    "platformdirs",    # Platform-specific paths
    "readchar",        # Cross-platform keyboard input
    "httpx[socks]",    # HTTP client with SOCKS
    "truststore>=0.10.4"  # SSL trust store
]

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"
```

### 1.3 Dependency Graph

```
specify-cli
│
├── typer ─────────────► click
│                        │
│                        └──► colorama (Windows)
│
├── rich ──────────────► markdown-it-py
│                        │
│                        ├──► pygments
│                        │
│                        └──► typing-extensions
│
├── httpx[socks] ──────► httpcore
│                        │
│                        ├──► h11
│                        │
│                        ├──► certifi
│                        │
│                        └──► socksio
│
├── truststore ────────► ssl (stdlib)
│
├── platformdirs ──────► (no deps)
│
└── readchar ──────────► (no deps)
```

---

## 2. Module Breakdown

### 2.1 Core Modules (src/specify_cli/__init__.py)

```python
# Module Structure Overview

# ═══════════════════════════════════════════════════════════════════════════
# SECTION 1: IMPORTS & SETUP (Lines 1-57)
# ═══════════════════════════════════════════════════════════════════════════

import os, subprocess, sys, zipfile, tempfile, shutil, json
from pathlib import Path
from typing import Optional, Tuple

# Third-party
import typer, httpx, readchar
from rich.console import Console
from rich.panel import Panel
from rich.progress import Progress, SpinnerColumn, TextColumn
from rich.text import Text
from rich.live import Live
from rich.align import Align
from rich.table import Table
from rich.tree import Tree

# SSL/TLS setup
import ssl, truststore
ssl_context = truststore.SSLContext(ssl.PROTOCOL_TLS_CLIENT)
client = httpx.Client(verify=ssl_context)
```

### 2.2 GitHub API Module (Lines 59-123)

```python
# ═══════════════════════════════════════════════════════════════════════════
# SECTION 2: GITHUB API HANDLING
# ═══════════════════════════════════════════════════════════════════════════

def _github_token(cli_token: str | None = None) -> str | None:
    """
    Priority: CLI arg > GH_TOKEN env > GITHUB_TOKEN env
    Returns: Sanitized token or None
    """

def _github_auth_headers(cli_token: str | None = None) -> dict:
    """
    Returns: {"Authorization": "Bearer <token>"} or {}
    """

def _parse_rate_limit_headers(headers: httpx.Headers) -> dict:
    """
    Extracts:
    - X-RateLimit-Limit
    - X-RateLimit-Remaining
    - X-RateLimit-Reset (epoch → datetime)
    - Retry-After
    """

def _format_rate_limit_error(status_code: int, headers: httpx.Headers, url: str) -> str:
    """
    User-friendly error with:
    - Rate limit info
    - Reset time (local timezone)
    - Troubleshooting tips
    - Auth recommendations
    """
```

### 2.3 Agent Configuration Module (Lines 126-217)

```python
# ═══════════════════════════════════════════════════════════════════════════
# SECTION 3: AGENT CONFIGURATION
# ═══════════════════════════════════════════════════════════════════════════

AGENT_CONFIG = {
    "copilot": {
        "name": "GitHub Copilot",
        "folder": ".github/",
        "install_url": None,      # IDE-based
        "requires_cli": False,
    },
    "claude": {
        "name": "Claude Code",
        "folder": ".claude/",
        "install_url": "https://docs.anthropic.com/en/docs/claude-code/setup",
        "requires_cli": True,     # CLI tool required
    },
    # ... 13 more agents
}

# Agent Types:
# ┌─────────────────────────────────────────────────────────────┐
# │  CLI-based (requires_cli: True)                             │
# │  - claude, gemini, qwen, opencode, codex, auggie,          │
# │    codebuddy, q, amp, shai                                  │
# │                                                             │
# │  IDE-based (requires_cli: False)                           │
# │  - copilot, cursor-agent, windsurf, kilocode, roo          │
# └─────────────────────────────────────────────────────────────┘
```

### 2.4 UI Components Module (Lines 233-411)

```python
# ═══════════════════════════════════════════════════════════════════════════
# SECTION 4: UI COMPONENTS
# ═══════════════════════════════════════════════════════════════════════════

class StepTracker:
    """
    Progress tracking with Rich Tree rendering.

    Status Types:
    - pending:  ○ (dim green)
    - running:  ○ (cyan)
    - done:     ● (green)
    - error:    ● (red)
    - skipped:  ○ (yellow)

    Usage:
        tracker = StepTracker("Initialize Project")
        tracker.add("step1", "Download template")
        tracker.start("step1")
        tracker.complete("step1", "done")

    Features:
    - Hierarchical step display
    - Live refresh support
    - Detail text in parentheses
    """

    def __init__(self, title: str): ...
    def attach_refresh(self, cb: Callable): ...
    def add(self, key: str, label: str): ...
    def start(self, key: str, detail: str = ""): ...
    def complete(self, key: str, detail: str = ""): ...
    def error(self, key: str, detail: str = ""): ...
    def skip(self, key: str, detail: str = ""): ...
    def render(self) -> Tree: ...


def select_with_arrows(options: dict, prompt_text: str, default_key: str) -> str:
    """
    Interactive arrow-key menu selection.

    Features:
    - ↑/↓ navigation
    - Enter to select
    - Escape to cancel
    - Live display update
    - Default selection support

    Returns: Selected option key
    """


def get_key() -> str:
    """
    Cross-platform keyboard input.

    Handles:
    - Arrow keys (UP, DOWN)
    - Enter, Escape
    - Ctrl+C (KeyboardInterrupt)
    - Ctrl+P/N (vim-style navigation)
    """
```

### 2.5 Git Management Module (Lines 503-556)

```python
# ═══════════════════════════════════════════════════════════════════════════
# SECTION 5: GIT MANAGEMENT
# ═══════════════════════════════════════════════════════════════════════════

def is_git_repo(path: Path = None) -> bool:
    """
    Check if path is inside a git repository.
    Uses: git rev-parse --is-inside-work-tree
    """

def init_git_repo(project_path: Path, quiet: bool = False) -> Tuple[bool, Optional[str]]:
    """
    Initialize git repository with initial commit.

    Steps:
    1. git init
    2. git add .
    3. git commit -m "Initial commit from Specify template"

    Returns: (success, error_message)
    """
```

### 2.6 File Operations Module (Lines 558-886)

```python
# ═══════════════════════════════════════════════════════════════════════════
# SECTION 6: FILE OPERATIONS
# ═══════════════════════════════════════════════════════════════════════════

def merge_json_files(existing_path: Path, new_content: dict, verbose: bool) -> dict:
    """
    Deep recursive JSON merge.

    Merge Strategy:
    - New keys: added
    - Existing keys: preserved (unless in new_content)
    - Nested dicts: recursive merge
    - Lists/values: replaced
    """

def handle_vscode_settings(sub_item, dest_file, rel_path, verbose, tracker):
    """
    Special handling for .vscode/settings.json.
    Merges instead of overwrites to preserve user settings.
    """

def download_template_from_github(
    ai_assistant: str,
    download_dir: Path,
    *,
    script_type: str = "sh",
    verbose: bool = True,
    show_progress: bool = True,
    client: httpx.Client = None,
    debug: bool = False,
    github_token: str = None
) -> Tuple[Path, dict]:
    """
    Download template ZIP from GitHub releases.

    Steps:
    1. Fetch latest release metadata
    2. Find matching asset (spec-kit-template-{agent}-{script}.zip)
    3. Stream download with progress
    4. Return (zip_path, metadata)

    Rate-limit handling: Detailed error messages with reset time
    """

def download_and_extract_template(
    project_path: Path,
    ai_assistant: str,
    script_type: str,
    is_current_dir: bool = False,
    *,
    verbose: bool = True,
    tracker: StepTracker | None = None,
    client: httpx.Client = None,
    debug: bool = False,
    github_token: str = None
) -> Path:
    """
    Download and extract template to project directory.

    Features:
    - Nested directory flattening
    - File merging for --here mode
    - VSCode settings merge
    - Progress tracking
    """

def ensure_executable_scripts(project_path: Path, tracker: StepTracker | None = None):
    """
    Set execute permissions on bash scripts (Unix only).

    Logic:
    - Find all .sh files in .specify/scripts/
    - Check shebang (#!)
    - chmod +x if readable
    """
```

### 2.7 CLI Commands Module (Lines 933-1350)

```python
# ═══════════════════════════════════════════════════════════════════════════
# SECTION 7: CLI COMMANDS
# ═══════════════════════════════════════════════════════════════════════════

@app.command()
def init(
    project_name: str = Argument(None),
    ai_assistant: str = Option(None, "--ai"),
    script_type: str = Option(None, "--script"),
    ignore_agent_tools: bool = Option(False, "--ignore-agent-tools"),
    no_git: bool = Option(False, "--no-git"),
    here: bool = Option(False, "--here"),
    force: bool = Option(False, "--force"),
    skip_tls: bool = Option(False, "--skip-tls"),
    debug: bool = Option(False, "--debug"),
    github_token: str = Option(None, "--github-token"),
):
    """
    Initialize a new Specify project.

    Steps:
    1. Validate arguments
    2. Interactive agent/script selection (if not provided)
    3. Check agent CLI tool (if requires_cli)
    4. Download template from GitHub
    5. Extract and merge files
    6. Initialize git repository (unless --no-git)
    7. Set script permissions
    8. Display next steps
    """

@app.command()
def check():
    """
    Check installed tools.

    Checks:
    - git
    - All AI agent CLI tools
    - VS Code variants (code, code-insiders)
    """

@app.command()
def version():
    """
    Display version and system information.

    Shows:
    - CLI version (from package metadata)
    - Template version (from GitHub API)
    - Release date
    - Python version
    - Platform info
    """
```

---

## 3. Scripts Module Analysis

### 3.1 Bash Scripts

#### create-new-feature.sh

```bash
#!/usr/bin/env bash
# Purpose: Create new feature branch and spec structure

# ═══════════════════════════════════════════════════════════════════════════
# FUNCTIONS
# ═══════════════════════════════════════════════════════════════════════════

find_repo_root()
    # Search for .git or .specify directory

get_highest_from_specs()
    # Find highest feature number in specs/

get_highest_from_branches()
    # Find highest feature number in git branches

check_existing_branches()
    # Check remote + local + specs for duplicates

clean_branch_name()
    # Normalize: lowercase, replace special chars with -

generate_branch_name()
    # Smart filtering: remove stop words, keep meaningful terms

# ═══════════════════════════════════════════════════════════════════════════
# MAIN FLOW
# ═══════════════════════════════════════════════════════════════════════════

1. Parse arguments (--json, --short-name, --number)
2. Find repository root
3. Generate branch name from description
4. Determine next available number
5. Create branch: git checkout -b XXX-branch-name
6. Create specs/XXX-branch-name/ directory
7. Copy spec-template.md → spec.md
8. Output JSON or text result
```

#### setup-plan.sh

```bash
#!/usr/bin/env bash
# Purpose: Prepare planning phase

# ═══════════════════════════════════════════════════════════════════════════
# MAIN FLOW
# ═══════════════════════════════════════════════════════════════════════════

1. Detect current feature branch
2. Locate spec file
3. Copy plan-template.md → plan.md
4. Output JSON: {FEATURE_SPEC, IMPL_PLAN, SPECS_DIR, BRANCH}
```

#### check-prerequisites.sh

```bash
#!/usr/bin/env bash
# Purpose: Verify required tools and context

# ═══════════════════════════════════════════════════════════════════════════
# OPTIONS
# ═══════════════════════════════════════════════════════════════════════════

--require-tasks    # Verify tasks.md exists
--include-tasks    # Include task content in output

# ═══════════════════════════════════════════════════════════════════════════
# OUTPUT
# ═══════════════════════════════════════════════════════════════════════════

{
    "FEATURE_DIR": "/path/to/specs/XXX-feature",
    "AVAILABLE_DOCS": ["spec.md", "plan.md", "tasks.md", ...]
}
```

### 3.2 PowerShell Scripts

Parallel implementations in `scripts/powershell/`:

| Bash | PowerShell | Function |
|------|------------|----------|
| `create-new-feature.sh` | `create-new-feature.ps1` | Feature creation |
| `setup-plan.sh` | `setup-plan.ps1` | Plan setup |
| `check-prerequisites.sh` | `check-prerequisites.ps1` | Tool verification |
| `update-agent-context.sh` | `update-agent-context.ps1` | Agent config update |
| `common.sh` | `common.ps1` | Shared utilities |

---

## 4. Template System

### 4.1 Slash Command Templates

```yaml
# Structure of command templates (templates/commands/*.md)

---
# YAML Frontmatter
description: Command description for AI agent
handoffs:           # Optional: workflow transitions
  - label: Next Step
    agent: speckit.next
    prompt: Continue with...
    send: true      # Auto-send to next command
scripts:            # Shell scripts to execute
  sh: scripts/bash/<script>.sh
  ps: scripts/powershell/<script>.ps1
agent_scripts:      # Agent-specific scripts
  sh: scripts/bash/update-agent-context.sh __AGENT__
  ps: scripts/powershell/update-agent-context.ps1 -AgentType __AGENT__
---

## User Input
```text
$ARGUMENTS
```

## Outline
<numbered workflow steps>

## Guidelines
<best practices and rules>
```

### 4.2 Document Templates

| Template | Purpose | Key Sections |
|----------|---------|--------------|
| `spec-template.md` | Feature specification | Overview, Scenarios, Requirements, Success Criteria |
| `plan-template.md` | Implementation plan | Tech Context, Phases, Deliverables |
| `tasks-template.md` | Task breakdown | Phases, Dependencies, Parallel Markers |
| `checklist-template.md` | Quality validation | Completeness, Consistency, Readiness |
| `agent-file-template.md` | Agent configuration | Context, Tech Stack, Commands |

---

## 5. Release System

### 5.1 GitHub Actions Workflow

```yaml
# .github/workflows/release.yml

name: Create Release

on:
  workflow_dispatch:
    inputs:
      version_type:
        description: 'Version increment type'
        required: true
        default: 'patch'
        type: choice
        options: [major, minor, patch]

jobs:
  release:
    steps:
      # 1. Checkout & Setup
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5

      # 2. Version Increment
      - name: Bump version
        run: |
          # Read current version from pyproject.toml
          # Increment based on input
          # Update pyproject.toml

      # 3. Build Template Packages
      - name: Create template ZIPs
        run: |
          for agent in claude copilot gemini ...; do
            for script in sh ps; do
              zip spec-kit-template-${agent}-${script}.zip ...
            done
          done

      # 4. Create Release
      - name: Create GitHub Release
        uses: softprops/action-gh-release@v2
        with:
          tag_name: v${{ new_version }}
          files: |
            spec-kit-template-*.zip

      # 5. Publish to PyPI
      - name: Publish to PyPI
        run: |
          python -m build
          twine upload dist/*
```

### 5.2 Release Artifacts

```
Release v0.0.22
├── spec-kit-template-claude-sh.zip
├── spec-kit-template-claude-ps.zip
├── spec-kit-template-copilot-sh.zip
├── spec-kit-template-copilot-ps.zip
├── spec-kit-template-gemini-sh.zip
├── spec-kit-template-gemini-ps.zip
├── ... (15 agents × 2 script types = 30 ZIPs)
└── CHANGELOG.md (release notes)
```

---

## 6. Documentation System

### 6.1 DocFX Configuration

```json
// docs/docfx.json
{
  "build": {
    "content": [
      {
        "files": ["**/*.md", "**/toc.yml"],
        "src": ".",
        "dest": "."
      }
    ],
    "dest": "_site",
    "template": ["default"]
  }
}
```

### 6.2 Documentation Structure

```
docs/
├── index.md            # Homepage
├── toc.yml             # Table of contents
├── installation.md     # Setup guide
├── quickstart.md       # Getting started
├── upgrade.md          # Version migration
├── local-development.md # Dev setup
└── api/                # API reference (future)
```

---

## 7. Security Considerations

### 7.1 Token Handling

```python
# Token sources (priority order):
1. --github-token CLI argument
2. GH_TOKEN environment variable
3. GITHUB_TOKEN environment variable

# Security measures:
- Tokens stripped of whitespace
- Empty strings treated as None
- No token logging
- Bearer auth only when token exists
```

### 7.2 SSL/TLS

```python
# Using truststore for system certificate store
ssl_context = truststore.SSLContext(ssl.PROTOCOL_TLS_CLIENT)

# Optional: --skip-tls for debugging (not recommended)
```

### 7.3 File Permissions

```python
# Script permission setting (Unix only)
def ensure_executable_scripts():
    # Only process .sh files with shebang
    # Preserve existing permissions
    # Add execute bits based on read bits
```

---

## 8. Performance Considerations

### 8.1 Download Optimization

- **Streaming downloads**: 8KB chunks, no full file in memory
- **Progress tracking**: Real-time progress bars
- **Connection reuse**: Single httpx client instance
- **Timeout handling**: 30s API, 60s downloads

### 8.2 File Operations

- **Temporary directory usage**: Extract to temp, then move
- **Incremental merge**: Only process changed files
- **Lazy loading**: Templates loaded on demand

### 8.3 UI Optimization

- **Transient displays**: Progress cleared after completion
- **Batched updates**: Refresh rate limited to 8/second
- **Minimal redraws**: Only update changed elements
