---
id: doc-concepts
type: doc
version: 1.0.0
language: en
status: active
last_updated: 2026-05-30
---

# 01 — Concepts

InfoWeave rests on a few ideas. Understand these and the rest follows.

## Single Source of Truth (SSOT)
There is exactly one authoritative rule file: `AGENTS.md`. Every agent reads it
first. Per-runtime filenames (e.g. `CLAUDE.md`) are **aliases**, never copies, so
the rules can never drift between agents.

## The three planes of state
- **Knowledge** (`knowledge/`): curated, indexed, long-lived. The things worth
  remembering across months and projects.
- **Memory** (`memory/`): operational history and current state. Append-only
  journals plus one small "what am I doing now" file.
- **Handoff** (`handoffs/`): explicit, on-demand relay notes written when a session
  or machine hands work to the next one.

Keeping these separate is what makes concurrent, multi-machine operation safe.

## Progressive disclosure
Agents should orient cheaply. They read a tiny top-level `INDEX.md`, then a category
INDEX only if relevant, then a `SKILL.md`, and only then a knowledge body file. This
keeps the cost of "getting your bearings" roughly constant no matter how much
content accumulates.

## Append-only journals
History is preserved by only ever *adding* to journals (`work-log`, `decisions`) and
only ever *creating* new handoff files. Nothing rewrites the past. Two agents can
write concurrently and never clobber each other.

## Environment-agnostic by placeholder
Real hosts, paths, and identities never appear hardcoded in scripts. They live behind
placeholders (`<workspace-root>`, `<site>`, `<user>`) and a single registry file. The
framework is therefore portable to any organization or machine.

## Why Markdown + YAML only
Tools change; plain text endures. Any future agent or editor that can read Markdown
can take over the workspace with zero migration. YAML frontmatter gives every file
just enough structure (`id`, `type`, `version`, `status`, `last_updated`) to be
indexed, validated, and audited.

## Where the real work goes
`projects/` holds the actual code/experiments/services. InfoWeave is the *operating
system* around that work — memory, rules, knowledge, and handoffs — not a replacement
for your project structure.
