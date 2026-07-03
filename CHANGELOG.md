# Changelog

All notable changes to the **higson** plugin are documented here.
The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and the plugin adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2026-07-03

### Added
- Initial release of the **higson** plugin for Claude Code.
- **Higson skill** — teaches Claude to operate Higson BRMS safely: read before write,
  consent before writing/publishing, work-session awareness, and read-only environment
  discovery cached to `.higson/knowledge.md`.
- **MCP server configuration** (`higson`) — HTTP connection to a Higson Studio instance,
  with user-supplied `studio_url` and integration `token`.

### Requirements
- Higson Studio **4.3+** (MCP is available from 4.3).
