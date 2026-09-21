# TFRS-Admin Repo Template

The starting repository for every new TFRS-Admin project. Create the repo from this template first, then attach it — together with the Base44 MCP connector — to a Claude Code session to build the application.

## How to use this template

1. Click **"Use this template"** on GitHub instead of creating a blank repo.
2. Fill in the bracketed placeholders in `docs/app/01-PRODUCT.md` with the real project's facts, and in `02-DATA.md` / `03-DESIGN.md` / `04-ARCHITECTURE.md` as those become known — an unfilled placeholder is worse than an honest "not yet decided."
3. Attach this repo and the Base44 connector to a Claude Code session and say what you want built. `AGENTS.md` and `docs/DEVELOPMENT_PROCESS.md` define how the session operates from there.

## Start here

Implementation agents must read:

1. `AGENTS.md`
2. `docs/DEVELOPMENT_PROCESS.md`
3. `docs/QUALITY_GATES.md`
4. applicable project-specific files under `docs/app/`

`CLAUDE.md` is the Claude Code entrypoint and intentionally delegates to `AGENTS.md`.

## Project documentation

Application-specific product, data, design, and architecture docs live under `docs/app/`. Those describe the app. The root and `docs/` files describe how work is performed in every repo created from this template.

## Development model

Base44 connector build → short-lived branch → verification → PR → automated checks → auto-merge → `main`. Base44 is the deployment platform — see `docs/REPO_SETTINGS.md`.

`main` is canonical stable software, not a development workspace.
