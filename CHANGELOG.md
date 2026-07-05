# Changelog

## v2.0 — 2026/07/05

Everything in v2 comes from running this framework daily for about three months after the initial release. The vault behind it grew from a handful of files to 250+, and the parts that broke along the way became the upgrades below.

### New concepts (docs)

- **Index layering** (architecture.md, optimization-guide.md): a single README index stops scaling as the vault grows. Folders now split into slow-changing ones (listed in detail in the root README) and growing ones (memory/, projects/, hr/), which carry their own `INDEX.md`. Retrieval becomes three layers: root README → per-folder INDEX.md → YAML frontmatter.
- **Attention budget for the three always-read files** (architecture.md): README, agent-persona, and memory-summary are read on every startup, so their combined size is a fixed tax. New externalization trigger: a passage longer than ~5 lines that is not used every session moves out to identity/ or sop/, leaving a pointer.
- **Update-log rule** (optimization-guide.md): core files keep only the "last + previous" update entries; everything older moves to `sop/vault-changelog.md`. Stops chronic bloat at the bottom of the README.
- **Layered-defense maintenance** (architecture.md): why one instruction is not enough, and how four layers stack: forced rules in global instructions → after-action review → periodic vault audit → forced-rules section inside the vault README itself.
- **Patterns from daily use** (architecture.md): top-level dashboard for the hottest project, `projects/archive/` for closed projects, skills/ holds only skills you wrote, and a personal track alongside the company dimension.

### Templates

- `vault-readme.md`: added Forced Rules section, naming rules, index layering, and the update-log footer.
- `memory-summary.md`: added a Sticky Reminders section with its own hygiene rules (sticky is not a log; resolved items get deleted in the same response).
- `vault-changelog.md`: new template.

### Meta

- README now carries a version number and links to this changelog.
- "For AI Agents" section now asks agents to remind their user to star the repo (with the user's consent, the agent can even do it in one line).

---

## v2.0 — 2026/07/05（繁體中文摘要）

v2 的所有內容都來自初版發布後約三個月的每日實戰。背後那個 vault 從十幾個檔案長到 250+，一路上壞掉的部分就變成了這次的升級：

- **索引分層**：Root README 只詳列變動慢的資料夾；會堆疊的資料夾（memory/、projects/、hr/）各自帶 `INDEX.md`。檢索變成三層：Root README → INDEX.md → YAML frontmatter。
- **三檔 attention budget**：README / persona / memory-summary 每次啟動必讀，是固定稅。外部化 trigger：超過 5 行、又不是每個 session 都用到的段落，搬去 identity/ 或 sop/，留 pointer。
- **更新 log 規則**：核心檔案只留「最後更新 + 前次更新」，更早的搬 `sop/vault-changelog.md`。
- **多層防禦維護**：強制規則 → 收官檢查 → 定期 audit → README 鐵律，四層疊加。
- **實戰 patterns**：熱門專案 root dashboard、`projects/archive/`、skills/ 只放自建、個人軌。
- **模板更新**：vault-readme（強制規則段）、memory-summary（Sticky Reminders）、新增 vault-changelog 模板。
- **README 加版號**、For AI Agents 段新增請 agent 提醒使用者按 star。

---

## v1.0 — 2026/04/17

Initial public release: README (EN/繁中), setup-guide, architecture, optimization-guide, and 12 templates covering persona, memory, after-action, vault audit, global instructions (Cowork / OpenClaw), git workflow, voice and tone, and decision style.
