---
description: Guided handoff creation. Run when explicitly asked to create a handoff, when switching environments, or when the autonomous rules in AGENTS.md §8 indicate a handoff is warranted.
---

# Generate Handoff

Create a structured handoff so the next session (or another agent) can continue without confusion.

## Step 1 — Choose a filename

```
handoffs/YYYY-MM-DD-HHMM_<site>_<topic>.md
```

Use the current timestamp and a short topic label (e.g., `nas-api-refactor`).

## Step 2 — Fill the standard sections

Use `templates/handoff.template.md` as a base. Fill every section:

| Section | What to write |
|---|---|
| **Context** | 1–2 sentences: why this handoff exists |
| **Current task** | The goal the next session should complete |
| **Completed work** | What is done and tested |
| **Pending work** | Ordered list of remaining steps |
| **Decisions made** | Key choices with rationale (so they aren't re-litigated) |
| **Issues / blockers** | Open problems, risks, anything waiting on an external dependency |
| **Files changed** | Paths of files touched this session |
| **Files to read next** | Ordered reading list for the next session |
| **Approval needed** | Decisions that require user input; state your default assumption |
| **Recommended next prompt** | A ready-to-use first message for the next session |
| **Exportability note** | `internal` or `shareable` |

## Step 3 — Update pointers

1. Update `handoffs/_latest.md` to point at the new handoff file
   (symlink: `ln -sf <filename> _latest.md`; or pointer file: write the filename on a single line).
2. Prepend a one-line entry to `handoffs/INDEX.md` (newest on top).

## Step 4 — Also record in memory

If the session included major decisions, append them to `memory/decisions.md` as well.

## Step 5 — Confirm

Tell the user: *"Handoff created: `handoffs/<filename>.md`. `_latest.md` updated."*
