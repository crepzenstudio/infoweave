---
id: agents-md
type: authority
version: 1.1.0
language: en
status: active
last_updated: 2026-09-11
applies_to: <workspace-name>
---

# AGENTS.md — Single Source of Truth (template)

> **AI agents**: Read this file **before any action** in this workspace.
> **Humans**: This is the authoritative convention file. Adapt it to your team.
>
> This is a *template*. Replace every `<placeholder>` with your own values and
> delete the guidance notes in **[brackets]** once you have tailored it.

---

## Read Order at Session Start (mandatory)
1. **This file** (AGENTS.md)
2. `knowledge/INDEX.md` — what persistent domain knowledge exists (load bodies on demand only)
3. `handoffs/_latest.md` (or `handoffs/INDEX.md`) — what the previous session left for you
4. `memory/current-focus.md` — current operational state
5. `memory/preference.md` — user preferences (if the file exists)
6. The relevant `ABOUT.md` for the directory you are about to work in

Do **not** preload `archive/`, large logs, or knowledge body files. Skim INDEX, decide, then load.

---

## 1. Workspace Layout

[Adapt this table to your storage. Use placeholders for real paths.]

| Location | Path | Role |
|---|---|---|
| **Main** | `<workspace-root>` | Active development — read/write. Single source of truth. |
| **Exchange** | `<share-path>` | Team file exchange — read/write |
| **Scratch** | `<scratch-path>` | Fast local scratch — **NOT** for long-term storage |

Top-level directories:

| Dir | Purpose |
|---|---|
| `projects/` | The actual work (experiments, services, pipelines) |
| `knowledge/` | Persistent domain knowledge (Skills pattern, on-demand load) |
| `memory/` | Cross-session operational memory (append-only journals) |
| `handoffs/` | Cross-session and cross-environment handoff records |
| `_system/` | Operational policies and registries (rarely changes) |
| `outputs/` | On-demand generated reports and deliverables |
| `environments/` | (optional) One profile per machine/site — needed only with a transit hub |
| `portability/` | (optional) Cross-environment move manifests — needed only with a transit hub |
| `archive/` | Old material kept for reference (never preloaded) |

Each top-level directory should have an `ABOUT.md` (see `ABOUT.template.md`).

---

## 2. Naming Conventions

Default: **dash-separated lowercase** (`my-project-name`). Avoid snake_case,
CamelCase, and dotted names except where an external tool/version requires it
(e.g. third-party SDK versions, compiler identifiers). Do not rename historical
names that other scripts already reference.

---

## 3. ABOUT.md Requirements

Every major directory has one. Required sections: **Purpose** (one sentence),
**Status** (ACTIVE / STALE / ARCHIVED / READ-ONLY), **Entry points**,
**Directory map**, **Safe commands**, **Off-limits**.

---

## 4. Safety Rules for AI Agents

### Always safe (no approval needed)
- Read-only commands (`ls`, `cat`, `find`, `grep`)
- Dry-run modes
- Reading `ABOUT.md`, `AGENTS.md`, `README.md`, `knowledge/INDEX.md`

### Requires explicit approval
- Large or destructive runs, deletions, training jobs, container builds
- Modifying configuration/parameter files
- Editing `AGENTS.md`, `_system/*` while another agent might be editing

### Forbidden without explicit consent
- Modifying read-only upstream/`platforms/` content (silent downstream breakage)
- `rm -rf` on any directory
- Committing secrets
- Pushing to a remote you were not told to push to
- Sending workspace data to third-party systems

[Tailor these lists to your environment's destructive-operation policy.]

---

## 5. Git Policy

[Optional. If you use Git for the meta-workspace, keep it lightweight and
gitignore generated/large content. Example minimum `.gitignore`:]

```
*.pyc
__pycache__/
venv/
bin/
lib/
out/
runs/
models/
logs/
*.env
*.key
*.pem
```

Commit messages: imperative, lowercase, concise.

---

## 6. Hardcoded Path Policy

```bash
# Preferred: relative to workspace root
DATA_DIR="data/<dataset>"
# Acceptable: env var
DATA_DIR="${WORKSPACE_DATA_ROOT:-data/<dataset>}"
# Avoid: absolute paths inside scripts/configs
```

Config files (YAML/conf) **must not** contain absolute paths. Machine-specific
values live in `_system/host-registry.yaml`, not in scripts.

---

## 7. Knowledge Loading Protocol & Autonomous Accumulation

Persistent domain knowledge lives in `knowledge/` and uses the **Skills pattern**:

1. Always read `knowledge/INDEX.md` at session start (kept tiny, <2KB).
2. Map the request to INDEX entries by topic/keyword.
3. For each match, read its `SKILL.md` first (description, file list, size).
4. Only then load the body files you actually need.
5. **Never load all knowledge files speculatively.** Tokens matter long-term.

### Autonomous Knowledge Accumulation (Proactive)

You are expected to **proactively identify and record reusable knowledge** without waiting for explicit user instructions:

- After solving a problem, debugging an issue, or implementing a feature — evaluate whether the solution is reusable. If yes, create the knowledge entry autonomously.
- When you notice a pattern emerging across multiple tasks (e.g., recurring configuration, shared utility), propose creating a new category to the user.
- **Even small corrections count as knowledge.** When the user says "no, like this!" and a one-line fix resolves an issue, record it. These micro-corrections are often the most practical knowledge for avoiding repeated mistakes. Example: *"The YAML parser rejects keys without quotes — always quote string values in host configs."*
- Never ask "Should I save this?" for every individual piece of knowledge. Instead, batch your observations: *"I've identified 3 reusable patterns from today's work. Shall I create a new knowledge category 'image-processing' with all three entries?"*

**Decision criteria for autonomous recording:**
| Situation | Action |
|---|---|
| Solved a bug with non-obvious root cause | ✅ Record automatically (add to existing category or propose new) |
| Implemented a feature using custom logic | ✅ Record (SKILL.md describes when it applies) |
| User corrected your approach ("no, like this!") | ✅ Record the correction — small fixes prevent repeated mistakes |
| User explicitly requests saving something | ✅ Always comply immediately |
| One-time, trivial task result with no future relevance | ❌ Don't record |

**Never record:** secrets, credentials, raw data dumps, or content that cannot be understood without external context.

### Knowledge Maintenance (Periodic Cleanup & Consolidation)

Knowledge will naturally accumulate over time. **You should periodically propose cleanup and consolidation to the user.** This is not automatic — you suggest it at natural checkpoints:

- At the end of a productive day
- When `knowledge/INDEX.md` exceeds ~5KB
- When duplicate or overlapping entries are detected across categories
- When a category has fewer than 2 entries (consider merging)

Suggested cleanup actions to propose:
1. **Merge** — Combine related entries (e.g., "bash-workflow" + "zsh-workflow" → "shell-workflow")
2. **Archive** — Move outdated or rarely-referenced knowledge to `archive/`
3. **Delete** — Remove entries that are no longer relevant or useful
4. **Re-index** — Reorder INDEX.md entries by actual usage frequency

Example prompt: *"I've noticed 4 knowledge entries about YAML handling across 2 categories. Would you like me to merge them into a single 'yaml-patterns' entry?"*

---

## 8. Memory & Handoff Protocol

### Memory (continuous record)
- `memory/work-log/<site>.md` — **append-only** journal, one file per machine.
  Each entry: `## YYYY-MM-DD HH:MM <agent-id>`, then bullet log.
- `memory/decisions.md` — append-only major decisions with rationale.
- `memory/current-focus.md` — small; may be rewritten. Latest state in 1–2 paragraphs.
- `memory/compressed-memory.md` — (optional) periodic distillation of old logs for space savings.

### Handoff (on-demand + proactive session-safety)

#### When to create a handoff

| Situation | Action |
|---|---|
| User explicitly says "stop for now" or "leave notes" | ✅ Create immediately |
| Session is ending naturally after a major milestone | ✅ **Autonomously** — record what was done, what remains next |
| Significant decision made (even if user didn't ask to record it) | ✅ Record in both `memory/decisions.md` and handoff |
| Switching machines or environments | ✅ Required before transfer |
| Work on a project is paused for >24h | ⚠️ Strongly recommended |
| Routine small task completed (e.g., typo fix) | ❌ Not needed — work-log is sufficient |

#### Session-safety auto-handoff (critical)

If the user sends an unexpected message that might indicate session interruption (e.g., "my connection dropped", "I'll be back later"), **immediately** create a handoff before closing. This prevents knowledge loss from unexpected session terminations.

```
handoffs/YYYY-MM-DD-HHMM_<site>_<topic>.md — never overwrite.
handoffs/_latest.md — update to point to the most recent.
Use the standard sections (see `handoff.template.md`).
```

---

## 9. Concurrent Agents Protocol

Multiple agents may operate from different sessions/machines simultaneously.

| Operation | Parallel-safe? |
|---|---|
| Appending to `memory/work-log/<site>.md` | ✅ |
| Creating new files in `handoffs/` | ✅ |
| Appending to `memory/decisions.md` | ✅ (append only) |
| Editing inside one `projects/<x>/` (single owner) | ✅ |
| Editing `knowledge/INDEX.md`, a category INDEX, `memory/current-focus.md`, `handoffs/_latest.md`/`INDEX.md`, or `_system/host-registry.yaml` | ⚠️ Declare a lock in `memory/locks/` first |
| Editing `AGENTS.md`, or `_system/*` other than `host-registry.yaml` | ❌ Single-session only |
| Editing the same `projects/<x>/` from multiple agents | ❌ Coordinate via handoff first |

Lock: `mkdir memory/locks/<resource>/` (atomic — this *is* the lock, not a file you
write-then-hope), then write `holder.txt` inside it, one line:
`<site>:<agent-id>:<ISO8601>:<purpose>`. Remove the whole directory when done
(`rm -rf`, not `rmdir` — it has `holder.txt` inside); >1h old = stale. Full protocol
and rationale: `docs/05-multi-agent.md`.

---

## 10. Language Policy
- This file and agent-facing metadata: **English**, concise.
- User-facing guides and deliverables: **[your team's language]**.
- Conversation with the user: **[your default]** unless told otherwise.

---

## 11. Long-term Sustainability
- Plain Markdown + YAML frontmatter only — no tool lock-in.
- Every Markdown file carries frontmatter (`id, type, version, status, last_updated`).
- Three-level INDEX keeps startup token cost constant as content grows.
- Append-only journals avoid edit conflicts.
- Keep a migration-out plan so you are never locked in.

---

## 12. Identify Yourself in Artifacts
When creating handoffs, work-log entries, or decisions, include your `agent_id`
(e.g. short runtime name + session/timestamp), so concurrent work is attributable.

---

## 13. Copilot CLI / VS Code Integration

### Auto-discovery (no config needed)

Copilot auto-discovers `AGENTS.md` at the workspace root and reads it automatically — for both CLI and VS Code Extension.

### If you already have `.github/copilot-instructions.md`

Do **not** overwrite your existing file. Instead, add one line near the top:

```markdown
# See AGENTS.md — all workspace rules are here (InfoWeave).
```

That single line is enough — Copilot reads both files and applies them in order. Your existing environment-specific content stays intact.

### Optional: Instruction-driven workflows (applyTo)

For users who want structured step-by-step guidance, copy templates into `.github/instructions/`:

```bash
cp templates/knowledge-instructions.template.md .github/instructions/knowledge.instructions.md
cp templates/handoff-instructions.template.md  .github/instructions/handoff.instructions.md
```

These files use the `applyTo` frontmatter to tell Copilot CLI which paths they govern. When present, agents follow detailed rules in these files **in addition to** AGENTS.md.

### Optional: Prompt templates (`.github/prompts/`)

For users who prefer explicit prompts over autonomous behavior, deploy templates from
`templates/prompts/` into `.github/prompts/`:

```bash
mkdir -p <workspace-root>/.github/prompts
cp templates/prompts/*.prompt.md <workspace-root>/.github/prompts/
```

```
.github/prompts/
├── distill-knowledge.prompt.md     # Distill session work into knowledge entries
├── generate-handoff.prompt.md      # Guided handoff creation
├── end-session.prompt.md           # Session-end checklist (includes auto-handoff)
├── start-session.prompt.md         # Session-start orientation checklist
└── audit-workspace.prompt.md       # Periodic workspace health check
```

When present, agents should run the relevant prompt at natural checkpoints. This
complements — not replaces — autonomous accumulation. If you do not deploy these files,
agents rely entirely on the autonomous rules in §7 and §8 of this file.

---

## 14. Versioning of This File

Increment the version on structural changes. Minor wording fixes do not require a bump.
