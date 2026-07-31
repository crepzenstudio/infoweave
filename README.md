# InfoWeave v1.0 — Markdown-native operating framework for AI agents

InfoWeave is a **plain Markdown + YAML** framework that turns an ordinary shared
folder into durable external memory, rules, and knowledge for AI coding agents
(and humans). No database, no proprietary format, no tool lock-in: any agent that
can read Markdown can operate the workspace.

It is designed for **multi-year, multi-agent, multi-environment** operation —
several agents on different machines can collaborate through the same folder
without stepping on each other.

## What you get

- **A single source of truth** (`AGENTS.template.md`) that every agent reads first.
- **Knowledge / Memory / Handoff protocols** for persistent, conflict-free state.
- **A three-level INDEX** that keeps agent startup cost constant as content grows.
- **Templates, schemas, and examples** to stand up your own instance fast.
- **Agent-neutral design**: one rule file, aliased to whatever each runtime expects.

## 5-minute orientation

| If you want to… | Read |
|---|---|
| Understand the idea in one page | this README |
| Complete setup (CLI / VS Code) | `AGENTS.template.md` + `docs/09-deployment-model.md` |
| See the architecture & data flow | `ARCHITECTURE.md` |
| Get the full rulebook to adapt | `AGENTS.template.md` |
| Understand how this kit becomes *your* environment | `docs/09-deployment-model.md` |
| Bridge isolated / cloud environments (transit hub) | `docs/08-transit-and-relay.md` |
| Navigate everything | `INDEX.md` |

## ⚠️ Safety warnings for adopters

**Before you or your AI agent does anything in this workspace, read these carefully.**

### Never overwrite `.github/copilot-instructions.md`

If your workspace already has `.github/copilot-instructions.md`, `CLAUDE.md`, or any
similar instruction file — **do not replace it**. InfoWeave works by placing only
`AGENTS.md` at the root. Copilot auto-discovers both files and applies them in order.

To wire an existing instruction file to InfoWeave, add just one line near the top:

```markdown
# See AGENTS.md — all workspace rules are here (InfoWeave).
```

Overwriting your existing file would erase environment-specific settings (paths,
credentials, language preferences) and could cause unexpected behavior.

### Never write secrets or hardcoded paths into templates

All files in this distribution use `<placeholders>` for environment values.
**No script or agent should fill them with real credentials, SSH keys, passwords,
or absolute paths that are not yours.** Real host/path/identity values go into
`_system/host-registry.yaml` (copied from `examples/hosts.example.yaml`) — and only
after you confirm what belongs there.

### Optional features require your explicit choice

This kit ships with optional planes that most single-machine adopters never need:

| Optional | File | What it adds | You need it if… |
|---|---|---|---|
| Transit/Relay | `docs/08-transit-and-relay.md` | Bridging isolated / cloud environments | You have a network-isolated machine that needs to sync |
| Multi-agent locks | `docs/05-multi-agent.md`, `memory/locks/` | Coordination for concurrent agents | Multiple agents run simultaneously on different machines |
| Portability manifests | `templates/port-manifest.template.yaml` | Cross-environment move checklists | You regularly move work between physically separate sites |

**Your agent must ask you before building any of these.** If you do not need them,
they should not be created. A minimal InfoWeave instance needs only:
`AGENTS.md`, `knowledge/INDEX.md`, `memory/current-focus.md`, and the directory skeleton.

### Interactive bootstrap (recommended)

The intended way to adopt InfoWeave is **not** a manual checklist — it is an
agent-led conversation. Drop this kit into any workspace, then tell your agent:

> "Read this InfoWeave kit starting with `INDEX.md`, `README.md`, and
> `AGENTS.template.md`. Then interview me about my environment (workspace path,
> machines, AI runtimes, language preference) and scaffold a concrete instance.
> **Never overwrite existing instruction files.** Ask before enabling any optional
> feature. When working on projects, proactively save reusable knowledge to `knowledge/`
> without waiting for explicit instructions — only skip when the result is truly one-off."

The agent will produce a working workspace that is shaped to *your* needs — nothing more,
nothing less. For the manual (faster but less tailored) path, follow **How to adopt** below.

## Core principles

1. **Markdown + YAML only** — survives any tool change for years.
2. **Single source of truth** — one `AGENTS.md`, agent-neutral, aliased per runtime.
3. **Append-only journals** — memory and handoffs never overwrite, so concurrent
   agents never conflict.
4. **Progressive disclosure** — agents read tiny INDEX files first, load bodies on demand.
5. **Environment-agnostic** — all host/path/identity values live behind placeholders
   and a single registry file; nothing is hardcoded in scripts.

## How to adopt

1. Read the safety warnings above (`⚠️ Safety warnings for adopters`).
2. Use the **agent-led interview** prompt above (recommended for first-time adoption), or
   set up manually: copy `AGENTS.template.md` to your workspace root as `AGENTS.md`, fill
   placeholders, and deploy templates from the `templates/` directory.
3. Point your agent at the workspace and ask it: *"Read AGENTS.md and summarize the rules."*

## Post-Setup Cleanup (Agent-Led)

After the workspace is built, **not all files need to stay**. This section tells your agent what to do at setup completion.

### Setup-time vs Runtime files

| Category | Files | Keep after setup? |
|---|---|---|
| **Core runtime** — always needed | `AGENTS.md`, `knowledge/INDEX.md`, `memory/`, `handoffs/` | ✅ Yes |
| **Concept docs** — optional reading | `ARCHITECTURE.md`, `docs/01~09` | ⚠️ Optional (move to archive or keep in projects/) |
| **Templates** — used once during deploy | `templates/*.template.*` | ❌ Can be removed after templates are deployed |
| **Schemas / Examples** — for reference | `schemas/`, `examples/`, `adapters/` | ⚠️ Optional (keep if you may add new agents later) |

### Agent cleanup prompt

When setup is complete, the agent should ask:

> "Setup is complete. Please decide what to do with the documentation files that
> are no longer needed in this environment (templates, schemas/examples, docs, etc.):
>
> 1. **Delete** — Remove templates and setup guides. Keeps the environment minimal.
> 2. **Move to Archive** — Move them under `archive/setup-kit/` for future reference.
> 3. **Record as Handoff** — Log the outcome as `handoffs/YYYY-MM-DD-HHMM_infoweave-setup.md`
>    and delete the originals.
> 4. **Keep as-is** — Leave everything in place; not needed now but might be useful later.
>
> This README is also for reference only. Should it be deleted or moved to Archive?"

### If you receive this package from someone else

The handoff message from the sender will include cleanup instructions. When your agent completes setup:

1. Review which files are still needed for daily operation
2. Ask the user to choose: **delete**, **archive**, or **keep**
3. Record the decision in a new handoff file (`handoffs/YYYY-MM-DD-HHMM_infoweave-setup.md`)
4. Update `handoffs/_latest.md` to point to this handoff

This ensures your workspace stays clean while preserving setup knowledge for future reference.

## Scope

- ✅ Included: the framework, protocols, templates, schemas, neutral examples, and
  the docs needed to run it on **any** Markdown-capable agent and ordinary shared storage.
- ❌ Not included: any specific organization's hosts, credentials, datasets, binaries,
  or internal project content. Those belong to **your** instance, not to the framework.

## License / sharing note

This is a framework distribution containing only neutral, self-contained material.
Adapt it freely inside your organization. Keep your instance's real identifiers,
data, and credentials out of any copy you re-share.
