---
id: knowledge-index
type: index-top
version: 1.0.0
language: en
status: active
last_updated: <YYYY-MM-DD>
size_target: max 2KB
---

# Knowledge INDEX (top-level)

> **Agents**: Read this at session start. Match the user's task to a category below.
> Open a category's INDEX only if relevant. Read an item's `SKILL.md` before loading
> its body. Never preload everything.

## Categories

| Category | Description | Items | Category INDEX |
|---|---|---|---|
| `<category-a>/` | <one-line description> | 0 | `<category-a>/INDEX.md` |
| `<category-b>/` | <one-line description> | 0 | `<category-b>/INDEX.md` |

## How to use

1. User mentions a topic → scan this table for a matching category.
2. Open that category's `INDEX.md` to see specific items and one-line descriptions.
3. For a candidate item, read its `SKILL.md` first (description + when_to_load).
4. Load only the body files actually needed.

## Adding a new category
Only with user approval. Update this file **and** create `<category>/INDEX.md`.
Editing this file requires a lock (see the concurrency rules in AGENTS.md).

## Trigger map (optional)
> Quick lookup: user phrase → minimal file set to load. Keep it short.

| User says… | Load |
|---|---|
| "<example phrase>" | `<category>/<item>/SKILL.md` |
