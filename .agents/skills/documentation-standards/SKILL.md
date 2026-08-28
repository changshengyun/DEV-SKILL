---
name: documentation-standards
description: Use when creating, updating, or organizing project documentation, bug reports, evidence records, or archival documents. Enforces unified format, standardized bug tracking, and separation of text documents from binary artifacts.
---

# Documentation Standards

## Objective

Maintain consistent, searchable, and archival-friendly project documentation. All text documents use a unified format, binary artifacts are stored separately, and bug reports follow a standardized template.

## Principle 1: Unified Document Format

**All text documents use `.md` (Markdown) format**, including:
- Project state and status documents
- Task descriptions and acceptance matrices
- Handoff reports between phases
- Debug reports and evidence records
- Architecture decisions and proposals
- Archival documents

**Binary artifacts** (screenshots, videos, build outputs) are NOT documents:
- Store in dedicated evidence directories (e.g., `.emulator-test-assets/`, `test-artifacts/`)
- Reference by path in documents, don't embed in Git
- Git tracks path references, not binary files

## Principle 2: Three-Document State Model

Project state is tracked in exactly three documents:

| Document | Role | Content |
|----------|------|---------|
| `DEVELOPMENT_ROADMAP.md` | Planning entry | Long-term direction, module order, key decisions |
| `PROJECT_STATE.md` | State entry | Current gate, decisions, evidence, risks, next action |
| `CURRENT_TASK.md` | Task entry | Active task ID, acceptance matrix, progress |

### Rules
- These three are the **only** active state sources
- Historical documents and chat logs are for traceability only, not current scheduling
- When documents conflict, reconcile using latest verified evidence; if unsafe, escalate to `HUMAN_DECISION`
- Phase end: all three must be updated consistently
- Never create new state documents for each debug session or handoff

## Principle 3: Bug Report Template

All bugs are registered in `docs/debug/BUG_LOG.md` with this format:

```markdown
## BUG-YYYYMMDD-NNN: [Short Chinese/English description]

### 现象描述 (Symptom)
[What the user observed]

### 根因分析 (Root Cause)
[Multi-factor root cause analysis]

### 排查证据 (Evidence)
- Reproduction steps on device
- Controlled experiments
- Log analysis results

### 修复方式 (Fix)
[Specific code location and logic change]

### 验证记录 (Verification)
- Deploy time: YYYY-MM-DD HH:MM
- Unit tests: X/Y passed
- E2E verification: [result]

### 遗留事项 (Follow-up)
- [Items requiring user action or future work]
```

### Bug ID Format
`BUG-YYYYMMDD-NNN` where:
- `YYYYMMDD` = date of registration
- `NNN` = three-digit sequence number (001, 002, ...)

### Commit Convention
Bug documentation is committed together with the fix code. Commit message includes Bug ID and description:
```
BUG-20260828-001: 修复 AI 增强错误提示被门控吞没的问题
```

## Principle 4: Document Lifecycle

```
Document created
     │
     ▼
┌──────────────────┐
│ Active           │  Being updated during current phase
│ (in three docs)  │
└────────┬─────────┘
         │ phase ends
         ▼
┌──────────────────┐
│ Archived         │  Moved to docs-BK/ or docs/archive/
│ (read-only)      │  Referenced for traceability only
└──────────────────┘
```

### Rules
- Active documents are updated at phase boundaries
- Archived documents are read-only historical evidence
- Never reactivate an archived document as current
- Archive naming convention: `docs-BK/` for bulk archive, `docs/archive/` for per-phase archive

## Principle 5: Evidence Recording

Every piece of evidence must include:
- **What**: The exact command or action that produced the evidence
- **When**: Timestamp
- **Where**: Device/simulator identifier, environment details
- **Result**: Quantitative results (test counts, measurements)
- **Artifacts**: File paths to screenshots, logs, build outputs

## Anti-patterns

- **Mixed formats**: Some docs in .md, others in .txt, .log, or .xml
- **Binary in Git**: Committing screenshots and videos to the repository
- **State sprawl**: Creating a new status document for every debug session
- **Incomplete bug reports**: Missing root cause or verification sections
- **Stale active docs**: Keeping old phase documents marked as "current"

## Completion Criteria

All documents use .md format, three-document state model is maintained, bug reports follow the template, binary artifacts are separated, and evidence records include all five required fields.
