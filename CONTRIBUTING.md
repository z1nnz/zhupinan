# 共同開發

儲存庫：<https://github.com/z1nnz/zhupinan>
工作範圍與驗收依據：[`docs/竹聘安_決賽系統開發_130人時.md`](docs/竹聘安_決賽系統開發_130人時.md)（S00–S10）

## 誰負責什麼

| 組 | 誰 | 範圍 |
|---|---|---|
| 核心組 | z1nnz（擁有者，最後決定） | 整合、資料表與 API 契約、審查與合併 |
| A | Johnnnny0823 | 依分配的工作包（原始程式作者） |
| B | joshsu54 | 依分配的工作包 |
| C | RoyLin0905 | 依分配的工作包 |

每個人只做分配給自己的工作包（S00–S10 中的一項，或其中一段）。**同一張資料表與 API 契約只由一人定稿**，不要兩個人同時大改 `demo/server/app.py`。

## 規則

- `main` **只透過 Pull Request 更新**，不直接推送，也不強制推送（`--force`）。GitHub 上已設定分支保護，合併前必須：
  - 有 **1 人核准**（協作者的 PR 由 z1nnz 審查核准；自己不能核准自己的 PR）
  - 自動測試 **`tests` 通過**（每次開 PR 或推送都會自動執行）
  - PR 上的留言討論**全部處理完**
  - 不能強制推送，也不能刪除 `main`
- 一個 PR 只處理一個工作包，或其中可以獨立驗收的一段。
- 每個人用**自己的 GitHub 帳號**登入，**不分享 Token**。使用 Claude Code 或 Codex 時，也一律用你自己的帳號推送，PR 上才會留下真實作者。
- PR 合併後**不要再推到同一條分支**；需要修正時，另開新的 `fix/...` 分支。
- 只能使用**合成資料**（`DEMO_DATA=true`）。以下一律不可提交：真實公文、移工資料、訪談紀錄、`line.env`、`.env`、API Token、SQLite 資料庫，以及 `demo/server/` 底下的 `data`、`uploads`、`cache`、`tmp`。
- 如果發現祕密已經進了提交：**立刻停止推送**，通知核心組，先撤銷那把金鑰，再處理提交歷史。

## 第一次設定（Windows PowerShell）

```powershell
git clone https://github.com/z1nnz/zhupinan.git
cd zhupinan
git config pull.ff only
py -3.12 -m venv .venv
.venv\Scripts\python -m pip install -r demo\requirements.txt
.venv\Scripts\python -m unittest discover -s demo\tests
```

最後一行應該顯示 `OK`。啟動方式、登入碼與資料重置請看 [`demo/DEVELOPMENT.md`](demo/DEVELOPMENT.md)。

第一次推送時，Git 會跳出 GitHub 登入視窗，用你自己的帳號登入即可。如果使用 GitHub CLI，執行 `gh auth login`。

## 每項工作

### 1. 開分支

分支名稱格式：

```
<類型>/<誰>-<工作包>-<主題>
```

- 類型：`feature`（新功能）、`fix`（修正已合併的內容）、`docs`（文件）、`test`（測試）
- 誰：`a`、`b`、`c`、`core`（用 Claude Code 或 Codex 做的工作，也用你自己的代號）
- 例子：`feature/b-s04-today-list`、`fix/a-s02-roc-date`

```powershell
git switch main
git pull --ff-only origin main
git switch -c feature/b-s04-today-list
```

### 2. 提交

只加入這次修改的檔案，提交訊息以工作包編號開頭：

```powershell
git add demo/web/app.js demo/web/app.css
git diff --cached --stat
git commit -m "S04：今日待辦清單接上 /api/todos"
git push -u origin feature/b-s04-today-list
```

推送前用 `git status --short` 確認沒有夾帶 `.env`、`*.db`、`demo/server/data/`、`uploads/`。

### 3. 開 PR

- 標題：`【A／B／C／核心組】S0X：<一句話>`，例如 `【B】S04：今日待辦清單`。
- 內容照 PR 範本填寫，需包含：
  - 工作包
  - 使用者看得到的變化
  - 變更的檔案
  - API 或資料表的更動（同步更新 `demo/API.md`）
  - **實際跑過的測試命令與結果**
  - 截圖（手機截圖請註明寬度）
  - 已知限制
- 規劃、靜態畫面、概念稿都不算完成；`design/concept/` 只是草稿。

測試失敗時，在同一條分支修正，不刪測試、不關規則、不強制推送。`main` 有更新時，在 PR 頁按「Update branch」，或執行：

```powershell
git fetch origin
git merge origin/main
.venv\Scripts\python -m unittest discover -s demo\tests
git push
```

### 4. 合併以後

由核心組審查並合併；涉及權限、隱私、LINE 收件人的變更，需要第二個人看過。合併後，各自執行 `git switch main`、`git pull --ff-only`，再從新的 `main` 開下一條分支。

## 驗證

- 修改程式後，至少要跑 `.venv\Scripts\python -m unittest discover -s demo\tests`，並在 PR 附上結果。
- 動到期限規則、擷取、問答或處分書檢核時，另外跑 `demo/DEVELOPMENT.md` 第 5.2 節的離線基準，`git status` 不能出現差異。
- 只在本機看過、沒有實際操作過的功能，PR 要寫「未實測」。

## 給 AI 助手（Claude Code、Codex）

請 AI 開工前先讀：

- [`AGENTS.md`](AGENTS.md)：Codex 會自動讀取
- [`CLAUDE.md`](CLAUDE.md)：Claude Code 會自動讀取
- [`AI_WORKFLOW.md`](AI_WORKFLOW.md)：交給 AI 的工作手冊，附起始訊息範本
