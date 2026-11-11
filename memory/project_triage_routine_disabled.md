---
name: triage-cloud-routine-disabled
description: Daily monitoring-triage runs locally via launchd since 2026-11-11; the cloud routine is deleted
metadata:
  type: project
---

Since **2026-11-11** the daily triage runs on Anne's laptop via launchd: `com.anneschuth.monitoring-triage` starts `scripts/monitoring-triage-daily.sh` in the private workspace repo (`anneschuth/overheid-skills`) every hour and at login. The script runs `claude -p "/monitoring-triage"` at most once per day, not before 10:00 local, skips when GitHub is unreachable, logs to `~/.local/share/monitoring-triage/triage.log` and shows a macOS notification with the last summary line. It uses Anne's own `gh` login and Claude subscription, so no extra secret is needed; it only runs when the laptop is awake.

The cloud routine "Daily monitoring-triage (developer-overheid-nl)" (`trig_01BWxRUTCkLMKm1TGSeEtJUo`) was disabled on 2026-07-19 and **deleted on 2026-11-11**. It needed a PAT in `GH_TOKEN` in its cloud environment for writes (the GitHub connector is read-only). A new cloud routine would need the same.

**Why:** Anne wanted a daily run without a token stored in a cloud environment, and session crons (CronCreate) expire after 7 days.

**How to apply:** If a triage run finds an unexpectedly large backlog, check `triage.log` and `launchctl print gui/$(id -u)/com.anneschuth.monitoring-triage` first (laptop asleep for days, auth expired). To force a rerun today, delete `~/.local/share/monitoring-triage/last-success`. A run in a cloud session ignores this file. See [[Recurring monitoring false positive sources]].
