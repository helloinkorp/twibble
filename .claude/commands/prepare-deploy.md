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

## Design System Compliance Audit
**COMPREHENSIVE PRE-DEPLOYMENT CHECK:**
1. **audit ALL UI components for design system compliance**
2. **validate complete adherence to DESIGN_SYSTEM.md contracts**
3. **verify zero hardcoded design values across entire codebase**
4. **measure color contrast ratios for all text (4.5:1 minimum)**
5. **validate touch target sizes across all interactive elements**
6. **test focus states and keyboard navigation**
7. **if ANY violations found, BLOCK deployment until resolved**

## Steps

1. **validate PRP traceability for all features (see above)**

2. **conduct design system compliance audit (see above)**

3. **run lint, typecheck, all tests (unit+E2E)**

4. **run code-reviewer and ux-a11y-auditor; collect blocking issues**

5. **validate PRP acceptance criteria completion**

6. **if clean, call devops-agent to build and deploy; print DNS steps**

## Output
• PRP validation report
• Design system compliance audit results
• Gate report with agent summaries
• Deploy summary or failure reasons
• Traceability matrix (feature → PRP → tests)
• Design system violations report (if any)

## Failure Conditions
**Deployment will be BLOCKED if:**
- Any feature lacks corresponding PRP in PLAN.md
- PRP acceptance criteria not fully met
- Scope creep beyond approved PRPs detected
- Quality gates fail (tests, code review, accessibility)
- **Design system violations detected:**
  - Hardcoded design values found
  - Color contrast below 4.5:1 ratio
  - Touch targets below 44px on mobile
  - Missing focus states or keyboard navigation
  - Non-compliant UI components

**Remediation:**
- Run `/generate-prp <feature>` for any unplanned features before deployment
- **Fix ALL design system violations by referencing DESIGN_SYSTEM.md contracts**
- **Re-run compliance audit until zero violations detected**