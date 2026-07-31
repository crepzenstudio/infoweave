# InfoWeave v1.0 — distribution INDEX

> Read this first. It maps your goal to the one or two files you actually need.
> Everything here is self-contained: all links resolve within this distribution.

## By goal

| Your goal | Open |
|---|---|
| Understand InfoWeave in one page | `README.md` |
| Complete setup (CLI / VS Code) | `AGENTS.template.md` + `docs/09-deployment-model.md` |
| See the architecture & data flow | `ARCHITECTURE.md` |
| Get the rulebook to adapt | `AGENTS.template.md` |
| Learn the core ideas | `docs/01-concepts.md` |
| Know what directories to create | `docs/02-directory-layout.md` |
| Capture reusable knowledge | `docs/03-knowledge-protocol.md` |
| Track state across sessions/machines | `docs/04-memory-and-handoff.md` |
| Run multiple agents/machines safely | `docs/05-multi-agent.md` |
| Wire in your hosts/paths/policies | `docs/06-customization.md` |
| Make it last for years / migrate out | `docs/07-longevity.md` |
| Bridge isolated/cloud environments (transit hub) | `docs/08-transit-and-relay.md` |
| Understand how the kit becomes *your* environment | `docs/09-deployment-model.md` |
| Bind a specific AI runtime | `adapters/README.md` |

## Contents

```
infoweave-release/
├── README.md            # overview + safety warnings
├── ARCHITECTURE.md      # structure & data flow
├── AGENTS.template.md   # the rulebook (the core)
├── INDEX.md             # this file
├── docs/                # 01..09 guides
├── templates/           # INDEX / current-focus / preference / handoff / _latest /
│                        #   ABOUT / instructions / port-manifest / prompts/ templates
├── schemas/             # frontmatter JSON Schemas (memory / handoff / knowledge)
├── examples/            # hosts + sync allow-list (neutral)
└── adapters/            # agent-neutral runtime binding
```

## Reading order for a new adopter
1. `README.md` → 2. `AGENTS.template.md` (copy & fill) → 3. the `docs/` you need, on demand.

## Reading order for an AI agent driving an existing instance
1. `AGENTS.md` (your filled copy) → 2. `knowledge/INDEX.md` →
3. `handoffs/_latest.md` → 4. `memory/current-focus.md` →
5. `memory/preference.md` (if present) → 6. the relevant `ABOUT.md`.

## Placeholders
This distribution uses `<…>` placeholders for everything environment-specific
(`<workspace-root>`, `<user>`, `<site>`, `<runtime>`, …). Find them all with:
```bash
grep -rno '<[a-z-]*>' .
```
