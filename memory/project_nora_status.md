---
name: skills-nora repo status
description: skills-nora is a local-only repo without GitHub remote — skip during monitoring triage and cross-repo operations
type: project
---

skills-nora exists locally at `/Users/anneschuth/overheid-skills/skills-nora` but has no GitHub remote configured.

**Why:** User confirmed on 2026-03-21 ("er hoeft nog geen remote") — the repo is still in local development.

**How to apply:** During monitoring triage, skip skills-nora when pulling from remotes or querying GitHub issues. Don't try to set up tracking or push. When the CLAUDE.md mentions skills-nora in cross-repo tasks (like normalize_html syncing), only apply if the repo has a remote by then.
