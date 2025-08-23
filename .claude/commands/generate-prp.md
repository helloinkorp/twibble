---
name: generate-prp
description: Plan a new feature/change using planning-engineer for comprehensive PRP generation
usage: "generate-prp <feature>"
---

# /generate-prp

## Purpose
Plan a new feature/change by calling planning-engineer to create comprehensive PRP and update PLAN.md.

## Steps

1. **call planning-engineer with feature request**

2. **planning-engineer anchors to PRD.md, FUNCTIONAL_SPEC.md, DESIGNSYSTEM.md, CLAUDE.md**

3. **planning-engineer generates PRP block with all required sections**

4. **planning-engineer writes PRP to PLAN.md and updates Active Tasks**

5. **validate PRP completeness with checklist**

## Output
• Complete PRP block in PLAN.md
• Updated Active Tasks section  
• Implementation brief for next session
• Validation checklist confirmation

## Implementation Brief Checklist
- [ ] **Goal** clearly stated and aligned to PRD.md
- [ ] **Non-goals** explicitly defined with scope boundaries  
- [ ] **User stories** mapped to specific Functional Spec sections
- [ ] **Acceptance criteria** are specific, testable, and measurable
- [ ] **Dependencies** identified with prerequisite analysis
- [ ] **Risks** assessed with mitigation strategies
- [ ] **Test strategy** covers unit, E2E, and accessibility validation
- [ ] **Task list** contains atomic, implementable items
- [ ] **PLAN.md** updated with Active Tasks and decisions
- [ ] **Next session focus** clearly defined

## Validation Gates
**Command will FAIL if:**
- Feature request is ambiguous or incomplete
- Acceptance criteria are not testable
- Missing traceability to Functional Spec
- Dependencies not properly analyzed
- No clear test strategy defined

**Planning-engineer will ask targeted questions if clarification needed**