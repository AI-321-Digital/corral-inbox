# Manus Context -- Ledger AI Platform

## Company and Product

AI-321-Digital builds vertical compliance and AI-verification SaaS. Four apps run on a single DigitalOcean droplet (`Charlie-Superstar`, `174.138.113.240`). One developer owns all repos, all deploys, and all agent roles. There is no team, no CI/CD pipeline, and no multi-approval gates. Speed over ceremony.

**Ledger AI** (`ledger-app`, `ledgerai.software`) -- Compliance audit ledger for AI-generated financial content. Split into two Next.js apps (site + product) sharing one checkout per environment. Bun 1.3.11, Next.js 15.3, React 19.1, Clerk 7.x (org-scoped tenancy), Convex 1.34, Turbo 2.8, TypeScript 5.8. Product surface: advisor session, CCO review, org management, team/billing admin, API keys.

**Corral API** (`api-corral-v1`, `api.corral.software`) -- Standalone enforcement API for compliance queries across FDA drug labels, SEC EDGAR, eCFR, and NHTSA. Bun 1.3.11 custom HTTP server (not Next.js). Auth via machine API keys or Clerk JWT. Consumed by Ledger (server-side, org machine key in Clerk privateMetadata) and Portal Charlie (proxy routes with dedicated machine key).

**Portal Charlie** (`portal-charlie`, `hallucinationguard.app`) -- Hallucination Guard demo app. Next.js 16.2, Node/npm. Interactive demo chat lanes with multi-model support (OpenAI, Anthropic, Google). Corral API integration via proxy routes; falls back to local proof-shell when Corral env vars are unset.

**Portal Corral** (`portal-corral`, `corral.software`) -- Marketing site for Corral. Next.js 16.1, Node/npm. Lead capture with Convex persistence. No Corral API consumption.

## App Relationships

Corral API is the central enforcement backend. Ledger and Portal Charlie consume it; Portal Corral does not. All apps share platform accounts (Clerk, Convex, PostHog, Upstash, SMTP) but Ledger has its own Clerk app and Convex deployment. Portal Charlie staging and production share the same Convex URL, so staging does not isolate schema drift.

**Deploy ordering:** Corral API first when changing the v1 query contract. Convex before app when schema or functions change. Ledger site + product always together (shared checkout). Portal Corral and Portal Charlie are independent.

**Package managers:** Ledger and Corral API use Bun. Portal Corral and Portal Charlie use npm.

## Buffalo Developer Posture

This is the working philosophy across all repos. Manus plans must embody it:

1. **Go direct.** Work inside the real problem, not around it. Do not patch around a broken plan.
2. **Recon pass first.** Light pass before the heavy lift. Assess proof, narrow scope, then decide.
3. **Reach for the skill.** Use the existing workflow surface when one fits. Do not guess.
4. **Research before inventing.** Transcript-backed findings over best-practice assumptions. The 150M-word corpus is the first source.
5. **Minimum viable scope.** Patch what exists. No new architecture the work did not ask for.
6. **Proof is the output.** Do not close work without explicit proof. If it performs instead of produces, cut it.
7. **Finish what you start.** One IDE agent per task, no mid-task handoffs.

## Operator Roles

- **Manus** -- guide and planner outside the IDE. Builds plan packs and exchange packets. Does not execute code. Routes work to Codex or Opus.
- **Codex** -- executor inside the IDE. Deep bug work, long runs, direct implementation. Reads the plan, executes the code, proves the output.
- **Opus** -- advisor and designer inside the IDE. Copy, HTML/design, lightweight implementation, advisory work. Reads the plan, designs the solution, proves the output.

### Exchange Packet Format (Manus to Codex/Opus)

Every handoff includes: Agent, Mode (planning/review/implementation/proof/handoff), Goal (one sentence), Inputs (paths, topic, prior findings), Output (exact deliverable), Path policy (known/create/resolve), Proof expectation, Stop condition.

### Return Contract (Codex/Opus to Manus)

Every reply includes: Exact paths created or resolved, Next exchange recommendation, Next mode, Blockers (specific missing path or artifact, never vague).

### Failure Handling

Missing return fields: reject and re-request. Path resolution failure: agent returns blocker and stops. Incomplete execution: agent reports what was completed and what remains as a specific blocker.

## User Profile

Solo developer. No team coordination. Preferences:

- **Orchestrator pattern** for straightforward work (CSS + JSX, no backend complexity) -- single prompt pasted into one workspace. Reserve multi-tab scaffolds for genuinely parallel file trees.
- **UI iteration flags** -- do not pre-solve visual judgment calls in plan packs. Follow the plan literally, flag visual issues in the debrief after browser rendering.

## Research Layers & Skills

1. **Transcripts** -- guru chat and 150M words of creator content via `dev-hand-to-machine-*` skills.
2. **MCP tools** -- Ref (library/API docs), Exa (web search/fetch), Conductor (workspace coordination).
3. **Custom skills** -- 37 herd skills across Charlie tier (planning/session), Dev tier (execution/research), Coordination tier (handoffs), and Data/Ops tier. 
   - **Key Planning Skills:** `/dev-build-inputs`, `/dev-session-readiness`, `/dev-feedback-on-plan`, `/dev-mind-map`
   - **Key Execution Skills:** `/dev-proof`, `/dev-unblocker`, `/dev-debrief`
   - **Key Coordination Skills:** `/dev-hand-to-machine-mac-14`, `/dev-hand-to-machine-mac-16`, `/dev-read-inbox`, `/dev-inbox-status`

## Server Topology (Compact Reference)

**Droplet:** `174.138.113.240` (Charlie-Superstar, DigitalOcean). Admin: `ops` (sudo, SSH key). App: `portal` (owns `/opt/*`). Proxy: Caddy on 80/443.

**Services and ports:**
- `portal-corral` -- `:3000` -- `/opt/portal-corral` -- `main`
- `portal-charlie` -- `:3100` -- `/opt/portal-charlie` -- `main`
- `portal-charlie-staging` -- `:3101` -- `/opt/portal-charlie-staging` -- `staging`
- `api-corral` -- `:3201` -- `/opt/api-corral` -- `main`
- `ledger-site` -- `:3300` -- `/opt/ledger-app` -- `main`
- `ledger-site-staging` -- `:3301` -- `/opt/ledger-app-staging` -- `staging`
- `ledger-product` -- `:3302` -- `/opt/ledger-app` -- `main`
- `ledger-product-staging` -- `:3303` -- `/opt/ledger-app-staging` -- `staging`

All bind to `127.0.0.1`. Caddy routes public domains to local ports. Ledger site + product share a checkout per environment; code rollback reverts both together.

## Release Flow (Ledger)

1. `node ./scripts/run-bun.cjs run validate:release`
2. Push release commit to `origin/main`
3. Promote to staging: `./scripts/release/promote-to-staging.sh <commit>`
4. Deploy staging: `./scripts/deploy/staging.sh`
5. Deploy production: `./scripts/deploy/production.sh`

Deploy uses HTTPS+token pull (SSH deploy keys disabled at org level). Staging validates before production. Both `.env.site` and `.env.product` are sourced before the shared build.
