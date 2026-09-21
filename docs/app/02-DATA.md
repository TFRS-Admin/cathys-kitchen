# Data

> AI TEMPLATE INSTRUCTION: Describe the business data model, not ORM/code structure. Do not invent fields for future use. Model this as Base44 entities unless a concrete requirement rules that out.

## Entities

### [Entity]
**Purpose:** [What it represents.]

| Field | Meaning | Required | Constraints |
|---|---|---:|---|
| [field] | [meaning] | Yes/No | [validation/default] |

**Relationships**
- [business relationship]

**Lifecycle**
- [Only meaningful creation/state/archive/delete behavior.]

## State rules
| Object | State | Can become | Trigger/rule |
|---|---|---|---|
| [entity] | [state] | [state] | [rule] |

[AI: Delete if state transitions are not meaningful.]

## Access and visibility
| Data/action | Actor | Rule |
|---|---|---|
| [data/action] | [actor] | [rule] |

[AI: Use Base44's own auth/entity-permission model unless the project explicitly requires custom authorization. Do not design custom auth unless required.]

## External data
| Source | Data | Source of truth | Sync expectation |
|---|---|---|---|
| [system] | [data] | [system/app] | [expectation] |

[AI: Prefer a Base44 connector for third-party data sources where one exists. Technical integration mechanics belong in Architecture.]
