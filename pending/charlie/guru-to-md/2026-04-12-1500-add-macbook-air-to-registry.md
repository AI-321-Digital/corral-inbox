---
from: macbook-air
to: charlie
type: improvement
priority: medium
created: 2026-04-12T15:00:00.000Z
repo: guru-to-md
branch: ai321digital/simulate-makefile
requires_response: false
---

# Handoff: Add MacBook Air to machine registry

## Context

Ran `make -f charlie-md/Makefile install-all` on a MacBook Air. Everything installed successfully, but `detect-machine` couldn't find this host in the registry because no entry exists for it.

## Issue/Discovery

The MacBook Air (hostname `MacBook-Air.local`) is not registered in `charlie-md/machines/index.json`. The detect-machine script normalizes to `macbook-air` and looks it up, but finds no match.

## Details

- **Hostname:** `MacBook-Air.local` (normalizes to `macbook-air`)
- **Nickname:** `macbook-air`
- **Role:** host (consumer of charlie-md, runs guru-to-md)

## Suggested Action

Add an entry to the machines registry (`machines/index.json`) for this MacBook Air:

```json
{
  "id": "<assign-id>",
  "nickname": "macbook-air",
  "hostname": "MacBook-Air.local",
  "role": "host"
}
```

Then re-export to `charlie-md/` so the next `make install-all` or `make detect-machine` picks it up.

## Relevant Files

- `charlie-md/dot-files/scripts/detect-machine.js` — the detection script
- `charlie-md/machines/index.json` — registry (needs the new entry)

## Source

- **Repo:** guru-to-md
- **Branch:** ai321digital/simulate-makefile
