---
id: doc-multi-agent
type: doc
version: 1.1.0
language: en
status: active
last_updated: 2026-09-11
---

# 05 — Multi-Agent & Multi-Machine Operation

InfoWeave assumes several agents on several machines share one workspace. Safety
comes from **file-write discipline**, not a coordinating server.

> **v1.1 (2026-09-11):** the lock mechanism below changed from a lock *file* (write,
> then re-read after a couple of seconds to confirm you still hold it) to a lock
> *directory* (`mkdir`, atomic by construction). The file-based version had a real
> TOCTOU race — this was found and fixed after a live deployment hit a lost-update
> collision on a shared status file. If you deployed InfoWeave before this version,
> update accordingly; the resource list a lock is required for also grew slightly.

## Operations by parallel safety

### ✅ Parallel-safe — no coordination needed
- Appending to `memory/work-log/<site>.md` (one file per machine)
- Creating new files in `handoffs/` (unique timestamped names)
- Appending to `memory/decisions.md` (append-only)
- Reading anything
- Editing inside a single `projects/<x>/` that has one owner

### ⚠️ Lock required — shared single-owner files
Before editing, claim a lock **directory** in `memory/locks/`:

```
memory/locks/<resource-name>/
```
`mkdir` on that path is the lock: on essentially every filesystem (including network
mounts) directory creation is atomic — it either succeeds or fails with "already
exists," with no window where two agents can both believe they hold the lock. A plain
lock *file* doesn't give you this for free: "check if the file exists, then create it"
is two steps, and two agents can both pass the check before either writes (classic
TOCTOU race). Don't recreate that bug by lock-filing instead of lock-`mkdir`-ing.

Once `mkdir` succeeds, drop a marker inside it recording who holds it:
```
memory/locks/<resource-name>/holder.txt
```
Content (one line):
```
<site>:<agent-id>:<ISO8601-timestamp>:<purpose>
```
Example: `site-a:assistant-AB12:2026-05-30T10:30:00+09:00:updating-index`

Edit, then remove the whole lock directory. A lock older than ~1 hour may be assumed
stale.

Resources that need a lock:
- `knowledge/INDEX.md`
- any category `knowledge/<category>/INDEX.md`
- `memory/current-focus.md` (rewritten in place per section, not append-only — a
  frequent source of lost-update collisions if left unlocked)
- `_system/host-registry.yaml` (the editable registry)
- `handoffs/_latest.md` and `handoffs/INDEX.md`
- any other file with a single logical owner that several agents might touch

### Lock lifecycle
1. **Acquire** — `mkdir memory/locks/<resource-name>/`. Success means you hold it;
   `EEXIST`/"already exists" means someone else does — see below, don't just retry
   the mkdir in a tight loop.
2. **Mark** — write `holder.txt` inside it with your identity and purpose.
3. **Operate** — make the edit.
4. **Release** — remove the lock directory (recursively — it has `holder.txt` inside,
   so a bare `rmdir` will refuse; use `rm -rf`/`Remove-Item -Recurse`). Do this
   promptly; don't hold a lock longer than the edit takes.
5. **Stale recovery** — a lock older than ~1 hour may be removed by another agent
   *after* logging the takeover in `memory/decisions.md`.

If the lock is already held and fresh, don't block-and-wait — agent sessions are often
long-running and asynchronous, so a tight retry loop just burns time for no benefit.
Either defer the edit and retry later in your own session, use an append-only fallback
if one exists for that resource, or surface the conflict to the user.

> Why marker directories instead of OS locks? Network filesystems make `flock`
> unreliable and SSH-launched processes may not share file handles. Cooperative,
> `mkdir`-based markers are atomic without relying on OS-level locking primitives, and
> stay human-debuggable — anyone can `ls memory/locks/` and read `holder.txt`.

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
