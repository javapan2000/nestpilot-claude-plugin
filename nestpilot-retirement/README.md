# nestpilot-retirement — plugin bundle

One distribution bundle for the anonymous NestPilot Foundation retirement surface, serving three
hosts from the same payload:

| Host | Manifest | Loads |
|---|---|---|
| ChatGPT (Apps SDK) | `.app.json` (app id) + `skills/*/agents/openai.yaml` | `skills/`, remote MCP |
| Codex | `.codex-plugin/plugin.json` | `skills/`, `.mcp.json`, `.app.json` |
| Claude Code / Cowork | `.claude-plugin/plugin.json` | `skills/` + `claude-skills/`, `.mcp.json` |

The MCP connector is remote streamable-HTTP (`https://mcp.nestpilot.net/mcp`) and requires no
account. Forecast and analysis tools are anonymous and read-only. If the user clicks the planner
CTA, the app creates a temporary, single-use handoff containing the confirmed plan; it does not
save an account plan or execute transactions.

## Claude Code / Cowork install

**Public channel** (anyone, no repo access) — the generated mirror repo
`javapan2000/nestpilot-claude-plugin`, auto-published by `publish-claude-plugin.yml` from this
bundle plus `../public-marketplace/`:

```
/plugin marketplace add javapan2000/nestpilot-claude-plugin
/plugin install nestpilot-retirement@nestpilot
```

**Dev channel** (this monorepo checkout) — the repo root is a Claude Code marketplace
(`.claude-plugin/marketplace.json`, marketplace name `nestpilot-local`):

```
/plugin marketplace add <path-or-github-ref-of-this-repo>
/plugin install nestpilot-retirement@nestpilot-local
```

(or the `claude plugin ...` CLI equivalents from a terminal, then start a new session so the
plugin loads).

Workflows once installed:

- `/nestpilot-retirement:retirement-checkup` gathers and confirms one baseline, then runs only the
  retirement-age, Social Security, Roth, or Medicare follow-ups justified by the request.
- `/nestpilot-retirement:retirement-quick-check` selects the narrowest matching public tool for one
  focused question.

Both workflows ship as **skills** in `claude-skills/` (declared via the manifest's `skills` field,
additive to the default `skills/` scan). They are deliberately NOT plugin `commands/`: command
files never reach the Skill tool in Cowork sessions, and they are kept out of the shared `skills/`
dir so the Codex/ChatGPT surface (which reads `./skills/`) stays unchanged.

## Zero-install option — remote MCP connector

The same server that backs this plugin is a public remote MCP endpoint; any Claude surface can
connect directly, with no repo checkout and no plugin install:

- **claude.ai / Claude Desktop:** Settings → Connectors → Add custom connector →
  `https://mcp.nestpilot.net/mcp` (anonymous, no auth).
- **Claude Code:** `claude mcp add --transport http nestpilot https://mcp.nestpilot.net/mcp`

The connector carries the 17 public tools, the server usage instructions, and the two guided
workflows as MCP prompts (`retirement-checkup` / `retirement-quick-check`, surfaced as
`/mcp__nestpilot__*` commands in Claude Code). What only the plugin adds: the auto-triggering
`nestpilot-retirement-checkup` skill (domain sequencing + reference files) and the
Claude-native `/nestpilot-retirement:*` skills.

## Parity rules

The canonical public skill source is
`products/nestpilot/apps/mcp/openai-skills/nestpilot-retirement-checkup`; the copy under `skills/`
here must remain byte-identical. Version lockstep with `@nestpilot/mcp-app` and the marketplace
entries is enforced by `products/nestpilot/apps/mcp/tests/codex-plugin-package.test.ts` — update
the bundle and that test together.
