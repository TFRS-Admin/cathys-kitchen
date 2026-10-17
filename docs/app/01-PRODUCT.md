# Product

## Purpose
Cathy's Kitchen is a weekly family-dinner order/pickup website for a home-kitchen business in the Kingwood/Forest Cove area (Houston, TX). It lets customers see Cathy's Monday–Friday dinner menu, submit a dinner request or catering inquiry, and opt into the weekly menu email — all without creating an account. Cathy personally confirms every request by text or email before anything is priced or paid. The business outcome is replacing ad hoc social-media/text ordering with one trusted, linkable destination that still feels like texting Cathy directly.

## Users
| User type | What they need to do |
|---|---|
| Visitor (no account) | Browse the weekly menu, submit a dinner request, a catering inquiry, or an email signup; get pickup directions; read the FAQ. |
| Owner / approved helper (admin) | Publish/unpublish the weekly menu, edit each day's dish/description/pricing/availability, create next week's menu, manage FAQ/testimonial content, invite one more admin. |

## Core workflows

### Weekly dinner request
1. Visitor browses the published Monday–Friday menu on the home page.
2. Visitor opens the request form — from the hero button, the menu section, or a specific day's "Request this dinner" (which preselects that dish) — and submits name, phone, requested day(s)/meals, and an explicit acknowledgment that this is a request, not a confirmed order.
3. The site never calculates a final price or takes payment. Cathy confirms availability and price afterward, by text or email.

**Failure/edge behavior:** If no menu week is published, the site shows "Next week's menu is being prepared. Text Cathy for today's availability." instead of an empty page.

### Catering inquiry
1. Visitor fills out the separate catering form (event date, guest count, notes) — never mixed into the weekly dinner request form.
2. Cathy follows up personally with the right next step. A submitted inquiry is not a confirmed booking.

### Weekly menu email signup
1. Visitor explicitly opts in (checkbox unchecked by default) to receive the weekly menu and ordering deadlines by email.
2. Visiting the site alone never enrolls anyone.

### Owner publishes the week
1. Owner creates next week's menu (five blank weekday slots via "Create Next Week"), fills in each day's dish/description/pricing/availability, and publishes it.
2. Only one published week is ever shown publicly; unpublishing falls back to the "menu is being prepared" state.

## Functional requirements
- **P-001:** The home page displays the current published week's Monday–Friday menu as real text (not only inside an image) — each day shows dish, description, two serving-size prices, and availability status.
- **P-002:** Every primary action (dinner request, catering inquiry, email signup, per-dish request) is reachable without creating an account.
- **P-003:** No page states or implies that a request is confirmed, priced, or paid before Cathy replies.
- **P-004:** The owner can create next week's menu, edit any day, mark a day sold out, and publish/unpublish the week, each in a few minutes, without needing to understand the underlying data model.
- **P-005:** If no week is published, the site shows the fallback message rather than an empty or broken menu section.
- **P-006:** The text-order fallback (`sms:` link to Cathy's number) always works even if the native forms or the GoHighLevel integration are unavailable.

## Business rules
- **BR-001:** No customer, order, catering, or subscriber data is stored in Base44 — it is validated, then sent to GoHighLevel, which is the sole system of record for that data.
- **BR-002:** No public payment button exists anywhere on the site; payment only happens after a human (Cathy) confirms availability and price.
- **BR-003:** The owner-only areas require an admin-role Base44 account; the public site never requires any account.
- **BR-004:** Real pickup address, phone, cutoff, and pricing facts are never guessed. An unresolved fact stays explicitly marked "not yet decided" rather than filled with a placeholder that could be mistaken for a real fact.

## Scope

### In scope
- A single scrolling public home page plus small Privacy and Terms pages.
- The weekly Monday–Friday menu, shown as real text with day/dish/description/pricing/availability.
- Native (non-redirect) forms for a weekly dinner request, a catering inquiry, and a weekly-menu email signup.
- An owner menu editor limited to four actions: Create Next Week, Edit Menu, Mark a Day Sold Out, Publish/Unpublish.
- A GoHighLevel integration that is the sole system of record for customer/order/catering/subscriber data (see `04-ARCHITECTURE.md` and `06-DECISIONS.md`).

### Explicitly out of scope
- Customer accounts, a customer dashboard, or a loyalty program.
- A shopping cart or any online payment collection.
- An AI chatbot, delivery routing, automatic inventory, live seat reservations, or automatic discounts.
- A large multi-page restaurant site.
- Redirecting customers to GoHighLevel-hosted forms — superseded by native Base44 forms per `06-DECISIONS.md` D-002.

## Acceptance
Transcribed from the approved build brief's acceptance tests. Each must be demonstrated, not assumed, before this is ready for Cathy's review:

- [ ] A valid weekly dinner request creates/updates exactly one GHL contact and one `Needs Review` opportunity.
- [ ] Repeating the same submission UUID does not create a second opportunity.
- [ ] An existing contact using the same mobile number is updated per GHL's duplicate-contact configuration; no unexpected duplicate is created.
- [ ] A valid catering inquiry creates exactly one contact and one `New Inquiry` opportunity in the catering pipeline.
- [ ] A valid email signup records the exact consent fields and the weekly-menu opt-in tag, without starting an SMS opt-in.
- [ ] An unchecked email-consent box prevents the newsletter signup from submitting.
- [ ] A filled honeypot field produces no GHL record.
- [ ] No submission response reveals any GHL contact, opportunity, pipeline, workflow, or payment data.
- [ ] Base44 page source, public configuration, and browser network calls contain no GHL token or other secret.
- [ ] The Base44 security scan is reviewed; no exposed-secret finding remains; public functions have narrow input/output behavior.
- [ ] No form submission sends an email, SMS, payment link, or invoice until GHL activation is separately approved.
- [ ] The full flow works at 375px mobile width, including the order form, catering form, error state, and success state.
