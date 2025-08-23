---
name: prepare-deploy
description: Pre-deployment gate and handoff.
usage: "prepare-deploy"
---

# /prepare-deploy

## Purpose
Pre-deployment gate and handoff.

## Steps

1. **run lint, typecheck, all tests (unit+E2E)**

2. **run code-reviewer and ux-a11y-auditor; collect blocking issues**

3. **if clean, call devops-agent to build and deploy; print DNS steps**

## Output
gate report + deploy summary or failure reasons