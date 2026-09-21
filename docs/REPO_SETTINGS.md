# Repository Settings

These are the intended GitHub defaults for repos created from this template.

## Repository defaults

| Setting | Default |
|---|---|
| Default branch | `main` |
| Issues | Off unless used |
| Projects | Off unless used |
| Wiki | Off |
| Discussions | Off |
| Auto-merge | On |
| Delete head branches | On |
| Squash merge | On |
| Merge commits | Off |
| Rebase merge | Off |

## `main` protection

- require a PR before merge;
- require 0 human approvals;
- require project verification checks;
- block force pushes;
- block deletion;
- use linear history where compatible;
- do not allow agents to bypass rules for convenience.

## Branch policy

Use:

`main`
`agent/<task>`

Do not create persistent `develop`, `staging`, `qa`, or environment branches unless there is a demonstrated need.

## Deployment

Base44 is the deployment platform for every repo created from this template. The GitHub repo is the history/collaboration layer; Base44 (via the Claude Code connector) is where the app is actually built, previewed, and deployed.

Do not add a separate hosting platform (Railway, Vercel, etc.) without a concrete current requirement and a recorded decision in `docs/app/06-DECISIONS.md`.

## Security

Enable platform-provided secret scanning, push protection, and dependency security alerts when available and applicable.

Never commit secrets. Use Base44's connector mechanism for third-party credentials, and GitHub's own secret store for anything that must live at the repo level.

## Applying these settings

Nothing in this repository configures GitHub's branch-protection or merge-strategy settings automatically — they're organization/repo-admin settings applied through GitHub itself (Settings → Branches / Settings → General) or the GitHub API, not files in this repo. Apply them once when a new repo is created from this template.
