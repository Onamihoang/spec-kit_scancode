# Spec Kit - Tài Liệu Phân Tích Toàn Diện

## Giới Thiệu

Đây là bộ tài liệu phân tích chi tiết về repository **Spec Kit** - toolkit của GitHub cho Spec-Driven Development (SDD).

## Mục Lục Tài Liệu

| File | Nội Dung | Tóm Tắt |
|------|----------|---------|
| [01-OVERVIEW.md](./01-OVERVIEW.md) | Tổng Quan Repository | Mục đích, triết lý, đối tượng sử dụng |
| [02-DIRECTORY-STRUCTURE.md](./02-DIRECTORY-STRUCTURE.md) | Cấu Trúc Thư Mục | Chi tiết từng folder và file |
| [03-ARCHITECTURE-DIAGRAMS.md](./03-ARCHITECTURE-DIAGRAMS.md) | Kiến Trúc & Diagrams | Sequence diagrams, data flow, state diagrams |
| [04-MODULES-TECHSTACK.md](./04-MODULES-TECHSTACK.md) | Modules & Tech Stack | Chi tiết code, dependencies, scripts |
| [05-CUSTOMIZATION-GUIDE.md](./05-CUSTOMIZATION-GUIDE.md) | Hướng Dẫn Custom | 8 use cases cụ thể với solutions |

---

## Tóm Tắt Nhanh

### Spec Kit Là Gì?

```
┌─────────────────────────────────────────────────────────────────┐
│                         SPEC KIT                                 │
│                                                                  │
│    GitHub's toolkit for Spec-Driven Development (SDD)           │
│                                                                  │
│    ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌────────┐  │
│    │  Specify │ -> │   Plan   │ -> │  Tasks   │ -> │Implement│  │
│    │  CLI     │    │          │    │          │    │         │  │
│    └──────────┘    └──────────┘    └──────────┘    └────────┘  │
│                                                                  │
│    Specs drive code, not the other way around                   │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Tech Stack

| Category | Technology |
|----------|------------|
| Language | Python 3.11+ |
| CLI Framework | Typer |
| UI | Rich |
| HTTP | httpx |
| Build | hatchling |

### Supported AI Agents (15+)

- Claude Code, GitHub Copilot, Gemini CLI, Cursor
- Qwen Code, Codex CLI, Windsurf, Kilo Code
- Amazon Q, Amp, Auggie, CodeBuddy, Roo, SHAI, opencode

### Key Commands

```bash
# Initialize project
specify init my-project --ai claude --script sh

# Check tools
specify check

# Show version
specify version
```

### SDD Workflow (Slash Commands)

```
1. /speckit.constitution  →  Thiết lập nguyên tắc
2. /speckit.specify       →  Tạo feature spec
3. /speckit.clarify       →  (optional) Làm rõ yêu cầu
4. /speckit.plan          →  Tạo implementation plan
5. /speckit.tasks         →  Tạo task breakdown
6. /speckit.analyze       →  (optional) Kiểm tra consistency
7. /speckit.implement     →  Thực thi tasks
8. /speckit.checklist     →  Validation checklist
```

---

## Điểm Nổi Bật

### Strengths

- Single-file CLI architecture (dễ maintain)
- Multi-agent support (15+ AI assistants)
- Cross-platform (Bash + PowerShell)
- Template-driven quality
- Robust GitHub API handling

### Limitations

- GitHub-dependent releases
- Learning curve for SDD methodology
- Template rigidity for domain-specific needs

---

## Quick Links

### Repository Structure

```
spec-kit/
├── src/specify_cli/     # CLI source code
├── templates/           # Spec/Plan/Task templates
│   └── commands/        # Slash command definitions
├── scripts/             # Automation (bash + powershell)
├── docs/                # DocFX documentation
├── memory/              # Constitution template
└── .github/workflows/   # CI/CD
```

### Key Files

| File | Purpose |
|------|---------|
| `src/specify_cli/__init__.py` | Main CLI (1357 lines) |
| `templates/commands/specify.md` | Feature spec workflow |
| `templates/commands/implement.md` | Implementation workflow |
| `scripts/bash/create-new-feature.sh` | Branch & spec creation |

---

## Hướng Dẫn Đọc

1. **Bắt đầu với**: [01-OVERVIEW.md](./01-OVERVIEW.md) - hiểu mục đích và triết lý
2. **Cấu trúc code**: [02-DIRECTORY-STRUCTURE.md](./02-DIRECTORY-STRUCTURE.md)
3. **Kiến trúc**: [03-ARCHITECTURE-DIAGRAMS.md](./03-ARCHITECTURE-DIAGRAMS.md) - sequence diagrams
4. **Chi tiết kỹ thuật**: [04-MODULES-TECHSTACK.md](./04-MODULES-TECHSTACK.md)
5. **Custom cho dự án**: [05-CUSTOMIZATION-GUIDE.md](./05-CUSTOMIZATION-GUIDE.md)

---

## Về Tài Liệu Này

- **Ngày tạo**: 2025-11-25
- **Phiên bản Spec Kit**: 0.0.22
- **Mục đích**: Phân tích toàn diện repository cho việc hiểu và custom

---

## License

Tài liệu này được tạo để phân tích repository Spec Kit (MIT License).
