---
name: triage-cloud-routine-disabled
description: The daily monitoring-triage cloud routine is disabled since 2026-07-19; triage runs only manually now
metadata: 
  node_type: memory
  type: project
  originSessionId: ee7d2a7b-a7c2-482c-a94e-b97eb2ffdf8b
  modified: 2026-07-19T06:25:59.439Z
---

The cloud routine "Daily monitoring-triage (developer-overheid-nl)" (`trig_01BWxRUTCkLMKm1TGSeEtJUo`, cron `0 10 * * *` UTC = 12:00 NL, environment skills-monitoring-triage) was **disabled on 2026-07-19** at Anne's request. It is disabled, not deleted.

**Why:** Anne asked to turn off the daily 12:00 check.

**How to apply:** Monitoring issues now accumulate until Anne runs `/monitoring-triage` manually, so expect larger backlogs per run (see [[Recurring monitoring false positive sources]]). Re-enable via `RemoteTrigger` update with `{"enabled": true}` on that trigger id; deletion only via https://claude.ai/code/routines.
