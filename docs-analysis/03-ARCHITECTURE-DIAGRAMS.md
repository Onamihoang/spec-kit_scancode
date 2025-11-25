# Spec Kit - Kiến Trúc Hệ Thống & Sequence Diagrams

## 1. Tổng Quan Kiến Trúc

### 1.1 High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              SPEC KIT ECOSYSTEM                              │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│    ┌─────────────────────────────────────────────────────────────────┐      │
│    │                         USER LAYER                               │      │
│    │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐        │      │
│    │  │Developer │  │  PM/PO   │  │Tech Lead │  │    QA    │        │      │
│    │  └────┬─────┘  └────┬─────┘  └────┬─────┘  └────┬─────┘        │      │
│    └───────┼─────────────┼─────────────┼─────────────┼───────────────┘      │
│            │             │             │             │                       │
│            ▼             ▼             ▼             ▼                       │
│    ┌─────────────────────────────────────────────────────────────────┐      │
│    │                       CLI INTERFACE                              │      │
│    │                                                                  │      │
│    │   $ specify init my-project --ai claude --script sh             │      │
│    │   $ specify check                                                │      │
│    │   $ specify version                                              │      │
│    │                                                                  │      │
│    └──────────────────────────┬──────────────────────────────────────┘      │
│                               │                                              │
│                               ▼                                              │
│    ┌─────────────────────────────────────────────────────────────────┐      │
│    │                      CORE ENGINE                                 │      │
│    │  ┌────────────┐  ┌────────────┐  ┌────────────┐  ┌──────────┐  │      │
│    │  │  Template  │  │   GitHub   │  │    Git     │  │   File   │  │      │
│    │  │  Manager   │  │ API Client │  │  Manager   │  │  System  │  │      │
│    │  └────────────┘  └────────────┘  └────────────┘  └──────────┘  │      │
│    └──────────────────────────┬──────────────────────────────────────┘      │
│                               │                                              │
│                               ▼                                              │
│    ┌─────────────────────────────────────────────────────────────────┐      │
│    │                    AI AGENT LAYER                                │      │
│    │                                                                  │      │
│    │  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐   │      │
│    │  │ Claude  │ │ Copilot │ │ Gemini  │ │ Cursor  │ │  Qwen   │   │      │
│    │  │  Code   │ │         │ │   CLI   │ │         │ │  Code   │   │      │
│    │  └─────────┘ └─────────┘ └─────────┘ └─────────┘ └─────────┘   │      │
│    │  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐   │      │
│    │  │ Codex   │ │Windsurf │ │KiloCode │ │ Auggie  │ │AmazonQ  │   │      │
│    │  └─────────┘ └─────────┘ └─────────┘ └─────────┘ └─────────┘   │      │
│    └─────────────────────────────────────────────────────────────────┘      │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 1.2 Component Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           SPECIFY CLI COMPONENTS                             │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │                         PRESENTATION LAYER                            │   │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌────────────┐  │   │
│  │  │   Banner    │  │   Console   │  │   Progress  │  │Interactive │  │   │
│  │  │  Renderer   │  │   Output    │  │   Tracker   │  │  Selector  │  │   │
│  │  │ (Rich/Text) │  │  (Rich)     │  │(StepTracker)│  │(Arrow Keys)│  │   │
│  │  └─────────────┘  └─────────────┘  └─────────────┘  └────────────┘  │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
│                                      │                                       │
│                                      ▼                                       │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │                          BUSINESS LOGIC LAYER                         │   │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌────────────┐  │   │
│  │  │    Init     │  │    Check    │  │   Version   │  │   Config   │  │   │
│  │  │   Command   │  │   Command   │  │   Command   │  │  Manager   │  │   │
│  │  └─────────────┘  └─────────────┘  └─────────────┘  └────────────┘  │   │
│  │                                                                       │   │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐                   │   │
│  │  │  Template   │  │   Script    │  │  Validation │                   │   │
│  │  │  Processor  │  │  Executor   │  │   Engine    │                   │   │
│  │  └─────────────┘  └─────────────┘  └─────────────┘                   │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
│                                      │                                       │
│                                      ▼                                       │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │                         INFRASTRUCTURE LAYER                          │   │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌────────────┐  │   │
│  │  │   GitHub    │  │     Git     │  │    File     │  │    SSL     │  │   │
│  │  │ API Client  │  │  Interface  │  │   System    │  │  Context   │  │   │
│  │  │   (httpx)   │  │(subprocess) │  │  (pathlib)  │  │(truststore)│  │   │
│  │  └─────────────┘  └─────────────┘  └─────────────┘  └────────────┘  │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 2. Sequence Diagrams

### 2.1 CLI Init Command Flow

```mermaid
sequenceDiagram
    participant U as User
    participant CLI as Specify CLI
    participant UI as UI Layer (Rich)
    participant GH as GitHub API
    participant FS as File System
    participant Git as Git

    U->>CLI: specify init my-project --ai claude

    CLI->>UI: Show Banner
    UI-->>U: Display ASCII Art

    CLI->>CLI: Validate Arguments

    alt Interactive Mode (no --ai flag)
        CLI->>UI: Show Agent Selection
        UI-->>U: Arrow-key Menu
        U->>UI: Select Agent
        UI-->>CLI: Selected: claude
    end

    alt Interactive Mode (no --script flag)
        CLI->>UI: Show Script Type Selection
        UI-->>U: Arrow-key Menu
        U->>UI: Select Script Type
        UI-->>CLI: Selected: sh
    end

    CLI->>UI: Initialize StepTracker

    CLI->>GH: GET /repos/github/spec-kit/releases/latest

    alt Rate Limited
        GH-->>CLI: 403 + Rate-Limit Headers
        CLI->>UI: Show Rate-Limit Error
        UI-->>U: Error Panel with Reset Time
    else Success
        GH-->>CLI: Release Data + Assets
    end

    CLI->>GH: Download spec-kit-template-claude-sh.zip
    GH-->>CLI: Stream ZIP File
    CLI->>UI: Show Download Progress

    CLI->>FS: Extract ZIP to project-path
    CLI->>FS: Merge VSCode Settings (if exists)
    CLI->>FS: Set Script Permissions (chmod +x)

    CLI->>Git: git init
    CLI->>Git: git add .
    CLI->>Git: git commit -m "Initial commit"
    Git-->>CLI: Repository Created

    CLI->>UI: Complete StepTracker
    UI-->>U: Show Success Panel + Next Steps
```

### 2.2 SDD Workflow - Full Feature Development

```mermaid
sequenceDiagram
    participant U as User
    participant AI as AI Agent (Claude)
    participant Spec as /speckit.specify
    participant Plan as /speckit.plan
    participant Task as /speckit.tasks
    participant Impl as /speckit.implement
    participant FS as File System
    participant Git as Git

    Note over U,Git: Phase 1: Specification

    U->>AI: /speckit.specify Add user authentication
    AI->>Spec: Parse Command

    Spec->>Git: git fetch --all --prune
    Spec->>Git: Check existing branches
    Spec->>Git: git checkout -b 001-user-auth

    Spec->>FS: mkdir specs/001-user-auth/
    Spec->>FS: Copy spec-template.md → spec.md

    Spec->>AI: Generate Specification Content
    AI->>AI: Extract: actors, actions, data
    AI->>AI: Generate: user scenarios, requirements
    AI->>AI: Mark: [NEEDS CLARIFICATION] items

    AI->>FS: Write spec.md
    AI->>FS: Create checklists/requirements.md
    AI->>AI: Validate against checklist

    AI-->>U: Spec ready + Clarification Questions
    U->>AI: Answer: Q1=A, Q2=B
    AI->>FS: Update spec.md with answers

    Note over U,Git: Phase 2: Planning

    U->>AI: /speckit.plan WebSocket + PostgreSQL
    AI->>Plan: Parse Command

    Plan->>FS: Read spec.md
    Plan->>FS: Read constitution.md
    Plan->>FS: Copy plan-template.md → plan.md

    Plan->>AI: Generate Plan
    AI->>AI: Phase 0: Research unknowns
    AI->>FS: Write research.md

    AI->>AI: Phase 1: Generate artifacts
    AI->>FS: Write data-model.md
    AI->>FS: Write contracts/api.yaml
    AI->>FS: Write quickstart.md

    AI->>FS: Update agent context file
    AI-->>U: Plan complete + Generated Artifacts

    Note over U,Git: Phase 3: Task Breakdown

    U->>AI: /speckit.tasks
    AI->>Task: Parse Command

    Task->>FS: Read plan.md
    Task->>FS: Read data-model.md
    Task->>FS: Read contracts/

    Task->>AI: Generate Tasks
    AI->>AI: Derive tasks from contracts
    AI->>AI: Mark parallel tasks [P]
    AI->>AI: Order by dependency

    AI->>FS: Write tasks.md
    AI-->>U: Task list ready

    Note over U,Git: Phase 4: Implementation

    U->>AI: /speckit.implement
    AI->>Impl: Parse Command

    Impl->>FS: Read tasks.md
    Impl->>FS: Check checklists/ status

    alt Checklists Incomplete
        Impl-->>U: Warning: Proceed anyway?
        U->>Impl: yes
    end

    loop For Each Task
        Impl->>AI: Execute Task
        AI->>FS: Write/Update Files
        AI->>FS: Mark task [X] complete
        AI-->>U: Task N complete
    end

    Impl->>AI: Validate Implementation
    AI-->>U: Implementation Complete!
```

### 2.3 Template Download & Extraction

```mermaid
sequenceDiagram
    participant CLI as Specify CLI
    participant API as GitHub API
    participant DL as Download Manager
    participant ZIP as ZIP Processor
    participant FS as File System
    participant Merge as JSON Merger

    CLI->>API: GET /repos/github/spec-kit/releases/latest
    Note right of API: Headers: Authorization (optional)

    API-->>CLI: Release Data
    Note left of CLI: {tag_name, assets[]}

    CLI->>CLI: Find matching asset
    Note right of CLI: Pattern: spec-kit-template-{agent}-{script}.zip

    CLI->>DL: Stream download
    DL->>API: GET asset.browser_download_url

    loop Streaming
        API-->>DL: Chunk (8192 bytes)
        DL->>DL: Write to temp file
        DL->>CLI: Update progress
    end

    CLI->>ZIP: Extract ZIP

    ZIP->>ZIP: List contents

    alt Nested Directory
        ZIP->>ZIP: Detect single root folder
        ZIP->>FS: Flatten structure
    end

    loop For Each File
        ZIP->>FS: Check if exists

        alt VSCode Settings
            ZIP->>Merge: merge_json_files()
            Merge->>FS: Deep merge JSON
        else Regular File
            ZIP->>FS: Copy file
        end
    end

    CLI->>FS: Remove temp ZIP
    CLI->>FS: chmod +x scripts/*.sh
```

### 2.4 Agent Configuration Selection

```mermaid
sequenceDiagram
    participant U as User
    participant CLI as CLI
    participant UI as Interactive UI
    participant Cfg as Agent Config

    CLI->>Cfg: Load AGENT_CONFIG
    Note right of Cfg: 15 agents defined

    CLI->>UI: select_with_arrows(options)

    UI->>UI: Create selection panel
    UI->>U: Display options with ▶ cursor

    loop Until Selection
        U->>UI: Press key

        alt Arrow Up
            UI->>UI: Move cursor up
            UI->>U: Refresh display
        else Arrow Down
            UI->>UI: Move cursor down
            UI->>U: Refresh display
        else Enter
            UI->>CLI: Return selected_key
        else Escape
            UI->>CLI: Exit(1)
        end
    end

    CLI->>Cfg: Get agent config
    Note right of Cfg: {name, folder, install_url, requires_cli}

    alt requires_cli = true
        CLI->>CLI: check_tool(agent_name)

        alt Tool Not Found
            CLI->>U: Error: Install from {install_url}
        end
    end

    CLI-->>CLI: Continue with selected agent
```

---

## 3. Data Flow Diagrams

### 3.1 Feature Specification Data Flow

```
┌──────────────────────────────────────────────────────────────────────────┐
│                    FEATURE SPECIFICATION DATA FLOW                        │
├──────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│  INPUT                    PROCESS                      OUTPUT             │
│  ═════                    ═══════                      ══════             │
│                                                                           │
│  ┌────────────┐          ┌────────────┐              ┌────────────┐      │
│  │   User     │          │  /speckit  │              │  spec.md   │      │
│  │Description │────────► │  .specify  │────────────► │            │      │
│  │            │          │            │              │ - Overview │      │
│  └────────────┘          └─────┬──────┘              │ - Scenarios│      │
│                                │                      │ - Reqs     │      │
│  ┌────────────┐                │                      │ - Success  │      │
│  │   spec-    │                │                      │   Criteria │      │
│  │  template  │────────────────┘                      └────────────┘      │
│  │   .md      │                                              │            │
│  └────────────┘                                              │            │
│                                                              ▼            │
│  ┌────────────┐          ┌────────────┐              ┌────────────┐      │
│  │Constitution│          │ Validation │              │ Checklist  │      │
│  │    .md     │────────► │   Engine   │◄─────────────│requirements│      │
│  │            │          │            │              │   .md      │      │
│  └────────────┘          └────────────┘              └────────────┘      │
│                                                                           │
└──────────────────────────────────────────────────────────────────────────┘
```

### 3.2 Implementation Plan Data Flow

```
┌──────────────────────────────────────────────────────────────────────────┐
│                   IMPLEMENTATION PLAN DATA FLOW                           │
├──────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│   INPUT                                           OUTPUT                  │
│   ═════                                           ══════                  │
│                                                                           │
│   ┌──────────┐                                   ┌──────────┐            │
│   │ spec.md  │─────────┐                    ┌───►│ plan.md  │            │
│   └──────────┘         │                    │    └──────────┘            │
│                        │                    │                             │
│   ┌──────────┐         │   ┌──────────┐     │    ┌──────────┐            │
│   │constitu- │─────────┼──►│ /speckit │─────┼───►│research  │            │
│   │tion.md   │         │   │  .plan   │     │    │  .md     │            │
│   └──────────┘         │   └──────────┘     │    └──────────┘            │
│                        │                    │                             │
│   ┌──────────┐         │                    │    ┌──────────┐            │
│   │ plan-    │─────────┘                    ├───►│data-model│            │
│   │template  │                              │    │  .md     │            │
│   └──────────┘                              │    └──────────┘            │
│                                             │                             │
│   ┌──────────┐                              │    ┌──────────┐            │
│   │  User    │                              ├───►│contracts/│            │
│   │Tech Stack│──────────────────────────────┤    │ api.yaml │            │
│   │Arguments │                              │    └──────────┘            │
│   └──────────┘                              │                             │
│                                             │    ┌──────────┐            │
│                                             └───►│quickstart│            │
│                                                  │  .md     │            │
│                                                  └──────────┘            │
│                                                                           │
└──────────────────────────────────────────────────────────────────────────┘
```

---

## 4. State Diagrams

### 4.1 Project Initialization States

```
                            ┌─────────────────┐
                            │     START       │
                            └────────┬────────┘
                                     │
                                     ▼
                            ┌─────────────────┐
                            │ Parse Arguments │
                            └────────┬────────┘
                                     │
                        ┌────────────┴────────────┐
                        │                         │
                        ▼                         ▼
               ┌─────────────────┐      ┌─────────────────┐
               │ Interactive     │      │ Non-Interactive │
               │ Selection       │      │ (flags provided)│
               └────────┬────────┘      └────────┬────────┘
                        │                         │
                        └────────────┬────────────┘
                                     │
                                     ▼
                            ┌─────────────────┐
                            │ Validate Config │
                            └────────┬────────┘
                                     │
                        ┌────────────┴────────────┐
                        │                         │
                        ▼                         ▼
               ┌─────────────────┐      ┌─────────────────┐
               │ Agent CLI Check │      │ Skip Check      │
               │ (requires_cli)  │      │ (IDE-based)     │
               └────────┬────────┘      └────────┬────────┘
                        │                         │
                        └────────────┬────────────┘
                                     │
                                     ▼
                            ┌─────────────────┐
                            │ Fetch Release   │
                            └────────┬────────┘
                                     │
                        ┌────────────┴────────────┐
                        │                         │
                        ▼                         ▼
               ┌─────────────────┐      ┌─────────────────┐
               │ Rate Limited    │      │ Success         │
               │ → Show Error    │      │ → Download      │
               └────────┬────────┘      └────────┬────────┘
                        │                         │
                        ▼                         ▼
               ┌─────────────────┐      ┌─────────────────┐
               │     EXIT        │      │ Extract & Setup │
               └─────────────────┘      └────────┬────────┘
                                                 │
                                                 ▼
                                        ┌─────────────────┐
                                        │ Git Initialize  │
                                        └────────┬────────┘
                                                 │
                                                 ▼
                                        ┌─────────────────┐
                                        │ Show Success    │
                                        │ + Next Steps    │
                                        └────────┬────────┘
                                                 │
                                                 ▼
                                        ┌─────────────────┐
                                        │     END         │
                                        └─────────────────┘
```

### 4.2 SDD Feature States

```
        ┌─────────────────────────────────────────────────────────────┐
        │                    FEATURE LIFECYCLE                         │
        └─────────────────────────────────────────────────────────────┘

        ┌─────────┐     /speckit.specify     ┌─────────┐
        │  IDEA   │─────────────────────────►│SPECIFIED│
        └─────────┘                          └────┬────┘
                                                  │
                         ┌────────────────────────┤
                         │                        │
                         ▼                        ▼
                  ┌─────────────┐          ┌─────────────┐
                  │  CLARIFIED  │◄────────►│  VALIDATED  │
                  │(clarify cmd)│          │(checklist)  │
                  └──────┬──────┘          └──────┬──────┘
                         │                        │
                         └───────────┬────────────┘
                                     │
                                     ▼ /speckit.plan
                              ┌─────────────┐
                              │   PLANNED   │
                              │ - research  │
                              │ - data-model│
                              │ - contracts │
                              └──────┬──────┘
                                     │
                                     ▼ /speckit.tasks
                              ┌─────────────┐
                              │   TASKED    │
                              │ - task list │
                              │ - parallel  │
                              │   markers   │
                              └──────┬──────┘
                                     │
                         ┌───────────┴───────────┐
                         │                       │
                         ▼ /speckit.analyze      ▼ /speckit.implement
                  ┌─────────────┐         ┌─────────────┐
                  │  ANALYZED   │         │IMPLEMENTING │
                  │(consistency)│         │  (active)   │
                  └──────┬──────┘         └──────┬──────┘
                         │                       │
                         └───────────┬───────────┘
                                     │
                                     ▼
                              ┌─────────────┐
                              │  COMPLETE   │
                              │  - code     │
                              │  - tests    │
                              │  - docs     │
                              └─────────────┘
```

---

## 5. Class Diagram (Python CLI)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         SPECIFY CLI CLASS DIAGRAM                            │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ┌───────────────────────────────────────────────────────────────────────┐  │
│  │                            StepTracker                                 │  │
│  ├───────────────────────────────────────────────────────────────────────┤  │
│  │ - title: str                                                          │  │
│  │ - steps: List[Dict]                                                   │  │
│  │ - status_order: Dict[str, int]                                        │  │
│  │ - _refresh_cb: Callable                                               │  │
│  ├───────────────────────────────────────────────────────────────────────┤  │
│  │ + __init__(title: str)                                                │  │
│  │ + attach_refresh(cb: Callable)                                        │  │
│  │ + add(key: str, label: str)                                           │  │
│  │ + start(key: str, detail: str)                                        │  │
│  │ + complete(key: str, detail: str)                                     │  │
│  │ + error(key: str, detail: str)                                        │  │
│  │ + skip(key: str, detail: str)                                         │  │
│  │ + render() -> Tree                                                    │  │
│  │ - _update(key: str, status: str, detail: str)                         │  │
│  │ - _maybe_refresh()                                                    │  │
│  └───────────────────────────────────────────────────────────────────────┘  │
│                                                                              │
│  ┌───────────────────────────────────────────────────────────────────────┐  │
│  │                            BannerGroup                                 │  │
│  ├───────────────────────────────────────────────────────────────────────┤  │
│  │ (extends TyperGroup)                                                  │  │
│  ├───────────────────────────────────────────────────────────────────────┤  │
│  │ + format_help(ctx, formatter)                                         │  │
│  └───────────────────────────────────────────────────────────────────────┘  │
│                                                                              │
│  ┌───────────────────────────────────────────────────────────────────────┐  │
│  │                          AGENT_CONFIG                                  │  │
│  ├───────────────────────────────────────────────────────────────────────┤  │
│  │ Dict[str, AgentInfo]                                                  │  │
│  │                                                                        │  │
│  │ AgentInfo:                                                            │  │
│  │   - name: str           # Display name                                │  │
│  │   - folder: str         # Config folder (.claude/, .gemini/)          │  │
│  │   - install_url: str    # Installation URL (nullable)                 │  │
│  │   - requires_cli: bool  # Needs CLI tool check                        │  │
│  └───────────────────────────────────────────────────────────────────────┘  │
│                                                                              │
│  ┌───────────────────────────────────────────────────────────────────────┐  │
│  │                          CLI Commands                                  │  │
│  ├───────────────────────────────────────────────────────────────────────┤  │
│  │ @app.command()                                                        │  │
│  │ + init(project_name, ai_assistant, script_type, ...)                  │  │
│  │ + check()                                                             │  │
│  │ + version()                                                           │  │
│  └───────────────────────────────────────────────────────────────────────┘  │
│                                                                              │
│  ┌───────────────────────────────────────────────────────────────────────┐  │
│  │                        Helper Functions                                │  │
│  ├───────────────────────────────────────────────────────────────────────┤  │
│  │ + _github_token(cli_token) -> str | None                              │  │
│  │ + _github_auth_headers(cli_token) -> dict                             │  │
│  │ + _parse_rate_limit_headers(headers) -> dict                          │  │
│  │ + _format_rate_limit_error(status_code, headers, url) -> str          │  │
│  │ + get_key() -> str                                                    │  │
│  │ + select_with_arrows(options, prompt, default) -> str                 │  │
│  │ + show_banner()                                                       │  │
│  │ + run_command(cmd, check, capture, shell) -> str | None               │  │
│  │ + check_tool(tool, tracker) -> bool                                   │  │
│  │ + is_git_repo(path) -> bool                                           │  │
│  │ + init_git_repo(path, quiet) -> Tuple[bool, str | None]               │  │
│  │ + merge_json_files(existing, new, verbose) -> dict                    │  │
│  │ + handle_vscode_settings(sub_item, dest, rel_path, verbose, tracker)  │  │
│  │ + download_template_from_github(...) -> Tuple[Path, dict]             │  │
│  │ + download_and_extract_template(...) -> Path                          │  │
│  │ + ensure_executable_scripts(path, tracker)                            │  │
│  └───────────────────────────────────────────────────────────────────────┘  │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 6. Deployment Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        SPEC KIT DEPLOYMENT                                   │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                         GitHub Repository                            │    │
│  │                    github.com/github/spec-kit                        │    │
│  └────────────────────────────┬────────────────────────────────────────┘    │
│                               │                                              │
│           ┌───────────────────┼───────────────────┐                         │
│           │                   │                   │                         │
│           ▼                   ▼                   ▼                         │
│  ┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐               │
│  │  GitHub Actions │ │ GitHub Releases │ │  GitHub Pages   │               │
│  │    (CI/CD)      │ │   (Artifacts)   │ │    (Docs)       │               │
│  └────────┬────────┘ └────────┬────────┘ └────────┬────────┘               │
│           │                   │                   │                         │
│           │    Creates        │   Hosts           │   Hosts                 │
│           ▼                   ▼                   ▼                         │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                         Distribution                                 │    │
│  │                                                                      │    │
│  │  ┌────────────────────────┐  ┌────────────────────────────────────┐ │    │
│  │  │     PyPI Package       │  │      Template ZIP Assets           │ │    │
│  │  │     specify-cli        │  │  spec-kit-template-{agent}-{type}  │ │    │
│  │  └───────────┬────────────┘  └────────────────┬───────────────────┘ │    │
│  │              │                                │                      │    │
│  └──────────────┼────────────────────────────────┼──────────────────────┘    │
│                 │                                │                           │
│                 ▼                                ▼                           │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                       User Installation                              │    │
│  │                                                                      │    │
│  │   $ pip install specify-cli        OR                               │    │
│  │   $ uvx specify-cli init my-project                                 │    │
│  │                                      │                               │    │
│  │                                      ▼                               │    │
│  │                             Download Template                        │    │
│  │                             from GitHub Release                      │    │
│  │                                                                      │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```
