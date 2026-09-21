# Quality Gates

Quality gates protect `main` without routine human code review.

Keep them small, reliable, and appropriate to the project.

## Principle

A check should exist because it catches a meaningful class of failure, not because mature engineering organizations commonly have it.

## Typical pre-merge gates

| Gate | Purpose |
|---|---|
| Build | Proves the app can produce a deployable build |
| Typecheck | Catches typed-code errors when applicable |
| Lint | Catches configured static-code errors |
| Tests | Runs meaningful automated tests when the project has them |

A project does not need every gate if its platform or stack makes one irrelevant.

## Runtime verification

CI is not enough for UI apps. Verify the running Base44 preview against the requested workflow, project requirements, approved design, responsive behavior, and relevant loading/empty/error states.

## Security defaults

- Never commit secrets, API keys, or production credentials. Use Base44's connector mechanism for third-party credentials wherever one exists.
- Never log or return sensitive data (passwords, tokens, full personal data) — allowlist what's safe to log or return, don't log everything and redact after the fact.
- Every entity action or endpoint that returns or changes data checks authorization, not just that a user is signed in.
- Treat any input from a user, an external service, or an LLM response as untrusted — validate it, don't execute or trust it directly.
- If a secret is ever committed, treat it as compromised the moment it touches a commit: rotate it first, then clean history — deleting the line alone is not sufficient.

## Merge policy

Protected `main` should normally:
- require a pull request;
- require 0 human approvals;
- require the project's verification status checks;
- block force pushes;
- block deletion;
- use linear history where compatible.

Auto-merge should be enabled. Squash merge should be the default.

## Failure behavior

If a required check fails, inspect it, fix the branch, rerun the check, and let auto-merge proceed only after required gates pass.

Do not bypass a failing required check unless the check itself is broken and the repository configuration is intentionally being repaired.

## Keep it lean

Do not add by default:
- mandatory coverage percentages;
- manual approval gates;
- release committees;
- long-lived integration branches;
- duplicate CI jobs;
- heavyweight scanners with no actionable value for the project's risk.
