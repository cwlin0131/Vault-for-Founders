# CW Vault 建置流程紀錄

> 紀錄日期：2026/04/14
>
> 這份文件紀錄從零開始建立 Obsidian + Git AI 知識庫的完整流程，包含每一步做了什麼、為什麼這樣做、以及最終的成果。

---

## 為什麼要建這個

知識庫的主要消費者是 AI Agent，不是人。Notion 的問題是 Agent 無法直接讀取（需 API）、沒有版本控制、資料被鎖在平台上。Obsidian + Git 讓 Agent 直接讀本機 .md 檔，零成本、有版本控制、資料永遠在自己手上。

參考架構：[Muse Crystal Seed 晶種指南](https://github.com/frank890417/muse-crystal-seed/blob/main/CRYSTAL-SEED.md)

建置方式：混合路徑 — 結構交給工具，核心內容（identity、persona）跟 AI 討論後寫入。

---

## Phase 1：基礎建設

### 1.1 安裝工具

1. 安裝 Git（git-scm.com）
   - 注意選「Git from the command line and also from 3rd-party software」
   - 安裝後關掉所有 cmd 再開新的才能用
   - 設定身份：`git config --global user.email` + `user.name`

2. 安裝 Obsidian（obsidian.md）
   - 建立 Vault，記住路徑

### 1.2 建立 GitHub Repo

- Private repo：`cwlin0131/CW-Vault`（知識庫本體）
- Public repo：`cwlin0131/AI-Vault`（公開手冊）

### 1.3 連結 Vault 與 GitHub

```
cd "Vault路徑"
git init
git remote add origin https://github.com/cwlin0131/CW-Vault.git
git branch -M main
git add .
git commit -m "init vault"
git push -u origin main
```

### 1.4 安裝 Obsidian Git 外掛

- 社群外掛 → 關掉安全模式 → 搜尋 Obsidian Git → 安裝啟用
- Auto commit-and-sync interval 設 30 分鐘
- 每個 Vault 要各自裝一次
- 手動立即同步：`Ctrl + P` → `git` → Create backup

---

## Phase 2：Vault 結構

### 2.1 資料夾結構

```
CW Vault/
├── README.md                 ← Vault 索引，Agent 第一個讀
├── cub-persona.md            ← Cub 人格設定與協作方式
├── memory-summary.md         ← 長期記憶摘要（精華版）
│
├── identity/
│   └── identity.md           ← CW 決策風格、品質標準
│
├── context/
│   ├── portaly.md            ← 公司總覽
│   ├── portaly-vibe-us-strategy.md  ← Vibe 完整策略
│   ├── portaly-vibe-pitch.md        ← 對外溝通素材
│   └── portaly-vibe-progress.md     ← Vibe 產品進度
│
├── memory/                   ← 每筆決策紀錄
├── sop/
│   ├── git-workflow.md       ← Git 同步 + 換電腦流程
│   └── after-action.md       ← 收官流程
│
├── operations/
│   └── us-entity.md          ← 美國公司設立資料
│
├── projects/
│   ├── sf-trip-overview.md   ← SF 兩周行程
│   └── stripe-sessions-strategy.md  ← Sessions 攻略
│
├── people/                   ← 重要聯絡人（待建）
└── skills/                   ← Skills 檔案（待搬遷）
```

### 2.2 各檔案的角色

| 檔案 | 對應 Muse 架構 | 功能 |
|------|---------------|------|
| README.md | README.md | Agent 的導覽地圖 |
| cub-persona.md | SOUL.md + IDENTITY.md + AGENTS.md | Agent 的靈魂與工作協議 |
| identity/identity.md | USER.md | CW 是怎樣的人 |
| memory-summary.md | MEMORY.md | 長期記憶精華版 |
| sop/after-action.md | skills/after-action | 收官流程 |
| context/ | （Muse 沒有） | 商業脈絡，CW Vault 獨有 |
| projects/ | （Muse 沒有） | 進行中項目，CW Vault 獨有 |

---

## Phase 3：核心內容撰寫

### 3.1 Cub Persona（跟 AI 討論產出）

透過問答確定：
- 定位：AI co-founder（不是小助手）
- 協作規則：什麼時候挑戰 CW、什麼時候以 CW 為主
- 判斷框架：過去決策矛盾 → 提出 / 客觀數據 → 主動給 / 用戶洞察 → 以 CW 為主 / 決心已定 → 執行
- 邊界：對外溝通口吻以 CW 為主

### 3.2 Identity（跟 AI 問答產出）

透過情境題討論出：
- 決策風格：先評估再加速，遇到 opportunity window 果斷抓住
- 最大後悔：行動太慢（尤其不熟悉的領域）
- 品質標準：成本效益衡量，有限資源內的最大成功機率

### 3.3 Context（從過去討論整理）

AI 從過往對話中彙整公司背景、產品策略、競爭環境，CW 確認後放入。

---

## Phase 4：工具串接

### 4.1 Cowork 授權 Vault

Claude Desktop → 新對話 → 附件 → 選 CW Vault 資料夾

### 4.2 Cowork 指令設定

Claude Desktop → 設定 → Cowork → Instructions：

```
每次新對話開始時，先讀取以下兩份檔案：
1. CW Vault/README.md
2. CW Vault/cub-persona.md

讀完後依照 cub-persona.md 的角色定義與協作方式跟我互動。
其他檔案依任務需要再讀取。
```

### 4.3 驗證

請 Cowork 寫一筆 memory，確認能讀寫 Vault → 成功。

---

## Phase 5：收官機制

### 5.1 Memory Summary

根目錄放 `memory-summary.md`，彙整重要決策、教訓、CW 的模式。Cub 啟動時讀這份快速掌握全貌。

### 5.2 After Action SOP

`sop/after-action.md` 定義收官三步：寫 memory → 更新相關文件 → 更新 summary。Cub 會主動提醒跑這個流程。

---

## 建置完成 Checklist

- [x] Git + Obsidian 安裝
- [x] GitHub Private Repo 建立並連結
- [x] Obsidian Git 外掛設定（30 分鐘自動同步）
- [x] 資料夾結構建立
- [x] README.md — Vault 索引
- [x] cub-persona.md — Agent 人格與協作方式
- [x] identity/identity.md — CW 決策風格
- [x] context/ — 公司與產品 context
- [x] sop/git-workflow.md — Git 操作流程
- [x] sop/after-action.md — 收官流程
- [x] memory-summary.md — 長期記憶摘要
- [x] Cowork 授權 Vault + 指令設定
- [x] 驗證 Cowork 可讀寫 Vault
- [x] projects/ — SF trip + Sessions 策略
- [x] 公開手冊上線（AI-Vault repo）
- [ ] Skills 搬遷到 Vault（待做）
- [ ] people/ 開始建檔（用到再補）

---

## 日後維護

- 用 Cowork 日常讀寫 Vault，累積 memory
- 重要決策後跑 after-action 流程
- 每週更新 memory-summary.md
- README.md 隨結構調整更新
- Skills 搬進 Vault 後更新 Cowork 指令

---

*建置方式：在 Claude.ai 討論核心內容 + Cowork 執行讀寫 + Obsidian Git 自動同步*
