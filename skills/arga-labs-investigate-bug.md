---
name: Investigate a bug across connected sources
description: Trace a bug's root cause by searching every tool a team has connected to Arga (GitHub, Slack, Jira, Linear, Sentry, Grafana, PostHog) from one MCP surface.
api: mcp/arga-labs-mcp.yml
source: https://docs.argalabs.com/mcp
method: generated
operations:
  - list_connected_sources
  - search_context
  - investigate_bug
  - get_source_detail
---

# Investigate a bug across connected sources

Use Arga's context MCP tools to correlate a reported issue across a team's
connected tools without leaving the editor. Authenticate with a Bearer
`arga_sk_` API key (`authentication/arga-labs-authentication.yml`).

## Steps

1. **See what is connected.** Call `list_connected_sources` to confirm which
   providers are available (GitHub, Slack, Jira, Linear, Sentry, Grafana,
   PostHog).
2. **Run the investigation.** Call `investigate_bug` with a free-text
   `description`, and any of `error_message`, `trace_id`, `sentry_issue_id`, and
   `time_window` (default `"last 24 hours"`). It returns a markdown
   investigation report with analysis and linked source references.
3. **Broaden with search.** Where more context is needed, call `search_context`
   with a natural-language `query` (optionally restrict with `sources`, e.g.
   `"github,slack"`), or the targeted `search_github` / `search_slack` /
   `search_sentry` / `search_linear` / `search_grafana` / `search_posthog`
   tools.
4. **Pull specific records.** Call `get_source_detail` with a `source_type` and
   `identifier` to fetch the exact record — e.g. `github_pr` as
   `owner/repo/pr_number`, `jira_issue` as `PROJ-123`, `slack_thread` as
   `channel_id/thread_ts`, `sentry_issue` as the issue ID.

## Rules

- Search tools return up to 8 results sorted by date; refine the query rather
  than expecting exhaustive output.
- `search_posthog` needs at least one of `query` or `hogql`; `hogql` takes
  precedence when both are given.
- Treat the investigation report as a lead to verify against the linked source
  records, not a final verdict.
