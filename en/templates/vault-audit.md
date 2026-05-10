# Vault Audit — Index Integrity Check

> The third defense layer in the layered defense architecture (see [cowork-instructions.md](cowork-instructions.md)). Periodically scans whether the README index is in sync with the actual file structure, catching files that drifted out of the index or stale entries pointing nowhere.

---

## When to Run

- **You ask for it**: "run a vault audit", "scan the vault for drift", "audit my index"
- **The Agent suggests it**: after a burst of file changes, after a long stretch without one, or when the user reports the Agent reading the wrong files
- **On a schedule** (optional): some founders make this a weekly task — auto-run it Sunday night and write the date into `memory-summary.md`

---

## How the Agent Runs It

### Step 1: List Actual Vault Files

List every `.md` file in the Vault, excluding folders that don't need indexing:

```bash
cd "[Your Vault path]"
find . -type f -name "*.md" \
  -not -path "./.obsidian/*" \
  -not -path "./.git/*" \
  | sort
```

(Adjust the excluded paths to match your setup — see "Ignore List" below.)

### Step 2: Extract Indexed Files

Read the **root `README.md`** and list every file and folder it mentions.

If you've added per-folder index files (e.g., `memory/INDEX.md`, `projects/INDEX.md`), read those too and merge into the indexed-files list. The basic framework only requires the root README — per-folder indexes are an optional optimization once a folder grows past ~10 files.

### Step 3: Compare and Find Drift

Two categories of drift:

- **🔴 Files exist but not indexed**: present in the Vault, missing from the README → needs to be added
- **🟡 Index entry is stale**: README mentions a file that doesn't exist anymore → README needs to be cleaned up

### Step 4: Report to the User

Use this format:

```
📋 Vault Audit (YYYY-MM-DD)

── Summary ──
Actual files: N
Indexed: M
Drift: X

── 🔴 Not indexed (N items) ──
- path/to/file1.md — [Agent's suggested one-line description]
- path/to/file2.md — [Agent's suggested one-line description]

── 🟡 Stale README entries (N items) ──
- path/to/old.md — file no longer exists

── 💡 Suggestions ──
- Which should be added to the index
- Which can be safely ignored (scratch / one-off deliverables)
- Which should be moved or deleted
```

### Step 5: User Decides

- **Add to index** → Agent updates the README on the spot
- **Don't index** (scratch, deliverable) → Agent notes it but will surface it again next audit (unless the user says "permanently ignore")
- **Delete the file** → User does it themselves, or explicitly tells the Agent to

---

## Key Principles

1. **Don't auto-modify the README.** Only report drift. Modifications need user approval.
2. **Scratch files can stay un-indexed, but must still appear in the report.** Otherwise "missed by accident" and "deliberately ignored" become indistinguishable.
3. **After every audit**, leave a "Last audit: YYYY-MM-DD" note in `memory-summary.md` so the next decision about when to audit has a baseline.
4. **Don't write audit results into `memory/`.** Audits are operational, not decisions or lessons. They have no long-term value.
5. **If you use per-folder indexes**: a missing individual file → update that folder's `INDEX.md`; a missing folder → update the root README.

---

## Ignore List (files that don't need to be indexed)

The following are normal even if they're not in the README:

- **Scratch coordination files** at the Vault root (e.g., `recovery-note.md`, deleted after use)
- **One-off deliverables** at the Vault root (e.g., `github-profile-README.md`, handed off and forgotten)
- Inside `skills/`: support files other than the main skill file (scripts, references, agents)
- Obsidian local settings (`.obsidian/`)
- Hidden / system folders (`.git/`, `.DS_Store`, etc.)

Everything else **should be indexed by default**.

---

## Why This Matters

Without a periodic audit:

- The README index slowly drifts out of date
- The Agent starts missing files because they're not in its map
- You don't notice until the Agent confidently misses something important

The audit isn't about catching the Agent — it's about catching yourself. After a few months, you'll have hundreds of files. The Forced Rules in your Global Instructions (first defense) and the After-action SOP (second defense) catch most cases at write time, but neither catches files you moved manually in Finder, or renamed in Obsidian without updating the index. That's what this third layer is for.

---

*Run frequency suggestion: monthly, or any time you notice the Agent reaching for files you don't recognize.*
