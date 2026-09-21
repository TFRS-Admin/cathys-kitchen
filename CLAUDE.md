<!-- Purpose: Claude Code entry point for this project. -->
# Claude Code Instructions — <project-name>

Read `AGENTS.md` (this repository's local contract) first, then the Very Good Software Co. Engineering OS's `AGENTS.md` and `agents/AGENT_OPERATING_MODEL.md` for the universal contract and session loop.

## Session Style

- Be concise, evidence-driven, and implementation-oriented.
- For multi-step work, maintain `.planning/task_plan.md`, `.planning/findings.md`, and `.planning/progress.md`.
- Read narrowly — load only the documents and source files required for the current step.
- Ask only when a material decision cannot be safely inferred; otherwise state assumptions and proceed.

## Before Editing

Run the Engineering OS's Decision Router. Confirm the work item in `docs/engineering/backlog/` is `Ready`, or create the required planning artifact per `standards/WORK_ITEM_STANDARD.md`. Identify this project's local test and build commands from `AGENTS.md` above.

## Before Completion

Run deterministic verification per `playbooks/VERIFY.md`. Summarize changed files, user-visible behavior, evidence, risks, and follow-up work.
