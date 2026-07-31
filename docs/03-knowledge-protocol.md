---
id: doc-knowledge-protocol
type: doc
version: 1.0.0
language: en
status: active
last_updated: 2026-06-17
---

# 03 — Knowledge Protocol (the Skills pattern)

`knowledge/` holds reusable, long-lived knowledge. It is organized so an agent can
find what it needs while reading as little as possible.

## Three levels

```
knowledge/INDEX.md            (<2KB) — categories + one-line descriptions
   └── <category>/INDEX.md           — items in that category, one line each
          └── <item>/SKILL.md        — self-description, when to load, file list
                 └── <body>.md       — the actual content
```

### Top INDEX (`knowledge/INDEX.md`)
- Always read at session start. Keep it under ~2KB.
- A table: category → description → item count → category INDEX path.
- Optionally a small "trigger map": user phrase → minimal file set to load.

### Category INDEX (`knowledge/<category>/INDEX.md`)
- Read only when the request matches that category.
- Lists items with a one-line description each.

### SKILL.md (per item)
- The agent reads this *before* loading any body file.
- Frontmatter carries `when_to_load`, `size`, and `files: [...]`.
- Lets the agent decide whether the body is worth the tokens.

### Body files
- The real knowledge. Loaded only when actually needed.
- Cite sources with `path:line` where applicable so claims are verifiable.

## Loading rules (for agents)
1. Read top `INDEX.md`.
2. Match the user's topic to a category; read that category INDEX.
3. Read the candidate item's `SKILL.md`.
4. Load only the body files you need. **Never** load everything speculatively.

## Adding knowledge

1. Decide the category (create a new one only with the user's approval).
2. Create `<category>/<item>/SKILL.md` describing it (use the Skills frontmatter:
   `id, type: skill, status, when_to_load, size, files`).
3. Add the body file(s).
4. Add a one-line entry to the category INDEX and, if it's a new category, to the
   top INDEX. **Editing INDEX files needs a lock** (see `docs/05-multi-agent.md`).

### Proactive accumulation (agent behavior)

See `AGENTS.md §7. Knowledge Loading Protocol & Autonomous Accumulation` for the full autonomous recording rules, decision table, and maintenance procedures. This doc focuses on the Skills pattern structure only.

## Frontmatter for knowledge files
Minimum: `id, type, version, status, last_updated`. `type` is `index-top`,
`index-category`, `skill`, or `knowledge-note` depending on the file's role. See
`schemas/knowledge.schema.json`.

## Keeping it healthy
- Items untouched for ~6 months are archive candidates.
- Keep the top INDEX small; push detail down a level rather than growing it.
- Prefer many small, well-described items over a few giant files — progressive
  disclosure only works if bodies are independently loadable.
