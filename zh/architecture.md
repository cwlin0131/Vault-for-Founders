# Vault for Founders：架構設計

> 這份文件說明知識庫的架構設計與取捨。

---

## 設計原則

這套架構是為創業者設計的。創業者跟 AI 的互動不只是聊天或知識累積，還涉及公司營運、產品策略、招募、人脈、專案管理。架構需要同時覆蓋「個人」和「公司」兩個維度。

核心取捨：

- **寧可少不要多** — 資料夾和檔案在需要時才建立，不預建空殼
- **Agent 是主要讀者** — 結構、命名、索引都優先考慮 LLM 的檢索效率，不是人的瀏覽習慣
- **一份文件一個職責** — 人格設定、記憶摘要、公司背景各自獨立，不混在一起

---

## 資料夾架構與設計理由

### 根目錄（每次啟動必讀）

| 檔案 | 職責 |
|------|------|
| README.md | Vault 索引，Agent 靠這份地圖決定讀什麼 |
| agent-persona.md | Agent 的人格設定、協作方式、溝通風格 |
| memory-summary.md | 長期記憶精華版：當前焦點、重大決策、教訓 |

這三份是 Agent 每次啟動都會讀的，其他檔案依任務需要才讀。

### 個人維度

| 資料夾 | 用途 | 什麼時候建 |
|--------|------|-----------|
| identity/ | 創辦人的決策風格、價值觀、溝通偏好 | 第一天就建 |
| memory/ | 每筆重要決策的完整紀錄 | 有重大決策時逐筆建 |
| sop/ | 操作流程（久沒用容易忘的步驟） | 同一件事問第二次時建 |

### 公司維度

| 資料夾 | 用途 | 什麼時候建 |
|--------|------|-----------|
| context/ | 公司背景、產品策略、競爭環境、對外溝通素材 | 第一天就建 |
| operations/ | 公司營運硬資訊（法人設立、銀行、稅務） | 有營運資料時建 |
| hr/ | 招募與人事，按檔期分資料夾 | 開始招人時建 |
| projects/ | 進行中的項目（出差、活動、市場計畫） | 有跨天項目時建 |
| people/ | 重要聯絡人的背景與互動紀錄 | 人脈開始複雜時建 |

---

## 索引分層（v2）

Vault 越長越大之後，單一 README 索引會撐不住。實戰下來，資料夾分成兩種：

- **變動慢的資料夾**（identity/、context/、operations/）：直接在 Root README 逐檔詳列。它們很少變，索引不容易過期。
- **會堆疊的資料夾**（memory/、projects/、hr/）：本質是時間序 log，會一直加。各自帶一份 `INDEX.md`，Root README 只留一行入口提示。

檢索因此變成三層：

```
Root README          ← 只放結構；會堆疊的資料夾只留一行 pointer
  ↓
各資料夾 INDEX.md     ← memory/、projects/、hr/ 的完整清單 + 狀態
  ↓
個別檔案              ← YAML frontmatter（updated / tags / summary）作為搜尋層
```

為什麼：Root README 每次啟動都會讀，成本敏感，不能讓它持續長大。`INDEX.md` 把增長隔離在資料夾內。frontmatter 補 INDEX 抓不到的跨維度查詢。

什麼時候該加 `INDEX.md`：資料夾超過大約 10 個檔案、而且預期會繼續長的時候。

---

## 每次必讀的三檔有 attention budget（v2）

`README.md`、`agent-persona.md`、`memory-summary.md` 是每次啟動必讀的三檔，它們的總長度是 Agent 開工前就要付的固定稅。只有「每個 session 都必須 fresh in mind」的內容才能放這裡：核心人格、查詢優先序、強制規則、當前焦點。

**外部化 trigger**（兩者同時成立就搬走）：

1. 該段超過 5 行
2. 該段不是每個 session 都會用到

搬去哪：行為機制 → `identity/`；操作流程 → `sop/`；變更紀錄 → 對應 `INDEX.md` 或 `sop/vault-changelog.md`。原地留一行 pointer。

每次對三檔新增內容時，順手 self-check 一次。寧可搬走之後發現需要再搬回來，也不要讓三檔慢性肥大。

---

## 維護機制：多層防禦（v2）

只靠一條「記得同步索引」的指令不可靠：Agent 會漏，你會忘。改用四層疊加：

1. **主動 — Global Instructions 的強制規則**：改了檔案就在同一個 response 內同步索引；寫任何對外內容前先讀 voice-and-tone
2. **安全網 — 收官檢查**：任務結束時再確認一次索引和相關文件有更新（[templates/after-action.md](templates/after-action.md)）
3. **定期體檢 — Vault audit**：隨時可跑或排程跑，比對實際檔案 vs 索引（[templates/vault-audit.md](templates/vault-audit.md)）
4. **背景紀律 — Vault README 內的強制規則段**：Agent 每次啟動讀 README 就會再看到一次鐵律（[templates/vault-readme.md](templates/vault-readme.md)）

任何單層都會漏，四層疊起來能把 drift 壓到接近零。

---

## 每日實戰長出來的 patterns（v2）

這些不在最初的設計裡，是用出來的：

- **最火熱專案的 root dashboard。** 當某個專案主宰你的每一週（產品上線、申請案、募資），在 vault root 給它一頁 dashboard，例如 `launch-dashboard.md`：當前狀態、今日 P0、關鍵日期、常查的 ID。細節留在 project 資料夾，dashboard 只是駕駛艙視角。
- **`projects/archive/`。** 收官的專案搬進去，讓 active 清單保持乾淨。搬的時候記得同步連結。
- **skills/ 只放自己寫的 skill。** 不要把 AI 工具內建的 skills 複製進 vault：檔案大、版本會漂移、工具本身就有。
- **公司維度之外的個人軌。** 一個小資料夾（todo、done-log、重要聯絡人）管理你個人的運轉節奏。done-log 累積起來就是你的 portfolio。

---

## Agent 啟動流程

不管用什麼工具（Cowork、OpenClaw、Claude Code），Agent 每次啟動的閱讀順序都一樣：

1. **README.md** 了解 Vault 的結構和地圖
2. **agent-persona.md** 了解自己的角色和協作方式
3. **memory-summary.md** 快速掌握近期重要事項
4. 依任務需要，讀對應資料夾的內容

這個順序寫進工具的自訂指令裡，Agent 會自動執行。

---

## 跟其他知識庫架構的差異

大部分 AI 知識庫架構是為超級個體或自由工作者設計的，偏向個人生活管理與知識累積。Vault for Founders 多了創業者需要的公司維度：context、operations、hr、projects、people。

另一個差異是「Agent 不只是記憶體，是合夥人」。persona 的設計不只定義語氣和風格，還定義了什麼時候該挑戰創辦人的想法、什麼時候以創辦人為主。這讓 Agent 從工具升級為思考夥伴。

---

*最後更新：2026/07/05（v2：索引分層、attention budget、多層防禦維護、實戰 patterns）*
