# Higson plugin for Claude Code

Work with Higson BRMS directly from Claude Code — read and change decision tables,
functions, domain, and flows, run tests, and publish changes, talking in plain English
or Polish. The plugin bundles the **Higson skill** (teaches Claude to work with Higson
safely) and the **MCP server configuration** (connection to your instance) in one package.

## Requirements

- Claude Code (recent version — the `/plugin` command must be available).
- A running **Higson Studio 4.3+** instance (MCP is available from version 4.3).
- An **integration token** generated in Studio (see below).

## Installation

```
/plugin marketplace add higson-io/higson-plugins
/plugin install higson@higson-plugins
```

- `higson-io/higson-plugins` — the GitHub repo hosting this marketplace.
- `higson` — the plugin name; `higson-plugins` — the marketplace name.

For local development/testing you can also load the plugin directly:
```
claude --plugin-dir /path/to/higson
```
(Note: `--plugin-dir` does not prompt for config — it uses defaults. Use marketplace install to be prompted for the token.)

On enable, Claude Code **prompts you for two values**:

| Field | What to enter |
|-------|---------------|
| **Higson Studio MCP URL** | e.g. `http://localhost:8282/api/mcp` or `https://your-instance/api/mcp` |
| **Studio integration token** | token from Higson Studio (stored in `settings.json` as plain text — use a least-privilege token, not admin) |

That's it — the plugin wires up the `higson` MCP server and loads the skill.

## Getting an integration token

1. Log in to Higson Studio.
2. Generate an **integration token** (STUDIO_INTEGRATION_TOKEN) in settings / the tokens UI.
3. **Recommendation (least-privilege):** use a user with only the rights the work needs,
   not admin. What the plugin can do via MCP equals what the token's user can do — an
   admin token can, among other things, reject/publish other users' work sessions.

## Usage

After installation, just write naturally, e.g.:
- "show my decision tables"
- "change the premium for drivers under 25"
- "add a flow that computes a discount"

The skill activates itself when a Higson topic comes up. On first use it offers a quick,
read-only discovery of the environment (with your consent) and saves notes to
`.higson/knowledge.md` in the current directory to speed up later sessions.

**Safety rules built into the skill:** it reads before changing, shows a diff and asks
for consent before writing, reminds you that a change is not live until `publish`, and
never publishes or rejects sessions without your explicit consent.

## Verification

- `/help` → the `higson` skill should be listed.
- Ask "what Higson version is this?" → Claude calls `higson_get_version`.

## Support

Higson documentation: https://docs.higson.io/ (versioned per release).
