---
from: manus
to: mac-14
type: improvement
priority: medium
created: 2026-04-22T12:00:00Z
repo: ledger-app
branch: main
requires_response: false
---

# Handoff: UI-E — Dashboard Chrome Overhaul

## Context

Frank has approved a new UI round for Ledger AI. The signed-in product (`apps/product`) currently uses a flat, pure-black top-nav layout. The goal is to upgrade it to a sidebar-driven dashboard architecture that matches the quality and density of `guru-management` (chatbar-cats-local branch), which Frank considers a near-finished UI product.

## Goal

Overhaul the `apps/product` signed-in layout to use a collapsible left sidebar (`shadcn/ui` sidebar-07 pattern), matching the institutional, high-density compliance SaaS aesthetic of Vanta/Drata/Secureframe while retaining the zinc dark palette.

## Relevant Files

Two plan documents accompany this handoff. They are the canonical inputs for this session:

- `plans/ui-e/plan.md` — Full execution plan with scope, design specs, step-by-step instructions, and proof expectations.
- `guru_management_ui_patterns.md` — Deep reference document cataloguing every hard-won UI pattern from `guru-management` that must be ported to Ledger. This includes the `AppShell`/`AppSidebar` composition, `DataTable`, `EnhancedStatCard`, `CommandPalette`, `MenuConfigEditor`, `CategoryManagerTree`, `useVisibilityAccess`, and `PageHeader`.

Both documents are attached inline below.

## Suggested Action

Invoke `/charlie-session` on mac-14 using the two plan documents as inputs. The session should:

1. Add `shadcn/ui` sidebar to `apps/product/src/components/ui/` via `bunx shadcn@latest add sidebar`.
2. Build `AppShell` and `AppSidebar` components in `apps/product/src/components/layout/` mirroring the `guru-management` pattern exactly.
3. Restructure the authenticated layout in `apps/product` to use `SidebarProvider` and `SidebarInset`.
4. Migrate all existing top-nav links into the new sidebar.
5. Build a slim top header with Clerk user/org switcher and breadcrumbs.
6. Verify `apps/site` is untouched.
7. Deliver proof: screenshot of `/advisor` and `/cco` inside the new shell, clean build via `node ./scripts/run-bun.cjs run build`.

## Key Constraints

- **Bun runtime:** Use `bunx` not `npx`. Build command is `node ./scripts/run-bun.cjs run build`.
- **Component home:** `apps/product/src/components/ui/` — do NOT create `packages/design-system`.
- **Color palette:** Sidebar `zinc-900` (`#18181b`), canvas `zinc-950` (`#09090b`). No warm tones.
- **Scope lock:** `apps/site` is untouched. Only `apps/product` layout changes.
- **Branch:** `feature/ui-e-dashboard-chrome`

## Source

- **Repo:** ledger-app
- **Branch:** main
- **Plan documents:** See attached content below

---

## Attached: plans/ui-e/plan.md

# UI-E: Dashboard Chrome Overhaul

### Goal
Overhaul the `apps/product` signed-in layout from a flat top-nav to a sidebar-driven dashboard architecture using `shadcn/ui` Sidebar components. This aligns Ledger AI with modern compliance SaaS patterns (Vanta, Drata, Secureframe) while maintaining the zinc dark institutional aesthetic.

### Scope & Boundaries
- **Target App:** `apps/product` ONLY.
- **Excluded:** `apps/site` must remain untouched.
- **Component Home:** `apps/product/src/components/ui/` (app-level shadcn, mirroring `guru-management`). Do NOT build `packages/design-system`.
- **Runtime:** Next.js 15 + Bun. Use `bunx shadcn@latest add sidebar`.

### Design Specifications
- **Layout Pattern:** `shadcn/ui` `sidebar-07` (collapsible-to-icon left rail).
- **Reference:** Mirror `AppShell`/`AppSidebar` from `guru-management` (`AI-321-Digital/guru-chat-workflows`, branch `chatbar-cats-local`, path `guru-management/src/components/layout/`).
- **Color Palette:** Sidebar `zinc-900`, canvas `zinc-950`. No warm tones.
- **Nav Items:** Advisor, Governance Log, Billing, Team & Access, API Keys, Verify.
- **Header Chrome:** Slim top bar with Clerk user/org switcher and breadcrumbs.

### Execution Steps
1. Add shadcn sidebar to `apps/product/src/components/ui/` via `bunx shadcn@latest add sidebar`.
2. Modify authenticated layout in `apps/product/src/app/` to use `SidebarProvider` and `SidebarInset`.
3. Create `apps/product/src/components/layout/AppSidebar.tsx` with migrated nav links.
4. Create slim top header with Clerk components and breadcrumbs.
5. Remove old top navigation component.
6. Verify zinc-900/950 depth tokens are applied correctly.

### Proof Expectation
- Clean build on branch `feature/ui-e-dashboard-chrome`.
- Screenshot of `/advisor` and `/cco` inside new sidebar shell.
- Screenshot of sidebar collapsed to icons.
- Confirmation `apps/site` is unaffected.

### Stop Condition
`apps/product` builds cleanly, sidebar is collapsible, palette is zinc dark, Clerk components are in the header.

---

## Attached: guru_management_ui_patterns.md

# Guru Management UI Patterns: Reference Guide for Ledger AI

This document catalogues the hard-won UI patterns from `guru-management` (branch `chatbar-cats-local`) that must be ported to Ledger AI.

### 1. Dashboard Shell (AppShell & AppSidebar)
- **Files:** `src/components/layout/AppShell.tsx`, `src/components/layout/AppSidebar.tsx`
- **Primitives:** `SidebarProvider`, `SidebarInset`, `SidebarTrigger`, `Sidebar`, `SidebarHeader`, `SidebarContent`, `SidebarFooter`, `SidebarRail`
- **Key patterns:** `collapsible="icon"` variant; distinct `--sidebar` CSS var for depth; menu collapse state persisted via `useUserSettings`; mobile `SidebarTrigger` in `MobileHeader`; desktop header with account control in `DesktopHeader`.

### 2. Data Tables (DataTable)
- **File:** `src/components/ui/data-table.tsx`
- **Key patterns:** Generic `<T>` wrapper; built-in `SkeletonRows`, `ErrorState`, `EmptyState`; integrated search input with clear button; `toolbar` prop for injecting custom actions; `DataTablePagination` sub-component with page size selector.

### 3. Visibility & Access Control
- **Files:** `src/hooks/useVisibilityAccess.ts`, `src/lib/access-policy.ts`, `src/types/contracts.ts`
- **Key patterns:** `VisibilityKey` union type (nav.* and feature.* keys); `useVisibilityAccess()` hook resolves keys against Convex backend; `hasAccess(keys)` used in `AppSidebar` to filter nav groups before render; `ADMIN_ACCESS_KEYS`, `WORKFLOWS_ACCESS_KEYS` etc. as const arrays.

### 4. Menu Management
- **Files:** `src/components/admin/menus/MenuConfigEditor.tsx`, `src/components/layout/HeaderMenuVisibilityDropdown.tsx`
- **Key patterns:** `MenuConfigBlock` → `MenuConfigItem` → `MenuConfigChildItem` hierarchy; up/down reorder buttons; Eye/EyeOff visibility toggles with optimistic pending state; quick-access dropdown in the top header for fast block visibility toggling without entering settings.

### 5. Category Management
- **File:** `src/components/admin/categories/CategoryManagerTree.tsx`
- **Key patterns:** Tree rendering of hub/leaf hierarchy; `Sheet` slide-out for create/edit forms; `readiness_status` and `sensitivity_tier` selects; `sort_order` numeric input.

### 6. Command Palette
- **File:** `src/components/CommandPalette.tsx`
- **Key patterns:** `Cmd+K` trigger; `CommandDialog`, `CommandInput`, `CommandList`, `CommandGroup`, `CommandItem` from shadcn; access-gated navigation commands; dynamic data search (sessions, topics) with loading states; `CommandPaletteHint` component for keyboard shortcut display.

### 7. High-Density Stat Cards
- **File:** `src/components/dashboard/EnhancedStatCard.tsx`
- **Key patterns:** `EnhancedStatCard` with `title`, `value`, `trend`, `sparklineData`, `icon`; `TrendIndicator` with emerald/red color coding; `Sparkline` using Recharts `AreaChart`; `StatCardsGrid` wrapper with 2/3/4/5/6 column responsive layouts.

### 8. Page Headers
- **File:** `src/components/common/PageHeader.tsx`
- **Key patterns:** `PageHeader` with `title`, `description`, `icon`, `actions`, `breadcrumbs` props; `Breadcrumbs` component from `src/components/layout/Breadcrumbs.tsx` using `usePathname()` for auto-derivation or explicit items array.
