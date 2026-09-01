---
name: project_forum_beslisboom_dekking
description: Forum beslisboom JSON:API, dekkingsanalyse 29/55, en welke standaarden bewust buiten scope vallen
metadata:
  type: project
---

Het Forum Standaardisatie heeft een [Beslisboom Open Standaarden](https://www.forumstandaardisatie.nl/beslisboom/beslisboom-open-standaarden) die op situatie routeert (sector, kanaal, gegevenssoort) in plaats van op domein. Aangekaart door Robbert Bos op 2026-08-31 na een quick scan van het MOZa-team.

**JSON:API (de HTML-pagina is een lege React-shell, curl geeft niets):**

```
https://www.forumstandaardisatie.nl/jsonapi/node/decision_tree?include=decisionTreeSteps.questions.answers.standards
```

Levert 2 bomen (Open Standaarden + documentstandaarden), 9 steps, 9 questions, 48 answers, 55 unieke standaarden. `node/standaarden` bestaat niet als losse collection (404), dus dit is de enige ingang. Hash is stabiel over 3 fetches maar dekt de hele body van 400KB, dus een omschrijvingswijziging triggert ook.

**Dekking per 2026-09-01: 27 van de 55.** Vastgelegd in `ls/SKILL.md` (PR skills-standaarden#804) inclusief de scope-tabel, zodat een volgende dekkingsanalyse niet dezelfde 26 opnieuw als gat rapporteert.

- **Bewust buiten scope (13):** Aquo, GWSW, NLCS, SIKB0101, SIKB0102, Stosag, VISI, SETU, XBRL, EML_NL, Erfgoedstandaard, E-Portfolio NL, WDO Datamodel. Sectorcommunities met eigen tooling.
- **Backlog (5):** ACME, STIX/TAXII, AdES Baseline Profiles, NEN-ISO/IEC 27001, NEN-ISO/IEC 27002.
- **Bewust niet (1):** StUF. Eelco Hotting 2026-08-31: "niet correct te doen" — leunt op sectormodellen en per-koppelvlak implementatiekeuzes; half-correct is schadelijker dan niets. Niet opnieuw als backlog opvoeren.
- **Geen eigenaar (11):** BWB, ECLI, JCDR, ODF, PDF/A, PDF/UA, EPUB, iCalendar, CMIS, ISO 3166-1, WPA2 Enterprise.

**Structurele oorzaak:** de plugins zijn ingedeeld naar beheerder (Logius, Geonovum, internet.nl, NORA, developer.overheid.nl), de beslisboom naar gebruikerssituatie. Standaarden zonder beheerder onder die vijf vallen daardoor tussen wal en schip. `skills-developer-overheid-nl` is de enige repo die op gebruikersbehoefte is ingedeeld en dus de logische thuisbasis voor wezen.

**Valkuil bij dekkingsanalyse (werkt twee kanten op):** matchen op standaardnaam onderschat de dekking HTTPS/HSTS en ACME stonden inhoudelijk in inet-web en inet-toolbox zonder de Forum-naam; gefixt in skills-internet#253. Het overschat de dekking ook: 27001/27002 telde ik als gedekt op een grep-hit, maar die staan er alleen als grondslag van de BIO en worden nergens als standaard behandeld. MOZa had gelijk, gecorrigeerd in #806. Controleer altijd wat de skill daadwerkelijk over een standaard zegt, niet of de naam voorkomt.

**Openstaand:** StUF is het grootste reële gat (verplicht, gemeentelijke interop, geen enkele skill dekt het). Aparte afweging want VNG/GEMMA is een vierde beheerder: nieuwe skill in skills-developer-overheid-nl of een eigen plugin. Zie ook [[project_pending_skill_updates]].

**Repo-namen:** de marketplace publiceert `developer-overheid-nl-agent-skills` (plugin `developer-overheid`, don-*). De lokale directory `skills-developer-overheid-nl` is een niet-gepubliceerde variant met dezelfde skills; beide meenemen bij een dekkingsanalyse, maar de gepubliceerde is leidend.

**MOZa-lijst (2026-08-31) komt overeen met de mijne** op 27001/27002 na (zij hadden gelijk) en PDF/A (zij noemden het, ik niet). Standaarden die zij niet noemden maar wel in de beslisboom zitten: SIKB0102, Stosag, Erfgoedstandaard, EPUB, iCalendar, CMIS, ISO 3166-1 — allemaal dezelfde sectorgroep.
