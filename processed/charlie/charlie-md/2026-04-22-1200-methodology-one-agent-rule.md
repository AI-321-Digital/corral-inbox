---
from: manus
to: charlie
type: improvement
priority: high
created: 2026-04-22T12:00:00Z
repo: charlie-md
branch: main
requires_response: false
processed_by: charlie
processed_at: 2026-04-24T11:02:18.949Z
status: processed
---

# Handoff: Methodology Update — One Write-Access Agent Per Worktree

## Context

During the planning of the Ledger/Corral public surface remake, we observed the collision risks of having two IDE agents (e.g., Codex and Opus) operating in the same worktree simultaneously. Even with good observer visibility, concurrent write access creates lock contention, dirty working trees, and merge chaos.

## Issue/Discovery

The Charlie methodology needs a strict, explicit rule governing worktree access and task routing to prevent these collisions. The "observer" value is real, but it is a read-only benefit and does not justify write access for multiple agents in the same checkout.

The cleaner model is:
1. **One write-access agent per worktree at a time.** Observer access is read-only and does not count.
2. **Inbox-first tasking.** The `corral-inbox` is the only legitimate tasking channel for non-birthing work. No direct tasking of IDE agents squatting in a worktree.
3. **Feature branch isolation.** Agents poll the inbox, check out a feature branch, execute, commit proof, and return a packet to the inbox. The operator reviews the PR.
4. **Dedicated machines for "birthing".** Projects that require heavy, continuous context (like birthing Ledger) warrant a dedicated IDE agent on a dedicated machine (e.g., mac-14 / mac-16) with full worktree visibility, rather than multiple agents fighting over the same local checkout.

## Relevant Files

- `charlie-md/` (The methodology documentation repository)

## Suggested Action

Please update the Charlie methodology documentation (likely within the core principles or agent interaction guidelines in `charlie-md`) to explicitly codify the following rules:

1. **The One-Agent Rule:** Enforce a strict limit of one write-access agent per worktree at any given time.
2. **Inbox-Driven Routing:** Mandate that all non-birthing tasks must be routed through `corral-inbox`, executed on isolated feature branches, and concluded with a return packet and PR.
3. **Birthing Exception:** Clarify that long-running "birthing" projects should use dedicated machines/agents rather than shared local worktrees.

This is a documentation update to formalize the operational posture we are already adopting.

## Source

- **Repo:** charlie-md
- **Branch:** main
