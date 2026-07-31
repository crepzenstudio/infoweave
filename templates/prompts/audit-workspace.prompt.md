---
description: Periodic workspace health check. Run monthly or when INDEX.md exceeds ~5KB, duplicates are suspected, or the workspace feels cluttered.
---

# Workspace Audit

Perform a structured health check of the workspace and report findings.

## 1. Frontmatter completeness

For files under `knowledge/`, `handoffs/`, `memory/`, and `_system/`, verify:
- Every file has YAML frontmatter.
- Required fields are present (`id`, `type`, `version`, `status`, `last_updated`).
- `id` values are unique within the workspace.
- `type` and `status` values are from the allowed vocabulary (see `schemas/`).
- `last_updated` dates are plausible (not in the future, not more than 2 years stale without `status: stale`).

Report: number of files checked, number of issues found, list any violations.

## 2. Knowledge health

- Is `knowledge/INDEX.md` under ~2KB? If not, propose pushing detail down a level.
- Are there categories with only 1 item? Propose merging with a related category.
- Are there items untouched for >6 months? Mark as `status: stale` and propose archiving.
- Are there duplicate or overlapping entries? Propose merging.

## 3. Handoff hygiene

- Are there handoffs with `status: open` that are >30 days old? Propose marking `consumed` or `superseded`.
- Is `handoffs/_latest.md` pointing at an actual file?
- Is `handoffs/INDEX.md` consistent with the actual files in `handoffs/`?

## 4. Memory logs

- Is `memory/current-focus.md` under 50 lines? If not, propose pushing older content to `work-log`.
- Are work-log files larger than ~500 lines? Propose distilling old entries into `memory/compressed-memory.md`.

## 5. Stale locks

- Are there files in `memory/locks/` older than 1 hour? Report them as stale and offer to remove.

## 6. Summary report

Produce a table:

| Area | Files checked | Issues | Action |
|---|---|---|---|
| Frontmatter | N | N | ... |
| Knowledge | N categories, N items | N | ... |
| Handoffs | N | N | ... |
| Memory | N | N | ... |
| Locks | N | N | ... |

Then ask: *"Would you like me to apply any of these fixes now?"*
