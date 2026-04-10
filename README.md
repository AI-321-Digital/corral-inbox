# Corral Inbox

Git-native handoff transport for Charlie OS machines.

## Repository Location

Primary remote:

```text
https://github.com/AI-321-Digital/corral-inbox
```

Participating repositories add this repository as a submodule at repo root:

```text
corral-inbox/
```

## Directory Schema

```text
corral-inbox/
+-- pending/       # New machine-targeted handoffs awaiting consumption
+-- processed/     # Consumed handoffs, preserving the original filename
+-- broadcast/     # Announcements intended for all machines
`-- README.md      # Schema and usage guide
```

`pending/` and `processed/` are the required lifecycle directories. `broadcast/` is reserved for all-machine notices and must not replace targeted handoffs.

## File Naming Convention

Required filename pattern:

```text
{timestamp}-{source-machine}-{target-machine}.md
```

Timestamp format:

```text
YYYYMMDDTHHMMSSZ
```

Examples:

```text
20260410T153000Z-harrisburg-stricklands.md
20260410T153015Z-harrisburg-all.md
```

Rules:

- `timestamp` is UTC and filename-safe.
- `source-machine` and `target-machine` are lowercase machine slugs.
- Use `all` as the target only for broadcast-style handoffs.
- If two handoffs collide in the same second, append a numeric suffix before `.md`, for example `20260410T153000Z-harrisburg-stricklands-2.md`.
- The basename is the stable handoff id unless frontmatter includes an explicit `id`.

## Frontmatter Contract

Every handoff file must begin with YAML frontmatter:

```yaml
---
schema_version: 1
id: 20260410T153000Z-harrisburg-stricklands
created_at: 2026-04-10T15:30:00Z
source_machine: harrisburg
target_machine: stricklands
source_agent: codex
target_agent: codex
mode: handoff
priority: normal
status: pending
related_repo: charlie-dev
related_branch: main
related_paths:
  - plans/corral-superstar/architecture.md
requires_response: false
response_to: null
legacy_smb_path: null
processed_at: null
processed_by: null
---
```

Required fields:

| Field | Required | Meaning |
|-------|----------|---------|
| `schema_version` | Yes | Integer schema version, starting at `1` |
| `id` | Yes | Stable id, normally filename basename |
| `created_at` | Yes | ISO 8601 UTC timestamp |
| `source_machine` | Yes | Machine slug that created the handoff |
| `target_machine` | Yes | Machine slug or `all` |
| `mode` | Yes | `planning`, `review`, `implementation`, `proof`, or `handoff` |
| `priority` | Yes | `low`, `normal`, `high`, or `urgent` |
| `status` | Yes | `pending` in `pending/`, `processed` in `processed/` |
| `related_repo` | Yes | Repo context, for example `charlie-dev` |
| `related_branch` | Yes | Branch context, normally `main` |
| `related_paths` | Yes | List of relevant repo paths, empty list allowed |
| `requires_response` | Yes | Boolean response expectation |

Optional fields:

| Field | Meaning |
|-------|---------|
| `source_agent` | Agent or tool that wrote the handoff |
| `target_agent` | Expected consumer, if known |
| `response_to` | Upstream handoff id being answered |
| `legacy_smb_path` | Original SMB path when using `--legacy` fallback |
| `processed_at` | ISO 8601 UTC processing timestamp |
| `processed_by` | Machine or agent that moved the file to `processed/` |

## Body Contract

After frontmatter, use this body shape:

```markdown
# Handoff: Short Title

## Goal
One sentence describing the requested outcome.

## Inputs
- Paths, prior findings, commands, or context the receiver needs.

## Requested Output
Exact deliverable expected from the receiver.

## Path Policy
Known paths, paths to create, or paths the receiver must resolve.

## Proof Expectation
Evidence file, command output, or verification required.

## Stop Condition
Concrete condition where the receiver should stop and report back.
```

## Lifecycle

Writer flow:

1. Pull the latest `corral-inbox` submodule state.
2. Create a new markdown file in `corral-inbox/pending/`.
3. Validate filename and frontmatter.
4. Commit and push the inbox repo.
5. Update the parent repo submodule pointer only when the parent workflow requires a pinned state.

Reader flow:

1. Pull the latest `corral-inbox` submodule state.
2. Select files in `pending/` where `target_machine` matches the local machine or `all`.
3. Read and execute according to the handoff contract.
4. Move consumed files from `pending/` to `processed/`, preserving the filename.
5. Update frontmatter: `status: processed`, `processed_at`, and `processed_by`.
6. Commit and push the inbox repo.

Lifecycle rules:

- A file in `pending/` must have `status: pending`.
- A file in `processed/` must have `status: processed`.
- Processed files preserve the original filename for audit continuity.
- Reprocessing should be idempotent. If a file already exists in `processed/`, the reader should not execute it again without operator approval.
- Handoffs are not transported through MCP.
- During transition, `--legacy` may write or read through SMB, but default behavior is git primary.
