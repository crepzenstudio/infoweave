---
id: doc-multi-agent
type: doc
version: 1.0.0
language: en
status: active
last_updated: 2026-05-30
---

# 05 — Multi-Agent & Multi-Machine Operation

InfoWeave assumes several agents on several machines share one workspace. Safety
comes from **file-write discipline**, not a coordinating server.

## Operations by parallel safety

### ✅ Parallel-safe — no coordination needed
- Appending to `memory/work-log/<site>.md` (one file per machine)
- Creating new files in `handoffs/` (unique timestamped names)
- Appending to `memory/decisions.md` (append-only)
- Reading anything
- Editing inside a single `projects/<x>/` that has one owner

### ⚠️ Lock required — shared single-owner files
Before editing, drop a marker in `memory/locks/`:

```
memory/locks/<resource-name>.lock
```
Content (one line):
```
<site>:<agent-id>:<ISO8601-timestamp>:<purpose>
```
Example: `site-a:assistant-AB12:2026-05-30T10:30:00+09:00:updating-index`

Edit, then remove the lock. A lock older than ~1 hour may be assumed stale.

Resources that need a lock:
- `knowledge/INDEX.md`
- any category `knowledge/<category>/INDEX.md`
- `_system/host-registry.yaml` (the editable registry)
- any other file with a single logical owner that several agents might touch

### Lock lifecycle
1. **Acquire** — write the lock file with your identity and purpose.
2. **Verify** — re-read it ~2 seconds later; if the content is still yours, the lock holds.
3. **Operate** — make the edit.
4. **Release** — delete the lock.
5. **Stale recovery** — a lock older than ~1 hour may be removed by another agent *after*
   logging the takeover in `memory/decisions.md`.

> Why marker files instead of OS locks? Network filesystems make `flock` unreliable and
> SSH-launched processes may not share file handles. Cooperative markers are robust and
> human-debuggable.

### ❌ Single-session only
- `AGENTS.md`
- `_system/*` **except** `_system/host-registry.yaml` (which is lock-required, see above —
  it is the editable registry)
- per-runtime rule aliases (`CLAUDE.md`, etc., since they point at `AGENTS.md`)
- the same `projects/<x>/` from multiple agents at once (coordinate via a handoff first)

## Why this works
Append-only journals and new-file-only handoffs mean concurrent writes **add** rather
than **overwrite**. The only files that need locks are the few shared documents a
second agent might rewrite (chiefly the INDEX files).

## Agent-neutral binding
One rulebook, many runtimes. Each AI runtime that expects its own filename gets an
**alias** to `AGENTS.md`, never a copy:

```
AGENTS.md          ← source of truth
CLAUDE.md  -> AGENTS.md
GEMINI.md  -> AGENTS.md
```

Per-runtime setup (install / login / alias) lives in one directory per runtime so
that adding or upgrading an agent touches exactly one place. See `adapters/README.md`.

## Crossing machines
Each machine appends to its **own** `work-log/<site>.md`. When you move work between
machines (especially across network boundaries), write a handoff describing what
moves and what to read first; the receiving session starts from `_latest.md`. The
*transport* is just file sync — InfoWeave only defines the manifest and placeholders
(see `docs/06-customization.md`).

## Identify yourself
Every journal entry, decision, and handoff records an `agent_id` (short runtime name
+ session/timestamp), so concurrent contributions are always attributable.
