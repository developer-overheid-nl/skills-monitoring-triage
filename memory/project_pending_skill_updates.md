---
name: Pending skill content updates
description: Tracked upstream changes that may warrant skill updates but haven't been applied yet
type: project
originSessionId: cd2d192d-978f-4a72-a852-fcfecda20e42
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

### ls-api: ADR 2.2.0 — DONE 2026-09-01

ADR v2.2.0 vastgesteld op 2026-06-02 (gitdocumentatie/publicatie/api/adr/2.2.0/). Skill wees drie maanden lang nog naar v2.1.0. Bijgewerkt in skills-standaarden#807:
- Signing en Encryption zijn **normatief** geworden onder sectie *Normative modules* (`/core/modules/signing`, `/core/modules/encryption`). De skill vermeldde beide als draft — statusfout, want ze zijn verplicht bij ondertekende/versleutelde payloads.
- Geospatial: `/core/geospatial` → `/core/modules/geospatial`.
- Transport Security-ambiguïteit uit v2.1.0 opgelost: geen leeswijzer-vermelding en geen `API-mod-transport-security`-verwijzing meer; eisen in sectie 2.11.
- Beide conflicten in ls-api/conflicts.md als opgelost gemarkeerd (niet verwijderd, historie blijft navolgbaar).

**Les:** de monitoring signaleert upstream commits/tags, maar niet dat een DEF-publicatie op gitdocumentatie is verschenen. Controleer bij twijfel `gitdocumentatie.logius.nl/publicatie/<domein>/<slug>/` direct; dat is de bron van waarheid voor DEF-versies, niet de GitHub-tag.

### ls-iam: Authorization Decision Log OpenTelemetry refactor — DONE 2026-04-30

ADL werkversie gerefactored in PR #21 / commit a910e4d (2026-04-28) naar OpenTelemetry-vorm.
Oude flat record (timestamp/type/request/response, ISO8601 timestamp) vervangen door OTel-shape:
trace_id, span_id, parent_span_id, event_name (5 vaste waarden uit AuthZEN APIs),
timestamp uint64 ms, status enum (Unset/Ok/Error), attributes.adl.core.* source-references vs
body voor raw payloads. FSC-veld is nu adl.fsc.transaction_id.
Skill-update gemerged in skills-standaarden#574.

### ls-iam: SAML-spec vastgesteld v1.0.0 — DONE 2026-06-04

st-saml-spec vastgesteld op 2026-06-01 als versie 1.0.0 (repo commit "Publiceer versie 1.0.0 #14"; develop weer werkversie via #16). Geen GitHub-tag/Release en nog geen gitdocumentatie-pagina; published ReSpec op github.io toont "Vastgestelde versie 1.0.0". Skill-update in skills-standaarden#685: SKILL.md DEF-kolom → v1.0.0 (link naar github.io zolang gitdocumentatie ontbreekt), conflicts.md sectie 1c toegevoegd. Herbeoordeel DEF-link zodra gitdocumentatie-pagina verschijnt.

### inet-web/inet-api: Internet.nl v1.11.0 TLS changes — DONE 2026-04-22

v1.11.0 released 2026-04-22. Documentatie toegevoegd in PR developer-overheid-nl/skills-internet#168:
- inet-web/reference.md: sectie *Renegotiation en OCSP (NCSC 2025-05)*
- inet-api/reference.md: tabel *TLS-velddetails sinds v1.11.0 (API v2.7.0)*

### geo-api: ogc-checker versie-pin — DONE 2026-08-31

ogc-checker liep van v1.1.0 naar v1.3.0. Versie-pin bijgewerkt op beide plekken in geo-api/SKILL.md en een CLI-sectie toegevoegd voor het `--standard` / `--version` model (PR skills-geo#318). Inhoudelijk relevant: sinds v1.2.0 selecteert `--standard ogc-api-processes` de goedgekeurde 1.0 (18-062r2) in plaats van de 2.0-draft; vanaf v1.3.0 vereist de CLI Node 22+.

Let op: de vorige triage-ronde noteerde dat PR #306 de pin naar v1.1.0 zou hebben bijgewerkt, maar in de praktijk stond er nog v1.1.0 terwijl upstream al op v1.3.0 zat. Controleer bij een volgende ogc-checker-tag of de pin daadwerkelijk in het bestand staat, niet alleen in de PR-beschrijving.

### ls-egov / ls-logboek: gearchiveerde Logius-repos — DONE 2026-08-31

Logius archiveerde in augustus 2026 e-procurement, basisfactuur-rijk, basisorder-rijk (ls-egov) en logboek-dataverwerkingen-demo (ls-logboek). Gemarkeerd in de repo-tabellen en conflicts.md (PR skills-standaarden#801). Bij de demo staat nu ook een waarschuwing bij de start-instructies in reference.md, omdat Logius zelf meldt dat de demo verouderd is.

### ls-egov: Digimelding tijdzone — DONE 2026-08-31

Digimelding-Koppelvlakspecificatie commit e418b72 (#14) legt vast dat alle tijdstempels van en naar Digimelding tijdzone `Europe/Amsterdam` hebben, niet UTC. Opgenomen in ls-egov/SKILL.md (PR skills-standaarden#801).

### skills-standaarden: lychee SNI-exclude — DONE 2026-08-31, met openstaande actie

`gitdocumentatie.logius.nl` uitgesloten in `.lychee.toml` (PR skills-standaarden#802) omdat het certificaat CN `logius.nl` heeft en de hostnaam niet dekt; lychee (rustls) breekt af waar curl doorgaat. **Openstaande actie:** melden bij Logius zodat het certificaat de hostnaam gaat dekken, daarna de exclude verwijderen. Zolang de exclude er staat, worden 33 DEF-publicatielinks niet meer gecontroleerd.

### Forum beslisboom: ontbrekende standaarden — OPEN, gemeld 2026-08-31

Robbert Bos meldt dat het Forum Standaardisatie nu een beslisboom heeft met een standaardenoverzicht, en dat het MOZa-team met een quick scan een aantal standaarden vond die in de skills ontbreken. Nog niet uitgezocht welke. Vraagt om een gap-analyse van de Forum-beslisboom tegen de bestaande skills (ls, inet, geo, nerds).
