# [Your Name] Vault

This is [Your Name]'s personal knowledge base, used by AI Agent ([Agent Name]) as its primary context source.

---

## Folder Structure

```
/
  README.md              ← You're reading this
  agent-persona.md       ← Agent's personality and collaboration style
  memory-summary.md      ← Long-term memory summary

  /identity              ← Who you are, values, decision style
  /context               ← Company background, product status, strategy
  /memory                ← Important decision records, meeting conclusions → INDEX.md
  /sop                   ← Standard operating procedures
  /projects              ← Project status tracking → INDEX.md (closed → archive/)
  /people                ← Key contacts and backgrounds
  /skills                ← AI Agent skill files
```

Growing folders (memory/, projects/, and later hr/) each keep their own `INDEX.md` with the full listing; this README only carries the one-line entries above. Read the folder's `INDEX.md` before diving in.

## Agent Reading Order

1. Read this README (understand the overall structure)
2. Read `agent-persona.md` (understand its role and collaboration style)
3. Read `memory-summary.md` (grasp recent priorities)
4. Read additional folders as needed for the task

## Naming Rules

- Filenames are the index: descriptive names, no generic ones (`corp-setup.md`, not `entity.md`)
- Same category: `topic-subtopic` format, topic first (`product-roadmap.md`, `product-pricing.md`)
- memory/ files: `YYYY-MM-DD_topic.md` · projects/ folders: `YYYY-MM-topic/`

## 🚨 Forced Rules (not suggestions)

### #1: Index sync
- **Add / delete a file** → update that folder's `INDEX.md` in the same response (for folders that have one)
- **Add / delete a folder, or any structural change** → update this README (folder description + update log below)

### #2: Public content
Before drafting anything that will be seen by people other than [Your Name] (LinkedIn, GitHub README, release notes, press, pitch, public issues / PRs), read `identity/voice-and-tone.md` first. When unsure, treat it as public.

Both rules are requirements for task completion. Second safety net: the after-action checklist re-verifies them at task close.

## Maintenance Principles

- One fact lives in one place only (Single Source of Truth)
- Search before creating a new file
- Mark the last-updated date at the top of every document
- Never put sensitive information (API keys, passwords) in this repo

---

*Last updated: YYYY/MM/DD (what changed)*
*Previous: YYYY/MM/DD (what changed)*

*📏 Update-log rule: keep only these two entries. When you add a new one, move the displaced entry to `sop/vault-changelog.md`.*
