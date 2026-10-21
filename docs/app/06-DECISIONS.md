# Decisions

### D-001 — No Base44↔GitHub sync; app source lives in a separate repo
**Status:** Active
**Decision:** This repo (`cathys-kitchen`) does not connect Base44's GitHub sync. It holds only the durable spec/decision record (`docs/app/*.md`) and PR history. The actual Base44 app source for Cathy's Kitchen lives in `TFRS-Admin/cathy-kitchen-2` — a pre-existing Base44 app scaffold (originally built and branded for a different, unrelated business) that is being rebranded and repointed for this project rather than built from scratch.
**Why:** `docs/REPO_SETTINGS.md` protects `main` here (PR-only, linear history); a second writer syncing from Base44 would conflict with that. A working Base44 React/Vite scaffold with reusable entity/RLS/admin-auth patterns already existed, so adapting it was smaller than building a second app via the builder AI from zero.
**Implication:** Future agents implementing or changing the actual site should work in `TFRS-Admin/cathy-kitchen-2`, not here. Changes to business facts, data model, or integration design still get recorded in this repo's `docs/app/*.md` first.

### D-002 — System boundary: Base44 is the entire public site and native form surface; GHL is the sole system of record
**Status:** Active (its "weekly dinner request" flow specifically is superseded by D-008 — that form no longer exists)
**Decision:** Native Base44 forms call narrow Base44 server-side functions that write to GoHighLevel via its API using server-side secrets — not redirects to GHL-hosted forms. No customer, order, or catering data may be intentionally stored in any Base44 data table; Base44 entities hold public website content only (menu, FAQs, photos, site settings). At the time this was written the site had three planned native forms (weekly dinner request, catering inquiry, email signup); only catering inquiry exists today — see D-008.
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

### D-007 — The interim owner-notification-email fallback is pre-launch scaffolding only; it is a launch-blocking gap, not an accepted second integration path
**Status:** Active (scope narrowed by D-008 — applies to the catering form only; weekly ordering is no longer a Base44 form at all)
**Decision:** Until real GHL secrets are set, the catering form in `TFRS-Admin/cathy-kitchen-2` (`sendOrder`) falls back to emailing the owner instead of writing to GHL. This exists solely so the form can be built and tested end-to-end before GHL is wired up. The site must not be published (`SiteSettings.content_status` set to `live`, or a public domain attached) while this fallback is still active.
**Why:** Caught by automated review on PR #1 — as originally documented, this fallback contradicted BR-001 ("GHL is the sole system of record") and acceptance test #11 ("no form submission sends an email... until GHL activation is separately approved"). Nothing in the code or docs previously stopped this interim mechanism from reaching real customer traffic if the app were prematurely linked/published; it is real customer PII (name, phone) transiting through an ad hoc, unapproved channel outside GHL.
**Implication:** The real GHL catering write path (`TFRS-Admin/cathy-kitchen-2`#20) is a hard prerequisite for publish, not a nice-to-have — see `04-ARCHITECTURE.md`'s constraints. It's implemented but inert until the GHL secrets listed there are set; any future agent finding the fallback still active at publish time must treat it as a stop condition, not proceed. Weekly ordering has no equivalent gate — see D-008.

### D-008 — Weekly ordering has no native Base44 form; it is a direct SMS to a GHL-owned number. Catering keeps D-002's original shape.
**Status:** Active
**Decision:** D-002 described a native "weekly dinner request" form calling a Base44 function that writes to GHL. That form was removed from `TFRS-Admin/cathy-kitchen-2` when the torn-paper design system was adopted (the system doesn't support a multi-field order form; the owner confirmed removing it rather than inventing a component outside the system) — weekly ordering is now a plain "Text to order" `sms:` link to a number `SiteSettings` stores. Per explicit owner instruction, that number is being replaced with one GHL provisions and owns, so inbound order texts land in GHL's own Conversations/CRM and auto-create a contact with zero Base44 code — Cathy will check order texts in GHL going forward, not her personal Messages app. The catering inquiry form is the one native form that still exists, and keeps D-002's original integration shape (Base44 function → GHL contact/opportunity write via API), implemented in `TFRS-Admin/cathy-kitchen-2`#20.
**Why:** D-002 assumed a native form for every customer-facing data path; that's no longer true for weekly ordering, and the gap would otherwise leave the architecture docs describing a flow that doesn't exist. Routing weekly ordering through a GHL-owned number, rather than inventing a Base44-side capture mechanism for it, uses GHL's native SMS/CRM capability instead of duplicating it.
**Implication:** `04-ARCHITECTURE.md`'s weekly-ordering flow, integration data list, and secret names are updated to match (no `GHL_WEEKLY_ORDERS_*` secrets — there is no weekly-order pipeline or function). Any future agent must not reintroduce a native weekly-order form without a new decision superseding this one. The GHL number swap is only a `SiteSettings.sms_number`/`phone_display` data edit, no code change — but that edit happens only once the new number is actually provisioned and confirmed working (D-006 still gates any Base44 secret).

---

Do not record routine library choices, ordinary implementation details, completed tasks, bug fixes, or facts already authoritative elsewhere.
