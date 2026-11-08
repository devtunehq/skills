# DevTune agent skills

Agent skills for [DevTune](https://devtune.ai): ask your coding agent what AI
answers say about your product, who gets cited instead, what AI systems fetch
from your site, what to fix next, and whether shipped fixes worked.

## Install

```bash
npx skills add devtunehq/skills
```

The skill uses the DevTune MCP server when your agent has it, or the REST API
with a project API key in `DEVTUNE_API_KEY`. Without either, it walks you
through connecting. Without a DevTune account, your agent drafts the project
and you sign up and approve it in DevTune.

- [MCP server](https://docs.devtune.ai/api-reference/mcp-server)
- [REST API](https://docs.devtune.ai/api-reference/overview)

## License

MIT. See [LICENSE.md](./LICENSE.md).
