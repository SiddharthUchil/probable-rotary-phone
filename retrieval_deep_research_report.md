# Deep Research: Production Retrieval Techniques vs. Your RAG Baseline
_Synthesized 2026-06-10. Companion to `rag_excel_knowledge_analysis.md` / `rag_excel_analysis_summary.md` (Salesforce Knowledge corpus, ~4,867 article versions, EN/FR, Azure AI Search)._

## Methodology & confidence

A 5-angle research fan-out (comparative benchmarks, Azure-specific, practitioner case studies, multilingual/domain-specific, contrarian "is RAG dead at small scale") gathered 21 sources and 105 claims; the top 25 were selected for adversarial verification. The automated verification pass was rate-limited, so verification was completed manually:

- **Verified** = re-fetched from the primary source today, numbers checked against the published tables.
- **High confidence** = primary source whose published numbers match independently known values.
- **Medium confidence** = consistent with the publisher's headline claims, but the full source body was not re-fetched (JS-rendered page); figures come from the research agents' original fetch.

---

## 1. The headline answer

**Your planned stack — hybrid BM25+vector with RRF, topped by a strong reranker — is the validated production architecture, not a baseline to be replaced.** Across every rigorous comparison found, two-stage *hybrid retrieval + neural reranking* beats every exotic single-stage alternative, usually by a wide margin. The biggest improvements available to you are not architecture swaps; they are:

1. **Contextual (situated) chunk enrichment at ingest** — the single clearest, cheapest, most consistently replicated lift not yet in your near-term plan.
2. **Query-class routing** — every technique's value is query-class dependent; spend the expensive techniques (agentic retrieval, multi-query) only on complex queries, where they earn +16 points, and skip them on simple lookups, where they earn ~0.
3. **Reranker quality and depth** — the largest single lever in the whole stack (R@5 0.695 → 0.816 in the closest benchmark analog).

And one **caution flag**: HyDE measurably *hurts* retrieval on fact-dense corpora like insurance/benefits content. It should be demoted from your roadmap, not piloted.

---

## 2. Evidence by technique, ranked by lift-per-complexity for THIS corpus

### Tier 1 — Adopt (high lift, low-to-medium complexity, strong evidence)

#### 2.1 Hybrid + semantic reranker (VERIFIED — your roadmap is right)
The closest published analog to your corpus is **T2-RAGBench** (23,088 queries over 7,318 mixed text-and-table financial documents — fact-dense, numeric, enterprise-style, like benefits plans). Ten retrieval strategies benchmarked, all differences significant at p<0.001 ([arXiv 2604.01733](https://arxiv.org/html/2604.01733v1)):

| Method | Recall@5 | MRR@3 |
|---|---|---|
| HyDE (gpt-4.1-mini) | 0.544 | 0.318 |
| Dense (text-embedding-3-large) | 0.587 | 0.351 |
| Multi-Query + RRF | 0.640 | 0.397 |
| BM25 | 0.644 | 0.411 |
| Corrective RAG (CRAG) | 0.658 | 0.415 |
| Hybrid (BM25+Dense, RRF) | 0.695 | 0.433 |
| Contextual Hybrid | 0.717 | 0.454 |
| **Hybrid + Cohere Rerank** | **0.816** | **0.605** |

Three findings to internalize:
- **BM25 beat state-of-the-art dense retrieval** on this document type. Your synonym-map and analyzer investments are not legacy work — lexical signal carries fact-dense corpora (plan numbers, acronyms, codes).
- **The reranker is the largest single lever**: +0.121 Recall@5 over hybrid alone. Reranker candidate depth materially matters (ablated in the paper) — tune how many candidates you pass to it (50–100, not 10).
- Single-stage "smart" methods (CRAG, multi-query, HyDE) all lose to dumb hybrid + good reranker.

Microsoft's own numbers agree (high confidence, [Azure hybrid+ranking blog](https://techcommunity.microsoft.com/t5/ai-azure-ai-services-blog/azure-ai-search-outperforming-vector-search-with-hybrid/ba-p/3929167)): on customer datasets, NDCG@3 = 40.6 keyword / 43.8 vector / 48.4 hybrid / **60.1 hybrid + semantic ranker**. On the multilingual MIRACL benchmark: NDCG@10 = **72.0** hybrid+ranker vs 58.8 hybrid alone — the ranker (adapted from Bing's models) is multilingual, directly relevant to your EN/FR split. The Nov-2024 semantic ranker generation roughly **doubled** the gain of the prior generation (~+22 NDCG@3 over hybrid vs ~+13 before), and Microsoft's MIML benchmark (70 indexes, 10 segments × 7 languages, including an explicit Insurance segment) showed **French +20.4 avg and Insurance/French +23.0** (medium-high confidence, [QR+SR blog](https://techcommunity.microsoft.com/blog/azure-ai-foundry-blog/raising-the-bar-for-rag-excellence-query-rewriting-and-new-semantic-ranker/4302729)). If you benchmarked Azure's ranker before late 2024, those results are stale — re-test.

**Action**: keep hybrid+RRF+semantic ranker as the backbone; A/B the Azure semantic ranker vs. an external cross-encoder (Cohere Rerank 3.5 is multilingual and strong on enterprise text) on your golden set; ablate reranker input depth.

#### 2.2 Contextual retrieval / situated chunks (VERIFIED — pull forward from "long-term" to "now")
Two independent confirmations:
- **Anthropic** (high confidence, [contextual retrieval post](https://www.anthropic.com/news/contextual-retrieval)): prepending 50–100 tokens of LLM-generated document context to each chunk before embedding *and* BM25 indexing cut top-20 retrieval failure rate from 5.7% → 2.9% (contextual embeddings + contextual BM25), and → **1.9% with a reranker stacked on top (67% total failure reduction)**.
- **T2-RAGBench** (verified): Contextual Hybrid 0.717 vs vanilla Hybrid 0.695 Recall@5; the paper's conclusion: "contextual retrieval provides consistent gains" while query-expansion methods don't.

This is exceptionally cheap for you: **~4,900 articles, and you already have `llm_summary`, `Title`, `Business_Unit`, and `llm_category` per article.** A minimal version — prepend title + article summary + category breadcrumb to every chunk before embedding and indexing — needs no new LLM enrichment at all. A fuller version (chunk-specific situating sentence generated per chunk with prompt caching) is a one-time job costing dollars, not thousands, at this scale. Your analysis filed this under "Long-Term Strategic Enhancements (2–4 quarters)"; the evidence says it belongs in the first sprint after chunking.

### Tier 2 — Route, don't blanket-apply

#### 2.3 Agentic retrieval (Azure-native) — for complex queries only
Microsoft's benchmark of Azure AI Search **agentic retrieval** (LLM query planning → parallel subqueries → per-subquery hybrid + semantic rank → merge) vs. exactly your planned stack (hybrid + text-embedding-3-large + semantic ranker) found (medium confidence, [agentic retrieval engine blog](https://techcommunity.microsoft.com/blog/azure-ai-foundry-blog/up-to-40-better-relevance-for-complex-queries-with-new-agentic-retrieval-engine/4413832), [docs](https://learn.microsoft.com/en-us/azure/search/agentic-retrieval-overview)):
- Complex multi-document queries: **+16 points answer relevance on average** (up to +33 on one dataset); ~40% relative on the hardest set.
- **Classical/simple queries: +0.03 points — i.e., nothing.**
- French complex queries: +10.26 points; French classical: +0.84.

The lesson is not "adopt agentic retrieval"; it's **query-class routing**. Classify incoming queries (simple lookup vs. complex/conversational/multi-part — a cheap classifier or heuristics on length/entities/turn-count). Send ~80% of traffic down the fast hybrid+ranker path; send the complex tail to agentic retrieval. This also caps the cost/latency that agentic retrieval adds. Caveat: the feature is preview — wrap it behind a flag and a fallback.

#### 2.4 Query rewrite & multi-query — keep, but expect little
Once hybrid + the new semantic ranker are in place, Microsoft's own data shows generative query rewriting adds **0.0–1.7 points NDCG@3** on hybrid configurations — its wins concentrate on text-only indexes (+4) and hard/misspelled/keyword-code queries (+6) (medium confidence, QR+SR blog). T2-RAGBench agrees: multi-query ≈ plain BM25 (0.640 vs 0.644). Verdict: a targeted fallback for failed/hard queries, not a default pipeline stage. Note Azure's native query-rewrite is **preview, not recommended for production**, and requires `queryLanguage` per request (fr-FR/fr-CA supported) — so your pipeline must detect query language first ([docs](https://learn.microsoft.com/en-us/azure/search/semantic-how-to-query-rewrite)).

#### 2.5 HyDE — demote (evidence says it can hurt here)
On the fact-dense T2-RAGBench corpus, HyDE scored **0.544 Recall@5 vs 0.587 for plain dense retrieval** — the hypothetical documents hallucinate plausible-but-wrong specifics (plan numbers, amounts, dates), which drags retrieval toward wrong neighborhoods (verified). HyDE's published wins are on open-web, underspecified queries — not numeric enterprise content. Your corpus (plan numbers, coverage amounts, procedure codes) is exactly the failure case. Keep it out of the default path; if ever tested, gate to short/vague queries only and measure against the golden set first.

### Tier 3 — Skip or defer for this corpus

#### 2.6 GraphRAG / RAPTOR — defer
The systematic RAG-vs-GraphRAG evaluation (high confidence, [arXiv 2502.11371](https://arxiv.org/html/2502.11371v3)) found:
- **Vanilla RAG beats GraphRAG variants on single-hop, fact-based QA** (Natural Questions: RAG 64.78 F1 vs 63.01 Community-GraphRAG vs 60.04 RAPTOR) and on detail-oriented questions (55.28 vs 46.88).
- GraphRAG wins only on multi-hop reasoning, by ~2–3 points.
- **Index construction is ~40–57× more expensive** (135s vs 5,560–7,702s on the same corpus), and quality depends on the graph-construction model.

Your corpus is overwhelmingly single-hop FAQ/procedure lookups. The expected lift is negative-to-tiny for a heavy ongoing infra cost. **LazyGraphRAG** is the interesting exception — in Microsoft's BenchmarkQED it won all 96 pairwise comparisons (medium confidence, [BenchmarkQED blog](https://www.microsoft.com/en-us/research/blog/benchmarkqed-automated-benchmarking-of-rag-systems/)) — but its advantage concentrates on *global/aggregative* questions ("summarize everything about X across the KB"), which a support chatbot rarely receives. Revisit only if production logs show real multi-hop or corpus-wide aggregate queries failing.

#### 2.7 Long-context stuffing instead of RAG — no
In BenchmarkQED, expanding vector RAG's context from 8k → 120k tokens didn't improve outcomes, and even a 1M-token condition lost to structured retrieval (medium confidence). Your corpus is also simply too big (median article 5,880 chars × 4,867 versions ≫ any context window), and "context rot" research shows quality degrades as windows fill. RAG is not being displaced at your scale; the displacement story applies to <100-document corpora.

#### 2.8 Late interaction (ColBERT) / learned sparse (SPLADE) — not on this platform
No Azure AI Search-native support; adopting them means running a second retrieval engine and reconciling ACL trimming across both. T2-RAGBench explicitly left ColBERT as future work — there is no evidence it beats hybrid+rerank on your document type, while the reranker already gives you cross-encoder-quality scoring at the top of the funnel. Skip unless you migrate off Azure AI Search.

#### 2.9 Self-RAG — take the idea, not the architecture
Self-RAG's published gains require training a custom model with reflection tokens — impractical against managed LLMs. The transferable 20%: orchestration-level groundedness checks ("is this answer supported by a retrieved citation? if not, retry or escalate"), which you already planned via faithfulness evaluation and escalation paths.

---

## 3. Verdict on your existing roadmap

| Roadmap item | Verdict | Evidence |
|---|---|---|
| Hybrid BM25+vector + RRF | ✅ **Validated** — backbone of every winning config | T2-RAGBench, Azure benchmarks |
| Azure semantic reranker | ✅ **Validated, undersold** — biggest single lever; re-test post-Nov-2024 ranker | +22 NDCG@3; FR +20.4; Insurance/FR +23.0 |
| Synonym maps, analyzers, scoring profiles | ✅ **Validated** — BM25 signal matters more than assumed on fact-dense content | BM25 > dense, T2-RAGBench |
| Heading-aware chunking, parent–child | ✅ **Validated** (uncontested best practice) | — |
| LLM rerank of top-20 | ✅ Validated, but **ablate depth** — top-20 may be too shallow; paper shows depth matters | T2-RAGBench ablations |
| Contextual retrieval (was "long-term") | ⬆️ **Pull forward to next sprint** — cheapest verified lift; you already have the enrichment fields | Anthropic 67% failure reduction; T2-RAGBench +2.2pp |
| HyDE | ⬇️ **Demote/remove** — measured harm on fact-dense corpora | 0.544 vs 0.587 R@5 |
| Multi-query expansion | ⬇️ **Demote to fallback** — ≈0 lift once hybrid+ranker in place | 0.640 vs 0.644; +0.0–1.7 NDCG@3 |
| Query rewrite | ⚠️ Keep for hard/misspelled queries only; Azure native version is preview-only | +4–6 on hard queries, ~0 otherwise |
| GraphRAG (long-term) | ❌ **Drop for now** — negative-to-tiny lift on single-hop corpora, 40–57× indexing cost | arXiv 2502.11371 |
| Agentic orchestrator (long-term) | ✅ but **scope it**: Azure agentic retrieval for complex-query tier only | +16 complex / +0.03 simple |
| Golden-set eval (Recall@K, MRR, nDCG, faithfulness) | ✅ **Validated** — every cited gain was found via exactly this measurement loop | all sources |

## 4. What's missing from the roadmap

1. **Query-class routing** — the meta-finding of this research: no technique wins everywhere; value is query-class dependent (simple/local vs complex/multi-part vs aggregate). A cheap classifier in front of the retriever, with per-class A/B measurement, is the highest-leverage *architectural* addition. Stratify your golden set by these classes (you already stratify by category/persona — add complexity).
2. **FAQ-as-index (doc2query you already paid for)** — `llm_faqs` holds ~4,600 article-level question sets. Index each FAQ question as a searchable + vector field mapped to its article. Query-to-question matching is a strong channel for FAQ-style corpora and costs you nothing new. (Practitioner-grade evidence, not benchmark-verified — measure it.)
3. **Cross-language retrieval decision** — per-language analyzers handle FR-queries→FR-articles, but decide explicitly whether an EN query should retrieve a FR-only article (multilingual embeddings + multilingual reranker make this possible; BM25 won't). The MIRACL result says the Azure ranker holds up multilingually; your embedding model choice must too.
4. **Reranker depth + model ablation** — depth-of-candidates and reranker choice (Azure semantic vs Cohere 3.5 vs LLM-rerank) are measurable, high-impact knobs; treat them as first-class golden-set experiments.

## 5. Note on the Perplexity report in this folder

`Beyond Vanilla RAG for Enterprise Knowledge Bases.md` is a reasonable technique survey, but three of its recommendations are contradicted by the benchmark evidence above: it recommends **piloting HyDE** (measured harm on fact-dense corpora), **RAG-Fusion/multi-query** (≈0 lift over hybrid+ranker), and **ColBERT adoption** (no Azure-native path, no evidence of lift over hybrid+rerank for this document type). Its GraphRAG enthusiasm should also be tempered by the 40–57× indexing cost and single-hop results. Its strongest advice — harden the hybrid baseline first — matches this report.

## 6. Recommended sequence

1. **Sprint 1–2**: chunking + contextual enrichment at ingest (title/summary/category prefix per chunk) → hybrid + current-gen semantic ranker → golden set live (stratified by query class).
2. **Sprint 3–4**: reranker ablation (depth, Azure vs Cohere 3.5); index `llm_faqs` as a retrieval channel; latest-version filter, synonym maps, freshness profile (already planned).
3. **Quarter 2**: query-class router; agentic retrieval (preview, flagged) for the complex tier; query-rewrite fallback for hard/failed queries.
4. **Backlog (re-open only on log evidence)**: GraphRAG/LazyGraphRAG, late interaction, HyDE.

---

## Sources

| Source | Type | Confidence |
|---|---|---|
| [T2-RAGBench: From BM25 to Corrective RAG (arXiv 2604.01733)](https://arxiv.org/html/2604.01733v1) | Benchmark paper, 23k queries | Verified today |
| [Anthropic — Introducing Contextual Retrieval](https://www.anthropic.com/news/contextual-retrieval) | Vendor research post | High |
| [Azure AI Search: Outperforming vector search with hybrid + ranking](https://techcommunity.microsoft.com/t5/ai-azure-ai-services-blog/azure-ai-search-outperforming-vector-search-with-hybrid/ba-p/3929167) | Vendor benchmark | High |
| [Raising the bar for RAG: query rewriting + new semantic ranker](https://techcommunity.microsoft.com/blog/azure-ai-foundry-blog/raising-the-bar-for-rag-excellence-query-rewriting-and-new-semantic-ranker/4302729) | Vendor benchmark (MIML incl. Insurance/FR) | Medium-high |
| [Up to 40% better relevance with agentic retrieval engine](https://techcommunity.microsoft.com/blog/azure-ai-foundry-blog/up-to-40-better-relevance-for-complex-queries-with-new-agentic-retrieval-engine/4413832) | Vendor benchmark | Medium |
| [RAG vs. GraphRAG: A Systematic Evaluation (arXiv 2502.11371)](https://arxiv.org/html/2502.11371v3) | Academic evaluation | High |
| [Microsoft Research — BenchmarkQED (LazyGraphRAG)](https://www.microsoft.com/en-us/research/blog/benchmarkqed-automated-benchmarking-of-rag-systems/) | Vendor research | Medium |
| [Azure agentic retrieval overview](https://learn.microsoft.com/en-us/azure/search/agentic-retrieval-overview) · [query rewrite docs](https://learn.microsoft.com/en-us/azure/search/semantic-how-to-query-rewrite) · [relevance overview](https://learn.microsoft.com/en-us/azure/search/search-relevance-overview) | Official docs | High |

_Vendor-benchmark caveat: Microsoft and Anthropic numbers are self-reported on their own platforms/datasets; the independent T2-RAGBench and RAG-vs-GraphRAG papers are the strongest neutral evidence, and they point the same direction._
