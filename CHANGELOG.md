# Changelog

All notable changes to the **higson** plugin are documented here.
The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and the plugin adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.2.1] - 2026-08-21

### Fixed
- **Skill**: two inline facts added in 1.2.0 were inverted and would have caused the very
  bugs they were meant to prevent — `date.getMonth` is **one**-based (not zero), and a
  failed decision table lookup **throws** `ParameterValueNotFoundException` unless the
  table is flagged nullable (the empty matrix with a null `row()` is the nullable-only
  case).
- **Skill**: dropped `higson.getLong` — no such method.

### Added
- **Skill**: inline essentials for the objects injected into every function body —
  `log`, `str`, `util`, `math`, `date`, `type`, `domain` — none of which may be declared
  or imported, plus two more *Common mistakes* entries.
- **Skill**: inline essentials for `ctx` — `set` throws on an existing path (`with(path,
  value, true)` overwrites), `has` is a flat check that skips sub-contexts, paths are
  case-insensitive, `getFirst` reads a collection, and the `getLocalDatetime` /
  `getLocalDateTime` spelling split.
- **Skill**: value holders as the way to tell "absent" from "zero" —
  `matrix.getHolder(col)`, `ctx.get<Type>Holder(path)`, `type.to<Type>Holder(v)`, whose
  `isNull()` and object getters preserve null where the primitives substitute `0`/`false`.
- **Skill**: further traps — `str.padLeft` appends while `padRight` prepends, `str.format`
  uses `{}` not `%s`, `util.eq` compares numeric-looking strings numerically,
  `calculateHaversineDistance` returns meters, `type.paramValue()` requires `withTypes`,
  a misspelled domain attribute code fails silently, `domain.get(path)` throws where
  `getSafe(path)` returns null, `rows()` is a live view, and the `_dec`/`_num`/…
  converters are Groovy/JavaScript only.

## [1.2.0] - 2026-08-21

### Added
- **Skill**: inline essentials for reading a decision table from a function body —
  `getValue` returns a matrix, the single-value getters return the first cell, and the
  four silent traps (unmatched `row()` yields `null`; `getNumber`/`getBoolean` are
  primitives that read an empty cell as `0.0`/`false`; array/list getters live on a row,
  not the matrix; `isEmpty()` is not `isBlank()`).
- **Skill**: three matching entries in *Common mistakes*, and an explicit instruction to
  read `higson://docs/functions` before writing or editing a function body.

### Fixed
- **Skill**: `higson.getAll(...)` is called out as non-existent. It was being written
  into function bodies from an outdated example in the MCP server's `higson://docs/functions`
  resource; the correct method for multi-row results is `higson.getValue(table, ctx)`.
- **Skill**: `metadata.version` was left at `1.0.0` while the plugin shipped `1.1.0`.

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
  user to the README instead of improvising REST calls); environment discovery is offered by default
  before starting real work — only a single quick lookup skips the offer (a series of
  targeted reads does not).

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
