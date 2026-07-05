# Vault Changelog 模板

> 存成 `sop/vault-changelog.md`。搭配更新 log 規則使用（見 optimization-guide 第 5 節）。

---

## 這是什麼

核心檔案（README.md、memory-summary.md）每次啟動都會被讀。如果底部的「最後更新：…」一條一條堆積，Agent 每個 session 都在為這些歷史付錢。更新 log 規則把這些檔案限制在兩條：「最後更新」+「前次更新」，更早的全部搬進這份檔案。

## 怎麼用

1. 建立這份檔案，存成 `sop/vault-changelog.md`
2. 每次 README（或 memory-summary）新增一條更新紀錄時，把被擠掉的那條搬進來，新的在上
3. 搬移跟 README 修改在**同一個 response** 內完成，不要「之後再搬」

## 收錄範圍

只收結構性事件，也就是「會影響 Agent 怎麼讀 vault」的事：

- 資料夾新增／刪除／改名
- 核心檔案新增、改名、或重大重構
- Agent 閱讀順序調整
- 強制規則新增或升級

一般 memory 紀錄、project 進度、內容修改**不進這份**，它們屬於 `memory/` 和各 project 資料夾。

---

## 格式（虛構範例）

*2026/07/01（**結構性變動**：新建 `projects/archive/`，3 個已收官專案搬入；全 vault 連結同步至新路徑）*

*2026/06/12（新增資料夾 `operations/tax/` 作為稅務 hub：`README.md` 索引 + 年度行事曆、`forms-guide.md`、`bookkeeping-stack.md`。起因：第一個報稅季）*

*2026/05/28（閱讀順序調整：`identity/decision-style.md` 加為第 4 步，讓 Agent 寫 draft 前先自查）*
