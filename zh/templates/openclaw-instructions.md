# OpenClaw Global Instructions 模板

> 最後更新：2026/05/10（v1：從 cowork-instructions.md 拆出，反映 OpenClaw 的不同寫入範圍）

## 這是什麼

這是 OpenClaw 的 Global Instructions 設定，跟 Cowork 分開。兩個工具的啟動行為與預設寫入範圍不同 —— 共用同一份指令會造成混亂（例如 OpenClaw 以為它該主動改 root README）。

## OpenClaw 跟 Cowork 的差異

|  | Cowork | OpenClaw |
|---|---|---|
| 寫入範圍 | 整個 Vault（root README、INDEX.md、所有資料夾）| **預設只寫 `openclaw/`**，其他區域要先確認 |
| 啟動載入 | 手動讀 README + agent-persona + memory-summary（依 Global Instructions 指示）| **自動載入 `openclaw/MEMORY.md`**，Global Instructions 不需要再指示 |
| 索引同步規則 | 新增檔案必須改 index | 不適用（預設不碰 root Vault）|

所以兩邊規則必須分開。否則 OpenClaw 會以為它有義務維護 root README —— 可能改掉你不想被改的東西。

## 怎麼用

1. 打開 OpenClaw → 設定 → Global Instructions（或對應的設定位置）
2. 把下面的內容複製貼上
3. 把 `[你的 Vault 名稱]` 換成你自己的 Vault 資料夾名稱
4. 確認 OpenClaw 能讀到你的 Vault 路徑
5. **每台電腦都要各自設定一次**（global instructions 不會跨機器同步）

## 設定內容

```
## 啟動載入
openclaw/MEMORY.md 已自動載入（長期核心記憶）。openclaw/memory/ 的近期日誌也會自動讀取。

需要補充了解使用者的深度脈絡時，主動讀：
1. [你的 Vault 名稱]/README.md
2. [你的 Vault 名稱]/agent-persona.md
3. [你的 Vault 名稱]/memory-summary.md

Skills 檔案優先讀取 [你的 Vault 名稱]/skills/；若 Vault 沒有對應的 skill，系統內建 skill 照常使用。

## 強制規則（不是建議，是完成任務的必要條件）
1. **寫入範圍限制**：預設只寫 openclaw/ 內容。任何 openclaw/ 以外的修改（root README、memory/、projects/、hr/、sop/、identity/ 等使用者區域），必須先跟使用者確認才能 commit/push。
2. **對外文稿規則**：協助撰寫任何會被使用者以外的人看到的文稿（LinkedIn、GitHub Release notes、媒體稿、Pitch、官網文案、公開 issue / PR 等）時，必須先讀取 [你的 Vault 名稱]/identity/voice-and-tone.md 並遵循當中規則。不確定是否對外時視為對外。

重要任務結束後，依照 sop/after-action.md 提醒使用者跑收官流程（範圍限於 openclaw/，或經使用者確認的修改）。
```

## 為什麼要限制寫入範圍

OpenClaw 是 coding agent —— 預設會自由編輯檔案。這在它自己的 `openclaw/` 工作資料夾內沒問題，但**在你的 Vault root 是危險的**，因為：

- Vault root README 是你手工策劃 + 互相 cross-reference 的，自動改寫會破壞索引
- `memory/` 檔案按慣例只 append（記錄決策的）。編輯它就像在改寫歷史
- `identity/` 與 `context/` 是個人化內容 —— 是跟你討論出來的，不是被 generate 的

這條強制規則把 OpenClaw 變成「在自己的範圍內自主，其他都要明確交接」。這是更安全的預設。

## Skills 資料夾行為

如果你有 `[你的 Vault 名稱]/skills/` 並放了東西，OpenClaw 會優先讀取你的 skills（蓋過內建的）。這讓你可以覆蓋或擴充特定 skill（例如你自己的 `linear-issue-creator` 優先）。Vault 沒有的 skill 會自動 fallback 到內建版。

## 備註

- Cowork 的對應 SOP 在 [cowork-instructions.md](cowork-instructions.md) —— **不要互貼**，規則不同
- 如果你的 OpenClaw 跑在 WSL2 / Linux，Vault 路徑可能跟 Obsidian 看到的不一樣（例如 `/home/user/Documents/Your-Vault/` 對 `D:\Your-Vault\`）。確認兩個工具指到同一個 Git working tree
- 規則演化時，記得**每台電腦的 global instructions 都要更新** —— 這是最容易漏的地方
- 同樣的多層防禦設計也適用（見 [cowork-instructions.md](cowork-instructions.md) 的「多層防禦設計」段）。第三道（[vault-audit.md](vault-audit.md)）跟 agent 無關，哪個工具觸發都跑一樣的流程
