---
name: Provision digital twins and validate a URL
description: Spin up stateful digital twins of third-party services, then run a browser-agent validation against a deployed app without touching real APIs.
api: mcp/arga-labs-mcp.yml
source: https://docs.argalabs.com/mcp
method: generated
operations:
  - get_twin_catalog
  - create_twin_run
  - get_twin_run
  - create_test_run
  - get_test_run
---

# Provision digital twins and validate a URL

Use the Arga `arga-context` MCP server (or REST API at `https://api.argalabs.com`)
to test an app against stateful twins of third-party services instead of
production. Authenticate with a Bearer `arga_sk_` API key
(`authentication/arga-labs-authentication.yml`).

## Steps

1. **Discover twins.** Call `get_twin_catalog` to list provisionable twin names
   and kinds (e.g. `slack`, `stripe`, `github`, `salesforce`, `notion`,
   `discord`, `jira`, `box`, `dropbox`, `gmail`, `google_calendar`).
2. **Provision twins.** Call `create_twin_run` with a comma-separated `twins`
   value (e.g. `"slack,stripe"`). Optionally pass a `scenario_id` to pre-seed
   twin state, `ttl_minutes` (default 60), and `public`. Note the returned twin
   run ID. On the Free plan you are limited to 1 twin per run.
3. **Wait for ready.** Poll `get_twin_run` with the run ID until each twin
   reports `ready`; read each twin's `base_url`, `admin_url`, `env_vars`, and
   `proxy_token`. Deploy or point your app at those `base_url`s.
4. **Run the validation.** Call `create_test_run` with a natural-language
   `prompt` describing what to test, plus either `start_url` (your deployed app)
   or `sandbox_id`. Reference the same `twins`/`scenario_id` so the run uses the
   seeded twins. A `prompt` is required on the Free plan.
5. **Read results.** Poll `get_test_run` with the returned run ID until a
   terminal status (`complete`, `failed`, or `cancelled`); inspect `passed`,
   `failed`, `total`, `summary`, per-step results, and artifact/screenshot URLs.

## Rules

- Terminal statuses are `complete`, `failed`, `cancelled`; non-terminal include
  `queued`, `running` (validation) and `provisioning`, `ready` (twins). Always
  poll rather than assuming completion.
- Errors arrive as a JSON `detail` string; usage-limit rejections explain the
  cap and remaining quota (`errors/arga-labs-error-codes.yml`). Respect plan
  limits (`lifecycle/arga-labs-lifecycle.yml`).
- Prefer the run-model tools above over legacy aliases (`provision_twins`,
  `start_url_validation`, `get_validation_results`).
