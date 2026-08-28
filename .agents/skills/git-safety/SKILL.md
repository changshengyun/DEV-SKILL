---
name: git-safety
description: Use for all Git operations during development. Enforces precise staging, checkpoint creation before major changes, prohibition of destructive operations, and controlled push policy. Prevents accidental code loss and ensures rollback capability.
---

# Git Safety Protocol

## Objective

Maintain a clean, safe, and auditable Git history. Prevent accidental code loss, ensure rollback capability before risky operations, and keep commits meaningful.

## Core Rules

### Rule 1: Precise Staging

```
Before every commit:
     │
     ▼
┌──────────────────────┐
│ git diff --cached     │  ← Review exactly what will be committed
└──────────┬───────────┘
           ▼
┌──────────────────────┐
│ Only current task     │  ← Never commit unrelated changes
│ files in staging      │
└──────────┬───────────┘
           ▼
┌──────────────────────┐
│ Preserve pre-existing │  ← Don't touch third_party/,
│ dirty state           │     untracked assets, etc.
└──────────────────────┘
```

### Rule 2: Checkpoint Before Risk

Create a checkpoint commit BEFORE:
- Substantial code changes
- Dependency upgrades
- Database migrations
- Configuration changes
- Any operation that could be hard to reverse

### Rule 3: Separate Commit Types

When practical, split into separate commits:
- Environment changes (SDK, toolchain)
- Architecture changes (refactoring, restructuring)
- Feature work (new functionality)
- Bug fixes
- Documentation updates

### Rule 4: Prohibited Operations

| Operation | Why Prohibited |
|-----------|---------------|
| `git reset --hard` | Destroys uncommitted work irreversibly |
| `git clean -f` | Deletes untracked files permanently |
| Force push to main | Rewrites shared history |
| `git checkout -- .` | Discards all working tree changes |

### Rule 5: Push Policy

- **Default: DO NOT push**
- Push only when user explicitly requests it
- Before push, run `git fetch` and record ahead/behind status
- Rebase/merge/push decisions belong to the user

## Workflow Per Phase

```
New phase starts
     │
     ▼
Switch activity docs to IN_PROGRESS
     │
     ▼
Write acceptance matrix
     │
     ▼
Create checkpoint commit ←── Git safety point
     │
     ▼
Implement changes
     │
     ▼
Verify against matrix
     │
     ▼
Create functional commit
     │
     ▼
git fetch → record divergence
     │
     ▼
Report Git status (push only if user asks)
```

## Rollback Protocol

Before any risky operation:
1. Create a named tag or checkpoint commit
2. Document what the rollback point is
3. Verify the checkpoint builds/passes tests
4. Only then proceed with the risky operation

If rollback is needed:
1. Return to the checkpoint commit
2. Investigate what went wrong
3. Create a new branch for the fix attempt

## Completion Criteria

All commits are precise (only task files), all risky operations have rollback points, no destructive operations were executed, and push status is documented.
