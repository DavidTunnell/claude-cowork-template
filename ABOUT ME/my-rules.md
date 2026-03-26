# My Rules

**Last Updated:** YYYY-MM-DD

<!-- ============================================================
     INSTRUCTIONS: This file defines non-negotiable rules for how
     Claude works with you.

     The WEBAPPER STANDARDS section contains team-wide rules that
     apply to everyone. Don't remove these unless the team agrees
     to change them.

     The MY PERSONAL RULES section at the bottom is yours to customize.
     Add rules that reflect how YOU want Claude to behave.
     ============================================================ -->

Non-negotiable rules for every interaction.

## Workflow (Webapper Standard)

1. **Read my context files first.** Read the ABOUT ME folder before every task. Don't produce generic output.
2. **Ask before assuming.** If my request is ambiguous, ask before you build.
3. **Plan before acting.** For anything non-trivial, outline the approach and get my approval before writing code or making changes. If the plan shifts mid-task, stop and check with me.
4. **Never delete or overwrite without approval.** No destructive changes unless I explicitly confirm.
5. **Stay in scope.** Only do what was asked. Don't refactor adjacent code, add unrequested features, or "improve" things outside the current task. If you see something worth fixing, flag it, don't just do it.

## Code Quality (Webapper Standard)

6. **No brittle code.** Handle edge cases, unexpected inputs, and failure states. Include error handling, input validation, and fallback behavior. No happy-path-only implementations.
7. **No coupled code.** Every module, function, and component works independently with its own inputs, outputs, and responsibilities. No shared internal state. If changing one thing breaks something unrelated, the architecture is wrong. If you can't deploy or test a module alone, refactor it.
8. **Don't break existing functionality.** After every change, verify that changed components and their neighbors still work. Run tests yourself, don't just trust they pass. Treat regressions as blockers, fix them before progressing.
9. **Production quality from the start.** Meaningful variable names, proper typing, realistic data structures. No `foo`, `bar`, placeholders, or dummy implementations.
10. **Match existing patterns.** Read surrounding code and follow established conventions (naming, file structure, error handling, logging). Consistency beats "better" in isolation. Don't introduce new dependencies without approval.
11. **Always read a file before editing it.** Never assume you know the current state. Read first, then change. This prevents overwriting recent work or editing based on stale context.
12. **Don't hallucinate APIs, methods, or imports.** Verify that any function, library, or module you reference actually exists in the codebase or dependency tree. If unsure, check, don't guess.

## Architecture (Webapper Standard)

<!-- NOTE: Rule 13 references the default Webapper stack. If your project uses
     a different stack, update this rule to match your project's architecture.
     See your project's project-context.md for the actual stack. -->

13. **Follow the stack.** React.js frontend, API Gateway + Lambda/Node.js backend, DynamoDB (Aurora MySQL for relational). AWS managed services and serverless by default. Functional components, hooks, ES6+.
14. **Server validates everything.** Never trust client-side data. Validate, sanitize, and verify permissions server-side. Confirm user ownership of resources. Keep secrets in environment variables / AWS Secrets Manager only.
15. **Audit trails on meaningful operations.** User changes, permission changes, errors, and state transitions generate logs. Generic error messages for users; detailed logs for developers.
16. **Comments explain why, not what.** JSDoc for public functions. `TODO`/`FIXME`/`DEPRECATED` tags with Jira ticket references for temporary code.

## Testing & Verification (Webapper Standard)

17. **Complexity scales with testing.** The bigger the change, the more time goes to verification. Spend 30-40% of effort on code health (inspections, refactoring, test coverage).
18. **Fix bugs before progressing.** Don't accumulate known bugs. If things go wrong, rollback to a git checkpoint rather than digging deeper into a broken state.
19. **When stuck, stop and ask.** If 2-3 approaches haven't worked, explain what you tried and ask for direction. Don't loop on failing patterns.

## AI-Assisted Development (Webapper Standard)

20. **Start coding tasks in plan mode.** Don't jump straight to writing code. Outline the approach, get agreement, then execute. This increases first-pass quality and prevents wasted iterations.
21. **Ask AI to improve its own work.** After generating code, ask it to review itself: identify weaknesses, find failure modes, improve structure, and increase test coverage.
22. **Break complex work into small tasks.** Don't give one massive prompt. Break features into 3-5+ discrete requests. Small tasks produce better results and fit the context window.
23. **Rollback, don't patch forward.** If AI-generated code goes wrong, return to the last good checkpoint and re-prompt. Patching over bad output compounds mistakes.
24. **Manage context proactively.** Keep context small and focused. When chat gets large, start a new session with a clean summary. Mention only relevant files. Use previously made components as reference for consistency.
25. **Leave it cleaner than you found it.** Boy Scout Rule: every task includes a small improvement to code health.

## Output & Communication (Webapper Standard)

26. **Use my voice.** Read my-voice.md. Match my tone. The test: would I publish this with my name on it, as-is? If an output reads like it could apply to anyone in any industry, rewrite it.
27. **Be direct.** Lead with the answer. Flag risks and tradeoffs proactively. Don't pad responses. No "As an AI..." disclaimers or caveats, skip them entirely.
28. **Never fabricate statistics or quotes.** If citing a stat, flag that I need to verify it. Never write quotes attributed to me or real people unless explicitly labeled as fictional.
29. **Flag uncertainty.** If you're unsure whether something matches my voice or intent, flag the specific phrase and offer an alternative. Don't silently guess.
30. **Don't repeat yourself.** If you've already explained something in this session, reference it, don't re-explain from scratch.
31. **Save deliverables to the OUTPUTS folder.** Docs, code, reports: save where I can find them.

---

## My Personal Rules

<!-- CUSTOMIZE: Add rules specific to how YOU want Claude to work with you.
     These are in addition to the Webapper standards above.

     Think about:
     - Do you prefer verbose or minimal responses?
     - Are there specific frameworks or patterns you always want followed?
     - Do you want Claude to proactively suggest improvements or only do what's asked?
     - Are there tools, formats, or conventions specific to your role?
     - Any hard "never do this" rules based on past bad experiences?

     Examples:
     - "Always include the Jira ticket number in commit messages"
     - "When writing SQL, always use CTEs over subqueries"
     - "Don't suggest React class components, always use functional with hooks"
     - "For client emails, always draft two versions: one short, one detailed"
-->

32. [Your personal rule]
33. [Your personal rule]
34. [Your personal rule]
