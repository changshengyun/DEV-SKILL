---
name: human-decision-boundary
description: Use when determining whether a decision can be made autonomously or requires human approval. Defines clear boundaries between developer-autonomous decisions and those requiring explicit user input. Prevents both unnecessary interruptions and unauthorized major changes.
---

# Human Decision Boundary

## Objective

Clearly separate decisions the developer can make autonomously from those that require explicit human approval. Prevents both over-asking (slowing development) and under-asking (making unauthorized major changes).

## Decision Classification

### Developer-Autonomous (No Approval Needed)

These are working-level decisions that the developer handles independently:

- Implementation choices (which algorithm, which data structure)
- Code error fixes (typos, logic bugs, missing null checks)
- Test failure resolution (fixing assertions, adding missing test cases)
- Build failure fixes (missing imports, configuration typos)
- Simulator/emulator issues (setup, configuration)
- Ordinary configuration adjustments

### Human Decision Required (Must Stop and Ask)

```
Decision arrives
     │
     ▼
┌─────────────────────┐
│ Architecture or     │──yes──▶ 🛑 HUMAN_DECISION
│ tech stack change?  │
└──────────┬──────────┘
           │no
           ▼
┌─────────────────────┐
│ New major           │──yes──▶ 🛑 HUMAN_DECISION
│ dependency or       │
│ large model download?│
└──────────┬──────────┘
           │no
           ▼
┌─────────────────────┐
│ Destructive file,   │──yes──▶ 🛑 HUMAN_DECISION
│ Git, device, or     │
│ user data operation? │
└──────────┬──────────┘
           │no
           ▼
┌─────────────────────┐
│ Product requirement │──yes──▶ 🛑 HUMAN_DECISION
│ conflicts with      │
│ active documents?   │
└──────────┬──────────┘
           │no
           ▼
┌─────────────────────┐
│ Development scope   │──yes──▶ 🛑 HUMAN_DECISION
│ significantly       │
│ expands?            │
└──────────┬──────────┘
           │no
           ▼
┌─────────────────────┐
│ Missing real data   │──yes──▶ 🛑 HUMAN_DECISION
│ that developer      │
│ cannot generate?    │
└──────────┬──────────┘
           │no
           ▼
     Developer decides autonomously
```

## Key Principles

1. **One approval ≠ blanket approval**: Approval for one state transition does NOT authorize subsequent transitions. Each major decision point needs its own approval.

2. **No infinite auto-recursion**: One orchestration cycle may invoke each role at most once. A failed review returns to `HUMAN_DECISION`; another cycle requires explicit human decision.

3. **Stop conditions are explicit**: When a `HUMAN_DECISION` boundary is hit, clearly state:
   - What decision is needed
   - Why it requires human input
   - What options are available
   - What the recommendation is
   - What happens if no decision is made

4. **Don't escalate ordinary problems**: Build failures, test failures, and implementation errors are developer responsibilities. Only escalate when the decision genuinely requires human authority or judgment.

## Examples

| Situation | Autonomous or Human? |
|-----------|---------------------|
| Fix a null pointer exception | Autonomous |
| Choose between two sorting algorithms | Autonomous |
| Replace SQLite with Realm | Human |
| Add a new analytics SDK | Human |
| Fix a broken test assertion | Autonomous |
| Delete user data files | Human |
| Adjust a color constant | Autonomous |
| Change from REST to GraphQL | Human |
| Fix a layout overflow | Autonomous |
| Expand feature to handle payments | Human |

## Completion Criteria

Every decision during a phase has been classified as autonomous or human. All human decisions have been explicitly surfaced with context and options. No unauthorized major changes were made.
