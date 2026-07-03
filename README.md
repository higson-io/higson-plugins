# Higson plugins for Claude Code

A [Claude Code](https://claude.com/claude-code) plugin marketplace for working with
**Higson BRMS** directly from Claude — read and change decision tables, functions, domain
elements and flows, run tests, and publish changes, talking in plain English or Polish.

## Plugins

| Plugin | What it does |
|--------|--------------|
| **`higson`** | Bundles the **Higson skill** (teaches Claude to operate Higson safely) and the **MCP server configuration** (connection to your Studio instance). |

## Installation

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

See the [plugin README](./higson/README.md) for details, token setup, and usage examples.

## Requirements

- **Claude Code** (recent version — the `/plugin` command must be available).
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
