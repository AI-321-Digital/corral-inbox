---
from: guru-to-md
to: charlie
type: improvement
priority: medium
created: 2026-04-12T17:33:21-0400
repo: guru-to-md
branch: main
requires_response: true
processed_by: charlie
processed_at: 2026-04-12T21:58:49.926Z
status: processed
---

# Handoff: Add Codex and Gemini global agent context to installer

## Context

Frank updated the Codex global agent file and Codex skills, then verified from a live Codex chat in `guru-to-md` that the launch context is visible going into new chats.

The verified Codex setup is:

- Global agent file exists at `/Users/franklennon/.codex/AGENTS.md`.
- Charlie/dev skills exist under `/Users/franklennon/.codex/skills/`.
- Example verified skills:
  - `/Users/franklennon/.codex/skills/charlie/SKILL.md`
  - `/Users/franklennon/.codex/skills/dev-proof/SKILL.md`
  - `/Users/franklennon/.codex/skills/dev-hand-to-machine-charlie/SKILL.md`
- The global `AGENTS.md` contains the Charlie executor role, Buffalo Protocol, skill list, `charlie-md/` context pointers, source-of-truth guardrail, `corral-inbox` handoff rules, and proof-oriented return contract.
- In this repo, generated repo context is visible at `/Users/franklennon/dev/repos/guru-to-md/charlie-md/AGENTS.md`.

Frank also noted that this same style of global context setup was done for Gemini and wants that captured as part of the install path too.

## Issue/Discovery

The current machine setup now has good launch context for Codex because the global agent and installed skills are present in the expected locations. This should not remain a one-off local setup. Charlie repo should make it part of the installer/bootstrap process so future installs consistently provision:

- Codex global `AGENTS.md`
- Codex Charlie/dev skills under `~/.codex/skills/`
- Gemini equivalent global agent/context files
- Any Gemini skill/context equivalent that was set up manually
- A post-install verification command or checklist proving the files are visible from a launched chat

One small environment note: the Codex tool environment had `rg`, but Frank's interactive terminal did not (`zsh: command not found: rg`). Installer verification should avoid assuming `rg` is installed, or install/check it explicitly if the workflow depends on it.

## Relevant Files

- `/Users/franklennon/.codex/AGENTS.md` - Verified global Codex instructions.
- `/Users/franklennon/.codex/skills/` - Verified installed Charlie/dev skills.
- `/Users/franklennon/.codex/skills/charlie/SKILL.md` - Verified Charlie router skill.
- `/Users/franklennon/.codex/skills/dev-proof/SKILL.md` - Verified proof skill.
- `/Users/franklennon/.codex/skills/dev-hand-to-machine-charlie/SKILL.md` - Skill used to create this handoff.
- `/Users/franklennon/dev/repos/guru-to-md/charlie-md/AGENTS.md` - Repo generated context layer visible in this workspace.

## Suggested Action

In the Charlie repo installer/bootstrap flow:

1. Add Codex provisioning for global `~/.codex/AGENTS.md` using the updated Charlie executor/Buffalo Protocol content.
2. Add Codex skill provisioning for the Charlie/dev skills currently installed under `~/.codex/skills/`.
3. Add the Gemini equivalent provisioning that Frank says was also set up manually.
4. Add verification output that confirms the global agent file and key skills exist after install.
5. Include a note that generated `charlie-md/` files should not be edited as the durable source; durable methodology changes should flow through the Charlie source/export pipeline.
6. Avoid using `rg` as a required verification dependency unless the installer installs it or falls back to `find`.

Please respond with where this belongs in the Charlie installer and what exact install task/file should own it.

## Source

- **Repo:** guru-to-md
- **Branch:** main
