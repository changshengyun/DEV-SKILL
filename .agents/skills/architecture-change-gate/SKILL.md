---
name: architecture-change-gate
description: Use before replacing a primary framework, runtime, build system, storage architecture, or core processing pipeline. Requires a written proposal with evidence, impact analysis, alternatives, rollback plan, and explicit user approval. Prevents impulsive technology switches triggered by debugging frustration.
---

# Architecture Change Gate

## Objective

Prevent impulsive architecture changes driven by debugging frustration. Major technology switches must be evidence-backed, formally proposed, and explicitly approved before execution.

## When This Gate Triggers

```
During development or debugging
     │
     ▼
┌──────────────────────────┐
│ Is someone proposing to  │
│ replace the primary      │
│ framework, runtime,      │
│ build system, storage,   │
│ or core pipeline?        │
└──────────┬───────────────┘
           │yes
           ▼
     🛑 STOP — Architecture Change Gate
     Do NOT execute the change yet
```

## Step 1: Evidence Requirement

Before any proposal, establish:

- **What is the confirmed problem?** (not assumed, not feared)
- **Is it an implementation bug or an architecture limitation?**
  - Implementation bugs don't require architecture changes
  - Architecture limitations have evidence that no implementation-level fix can work
- **What evidence proves the current architecture cannot meet the requirement?**

## Step 2: Written Proposal

Create `docs/decisions/CHANGE_PROPOSAL.md` with:

### 2.1 Current Failure
- What is broken or cannot be achieved
- Reproduction steps and evidence
- Impact on users/product

### 2.2 Root Cause Analysis
- Confirmed root cause (not speculation)
- Why implementation-level fixes are insufficient
- Evidence that the limitation is architectural, not incidental

### 2.3 Alternatives Considered
At least three options:

| Option | Description | Pros | Cons | Risk |
|--------|-------------|------|------|------|
| Stay current + patch | Fix within existing architecture | Low cost, low risk | May not solve root problem | ... |
| Partial replacement | Replace only the failing component | Medium cost | Integration complexity | ... |
| Full replacement | Replace the entire subsystem | Solves root problem | High cost, high risk | ... |

### 2.4 Impact Analysis
- Files and modules affected
- Data migration required
- Testing effort
- Timeline estimate
- Who needs to learn new technology

### 2.5 Rollback Plan
- How to return to current state if the change fails
- What data/state needs to be preserved
- Maximum time before rollback decision

### 2.6 Recommendation
- Which option is recommended and why
- Confidence level (High / Medium / Low)
- What would change the recommendation

## Step 3: Approval

```
Proposal written
     │
     ▼
┌──────────────────────────┐
│ User reviews proposal    │
│ Asks questions           │
│ Requests modifications   │
└──────────┬───────────────┘
           │
     ┌─────┴─────┐
     │ Approved?  │
     └─────┬─────┘
      yes  │  no
      │    │   │
      │    │   ▼
      │    │  Revise proposal or reject
      │    │
      ▼    ▼
  Execute  Do NOT execute
  change   (stay with current architecture)
```

## Step 4: Execution (Only After Approval)

- Create Git checkpoint before any changes
- Execute in phases with validation after each phase
- Keep rollback plan ready and tested
- Document deviations from the proposal

## Architecture-Change Threshold

Evidence must demonstrate at least ONE of:

1. A **hard constraint** cannot be met by the current route
2. Target platform **lacks a required capability** with no acceptable implementation path
3. Measured performance remains **outside threshold** after reasonable implementation-level corrections
4. Dependency, licensing, privacy, security, or maintenance constraints **fundamentally invalidate** the route

If none of these are met, the change is NOT justified.

## Anti-patterns

- **"This is hard, let's start over"**: Difficulty is not an architecture reason
- **"The cool kids use X"**: Popularity is not an architecture reason
- **Changing mid-debug**: Switching architecture because debugging the current one is frustrating
- **Skipping the proposal**: "I'll just try it and see" without impact analysis
- **No rollback plan**: Making irreversible changes without a way back

## Completion Criteria

Either: (a) the proposal is written, approved, and execution begins with checkpoint and rollback plan, OR (b) the evidence shows the threshold is not met and the current architecture is retained with a plan to fix the implementation-level issue.
