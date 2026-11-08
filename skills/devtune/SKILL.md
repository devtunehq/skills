---
name: devtune
description: Use when the user asks what AI answers say about their product, how often AI platforms cite or mention them, which competitors get cited instead, AI crawler or referral traffic to their site, what to fix next for AI visibility, whether shipped fixes worked, or to set up DevTune for their product.
---

# DevTune

DevTune measures what AI answer engines tell buyers about a product, ranks the
fixes most likely to change it, and measures each fix after it ships. Each
credential reaches one DevTune project. Answer from DevTune data, not from
general knowledge.

## Connect

Use the first route that applies.

1. **MCP tools.** If `devtune_*` tools are available, use them.
2. **API key.** If `DEVTUNE_API_KEY` is set, call the REST API at
   `https://devtune.ai/api/v2` with the header
   `Authorization: Bearer $DEVTUNE_API_KEY`. Expand the variable in the shell
   so the key stays out of chat, files and printed commands. Every path below
   sits under `/projects/{projectId}`.
3. **Neither.** Connect the DevTune MCP server, which signs in through the
   browser:
   - Claude Code: `claude mcp add --transport http devtune https://devtune.ai/api/mcp`,
     then run `/mcp` and sign in.
   - Codex: `codex plugin marketplace add devtunehq/codex-plugins`, then
     install DevTune from the plugin list.
   - Other clients: add a Streamable HTTP server at
     `https://devtune.ai/api/mcp`, following
     [the client setup](https://docs.devtune.ai/api-reference/mcp-server).

   The user signs in, selects the project and approves the scopes. Scripts, CI
   jobs and clients without OAuth use a project API key from **API Keys** in
   the DevTune account sidebar.
4. **No DevTune account.** Register yourself by following
   https://devtune.ai/auth.md. Ask the user for the domain to track and the
   email they sign up with, draft the project setup, then send them the link
   and code to claim it. The access token you get after the claim works like an
   API key: call the REST API with it as the bearer token.

First read the connection context: `devtune_get_connection_context`, or
`GET /connection` over REST, which also returns the project ID. Tell the user
which project is connected and whether it is paused. A paused project can be
read but not changed until someone resumes it in DevTune.

## Pick the starting read

| The user asks about | MCP tool | REST `GET` |
| --- | --- | --- |
| How visible they are in AI answers | `devtune_get_visibility_summary` | `/visibility/summary` |
| How that changed | `devtune_get_visibility_diff` | `/visibility/diff` |
| Competitors | `devtune_get_competitive_position` | `/competitive/position` |
| Which sites and pages AI cites | `devtune_get_top_citation_domains`, `devtune_get_top_citation_pages` | `/citations/top-domains`, `/citations/top-pages` |
| What an AI platform actually said | `devtune_list_visibility_responses`, then `devtune_get_visibility_response` | `/visibility/responses`, then `/visibility/responses/{executionId}` |
| Topics where competitors are cited and they aren't | `devtune_get_content_gaps`, then `devtune_get_content_gap_evidence` | `/content-gaps/list`, then `/content-gaps/evidence` |
| AI crawlers, answer fetches, AI referrals | `devtune_get_traffic_summary`, `devtune_get_traffic_platforms`, `devtune_get_ai_referrals` | `/traffic/summary`, `/traffic/platforms`, `/traffic/referrals` |
| Pages AI keeps requesting that don't exist | `devtune_get_404_demand` | `/traffic/404-demand` |
| What to fix next | `devtune_get_actions`, then `devtune_get_action_brief` | `/actions/list`, then `/actions/{actionId}/brief` |
| Site readiness for AI (crawlability, `llms.txt`) | `devtune_get_audit_summary`, `devtune_get_audit_findings` | `/audit/summary`, `/audit/findings` |
| Whether shipped fixes worked | `devtune_get_outcomes_ledger` | `/outcomes/ledger` |

Measurement reads take `windowDays` of 30 or 90; Results take 7, 30, 90 or
365. Lists return `pagination.nextCursor`: pass it back as `cursor` with every
other parameter unchanged, and tell the user when you stopped while `hasMore`
was still true. Every other endpoint, its parameters and its required scope are
in the [REST reference](https://docs.devtune.ai/api-reference/overview).

A `403` or a refused tool means the credential lacks a scope or the account
lacks a feature. Read the connection context and tell the user what is
missing. Existing keys and connections never gain scopes added later; the user
edits the key or reconnects.

## Report numbers the way DevTune measures them

- A citation is an AI answer linking to a URL. A mention is the brand named
  without a link. Keep them separate.
- Share of voice compares the user's citations with their configured
  competitors' citations only. Describe it that way, not as a share of
  everything cited.
- Crawls, answer fetches and AI-referred visits are different counts. Report
  each on its own.
- A null value, a day without coverage, or `no_data` means not measured.
  Report it as not measured, never as zero or as an estimate.
- When a result has a range or a `directional` flag, give the range or say the
  sample is small.
- Results are improved, mixed, no clear change, declined, still measuring or
  unmeasured. They show what moved after a fix shipped, not what caused it.
- Keep what DevTune returned separate from your own interpretation.

## Ask before changing anything

- Reads are safe to run without asking.
- Before anything that creates, updates, archives or deletes (prompts, topics,
  brands, actions, webhooks), say what will change and wait for the user to
  confirm.
- Before anything that spends DevTune credits (briefs, drafts, agent runs,
  Agent Picks tests, prompt set analysis), say so and get approval first. When
  a call asks for an approved credit estimate, use only the figure the user
  approved.
- DevTune drafts content and never publishes it. Open a draft pull request only
  when the user asks for one.
- Locked pages are off-limits. Steer suggestions around them and say so.
- The user's explicit instructions take priority over this skill.
