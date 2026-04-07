💁‍♂️ Problem/Context

CloudSee Drive has no programmatic access layer. Every interaction requires a human in the browser. This blocks three high-value opportunities: (1) customers cannot automate S3 file management workflows, (2) AI assistants like Claude cannot interact with CloudSee Drive on behalf of users, and (3) we cannot list a connector in the Claude Marketplace, which is a growing distribution channel for SaaS tools.

This initiative delivers a three-phase rollout: a public REST API documented with Swagger/OpenAPI, an open-source MCP (Model Context Protocol) server that wraps the API, and a published connector in the Claude Marketplace.

📖 User Story

As a CloudSee Drive customer, I want a public REST API so I can automate file management workflows (upload, download, search, share) without using the browser UI.

As a developer building AI-powered tools, I want an MCP server for CloudSee Drive so Claude (or other LLM agents) can browse, search, and manage S3 files on my behalf through natural language.

As an Anthropic Claude user, I want a CloudSee Drive connector in the Claude Marketplace so I can connect my S3 storage to Claude with one click and manage files conversationally.

🎨 Design

Phase 1 — Public REST API: Swagger/OpenAPI 3.0 interactive docs at /api/docs. API surface covers all existing backend microservices (User API, Storage API, Fast Bucket Service, Metric Service, Task Manager, Zipper Task). Auth via API Gateway API key + secret. Versioned at /v1/. Rate limiting per API key via API Gateway usage plans.

Phase 2 — Open-Source MCP Server: TypeScript MCP server published to NPM and GitHub (open source). Wraps the public REST API (single auth model). Tools scoped to safe, user-facing operations with guardrails (read-heavy, confirmations for destructive ops). Tools include: list_buckets, browse_folder, search_files, get_file_metadata, download_file, upload_file, create_share_link, list_favorites, manage_tags. Initially built for Claude via MCP, with future support planned for other LLM platforms (e.g., OpenAI function calling, Google Gemini).

Phase 3 — Claude Marketplace Connector: Packaged MCP server as a one-click installable connector. OAuth-based connection flow. Hosted infrastructure managed by Webapper. Future: Adapt connector for other AI marketplaces as they emerge.

🉑 Acceptance Criteria

Phase 1 (Public REST API):

Given a valid API key and secret, When a developer calls any documented endpoint, Then the API returns the correct response with proper status codes.

Given the API is deployed, When a developer visits /api/docs, Then they see interactive Swagger documentation covering all endpoints.

Given rate limiting is configured, When a client exceeds their plan limits, Then the API returns 429 with retry-after headers.

All existing UI functionality is accessible via API endpoints.

Phase 2 (MCP Server):

Given a developer installs the MCP package, When they configure it with their API key, Then Claude can list buckets, search files, upload/download, and create share links.

Given a destructive operation is requested, When the MCP server processes it, Then it requires explicit user confirmation before executing.

The MCP server is published as open source on GitHub with MIT license.

Phase 3 (Marketplace Connector):

Given a Claude user installs the connector, When they authenticate with CloudSee Drive, Then they can manage their S3 files through natural language in Claude.

The connector is listed and discoverable in the Claude Marketplace.

🙅‍♂️ Out of Scope

GraphQL API (REST only for v1).

Mobile SDKs or native client libraries.

Webhook/event streaming (future consideration).

Admin-level operations in the MCP layer (API-only).

White-label or multi-tenant API gateway.

Real-time file sync or watch capabilities.

🙈 User Roles

API keys are scoped to the same RBAC model as the UI: Regular User, Admin, Owner Admin. API key inherits the permissions of the user who generated it. MCP server operates within the permissions of the connected API key. Marketplace connector uses OAuth to map to an existing CSD user account.

❓ Other Questions to Consider

Do we need separate rate limit tiers (free trial vs paid)?

Should API access be an add-on or included in all plans?

What is Anthropic's current process and timeline for Marketplace connector submissions?

Do we need a developer portal beyond Swagger docs (e.g., API key management UI, usage dashboard)?

What is the timeline/effort to adapt the MCP server for OpenAI function calling and other LLM platforms?

How do we handle large file uploads/downloads through the MCP layer?

📅 Deadline?

No hard external deadline. Target: Phase 1 (API) in Q3 2026, Phase 2 (MCP) in Q4 2026, Phase 3 (Marketplace) in Q1 2027. Timeline depends on Anthropic Marketplace availability and submission process.
