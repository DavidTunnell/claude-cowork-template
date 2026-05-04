# Verification Patterns

**Last Updated:** 2026-05-04

"Verify before declaring done" (rule 34) is the single most valuable harness rule. This file is the recipe book — what verification *actually looks like* by domain. Pick the ones that fit the work you just did and run them. If verify fails, you're not done.

## The general shape

Every verification has three parts:

1. **The command** — exact, copy-pasteable, exit-code-matters.
2. **The pass condition** — what specifically counts as "verified." Exit 0 isn't always enough.
3. **The output to show the user** — paste the relevant lines back. "It passed" without output is not verification.

If you can't say all three, the change isn't verified yet.

## TypeScript / JavaScript

```
[package manager] tsc --noEmit
[package manager] test -- --run
[package manager] lint
[package manager] build
```

- **tsc --noEmit** — type-check everything without emitting. Should be clean before any commit.
- **test --run** — run tests once, no watch mode. Show pass/fail counts and any failures verbatim.
- **lint** — show zero warnings as the bar, not zero errors. Warnings tend to be load-bearing in mature codebases.
- **build** — only catches a class of bugs the type-checker doesn't (bundling, env vars, side effects). Worth running for non-trivial changes.

## Python

```
ruff check .
mypy .
pytest -q
```

- **ruff check** — fast lint + format check. Use `ruff format --check` if you want format-strictness too.
- **mypy** — only useful if the project has type hints. If it doesn't, skip and mention.
- **pytest -q** — quiet output, just the dots. If anything fails, re-run failing tests with `-v` and show the failing assertions.

## React / browser changes

Type-check + tests aren't enough. You also need:

- **Run the dev server** and load the affected page. Confirm no console errors.
- **Take a screenshot** — use the browser MCP if available (chrome-devtools-mcp / playwright). Show the screenshot back to the user.
- **Click through the interaction.** If the change was a button, click the button. If the change was a form, submit the form. Verify happens in the actual UI, not just in unit tests.
- **Check the network tab** for unexpected 4xx/5xx if the change touched data fetching.

## Backend / API changes

```
[verify command] && curl <local endpoint>
```

- Run the verify command (typecheck + tests).
- Hit the affected endpoint with `curl` or via the Browser MCP. Show response body and status code.
- For changes that touch DB queries: log the actual SQL being generated. Cheap to add, catches a lot of mistakes.

## AWS / infra changes

```
aws <command> --profile ama-<project> --dry-run
aws <command> --profile ama-<project>
```

- **Always `--dry-run` first** if the AWS service supports it (CloudFormation, S3, Lambda for some operations).
- **Diff the deployed state** before deploy: for CloudFormation, `aws cloudformation get-template` and compare. For Terraform, `terraform plan` and read it.
- **After deploy, hit the resource.** A green deploy that doesn't actually serve traffic isn't verified.
- **Check CloudTrail** for the change you just made — proof it landed and got logged.

## Database migrations

This is the one where verify-before-done matters most because the failure mode is data loss.

- **Run on a copy of production data** if you can. Schema-only or staged data, not real production.
- **Check the rollback plan exists** and works. Apply the up migration, apply the down migration, confirm schema returns to pre-state.
- **Time the migration** on a representative dataset. A 30-second migration on dev that takes 2 hours on prod is a different change.
- **Hand-execute one query that exercises the new schema** to confirm it produces the expected result.

## Documentation / content changes

Yes, even content. This is where I see the most "I'm done" with the wrong output.

- **Spell-check** with the editor's tools or `aspell`/`hunspell`.
- **Markdown lint** with `markdownlint` if the project uses it.
- **Render the file** — if it's markdown, render to HTML and skim. Bad heading levels and broken links are invisible until rendered.
- **Read the diff out loud once.** Catches more than you'd think.

## Multi-model consultation (for high-stakes calls)

When the change is architectural, security-sensitive, or a migration, one model's confident answer isn't enough. Run a consultation:

1. Phrase the question clearly with the constraints.
2. Dispatch a Plan subagent for the implementation strategy.
3. Independently, ask a second model (Gemini, GPT) the same question if access is available.
4. Compare the answers. Where they diverge is where you need to dig in.

This is rule 39's mechanic. Use it for:

- Choosing between technologies (queue vs. stream, RDBMS vs. NoSQL)
- Security model design (auth flow, secrets management, data segregation)
- Migration plans (DB engine change, framework upgrade, deprecation)
- Anything where a wrong answer is expensive to undo

## When verification fails

Don't patch forward. The pattern is:

1. **Show the failure** to the user. Full output, not a summary.
2. **Roll back to the last green state** (git checkpoint, branch reset).
3. **Re-prompt with the failure as new context.** "tsc said X — let's fix that, then re-attempt the original change."

Patching over a failed verify compounds mistakes. Rollback is cheap; debugging a half-broken state is expensive.

## What "verified" doesn't mean

Verified does **not** mean:

- "It compiled."
- "Claude said it should work."
- "The tests didn't change so they should still pass."
- "I've done this kind of change before."

Verified means: I ran the verify command, here's the output, here's why this output proves the change is good.
