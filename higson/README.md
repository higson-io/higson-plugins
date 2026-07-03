# Higson plugin for Claude Code

Work with Higson BRMS directly from Claude Code — read and change decision tables,
functions, domain, and flows, run tests, and publish changes, talking in plain English
or Polish. The plugin bundles the **Higson skill** (teaches Claude to work with Higson
safely) and the **MCP server configuration** (connection to your instance) in one package.

## Requirements

- Claude Code (recent version — the `/plugin` command must be available).
- A running **Higson Studio 4.3+** instance (MCP is available from version 4.3).
- The **MCP server enabled** on that instance (an optional server-side feature) — see the
  [Higson docs](https://docs.higson.io/) for how to enable it.
- An **integration token** generated in Studio (see below).

## Installation

```
/plugin marketplace add higson-io/higson-plugins
/plugin install higson@higson-plugins
/reload-plugins
```

- `higson-io/higson-plugins` — the GitHub repo hosting this marketplace.
- `higson` — the plugin name; `higson-plugins` — the marketplace name.
- **`/reload-plugins` is required** after installing — it activates the skill and connects
  the MCP server without restarting the session (or restart Claude Code instead).

On enable, Claude Code **prompts you for two values**:

| Field | What to enter |
|-------|---------------|
| **Higson Studio MCP URL** | e.g. `https://your-instance/api/mcp` (or `http://localhost:8282/api/mcp` for a local dev instance) |
| **Studio integration token** | token from Higson Studio — stored securely (OS keychain / Claude Code secret store), **not** in `settings.json`. Still, use a least-privilege token, not admin. |

That's it — the plugin wires up the `higson` MCP server and loads the skill.

## Getting an integration token

1. Log in to Higson Studio.
2. In the top menu, open **Licenses and tokens** → the **Tokens** tab.
3. Click **+ Add token** and fill in the token details — type **Studio**, the **user**
   whose permissions the token should carry, and an **expiration date**.
4. Copy the generated token value (the long `eyJ…` string) — that is what you paste into
   the plugin's token field.
5. **Least-privilege:** pick a user with only the rights the work needs, **not admin**.
   What the plugin can do over MCP equals what the token's user can do — an admin token
   can, among other things, reject/publish other users' work sessions.

## Usage

After installation, just write naturally (English or Polish), e.g.:

**Explore**
- "what Higson version is this?"
- "show my decision tables" / "pokaż moje tabele decyzyjne"
- "list the functions tagged pricing"

**Read**
- "show the matrix of the PREMIUM table"
- "what does the calculateDiscount function do?"

**Change (asks for consent first)**
- "change the premium for drivers under 25"
- "add a flow that computes a discount"

**Test & publish (asks for confirmation)**
- "run the tests for this table"
- "publish my changes"

The skill activates itself when a Higson topic comes up. On first use it offers a quick,
read-only discovery of the environment (with your consent) and saves notes to
`.higson/knowledge.md` in the current directory to speed up later sessions.

**Safety rules built into the skill:** it reads before changing, shows a diff and asks
for consent before writing, reminds you that a change is not live until `publish`, and
never publishes or rejects sessions without your explicit consent.

## Verification

- `/help` → the `studio` skill (`higson:studio`) should be listed.
- Ask "what Higson version is this?" → Claude calls `higson_get_version`.

## Troubleshooting

| Symptom | Likely cause / fix |
|---------|--------------------|
| Higson tools not available | Plugin not enabled — check `/plugin`. After changing plugin files run `/reload-plugins` or restart. |
| MCP server won't connect | `studio_url` unreachable or wrong. It must be the **MCP** endpoint, e.g. `…/api/mcp`. Confirm the instance is running and reachable from your machine. |
| `401` / `403` from the server | Token missing, expired, or the token's user lacks the needed rights. Generate a fresh token; grant it the permissions the work requires. |
| Changes don't show up in the runtime | Edits sit in an open **work session** until you publish — ask Claude to `publish` (edits are not live on a `SUCCESS` alone). |
| `list_*` returns too much / overflows | Large profile — use filters: `pageSize`, `filterCode`, `filterTags` (functions also `filterTypes`), or `higson_search`. |
| Need to change the URL or token | Reinstall the plugin to re-enter them. The `studio_url` lives in `pluginConfigs` in `~/.claude/settings.json`; the token lives in the secret store, not `settings.json`. |
| Requires Higson 4.3+ | MCP is available from 4.3 — older instances won't expose the MCP endpoint. |

## Support

Higson documentation: https://docs.higson.io/ (versioned per release).
