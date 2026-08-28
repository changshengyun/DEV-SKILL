---
name: error-handling
description: Use when designing or reviewing error handling, exception propagation, UI error display, degradation strategies, or cancellation handling. Covers state-driven error display, exception message sanitization, whitelist-only degradation, and cancellation-as-separate-state patterns.
---

# Error Handling Patterns

## Objective

Design error handling that is safe, user-friendly, and auditable. Errors must be visible to users, sensitive data must be stripped from messages, degradation must be controlled, and cancellation must be distinguished from failure.

## Pattern 1: Error Display Must Not Be Gated by Running State

### The Bug
```
❌ Error visible when: running=true AND hasError=true
   → But when errors occur, running is set to false
   → Error message is NEVER visible to user
```

### The Fix
```
✅ Error visible when: hasError=true AND errorBelongsToCurrentOperation
   → Error display is independent of running state
   → User always sees feedback when something goes wrong
```

### Rule
Never gate error message visibility on a `running` flag that is set to `false` in all error paths. Display errors independently of the running state.

## Pattern 2: Exception Message Sanitization

All exception messages must be sanitized before being logged or shown to users:

| Strip | Replace With |
|-------|-------------|
| `Authorization: Bearer xxx` | `[REDACTED]` |
| API keys (`sk-*`, tokens) | `[REDACTED]` |
| File paths / URIs | `[REDACTED]` |
| Internal URLs | `[REDACTED]` |

### Implementation
```
class SanitizedException(message: String) {
    init {
        require(!message.contains("Bearer"))
        require(!message.contains("sk-"))
        // Apply sanitization regex before storing
    }
}
```

## Pattern 3: Whitelist-Only Degradation

```
Error occurs
     │
     ▼
┌────────────────────────┐
│ Error kind in          │──no──▶ FAIL HARD
│ degradation whitelist? │        (auth errors, unknown
│                        │         errors must not degrade)
└──────────┬─────────────┘
           │yes
           ▼
┌────────────────────────┐
│ Local fallback         │──no──▶ FAIL with safe message
│ available?             │
└──────────┬─────────────┘
           │yes
           ▼
     Apply local fallback
     Record: which fallback, why, original error kind
```

**Whitelist example**:
- ✅ OFFLINE, CONNECTION, TIMEOUT, RETRYABLE_SERVER → allow degradation
- ❌ AUTHENTICATION, UNKNOWN, VALIDATION → must fail hard

## Pattern 4: Cancellation ≠ Business Failure

```
Async operation
     │
     ▼
┌─────────────────────┐
│ catch               │──▶ RETHROW immediately
│ CancellationException│    Do NOT treat as business error
└─────────────────────┘
     │other exception
     ▼
┌─────────────────────┐
│ catch Throwable     │──▶ Convert to business error state
│                     │    (FAILED, with error kind)
└─────────────────────┘
```

### Rules
- Every `catch Throwable` must first check for `CancellationException` and rethrow
- Cancellation has its own state (e.g., `CANCELLED`), never mixed with `FAILED`
- UI shows different messages for "cancelled" vs "failed"

## Pattern 5: State-Driven Error Display

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ ViewModel    │────▶│ StateFlow    │────▶│ UI (Compose) │
│ writes error │     │ (single      │     │ reads state, │
│ to state     │     │  source of   │     │ displays     │
│              │     │  truth)      │     │ accordingly  │
└──────────────┘     └──────────────┘     └──────────────┘
```

### Rules
- UI layer never receives exceptions — only state
- All errors are converted to state fields (status, errorKind, etc.)
- Error recovery is expressed as state transitions
- Structured error codes (enums) for cross-module error communication

## Anti-patterns

- **Running-gated errors**: Error display depends on `running=true` when errors set `running=false`
- **Raw exception messages in logs**: API keys, file paths, or tokens leak into logcat/crash reports
- **Blanket degradation**: All errors trigger fallback, including auth failures
- **Cancellation as failure**: User cancel shows "Error: operation failed"
- **Exception propagation to UI**: Activity/Fragment catches and displays raw exception messages

## Completion Criteria

Error handling covers all five patterns: display is independent of running state, messages are sanitized, degradation is whitelist-only, cancellation is separate from failure, and UI receives only state (never exceptions).
