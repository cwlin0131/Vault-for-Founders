# Vault Changelog Template

> Save as `sop/vault-changelog.md`. Companion to the update-log rule (see optimization-guide, section 5).

---

## What This Is

Core files (README.md, memory-summary.md) are read on every startup. If their "last updated: ..." entries keep accumulating at the bottom, the Agent pays for that history on every single session. The update-log rule caps those files at two entries: "last updated" and "previous update." Everything older moves here.

## How to Use

1. Create this file as `sop/vault-changelog.md`
2. Every time you add a new update entry to the README (or memory-summary), move the entry it displaces into this file, newest first
3. That move happens in the same response as the README edit, not "later"

## Scope

Structural events only, meaning anything that changes how the Agent reads the vault:

- Folder added / removed / renamed
- Core file added, renamed, or significantly restructured
- Agent reading order changed
- Forced rules added or upgraded

Ordinary memory entries, project updates, and content edits do NOT belong here. Those live in `memory/` and the project folders.

---

## Format (fictional examples)

*2026/07/01 (**Structural change**: created `projects/archive/`, moved 3 closed projects in; all vault links updated to the new paths)*

*2026/06/12 (Added folder `operations/tax/` as the tax hub: `README.md` index + annual calendar, `forms-guide.md`, `bookkeeping-stack.md`. Trigger: first filing season)*

*2026/05/28 (Reading order updated: added `identity/decision-style.md` as step 4, so the Agent self-checks before drafting anything)*
