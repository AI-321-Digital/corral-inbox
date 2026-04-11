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
+-- pending/
|   `-- {target-machine}/
|       `-- {source-repo}/
|           `-- {YYYY-MM-DD-HHMM}-{short-name}.md
+-- processed/
|   `-- {target-machine}/
|       `-- {source-repo}/
|           `-- {YYYY-MM-DD-HHMM}-{short-name}.md
+-- broadcast/
|   `-- {YYYY-MM-DD-HHMM}-{short-name}.md
+-- machines.json
`-- README.md
```

`pending/` contains handoffs waiting for the target machine. `processed/`
contains consumed handoffs and preserves the `{target-machine}/{source-repo}/`
path from `pending/`. `broadcast/` is reserved for all-machine notices and must
not replace targeted handoffs.

## File Naming Convention

Required filename pattern:

```text
{YYYY-MM-DD-HHMM}-{short-name}.md
```

Examples:

```text
2026-04-10-1430-form-validation.md
2026-04-10-1600-export-cleanup.md
2026-04-10-0900-deploy-freeze.md
```

Rules:

- Timestamp is UTC and filename-safe.
- Short names are lowercase kebab-case and should stay under about 40 chars.
- Source and target machines are not encoded in the filename; they live in
  frontmatter and in the directory path.
- If two handoffs collide in the same minute, append a numeric suffix before
  `.md`, for example `2026-04-10-1430-form-validation-2.md`.
- The basename is the stable handoff id.

## Frontmatter Contract

Every handoff file must begin with YAML frontmatter:

```yaml
---
from: mac-14
to: charlie
type: bug | improvement | question | info
priority: high | medium | low
created: 2026-04-10T14:30:00Z
repo: harrisburg
branch: main
requires_response: true
---
```

Required fields:

| Field | Required | Meaning |
|-------|----------|---------|
| `from` | Yes | Machine nickname that created the handoff |
| `to` | Yes | Target machine nickname, or `all` for broadcast |
| `type` | Yes | `bug`, `improvement`, `question`, or `info` |
| `priority` | Yes | `high`, `medium`, or `low` |
| `created` | Yes | ISO 8601 UTC timestamp |
| `repo` | Yes | Source repository context |
| `branch` | Yes | Source branch context |
| `requires_response` | Yes | Boolean response expectation |

Processed files may add:

| Field | Meaning |
|-------|---------|
| `processed_at` | ISO 8601 UTC timestamp when consumed |
| `processed_by` | Machine nickname that consumed the handoff |

## Body Contract

After frontmatter, use this body shape:

```markdown
# Handoff: Short Title

## Context

## Issue/Discovery

## Relevant Files

## Suggested Action

## Source
```

`Relevant Files` can be omitted for broadcast or informational handoffs that do
not name files.

## Lifecycle

Writer flow:

1. Pull the latest `corral-inbox` submodule state.
2. Resolve the current repo name from git remote, not from the working directory.
3. Create a new markdown file in `pending/{target-machine}/{source-repo}/`.
4. Validate filename, directory path, and frontmatter.
5. Commit and push the inbox repo.
6. Update the parent repo submodule pointer only when the parent workflow
   requires a pinned state.

Reader flow:

1. Pull the latest `corral-inbox` submodule state.
2. Scan `pending/{my-nick}/**/*.md`.
3. Read and execute according to the handoff contract.
4. Move consumed files from `pending/{target}/{repo}/` to
   `processed/{target}/{repo}/`, preserving the filename.
5. Add `processed_at` and `processed_by` frontmatter.
6. Commit and push the inbox repo.

Lifecycle rules:

- Directory location is the lifecycle state: pending vs processed.
- Handoffs in `pending/{target-machine}/` must have matching `to` frontmatter.
- Broadcast files live in `broadcast/` and use `to: all`.
- Processed files preserve the original filename and relative path for audit
  continuity.
- Reprocessing should be idempotent. If a file already exists in `processed/`,
  the reader should not execute it again without operator approval.
- Handoffs are not transported through MCP.
- `--legacy` may read the old SMB layout during transition, but default behavior
  is git-native `corral-inbox`.

## Machine Registry

`machines.json` reserves a path for future machine discovery:

```json
{
  "_comment": "Machine registry stub. See plans/corral-inbox/plan.md for context. Full implementation deferred.",
  "machines": []
}
```

The current source of truth for machine identity remains the Charlie database
and exported machine config.
