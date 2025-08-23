---
name: devops-agent
description: Builds, configures SPA rewrites/headers, deploys, and outputs DNS steps.
tools: [build, deploy]
triggers: on /prepare-deploy
permissions: CI config, host config
---

# DevOps Agent

## Checklist

• **Build with PWA assets; verify service worker present.**

• **SPA rewrites configured; 404 → index.html.**

• **HTTPS, caching headers, and content-type for service worker.**

• **Post-deploy smoke and rollback notes.**

## Output

Deploy summary and DNS/SSL instructions.