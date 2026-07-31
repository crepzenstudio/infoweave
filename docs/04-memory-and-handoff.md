---
id: doc-memory-and-handoff
type: doc
version: 1.0.0
language: en
status: active
last_updated: 2026-06-17
---

# 04 — Memory & Handoff

Two complementary mechanisms keep operational state across time and machines.

## Memory — the continuous record

### `memory/work-log/<site>.md` (append-only, one per machine)
The running diary of what happened. One file per machine means two agents on two
machines never contend for the same file.

Entry format:
```
## YYYY-MM-DD HH:MM <agent-id>
- did X
- decided Y (see decisions.md)
- next: Z
```

### `memory/decisions.md` (append-only)
Major decisions with rationale, newest appended at the bottom. This is the
"why did we do it this way" record that survives personnel and tool changes.

### `memory/current-focus.md` (small, may be rewritten)
1–2 paragraphs describing the current operational state. Unlike the journals, this
*may* be overwritten — it is a snapshot, not a history. Keep it short (a soft cap
of ~50 lines); push older context out to the journals or an archive section.

### `memory/compressed-memory.md` (optional)
Periodic distillation of old work-log entries for space savings. Not required — useful when logs grow large.

## Handoff — the explicit relay

Handoffs are created **on demand** and also **autonomously** when sessions end naturally at milestones or interruptions occur. See `AGENTS.md §8` for the full decision table.

### Creating a handoff
- New file: `handoffs/YYYY-MM-DD-HHMM_<site>_<topic>.md`. **Never overwrite** an
  existing handoff; to retire one, set `status: superseded` and add `superseded_by:`.
- Update `handoffs/_latest.md` to point at the newest open handoff (a symlink on
  systems that support it, or a thin pointer file otherwise).
- Add a line to `handoffs/INDEX.md` (append-only, newest on top).

### Required frontmatter
`id, type: handoff, from_env, to_env, agent_id, status, last_updated`.

### Standard sections (use all of them)
`Context` · `Current task` · `Completed work` · `Pending work` ·
`Decisions made` · `Issues / blockers` · `Files changed` · `Files to read next` ·
`Approval needed` · `Recommended next prompt` · `Exportability note`.

See `templates/handoff.template.md`.

## How they work together
- The **work-log** answers "what happened?".
- **decisions.md** answers "why?".
- **current-focus.md** answers "where are we right now?".
- A **handoff** answers "what should the next session do, and what does it need to
  read first?".

An agent resuming work reads `_latest.md` (handoff) then `current-focus.md`, and is
immediately oriented without scanning the entire history.

## Exportability discipline
Operational records are internal by nature. When you generate an external deliverable
into `outputs/`, do **not** carry handoff/memory references or internal identifiers
into it. Keep distributables self-contained and neutral.
