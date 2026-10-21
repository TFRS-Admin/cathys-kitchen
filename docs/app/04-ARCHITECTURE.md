# Architecture

## Summary
Base44 hosts the entire public website, the one remaining native form (catering inquiry), and the owner-only menu editor. There is no native weekly-order form and no email-signup form — the design system that shipped after this document was first written doesn't support either, and weekly ordering is instead a plain "Text to order" `sms:` link (see `06-DECISIONS.md` D-008, which supersedes D-002's weekly-dinner-request flow). GoHighLevel (GHL) is still the sole system of record for all customer, order, and catering data — nothing customer-identifying is ever stored in a Base44 entity (D-002). Base44 server-side functions are the only code allowed to call the GHL API; GHL credentials live only in Base44's secret store, never in frontend code or this repo.

The actual Base44 app source for this build lives in a separate GitHub repo, `TFRS-Admin/cathy-kitchen-2` — a Base44 app scaffold that was rebranded from a different, unrelated business for this purpose. This repo (`cathys-kitchen`) holds only the durable spec/decision record; see D-001.

## Platform boundaries
| Concern | Owner/choice | Responsibility boundary |
|---|---|---|
| Application | Base44 (`TFRS-Admin/cathy-kitchen-2`) | Public site, the native catering form, owner menu editor |
| Data (public content) | Base44 entities — `SiteSettings`, `MenuWeek`, `MenuDay`, `FAQItem`, `Testimonial` | Menu/FAQ/testimonial/site-settings content only |
| Data (customer/order/catering/subscriber) | GoHighLevel | Sole system of record — never duplicated into Base44 |
| Authentication | Base44 built-in auth, admin role only | Gates the owner editor; the public site never requires any account |
| Deployment | Base44 | Build, preview, and publish |

## Major system flows

### Weekly ordering (no Base44 form — see D-008)
1. The visitor texts the GHL-owned business number directly from the "Text to order" link/display shown sitewide (`SiteSettings.sms_number`/`phone_display`) — no Base44 form, page JavaScript, or server-side function is involved at any point.
2. GHL's own Conversations/LC Phone system receives the text and auto-creates or updates a contact natively — this is default GHL phone-system behavior, not custom code.
3. Cathy reads and replies from the GHL Conversations inbox/app, not her personal Messages app.

### Catering inquiry
1. Visitor submits the native catering form in Base44 (name, phone, event date, guest count, notes).
2. A Base44 server-side function (`sendOrder` in `TFRS-Admin/cathy-kitchen-2`) validates input and rejects a filled honeypot.
3. The function upserts the GHL contact, adds a `CK - Website Catering Inquiry` tag via a non-destructive Add Tags call (never the upsert's own `tags` field, which would overwrite a repeat customer's existing tags), then creates one opportunity in `Catering → New Inquiry` with event date/guest count/notes as custom fields.
4. The browser receives only a success/error state — never a contact/opportunity ID or CRM data.
**Failure behavior:** if GHL isn't configured yet, or any step of the sequence above fails, the function falls back to emailing the owner (the same interim mechanism described in D-007) so the inquiry is never silently lost. The frontend sees the same success/error shape either way.

## Integrations

### GoHighLevel
- **Purpose:** Sole system of record for every customer interaction — both the GHL-owned order-texting number (contacts captured natively, no Base44 code) and the catering form (contacts, one opportunity per inquiry, tags).
- **Direction:** Outbound only from the one Base44 server-side function (`sendOrder`) that still calls GHL. No inbound GHL → Base44 calls. Weekly ordering never touches Base44 at all — it's a direct SMS to a GHL-owned number.
- **Data:** Contact name/phone (from either channel); event date/guest count/notes (catering only); source tag; the GHL-provisioned phone number itself.
- **Credentials:** Base44 server-side secrets only — never in frontend code, this repo, or any public content field. Secret names (values are never recorded anywhere): `GHL_PRIVATE_INTEGRATION_TOKEN`, `GHL_LOCATION_ID`, `GHL_CATERING_PIPELINE_ID`, `GHL_CATERING_STAGE_ID`, plus `GHL_FIELD_*`/`GHL_TAG_*` custom-field and tag identifiers. (The earlier `GHL_WEEKLY_ORDERS_*` secrets from this document's first draft are moot — there is no weekly-order pipeline or function; see D-008.)
- **Failure behavior:** Not transactional — a contact can be created while the opportunity call fails. The catering function never retries blindly; on any GHL failure it falls back to the pre-existing owner-notification email so the inquiry is never lost, and the browser sees the same success/error shape either way.
- **Status:** No GHL location, pipeline, or credential exists yet for this business in the connected workspace — none may be assumed from another client's account (D-006). The owner is creating the new location directly in the GHL UI (location/pipeline creation has no API) and will share back the location ID; `TFRS-Admin/cathy-kitchen-2`#20 implements the catering → GHL write path ready to activate once the resulting secrets are set, falling back to the owner-notification email (D-007) until then.

## Constraints
- No customer, order, or catering data may be intentionally stored in a Base44 data table (D-002).
- `.github/workflows/validate-template.yml` runs `markdown-link-check` on every `.md` file in this repo as a required check. Any live/volatile/private URL recorded in `docs/app/*.md` (a Base44 preview URL, an unregistered domain, a GHL link) must be written in backticks as plain text, never as a clickable markdown link, or it will fail CI.
- This repo does not sync with Base44's GitHub integration (D-001) — `main` here stays protected, PR-only.
- The site may not be published (`SiteSettings.content_status` set to `live`, or a public domain attached) while the catering form still depends on the interim owner-notification-email fallback described above — doing so would send real customer data through an unapproved channel. The real GHL catering write path must be activated (real secrets set) in `TFRS-Admin/cathy-kitchen-2` first (D-007). This no longer applies to weekly ordering — it was never a Base44 form to begin with (D-008).

## Diagram
Not needed — the two-repo, two-system split above is small enough to describe in prose.
