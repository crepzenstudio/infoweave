---
id: doc-longevity
type: doc
version: 1.0.0
language: en
status: active
last_updated: 2026-05-30
---

# 07 — Longevity & Migration-Out

InfoWeave is built to remain usable across years, tool changes, and team handovers.
These rules are what make that possible — and the deliberate exit path that means you
are never locked in.

## 1. No tool lock-in
All persistent content is **plain Markdown + YAML frontmatter**. No proprietary
formats, no required database. Any future tool that reads Markdown can take over.

## 2. Frontmatter is mandatory
Every Markdown file under `knowledge/`, `_system/`, and `handoffs/` carries:
```yaml
---
id: <unique-stable-id>
type: <authority|policy|doc|index-top|index-category|skill|knowledge-note|handoff|about|changelog|...>
version: <semver>
status: active | draft | stale | archived
last_updated: YYYY-MM-DD
---
```
IDs are stable: do not rename them even when a file moves.

## 3. Three-level INDEX scales without cost
Top INDEX (<2KB) → category INDEX → item `SKILL.md` → body. Agents always read the
top INDEX, category INDEX on a match, bodies on demand. Startup token cost stays
roughly constant whether the corpus is KB or GB.

## 4. Append-only journals
`work-log` and `decisions` are append-only; handoffs are new-file-only. This both
avoids merge conflicts under concurrency and preserves history permanently.

## 5. Hardcoded values centralized
Hosts, paths, and identifiers live only in `_system/host-registry.yaml`. No script
hardcodes them. Re-homing the workspace means editing one file.

## 6. Archive lifecycle
Items untouched for ~6 months become archive candidates. Old logs are periodically
distilled into a compressed summary and the raw moved to `archive/`, which agents
**never** preload.

## 7. Periodic audit
Run a periodic check that every applicable file has frontmatter, `id`s are unique,
`type`/`status` are from the vocabulary, and `last_updated` is plausible. Fix drift
early; it is cheap when caught.

## 8. Migration-out plan (you are never trapped)
Because everything is Markdown + YAML in an ordinary folder tree, exit is trivial:

1. **Copy the folder.** That is the entire export — there is no hidden state.
2. **Keep frontmatter.** Any new indexer can rebuild navigation from `id`/`type`.
3. **Drop the conventions you don't want.** The content survives without the rules;
   the rules are just Markdown too.
4. **Rebind agents.** Point a new runtime at `AGENTS.md` (or alias it) and continue.

There is no database to dump, no service to decommission, no format to convert. The
durability guarantee is simply: *it was always just files.*
