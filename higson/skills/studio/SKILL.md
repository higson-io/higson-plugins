---
name: studio
description: >-
  Operate a Higson BRMS instance through its MCP server — explore an unfamiliar
  Higson environment, or read/change/test/publish decision tables, Groovy/Python
  functions, domain elements and attributes, context types, or flows. Use whenever
  the user mentions Higson, BRMS, business rules, rule publishing, or (PL) tabela
  decyzyjna, funkcja, przepływ (flow), profil, publikacja, składka, stawka, taryfa,
  reguła cenowa. Also use for pricing/rating/eligibility logic backed by Higson.
metadata:
  version: 1.2.1
  docs_url: "https://docs.higson.io/{version}/"
---

# Higson BRMS

Higson is a Business Rules Management System. Business logic — a **Context** (input
data model), a **Domain** model, **Decision Tables**, **Functions** (Groovy/Python),
and **Flows** — is configured in Higson Studio and executed by the Higson Runtime, so
logic changes without redeploying application code. You operate it through an MCP
server (tools named `higson_*`; your agent may add its own prefix, e.g.
`mcp__higson__higson_get_version` in Claude Code). Be a safe Higson operator: learn
the specific environment, don't assume its business domain.

**Core principle:** *Read before you write. Nothing reaches the runtime until you
publish. Never write or publish without the user's explicit consent.*

## When to use / when NOT

**Use** for any task touching a Higson instance: exploring it, reading/editing
decision tables, functions, domain elements/attributes, context types, flows, tests,
or publishing changes.

**Do NOT use** for: general Groovy/Python/programming questions unrelated to Higson
configuration; tasks that don't touch Higson's decision tables, functions, domain,
flows, or tests. If in doubt whether the environment is Higson, ask.

## Startup (run once per session)

If no `higson_*` tools are available, the Higson MCP server is not connected. Do not
improvise a workaround (no direct REST calls, no logging in with curl) — tell the user
the server is not set up and point them to the **Installation** section of the plugin
README (https://github.com/higson-io/higson-plugins) for their agent, then stop.

1. `higson_get_version` → note the Studio version.
2. **Pick the docs version.** Docs live at `https://docs.higson.io/<MAJOR.MINOR>/` and
   ship with each release. If the running version is a dev build (e.g. `4.4.0-SNAPSHOT`)
   or otherwise unpublished, **do not assume** — step down to the nearest *published*
   version (check it exists) and tell the user docs may lag the running build. MCP
   exists from **4.3 onward**, so you are on 4.3+.
3. Check `.higson/knowledge.md` in the current working directory. If present, read it
   and use it as environment context — skip discovery.
4. If absent, make the discovery offer (see below) **in your first answer that uses MCP
   data — always, no exceptions, no judging whether the task is "big enough"**. Answer
   the user's question first, then append one line, e.g.: *"I have no cached knowledge of
   this environment — I can run a read-only, environment-wide discovery (a handful of MCP
   calls) and save notes to `.higson/knowledge.md` to speed up future sessions. Want me
   to?"* The offer covers the **whole environment**, not just the profile under
   discussion — the cache must serve future sessions on any profile. Make it **once per
   session**: if the user declines or ignores it, work against live MCP without a cache
   and do not repeat the offer. Never scan unprompted.

## Environment discovery (consent-gated)

You will not know a specific instance's business domain — learn it, don't assume it.

**When to offer discovery:** whenever `.higson/knowledge.md` is absent — once per
session, appended to your first MCP-based answer (see Startup). It pays off most for an
**unfamiliar environment you will revisit** — it builds context and speeds up later
sessions. Discovery is **never required to do a task** — you can always work against
live MCP, finding just what the task needs. Offer it, never force it; and keep the
cache fresh, because a stale `.higson/knowledge.md` misleads (the live MCP is the
source of truth, the cache is only notes).

1. **Check the cache first.** If `.higson/knowledge.md` exists, read it and use it as
   context — skip discovery.
2. **No cache → ask before scanning.** Discovery is several-to-a-dozen MCP calls, so
   say something like: *"I have no cached knowledge of this Higson environment. I can
   explore it read-only (a handful of MCP calls) and save a summary to
   `.higson/knowledge.md`. Proceed?"* Do not scan unprompted; if consent is declined,
   work against live MCP calls without a cache.
3. **On consent, probe read-only, broad → narrow — environment-wide, not one profile**
   (give the profile you are currently working with a deeper pass, but map them all):
   `higson_list_context_types` +
   `higson_get_context_type_paths`; `higson_get_domain_configuration_tree`; a few
   representative decision tables / functions (`higson_list_decision_tables` /
   `higson_list_functions` with `pageSize`/`filterCode`/`filterTags`, then
   `higson_get_decision_table` / `higson_get_function`); `higson_list_flows`,
   `higson_list_tests`. Note the active profile and version.
4. **Large environments:** a profile can hold hundreds of tables/functions, and raw
   `higson_list_decision_tables` / `list_functions` may return 100k+ characters and
   overflow. Do **not** dump them raw. Prefer `higson_search`, or filter by
   name/tags; if you must traverse everything, summarize via a sub-agent so the bulk
   stays out of context. Note the profile size.
5. **Write `.higson/knowledge.md`** (per-project cache, not part of this skill) using
   this template; update it whenever you learn something new in later sessions:

```markdown
# Higson environment knowledge
Environment: <studio url / how connected>   Profile: <profileCode>   Discovered: <YYYY-MM-DD>
Higson version: <MAJOR.MINOR>   Docs: https://docs.higson.io/<MAJOR.MINOR>/

## Context (input data model)
- key types and notable paths (e.g. quote.driver.age)

## Domain (products / attributes)
- hierarchy of collections/elements; notable attributes and their valueType

## Decision tables
- <code>: purpose, IN columns + matchers, OUT columns

## Functions
- <code>: purpose, tables/functions it calls, return

## Flows
- <code>: purpose, tables/functions it orchestrates, return

## Tests
- name: what behavior it documents; the element it targets

## Naming conventions & gotchas
- observed naming patterns; anything surprising. Known: profileCode is case-sensitive;
  element name/description/tags may be unreliable placeholders; watch for test/QA artifacts.
```

## Write-safety rules (non-negotiable)

**Tool categories — respect each tool's annotations.** Every MCP tool carries
`readOnlyHint` / `destructiveHint`:
- `readOnlyHint: true` (get/list/search/export) — **use freely, no need to ask**.
- not read-only — it modifies state → **consent first**.
- `destructiveHint: true` (delete/publish/reject/import) — **explicit confirmation**.

If a tool lacks annotations, fall back to the name: `get_*`/`list_*`/`search` = read;
`set_*`/`update_*`/`create_*`/`delete_*`/`publish`/`reject`/`import` = write.

1. **Read current state before any write** (`higson_get_*` the element/table/function).
2. **Prefer `higson_set_domain_attribute` over `higson_update_domain_element`.**
   `set_domain_attribute` changes one attribute safely (reads, patches, writes back).
   `update_domain_element` **overwrites the whole element** — every field you omit is
   lost. Use it only for multi-attribute changes, after reading the full element and
   sending it back complete.
3. **A successful write is NOT live.** Edits sit in an open **work session**. Nothing
   reaches the runtime until `higson_publish_changes`. A `SUCCESS` on `set_*`/`update_*`
   does not mean it is in effect.
4. **Consent before writing, and again before publishing.** State exactly what you will
   change and why; wait for "yes". Publishing and any destructive op need their own
   confirmation.
5. **Undo carefully — don't reject to fix one thing.** `higson_reject_changes` discards
   pending work-session changes and is not reversible. Never call it on your own
   initiative. To undo a single change, **restore the previous value** with
   `set_domain_attribute` instead. If a reject is genuinely wanted, show the user
   `higson_list_work_sessions` first and get explicit consent.
6. Verify after publish with `higson_run_test` (or re-read) when tests exist. Match
   existing naming conventions.

## Essential facts (keep these inline)

These are **minimal inline copies** for safety; the **source of truth is the linked
resource**. If you update a fact here, update the resource too (and vice-versa).

**Model:** Context (input, dotted paths like `quote.driver.age`) · Domain Definition
(type schema) vs Domain Configuration (element instances, paths like
`/PLANS[FULL]/COVERAGES[BI]/`) · Decision Table (rows: IN conditions → OUT results) ·
Function (Groovy/Python) · Flow (visual graph) · Test · **Profile** (`profileCode`
case-sensitive) · **Work Session** (edits are not live until published).
*(full: `higson://docs/overview`, `.../context`, `.../domain`)*

**Decision-table `between` matcher format:** a range cell is a **single string**
`"18 - 25"` — space-dash-space. Not `18-25`, not `18:25`. Wrong format = validation
error / column shift. `*` in a cell = matches anything incl. null (default row);
`* - *` = unbounded range, NOT null-safe.
*(full matchers & flags: `higson://docs/decision-tables`)*

**Attribute `valueType` (5 kinds)** — how a domain attribute is sourced:
`LITERAL` (fixed value), `FUNCTION` (`$f`), `UTIL_FUNCTION` (`$u`), `PARAMETER`
(`$p`, → decision table), `FLOW` (`$w`). Read the element before editing: changing a
`LITERAL` won't affect behavior if the attribute is actually `PARAMETER`/`FUNCTION`/
`FLOW` — the table/function/flow drives it. Pass `valueType` to `set_domain_attribute`.
*(full: `higson://docs/domain`)*

**Function body — reading a decision table:** the single-value getters
(`higson.getDecimal`/`getString`/`getInteger`/…) return the first cell;
**`higson.getValue(table, ctx)`** returns a **matrix** — every row matching the context.
**There is no `getAll`, and no `higson.getLong`.** On no match the behaviour depends on the
table's **nullable** flag (off by default): non-nullable **throws**
`ParameterValueNotFoundException`; nullable returns an empty matrix whose `row()` yields
**`null`** instead of throwing — while `row(99)` out of range *does* throw, so guard with
`isEmpty()` before addressing a row. `getNumber`/`getBoolean` are primitives, so an empty
cell reads as `0.0`/`false` with no error — use `getDecimal`/`getInteger` for nullable
columns, or a **holder** (`matrix.getHolder(col)`, `ctx.get<Type>Holder(path)`,
`type.to<Type>Holder(v)`) whose `isNull()` and object getters keep absent apart from zero. Array/list getters (`getStringList`, …) live on a **row**, not on the matrix.
`rows()` is a **live view**: `rows().sort { }` reorders the matrix itself — pass
`sort(false)`. A matrix from `callValue` is never the empty one, so check `size()` there.
*(full API, addressing, collection idioms: `higson://docs/functions`)*

**Function body — built-in objects:** besides `ctx` and `higson`, a function is handed
`log` (slf4j, `{}` placeholders), `str`, `util`, `math`, `date`, `type` and `domain` —
never declare or import them. Reach for them before hand-rolling a helper; null-safe
versions of most string, date and comparison work already exist. What bites: `date.getMonth` is **one-based**, and a `date` field getter returns `0` when it
cannot parse the input; `str.padLeft` **appends** and `padRight` **prepends** (they name the
alignment, not the pad side); `str.format` uses slf4j `{}` anchors, not `%s`; `util.eq`
compares two numeric-looking strings numerically, so `'10'` equals `'10.0'`;
`calculateHaversineDistance` returns **meters**; `domain.get(path)` **throws** where
`getSafe(path)` returns `null`, dynamic attributes need `ctx`, and a misspelled attribute
code returns `null`/`0.0`/`false` silently; `domain` exists only when the domain cache is
enabled; `type.paramValue()` needs `withTypes` as well as `withCodes`; and `util` is the
Java helper object, **not** the `$u` Util Functions of Domain Configuration.
The `_dec`/`_num`/`_int`/`_str`/`_bool`/`_date` converters are Groovy/JavaScript only —
Python functions use `type.getDecimal(x)` and friends.
**Function body — the context:** `ctx.set(path, value)` **throws when the path already
exists** — use `ctx.with(path, value, true)` to overwrite. `ctx.has(key)` is a **flat** key
check that does not walk sub-contexts, so it answers `false` for nested paths `get` resolves
fine. Paths are case-insensitive (keys are lowercased). `ctx.getFirst(path)` reads the first
element of a collection. Note the spelling: `ctx.getLocalDatetime` (lowercase `t`) versus
`getLocalDateTime` on `higson` and `type`. Avoid `ctx.get(key, SomeClass)` — it casts
without converting.
*(full method lists: `higson://docs/functions`)*

## Deeper knowledge — read on demand from MCP resources

The server ships versioned docs as MCP resources. When unsure about syntax, flags, or
tool semantics, read the relevant one instead of guessing —
`ReadMcpResourceTool(server="higson", uri="higson://docs/<topic>")`:

| Topic | uri |
|-------|-----|
| Overview / architecture | `higson://docs/overview` |
| Context (input model) | `higson://docs/context` |
| Domain (definition/config) | `higson://docs/domain` |
| Decision tables (all matchers, flags, sorts, alias) | `higson://docs/decision-tables` |
| Functions (Groovy API, matrix results, built-in objects, traps) | `higson://docs/functions` |
| Flows | `higson://docs/flows` |
| Testing | `higson://docs/testing` |
| Naming conventions | `higson://docs/naming-conventions` |
| Troubleshooting | `higson://docs/troubleshooting` |
| Full MCP tools reference | `higson://docs/mcp-tools-reference` |

`ListMcpResourcesTool(server="higson")` lists them. Prefer these over the versioned web
docs when connected. **Read `higson://docs/functions` before writing or editing a
function body** — the decision-table result API is where invented methods creep in.

## Common mistakes — STOP

- Calling `update_domain_element` after reading only one attribute → you wipe the rest.
  Use `set_domain_attribute`.
- Treating a `set_*`/`update_*` `SUCCESS` as live → you forgot `publish_changes`.
- Calling `reject_changes` to undo one change → it discards pending work-session changes. Restore the
  value instead.
- Editing a `LITERAL` when the attribute is `PARAMETER`/`FUNCTION`/`FLOW`.
- Writing `higson.getAll(...)` in a function body → no such method. `getValue` returns
  the matrix.
- Reading a matrix without an `isEmpty()` guard → `row()` hands back `null` and the
  NullPointerException surfaces somewhere else.
- Reaching for `getNumber`/`getBoolean` on a nullable column → a missing rate prices as
  `0.0` instead of failing.
- Declaring or importing `log`/`str`/`util`/`math`/`date`/`type`/`domain` in a function
  body → they are already injected.
- Treating `date.getMonth` as 0-based → it is 1-based; a `0` means the input could not be
  parsed.
- Treating `domain.get(path)` as null-safe → it throws; `getSafe(path)` is the
  null-returning form.
- Assuming a failed lookup returns an empty matrix → only on a table flagged nullable;
  otherwise it throws `ParameterValueNotFoundException`.
- Writing `higson.getLong(...)` or `ctx.set` over an existing path → neither exists / it
  throws.
- Dumping raw `list_*` on a large profile → overflow. Use `search`/filters.
- Scanning, writing, or publishing without asking first.
- Persisting environment knowledge to ad-hoc memory instead of `.higson/knowledge.md`.
- Re-running discovery when `.higson/knowledge.md` already exists.

## Setup notes

- Connection needs the Studio URL and an integration token (generated in Studio UI).
  **Least-privilege:** use a token for a user with only the rights the work needs, not
  a broad admin token.
- Some raw Higson APIs need function `body` Base64-encoded; the `create_function` /
  `update_function` MCP tools handle that for you.
- This skill hardcodes no business domain; any insurance/pricing example is illustrative.
