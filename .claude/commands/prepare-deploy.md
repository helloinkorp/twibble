---
name: prepare-deploy
description: Pre-deployment gate and handoff with PRP traceability validation.
usage: "prepare-deploy"
---

# /prepare-deploy

## Purpose
Pre-deployment gate and handoff with PRP traceability validation.

## PRP Traceability Check
**REQUIRED BEFORE PROCEEDING:**
1. **verify all deployed features link to PRP entries in PLAN.md**
2. **if missing PRPs found, STOP and instruct: run `/generate-prp <feature>` first**
3. **confirm deployment scope matches approved PRPs**
4. **validate all acceptance criteria are met**

## Steps

1. **validate PRP traceability for all features (see above)**

2. **run lint, typecheck, all tests (unit+E2E)**

3. **run code-reviewer and ux-a11y-auditor; collect blocking issues**

4. **validate PRP acceptance criteria completion**

5. **if clean, call devops-agent to build and deploy; print DNS steps**

## Output
• PRP validation report
• Gate report with agent summaries
• Deploy summary or failure reasons
• Traceability matrix (feature → PRP → tests)

## Failure Conditions
**Deployment will be BLOCKED if:**
- Any feature lacks corresponding PRP in PLAN.md
- PRP acceptance criteria not fully met
- Scope creep beyond approved PRPs detected
- Quality gates fail (tests, code review, accessibility)

**Remediation:**
Run `/generate-prp <feature>` for any unplanned features before deployment