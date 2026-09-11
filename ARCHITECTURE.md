# ARCHITECTURE — how InfoWeave is put together

InfoWeave has no runtime. It is a **convention over a shared folder**. The
"architecture" is the set of files, the read order, and the protocols that let
many agents and machines share one durable state without conflict.

## 1. The big picture

```
                         ┌───────────────────────────────────────────┐
                         │            <workspace-root>                │
                         │        (single source of truth)            │
                         │                                            │
   ┌──────────┐   read   │   AGENTS.md  ── the rulebook (read first)  │
   │ Agent A  │ ───────► │      │                                     │
   │ @ <site> │          │      ├── knowledge/   persistent knowledge │
   └──────────┘   write  │      │     └── INDEX.md (tiny, read first)  │
        │  append-only   │      ├── memory/      operational state     │
        ▼                │      │     ├── current-focus.md             │
   ┌──────────┐          │      │     ├── preference.md (if present)   │
   │ Agent B  │ ───────► │      │     ├── decisions.md   (append-only) │
   │ @ <site2>│          │      │     ├── work-log/<site>.md (append)  │
   └──────────┘          │      │     └── locks/         (coordination)│
                         │      ├── handoffs/    cross-session relay   │
                         │      │     ├── INDEX.md                     │
                         │      │     └── _latest.md  (pointer)         │
                         │      ├── .github/     (optional integrations)│
                         │      │     ├── instructions/ (applyTo rules) │
                         │      │     └── prompts/      (session hooks) │
                         │      ├── _system/      policies (rarely chg)│
                         │      └── projects/     the actual work      │
                         └───────────────────────────────────────────┘
```

Every agent, on every machine, reads the **same** files. Coordination is achieved
by *what kind of write* each file allows, not by a server process.

## 2. Session read order (the contract)

At the start of every session an agent reads, in order:

1. `AGENTS.md` — the rulebook.
2. `knowledge/INDEX.md` — what durable knowledge exists (tiny; bodies loaded on demand).
3. `handoffs/_latest.md` — what the previous session left behind.
4. `memory/current-focus.md` — the current operational state in 1–2 paragraphs.
5. `memory/preference.md` — user preferences (if the file exists).
6. The `ABOUT.md` of the directory it is about to work in.

It does **not** preload archives, large logs, or knowledge bodies. Skim INDEX → decide → load.

## 3. The three planes of state

| Plane | Directory | Write discipline | Purpose |
|---|---|---|---|
| **Knowledge** | `knowledge/` | curated, indexed | Reusable, long-lived domain knowledge |
| **Memory** | `memory/` | append-only journals + small focus file | Operational history & current state |
| **Handoff** | `handoffs/` | new files only | Explicit relay between sessions/machines |

This separation is what makes concurrent operation safe (see §5).

## 4. Progressive disclosure (constant startup cost)

Knowledge is a **three-level INDEX**:

```
knowledge/INDEX.md            (<2KB, always read)
   └── <category>/INDEX.md    (read only on topic match)
          └── <item>/SKILL.md (read before loading body)
                 └── body.md  (loaded only when needed)
```

As content grows from KB to GB, the agent still only reads a couple of KB to orient.
Token cost at startup stays roughly constant.

## 5. Concurrency model

Multiple agents on multiple machines are expected. Safety comes from file discipline:

- **Parallel-safe (no coordination):** appending to `memory/work-log/<site>.md`
  (one file per machine), creating new files in `handoffs/`, appending to
  `memory/decisions.md`, and reading anything.
- **Lock required:** editing shared single-owner files such as `knowledge/INDEX.md`
  or any category INDEX. An agent `mkdir`s `memory/locks/<resource>/` first (atomic —
  this is the lock itself, not a plain file you'd need to re-check for a race), writes
  a `holder.txt` marker inside, edits, then removes the directory. Locks older than
  ~1 hour are treated as stale. Details: `docs/05-multi-agent.md`.
- **Single-session only:** editing the rulebook (`AGENTS.md`) and `_system/*`.

Because journals are append-only and handoffs are new-file-only, two agents writing
"at the same time" never clobber each other.

## 6. Multi-environment relay

When work must move between physically separated environments (e.g. an isolated
network and a connected one), InfoWeave adds an **optional fourth plane**: a
non-authoritative **transit hub** that can see both sides and relays files between
them. It stores no source-of-truth state. A `portability/` manifest describes *what*
moves and a single registry says *where* — but scripts never hardcode those.
Most single-machine adopters never enable this plane. See `docs/08-transit-and-relay.md`
and `docs/06-customization.md`.

## 7. Agent-neutral runtime binding

`AGENTS.md` is the only rulebook. Each AI runtime that expects its own filename gets
an **alias**, not a copy:

```
AGENTS.md          ← source of truth
CLAUDE.md  -> AGENTS.md   (symlink / thin pointer)
GEMINI.md  -> AGENTS.md
```

Adapters (install / login / link) live in one directory per runtime so that adding
or upgrading an agent touches exactly one place. See `adapters/README.md`.

## 8. Longevity guarantees

- Plain Markdown + YAML only — no proprietary formats, no required database.
- Every Markdown file carries YAML frontmatter (`id, type, version, status, last_updated`).
- Append-only journals preserve history and avoid merge conflicts.
- A documented migration-out path means you are never locked in (`docs/07-longevity.md`).
