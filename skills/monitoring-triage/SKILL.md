---
name: monitoring-triage
description: "Triage and fix monitoring issues across the developer-overheid-nl plugin repos (skills-geo, skills-internet, skills-standaarden, skills-marketplace). Use when the user mentions 'monitoring issues', 'monitoring', 'false positives', 'content monitoring', 'monitoring triage', 'monitoring fixen'."
allowed-tools:
  - Bash(gh issue *)
  - Bash(gh pr *)
  - Bash(gh run *)
  - Bash(gh api *)
  - Bash(gh label *)
  - Bash(git *)
  - Bash(cd *)
  - Bash(uv *)
  - Bash(shasum *)
  - Bash(ls *)
  - Read
  - Edit
  - Write
  - Grep
  - Glob
  - WebSearch
  - WebFetch
---

# Monitoring Triage

Triage, diagnose, and fix content monitoring issues across the developer-overheid-nl plugin repos. Also checks release-please PRs and marketplace status.

## Workspace

This skill works both on a maintainer's laptop and in a scheduled cloud session. The content repos live under a single parent directory referred to here as `WORKSPACE`. Locally that is `/Users/anneschuth/overheid-skills`. In a cloud session it is whatever directory the repos were cloned into, typically the current working directory. Resolve `${WORKSPACE}` to that directory before running any path-dependent command (for example `export WORKSPACE="$(pwd)"` at the start of a cloud run). Every local path in this skill is written as `${WORKSPACE}/<repo>` so it resolves correctly in both environments.

In a cloud session the repos are usually not all present yet. Clone the ones you need first, for example `git clone https://github.com/developer-overheid-nl/skills-geo "${WORKSPACE}/skills-geo"`. The stability test in Step 3 needs `monitor_content.py` from a content repo, so clone that repo before running it.

## How to run this

This skill is procedural but the procedure parallelises well. Two principles:

1. **Fan out aggressively.** When step N has independent sub-tasks (per-repo queries, per-issue body fetches, per-URL stability tests), put them in a single message with multiple Bash calls. Do not write `for repo in ...; do` shell loops when N parallel calls would surface failures sooner and be readable inline. For a large backlog (roughly >8-10 issues each needing an independent stability test or full classification), consider farming per-issue work out via the Workflow/Task tooling; for the typical small run, inline parallel Bash stays correct and is simpler.
2. **Decide, then act.** Each issue gets classified once via the rubric below, then acted on. Do not ask the user mid-flow whether to close obviously-fast-path items. If you have to ask, batch the question to the end.

End-of-run summary should be one paragraph: how many issues closed in each bucket, plus any open follow-ups (PRs created, memory updates). A zero-issue run is a legitimate outcome, not a failure; see "Clean run" below for what to report instead.

## Repos at a glance

| Repo | GitHub | Local path | Role |
|------|--------|------------|------|
| skills-geo | `developer-overheid-nl/skills-geo` | `${WORKSPACE}/skills-geo` | content repo |
| skills-internet | `developer-overheid-nl/skills-internet` | `${WORKSPACE}/skills-internet` | content repo |
| skills-standaarden | `developer-overheid-nl/skills-standaarden` | `${WORKSPACE}/skills-standaarden` | content repo |
| skills-marketplace | `developer-overheid-nl/skills-marketplace` | `${WORKSPACE}/skills-marketplace` | marketplace |
| skills-developer-overheid-nl | `developer-overheid-nl/skills-developer-overheid-nl` | `${WORKSPACE}/skills-developer-overheid-nl` | no automation yet |

The **three content repos** (geo/internet/standaarden) each run two daily monitoring workflows: `monitoring-content.yml` (content hash drift) and `monitoring-links.yml` (broken links). Both are release-please repos. The **marketplace** repo has its own `check-versions.yml` (plugin-version compare) plus `validate.yml` (CI gate); no release-please. **skills-developer-overheid-nl** (formerly `developer-overheid-nl-agent-skills`) has a GitHub remote but no monitoring workflows, no release-please config, and no tags, so it has nothing to triage today; it stays out of every sweep until it adopts automation.

`skills-nora` is deliberately absent: it is local-only with no GitHub remote, so its workflows can never file issues. Skip it.

So the cardinalities below resolve to: **pull** = the 4 repos with a remote you care about (3 content + marketplace); **monitoring-issue queries** = the 3 content repos; **release-PR sweep** = the 3 content repos only (the only release-please repos).

## Fast-path: known recurring patterns

The overwhelming majority of monitoring issues are recurring false positives that close without a skill change. Two archetype rules cover almost all of them; only a handful of exceptions need different handling. The full per-URL log of confirmed-stable sources (with dates) lives in **one place**: the memory file `memory/project_monitoring_recurring_fps.md`, which is the single source of truth, not this skill. When you confirm a new recurring pattern, add it to the memory file, not here.

### Rule A: HTTP body-hash drift on a reference page

A `published_doc` / `draft_doc` / `*_site` / `forum` / `pdok` / `ncsc` / `geonovum_*` URL flips because the upstream CMS or a ReSpec draft rebuilt the page text.

**Close criterion:** hashes stable across 3 fetches (Step 3) AND the skill only links to the page as reference, describing the standard functionally rather than reproducing page text. Use the "Stable false positive" close template.

Known-noisy prefixes that almost always match Rule A: `geonovum.nl/geo-standaarden` (and subpages), `docs.geostandaarden.nl/nen3610`, `internet.nl/test-mail` + `/test-site` (version-footer flip, use the version-footer template), `forumstandaardisatie.nl/open-standaarden/*`, `ncsc.nl/*`, `pdok.nl/introductie/*`, `gitdocumentatie.logius.nl/publicatie/dk/beveilig`, and every `logius-standaarden.github.io/*` ReSpec draft.

### Rule B: pure-beheer commit in an upstream GitHub repo

A `github_repo` type under `github.com/logius-standaarden/*` or `github.com/Geonovum/*` flips on a beheer/redactioneel commit: NLgov-notatie for standard names, LICENSE add/change, RFC-notatie fixes, link-fixes, `sections/`-moves, markdownlint, schrijfwijze edits, NL-ReSpec-template workflow chores (`Snapshot + checks gegenereerd`), develop→main / status merges, or dependabot bumps in `examples/`.

**Close criterion:** read the commits (`gh api repos/OWNER/REPO/commits`). If they are purely beheer as above, close without skill change. The matching skill describes the koppelvlak / standard functionally (rule IDs, attribute names), so notatie and editorial changes do not touch it.

### Exceptions: these do NOT auto-close

| Pattern | Skill | Why it is different |
|---------|-------|---------------------|
| `github.com/Geonovum/ogc-checker`, **new tag** (not a dependabot bump) | geo-api | `geo-api/SKILL.md` pins the ogc-checker version in two places (`(v1.0.x)`). A new tag needs a version-pin bump (PR), then close with a reference to the PR. Pure dependabot bumps still fall under Rule B. |
| `github.com/internetstandards/Internet.nl`, new release | inet | Read the release notes. Usually TLS/API detail the skill covers functionally, so close. But a genuinely new test or removed test may need a skill update. |
| `github.com/logius-standaarden/st-saml-spec`, version publication | ls-iam | The skill tracks the st-saml-spec version explicitly (`conflicts.md` + SKILL.md table). Close only if the published version is already recorded there; otherwise update the skill. |
| `github.com/logius-standaarden/logboek-extensie-lezen`, OpenAPI method change | ls-logboek | Safe to close only because the skill is HTTP-method-agnostic (filters on `trace_id`/tijdsbereik/betrokkene). A change to the filter *semantics* would need a skill update. |

When in doubt for an exception, fall through to the full Step 2 rubric instead of fast-pathing.

## Step 1: sync repos and pull all signal in parallel

Run these as parallel Bash calls in a single message:

- `git -C ${WORKSPACE}/skills-geo pull --ff-only` (and the same for skills-internet, skills-standaarden, skills-marketplace), four parallel calls.
- `gh issue list --repo developer-overheid-nl/skills-geo --label monitoring --state open --json number,title,createdAt,labels --limit 100` for each of the three content repos, three parallel calls.
- `gh pr list --repo developer-overheid-nl/REPO --label "autorelease: pending" --state open --json number,title,createdAt,mergeable --limit 10` for the three content repos (geo/internet/standaarden, the only release-please repos), three parallel calls. Marketplace has no release-please (it is handled in Step 6); skills-developer-overheid-nl has no release automation, so neither is queried here.
- Marketplace status: latest `check-versions.yml` runs, open `automated-bump` PRs, recent failed runs, three parallel calls.

That is one message, ~13 Bash calls, full picture in one round-trip.

Note: monitoring issues carry one of two labels. The content workflow (`monitoring-content.yml`) files `monitoring,content-changed` for both genuine content drift and unreachable sources (the "Bron onbereikbaar" distinction is in the issue title/body, not a separate label). The links workflow (`monitoring-links.yml`) files `monitoring,broken-link`. Listing on `monitoring` alone catches both kinds.

## Step 2: classify with this rubric

Walk the open issues in order. For each, apply the first matching rule:

1. **Fast-path match** (rules above): fast-path action, skip the rest.
2. **Bulk hash reset**: many/all issues created on the same day, all "body hash verschilt", and `git log -10 scripts/monitor_content.py` shows a recent change to `extract_visible_text()` (or a rename of it). Close all in bulk with the bulk-reset comment.
3. **Broken link** (label `monitoring,broken-link`, one rolling issue per repo titled `[monitoring] Broken links gedetecteerd (YYYY-MM-DD)`): open the lychee report in the issue body. For each dead link, decide moved vs. gone. If moved, fix the link in `SKILL.md`/`reference.md` via a PR. **Do NOT manually close this issue while links are still dead.** The workflow auto-closes it on the next 06:00 run once all links resolve, and re-comments while they don't, so a manual close just gets reopened. Note lychee already accepts 403 and excludes `example.com`/`.nl`/`.org` (and a few per-repo extras), so anything reported is a genuine non-200/3xx.
4. **Unreachable source** (issue title `[monitoring] Bron onbereikbaar: <url>`, label `monitoring,content-changed`): fetch the URL once. If 4xx/5xx persists, check whether the page moved (search the new site for the title). If moved, update the skill link and close. If down temporarily, leave open with a comment.
5. **GitHub repo change**: type `github_repo` in the issue body. Read the release notes / commits via `gh api repos/OWNER/REPO/releases/latest` or `gh api repos/OWNER/REPO/commits`. Decide: does the skill reference anything that changed? If no, close as "upstream wijziging gecontroleerd, skill-content is nog actueel". If yes, edit the skill, open a PR, close the issue with reference to the PR.
6. **HTTP resource change**: any non-`github_repo` type (the body-hash path: `published_doc`, `draft_doc`, `forum`, `geonovum_doc`, `geonovum_site`, `internet_nl`, `pdok`, `ncsc`, etc.). Run the 3-fetch stability test (Step 3). If stable: editorial change on their side, check if the skill reproduces specific page content. If unstable: false positive in `extract_visible_text()`, fix the function (Step 4).

Close-comment templates (Dutch, no co-author):

```
# Bulk hash reset
Gesloten: eenmalige hash-reset door wijziging in extract_visible_text(). Alle hashes zijn herberekend, content is niet daadwerkelijk gewijzigd.

# Stable false positive (recurring CMS edit)
Gesloten: hashes stabiel over 3 fetches. <kort wat de pagina wijzigde>; skill <X> verwijst er alleen als bron naar en de inhoud is nog actueel.

# Internet.nl version-footer flip
Gesloten: pagina-tekst verandert door versiebump in de footer (zichtbaar v<X.Y.Z>, was v<A.B.C>). Skill inet beschrijft de test functioneel en is niet versie-gebonden.

# GitHub upstream change, no skill update
Gesloten: upstream <repo> release v<X.Y.Z> gecontroleerd. <Korte samenvatting van wat veranderde>. Skill <X> blijft op functioneel niveau accuraat. Geen skill-content update nodig.

# Real change, skill updated
Gesloten: skill-content bijgewerkt in PR #<NR>.

# Broken link fixed (let the workflow auto-close; comment only)
Verplaatste link(s) bijgewerkt in PR #<NR>. De resterende links zijn bereikbaar; deze issue sluit automatisch zodra de volgende link-check groen is.
```

## Step 3: stability test (parallel)

When you need to verify HTTP-resource stability, fetch each URL three times and compare hashes. Run all URLs **in parallel**, not in a serial Python loop.

In a cloud session, `git clone` the content repo whose `extract_visible_text()` you want to use first, so `monitor_content.py` is on disk.

Generate one Bash call per URL like this (substitute the URL and the repo whose `extract_visible_text()` you want to use):

```bash
cd /tmp && uv run --with requests python <<'PY'
import sys, hashlib, time, urllib.request
sys.path.insert(0, '${WORKSPACE}/skills-geo/scripts')
from monitor_content import extract_visible_text

url = "https://example.com/path"
hashes = []
for i in range(3):
    req = urllib.request.Request(url, headers={"User-Agent": "monitoring-test/1.0"})
    with urllib.request.urlopen(req, timeout=30) as resp:
        normed = extract_visible_text(resp.read().decode('utf-8', errors='replace'))
    hashes.append(hashlib.sha256(normed.encode()).hexdigest()[:16])
    time.sleep(2)
print(url, "STABLE" if len(set(hashes)) == 1 else "UNSTABLE", hashes)
PY
```

If unstable, diff the normalized text between two fetches to find the varying element, then fix `extract_visible_text()`.

## Step 4: fix `extract_visible_text()` across all three repos

The three content repos share an **identical** `monitor_content.py`. Any fix to `extract_visible_text()` (or sibling helpers) must land in all three. As of 2026-04-07 the function strips scripts/styles/noscript and HTML tags rather than running per-CMS regexes; before adding any per-CMS regex, ask whether widening the visible-text approach would do the same job.

Workflow per fix:

1. Edit `scripts/monitor_content.py` in skills-geo. Run `uv run pytest -q` locally.
2. Copy the same diff to skills-internet and skills-standaarden (`cp` or `Edit` the same lines).
3. Open three PRs in parallel (one Bash message, three `gh pr create` calls). Conventional commit `fix:` so release-please will bump.
4. Wait for CI green on all three. Merge with `--admin` if branch protection blocks (this is allowed in the dev-overheid org per project memory).

## Step 5: release-please PRs

For each open `autorelease: pending` PR:

1. **Trigger CI**: GITHUB_TOKEN-created PRs do not trigger workflows. From the repo dir: `gh pr checkout <nr> && git commit --allow-empty -m "chore: trigger CI" && git push && git checkout main`.
2. **Regenerate platform files**: release-please bumps `.plugin/plugin.json`, `pyproject.toml`, and `publiccode.yml` (the neutral sources of truth) but not the platform-specific `.claude-plugin/plugin.json` and `.cursor-plugin/plugin.json`. The `validate` check will fail until those two are regenerated.
   - skills-geo/internet/standaarden: `uv run python scripts/generate_plugin.py`
   - skills-marketplace: `uv run python .github/scripts/generate_marketplace.py`
   Commit, push, back to main.
3. **Review the changelog**: does the version bump match the conventional-commit types in the changelog?
4. **Merge** with `gh pr merge <nr> --repo developer-overheid-nl/<repo> --squash`. If branch protection blocks, add `--admin`.
5. **After merging plugin repos**: check if `marketplace.json` versions need bumping. If yes, edit `marketplace.json`, run `uv run python .github/scripts/generate_marketplace.py`, open a PR.

## Step 6: marketplace status

Three signals to check (in parallel):

- Latest `check-versions.yml` ("Controleer plugin versies") runs (success/failure trend)
- Open `automated-bump` PRs. Before merging one, wait for the `validate` check ("Valideer marketplace", which runs `generate_marketplace.py --check` plus repo-reachability and platform-file checks) to go green; it fires on the `pull_request` event automatically.
- Recent failed runs (often upstream repos with missing/moved `plugin.json`; investigate one-off vs. systemic)

Compare `marketplace.json` versions to upstream tags: `gh api repos/developer-overheid-nl/<repo>/tags --jq '.[0].name'` for each plugin repo, in parallel. If drift exists and there's no automated-bump PR, the workflow may have failed silently. Check the run logs.

## Step 7: write back to memory

If during triage you discovered:

- A **new recurring false-positive pattern** → add a row to `memory/project_monitoring_recurring_fps.md` (the single source of truth for the per-URL log). Only edit the fast-path rules in this skill if the new pattern is a genuine *exception* that does not fit Rule A or Rule B above.
- A **completed pending skill update** → mark it done in `memory/project_pending_skill_updates.md`.
- A **new skill update need** → add it to `memory/project_pending_skill_updates.md`.

Memory updates are part of the run, not optional cleanup.

### Push the memory back to this repo

The operational memory lives in two places: the local memory directory the skill reads during a run, and `memory/` in `developer-overheid-nl/skills-monitoring-triage`, which is what the cloud routine clones. **A local memory edit that is not pushed here is invisible to the next cloud run**, so finish every run that touched memory with this sync:

```bash
git clone https://github.com/developer-overheid-nl/skills-monitoring-triage /tmp/mt-sync
LOCAL_MEM="$HOME/.claude/projects/-Users-anneschuth-overheid-skills/memory"
for f in /tmp/mt-sync/memory/*.md; do
    name="$(basename "$f")"
    [ "$name" = "README.md" ] && continue
    diff -q "$f" "$LOCAL_MEM/$name" >/dev/null 2>&1 || echo "DRIFT $name"
done
```

Copy over every file that reports DRIFT, add any genuinely new operational memory, update `memory/README.md` when you add a file, and commit.

**Only operational triage knowledge belongs here.** This repo is **public**. The local memory directory also holds personal memory (`user_role.md`, `feedback_*.md`) and the `MEMORY.md` index; never copy those. Sync only the `project_*.md` files that a triage run actually uses, and check the contents of anything new before pushing it.

Do the same in the other direction when this repo's `SKILL.md` is ahead of the local copy: the local skill is a symlink into `~/dotfiles`, so update `~/dotfiles/claude/skills/monitoring-triage/SKILL.md` and commit there.

## Clean run (nothing to do)

A run with zero open monitoring issues, zero `autorelease: pending` PRs, and a healthy marketplace is the common case, not an error. When that happens, skip the per-bucket summary and instead: confirm the latest `monitoring-content.yml` and `monitoring-links.yml` runs went green in each content repo (`gh run list --workflow monitoring-content.yml --limit 1`), confirm marketplace `check-versions.yml` is green, and report a one-line clean summary. Do not invent work.

## Important notes

- The three content-monitoring repos have an **identical** `monitor_content.py`. Always apply fixes to all three.
- `extract_urls.py` differs per repo (different URL patterns per domain). Do not copy it across.
- The monitoring workflow stores `checksums.json` in the GitHub Actions cache. The **save** key is suffixed with `${{ github.run_id }}` (caches are immutable, so each run writes a fresh entry), but the **restore** step uses `restore-keys: monitoring-checksums-` (a prefix), which recovers the most recent prior entry. So state does persist across runs; the run-id suffix only forces a new write, it does not mean each run starts blank.
- `manage_issue()` uses bulk-fetch + exact title match (not `--search`) to avoid issues with special characters.
- GitHub's "Sluit #N" (Dutch close keyword) does NOT auto-close issues. Use `gh issue close` explicitly.
- When running Python scripts in these repos, always use `uv run python`. They use uv for dependency management.
- PRs MUST NOT have co-author lines.
- All repos have platform-specific generated files (`.claude-plugin/`, `.cursor-plugin/`) that must be regenerated after version changes; the `validate` CI check fails if these files are out of sync.
- `audit-visibility.yml` (skills-internet and skills-standaarden only, not geo/marketplace) is latent infrastructure: a PR-triggered, non-blocking check that reads `<skilldir>/audit.md` and applies an `audit-flagged` label + sticky comment. It is currently dormant (no `audit.md` is committed and the `audit-flagged` label does not exist), so it emits nothing today. If audit-tooling starts committing `audit.md`, this becomes a PR signal the triage should handle.
