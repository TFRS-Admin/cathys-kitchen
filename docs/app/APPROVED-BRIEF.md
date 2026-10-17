# Approved Build Brief — Cathy's Kitchen Weekly Dinner Hub

> This is the owner-approved build brief referenced as the design and product authority throughout `docs/app/*.md` (see `03-DESIGN.md`, `01-PRODUCT.md`, `06-DECISIONS.md` D-002/D-005). It is captured here verbatim because it exists only in the owner's conversation with the implementing agent, not in any external document — without this file, "the approved brief" would not be an inspectable, verifiable authority for a future agent. This is the final, current version: it supersedes any earlier draft of the brief, specifically by replacing the original "link to GHL-hosted forms" recommendation with the native-Base44-forms/GHL-system-of-record architecture below (`06-DECISIONS.md` D-002).

## Architecture decision

**Base44 is the full customer-facing website and form experience. GoHighLevel is the single operational system of record.**

Base44 displays the weekly menu and collects customer requests through native website forms. Each form submission calls a Base44 server-side function, which securely creates or updates the contact and creates the appropriate operational record in GoHighLevel. GHL then owns the customer relationship, pipelines, workflows, payments, SMS, email, and reporting.

Base44 is the front door. GHL is the back office. No customer should have to leave the Cathy's Kitchen website to submit an order, a catering inquiry, or an email opt-in.

## Non-negotiable system boundary

| System | Owns | Must not own |
|---|---|---|
| **Base44** | Public website; menu display; native customer forms; a protected owner menu editor; form validation; user-friendly submission confirmation. | Customer CRM records; order fulfillment queue; payment status; email/SMS audiences; marketing automation; customer conversation history; payment credentials; GHL API credentials. |
| **GoHighLevel** | Contacts; order-request opportunities; catering opportunities; consent fields; tags; workflows; internal tasks; payments/invoices; conversations; SMS/email; reports; operational history. | Public website layout and customer-facing browsing experience. |

**Data rule:** No customer or order data may be intentionally stored in a Base44 data table. A form may hold a visitor's input in the browser only long enough to submit it to the server function. The Base44 function sends the validated data to GHL and returns a simple success/failure response. Do not create `Orders`, `Leads`, `Subscribers`, `Catering Requests`, or `Contact Submissions` entities in Base44. Base44 may store public website content only (the currently displayed menu, food photos, FAQs, site settings) — none of which has customer PII.

## Business model

Cathy's Kitchen is a weekly, made-from-scratch family-dinner program — not a conventional walk-in restaurant or a self-service food-delivery app.

- Cathy posts a Monday–Friday menu.
- Customers may order ahead for the week or, when available, place a same-day request by 10:00 AM.
- Meals are collected hot and ready between 5:00–6:00 PM.
- Standard public pickup location: The Cove on Hamblen, 1101 Hamblen Road.
- Customers have historically ordered by text at (713) 725-6963.
- Public menu examples show `Dinner for 2 — $28` and `Dinner for 4 — $56`, but the site must treat price and availability as menu-week data, not permanent guarantees.
- Catering is a separate line of business and can affect a weekly dinner day.

## The website to build in Base44

Mobile-first, single-page public website with small Privacy and Terms pages. Warm "Sunday supper invitation" feel, not a chain restaurant or food-delivery marketplace.

**Header:** Brand "Cathy's Kitchen"; anchors This Week's Menu / How It Works / Catering / Questions; persistent mobile CTA "Request This Week's Dinners."

**Hero:** Headline "Made-from-scratch dinners for your week." Supporting line "Choose your family dinners, Cathy confirms your order, then pick up hot and ready to eat." Authentic owner-approved food photography only — never AI-generated or stock.

**This Week's Menu:** Monday–Friday as readable HTML cards (never only a menu image), each with day/date, dish name, description/sides, serving-size choices and prices, availability (Available/Limited/Sold Out/Not Cooking Today), and a "Request this dinner" control that preselects that meal in the native order form. Below: "Order ahead for the week, or order by 10:00 AM for today when available. Meals are picked up hot from 5:00–6:00 PM at The Cove on Hamblen."

**How It Works:** 1. Pick your dinners. 2. Cathy confirms. 3. Pick up hot. Plus: "A request is not confirmed until Cathy replies."

**Pickup:** Title "Pickup at The Cove on Hamblen." Copy: "Weekly dinner pickup is at The Cove on Hamblen, 1101 Hamblen Road, from 5:00–6:00 PM." "Get Pickup Directions" button. Never claim Cathy owns The Cove or manages its alcohol service.

**Catering:** Headline "Made-from-scratch catering for your next gathering." Body: "Tell Cathy about your event, guest count, date, and what you have in mind. She will follow up personally with the right next step." Button: "Ask About Catering" → native catering form.

**Weekly menu signup:** Headline "Get the menu before it sells out." Body: "Join Cathy's weekly dinner list for the newest menu, pickup details, and ordering deadline." Button: "Email Me This Week's Menu" → native email signup form.

**Text-order fallback:** "Text Cathy to Order" → `sms:7137256963` with prefilled message: "Hi Cathy! I would like to order from this week's menu. Name: Pickup day(s): Dinner for 2 or 4: Any notes:" — this does not replace the native forms; it respects existing customer behavior while the structured form becomes the preferred option.

**Social/footer:** Facebook `https://www.facebook.com/cathyssuperyummy/`; Instagram `https://www.instagram.com/cathyskitchenkingwood/`; Privacy and Terms links. Do not show the publicly listed iCloud email unless Cathy explicitly confirms it as a public contact channel.

## Visual direction

| Element | Direction |
|---|---|
| Mood | Warm, local, handmade, calm, polished — a handwritten recipe card meets a well-run neighborhood supper club. |
| Color | Cream/linen; deep blackberry/burgundy primary; sage/olive secondary; marigold highlights; charcoal copy. No purple gradients or neon. |
| Type | Editorial serif display (Fraunces or equivalent) for headlines; warm readable sans serif for body. |
| Photography | Authentic Cathy's Kitchen food photos only, owner-selected. |
| Layout | Spacious, mobile-first, menu-led. Subtle paper texture, understated rules, refined shadows over distracting animation. |
| Accessibility | 16px minimum body copy; clear labels; high contrast; visible focus styles; semantic headings; keyboard-friendly controls; meaningful alt text. |

## Base44 menu content and owner editor

Authenticated, owner-only menu editor; public visitors never need an account. Public-content items: Site Settings, Menu Week, Menu Day, FAQ, Testimonial (full field lists in `02-DATA.md`). Owner-facing tasks: Create Next Week, Edit Menu, Mark a Day Sold Out, Publish/Unpublish. Fallback when no week is published: "Next week's menu is being prepared. Text Cathy for today's availability."

## Native Base44 forms

**A. Weekly dinner request** — first/last name, mobile (required), email (optional), requested day(s), requested meal(s)/portions/quantities, notes, required acknowledgment this is a request not a confirmed order, optional separate email and SMS marketing consent (both default unchecked), hidden submission UUID, hidden honeypot. Success message: "Thanks—your dinner request has been sent to Cathy. She will confirm availability and payment details by text or email. Your request is not confirmed until she replies."

**B. Catering inquiry** — first/last name, mobile, email (both required), event date, event type, guest count, optional venue, optional budget range, optional notes, preferred contact method, optional email consent, hidden submission UUID, hidden honeypot. Success message: "Thanks—Cathy has your event details and will follow up personally. A submitted inquiry is not a confirmed catering booking."

**C. Weekly menu email signup** — first name, email (required), mobile (optional), required unchecked email-consent checkbox: "Yes, email me Cathy's Kitchen's weekly menu, pickup details, and ordering reminders. I can unsubscribe at any time." Hidden honeypot. Email consent only — never SMS consent.

## Base44 → GHL API contract

Request path: `Base44 form → frontend validation → Base44 backend function → GHL API (server-side secret) → contact/opportunity/tag → minimal success/error response to browser.` Never call GHL from page JavaScript; never let a token/location/pipeline/stage/field ID reach the browser, query strings, or Base44 public content fields.

Functions: `submit-weekly-dinner-request`, `submit-catering-inquiry`, `subscribe-weekly-menu` (public-callable, narrow, input-validated, no CRM read access). `publish-menu-to-ghl` is explicitly phase-two/owner-only, out of scope for v1.

Secrets (names only, never values, anywhere in this repo): `GHL_PRIVATE_INTEGRATION_TOKEN`, `GHL_LOCATION_ID`, `GHL_WEEKLY_ORDERS_PIPELINE_ID`, `GHL_WEEKLY_ORDERS_NEEDS_REVIEW_STAGE_ID`, `GHL_CATERING_PIPELINE_ID`, `GHL_CATERING_NEW_INQUIRY_STAGE_ID`, `GHL_FIELD_*`, `GHL_TAG_*`.

Order-request sequence: reject malformed input/filled honeypot/missing acknowledgment before calling GHL → normalize phone to E.164 → GHL Contact Upsert (source "Cathy's Kitchen Website," without clobbering existing tags) → add only the relevant source tag → search the pipeline for an existing opportunity with the same submission UUID (duplicate/retry guard) → if none, create one open opportunity in `Weekly Dinner Orders → Needs Review` with meal summary/pickup dates/notes/source/submission UUID/timestamp as custom fields → return only a success state and opaque submission reference. No email/SMS/payment is ever sent from the function; a GHL workflow that would do so activates only after internal testing and explicit written approval. Catering follows the same shape into `Catering → New Inquiry`. Email signup follows the same shape, recording consent timestamp/source/version and the weekly-menu opt-in tag.

Partial-failure policy (GHL is not transactional):

| Situation | Function behavior | Customer sees |
|---|---|---|
| Contact + opportunity succeed | Return success | Normal thank-you message |
| Contact succeeds, opportunity fails | No blind retry; log non-sensitive diagnostics; flag for manual recovery | "We couldn't finish saving that request. Please text Cathy at (713) 725-6963." |
| GHL rejects input | Field-level correction only, no GHL schema details | Highlighted field to fix |
| Rate limit/transient failure | No new browser submission created | "Please try again shortly or text Cathy." |
| Duplicate/retry, same submission UUID | Checked before create | Normal thank-you message, no second record |

## GHL configuration required before connecting Base44

Contact/opportunity custom fields; tags (`CK - Website Order Request`, `CK - Website Catering Inquiry`, `CK - Weekly Menu Email Opt-In`); pipelines/stages (`Weekly Dinner Orders → Needs Review`, `Catering → New Inquiry`); workflows/email/SMS/payments configured but disabled/test-only for v1 — no automated customer-facing send or charge is wired to a live submission without separate, explicit activation approval. Nothing here may be assumed from another client's GHL account — the target location's ID/name/address must be verified with the owner first.

## Acceptance tests before the site is published

Run with a dedicated non-customer test contact. Do not test with a real customer or a real payment.

1. A valid weekly dinner request creates/updates exactly one GHL contact and one `Needs Review` opportunity.
2. Repeating the same submission UUID does not create a second opportunity.
3. An existing contact using the same mobile number is updated per GHL's duplicate-contact configuration; no unexpected duplicate.
4. A valid catering inquiry creates exactly one contact and one `New Inquiry` opportunity.
5. A valid email signup records consent fields and the weekly-menu tag, without starting an SMS opt-in.
6. An unchecked email-consent box prevents the newsletter signup from submitting.
7. A filled honeypot produces no GHL record.
8. No submission response reveals any GHL contact/opportunity/pipeline/workflow/payment data.
9. Base44 page source, public configuration, and browser network calls contain no GHL token or secret.
10. The Base44 security scan is reviewed; no exposed-secret finding; public functions have narrow input/output behavior.
11. No form submission sends an email, SMS, payment link, or invoice until GHL activation is separately approved.
12. The full flow works at 375px mobile width, including both forms' error and success states.

## What still needs to exist before the integration can be connected

The verified Cathy's Kitchen GHL location/token/pipeline/stage/field IDs; owner-approved consent language and email/SMS policy; the payment/refund process; owner/venue approval of public pickup wording; approved photos/logo.
