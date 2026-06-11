# RAG Excel Analysis — Executive Summary
_Companion to `rag_excel_knowledge_analysis.md`. Source: `GBL1Summarydump_Addltags_06012026.xlsx`._

## Top 10 Insights
1. Dataset is a **Salesforce Knowledge dump** of ~4,867 article versions across 1 sheet(s) and 41 columns, already LLM-enriched with summary, FAQ, category, and entity fields.
2. **`SalesforceID`** is the per-version primary key (100% unique). **`ArticleNumber`** is the parent key — 2 articles have more than one version (max 2).
3. Two content variants exist: **`Knowledge_Content`** (HTML) and **`Knowledge_Content_md`** (Markdown). The Markdown variant should be the embedding source after cleanup.
4. The dataset already includes an **LLM-generated FAQ column (`llm_faqs`)** and entity list (`llm_entities`) — gold seeds for both the chatbot's retrieval enrichment and the evaluation golden set.
5. **4 empty columns** (`Forms_Channel__c`, `Forms_Product_Name__c`, `Sort_Order__c`, `deleted_at`) and **7 constant columns** (`PublishStatus`, `is_deleted`, `ingested_at`, `run_id`, `source_run_id`, `prompt_version`, `content_format`) should be excluded from the index.
6. Strong structured metadata is present (**`Business_Unit`, `Contact_Type`, `Product_Family`, `Case_Type`, `Case_Subtype`, `System_of_Record`, `llm_category`**) — directly usable as filters, facets, and routing signals.
7. Several metadata fields are **comma-delimited multi-values** (e.g. `Business_Unit`, `Contact_Type`) — must be stored as `Collection(Edm.String)` for correct filtering.
8. `Language` carries two locale codes — the index must apply per-language analyzers and ideally separate per-language semantic configurations.
9. `PrivacyFlag` is binary and uniformly `No` across most rows; this is **insufficient for enterprise-grade ACLs** — layer a fine-grained ACL collection bound to identity-provider groups.
10. `LastModifiedDate` + `summarized_at` enable a **freshness scoring profile** today — one of the highest-ROI quick wins.

## Top 10 Data Quality Issues
1. `Forms_Channel__c` is entirely empty.
2. `Forms_Product_Name__c` is entirely empty.
3. `Knowledge_Content` is HTML-heavy — strip before embedding.
4. `Knowledge_Content` carries 3466 rows with HTML entities.
5. `Knowledge_Content` has 3545 near-duplicates.
6. `PublishStatus` is constant.
7. `Summary` has 932 near-duplicates.
8. `Title` has 16 near-duplicates.
9. `Sort_Order__c` is entirely empty.
10. `Business_Unit` is multi-valued — needs Collection(Edm.String).

## Top 10 Retrieval Improvement Opportunities
1. **Recency boost via freshness scoring profile** _(priority High, complexity Low)_ — Older versions out-rank current ones for the same query. **Fields**: `LastModifiedDate`, `summarized_at`, `VersionNumber`.
2. **Category / product / persona boosting + filtering** _(priority High, complexity Low)_ — All articles compete uniformly — irrelevant business units leak into results. **Fields**: `RecordTypeName__c`, `Business_Unit`, `System_of_Record`, `Case_Type`, `llm_category` · `Product_Family` · `Contact_Type`.
3. **Synonym maps for acronyms and product aliases** _(priority High, complexity Low)_ — User queries use short forms (e.g. plan codes, business-unit acronyms) that don't match indexed text. **Fields**: `Product_Family` · `RecordTypeName__c`, `Business_Unit`, `System_of_Record`, `Case_Type`, `llm_category`.
4. **Semantic configuration with explicit title/keyword/content fields** _(priority High, complexity Low)_ — Default ranking ignores high-signal fields. **Fields**: `Title` · `RecordTypeName__c`, `Business_Unit`, `System_of_Record`, `Case_Type`, `llm_category` · `Knowledge_Content`, `Knowledge_Content_md`.
5. **Hybrid retrieval with RRF + chunk-level vectors** _(priority High, complexity Medium)_ — Vector-only retrieval misses keyword-specific hits; BM25-only misses paraphrases. **Fields**: `Knowledge_Content`, `Knowledge_Content_md` · `Summary`, `llm_summary`.
6. **Query rewrite & HyDE for sparse retrieval** _(priority Medium, complexity Medium)_ — Short user queries don't surface the right chunks. **Fields**: `Summary`, `llm_summary` · `llm_faqs`, `llm_focus`, `llm_entities`.
7. **Parent–child retrieval (chunk → parent article)** _(priority High, complexity Medium)_ — Returned chunks lack surrounding context, hurting grounding. **Fields**: `ArticleNumber`, `SalesforceID`, `SFUrlName` · `Title`.
8. **Duplicate / near-duplicate suppression** _(priority High, complexity Low)_ — Multi-version articles fight for the same slot. **Fields**: `ArticleNumber`, `SalesforceID`, `SFUrlName` · `VersionNumber`.
9. **LLM reranking on the top 20** _(priority Medium, complexity Medium)_ — Even after semantic reranking, the top result is not always the best answer. **Fields**: `Knowledge_Content`, `Knowledge_Content_md` · `Summary`, `llm_summary`.
10. **Access-control-aware retrieval (security trimming)** _(priority High, complexity Medium)_ — Restricted articles could surface to unauthorized users. **Fields**: `PrivacyFlag`.

## Top 10 Azure AI Search Schema Recommendations
1. **Key field**: `SalesforceID` (per-version). Derive a `chunk_id` once chunking is added.
2. **Citation contract**: `Title` + `content_path` (deep-link URL) on every doc.
3. **Searchable content**: `Knowledge_Content_md` (cleaned) and `Summary` / `llm_summary`.
4. **Vector fields**: `content_vector` (chunk) + optional `summary_vector` and `title_vector`.
5. **Filterable + facetable**: `Business_Unit`, `Contact_Type`, `Product_Family`, `Case_Type`, `Case_Subtype`, `System_of_Record`, `RecordTypeName__c`, `llm_category`, `Language`, `PublishStatus`.
6. **Collections** for multi-valued fields: `Business_Unit`, `Contact_Type`, `Case_Subtype`, `llm_focus`, `llm_entities`, `Forms_Plan_Number__c`.
7. **Freshness**: `LastModifiedDate` (DateTimeOffset, sortable, drives a freshness scoring profile).
8. **Versioning**: `ArticleNumber` + `VersionNumber` (filterable; default = latest only).
9. **Security trimming**: `PrivacyFlag` plus a fine-grained `acl_groups` collection.
10. **Exclude from index**: empty (`Forms_Channel__c`, `Forms_Product_Name__c`, `Sort_Order__c`, `deleted_at`) and constant (`PublishStatus`, `is_deleted`, `ingested_at`, `run_id`, `source_run_id`, `prompt_version`, `content_format`) columns — keep them in source-of-truth, not in the search index.

## Top 10 Questions for Content Owners
1. Who owns each `Business_Unit` of articles, and what is the review cadence?
2. Is the binary `PrivacyFlag` the actual access model, or do you have richer ACL groups?
3. Are LLM-generated fields (`llm_summary`, `llm_faqs`, `llm_category`) authoritative or advisory for citations?
4. Should non-latest article versions ever be retrievable by end users?
5. Which languages must the chatbot support, and how should the bot handle cross-language queries?
6. Are `Forms_Plan_Number__c` codes customer-visible identifiers? (Drives synonym strategy.)
7. What is the escalation path when no answer can be grounded?
8. Are `Attachment_Links__c` HTML blobs intended as downloadable references in the answer?
9. What latency and per-query cost budgets must the production chatbot meet?
10. Where is the **source of truth** for content updates — Salesforce, an upstream CMS, or both?

## Top 10 Next Steps
1. Clean the workbook columns: drop empty + constant; rename Salesforce `__c` suffixes.
2. Build an ingestion job that converts HTML → clean Markdown, decodes entities, normalizes whitespace.
3. Add heading-aware chunking (400–800 tokens, 10–15% overlap, preserve title/breadcrumb on each chunk).
4. Stand up the Azure AI Search index with the schema from §7.
5. Configure semantic configuration + freshness + category scoring profiles.
6. Add synonym maps for business-unit / product acronyms.
7. Bind `PrivacyFlag` (and future ACL groups) to security trimming filters.
8. Curate a 300-query golden set from `llm_faqs`; instrument Recall@K / MRR / nDCG / faithfulness.
9. Add ApplicationInsights traces correlating `query_id` ↔ `article_id` ↔ `chunk_id`.
10. Run a hybrid + semantic rerank A/B against vector-only and report at the next sprint review.
