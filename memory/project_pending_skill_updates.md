---
name: Pending skill content updates
description: Tracked upstream changes that may warrant skill updates but haven't been applied yet
type: project
---
### geo-meta: MDTO and SHACL validation (identified 2026-03-26)

Upstream Geonovum/Metadata-handreiking added MDTO (Metagegevens Duurzaam Toegankelijke Overheidsinformatie) content and SHACL-based DCAT validation guidance. The geo-meta skill doesn't mention MDTO yet.

Recommended updates:
- Add MDTO to the standaarden overzicht as a related standard
- Add section on relationship between ISO 19115, DCAT-AP-NL, and MDTO
- Expand validation section with SHACL validation for DCAT metadata

### geo / geo-model: missing Geonovum thematic standards (identified 2026-04-03)

Comparison of geonovum.nl/geo-standaarden page with geo skills revealed gaps. Core standards are well covered, but several Geonovum workstreams are absent:

- **Medium priority**: Omgevingswet (TPOD) and RO Standaarden - major Geonovum activities, users may ask about these
- **Low priority**: Additional sectormodellen not in geo-model: IMKL, IMEV, IMG, BRO, Informatiemodel Bestuurlijke Gebieden
- **Low priority**: CRS (coordinaatreferentiesystemen), Geo op het web

### ls-api: ADR 2.2 consultatie (identified 2026-03-26)

Openbare consultatie gestart voor API Design Rules v2.2. Current skill correctly lists v2.1.0 as DEF version. ADR linter is bijgewerkt naar 22 regels (PR #476, 2026-04-11). Consider adding a note about the ongoing consultation when v2.2 reaches VV status.

### ls-iam: Authorization Decision Log OpenTelemetry refactor (DONE 2026-04-30)

ADL werkversie gerefactored in PR #21 / commit a910e4d (2026-04-28) naar OpenTelemetry-vorm.
Oude flat record (timestamp/type/request/response, ISO8601 timestamp) vervangen door OTel-shape:
trace_id, span_id, parent_span_id, event_name (5 vaste waarden uit AuthZEN APIs),
timestamp uint64 ms, status enum (Unset/Ok/Error), attributes.adl.core.* source-references vs
body voor raw payloads. FSC-veld is nu adl.fsc.transaction_id.
Skill-update gemerged in skills-standaarden#574.

### ls-iam: SAML-spec vastgesteld v1.0.0 (DONE 2026-06-04)

st-saml-spec vastgesteld op 2026-06-01 als versie 1.0.0 (repo commit "Publiceer versie 1.0.0 #14"; develop weer werkversie via #16). Geen GitHub-tag/Release en nog geen gitdocumentatie-pagina; published ReSpec op github.io toont "Vastgestelde versie 1.0.0". Skill-update in skills-standaarden#685: SKILL.md DEF-kolom → v1.0.0 (link naar github.io zolang gitdocumentatie ontbreekt), conflicts.md sectie 1c toegevoegd. Herbeoordeel DEF-link zodra gitdocumentatie-pagina verschijnt.

### inet-web/inet-api: Internet.nl v1.11.0 TLS changes (DONE 2026-04-22)

v1.11.0 released 2026-04-22. Documentatie toegevoegd in PR developer-overheid-nl/skills-internet#168:
- inet-web/reference.md: sectie *Renegotiation en OCSP (NCSC 2025-05)*
- inet-api/reference.md: tabel *TLS-velddetails sinds v1.11.0 (API v2.7.0)*
