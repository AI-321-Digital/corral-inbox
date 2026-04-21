# Ledger — Log Vertical Concept + Resume v2-buffalo-100

**From:** Manus  
**To:** Opus (mac-14)  
**Date:** 2026-04-21  
**Priority:** Normal  

---

## Context

A strategy session with Frank produced two artifacts that need to land in the repo before execution resumes on the public remake. The vertical expansion concept is a planning artifact only — no session, no execution. After it is saved, flip back to the v2-buffalo-100 plan and hand to Codex.

---

## Action 1 — Save the Vertical Expansion Concept

**Target path (create if missing):**
```
plans/vertical-expansion/concept.md
```

**Content to save:**

```markdown
# Ledger AI: Vertical Expansion Concept

## The Core Thesis: Trust Restoration

Ledger AI is not selling software; it is practicing trust restoration. The primary buyer is a professional who has been "burned by AI" — they relied on a probabilistic tool, it hallucinated a compliance verdict, and they suffered a professional injury (embarrassment, regulatory scrutiny, or client loss).

The $49.99 Proof Session is the diagnostic. The annual contract ($12k/$24k/$48k) is the treatment plan. Ledger AI is the doctor.

## The Subdomain Model

Compliance is not horizontal. A CCO at a hedge fund and a General Counsel at a hospital have different regulatory vocabularies and rely on different source sets. A single `ledgerai.software` homepage cannot speak to both with equal credibility.

The solution is a vertical path model (paths first, subdomains later):

- `ledgerai.software/finance` (SEC EDGAR, FINRA, eCFR Title 17)
- `ledgerai.software/health` (FDA, CMS, HIPAA, eCFR Title 42)
- `ledgerai.software/legal` (eCFR, Case Law, State Statutes)

**Structural Advantage:** This model maps onto the existing Corral API architecture. Corral already routes queries by data source. The paths simply act as specialized front doors to the existing enforcement backend.

## The Four-Color System

- The Constant: Bloomberg-terminal density, high-contrast dark mode (zinc/slate), cryptographic proof chain — identical across all verticals.
- The Variable: Each vertical is assigned a specific accent color via a single `--accent` CSS variable in `globals.css`.
  - Finance: Emerald Green
  - Health: Sapphire Blue
  - Legal: Amethyst Purple

## Sequencing Rules

1. **Phase 1:** Launch `ledgerai.software` with the full four-tier pricing model and the fine-print comparison page.
2. **Phase 2:** Acquire the first paying Proof Session customers. Capture the first anonymized proof chain as a case study. Validate "burned by AI" messaging converts.
3. **Phase 3:** Launch `/finance` path with real-world use cases drawn from Phase 2 customers.
4. **Phase 4:** Launch `/health` and `/legal` only when Corral has robust, tested integrations with their specific source sets.

## Resolved Decisions (Opus Review, April 2026)

- **$49.99 Proof Session:** Hold out of v2-buffalo-100. Run as a Phase 2 measured experiment (5 manually-processed sessions, measure conversion to Pilot). Build Stripe flow only if it converts. Do not bake into pricing page as a permanent SKU.
- **Verticals:** Finance first, as `/finance` path (not subdomain). Health and Legal are hypotheses tied to Corral integration readiness, not roadmap commitments.
- **Subdomains vs paths:** Paths first. Subdomains come after a vertical earns its own marketing team and its own server. Current single-droplet topology makes subdomains an ops burden.
- **Three low-cost optionality moves for v2-buffalo-100:**
  1. Define `--accent` CSS variable in `apps/site/app/globals.css` distinct from semantic colors. Today it points at zinc. Vertical recoloring becomes a one-line swap.
  2. Solutions dropdown becomes the seedbed for Finance vertical content.
  3. Claims Library stays generic (principle → proof), not vertical-specific.
- **Trust restoration needs one healed patient.** Phase 2 must include capturing the first anonymized proof chain from a real customer.

## The One-Sentence Product Thesis

> Ledger AI is a trust restoration platform where the Proof Session is the diagnostic, the annual contract is the treatment, and each vertical path is a specialized clinic pre-scoped to the regulatory source set that injured the patient.
```

---

## Action 2 — Resume v2-buffalo-100

After saving the concept file, the plan pack at `plans/public-remake/v2-buffalo-100/` is complete and ready for Codex. All five open decisions are resolved:

| # | Decision | Answer |
| :--- | :--- | :--- |
| 1 | Route for fine-print page | `/proof/fine-print` |
| 2 | Retire "trial" | Yes — replace with "Pilot" or "Proof Session" |
| 3 | Enterprise tier + "no demo" claim | Pilot/Supervision are self-serve; Enterprise includes a scoping session |
| 4 | Competitor quotes fair game? | Yes, verbatim from public ToS/docs. Structural frame only. D6 copy reviewed before publish. |
| 5 | Did `ui-c` land? | Confirmed — `globals.css` is pure zinc. No token work needed. |

**One D6 addition to communicate to Codex:** Every competitor quote in the fine-print table must show the source URL as a visible footnote, not hidden behind anchor text.

**Codex handoff prompt:**

```
/charlie-session — invoke the public-remake session. Read
plans/public-remake/v2-buffalo-100/mind-map.md and plan.md before any
edit. Scope: apps/site only. Execute Phase 1 → Phase 4. Close with
/dev-proof (hero before/after, new header, new footer, /proof/fine-print,
one Proof page, one Solutions page, one Platform page).
```

---

**Stop condition:** Concept file saved at `plans/vertical-expansion/concept.md`, committed. Codex handoff prompt pasted. No further action from Opus on this message.
