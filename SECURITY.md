# Security Policy

This policy covers the **higson** Claude Code plugin published in this repository (the
skill and the MCP server configuration). It does **not** cover the Higson product/server
itself — report those through your usual Higson support channel.

## Supported versions

| Version | Supported |
|---------|-----------|
| 1.0.x   | ✅ |
| < 1.0   | ❌ |

## Reporting a vulnerability

Please report vulnerabilities **privately** — do not open a public issue.

Use GitHub's private reporting: go to the **Security** tab of this repository →
**Report a vulnerability** (GitHub Security Advisories). We aim to acknowledge a report
within a few business days and will keep you updated on the fix.

Please include: affected version, a description, and steps to reproduce or a proof of concept.

## Security model & safe use

Things to know when using this plugin:

- **Your integration token is marked `sensitive`** and stored in your OS keychain — or,
  where no keychain is available, in Claude Code's local secret store
  (`~/.claude/.credentials.json`, file mode `0600`). It is **not** written to
  `settings.json`. The plugin's `.mcp.json` contains only a `${user_config.token}`
  placeholder — **no secret is committed to this repo**.
- **Use a least-privilege token, not an admin token.** What the plugin can do over MCP
  equals what the token's user can do. An admin token can, among other things,
  reject or publish **other users'** work sessions.
- **The plugin talks to the Studio instance you configure** (`studio_url`). Your data is
  sent to that instance, not to the plugin authors.
- **Nothing reaches the runtime until you publish.** The bundled skill reads before it
  writes, asks for consent before writing, and never publishes or rejects sessions
  without explicit confirmation.

## Note for maintainers

The "Report a vulnerability" button requires **private vulnerability reporting** to be
enabled in the repository settings (Settings → Security → enable private reporting).
