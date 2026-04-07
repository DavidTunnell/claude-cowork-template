# AI Dashboard — Epic Feature Brainstorm

**Date:** 2026-03-31
**Author:** David Tunnell (with Claude)
**Status:** Draft — Feature Discovery
**Epic:** Webapper AI Dashboard

---

## Vision

A standalone internal dashboard where Webapper team members can create, deploy, monitor, and manage AI agents from a single interface. Agents are built from a standardized template (code-first via Claude Code), registered on the dashboard, and deployed/scheduled/triggered without touching infrastructure directly. Architected internal-first with a path to offering it as a client-facing product.

---

## 1. Agent Template System

The foundation. A standardized project template that any developer can clone and use as the starting point for building a new agent.

### 1.1 Template Scaffold (CLI / Repo)

- **Cookiecutter-style generator** — `npx create-webapper-agent my-agent` (or equivalent) that scaffolds a new agent project with the standard structure. Phase 1: can just be a GitHub/Bitbucket template repo you clone manually.
- **Runtime: AWS Lambda (TypeScript or Python)** — Phase 1 targets Lambda functions that receive an event and return a result. Two language templates ship from day one: TypeScript (matches CloudSee/dashboard stack) and Python (matches Daily Pulse stack, better for AI-heavy agents). Both share the same `agent.config.json` schema and interface contract. Claude Code Agent SDK and Fargate are future runtime options (Phase 3+), not baked into the template abstraction.
- **Standard project structure (TypeScript variant):**
  - `agent.config.json` — Name, description, version, trigger type, input schema, output schema, owner, tags
  - `src/handler.ts` — Main Lambda entrypoint implementing the standard interface (`execute(context, inputs) → outputs`)
  - `src/tools/` — Agent-specific helper modules (API clients, data fetchers, formatters)
  - `src/prompts/` — System prompts and templates for agents that call the Anthropic API
  - `tests/` — Unit and integration tests with mock Lambda events
  - `template.yaml` — SAM template for deploying the Lambda + EventBridge rule + IAM role
  - `README.md` — Auto-generated from `agent.config.json`
- **Standard project structure (Python variant):**
  - `agent.config.json` — Same schema as TypeScript variant
  - `src/handler.py` — Main Lambda entrypoint implementing the same interface contract
  - `src/tools/` — Agent-specific helper modules
  - `src/prompts/` — System prompts and templates
  - `tests/` — pytest-based unit and integration tests with mock Lambda events
  - `requirements.txt` — Python dependencies
  - `template.yaml` — SAM template (Lambda Python runtime)
  - `README.md` — Auto-generated from `agent.config.json`
- **Built-in local dev experience** — `sam local invoke` for testing with sample events, plus `npm test` (TS) or `pytest` (Python) for unit tests. No custom test harness UI in Phase 1.
- **Config-driven metadata** — Everything the dashboard needs to display and manage the agent comes from `agent.config.json` (no manual dashboard entry)

### 1.1.1 Example `agent.config.json`

This is what a real config looks like, using the Stale Ticket Flagger (the Phase 1 validation agent) as the example:

```json
{
  "$schema": "https://agents.webapper.com/schemas/agent-config-v1.json",
  "name": "stale-ticket-flagger",
  "displayName": "Stale Ticket Flagger",
  "description": "Runs the Webapper stale tickets JQL filter daily and posts a summary to Google Chat with action owners.",
  "version": "1.0.0",
  "runtime": "lambda",
  "language": "typescript",
  "owner": {
    "email": "david@webapper.com"
  },
  "tags": ["jira", "notifications", "daily", "all-projects"],
  "trigger": {
    "type": "scheduled",
    "schedule": {
      "expression": "cron(0 13 ? * MON-FRI *)",
      "timezone": "America/Denver",
      "description": "Weekdays at 8:00 AM ET / 6:00 AM MT"
    }
  },
  "inputSchema": {
    "type": "object",
    "properties": {
      "projectKeys": {
        "type": "array",
        "items": { "type": "string" },
        "default": ["CSD", "VAST", "EDS"],
        "description": "Jira project keys to check for stale tickets"
      },
      "staleDays": {
        "type": "integer",
        "default": 7,
        "description": "Number of days without update before a ticket is considered stale"
      }
    }
  },
  "outputSchema": {
    "type": "object",
    "properties": {
      "staleCount": { "type": "integer" },
      "ticketsByAssignee": {
        "type": "object",
        "additionalProperties": {
          "type": "array",
          "items": { "type": "string" }
        }
      },
      "notificationSent": { "type": "boolean" }
    }
  },
  "secrets": [
    "JIRA_API_TOKEN",
    "GOOGLE_CHAT_WEBHOOK_URL"
  ],
  "resources": {
    "memoryMB": 256,
    "timeoutSeconds": 60
  },
  "notifications": {
    "onFailure": {
      "channel": "google-chat",
      "webhookSecretRef": "GOOGLE_CHAT_WEBHOOK_URL"
    }
  }
}
```

**Key design choices in this schema:**
- `$schema` field enables IDE autocomplete and validation against a published JSON Schema
- `trigger` is a single object (not an array) in Phase 1. Phase 2 extends this to support multiple triggers.
- `secrets` lists the Secrets Manager key names the agent needs — the platform injects these as environment variables at runtime. The agent never sees raw secret values in config.
- `inputSchema` / `outputSchema` are standard JSON Schema — the dashboard auto-generates input forms and output display from these.
- `runtime` and `language` are explicit so the deployment pipeline knows which SAM template and build process to use.

**Future runtime support (Phase 3+):** When a use case exceeds Lambda's 15-min timeout or needs persistent connections, the template can be extended with a Fargate variant (Dockerfile in `deploy/`) or a Claude Code Agent SDK variant. These would be separate template flavors, not a runtime toggle — the execution models are fundamentally different and trying to abstract over them creates a leaky abstraction.

### 1.2 Agent Interface Contract

- **Standard input/output schema** — Every agent declares what it expects and what it returns (JSON Schema)
- **Execution context object** — Injected at runtime with: trigger metadata, environment variables, secrets references, caller identity, run ID, parent run ID (for chaining)
- **Lifecycle hooks:**
  - `onInit` — Setup, load config, warm connections
  - `execute` — Main logic
  - `onSuccess` — Post-processing, notifications
  - `onError` — Error handling, alerting, retry logic
  - `onTimeout` — Graceful timeout handling
- **Logging contract** — Structured JSON logs with standard fields (agentId, runId, timestamp, level, message, metadata) that the dashboard can ingest and display
- **Health check endpoint** — Simple ping/status for the dashboard to verify an agent is alive and deployable

### 1.3 Template Variants

**Phase 1: Blank template only.** One template, well-tested, with clear documentation. Resist the urge to pre-build variants before real agents have been built — patterns should emerge from actual agent development, not be guessed upfront.

- **Blank agent** — Standard Lambda handler with the interface contract, `agent.config.json`, SAM template, test scaffold, and README. This is the only template for Phase 1-2.

**Future variants (Phase 3+, extract from real agents):** Once 3+ agents are running in production, identify repeated patterns and extract them into named variants. Likely candidates based on the first-agents list: "scheduled report" (pull data → summarize → send), "event responder" (receive event → process → notify), "Jira automation" (query Jira → transform → act). But let real code drive these — don't design them in advance.

---

## 2. Dashboard — Agent Registry & Catalog

The main view. Every registered agent appears here.

### 2.1 Agent List View

- **Card or table layout** (toggle between views) showing all registered agents
- **Each agent card shows:**
  - Agent name and icon/avatar
  - Short description (from `agent.config.json`)
  - Trigger type badge(s): `Scheduled` | `Event` | `Manual` | `Multi`
  - Current status: `Active` | `Paused` | `Error` | `Draft` | `Deploying`
  - Last run timestamp + duration
  - Next scheduled run (if applicable)
  - Success/failure indicator (last N runs sparkline or color dot)
  - Owner (team member who created it)
  - Tags for filtering (project, category, team)
- **Filtering and search:**
  - Filter by: status, trigger type, owner, project, tag
  - Full-text search across agent name and description
  - Sort by: name, last run, next run, status, created date
- **Quick actions from list view:**
  - Run now (manual trigger)
  - Pause / Resume
  - View last run log
  - Open detail view

### 2.2 Agent Detail View (Expanded)

Click into any agent to see its full profile:

- **Overview tab:**
  - Full description, version, owner, created/updated dates
  - README rendered from the agent's repo
  - Input/output schema documentation (auto-generated from JSON Schema)
  - Links: source repo, Jira epic/tickets, Confluence docs
- **Configuration tab:**
  - Trigger configuration (schedule, events, manual — see Section 4)
  - Environment variables (masked, with edit capability)
  - Secrets references (AWS Secrets Manager ARNs — display-only, edit in AWS console)
  - Resource limits: timeout, memory, concurrency
  - Retry policy: max retries, backoff strategy
  - Notification settings: who gets alerted on success/failure/timeout
- **Run History tab:**
  - Table of all runs with: run ID, trigger source, start/end time, duration, status, link to logs
  - Filter by date range, status, trigger type
  - Click into a run to see full structured logs, inputs, outputs, and error details
  - Compare two runs side-by-side (useful for debugging regressions)
- **Metrics tab:**
  - Success rate over time (chart)
  - Average duration over time (chart)
  - Runs per day/week (chart)
  - Error rate breakdown by error type
  - Cost estimate per run (Lambda compute + API calls)
- **Dependencies tab (Phase 3+, deferred):** No agent-to-agent dependencies exist until chaining is built. Revisit when agents start triggering other agents. For now, external service connections are documented in the agent's README, not tracked in the dashboard.

---

## 3. Deployment Pipeline

How an agent goes from code on a developer's machine to running in production.

### 3.1 Registration Flow

**Phase 1 (manual):**
1. Developer builds agent locally using the template
2. Developer pushes to a Bitbucket repo (standard Webapper git flow)
3. Developer opens dashboard → "Register Agent" → pastes repo URL and uploads or pastes `agent.config.json`
4. Dashboard validates the config against the JSON Schema and creates the agent entry
5. Agent appears in dashboard as `Draft` status

**Phase 2 (CLI):**
- `webapper-agent register` command reads `agent.config.json` from the current directory and calls the dashboard API to register the agent automatically. Same result, less manual copying.

### 3.2 Deployment Flow

- **From dashboard:** Click "Deploy" on a Draft or updated agent → triggers the deployment pipeline
- **Phase 1 (manual deploy):** Developer runs `sam build && sam deploy` locally or via Bitbucket Pipeline. Dashboard tracks the deployed Lambda ARN and status — it doesn't orchestrate the deployment itself. This avoids building a custom CI/CD system before the platform is validated.
- **Phase 2 (dashboard-triggered deploy):**
  1. Pull latest code from repo
  2. Run tests (`npm test`)
  3. Build via SAM (`sam build`)
  4. Deploy via SAM (`sam deploy --no-confirm-changeset`)
  5. Verify Lambda is invocable (test invocation with health check payload)
  6. Update dashboard status to `Active`
  - Implementation: a "deployer" Lambda that wraps SAM CLI via CodeBuild (SAM CLI needs Docker for builds, which Lambda can't provide — CodeBuild can).
- **Deployment target: Lambda only (Phase 1-2).** Fargate and Step Functions are Phase 3+ when a concrete use case demands them.
- **Rollback (Phase 2):** One-click rollback to previous Lambda version using Lambda aliases and version publishing. Dashboard keeps a pointer to the last 5 published versions.
- **Environment promotion (Phase 3):** Deploy to `dev` → `staging` → `prod` with approval gates. Phase 1-2 deploys directly to a single environment.

### 3.3 Infrastructure as Code

- Each agent's `deploy/` folder contains its IaC (SAM or CDK)
- The dashboard doesn't provision raw AWS resources — it orchestrates the agent's own IaC
- Shared infrastructure (EventBridge bus, API Gateway, log groups, IAM roles) is managed by a separate "platform" stack
- New agents inherit sensible defaults (VPC, subnets, security groups, IAM role with least privilege)

---

## 4. Scheduling & Trigger Management

Configurable from the dashboard UI — no code changes needed to adjust when/how an agent runs.

### 4.1 Schedule-Based Triggers (Cron)

- **Visual cron builder** — Pick frequency (every N minutes/hours/days, specific days of week, specific dates), preview next 5 run times
- **Presets:** Every 15 min, hourly, daily at 9am ET, weekdays at 8am ET, weekly Monday AM, first of month, end of sprint
- **Raw cron expression** option for power users
- **Timezone-aware** — All schedules stored in UTC, displayed in user's local timezone
- **Blackout windows** — "Don't run on weekends" or "Don't run during deploy windows"
- **Implementation:** EventBridge Scheduler rules targeting the agent's Lambda or Step Function

### 4.2 Event-Based Triggers (Phase 2+)

Event-based triggers are not in Phase 1 MVP. Phase 1 supports scheduled and manual triggers only.

- **Phase 2 event sources:**
  - **Jira** — Issue created, issue transitioned, comment added, sprint started/ended (via Jira webhook → EventBridge)
  - **Bitbucket** — PR created, PR merged, push to branch, pipeline completed (via Bitbucket webhook → EventBridge)
  - **Webhook (generic)** — Dashboard generates a unique webhook URL per agent, any external service can POST to it
  - **Manual API call** — REST endpoint for triggering programmatically
- **Phase 3+ event sources:**
  - **GitHub** — Same as Bitbucket but for any GitHub repos
  - **Email** — New email matching a filter (via Gmail MCP or SES inbound)
  - **Google Chat** — Message in channel, mention, reaction
  - **CloudWatch Alarm** — Metric threshold crossed
  - **S3** — Object created/deleted in a bucket
  - **Another agent** — Agent A completes → triggers Agent B (chaining via EventBridge)
  - **Daily Pulse** — Standup submitted, blocker detected (requires Daily Pulse API — doesn't exist yet)
  - **Calendar** — Meeting starting in 15 min, new event created
- **Event filtering** — Configure conditions on the event payload (e.g., "only trigger on Jira issues in project CSD with priority >= High")
- **Event mapping** — Map fields from the event payload to the agent's input schema (visual mapper or JSONPath expressions)
- **Implementation:** EventBridge event bus with rules per agent. Webhooks land in API Gateway → Lambda router → EventBridge.

### 4.3 Manual Triggers

- **"Run Now" button** on the dashboard with optional input form (auto-generated from the agent's input schema)
- **Pre-filled templates** — Save commonly used input configurations as named presets
- **Dry run mode** — Execute the agent but don't commit side effects (send emails, update Jira, etc.)
- **Bulk trigger** — Select multiple agents and run them all (useful for "run all daily agents now")

### 4.4 Trigger Combinations

- An agent can have multiple triggers simultaneously (e.g., runs on a schedule AND can be triggered manually AND listens for a Jira event)
- **Priority/deduplication** — If a scheduled run and an event trigger fire at the same time, configurable behavior: run both, skip duplicate, queue

---

## 5. Monitoring & Observability

### 5.1 Dashboard Home / Overview

- **System health at a glance:**
  - Total agents: active, paused, errored, draft
  - Runs in last 24h: total, succeeded, failed
  - Agents currently running
  - Next 5 upcoming scheduled runs
- **Recent activity feed** — Chronological log of agent runs, deployments, config changes
- **Alerts banner** — Any agents in error state or with repeated failures

### 5.2 Run Logs & Debugging

- **Structured log viewer** — Filter, search, and tail logs for any agent run
- **Log levels:** DEBUG, INFO, WARN, ERROR with color coding
- **Execution trace** — For multi-step agents, show the step-by-step execution with timing
- **Input/output inspector** — View the exact input the agent received and the output it produced
- **Error details** — Stack trace, error classification, link to relevant code in Bitbucket
- **Log retention** — Configurable per agent (default 30 days), archived to S3 after

### 5.3 Alerting & Notifications

- **Per-agent notification config:**
  - On failure: Google Chat message, email, or both
  - On success (optional): useful for critical agents where you want confirmation
  - On timeout
  - On repeated failure (e.g., 3 failures in a row)
- **Global notification channels:**
  - Google Chat webhook (primary — Webapper's team chat)
  - Email (via SES or Gmail)
  - Future: PagerDuty, Slack
- **Notification templates** — Customizable message format per agent

---

## 6. Access Control & Multi-Tenancy

### 6.1 Phase 1 — Internal (Webapper Team)

- **Google OAuth login** (existing Google Workspace accounts)
- **Role-based access:**
  - **Admin** — Full access: create, deploy, delete agents, manage users, view all logs
  - **Developer** — Create and deploy own agents, view all agent details and logs
  - **Viewer** — Read-only: see agent list, status, run history, logs
- **Ownership** — Each agent has an owner. Only owner + admins can edit config or deploy.
- **Audit log** — Who did what, when (config changes, deployments, manual triggers)

### 6.2 Phase 2 — Client-Facing

- **Organization / tenant model** — Each client gets their own namespace
- **Client-visible agents** — Mark specific agents as visible to a client's dashboard
- **Client roles** — Client admins can view agent status and trigger manual runs, but can't modify
- **White-labeling** — Optionally brand the dashboard per client
- **Billing integration** — Track compute cost per client/agent for invoicing

---

## 7. ~~Agent Marketplace / Library~~ (Deferred — Phase 4+)

Not designing this now. With <10 agents expected in the first 6 months, a marketplace adds complexity with no user. Revisit when the agent count crosses ~15 and team members are duplicating work. The concrete agent ideas that were here have been moved to Section 9 (First Agents) where they belong.

---

## 8. Developer Experience

### 8.1 Claude Code Integration

- **`/create-agent` command** — Invoke from Claude Code to scaffold a new agent project interactively
- **`/deploy-agent` command** — Deploy the current agent project to the dashboard from the terminal
- **`/test-agent` command** — Run the agent locally with a simulated trigger
- **Agent context awareness** — When working in an agent project, Claude Code understands the agent template structure and can help with tools, prompts, and config
- **MCP tool library** — Pre-packaged MCP tool collections (Jira tools, Google Workspace tools, AWS tools) that agents can import

### 8.2 Testing & Validation

- **Local test harness** — Simulates triggers, provides mock context, captures output
- **Integration test mode** — Runs against real services in a dev/sandbox environment
- **Schema validation** — `agent.config.json` validated against a JSON Schema on save and before deploy
- **Linting** — ESLint/Prettier config included in template, enforced on deploy
- **Contract testing** — Verify agent output matches declared output schema

### 8.3 Documentation Auto-Generation

- Agent README auto-generated from config
- API docs for webhook endpoints auto-generated
- Run history and metrics feed into auto-generated "agent health" pages in Confluence

---

## 9. Webapper-Specific First Agents

Concrete agents to build as part of the epic — both to validate the platform and to deliver immediate value.

| Agent Name | Trigger | Description | Priority |
|---|---|---|---|
| Sprint Progress Email | Scheduled (end of sprint) | Generates and sends a sprint summary email to the client with completed/in-progress/blocked tickets, velocity, and highlights. Referenced in WAG as a future goal. | High |
| Stale Ticket Flagger | Scheduled (daily, 8am ET) | Runs the existing Stale Tickets JQL filter, posts a summary to Google Chat with action owners. | High |
| PR Review Nudger | Event (PR open > 48h) | Monitors open PRs in Bitbucket, nudges reviewers via Google Chat after 48h. | Medium |
| Daily Standup Digest | Scheduled (daily, 9:30am ET) | Pulls Daily Pulse standups, generates an AI summary highlighting blockers and themes, posts to Google Chat. | Medium |
| Sprint Kickoff Prep | Event (sprint started in Jira) | Auto-generates a sprint kickoff summary with capacity, planned tickets, and risks. | Medium |
| Deployment Watcher | Event (Bitbucket pipeline completed) | Reports build success/failure to Google Chat with links and commit summaries. | Low |
| Weekly Cost Report | Scheduled (Monday 8am ET) | Pulls AWS Cost Explorer data, generates a spend summary with trend and anomaly flags. | Low |

---

## 10. Tech Stack (Proposed)

| Layer | Technology | Notes |
|---|---|---|
| Frontend | React, TypeScript, Vite, Tailwind, shadcn/ui | Consistent with CloudSee Drive and Daily Pulse |
| Backend API | Node.js, TypeScript on Lambda (SAM) | Serverless, standard Webapper stack |
| Database | DynamoDB (Agents + Runs tables) | See Section 11 for schema and access patterns |
| Agent Languages | TypeScript (primary) + Python | Both supported from day one. Shared `agent.config.json` contract. |
| Agent Execution | Lambda (Phase 1-2). Fargate/Step Functions Phase 3+. | Single runtime keeps deployment simple |
| Agent Repos | Multi-repo (one per agent) | Shared code in `@webapper/agent-sdk` npm package |
| Scheduling | EventBridge Scheduler | Native AWS cron with timezone support |
| Event Bus | EventBridge | All agent triggers — webhooks, service events, agent-to-agent (Phase 3) |
| Webhooks | API Gateway → Lambda → EventBridge | Inbound webhook receiver (Phase 2) |
| Logs | CloudWatch Logs Insights | Query via API, render in dashboard (Phase 2). Migrate to OpenSearch only if needed. |
| Auth | Google OAuth via Cognito | Existing Webapper Google Workspace |
| Hosting | agents.webapper.com — CloudFront + S3 (frontend), API Gateway (backend) | Standalone subdomain |
| IaC | AWS SAM | Consistent with existing projects |
| CI/CD | Phase 1: manual `sam deploy`. Phase 2: CodeBuild triggered by dashboard. | No custom CI/CD pipeline to maintain |
| Secrets | AWS Secrets Manager | API keys, tokens referenced by agents |
| Notifications | Google Chat webhooks, SES | Phase 2 |

---

## 11. Data Model (DynamoDB)

Two tables cover all Phase 1-2 access patterns. Single-table design was considered but rejected — the Agents and Runs entities have different enough access patterns that separate tables are cleaner and easier to reason about.

### Agents Table

Stores agent registry metadata. Low write volume, frequent reads.

| Attribute | Type | Role |
|---|---|---|
| `agentId` | String (ULID) | **Partition Key** |
| `name` | String | Display name |
| `description` | String | Short description |
| `status` | String | `draft` / `active` / `paused` / `error` |
| `triggerType` | String | `scheduled` / `event` / `manual` / `multi` |
| `ownerId` | String | Google OAuth user ID |
| `ownerEmail` | String | For display |
| `repoUrl` | String | Bitbucket repo URL |
| `lambdaArn` | String | Deployed Lambda ARN (null if draft) |
| `scheduleExpression` | String | EventBridge cron/rate expression (null if not scheduled) |
| `scheduleRuleArn` | String | EventBridge Scheduler rule ARN |
| `inputSchema` | Map (JSON) | JSON Schema for agent inputs |
| `outputSchema` | Map (JSON) | JSON Schema for agent outputs |
| `config` | Map (JSON) | Full `agent.config.json` contents |
| `tags` | String Set | Filterable tags (project, category) |
| `lastRunAt` | String (ISO 8601) | Denormalized for list view sort |
| `lastRunStatus` | String | Denormalized for list view display |
| `createdAt` | String (ISO 8601) | |
| `updatedAt` | String (ISO 8601) | |

**GSI-1: ByOwner** — `ownerId` (PK), `updatedAt` (SK). Query: "all agents owned by Kevin, most recent first."

**GSI-2: ByStatus** — `status` (PK), `name` (SK). Query: "all active agents" for the dashboard home view.

### Runs Table

Stores execution history. High write volume (one row per agent run), queried by agent and time range.

| Attribute | Type | Role |
|---|---|---|
| `agentId` | String | **Partition Key** |
| `runId` | String (ULID, sortable) | **Sort Key** |
| `triggerSource` | String | `scheduled` / `event` / `manual` / `api` |
| `triggerDetail` | Map (JSON) | Event payload, schedule expression, or manual input |
| `status` | String | `running` / `succeeded` / `failed` / `timed_out` |
| `startedAt` | String (ISO 8601) | |
| `completedAt` | String (ISO 8601) | Null while running |
| `durationMs` | Number | Computed on completion |
| `input` | Map (JSON) | Actual input passed to the agent |
| `output` | Map (JSON) | Agent return value (truncated if large) |
| `errorMessage` | String | Null on success |
| `errorType` | String | Error classification (null on success) |
| `logGroupName` | String | CloudWatch log group |
| `logStreamName` | String | CloudWatch log stream for this run |
| `ttl` | Number | Unix timestamp for DynamoDB TTL (auto-delete after retention period) |

**No GSIs needed for Phase 1.** Primary access pattern is "get runs for agent X, newest first" which is covered by the composite key (`agentId` PK + `runId` SK with ULID sort). The `runId` ULID is time-sortable, so `ScanIndexForward: false` returns newest first.

**Phase 2 GSI (if needed):** ByStatus — `status` (PK), `startedAt` (SK). Query: "all currently running agents" for the dashboard home view. Could also be done with a DynamoDB filter on the Agents table's `lastRunStatus` field to avoid the extra GSI.

### Access Pattern Summary

| Access Pattern | Table | Key / Index |
|---|---|---|
| Get agent by ID | Agents | PK = `agentId` |
| List all agents | Agents | Scan (small table, <100 items for years) |
| List agents by owner | Agents | GSI-1 (ByOwner) |
| List agents by status | Agents | GSI-2 (ByStatus) |
| Get runs for agent (newest first) | Runs | PK = `agentId`, SK descending |
| Get runs for agent in date range | Runs | PK = `agentId`, SK between ULID(start) and ULID(end) |
| Get single run | Runs | PK = `agentId`, SK = `runId` |
| Count runs by status for agent | Runs | PK = `agentId`, filter on `status` |
| Auto-expire old runs | Runs | DynamoDB TTL on `ttl` field |

---

## Priority & Phasing (Suggested)

### Phase 1 — MVP (Target: 1 sprint, 1-2 people)

Scope gate: if any item below takes longer than estimated, cut from the bottom up.

- Agent template scaffold (blank template in both TypeScript and Python)
- `agent.config.json` schema definition and validation
- Agent interface contract (`execute(context, inputs) → outputs` for Lambda)
- DynamoDB tables: Agents, Runs (see Section 11 — Data Model)
- Backend API: CRUD for agents, trigger run, fetch run history (Node.js/Lambda/SAM)
- Dashboard: agent list view (table only, no card toggle), detail view (overview + run history tabs only), "Run Now" button with input form
- Registration: manual via dashboard form (paste repo URL + config) — no CLI tool yet
- Lambda deployment: manual SAM deploy per agent, dashboard just tracks status — no custom CI/CD pipeline yet
- Schedule triggers: EventBridge Scheduler rules created manually or via SAM, dashboard displays them read-only
- Basic log viewer: link to CloudWatch Logs console per run (no custom log UI yet)
- Google OAuth login (admin/developer roles, no viewer role yet)
- 1 deployed agent: Stale Ticket Flagger (validates the template end-to-end)

**What's explicitly NOT in Phase 1:** Visual cron builder, event-based triggers, custom deployment pipeline, rollback, environment promotion, metrics charts, notifications, run comparison, filtering/search beyond basic status filter.

### Phase 2 — Automation & Events
- Dashboard-managed scheduling: visual cron builder, create/edit EventBridge rules from the UI
- Custom log viewer (structured logs in-dashboard, not just CloudWatch links)
- Event-based triggers: Jira webhooks, Bitbucket webhooks, generic webhook URL
- Event filtering and payload mapping
- Alerting and notifications (Google Chat webhook, email via SES)
- CLI tool: `webapper-agent register` and `webapper-agent deploy` commands
- Deployment pipeline: automated build/test/deploy triggered from dashboard
- Rollback (one-click to previous Lambda version)
- Metrics tab (success rate, duration, runs over time)
- 2-3 more agents (Sprint Progress Email, Daily Standup Digest, PR Review Nudger)

### Phase 3 — Scale & Polish
- Fargate deployment path for long-running agents
- Step Functions deployment path for complex workflows
- Agent-to-agent chaining via EventBridge
- Dry run mode
- Environment promotion (dev → staging → prod)
- Full-text search and advanced filtering on agent list
- Run comparison (side-by-side)
- Audit logging

### Phase 4 — Productize (if validated internally)
- Multi-tenant model (org/namespace)
- Client-facing read-only dashboard
- Dashboard wizard for simple agent creation (non-technical users)
- Cost tracking per agent and per tenant
- White-labeling
- Agent library / sharing (if agent count warrants it)

---

## Open Questions & Decisions Needed

All questions resolved as of 2026-03-31.

1. ~~**Primary execution runtime**~~ **DECIDED: Lambda-only for Phase 1-2.** Fargate and Step Functions are Phase 3+ when a concrete use case exceeds Lambda's 15-min ceiling. Claude Code Agent SDK is a separate template variant, not a runtime toggle.

2. ~~**Agent code language**~~ **DECIDED: TypeScript AND Python from day one.** Ship both template variants in Phase 1. TypeScript is primary (matches dashboard/CloudSee stack), Python variant covers Kevin's skillset and AI-heavy agents (Anthropic SDK, data processing). Both templates share the same `agent.config.json` schema and interface contract — only the handler implementation differs. SAM template works for both (Lambda supports both runtimes natively).

3. ~~**Deployment orchestration**~~ **DECIDED: CodeBuild triggered by dashboard (Phase 2).** Phase 1: manual `sam deploy` by the developer. Phase 2: dashboard API triggers a CodeBuild project that runs `sam build` + `sam deploy`. AWS handles Docker builds, IAM, retries, and timeout. Dashboard polls CodeBuild for status via `BatchGetBuilds` API. No custom CI/CD to build or maintain — CodeBuild is the CI/CD.

4. ~~**Log aggregation**~~ **DECIDED: CloudWatch Logs Insights.** No extra infrastructure. Dashboard queries logs via CloudWatch Logs Insights API, renders results in a custom viewer (Phase 2). Good enough for <50 agents. Migrate to OpenSearch only if cross-agent log search or advanced filtering becomes a real pain point.

5. ~~**Agent-to-agent communication**~~ **DECIDED: EventBridge events (Phase 3).** Agent A emits a completion event to the shared EventBridge bus, Agent B subscribes via an EventBridge rule. Fully decoupled — agents don't know about each other. Matches the existing EventBridge architecture for triggers. No direct Lambda-to-Lambda invocation.

6. ~~**Repo structure**~~ **DECIDED: Multi-repo.** One Bitbucket repo per agent. Independent deploys, independent CI, no coupling between agents. Shared code (interface contract, logging helpers, config schema validation) lives in an npm package (`@webapper/agent-sdk`) published to a private registry. Template repo is a standalone Bitbucket repo that developers clone to start a new agent.

7. ~~**Dashboard hosting**~~ **DECIDED: agents.webapper.com.** Standalone subdomain. CloudFront + S3 for the React frontend, API Gateway for the backend API. Cognito + Google OAuth for auth. Clean DNS scoping, easy to add client-specific subdomains later (Phase 4) if needed (e.g., `clientname.agents.webapper.com`).

8. ~~**Who builds first**~~ **DEFERRED.** Team assignment will be decided when the epic is scheduled. The doc captures what needs to be built, not who builds it.

---

## Dependencies & Prerequisites

**Phase 1 (must have before MVP):**
- DNS: `agents.webapper.com` subdomain configured, SSL cert via ACM
- AWS: EventBridge event bus provisioned, Cognito user pool with Google OAuth
- Bitbucket: template repos created (TS + Python), `@webapper/agent-sdk` npm package repo
- Anthropic: Claude API key for agents that call the Anthropic API
- CloudFront + S3: hosting for the dashboard frontend

**Phase 2 (must have before events/automation):**
- Jira webhook configuration for issue events
- Bitbucket webhook configuration for repos that will trigger agents
- Google Chat webhook URL for team notification channel
- CodeBuild project configured for agent deployments
- `@webapper/agent-sdk` npm package published to private registry

---

## Success Metrics

- **Platform:** Number of agents deployed and running without manual intervention
- **Reliability:** Agent success rate > 95% across all scheduled runs
- **Adoption:** Every Webapper team member has deployed at least one agent within 3 months
- **Time-to-agent:** A developer can go from `create-webapper-agent` to a deployed, scheduled agent in under 2 hours
- **Operational value:** At least 5 hours/week of manual work automated by agents within 6 months
