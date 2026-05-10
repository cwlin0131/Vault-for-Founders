# Vault Audit — 索引完整性檢查

> 多層防禦架構的第三道（見 [cowork-instructions.md](cowork-instructions.md)）。定期檢查 vault 索引（root `README.md`，以及選用的各資料夾 `INDEX.md`）是否跟實際檔案結構一致，抓出漏掉沒 index 的檔案或過時條目。

---

## 什麼時候跑

- **使用者主動要求**：「跑一次 vault audit」「掃一下 vault 有沒有漏」「audit 一下」
- **Agent 主動建議**：最近做了大量檔案變動、一陣子沒跑、或收到使用者反饋指向索引不一致時
- **可選的自動週期**：有些人會排成每週日晚上自動跑，並把日期寫進 `memory-summary.md`

---

## 怎麼跑（Agent 行為）

### Step 1：列出實際 vault 檔案

列出 vault 內所有 `.md` 檔案，排除不需要 index 的目錄：

```bash
cd "[你的 Vault 路徑]"
find . -type f -name "*.md" \
  -not -path "./.obsidian/*" \
  -not -path "./.git/*" \
  | sort
```

（依照你的設定調整忽略路徑，見下面「忽略清單」）

### Step 2：抽出已 index 的檔案路徑

讀 **root `README.md`**，列出它提到的所有檔案與資料夾。

如果你有額外建立各資料夾的索引檔（例如 `memory/INDEX.md`、`projects/INDEX.md`），也讀進來合併。基本框架只要求 root README，各資料夾索引是當資料夾超過 ~10 個檔案後的選用優化。

### Step 3：比對差異

兩類問題：

- **🔴 有檔案但沒 index**：vault 內實際存在但 README 沒提到 → 需要補
- **🟡 有 index 但檔案不存在**：README 提到但實際沒有 → 過時，需要移除或修正

### Step 4：產出報告

固定格式：

```
📋 Vault Audit 結果（YYYY-MM-DD）

── 摘要 ──
實際檔案數：N
已 index：M
差異：X 個

── 🔴 未 index 的檔案（N 個）──
- path/to/file1.md — [Agent 建議的簡短描述]
- path/to/file2.md — [Agent 建議的簡短描述]

── 🟡 README 過時條目（N 個）──
- path/to/old.md — 實際檔案已不存在

── 💡 建議 ──
- 哪些應該補進 index
- 哪些可以忽略（暫存 / deliverable 類）
- 哪些應該搬到其他資料夾或刪除
```

### Step 5：使用者決定

- **要 index** → Agent 立即更新 README
- **不要 index**（暫存、deliverable 類）→ Agent 記下，但下次 audit 仍會再提一次（除非明示「永久忽略」）
- **要刪除檔案** → 使用者親自處理，或明確指示 Agent

---

## 關鍵原則

1. **不自動修改 README**，只回報差異。修改動作需使用者同意。
2. **暫存檔可以不 index，但必須在報告裡列出** —— 避免「漏看」跟「故意不 index」分不清
3. **每次跑完**，在 `memory-summary.md` 留下「最後 audit 日期」，方便判斷何時該下一次
4. **不要把 audit 結果寫進 `memory/`** —— audit 是操作行為，不是決策或教訓，沒有長期保留價值
5. **如果有用各資料夾索引**：個別檔案漏 index → 更新對應 `INDEX.md`；資料夾漏 index → 更新 root README

---

## 忽略清單（不需 index 的檔案類型）

以下檔案即使不在 README 索引內也算正常：

- vault root 的**暫存協調檔**（例如 `recovery-note.md`，完成後會刪除）
- vault root 的**一次性 deliverable**（例如 `github-profile-README.md`，產出後即用即丟）
- `skills/` 資料夾內主 skill 檔以外的**內部支援檔**（scripts、references、agents 等）
- Obsidian 本機設定類檔案（`.obsidian/`）
- 各種隱藏 / 系統目錄（`.git/`、`.DS_Store` 等）

其他檔案**原則上都應該 index**。

---

## 為什麼需要這層

如果不定期做 audit：

- README 索引會慢慢過時
- Agent 因為檔案不在它的地圖上而錯過
- 你不會發現，直到 Agent 信心滿滿地漏掉某個重要檔案

Audit 不是在抓 Agent，是在抓你自己。幾個月後你會有上百個檔案。Global Instructions 的強制規則（第一道）跟收官 SOP（第二道）能在「寫入時」抓到大部分情況，但它們抓不到你在 Finder 手動搬檔、在 Obsidian 改檔名沒同步更新 README 的狀況。這就是第三層的意義。

---

*建議頻率：每月一次，或任何時候發現 Agent 找的檔案你不認得時。*
