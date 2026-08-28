---
name: ai-llm-integration
description: Use when integrating LLM/AI services into any application. Covers trust boundaries, local validation gates, prompt design for high-uncertainty inputs, and degradation strategies. Ensures AI outputs are validated by deterministic rules before being treated as facts.
---

# AI/LLM Integration Principles

## Objective

Integrate LLM/AI services safely by establishing clear trust boundaries, using local deterministic rules as the final authority, and designing prompts that balance exploration with safety.

## Principle 1: Local Validation Is the Authority

```
LLM returns judgment
     │
     ▼
┌──────────────────────────┐
│ Discard model's           │  Model self-assessment
│ self-assessment           │  (confidence, match, etc.)
│                           │  is NOT trusted
└──────────┬───────────────┘
           ▼
┌──────────────────────────┐
│ Local algorithm computes  │  Deterministic, reproducible,
│ independent confidence    │  auditable
└──────────┬───────────────┘
           ▼
     ┌───────────────┐
     │ Local          │──yes──▶ CONFIRMED
     │ confidence     │
     │ ≥ threshold?   │
     └──────┬────────┘
            │no
            ▼
      DEGRADED / CONSERVATIVE MODE
      (do NOT force-accept model's guess)
```

**Rules**:
- Model's self-reported confidence, match scores, or classifications are NOT the final authority
- Local deterministic validation (DP algorithm, rule engine, checksum, etc.) is the ONLY trusted source
- If local validation fails to confirm, treat as unconfirmed — never force-accept the model's guess
- The threshold for confirmation is set by local rules, not by the model

## Principle 2: Dare to Guess Upstream, Strict Control Downstream

When the input stage has high uncertainty (recognition, search, classification):

**Upstream (identification/search)**:
- **Forbid empty results**: Prompt must require the model to return its best guess, even if uncertain
- **Tell the model it's safe to guess**: "Wrong guesses will be caught by downstream validation"
- **Single best candidate**: Prefer one strong candidate over multiple weak ones

**Downstream (validation/confirmation)**:
- **Strict thresholds**: Local validation uses hard thresholds (e.g., confidence ≥ 0.82)
- **Full rejection over partial acceptance**: If validation fails, reject the entire batch
- **Conservative fallback**: Unconfirmed results get safe default treatment

## Principle 3: Prompt Design Patterns

### Anti-Abandonment Pattern
```
❌ "If evidence is insufficient, return empty array"
✅ "You MUST return the most likely candidate. 
    Wrong guesses will be validated downstream 
    and will NOT cause false confirmation."
```

### Anti-Hallucination Pattern
```
❌ "Return the correct lyrics"
✅ "Only fix spelling errors in the provided text.
    Do NOT add content not present in the input.
    Output count MUST equal input count (1:1 mapping)."
```

### Structured Output Pattern
```
- Use JSON mode (response_format=json_object)
- Fixed temperature=0 for deterministic output
- Explicit schema with required fields
- Dynamic max_tokens based on input size
```

## Principle 4: Degradation Strategy

```
AI service call
     │
     ▼
┌───────────────┐
│ Success?      │──yes──▶ Validate response → Use result
└──────┬────────┘
       │no
       ▼
┌───────────────────────────┐
│ Error in whitelist?       │──no──▶ FAIL (do not degrade)
│ (OFFLINE/TIMEOUT/         │        Auth errors, unknown
│  CONNECTION/RETRYABLE)    │        errors must fail hard
└──────────┬────────────────┘
           │yes
           ▼
┌───────────────────────────┐
│ Local fallback available? │──no──▶ FAIL with safe message
└──────────┬────────────────┘
           │yes
           ▼
     Local fallback applied
     (e.g., original ASR + translation only)
```

**Whitelist-only degradation**: Only explicitly listed error types are allowed to trigger fallback. All other errors must fail hard.

## Principle 5: Data Contract Enforcement

Every AI output must satisfy:
- **1:1 mapping**: Output count = Input count
- **Immutable fields**: IDs, timestamps, ordering cannot be changed
- **Atomic commitment**: Entire batch succeeds or entire batch fails
- **Sanitized errors**: Exception messages must strip API keys, tokens, file paths

## Anti-patterns

- **Trusting model confidence**: "The model said 95% confidence, so it must be right"
- **Allowing empty results**: "If the model can't find it, return nothing" — leads to downstream starvation
- **Partial application**: "3 out of 5 results look good, let's use those" — breaks data contracts
- **Blanket degradation**: "Any error triggers fallback" — auth errors should fail, not silently use stale data

## Completion Criteria

AI integration has local validation gates, trust boundaries are defined, prompts follow anti-abandonment and anti-hallucination patterns, degradation is whitelist-only, and data contracts are enforced.
