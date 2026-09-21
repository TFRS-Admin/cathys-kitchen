# AGENTS.md

This repository is created first, then attached — together with the Base44 MCP connector — to a Claude Code session that builds and iterates the application it describes. Base44 is the exclusive build and deployment platform; GitHub is the history, collaboration, and integration layer for the repo Base44 backs. Do not introduce a second hosting/build platform without a concrete current requirement.

Your job is to turn approved product/design inputs into a working Base44 application, verify it, integrate it safely, and leave the repository in a clean state without routine owner intervention.

## Authority order

1. Owner's current explicit instruction
2. Approved build brief / design handoff
3. Project-specific docs under `docs/app/`
4. This file
5. `docs/DEVELOPMENT_PROCESS.md`
6. `docs/QUALITY_GATES.md`
7. Existing implementation

Do not silently override a higher-authority source.

## Operating rules

- Inspect before editing — read the existing app through the Base44 connector and this repo's docs before changing either.
- Build the smallest correct solution.
- Prefer Base44's native capabilities (entities, connectors, hosting, and built-in auth where applicable) over custom infrastructure.
- Do not add architecture for hypothetical future needs.
- Do not ask the owner to choose between ordinary coding approaches.
- Make reasonable, reversible implementation decisions autonomously.
- Do not redesign approved product behavior or visual direction.
- Do not refactor unrelated working code merely because you prefer another structure.
- Do not introduce new infrastructure, services, databases, queues, caches, auth systems, frameworks, or major dependencies without a concrete current requirement — Base44 already provides entities, hosting, and often auth.
- Never embed secrets or production-only values; use Base44's connector mechanism for third-party credentials wherever one exists, GitHub's own secret store otherwise.
- Never claim verification succeeded unless it was actually performed.
- Every confirmed bug gets a regression check before the fix counts as done — reproduce it first, then confirm the specific failure is actually resolved, not just that something changed.

## Required workflow

1. Read the task, relevant `docs/app/` files, and design handoff.
2. Inspect the existing app through the Base44 connector and the existing repo.
3. Create a short-lived `agent/<task>` branch.
4. Build the change in Base44 through the connector (entities, pages, logic, auth as needed).
5. Run relevant available checks (lint/type/build/test) where the platform and stack make them meaningful.
6. Verify the requested workflow and important edge states using Base44's preview.
7. Verify design fidelity when a design exists.
8. Fix discovered problems.
9. Open a PR and enable auto-merge.
10. Own the change until required checks pass and the PR merges.
11. Let the branch be deleted after merge.

Opening a PR is not completion.

Sync with the target branch periodically during any change that runs long — catching a conflict early costs a small resolution; catching it after a large, unmerged change costs a full reconciliation, and the size of that reconciliation grows with everything else that landed on the target branch in the meantime.

## Stop only when

- authoritative requirements genuinely contradict;
- required access, credentials, or connector permissions are unavailable;
- an unapproved destructive/irreversible action is required;
- a security/privacy consequence materially changes approved behavior;
- the requested behavior requires changing an explicit platform constraint.

Do not stop for ordinary implementation decisions.

## Done

A change is done when the requested behavior exists in Base44, relevant verification has passed or limitations are explicitly reported, approved design is matched where applicable, required CI passes, the PR merges or is blocked only by an external required check, no unnecessary architecture was introduced, and project docs remain accurate.
