# NestPilot Claude plugin marketplace

Free retirement-decision tools from **NestPilot Foundation**, packaged for Claude. No account
required; all calculations are anonymous, educational estimates.

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

## Zero-install alternative — remote MCP connector

Any Claude surface can use the tools without installing anything:

- **claude.ai / Claude Desktop:** Settings → Connectors → Add custom connector →
  `https://mcp.nestpilot.net/mcp`
- **Claude Code:** `claude mcp add --transport http nestpilot https://mcp.nestpilot.net/mcp`

The connector carries the public tools, usage instructions, and the two guided workflows as MCP
prompts; the plugin adds the auto-triggering skill and the native workflow skills on top.

## About this repository

This repository is a **generated distribution mirror** — it is refreshed automatically from the
NestPilot monorepo whenever the plugin bundle changes. Please do not open pull requests against
the content here; report problems via https://www.nestpilot.org.

For year-by-year drawdown modeling and ongoing plan monitoring, the free planning workspace is at
**nestpilot.org**.
