# PLAN.md

## Context

This plan tracks the current milestone and next, mapping directly to PRD.md and FUNCTIONAL_SPEC.md.

## 🚫 **PLAN-FIRST ENFORCEMENT**

### **Mandatory Pre-Implementation Policy**
**NO DEVELOPMENT WORK may begin without:**
1. **Approved PRP in PLAN.md** - Every feature/change must have a Pull Request Proposal
2. **Design System Validation** - All UI work must reference DESIGN_SYSTEM.md contracts
3. **Requirements Traceability** - Clear links to PRD.md and FUNCTIONAL_SPEC.md
4. **Acceptance Criteria** - Specific, testable, measurable outcomes defined

### **Implementation Request Handling**
**If asked to implement WITHOUT a PRP:**
- ❌ **REFUSE the implementation request**
- ✅ **INSTRUCT**: "Run `/generate-prp <feature>` first"
- ✅ **BLOCK**: Development until proper planning completed
- ✅ **REDIRECT**: Point to this policy and PRP requirements

### **Design-System-First Gates**
**ALL UI implementation MUST:**
- [ ] Reference specific DESIGN_SYSTEM.md components and tokens
- [ ] Complete design system compliance checklist
- [ ] Validate color contrast (4.5:1 minimum)
- [ ] Confirm touch targets (44px+ mobile)
- [ ] Test screen reader compatibility

**UI work WITHOUT design system compliance will be REJECTED**

## 🔍 **PRPs (Pull Request Proposals)**

### **Active PRPs**
*All approved features ready for implementation*

1. **[M1] App Infrastructure**
   - **Status**: Planning
   - **Requirements**: App skeleton, routing, role select, localStorage
   - **Design Impact**: Core layout system, navigation components
   - **Acceptance**: Functional routing between pages, localStorage working

### **Pending PRPs**
*Awaiting approval or clarification*

*(None currently)*

### **PRP Template Requirements**
Each PRP must include:
- **Goal** (aligned to PRD.md)
- **Non-Goals** (scope boundaries)
- **User Stories** (mapped to Functional Spec)
- **Acceptance Criteria** (testable requirements)
- **Dependencies** (prerequisite work)
- **Design System Impact** (affected components/tokens)
- **Risks & Mitigations**
- **Test Strategy**

## Current Milestone (M1 → M8)

**M1:** App skeleton, routing, role select, teacher dashboard list, localStorage infra.

**M2:** Create Lesson (manual entry, chips, toggles, dedupe, drag-and-drop).

**M3:** Schedule (40% day 1, exponential decay, last day review-only, review-after-learn validation).

**M4:** Lesson Summary/Share (unique ID, share URL, QR, copy, test-as-student).

**M5:** Student join + dashboard (day gating, progress model).

**M6:** Activities (Vocabulary, Phonics with curated/fallback chunking, Spelling).

**M7:** PWA/offline/caching/quota/error handling; non-blocking fallbacks.

**M8:** Deployment to twibble.co (hosting, SSL, SPA rewrites, CI).

## Active Tasks (Today)

2.1/2.2/2.3 setup, then move to M1 implementation.

## Next Phase: Agent/Automation Implementation

**Phase 3 Kickoff Tasks:**
- Generate 5 subagents in /.claude/agents/ (code-reviewer, test-engineer, docs-generator, devops-agent, ux-a11y-auditor)
- Generate 4 commands in /.claude/commands/ (/checkpoint, /generate-prp, /compact, /prepare-deploy)  
- Generate 2 hooks in /.claude/hooks/ (after-agent-run, pre-deploy)
- Each with full front-matter, permissions, tools, and implementation logic
- Integrate with M1 development workflow

## Risks / Decisions

API keys scoped to teacher-only features; ensure no student PII stored.

## Acceptance Criteria

Each milestone meets Functional Spec behaviors and passes tests.