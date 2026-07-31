---
id: doc-directory-layout
type: doc
version: 1.0.0
language: en
status: active
last_updated: 2026-05-30
---

# 02 — Directory Layout

A minimal InfoWeave instance. Add directories as your needs grow; start small.

```
<workspace-root>/
├── AGENTS.md                 # the rulebook (from AGENTS.template.md)
├── README.md                 # your user-facing guide (optional)
├── .github/                  # (optional) Copilot-specific integrations
│   ├── instructions/         # applyTo rules (knowledge.instructions.md, etc.)
│   └── prompts/              # session-lifecycle prompts (end-session, etc.)
├── knowledge/                # persistent, indexed knowledge
│   ├── INDEX.md              # tiny top-level map (always read first)
│   └── <category>/
│       ├── INDEX.md          # category map
│       └── <item>/
│           ├── SKILL.md      # self-description + file list
│           └── <body>.md     # the actual knowledge, loaded on demand
├── memory/                   # operational state
│   ├── current-focus.md      # 1–2 paragraphs: what's happening now
│   ├── preference.md         # user preferences (read at session start if present)
│   ├── decisions.md          # append-only major decisions
│   ├── work-log/
│   │   └── <site>.md         # append-only journal, one per machine
│   └── locks/                # lock markers for shared-file edits
├── handoffs/                 # cross-session/machine relay
│   ├── INDEX.md              # append-only list, newest on top
│   ├── _latest.md            # pointer to the newest open handoff
│   └── YYYY-MM-DD-HHMM_<site>_<topic>.md
├── _system/                  # policies & registries (rarely change)
│   └── host-registry.yaml    # the ONLY place real hosts/paths/ids live
├── projects/                 # the actual work
│   └── <project>/ABOUT.md
├── environments/             # (optional) one profile per machine/site
├── portability/              # (optional) cross-environment move manifests + checklists
├── outputs/                  # generated reports & deliverables
└── archive/                  # old material (never preloaded by agents)
```

## Which directories are required?

| Directory | Required for v1? | Notes |
|---|---|---|
| `AGENTS.md` | **Yes** | The contract. Nothing works without it. |
| `knowledge/INDEX.md` | **Yes** | Can start nearly empty. |
| `memory/` | **Yes** | At least `current-focus.md` + one `work-log/<site>.md`. |
| `memory/preference.md` | Recommended | User preferences; read at session start if present. |
| `handoffs/` | Recommended | Needed as soon as you cross sessions/machines. |
| `_system/` | Recommended | Put `host-registry.yaml` here on day one. |
| `.github/instructions/` | Optional | `applyTo` rules for Copilot CLI; deploy from `templates/`. |
| `.github/prompts/` | Optional | Session-lifecycle prompts; deploy from `templates/prompts/`. |
| `projects/` | Yours | Whatever structure your work needs. |
| `environments/` | Only with a transit hub | One profile file per machine/site. |
| `portability/` | Only with a transit hub | Cross-environment move manifests + checklists. |
| `outputs/`, `archive/` | Optional | Add when you first need them. |

## Naming
Dash-separated lowercase for everything you control. Keep external/version names
as-is. Don't rename files that scripts already reference — IDs in frontmatter are
stable even when files move.

## ABOUT.md per directory
Each major directory carries an `ABOUT.md` describing its purpose, status, entry
points, and what is off-limits. Use `templates/ABOUT.template.md`.
