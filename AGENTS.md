<!-- Purpose: Project-local agent contract. Unlike the Engineering OS's own AGENTS.md, this file is project-specific -- state what's true about THIS repository, not universal rules the OS already covers. -->
# AGENTS.md — <project-name>

This repository follows the Very Good Software Co. Engineering OS (`<path-or-URL-to-the-Engineering-OS-repository>`), version `<recorded version, from Engineering OS VERSION.md>`. That repository defines the universal agent contract, standards, agent roles, and playbooks — this file states what's true about *this* project specifically. Read the Engineering OS `AGENTS.md` and `agents/AGENT_OPERATING_MODEL.md` first; this file adds to it and may state a stricter local rule, which wins on conflict.

## Commands

```bash
<install command>
<lint command>
<typecheck command>
<test command>
<build command>
```

## Branch Pattern

Pattern B (Trunk Plus Staging), per the Engineering OS's `standards/GIT_STANDARD.md` and `adrs/0007-trunk-plus-staging-branch-pattern.md` — this is what `repo-template` defaults new repositories to, not a placeholder to fill in. Change to Pattern A here only if this project is simple enough not to need `staging` (state that as a Local Deviation below if so).

## Architecture Boundaries

<Name the layers/modules an agent must not cross without explicit approval -- e.g. "domain logic never imports a framework adapter directly.">

## Prohibited Operations

<Anything specific to this repository beyond the Engineering OS's own prohibited-behavior list -- e.g. "never modify the payments module without a human review," "never run destructive migrations against the staging database directly.">

## Local Deviations from the Engineering OS

<State explicitly, or write "None currently." A silent deviation is a bug; a stated one is a decision.>
