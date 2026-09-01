# Operational memory

These files are the operational knowledge the `monitoring-triage` skill relies on during a run. Each is a point-in-time observation, not live state; verify against the current repos before treating any specific issue number, version, or file line as fact.

| File | What it holds |
|------|---------------|
| `project_monitoring_recurring_fps.md` | The single source of truth for the per-URL log of confirmed-stable monitoring sources, with dates, plus the `extract_visible_text()` approach and the two archetype close rules. |
| `project_logius_sections_restructuring.md` | Logius repo beheer patterns (sections/ moves, ReSpec config changes, build-tooling updates) that trigger monitoring but need no skill update. |
| `project_release_please_pr_unstick.md` | Step-by-step workflow for unblocking release-please PRs that have no CI checks and stale platform files. |
| `project_pending_skill_updates.md` | Tracked upstream changes that may warrant skill updates, including which ones are already done. |
| `project_nora_status.md` | Why skills-nora is skipped in triage (local-only, no GitHub remote). |
| `project_forum_beslisboom_dekking.md` | Forum beslisboom JSON:API, the 27/55 coverage analysis, and which standards are deliberately out of scope. |
| `project_triage_routine_disabled.md` | Why the daily cloud routine is off and what that means for backlog size. |
| `project_local_changes_pattern.md` | Repos often carry uncommitted WIP; stash before pulling. |
