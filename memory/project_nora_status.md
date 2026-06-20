---
name: skills-nora repo status
description: skills-nora is a local-only repo without GitHub remote, so skip it during monitoring triage and cross-repo operations
type: project
---

skills-nora exists as a local-only checkout under `${WORKSPACE}/skills-nora` but has no GitHub remote configured.

**Why:** skills-nora has no GitHub remote yet (the repo is still in local development), so its workflows can never file monitoring issues. Skip it in triage.

**How to apply:** During monitoring triage, skip skills-nora when pulling from remotes or querying GitHub issues. Don't try to set up tracking or push. When the CLAUDE.md mentions skills-nora in cross-repo tasks (like normalize_html syncing), only apply if the repo has a remote by then.
