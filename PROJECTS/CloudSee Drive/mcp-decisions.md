# MCP Decisions — CloudSee Drive

**Last Updated:** 2026-05-04

Walking through `HARNESS/mcp-shortlist.md` with CSD's specific context. One pass, table-form per skill step 4 fix.

## Audit

| MCP | Decision | Rationale |
|-----|----------|----------|
| Atlassian (Jira + Confluence) | Install | CSD is the project key; Productboard → Discovery → sprint flow runs through Jira. Confluence has the Deployment Spec, Sprint History, Permissions Spec — all referenced regularly. |
| Bitbucket | Install | `csd-frontend` and `csd-backend` are both in Bitbucket. PR reviews, diffs, file reads are daily-driver work. |
| Google Drive | Install | Permissions Spec and shared docs live in Google Drive. Lower volume than for Herb Co but still in the loop. |
| AWS API + AWS CLI v2 | Install | Active SAM deploys, CloudFormation introspection, DynamoDB schema queries, CloudWatch logs. Use `--profile ama-cloudsee`. |
| Browser automation (Chrome / Playwright) | Install | Frontend changes require screenshot + click-through verification per CLAUDE.md. Without browser MCP, this is manual. |
| Slack (Webapper) | Install | Internal comms; sprint coordination; production-issue notifications via channel webhooks. |
| Sentry | Install if wired up | CSD's tech-debt epic (CSD-202) includes "Observability and monitoring." If Sentry is already instrumented, install. If not, this is a Phase 2 dependency. **Confirm current state.** |
| Database MCP — DynamoDB | Skip (covered by AWS API) | The AWS API MCP handles DynamoDB queries. No separate DB MCP needed. |
| Database MCP — OpenSearch | Pending | Useful for ad-hoc Fast Buckets index queries during search-related dev. **Decide based on whether anyone's actively wishing for it during search/index work.** |
| Productboard MCP | Pending | If a Productboard MCP exists and is reasonable to install, it would close the gap between roadmap prioritization and Jira commitments. **Check the registry.** |
| Context7 | Skip | React + AWS SDK v3 + Lambda Node.js runtimes are stable enough. Training data isn't notably stale. Reconsider if hitting hallucinated API issues. |
| Firecrawl | Skip | External docs are mostly the AWS / React official sources, accessible via WebFetch. No structured-extraction need. |
| GitHub | Skip | Webapper code is in Bitbucket. Only install if integrating with an OSS dependency that's on GitHub and needs deeper interaction than WebFetch. |
| Intercom | Skip until support flow is wired | If/when CSD's customer support runs through Intercom (currently the support portal is a separate domain), revisit. |

## Pending — needs confirmation before next session

1. **Sentry** — is it already instrumented? Yes → install MCP. No → Phase 2 of CSD-202 tech debt.
2. **OpenSearch MCP** — does one exist that beats AWS-API-CLI's `aws opensearch` invocations? Worth checking the registry.
3. **Productboard MCP** — is there a usable one in the registry? If yes, install since the prioritization flow is high-volume.

## Smoke-test calls (run these after install to verify)

- Atlassian: `searchJiraIssuesUsingJql` with `project = CSD AND statusCategory != Done` — should return current sprint tickets
- Bitbucket: read the most recent PR on `csd-frontend` — should return PR metadata + diff
- Google Drive: `search_files` with `title contains 'Permissions Spec'` — should find the doc
- AWS API: `aws cloudformation list-stacks --profile ama-cloudsee` — should list CSD stacks
- Browser: navigate to `https://drive-qa.cloudsee.cloud`, take screenshot — should render the QA login screen
