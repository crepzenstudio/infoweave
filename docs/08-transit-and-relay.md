---
id: doc-transit-and-relay
type: doc
version: 1.0.0
language: en
status: active
last_updated: 2026-05-30
---

# 08 — Transit Hub & Relay (the optional fourth plane)

> **For users:** this page explains a concept you may or may not need. If all your
> machines can already reach the workspace folder directly, you can skip it.
> **For agents:** treat the transit hub as a *non-authoritative* plane. Never store
> source-of-truth state there.

## The problem it solves

The workspace (`<workspace-root>`) is the single source of truth. But in some
organizations, **not every environment can reach that storage directly**:

- An isolated or overseas network that can only be entered through a jump host.
- A cloud / document service (shared drives, mail, wikis) that lives outside the
  network where the workspace sits.
- A deliverable that must be packaged and dropped into an external archive.

When the authoritative storage and some environment cannot see each other, you need
a place that **can see both**. That place is the **transit hub**.

## What the transit hub is

A **non-authoritative relay node** that bridges environments which cannot talk
directly. It is defined by what it does, not by any particular machine:

| Responsibility | Plain meaning |
|---|---|
| **Tunnel relay** | Opens a path into an isolated/remote network (e.g. via a jump host) so files can move in and out. |
| **Cloud/office entry point** | The one place wired to reach external document/mail/drive services. |
| **Staging & packaging** | Where deliverables are assembled, zipped, and handed off to an external archive. |
| **Optional read-only mirror** | May hold a *read-only* copy of the workspace for offline reference. |

### The one rule that matters
**The transit hub stores no source-of-truth state.** Authoritative knowledge,
memory, and handoffs always live in `<workspace-root>`. The hub only *moves* and
*stages* things. If the hub is wiped, nothing authoritative is lost.

## Where it sits

```
   isolated / overseas            transit hub                authoritative
      environment        ◄──tunnel──►  (relay)  ◄──sync──►   <workspace-root>
   (cannot reach SSOT directly)   (can reach both sides)     (single source of truth)
                                       │
                                       ├── cloud / office services entry
                                       └── deliverable staging → external archive
```

## When you need it — and when you don't

| Your situation | Need a transit hub? |
|---|---|
| One machine, or all machines mount the same workspace | **No.** Skip this plane entirely. |
| Several machines that can all reach `<workspace-root>` | **No.** Plain file access is enough. |
| Some environment is network-isolated / reachable only via a jump host | **Yes.** |
| You must bridge to cloud/office services from a separate network | **Yes.** |
| You package deliverables for an external drive/archive | **Yes (lightweight).** |

The relay is the **optional fourth plane** on top of knowledge / memory / handoff.
Most small adopters never turn it on.

## How a move actually happens (manifest + handoff)

Cross-environment moves are described, approved, executed, and recorded — never ad hoc:

1. **Describe** — copy `templates/port-manifest.template.yaml` to a real manifest
   stating *what* moves, *from/to* which environment, *via* direct or relay, the
   data class, size, and dependencies.
2. **Approve** — the user reviews the manifest. Nothing moves without it.
3. **Execute** — the transit hub runs the transport (file sync of your choice),
   following the manifest's command template and preflight checks.
4. **Record** — write a handoff in `handoffs/` so the receiving session can continue,
   and append a line to the receiving environment's `memory/work-log/<site>.md`.

The framework defines the **manifest shape, the placeholders, and the workflow**.
The actual transport mechanism (rsync, scp, shared mount, tunnel script, …) is yours
to provide — InfoWeave never hardcodes it.

## Agent rules for the relay

- Treat the hub as scratch/transit only; do not write authoritative knowledge there.
- Never move anything not listed in an approved manifest.
- Never carry secrets, raw datasets, or restricted/personal data across the relay;
  honor the knowledge-sync allow-list (`examples/knowledge-sync-allowlist.example.txt`).
- After a move, always leave a handoff + work-log trail so the trip is auditable.

## Configuring it

The hub and its endpoints are just entries in your single registry
(`_system/host-registry.yaml`). See the `relay:` block in
`examples/hosts.example.yaml`. Set `relay.enabled: false` if you don't use it.
