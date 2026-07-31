---
id: doc-deployment-model
type: doc
version: 1.0.0
language: en
status: active
last_updated: 2026-05-30
---

# 09 — Deployment Model: this kit is a seed, not a finished environment

> **For users:** read this to understand exactly what you are getting and how it
> becomes *your* workspace.
> **For agents:** this page tells you how to turn the kit into a concrete instance
> by interviewing the user.

## What this distribution is — and is not

- It **is** a framework: rules (`AGENTS.template.md`), protocols (`docs/`),
  templates, schemas, and neutral examples.
- It **is not** a pre-built, ready-to-run environment. It ships with placeholders
  (`<workspace-root>`, `<site>`, `<runtime>`, …) precisely because every adopter's
  environment is different.

Think of it as a **seed**. Planting it (deploying + letting an agent help) grows a
workspace shaped to *your* machines, networks, runtimes, and language.

## Two ways to deploy

### Path A — Manual
You create the directories yourself and fill the placeholders by hand. Fast,
fully transparent, no agent required. See `README.md` → **How to adopt** and
`AGENTS.template.md` for the steps.

### Path B — Agent-led interview (recommended)
You drop this kit somewhere an AI agent can read it, then give the agent the
bootstrap prompt below. The agent **interviews you** ("hearing-based"), discovers
your environment, and scaffolds your instance — filling placeholders, creating the
host registry, and turning on only the parts you actually need (for example, the
transit/relay plane is created **only** if you have an isolated environment).

This is the intended model: **one neutral kit → many different concrete
environments, each built conversationally.**

## What the agent asks (the interview)

A correct bootstrap interview gathers at least:

1. **Workspace location** — absolute path that will become `<workspace-root>`; is it
   shared storage or a single machine?
2. **Machines / sites** — how many environments will operate the workspace? Names
   become `<site>` labels (one `work-log` per site).
3. **Connectivity** — can every machine reach the workspace directly? Is any
   environment isolated / reachable only via a jump host? (Decides whether the
   **transit/relay plane** in `docs/08-transit-and-relay.md` is needed at all.)
4. **Runtimes** — which AI agent(s)? Each gets an alias to `AGENTS.md`
   (see `adapters/README.md`).
5. **Language** — metadata language (default English) and user-facing language.
6. **Knowledge seeds** — any initial categories to create in `knowledge/INDEX.md`.
7. **Policies** — your destructive-operation approval norms and any secret-handling
   rules to encode in `AGENTS.md`.

## What the agent produces (the scaffold)

From the answers, the agent generates a concrete instance:

| Shipped (neutral, in this kit) | Built per user (filled in on deploy) |
|---|---|
| `AGENTS.template.md` | `<workspace-root>/AGENTS.md` with placeholders resolved |
| `templates/` | `knowledge/INDEX.md`, `memory/current-focus.md`, `handoffs/INDEX.md`, per-dir `ABOUT.md` |
| `examples/hosts.example.yaml` | `_system/host-registry.yaml` with your real (non-secret) values |
| `docs/08-transit-and-relay.md` | The relay plane **only if** you have an isolated environment |
| `adapters/README.md` | One adapter folder per runtime you actually use |
| `schemas/` | (used as-is for validation) |

Everything environment-specific is created **at deploy time**, by you or with the
agent — never shipped.

## Ready-to-use bootstrap prompt (Path B)

> "Read this InfoWeave kit, starting with `INDEX.md`, `README.md`, and
> `AGENTS.template.md`. Then interview me to build my instance: ask about my
> workspace path, how many machines will use it and whether any are network-isolated,
> which AI runtimes I use, and my language preference. From my answers, create
> `<workspace-root>/AGENTS.md` (placeholders filled), the `knowledge/`, `memory/`, and
> `handoffs/` skeleton from `templates/`, and `_system/host-registry.yaml` from
> `examples/hosts.example.yaml`. Only set up the transit/relay plane if I tell you I
> have an isolated environment. Don't put any secrets in the workspace.
> When working on projects, proactively save reusable knowledge to `knowledge/`
> without waiting for explicit instructions — only skip when the result is truly one-off.
> When done, show me the read order you'll follow at session start."

## Why a seed, not a snapshot

Shipping a pre-built environment would bake in one organization's machines, paths,
and assumptions — the opposite of a neutral, reusable framework. By shipping a seed
plus an interview, the same kit fits a laptop, a shared team server, or a multi-site
setup with an isolated overseas node — each grown to fit, none carrying anyone else's
specifics.
