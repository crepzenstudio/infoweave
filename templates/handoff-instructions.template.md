---
applyTo: "handoffs/**"
description: Rules for creating handoffs
---

# Handoff Instructions (template)

- Follow the standard sections (Context / Current task / Completed work / Pending work / Decisions made / Issues blockers / Files changed / Files to read next / Approval needed / Recommended next prompt / Exportability note).
- Filename format: `YYYY-MM-DD-HHMM_<site>_<topic>.md` — never overwrite.
- Required frontmatter fields: id, type: handoff, from_env, to_env, agent_id, status, last_updated.
- Update `handoffs/_latest.md` (symlink or content pointer) to point to this file.
- Add a line in `handoffs/INDEX.md`.
- Never edit a past handoff. To deprecate: set `status: superseded` and add `superseded_by:`.
