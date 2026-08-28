---
name: change-classification
description: Use when deciding how much verification a code change needs. Classifies changes into S0/S1/S2 levels with proportional verification effort. Prevents both over-testing trivial changes and under-testing complex ones.
---

# Change Classification

## Objective

Apply proportional verification effort based on change risk. Not every change needs the same level of testing — but every change needs at least some.

## Classification Levels

```
Change arrives
     │
     ▼
┌─────────────┐
│ Is it just   │──yes──▶ S0: Check diff only
│ text/const/  │         No build, no test
│ local style? │
└──────┬──────┘
       │no
       ▼
┌─────────────┐
│ Is the       │──yes──▶ S1: Fix → focused verify
│ boundary     │         Max 3 attempts per fault
│ clear?       │
└──────┬──────┘
       │no
       ▼
       S2: Evidence-first analysis
       Prove the failure layer
       before any code change
```

## S0 — Trivial Changes

**Applies to**: Copy changes, constants, local UI style tweaks, comment updates.

**Verification**: 
- Check the exact diff for correctness
- No build, test, or device verification required
- S0 only exempts single-change verification, not phase-end acceptance

**Prohibited**: 
- Classifying complex logic changes as S0
- Splitting a complex change into multiple "small" changes to avoid verification

## S1 — Bounded Changes

**Applies to**: Features or bugs with clear boundaries and known root cause.

**Verification**:
- Locate the issue → Apply fix → Run the closest focused verification
- Same fault allows maximum 3 code-modifying attempts
- After 3 failed attempts → freeze and enter root-cause analysis (see `three-strike-fix` skill)

## S2 — Complex or Unknown Changes

**Applies to**: Build failures, runtime crashes, test failures, dependency conflicts, device issues, integration failures, performance regressions, or problems where root cause is unclear.

**Verification**:
- From the FIRST attempt, execute evidence-first analysis
- Prove which layer is failing and validate hypotheses before changing code
- Use `evidence-first-debugging` skill

## Decision Rules

| Question | Answer | Level |
|----------|--------|-------|
| Only text/constants/style? | Yes | S0 |
| Root cause known + boundary clear? | Yes | S1 |
| Root cause unknown or cross-module? | Yes | S2 |
| Involves architecture change? | Yes | S2 + architecture gate |
| Involves new major dependency? | Yes | S2 + written proposal |

## Anti-patterns

- **Labeling S2 as S0**: "It's just a one-line change" when the one line affects core logic
- **Splitting to avoid verification**: Breaking a complex change into 5 "small" changes
- **Skipping S2 analysis**: Jumping straight to code changes without evidence

## Completion Criteria

Every change has been assigned a level, and the corresponding verification has been performed before the change is considered done.
