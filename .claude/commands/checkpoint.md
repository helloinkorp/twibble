---
name: checkpoint
description: Safe snapshot after a small, complete task.
usage: "checkpoint"
---

# /checkpoint

## Purpose
Safe snapshot after a small, complete task.

## Steps

1. **run lint, typecheck, unit tests**

2. **call docs-generator to update PLAN.md (what changed, what's next)**

3. **create a conventional commit message interactively or from summary**

4. **push branch or main (configurable)**

## Output
test summary, commit hash