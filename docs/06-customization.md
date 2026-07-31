---
id: doc-customization
type: doc
version: 1.0.0
language: en
status: active
last_updated: 2026-05-30
---

# 06 — Customization

InfoWeave ships neutral. You make it yours by filling placeholders and wiring in
your own hosts, paths, and policies — **in one place**, never scattered through
scripts.

## Placeholders to replace

| Placeholder | Meaning |
|---|---|
| `<workspace-root>` | Absolute path to the workspace |
| `<share-path>` | Team exchange folder (if any) |
| `<scratch-path>` | Fast local scratch (not for long-term storage) |
| `<user>` | Account / agent owner |
| `<site>` | A machine/location label (one `work-log` per site) |
| `<agent>` / `<runtime>` | The AI runtime(s) you use |
| `<workspace-name>` | A short id for `applies_to` in frontmatter |

Search the kit for `<` to find them all:
```bash
grep -rno '<[a-z-]*>' <workspace-root>
```

## The single registry: `_system/host-registry.yaml`
All machine-specific values live here and **nowhere else**. Scripts read from it;
they never hardcode hosts/paths/ids. See `examples/hosts.example.yaml` for a neutral
starting point. Typical contents:
- machine names and their roles
- base paths per machine
- any relay/transport endpoints
- non-secret identifiers needed to locate resources

> Secrets (tokens, keys, passwords) do **not** belong in the registry or anywhere in
> the workspace. Keep them in your platform's secret store and reference them by name.

## Knowledge sync allow-list
If you sync a subset of the workspace between environments, keep an explicit
allow-list of what may travel. See `examples/knowledge-sync-allowlist.example.txt`.
Default to **deny**; add paths deliberately. Never include anything carrying
secrets, raw datasets, or personal/customer data.

## Adapting the rulebook
Edit your `AGENTS.md` (copied from `AGENTS.template.md`):
- Trim sections you don't need; keep the read order, memory/handoff, and concurrency
  rules — those are the load-bearing parts.
- Set the language policy and the destructive-operation approval lists to match your
  team's norms.
- Bump the `version` in frontmatter when you make structural changes.

## Adding policies
Put durable team policies in `_system/` as `type: policy` Markdown files
(`id, type, version, status, last_updated`). Keep them small and reference them from
`AGENTS.md` rather than inlining everything.

## Moving work between environments
Describe *what* moves with a small manifest (source, targets, dependencies, data
references) and resolve *where* via the registry. The framework defines the manifest
shape and placeholders; the actual transport is your file-sync mechanism of choice.
Keep cross-environment transfers free of secrets and restricted data.
