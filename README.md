# Higson plugins for Claude Code and Codex

Work with Higson BRMS directly from your coding agent —
[Claude Code](https://claude.com/claude-code) or [OpenAI Codex](https://developers.openai.com/codex/) —
read and change decision tables, functions, domain, and flows, run tests, and publish
changes, talking in plain English or Polish. On first use, the skill offers a read-only
discovery of your Studio instance — reading the version, domains, decision tables, and
functions configured there — and saves the results to `.higson/knowledge.md`, so the
agent understands your business configuration from the start without re-querying the
instance every session. The plugin bundles the Higson skill (teaches the agent to work
with Higson safely) and the MCP server configuration (connection to your instance) in
one package.

## Plugins

| Plugin | What it does |
|--------|--------------|
| **`higson`** | Bundles the **Higson skill** (teaches the agent to operate Higson safely) and the **MCP server configuration** (connection to your Studio instance). |

## Installation

<details>
<summary><h3>Claude Code</h3></summary>
<table><tr><td width="1000">

```
/plugin marketplace add higson-io/higson-plugins
/plugin install higson@higson-plugins
/reload-plugins
```

**`/reload-plugins` is required** after install — it activates the skill and connects the
MCP server in the current session (otherwise restart Claude Code).

On enable, Claude Code prompts you for:

| Field | Value |
|-------|-------|
| **Higson Studio MCP URL** | e.g. `https://your-instance/api/mcp` (or `http://localhost:8282/api/mcp` for a local dev instance) |
| **Studio integration token** | a token generated in Higson Studio — **use a least-privilege user, not admin** (stored securely in your OS keychain / Claude Code secret store, not in `settings.json`) |

</td></tr></table>
</details>

<details>
<summary><h3>Codex</h3></summary>
<table><tr><td width="1000">

```bash
codex plugin marketplace add higson-io/higson-plugins
codex plugin add higson@higson-plugins
export HIGSON_MCP_TOKEN='<your integration token>'
codex mcp add higson --url https://your-instance/api/mcp --bearer-token-env-var HIGSON_MCP_TOKEN
```

Codex has no install-time configuration prompt, so the last two lines connect the MCP
server: the token comes from the environment variable, the URL is your instance's MCP
endpoint.

</td></tr></table>
</details>

See the [plugin README](./higson/README.md) for details, token setup, and usage examples.

## Requirements

- **Claude Code** (recent version — the `/plugin` command must be available), or
  **Codex CLI** (recent version — with `codex plugin` support).
- A running **Higson Studio 4.3+** instance (MCP is available from version 4.3).
- The **MCP server enabled** on that instance (an optional server-side feature) — see the
  [Higson docs](https://docs.higson.io/) for how to enable it.
- An **integration token** generated in Studio.

## Usage

After installation, just write naturally — the skill activates itself when a Higson topic
comes up:

- *"show my decision tables"*
- *"change the premium for drivers under 25"*
- *"add a flow that computes a discount"*

The skill reads before it changes, asks for consent before writing, and never publishes or
rejects work sessions without your explicit confirmation.

## Documentation

Higson documentation: https://docs.higson.io/ (versioned per release).
