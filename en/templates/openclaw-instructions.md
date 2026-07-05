# OpenClaw Global Instructions Template

> Last updated: v1, split from cowork-instructions.md to reflect OpenClaw's distinct write scope

## What Is This

This is the Global Instructions configuration for OpenClaw, separate from Cowork. The two tools have different startup behaviors and different default write scopes. Using the same instructions for both leads to confusion (e.g., OpenClaw thinking it should rewrite your root README on its own).

## How OpenClaw Differs from Cowork

|  | Cowork | OpenClaw |
|---|---|---|
| Write scope | Whole Vault (root README, indexes, all folders) | **Defaults to writing only inside `openclaw/`**; anything else needs your confirmation |
| Startup loading | Manually reads README + agent-persona + memory-summary (per Global Instructions) | **Auto-loads `openclaw/MEMORY.md`** at startup; Global Instructions don't need to specify it |
| Index-sync rule | Adding files must update index | Doesn't apply (doesn't touch root Vault by default) |

So they need separate Global Instructions. Otherwise OpenClaw will think it's responsible for keeping your root README in sync, and may overwrite things you didn't intend.

## How to Use

1. Open OpenClaw → Settings → Global Instructions (or equivalent config location)
2. Copy the content below
3. Replace `[Your Vault Name]` with your actual Vault folder name
4. Confirm OpenClaw can read your Vault path
5. **Every machine needs its own setup** (global instructions don't sync across machines)

## Instructions Content

```
## Startup Loading
openclaw/MEMORY.md is auto-loaded (long-term core memory). Recent logs in openclaw/memory/ are also auto-loaded.

When you need deeper context about the user, proactively read:
1. [Your Vault Name]/README.md
2. [Your Vault Name]/agent-persona.md
3. [Your Vault Name]/memory-summary.md

Skill files: prefer [Your Vault Name]/skills/ first. If the Vault doesn't have a corresponding skill, fall back to OpenClaw's built-in skills.

## Forced Rules (not suggestions; required for task completion)
1. **Write scope limit**: by default, only write inside openclaw/. Any modification outside openclaw/ (root README, memory/, projects/, hr/, sop/, identity/, etc.) requires explicit user confirmation before commit/push.
2. **Writing rules for public content**: when helping draft anything that will be seen by people other than the user (LinkedIn, GitHub Release notes, press, pitch, landing copy, public issues / PRs, etc.), read [Your Vault Name]/identity/voice-and-tone.md first and follow its rules. When in doubt whether something is public, treat it as public.

After significant tasks, follow the after-action SOP (sop/after-action.md) and remind the user, limited to changes inside openclaw/ or explicitly confirmed modifications.
```

## Why the Write-Scope Limit Matters

OpenClaw is a coding agent: by default it edits files freely. That's fine inside its own `openclaw/` working folder, but **dangerous in your Vault root**, because:

- Your Vault root README is hand-curated and cross-referenced. An autonomous rewrite breaks the index.
- Your `memory/` files are append-only by convention (they record decisions). Edits look like history rewriting.
- Your `identity/` and `context/` files are personal: drafted with you in conversation, not generated.

The forced rule turns OpenClaw into "scoped autonomy + explicit handoff for everything else." This is the safer default.

## Skills Folder Behavior

If you have `[Your Vault Name]/skills/` populated, OpenClaw will prefer those over its built-ins. This lets you override or extend specific skills (e.g., your own custom skill taking precedence). If a skill doesn't exist in your Vault, it falls back automatically.

## Notes

- The Cowork equivalent is in [cowork-instructions.md](cowork-instructions.md). **Don't paste them into each other's settings**, the rules are different.
- If your OpenClaw setup runs in WSL2 / Linux, the Vault path might be different from your Obsidian path (e.g., `/home/user/Documents/Your-Vault/` vs `D:\Your-Vault\`). Make sure both tools point at the same Git working tree.
- When rules evolve, **remember to update Global Instructions on every machine**, the easiest thing to forget.
- The same layered defense model applies (see [cowork-instructions.md](cowork-instructions.md) "Layered Defense" section). The third layer ([vault-audit.md](vault-audit.md)) is tool-agnostic and runs the same regardless of which agent triggers it.
