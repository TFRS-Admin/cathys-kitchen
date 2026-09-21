# Architecture

> AI TEMPLATE INSTRUCTION: Explain only what is needed to make safe implementation decisions. Do not document folders, components, functions, endpoints, or libraries that can be discovered from the repo or from Base44 itself.

## Summary
[AI: Briefly explain where the app runs, where data lives, major platform responsibilities, and external dependencies. Default answer for Application/Data/Deployment is Base44 unless a recorded decision says otherwise.]

## Platform boundaries
| Concern | Owner/choice | Responsibility boundary |
|---|---|---|
| Application | Base44 | [responsibility] |
| Data | Base44 entities | [responsibility] |
| Authentication | Base44 (unless a connector/custom flow is required) | [responsibility] |
| Deployment | Base44 | [responsibility] |

[AI: Prefer Base44's native capabilities. Do not introduce separate infrastructure when Base44 adequately solves the current requirement — a deviation from the defaults above should be a recorded decision in `06-DECISIONS.md`, not a silent choice.]

## Major system flows

### [Flow]
1. [source]
2. [processing/boundary]
3. [destination]
4. [failure/retry behavior only if material]

[AI: Include only flows crossing meaningful system boundaries.]

## Integrations

### [Integration]
- **Purpose:** [why]
- **Direction:** [inbound/outbound/both]
- **Data:** [business-level description]
- **Credentials:** [managed via a Base44 connector where one exists; never include secrets here]
- **Failure behavior:** [if material]

## Constraints
- [Real technical/platform/security/cost constraint.]

[AI: Do not turn preferences into constraints.]

## Diagram
[AI: Add a small PlantUML component diagram only if the system boundaries are genuinely hard to understand. Otherwise delete this section.]
