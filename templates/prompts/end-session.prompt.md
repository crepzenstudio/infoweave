---
description: Session-end checklist. Run this at natural session endings, milestone completions, or when the user signals they are done. Includes auto-handoff and knowledge distillation.
---

# Session End — Checklist

The session is ending. Execute these steps before closing:

## 1. Distill knowledge (if anything reusable was produced)

Ask yourself: did this session produce patterns, fixes, or insights worth remembering?

- If **yes**: run the `distill-knowledge` prompt (or follow AGENTS.md §7 to add entries to `knowledge/`).
- If the result is truly one-off: skip.
- **Do not ask the user for permission for every small item.** Batch observations and propose in one message: *"I found 2 reusable patterns from today's session. Shall I save them to `knowledge/`?"*

## 2. Update `memory/current-focus.md`

Rewrite `memory/current-focus.md` to reflect the current state:
- Active phase: what was accomplished and what remains
- Next steps: the 1–3 most important actions for the next session
- Open threads: anything in-flight that the next session should know about

Keep it under 50 lines.

## 3. Append to `memory/work-log/<site>.md`

Append a brief entry:
```
## YYYY-MM-DD HH:MM <agent-id>
- <what was done>
- <decisions made (cross-ref decisions.md if significant)>
- next: <most important next action>
```

## 4. Create a handoff (if warranted)

Decide whether a handoff is needed (see AGENTS.md §8 decision table):
- Major milestone completed → **yes**
- Significant open items remain → **yes**
- Routine small task only → **no** (work-log is enough)

If yes: create `handoffs/YYYY-MM-DD-HHMM_<site>_<topic>.md` using the standard sections
(see `templates/handoff.template.md`). Update `handoffs/_latest.md` and `handoffs/INDEX.md`.

## 5. Confirm with the user

Summarize what was saved:
> "Session closed. Saved: [knowledge entries if any] / updated current-focus / appended work-log / [created handoff if any]. Ready to continue next session."
