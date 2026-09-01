---
name: Local uncommitted changes in repos
description: Repos frequently have uncommitted local changes (uv.lock, SKILL.md edits) that block git pull — always stash first
type: project
---

skills-standaarden and skills-marketplace frequently have uncommitted local changes (modified uv.lock, SKILL.md edits, generated plugin files) that cause `git pull --ff-only` to fail.

**Why:** The user works across multiple repos and sometimes has WIP changes. This has happened in at least 3 sessions (2026-03-08, 2026-03-12, 2026-03-26).

**How to apply:** During monitoring triage startup, use `git stash` before `git pull` if pull fails due to local changes. Always `git stash` as fallback, don't ask the user. Remember to note the stash so it can be popped later if needed.
