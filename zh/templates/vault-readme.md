# [你的名字] Vault

這是 [你的名字] 的個人知識庫，供 AI Agent（[Agent 名字]）讀取使用。

---

## 資料夾結構

```
/
  README.md              ← 你正在讀的這份
  agent-persona.md       ← Agent 的人格設定與協作方式
  memory-summary.md      ← 長期記憶摘要

  /identity              ← 你是誰、價值觀、決策風格
  /context               ← 工作背景、產品現況、策略方向
  /memory                ← 重要決策紀錄、會議結論 → INDEX.md
  /sop                   ← 操作流程
  /projects              ← 各項目的狀態 → INDEX.md（收官的搬 archive/）
  /people                ← 重要聯絡人背景
  /skills                ← Agent 的技能檔案
```

會堆疊的資料夾（memory/、projects/、之後的 hr/）各自維護一份 `INDEX.md` 放完整清單；這份 README 只留上面的一行入口。進資料夾前先讀它的 `INDEX.md`。

## Agent 閱讀順序

1. 讀這份 README（了解整體結構）
2. 讀 `agent-persona.md`（了解自己的角色與協作方式）
3. 讀 `memory-summary.md`（掌握近期重要事項）
4. 依任務需要，讀對應資料夾的內容

## 命名規則

- 檔名即索引：用描述性檔名，避免泛稱（`corp-setup.md`，不要 `entity.md`）
- 同分類用 `主題-子項` 格式，主題在前（`product-roadmap.md`、`product-pricing.md`）
- memory/ 檔名：`YYYY-MM-DD_主題.md` · projects/ 資料夾：`YYYY-MM-主題/`

## 🚨 強制規則（不是建議）

### #1 — 索引同步
- **新增 / 刪除檔案** → 當次 response 內改對應資料夾的 `INDEX.md`（有 INDEX 的資料夾）
- **新增 / 刪除「資料夾」或其他結構性變動** → 改這份 README（資料夾描述 + 底部更新 log）

### #2 — 對外文稿規則
只要這段文字**會被 [你的名字] 以外的人看到**（LinkedIn、GitHub README、Release notes、媒體稿、pitch、公開 issue / PR 等），撰寫前必須讀 `identity/voice-and-tone.md`。不確定時視為對外。

兩條都是完成任務的必要條件。第二道安全網：收官檢查（after-action）時再確認一次。

## 維護原則

- 一個事實只寫在一個地方（Single Source of Truth）
- 建新檔案前先搜尋有沒有現有的
- 每份文件開頭標注最後更新日期
- 敏感資訊（API key、密碼等）不進這個 repo

---

*最後更新：YYYY/MM/DD（改了什麼）*
*前次更新：YYYY/MM/DD（改了什麼）*

*📏 更新 log 規則：只留這兩條。新增一條時，把被擠掉的那條搬進 `sop/vault-changelog.md`。*
