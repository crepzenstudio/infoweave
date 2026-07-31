---
# Example: id: handoff-2026-07-17-1800-site-a-matching-test
id: handoff-<YYYY-MM-DD-HHMM>-<site>-<topic>
type: handoff
from_env: <site>
to_env: <site-or-any>
# Example: agent_id: copilot-cli@20260717T1800Z
agent_id: <runtime + session/timestamp>
status: open
# Example: last_updated: 2026-07-17T18:00:00+09:00
last_updated: <YYYY-MM-DDTHH:MM:SS+00:00>
related_handoffs: []
---

# <Short Title>

## Context (1-2 sentences)
<Why this handoff exists; the minimum background the next session needs.>

## Current task
<The goal the next session should accomplish.>

## Completed work
<What is already done.>

## Pending work
<What remains, ideally as ordered steps.>

## Decisions made (with rationale)
<Key choices and why, so they aren't re-litigated.>

## Issues / blockers
<Open problems, risks, anything blocked and on what.>

## Files changed (paths)
<Files touched in this session.>

## Files to read next (paths)
1. `handoffs/_latest.md` (this file)
2. <next file>

## Approval needed (from user)
<Decisions that need the user, with your default assumption if proceeding autonomously.>

## Recommended next prompt or action
> <A ready-to-use prompt the user (or next agent) can run to continue.>

## Exportability note (data class)
<internal | shareable. If a deliverable is produced, keep handoff/memory references
out of it.>
