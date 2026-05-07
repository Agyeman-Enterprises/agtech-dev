# AGtech Bootstrap Sprint

**Sprint ID:** bootstrap-2026-W19
**State:** complete
**Dates:** 2026-05-06 → 2026-05-07
**Goal:** Ship a working AGtech dashboard (Kanban board, HITL inbox, card detail, sprint management) with full auth, CRUD, and behavioral test coverage; initialize the methodology repo.

---

## Cards in This Sprint

| ID | Title | Severity | Type | Owner |
|----|-------|----------|------|-------|
| AGT-BOOT-001 | Build AGtech Kanban dashboard with Supabase + Next.js 15 | high | new_build | Builder AI |
| AGT-BOOT-002 | Wire HITL inbox and card state approval flow | high | new_build | Builder AI |
| AGT-BOOT-003 | Add EditCardButton modal with full field editing and delete | medium | new_build | Builder AI |
| AGT-BOOT-004 | Color-code Kanban column headers by state | low | new_build | Builder AI |
| AGT-BOOT-005 | Fix signout scope bug (global → local) | high | issue_fix | Builder AI |
| AGT-BOOT-006 | Fix UpdateSchema missing `state` field (Zod silently stripping) | high | issue_fix | Builder AI |
| AGT-BOOT-007 | Expand Playwright suite to cover Gates 3-6 (35 tests) | medium | new_build | Builder AI |
| AGT-BOOT-008 | Initialize agtech-dev v0.1 methodology repo | medium | new_build | Builder AI |

---

## Acceptance Criteria

Sprint is COMPLETE when:

- [x] All cards in state `shipped` or `rejected`
- [x] Akua has signed completion_approval (bootstrap sprint — waived, Akua is the builder)
- [x] `npx playwright test` passes on deployed build (35/35)
- [x] No regressions in previously passing tests
- [x] Vercel deployment live: `https://agtech-ecru.vercel.app`
- [x] GitHub repo live: `Agyeman-Enterprises/agtech`
- [x] Methodology repo initialized: `Agyeman-Enterprises/agtech-dev`

---

## What Was Built

### Application (`Agyeman-Enterprises/agtech`)

**Stack:** Next.js 15.5, Supabase SSR, Tailwind CSS, Radix UI, Playwright

**Features shipped:**
- Sprint management (create, list, detail view)
- Kanban board with 10 state columns, drag-and-drop card reordering
- Color-coded column headers (slate/blue/amber/purple/violet/orange/cyan/emerald/red)
- Card creation modal (title, description, type, severity)
- Card detail view with full field display and artifact history
- EditCardButton modal — edit all fields including state, assigned_machine, branch; two-click delete
- HITL inbox (`/hitl`) — shows all cards in `spec_approval` or `completion_approval` states
- Spec approval and completion approval actions with reject (with note) and approve flows
- Escalation inbox
- Supabase SSR auth (OTP email + password visibility toggle)
- `audit_log` table wired to all API routes
- Full CRUD for sprints and cards via REST API handlers

**Bugs fixed:**
- `signOut()` was using `scope: "global"` (default) — revokes refresh token in DB, invalidated all Playwright sessions. Fixed to `scope: "local"`.
- `UpdateSchema` Zod object was missing `state` field — silently stripped on every PATCH. Added.
- `updateCard()` Pick type was missing `"state"`. Added.
- `waitForURL()` in Playwright used string (exact match) — fails for path-only URLs. Changed to `RegExp`.
- HITL inbox label was "HITL Inbox" in tests but "Escalation Inbox" in UI. Tests corrected.

**Test coverage (35 Playwright tests):**
- Gate 3: Auth (login, session persistence, signout isolated, password visibility toggle)
- Gate 4: CRUD (sprint create, card create, card edit with state change + DB verify, card delete)
- Gate 5: Navigation (logo, sprints link, escalations link, new sprint button, breadcrumb)
- Gate 6: Data integrity (audit log writes on API calls)
- Gate 7: Behavioral GATE7.txt checks (HITL flow, spec approval, completion approval)
- Gate 8: Full Playwright suite

### Methodology Repo (`Agyeman-Enterprises/agtech-dev`)

- `README.md` — overview, state machine, 5-AI loop, org chart
- `JOB_CARD_SCHEMA.md` — canonical field definitions, state machine diagram, artifact types
- `SPRINT_PLAN_FORMAT.md` — sprint plan template
- `HITL_GATES.md` — Gate 1 and Gate 2 detailed specs, escalation protocol, anti-patterns
- `roles/spec-writer.md` — Spec Writer AI system prompt + protocol
- `roles/architect.md` — Architect AI system prompt + protocol
- `roles/builder.md` — Builder AI system prompt + protocol
- `roles/auditor.md` — Auditor AI system prompt + protocol
- `roles/verifier.md` — Verifier AI system prompt + protocol
- `.github/ISSUE_TEMPLATE/job-card.yml` — GitHub Issue template in Job Card schema

---

## Out of Scope

- CTO Agent (Node.js on AMIACODA) — next sprint
- AGtech MCP Server — next sprint
- Coolify/Hetzner dual deployment — next sprint
- Supabase auth redirect URL update (manual step: add `https://agtech-ecru.vercel.app/**` to allowlist in Supabase project `maauhdiylqgfczloornk`)

---

## Lessons Learned

1. **Zod silently strips unrecognized fields.** If a PATCH field isn't in the schema, it vanishes. Always verify the Zod schema when debugging "the save doesn't work" issues — the API returns 200 with no error.

2. **`signOut({ scope: "global" })` revokes the refresh token DB-side.** Any other session (including Playwright's shared `auth-state.json`) using that token becomes invalid immediately. For test isolation, use `scope: "local"` in the app AND isolate signout tests in a fresh browser context.

3. **`waitForURL` requires exact string match.** `waitForURL("/sprints/abc")` fails when Playwright prepends the base URL. Use `waitForURL(new RegExp("/sprints/abc$"))` for path-only matching.

4. **Playwright strict mode.** `locator("text=Card Title")` fails if that text appears in more than one element (e.g., breadcrumb + h1). Always scope to the specific element: `locator("h1").toContainText(...)`.

5. **`beforeAll` fixtures in Playwright.** Using `async ({ request })` in `test.beforeAll` gives you an authenticated Playwright request fixture — the right way to seed data that goes through the app's API (and therefore triggers audit logs, middleware, etc.). Direct Supabase REST calls bypass all app-level logic.
