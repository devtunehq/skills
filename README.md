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

## License

MIT. See [LICENSE.md](./LICENSE.md).
