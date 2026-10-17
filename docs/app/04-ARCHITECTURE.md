# Architecture

## Summary
Base44 hosts the entire public website, the native weekly-dinner-request/catering/email-signup forms, and the owner-only menu editor. GoHighLevel (GHL) is the sole system of record for all customer, order, catering, and subscriber data — nothing customer-identifying is ever stored in a Base44 entity (see `06-DECISIONS.md` D-002). Base44 server-side functions are the only code allowed to call the GHL API; GHL credentials live only in Base44's secret store, never in frontend code or this repo.

The actual Base44 app source for this build lives in a separate GitHub repo, `TFRS-Admin/cathy-kitchen-2` — a Base44 app scaffold that was rebranded from a different, unrelated business for this purpose. This repo (`cathys-kitchen`) holds only the durable spec/decision record; see D-001.

## Platform boundaries
| Concern | Owner/choice | Responsibility boundary |
|---|---|---|
| Application | Base44 (`TFRS-Admin/cathy-kitchen-2`) | Public site, native forms, owner menu editor |
| Data (public content) | Base44 entities — `SiteSettings`, `MenuWeek`, `MenuDay`, `FAQItem`, `Testimonial` | Menu/FAQ/testimonial/site-settings content only |
| Data (customer/order/catering/subscriber) | GoHighLevel | Sole system of record — never duplicated into Base44 |
| Authentication | Base44 built-in auth, admin role only | Gates the owner editor; the public site never requires any account |
| Deployment | Base44 | Build, preview, and publish |

## Major system flows

### Weekly dinner request
1. Visitor submits the native request form in Base44 (name, phone, requested day(s)/meals, acknowledgment).
2. A Base44 server-side function validates input, rejects a filled honeypot, and normalizes the phone number.
3. The function upserts the GHL contact, adds a source tag, and creates (or recovers, via a submission-UUID duplicate guard) one opportunity in `Weekly Dinner Orders → Needs Review`.
4. The browser receives only a success/error state — never a contact/opportunity ID or CRM data.
**Failure behavior:** if the contact succeeds but the opportunity call fails, the function does not retry blindly; the visitor sees a fallback asking them to text Cathy.

### Catering inquiry
Same shape as above, targeting `Catering → New Inquiry`.

### Weekly menu email signup
Same shape as above; records consent timestamp/source/version and the weekly-menu opt-in tag. Base44 never sends the newsletter itself — GHL owns that.

## Integrations

### GoHighLevel
- **Purpose:** Sole system of record for every customer interaction (contacts, opportunities, consent, tags, pipelines, payments, SMS/email, reporting).
- **Direction:** Outbound only from Base44 server-side functions in v1. No inbound GHL → Base44 calls.
- **Data:** Contact name/phone/email; requested day(s)/meals or event details; consent flags; source tag; submission id/timestamp.
- **Credentials:** Base44 server-side secrets only — never in frontend code, this repo, or any public content field. Secret names (values are never recorded anywhere): `GHL_PRIVATE_INTEGRATION_TOKEN`, `GHL_LOCATION_ID`, `GHL_WEEKLY_ORDERS_PIPELINE_ID`, `GHL_WEEKLY_ORDERS_NEEDS_REVIEW_STAGE_ID`, `GHL_CATERING_PIPELINE_ID`, `GHL_CATERING_NEW_INQUIRY_STAGE_ID`, plus `GHL_FIELD_*`/`GHL_TAG_*` custom-field and tag identifiers.
- **Failure behavior:** Not transactional — a contact can be created while the opportunity call fails. The function never retries blindly; the customer always gets a working fallback (text Cathy), and the owner can recover the contact manually in GHL.
- **Status:** Not yet connected. No GHL location, pipeline, or credential exists yet for this business in the connected workspace — none may be assumed from another client's account. The weekly-order and catering forms currently use a interim, GHL-free fallback (an owner-notification email) until this integration is built; see the implementation plan.

## Constraints
- No customer, order, catering, or subscriber data may be intentionally stored in a Base44 data table (D-002).
- `.github/workflows/validate-template.yml` runs `markdown-link-check` on every `.md` file in this repo as a required check. Any live/volatile/private URL recorded in `docs/app/*.md` (a Base44 preview URL, an unregistered domain, a GHL link) must be written in backticks as plain text, never as a clickable markdown link, or it will fail CI.
- This repo does not sync with Base44's GitHub integration (D-001) — `main` here stays protected, PR-only.

## Diagram
Not needed — the two-repo, two-system split above is small enough to describe in prose.
