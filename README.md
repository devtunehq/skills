# DevTune agent skills

Agent skills for [DevTune](https://devtune.ai), which measures what AI tells
your buyers about your product and helps you change it.

| Skill | Ask your coding agent |
| --- | --- |
| `devtune` | What AI answers say about your product, who gets cited instead, what AI systems fetch from your site, what to fix next, and whether shipped fixes worked. |
| `agent-picks` | Which tool Claude Code, Codex and other coding agents pick when asked to add auth, payments, email or your category, how often they pick yours, and whether a fix moved it. |

## Install

```bash
npx skills add devtunehq/skills
```

Add `--skill devtune` or `--skill agent-picks` to install one.

The skills use the DevTune MCP server when your agent has it, or the REST API
with a project API key in `DEVTUNE_API_KEY`. Without either, they walk you
through connecting. Without a DevTune account, your agent drafts the project
and you sign up and approve it in DevTune.

- [MCP server](https://docs.devtune.ai/api-reference/mcp-server)
- [REST API](https://docs.devtune.ai/api-reference/overview)
- [Agent Picks API and MCP](https://docs.devtune.ai/agent-picks/api-and-mcp)

## Hermes Agent

This repository is also a Hermes plugin: both skills plus the DevTune MCP
server.

1. Create a project API key under **API Keys** in DevTune.
2. Add it to `~/.hermes/.env`:

   ```bash
   DEVTUNE_API_KEY=dtk_live_your_key_here
   ```

3. Install and enable the plugin, then start a new session:

   ```bash
   hermes plugins install devtune
   hermes plugins enable devtune
   ```

What the plugin does on your machine and over the network:

- Every tool call goes to DevTune's hosted MCP server at `devtune.ai`, sending
  your API key as a bearer header. The tools are served by that server and
  follow the key's project and scopes.
- Some tools change project data (prompts, competitors, actions) or spend
  DevTune credits (briefs, drafts, Agent Picks tests). The plugin marks the
  server untrusted, so Hermes asks before every tool not annotated read-only
  and refuses them in unattended runs. Agent Picks tests also need a quote
  you approve before they start.
- No local code, hooks, background processes or telemetry. The plugin stores
  nothing beyond what Hermes keeps for any MCP server.

## License

MIT. See [LICENSE.md](./LICENSE.md).
