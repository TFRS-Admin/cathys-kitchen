# Dev Doc Suite

This folder is durable project context for AI implementation agents.

## Rule
Document decisions and behavior, not code. Include enough information to build and maintain the app correctly without rediscovering product decisions. Do not document implementation details that can be safely discovered from the repository or from Base44 itself.

Never add infrastructure, abstractions, services, extensibility, or documentation for hypothetical future needs.

## Authority order
When sources conflict:
1. Owner's current explicit instruction
2. Approved build brief
3. `01-PRODUCT.md`
4. `02-DATA.md`
5. Approved design package + `03-DESIGN.md`
6. `04-ARCHITECTURE.md`
7. `05-BUILD-RULES.md`
8. Existing implementation

Never silently resolve a genuine contradiction between higher-authority sources.

## Maintenance
Keep each fact in one authoritative place. Reference rather than duplicate. Update docs only when their authoritative facts change. Do not create additional docs by default.
