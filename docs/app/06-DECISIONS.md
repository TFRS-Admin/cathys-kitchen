# Decisions

### D-001 — No Base44↔GitHub sync; app source lives in a separate repo
**Status:** Active
**Decision:** This repo (`cathys-kitchen`) does not connect Base44's GitHub sync. It holds only the durable spec/decision record (`docs/app/*.md`) and PR history. The actual Base44 app source for Cathy's Kitchen lives in `TFRS-Admin/cathy-kitchen-2` — a pre-existing Base44 app scaffold (originally built and branded for a different, unrelated business) that is being rebranded and repointed for this project rather than built from scratch.
**Why:** `docs/REPO_SETTINGS.md` protects `main` here (PR-only, linear history); a second writer syncing from Base44 would conflict with that. A working Base44 React/Vite scaffold with reusable entity/RLS/admin-auth patterns already existed, so adapting it was smaller than building a second app via the builder AI from zero.
**Implication:** Future agents implementing or changing the actual site should work in `TFRS-Admin/cathy-kitchen-2`, not here. Changes to business facts, data model, or integration design still get recorded in this repo's `docs/app/*.md` first.

### D-002 — System boundary: Base44 is the entire public site and native form surface; GHL is the sole system of record
**Status:** Active
**Decision:** Native Base44 forms (weekly dinner request, catering inquiry, email signup) call narrow Base44 server-side functions that write to GoHighLevel via its API using server-side secrets — not redirects to GHL-hosted forms. No customer, order, catering, or subscriber data may be intentionally stored in any Base44 data table; Base44 entities hold public website content only (menu, FAQs, photos, site settings).
**Why:** Explicit, current owner instruction, overriding the original brief's "safest first integration" recommendation (GHL-hosted-form redirects) per the authority order in `00-README.md` (owner's current instruction ranks above the approved build brief).
**Implication:** Any future form or data model change must keep customer data out of Base44. The GHL credential and location/pipeline/field/tag IDs must be verified against the real Cathy's Kitchen GHL account before use — never assumed from another client's account in the workspace.

### D-003 — `MenuDay.serve_date` is stored explicitly
**Status:** Active
**Decision:** Each `MenuDay` record stores its own `serve_date`, rather than deriving it from `MenuWeek.week_start_date + day_of_week` at render time.
**Why:** Avoids timezone and week-rollover bugs around "what is today/this week" for a cutoff- and pickup-window-driven site.
**Implication:** Any menu-creation flow (e.g. "Create Next Week") must compute and persist `serve_date` for each day up front.

### D-004 — No per-dish GHL form or link
**Status:** Active
**Decision:** The per-dish "Request this dinner" control opens the single native weekly-order form with that dish preselected, rather than a separate form or a separate GHL destination per dish.
**Why:** Keeps one request form and one GHL integration path; avoids multiplying forms/functions per dish.
**Implication:** Do not add a second native or GHL-hosted form for individual dishes.

### D-005 — The approved build brief is the design authority
**Status:** Active
**Decision:** With no separate Figma/wireframe/design package ever supplied, the brief itself (including its visual-direction table) is treated as the approved design authority referenced by `03-DESIGN.md`.
**Why:** `00-README.md`'s authority order ranks the approved build brief above `03-DESIGN.md` itself; this makes "verify design fidelity" (per `AGENTS.md`'s required workflow) executable instead of undefined.
**Implication:** A future design handoff, if one ever arrives, supersedes this and should be recorded as a new decision.

### D-006 — GHL asset creation is scoped and verified before any Base44 secret is set
**Status:** Active
**Decision:** The GHL location, pipelines, stages, most custom fields, and tags are confirmed/created against the real, verified Cathy's Kitchen GHL account before any corresponding Base44 secret is configured. Nothing is assumed from another client's location in the connected workspace.
**Why:** None of the GHL locations visible in the connected workspace matches Cathy's Kitchen; using the wrong location/credential would be a serious data-routing error.
**Implication:** Treat "no verified GHL location/credential yet" as a stop condition for wiring the real integration (see `04-ARCHITECTURE.md`), not something to work around with a guess.

---

Do not record routine library choices, ordinary implementation details, completed tasks, bug fixes, or facts already authoritative elsewhere.
