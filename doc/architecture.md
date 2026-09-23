# seo-optimize - Architecture

> Back to [README](../README.md)

## Overview

```mermaid
graph TB
    User[User invokes /seo-optimize] --> Skill[SKILL.md<br/>Orchestration]
    Skill --> Research[research_protocol.md<br/>Phase A / B]
    Research --> Web[Web Search + WebFetch<br/>Tier 1 Primary Sources]
    Research <--> Anchors[knowledge_anchors.md<br/>Position Snapshot A1–A11]
    Skill --> Analyze[analyze_seo.py<br/>Surface Inventory]
    Skill --> Source[Source Reading<br/>Purpose / Audience / Domain]
    Skill --> Ask[AskUserQuestion<br/>Keywords / Locality]
    Ask --> Config[config.json]
    Analyze --> Rules[optimization_rules.md<br/>Routing + R1–R12]
    Source --> Rules
    Config --> Rules
    Research --> Digest[research-date.md]
    Digest --> Rules
    Rules --> Plan[ts-plan.md]
    Plan --> Gate{User Confirmation}
    Gate -->|Confirmed| Apply[Apply File Changes]
    Apply --> Applied[ts-applied.md]
```

## Module: Research Protocol

Fetches the last six months of SEO / AEO / GEO guidance on every run, resolves conflicts by source tier, and writes changes back to the position snapshot.

```mermaid
graph TB
    subgraph Research[Research Protocol]
        Date[date for today<br/>six-month window] --> A1[A-1 Eleven Parallel Queries]
        Date --> A2[A-2 Fetch Five Primary Sources]
        A1 --> Tier[A-3 Source Tiers<br/>Tier 1–4]
        A2 --> Tier
        Tier --> Conflict[A-4 Conflict Handling]
        Conflict -->|Consistent with Tier 1| Actionable[Actionable Conclusions]
        Conflict -->|Overturned by Tier 1| Myth[Industry Myths Column]
        PhaseB[Phase B<br/>Keywords / Languages / Locality / Dev Tools] --> Tier
    end
    Google[Google Search Central<br/>AI Guide / Updates / Fetcher List] --> A2
    Vendors[OpenAI Bots Docs<br/>Anthropic Crawler Docs] --> A2
    Anchors[knowledge_anchors.md] -->|Detect Changes| Conflict
    Conflict -->|Update In Place on Mismatch| Anchors
    Actionable --> Digest[research-date.md]
    Myth --> Digest
```

## Module: Analyzer (analyze_seo.py)

Performs a mechanical inventory only, reporting code type and deployable web surfaces separately and reporting absent surfaces as absent.

```mermaid
graph TB
    subgraph Analyzer[analyze_seo.py]
        Iter[_iter_files<br/>skips node_modules / dist etc.] --> HTML[parse_html]
        Iter --> MD[parse_content]
        Iter --> Crawl[collect_crawler_directives]
        Iter --> APIs[scan_metadata_apis]
        HTML --> Meta[_collect_meta<br/>description / robots / OG / verification]
        HTML --> Links[_collect_links<br/>canonical / hreflang]
        HTML --> LD[_collect_jsonld<br/>types / dateModified / parse failures]
        HTML --> CSR[csr_shell / data_nosnippet]
        Crawl --> Robots[_classify_robots_agents<br/>training / retrieval]
        HTML --> Group[group_web_surfaces]
        FW[detect_web_framework] --> Type[detect_code_type]
        Pkg[collect_package_metadata<br/>npm / Go / PyPI / Packagist]
    end
    Root[PROJECT_PATH] --> Iter
    Root --> FW
    Root --> Pkg
    Group --> Out[JSON Output]
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

## Module: Rule Routing

Selects the active rule groups from `code_type` and `web_surfaces`, then filters suggestions through the forbidden-actions table.

```mermaid
graph TB
    subgraph Routing[optimization_rules.md]
        In[Analysis Result] --> HasWeb{web_surfaces not empty}
        In --> IsPkg{code_type is library / cli}
        HasWeb -->|Yes| Page[R1–R6 Page Level<br/>R7 Crawler Directives<br/>R11 Initial HTML<br/>R12 Index Submission]
        IsPkg -->|Yes| Pkg[R8 Package Registry<br/>R9 GitHub / README]
        Page --> Entity[R10 Entity Consistency]
        Pkg --> Entity
        HasWeb -->|No and IsPkg| NoWeb[Report No Web SEO Surface]
        Entity --> Filter[Forbidden Actions Filter]
        Filter --> Severity[Severity<br/>Critical / High / Medium / Low]
    end
    Severity --> Plan[ts-plan.md]
```

## Data Flow

```mermaid
sequenceDiagram
    participant U as User
    participant S as SKILL.md
    participant R as Research Protocol
    participant A as analyze_seo.py
    participant C as config.json
    participant P as Project Files
    U->>S: /seo-optimize [PATH] [--plan] [--reset]
    S->>R: Phase A (queries + primary-source fetch)
    R-->>S: research-date.md (updates knowledge_anchors.md if needed)
    S->>A: Inventory PROJECT_PATH
    A-->>S: JSON (code_type / surfaces / pages ...)
    S->>P: Read source and README
    S->>C: Load existing settings
    alt Missing, incomplete, or --reset
        S->>U: Ask keywords and locality
        U-->>S: Answers
        S->>C: Write
    end
    S->>R: Phase B (keyword-targeted research)
    S->>S: Build ts-plan.md from rules
    S->>U: Present plan summary
    alt --plan
        S-->>U: Stop
    else User confirms
        S->>P: Apply item by item (no git / gh)
        S-->>U: ts-applied.md + manual follow-ups
    end
```

## State Machine

```mermaid
stateDiagram-v2
    [*] --> Research
    Research --> Analyze: digest written
    Research --> Analyze: offline (snapshot date stated)
    Analyze --> LoadConfig
    LoadConfig --> Ask: missing / incomplete / --reset
    LoadConfig --> TargetedResearch: settings complete
    Ask --> TargetedResearch
    TargetedResearch --> Plan
    Plan --> [*]: --plan
    Plan --> AwaitConfirm
    AwaitConfirm --> Apply: user confirms
    AwaitConfirm --> [*]: user rejects
    Apply --> Verify
    Verify --> [*]
```
