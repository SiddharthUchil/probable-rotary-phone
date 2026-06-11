# RAG Knowledge Base Analysis — `GBL1Summarydump_Addltags_06012026.xlsx`
_Generated 2026-06-10 16:28 UTC by `analyze_rag_excel.py`._

## 1. Executive Summary
- **Workbook**: `GBL1Summarydump_Addltags_06012026.xlsx` with **1 sheet(s)** and **4,867 rows** across **41 columns**.
- **Detected business purpose**: Salesforce Knowledge dump augmented with LLM-derived metadata (summary, FAQs, category, entities). Records represent **knowledge articles (parent + version)** intended for ingestion into an enterprise hybrid-search RAG index.
- **Column-role distribution**: constant=7, category=5, empty=4, key=3, url=3, tag=3, metadata=2, content=2, date=2, summary=2, language=1, version=1, title=1, persona=1, product=1, access=1, metric=1, tracking=1
- **Top RAG risks**: 4 empty columns; 7 constant columns; 8 columns with PII pattern hits; 0 exact-duplicate content rows.
- **Headline scorecard verdict**: Content completeness=5, Content freshness=4, Metadata quality=4, Citation readiness=5, Searchability=5 …

## 2. Dataset Overview
| Sheet | Rows | Columns | Empty cols | Constant cols | Content cols |
| --- | --- | --- | --- | --- | --- |
| result | 4867 | 41 | 4 | 7 | 4 |

**Inferred dataset character**
- Records are **whole knowledge articles** (one row per Salesforce Knowledge article version) rather than pre-chunked passages — every row carries the full article body in HTML and Markdown variants, plus an LLM summary and FAQ extraction.
- IDs follow Salesforce conventions (15/18-char alphanumeric). Multiple versions per `ArticleNumber` exist; `SalesforceID` is the natural per-version primary key.
- Content paths are deep-links into Salesforce Lightning — usable as citations.
- Several columns are tracking metadata (run IDs, timestamps, format flag); these should live in operational logs, not in the search index.

## 3. Column Inventory
| Column | Inferred type | Null # | Null % | Unique # | Examples | Role | Index intent (search/filter/facet/sort/retrieve) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `ArticleNumber` | int64 | 0 | 0.0% | 3028 | `7415`<br>`7415`<br>`7419` | key | ·F····R |
| `Attachment_Links__c` | object | 4755 | 97.7% | 88 | `<div style="box-sizing: border-box;color: rgb(8, 7, 7);font-family: &quot;Sales…`<br>`<span style="font-size: 12px;"><span style="font-family: Arial,Helvetica,sans-s…`<br>`<div>​​​​​​</div>` | url | ······R |
| `Forms_Channel__c` | float64 | 4867 | 100.0% | 0 | _(none)_ | empty | ······· |
| `Forms_Plan_Number__c` | object | 1137 | 23.36% | 1508 | `P113219, P115492, P133548`<br>`P113219, P115492, P133548`<br>`$ystems&Workflow` | metadata | ·F····R |
| `Forms_Product_Name__c` | float64 | 4867 | 100.0% | 0 | _(none)_ | empty | ······· |
| `SalesforceID` | object | 0 | 0.0% | 4867 | `ka5OM000000hugTYAQ`<br>`ka5OM000000hui5YAA`<br>`ka5OM0000012deDYAQ` | key | ·F····R |
| `Knowledge_Content` | object | 5 | 0.1% | 4705 | `<table border="1" style="width: 100.02%;border-width: 1px;"><tbody><tr><td cols…`<br>`<table border="1" style="width: 100.02%;border-width: 1px;"><tbody><tr><td cols…`<br>`<table border="1" style="width: 100%;"><tbody><tr><td colspan="1" rowspan="1" s…` | content | S·····R |
| `Language` | object | 0 | 0.0% | 2 | `en_US`<br>`fr`<br>`en_US` | language | ·FFa··R |
| `VersionNumber` | int64 | 0 | 0.0% | 77 | `5`<br>`5`<br>`7` | version | ·F··SoR |
| `LastModifiedDate` | datetime64[ns] | 0 | 0.0% | 2952 | `2025-06-10 14:18:41`<br>`2025-06-10 14:18:41`<br>`2026-03-11 15:12:05` | date | ·F··SoR |
| `PublishStatus` | object | 0 | 0.0% | 1 | `Online`<br>`Online`<br>`Online` | constant | ······· |
| `RecordTypeName__c` | object | 0 | 0.0% | 9 | `Procedure`<br>`Procedure`<br>`Procedure` | category | ·FFa··R |
| `Summary` | object | 25 | 0.51% | 3914 | `This article  outlines the specific details for Liteline Corporation`<br>`Cet article décrit les détails spécifiques à Liteline Corporation`<br>`This article outlines how to handle returned mail from Canada Post.` | summary | S·····R |
| `Title` | object | 0 | 0.0% | 4853 | `PS Notes V3 - Plan Number 113219 Liteline Corporation`<br>`PS Notes V3 - Régime Numéro 113219 Liteline Corporation`<br>`GB CSC Workflow: Returned Mail` | title | S···SoR |
| `SFUrlName` | object | 0 | 0.0% | 4635 | `PS-Notes-V3-Plan-Number-113219-Liteline-Corporation`<br>`PS-Notes-V3-Régime-Numéro-113219-Liteline-Corporation`<br>`GB-CSC-Workflow-Returned-Mail` | key | ·F····R |
| `Sort_Order__c` | float64 | 4867 | 100.0% | 0 | _(none)_ | empty | ······· |
| `Business_Unit` | object | 0 | 0.0% | 77 | `GB_Living_Benefits,GB_Internet,GB_Dental,GB_Admin,GB_Health,GB_Vitality,GB_Flex…`<br>`GB_Living_Benefits,GB_Internet,GB_Dental,GB_Admin,GB_Health,GB_Vitality,GB_Flex…`<br>`GB_Living_Benefits,GB_Internet,GB_Dental,GB_Admin,GB_Health,GB_Vitality,GB_Flex…` | category | ·FFa··R |
| `Contact_Type` | object | 937 | 19.25% | 118 | `Allianz,Plan_Administrator,Dental_Provider,Advisor,Customer_Beneficiary,Interna…`<br>`Allianz,Plan_Administrator,Dental_Provider,Advisor,Customer_Beneficiary,Interna…`<br>`Plan_Administrator,Internal_Employee,RGO,Third_Party_Administrator` | persona | ·FFa··R |
| `Case_Subtype` | object | 1024 | 21.04% | 77 | `All`<br>`All`<br>`All` | metadata | ·F····R |
| `System_of_Record` | object | 1025 | 21.06% | 11 | `Gipsy,Manuconnect`<br>`Gipsy,Manuconnect`<br>`Gipsy,Manuconnect` | category | ·FFa··R |
| `Case_Type` | object | 1018 | 20.92% | 61 | `All`<br>`All`<br>`All` | category | ·FFa··R |
| `Product_Family` | object | 1023 | 21.02% | 7 | `GROUP_BENEFITS`<br>`GROUP_BENEFITS`<br>`GROUP_BENEFITS` | product | SFFa··R |
| `PrivacyFlag` | object | 0 | 0.0% | 2 | `No`<br>`No`<br>`No` | access | ·F····· |
| `content_path` | object | 0 | 0.0% | 4867 | `https://mlc.lightning.force.com/lightning/r/Knowledge__kav/ka5OM000000hugTYAQ/v…`<br>`https://mlc.lightning.force.com/lightning/r/Knowledge__kav/ka5OM000000hui5YAA/v…`<br>`https://mlc.lightning.force.com/lightning/r/Knowledge__kav/ka5OM0000012deDYAQ/v…` | url | ······R |
| `priorityscore` | int64 | 0 | 0.0% | 4 | `1`<br>`1`<br>`1` | metric | ·F··SoR |
| `parent_path` | object | 3996 | 82.1% | 30 | `https://mlc.lightning.force.com/lightning/r/Knowledge__kav/ka5OM000000Tpn7YAC/v…`<br>`https://mlc.lightning.force.com/lightning/r/Knowledge__kav/ka5OM000000Tpn7YAC/v…`<br>`https://mlc.lightning.force.com/lightning/r/Knowledge__kav/ka5OM000000Tpn7YAC/v…` | url | ······R |
| `is_deleted` | bool | 0 | 0.0% | 1 | `False`<br>`False`<br>`False` | constant | ······· |
| `deleted_at` | float64 | 4867 | 100.0% | 0 | _(none)_ | empty | ······· |
| `ingested_at` | datetime64[ns] | 0 | 0.0% | 1 | `2026-05-23 21:53:42.933000`<br>`2026-05-23 21:53:42.933000`<br>`2026-05-23 21:53:42.933000` | constant | ······· |
| `run_id` | object | 0 | 0.0% | 1 | `layer2-20260601-035813`<br>`layer2-20260601-035813`<br>`layer2-20260601-035813` | constant | ······· |
| `llm_summary` | object | 0 | 0.0% | 4867 | `PS Notes V3 - Plan Number 113219 Liteline Corporation. The group has Short Term…`<br>`PS Notes V3 - Régime Numéro 113219 Liteline Corporation. Le groupe bénéficie de…`<br>`GB CSC Workflow: Returned Mail. Mail is returned from Canada Post when the addr…` | summary | S·····R |
| `llm_faqs` | object | 0 | 0.0% | 4605 | `["What is included under Plan 133548 for Liteline Corporation?","Where can I fi…`<br>`["Quels services de gestion des absences sont disponibles pour le régime numéro…`<br>`["What happens when mail is returned from Canada Post due to an incorrect addre…` | tag | SFFa··R |
| `source_run_id` | object | 0 | 0.0% | 1 | `layer1-20260523-215308`<br>`layer1-20260523-215308`<br>`layer1-20260523-215308` | constant | ······· |
| `prompt_version` | object | 0 | 0.0% | 1 | `v4`<br>`v4`<br>`v4` | constant | ······· |
| `summarized_at` | datetime64[ns] | 0 | 0.0% | 99 | `2026-06-01 04:02:42.690000`<br>`2026-06-01 04:02:42.690000`<br>`2026-06-01 04:02:42.690000` | date | ·F··SoR |
| `Knowledge_Content_md` | object | 5 | 0.1% | 4532 | `\\|  \\| \\| --- \\| \\| **Absence and Disability Management** \\| \\| - Group has Short Term…`<br>`\\|  \\| \\| --- \\| \\| **Gestion des absences et de l’invalidité** \\| \\| - Ce groupe béné…`<br>`\\|  \\| \\| --- \\| \\| **Important Information** \\| \\| Mail is returned from Canada Post…` | content | S·····R |
| `content_format` | object | 0 | 0.0% | 1 | `markdown`<br>`markdown`<br>`markdown` | constant | ······· |
| `llm_category` | object | 0 | 0.0% | 25 | `plan_administration`<br>`plan_administration`<br>`plan_administration` | category | ·FFa··R |
| `llm_focus` | object | 0 | 0.0% | 143 | `["member_administration"]`<br>`["member_administration","reference"]`<br>`["member_administration","document_fulfillment"]` | tag | SFFa··R |
| `llm_entities` | object | 0 | 0.0% | 4516 | `["Short Term Absence Salary Continuance (STASC)","Plan 133548"]`<br>`["Solution de gestion des absences de courte durée","Continuation du salaire","…`<br>`["Returned mail","Canada Post","Plan Administrator (PA)","[REDACTED-EMAIL]","AW…` | tag | SFFa··R |
| `llm_error` | object | 4863 | 99.92% | 4 | `LLM parse failure: parsed=None, parsing_error=None (input_chars=24802, tool_cal…`<br>`LLM parse failure: parsed=None, parsing_error=None (input_chars=63207, tool_cal…`<br>`LLM parse failure: parsed=None, parsing_error=None (input_chars=66842, tool_cal…` | tracking | ······· |

_Legend_: `S`=searchable, `F`=filterable, `Fa`=facetable, `So`=sortable, `R`=retrievable. Detailed per-column profile in the Appendix.

## 4. Data Quality Findings
### 4.1 Headline metrics per column
| Column | Null % | Blank/WS % | Constant | Near-const | PII hits | HTML % | JSON-like % | URL % | Date % | Multi-value % | Casing inconsistent |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `ArticleNumber` | 0.0% | 0.0% |  |  |  | 0% | 0% | 0% | 0% | 0% |  |
| `Attachment_Links__c` | 97.7% | 0.0% |  |  |  | 100% | 0% | 67% | 0% | 66% |  |
| `Forms_Channel__c` | 100.0% | 0.0% |  |  |  | 0% | 0% | 0% | 0% | 0% |  |
| `Forms_Plan_Number__c` | 23.36% | 0.0% |  |  |  | 0% | 0% | 0% | 0% | 39% |  |
| `Forms_Product_Name__c` | 100.0% | 0.0% |  |  |  | 0% | 0% | 0% | 0% | 0% |  |
| `SalesforceID` | 0.0% | 0.0% |  |  |  | 0% | 0% | 0% | 0% | 0% |  |
| `Knowledge_Content` | 0.1% | 0.0% |  |  | 1077 | 100% | 0% | 47% | 0% | 83% |  |
| `Language` | 0.0% | 0.0% |  |  |  | 0% | 0% | 0% | 0% | 0% |  |
| `VersionNumber` | 0.0% | 0.0% |  |  |  | 0% | 0% | 0% | 0% | 0% |  |
| `LastModifiedDate` | 0.0% | 0.0% |  |  |  | 0% | 0% | 0% | 100% | 0% |  |
| `PublishStatus` | 0.0% | 0.0% | ✓ |  |  | 0% | 0% | 0% | 0% | 0% |  |
| `RecordTypeName__c` | 0.0% | 0.0% |  |  |  | 0% | 0% | 0% | 0% | 0% |  |
| `Summary` | 0.51% | 0.0% |  |  | 3 | 0% | 0% | 0% | 0% | 5% |  |
| `Title` | 0.0% | 0.0% |  |  | 51 | 0% | 0% | 0% | 0% | 20% |  |
| `SFUrlName` | 0.0% | 0.0% |  |  | 51 | 0% | 0% | 0% | 0% | 0% |  |
| `Sort_Order__c` | 100.0% | 0.0% |  |  |  | 0% | 0% | 0% | 0% | 0% |  |
| `Business_Unit` | 0.0% | 0.0% |  |  |  | 0% | 0% | 0% | 0% | 71% |  |
| `Contact_Type` | 19.25% | 0.0% |  |  |  | 0% | 0% | 0% | 0% | 93% |  |
| `Case_Subtype` | 21.04% | 0.0% |  |  |  | 0% | 0% | 0% | 0% | 13% |  |
| `System_of_Record` | 21.06% | 0.0% |  |  |  | 0% | 0% | 0% | 0% | 73% |  |
| `Case_Type` | 20.92% | 0.0% |  |  |  | 0% | 0% | 0% | 0% | 4% |  |
| `Product_Family` | 21.02% | 0.0% |  | ✓ |  | 0% | 0% | 0% | 0% | 0% |  |
| `PrivacyFlag` | 0.0% | 0.0% |  | ✓ |  | 0% | 0% | 0% | 0% | 0% |  |
| `content_path` | 0.0% | 0.0% |  |  |  | 0% | 0% | 100% | 0% | 0% |  |
| `priorityscore` | 0.0% | 0.0% |  |  |  | 0% | 0% | 0% | 0% | 0% |  |
| `parent_path` | 82.1% | 0.0% |  |  |  | 0% | 0% | 100% | 0% | 0% |  |
| `is_deleted` | 0.0% | 0.0% | ✓ |  |  | 0% | 0% | 0% | 0% | 0% |  |
| `deleted_at` | 100.0% | 0.0% |  |  |  | 0% | 0% | 0% | 0% | 0% |  |
| `ingested_at` | 0.0% | 0.0% | ✓ |  |  | 0% | 0% | 0% | 100% | 0% |  |
| `run_id` | 0.0% | 0.0% | ✓ |  |  | 0% | 0% | 0% | 0% | 0% |  |
| `llm_summary` | 0.0% | 0.0% |  |  | 745 | 0% | 0% | 2% | 0% | 52% |  |
| `llm_faqs` | 0.0% | 0.0% |  |  | 6 | 0% | 100% | 0% | 0% | 26% |  |
| `source_run_id` | 0.0% | 0.0% | ✓ |  |  | 0% | 0% | 0% | 0% | 0% |  |
| `prompt_version` | 0.0% | 0.0% | ✓ |  |  | 0% | 0% | 0% | 0% | 0% |  |
| `summarized_at` | 0.0% | 0.0% |  |  |  | 0% | 0% | 0% | 100% | 0% |  |
| `Knowledge_Content_md` | 0.1% | 0.0% |  |  | 1285 | 7% | 0% | 41% | 0% | 35% |  |
| `content_format` | 0.0% | 0.0% | ✓ |  |  | 0% | 0% | 0% | 0% | 0% |  |
| `llm_category` | 0.0% | 0.0% |  |  |  | 0% | 0% | 0% | 0% | 0% |  |
| `llm_focus` | 0.0% | 0.0% |  |  |  | 0% | 100% | 0% | 0% | 0% |  |
| `llm_entities` | 0.0% | 0.0% |  |  | 697 | 0% | 100% | 1% | 0% | 9% |  |
| `llm_error` | 99.92% | 0.0% |  |  |  | 0% | 0% | 0% | 0% | 75% |  |

### 4.2 Data Quality Risks for RAG
- **`Forms_Channel__c` is entirely empty** → wastes a slot in the schema and confuses content owners. Drop or repurpose.
- **`Forms_Product_Name__c` is entirely empty** → wastes a slot in the schema and confuses content owners. Drop or repurpose.
- **`Knowledge_Content` contains PII patterns** (EMAIL=521, PHONE=552, IPV4=2, CREDITCARD=2) → review for redaction before embedding/indexing to avoid leaking PII via vector or BM25 retrieval.
- **`Knowledge_Content` carries raw HTML entities** (3466 rows) → decode before embedding to avoid token pollution and broken citations.
- **`Knowledge_Content` is HTML-heavy** → strip tags / convert to clean Markdown before embedding; otherwise vectors encode markup noise.
- **`Knowledge_Content` has 3545 near-duplicates** (402 signature groups) → duplicate suppression needed at ingest.
- **`PublishStatus` is constant** (single value across all rows) → zero retrieval signal. Exclude from the index; keep in source metadata for lineage.
- **`Summary` contains PII patterns** (PHONE=3) → review for redaction before embedding/indexing to avoid leaking PII via vector or BM25 retrieval.
- **`Summary` has 932 near-duplicates** (33 signature groups) → duplicate suppression needed at ingest.
- **`Title` contains PII patterns** (PHONE=51) → review for redaction before embedding/indexing to avoid leaking PII via vector or BM25 retrieval.
- **`Title` has 16 near-duplicates** (16 signature groups) → duplicate suppression needed at ingest.
- **`SFUrlName` contains PII patterns** (PHONE=51) → review for redaction before embedding/indexing to avoid leaking PII via vector or BM25 retrieval.
- **`Sort_Order__c` is entirely empty** → wastes a slot in the schema and confuses content owners. Drop or repurpose.
- **`Business_Unit` appears multi-valued** (delimited tokens) → store as `Collection(Edm.String)` so each value is independently filterable / facetable.
- **`Contact_Type` appears multi-valued** (delimited tokens) → store as `Collection(Edm.String)` so each value is independently filterable / facetable.
- **`System_of_Record` appears multi-valued** (delimited tokens) → store as `Collection(Edm.String)` so each value is independently filterable / facetable.
- **`Product_Family` is near-constant** (>95% one value) → weak facet/filter; consider excluding.
- **`PrivacyFlag` is near-constant** (>95% one value) → weak facet/filter; consider excluding.
- **`is_deleted` is constant** (single value across all rows) → zero retrieval signal. Exclude from the index; keep in source metadata for lineage.
- **`deleted_at` is entirely empty** → wastes a slot in the schema and confuses content owners. Drop or repurpose.
- **`ingested_at` is constant** (single value across all rows) → zero retrieval signal. Exclude from the index; keep in source metadata for lineage.
- **`run_id` is constant** (single value across all rows) → zero retrieval signal. Exclude from the index; keep in source metadata for lineage.
- **`llm_summary` contains PII patterns** (EMAIL=295, PHONE=450) → review for redaction before embedding/indexing to avoid leaking PII via vector or BM25 retrieval.
- **`llm_summary` carries raw HTML entities** (17 rows) → decode before embedding to avoid token pollution and broken citations.
- **`llm_summary` has 1 near-duplicates** (1 signature groups) → duplicate suppression needed at ingest.
- **`llm_faqs` contains PII patterns** (EMAIL=1, PHONE=5) → review for redaction before embedding/indexing to avoid leaking PII via vector or BM25 retrieval.
- **`source_run_id` is constant** (single value across all rows) → zero retrieval signal. Exclude from the index; keep in source metadata for lineage.
- **`prompt_version` is constant** (single value across all rows) → zero retrieval signal. Exclude from the index; keep in source metadata for lineage.
- **`Knowledge_Content_md` contains PII patterns** (EMAIL=627, PHONE=654, IPV4=2, CREDITCARD=2) → review for redaction before embedding/indexing to avoid leaking PII via vector or BM25 retrieval.
- **`Knowledge_Content_md` carries raw HTML entities** (1 rows) → decode before embedding to avoid token pollution and broken citations.
- **`Knowledge_Content_md` has 2177 near-duplicates** (206 signature groups) → duplicate suppression needed at ingest.
- **`content_format` is constant** (single value across all rows) → zero retrieval signal. Exclude from the index; keep in source metadata for lineage.
- **`llm_entities` contains PII patterns** (EMAIL=323, PHONE=373, IPV4=1) → review for redaction before embedding/indexing to avoid leaking PII via vector or BM25 retrieval.

## 5. Content Analysis
| Column | Role | Min len | Median | Mean | P95 | Max | Tokens median | Tokens P95 | Tokens >2K | Tokens >8K | HTML % | Markdown signals % |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `Knowledge_Content` | content | 124 | 5880 | 10964 | 32767 | 32767 |  |  |  |  | 100% | 0% |
| `Summary` | summary | 31 | 74 | 85 | 175 | 455 |  |  |  |  | 0% | 0% |
| `Title` | title | 11 | 61 | 62 | 94 | 208 |  |  |  |  | 0% | 0% |
| `llm_summary` | summary | 92 | 768 | 1026 | 2498 | 32767 |  |  |  |  | 0% | 3% |
| `Knowledge_Content_md` | content | 24 | 1190 | 4646 | 24324 | 32767 |  |  |  |  | 7% | 100% |

**Per-column content recommendations**
- `Knowledge_Content` → **Embed; verify token sizes with a tokenizer before ingestion.**
- `Summary` → **Embed; verify token sizes with a tokenizer before ingestion.**
- `Title` → **Embed as title vector or include in semantic ranker title field.**
- `llm_summary` → **Embed; verify token sizes with a tokenizer before ingestion.**
- `Knowledge_Content_md` → **Embed; verify token sizes with a tokenizer before ingestion.**

**Format signals**
- `Knowledge_Content`: HTML-tagged, 3466 rows with HTML entities
- `llm_summary`: 17 rows with HTML entities
- `Knowledge_Content_md`: Markdown-formatted (tables/lists/headings), 1 rows with HTML entities

## 6. Chunking and Ingestion Readiness
**Current state inferred from the dump**
- Rows look like **full articles, not pre-chunked passages** (single row carries the entire HTML/Markdown body).
- **Stable IDs exist** at the article-version level (`SalesforceID`) and at the article level (`ArticleNumber`), enabling parent-child design.
- **Version histories are real** — multiple versions per article (see `VersionNumber`).
- **No chunk-level IDs yet**; chunking is up to the ingestion pipeline.
- **Repeated metadata**: every chunk should inherit `Title`, `Business_Unit`, `Contact_Type`, `Language`, `LastModifiedDate`, `PrivacyFlag`, parent `ArticleNumber`, `VersionNumber`, and `content_path` (citation URL).

**Recommended chunking strategy**
| Scenario | Target chunk size | Overlap | Notes |
| --- | --- | --- | --- |
| Standard prose | 400–800 tokens | 10–15% | Section-aware; honour Markdown headings.
| FAQ-style content | One Q/A pair per chunk | 0 | Each pair already self-contained.
| Procedural steps | Whole procedure, capped at 1,200 tokens | 0 | Don't split a procedure across chunks.
| Tables | One table per chunk (preserve header) | 0 | Re-emit as Markdown table; include caption.
| Policy / compliance | Section-bounded; include section number | 5–10% | Cite the section in the answer.

**ID & metadata schema for each chunk**
```
chunk_id        = f"{SalesforceID}::s{section_idx:03d}::c{chunk_idx:03d}"
parent_id       = SalesforceID
article_number  = ArticleNumber
version_number  = VersionNumber
section_heading = <nearest Markdown H1/H2>
language        = Language
category        = llm_category
business_unit   = Business_Unit (collection)
contact_type    = Contact_Type (collection)
product_family  = Product_Family
modified_at     = LastModifiedDate
privacy_flag    = PrivacyFlag
source_url      = content_path
title           = Title
summary         = Summary  (or llm_summary)
content         = <clean chunk text>
content_vector  = embed(content)
```

**Versioning**: `ArticleNumber` has up to **2 versions per article** (mean 1.0, **2 articles have >1 version**). At query time, filter to the latest `VersionNumber` per `ArticleNumber` unless the caller asks for history.

## 7. Azure AI Search Schema Recommendations
| Field | Azure type | Key | Search | Filter | Facet | Sort | Retrieve | Vector | Sem. title | Sem. keywords | Sem. content | Sec. trim | Freshness | Score profile | Exclude | Rationale |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `ArticleNumber` | Edm.String |  |  | ✓ |  |  | ✓ |  |  |  |  |  |  |  |  | Stable unique identifier — use as the index `key` (or `filterable` secondary key) and propagate to every chunk for parent-child retrieval. |
| `Attachment_Links__c` | Edm.String |  |  |  |  |  | ✓ |  |  |  |  |  |  |  |  | URL is a citation field — retrievable only; do not tokenize or facet. |
| `Forms_Channel__c` | Edm.String |  |  |  |  |  |  |  |  |  |  |  |  |  | ✓ | Column is entirely empty — drop from index. |
| `Forms_Plan_Number__c` | Edm.String |  |  | ✓ |  |  | ✓ |  |  |  |  |  |  |  |  | General metadata — retrievable; filter only if values are clean. |
| `Forms_Product_Name__c` | Edm.String |  |  |  |  |  |  |  |  |  |  |  |  |  | ✓ | Column is entirely empty — drop from index. |
| `SalesforceID` | Edm.String | ✓ |  | ✓ |  |  | ✓ |  |  |  |  |  |  |  |  | Stable unique identifier — use as the index `key` (or `filterable` secondary key) and propagate to every chunk for parent-child retrieval. |
| `Knowledge_Content` | Edm.String |  | ✓ |  |  |  | ✓ | ✓ |  |  | ✓ |  |  |  |  | Primary article body — searchable, semantic ranker content field, and embedded into the vector index after chunking + cleaning. |
| `Language` | Edm.String |  |  | ✓ | ✓ |  | ✓ |  |  |  |  |  |  |  |  | Language code — filter for per-language indexes/analyzers and language-aware semantic ranking. |
| `VersionNumber` | Edm.Int32 |  |  | ✓ |  | ✓ | ✓ |  |  |  |  |  |  |  |  | Version — filter to latest, sort to disambiguate parent/child chunks. |
| `LastModifiedDate` | Edm.DateTimeOffset |  |  | ✓ |  | ✓ | ✓ |  |  |  |  |  | ✓ | ✓ |  | Date — drives recency boosting via a freshness scoring profile and time-based filters; not searchable text. |
| `PublishStatus` | Edm.String |  |  |  |  |  |  |  |  |  |  |  |  |  | ✓ | Single distinct value provides zero retrieval/filter signal — drop from index. |
| `RecordTypeName__c` | Edm.String |  |  | ✓ | ✓ |  | ✓ |  |  | ✓ |  |  |  | ✓ |  | Category — facet, filter, intent routing, and category-aware boosting. |
| `Summary` | Edm.String |  | ✓ |  |  |  | ✓ | ✓ |  |  | ✓ |  |  |  |  | Summary is high-density content — searchable, included in semantic ranking, and a strong vector field candidate alongside body text. |
| `Title` | Edm.String |  | ✓ |  |  | ✓ | ✓ |  | ✓ |  |  |  |  | ✓ |  | Title carries high-signal terms — boost in scoring profile, set as semantic ranker title field, include in citations. |
| `SFUrlName` | Edm.String |  |  | ✓ |  |  | ✓ |  |  |  |  |  |  |  |  | Stable unique identifier — use as the index `key` (or `filterable` secondary key) and propagate to every chunk for parent-child retrieval. |
| `Sort_Order__c` | Edm.String |  |  |  |  |  |  |  |  |  |  |  |  |  | ✓ | Column is entirely empty — drop from index. |
| `Business_Unit` | Collection(Edm.String) |  |  | ✓ | ✓ |  | ✓ |  |  | ✓ |  |  |  | ✓ |  | Category — facet, filter, intent routing, and category-aware boosting. |
| `Contact_Type` | Collection(Edm.String) |  |  | ✓ | ✓ |  | ✓ |  |  |  |  |  |  | ✓ |  | Persona/audience — drives persona-aware filtering and routing. |
| `Case_Subtype` | Edm.String |  |  | ✓ |  |  | ✓ |  |  |  |  |  |  |  |  | General metadata — retrievable; filter only if values are clean. |
| `System_of_Record` | Collection(Edm.String) |  |  | ✓ | ✓ |  | ✓ |  |  | ✓ |  |  |  | ✓ |  | Category — facet, filter, intent routing, and category-aware boosting. |
| `Case_Type` | Edm.String |  |  | ✓ | ✓ |  | ✓ |  |  | ✓ |  |  |  | ✓ |  | Category — facet, filter, intent routing, and category-aware boosting. |
| `Product_Family` | Edm.String |  | ✓ | ✓ | ✓ |  | ✓ |  |  | ✓ |  |  |  | ✓ |  | Product / plan — high-value filter, facet, and scoring boost; expand acronyms via synonym map. |
| `PrivacyFlag` | Edm.String |  |  | ✓ |  |  |  |  |  |  |  | ✓ |  |  |  | Access flag — drive security trimming at query time; do not surface to end users. |
| `content_path` | Edm.String |  |  |  |  |  | ✓ |  |  |  |  |  |  |  |  | URL is a citation field — retrievable only; do not tokenize or facet. |
| `priorityscore` | Edm.Double |  |  | ✓ |  | ✓ | ✓ |  |  |  |  |  |  | ✓ |  | Numeric metric — sort/filter and weight in scoring profile. |
| `parent_path` | Edm.String |  |  |  |  |  | ✓ |  |  |  |  |  |  |  |  | URL is a citation field — retrievable only; do not tokenize or facet. |
| `is_deleted` | Edm.String |  |  |  |  |  |  |  |  |  |  |  |  |  | ✓ | Single distinct value provides zero retrieval/filter signal — drop from index. |
| `deleted_at` | Edm.String |  |  |  |  |  |  |  |  |  |  |  |  |  | ✓ | Column is entirely empty — drop from index. |
| `ingested_at` | Edm.String |  |  |  |  |  |  |  |  |  |  |  |  |  | ✓ | Single distinct value provides zero retrieval/filter signal — drop from index. |
| `run_id` | Edm.String |  |  |  |  |  |  |  |  |  |  |  |  |  | ✓ | Single distinct value provides zero retrieval/filter signal — drop from index. |
| `llm_summary` | Edm.String |  | ✓ |  |  |  | ✓ | ✓ |  |  | ✓ |  |  |  |  | Summary is high-density content — searchable, included in semantic ranking, and a strong vector field candidate alongside body text. |
| `llm_faqs` | Collection(Edm.String) |  | ✓ | ✓ | ✓ |  | ✓ |  |  | ✓ |  |  |  |  |  | Tag-like field — facet/filter and inject into semantic keyword field; split JSON / delimited values into a collection. |
| `source_run_id` | Edm.String |  |  |  |  |  |  |  |  |  |  |  |  |  | ✓ | Single distinct value provides zero retrieval/filter signal — drop from index. |
| `prompt_version` | Edm.String |  |  |  |  |  |  |  |  |  |  |  |  |  | ✓ | Single distinct value provides zero retrieval/filter signal — drop from index. |
| `summarized_at` | Edm.DateTimeOffset |  |  | ✓ |  | ✓ | ✓ |  |  |  |  |  | ✓ | ✓ |  | Date — drives recency boosting via a freshness scoring profile and time-based filters; not searchable text. |
| `Knowledge_Content_md` | Edm.String |  | ✓ |  |  |  | ✓ | ✓ |  |  | ✓ |  |  |  |  | Primary article body — searchable, semantic ranker content field, and embedded into the vector index after chunking + cleaning. |
| `content_format` | Edm.String |  |  |  |  |  |  |  |  |  |  |  |  |  | ✓ | Single distinct value provides zero retrieval/filter signal — drop from index. |
| `llm_category` | Edm.String |  |  | ✓ | ✓ |  | ✓ |  |  | ✓ |  |  |  | ✓ |  | Category — facet, filter, intent routing, and category-aware boosting. |
| `llm_focus` | Collection(Edm.String) |  | ✓ | ✓ | ✓ |  | ✓ |  |  | ✓ |  |  |  |  |  | Tag-like field — facet/filter and inject into semantic keyword field; split JSON / delimited values into a collection. |
| `llm_entities` | Collection(Edm.String) |  | ✓ | ✓ | ✓ |  | ✓ |  |  | ✓ |  |  |  |  |  | Tag-like field — facet/filter and inject into semantic keyword field; split JSON / delimited values into a collection. |
| `llm_error` | Edm.String |  |  |  |  |  |  |  |  |  |  |  |  |  | ✓ | Operational/tracking field — keep in source-of-truth store for lineage; do not index. |

### 7.1 Required schema essentials
- **Key field**: `SalesforceID` (per-version unique) → top-level `key`. Add a derived `chunk_id` as the chunk-level key when chunking is introduced (then `SalesforceID` becomes a `parent_id` filter).
- **Citation fields**: `Title` + `content_path` (deep-link URL) returned on every result.
- **Security/access**: `PrivacyFlag` (boolean) on every doc; consider a `Collection(Edm.String)` `acl_groups` field bound to identity-provider groups for fine-grained trimming.
- **Freshness/version fields**: `LastModifiedDate` (DateTimeOffset, sortable, used by the freshness scoring function), `VersionNumber` (Int32, filter-to-latest).
- **Content fields**: cleaned chunk text (searchable, vectorised), `Summary` / `llm_summary` (searchable, vectorised, semantic content).
- **Metadata fields**: `Business_Unit`, `Contact_Type`, `Case_Type`, `Case_Subtype`, `Product_Family`, `System_of_Record`, `RecordTypeName__c`, `llm_category`, `llm_focus`, `llm_entities`, `Forms_Plan_Number__c` — all filter/facet candidates.

### 7.2 Scoring profiles
1. **`freshness_profile`** — `freshness` function over `LastModifiedDate` (boostingDuration = P180D, boost = 2.0).
2. **`category_match_profile`** — `tag` function boosting docs whose `llm_category` matches the query-classified intent.
3. **`priority_profile`** — `magnitude` function on `priorityscore` (boost = 1.5).
4. **`persona_profile`** — `tag` function on `Contact_Type` weighted by the caller's persona.

### 7.3 Synonym maps
- **`bu_synonyms`** — expand business-unit acronyms (`GB`, `LB`, `STASC`, `LTD`, …). Attach to `Business_Unit`, `Title`, `content`.
- **`product_synonyms`** — plan/product aliases extracted from `Forms_Plan_Number__c` + common customer phrasing.
- **`role_synonyms`** — collapse persona variants (`Plan_Administrator` ↔ `plan admin` ↔ `PA`).

### 7.4 Analyzers
- Use **`en.microsoft`** (or `en.lucene`) for English content; **`fr.microsoft`** for French content if `Language` shows `fr_*`. Split into per-language indexes or apply a `languageCode` filter.
- Use a custom analyzer for IDs (`keyword` tokenizer) to keep them atomic.

### 7.5 Faceting strategy
- Primary facets: `Business_Unit`, `Product_Family`, `Contact_Type`, `llm_category`, `Language`.
- Secondary facets (revealed on demand): `Case_Type`, `Case_Subtype`, `System_of_Record`, `RecordTypeName__c`, `VersionNumber`.

### 7.6 Filter strategy
- Always filter `PublishStatus eq 'Online' and is_deleted eq false`.
- Filter to the latest version per article unless history is explicitly requested.
- Apply `PrivacyFlag eq 'No'` for unauthenticated callers; honour `acl_groups/any(g: g in caller_groups)` otherwise.
- Optionally scope by `Language eq <bcp47>` based on caller locale.

## 8. RAG Readiness Scorecard
| Dimension | Score | Evidence | Risk if not fixed | Recommendation | Priority |
| --- | --- | --- | --- | --- | --- |
| Content completeness | 5/5 | Content columns: Knowledge_Content, Knowledge_Content_md, Summary, llm_summary; worst null+blank share = 0.5%. | Rows with missing body fall back to title/summary only, weakening grounding. | Backfill body for missing rows or exclude them from the index until repaired. | Medium |
| Content freshness | 4/5 | Date columns: LastModifiedDate, summarized_at. | Stale answers and reduced user trust. | Add an `effective_from` / `effective_to` field and a freshness scoring profile. | Medium |
| Metadata quality | 4/5 | 12 metadata columns; mean null% = 6.9%. | Sparse metadata weakens filters, facets, and scoring boosts. | Fill gaps via rules / LLM enrichment; enforce required fields in source system. | Medium |
| Citation readiness | 5/5 | URL columns: Attachment_Links__c, content_path, parent_path; title: Title. | Ungrounded or untraceable answers. | Use the URL + title pair as the citation contract in the chat UI. | Low |
| Searchability | 5/5 | Searchable text columns: Knowledge_Content, Summary, Title, llm_summary, Knowledge_Content_md. | Hybrid search degrades to vector-only without strong BM25 signal. | Map title/summary/content to `searchable` with the language-specific analyzer. | Low |
| Filterability | 5/5 | 10 filterable fields: Language, VersionNumber, RecordTypeName__c, Business_Unit, Contact_Type, System_of_Record, Case_Type, Product_Family, PrivacyFlag, llm_category. | Cannot scope retrieval by product, persona, or language at query time. | Make these fields `filterable=true`; collection-ify multi-value fields. | Medium |
| Facetability | 4/5 | 8 facet candidates. | Limited UI affordances for users to refine results. | Enable `facetable=true` after normalization (lowercase, trim, synonym map). | Low |
| Chunking quality | 2/5 | Token stats unavailable — install `tiktoken`. | Over-long chunks dilute embeddings; under-long chunks lose context. | Enable tokenizer; profile token-length distribution. | High |
| Embedding readiness | 4/5 | Content + summary columns are available for embedding. | Vector recall degrades without high-density embedding inputs. | Embed `Summary` + chunked content; consider hybrid title-enhanced vectors. | Medium |
| Semantic ranking readiness | 5/5 | Title=True; keywords=True; content=True. | Weak semantic config blunts the reranker's gains. | Configure the semantic configuration with title / keywords / content fields. | Medium |
| Hybrid search readiness | 4/5 | Required BM25 + vector inputs available. | Hybrid fusion underperforms vector-only. | Run hybrid (vector + keyword) with RRF and tune `k` per query class. | Medium |
| Access control / security trimming | 4/5 | Access columns: PrivacyFlag. | Risk of leaking restricted content to unauthorized users. | Bind access field to `searchFields=acl` filter; integrate with identity provider groups. | Medium |
| Multilingual readiness | 3/5 | Multiple languages detected: {'en_US': 3013, 'fr': 1854}. | Cross-language recall failures. | Split per-language analyzers, run per-language semantic ranker, translate or detect query language. | Medium |
| Evaluation readiness | 3/5 | FAQ/Q&A column present — seeds a golden set. | Cannot quantify retrieval/answer quality regressions. | Bootstrap a golden set from LLM-generated FAQs + human review; track Recall@K and groundedness. | High |
| Observability readiness | 4/5 | Tracking columns: llm_error. | Hard to root-cause bad answers if you cannot trace ingestion lineage. | Persist tracking fields out-of-band (App Insights / Log Analytics), not in the search index. | Medium |
| Governance / compliance | 4/5 | Owner=False, access=True, dates=True. | Unclear ownership and lifecycle controls. | Add owner, reviewer, effective_from/to, approval status; bind to a review SLA. | Medium |

## 9. Retrieval Improvement Opportunities
| Opportunity | Problem | Supporting fields | Implementation | Expected impact | Complexity | Priority | Azure feature / RAG technique |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Recency boost via freshness scoring profile | Older versions out-rank current ones for the same query. | `LastModifiedDate`, `summarized_at`, `VersionNumber` | Define a `scoringProfile` with a `freshness` function over the modified-date field; weight by months since last update. | Lower stale-answer rate; better trust on policy/procedural content. | Low | High | Azure AI Search scoring profile (freshness function) |
| Category / product / persona boosting + filtering | All articles compete uniformly — irrelevant business units leak into results. | `RecordTypeName__c`, `Business_Unit`, `System_of_Record`, `Case_Type`, `llm_category` · `Product_Family` · `Contact_Type` | Make these `filterable=true, facetable=true`. Add a `tag` scoring function that boosts categories matching the inferred query intent. | Higher precision; cleaner facet UI. | Low | High | Azure AI Search filters + scoringProfile.tag function |
| Synonym maps for acronyms and product aliases | User queries use short forms (e.g. plan codes, business-unit acronyms) that don't match indexed text. | `Product_Family` · `RecordTypeName__c`, `Business_Unit`, `System_of_Record`, `Case_Type`, `llm_category` | Create a `synonymMap` resource and attach it to the title/content/category fields. | Better BM25 recall on abbreviated queries. | Low | High | Azure AI Search synonym maps |
| Semantic configuration with explicit title/keyword/content fields | Default ranking ignores high-signal fields. | `Title` · `RecordTypeName__c`, `Business_Unit`, `System_of_Record`, `Case_Type`, `llm_category` · `Knowledge_Content`, `Knowledge_Content_md` | Add a `semanticConfiguration` mapping title → semantic title, category/tag → keywords, content/summary → content. | Significant nDCG lift from the semantic ranker. | Low | High | Azure AI Search semantic ranker |
| Hybrid retrieval with RRF + chunk-level vectors | Vector-only retrieval misses keyword-specific hits; BM25-only misses paraphrases. | `Knowledge_Content`, `Knowledge_Content_md` · `Summary`, `llm_summary` | Run vector search + BM25 in parallel, combine via RRF, then semantic rerank top 50. | Best-in-class recall + precision for enterprise RAG. | Medium | High | Azure AI Search hybrid + RRF + semantic reranker |
| Query rewrite & HyDE for sparse retrieval | Short user queries don't surface the right chunks. | `Summary`, `llm_summary` · `llm_faqs`, `llm_focus`, `llm_entities` | LLM rewrites the query, optionally generates a hypothetical answer (HyDE), and embeds both for vector search. | Better recall on under-specified queries. | Medium | Medium | RAG technique — query rewrite / HyDE |
| Parent–child retrieval (chunk → parent article) | Returned chunks lack surrounding context, hurting grounding. | `ArticleNumber`, `SalesforceID`, `SFUrlName` · `Title` | Index chunk-level rows that carry the parent article ID; at answer time stitch sibling chunks back together for the LLM context window. | Better grounded, more coherent answers. | Medium | High | RAG technique — small-to-big / parent-child |
| Duplicate / near-duplicate suppression | Multi-version articles fight for the same slot. | `ArticleNumber`, `SalesforceID`, `SFUrlName` · `VersionNumber` | Keep only the latest version per article ID (filter at query time) or hash-dedupe chunks at ingest. | Higher diversity in the top-K; less stale content. | Low | High | Filter + ingest-time dedupe |
| LLM reranking on the top 20 | Even after semantic reranking, the top result is not always the best answer. | `Knowledge_Content`, `Knowledge_Content_md` · `Summary`, `llm_summary` | Pass top 20 hits to a cheap reranker LLM with a relevance scoring prompt; reorder before answer synthesis. | Higher precision@1, fewer hallucinations. | Medium | Medium | RAG technique — LLM reranker |
| Access-control-aware retrieval (security trimming) | Restricted articles could surface to unauthorized users. | `PrivacyFlag` | Add ACL field to every doc; pass the user's group memberships in the `filter` clause. | Compliance + reduced data leakage risk. | Medium | High | Azure AI Search security trimming |
| Multi-query expansion for ambiguous user intent | User queries map to multiple plausible knowledge areas. | `RecordTypeName__c`, `Business_Unit`, `System_of_Record`, `Case_Type`, `llm_category` · _(none detected)_ | Generate N query variants (LLM), retrieve for each, then RRF the union. | Higher recall, modest cost increase. | Medium | Medium | RAG technique — multi-query retrieval |
| Intent / persona routing | Same query means different things for different personas. | `Contact_Type` · _(none detected)_ | Classify query → persona/intent; apply a persona filter and persona-specific scoring profile. | Higher precision per audience. | Medium | Medium | Persona-aware retrieval |
| Contextual compression before LLM grounding | Long context inflates cost and dilutes grounding. | `Knowledge_Content`, `Knowledge_Content_md` | Sentence-level compress top-K hits with a small LLM before passing to the answer model. | Lower token cost, sharper grounding. | Medium | Medium | RAG technique — contextual compression |
| Content canonicalization (HTML → clean text) | HTML markup, stripped tables, and entities pollute embeddings and BM25 tokens. | `Knowledge_Content`, `Knowledge_Content_md` | Pre-process: strip HTML, normalize whitespace, decode entities, repair stripped tables; index the clean Markdown variant. | Cleaner tokens, better vector quality, simpler citations. | Low | High | Ingestion-time preprocessing |
| Evaluation-driven tuning (golden set + A/B) | No measurable proof that changes improve retrieval. | `llm_faqs`, `llm_focus`, `llm_entities` · `Title` · `Attachment_Links__c`, `content_path`, `parent_path` | Curate 200–500 golden queries; track Recall@K, MRR, faithfulness; A/B test scoring/semantic configs. | Confidence in every tuning change. | Medium | High | Evaluation framework |

## 10. Advanced RAG Technique Recommendations
| Technique | Applicable? | Evidence from data | Implementation | Evaluation | Risks / costs |
| --- | --- | --- | --- | --- | --- |
| Hybrid retrieval tuning (RRF weights, k) | Yes | Content + title + tags are all present, enabling both vector and BM25 channels. | Sweep RRF `k` (10–60); per-intent weights; evaluate against golden set. | Compare Recall@K and nDCG vs. vector-only baseline. | Over-fitting to a small eval set. |
| Semantic reranking (Azure built-in) | Yes | Title and content fields exist — perfect inputs for the semantic ranker. | Add a semantic configuration; query with `queryType=semantic`. | Track @1/@5 precision improvement. | Latency add; quota limits. |
| Query rewrite | Yes | Short user queries are the norm; titles are descriptive. | LLM-rewrite step before retrieval; cache rewrites per query. | Compare answer-faithfulness scores before/after. | Rewrite drift; cost. |
| Multi-query retrieval | Yes | Multi-business-unit dataset → many synonymous phrasings. | Generate 3–5 variants; retrieve & RRF. | Recall@K vs. baseline. | Higher cost and latency. |
| HyDE (hypothetical document embeddings) | Yes | Summary column already approximates answer-shaped text. | LLM drafts a hypothetical answer; embed and retrieve. | Recall@K vs. baseline on under-specified queries. | Hallucinated drafts can mis-direct retrieval. |
| Step-back prompting | Conditional | Useful when category hierarchy exists. | LLM first asks a broader question, then narrows; combine results. | Manual review on complex multi-hop queries. | Slower; extra LLM calls. |
| Metadata-aware retrieval | Yes | Strong metadata: Language, RecordTypeName__c, Business_Unit, Contact_Type, System_of_Record, Case_Type, Product_Family, llm_category. | Extract filters from user query (LLM or rules) and apply them. | Precision lift on filtered queries. | Over-filtering can starve recall. |
| Parent–child / small-to-big retrieval | Yes | Article ↔ version relationships detected: [{'article_col': 'ArticleNumber', 'version_col': 'VersionNumber', 'articles_with_multiple_versions': 2, 'max_versions_per_article': 2, 'mean_versions_per_article': 1.0}]. | Index small chunks; fetch parent article context at answer time. | Faithfulness and answer completeness. | Larger LLM context cost. |
| Late chunking | Conditional | Useful where article boundaries are clean but section boundaries arbitrary. | Embed entire document first, then aggregate token-level vectors into chunk vectors. | Compare vs. naive chunking on long-document queries. | Higher embedding compute. |
| Contextual retrieval (Anthropic-style situated chunks) | Yes | Multi-version articles benefit from explicit context per chunk. | Prepend an LLM-generated short context summary to each chunk before embedding. | Recall@K on long-tail queries. | One-time enrichment cost. |
| Contextual compression | Yes | Long content rows present. | Sentence-rank or LLM-summarize top hits before answer generation. | Cost per answer + faithfulness. | Information loss. |
| LLM reranking | Yes | Hybrid + semantic still leaves room for reordering top 20. | Cheap LLM scores top 20; reorder. | Precision@1, latency. | Cost; deterministic-rerank prompts required. |
| RRF tuning | Yes | Hybrid retrieval enabled. | Grid-search `k`; evaluate per intent class. | Recall@K, MRR. | Minor. |
| GraphRAG / knowledge graph | Conditional | Entity column present (LLM-extracted entities). | Build an entity graph from `llm_entities`; use graph hops for multi-hop questions. | Multi-hop QA accuracy. | Heavy infra investment. |
| Agentic retrieval | Yes | Rich metadata supports tool routing. | Orchestrator agent picks: filter, retrieve, escalate. | Task success rate. | Complexity, latency. |
| Feedback-based retrieval optimization | Yes | Production chatbot can capture thumbs up/down. | Log per-query rankings; fine-tune scoring profiles or rerankers. | Long-term CSAT. | Reinforcement of bias. |
| Golden-set evaluation | Yes | LLM-generated FAQs seed candidates. | Curate 200–500 queries with expected article IDs; CI-run weekly. | Recall@K, MRR, nDCG. | Set drift over time. |
| Synthetic Q/A generation for eval | Yes | FAQ column already populated by LLM. | Cluster + de-dup synthetic Qs; human-review a subset. | Manual relevance audit. | LLM bias in question phrasing. |
| Embedding model comparison | Yes | Multiple content modalities (HTML, Markdown). | A/B `text-embedding-3-large` vs. `text-embedding-3-small` on golden set. | Recall@K vs. cost. | Re-indexing cost. |
| Reranker model comparison | Yes | Multiple rerankers available (semantic, cross-encoder, LLM). | A/B test on golden set; pick best cost/quality point. | nDCG, latency. | Quota. |
| Prompt-injection detection in retrieved content | Yes | Articles include user-authored / customer-facing text. | Scanner pass over retrieved chunks before LLM input. | Red-team prompts. | False positives suppressing legitimate content. |
| Freshness / lifecycle management | Yes | Date columns: ['LastModifiedDate', 'summarized_at']. | Set effective_from/effective_to; soft-delete stale; weekly recency report. | Stale-answer rate. | Operational overhead. |

## 11. Evaluation Framework
### 11.1 Source fields that seed evaluation
| Eval need | Source column(s) | Notes |
| --- | --- | --- |
| Ground-truth questions | `llm_faqs` | Parse JSON array; each entry → candidate query.
| Expected answers | `Knowledge_Content_md` + `Summary` | Use as the gold passage for faithfulness/groundedness scoring.
| Article titles | `Title` | Use in test display & for diagnosing rank issues.
| Article categories | `llm_category`, `Business_Unit`, `Product_Family`, `Contact_Type` | Stratify the golden set across these dimensions.
| Source URLs | `content_path`, `parent_path` | Citation accuracy.
| Article IDs | `SalesforceID`, `ArticleNumber` | Recall@K / Hit-rate targets.
| Effective dates | `LastModifiedDate`, `summarized_at`, `ingested_at` | Freshness / version eval.
| Tags / entities | `llm_focus`, `llm_entities` | Entity-level eval; rerank diagnostics.
| Personas | `Contact_Type` | Persona-stratified eval.
| Products | `Product_Family`, `Forms_Plan_Number__c` | Product-stratified eval.
| Regions / language | `Language` | Language slice metrics.
| Access groups | `PrivacyFlag` | Security trimming eval.

### 11.2 Golden set design
1. **Seed**: Sample 1 FAQ per article across `llm_category × Business_Unit × Contact_Type` strata → ~500 candidate queries.
2. **Curate**: Human review for naturalness, intent clarity, and answerability.
3. **Label**: For each query, store `expected_article_ids`, `expected_chunk_ids` (after chunking), `expected_url`, and `intent_class`.
4. **Hard negatives**: For each query, sample 5 articles from a sibling category as negatives.
5. **Refresh cadence**: Quarterly review; auto-add queries flagged by production thumbs-down.

### 11.3 Metrics
- **Retrieval**: Recall@1/5/10, Precision@K, MRR, nDCG@10, Hit-rate@K.
- **Answer quality**: faithfulness, groundedness, citation accuracy, context relevance, answer completeness.
- **Ops**: latency P50/P95, cost per query, escalation accuracy, no-answer accuracy, thumbs-up / down ratio.

### 11.4 Sample evaluation plan
1. **Weekly CI run** of the golden set against staging index. Block release on Recall@5 < baseline − 2%.
2. **Per-PR smoke tests** (50 queries) on every retrieval/prompt change.
3. **Monthly faithfulness audit** — sample 100 prod traces, human-review for citation accuracy.
4. **A/B tests** for each retrieval change (RRF weight, semantic config, reranker, embedding model) split by query intent.
5. **Persistent dashboards** in Application Insights / Log Analytics for the ops metrics.

## 12. Governance and Security Considerations
| Concern | Finding / Recommendation |
| --- | --- |
| Potential PII / sensitive fields | `Knowledge_Content` ({'EMAIL': 521, 'PHONE': 552, 'IPV4': 2, 'CREDITCARD': 2}), `Summary` ({'PHONE': 3}), `Title` ({'PHONE': 51}), `SFUrlName` ({'PHONE': 51}), `llm_summary` ({'EMAIL': 295, 'PHONE': 450}), `llm_faqs` ({'EMAIL': 1, 'PHONE': 5}), `Knowledge_Content_md` ({'EMAIL': 627, 'PHONE': 654, 'IPV4': 2, 'CREDITCARD': 2}), `llm_entities` ({'EMAIL': 323, 'PHONE': 373, 'IPV4': 1}) |
| Internal-only content flags | `PrivacyFlag` is binary today — too coarse; layer a fine-grained ACL collection on top. |
| Role-based access requirements | Drive from `Contact_Type` + identity-provider groups; surface only the personas the caller is entitled to. |
| Security trimming | Apply ACL filter at every query; deny-by-default for missing ACL. |
| Compliance / legal | Group benefits / insurance content is regulated — keep an audit log of which article version answered each query. |
| Outdated / versioned content | `VersionNumber` + `LastModifiedDate` exist; surface only the latest version per `ArticleNumber` unless caller opts in to history. |
| Content ownership gaps | No owner / author column detected — add `OwnerEmail` and `LastReviewedBy`. |
| Source / citation fields | `content_path` + `Title` cover citations; ensure every answer carries them. |
| Audit fields | Add `created_at`, `last_reviewed_at`, `next_review_due_at`, `approval_state`. |
| Effective / expiry dates | Add `effective_from`, `effective_to`; auto-archive on expiry. |
| Approval workflow | Add an `approval_state` (draft/approved/expired) and only index `approved`. |
| Confidence / authority | Consider an `authority_score` derived from owner team + review recency. |

**Recommended governance model**
- **Source of truth**: Salesforce Knowledge (existing).
- **Ingestion gates**: schema validation, PII scan, HTML sanitization, language detection, approval-state check.
- **Stewardship**: each `Business_Unit` owns its articles, with a named reviewer; SLA = review every 180 days.
- **Lifecycle**: draft → approved → in-index → expired (auto-archive based on `effective_to` + freshness signal).
- **Audit**: log every ingestion, every retrieval, every escalation; tie back to article + version IDs.

## 13. Prioritized Action Plan
| # | Action | Why now | Owner | Effort | Impact |
| --- | --- | --- | --- | --- | --- |
| 1 | Drop empty + constant columns from the index schema | Reduces noise, simplifies maintenance | Data eng | XS | M |
| 2 | Convert HTML body → clean Markdown before embedding | HTML pollutes embeddings + tokens | Data eng | S | H |
| 3 | Implement chunking (400–800 tokens, 10–15% overlap, heading-aware) | Long articles exceed retrieval sweet spot | Data eng | M | H |
| 4 | Add a freshness scoring profile on `LastModifiedDate` | Stale answers visibly hurt trust | Search eng | S | H |
| 5 | Configure semantic configuration (title + keywords + content) | Largest single quality lever | Search eng | S | H |
| 6 | Bind `PrivacyFlag` + ACL filter on every query | Compliance risk | Security + Search eng | M | H |
| 7 | Curate a 300-query golden set from `llm_faqs` | No measurable improvement without it | App AI lead | M | H |
| 8 | Filter to latest `VersionNumber` per `ArticleNumber` at query time | Duplicate-suppression | Search eng | XS | M |
| 9 | Add synonym maps for business-unit and product acronyms | Customers use short forms | Content + Search | S | M |
| 10 | Stand up evaluation dashboard (Recall@K, faithfulness) | Drives every other change | App AI lead | M | H |

## 14. Quick Wins (≤ 1 sprint)
- Drop empty/constant columns; rename `__c` Salesforce columns to clean snake_case for the index.
- HTML strip + entity decode in the ingestion job; index Markdown.
- Semantic configuration + freshness scoring profile.
- `latest-version-only` query filter.
- Acronym synonym map for the top 50 business-unit / product abbreviations.
- Wire ApplicationInsights traces with `article_id`, `chunk_id`, `query_id` for every retrieval.

## 15. Medium-Term Improvements (1–2 quarters)
- Heading-aware chunking + parent-child retrieval.
- LLM reranker on top 20.
- Query rewrite + multi-query expansion behind a feature flag.
- Persona-aware filtering driven by caller identity → `Contact_Type`.
- Fine-grained ACL collection (replace binary `PrivacyFlag`).
- Golden-set CI in the deployment pipeline; A/B retrieval configs.
- Boilerplate detection and removal at ingest.

## 16. Long-Term Strategic Enhancements (2–4 quarters)
- GraphRAG layered on `llm_entities` for multi-hop questions.
- Agentic orchestrator with tool routing (retrieve / escalate / form-fill).
- Contextual retrieval (situated chunks) — prepend article context to every chunk before embedding.
- Embedding-model A/B with quarterly refresh.
- Prompt-injection scanner on retrieved chunks.
- Feedback-loop fine-tuning of scoring profiles + rerankers.
- Knowledge-graph-backed authoring assistant for content owners.

## 17. Open Questions for Business / Product / Content Owners
1. Who is the authoritative **content owner** per `Business_Unit` and what is the review cadence?
2. Should the chatbot ever answer from **draft / unpublished** articles? (Currently `PublishStatus` is uniformly `Online`.)
3. What is the **canonical ACL model** — does `PrivacyFlag` map to identity groups today?
4. Are the **LLM-generated `llm_summary` / `llm_faqs` / `llm_category`** considered authoritative, or only for retrieval enrichment? (Affects citation behavior.)
5. Should historical **`VersionNumber` > 1** be retrievable, or hidden from end users entirely?
6. What is the **language coverage commitment** — English only, or English + French (and which regions)?
7. What is the **escalation path** when the bot has no grounded answer — live agent, form, KB article suggestion?
8. Are **`Attachment_Links__c`** (HTML blob with links) intended to be surfaced as downloadable references?
9. Are **`Forms_Plan_Number__c`** values customer-visible identifiers or internal codes? Affects synonym strategy.
10. What is the **target answer latency P95** and **per-query cost budget** for the production chatbot? Drives reranker + multi-query decisions.

## 18. Appendix — Per-Column Profiling

### `ArticleNumber`
- **Role**: `key` — _name matches `key` hint_
- **Type**: `int64`
- **Rows**: 4,867; non-null = 4,867 (100.00%)
- **Nulls**: 0 (0.0%); blanks=0; whitespace-only=0
- **Unique values**: 3,028
- **String length**: min=4, median=6, mean=6, p95=6, max=6
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=0%
- **Top values**:
  - `7415` × 2
  - `7419` × 2
  - `7421` × 2
  - `7422` × 2
  - `7426` × 2
- **Examples**: `7415` · `7415` · `7419`
- **Embedding recommendation**: Do not embed; use as metadata only.
- **Schema**: type=`Edm.String`, flags=[filterable retrievable ]; rationale: Stable unique identifier — use as the index `key` (or `filterable` secondary key) and propagate to every chunk for parent-child retrieval.

### `Attachment_Links__c`
- **Role**: `url` — _name matches `url` hint, majority of values are URLs_
- **Type**: `object`
- **Rows**: 4,867; non-null = 112 (2.30%)
- **Nulls**: 4,755 (97.7%); blanks=0; whitespace-only=0
- **Unique values**: 88
- **String length**: min=9, median=773, mean=1865, p95=5227, max=22428
- **Shape signals**: URL=67%, HTML=100%, Markdown=0%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=66%
- **Other**: 23 value(s) contain HTML entities
- **Top values**:
  - `<b style="box-sizing: border-box;font-weight: var(--lwc-fontWeightBold,700);col…` × 8
  - `<a href="https://mlc.my.salesforce.com/sfc/p/U0000000Lj7j/a/0A0000000Tw3/ht9UwX…` × 3
  - `<div>​​​​​​</div>` × 2
  - `<span style="font-size: 12px;"><span style="font-family: Arial,Helvetica,sans-s…` × 2
  - `<span style="font-size: 12px;"><span style="font-family: Arial,Helvetica,sans-s…` × 2
- **Examples**: `<div style="box-sizing: border-box;color: rgb(8, 7, 7);font-family: &quot;Sales…` · `<span style="font-size: 12px;"><span style="font-family: Arial,Helvetica,sans-s…` · `<div>​​​​​​</div>`
- **Embedding recommendation**: Do not embed; use as metadata only.
- **Schema**: type=`Edm.String`, flags=[retrievable ]; rationale: URL is a citation field — retrievable only; do not tokenize or facet.

### `Forms_Channel__c`
- **Role**: `empty` — _column is empty_
- **Type**: `float64`
- **Rows**: 4,867; non-null = 0 (0.00%)
- **Nulls**: 4,867 (100.0%); blanks=0; whitespace-only=0
- **Unique values**: 0
- **String length**: min=0, median=0, mean=0, p95=0, max=0
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=0%
- **Embedding recommendation**: Do not embed; use as metadata only.
- **Schema**: type=`Edm.String`, flags=[exclude]; rationale: Column is entirely empty — drop from index.

### `Forms_Plan_Number__c`
- **Role**: `metadata` — __
- **Type**: `object`
- **Rows**: 4,867; non-null = 3,730 (76.64%)
- **Nulls**: 1,137 (23.36%); blanks=0; whitespace-only=0
- **Unique values**: 1,508
- **String length**: min=5, median=7, mean=23, p95=43, max=10799
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=39%
- **Top values**:
  - `H0WT0` × 538
  - `$ystems&Workflow` × 328
  - `P15900, P15901, P15902, P134065, P147819` × 8
  - `P788000, P788021, P788022, P788023, P788026, P788027, P788030, P788031, P788032…` × 4
  - `P79373` × 4
- **Examples**: `P113219, P115492, P133548` · `P113219, P115492, P133548` · `$ystems&Workflow`
- **Embedding recommendation**: Do not embed; use as metadata only.
- **Schema**: type=`Edm.String`, flags=[filterable retrievable ]; rationale: General metadata — retrievable; filter only if values are clean.

### `Forms_Product_Name__c`
- **Role**: `empty` — _name matches `product` hint, column is empty_
- **Type**: `float64`
- **Rows**: 4,867; non-null = 0 (0.00%)
- **Nulls**: 4,867 (100.0%); blanks=0; whitespace-only=0
- **Unique values**: 0
- **String length**: min=0, median=0, mean=0, p95=0, max=0
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=0%
- **Embedding recommendation**: Do not embed; use as metadata only.
- **Schema**: type=`Edm.String`, flags=[exclude]; rationale: Column is entirely empty — drop from index.

### `SalesforceID`
- **Role**: `key` — _name matches `key` hint, values match ID pattern_
- **Type**: `object`
- **Rows**: 4,867; non-null = 4,867 (100.00%)
- **Nulls**: 0 (0.0%); blanks=0; whitespace-only=0
- **Unique values**: 4,867
- **String length**: min=18, median=18, mean=18, p95=18, max=18
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=0%, Date=0%, SalesforceID=100%, Multi-value=0%
- **Top values**:
  - `ka5OM000000hugTYAQ` × 1
  - `ka5OM000000hui5YAA` × 1
  - `ka5OM0000012deDYAQ` × 1
  - `ka5OM0000012dfpYAA` × 1
  - `ka5OM0000018MfVYAU` × 1
- **Examples**: `ka5OM000000hugTYAQ` · `ka5OM000000hui5YAA` · `ka5OM0000012deDYAQ`
- **Embedding recommendation**: Do not embed; use as metadata only.
- **Schema**: type=`Edm.String`, flags=[key filterable retrievable ]; rationale: Stable unique identifier — use as the index `key` (or `filterable` secondary key) and propagate to every chunk for parent-child retrieval.

### `Knowledge_Content`
- **Role**: `content` — _name matches `content` hint, long-form content_
- **Type**: `object`
- **Rows**: 4,867; non-null = 4,862 (99.90%)
- **Nulls**: 5 (0.1%); blanks=0; whitespace-only=0
- **Unique values**: 4,705
- **String length**: min=124, median=5880, mean=10964, p95=32767, max=32767
- **Shape signals**: URL=47%, HTML=100%, Markdown=0%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=83%
- **PII pattern hits**: {'EMAIL': 521, 'PHONE': 552, 'IPV4': 2, 'CREDITCARD': 2}
- **Other**: 3466 value(s) contain HTML entities; PII pattern hits: EMAIL=521, PHONE=552, IPV4=2, CREDITCARD=2
- **Top values**:
  - `<table style="box-sizing: border-box;border-collapse: collapse;width: 100%;over…` × 11
  - `<table border="1" style="width: 100.02%;border-width: 1px;"><tbody><tr><td cols…` × 11
  - `<table border="1" style="width: 100%;border-width: 1px;height: 209.521px;"><tbo…` × 10
  - `<table style="box-sizing: border-box;border-collapse: collapse;width: 100%;over…` × 8
  - `<table border="1" style="width: 100%;"><tbody><tr><td colspan="1" rowspan="1" s…` × 7
- **Examples**: `<table border="1" style="width: 100.02%;border-width: 1px;"><tbody><tr><td cols…` · `<table border="1" style="width: 100.02%;border-width: 1px;"><tbody><tr><td cols…` · `<table border="1" style="width: 100%;"><tbody><tr><td colspan="1" rowspan="1" s…`
- **Embedding recommendation**: Embed; verify token sizes with a tokenizer before ingestion.
- **Schema**: type=`Edm.String`, flags=[searchable retrievable vector ]; rationale: Primary article body — searchable, semantic ranker content field, and embedded into the vector index after chunking + cleaning.

### `Language`
- **Role**: `language` — _name matches `language` hint, low-cardinality short tokens_
- **Type**: `object`
- **Rows**: 4,867; non-null = 4,867 (100.00%)
- **Nulls**: 0 (0.0%); blanks=0; whitespace-only=0
- **Unique values**: 2
- **String length**: min=2, median=5, mean=4, p95=5, max=5
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=0%
- **Top values**:
  - `en_US` × 3,013
  - `fr` × 1,854
- **Examples**: `en_US` · `fr` · `en_US`
- **Embedding recommendation**: Do not embed; use as metadata only.
- **Schema**: type=`Edm.String`, flags=[filterable facetable retrievable ]; rationale: Language code — filter for per-language indexes/analyzers and language-aware semantic ranking.

### `VersionNumber`
- **Role**: `version` — _name matches `version` hint_
- **Type**: `int64`
- **Rows**: 4,867; non-null = 4,867 (100.00%)
- **Nulls**: 0 (0.0%); blanks=0; whitespace-only=0
- **Unique values**: 77
- **String length**: min=1, median=1, mean=1, p95=2, max=3
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=0%
- **Top values**:
  - `1` × 1,961
  - `2` × 453
  - `3` × 345
  - `4` × 304
  - `5` × 207
- **Examples**: `5` · `5` · `7`
- **Embedding recommendation**: Do not embed; use as metadata only.
- **Schema**: type=`Edm.Int32`, flags=[filterable sortable retrievable ]; rationale: Version — filter to latest, sort to disambiguate parent/child chunks.

### `LastModifiedDate`
- **Role**: `date` — _name matches `date` hint, majority of values are dates/timestamps_
- **Type**: `datetime64[ns]`
- **Rows**: 4,867; non-null = 4,867 (100.00%)
- **Nulls**: 0 (0.0%); blanks=0; whitespace-only=0
- **Unique values**: 2,952
- **String length**: min=19, median=19, mean=19, p95=19, max=19
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=0%, Date=100%, SalesforceID=0%, Multi-value=0%
- **Top values**:
  - `2025-06-09 12:23:31` × 72
  - `2024-11-06 18:10:36` × 53
  - `2025-06-16 20:37:56` × 43
  - `2024-11-06 18:08:00` × 43
  - `2025-06-09 12:24:09` × 40
- **Examples**: `2025-06-10 14:18:41` · `2025-06-10 14:18:41` · `2026-03-11 15:12:05`
- **Embedding recommendation**: Do not embed; use as metadata only.
- **Schema**: type=`Edm.DateTimeOffset`, flags=[filterable sortable retrievable freshness score-prof ]; rationale: Date — drives recency boosting via a freshness scoring profile and time-based filters; not searchable text.

### `PublishStatus`
- **Role**: `constant` — _name matches `status` hint, single unique value_
- **Type**: `object`
- **Rows**: 4,867; non-null = 4,867 (100.00%)
- **Nulls**: 0 (0.0%); blanks=0; whitespace-only=0
- **Unique values**: 1
- **String length**: min=6, median=6, mean=6, p95=6, max=6
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=0%
- **Top values**:
  - `Online` × 4,867
- **Examples**: `Online` · `Online` · `Online`
- **Embedding recommendation**: Do not embed; use as metadata only.
- **Schema**: type=`Edm.String`, flags=[exclude]; rationale: Single distinct value provides zero retrieval/filter signal — drop from index.

### `RecordTypeName__c`
- **Role**: `category` — _name matches `category` hint, low-cardinality short tokens_
- **Type**: `object`
- **Rows**: 4,867; non-null = 4,867 (100.00%)
- **Nulls**: 0 (0.0%); blanks=0; whitespace-only=0
- **Unique values**: 9
- **String length**: min=3, median=9, mean=9, p95=9, max=21
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=0%
- **Top values**:
  - `Procedure` × 3,828
  - `Twistie` × 846
  - `Process_Guidance` × 100
  - `Corp_Info_News_Events` × 62
  - `Tab` × 25
- **Examples**: `Procedure` · `Procedure` · `Procedure`
- **Embedding recommendation**: Do not embed; use as metadata only.
- **Schema**: type=`Edm.String`, flags=[filterable facetable retrievable score-prof ]; rationale: Category — facet, filter, intent routing, and category-aware boosting.

### `Summary`
- **Role**: `summary` — _name matches `summary` hint_
- **Type**: `object`
- **Rows**: 4,867; non-null = 4,842 (99.49%)
- **Nulls**: 25 (0.51%); blanks=0; whitespace-only=0
- **Unique values**: 3,914
- **String length**: min=31, median=74, mean=85, p95=175, max=455
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=5%
- **PII pattern hits**: {'PHONE': 3}
- **Other**: PII pattern hits: PHONE=3
- **Top values**:
  - `Claims - Health Claims - Health Claims - Health` × 805
  - `This article highlights all Agent Desktop documentation updates and projects th…` × 41
  - `Claims - Health Claims - Health` × 32
  - `Claims - Health - French Claims - Health - French` × 24
  - `Il existe cinq articles pour la couverture des soins dentaires dans le cadre de…` × 3
- **Examples**: `This article  outlines the specific details for Liteline Corporation` · `Cet article décrit les détails spécifiques à Liteline Corporation` · `This article outlines how to handle returned mail from Canada Post.`
- **Embedding recommendation**: Embed; verify token sizes with a tokenizer before ingestion.
- **Schema**: type=`Edm.String`, flags=[searchable retrievable vector ]; rationale: Summary is high-density content — searchable, included in semantic ranking, and a strong vector field candidate alongside body text.

### `Title`
- **Role**: `title` — _name matches `title` hint_
- **Type**: `object`
- **Rows**: 4,867; non-null = 4,867 (100.00%)
- **Nulls**: 0 (0.0%); blanks=0; whitespace-only=0
- **Unique values**: 4,853
- **String length**: min=11, median=61, mean=62, p95=94, max=208
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=20%
- **PII pattern hits**: {'PHONE': 51}
- **Other**: PII pattern hits: PHONE=51
- **Top values**:
  - `MULTI BU CSC Accessibility - Managing or Handling Inquiries` × 2
  - `MULTI BU CSC Audit Dispute Procedure` × 2
  - `GB CSC Workflow: Troubleshoot SSO Logins` × 2
  - `PS Notes V3 - All - Manulife Staff Plan` × 2
  - `PS Notes V3 - Plan Number 79373 Extreme Excavating Ltd.` × 2
- **Examples**: `PS Notes V3 - Plan Number 113219 Liteline Corporation` · `PS Notes V3 - Régime Numéro 113219 Liteline Corporation` · `GB CSC Workflow: Returned Mail`
- **Embedding recommendation**: Embed as title vector or include in semantic ranker title field.
- **Schema**: type=`Edm.String`, flags=[searchable sortable retrievable score-prof ]; rationale: Title carries high-signal terms — boost in scoring profile, set as semantic ranker title field, include in citations.

### `SFUrlName`
- **Role**: `key` — _name matches `key` hint_
- **Type**: `object`
- **Rows**: 4,867; non-null = 4,867 (100.00%)
- **Nulls**: 0 (0.0%); blanks=0; whitespace-only=0
- **Unique values**: 4,635
- **String length**: min=9, median=59, mean=61, p95=98, max=202
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=0%
- **PII pattern hits**: {'PHONE': 51}
- **Other**: PII pattern hits: PHONE=51
- **Top values**:
  - `PS-Notes-V3---Plan-Number-103919-ATS-Automation-Tooling-Systems-Inc` × 2
  - `MULTI-BU-CSC-Accessibility-Managing-or-Handling-Inquiries` × 2
  - `Critical-Illness-Conversion` × 2
  - `MULTI-BU-CSC-Audit-Dispute-Procedure` × 2
  - `PS-Notes-V3-Plan-Number-94598-Adacel-Inc` × 2
- **Examples**: `PS-Notes-V3-Plan-Number-113219-Liteline-Corporation` · `PS-Notes-V3-Régime-Numéro-113219-Liteline-Corporation` · `GB-CSC-Workflow-Returned-Mail`
- **Embedding recommendation**: Do not embed; use as metadata only.
- **Schema**: type=`Edm.String`, flags=[filterable retrievable ]; rationale: Stable unique identifier — use as the index `key` (or `filterable` secondary key) and propagate to every chunk for parent-child retrieval.

### `Sort_Order__c`
- **Role**: `empty` — _column is empty_
- **Type**: `float64`
- **Rows**: 4,867; non-null = 0 (0.00%)
- **Nulls**: 4,867 (100.0%); blanks=0; whitespace-only=0
- **Unique values**: 0
- **String length**: min=0, median=0, mean=0, p95=0, max=0
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=0%
- **Embedding recommendation**: Do not embed; use as metadata only.
- **Schema**: type=`Edm.String`, flags=[exclude]; rationale: Column is entirely empty — drop from index.

### `Business_Unit`
- **Role**: `category` — _name matches `category` hint_
- **Type**: `object`
- **Rows**: 4,867; non-null = 4,867 (100.00%)
- **Nulls**: 0 (0.0%); blanks=0; whitespace-only=0
- **Unique values**: 77
- **String length**: min=8, median=100, mean=74, p95=100, max=134
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=71%
- **Top values**:
  - `GB_Living_Benefits,GB_Internet,GB_Dental,GB_Admin,GB_Health,GB_Vitality,GB_Flex…` × 3,079
  - `Medical_Equipment` × 869
  - `GB_Health` × 272
  - `GB_Dental` × 109
  - `GB_Living_Benefits,MLB,GB_Health,GRS,MI,FSL,GB_Internet,IIC,AFF,DCC,GB_Dental,G…` × 73
- **Examples**: `GB_Living_Benefits,GB_Internet,GB_Dental,GB_Admin,GB_Health,GB_Vitality,GB_Flex…` · `GB_Living_Benefits,GB_Internet,GB_Dental,GB_Admin,GB_Health,GB_Vitality,GB_Flex…` · `GB_Living_Benefits,GB_Internet,GB_Dental,GB_Admin,GB_Health,GB_Vitality,GB_Flex…`
- **Embedding recommendation**: Do not embed; use as metadata only.
- **Schema**: type=`Collection(Edm.String)`, flags=[filterable facetable retrievable score-prof ]; rationale: Category — facet, filter, intent routing, and category-aware boosting.

### `Contact_Type`
- **Role**: `persona` — _name matches `persona` hint_
- **Type**: `object`
- **Rows**: 4,867; non-null = 3,930 (80.75%)
- **Nulls**: 937 (19.25%); blanks=0; whitespace-only=0
- **Unique values**: 118
- **String length**: min=3, median=204, mean=180, p95=204, max=322
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=93%
- **Top values**:
  - `Allianz,Plan_Administrator,Dental_Provider,Advisor,Customer_Beneficiary,Interna…` × 2,983
  - `All` × 128
  - `Plan_Administrator,Advisor,Internal_Employee,RGO,Unknown_Caller,Third_Party_Adm…` × 84
  - `Plan_Administrator,Advisor,Internal_Employee,RGO,Third_Party_Administrator,Cust…` × 70
  - `Customer_Member` × 70
- **Examples**: `Allianz,Plan_Administrator,Dental_Provider,Advisor,Customer_Beneficiary,Interna…` · `Allianz,Plan_Administrator,Dental_Provider,Advisor,Customer_Beneficiary,Interna…` · `Plan_Administrator,Internal_Employee,RGO,Third_Party_Administrator`
- **Embedding recommendation**: Do not embed; use as metadata only.
- **Schema**: type=`Collection(Edm.String)`, flags=[filterable facetable retrievable score-prof ]; rationale: Persona/audience — drives persona-aware filtering and routing.

### `Case_Subtype`
- **Role**: `metadata` — __
- **Type**: `object`
- **Rows**: 4,867; non-null = 3,843 (78.96%)
- **Nulls**: 1,024 (21.04%); blanks=0; whitespace-only=0
- **Unique values**: 77
- **String length**: min=3, median=3, mean=12, p95=61, max=398
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=13%
- **Top values**:
  - `All` × 3,287
  - `Claim_Status,Claims_Explanation,Benefit_Details` × 192
  - `Claim_Status,Privacy_Issue,Claims_Explanation,Benefit_Details` × 64
  - `Benefit_Details` × 22
  - `Claim_Void_Cancellation,Claim_Status,Privacy_Issue,Claims_Explanation,Benefit_D…` × 19
- **Examples**: `All` · `All` · `All`
- **Embedding recommendation**: Do not embed; use as metadata only.
- **Schema**: type=`Edm.String`, flags=[filterable retrievable ]; rationale: General metadata — retrievable; filter only if values are clean.

### `System_of_Record`
- **Role**: `category` — _low-cardinality short tokens_
- **Type**: `object`
- **Rows**: 4,867; non-null = 3,842 (78.94%)
- **Nulls**: 1,025 (21.06%); blanks=0; whitespace-only=0
- **Unique values**: 11
- **String length**: min=3, median=17, mean=15, p95=17, max=55
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=73%
- **Top values**:
  - `Gipsy,Manuconnect` × 2,825
  - `Manuconnect` × 972
  - `All` × 22
  - `Manulife_Bank` × 8
  - `Gipsy` × 3
- **Examples**: `Gipsy,Manuconnect` · `Gipsy,Manuconnect` · `Gipsy,Manuconnect`
- **Embedding recommendation**: Do not embed; use as metadata only.
- **Schema**: type=`Collection(Edm.String)`, flags=[filterable facetable retrievable score-prof ]; rationale: Category — facet, filter, intent routing, and category-aware boosting.

### `Case_Type`
- **Role**: `category` — _name matches `category` hint_
- **Type**: `object`
- **Rows**: 4,867; non-null = 3,849 (79.08%)
- **Nulls**: 1,018 (20.92%); blanks=0; whitespace-only=0
- **Unique values**: 61
- **String length**: min=3, median=3, mean=5, p95=16, max=332
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=4%
- **Top values**:
  - `All` × 3,436
  - `Drugs,Drug_Prior_Auth,ESC_DIN_Exception` × 60
  - `Basic` × 44
  - `Drugs` × 36
  - `Hospital` × 32
- **Examples**: `All` · `All` · `All`
- **Embedding recommendation**: Do not embed; use as metadata only.
- **Schema**: type=`Edm.String`, flags=[filterable facetable retrievable score-prof ]; rationale: Category — facet, filter, intent routing, and category-aware boosting.

### `Product_Family`
- **Role**: `product` — _name matches `product` hint, >95% of values share one value_
- **Type**: `object`
- **Rows**: 4,867; non-null = 3,844 (78.98%)
- **Nulls**: 1,023 (21.02%); blanks=0; whitespace-only=0
- **Unique values**: 7
- **String length**: min=3, median=14, mean=14, p95=14, max=241
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=0%
- **Top values**:
  - `GROUP_BENEFITS` × 3,804
  - `All` × 30
  - `Critical_Illness,Mortgage,GROUP_BENEFITS,Universal,Interest_Sensitive_Life,Loan…` × 2
  - `Critical_Illness,Structured_Settlement_Annuity,GROUP_BENEFITS,Universal,Interes…` × 2
  - `Critical_Illness,GROUP_BENEFITS,Paid_Up_Life,Term_Life,Disability_Insurance,Who…` × 2
- **Examples**: `GROUP_BENEFITS` · `GROUP_BENEFITS` · `GROUP_BENEFITS`
- **Embedding recommendation**: Do not embed; use as metadata only.
- **Schema**: type=`Edm.String`, flags=[searchable filterable facetable retrievable score-prof ]; rationale: Product / plan — high-value filter, facet, and scoring boost; expand acronyms via synonym map.

### `PrivacyFlag`
- **Role**: `access` — _name matches `access` hint, >95% of values share one value_
- **Type**: `object`
- **Rows**: 4,867; non-null = 4,867 (100.00%)
- **Nulls**: 0 (0.0%); blanks=0; whitespace-only=0
- **Unique values**: 2
- **String length**: min=2, median=2, mean=2, p95=2, max=3
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=0%
- **Top values**:
  - `No` × 4,846
  - `Yes` × 21
- **Examples**: `No` · `No` · `No`
- **Embedding recommendation**: Do not embed; use as metadata only.
- **Schema**: type=`Edm.String`, flags=[filterable sec-trim ]; rationale: Access flag — drive security trimming at query time; do not surface to end users.

### `content_path`
- **Role**: `url` — _name matches `url` hint, majority of values are URLs_
- **Type**: `object`
- **Rows**: 4,867; non-null = 4,867 (100.00%)
- **Nulls**: 0 (0.0%); blanks=0; whitespace-only=0
- **Unique values**: 4,867
- **String length**: min=82, median=82, mean=82, p95=82, max=82
- **Shape signals**: URL=100%, HTML=0%, Markdown=0%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=0%
- **Top values**:
  - `https://mlc.lightning.force.com/lightning/r/Knowledge__kav/ka5OM000000hugTYAQ/v…` × 1
  - `https://mlc.lightning.force.com/lightning/r/Knowledge__kav/ka5OM000000hui5YAA/v…` × 1
  - `https://mlc.lightning.force.com/lightning/r/Knowledge__kav/ka5OM0000012deDYAQ/v…` × 1
  - `https://mlc.lightning.force.com/lightning/r/Knowledge__kav/ka5OM0000012dfpYAA/v…` × 1
  - `https://mlc.lightning.force.com/lightning/r/Knowledge__kav/ka5OM0000018MfVYAU/v…` × 1
- **Examples**: `https://mlc.lightning.force.com/lightning/r/Knowledge__kav/ka5OM000000hugTYAQ/v…` · `https://mlc.lightning.force.com/lightning/r/Knowledge__kav/ka5OM000000hui5YAA/v…` · `https://mlc.lightning.force.com/lightning/r/Knowledge__kav/ka5OM0000012deDYAQ/v…`
- **Embedding recommendation**: Do not embed; use as metadata only.
- **Schema**: type=`Edm.String`, flags=[retrievable ]; rationale: URL is a citation field — retrievable only; do not tokenize or facet.

### `priorityscore`
- **Role**: `metric` — _name matches `metric` hint, low-cardinality short tokens_
- **Type**: `int64`
- **Rows**: 4,867; non-null = 4,867 (100.00%)
- **Nulls**: 0 (0.0%); blanks=0; whitespace-only=0
- **Unique values**: 4
- **String length**: min=1, median=1, mean=1, p95=1, max=1
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=0%
- **Top values**:
  - `1` × 4,597
  - `4` × 106
  - `3` × 94
  - `5` × 70
- **Examples**: `1` · `1` · `1`
- **Embedding recommendation**: Do not embed; use as metadata only.
- **Schema**: type=`Edm.Double`, flags=[filterable sortable retrievable score-prof ]; rationale: Numeric metric — sort/filter and weight in scoring profile.

### `parent_path`
- **Role**: `url` — _name matches `url` hint, majority of values are URLs_
- **Type**: `object`
- **Rows**: 4,867; non-null = 871 (17.90%)
- **Nulls**: 3,996 (82.1%); blanks=0; whitespace-only=0
- **Unique values**: 30
- **String length**: min=82, median=82, mean=82, p95=82, max=82
- **Shape signals**: URL=100%, HTML=0%, Markdown=0%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=0%
- **Top values**:
  - `https://mlc.lightning.force.com/lightning/r/Knowledge__kav/ka5OM000000TpITYA0/v…` × 74
  - `https://mlc.lightning.force.com/lightning/r/Knowledge__kav/ka5OM000000hli1YAA/v…` × 73
  - `https://mlc.lightning.force.com/lightning/r/Knowledge__kav/ka5OM000000TpK5YAK/v…` × 71
  - `https://mlc.lightning.force.com/lightning/r/Knowledge__kav/ka5OM000000TpGrYAK/v…` × 63
  - `https://mlc.lightning.force.com/lightning/r/Knowledge__kav/ka5OM000000iMG9YAM/v…` × 57
- **Examples**: `https://mlc.lightning.force.com/lightning/r/Knowledge__kav/ka5OM000000Tpn7YAC/v…` · `https://mlc.lightning.force.com/lightning/r/Knowledge__kav/ka5OM000000Tpn7YAC/v…` · `https://mlc.lightning.force.com/lightning/r/Knowledge__kav/ka5OM000000Tpn7YAC/v…`
- **Embedding recommendation**: Do not embed; use as metadata only.
- **Schema**: type=`Edm.String`, flags=[retrievable ]; rationale: URL is a citation field — retrievable only; do not tokenize or facet.

### `is_deleted`
- **Role**: `constant` — _name matches `status` hint, single unique value_
- **Type**: `bool`
- **Rows**: 4,867; non-null = 4,867 (100.00%)
- **Nulls**: 0 (0.0%); blanks=0; whitespace-only=0
- **Unique values**: 1
- **String length**: min=5, median=5, mean=5, p95=5, max=5
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=0%
- **Top values**:
  - `False` × 4,867
- **Examples**: `False` · `False` · `False`
- **Embedding recommendation**: Do not embed; use as metadata only.
- **Schema**: type=`Edm.String`, flags=[exclude]; rationale: Single distinct value provides zero retrieval/filter signal — drop from index.

### `deleted_at`
- **Role**: `empty` — _name matches `date` hint, column is empty_
- **Type**: `float64`
- **Rows**: 4,867; non-null = 0 (0.00%)
- **Nulls**: 4,867 (100.0%); blanks=0; whitespace-only=0
- **Unique values**: 0
- **String length**: min=0, median=0, mean=0, p95=0, max=0
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=0%
- **Embedding recommendation**: Do not embed; use as metadata only.
- **Schema**: type=`Edm.String`, flags=[exclude]; rationale: Column is entirely empty — drop from index.

### `ingested_at`
- **Role**: `constant` — _name matches `date` hint, single unique value_
- **Type**: `datetime64[ns]`
- **Rows**: 4,867; non-null = 4,867 (100.00%)
- **Nulls**: 0 (0.0%); blanks=0; whitespace-only=0
- **Unique values**: 1
- **String length**: min=26, median=26, mean=26, p95=26, max=26
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=0%, Date=100%, SalesforceID=0%, Multi-value=0%
- **Top values**:
  - `2026-05-23 21:53:42.933000` × 4,867
- **Examples**: `2026-05-23 21:53:42.933000` · `2026-05-23 21:53:42.933000` · `2026-05-23 21:53:42.933000`
- **Embedding recommendation**: Do not embed; use as metadata only.
- **Schema**: type=`Edm.String`, flags=[exclude]; rationale: Single distinct value provides zero retrieval/filter signal — drop from index.

### `run_id`
- **Role**: `constant` — _name matches `key` hint, single unique value_
- **Type**: `object`
- **Rows**: 4,867; non-null = 4,867 (100.00%)
- **Nulls**: 0 (0.0%); blanks=0; whitespace-only=0
- **Unique values**: 1
- **String length**: min=22, median=22, mean=22, p95=22, max=22
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=0%
- **Top values**:
  - `layer2-20260601-035813` × 4,867
- **Examples**: `layer2-20260601-035813` · `layer2-20260601-035813` · `layer2-20260601-035813`
- **Embedding recommendation**: Do not embed; use as metadata only.
- **Schema**: type=`Edm.String`, flags=[exclude]; rationale: Single distinct value provides zero retrieval/filter signal — drop from index.

### `llm_summary`
- **Role**: `summary` — _name matches `summary` hint, long-form content_
- **Type**: `object`
- **Rows**: 4,867; non-null = 4,867 (100.00%)
- **Nulls**: 0 (0.0%); blanks=0; whitespace-only=0
- **Unique values**: 4,867
- **String length**: min=92, median=768, mean=1026, p95=2498, max=32767
- **Shape signals**: URL=2%, HTML=0%, Markdown=3%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=52%
- **PII pattern hits**: {'EMAIL': 295, 'PHONE': 450}
- **Other**: 17 value(s) contain HTML entities; PII pattern hits: EMAIL=295, PHONE=450
- **Top values**:
  - `PS Notes V3 - Plan Number 113219 Liteline Corporation. The group has Short Term…` × 1
  - `PS Notes V3 - Régime Numéro 113219 Liteline Corporation. Le groupe bénéficie de…` × 1
  - `GB CSC Workflow: Returned Mail. Mail is returned from Canada Post when the addr…` × 1
  - `GB CSC Flux de travail du Service à la clientèle : courrier retourné. Le courri…` × 1
  - `PS Notes V3 - Plan Number 103919 ATS Automation Tooling Systems Inc. outlines s…` × 1
- **Examples**: `PS Notes V3 - Plan Number 113219 Liteline Corporation. The group has Short Term…` · `PS Notes V3 - Régime Numéro 113219 Liteline Corporation. Le groupe bénéficie de…` · `GB CSC Workflow: Returned Mail. Mail is returned from Canada Post when the addr…`
- **Embedding recommendation**: Embed; verify token sizes with a tokenizer before ingestion.
- **Schema**: type=`Edm.String`, flags=[searchable retrievable vector ]; rationale: Summary is high-density content — searchable, included in semantic ranking, and a strong vector field candidate alongside body text.

### `llm_faqs`
- **Role**: `tag` — _name matches `tag` hint, JSON-shaped values_
- **Type**: `object`
- **Rows**: 4,867; non-null = 4,867 (100.00%)
- **Nulls**: 0 (0.0%); blanks=0; whitespace-only=0
- **Unique values**: 4,605
- **String length**: min=2, median=395, mean=381, p95=517, max=668
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=100%, Date=0%, SalesforceID=0%, Multi-value=26%
- **PII pattern hits**: {'EMAIL': 1, 'PHONE': 5}
- **Other**: PII pattern hits: EMAIL=1, PHONE=5
- **Top values**:
  - `[]` × 261
  - `["What is the Ré-Action employee assistance program?","How are claims for the R…` × 2
  - `["Qu'est-ce que le programme Ré-Action?","Comment les services du programme Ré-…` × 2
  - `["What is included under Plan 133548 for Liteline Corporation?","Where can I fi…` × 1
  - `["Quels services de gestion des absences sont disponibles pour le régime numéro…` × 1
- **Examples**: `["What is included under Plan 133548 for Liteline Corporation?","Where can I fi…` · `["Quels services de gestion des absences sont disponibles pour le régime numéro…` · `["What happens when mail is returned from Canada Post due to an incorrect addre…`
- **Embedding recommendation**: Do not embed directly; use as filter/facet metadata or expand into searchable text.
- **Schema**: type=`Collection(Edm.String)`, flags=[searchable filterable facetable retrievable ]; rationale: Tag-like field — facet/filter and inject into semantic keyword field; split JSON / delimited values into a collection.

### `source_run_id`
- **Role**: `constant` — _name matches `key` hint, single unique value_
- **Type**: `object`
- **Rows**: 4,867; non-null = 4,867 (100.00%)
- **Nulls**: 0 (0.0%); blanks=0; whitespace-only=0
- **Unique values**: 1
- **String length**: min=22, median=22, mean=22, p95=22, max=22
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=0%
- **Top values**:
  - `layer1-20260523-215308` × 4,867
- **Examples**: `layer1-20260523-215308` · `layer1-20260523-215308` · `layer1-20260523-215308`
- **Embedding recommendation**: Do not embed; use as metadata only.
- **Schema**: type=`Edm.String`, flags=[exclude]; rationale: Single distinct value provides zero retrieval/filter signal — drop from index.

### `prompt_version`
- **Role**: `constant` — _name matches `version` hint, single unique value_
- **Type**: `object`
- **Rows**: 4,867; non-null = 4,867 (100.00%)
- **Nulls**: 0 (0.0%); blanks=0; whitespace-only=0
- **Unique values**: 1
- **String length**: min=2, median=2, mean=2, p95=2, max=2
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=0%
- **Top values**:
  - `v4` × 4,867
- **Examples**: `v4` · `v4` · `v4`
- **Embedding recommendation**: Do not embed; use as metadata only.
- **Schema**: type=`Edm.String`, flags=[exclude]; rationale: Single distinct value provides zero retrieval/filter signal — drop from index.

### `summarized_at`
- **Role**: `date` — _name matches `date` hint, majority of values are dates/timestamps_
- **Type**: `datetime64[ns]`
- **Rows**: 4,867; non-null = 4,867 (100.00%)
- **Nulls**: 0 (0.0%); blanks=0; whitespace-only=0
- **Unique values**: 99
- **String length**: min=26, median=26, mean=26, p95=26, max=26
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=0%, Date=100%, SalesforceID=0%, Multi-value=0%
- **Top values**:
  - `2026-06-01 04:02:42.690000` × 50
  - `2026-06-01 04:08:15.135000` × 50
  - `2026-06-01 04:11:55.415000` × 50
  - `2026-06-01 04:15:38.801000` × 50
  - `2026-06-01 04:18:52.875000` × 50
- **Examples**: `2026-06-01 04:02:42.690000` · `2026-06-01 04:02:42.690000` · `2026-06-01 04:02:42.690000`
- **Embedding recommendation**: Do not embed; use as metadata only.
- **Schema**: type=`Edm.DateTimeOffset`, flags=[filterable sortable retrievable freshness score-prof ]; rationale: Date — drives recency boosting via a freshness scoring profile and time-based filters; not searchable text.

### `Knowledge_Content_md`
- **Role**: `content` — _name matches `content` hint, long-form content_
- **Type**: `object`
- **Rows**: 4,867; non-null = 4,862 (99.90%)
- **Nulls**: 5 (0.1%); blanks=0; whitespace-only=0
- **Unique values**: 4,532
- **String length**: min=24, median=1190, mean=4646, p95=24324, max=32767
- **Shape signals**: URL=41%, HTML=7%, Markdown=100%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=35%
- **PII pattern hits**: {'EMAIL': 627, 'PHONE': 654, 'IPV4': 2, 'CREDITCARD': 2}
- **Other**: 1 value(s) contain HTML entities; PII pattern hits: EMAIL=627, PHONE=654, IPV4=2, CREDITCARD=2
- **Top values**:
  - `\|  \| \| --- \| \| **Plastic Drug Cards** \| \| - Plastic drug cards are produced for…` × 43
  - `\|  \| \| --- \| \| **Cartes d’assurance médicament en plastique** \| \| Des cartes d’…` × 41
  - `\|  \| \| --- \| \| **Cartes d’assurance médicament en plastique** \| \| - Des cartes…` × 37
  - `\|  \| \| --- \| \| **Plastic Drug Cards** \| \| Plastic drug cards are produced for t…` × 28
  - `\|  \| \| --- \| \| **Self Admin Benefits** \| \| Plan has Self Admin ADD, DLI, ELI ma…` × 14
- **Examples**: `\|  \| \| --- \| \| **Absence and Disability Management** \| \| - Group has Short Term…` · `\|  \| \| --- \| \| **Gestion des absences et de l’invalidité** \| \| - Ce groupe béné…` · `\|  \| \| --- \| \| **Important Information** \| \| Mail is returned from Canada Post…`
- **Embedding recommendation**: Embed; verify token sizes with a tokenizer before ingestion.
- **Schema**: type=`Edm.String`, flags=[searchable retrievable vector ]; rationale: Primary article body — searchable, semantic ranker content field, and embedded into the vector index after chunking + cleaning.

### `content_format`
- **Role**: `constant` — _name matches `tracking` hint, single unique value_
- **Type**: `object`
- **Rows**: 4,867; non-null = 4,867 (100.00%)
- **Nulls**: 0 (0.0%); blanks=0; whitespace-only=0
- **Unique values**: 1
- **String length**: min=8, median=8, mean=8, p95=8, max=8
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=0%
- **Top values**:
  - `markdown` × 4,867
- **Examples**: `markdown` · `markdown` · `markdown`
- **Embedding recommendation**: Do not embed; use as metadata only.
- **Schema**: type=`Edm.String`, flags=[exclude]; rationale: Single distinct value provides zero retrieval/filter signal — drop from index.

### `llm_category`
- **Role**: `category` — _name matches `category` hint, low-cardinality short tokens_
- **Type**: `object`
- **Rows**: 4,867; non-null = 4,867 (100.00%)
- **Nulls**: 0 (0.0%); blanks=0; whitespace-only=0
- **Unique values**: 25
- **String length**: min=4, median=19, mean=17, p95=19, max=22
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=0%
- **Top values**:
  - `plan_administration` × 2,849
  - `medical_equipment` × 890
  - `system_navigation` × 156
  - `dental` × 145
  - `drugs` × 127
- **Examples**: `plan_administration` · `plan_administration` · `plan_administration`
- **Embedding recommendation**: Do not embed; use as metadata only.
- **Schema**: type=`Edm.String`, flags=[filterable facetable retrievable score-prof ]; rationale: Category — facet, filter, intent routing, and category-aware boosting.

### `llm_focus`
- **Role**: `tag` — _JSON-shaped values_
- **Type**: `object`
- **Rows**: 4,867; non-null = 4,867 (100.00%)
- **Nulls**: 0 (0.0%); blanks=0; whitespace-only=0
- **Unique values**: 143
- **String length**: min=11, median=41, mean=36, p95=48, max=51
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=100%, Date=0%, SalesforceID=0%, Multi-value=0%
- **Top values**:
  - `["member_administration"]` × 1,253
  - `["member_administration","eligibility_balances"]` × 1,253
  - `["eligibility_balances","claims_process"]` × 648
  - `["eligibility_balances"]` × 440
  - `["system_navigation"]` × 118
- **Examples**: `["member_administration"]` · `["member_administration","reference"]` · `["member_administration","document_fulfillment"]`
- **Embedding recommendation**: Do not embed directly; use as filter/facet metadata or expand into searchable text.
- **Schema**: type=`Collection(Edm.String)`, flags=[searchable filterable facetable retrievable ]; rationale: Tag-like field — facet/filter and inject into semantic keyword field; split JSON / delimited values into a collection.

### `llm_entities`
- **Role**: `tag` — _name matches `tag` hint, JSON-shaped values_
- **Type**: `object`
- **Rows**: 4,867; non-null = 4,867 (100.00%)
- **Nulls**: 0 (0.0%); blanks=0; whitespace-only=0
- **Unique values**: 4,516
- **String length**: min=2, median=138, mean=329, p95=1151, max=8127
- **Shape signals**: URL=1%, HTML=0%, Markdown=0%, JSON=100%, Date=0%, SalesforceID=0%, Multi-value=9%
- **PII pattern hits**: {'EMAIL': 323, 'PHONE': 373, 'IPV4': 1}
- **Other**: 1 value(s) contain HTML entities; PII pattern hits: EMAIL=323, PHONE=373, IPV4=1
- **Top values**:
  - `["Plastic drug cards"]` × 84
  - `["Cartes d’assurance médicament en plastique"]` × 79
  - `[]` × 27
  - `["Self Admin ADD","DLI","ELI"]` × 16
  - `["TPA Effortlessadmin","https://app.effortlessadmin.com/","1-800-311-MYEA (6932…` × 9
- **Examples**: `["Short Term Absence Salary Continuance (STASC)","Plan 133548"]` · `["Solution de gestion des absences de courte durée","Continuation du salaire","…` · `["Returned mail","Canada Post","Plan Administrator (PA)","[REDACTED-EMAIL]","AW…`
- **Embedding recommendation**: Do not embed directly; use as filter/facet metadata or expand into searchable text.
- **Schema**: type=`Collection(Edm.String)`, flags=[searchable filterable facetable retrievable ]; rationale: Tag-like field — facet/filter and inject into semantic keyword field; split JSON / delimited values into a collection.

### `llm_error`
- **Role**: `tracking` — _name matches `tracking` hint_
- **Type**: `object`
- **Rows**: 4,867; non-null = 4 (0.08%)
- **Nulls**: 4,863 (99.92%); blanks=0; whitespace-only=0
- **Unique values**: 4
- **String length**: min=100, median=121, mean=116, p95=121, max=121
- **Shape signals**: URL=0%, HTML=0%, Markdown=0%, JSON=0%, Date=0%, SalesforceID=0%, Multi-value=75%
- **Top values**:
  - `LLM parse failure: parsed=None, parsing_error=None (input_chars=24802, tool_cal…` × 1
  - `LLM parse failure: parsed=None, parsing_error=None (input_chars=63207, tool_cal…` × 1
  - `LLM parse failure: parsed=None, parsing_error=None (input_chars=66842, tool_cal…` × 1
  - `PermissionDeniedError: Error code: 403 - {'statusCode': 403, 'message': 'Unauth…` × 1
- **Examples**: `LLM parse failure: parsed=None, parsing_error=None (input_chars=24802, tool_cal…` · `LLM parse failure: parsed=None, parsing_error=None (input_chars=63207, tool_cal…` · `LLM parse failure: parsed=None, parsing_error=None (input_chars=66842, tool_cal…`
- **Embedding recommendation**: Do not embed; use as metadata only.
- **Schema**: type=`Edm.String`, flags=[exclude]; rationale: Operational/tracking field — keep in source-of-truth store for lineage; do not index.
