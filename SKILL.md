---
name: seo-optimize
description: 分析專案並實際套用 SEO / AEO / GEO 優化。當使用者要求優化搜尋排名、提升 AI 引擎（AI Overviews / ChatGPT / Perplexity / Claude）引用機率、檢查 meta 標籤與結構化資料、處理 robots.txt、sitemap、hreflang 與 Bing / IndexNow 索引提交、或針對特定關鍵字與地區推廣專案時使用。每次執行都會重新查找近半年的 SEO / AEO 現況。
---

# SEO / AEO Optimizer

以**當次查得的近半年研究**為依據，分析專案實際暴露的搜尋面向，規劃並套用 SEO / AEO 優化。

## Command Syntax

```
/seo-optimize [PROJECT_PATH] [--plan] [--reset]
```

### Parameters (All Optional)

| Parameter | Default | Description |
|-----------|---------|-------------|
| `PROJECT_PATH` | 當前目錄 | 專案根目錄 |
| `--plan` | — | 只產出優化規劃，不修改任何檔案 |
| `--reset` | — | 清除 `.doc/seo-optimize/config.json`，重新詢問關鍵字 |

```bash
/seo-optimize                      # 完整流程：研究 → 分析 → 詢問 → 規劃 → 確認 → 套用
/seo-optimize --plan               # 只出規劃
/seo-optimize ./my-site --reset    # 指定專案並重設目標設定
```

---

## Workflow

```
1. 研究   →  強制重跑 research_protocol.md Phase A，產出 research digest
2. 分析   →  analyze_seo.py 盤點 surface + 實讀原始碼理解專案在做什麼
3. 詢問   →  AskUserQuestion 取得關鍵字 / 在地性；地區與引擎用固定預設；寫入 config.json
3.5 補研究 →  research_protocol.md Phase B，針對關鍵字補查
4. 規劃   →  產出 {ts}-plan.md，向使用者呈現並等待確認
5. 套用   →  確認後依 optimization_rules.md 修改檔案
6. 驗證   →  產出 {ts}-applied.md，列出已套用 / 未套用 / 需人工後續
```

`--plan` 時流程止於 Step 4。

---

## Step 1：研究（強制 gate，不可跳過）

**每次執行都必須完整跑一次 [`scripts/research_protocol.md`](scripts/research_protocol.md) 的 Phase A。**

| 條件 | 行為 |
|---|---|
| 開始執行 | 先 `date +%Y-%m-%d` 取得今日，推算近半年時間窗 |
| 執行查詢 | Phase A-1 十一組查詢並行送出；Phase A-2 五個一手來源以 WebFetch 實抓 |
| 查得結果與 [`scripts/knowledge_anchors.md`](scripts/knowledge_anchors.md) 不符 | 以本次實抓的 Tier 1 來源為準，**就地更新 knowledge_anchors.md** 並改寫其「上次驗證日期」 |
| 網路不可用 | 明確告知使用者「本次未取得最新研究，以下依據為 {anchors 驗證日期} 的快照」，**不得靜默沿用** |

**為何不快取：** SEO / AEO 有效做法半年內會反轉。2025 年主流指南建議加 llms.txt 提升 AI 可見度，2026-05 Google 官方文件表明其被忽略；同份文件亦推翻「特定 schema 觸發 AI 引用」。憑記憶作答會產出反效果的動作。

產出：`.doc/seo-optimize/research-{yyyy-MM-dd}.md`（格式見 research_protocol.md）。

---

## Step 2：分析專案

```bash
python3 ~/.claude/skills/seo-optimize/scripts/analyze_seo.py {PROJECT_PATH}
```

輸出 JSON：`code_type`、`web_surfaces`、`web_framework`、`package_metadata`、`crawler_directives`、`metadata_apis`、`pages`、`contents`。

**腳本只做機械盤點，不理解專案在做什麼。**接著必須**實讀原始碼**（同 `readme-generate` 的做法）：入口檔、主要型別與匯出函式、README、既有文件，據此判斷：

| 判斷項 | 用途 |
|---|---|
| 這個專案解決什麼問題 | Step 3 的關鍵字候選來源 |
| 目標使用者是誰（開發者／終端使用者／企業） | 決定 R6 的 schema 類型與 R7.3 的 llms.txt 判準 |
| 有無實際部署網域 | 決定 canonical / sitemap 能否產生 |
| README 是否由 `/readme-generate` 管理 | 決定 R9 是直接改寫還是只回報建議 |

**禁止**在 `web_surfaces` 為空時虛構頁面優化——該類專案的 surface 是套件登錄頁與 GitHub（規則 R8–R10）。

---

## Step 3：詢問推廣目標

先檢查 `{PROJECT_PATH}/.doc/seo-optimize/config.json`：

| 狀態 | 行為 |
|---|---|
| 存在且欄位完整，未帶 `--reset` | 載入並**向使用者複述一行**目前設定，繼續 Step 3.5 |
| 缺失、欄位不完整，或帶 `--reset` | 以 `AskUserQuestion` 詢問 |

### 已固定的預設（**不再詢問**）

下列兩項由使用者於 2026-08-16 定案，適用所有專案。直接寫入 config.json，**禁止再以 AskUserQuestion 詢問**。

| 項目 | 固定值 | 意涵 |
|---|---|---|
| 地區 / 語言 | **每個語言版本各自完整優化，語言間等權** | `zh` 頁面全繁中、`en` 頁面全英文，日後新增任何語言同理。無「主語言／次語言」之分，`x-default` 不偏向任一語言。每個語言版本的 title / description / JSON-LD / og:locale 都要用該語言自身撰寫，禁止跨語言共用同一句 |
| 目標引擎 | **全部**：Google 一般搜尋、Google AI Overviews·AI Mode、ChatGPT、Perplexity、Claude | 因涵蓋 ChatGPT（OAI-SearchBot 為 HTML-only parser，見 A6），**「主要內容必須存在於初始 HTML」永遠是最高優先項**；CSR-only 站台一律列 Critical |

### 詢問內容（一次問完兩題）

| # | Header | 問題 | 選項來源 |
|---|---|---|---|
| 1 | 關鍵字 | 想推廣的主要關鍵字（可複選 / 自填） | **由 Step 2 的原始碼理解產出 3–4 個候選**，使用者可改用 Other 自填 |
| 2 | 在地性 | 有無實體營業地點 | 有（觸發 LocalBusiness schema 與 GBP 建議）／純線上 |

**關鍵字候選必須來自實際讀過的程式碼**，不得從專案名硬湊。候選要是使用者會輸入搜尋框的詞，不是內部術語。

### config.json

```json
{
  "primary_keywords": ["go scheduler", "golang cron library"],
  "secondary_keywords": ["task scheduling", "fsnotify hot reload"],
  "locales": ["en", "zh-Hant"],
  "locale_policy": "per-language-full",
  "engines": ["google", "google-ai", "chatgpt", "perplexity", "claude"],
  "has_physical_location": false,
  "domain": "https://pardn.io/go-scheduler"
}
```

`locales` 依專案實際存在的語言版本填寫；`locale_policy` 與 `engines` 為固定值，不因專案而異。

`domain` 無法從 git remote、`robots.txt` 的 `Sitemap:`、既有 canonical 或部署設定推得時，**問使用者**，不得填入佔位網域。

---

## Step 3.5：補研究

依 Step 3 的答案跑 [`scripts/research_protocol.md`](scripts/research_protocol.md) 的 Phase B，結果併入同一份 research digest。

---

## Step 4：規劃

依 [`scripts/optimization_rules.md`](scripts/optimization_rules.md) 逐條比對分析結果，產出 `.doc/seo-optimize/{yyyy-MM-dd_HH-mm}-plan.md`（格式見 [`scripts/output_format.md`](scripts/output_format.md)）。

**產出後必須向使用者呈現摘要並取得明確確認才進入 Step 5。**規劃會修改專案檔案，屬不易還原的動作。

`--plan` 模式在此停止。

---

## Step 5：套用

僅套用 Step 4 規劃中**已列出且使用者未否決**的項目。

| 界線 | 規則 |
|---|---|
| 檔案修改 | 逐項套用，每項對應規劃中的編號 |
| `robots.txt` 封鎖規則、`noindex` 移除 | 規劃中已標為「需使用者決策」者，未獲答覆前不動 |
| 遠端 repo metadata（`gh repo edit`） | **只列出指令，不執行**，交由使用者跑 |
| README | 由 `/readme-generate` 管理時只回報建議，不直接改寫結構 |
| git | **不執行 `git commit` / `git push` / `git tag`** |

規劃外的「順手改一下」一律不做。

---

## Step 6：驗證

產出 `.doc/seo-optimize/{yyyy-MM-dd_HH-mm}-applied.md`，並執行下列自我檢查：

- [ ] Step 1 研究本次實際執行，digest 已落檔且含實抓的 Tier 1 來源
- [ ] knowledge_anchors.md 與本次研究一致（不一致者已更新）
- [ ] 每項套用的變更都能指到 `optimization_rules.md` 的規則編號
- [ ] 每項變更都有實際檔案錨點，無虛構路徑
- [ ] 無「禁止動作」表中的任何一項（關鍵字堆砌、假 schema、無效 llms.txt 等）
- [ ] `web_surfaces` 為空時未產生任何頁面層變更
- [ ] canonical / sitemap 中的網域為實際確認過的網域
- [ ] `csr_shell == true` 的頁面已列為 R11 Critical，未被其他內容層項目蓋過
- [ ] 目標引擎含 ChatGPT 時，R12 的 Bing Webmaster Tools 驗證狀態已檢查
- [ ] 驗證碼、IndexNow key 未被填造
- [ ] 未執行 git 寫入指令、未執行 `gh repo edit`
- [ ] 已提醒使用者 `.doc/` 是否需加入 `.gitignore`

---

## Reference Documents

| 階段 | 參考檔 | 用途 |
|---|---|---|
| Step 1 / 3.5 | [`scripts/research_protocol.md`](scripts/research_protocol.md) | 強制研究協定：查詢集、來源分級、衝突裁決、digest 格式 |
| Step 1 / 4 | [`scripts/knowledge_anchors.md`](scripts/knowledge_anchors.md) | 已驗證的一手立場快照，用於偵測變動與識破業界迷思；每次執行後更新 |
| Step 4 / 5 | [`scripts/optimization_rules.md`](scripts/optimization_rules.md) | 規則 R1–R12、禁止動作、嚴重度定義 |
| Step 4 / 6 | [`scripts/output_format.md`](scripts/output_format.md) | 規劃與執行結果的報告範本 |
