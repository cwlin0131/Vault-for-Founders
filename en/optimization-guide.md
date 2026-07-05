# Vault Optimization Guide — Help Your Agent Find the Right Files Faster

> After building your Vault, retrieval efficiency degrades as files accumulate. This guide covers practical optimization techniques based on real experience.

---

## Core Concept

The primary reader of your Vault is an AI Agent, not a human. Every optimization here serves one goal: **reduce the cost for the Agent to find the right file.**

The Agent's behavior on every startup: read README → decide which files are needed → open and read them. If you can make the first step precise, it won't waste time opening the wrong files.

---

## 1. README Is the Agent's Map

README is the first file the Agent reads on every startup. It uses this index to decide whether to open each file.

**How to do it (small vault, under ~30 files):**
- Don't just describe folders — go to the granularity of every single file, with a one-line summary each
- The summary describes the file's content, not a repetition of the filename
- When you add, delete, or move files, update the README in sync

**How to do it (growing vault), v2: layer the index.**

The per-file README index stops scaling once folders start accumulating. Split your folders into two kinds:

- **Slow-changing folders** (identity/, context/, operations/): keep listing them file by file in the README
- **Growing folders** (memory/, projects/, hr/): give each its own `INDEX.md` with the full listing and status; the README keeps only a one-line entry pointing to it

Trigger: a folder passes ~10 files and keeps growing → add an `INDEX.md`. New rule of thumb for sync: file added or deleted → update that folder's `INDEX.md`; folder added or removed (structural change) → update the root README. See [architecture.md](architecture.md) for the full rationale.

**Bad example:**
```
├── /operations
│   └── entity.md          ← Company data
```
The Agent sees "company data" but doesn't know if it's incorporation docs, bank accounts, or tax filings.

**Good example:**
```
├── /operations
│   └── corp-setup.md      ← Delaware C Corp incorporation data, EIN status, bank account setup
```
The Agent immediately knows this is about incorporation — no need to open the file to check.

---

## 2. Filenames Are the Index

Good filenames let the Agent decide whether to read a file just by looking at the file list, without relying on README or opening the file.

**Naming rules:**
- Avoid generic names — use descriptive filenames (`corp-setup.md` instead of `entity.md`)
- For files in the same category, use `topic-subtopic` format with the topic first (`product-roadmap.md`, `product-pricing.md`)
- Memory files: use `YYYY-MM-DD_topic-summary.md` (dates for easy sorting)
- Project folders: use `YYYY-MM-topic/`

**Write your naming rules into the README** so both your future self and the Agent follow the same standard.

---

## 3. YAML Frontmatter Tagging

Add YAML frontmatter at the top of every `.md` file so the Agent can assess a file's nature and freshness without reading the entire thing.

**You only need three fields:**

```yaml
---
updated: 2026-04-15
tags: [context, product, strategy]
summary: 90-day expansion strategy — positioning, channels, pricing, milestones
---
```

- `updated` — Last modified date, so the Agent can judge information freshness
- `tags` — Category labels for search and cross-referencing
- `summary` — One-line summary, kept consistent with the README index description

**Don't add too many metadata fields.** The more fields you have, the higher the maintenance cost. You'll start skipping updates, and eventually the metadata becomes less accurate than the content itself. Three fields is enough.

---

## 4. Memory Summary Periodic Cleanup

`memory-summary.md` is read by the Agent on every startup. If you don't clean it periodically, stale information will mislead the Agent's judgment.

**Cleanup rules:**
- After completing a task, go back and update the result column or remove items that are no longer relevant
- Periodically check for duplicates, outdated entries, or items whose status has changed
- Review before deleting — confirm with yourself to avoid removing items that are still important but look old

**Suggested frequency:** Once a week, or after every major decision. Doesn't need to be too frequent, but can't be never.

**Sticky reminders are not a log (v2).** If your memory-summary has a sticky/priority section, apply two rules: when an item is resolved, delete it in the same response (don't move it to a "done" pile inside the file), and never let status updates accumulate as history. History belongs in `memory/` records.

---

## 5. Update-Log Rule — Stop Chronic Bloat (v2)

Core files (README.md, memory-summary.md) tend to grow a tail of "last updated: ..." entries at the bottom. Each one looks harmless; two months later the Agent is paying for fifty of them on every startup.

**The rule:** keep only two entries, "last updated" and "previous update." When you add a new one, move the entry it displaces to `sop/vault-changelog.md` (template provided: [templates/vault-changelog.md](templates/vault-changelog.md)).

Scope the changelog to structural events only: folder add/remove, core-file changes, reading-order changes, rule upgrades. Ordinary memory entries don't belong there.

---

## 6. Keep the Three Always-Read Files on a Diet (v2)

README.md, agent-persona.md, and memory-summary.md are read on every startup, so every line in them is a recurring cost. Self-check whenever you add to any of the three:

> Is this something the Agent must have fresh in mind **every** session?

If a passage is longer than ~5 lines AND not used every session, move it out and leave a one-line pointer: mechanisms to `identity/`, procedures to `sop/`, change history to an `INDEX.md` or `sop/vault-changelog.md`.

---

## Execution Order Matters

If you're doing multiple optimizations at once, follow this order:

1. **Rename files first** — Change vague names to descriptive ones
2. **Then write the README index and frontmatter** — Based on final filenames, so you don't have to do it twice
3. **Run a verification pass last** — Confirm all internal links and references point to the new filenames

Rename first, then index — avoid double work.

---

## When to Do This

- **Right after building your Vault** — No rush. Accumulate some files first
- **When you pass 10 files** — Worth investing in README index enhancement and frontmatter
- **When a folder passes 10 files and keeps growing** — Give it an `INDEX.md` and shrink its README entry to one line
- **When you notice the Agent opening wrong files** — The index isn't clear enough, time to optimize
- **When the bottom of your README keeps growing** — Apply the update-log rule (section 5)
- **Every time you add a new file** — Update the index and add frontmatter as you go. Building the habit is more effective than one big cleanup session
