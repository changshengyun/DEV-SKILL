---
name: mvp-implementation-gate
description: Use after a technical route has been explicitly approved and the required feasibility spikes have passed. Implement the smallest end-to-end vertical slice, preserve architecture boundaries, verify it in the agreed target environment, and update durable project state.
---

# MVP Implementation Gate

## Preconditions

1. Read `AGENTS.md`, `docs/PROJECT_STATE.md`, `docs/REQUIREMENTS.md`, `docs/TECH_OPTIONS.md`, and `docs/SPIKE_PLAN.md`.
2. Confirm `docs/PROJECT_STATE.md` records explicit technical-route approval.
3. Confirm required spikes passed or the user explicitly accepted the remaining risk.
4. If any precondition is missing, stop and return to `project-architecture-gate`.

## Implementation Workflow

1. Restate the approved stack, hard constraints, vertical-slice input, processing path, output, non-goals, and acceptance checks.
2. Create a Git checkpoint before substantial edits.
3. Break implementation into narrow milestones with a validation after each milestone.
4. Implement the shortest real end-to-end path that produces user-visible value.
5. Prefer one production path over parallel competing implementations.
6. Do not change the approved primary framework, runtime, build system, or core processing pipeline without a change proposal and user approval.
7. Do not hide incomplete work behind mocks, fixed outputs, silent fallbacks, or skipped checks.
8. Run the agreed tests and target-environment verification.
9. Record exact commands, outputs, target device/runtime, measurements, limitations, and remaining assumptions.

## Required Outputs

Update `docs/PROJECT_STATE.md` with:

- Current gate
- Implemented vertical slice
- Commits and checkpoints
- Tests and real-environment results
- Measured performance and resource data
- Known limitations and unresolved risks
- Next smallest permitted iteration

If an architectural blocker appears, stop implementation and invoke `evidence-first-debugging`. Do not rewrite the architecture directly.

## Completion Criteria

The MVP is complete only when the agreed end-to-end path passes the measurable acceptance criteria in the agreed target environment, or the report clearly marks every unverified criterion as not accepted.
