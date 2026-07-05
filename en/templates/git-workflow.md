# Git Workflow SOP: Day-to-Day and Recovery

> Last updated: v1

The setup-guide gets you started; this SOP covers what comes after: daily sync, second-machine setup, and how to dig out of conflicts. Save this in your Vault's `sop/` folder, and skim it whenever you forget the steps.

---

## Daily Sync (Auto)

With Obsidian Git installed, every change auto-commits and pushes every 30 minutes (or whatever interval you set). You don't run any commands.

To force a sync immediately: in Obsidian, press `Ctrl + P` → type `git` → choose **Create backup**.

---

## Manual Sync (Backup)

If the plugin acts up, open a terminal in your Vault folder:

```bash
cd "[your Vault path]"
git add .
git commit -m "manual sync"
git push
```

Use this when:
- Obsidian Git refuses to sync and shows an error
- You're editing files in a tool other than Obsidian (e.g., a code editor) and want a clean commit message

---

## Second Machine Setup

### 1. Install Git

Download from https://git-scm.com. During install, when you see "Adjusting your PATH environment," select **Git from the command line and also from 3rd-party software** (the default).

After install, **close every terminal window** and open a fresh one. Verify:

```bash
git --version
```

Set your identity (one-time):

```bash
git config --global user.email "your-github-email"
git config --global user.name "your-github-username"
```

### 2. Install Obsidian

Download from https://obsidian.md. Install but don't open it yet.

### 3. Clone the Vault

```bash
cd Documents
git clone https://github.com/[your-username]/[your-repo].git
```

The whole Vault (every file and folder) downloads to `Documents/[your-repo]`.

### 4. Open in Obsidian

Open Obsidian → "Open folder as vault" → point at `Documents/[your-repo]`.

### 5. Install Obsidian Git plugin

Settings ⚙️ → Community plugins → turn off Restricted mode → Browse → search **Obsidian Git** → Install → Enable → set "Auto commit-and-sync interval" to `30`.

Done. Every file matches your other machine, and future changes auto-sync.

---

## When Sync Conflicts Happen

The most common case: you edited file `X` on machine A, edited the same file on machine B without syncing first, and now the second push refuses.

### Recovery steps

1. **Stop editing on the conflicting machine.** Don't make it worse.
2. In Obsidian's Git plugin, check for "merge conflict" notifications. Open the affected file. Git will have left conflict markers like:
   ```
   <<<<<<< HEAD
   your local version
   =======
   the remote version
   >>>>>>> origin/main
   ```
3. **Manually choose what to keep.** Delete the markers and keep the merged content.
4. Save → in the plugin, "Commit all changes" → "Push".

### If it's beyond your repair

If conflicts get tangled (e.g., dozens of files, binary files, you don't remember what you changed), the safer move is **back up your local copy elsewhere first**, then `git reset --hard origin/main` to match the remote, then manually re-apply your changes from the backup. Don't reset without backing up. It's destructive.

---

## Viewing Change History

The fastest way: go to your repo on GitHub → click "commits". Every backup is one entry, with a timestamp and the file diff.

For a specific file: open it on GitHub → "History" button (top right) → see every version.

For local viewing in Obsidian Git: `Ctrl + P` → `git: open history view`.

---

## What Goes in `.gitignore` (and What Doesn't)

The setup-guide ships a starter `.gitignore`. Beyond that:

- **Add to .gitignore**: temporary scratch files you don't want in version history, machine-specific config, anything sensitive that slipped in
- **Don't add**: anything inside your main folders (`memory/`, `context/`, etc.). Those are the whole point of having a Vault

If you accidentally committed a file that should be ignored, see the next section.

---

## Removing a Sensitive File from History

If you committed something sensitive (API key, password, internal doc):

1. Add it to `.gitignore` first
2. Remove it from the current commit:
   ```bash
   git rm --cached path/to/sensitive-file
   git commit -m "remove sensitive file from tracking"
   git push
   ```
3. **Important**: this only removes it from future commits. The file is still in past commits. If it's truly sensitive (like a leaked API key), **rotate the credential**: assume it's already public.
4. For full purge from history (advanced): use `git filter-repo` or BFG Repo-Cleaner. Out of scope for this SOP.

---

## When Things Look Weird and You're Stuck

A safe diagnostic move that doesn't break anything:

```bash
git status
git log --oneline -10
```

This tells you:
- What's modified, staged, or untracked right now
- The last 10 commits

Send this output to your AI agent. It can usually diagnose from these two commands alone.

---

*Run frequency: read once when setting up your second machine. Reread when something breaks.*
