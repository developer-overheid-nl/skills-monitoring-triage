---
name: Logius repo beheer patterns
description: Logius repos undergo frequent beheer changes (sections/ moves, pubdomain, URL normalization, license) — triggers monitoring but no skill updates needed
type: project
originSessionId: 738612db-8826-4a7f-93c7-5b6b0c9d9bb0
---
Since 2026-03-25, Logius standaarden repos regularly undergo beheer changes that trigger monitoring issues but have no impact on skill content:

### Pattern 1: sections/ restructuring
Chapter files moving into `sections/` subdirectories. Seen in:
- Digikoppeling-Beveiligingsstandaarden-en-voorschriften
- Digikoppeling-Koppelvlakstandaard-REST-API
- NL-GOV-profile-for-CloudEvents (2026-03-26)
- fsc-core (2026-03-26)

### Pattern 2: ReSpec config beheer
Updates to `pubdomain`, `latestVersion` links, editor lists, license changes to CC-BY-4.0, companyURL normalization. Examples:
- authorization-decision-log: pubdomain/editor changes (2026-03-30)
- publicatie: ftv pubDomain addition, email-mapping rewrite (2026-04-01)
- API-Design-Rules: URL normalization www.logius.nl → logius.nl in ReSpec config (2026-04-02)
- OAuth-NL-profiel / OIDC-NLGOV: "gebruik NLgov notatie voor naam van profiel" (2026-04-15/17)
- Multiple repos: "Beheer: fix Logius links" wave (2026-04-15)

### Pattern 3: Build/tooling repo updates
Automatisering, publicatie, Openbare-Consultaties repos get frequent build workflow improvements. These are infra changes, no skill impact.

**Why:** These all trigger monitoring issues (GitHub repo commits + published draft page re-renders) but are purely organizational or administrative.

**How to apply:** When triaging and you see commits with messages like "Beheer:", "Verplaats hoofdstukken", "fix logius.nl links", "toevoegen pubdomain", classify as beheer and close. Only investigate further if commit messages suggest actual standard content changes (new rules, version bumps, specification text edits).
