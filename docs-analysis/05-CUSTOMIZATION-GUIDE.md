# Spec Kit - Hướng Dẫn Custom Cho Bài Toán Cụ Thể

## 1. Tổng Quan Các Điểm Có Thể Custom

### 1.1 Customization Matrix

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        CUSTOMIZATION OPPORTUNITIES                           │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  LEVEL 1: CONFIGURATION (No Code Changes)                                   │
│  ════════════════════════════════════════                                   │
│  ├── Agent selection (--ai flag)                                            │
│  ├── Script type (--script sh/ps)                                           │
│  ├── Project constitution (memory/constitution.md)                          │
│  └── Template content (templates/*.md)                                      │
│                                                                              │
│  LEVEL 2: TEMPLATE CUSTOMIZATION                                            │
│  ═══════════════════════════════                                            │
│  ├── Spec template sections                                                 │
│  ├── Plan template phases                                                   │
│  ├── Task template structure                                                │
│  ├── Checklist criteria                                                     │
│  └── Slash command workflows                                                │
│                                                                              │
│  LEVEL 3: SCRIPT EXTENSION                                                  │
│  ════════════════════════════                                               │
│  ├── Add custom bash/powershell scripts                                     │
│  ├── Modify existing scripts                                                │
│  └── Add new slash commands                                                 │
│                                                                              │
│  LEVEL 4: CLI EXTENSION                                                     │
│  ═══════════════════════                                                    │
│  ├── Add new CLI commands                                                   │
│  ├── Add new agents                                                         │
│  ├── Modify download sources                                                │
│  └── Custom integrations                                                    │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 2. Use Case 1: Enterprise/Domain-Specific Specs

### 2.1 Bài Toán

Tổ chức muốn thêm các yêu cầu đặc thù (compliance, security, domain-specific) vào process SDD.

### 2.2 Giải Pháp: Custom Constitution

**File:** `memory/constitution.md`

```markdown
# Project Constitution

## Article X: [Domain] Compliance Requirements

Every feature specification MUST include:

### X.1 Data Privacy
- [ ] GDPR compliance checklist completed
- [ ] PII handling documented
- [ ] Data retention policy defined

### X.2 Security Requirements
- [ ] Threat model documented
- [ ] Authentication/Authorization specified
- [ ] Audit logging requirements defined

### X.3 [Industry] Regulations
- [ ] [HIPAA/PCI/SOX] compliance verified
- [ ] Regulatory documentation attached
```

### 2.3 Giải Pháp: Custom Spec Template

**File:** `templates/spec-template.md`

```markdown
# Feature Specification: [Feature Name]

## 1. Overview
<!-- Standard section -->

## 2. User Scenarios & Testing
<!-- Standard section -->

## 3. Functional Requirements
<!-- Standard section -->

## 4. Non-Functional Requirements
<!-- Standard section -->

## 5. [CUSTOM] Compliance Requirements

### 5.1 Data Classification
| Data Type | Classification | Handling |
|-----------|---------------|----------|
| | | |

### 5.2 Regulatory Mapping
| Requirement | Regulation | Evidence |
|-------------|------------|----------|
| | | |

### 5.3 Security Controls
- [ ] Access control defined
- [ ] Encryption requirements specified
- [ ] Audit trail requirements documented

## 6. Success Criteria
<!-- Standard section -->

## 7. [CUSTOM] Compliance Sign-off
- [ ] Legal review completed
- [ ] Security review completed
- [ ] Privacy review completed
```

---

## 3. Use Case 2: Multi-Team/Microservices

### 3.1 Bài Toán

Organization có nhiều teams, mỗi team quản lý microservices khác nhau, cần coordination specs.

### 3.2 Giải Pháp: Extended Directory Structure

```
specs/
├── 001-user-service/              # Team A
│   ├── spec.md
│   ├── plan.md
│   ├── contracts/
│   │   ├── api.yaml               # OpenAPI
│   │   └── events.yaml            # Event schemas
│   └── dependencies.md            # NEW: Cross-service deps
│
├── 002-payment-service/           # Team B
│   ├── spec.md
│   ├── plan.md
│   ├── contracts/
│   │   └── api.yaml
│   └── dependencies.md
│
└── _shared/                       # NEW: Shared artifacts
    ├── domain-model.md            # Ubiquitous language
    ├── integration-contracts/     # Service contracts
    │   ├── user-payment.yaml
    │   └── user-notification.yaml
    └── architecture-decisions/    # ADRs
        ├── 001-event-sourcing.md
        └── 002-api-versioning.md
```

### 3.3 Giải Pháp: Custom Analyze Command

**File:** `templates/commands/analyze.md` (extended)

```markdown
## Extended Analysis: Cross-Service Consistency

After standard analysis, also check:

### Service Dependencies
1. Read dependencies.md for current feature
2. For each dependency:
   - Verify contract exists in _shared/integration-contracts/
   - Check contract version compatibility
   - Validate event schemas match

### Domain Model Alignment
1. Extract entities from current spec
2. Compare with _shared/domain-model.md
3. Flag any terminology mismatches

### Output: Integration Report
```markdown
## Cross-Service Analysis

### Dependencies Verified
| Service | Contract | Status |
|---------|----------|--------|
| user-service | user-payment.yaml v2.1 | ✓ Compatible |

### Domain Alignment
| Term | This Spec | Shared Model | Status |
|------|-----------|--------------|--------|
| User | Customer entity | User entity | ⚠️ Review |
```
```

---

## 4. Use Case 3: Test-Driven Specification

### 4.1 Bài Toán

Team muốn tích hợp test scenarios trực tiếp vào specs, có thể generate test code.

### 4.2 Giải Pháp: Enhanced Spec Template

**File:** `templates/spec-template.md` (test-focused)

```markdown
## User Scenarios & Testing

### Scenario 1: [Happy Path]

#### Gherkin
```gherkin
Feature: [Feature Name]
  As a [user type]
  I want to [action]
  So that [benefit]

  Scenario: [Scenario Name]
    Given [initial context]
    And [additional context]
    When [action is performed]
    Then [expected outcome]
    And [additional verification]
```

#### Test Data
```json
{
  "input": {
    "userId": "user-123",
    "amount": 100.00
  },
  "expected": {
    "status": "success",
    "transactionId": "txn-*"
  }
}
```

#### Edge Cases
| Case | Input | Expected | Priority |
|------|-------|----------|----------|
| Empty input | `{}` | 400 error | High |
| Invalid user | `userId: "invalid"` | 404 error | High |
| Negative amount | `amount: -10` | Validation error | Medium |
```

### 4.3 Giải Pháp: Custom Test Generation Command

**File:** `templates/commands/generate-tests.md` (NEW)

```markdown
---
description: Generate test files from specification scenarios
scripts:
  sh: scripts/bash/generate-tests.sh --json
  ps: scripts/powershell/generate-tests.ps1 -Json
---

## Outline

1. Read spec.md and extract all Gherkin scenarios
2. Read plan.md for tech stack (test framework)
3. For each scenario:
   - Parse Given/When/Then steps
   - Generate test file skeleton
   - Include test data from spec

## Output Structure
```
tests/
├── integration/
│   └── [feature]-scenarios.test.ts
├── e2e/
│   └── [feature].e2e.ts
└── fixtures/
    └── [feature]-test-data.json
```

## Template Mapping

| Tech Stack | Test Framework | Template |
|------------|----------------|----------|
| Node.js | Jest | jest-template.ts |
| Python | pytest | pytest-template.py |
| Java | JUnit | junit-template.java |
| Go | testing | go-test-template.go |
```

---

## 5. Use Case 4: Custom AI Agent Integration

### 5.1 Bài Toán

Tổ chức sử dụng AI agent tự phát triển hoặc agent chưa được hỗ trợ.

### 5.2 Giải Pháp: Add New Agent Configuration

**File:** `src/specify_cli/__init__.py` (Line ~126)

```python
AGENT_CONFIG = {
    # ... existing agents ...

    # ADD NEW AGENT
    "my-agent": {
        "name": "My Custom Agent",
        "folder": ".my-agent/",
        "install_url": "https://internal.company.com/my-agent/install",
        "requires_cli": True,
    },

    # ADD IDE-BASED AGENT
    "my-ide-plugin": {
        "name": "My IDE Plugin",
        "folder": ".my-ide/",
        "install_url": None,
        "requires_cli": False,
    },
}
```

### 5.3 Giải Pháp: Create Agent-Specific Command Format

**File:** `templates/commands/specify.md` (agent variant)

```markdown
---
description: Create feature specification
# Agent-specific format
format: toml  # or yaml, json (instead of default markdown)
---

# For agents that use TOML config (like Gemini)
[command]
name = "specify"
description = "Create feature specification"

[scripts]
sh = "scripts/bash/create-new-feature.sh --json \"{ARGS}\""
ps = "scripts/powershell/create-new-feature.ps1 -Json \"{ARGS}\""
```

---

## 6. Use Case 5: CI/CD Integration

### 6.1 Bài Toán

Tích hợp SDD workflow vào CI/CD pipeline, auto-validate specs trước khi merge.

### 6.2 Giải Pháp: GitHub Actions Workflow

**File:** `.github/workflows/spec-validation.yml`

```yaml
name: Spec Validation

on:
  pull_request:
    paths:
      - 'specs/**/*.md'

jobs:
  validate-spec:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'

      - name: Install dependencies
        run: |
          pip install specify-cli
          pip install markdown-it-py pyyaml

      - name: Find changed specs
        id: changed
        run: |
          echo "specs=$(git diff --name-only origin/main | grep 'specs/.*\.md')" >> $GITHUB_OUTPUT

      - name: Validate spec structure
        run: |
          python scripts/validate-spec.py ${{ steps.changed.outputs.specs }}

      - name: Check completeness
        run: |
          for spec in ${{ steps.changed.outputs.specs }}; do
            # Check no [NEEDS CLARIFICATION] markers
            if grep -q "\[NEEDS CLARIFICATION" "$spec"; then
              echo "::error::$spec contains unresolved clarification markers"
              exit 1
            fi
          done

      - name: Validate contracts
        run: |
          # Find all API contracts and validate with openapi-spec-validator
          find specs -name "*.yaml" -path "*/contracts/*" | while read contract; do
            openapi-spec-validator "$contract" || exit 1
          done
```

### 6.3 Giải Pháp: Custom Validation Script

**File:** `scripts/validate-spec.py`

```python
#!/usr/bin/env python3
"""Validate spec files for completeness and consistency."""

import sys
import re
from pathlib import Path

REQUIRED_SECTIONS = [
    "## 1. Overview",
    "## 2. User Scenarios",
    "## 3. Functional Requirements",
    "## 4. Success Criteria",
]

FORBIDDEN_PATTERNS = [
    r"\[NEEDS CLARIFICATION",
    r"TODO:",
    r"TBD",
    r"FIXME",
]

def validate_spec(spec_path: Path) -> list[str]:
    """Validate a spec file and return list of errors."""
    errors = []
    content = spec_path.read_text()

    # Check required sections
    for section in REQUIRED_SECTIONS:
        if section not in content:
            errors.append(f"Missing required section: {section}")

    # Check forbidden patterns
    for pattern in FORBIDDEN_PATTERNS:
        matches = re.findall(pattern, content)
        if matches:
            errors.append(f"Found forbidden pattern: {pattern} ({len(matches)} occurrences)")

    # Check success criteria are measurable
    success_section = re.search(r"## \d+\. Success Criteria(.*?)(?=## |$)", content, re.DOTALL)
    if success_section:
        criteria = success_section.group(1)
        if not re.search(r"\d+", criteria):
            errors.append("Success criteria should include measurable numbers")

    return errors

def main():
    all_errors = []
    for spec_file in sys.argv[1:]:
        path = Path(spec_file)
        if path.exists():
            errors = validate_spec(path)
            if errors:
                all_errors.append((spec_file, errors))

    if all_errors:
        for file, errors in all_errors:
            print(f"\n❌ {file}:")
            for error in errors:
                print(f"   - {error}")
        sys.exit(1)
    else:
        print("✅ All specs validated successfully")
        sys.exit(0)

if __name__ == "__main__":
    main()
```

---

## 7. Use Case 6: Custom Output Formats

### 7.1 Bài Toán

Export specs sang các format khác (JIRA, Confluence, Notion, etc.)

### 7.2 Giải Pháp: Export Command

**File:** `templates/commands/export.md` (NEW)

```markdown
---
description: Export spec to external format
scripts:
  sh: scripts/bash/export-spec.sh --json --format "{FORMAT}"
  ps: scripts/powershell/export-spec.ps1 -Json -Format "{FORMAT}"
---

## Supported Formats

| Format | Output | Use Case |
|--------|--------|----------|
| jira | JIRA issues JSON | Import to JIRA |
| confluence | Confluence wiki markup | Documentation |
| notion | Notion blocks JSON | Import to Notion |
| csv | Flat CSV | Spreadsheet tracking |
| html | Standalone HTML | Sharing |

## Outline

1. Read spec.md
2. Parse sections into structured data
3. Transform to target format
4. Output to file or stdout
```

### 7.3 Giải Pháp: Export Script

**File:** `scripts/bash/export-spec.sh`

```bash
#!/usr/bin/env bash

FORMAT="${1:-jira}"
SPEC_FILE="$2"

case "$FORMAT" in
  jira)
    # Convert to JIRA JSON format
    python3 << 'EOF'
import sys
import json
import re

# Parse spec sections
# Generate JIRA issue structure
issues = []
# ... parsing logic ...
print(json.dumps({"issues": issues}, indent=2))
EOF
    ;;

  confluence)
    # Convert to Confluence wiki markup
    sed -e 's/^## /h2. /' \
        -e 's/^### /h3. /' \
        -e 's/\*\*\([^*]*\)\*\*/*\1*/' \
        "$SPEC_FILE"
    ;;

  *)
    echo "Unknown format: $FORMAT" >&2
    exit 1
    ;;
esac
```

---

## 8. Use Case 7: Localization/Multi-Language

### 8.1 Bài Toán

Team đa ngôn ngữ cần specs bằng nhiều ngôn ngữ.

### 8.2 Giải Pháp: Localized Templates

```
templates/
├── spec-template.md              # Default (English)
├── spec-template.vi.md           # Vietnamese
├── spec-template.ja.md           # Japanese
├── spec-template.zh.md           # Chinese
└── commands/
    ├── specify.md                # Default
    └── i18n/
        ├── specify.vi.md
        └── specify.ja.md
```

### 8.3 Giải Pháp: Language Selection

**File:** `src/specify_cli/__init__.py` (extended)

```python
@app.command()
def init(
    # ... existing params ...
    language: str = typer.Option("en", "--lang", help="Template language: en, vi, ja, zh"),
):
    # ...
    template_suffix = f".{language}" if language != "en" else ""
    template_name = f"spec-template{template_suffix}.md"
    # ...
```

---

## 9. Use Case 8: Metrics & Analytics

### 9.1 Bài Toán

Track spec quality metrics over time.

### 9.2 Giải Pháp: Metrics Collection

**File:** `templates/commands/metrics.md` (NEW)

```markdown
---
description: Collect and report spec quality metrics
---

## Metrics Collected

### Completeness Metrics
- Total sections: X
- Completed sections: Y
- Completeness %: Y/X * 100

### Quality Metrics
- Clarification markers: N
- Test scenarios: N
- Requirements count: N
- Success criteria count: N

### Velocity Metrics
- Time from specify to plan: N hours
- Time from plan to implement: N hours
- Total feature time: N hours

## Output Format

```json
{
  "feature": "001-user-auth",
  "metrics": {
    "completeness": 85,
    "clarifications_remaining": 0,
    "test_scenarios": 12,
    "requirements": 8,
    "success_criteria": 5
  },
  "timestamps": {
    "specified_at": "2024-01-15T10:00:00Z",
    "planned_at": "2024-01-15T14:00:00Z",
    "implemented_at": "2024-01-16T18:00:00Z"
  }
}
```
```

---

## 10. Implementation Roadmap

### 10.1 Quick Wins (1-2 days)

| Customization | Effort | Impact |
|---------------|--------|--------|
| Custom constitution | Low | High |
| Add template sections | Low | Medium |
| Custom checklist items | Low | Medium |

### 10.2 Medium Effort (1-2 weeks)

| Customization | Effort | Impact |
|---------------|--------|--------|
| New slash commands | Medium | High |
| CI/CD integration | Medium | High |
| Export formats | Medium | Medium |
| New agent support | Medium | Medium |

### 10.3 High Effort (1+ month)

| Customization | Effort | Impact |
|---------------|--------|--------|
| Full localization | High | Medium |
| Metrics dashboard | High | Medium |
| Custom UI components | High | Low |
| Multi-repo support | High | High |

---

## 11. Best Practices

### 11.1 Template Customization

```markdown
✅ DO:
- Keep standard sections (Overview, Requirements, Success Criteria)
- Add domain-specific sections at the end
- Use consistent formatting
- Include validation criteria

❌ DON'T:
- Remove core SDD sections
- Add implementation details to specs
- Create overly complex templates
- Forget to update checklists
```

### 11.2 Script Extension

```bash
✅ DO:
- Follow existing script patterns
- Support both --json and text output
- Handle errors gracefully
- Document new scripts

❌ DON'T:
- Modify core scripts directly
- Create scripts without PowerShell equivalent
- Hardcode paths
- Skip input validation
```

### 11.3 CLI Extension

```python
✅ DO:
- Add new commands as separate functions
- Maintain backwards compatibility
- Add comprehensive --help text
- Test on all platforms

❌ DON'T:
- Modify existing command signatures
- Break rate-limit handling
- Skip error handling
- Forget SSL/TLS considerations
```

---

## 12. Troubleshooting Custom Setups

### 12.1 Common Issues

| Issue | Cause | Solution |
|-------|-------|----------|
| Template not found | Wrong path | Check `.specify/templates/` structure |
| Script permission denied | Missing chmod | Run `ensure_executable_scripts()` |
| Agent not recognized | Config missing | Add to `AGENT_CONFIG` |
| CI validation fails | Spec incomplete | Check required sections |

### 12.2 Debug Mode

```bash
# Enable debug output
specify init my-project --debug

# Check template content
cat .specify/templates/spec-template.md

# Verify script execution
bash -x scripts/bash/create-new-feature.sh --help
```
