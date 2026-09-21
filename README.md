# TFRS-Admin Repo Template

The starting scaffold for every new TFRS-Admin repository, generated from the Very Good Software Co. Engineering OS (`TFRS-Admin/tfrs-engineering-playbook`, `project-template/`). See `docs/engineering-os-version.md` for the version this scaffold was cut from.

## How to Use This Template

1. Click **"Use this template"** on GitHub instead of creating a blank repo.
2. Fill in every `<placeholder>` in `AGENTS.md`, `docs/architecture/ARCHITECTURE.md`, and `docs/product/PRD.md` — an unfilled placeholder is worse than an honest "not yet decided."
3. Update `docs/engineering-os-version.md` if you're adopting a newer Engineering OS version than this template currently pins.
4. Run the Engineering OS's `migration/ADOPTION_CHECKLIST.md` against the result.
5. Add `.planning/` to `.gitignore` — it's session-local scratch space, not committed.
6. Create the first `Ready` work item under `docs/engineering/backlog/` and continue with `playbooks/BUILD_FEATURE.md` or whichever playbook the Decision Router names.

## Branch Model

This repository defaults new product repositories to **Pattern B (Trunk Plus Staging)** from the Engineering OS's `standards/GIT_STANDARD.md`, per `adrs/0007-trunk-plus-staging-branch-pattern.md`.

| Branch | Purpose |
|---|---|
| `main` | Trunk. Always deployable. Protected — merge only via reviewed pull request, never commit directly. Promotion from `staging` requires human approval by default. |
| `staging` | The one permitted persistent branch beyond `main`. Short-lived branches merge here first; this is where autonomous agent work and anything not yet ready for production lands and gets observed before promotion. |
| `feature/<scope>`, `fix/<scope>`, `chore/<scope>`, `docs/<scope>` | Short-lived. Merge into `staging` within 1–3 days per `standards/GIT_STANDARD.md` — this discipline applies to Pattern B exactly as it does to single-trunk. |

**Do not add a `develop` branch or further environment branches.** `staging` is the only permitted persistent branch beyond `main` — adding more reintroduces the divergence-and-drift failure GitFlow-style permanent-lane models are known for, which is exactly what this pattern is designed to avoid while still giving autonomous work a contained space. If this repository is simple enough not to need that (e.g. it ships no deployed artifact), delete `staging` and use Pattern A instead — state that deviation in this repository's own `AGENTS.md`.

When using this template on GitHub, check **"Include all branches"** so the new repository gets `staging` too; otherwise create it manually (`git checkout -b staging` from `main`, then `git push -u origin staging` — `-b` alone only creates it locally) as part of `migration/ADOPTION_CHECKLIST.md`.

## Engineering Standard

This project follows the Very Good Software Co. Engineering OS — `TFRS-Admin/tfrs-engineering-playbook`. See `docs/engineering-os-version.md` for the adopted version and this repository's own `AGENTS.md` for project-specific commands, boundaries, and any local deviations.
