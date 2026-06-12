# Final Retrieval Strategy — Comparison, Verdict, and Consolidated Plan
_2026-06-10. Synthesizes two research reports against the corpus described in `rag_excel_knowledge_analysis.md`:_
- **Report A (Perplexity)**: `Beyond Vanilla RAG for Enterprise Knowledge Bases.md` — technique survey (ColBERT, HyDE, RAG-Fusion, GraphRAG, Self-RAG).
- **Report B (Claude deep-research)**: `retrieval_deep_research_report.md` — benchmark-evidence-ranked verdicts (T2-RAGBench, Anthropic, Azure benchmarks, RAG-vs-GraphRAG evaluation).

---

## 1. How the two reports differ in kind

| Dimension | Report A (Perplexity) | Report B (deep-research) |
|---|---|---|
| Evidence type | **Proposer papers** — cites the original ColBERTv2/HyDE/Self-RAG/RAG-Fusion papers, i.e., each technique's own authors reporting wins on open-web benchmarks | **Head-to-head comparisons** — independent benchmarks that pit techniques against each other on enterprise-style corpora, plus platform-vendor benchmarks on Azure AI Search itself |
| Corpus fit | Generic enterprise KB framing | Matched to this corpus type (fact-dense, table-heavy, FAQ/procedural, bilingual EN/FR) |
| Platform fit | Platform-agnostic; several recommendations require infra outside Azure AI Search | Azure AI Search-native options evaluated explicitly (semantic ranker generations, agentic retrieval, query rewrite preview status) |
| Recency | Core citations 2020–2024 | Core citations 2024–2026, incl. post-Nov-2024 Azure ranker |
| Confidence labeling | None | Per-claim (verified / high / medium) |
| Production ops | **Stronger** — tiered fast/slow paths, LLM data-handling/privacy for query-side calls, index/model governance | Lighter on governance; stronger on measurement gates |

**The structural difference matters**: a technique's own paper almost always shows a win (that's why it was published). The only way to rank techniques is comparative evaluation on a similar corpus — which is what Report B is built on. Where the two conflict, Report B's evidence should win; where Report A adds operational substance the benchmarks don't speak to (orchestration, governance), it should be kept.

## 2. Technique-by-technique: where they agree, where they conflict, final call

| Technique | Report A says | Report B says | **Final call** |
|---|---|---|---|
| Hybrid BM25+vector + RRF | Harden first, foundation | Validated; backbone of every winning config (R@5 0.695 vs 0.587 dense-only) | ✅ **Backbone** — both agree |
| Cross-encoder / semantic reranking | Mentioned mostly via late-interaction framing | **Biggest single lever**: R@5 0.695→0.816; Azure +12–22 NDCG@3; multilingual (MIRACL 72.0) | ✅ **Adopt + ablate depth (50–100 candidates) and model (Azure SR vs Cohere Rerank 3.5)** |
| Contextual retrieval (situated chunks) | **Absent** — the survey's biggest gap | Verified twice (Anthropic 5.7%→1.9% failure w/ reranker; T2-RAGBench +2.2pp R@5); near-free here since `llm_summary`/`Title`/`llm_category` already exist | ✅ **Adopt in first sprint** |
| HyDE | Pilot for difficult queries (#2 recommendation) | **Measured harm** on fact-dense corpora: R@5 0.544 vs 0.587 plain dense — hypotheticals hallucinate plan-number-style specifics | ⬇️ **Backlog.** Conflict resolved by evidence: an insurance/benefits KB is exactly the failure case. Re-open only for a vague-query tier, gated on golden set |
| RAG-Fusion / multi-query | Adopt for complex tasks (#3 recommendation) | ≈0 lift over hybrid (R@5 0.640 vs BM25's 0.644; +0.0–1.7 NDCG@3 once ranker present) | ⬇️ **Fallback only** for hard/failed/misspelled queries. Report A's instinct to gate it on complex queries was right; the expected lift was overstated |
| ColBERT / late interaction | Pilot on a business unit (#4 recommendation) | No Azure-native support; second index + ACL reconciliation; no head-to-head evidence it beats hybrid+rerank on this document type | ❌ **Skip** unless migrating off Azure AI Search. The reranker already delivers token-level interaction quality at the top of the funnel |
| GraphRAG | Prototype using `llm_entities` (#5 recommendation) | Vanilla RAG **beats** GraphRAG on single-hop QA; wins only ~2–3pts on multi-hop; **40–57× indexing cost** | ❌ **Defer with explicit trigger**: revisit only if production logs show multi-hop or corpus-wide aggregate queries failing at meaningful volume |
| Self-RAG | Approximate via orchestration, don't retrain (#6) | Same conclusion | ✅ **Adopt the principles**: groundedness gate, abstain/escalate policy — both reports agree |
| Agentic retrieval (Azure-native) | Absent | +16 pts answer relevance on complex queries (+10.3 French), **+0.03 on simple** — preview feature | ✅ **Adopt for complex-query tier only**, behind a flag with fallback |
| Long-context instead of RAG | Absent | 8k→120k context didn't help; 1M-token condition still lost to structured retrieval; corpus too large anyway | ✅ **No change** — RAG stands at this scale |
| Tiered fast/slow architecture | ✔ fast path / slow path by query difficulty | ✔ query-class routing (the meta-finding: every technique's value is query-class dependent) | ✅ **Convergent — make routing a first-class component.** Both reports independently arrived here; strongest shared insight |
| Golden-set evaluation + A/B | ✔ | ✔ (every cited gain was found this way) | ✅ **CI gate on Recall@5/MRR/nDCG, stratified by query class, language, persona** |
| Governance / LLM privacy | **Stronger**: query-side LLM calls must respect data-handling policy; new infra inherits ACL/observability standards | Lighter | ✅ **Keep Report A's guidance** (see §5) |

## 3. Overall best pick

**Report B (`retrieval_deep_research_report.md`) is the better foundation.** Three of Report A's six numbered recommendations (HyDE pilot, RAG-Fusion adoption, ColBERT pilot) are contradicted or unsupported when techniques are tested head-to-head on corpora like this one, and it misses the two highest-value items entirely (contextual retrieval, Azure agentic retrieval) plus the reranker's outsized role. Report A's lasting contributions — convergent fast/slow tiering, Self-RAG-style orchestration principles, and LLM data-handling governance — are folded into the consolidated plan below.

---

## 4. The consolidated final approach

### Target architecture

```
                                ┌────────────────────────────────────────────┐
 user query ──► language detect │  Query-class router (cheap classifier /    │
              (en / fr)         │  heuristics: length, entities, turn count) │
                                └───────┬───────────────────────┬────────────┘
                                 simple │ (~80%)         complex│ (~20%)
                                        ▼                       ▼
                          ┌──────────────────────┐   ┌─────────────────────────┐
                          │ FAST PATH             │   │ SLOW PATH               │
                          │ hybrid BM25+vector    │   │ Azure agentic retrieval │
                          │ (RRF) over contextual │   │ (LLM query planning →   │
                          │ chunks + llm_faqs     │   │ parallel subqueries →   │
                          │ channel               │   │ per-subquery hybrid+SR) │
                          │  → semantic ranker    │   │ behind preview flag,    │
                          │    (depth 50–100)     │   │ falls back to fast path │
                          └──────────┬───────────┘   └──────────┬──────────────┘
                                     ▼                          ▼
                          security trimming (ACL) · latest-version filter ·
                          freshness/category/persona scoring profiles
                                     ▼
                          groundedness gate: answer must cite retrieved
                          Title + content_path; weak support → retry with
                          query-rewrite fallback → else abstain & escalate
```

### Phase 0 — Ingest hygiene (prerequisite, already planned)
- HTML → clean Markdown, entity decode, near-duplicate suppression, drop empty/constant columns, **PII redaction before any embedding** (1,077 PII hits in `Knowledge_Content` per the data audit). Near-duplicates that *contradict* each other are flagged to content owners rather than silently suppressed (§6).
- Heading-aware chunking 400–800 tokens, parent–child IDs (`chunk_id` ← `SalesforceID`).

### Phase 1 (Sprints 1–2) — The verified-lift core
1. **Contextual chunk enrichment**: prepend `Title` + `llm_summary` + `llm_category`/`Business_Unit` breadcrumb to every chunk before embedding *and* BM25 indexing. Optional upgrade: per-chunk situating sentence via a cached-prompt LLM pass (one-time, trivial cost at ~4.9k articles).
2. Hybrid BM25+vector with RRF; **current-generation Azure semantic ranker** (re-benchmark if last tested before Nov 2024).
3. **Golden set live first** (300–500 queries seeded from `llm_faqs`, human-curated), stratified by `llm_category` × `Business_Unit` × persona × **language** × **query complexity class**. CI gate: block on Recall@5 < baseline − 2%.
4. App Insights traces: `query_id` ↔ `article_id` ↔ `chunk_id` ↔ query class.

### Phase 2 (Sprints 3–4) — Ranking & channels
5. **Reranker ablation** on the golden set: candidate depth (20 vs 50 vs 100) × model (Azure SR vs Cohere Rerank 3.5 vs LLM-rerank). Pick by nDCG-per-latency.
6. **`llm_faqs` as a retrieval channel**: index FAQ questions as searchable + vector fields mapped to their article (query-to-question matching; the doc2query work is already paid for). Measure — practitioner-grade evidence, not benchmark-verified.
7. Synonym maps (acronyms, plan codes), freshness scoring profile, latest-`VersionNumber` filter, ACL security trimming (replace binary `PrivacyFlag` with group collection).

### Phase 3 (Quarter 2) — Routing & the complex tail
8. **Query-class router** in front of retrieval (the convergent insight of both reports). Start with heuristics; upgrade to a small classifier trained on logged traffic.
9. **Azure agentic retrieval** for the complex/conversational tier, behind a feature flag with fast-path fallback (preview, no SLA).
10. **Query-rewrite fallback** for hard/misspelled/zero-result queries only (Azure native is preview and needs per-request `queryLanguage`; fr-FR/fr-CA supported — language detection from step 0 feeds this).
11. **Groundedness gate + abstain/escalate** (Self-RAG principles, no retraining): answer must be supported by ≥1 retrieved citation; weak support triggers one retry, then escalation. Log critique signals for tuning, plus cited article `VersionNumber` + publish date so stale-version citations are detectable (§6).
12. **Cross-language policy decision**: choose whether EN queries may retrieve FR-only articles. If yes → multilingual embedding model + multilingual reranker (Azure SR is MIRACL-validated); BM25 will not cross languages.
13. **Multi-turn conversation handling on the fast path** (§6): rewrite the latest user turn into a self-contained query from conversation history *before* retrieval (cheap LLM rewrite; QReCC/TopiOCQA-validated, ~10–20% retrieval gain over naive history handling — anaphora like "what about for Quebec?" currently retrieves nothing useful on the fast path). History policy: full history to the generator up to ~15 turns, rolling summary of older turns beyond that ("Lost in the Middle" guard, arXiv 2307.03172); no persistent memory store. The agentic slow path already consumes history natively (`messages` parameter).

### Backlog — re-open only on a named trigger
| Item | Trigger to revisit |
|---|---|
| GraphRAG / LazyGraphRAG | Logs show recurring multi-hop or corpus-wide aggregate questions failing (e.g., >5% of thumbs-down traffic). If triggered, start with a lightweight typed entity graph over `llm_entities` (§6) before full GraphRAG |
| Persistent cross-session user memory (MemGPT-style consolidation tiers) | Product adds returning-user personalization or unbounded sessions; bounded 5–30-turn support sessions don't need it (§6) |
| Stable-core-in-context (curated ~5–10k-token canonical-facts/glossary page in system prompt) | Traffic logs show heavy concentration on a small, stable FAQ core; pilot gated on golden set (§6) |
| HyDE | A distinct vague/underspecified-query tier underperforms after routing exists; golden-set-gated pilot only |
| ColBERT / SPLADE / late interaction | Migration off Azure AI Search, or a future Azure-native multi-vector capability |
| Embedding fine-tuning | Golden set shows systematic domain-vocabulary misses that synonym maps + contextual chunks don't fix |

### Governance (carried from Report A + data audit)
- Query-side LLM calls (rewrite, routing, agentic planning, groundedness checks) send user queries — and sometimes content — to model endpoints: route only through approved, region-compliant endpoints per enterprise data-handling policy.
- New components (router, reranker, agentic tier) inherit the same ACL trimming, observability, and audit logging as the core index; log which article *version* answered each query (regulated content).
- Preview features (agentic retrieval, query rewrite) stay behind flags with fallbacks; no SLA dependence.

---

## 5. Why this plan, in one paragraph

Head-to-head evidence says retrieval quality at this corpus scale is won in three places: **what you index** (contextually enriched, deduplicated, PII-safe chunks), **how you rank** (hybrid recall + a strong reranker at the right depth), and **how you route** (cheap path for the 80% of simple lookups, LLM-planned retrieval for the complex 20%). Everything else — HyDE, multi-query, GraphRAG, late interaction — either measured flat-to-negative on corpora like this one or costs platform-breaking complexity for single-digit gains, so it sits in a triggered backlog rather than the roadmap. The golden set goes in first, not last, because every number above was only discoverable through exactly that measurement loop.

## 6. Addendum (2026-06-12) — LLM-Wiki pattern sources evaluated

Three externally supplied sources (Karpathy's "LLM Wiki" gist, the nashsu/llm_wiki implementation, rohitg00's "LLM Wiki v2" gist) were deep-analyzed against this plan in [llm_wiki_pattern_analysis.md](llm_wiki_pattern_analysis.md), with key claims independently verified. Verdicts:

- **Wiki-as-replacement-for-RAG: ❌ skip.** Ruled out by the pattern's own decision tree — the corpus (~5M tokens) is ~50× past the ~100k-token context ceiling at which the wiki pattern wins. Consistent with the existing long-context verdict in §2.
- **The sources corroborate the backbone** (hybrid BM25+vector+RRF; "compile once" enrichment ≙ our `llm_summary`/`llm_faqs` work) and change no existing verdict.
- **One substantive adoption — multi-turn fast path** (new Phase 3 item 13): conversational query rewriting before retrieval + bounded-history policy (≤~15 turns full, rolling summary beyond). Evidence comes from published literature (QReCC, TopiOCQA, Lost in the Middle), not the sources themselves — the sources surfaced the gap.
- **Minor extensions**: contradiction flagging at ingest (Phase 0); version/publish-date logging at the groundedness gate (item 11).
- **Backlog additions**: persistent cross-session user memory (MemGPT-style tiers — wrong fit for bounded support sessions today); stable-core-in-context pilot; lightweight typed entity graph noted as the preferred first step if the GraphRAG trigger ever fires.
- Discarded as not applicable: LLM-authored-content quality gates, event hooks/self-healing lint (this KB is Salesforce-authored); an unverifiable "95.2% LongMemEval-S" figure was traced to a summarization artifact and excluded.

## Sources
Report B's source table applies ([retrieval_deep_research_report.md](retrieval_deep_research_report.md) §Sources): T2-RAGBench (arXiv 2604.01733, verified), Anthropic Contextual Retrieval, Azure hybrid/semantic-ranker/agentic-retrieval benchmarks, RAG-vs-GraphRAG systematic evaluation (arXiv 2502.11371), BenchmarkQED. Report A's source list (ColBERTv2, HyDE, RAG-Fusion, Self-RAG, GraphRAG originals) documents the techniques themselves. Addendum (§6) sources: Karpathy LLM Wiki gist, nashsu/llm_wiki, rohitg00 LLM Wiki v2 gist, QReCC (arXiv 2010.04898), Lost in the Middle (arXiv 2307.03172), MemGPT (arXiv 2310.08560), Azure agentic-retrieval docs — per-claim verification table in [llm_wiki_pattern_analysis.md](llm_wiki_pattern_analysis.md) §5.
