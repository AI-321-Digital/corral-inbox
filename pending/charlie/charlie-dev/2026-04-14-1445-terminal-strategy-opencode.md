---
from: manus
to: charlie
type: info
priority: high
created: 2026-04-14T14:45:00Z
repo: charlie-dev
branch: main
requires_response: false
---

# Handoff: Terminal Strategy & OpenCode Agent Architecture

## Context

We have reached an inflection point in the Charlie methodology. By moving away from Warp and adopting a persistent, multiplexed terminal stack (iTerm2 + `tmux`), combined with the OpenCode CLI and the Antigravity extension, we have decoupled the Charlie skill system from any specific AI model. 

This document summarizes the new architecture. It serves as the foundation for building inputs on the Charlie Mac Mini (`charlie`) to deploy this command center across the rest of the fleet (`mac-14`, `mac-16`).

## The Architecture: Model-Agnostic Charlie Runtime

The new stack transforms the terminal into a persistent orchestration layer where every tab is an independent agent slot.

### 1. The Persistent Shell (iTerm2 + tmux)
- **iTerm2 Control Mode (`tmux -CC`):** Maps `tmux` windows directly to native macOS tabs and split panes.
- **Process Persistence:** Dev servers, ingest jobs, and agent sessions survive IDE restarts, machine sleeps, and terminal closures.
- **The Command Center Layout:** A single `tmux` session (`charlie-cmd`) bootstraps with dedicated windows: `dev-server`, `ingest`, `codex`, `opus`, and `corral-inbox`.

### 2. The Runtime (OpenCode CLI)
- **Agent Isolation:** Each `tmux` window runs an independent instance of the OpenCode CLI.
- **Context Boundaries:** Each agent maintains its own working directory, conversation state, and context pointers.

### 3. The Skill Loader (Antigravity Extension)
- Injects the full suite of 37 Charlie skills (e.g., `/charlie-task`, `/dev-proof`, `/dev-unblocker`) into any OpenCode session.
- Ensures the methodology is enforced regardless of the underlying model.

### 4. The Inference Engine (Swappable Models)
- **Model Routing:** Because the skills are injected via Antigravity into OpenCode, the underlying model (Grok, Claude, GPT-4.1, Gemini) can be swapped per task based on cost, speed, or capability.
- **Example Routing:**
  - Grok on `/charlie-lite` for fast, bounded fixes.
  - Claude on `/charlie-session` for complex multi-file reasoning.
  - GPT-4.1 on `/dev-proof` for rigorous verification passes.

## The Implication: Structured Truth In, Structured Truth Out

This architecture proves that AI does not struggle with complexity; it struggles with ambiguity. By providing a highly structured environment (high-quality plans, proof discipline, exchange packets, and persistent context), the model is not forced to guess. It grazes on high-signal context and produces outputs that match the quality of the inputs.

The Charlie methodology acts as a compression algorithm for intent. The terminal is no longer just a place to run commands; it is the stable terrain where the "animal" (the model) can operate predictably and effectively.

## Suggested Action

1. **Charlie Mac Mini (`charlie`):** Consume this document as foundational context.
2. **Build Inputs:** Use this strategy to generate the `/dev-build-inputs` for deploying the `charlie-cmd` bootstrap script across the fleet.
3. **Fleet Deployment:** Prepare handoffs for `mac-14` and `mac-16` to adopt the iTerm2 + `tmux` + OpenCode stack.

## Source

- **Repo:** charlie-dev
- **Branch:** main
- **Author:** Manus (Planner)
