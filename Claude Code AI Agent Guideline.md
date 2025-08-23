Claude Code AI Agent Guideline

1. Principles & Goals
Consistency: Keep workflows, decisions, and conventions explicit and reproducible.

Context Control: Actively manage what the agent “sees” to ensure accuracy and reliability.

Specialization: Divide labor using subagents with clear scopes for minimal context bloat and parallel tasking.

Automation: Use slash commands and hooks to abstract routine actions, accelerate work, and avoid manual errors.

2. Context Engineering
Why: The quality of code, communication, and planning is tightly linked to how you structure, segment, and refresh context.

Workflow
Single-Goal Sessions: Limit conversation scope to one feature or tightly related set of changes. Avoid crossing domains (e.g., don’t discuss database schema while working on UI, unless necessary).

External Memory: Store requirements, technical plans (using PLAN.md/ROADMAP.md), PRDs/PRPs, and critical design files in markdown or doc files. Reference these in sessions for concise, reliable context.

Prompt Engineering: Use deliberate cues like "think harder," "ultrathink," or "deliberate reasoning" to force Claude to provide more structured, multi-step outputs.

PRD/PRP First: For new features/components, prompt the agent to summarize requirements and generate a comprehensive plan (via PRD/PRP or similar)—use as anchor context for all related work.

Context Refresh Management
/clear Frequently: After finishing a task/feature, or when starting a new topic, always clear the context.

/resume for Recovery: If you lose track or need to pause and return, use the resume function to reload a previous conversation's state.

Compacting and Anchoring: For large features, keep PLAN.md and summary reference files up-to-date, and reload only the required sections after clearing context. Use commands (e.g., /compact) to focus on essential context for the next step.

Monitor Token Warnings: When notified of context size issues, offload to files and reset the session.

Explicit Handoffs: If multiple steps are required but context might overflow, have the agent create a detailed prompt capturing only the necessary details for the next session or subagent handoff.

3. Subagents: Design & Deployment
Purpose: Subagents are focused, self-contained assistants with unique roles that can split up workflows, improve accuracy, and isolate context for reliability.

Setup and Organization
Define in .claude/agents/: Place all configuration in project or user-level folders.

Specialization Examples:

Code Reviewer: Focuses on style, security, performance.

Test Engineer: Automates runs/fixes tests.

Docs Generator: Maintains and updates documentation.

DevOps Agent: Handles deployment, CI/CD scripts, and infra configs.

Lang/Framework Expert: Eg., React Expert, Vue Guru.

Best Practices
Clear Role Descriptions: In agent configs, describe explicit triggers ("Invoke on every PR") and task boundaries.

Minimal Permissions: Only allow each subagent access to its required toolkit (e.g., test agent cannot deploy, docs agent can’t commit).

Reusable Prompts: Make agent instructions concise, visible, and reusable. Use markdown front-matter for integration with CLI and direct editing.

Parallelization: Use parallel agents to run separate tasks (review + test + docs), then merge results to main session.

Typical Subagent Config Excerpt
text
---
name: test-engineer
description: Runs and fixes tests on each commit.
tools: [run, test]
---
You are a testing expert. For every code change, proactively run all tests. Summarize issues and attempt fixes while preserving the intent.
Team Consistency: Store critical subagents and their templates in a shared repository for systemic reliability.

4. CLAUDE.md & Rules Files: System Prompt Engineering
Compact & Declarative: Use bullets and short, focused sentences, not prose. Only state what’s essential for the agent’s behavior in this project.

Separate by Scope: Have root-level CLAUDE.md (project-wide) and subfolder CLAUDE.md files for module or team-specific rules.

Update Regularly: Review and trim often, reflecting only up-to-date patterns, conventions, and reminders.

Reference, Don’t Repeat: Instead of bloating rules files, reference external docs and link to team standards.

Dynamic Edits: Use inline prompts or direct file edits to update preferences as workflows evolve.

Example structure:

text
# Code Style
- Use ES modules
- No deprecated APIs

# Git Workflow
- PRs require one passing review
- Tests must pass before merge
5. Automation: Slash Commands & Hooks
Slash Commands: Create markdown/instruction files in .claude/commands/ for common workflows (e.g., codebase prep, priming, deployment, PR fixes).

Parameterization: Use variables in commands for flexible re-use (e.g., /generate-prp <feature_name>).

Hooks: Script actions on events (after save, after agent run, etc) using JSON configs. Use for: running linters, auto-formatting, logging agent activity, and post-test summaries.

6. End-to-End Example Workflow
text
1. Start by stating the target feature or change.
2. Generate and save a formal plan in PLAN.md using the agent.
3. Break down plan into actionable subtasks with /generate-tasks.
4. Assign each subtask to corresponding subagents (review, test, docs, expert).
5. For each completed task:
   - Commit changes (automate with a checkpoint slash command)
   - Update PLAN.md or ROADMAP.md for traceability
   - Use /clear or /compact to refresh session context
6. For returning or multi-session work:
   - Use /resume to continue, or reload context from PLAN.md and related docs.
7. Script repetitive workflows as hooks and slash commands!
7. Key Principles to Enforce
Explicit context planning—never “just wing it.”

Always clear or refresh context between distinct topics or phases.

Use subagents for any specialized, repeatable, or parallelizable task.

Store all requirements, plans, and design files in external markdown—never rely solely on memory.

Regularly review and prune CLAUDE.md, subagent configs, and automations for relevancy.

