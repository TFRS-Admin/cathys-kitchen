# Architecture

## Summary
Base44 hosts the entire public website, the one remaining native form (catering inquiry), and the owner-only menu editor — full stop. There is no native weekly-order form and no email-signup form (the design system that shipped after this document was first written doesn't support either, and weekly ordering is instead a plain "Text to order" `sms:` link to Cathy's own personal number). There is no GoHighLevel (or any other CRM) integration: the owner decided to drop that entirely (`06-DECISIONS.md` D-009, which supersedes D-002/D-006/D-007/D-008's GHL content). The catering form emails the owner directly; that is the permanent mechanism, not a stand-in for something else. No customer data is stored in a Base44 entity — Base44 entities hold public website content only (menu, FAQs, photos, site settings); catering leads exist only in the owner's email inbox.

The actual Base44 app source for this build lives in a separate GitHub repo, `TFRS-Admin/cathy-kitchen-2` — a Base44 app scaffold that was rebranded from a different, unrelated business for this purpose. This repo (`cathys-kitchen`) holds only the durable spec/decision record; see D-001.

## Platform boundaries
| Concern | Owner/choice | Responsibility boundary |
|---|---|---|
| Application | Base44 (`TFRS-Admin/cathy-kitchen-2`) | Public site, the native catering form, owner menu editor |
| Data (public content) | Base44 entities — `SiteSettings`, `MenuWeek`, `MenuDay`, `FAQItem`, `Testimonial` | Menu/FAQ/testimonial/site-settings content only |
| Data (catering leads) | The owner's email inbox (via Base44's `SendEmail` integration) | Sole record of a catering inquiry — nothing is stored in Base44 or any CRM |
| Authentication | Base44 built-in auth, admin role only | Gates the owner editor; the public site never requires any account |
| Deployment | Base44 | Build, preview, and publish |

## Major system flows

### Weekly ordering (no Base44 form)
1. The visitor texts Cathy's own personal number directly from the "Text to order" link/display shown sitewide (`SiteSettings.sms_number`/`phone_display`) — no Base44 form, page JavaScript, or server-side function is involved at any point.
2. Cathy reads and replies from her own phone's Messages app, same as before any of this was built.

### Catering inquiry
1. Visitor submits the native catering form in Base44 (name, phone, event date, guest count, notes).
2. A Base44 server-side function (`sendOrder` in `TFRS-Admin/cathy-kitchen-2`) validates input, rejects a filled honeypot, and emails the owner the inquiry details.
3. The browser receives only a success/error state.
**Failure behavior:** if the email send itself fails (e.g. `OWNER_NOTIFICATION_EMAIL` isn't configured), the function returns an error and the visitor is told to text Cathy directly instead.

## Integrations
None. This project does not integrate with GoHighLevel or any other CRM (`06-DECISIONS.md` D-009) — that was explored and then explicitly dropped by the owner. If GHL secret names (`GHL_PRIVATE_INTEGRATION_TOKEN`, etc.) ever turn up referenced anywhere in `TFRS-Admin/cathy-kitchen-2`, they're dead/historical, not something to configure.

## Constraints
- No customer, order, or catering data may be intentionally stored in a Base44 data table.
- `.github/workflows/validate-template.yml` runs `markdown-link-check` on every `.md` file in this repo as a required check. Any live/volatile/private URL recorded in `docs/app/*.md` (a Base44 preview URL, an unregistered domain) must be written in backticks as plain text, never as a clickable markdown link, or it will fail CI.
- This repo does not sync with Base44's GitHub integration (D-001) — `main` here stays protected, PR-only.

## Diagram
Not needed — the two-repo, two-system split above is small enough to describe in prose.
