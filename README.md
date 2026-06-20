# skills-monitoring-triage

This repo packages the `monitoring-triage` Claude Code skill and its operational memory for the developer-overheid-nl plugin ecosystem (skills-geo, skills-internet, skills-standaarden, skills-marketplace). It is used both locally by a maintainer and by a scheduled cloud agent that runs the triage daily.

## What's here

- `skills/monitoring-triage/SKILL.md` is the skill: how to sync the content repos, classify open monitoring issues, run the content-stability test, handle release-please PRs, and check marketplace status.
- `memory/` holds the operational knowledge the skill leans on: the per-URL log of confirmed-stable monitoring sources, the recurring Logius beheer patterns, the release-please unsticking workflow, the pending skill updates, and the note on why skills-nora is skipped. See `memory/README.md` for the index.

## How it's used

Locally, this is loaded as a Claude Code skill and invoked when a maintainer triages monitoring issues across the plugin repos.

In the cloud, the repo is cloned by a scheduled routine that runs the triage once a day. The agent resolves a `WORKSPACE` directory, clones the content repos it needs into it, and works through the skill: it sweeps open monitoring issues and release PRs across the GitHub repos, closes the recurring false positives, and reports a short summary.

## Scope and limitations

The cloud agent does GitHub-side triage: open monitoring issues, release PRs, and marketplace status. Work that needs a repo checkout, namely the content-stability tests and any skill PRs, is done by cloning the relevant content repo first, or handed off to a maintainer when a change is non-trivial. A run with nothing to do is the common case and a valid outcome, not a failure.
