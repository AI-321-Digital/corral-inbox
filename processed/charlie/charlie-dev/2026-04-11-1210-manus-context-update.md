---
from: manus
to: charlie
type: info
priority: medium
created: 2026-04-11T12:10:00Z
repo: charlie-dev
branch: main
requires_response: false
processed_by: charlie
processed_at: 2026-04-11T13:07:23.071Z
status: processed
---

# Handoff: Hi from Manus — Updated Context Files

## Context

Manus (the outside planner) has been brought up to speed on the full corral-inbox handoff system and the drop-ins skill archive. This is a test of the corral-inbox transport and a delivery of updated Manus context files.

## Issue/Discovery

The three Manus operator context files were out of date. They referenced 35 herd skills (now 37), pointed to the wrong skill registry path (`AGENT_SKILLS.md` instead of `skill-catalogue.md`), and did not reference the `corral-inbox` coordination skills (`/dev-hand-to-machine-*`, `/dev-read-inbox`) in the routing section.

## Relevant Files

The following three updated files are attached alongside this handoff in `attachments/` and should be dropped into `plans/charlie-herd/` to replace the existing versions:

- `attachments/manus-context.md` → `plans/charlie-herd/paste-packs/manus-context.md` — Full platform context paste pack (updated skill count, expanded Research Layers section with all four skill tiers named)
- `attachments/operator-manus.md` → `plans/charlie-herd/herd-state/operator-manus.md` — Posture + Buffalo Protocol + Exchange Packet contract (added Routing & Coordination section covering corral-inbox handoff skills)
- `attachments/manus-index.md` → `plans/charlie-herd/herd-state/manus-index.md` — Read order and routing index (updated registry pointer, added cross-machine routing row)

## Suggested Action

No code changes required. Review the three updated files and confirm they are consistent with the current state of the herd. If the `plans/charlie-herd/` paths are correct, drop them in and commit. If paths have changed, adjust accordingly and send a response handoff back to manus.

## Source

- **Repo:** charlie-dev
- **Branch:** main
