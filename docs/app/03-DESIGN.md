# Design

## Approved design inputs
**Design package:** None exists separately — no Figma/wireframe/mockup was ever supplied.
**Approved build brief/design:** `docs/app/APPROVED-BRIEF.md` — the Cathy's Kitchen build brief, captured verbatim in this repo (it otherwise exists only in the owner's conversation with the implementing agent, which is not an inspectable or verifiable source for a future agent). It includes the owner's architecture override to native Base44 forms over GHL-hosted redirects (see `06-DECISIONS.md` D-002). Its "Visual direction" table is the canonical token source.

## Implementation rule
The approved design is a specification, not production code. Preserve its visual language, hierarchy, states, responsive behavior, and content intent while implementing in Base44/React. Do not redesign the approved UX because another pattern is preferred.

## Design-system usage
Canonical tokens, taken directly from the brief's visual-direction table:
- **Mood:** warm, confident, local, homemade, polished — a handwritten recipe card meets a well-run neighborhood supper club.
- **Color:** cream/linen background; deep blackberry/burgundy primary action color; sage/olive supporting tone; marigold for small highlights; charcoal text. No neon, no purple gradients.
- **Typography:** an editorial serif display face (Fraunces or closest equivalent) for headlines; a warm, readable sans serif for body copy. Avoid Arial/Inter/sterile startup typography.
- **Photography:** Cathy's real food/kitchen photos only. Never AI-generated or stock imagery that could misrepresent the menu.
- **Layout:** spacious, mobile-first; the weekly menu cards are the visual star. Subtle paper/linen texture and refined shadows over heavy animation.

## Project-specific UX rules
- Single scrolling home page plus small Privacy and Terms pages — no large navigation menu.
- A strong, sticky mobile CTA ("Request This Week's Dinners") stays visible at all times.
- Per-dish "Request this dinner" preselects that dish inside the one native weekly-order form rather than opening a separate form (`06-DECISIONS.md` D-004).
- A price field that is zero/unset always displays as "Price confirmed with order," never as `$0` or blank.
- Menu details are real HTML text — never only inside an image.

## Required states
| State | Expected behavior |
|---|---|
| Loading | Menu/FAQ/testimonial sections show a simple spinner — never a blank flash that could be read as "no menu." |
| Empty | No published `MenuWeek` → "Next week's menu is being prepared. Text Cathy for today's availability." |
| Error | A failed form submission shows a visible error state with a "text Cathy instead" fallback — never a silent failure or a fake success message. |
| Success | A submitted request shows a thank-you state that explicitly says the request is not yet confirmed. |
| Restricted | A signed-in non-admin visiting the owner editor sees "Admin access only," never a blank or broken page. |

## Conflicts
None recorded yet. If design sources ever conflict, follow: (1) the owner's explicit current instruction, (2) the approved build brief, (3) this file. Record any consequential resolution in `06-DECISIONS.md`.
