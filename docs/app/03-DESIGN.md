# Design

> AI TEMPLATE INSTRUCTION: Point to the approved design authority. Do not duplicate a supplied design system.

## Approved design inputs
**Design package:** [path/identifier]
**Approved build brief/design:** [path/prompt/artifact]

## Implementation rule
The approved design is a specification, not necessarily production code. Preserve its visual language, hierarchy, states, responsive behavior, components, content intent, and interaction patterns while implementing appropriately in Base44.

Do not redesign approved UX because another pattern is preferred.

## Design-system usage
[AI: Record only what an implementation agent needs to correctly consume the package: canonical token source, assets, component guidance, responsive rules, etc. Reference source files instead of copying them.]

## Project-specific UX rules
- [Only rules not already authoritative in the design package.]

## Required states
[AI: Define applicable non-happy-path states. Delete irrelevant rows.]

| State | Expected behavior |
|---|---|
| Loading | [expectation] |
| Empty | [expectation] |
| Error | [expectation] |
| Success | [expectation] |
| Restricted | [expectation] |

## Conflicts
If design sources conflict, follow:
1. explicit approved build brief;
2. most specific project-level design instruction;
3. the design-system source identified as canonical.

Do not silently combine contradictory rules. Record consequential resolutions in `06-DECISIONS.md`.
