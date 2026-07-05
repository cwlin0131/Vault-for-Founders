# Vault for Founders: Architecture

> Design decisions and trade-offs behind the knowledge base structure.

---

## Design Principles

This architecture is built for founders. A founder's interaction with AI goes beyond chat or personal knowledge management. It involves company operations, product strategy, hiring, networking, and project management. The architecture needs to cover both "personal" and "company" dimensions.

Core trade-offs:

- **Less is more**: Folders and files are created only when needed, not pre-scaffolded as empty shells
- **Agent is the primary reader**: Structure, naming, and indexing prioritize LLM retrieval efficiency over human browsing habits
- **One file, one responsibility**: Persona settings, memory summaries, and company context are each independent, never mixed

---

## Folder Architecture and Rationale

### Root Directory (read on every startup)

| File | Responsibility |
|------|----------------|
| README.md | Vault index: the Agent uses this map to decide what to read |
| agent-persona.md | Agent's personality, collaboration style, communication norms |
| memory-summary.md | Distilled long-term memory: current focus, major decisions, lessons |

These three files are read on every Agent startup. Everything else is read as needed.

### Personal Dimension

| Folder | Purpose | When to create |
|--------|---------|----------------|
| identity/ | Founder's decision style, values, communication preferences | Day one |
| memory/ | Full record of each important decision | As major decisions happen |
| sop/ | Operating procedures (steps you forget when you haven't done them in a while) | When you ask about the same thing twice |

### Company Dimension

| Folder | Purpose | When to create |
|--------|---------|----------------|
| context/ | Company background, product strategy, competitive landscape, messaging | Day one |
| operations/ | Hard operational data (incorporation, banking, taxes) | When you have operations data |
| hr/ | Hiring and people, organized by cohort | When you start hiring |
| projects/ | Active projects (trips, events, market plans) | When you have multi-day projects |
| people/ | Key contacts: background and interaction notes | When your network gets complex |

---

## Index Layering (v2)

A single README index stops scaling as the vault grows. In practice, folders split into two kinds:

- **Slow-changing folders** (identity/, context/, operations/): listed file by file directly in the root README. They change rarely, so the index stays accurate.
- **Growing folders** (memory/, projects/, hr/): time-ordered logs that keep accumulating. Each carries its own `INDEX.md`; the root README keeps only a one-line entry pointing to it.

Retrieval then works in three layers:

```
Root README          ← structure only; growing folders get a one-line pointer
  ↓
INDEX.md per folder  ← full listing + status for memory/, projects/, hr/
  ↓
Individual files     ← YAML frontmatter (updated / tags / summary) as the search layer
```

Why: the root README is read on every startup, so it is cost-sensitive and must not keep growing. `INDEX.md` isolates the growth. Frontmatter catches the cross-cutting queries the indexes can't.

Trigger for adding an `INDEX.md`: the folder passes roughly 10 files and you expect it to keep growing.

---

## The Three Always-Read Files Have a Budget (v2)

`README.md`, `agent-persona.md`, and `memory-summary.md` are read on every startup. Their combined size is a fixed attention tax your Agent pays before doing any work. Only content that must be fresh in mind every session belongs there: core persona, retrieval priorities, forced rules, current focus.

**Externalization trigger** (both must hold):

1. The passage is longer than ~5 lines
2. It is not used in every session

Move it out: behavioral mechanisms go to `identity/`, procedures go to `sop/`, change history goes to the folder's `INDEX.md` or `sop/vault-changelog.md`. Leave a one-line pointer behind.

Re-run this check every time you add to any of the three files. Moving something out and later bringing it back is cheap; letting the core files bloat is not.

---

## Maintenance: Layered Defense (v2)

A single "keep the index in sync" instruction is not reliable: the Agent will miss it, and you will forget to check. Stack four layers instead:

1. **Active (Forced Rules in Global Instructions)**: sync indexes in the same response that changed the files; read voice-and-tone before drafting anything public
2. **Safety net (After-action review)**: at task close, re-check that indexes and related docs were updated ([templates/after-action.md](templates/after-action.md))
3. **Periodic checkup (Vault audit)**: on demand or on a schedule, compare actual files against the indexes ([templates/vault-audit.md](templates/vault-audit.md))
4. **Background discipline (Forced Rules in the vault README itself)**: the Agent re-reads the rules on every startup ([templates/vault-readme.md](templates/vault-readme.md))

Any single layer leaks. Four stacked layers bring drift close to zero.

---

## Patterns That Emerged from Daily Use (v2)

Patterns that weren't in the original design but earned their place:

- **Top-level dashboard for the hottest project.** When one project dominates your weeks (a launch, a filing, a fundraise), give it a one-page dashboard at the vault root, e.g. `launch-dashboard.md`: current status, today's P0, key dates, quick-reference IDs. Details stay in the project folder; the dashboard is just the cockpit view.
- **`projects/archive/`.** Move closed projects there so the active list stays readable. Update links when you move them.
- **skills/ holds only skills you wrote.** Don't copy your AI tool's built-in skills into the vault; they're large, they version-drift, and the tool already ships them.
- **A personal track alongside the company dimension.** A small folder (todo, done-log, key contacts) for your personal operating rhythm. The done-log doubles as a portfolio of shipped work.

---

## Agent Startup Sequence

Regardless of the tool (Cowork, OpenClaw, Claude Code), the Agent follows the same reading order on every startup:

1. **README.md**: Understand the Vault's structure and map
2. **agent-persona.md**: Understand its role and collaboration style
3. **memory-summary.md**: Quickly grasp recent priorities
4. Read additional folders as needed for the task at hand

This sequence is baked into the tool's custom instructions, so the Agent executes it automatically.

---

## How This Differs from Other Frameworks

Most AI knowledge base frameworks are designed for solo creators or freelancers, focused on personal life management and knowledge accumulation. Vault for Founders adds the company dimension that founders need: context, operations, hr, projects, people.

Another difference: the Agent isn't just a memory store. It's a cofounder. The persona design doesn't just define tone and style. It defines when the Agent should challenge the founder's thinking and when to defer. This upgrades the Agent from a tool to a thinking partner.

---

*Last updated: 2026/07/05 (v2: index layering, attention budget, layered-defense maintenance, daily-use patterns)*
