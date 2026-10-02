# Knowledge Anchors

**上次驗證日期：2026-10-02**

本檔是「已驗證的一手立場」快照，**不是免跑研究的理由**。用途只有兩個：

1. 讓 Phase A 研究能**偵測變動**——新查到的說法與此處不同，代表立場有變（或此處已過期），必須以本次實抓的 Tier 1 來源為準
2. 讓模型**識破業界迷思**——多數 SEO 部落格的主張與下表衝突

**每次執行完 Phase A 後，若本檔任一條與實抓結果不符，就地更新本檔並改寫「上次驗證日期」。**

---

## A1 — AEO / GEO 不是獨立於 SEO 的學科

**立場：** Google 2026-05-15 發布的 *Optimizing your website for generative AI features on Google Search* 明確表示，讓內容出現在 AI Overviews / AI Mode 所需的，就是既有的 SEO 基本功：可被索引、可被抓取、具備 snippet 資格、內容有獨到價值。AI 功能與一般搜尋共用同一份索引與同一套 E-E-A-T 判準。

**來源：** `developers.google.com/search/docs/fundamentals/ai-optimization-guide`（2026-05-15 發布；2026-08-25 實抓時頁面標示 Last Updated 2026-07-10，立場未變）

**推翻了：** 「AEO 需要另一套與 SEO 平行的技術棧」。

**AEO / GEO 用語（2026-09-24 補記）：** 同頁明文「optimizing for generative AI search is optimizing for the search experience, and thus still SEO」，並列出不需要的動作：不需為 AI 改寫文風、不需擔心 long-tail 關鍵字不足、不需追求假的站外提及。2026-06-05 Google 另發布 *Google Search's guidance on third-party SEO tools, services, and advice*：第三方工具沒有 Google 內部排序資料，其分數不等於 Google 的分數，一手數據以 Search Console 為準。**因此的判斷規則：** 規劃中的量測項一律指向 Search Console / Bing Webmaster Tools，不引用第三方工具的「AI 可見度分數」作為依據。

**垃圾政策涵蓋 AI 回答（2026-09-24 補記）：** 2026-05-15 Google 釐清 spam policies 同樣適用於生成式 AI 回答——以 scaled content、cloaking、site reputation abuse 等手法操弄 AI Overviews / AI Mode 屬違規。2026-04-13 新增「back button hijacking」政策，2026-06-15 起執行。Search Status Dashboard（2026-10-01 實抓）：2026 年 spam update 已有 03-24、06-24、08-18、09-24（進行中）四次。**2026-10-01**（search/updates 實抓）：`using-gen-ai-content` 指南依 Search Quality Raters guidelines 更新，對象為以生成式 AI 產出內容的站台。

---

## A2 — 沒有能觸發 AI 引用的特殊 schema

**立場：** 結構化資料**不是** generative AI 功能的必要條件，也不存在專供 AI 引用的 schema.org 類型。structured data 的價值仍在於 rich results 資格與幫助機器理解實體，不是引用觸發器。

**來源：** 同 A1。

**推翻了：** 「加上 FAQPage / Speakable 就能被 AI 引用」。

**仍然該做的：** 對**確實符合**該類型語意的頁面標註對應 schema（軟體專案 → `SoftwareApplication` / `SoftwareSourceCode`；教學頁 → `TechArticle` / `HowTo`；有實體店面 → `LocalBusiness`）。理由是實體理解與 rich results，不是 AI 引用。

**Discussion Forum / QA Page（2026-09-07 補記）：** Google 於 2026-03-24 為這兩種標記新增支援屬性，兩者仍在維護中。

**FAQ rich result 已停用（2026-08-16 補記）：** Google 於 2026-05-07 停止顯示 FAQ rich result，並於 2026-06-15 移除該功能文件（以 developers.google.com/search/updates 的變更清單為準）；Search Console API 支援於 2026-08 終止。**`FAQPage` 型別本身仍為合法 schema.org 型別**，既有標記留著不會受罰。因此「移除既有 FAQPage 標記」不是必要動作，但「為了 rich result 而新加 FAQPage」已無收益。

---

## A3 — llms.txt：Google 忽略，例外是 agent-facing 開發文件

**立場：**

| 主體 | 現況 |
|---|---|
| Google | 明文表示不需要、不讀取 llms.txt，既不加分也不扣分。**2026-06-15 已寫入官方文件變更日誌**（不再僅是 Gary Illyes / John Mueller 的公開發言），措辭為 neither harm nor help |
| OpenAI / Anthropic / Perplexity 的**搜尋與答覆管線** | 無任何一家公開承諾將其作為排序或引用訊號 |
| 採用率 vs 實際取用 | Ahrefs 2026-05 實測 137,000 網域：**97% 的 llms.txt 該月收到零次請求**；AI 檢索 bot 僅佔對這些檔案請求的 1.1%（GPTBot 4.51%、ClaudeBot 0.80%）。採用率高、取用率趨近於零 |
| **有效例外** | Anthropic 的 *Writing for Agents* 指南建議 agent 取用的開發文件提供 llms.txt；OpenAI 於 Agents SDK 與 Agentic Commerce Protocol 文件站自用 |

**來源：** 同 A1（Google 立場，2026-06-15 已入官方變更日誌）；Ahrefs 2026-05 取用率實測；Anthropic *Writing for Agents* 與 OpenAI Agents SDK 文件站實例。

**雙檔模式（2026-09-07 補記）：** 落在例外內的專案，2026 主流做法是 `llms.txt`（索引，供定位）＋ `llms-full.txt`（全文，供深度 ingestion），Anthropic、Vercel、LangGraph 皆採此模式。

**規格 v2（2026-10-01 補記）：** llmstxt.org 於 2026-08-10 發布 v2：以 `rel="alternate" type="text/markdown"` 與 `rel="describedby"`（`<link>` 或 HTTP header）供 agent 發現 markdown 版與 llms.txt；允許 `page.md` 取代 `page.html.md`；子路徑可各自放 llms.txt（最具體者適用）；移除 `llms_txt2ctx`。規格**未定義** `llms-full.txt`——雙檔模式是實務慣例而非規格要求。預期用法是 agent 讀 llms.txt 後追連結，故連結應指向 LLM-friendly（markdown）內容。

**因此的判斷規則：** 專案是**供 AI agent 取用的開發者文件站**（SDK / CLI / library docs）→ 產生 llms.txt 有實際用途。行銷官網 / 一般內容站 → **不產生**；已存在者列為可移除項。

---

## A4 — 不需要為了 AI 而把內容切碎

**立場：** Google 表示其系統能理解涵蓋多主題的完整頁面並自行抽取相關段落，**不需要**站方預先「chunking」或拆成大量單一問題的頁面。為查詢變體大量產生近似頁面屬於 scaled content abuse。

**來源：** 同 A1。

**推翻了：** 「每個 section 都要放 40–60 字直答塊、把長文拆成 N 個問答頁」這類機械式指令。

**仍然該做的：** 清楚的段落／標題結構、語意化 HTML、主要內容與導覽可區分——這是可讀性與可抓取性要求，不是「為 AI 切塊」。

**成效追蹤（2026-08-25 補記）：** Search Console 已於 2026-06 提供 Generative AI performance report，AI 功能的曝光／點擊可直接在 GSC 觀測，不需第三方 AEO 追蹤工具。

**Preferred Sources（2026-09-07 補記）：** Google 已將 Preferred Sources 帶入 AI Overviews 與 AI Mode（2026-04-30 擴及所有語言，2026-05-27 進入 AI 功能），使用者可自選偏好站台；2026-08-20 官方文件新增自訂互動按鈕的實作說明。這是「讓既有讀者把你設為偏好來源」的通道，不是排序技巧。

---

## A5 — 訓練型與檢索型 crawler 必須分開處理

**立場：** AI crawler 分兩類，封鎖後果完全不同。

| 類別 | User-agent | 封鎖後果 |
|---|---|---|
| 訓練型 | `GPTBot`、`ClaudeBot`、`Google-Extended`、`Applebot-Extended`、`Meta-ExternalAgent`、`Bytespider`、`CCBot`、`Amazonbot` | 不影響被引用；僅退出模型訓練 |
| 檢索型 | `OAI-SearchBot`、`ChatGPT-User`、`Claude-SearchBot`、`Claude-User`、`PerplexityBot`、`Perplexity-User`、`Googlebot`、`Bingbot`、`Applebot` | **封鎖等於放棄該引擎的引用資格** |

**因此的判斷規則：** `robots.txt` 中 `User-agent: *` + `Disallow: /` 會同時斷掉檢索型 crawler，屬 Critical。想退出訓練但保留引用，須逐一列出訓練型 bot 而非用萬用字元。

**注意：** `Bytespider` 與 Perplexity 的部分抓取行為有被記錄為不遵守 robots.txt。robots.txt 是宣告而非強制手段。

**使用者觸發型 fetcher（2026-09-24 補記）：** 第三類 bot 代表使用者即時抓取，多數**不遵守** robots.txt，封鎖它們在 robots.txt 中無效。

| User-agent | robots.txt | 來源 |
|---|---|---|
| `Google-Agent`（2026-03-20 加入官方清單，代使用者瀏覽與操作） | 忽略；使用 `user-triggered-agents.json` IP 段，Google **實驗中**以 Web Bot Auth（`https://agent.bot.goog`）簽章（2026-08-19 版文件措辭） | Google user-triggered fetchers 文件 |
| `ChatGPT-User` | 「may not apply」 | OpenAI bots 文件 |
| `Perplexity-User` | 一般忽略 | Perplexity crawler 文件 |
| `Claude-User` | **遵守**（Anthropic 表明三個 bot 皆遵守，含 `Crawl-delay`） | Anthropic 支援文章 8896518 |

**Google-Extended 不控制 AI Overviews：** AI Overviews / AI Mode 走 Googlebot 的一般索引，`Google-Extended` 只管 Gemini 訓練與其他 Google 系統的 grounding。想讓頁面不進 AI Overviews，唯一手段是 `nosnippet` / `data-nosnippet` / `max-snippet` / `noindex`——**這些同時會移除一般搜尋的摘要**，不存在「只退出 AI、保留傳統摘要」的選項。

**OAI-AdsBot（2026-10-01 補記）：** OpenAI bots 文件新增 `OAI-AdsBot`：只造訪送審為 ChatGPT 廣告的 landing page 以驗證政策合規與投放相關性，資料不用於訓練；**2026-10-02 實抓：文件表列其 robots.txt 不適用**（IP 清單 `openai.com/adsbot.json`）。非廣告主站台不需處理。

**OAI-SearchBot 生效延遲：** OpenAI 文件載明 robots.txt 變更約 24 小時後生效；封鎖後頁面仍可能以導覽連結形式出現。

**Web Bot Auth：** IETF draft，agent 以私鑰簽署每個 HTTP 請求供站方驗證身分（Cloudflare、Amazon、Akamai、OpenAI 支持，2026 成立 IETF 工作小組）。它處理「身分驗證」，robots.txt 處理「意願宣告」，兩者不互相取代。

---

## A6 — 各引擎的檢索來源與引用行為差異極大

**立場（Tier 2，數據來源需每次重新確認）：**

| 引擎 | 檢索基礎 | 每次回答引用數（概略） | 明顯偏好 |
|---|---|---|---|
| ChatGPT | Bing 索引 | ~4 | Wikipedia、廣泛網路權威、編輯型媒體 |
| Perplexity | 自有抓取，每次查訪約 10 頁引用 3–4 | ~12 | Reddit 等社群、時效性 |
| Claude | Brave Search（Anthropic subprocessor 清單列 Brave 為 Web Search vendor；Anthropic 未正式確認為唯一來源，另有自家 `Claude-SearchBot`） | ~2–3 | 技術精確、來源完備的內容 |
| Google AI Overviews / AI Mode | Google 索引 | 不定 | 與一般搜尋同一套判準 |

**引用數分歧（2026-10-02 補記）：** 2026 各彙整研究數字差異大——everything-pr 彙整 6 份研究、6.8 億則引用（2024-08 ~ 2026-04）：Perplexity 平均 16.35、Google AI Overviews 12.06、ChatGPT 6.88；Perplexity 與 Claude 來源重疊 13.6%、ChatGPT 與 Claude 僅 0.5%。上表概略值只作量級參考。

Yext 分析 680 萬則引用顯示，同一查詢下**僅約 11% 的被引用網域會跨平台重複出現**（Averi 於 2026-03 以 6.8 億則引用重測，同樣得到約 11%）。

**OAI-SearchBot 不執行 JavaScript（2026-08-16 補記）：** Writesonic 於 2026-03 的實驗確認 ChatGPT 的檢索端為 HTML-only parser。**因此的判斷規則：** 主要內容僅在 client-side JS 執行後才出現的站台（CSR SPA），對 ChatGPT 等同不存在；SSR / SSG / 靜態 HTML 站台不受此限。此項優先於任何內容層優化——內容抓不到時，其餘皆無意義。

**因此的判斷規則：** 「一套做法通吃所有 AI 引擎」不成立。目標引擎必須由使用者指定（Step 3），並據此調整重點；未指定時預設以 Google 一般搜尋為主軸，因為它同時是 AI Overviews 的基礎。

---

## A7 — Core Web Vitals 門檻

| 指標 | Good | Poor | 量測 |
|---|---|---|---|
| LCP | ≤ 2.5s | > 4.0s | CrUX 真實使用者第 75 百分位、28 天滾動 |
| INP | ≤ 200ms | > 500ms | 同上；2024-03 起取代 FID |
| CLS | ≤ 0.1 | > 0.25 | 同上 |

INP 是最常未達標的一項——2026 年統計約 **43% 的站台未達 200ms**。

**INP 取樣收緊（2026-09-24 補記）：** 門檻數值不變，但 2026 年 INP 對大量／重複互動頁面的取樣方式收緊，過去被平均掉的延遲會浮現；2026-05 CrUX（2026-06-09 發布）僅 55.9% origin 三項全過。程式未改而分數變差時，先查此項，不要誤判為回歸。

---

## A8 — 真正有槓桿的內容投資（Tier 2）

Princeton / Georgia Tech / IIT Delhi 的 GEO 研究指出，實體密集（entity-rich）、事實密集的內容在生成式回答中的可見度提升幅度顯著（研究報告區間 30–115%）。實務上對應到：

- **獨有數據**：benchmark、實測數字、案例——別人沒有的數字最容易被引用
- **實體一致性**：專案名、作者名、產品名在官網、GitHub、套件登錄頁、社群檔案間完全一致
- **第三方提及**：AI 引擎偏好站外佐證高於自家站內宣稱

**注意：** 該研究的可見度提升是相對於未優化基準的實驗結果，不是對任意站點的保證值。引用時須標註為研究結論而非承諾。

**GEO 文獻綜述（2026-09-24 補記）：** Martinez, *Optimizing Visibility in Generative Engines: A Critical Survey of GEO (2023–2026)*（arXiv 2607.14035，2026-07-15）回顧 45 篇研究：可重現的槓桿只有**主題相關性**與**內容在 context 中的位置**；已被檢索到的內容改寫後可因果地改變引用，但**沒有任何技術具備穩定、長期、跨平台的因果效果**；泛用技巧轉移性差，為引用而改寫甚至可能**損害檢索**。**因此的判斷規則：** 上列 30–115% 等數字只能標為實驗室條件下的研究結論；規劃不得以「GEO 技巧」為由改寫既有內容。

**站外品牌提及（2026-09-24 補記，Tier 2 相關性）：** Ahrefs 75,000 品牌研究顯示品牌提及（含無連結）與 AI Overviews 可見度相關係數 0.664，高於 backlinks 的 0.218；2026-05 追蹤報告中 YouTube 提及相關係數約 0.737，為所有訊號最高。這是**相關而非因果**，且無法由修改 repo 檔案達成——只能列入「需人工後續」，不得列為待執行項。

---

## A9 — ChatGPT 與 Copilot 的檢索層是 Bing 索引

**立場：** Bing 索引是 ChatGPT search 與 Microsoft Copilot 的檢索基礎（Tier 2：87%+ 的 ChatGPT search 引用與 Bing 前段結果重合）；頁面不在 Bing 索引等同於在這兩個引擎中不存在，與 Google 排名無關。Microsoft 於 2026-02 推出 Bing Webmaster Tools **AI Performance**（public preview），提供 Copilot 與 Bing AI 摘要的引用次數、被引用頁面、grounding queries。官方涵蓋範圍原文為「Microsoft Copilot, AI-generated summaries in Bing, and select partner integrations」，**未點名 ChatGPT**（2026-10-02 重抓確認）；第三方文章稱其涵蓋 ChatGPT 屬推論，不採信。

**Bing 官方建議（Tier 1）：** IndexNow 在內容新增、更新、刪除時主動通知參與的搜尋引擎，讓 AI 回答引用最新版本；另建議清楚的標題與表格結構、以證據支持主張、維持內容新鮮度。

**來源：** Bing Webmaster Blog *Introducing AI Performance in Bing Webmaster Tools Public Preview*（2026-02）。

**因此的判斷規則：** 目標引擎含 ChatGPT（固定預設包含）→ Bing Webmaster Tools 驗證與 sitemap 提交為必要檢查項；有建置／部署流程者建議接 IndexNow。

---

## A10 — 日期與新鮮度

**立場（Tier 1）：** Google 建議以 `datePublished` / `dateModified` 標註於 `CreativeWork` 子類型（`Article`、`BlogPosting`、`TechArticle`），並在頁面上可見地顯示「最後更新」日期；日期必須是實際發布或更新日，**禁止未來日期或與內容無關的日期**。

**Tier 2（樣本與方法差異大，每次重新確認）：** 多份 2026 研究指出 AI 引用偏好新內容——約半數被引用內容小於 13 週、Perplexity 對當年度內容偏好約 1.69 倍、Gemini 幾乎無偏好（0.78 倍）、ChatGPT 隨模型版本擺盪。

**因此的判斷規則：** 內容型頁面缺 `dateModified` → 依實際 git 修改時間或建置時間補上。**禁止**在內容未實質變動時改寫日期以製造新鮮度——這屬於操弄，且 Google 已將 spam policies 延伸到 AI 回答（A1）。

---

## A11 — 指定預覽圖（Preferred image）

**立場（Tier 1，2026-03-02）：** Google 於 image SEO 文件新增「Specify a preferred image with metadata」：可用 schema.org `primaryImageOfPage`、主實體（`mainEntity` / `mainEntityOfPage`）的 `image`，或 `og:image` 指定。選圖原則：與頁面相關且具代表性、避免通用圖與含文字的圖、避免極端長寬比、盡量高解析度。Google 的選圖仍為自動化，metadata 是訊號而非保證。

---

## A12 — AI 讀取／使用控制的標準與提案追蹤

**上次驗證：2026-10-02**（每次由 research_protocol A-5 逐列比對更新）

| 名稱 | 組織 | 機制 | 狀態 | 可實作 |
|---|---|---|---|---|
| llms.txt | Answer.AI（llmstxt.org） | `/llms.txt`、每頁 `.md`、`rel="describedby"`／`rel="alternate" type="text/markdown"` | v2，修改日 2026-08-10；無標準組織、IANA 未登錄 | 是（事實慣例，見 A3） |
| AI Usage Preferences vocab | IETF aipref WG | `train-ai`、`ai-use`、`search`，值 `y`／`n` | draft-ietf-aipref-vocab-08（2026-09-14），WG Document；IESG 里程碑 2026-08-31 已過未送 | 草案；需使用者決定政策 |
| AI Usage Preferences attach | IETF aipref WG | robots.txt `Content-Usage: [path] <prefs>`；HTTP header `Content-Usage` | draft-ietf-aipref-attach-05（2026-08-19），WG Document | 草案；需使用者決定政策 |
| Content Signals | Cloudflare | robots.txt `Content-signal: search=yes, ai-input=yes, ai-train=no`（詞彙與 aipref 不同） | 廠商自訂，文件 2026-08-03 | 是；需使用者決定政策 |
| TDMRep | W3C CG | `/.well-known/tdmrep.json`、header／meta `tdm-reservation`、`tdm-policy` | CG Final Report 2024-05-10；IANA provisional | 是；僅在需表達 EU DSM 第 4 條保留時 |
| Web Bot Auth | IETF webbotauth WG | bot 端 HTTP Message Signatures；bot 在自身網域發布 `/.well-known/http-message-signatures-directory` | draft-ietf-webbotauth-httpsig-protocol-00（2026-09-01） | 網站端無需動作 |
| A2A Agent Card | Linux Foundation | `/.well-known/agent-card.json` | IANA permanent，A2A 1.0.0 | 僅限提供 A2A agent 的站 |
| MCP Server Card | MCP Server Card WG | well-known 路徑未定（SEP-2127 PR Open） | experimental | 否 |
| WebMCP | W3C Web ML CG | 前端 `document.modelContext.registerTool()` | Draft CG Report 2026-09-30 | 否（需互動工具） |
| agents.txt、`/.well-known/ai` 等 agent discovery | 個人 I-D | 各自不同 | 皆未被 WG 採納 | 否 |

**廠商支援（官方頁）：** 截至驗證日，Google、OpenAI、Anthropic、Bing 的官方 crawler 頁皆未提及 `Content-Usage`、Content-Signal、TDMRep 或 llms.txt；Cloudflare 自家文件支援 Content Signals。

**判斷規則：** 「可實作」欄為「需使用者決定政策」者，技術上可直接加，但值（是否允許 AI 訓練、AI 輸入、搜尋）是內容授權決策，第一次必須詢問並寫入 config，之後依 config 套用；「否」者只追蹤不實作。
