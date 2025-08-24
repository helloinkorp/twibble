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

## Design System Compliance Check
**REQUIRED FOR ANY UI CHANGES:**
1. **validate all UI components reference DESIGN_SYSTEM.md tokens**
2. **confirm no hardcoded colors, spacing, or typography values**
3. **verify color contrast meets 4.5:1 minimum ratio**
4. **check touch targets are 44px+ on mobile**
5. **validate focus states are implemented and accessible**
6. **if violations found, STOP and require design system compliance fix**

## Steps

1. **validate PRP traceability (see above)**

2. **validate design system compliance (see above) - UI changes only**

3. **run lint, typecheck, unit tests**

4. **call docs-generator to update PLAN.md (what changed, what's next)**

5. **create a conventional commit message interactively or from summary**

6. **push branch or main (configurable)**

## Output
• PRP validation status
• Design system compliance status (for UI changes)
• Test summary 
• Commit hash
• Updated PLAN.md reference

## Failure Conditions
**Command will STOP if:**
- No PRP found in PLAN.md for current work
- Active Tasks don't match current implementation
- Current work scope exceeds approved PRP
- UI changes violate design system contracts
- Color contrast below 4.5:1 ratio
- Hardcoded design values detected
- Touch targets below 44px on mobile

**Remediation:**
- Run `/generate-prp <feature>` to create proper planning before implementation
- Fix design system violations by referencing DESIGN_SYSTEM.md tokens and components