# Git Workflow SOP — 日常與救火

> 最後更新：2026/05/11（v1）

setup-guide 教你建好；這份 SOP 講「建好之後」—— 日常同步、第二台機器設定、衝突發生時怎麼救。存在你 Vault 的 `sop/` 資料夾，忘記步驟時翻一下。

---

## 日常同步（自動）

裝了 Obsidian Git 外掛後，每 30 分鐘自動 commit + push（看你設多久）。你不用跑任何指令。

如果要立即同步：在 Obsidian 按 `Ctrl + P` → 輸入 `git` → 選 **Create backup**。

---

## 手動同步（備用）

外掛出問題時，在 Vault 資料夾打開終端機：

```bash
cd "[你的 Vault 路徑]"
git add .
git commit -m "manual sync"
git push
```

什麼時候需要：
- Obsidian Git 同步失敗、跳錯誤訊息
- 你在 Obsidian 以外的工具改檔（例如 code editor），想留乾淨的 commit message

---

## 換電腦：完整安裝流程

### 1. 安裝 Git

到 https://git-scm.com 下載安裝。安裝時「Adjusting your PATH environment」選 **Git from the command line and also from 3rd-party software**（預設值）。

裝完後**關掉所有終端機視窗**，開一個新的，確認：

```bash
git --version
```

設定身份（只要做一次）：

```bash
git config --global user.email "你的 GitHub 信箱"
git config --global user.name "你的 GitHub 帳號"
```

### 2. 安裝 Obsidian

到 https://obsidian.md 下載安裝。先裝好，還不用開。

### 3. Clone Vault

```bash
cd Documents
git clone https://github.com/[你的帳號]/[你的 repo].git
```

整個 Vault（所有檔案、資料夾）會下載到 `Documents/[你的 repo]`。

### 4. 用 Obsidian 打開

打開 Obsidian → 「開啟資料夾作為儲存庫」→ 指向 `Documents/[你的 repo]`。

### 5. 裝 Obsidian Git 外掛

設定 ⚙️ → 社群外掛 → 關掉安全模式 → 瀏覽 → 搜尋 **Obsidian Git** → 安裝 → 啟用 → 設定 Auto commit-and-sync interval 為 `30`。

完成。所有檔案會跟原本的電腦一模一樣，之後自動同步。

---

## 同步衝突發生時

最常見的情境：你在電腦 A 改了檔案 X，電腦 B 沒先同步又改了同個檔案 X，第二次 push 被拒絕。

### 救援步驟

1. **停止在衝突的機器上編輯。** 不要讓它更糟。
2. 在 Obsidian Git 外掛裡，看有沒有 "merge conflict" 通知。打開受影響的檔案 —— Git 會留下衝突標記：
   ```
   <<<<<<< HEAD
   你本機的版本
   =======
   遠端的版本
   >>>>>>> origin/main
   ```
3. **手動選擇要保留什麼**。把標記刪掉，留下整理好的內容。
4. 存檔 → 外掛裡 "Commit all changes" → "Push"。

### 修不了怎麼辦

如果衝突太多（幾十個檔案、binary 檔案、你不記得改了什麼），比較安全的做法：**先把本機 copy 備份到別的地方**，再 `git reset --hard origin/main` 把本機重置成跟遠端一樣，然後從備份手動再 apply 你的修改。**沒備份就不要 reset** —— 那是破壞性操作。

---

## 看修改歷史

最快：到 GitHub 看你的 repo → 點 "commits"。每次備份一筆，有 timestamp 跟 diff。

看單一檔案：在 GitHub 打開檔案 → 右上 "History" → 看每個版本。

本機在 Obsidian Git 看：`Ctrl + P` → `git: open history view`。

---

## `.gitignore` 該放什麼（不該放什麼）

setup-guide 已經給你一份起手 `.gitignore`。額外的：

- **加進 .gitignore**：你不想進 git 歷史的暫存檔、機器特定設定、不小心溜進去的敏感資料
- **不要加**：主要資料夾內的東西（`memory/`、`context/` 等）—— 那是 Vault 存在的目的

不小心 commit 了該被 ignore 的檔案，看下一節。

---

## 把敏感檔案從歷史移除

如果你 commit 了敏感資料（API key、密碼、內部文件）：

1. 先加到 `.gitignore`
2. 從目前 commit 移除：
   ```bash
   git rm --cached path/to/sensitive-file
   git commit -m "remove sensitive file from tracking"
   git push
   ```
3. **重要**：這只移除「未來」的 commit。**過去**的 commit 還有它。如果是真的敏感（例如 leak 出去的 API key），**請輪替憑證** —— 當作它已經公開了。
4. 完整從歷史清除（進階）：用 `git filter-repo` 或 BFG Repo-Cleaner。本 SOP 不展開。

---

## 看起來怪怪的卡住時

安全的診斷指令（不會破壞東西）：

```bash
git status
git log --oneline -10
```

告訴你：
- 現在哪些檔案被改了、staged 了、untracked
- 最近 10 個 commit

把輸出貼給 AI agent，光這兩個指令的輸出通常就能診斷出問題。

---

*建議讀的頻率：第二台機器設定時讀一次。出事時再讀。*
