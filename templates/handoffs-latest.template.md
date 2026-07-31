# handoffs/_latest.md — pointer to the newest open handoff
#
# PURPOSE: This file always points at the most recently created open handoff.
# Agents read it at session start to orient themselves instantly.
#
# --- HOW TO USE ---
#
# Option A: Symlink (preferred on Unix/Linux/macOS)
#   ln -sf YYYY-MM-DD-HHMM_<site>_<topic>.md _latest.md
#   Update the symlink whenever a new handoff is created.
#
# Option B: Pointer file (for systems without symlink support, e.g. some Windows setups)
#   Replace the entire content of this file with a single line:
#
#     -> YYYY-MM-DD-HHMM_<site>_<topic>.md
#
#   Example:
#     -> 2026-06-16-1800_site-a_api-refactor.md
#
# --- INITIAL STATE ---
# If no handoff exists yet, leave this file empty or write:
#   (no handoff yet — workspace is being initialized)
#
# Once the first handoff is created, update this file immediately.

(no handoff yet — workspace is being initialized)
