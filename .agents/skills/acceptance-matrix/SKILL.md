---
name: acceptance-matrix
description: Use before starting any development phase to define measurable completion criteria. Creates a frozen acceptance matrix covering main path, required evidence, prohibited actions, exit conditions, and incomplete states. Prevents retroactive lowering of standards after results fail.
---

# Acceptance Matrix Gate

## Objective

Ensure every development phase has a clear, frozen definition of "done" before any code is written. Prevents the common failure mode of moving the goalposts when results don't meet expectations.

## When to Use

- Starting a new development phase or milestone
- Beginning a new feature, bug fix batch, or integration task
- Any time the team needs to agree on what "complete" means

## Non-negotiable Behavior

- Matrix must be frozen BEFORE coding begins
- Never modify the matrix after results are known
- Never delete evidence requirements or lower PASS thresholds retroactively
- If the matrix is missing, state is `MATRIX_REQUIRED` — no implementation allowed

## Step 1: Define the Five Categories

Create or update `docs/CURRENT_TASK.md` with the acceptance matrix containing:

### 1. Main Path
The single most important end-to-end journey the user needs to complete, starting from a specific product entry point.

### 2. Required Evidence
What proof must be obtained before declaring PASS:
- Test results (unit, integration, e2e)
- Screenshots or recordings
- Log extracts
- Build artifacts
- Device/simulator verification

### 3. Prohibited Actions
What must NOT be changed or used during this phase:
- Modules not in scope
- Demo/fallback implementations
- Lowered thresholds

### 4. Exit Conditions
Specific conditions that must ALL be true for PASS, with corresponding evidence levels:
- `BUILD_VERIFIED` — build passes
- `COMPONENT_VERIFIED` — component tests pass
- `SIMULATOR_VERIFIED` — simulator verification passes
- `DEVICE_VERIFIED` — real device verification passes

### 5. Incomplete States
What status to use when evidence is missing:
- `PARTIAL_PASS` — some criteria met, others not
- `BLOCKED` — external dependency or decision pending
- `HUMAN_DECISION` — requires user input

## Step 2: Freeze the Matrix

- Review the matrix for completeness and measurability
- Each criterion must have a clear pass/fail threshold
- Commit the matrix to `docs/CURRENT_TASK.md`
- Create a Git checkpoint after the matrix is frozen

## Step 3: Execute Against the Matrix

- Implement the phase
- Run all verification steps
- Record actual evidence obtained

## Step 4: Grade Against the Matrix

At phase end, compare results item-by-item against the matrix:
- Only criteria with actual evidence count as met
- Do not write "PASS" when only implementation or build is done
- Every status elevation must trace to a specific matrix evidence item

## Completion Criteria

The matrix is complete when all five categories are filled with measurable, phase-specific content (not generic placeholders), and the matrix is committed before any code changes.
