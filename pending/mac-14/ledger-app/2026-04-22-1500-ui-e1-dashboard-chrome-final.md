---
from: manus
to: mac-14
type: improvement
priority: high
created: 2026-04-22T15:00:00Z
repo: ledger-app
branch: main
requires_response: false
supersedes: 2026-04-22-1400-ui-e1-dashboard-chrome-patched.md
---

# Handoff: UI-E Phase 1 — Dashboard Chrome (Final Pack)

## Why This Supersedes the 1400 Handoff

The 1400 pack had the F1–F5 blockers resolved. This final pack adds three additional fixes applied by Opus review:

| Fix | Change |
|---|---|
| mind-map.md | E1 heading renamed to "Chrome + PageHeader"; `useUserSettings` moved to E2; E1 stop gate rewritten to chrome-only posture; Decision Log updated |
| plan-e1.md Phase 0 (CSS) | Pinned exact CSS import order for `apps/product/app/globals.css` (shared root first → Tailwind v4 → `@layer base` with `--sidebar`) and explicit `layout.tsx` swap instruction |
| plan-e1.md Phase 4 | Removed auth-group-layout hedge; pinned `app/layout.tsx` as sole edit surface; named all 6 current importers of `dashboard-layout.tsx` with explicit unwind-then-delete sequence + grep verification |

**Do not process the 1200 or 1400 handoffs. Process this one only.**

---

## Goal

Overhaul `apps/product`'s signed-in layout from a flat top-nav to a sidebar-driven dashboard using `shadcn/ui` sidebar-07, migrate the existing nav links into the sidebar, add a slim top header with Clerk user/org switcher + breadcrumbs, and deliver visual + build proof — on branch `feature/ui-e-dashboard-chrome`. `apps/site` must remain untouched. **Do not expand scope beyond chrome in this session.**

---

## Suggested Action

Invoke `/charlie-session` on mac-14 using `plans/ui-e/plan-e1.md` (attached below) as the primary input. Save all four plan files (`plan.md`, `plan-e1.md`, `mind-map.md`, `guru_management_ui_patterns.md`) to `plans/ui-e/` before starting.

---

## Key Constraints

- **Bun runtime:** Use `bunx` not `npx`. Build command is `node ./scripts/run-bun.cjs run build`.
- **Component home:** `apps/product/src/components/ui/` — do NOT create `packages/design-system`.
- **Color palette:** Sidebar `zinc-900` (`#18181b`), canvas `zinc-950` (`#09090b`). No warm tones.
- **CSS isolation:** `apps/product/app/globals.css` (new, product-local). Do NOT touch `app/globals.css` at repo root.
- **Scope lock:** `apps/site` zero changes. Only `apps/product` layout changes.
- **Branch:** `feature/ui-e-dashboard-chrome`
- **AppShell placement:** Inside `ProductProviders`, not replacing it.
- **No Convex in E1:** No `user_settings` table, no `useUserSettings`. Collapse state is in-memory only. Convex additions land in E2.
- **Layout surface:** `apps/product/app/layout.tsx` is the sole edit surface. No separate `(auth)` group layout exists.

---

## Attached: plans/ui-e/plan-e1.md (Final)

# Plan: UI-E Phase 1 — Dashboard Chrome Only

**Created:** 2026-04-22
**Status:** Pending
**Source:** Handoff from manus (`corral-inbox/pending/mac-14/ledger-app/2026-04-22-1500-ui-e1-dashboard-chrome-final.md`)
**Session posture:** Chrome only. E2/E3/E4 are **future handoffs**, not this session.

## Restated Instructions

> **The sender is asking:** Overhaul `apps/product`'s signed-in layout from a flat top-nav to a sidebar-driven dashboard using shadcn/ui `sidebar-07`, migrate the existing nav links into the sidebar, add a slim top header with Clerk user/org switcher + breadcrumbs, and deliver visual + build proof — on branch `feature/ui-e-dashboard-chrome`. `apps/site` must remain untouched. **Do not expand scope beyond chrome in this session.**

## Background

- `apps/product` currently uses a flat top-nav. Target: sidebar-driven shell matching Vanta/Drata/Secureframe density while keeping the zinc dark institutional palette.
- Reference implementation is `guru-management` (branch `chatbar-cats-local`) at `/Users/franklennon/conductor/workspaces/guru-chat-workflows/sydney/guru-management/`.
- Canonical UI patterns catalogue is `plans/ui-e/guru_management_ui_patterns.md` (8 patterns). **Only patterns #1 (Shell) and #8 (PageHeader) are in scope for this session.** Patterns #2–#7 are deferred to E2–E4 and future passes.
- The broader 4-PR strategy lives in `plans/ui-e/plan.md`. This file (`plan-e1.md`) scopes the current session strictly to phase E1.

## Objective

Ship a single clean PR on `feature/ui-e-dashboard-chrome` that:

- Wraps `apps/product`'s authenticated layout in a collapsible left sidebar (shadcn `sidebar-07` pattern).
- Moves existing nav items (Advisor, Governance Log, Billing, Team & Access, API Keys, Verify) into the sidebar.
- Adds a slim top header inside the main content area with breadcrumbs and the Clerk user/org switcher.
- Uses two CSS tokens for depth: `--background` (`zinc-950`) and `--sidebar` (`zinc-900`).
- Leaves `apps/site` entirely unchanged.
- Builds clean via `node ./scripts/run-bun.cjs run build`.

## Path Validation

All targets are inside the repo working directory, no `../` traversal, no sensitive files touched.

| Path | Status | Note |
|---|---|---|
| `apps/product/app/layout.tsx` | ✅ allowed | Canonical edit surface (no separate auth group layout exists) |
| `apps/product/src/components/dashboard-layout.tsx` | ✅ allowed | Delete at end of Phase 4 after unwinding 6 page importers |
| `apps/product/src/components/ui/sidebar.tsx` (new, via shadcn CLI) | ✅ allowed | App-level shadcn home |
| `apps/product/src/components/layout/AppShell.tsx` (new) | ✅ allowed | |
| `apps/product/src/components/layout/AppSidebar.tsx` (new) | ✅ allowed | |
| `apps/product/src/components/layout/Breadcrumbs.tsx` (new) | ✅ allowed | |
| `apps/product/src/components/layout/index.ts` (new) | ✅ allowed | |
| `apps/product/src/components/common/PageHeader.tsx` (new) | ✅ allowed | Pattern #8 |
| `apps/product/components.json` (new or edit) | ✅ allowed | shadcn config at app level |
| `apps/product/app/globals.css` (new, product-local) | ✅ allowed | Create this file for Tailwind directives + `--sidebar` token. Do NOT touch the shared `app/globals.css` at repo root. |
| `apps/site/**` | ⛔ forbidden this session | Scope lock per handoff |
| `packages/design-system/**` | ⛔ forbidden | Does not exist; do not create |

## Tasks

### Phase 0: Snow-Plow & Stack Initialization

- [ ] **Read before write:** Read `apps/product/app/layout.tsx`, `apps/product/components/product-providers.tsx`, and `app/globals.css`. Report ground truth before running any CLI.
- [ ] **Initialize Tailwind/shadcn:** The repo currently lacks Tailwind and shadcn. You must install `tailwindcss` v4, `@tailwindcss/postcss`, `postcss`, `lucide-react`, `class-variance-authority`, `clsx`, `tailwind-merge`, `tailwindcss-animate`, and required radix primitives (`@radix-ui/react-slot`, `@radix-ui/react-separator`, `@radix-ui/react-tooltip`, `@radix-ui/react-dialog`) plus `cmdk`.
- [ ] **Create `components.json`:** Create `apps/product/components.json` configured for app-local shadcn (aliases pointing to `apps/product/src/components`, `iconLibrary: "lucide"`, `baseColor: "neutral"`, `cssVariables: true`).
- [ ] **CSS Isolation:** Do NOT add Tailwind directives to the shared `app/globals.css` at the repo root. Instead, create `apps/product/app/globals.css` with this exact import order:

    ```css
    @import "../../../app/globals.css"; /* cascade shared zinc tokens from repo root */
    @import "tailwindcss";               /* Tailwind v4 single-line import */

    @layer base {
      :root {
        --sidebar: 240 5% 10%;           /* zinc-900 depth layer */
        /* other product-local shadcn tokens go here */
      }
    }
    ```

    Then update `apps/product/app/layout.tsx` to import `./globals.css` (the new product-local file) instead of `../../../app/globals.css` (the shared root file). `apps/site` keeps importing the root file unchanged.

### Phase 1: Scaffold shadcn in apps/product

- [ ] Run `bunx shadcn@latest add sidebar` inside `apps/product/`. Confirm files land in `apps/product/src/components/ui/` (not in a sibling app, not in a shared package).
- [ ] Confirm Bun resolves the install. If `bunx` is unavailable in the env, fall back to `node ./scripts/run-bun.cjs x shadcn@latest add sidebar`.

### Phase 2: Build the Shell

Use `guru-management/src/components/layout/` as a structural reference, but **scaffold fresh** rather than copy-pasting, as the source is entangled with non-existent hooks (`useImpersonation`, `usePortal`, `useMenuConfig`).

- [ ] Create `apps/product/src/components/layout/AppShell.tsx` wrapping `SidebarProvider` + `SidebarInset`, with desktop/mobile header split. **Crucially, this must sit inside the existing `ProductProviders` wrapper** found in `apps/product/app/layout.tsx`.
- [ ] Create `apps/product/src/components/layout/AppSidebar.tsx` using `Sidebar`, `SidebarHeader`, `SidebarContent`, `SidebarGroup`, `SidebarRail`, `collapsible="icon"`. Static nav array for this phase (no visibility filtering — E2 adds that).
- [ ] Create `apps/product/src/components/layout/Breadcrumbs.tsx` — derive from `usePathname()` or explicit items array, per pattern #8.
- [ ] Create `apps/product/src/components/layout/index.ts` re-exporting the three.
- [ ] Create `apps/product/src/components/common/PageHeader.tsx` with `title`, `description`, `icon`, `actions`, `breadcrumbs` props (pattern #8).

### Phase 3: Depth tokens

- [ ] Add `--sidebar: 240 5% 10%` (or equivalent zinc-900) to the **newly created `apps/product/app/globals.css`** (from Phase 0). Do **not** touch the shared `app/globals.css` at the repo root. Verify the sidebar component reads `var(--sidebar)`.
- [ ] Visually confirm zinc-900 sidebar sits on zinc-950 canvas. No warm tones.

### Phase 4: Migrate authenticated layout

- [ ] Edit `apps/product/app/layout.tsx` to wrap children in `<AppShell>…</AppShell>` — inside the existing `ProductProviders` wrapper. There is no separate `(auth)` group layout in this repo; `app/layout.tsx` is the canonical surface.
- [ ] Move Clerk `<UserButton />` / `<OrganizationSwitcher />` into the desktop header inside `AppShell`.
- [ ] Migrate nav items into `AppSidebar`: Advisor, Governance Log, Billing, Team & Access, API Keys, Verify. Use `lucide-react` icons.
- [ ] Unwind `apps/product/src/components/dashboard-layout.tsx` — currently imported by 6 pages: `app/advisor/page.tsx`, `app/cco/page.tsx`, `app/api-keys/page.tsx`, `app/billing/page.tsx`, `app/team/page.tsx`, `app/organization/select/page.tsx`. Remove the `<DashboardLayout>` wrapper from each (the new `AppShell` at the root layout replaces it), then delete `dashboard-layout.tsx`. Verify zero remaining imports via `grep -r "dashboard-layout\|DashboardLayout" apps/product`.

### Phase 5: Proof

- [ ] `node ./scripts/run-bun.cjs run validate:release` — clean.
- [ ] `node ./scripts/run-bun.cjs run build` — clean.
- [ ] Run `apps/product` locally. Screenshot `/advisor` and `/cco` inside the new shell (sidebar expanded).
- [ ] Screenshot sidebar collapsed to icons.
- [ ] Screenshot `apps/site` home page to prove it's unchanged.
- [ ] Commit on `feature/ui-e-dashboard-chrome`, open PR against `main`.

## Out of Scope (Explicit, per operator)

- **E2 (visibility/menu toggle):** no `useVisibilityAccess`, no `useMenuConfig`, no `HeaderMenuVisibilityDropdown`, no Convex schema additions.
- **E3 (menu manager):** no `/admin/menus` page, no `DataTable`, no `MenuConfigEditor`.
- **E4 (category manager):** no `/admin/categories`, no `CategoryManagerTree`.
- **#6 CommandPalette, #7 StatCards:** future passes.
- **`apps/site`:** zero changes.
- **`packages/design-system`:** not created.
- **`useUserSettings` collapse persistence:** deferred to E2 (needs Convex `user_settings` table, which fits cleaner with E2's Convex port). Sidebar collapse state this session is in-memory only.
- Color token retune — ui-c's zinc palette stands; we add `--sidebar` only.

## Success Criteria

- [ ] `apps/product` builds clean via `node ./scripts/run-bun.cjs run build`.
- [ ] `/advisor` and `/cco` render inside the new sidebar shell.
- [ ] Sidebar collapses to icons and expands back.
- [ ] Clerk user/org switcher accessible in the new header.
- [ ] Zinc-900 sidebar / zinc-950 canvas depth visible, no warm tones.
- [ ] `apps/site` screenshot identical to pre-change state.
- [ ] PR opened on `feature/ui-e-dashboard-chrome` with all four screenshots attached.

## Stop Condition

Chrome renders, palette holds, site untouched, build clean, PR open. **Do not start E2.** Next handoff from manus will arrive after this ships to staging and is verified.

## Related

- **This handoff:** `corral-inbox/pending/mac-14/ledger-app/2026-04-22-1500-ui-e1-dashboard-chrome-final.md`
- **Supersedes:** `corral-inbox/pending/mac-14/ledger-app/2026-04-22-1400-ui-e1-dashboard-chrome-patched.md`
- **Strategic plan (4-PR overview):** `plans/ui-e/plan.md`
- **Mind map:** `plans/ui-e/mind-map.md`
- **Canonical patterns reference:** `plans/ui-e/guru_management_ui_patterns.md`
- **Reference implementation (read-only):** `/Users/franklennon/conductor/workspaces/guru-chat-workflows/sydney/guru-management/` (branch `chatbar-cats-local`)
