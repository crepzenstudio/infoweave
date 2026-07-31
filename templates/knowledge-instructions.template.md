---
applyTo: "knowledge/**"
description: Rules for reading and writing knowledge/
---

# Knowledge Instructions (template)

When working inside `knowledge/`:

- Follow the Skills pattern: read INDEX first, then SKILL.md, then body files on demand.
- Every new file requires YAML frontmatter (`id, type, version, status, last_updated`).
- Adding a new item: also add a one-line entry to the category `INDEX.md`.
- Adding a new category: ask user for approval, update `knowledge/INDEX.md`, create `<cat>/INDEX.md`.
- Items with >5 files: use directory + `SKILL.md` (copy from templates if available).
- Never embed raw PrivateData; only derived facts, summaries, and actionable rules.
- For library/tool analyses: source repository revision must be recorded in `SKILL.md`.
- Curation: prefer linking related skills via `related:` frontmatter over duplicating content.
