# NestPilot plugin marketplace

Free retirement-decision tools from **NestPilot Foundation**, packaged as an
[Agent Plugins 1.0](https://agent-plugins.org/) plugin (`nestpilot-retirement/`) with the
Claude Code host files beside it. No account required; all calculations are anonymous,
educational estimates.

## Install (Claude Code / Claude Cowork)

```
/plugin marketplace add javapan2000/nestpilot-claude-plugin
/plugin install nestpilot-retirement@nestpilot
```

(or from a terminal: `claude plugin marketplace add javapan2000/nestpilot-claude-plugin` and
`claude plugin install nestpilot-retirement@nestpilot`, then start a new session).

You get the `nestpilot-retirement-checkup` skill, the guided
`/nestpilot-retirement:retirement-checkup` and `/nestpilot-retirement:retirement-quick-check`
workflows, and the `nestpilot` MCP connector (remote, no local server).

## Install in other Agent Plugins 1.0 hosts

This repository is also a marketplace for the hosts below; each reads its own manifest at the
repository root and installs the same plugin.

- **GitHub Copilot CLI:** `copilot plugin marketplace add javapan2000/nestpilot-claude-plugin`,
  then `copilot plugin install nestpilot-retirement@nestpilot`
- **VS Code (Copilot):** add `"javapan2000/nestpilot-claude-plugin"` to the
  `chat.plugins.marketplaces` setting, then Extensions view → `@agentPlugins` → **Install**
- **Cursor:** Dashboard → Plugins → Team Marketplaces → **Add Marketplace** → Import from Repo
  `javapan2000/nestpilot-claude-plugin`
- **Codex:** `codex plugin marketplace add javapan2000/nestpilot-claude-plugin`, then
  `codex plugin add nestpilot-retirement@nestpilot`

In these hosts you get the `nestpilot-retirement-checkup` skill and the `nestpilot` MCP
connector. The skill routes both the full checkup and single-question flows; the
`/nestpilot-retirement:*` workflow skills are Claude-only.

## Zero-install alternative — remote MCP connector

Any MCP-capable assistant can use the tools without installing anything:

- **claude.ai / Claude Desktop:** Settings → Connectors → Add custom connector →
  `https://mcp.nestpilot.net/mcp`
- **Claude Code:** `claude mcp add --transport http nestpilot https://mcp.nestpilot.net/mcp`
- **Any other host:** add a streamable-HTTP MCP server at `https://mcp.nestpilot.net/mcp`

The connector carries the public tools, usage instructions, and the two guided workflows as MCP
prompts; the plugin adds the auto-triggering skill (and, on Claude, the native workflow skills)
on top.

## About this repository

This repository is a **generated distribution mirror** — it is refreshed automatically from the
NestPilot monorepo whenever the plugin bundle changes. Please do not open pull requests against
the content here; report problems via https://www.nestpilot.org.

For year-by-year drawdown modeling and ongoing plan monitoring, the free planning workspace is at
**nestpilot.org**.
