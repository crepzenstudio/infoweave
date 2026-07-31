# adapters/ — agent-neutral runtime binding

InfoWeave treats `AGENTS.md` as the **single source of truth**. Different AI runtimes
expect different filenames or setup steps; this directory is where you keep the small,
per-runtime glue so that adding or upgrading an agent touches exactly one place.

## The pattern

One subdirectory per runtime:

```
adapters/
├── <runtime-a>/
├── <runtime-b>/
└── README.md        ← this file
```

Each runtime subdirectory should contain (create as needed):

- `install.md` or `install.sh` — how to install/upgrade the runtime
- `login.md` or `login.sh` — how to authenticate (e.g. device-code flow)
- `link.sh` — create the alias the runtime expects, pointing at `AGENTS.md`
- `README.md` — quirks, known issues, version pinning

## Aliasing, not copying

The key rule: never duplicate the rulebook. Materialize the per-runtime filename as
an alias so the rules can never drift.

```bash
# from <workspace-root>, keep AGENTS.md authoritative:
ln -s AGENTS.md CLAUDE.md
ln -s AGENTS.md GEMINI.md
# ...one symlink per runtime that wants its own filename
```

On systems without symlinks, use a one-line pointer file instead:

```
# CLAUDE.md
See AGENTS.md — the single source of truth for this workspace.
```

## Adding a new runtime

1. `mkdir adapters/<new-runtime>/`
2. Add `install`, `login`, `link`, and `README` for it.
3. Wire its expected rule-filename to `AGENTS.md` via `link.sh`.
4. Record the runtime in your workspace's tool/agent registry (if you keep one).

## Why this exists

Per-runtime config files proliferate quickly. By keeping one rulebook and generating
the per-runtime aliases, you avoid the drift where each agent slowly ends up with
slightly different rules.
