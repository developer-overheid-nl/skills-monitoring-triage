---
name: Release-please PR unsticking workflow
description: How to unblock release-please PRs that have no CI checks and missing platform files
type: project
---
Release-please PRs in skills-geo, skills-internet en skills-standaarden komen vaak vast te zitten omdat:

1. **Geen CI checks**: release-please pusht via GITHUB_TOKEN dat geen workflows triggert
2. **Platform files niet geregenereerd**: `pyproject.toml` wordt door release-please gebumpt, maar `.claude-plugin/plugin.json` en `.cursor-plugin/plugin.json` niet, dus `validate` faalt dan

**Why:** Dit is een terugkerend patroon na elke release. Ook de `chore: bump deps` workflow kent dezelfde issue (CI niet getriggerd voor dependabot-merge commits binnen branches).

**How to apply:** Voor elke release-please PR (`chore(main): release X.Y.Z`):
1. `gh pr checkout <nr>`
2. `git pull --rebase` (release-please force-pusht regelmatig)
3. `uv run python scripts/generate_plugin.py` (regenereert `.claude-plugin/` en `.cursor-plugin/`)
4. `git add .claude-plugin/plugin.json .cursor-plugin/plugin.json`
5. `git commit -m "chore: regenerate plugin.json voor X.Y.Z"`
6. Als CI nog niet runt: `git commit --allow-empty -m "chore: trigger CI"` + push
7. Wacht op groen, dan `gh pr merge <nr> --squash --admin`
8. Daarna: handmatig `gh workflow run check-versions.yml --repo developer-overheid-nl/skills-marketplace` om de marketplace-bump PR te triggeren

Bij admin-merge van NCSC URL fix (#153, 14 april 2026): werkte zonder regenerate omdat het geen versie-bump PR was.
