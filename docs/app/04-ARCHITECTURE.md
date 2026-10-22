# Architecture

## Summary
Base44 hosts the entire public website, the native catering form, and the owner-only menu editor — full stop. There is no email-signup form (the design system that shipped after this document was first written doesn't support one). The catering form emails the owner directly; that is the permanent mechanism, not a stand-in for something else (`06-DECISIONS.md` D-009). No customer data is stored in a Base44 entity — Base44 entities hold public website content only (menu, FAQs, photos, site settings); catering leads exist only in the owner's email inbox.

Weekly ordering is the one place this project's architecture currently forks: `TFRS-Admin/cathy-kitchen-2` carries two parallel, both-current implementations, deliberately kept unmerged into each other per explicit owner instruction (`06-DECISIONS.md` D-010, superseding D-009's "GHL dropped entirely" for weekly ordering specifically — D-009's catering content still holds). `main` is a plain "Text to order" `sms:` link to Cathy's own personal number, same as before, with no CRM integration. `feature/ghl-order-funnel` replaces that link with a native form that writes the order into GoHighLevel via a server-side function, inert until real GHL credentials are set. See **Weekly ordering** below for both flows in full, and `TFRS-Admin/cathy-kitchen-2`'s `GHL_SETUP.md` for the GHL branch's setup and test checklist.

The actual Base44 app source for this build lives in a separate GitHub repo, `TFRS-Admin/cathy-kitchen-2` — a Base44 app scaffold that was rebranded from a different, unrelated business for this purpose. This repo (`cathys-kitchen`) holds only the durable spec/decision record; see D-001.

## Platform boundaries
| Concern | Owner/choice | Responsibility boundary |
|---|---|---|
| Application | Base44 (`TFRS-Admin/cathy-kitchen-2`) | Public site, the native catering form, owner menu editor |
| Data (public content) | Base44 entities — `SiteSettings`, `MenuWeek`, `MenuDay`, `FAQItem`, `Testimonial` | Menu/FAQ/testimonial/site-settings content only |
| Data (catering leads) | The owner's email inbox (via Base44's `SendEmail` integration) | Sole record of a catering inquiry — nothing is stored in Base44 or any CRM |
| Data (weekly orders, `main`) | Nowhere — a plain `sms:` link to Cathy's personal number | No form, server-side function, or data write of any kind |
| Data (weekly orders, `feature/ghl-order-funnel`) | GoHighLevel, via the `submitOrder` function | Sole record of a web order — not a Base44 entity (see Weekly ordering below) |
| Authentication | Base44 built-in auth, admin role only | Gates the owner editor; the public site never requires any account |
| Deployment | Base44 | Build, preview, and publish |

## Major system flows

### Weekly ordering — two parallel branches (`06-DECISIONS.md` D-010)
Both branches share the same same-day ordering cutoff — **10:00 AM America/Chicago**, kept in one constant (`base44/shared/orderCutoff.js`, re-exported for the frontend as `src/lib/orderCutoff.js`). A day already past, or today once the cutoff has passed, shows a disabled Order button with a short note instead of a live link/form, on both branches.

**`main` — plain SMS, no Base44 form or server-side involvement**
1. The visitor clicks "Order for N" on a dish; if the week has any on-this-week extras, an in-page picker offers them first.
2. The control itself is a pre-filled `sms:` link to Cathy's own personal number (`SiteSettings.sms_number`/`phone_display`) — no Base44 form, page JavaScript beyond building that link, or server-side function is involved at any point.
3. Cathy reads and replies from her own phone's Messages app, same as before any of this was built.

**`feature/ghl-order-funnel` — GHL-backed native form, not yet merged to `main`**
1. Same extras picker (if any), then "Order for N" opens a native Name + Phone form (both required) instead of linking out.
2. On submit, the Base44 server-side function `submitOrder` re-validates name, phone (normalized to E.164), the required day/week fields, and the same-day cutoff above, and silently no-ops a filled honeypot.
3. If validation passes: upserts the visitor as a GHL contact by phone, tags it `web-order` via a separate call (GHL's upsert overwrites tags rather than merging them), adds one new timestamped note with the order summary, and notifies the owner two ways — a GHL internal comment on her own contact record, and the existing owner-notification email — both best-effort, so either failing doesn't lose the order (the contact + note already captured it).
4. The confirmation screen displays the exact order-summary string the server returns — the same string used in the note and the notification, built once per order by a single function, so the three can't drift apart.
**Failure behavior:** if the GHL call fails, the visitor sees an error and the same pre-filled `sms:` link `main` uses, so no order is ever silently lost.
**Status:** code-complete but inert — `GHL_PRIVATE_INTEGRATION_TOKEN`, `GHL_LOCATION_ID`, and `GHL_OWNER_CONTACT_ID` aren't set yet, and no Cathy's Kitchen GHL location or Private Integration token exists yet (human-only setup, no API for either). See `GHL_SETUP.md` in `TFRS-Admin/cathy-kitchen-2` for the required secrets, scopes, and a manual test checklist.

### Catering inquiry
1. Visitor submits the native catering form in Base44 (name, phone, event date, guest count, notes).
2. A Base44 server-side function (`sendOrder` in `TFRS-Admin/cathy-kitchen-2`) validates input, rejects a filled honeypot, and emails the owner the inquiry details.
3. The browser receives only a success/error state.
**Failure behavior:** if the email send itself fails (e.g. `OWNER_NOTIFICATION_EMAIL` isn't configured), the function returns an error and the visitor is told to text Cathy directly instead.

## Integrations
**GoHighLevel — weekly ordering only, `feature/ghl-order-funnel` branch only, not yet live.** Reintroduced per `06-DECISIONS.md` D-010, superseding D-009's "dropped entirely" for weekly ordering specifically; D-009's catering content is unchanged — catering still emails the owner, never GHL. The branch's `submitOrder` function (GHL API v2, `Version: v3`) upserts a contact, tags it, adds a note, and best-effort-notifies the owner per order; see `TFRS-Admin/cathy-kitchen-2`'s `GHL_SETUP.md` for the exact endpoints, required secrets, and Private Integration scopes. `main` carries no GHL (or any other CRM) integration at all. Neither branch writes customer/order data into a Base44 entity.

## Constraints
- No customer, order, or catering data may be intentionally stored in a Base44 data table.
- `.github/workflows/validate-template.yml` runs `markdown-link-check` on every `.md` file in this repo as a required check. Any live/volatile/private URL recorded in `docs/app/*.md` (a Base44 preview URL, an unregistered domain) must be written in backticks as plain text, never as a clickable markdown link, or it will fail CI.
- This repo does not sync with Base44's GitHub integration (D-001) — `main` here stays protected, PR-only.
- `TFRS-Admin/cathy-kitchen-2` carries two deliberately separate, both-current branches for weekly ordering (`06-DECISIONS.md` D-010). A non-order-flow fix always lands on that repo's `main` first and is then merged into `feature/ghl-order-funnel`, never the reverse; the two branches are not merged into each other without a further, explicit owner instruction.

## Diagram
Not needed — the two-repo, two-system split above is small enough to describe in prose.
