---
name: checkpoint
description: Safe snapshot after a small, complete task with PRP traceability validation.
usage: "checkpoint"
---

# /checkpoint

## Purpose
Safe snapshot after a small, complete task with PRP traceability validation.

## PRP Traceability Check
**REQUIRED BEFORE PROCEEDING:**
1. **verify current work links to a PRP entry in PLAN.md**
2. **if no PRP found, STOP and instruct: run `/generate-prp <feature>` first**
3. **confirm Active Tasks in PLAN.md match current implementation**

## Steps

1. **validate PRP traceability (see above)**

2. **run lint, typecheck, unit tests**

3. **call docs-generator to update PLAN.md (what changed, what's next)**

4. **create a conventional commit message interactively or from summary**

5. **push branch or main (configurable)**

## Output
• PRP validation status
• Test summary 
• Commit hash
• Updated PLAN.md reference

## Failure Conditions
**Command will STOP if:**
- No PRP found in PLAN.md for current work
- Active Tasks don't match current implementation
- Current work scope exceeds approved PRP

**Remediation:**
Run `/generate-prp <feature>` to create proper planning before implementation