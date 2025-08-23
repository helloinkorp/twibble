# CLAUDE.md

**References:** PRD.md, FUNCTIONAL_SPEC.md

## Non-negotiables

• Follow PRD.md and FUNCTIONAL_SPEC.md exactly; ask before deviating
• Mobile-first, accessible UI; offline-first PWA; never block progress due to missing assets
• Students do not sign up; progress is local; privacy-first

## Plan-First Policy

• **Every new feature/change must start with `/generate-prp` and an approved PRP in PLAN.md**
• **Implementation requests without a PRP are declined** with instructions to run `/generate-prp <feature>`
• **All PRPs must anchor to PRD.md, FUNCTIONAL_SPEC.md, DESIGNSYSTEM.md, and CLAUDE.md**
• **Acceptance criteria must be specific, testable, and mapped to Functional Spec**

## Code Rules

• **TypeScript + ES modules:** strict typing; no deprecated APIs
• **Small, pure functions:** clear types for lesson, schedule, progress, assets
• **Prefer composition over inheritance:** keep components small and testable

## Testing & Quality Gates

• **Unit tests:** scheduler, chunker, storage
• **E2E tests:** teacher→student flows
• **Block deploy on failures:** all quality gates must pass

## Asset/TTS Policy

**Priority order:** local cache → curated (Supabase mappings) → APIs (Pixabay/ElevenLabs) → fallbacks (placeholders/browser TTS)  
**Never block progression:** activities must work even with missing assets

## Context Hygiene

• **Single-goal sessions:** complete one task fully before moving to next
• **After each task:** run `/checkpoint`
• **Update documentation:** keep PLAN.md current
• **Compress context:** run `/compact` when needed

## Subagents

Use specialized agents with minimal permissions; run in parallel when possible:
• **planning-engineer:** PRP generation, PLAN.md updates, requirements anchoring
• **code-reviewer:** ESM compliance, accessibility, security validation
• **test-engineer:** cross-platform testing, performance benchmarks
• **docs-generator:** maintain PRD/spec alignment, API documentation
• **devops-agent:** deployment preparation, environment validation
• **ux-a11y-auditor:** WCAG 2.1 AA compliance, mobile usability