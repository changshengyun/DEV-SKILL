---
name: ai-evaluation
description: Use when evaluating AI/LLM feature quality, comparing prompt versions, or establishing baseline metrics. Builds a golden-set benchmark, runs multiple iterations for stability measurement, and produces comparison reports. Prevents single-run evaluation fallacies.
---

# AI Evaluation Framework

## Objective

Evaluate AI/LLM features with statistical rigor. Single runs are meaningless due to LLM non-determinism. This framework establishes golden-set benchmarks, multi-run stability testing, and structured comparison reports.

## Why Single-Run Evaluation Fails

LLMs produce different outputs for the same input across runs. Testing once tells you nothing about reliability. You need:
- A **golden set** (known correct answers) as baseline
- **Multiple runs** (recommended: 5) to measure stability
- **Comparison reports** to see trends across versions

## Step 1: Build the Golden Set

Create a manually-annotated benchmark:

| Field | Content |
|-------|---------|
| Input | The actual input data (e.g., ASR transcript, user query) |
| Expected output | Human-verified correct answer |
| Known failure points | Specific items that are commonly wrong |
| Difficulty | Easy / Medium / Hard |

**Requirements**:
- At least 3-5 diverse test cases covering different scenarios
- Include edge cases (noisy input, partial data, ambiguous cases)
- Golden set must be versioned alongside prompt versions

## Step 2: Define Core Metrics

| Metric | Definition | Target |
|--------|-----------|--------|
| **Error confirmation rate** (RED LINE) | Cases wrongly marked as "confirmed" | **Must be 0** |
| Confirmation hit rate | Correctly confirmed cases / Total | Higher is better |
| Stability | Same output across 5 runs | ≥ 80% |
| Correction accuracy | Known errors that were correctly fixed | Sample check |
| Output quality | Faithfulness + fluency | Human spot-check |

### Error Confirmation Rate — The Hard Red Line

This is the most critical metric. It measures cases where the system says "confirmed" but the answer is actually wrong. **This must be zero.** Even one false confirmation is unacceptable because it means the system is presenting wrong information as verified fact.

## Step 3: Multi-Run Stability Testing

```
Golden set input
     │
     ├──▶ Run 1 ──▶ Record output
     ├──▶ Run 2 ──▶ Record output
     ├──▶ Run 3 ──▶ Record output
     ├──▶ Run 4 ──▶ Record output
     └──▶ Run 5 ──▶ Record output
              │
              ▼
     ┌──────────────────┐
     │ Compare outputs   │
     │ Calculate         │
     │ stability ratio   │
     └──────────────────┘
```

**Stability = (Number of identical outputs) / (Total runs)**

If stability < 80%:
- The prompt is too sensitive to LLM randomness
- Consider reducing temperature further
- Simplify the prompt or add more constraints
- Add few-shot examples to anchor the output

## Step 4: Generate Comparison Report

For each prompt version, produce:

```
## Version Comparison Report

### Input Summary
- Test cases: N
- Difficulty distribution: X easy, Y medium, Z hard

### Per-Version Results
| Version | Error Rate | Hit Rate | Stability | Notes |
|---------|-----------|----------|-----------|-------|
| v1      | 0         | 60%      | 80%       | Baseline |
| v2      | 0         | 80%      | 60%       | Better accuracy, less stable |
| v3      | 0         | 80%      | 100%      | Best overall |

### Detailed Findings
- What improved in v3 vs v1
- What trade-offs exist
- Recommended version

### Risk Assessment
- Edge cases that still fail
- Known limitations
```

## Step 5: Automation

Build scripts to:
1. Run the same input N times against the current prompt version
2. Collect all outputs
3. Calculate stability ratio
4. Compare against golden set
5. Generate the comparison report automatically

## When to Re-evaluate

- After any prompt change
- After any threshold adjustment
- After adding new search/retrieval strategies
- After model version changes
- Periodically (weekly/monthly) for production systems

## Anti-patterns

- **Single-run evaluation**: "It worked once, so it's fine"
- **No golden set**: Evaluating without known correct answers
- **Ignoring stability**: Only checking accuracy without measuring consistency
- **Manual comparison**: Comparing outputs by eye instead of automated scripts

## Completion Criteria

Golden set is built, multi-run stability is measured (≥5 runs), comparison report is generated, and the recommended version is documented with evidence.
