# project_release_process

**Last Updated:** 2026-05-04

**Summary:** How a CSD change moves from idea to production. Reference this any time the question is "what's the right way to ship X."

## Sprint cadence

- **Sprint length:** 2 weeks (synced with WAG across all Webapper projects)
- **Sprint planning:** Tuesdays 2:30 PM CDT, weekly
- **Roadmap source:** Productboard with ICE scoring → Patrick promotes to Jira Discovery → estimated by Steven → committed to a sprint

## Ticket workflow (WAG-style)

```
New > Discovery > Confirmed > In Progress > Ready to Test > Testing Complete > Ready to Deploy > Ready for Production Testing > Done!
```

Notes:

- "Ready to Test" — dev complete on QA env. Daniela picks up.
- "Testing Complete" — Daniela has signed off on QA.
- "Ready to Deploy" — green light to push to production.
- "Ready for Production Testing" — deployed to prod, dev verifies, then Daniela / client UAT.
- "Done!" only after UAT passes. Don't close prematurely.

## Branch flow

`develop -> qa -> production`. Always.

- `develop` — integration branch, deployed to dev environment
- `qa` — QA env, deploys after merge from develop
- `production` — production env, deploys after merge from qa
- Feature branches off `develop`, named `[CSD-NNN]-short-description`

## Per-environment deploy targets

| Environment | Frontend URL | Frontend S3 | API S3 | CloudFront | DynamoDB |
|-------------|-------------|-------------|--------|-----------|----------|
| Production | https://drive.cloudsee.cloud | `cloudsee-drive-frontend-mp-saas` | `cloudsee-drive-api-stack-production` | E3MYVWORHVNEPX | `production_*` |
| QA | https://drive-qa.cloudsee.cloud | `cloudsee-drive-frontend-qa` | `cloudsee-drive-api-stack-qa` | E21TCNFRHC2JD7 | `qa_*` |
| Dev | https://drive-test.cloudsee.cloud | `cloudsee-drive-frontend-test` | `cloudsee-drive-api-stack-test` | E18Q5AYZVNBZS7 | `qa_*` |
| UAT/Demo | https://drive-uat.cloudsee.cloud | — | — | — | — |

Support portal mirrors the same three (production / uat / test).

## Deploy mechanics

- **Frontend:** S3 sync + CloudFront invalidation. After every deploy, invalidate.
- **Backend:** SAM CLI per service per environment. Bat scripts for Windows, sh for Linux. One service deploys at a time; coordinated rollouts when SDK changes are involved.
- **SDK changes:** version bump first, publish to NPM, then bump consumers. Don't publish a breaking SDK change without a coordinated consumer-update plan.

## Who deploys

- **QA:** any developer
- **Production:** review required. Typically Steven or David approves. Daniela handles QA test pass before sign-off.
- **Production verification:** dev verifies on prod, then Daniela / client UAT.

## Release strategy

- **Feature-per-month** cadence target
- **Epics ship as complete units.** No partial-epic releases.
- **No feature flags currently.** Releases are big-bang within a feature scope.
- **Public roadmap** at `cloudseedrive.com` shows Launched / In Progress / Planned. Don't reference internal feature names externally; check with Scott before any public-facing roadmap update.

## Verify gates

Before the "Ready to Deploy" transition, the change should have:

1. Verify command pass per package (typecheck, tests, lint)
2. Selenium pass on Jenkins (Henry)
3. Manual QA sign-off in QA env (Daniela)
4. PR review approved

Production deploys without all four are an exception, not a process — should appear in a retro.
