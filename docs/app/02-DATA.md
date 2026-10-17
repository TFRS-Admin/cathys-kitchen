# Data

## Entities
All five are Base44 entities holding **public website content only**. None may hold customer, order, catering, or subscriber data (see `06-DECISIONS.md` D-002) — that data lives solely in GoHighLevel.

### SiteSettings
**Purpose:** The single source of truth for business facts the whole site reads from (business name, phone, pickup location/hours, cutoff, social links, map link). Exactly one record exists.

| Field | Meaning | Required | Constraints |
|---|---|---:|---|
| business_name | Public business name | Yes | Defaults to "Cathy's Kitchen" |
| tagline | Short hero/sub copy | No | |
| phone_display | Human-readable phone | No | |
| sms_number | Digits-only number for `sms:` links | No | |
| pickup_location_name | Pickup location's name | No | |
| pickup_address_line | Pickup address | No | |
| pickup_window_text | Pickup hours, e.g. "5:00–6:00 PM" | No | |
| order_cutoff_text | Same-day cutoff, e.g. "10:00 AM" | No | |
| map_url | Directions link | No | |
| facebook_url / instagram_url | Social links | No | |
| privacy_url / terms_url | Privacy/Terms page links | No | Must exist before the email signup form is public |
| announcement_banner_text | Optional short banner | No | |
| content_status | `placeholder` \| `live` | Yes | Site only treats facts as real once `live` |

**Relationships:** none — a flat settings record.
**Lifecycle:** Created once by the owner; updated in place. Never deleted while the site is live.

### MenuWeek
**Purpose:** One calendar week of the Monday–Friday menu.

| Field | Meaning | Required | Constraints |
|---|---|---|---|
| week_start_date | The week's Monday | Yes | ISO date |
| label | Display label | No | |
| status | `draft` \| `published` \| `archived` | Yes | Exactly one `published` week is shown publicly at a time |
| announcement_text | Optional short note | No | |

**Relationships:** has many MenuDay.
**Lifecycle:** Created via "Create Next Week" (with five blank MenuDay rows); edited while `draft`; shown publicly only once `published`; `archived` once superseded.

### MenuDay
**Purpose:** One weekday's dinner within a MenuWeek.

| Field | Meaning | Required | Constraints |
|---|---|---|---|
| menu_week_id | Owning MenuWeek | Yes | |
| day_of_week | mon/tue/wed/thu/fri | Yes | |
| serve_date | The actual calendar date | Yes | Stored explicitly, never derived from week_start_date + day_of_week, to avoid timezone/week-rollover bugs |
| dish_name | Dish name | Yes | |
| description | Short description / sides | No | |
| price_dinner_for_two / price_dinner_for_four | USD prices | No | A zero/unset price displays as "Price confirmed with order," never as $0 |
| availability | available \| limited \| sold_out \| not_cooking | Yes | Drives the day card's badge and whether its "Request this dinner" is enabled |
| photo_url | Optional dish photo | No | Real photos only — never AI-generated or stock |
| sort_order | Display order | No | |

**Relationships:** belongs to one MenuWeek.
**Lifecycle:** Created blank alongside its MenuWeek; edited by the owner; "Mark a Day Sold Out" toggles `availability`.

### FAQItem
**Purpose:** The six-or-so FAQ entries shown on the home page.

| Field | Meaning | Required | Constraints |
|---|---|---|---|
| question / answer | FAQ content | Yes | Answers must reflect owner-approved policy — never an allergen-free guarantee |
| sort_order | Display order | No | |
| is_published | Whether it's shown | Yes | Default true |

### Testimonial
**Purpose:** Owner-curated customer quotes. Not an open review system — there is no public submission form.

| Field | Meaning | Required | Constraints |
|---|---|---|---|
| quote | The testimonial text | Yes | Real, owner-approved quotes only |
| attribution | First name/initial only | No | Never a full name without consent |
| sort_order | Display order | No | |
| is_published | Whether it's shown | Yes | Default true |

## Access and visibility
| Data/action | Actor | Rule |
|---|---|---|
| Read any of the five entities | Anyone (public) | Public read — needed for an account-free site |
| Create/update/delete any of the five entities | Admin-role Base44 user only | Enforced via Base44 row-level security (`{"user_condition": {"role": "admin"}}`) — this is the entire "protected owner editor," no custom auth needed |
| Invite another admin | Existing admin-role user | Capped at a small number of admins (owner + one approved helper, per the brief) |

## External data
| Source | Data | Source of truth | Sync expectation |
|---|---|---|---|
| GoHighLevel | Contacts, weekly-order opportunities, catering opportunities, consent/tag data | GoHighLevel | One-way: Base44 functions write to GHL on form submission. Base44 never reads this data back and never stores a copy of it (D-002). |
