# Changelog

完整規範以 `SKILL.md` 與 `scripts/` 為準（＝最新規範）；本檔只用於快速定位先前執行本 skill 套用到專案的 SEO 變更（meta、JSON-LD、robots.txt、sitemap、`_headers`、llms.txt、建置腳本等）與最新規範的差異，命中即直接修改。

最新改動：2026-10-07

## 破壞性變更

- JSON-LD Organization 英文名 `Pardn Co., LTD` → `Pardn Co., Ltd`（英文頁 `name`、中文頁 `alternateName`；使用者 2026-10-06 指定，取代先前的 LTD 寫法）
- 關鍵字僅由原始碼推導（config 無 `keyword_research`）→ 跑 Phase B 競品研究（搜尋第一頁、GitHub 高星同類、HN），依 Step 3「候選設計」重新提出關鍵字並詢問，寫入 `keyword_research`；關鍵字變動時連帶重評 `one_liner` 與 topics（R9）。舊 Phase B（關鍵字後補查）改名 Phase C
- README 首段只回報建議、repo description 另寫 → 依 R9 設計 config `one_liner`（en／zh），repo description 改為 `one_liner.en`（保留 `(module)` 等前綴），README 與中文 README 順序 3 那一行替換為 `one_liner`
- robots.txt `Content-Usage:` 與 `_headers` 的 `Content-Usage` header 缺 `ai-use=` → 依 config `ai_usage` 補成 `train-ai=y|n, ai-use=y|n, search=y|n`（R13）
- Person `sameAs` 未含 `~/.skill-readme-generate.json` 的 `same_as` 全部項目 → 補齊，作者網站本身也放入；舊規則「`sameAs` 不放節點自己的 `url`」已移除（R10）
- 以地區／職能／口號等定位文字宣告的 `Organization` 節點（作者掛 `founder`）→ 移除，改放可見署名或 tagline；多語頁改用各語言正式名稱並共用 `@id`（R6）。事故：go-llm-router 文件站
- `dateModified`／sitemap `lastmod` 取自檔案 mtime 或每次建置日期 → 改為內容雜湊變動才更新 `modified`，頁面可見日期、JSON-LD、`lastmod` 取同一值（R6 日期）
- llms.txt 連結指向 HTML 頁 → 改指向每頁 Markdown 版（`/{slug}.md`），並補齊 R7.3 v2 的探索連結、可見連結與 `.md`／`.txt` 的 `charset=utf-8`
- IndexNow 每次重送全部 URL 或未檢查回應碼 → 只送 `lastmod` 與上次送出紀錄不同的 URL，200／202 才記為已送出，key 檔於部署前產生（R12）
