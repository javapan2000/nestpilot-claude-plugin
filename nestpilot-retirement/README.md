# nestpilot-retirement — plugin bundle

One distribution bundle for the anonymous NestPilot Foundation retirement surface. The bundle is
an [Agent Plugins 1.0](https://agent-plugins.org/) package — root `plugin.json` + `skills/` +
`mcp.json` — with per-host compatibility files beside it for the hosts that still read their own
layout. Every host loads the same payload from the same remote server.

| Host | Reads | Loads |
|---|---|---|
| Agent Plugins 1.0 clients — GitHub Copilot (CLI / VS Code / app), Cursor, Kiro | `plugin.json`, `mcp.json`, `skills/` | 17 tools, checkup skill |
| Codex | `.codex-plugin/plugin.json` + `.app.json` (the shipping CLI still installs from these; root `plugin.json` + `mcp.json` are canonical per OpenAI's docs) | `skills/`, `.mcp.json`, ChatGPT app id |
| ChatGPT (Apps SDK) | `.app.json` (app id) + `skills/*/agents/openai.yaml` | `skills/`, remote MCP |
| Claude Code / Cowork | `.claude-plugin/plugin.json` | `skills/` + `claude-skills/`, `.mcp.json` |

The MCP connector is remote streamable-HTTP (`https://mcp.nestpilot.net/mcp`) and requires no
account. Forecast and analysis tools are anonymous and read-only. If the user clicks the planner
CTA, the app creates a temporary, single-use handoff containing the confirmed plan; it does not
save an account plan or execute transactions.

## Layout — portable core vs. compatibility layer

**Portable core** (the standard's closed schemas; transcribed into the packaging test):

- `plugin.json` — identity and metadata only. The standard forbids component paths and client
  fields (`skills`, `mcpServers`, `apps`, `interface`, `hooks`, …) at the top level.
- `mcp.json` — the remote server, `"type": "streamable-http"`.
- `skills/<name>/SKILL.md` — Agent Skills; only immediate children are discovered.

**Compatibility layer** (kept until each host reads the portable files; same metadata, locked by
the packaging test):

- `.claude-plugin/plugin.json` + `.mcp.json` (`"type": "http"`) — Claude Code reads only these; it
  does not implement the standard and ignores the root files.
- `.codex-plugin/plugin.json` — a complete Codex manifest (`skills`, `mcpServers`, `apps`,
  `interface`). OpenAI's docs make the root `plugin.json` canonical and reduce this file to an
  overlay for `interface` + `apps`, but the shipping CLI (0.144.0-alpha.4, observed on PR #1408)
  still installed this file's version (`<version>+codex.<stamp>`), so it stays complete and
  canonical for Codex until an install is observed selecting the root manifest. It is
  deliberately **not** mirrored into `extensions.com.openai`: when that object exists it replaces
  this file wholesale as the source of OpenAI settings, so one copy of the interface block is one
  less thing to drift.
- `.app.json`, `skills/*/agents/openai.yaml` — the ChatGPT app binding.
- `claude-skills/` — the two guided workflows use Claude-only conventions (`$ARGUMENTS`,
  `argument-hint`) and are Claude-scoped by design; in other hosts the
  `nestpilot-retirement-checkup` skill routes the same flows (the server also exposes them as MCP
  prompts, which Claude surfaces and Codex does not yet).

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
dir so the Codex/ChatGPT/standard surface (which reads `./skills/`) stays unchanged.

## Other hosts — the same mirror repo

The mirror carries one marketplace manifest per host family, all pointing at the same bundle
directory (`nestpilot-retirement/`). The commands are the vendors' documented ones; the Claude
path is exercised routinely and the Codex path was exercised once (Codex review of PR #1408, CLI
0.144.0-alpha.4, from a staged copy of the mirror) — smoke-test Copilot and Cursor before pointing
users at them.

| Host | How |
|---|---|
| GitHub Copilot CLI | `copilot plugin marketplace add javapan2000/nestpilot-claude-plugin` → `copilot plugin install nestpilot-retirement@nestpilot` (reads `.claude-plugin/marketplace.json` as its fallback location) |
| VS Code / Copilot | add `"javapan2000/nestpilot-claude-plugin"` to `chat.plugins.marketplaces`, then Extensions view → `@agentPlugins` → Install |
| Cursor | Dashboard → Plugins → Team Marketplaces → Add Marketplace → Import from Repo `javapan2000/nestpilot-claude-plugin` (reads `.cursor-plugin/marketplace.json`) |
| Codex | `codex plugin marketplace add javapan2000/nestpilot-claude-plugin` (reads `.agents/plugins/marketplace.json`) → `codex plugin add nestpilot-retirement@nestpilot` |
| Kiro | powers registry submission — not filed |

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
here must remain byte-identical. Version lockstep across `plugin.json`, both host manifests, the
marketplace entries, and `@nestpilot/mcp-app` is enforced by
`products/nestpilot/apps/mcp/tests/codex-plugin-package.test.ts`, which also transcribes the two
official Agent Plugins schemas — update the bundle and that test together.
