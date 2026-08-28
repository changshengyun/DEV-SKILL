---
name: build-deploy-verify
description: Use after building any application with optional native components, feature flags, or conditional dependencies. Enforces pre-deployment integrity checks and post-deployment smoke tests. Prevents silent capability loss from missing build parameters.
---

# Build, Deploy & Verify

## Objective

Ensure that built artifacts contain all required components before deployment, and verify functionality after deployment. Prevents the common failure of "it builds but doesn't work" due to missing optional components.

## Principle 1: Pre-Deployment Integrity Check

When the project has build switches that control optional components (native libraries, GPU acceleration, proprietary codecs, etc.):

```
Build completes
     │
     ▼
┌──────────────────────────┐
│ Unpack the artifact       │  (APK, IPA, binary, etc.)
│ List all components       │
└──────────┬───────────────┘
           ▼
┌──────────────────────────┐
│ Required components      │──missing──▶ Rebuild with
│ present?                 │             correct flags
└──────────┬───────────────┘
           │present
           ▼
     Deploy to target
           │
           ▼
┌──────────────────────────┐
│ Post-deploy smoke test   │  Check logs for load errors
│ (log inspection)         │  Verify core functionality
└──────────────────────────┘
```

### Checklist

| Check | How |
|-------|-----|
| Required native libraries exist | Unpack artifact, inspect `lib/` or equivalent |
| Build flags were correct | Check build log for expected compiler invocations |
| No silent capability loss | Compare current artifact with known-good baseline |
| Post-deploy logs clean | Check for JNI load errors, missing resource warnings |

### Example: Missing Native Library

**Symptom**: App installs but recognition feature is completely non-functional.
**Root cause**: Build parameter `enableWhisperNative=true` was not passed, so `externalNativeBuild` didn't execute, and the APK is missing `liblyriccaptioner_whisper.so`.
**Detection**: Unpack APK → check `lib/arm64-v8a/` → `.so` not found.
**Fix**: Rebuild with explicit parameter.

## Principle 2: High-Throughput Log Monitoring

When system log throughput is very high (>100 lines/second), traditional tail-based polling fails:

```
❌ Get-Content -Tail N | Poll every 3s
   → Window covers <1 second of logs
   → Events are flushed between polls
   → Script times out even though event occurred

✅ Alternative approaches:
   → UI snapshot parsing (uiautomator dump)
   → Timestamp-range queries
   → Dedicated event bus / structured log aggregation
```

### Rule
When write rate > window refresh rate, fixed-position tail polling will ALWAYS fail. Use an independent data source instead.

## Principle 3: Regression Verification Matrix

Every phase end must run the applicable verification commands:

| Verification | When Required |
|-------------|--------------|
| Unit tests | Always after code changes |
| Lint / static analysis | Always after code changes |
| Standard build | Always |
| Native/full build | When native components are involved |
| Integration tests | After cross-module changes |
| Device/simulator verification | After UI or end-to-end changes |
| Performance benchmarks | After performance-sensitive changes |

### Evidence Recording

Every verification must record:
- Exact command executed
- Test count and pass/fail numbers
- Target device/simulator identifier
- Key artifact paths and sizes
- Timestamp of verification

## Principle 4: Evidence Level Tracking

| Level | Meaning | When to Use |
|-------|---------|-------------|
| `BUILD_VERIFIED` | Build passes | Minimum bar |
| `COMPONENT_VERIFIED` | Component/unit tests pass | After isolated changes |
| `SIMULATOR_VERIFIED` | Simulator/emulator passes | Before device testing |
| `DEVICE_VERIFIED` | Real device passes | Final acceptance |

### Rules
- Lower-level evidence cannot be reported as higher-level
- Simulator evidence ≠ device evidence
- Demo/mock results ≠ real product success
- Phase completion report must state the actual evidence level achieved

## Anti-patterns

- **Skipping pre-deploy check**: "It built successfully, so everything must be there" — optional components can be silently missing
- **Tail polling in high-throughput**: Using `Get-Content -Tail` when logs flow at 800+ lines/second
- **Skipping regression suite**: "Only changed one line, no need to run tests"
- **Upgrading evidence level**: Writing "DEVICE_VERIFIED" when only simulator testing was done
- **No evidence recording**: "Tests passed" without recording how many, which device, or what command

## Completion Criteria

All required verification commands have been executed, evidence levels are correctly reported, pre-deployment integrity is confirmed, and post-deployment smoke test passes.
