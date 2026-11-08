---
name: agent-picks
description: Use when the user asks which tool or library coding agents (Claude Code, Codex and others) pick or install when asked to add a capability such as auth, payments or email, how often agents pick their product over competitors, why they lose those picks, or to test or re-test agent picks with DevTune.
---

# DevTune Agent Picks

Agent Picks measures which tool a coding agent picks when asked to add a
capability to an app. DevTune runs each request in a fresh test app and records
the packages and code the agent adds. Each scenario is one test app, request,
agent and model. Answer from DevTune data, not from general knowledge.

## Connect

Use the first route that applies.

1. **MCP tools.** If `devtune_*agent_picks*` tools are available, use them.
2. **API key.** If `DEVTUNE_API_KEY` is set, call `https://devtune.ai/api/v2`
   with the header `Authorization: Bearer $DEVTUNE_API_KEY`. Expand the
   variable in the shell so the key stays out of chat, files and printed
   commands. `GET /connection` returns the project ID; the paths below sit
   under `/projects/{projectId}`.
3. **Neither.** Connect the DevTune MCP server at `https://devtune.ai/api/mcp`,
   which signs in through the browser. Claude Code:
   `claude mcp add --transport http devtune https://devtune.ai/api/mcp`, then
   `/mcp`. Other clients:
   [the client setup](https://docs.devtune.ai/api-reference/mcp-server).
4. **No DevTune account.** Register yourself by following
   https://devtune.ai/auth.md with `product` set to `agent-picks`. Ask the user
   for the domain and the email they sign up with, draft the setup, then send
   them the link and code to claim it. Use the access token you get after the
   claim as the bearer token.

A project can have several setups, one per category or custom job. With more
than one, pass `setup` (its `id` or category slug) on every call; a refusal with
`agent_picks_setup_required` lists them in `details.setups`.

## Pick the starting read

| The user asks about | MCP tool | REST `GET` |
| --- | --- | --- |
| Pick and install rates from the latest test | `devtune_get_agent_picks_summary` | `/agent-picks/summary` |
| Results by test app, request, agent and model | `devtune_get_agent_picks_cells` | `/agent-picks/cells` |
| Trend across tests | `devtune_get_agent_picks_history` | `/agent-picks/history` |
| What happened in individual runs | `devtune_list_agent_picks_runs`, then `devtune_get_agent_picks_run_trace` | `/agent-picks/runs`, then `/agent-picks/runs/{runId}/trace` |
| Losing scenarios and what to fix | `devtune_get_agent_picks_actions` | `/agent-picks/actions` |
| A test's status and credits | `devtune_get_agent_picks_test` | `/agent-picks/test?test_id=` |
| The schedule | `devtune_get_agent_picks_schedule` | `/agent-picks/schedule` |
| Categories, tools, test apps and models to set up | `devtune_get_agent_picks_catalogue` | `/setup/catalogue/agent-picks` |

Fields, parameters and refusal reasons are in
[Agent Picks API and MCP](https://docs.devtune.ai/agent-picks/api-and-mcp).

## Report picks the way DevTune measures them

- Give every rate with its likely range (`rate_low`, `rate_high`) and its
  coverage. Rates count completed runs only.
- Pick rate and install rate are different numbers. Report both, side by side.
- **Built its own** and **No provider chosen** are outcomes like any tool. Keep
  them in rankings; never fold them into a competitor.
- A `null` rate, `measured: false` or an unfinished test is not measured.
  Report it that way, never as zero.
- When `test.scope` is `public`, the figures come from the category's public
  test, not the user's own.
- Compare two tests only when their `comparison_key` values match, and trends
  only while `judging_key` stays the same.
- A pick records what the agent added. It doesn't prove the integration works
  or explain why the agent chose it. A re-test shows what moved after a fix,
  not that the fix caused it.

## Ask before spending or changing

- Reads are safe to run without asking.
- A paid test, re-test or schedule spends credits. Quote first
  (`devtune_quote_agent_picks_test` or `POST /agent-picks/tests/quote`), show
  the user `estimate.high` in credits, and start only after they approve, with
  `max_credits` set to the amount they approved. Each start needs a fresh
  quote; a quote expires after 15 minutes.
- The free starter test can be used once per work domain. Ask before starting
  it.
- Before saving or clearing a setup, changing models, or cancelling a test,
  say what will change and wait for the user to confirm.
- DevTune drafts fixes and never publishes them. Locked pages are off-limits.
- The user's explicit instructions take priority over this skill.
