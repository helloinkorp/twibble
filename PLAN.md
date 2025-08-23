# PLAN.md

## Context

This plan tracks the current milestone and next, mapping directly to PRD.md and FUNCTIONAL_SPEC.md.

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