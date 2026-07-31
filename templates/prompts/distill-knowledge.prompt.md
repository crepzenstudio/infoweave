---
description: Distill reusable patterns and insights from the current session into knowledge/ entries. Run at session end or whenever a notable technique, fix, or pattern is identified.
---

# Knowledge Distillation

Review the work done in this session and extract anything worth preserving as reusable knowledge.

## Step 1 — Identify candidates

Scan the session's work for:
- Bugs fixed with non-obvious root cause
- Patterns used more than once
- Techniques or configurations that solved a problem in a novel way
- User corrections ("no, like this!") — **these always count**
- Decisions with rationale that future agents should know

For each candidate, ask: *"Would a future agent working on a similar problem benefit from knowing this?"*
If yes → record it. If it is truly one-off → skip.

## Step 2 — Match to existing categories

Read `knowledge/INDEX.md`. For each candidate:
- If it fits an existing category: add it there.
- If it needs a new category: note it and ask the user for approval before creating.

## Step 3 — Create entries

For each item to record:
1. Create `knowledge/<category>/<item>/SKILL.md` with frontmatter:
   ```yaml
   id: <kebab-case-id>
   type: skill
   version: 1.0.0
   status: active
   last_updated: <YYYY-MM-DD>
   when_to_load: <one-line cue describing when this applies>
   size: ~<N> lines
   files: [body.md]
   ```
2. Create `knowledge/<category>/<item>/body.md` with the actual knowledge.
3. Add a one-line entry to `knowledge/<category>/INDEX.md`.
4. If new category: acquire a lock on `knowledge/INDEX.md`, add a row, release lock.

## Step 4 — Report

Tell the user what was saved:
> "Distilled [N] knowledge entries: [brief list]. Added to categories: [names]."

If nothing was worth saving, say so briefly and skip.
