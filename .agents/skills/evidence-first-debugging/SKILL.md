---
name: evidence-first-debugging
description: Use for build failures, runtime crashes, failing tests, dependency conflicts, integration failures, device incompatibility, performance regressions, or unexpected outputs. Reproduce the failure, preserve evidence, localize the failing layer, test discriminating hypotheses, apply the smallest fix, and require proof before proposing architecture replacement.
---

# Evidence-First Debugging

## Objective

Diagnose and repair failures without speculative rewrites or premature technology switching. Every code change must be justified by evidence, not intuition.

## Freeze Scope

- Pause feature expansion during diagnosis.
- Do not change the primary architecture, framework, language, build system, or core dependency strategy.
- Preserve the failing state and create a Git checkpoint when safe.

## Step 1: Establish the Failure

Record in `docs/debug/DEBUG_REPORT.md`:

- Expected behavior vs actual behavior
- Exact reproduction steps
- Target environment and versions
- Complete error text, logs, stack trace, exit code, and timestamps
- Whether the failure is deterministic
- Last known working state and relevant diff

Do not paraphrase away important log details.

## Step 2: Localize the Failing Layer

Classify the failure (multiple layers when evidence supports it):

- Environment or permissions
- Toolchain / build system
- Dependency resolution or ABI/API mismatch
- Configuration / secrets / network
- Application logic
- Data / input
- External service / API
- OS / device / hardware compatibility
- Concurrency / resource exhaustion
- Performance / timeout
- Output validation / consumer compatibility

Identify the **earliest confirmed failing boundary**, not merely the last visible symptom.

## Step 3: Build a Hypothesis Table

For each plausible root cause:

| Field | Content |
|-------|---------|
| Hypothesis | What might be wrong |
| Supporting evidence | What points to this |
| Contradicting evidence | What argues against |
| Confidence | High / Medium / Low |
| Cheapest discriminating experiment | Minimum test to verify |
| Expected result if true | What we'd observe |
| Expected result if false | What we'd observe instead |
| Risk | What could go wrong |

Order experiments by information gain, cost, and safety. Prefer read-only checks and minimal isolated reproductions.

## Step 4: Run One Experiment at a Time

- Change one major variable at a time
- Preserve command output and measurements
- Update the hypothesis table after every experiment
- Distinguish a workaround from a root-cause fix
- One failed patch does NOT prove the architecture is invalid

## Step 5: Apply the Smallest Justified Fix

Before editing, state:

- Confirmed root cause
- Files/configuration to change
- Why the change addresses the cause
- Regression risk
- Rollback method

Apply minimum change, then rerun original reproduction and relevant regression checks.

## Step 6: Validate Resolution

A fix is accepted only when:

- Original reproduction no longer fails
- Relevant tests pass
- Target environment is verified when applicable
- No silent bypass or hidden fallback was introduced
- Measurements satisfy acceptance criteria

## Architecture-Change Threshold

Propose architecture replacement ONLY when evidence demonstrates:

- A hard constraint cannot be met by the current route
- Target platform lacks a required capability with no acceptable path
- Performance remains outside threshold after reasonable corrections
- Dependency, licensing, privacy, or security constraints fundamentally invalidate the route

If threshold is met, create `docs/decisions/CHANGE_PROPOSAL.md` with: current failure, evidence, why implementation fixes are insufficient, alternatives, impact, migration cost, rollback plan, recommendation, and approval request.

## Completion Criteria

Failure is resolved with evidence-backed root cause, smallest fix applied, and full validation passed. Or architecture-change proposal is documented and awaiting approval.
