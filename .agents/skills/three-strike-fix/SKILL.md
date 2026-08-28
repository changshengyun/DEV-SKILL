---
name: three-strike-fix
description: Use when a simple bug has not been resolved after multiple fix attempts. Allows up to 3 quick fixes with direct verification, then forces a structured root-cause analysis with enumeration and validation. Prevents infinite blind fix attempts.
---

# Three-Strike Fix with Root-Cause Fuse

## Objective

Balance speed and rigor for bug fixing. Allow quick attempts for simple bugs, but enforce a hard stop after 3 failures to prevent blind trial-and-error.

## When to Use

- A bug has been identified and initial fix attempts are underway
- The bug is simple and well-scoped (S1 level)
- Fix attempts are not converging

## Phase 1: Quick Fix Channel (Attempts 1-3)

```
Bug identified
     │
     ▼
┌─ Attempt 1 ──▶ Fix → Deploy → Verify ──▶ Pass? ──yes──▶ Done
     │                                          │no
     ▼                                          ▼
┌─ Attempt 2 ──▶ Fix → Deploy → Verify ──▶ Pass? ──yes──▶ Done
     │                                          │no
     ▼                                          ▼
┌─ Attempt 3 ──▶ Fix → Deploy → Verify ──▶ Pass? ──yes──▶ Done
                                              │no
                                              ▼
                                        🔒 FUSE TRIGGERED
```

**Rules for Quick Fix Phase**:
- Maximum 3 code-modifying attempts
- No intermediate validation required between attempts
- Each attempt MUST be deployed and verified on target device/environment
- No analysis documentation required during this phase

## Phase 2: Root-Cause Analysis (After 3 Failures)

```
🔒 Fuse triggered
     │
     ▼
┌────────────────────────┐
│ Step 1: Enumerate      │  List ALL possible causes
│ all possible causes    │  No filtering yet
└──────────┬─────────────┘
           ▼
┌────────────────────────┐
│ Step 2: Narrow down    │  Eliminate causes with
│ candidate range        │  evidence
└──────────┬─────────────┘
           ▼
┌────────────────────────┐
│ Step 3: Validate each  │  One experiment per
│ candidate individually │  candidate, in order
└──────────┬─────────────┘
           ▼
┌────────────────────────┐
│ Step 4: Confirm root   │  Exactly ONE root cause
│ cause                  │  identified with evidence
└──────────┬─────────────┘
           ▼
┌────────────────────────┐
│ Step 5: One targeted   │  Minimum change that
│ fix                    │  addresses confirmed cause
└──────────┬─────────────┘
           ▼
┌────────────────────────┐
│ Step 6: Full verify    │  Original reproduction
│                        │  + Related scenarios
│                        │  + Phase integration
└──────────┬─────────────┘
           ▼
     Pass? ──yes──▶ Done
           │no
           ▼
     Return to Step 1
     (NO blind attempts allowed)
```

**Rules for Root-Cause Phase**:
- Must enumerate ALL possible causes before narrowing
- Each candidate must be individually validated
- Only ONE targeted fix per confirmed root cause
- Verification sequence: original reproduction → related scenarios → full phase verification
- If still failing → return to Step 1 (full enumeration again)
- **No unanalyzed attempts are allowed after the fuse triggers**

## Hypothesis Table Format

For each candidate root cause, record:

| Field | Content |
|-------|---------|
| Hypothesis | What might be wrong |
| Supporting evidence | What points to this |
| Contradicting evidence | What argues against |
| Confidence | High / Medium / Low |
| Cheapest test | Minimum experiment to verify |
| Expected if true | What we'd see |
| Expected if false | What we'd see instead |

## Anti-patterns

- **Blind retry**: Making the same change again hoping for different results
- **Shotgun debugging**: Changing multiple things at once
- **Premature architecture change**: "The whole approach is wrong" after 3 failed attempts without root-cause analysis
- **Skipping enumeration**: Jumping to the first plausible cause without listing all candidates

## Completion Criteria

Bug is resolved with evidence, OR root-cause analysis has been completed with all candidates documented and the confirmed root cause has a targeted fix with full verification.
