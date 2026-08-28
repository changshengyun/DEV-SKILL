---
name: project-architecture-gate
description: Use before production coding for a new software or AI project, a new subsystem, or a major feature with unvalidated technical assumptions. Convert fuzzy requirements into measurable acceptance criteria, audit the development and target environments, compare technical routes, identify fatal assumptions, and design disposable feasibility spikes.
---

# Project Architecture Gate

## Objective

Prevent "write first, discover incompatibility later." Produce evidence-backed project specifications and a technical-validation plan before substantial production implementation.

## Non-negotiable Behavior

- Do not build the complete product in this workflow.
- Do not bulk-install or upgrade tooling before presenting the environment plan.
- Do not select a technology merely because it is popular or familiar.
- Do not convert unknowns into confident claims.
- A hard constraint is a veto condition, not a weighted preference.

## Step 1: Read Durable Context

1. Read `AGENTS.md` and any nested instruction files that apply.
2. Read `docs/PROJECT_STATE.md` if present.
3. Read existing requirement, architecture, decision, environment, and test documents.
4. Inspect the repository and current environment before proposing changes.

## Step 2: Convert the Request into a Testable Specification

Create or update `docs/REQUIREMENTS.md` with:

- User outcome and real usage scenario
- MVP functions and explicit non-goals
- Hard constraints
- Target runtime and minimum supported device/environment
- Privacy, network, cost, licensing, compatibility, resource, and maintenance constraints
- Functional, performance, failure, and observability acceptance criteria
- Assumptions and unknowns

Each acceptance criterion must contain: preconditions, input, action, expected observable result, measurement method, pass/fail threshold, and target environment.

Do not invent precise thresholds without evidence. When a threshold is unknown, label it `TO BE BASELINED` and design a spike to measure it.

## Step 3: Audit Environments

Create or update `docs/ENVIRONMENT_REPORT.md` with:

- Development OS, architecture, hardware, memory, and storage
- Installed SDKs, compilers, runtimes, build tools, and versions
- Network, proxy, permissions, certificates, and download restrictions
- Target runtime/device information
- Compatibility facts, missing components, and version conflicts
- Proposed installation plan and rollback instructions

## Step 4: Compare Technical Routes

Create or update `docs/TECH_OPTIONS.md`.

Present at least three meaningful routes: conservative, balanced, and aggressive/experimental.

Evaluate each route against:
- Hard constraint compliance
- Target compatibility
- Critical path feasibility
- Performance, maturity, ecosystem
- Build and dependency risk
- Debugging and observability
- Privacy, security, licensing, and cost
- Maintenance and migration cost
- Rollback cost

Disqualify routes that violate a hard constraint.

## Step 5: Rank Fatal Assumptions

Create a risk table using impact, uncertainty, verification cost, and reversibility. Prioritize assumptions that could invalidate the project or force architecture replacement.

## Step 6: Design Disposable Technical Spikes

Create or update `docs/SPIKE_PLAN.md`.

For each high-risk assumption define:
- Exact hypothesis and minimal isolated experiment
- Required environment and data
- Expected evidence and quantitative success threshold
- Failure interpretation and cleanup instructions

The spike must be smaller than the MVP and may be discarded.

## Step 7: Produce a Decision Gate

Create or update `docs/PROJECT_STATE.md` with:

- Current gate: `ARCHITECTURE_REVIEW`
- Canonical documents and their paths
- Recommended route and confidence
- Rejected routes and reasons
- Required spikes and unresolved questions
- Decisions requiring user approval
- The only next permitted action

Stop before substantial production implementation. Ask for approval of the technical route and spike plan.

## Completion Criteria

This skill is complete only when the requirements, environment report, option comparison, spike plan, and project state are internally consistent and identify all unverified assumptions that could materially change the architecture.
