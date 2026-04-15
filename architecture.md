# Vault for Founders：架構設計

> 這份文件說明知識庫的設計取捨，以及跟 Muse 晶種的差異。

---

## 跟 Muse 晶種的比較

[Muse Crystal Seed](https://github.com/frank890417/muse-crystal-seed) by Che-Yu Wu 吳哲宇，是一套完整的 Agent 知識庫架構，針對超級個體設計。Vault for Founders 在它的基礎上，針對創業者場景做了調整。

### 檔案對照

| Muse 架構 | Vault for Founders | 差異說明 |
|-----------|-------------------|----------|
| SOUL.md | agent-persona.md | 合併 SOUL + IDENTITY + AGENTS 成一份 |
| USER.md | identity/identity.md | 放進資料夾，方便擴充 |
| MEMORY.md | memory-summary.md | 搭配 memory/ 資料夾的逐筆紀錄 |
| AGENTS.md | 合併進 agent-persona.md | 協作規則跟人格設定放一起更直覺 |
| HEARTBEAT.md | 未建立 | 24/7 常駐用，Cowork 場景暫不需要 |
| TOOLS.md | 未建立 | 工具鏈簡單，暫不需要額外文件 |
| BOOTSTRAP.md | 工具的自訂指令 | 用 Cowork / OpenClaw 的 Instructions 取代 |
| skills/ | sop/ + skills/ | SOP 和 Skills 分開管理 |

### 創業者場景多了什麼

Muse 偏向個人生活管理與知識累積。創業者需要額外管理：

| 資料夾 | 用途 |
|--------|------|
| context/ | 公司背景、產品策略、競爭環境、對外溝通素材 |
| operations/ | 公司營運資料（法人設立、帳號管理） |
| hr/ | 招募與人事，按檔期分資料夾 |
| projects/ | 進行中的項目（出差、活動、市場計畫） |
| people/ | 人脈管理 |

---

## Agent 啟動流程

不管用什麼工具（Cowork、OpenClaw、Claude Code），Agent 每次啟動的閱讀順序都一樣：

1. **README.md** 了解 Vault 的結構和地圖
2. **agent-persona.md** 了解自己的角色和協作方式
3. **memory-summary.md** 快速掌握近期重要事項
4. 依任務需要，讀對應資料夾的內容

這個順序寫進工具的自訂指令裡，Agent 會自動執行。

---

*最後更新：2026/04/14*
