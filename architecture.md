# 架構設計 — 為什麼這樣建 AI 知識庫

---

## 為什麼不用 Notion

當知識庫的主要讀者是 AI Agent 而不是人，Notion 有幾個根本問題：

- Agent 無法直接讀取，必須透過 API（需要 token、有 rate limit、額外成本）
- 沒有原生版本控制，改壞了很難回溯
- Block 結構匯出的 .md 有格式雜訊
- 資料被鎖在平台上，換工具等於重來

Obsidian + Git 的組合：Agent 直接讀本機 .md 檔（零成本），Git 做版本控制（改壞可回溯），GitHub 做備份（換電腦一行指令就能恢復）。

Notion 繼續用在團隊協作和專案管理，不適合當 Agent 知識庫的主幹。

---

## 跟 Muse 晶種的比較

[Muse Crystal Seed](https://github.com/frank890417/muse-crystal-seed) 是一套很完整的 Agent 知識庫架構。以下是跟它的對照，以及我做了什麼調整。

### 檔案對照

| Muse 架構 | 我的做法 | 差異說明 |
|-----------|----------|----------|
| SOUL.md（靈魂） | cub-persona.md | 合併了 SOUL + IDENTITY + AGENTS 成一份，因為對我來說這三者不需要分開管理 |
| USER.md（主人是誰） | identity/identity.md | 放進資料夾，方便之後擴充（價值觀、溝通風格等） |
| MEMORY.md（長期記憶） | memory-summary.md | 同樣是精華版，搭配 memory/ 資料夾的逐筆紀錄 |
| AGENTS.md（工作協議） | 合併進 cub-persona.md | 協作規則跟人格設定放一起更直覺 |
| HEARTBEAT.md（心跳巡邏） | 未建立 | 這是 OpenClaw 24/7 常駐用的，我主要用 Cowork，暫不需要 |
| TOOLS.md（工具箱） | 未建立 | 可之後補，目前工具鏈簡單不需要額外文件 |
| BOOTSTRAP.md（啟動引導） | Cowork 指令設定 | 用 Cowork 的 Instructions 功能取代，效果一樣 |
| skills/（技能） | sop/ + skills/ | SOP 和 Skills 分開管理 |

### 我多了什麼

Muse 的設計偏向「個人生活管理 + 知識累積」。我的知識庫是給創業用的，所以多了這些：

- **context/** — 公司背景、產品策略、競爭環境、對外溝通素材
- **projects/** — 進行中的項目（出差、活動、市場計畫）
- **operations/** — 公司營運資料（法人設立、帳號管理）
- **people/** — 人脈管理

這些是 Muse 架構裡沒有的，但對 CEO / 創業者來說是必要的。

---

## 兩種建置路徑

### 路徑 A：手動 Walkthrough

跟 AI 對話，一步一步建。AI 帶你安裝工具、建結構，核心內容（identity、persona）透過問答討論後寫入。

**優點：** 對每個設計決定有清楚理解，核心內容是自己想過的。

**缺點：** 比較慢。

### 路徑 B：Agent 自動建置

把手冊或 [Muse 晶種指南](https://github.com/frank890417/muse-crystal-seed/blob/main/CRYSTAL-SEED.md) 直接餵給 AI，讓它照著建。

**優點：** 速度快。

**缺點：** 核心內容會是模板填空，Agent 讀了不會真的「了解你」。

### 推薦：混合路徑

**結構交給 Agent，內容自己想。**

機械性工作（建資料夾、建模板、設定 Git）讓 Agent 處理。identity、persona、context 這些核心內容，跟 AI 討論後再寫入。

---

## Agent 啟動流程

不管用什麼工具（Cowork、OpenClaw、Claude Code），Agent 每次啟動的閱讀順序都一樣：

1. **README.md** — 了解 Vault 的結構和地圖
2. **agent-persona.md** — 了解自己的角色和協作方式
3. **memory-summary.md** — 快速掌握近期重要事項
4. 依任務需要，讀對應資料夾的內容

這個順序可以寫進工具的自訂指令裡，讓 Agent 自動執行。

---

## 維護原則

- **Single Source of Truth** — 一個事實只寫在一個地方
- **建檔前先搜尋** — 先找有沒有現有的，有就更新
- **每份文件標注更新日期** — 讓 Agent 知道資訊的新鮮度
- **做了不記 = 沒做** — 重要決策後跑收官流程
- **敏感資訊不進 repo** — API key、密碼等用 .gitignore 排除
