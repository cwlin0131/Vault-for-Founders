# Vault 優化指南 — 讓 Agent 更快找到正確資訊

> 建完 Vault 之後，隨著檔案越來越多，Agent 找資料的效率會開始下降。這份指南是持續優化 Vault 可讀性的實戰經驗。

---

## 核心觀念

Vault 的主要讀者是 AI Agent，不是人。所有優化的目標都是同一個：**降低 Agent 找到正確檔案的成本**。

Agent 每次啟動時的行為是：讀 README → 判斷需要哪些檔案 → 開檔讀取。你能在第一步就讓它精準判斷，後面就不會浪費時間開錯檔案。

---

## 1. README 是 Agent 的地圖

README 是 Agent 每次啟動必讀的第一份文件，它靠這份索引決定要不要開某個檔案。

**做法（小 Vault，30 個檔案以內）：**
- 不只寫資料夾說明，要到「每個檔案」的粒度，每個檔案寫一句話摘要
- 摘要寫的是檔案的內容，不是檔名的重複
- 新增、刪除、搬移檔案時，同步更新 README

**做法（Vault 長大之後）— 索引分層（v2）：**

資料夾開始堆疊之後，「README 詳列到每個檔案」會撐不住。把資料夾分成兩種：

- **變動慢的資料夾**（identity/、context/、operations/）：繼續在 README 逐檔詳列
- **會堆疊的資料夾**（memory/、projects/、hr/）：各自建一份 `INDEX.md` 放完整清單和狀態，README 只留一行入口提示

Trigger：資料夾超過大約 10 個檔案、而且會繼續長 → 加 `INDEX.md`。同步規則也跟著分層：新增／刪除檔案 → 改該資料夾的 `INDEX.md`；新增／刪除資料夾（結構性變動）→ 改 Root README。完整設計理由見 [architecture.md](architecture.md)。

**反例：**
```
├── /operations
│   └── entity.md          ← 公司資料
```
Agent 看到「公司資料」不知道裡面是公司設立文件、銀行帳戶、還是稅務申報。

**正例：**
```
├── /operations
│   └── us-corp-setup.md   ← 美國 Delaware C Corp 設立資料、EIN 進度、銀行開戶
```
Agent 一看就知道這份是公司設立相關，不需要開檔確認。

---

## 2. 檔名本身就是索引

好的檔名讓 Agent 光看檔案列表就能判斷要不要讀，不需要依賴 README 或開檔。

**命名規則：**
- 避免泛稱，用描述性檔名（`corp-setup.md` 而非 `entity.md`）
- 同分類用 `主題-子項` 格式，主題在前（`product-roadmap.md`、`product-pricing.md`）
- memory 檔案用 `YYYY-MM-DD_主題摘要.md`（帶日期方便排序）
- projects 資料夾用 `YYYY-MM-主題/`

**把命名規則寫進 README**，讓未來的自己和 Agent 都遵守同一套標準。

---

## 3. YAML Frontmatter 標籤化

在每個 `.md` 檔開頭加入 YAML frontmatter，讓 Agent 不用讀完整篇就能判斷檔案的性質和新舊。

**只需要三個欄位：**

```yaml
---
updated: 2026-04-15
tags: [context, product, strategy]
summary: 產品 90 天擴張策略：定位、通路、定價、里程碑
---
```

- `updated` — 最後更新日期，Agent 判斷資訊新鮮度
- `tags` — 分類標籤，方便搜尋和交叉引用
- `summary` — 一句話摘要，跟 README 索引裡的描述保持一致

**不要搞太多 metadata 欄位。** 欄位越多，維護成本越高，你會開始懶得更新，最後 metadata 比內容還不準確。三個就夠了。

---

## 4. Memory Summary 定期清洗

`memory-summary.md` 是 Agent 每次啟動都會讀的長期記憶摘要。如果不定期清理，過期資訊會誤導 Agent 的判斷。

**清洗規則：**
- 任務完成後，回去更新結果欄或移除已不相關的項目
- 定期檢查有沒有重複、過期、或狀態已變的內容
- 清洗前跟自己確認一遍，避免誤刪仍然重要但看起來舊的項目

**建議頻率：** 每週一次，或每次重大決策後。不需要太頻繁，但不能完全不做。

**Sticky reminders 不是 log（v2）。** 如果你的 memory-summary 有 sticky／優先事項區，遵守兩條：項目解決了就在同一個 response 內直接刪掉（不要在檔案裡留一堆「已完成」）；狀態更新不要當歷史堆積。歷史屬於 `memory/` 的紀錄檔。

---

## 5. 更新 log 規則 — 阻止慢性肥大（v2）

核心檔案（README.md、memory-summary.md）底部很容易長出一條一條的「最後更新：…」。每一條看起來都無害，兩個月後 Agent 每次啟動都在為五十條歷史付錢。

**規則：** 只留兩條 —「最後更新」和「前次更新」。新增一條時，把被擠掉的那條搬進 `sop/vault-changelog.md`（模板：[templates/vault-changelog.md](templates/vault-changelog.md)）。

changelog 只收結構性事件：資料夾增刪、核心檔案重大變動、閱讀順序調整、規則升級。一般 memory 紀錄不進這份。

---

## 6. 每次必讀的三檔要持續瘦身（v2）

README.md、agent-persona.md、memory-summary.md 每次啟動都會被讀，裡面每一行都是重複收費。每次對三檔新增內容時 self-check 一次：

> 這條是 Agent **每個** session 都必須 fresh in mind 的嗎？

如果某段超過 5 行、又不是每個 session 都會用到，就搬走：行為機制 → `identity/`、操作流程 → `sop/`、變更紀錄 → 對應 `INDEX.md` 或 `sop/vault-changelog.md`，原地留一行 pointer。

---

## 執行順序很重要

如果你要一次做多項優化，建議這個順序：

1. **先改檔名** — 把模糊的改成描述性的
2. **再寫 README 索引和 frontmatter** — 基於最終檔名寫，不用改兩次
3. **最後跑一次驗證** — 確認所有內部連結、引用都指向新檔名

先改檔名再改索引，避免重工。

---

## 什麼時候該做這些

- **Vault 剛建好時** — 不用急，先累積一些檔案再說
- **檔案超過 10 個時** — 開始值得做 README 索引強化和 frontmatter
- **某個資料夾超過 10 個檔案而且還在長時** — 給它一份 `INDEX.md`，README 的對應段落縮成一行
- **你發現 Agent 開始讀錯檔案時** — 代表索引不夠清楚，該優化了
- **README 底部越長越長時** — 套用更新 log 規則（第 5 節）
- **每次加新檔案時** — 順手更新索引和加 frontmatter，維持習慣比一次大整理有效
