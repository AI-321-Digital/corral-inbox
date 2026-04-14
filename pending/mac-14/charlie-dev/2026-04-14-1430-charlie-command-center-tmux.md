---
from: manus
to: mac-14
type: improvement
priority: high
created: 2026-04-14T14:30:00Z
repo: charlie-dev
branch: main
requires_response: true
---

# Handoff: Charlie Command Center (iTerm2 + tmux + OpenCode)

## Context

We are moving away from Warp and establishing a persistent, agent-per-tab command center using iTerm2's Control Mode (`tmux -CC`). This architecture ensures that dev servers, ingest jobs, and OpenCode agent sessions survive IDE restarts and terminal closures. 

The goal is to build a bootstrap script that spins up a named `tmux` session (`charlie-cmd`) with pre-configured windows for each role in the Charlie methodology.

## Exchange Packet

**Agent:** Codex
**Mode:** implementation
**Goal:** Write a bash script (`bin/charlie-cmd-start.sh`) that bootstraps a persistent `tmux` session with dedicated windows for dev-server, ingest, codex, opus, and corral-inbox.
**Inputs:** 
- Target layout: 5 windows (`dev-server`, `ingest`, `codex`, `opus`, `corral-inbox`).
- The script must check if the session `charlie-cmd` exists. If yes, attach to it. If no, create it and build the windows.
- The script must be designed to be run via iTerm2's "Send text at start" profile setting or manually via `tmux -CC attach -t charlie-cmd || bin/charlie-cmd-start.sh`.
**Output:** 
1. `bin/charlie-cmd-start.sh` (executable bash script).
2. A brief markdown guide (`docs/charlie-command-center.md`) explaining how to configure iTerm2 to launch this on startup.
**Path policy:** create
**Proof expectation:** 
- The script executes without syntax errors.
- Running the script creates a `tmux` session named `charlie-cmd` with exactly the 5 specified windows.
- Provide the output of `tmux list-windows -t charlie-cmd` as proof.
**Stop condition:** The script is written, made executable, and the proof of window creation is provided in the Return Contract.

## Suggested Action

1. Invoke `/charlie-task` to begin implementation.
2. Create `bin/charlie-cmd-start.sh` using standard `tmux new-session`, `tmux new-window`, and `tmux send-keys` commands.
3. Ensure the script handles the "session already exists" case gracefully.
4. Create the documentation file.
5. Run the script locally to verify it builds the session correctly.
6. Return the Exchange Packet Return Contract to Manus via `corral-inbox`.
