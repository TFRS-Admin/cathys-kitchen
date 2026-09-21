# Development Process

The goal is autonomous delivery without enterprise ceremony.

## Default flow

```text
approved input
    ↓
short-lived agent branch
    ↓
implementation in Base44 (via the Claude Code connector)
    ↓
local verification (where applicable)
    ↓
Base44 preview
    ↓
product/design verification
    ↓
pull request
    ↓
required automated checks
    ↓
auto-merge
    ↓
main
    ↓
Base44 deployment
```

## Branching

Use short-lived branches:

`agent/<short-purpose>`

Do not maintain long-lived `develop`, `staging`, `qa`, or release branches by default.

Merge within 1–3 days. A branch open longer than that is accumulating drift, not "still in progress" — land incrementally instead. During any longer-running change, sync with the target branch periodically rather than only at the end: catching a conflict early costs a small resolution; catching it after a full session of unmerged work costs a full reconciliation, and the size of that reconciliation grows with everything else that landed on the target branch in the meantime.

## Build

Implement the smallest complete version of the approved change directly in Base44. Intermediate commits may be messy; squash merge keeps permanent history clean.

## Verification

Run the checks that exist and matter to the change — typical examples are build, typecheck, lint, and meaningful tests, where the stack and Base44's own tooling make them available.

For any confirmed bug: reproduce it first (a failing check or a clearly described repro), then fix it, then confirm the specific failure is actually resolved. A bug fix without that confirmation isn't done.

Do not manufacture heavyweight test infrastructure just to satisfy process.

## Preview

Use Base44's preview URL for functional, visual, responsive, integration, and runtime verification before opening a PR.

## Agent QA

The implementation agent owns first-pass QA. Verify requested workflows, material edge states, relevant data behavior, permissions where applicable, design fidelity, and obvious adjacent regressions.

The owner is not the default code reviewer.

## Pull request

A PR is an automated integration boundary, not a request for routine human review. Keep it reviewable in principle even though no human review is required by default — a PR a person couldn't make sense of at a glance is a signal the change should have been split.

## Auto-merge

Enable auto-merge after the PR is ready. Default merge strategy is squash merge. Required checks must pass first. Delete the source branch after merge.

## Main

`main` represents accepted working software.

Never develop directly on `main`, force-push it, or bypass required checks for convenience.

## Deployment

Base44 is the deployment platform. There is no separate hosting/build platform to configure by default.

If a project genuinely requires a staging step ahead of Base44's production deployment, add it deliberately and record why in `docs/app/06-DECISIONS.md` rather than defaulting to one.

## Completion

The normal endpoint is:

`verified → checks passed → auto-merged → branch removed → deployed via Base44`
