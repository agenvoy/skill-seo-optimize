# seo-optimize - 架構

> 返回 [README](./README.zh.md)

## 概覽

```mermaid
graph TB
    User[使用者呼叫 /seo-optimize] --> Skill[SKILL.md<br/>流程協調]
    Skill --> Research[research_protocol.md<br/>Phase A / B]
    Research --> Web[網路搜尋 + WebFetch<br/>Tier 1 一手來源]
    Research <--> Anchors[knowledge_anchors.md<br/>立場快照 A1–A11]
    Skill --> Analyze[analyze_seo.py<br/>surface 盤點]
    Skill --> Source[實讀原始碼<br/>用途 / 使用者 / 網域]
    Skill --> Ask[AskUserQuestion<br/>關鍵字 / 在地性]
    Ask --> Config[config.json]
    Analyze --> Rules[optimization_rules.md<br/>路由 + R1–R12]
    Source --> Rules
    Config --> Rules
    Research --> Digest[research-date.md]
    Digest --> Rules
    Rules --> Plan[ts-plan.md]
    Plan --> Gate{使用者確認}
    Gate -->|確認| Apply[套用檔案變更]
    Apply --> Applied[ts-applied.md]
```

## Module: 研究協定

每次執行都重新取得近半年的 SEO / AEO / GEO 現況，以來源分級裁決衝突，並回寫立場快照。

```mermaid
graph TB
    subgraph Research[研究協定]
        Date[date 取今日<br/>推算半年時間窗] --> A1[A-1 十一組查詢並行]
        Date --> A2[A-2 五個一手來源實抓]
        A1 --> Tier[A-3 來源分級<br/>Tier 1–4]
        A2 --> Tier
        Tier --> Conflict[A-4 衝突處理]
        Conflict -->|與 Tier 1 一致| Actionable[可執行結論]
        Conflict -->|被 Tier 1 推翻| Myth[業界迷思欄]
        PhaseB[Phase B<br/>關鍵字 / 多語 / 在地 / 開發者工具] --> Tier
    end
    Google[Google Search Central<br/>AI 優化指南 / 更新日誌 / fetcher 清單] --> A2
    Vendors[OpenAI bots 文件<br/>Anthropic crawler 文件] --> A2
    Anchors[knowledge_anchors.md] -->|偵測變動| Conflict
    Conflict -->|不符時就地更新| Anchors
    Actionable --> Digest[research-date.md]
    Myth --> Digest
```

## Module: 分析器（analyze_seo.py）

只做機械盤點，把程式碼類型與可部署的 web surface 分開回報，不存在的 surface 回報為不存在。

```mermaid
graph TB
    subgraph Analyzer[analyze_seo.py]
        Iter[_iter_files<br/>略過 node_modules / dist 等] --> HTML[parse_html]
        Iter --> MD[parse_content]
        Iter --> Crawl[collect_crawler_directives]
        Iter --> APIs[scan_metadata_apis]
        HTML --> Meta[_collect_meta<br/>description / robots / OG / 驗證碼]
        HTML --> Links[_collect_links<br/>canonical / hreflang]
        HTML --> LD[_collect_jsonld<br/>類型 / dateModified / 解析失敗]
        HTML --> CSR[csr_shell / data_nosnippet]
        Crawl --> Robots[_classify_robots_agents<br/>訓練型 / 檢索型]
        HTML --> Group[group_web_surfaces]
        FW[detect_web_framework] --> Type[detect_code_type]
        Pkg[collect_package_metadata<br/>npm / Go / PyPI / Packagist]
    end
    Root[PROJECT_PATH] --> Iter
    Root --> FW
    Root --> Pkg
    Group --> Out[JSON 輸出]
    Type --> Out
    Robots --> Out
    APIs --> Out
    MD --> Out
    Pkg --> Out
```

```mermaid
classDiagram
    class PageInfo {
        path
        title / description
        canonical / lang / robots_meta
        og / twitter
        jsonld_types / jsonld_invalid
        jsonld_date_modified
        data_nosnippet
        site_verification
        csr_shell
        hreflang / h1 / h2_count / word_count
    }
    class ContentInfo {
        path
        title / description / keywords
        has_frontmatter
        h1 / word_count
    }
    class CrawlerDirectives {
        robots_txt / robots_txt_agents
        robots_txt_blocked_training
        robots_txt_blocked_retrieval
        robots_txt_sitemaps
        sitemap_files / sitemap_generators
        llms_txt / manifest
    }
```

## Module: 規則路由

依 `code_type` 與 `web_surfaces` 決定生效的規則組，並以禁止動作表過濾建議。

```mermaid
graph TB
    subgraph Routing[optimization_rules.md]
        In[分析結果] --> HasWeb{web_surfaces 非空}
        In --> IsPkg{code_type 為 library / cli}
        HasWeb -->|是| Page[R1–R6 頁面層<br/>R7 爬蟲指令<br/>R11 初始 HTML<br/>R12 索引提交]
        IsPkg -->|是| Pkg[R8 套件登錄頁<br/>R9 GitHub / README]
        Page --> Entity[R10 實體一致性]
        Pkg --> Entity
        HasWeb -->|否且 IsPkg| NoWeb[告知無 web SEO surface]
        Entity --> Filter[禁止動作過濾]
        Filter --> Severity[嚴重度分級<br/>Critical / High / Medium / Low]
    end
    Severity --> Plan[ts-plan.md]
```

## 資料流

```mermaid
sequenceDiagram
    participant U as 使用者
    participant S as SKILL.md
    participant R as 研究協定
    participant A as analyze_seo.py
    participant C as config.json
    participant P as 專案檔案
    U->>S: /seo-optimize [PATH] [--plan] [--reset]
    S->>R: Phase A（查詢 + 一手來源實抓）
    R-->>S: research-date.md（必要時更新 knowledge_anchors.md）
    S->>A: 盤點 PROJECT_PATH
    A-->>S: JSON（code_type / surfaces / pages ...）
    S->>P: 實讀原始碼與 README
    S->>C: 讀取既有設定
    alt 缺失、不完整或 --reset
        S->>U: 詢問關鍵字與在地性
        U-->>S: 回答
        S->>C: 寫入
    end
    S->>R: Phase B（依關鍵字補研究）
    S->>S: 依規則產出 ts-plan.md
    S->>U: 呈現規劃摘要
    alt --plan
        S-->>U: 結束
    else 使用者確認
        S->>P: 逐項套用（不執行 git / gh）
        S-->>U: ts-applied.md + 需人工後續
    end
```

## 狀態機

```mermaid
stateDiagram-v2
    [*] --> Research
    Research --> Analyze: digest 落檔
    Research --> Analyze: 網路不可用（明示使用快照日期）
    Analyze --> LoadConfig
    LoadConfig --> Ask: 缺失 / 不完整 / --reset
    LoadConfig --> TargetedResearch: 設定完整
    Ask --> TargetedResearch
    TargetedResearch --> Plan
    Plan --> [*]: --plan
    Plan --> AwaitConfirm
    AwaitConfirm --> Apply: 使用者確認
    AwaitConfirm --> [*]: 使用者否決
    Apply --> Verify
    Verify --> [*]
```
