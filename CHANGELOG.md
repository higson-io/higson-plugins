# Changelog

All notable changes to the **higson** plugin are documented here.
The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and the plugin adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.1.0] - 2026-07-13

### Added
- **OpenAI Codex support** — the plugin now also installs in Codex
  (`codex plugin marketplace add higson-io/higson-plugins`): a second manifest
  (`.codex-plugin/`) alongside the Claude Code one, same canonical skill.
- README: per-agent installation sections (Claude Code / Codex) and
  Codex-specific troubleshooting entries.

### Changed
- **Skill**: tool references are agent-neutral (`higson_*` — agents may add their own
  prefix); new no-MCP fallback (with no `higson_*` tools available, the agent points the
  user to the README instead of improvising REST calls); environment discovery is offered
  only for tasks beyond a quick lookup.

### Notes
- In Codex the MCP server is connected manually
  (`codex mcp add higson --url … --bearer-token-env-var HIGSON_MCP_TOKEN`) —
  Codex has no equivalent of Claude Code's install-time URL/token form, and the plugin
  deliberately ships no default URL.

## [1.0.0] - 2026-07-03

### Added
- Initial release of the **higson** plugin for Claude Code.
- **Higson skill** — teaches Claude to operate Higson BRMS safely: read before write,
  consent before writing/publishing, work-session awareness, and read-only environment
  discovery cached to `.higson/knowledge.md`.
- **MCP server configuration** (`higson`) — HTTP connection to a Higson Studio instance,
  with user-supplied `studio_url` and integration `token`.
- The integration `token` is marked **`sensitive`** — stored in the OS keychain / Claude
  Code secret store, never in plain text in `settings.json`.

### Requirements
- Higson Studio **4.3+** (MCP is available from 4.3).
