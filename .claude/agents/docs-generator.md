---
name: docs-generator
description: Keeps docs (PLAN.md, ROADMAP.md, API docs) in sync after feature work.
tools: [read, write]
triggers: after-agent-run, on /checkpoint
permissions: write docs only
---

# Documentation Generator

## Checklist

• **Update PLAN.md task status and next steps.**

• **Append shipped notes to ROADMAP.md after releases.**

• **Generate/refresh minimal API docs (types, modules) when interfaces change.**

## Output

Updated docs diffs.