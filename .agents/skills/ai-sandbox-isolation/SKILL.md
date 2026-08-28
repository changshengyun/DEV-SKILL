---
name: ai-sandbox-isolation
description: Use before implementing any AI/LLM feature changes, prompt modifications, threshold adjustments, or new search strategies. Copies relevant code to an isolated sandbox, develops and validates changes there, then merges back only after user approval. Prevents AI experimentation from destabilizing production code.
---

# AI Sandbox Isolation

## Objective

All AI/LLM feature iterations must be developed and validated in an isolated sandbox copy before merging into production source code. This ensures zero risk to the stable codebase during experimentation.

## When to Use

- Changing AI prompts or prompt templates
- Adjusting confidence thresholds or scoring formulas
- Adding new search/retrieval strategies
- Modifying validation logic for AI outputs
- Testing new AI model configurations
- Any change to the AI enhancement pipeline

## Workflow

```
AI feature change requested
     │
     ▼
┌─────────────────────────────┐
│ Step 1: Copy to sandbox     │  Copy Provider, Verifier,
│                             │  Contract, Validator to
│                             │  independent sandbox package
└──────────┬──────────────────┘
           ▼
┌─────────────────────────────┐
│ Step 2: Develop in sandbox  │  All new logic goes
│                             │  ONLY in the sandbox copy
└──────────┬──────────────────┘
           ▼
┌─────────────────────────────┐
│ Step 3: Validate with       │  Run with real data,
│ real data, output report    │  generate comparison report
└──────────┬──────────────────┘
           ▼
     ┌─────────────┐
     │ User         │──no──▶ Back to Step 2
     │ satisfied?   │
     └──────┬──────┘
            │yes
            ▼
┌─────────────────────────────┐
│ Step 4: Merge back          │  Copy validated sandbox
│ to production source        │  results into production code
└─────────────────────────────┘
```

## Sandbox Rules

### Physical Isolation Required
- Sandbox must be a **physical copy** (separate files), not just a different branch
- The sandbox copy must not import from or modify production source
- Changing sandbox = changing sandbox ONLY

### Validation Standards
- **Hard red line**: Error confirmation rate = 0 (zero false confirmations allowed)
- Run with real production data (not synthetic test data)
- Same input must be run multiple times (recommended: 5x) to measure stability
- Generate a comparison report showing metrics across runs

### Report Format
The sandbox validation report must include:
- Input data summary
- Per-stage prompts (full text)
- Per-stage inputs and outputs
- Triggered branches/interceptions
- Results table and summary table
- Stability metrics (consistency across multiple runs)

### Merge Criteria
- User has reviewed and approved the report
- All hard red lines are met
- Stability ≥ 80% (same input produces same output across 5 runs)
- No regression in existing passing scenarios

## Anti-patterns

- **Logical isolation only**: "I'll just use a different branch" — branches share the same code, changes can leak
- **Skipping sandbox**: "It's just a threshold change" — threshold changes affect confidence scoring and can cause false confirmations
- **Merging without approval**: "The tests passed so I merged it" — user must explicitly approve the validation report
- **Testing with synthetic data only**: Real AI behavior differs from synthetic scenarios

## Completion Criteria

Changes are validated in sandbox with real data, report is generated, user has approved, and changes are merged into production source.
