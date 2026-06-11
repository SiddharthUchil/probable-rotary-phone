# Beyond Vanilla RAG for Enterprise Knowledge Bases

## Executive Summary

Recent research shows that many production systems are moving beyond vanilla "vector + BM25" RAG toward techniques that improve recall, ranking quality, and factuality while respecting enterprise constraints such as ACLs, latency, and cost. Key improvements fall into four buckets: better retrievers (e.g., late interaction models like ColBERT), smarter query-side techniques (HyDE, RAG-Fusion), graph-augmented retrieval (GraphRAG), and self-reflective generator control (Self-RAG).  These are complementary rather than mutually exclusive and can be layered on top of an Azure AI Search–style hybrid index like the one described in your Excel analysis.[^1][^2][^3][^4][^5]

For an enterprise Salesforce Knowledge corpus with strong metadata, multi-language content, and production requirements, the highest-ROI next steps typically combine: (1) upgrading the retriever from pure dense embeddings to hybrid + late interaction where latency permits, (2) adding smarter query rewriting (HyDE or multi-query) with Reciprocal Rank Fusion, (3) introducing graph-based retrieval for entity- and relationship-heavy content, and (4) using Self-RAG-style controls to decide when and how to retrieve and to enforce evidence-supported generations.[^6][^2][^3][^4][^5][^1]

## Your Current State (From the Excel Analyses)

The `GBL1Summarydump_Addltags_06012026.xlsx` workbook is a Salesforce Knowledge dump with 4,867 article versions, rich structured metadata, and LLM-enriched fields (`llm_summary`, `llm_faqs`, `llm_category`, `llm_entities`).  Each row represents a full article body (HTML and Markdown), with IDs following Salesforce conventions and content paths suitable for citation in a chat UI.  Metadata spans business unit, product family, contact type, case type/subtype, system of record, language, privacy flags, versioning information, and LLM-derived categories and entities.[^7][^8]

The summary file already recommends a robust Azure AI Search schema: `SalesforceID` as the per-version key, cleaned Markdown as the main searchable content, vector fields for chunk-level embeddings, and filterable/facetable metadata for business unit, product, persona, language, and ACLs.  It also proposes hybrid retrieval with BM25 + vectors, semantic reranking, freshness scoring, synonym maps, parent–child retrieval (chunk → article), duplicate suppression, and security trimming as near-term improvements to the current RAG pipeline.[^8][^7]

In short, you already have a strong baseline hybrid RAG design focused on Azure AI Search with chunking, metadata filters, semantic ranking, and LLM reranking. The research question is: what more advanced retrieval and control techniques from the literature are worth adopting to significantly improve production relevance and faithfulness beyond this baseline, given enterprise constraints.

## Category 1: Better Retrievers (Beyond Single-Vector Dense)

### Late Interaction Models (ColBERTv2, Jina-ColBERT-v2)

Most current RAG implementations use single-vector dense retrievers (e.g., sentence-transformer, OpenAI embeddings) that represent each document/chunk as one embedding, which can lose fine-grained token-level signals and harm ranking for long, complex documents like knowledge articles. Late interaction models such as ColBERT and ColBERTv2 instead produce a small set of token-level embeddings per passage and compute relevance as a maximized interaction across tokens, which more closely approximates cross-encoder quality while remaining indexable at scale.[^9][^10][^1]

ColBERTv2 introduces aggressive residual compression and denoised supervision to reduce the space footprint by roughly 5–8× while maintaining or improving retrieval quality relative to earlier ColBERT versions.  A recent Jina-ColBERT-v2 extension generalizes this to multilingual and long-context settings using a modified XLM-Roberta backbone and Matryoshka Representation Loss, enabling flexible embedding dimensionalities (e.g., 64–768) with minimal impact on performance and up to 50 percent storage savings.[^11][^12][^13][^1][^9]

For your multilingual Salesforce knowledge base (en/fr) with long Markdown articles, a late interaction retriever could significantly improve passage-level recall and ranking, especially on longer queries and complex troubleshooting questions that hinge on specific phrases or entities.  Production trade-offs include increased index size (mitigated by compression), more complex infrastructure (custom index rather than purely Azure AI Search vectors), and latency considerations, though ColBERTv2-style implementations support efficient ANN search and have been used in real-world QA systems.[^14][^10][^7][^1][^11]

### Hybrid Keyword + Dense + Late Interaction

Your current design already embraces hybrid retrieval with BM25 + vector search fused by techniques like RRF, which is now considered best practice for many domains.  Late interaction can be layered into this stack as an additional retriever: BM25 handles exact keyword and acronym matches, dense vectors capture paraphrases and semantic similarity, and late interaction improves fine-grained matching within long passages.[^2][^8][^1][^14]

In practice, this often looks like a two-stage or three-stage pipeline: fast BM25+dense retrieval to narrow candidates to 500–1000 passages, followed by a ColBERT-style scorer to rerank the top N (e.g., 50–100) that are then passed to the generator.  This can yield better nDCG and Recall@k without exploding latency if carefully engineered and is particularly attractive for enterprise knowledge where recall of niche, long-tail content is critical.[^1][^14]

## Category 2: Smarter Query-Side Techniques (HyDE, RAG-Fusion)

### HyDE: Hypothetical Document Embeddings

HyDE (Hypothetical Document Embeddings) improves dense retrieval by prompting an LLM to generate a synthetic "ideal" answer document to the user query, then embedding that document and using its embedding for retrieval instead of (or in addition to) the raw query.  This approach was introduced to address the poor zero-shot generalization of many dense retrievers to new domains and query styles by letting the LLM expand the query into a rich, domain-like document before performing similarity search.[^3][^15][^16]

Implementation-wise, given a query like "How do I escalate a claim for product X in Quebec?", the system asks an LLM to output a detailed hypothetical article that answers this question in the style of your knowledge base, embeds that article (possibly averaging several generated variants), and then retrieves real documents whose embeddings are close to this synthetic one.  Experiments show that HyDE substantially improves zero-shot dense retrieval across web search, QA, and fact verification tasks, achieving performance closer to supervised retrievers even without labels.[^15][^16][^6][^3]

For your Salesforce corpus, HyDE is especially promising for: (1) short, underspecified queries from agents or customers, (2) long-tail topics that do not appear frequently in the training data of generic embedding models, and (3) multilingual queries where the LLM can produce a well-structured hypothetical document in the target language before retrieval.  The main downsides are additional latency and cost per query, since each request requires at least one extra LLM call, which may be acceptable for internal agent-assist use but not for very high-QPS public chatbots.[^8][^3][^15]

### RAG-Fusion: Multi-Query + Reciprocal Rank Fusion

RAG-Fusion extends basic RAG by generating multiple query variants from the original user query, retrieving results for each variant, and then combining those results with Reciprocal Rank Fusion (RRF) to surface documents that are consistently relevant across variants.  The method is designed to capture different facets of user intent (synonyms, related entities, sub-questions) and mitigate the fragility of single-query retrieval.[^17][^18][^2]

The algorithm typically follows these steps: use an LLM to generate several reformulations of the user query, embed each variant, retrieve top-k documents for each, then apply RRF to merge ranked lists into a single ranking, favoring documents that appear high across multiple lists.  Empirical evaluations report improved accuracy, relevance, and comprehensiveness of answers, though some answers can drift off-topic if the generated queries diverge too far from the original intent, which calls for careful prompt design and possibly constraint checks.[^18][^2][^17]

In your scenario, RAG-Fusion can leverage existing LLM-enriched fields like `llm_faqs` and `llm_entities` to guide query generation and to bias the fusion toward documents matching the inferred facets (e.g., product, persona, system of record).  The additional LLM usage and multiple retrieval passes increase cost and latency, so RAG-Fusion is often best used selectively: only for complex or ambiguous queries, or as part of a "slow path" for escalated sessions where answer quality is more important than speed.[^2][^18][^8]

## Category 3: Graph-Enhanced RAG (GraphRAG)

### What GraphRAG Adds

GraphRAG augments RAG with an explicit knowledge graph built from the corpus, capturing entities (e.g., products, business units, case types) and their relationships, and using graph algorithms plus LLM summarization to retrieve higher-level or multi-hop context.  Microsoft’s GraphRAG project demonstrates an end-to-end pipeline that extracts entities and relations using LLMs, constructs a graph, performs network analysis (e.g., community detection, centrality), and uses graph neighborhoods as retrieval units for generation.[^5][^19][^20]

The key idea is that in domains where relationships matter (e.g., product hierarchies, escalation workflows, jurisdiction-specific rules), retrieving along graph neighborhoods can produce more coherent and context-rich answers than independent chunk retrieval.  Graph-based retrieval also supports use cases like "What are all related policies for this benefit plan and jurisdiction?" or "Show me known issues that often co-occur with this error code," which are difficult to answer with flat chunk retrieval alone.[^19][^20][^5]

### Fit for Your Salesforce Knowledge Base

Your dataset already surfaces entities (`llm_entities`), categories (`llm_category`), product codes (`Forms_Plan_Number__c`), and rich metadata fields that are natural nodes and edges in a graph, making it well-suited to GraphRAG.  For example, nodes could include products, business units, contact types, and case subtypes, with edges representing "article mentions entity," "applies to product," or "handled by business unit."[^7][^5][^19][^8]

In production, GraphRAG can complement your existing Azure AI Search index rather than replace it: the graph is used to identify a subgraph (relevant entities and articles) given a query, then documents in that subgraph are retrieved and summarized with standard hybrid RAG.  This limits the computation overhead and lets you continue using Azure AI Search for indexing and filtering while benefiting from graph-aware retrieval for complex, multi-entity queries.[^5][^19]

## Category 4: Self-Reflective Retrieval Control (Self-RAG)

### Self-RAG: Retrieve, Generate, and Critique

Self-RAG (Self-Reflective Retrieval-Augmented Generation) trains a model to decide when to retrieve, how to use retrieved passages, and how to critique its own outputs using special reflection tokens.  Instead of always retrieving a fixed number of documents at the start of generation, Self-RAG predicts retrieval tokens during generation to request retrieval on demand and critique tokens to assess relevance, support, and usefulness of both retrieved passages and generated text.[^4][^21][^22][^23]

Experiments show that Self-RAG models (7B and 13B) outperform standard RAG setups and even proprietary models like ChatGPT on a range of open-domain QA, reasoning, and fact verification tasks, with notable gains in factuality and citation accuracy for long-form generation.  This framework allows inference-time customization: systems can weight critique tokens that prioritize evidence-backed responses versus fluency or completeness, enabling different behaviors for, say, customer-facing vs internal diagnostic bots.[^22][^23][^4]

### Applying Self-RAG Ideas Without Full Re-Training

Fully adopting Self-RAG requires training a custom LM with reflection tokens, which may not be feasible in an enterprise environment constrained to managed LLM services. However, the underlying ideas can be approximated with orchestration logic: use the LLM to first decide "Do I need retrieval for this query?" then route to RAG or direct answer path, and later ask the LLM to self-assess whether the draft answer is adequately supported by retrieved citations, possibly triggering another retrieval round.[^23][^24][^25]

Given your focus on enterprise security and ACL-aware retrieval, self-reflective controls can help reduce hallucinated answers that are not actually supported by any authorized article and can implement policies like "never answer without at least one grounded citation" or "escalate to human when support is weak."  This complements your existing plans for evaluation metrics (Recall@K, MRR, groundedness) by adding per-query, per-answer self-critique signals that can be logged and analyzed over time.[^22][^23][^8]

## Production Trade-offs and Architecture Patterns

### Latency vs Quality Tiers

Many of these advanced techniques (HyDE, RAG-Fusion, late interaction reranking, GraphRAG, Self-RAG) add computational overhead and additional LLM calls, so production systems often implement tiered architectures: a fast path for simple queries using standard hybrid retrieval, and a slow path for complex or high-stakes queries using richer techniques.  Query difficulty can be estimated via heuristics (length, presence of entities, prior failure) or via a small classifier.[^3][^18][^2][^22]

Your current plan to instrument Application Insights with query and chunk IDs is aligned with this approach, enabling analysis of which queries routinely fail or require manual escalation and thus should be routed to a more sophisticated retrieval stack.  For internal agent-assist scenarios where QPS is moderate and quality is paramount, you can afford more aggressive techniques (HyDE + RAG-Fusion + late interaction reranking) than for high-traffic public channels.[^8]

### Index and Model Governance

Introducing late interaction retrievers and GraphRAG will likely require new indexing pipelines and infrastructure beyond vanilla Azure AI Search vectors, such as external vector stores or custom ColBERT-compatible indexes and a graph database or GraphRAG pipeline.  These components must be integrated with your existing ingestion flow that cleans HTML, normalizes metadata, and enforces ACLs, and they must be subject to the same governance and observability standards you already outlined (owners, review cadence, effective dates, tracking fields).[^10][^19][^7][^1][^5][^8]

HyDE, RAG-Fusion, and Self-RAG-style orchestration also raise cost and privacy considerations because they involve sending queries and sometimes partial content to LLMs for query generation or critique, which must comply with your enterprise’s data-handling policies.  Careful prompt design is needed to avoid leaking sensitive identifiers, and some organizations restrict LLM calls for retrieval-side augmentation to approved, region-specific endpoints.[^25][^18][^3][^22]

## Recommendations Tailored to Your Context

### 1. Solidify the Baseline Hybrid RAG

Before adopting more exotic techniques, ensure that the recommended Azure AI Search–based hybrid retrieval is fully implemented: clean Markdown content, heading-aware chunking with parent–child mapping, BM25+vector hybrid search with tuned scoring profiles, synonym maps, freshness boosting, and strong ACL-based filters.  This alone typically yields substantial improvements over vector-only or naive RAG and sets a robust foundation for further enhancements.[^7][^8]

### 2. Pilot HyDE for Difficult Queries

Introduce HyDE as an optional query-expansion step for queries that are short, ambiguous, or repeatedly lead to low-confidence answers, using an internal LLM to generate hypothetical documents that are then embedded and used for retrieval.  Start with a small-scale A/B test limited to internal pilot users, measure improvements in Recall@k and grounded answer quality on your curated 300-query golden set, and monitor latency and cost impacts.[^16][^15][^3]

### 3. Add Multi-Query RAG-Fusion for Complex Tasks

Layer RAG-Fusion on top of your hybrid retriever for complex, multi-facet questions, using LLM-generated query variants and RRF to fuse results, with strong constraints to keep variants close to the original intent and incorporate metadata facets inferred from `llm_faqs` and `llm_entities`.  Again, gate this on query difficulty and evaluate via offline metrics and controlled user studies before rolling out widely.[^17][^18][^2][^8]

### 4. Evaluate Late Interaction Retrievers for High-Value Domains

Run a focused experiment with a ColBERTv2-style retriever (e.g., via FastEmbed or Jina-ColBERT-v2) on a subset of the knowledge base, such as a single business unit or high-impact product line, to assess gains in passage-level ranking against your golden set.  If the improvement is significant and latency acceptable, integrate it as a reranker over your top-k hybrid results or as a specialized retriever for long-form troubleshooting queries.[^13][^11][^10][^1]

### 5. Plan a GraphRAG Prototype Using Existing Entities

Leverage `llm_entities`, `llm_category`, product codes, and business unit metadata to construct a minimal knowledge graph and integrate GraphRAG-style retrieval for a narrow use case where relationships matter, such as cross-product dependencies or jurisdiction-specific policy bundles.  Measure whether graph-aware retrieval improves multi-hop query answers, and consider scaling up graph coverage if results are promising.[^20][^19][^5][^8]

### 6. Incorporate Self-RAG Principles in Orchestration

Even if you cannot train a full Self-RAG model, adopt its principles in your orchestration layer: ask the LLM to decide when retrieval is necessary, to rate whether its answer is supported by retrieved citations, and to abstain or escalate when support is weak.  Log these self-critique signals alongside your existing observability data to guide further tuning and to detect drift or emerging failure modes in production.[^4][^23][^25][^22]

## Conclusion

There are indeed more advanced retrieval and control techniques than a standard RAG stack, and several of them have demonstrated production viability in enterprise and domain-specific contexts. Late interaction retrievers, HyDE, RAG-Fusion, GraphRAG, and Self-RAG provide complementary ways to improve recall, ranking quality, and factuality, especially on complex, long-tail, or multi-hop queries.  Given your already-strong Azure AI Search–based design and rich Salesforce knowledge dataset, the most pragmatic path is to harden the baseline hybrid approach and then selectively pilot these techniques behind feature flags and A/B tests, focusing first on HyDE and RAG-Fusion for difficult queries, and late interaction and GraphRAG for specific high-value domains where their added complexity is justified by the quality gains.[^1][^2][^3][^4][^5]

---

## References

1. [[2112.01488] ColBERTv2: Effective and Efficient Retrieval ...](https://arxiv.org/abs/2112.01488) - In this work, we introduce ColBERTv2, a retriever that couples an aggressive residual compression me...

2. [RAG-Fusion: Enhanced Retrieval-Augmented Generation](https://aiengineering.academy/RAG/08_RAG_Fusion/) - Key Features of RAG-Fusion¶. Multi-query generation for comprehensive intent capture; Reciprocal Ran...

3. [What is HyDE (Hypothetical Document Embeddings) and ...](https://milvus.io/ai-quick-reference/what-is-hyde-hypothetical-document-embeddings-and-when-should-i-use-it) - HyDE (Hypothetical Document Embeddings) is a technique used in information retrieval to improve sear...

4. [Self-RAG: Learning to Retrieve, Generate, and Critique ...](https://arxiv.org/abs/2310.11511) - We introduce a new framework called Self-Reflective Retrieval-Augmented Generation (Self-RAG) that e...

5. [Project GraphRAG - Microsoft Research](https://www.microsoft.com/en-us/research/project/graphrag/) - GraphRAG (Graphs + Retrieval Augmented Generation) is a technique for richly understanding text data...

6. [Hypothetical Document Embeddings (HyDE)](https://docs.haystack.deepset.ai/docs/hypothetical-document-embeddings-hyde) - Enhance the retrieval in Haystack using HyDE method by generating a mock-up hypothetical document fo...

7. [rag_excel_knowledge_analysis.md](https://ppl-ai-file-upload.s3.amazonaws.com/web/direct-files/attachments/81585645/a3b5e58d-3bce-4d17-ae1f-3e74fb07d4e4/rag_excel_knowledge_analysis.md?AWSAccessKeyId=ASIA2F3EMEYEQLDD4DIT&Signature=pB6zq0NR2kajstbulVXqbEPRVYE%3D&x-amz-security-token=IQoJb3JpZ2luX2VjECUaCXVzLWVhc3QtMSJGMEQCIBsEr86ePYCnJuRNWIRh0p4nY0rI0YgdkoQrC3AtV4u8AiB%2FwgGTNCdQ5%2BYYO5MyrWpdK3NYVDWme35CMTDUtmZstyr8BAju%2F%2F%2F%2F%2F%2F%2F%2F%2F%2F8BEAEaDDY5OTc1MzMwOTcwNSIMzIwBZV0%2BzBsJLdI7KtAEl4jCPEefs7Yr%2BHz3BeleoRiXQyMkP5O2967e%2BORqaR2ZW7foyZlPMrbtaGraiD6HyczmiiaIyNXgCIZPVsVeDHETwUD25zGbVE7Pvkf6ss909hIbsM7hNwjARJBltNgWV0RfbgjJF%2BmQ7Wt0SXlS2N3SJ79P036urbAj7uq60I2E1X2SQKwMGkV4Z8FbzALAf9omtSmlvQNXNMQiTg9eeUSBHPQG0P3GFidPokhPXDMTjR1Nk7ytLLO6cV%2F4qwOCCXwB2ARTCaO5%2BTyVjzkPPAMVa5M3zBMT38LEDkZ6G6lgcYl%2FKpNHoYFcTCPB4KEmVcWXoQ7WzqmNFyDI7B1kjNQ1A5R9Mrm3NBdGhMKkhJs07FzFXCwMpPTW3UCMCPv6g%2F2%2FZ9MzbAU4fEBVXhPWbcYMsAo9cPyjbJ1bBjagNF5pF842C%2BkEfW0x8sMgeiINZs8JsRUu4xL1ndgBlfsfVIqxftmTECfNUbYFRPTorg1BAZ5i4BLswWrrW190B7zsnmI%2Fm4cmoJPxM4hJTPGrwdW%2F7NxpMzzJKI9hvWCUZVosHt6JBWY2XVL4Juf9%2BbqQkoFY9HwoSYQhLXCbBsV%2BhPu0Q%2BihrakFIjmQzxQLvhQzsiOiDX%2BQC1udoc0f9kwbeHowe1msxnVU0KMLQR3r6R%2BXPr0S85bjsheLIVlOarFW32amfBXeINbPdvt0PYF1Ovkm0D049F0G%2F29lact7TpjTI2EO1qu7rAl%2FDUoY0BG1xJEmf8RamNxuulQZ%2BeQ8jNlknnW%2BznREq0ZI2iV%2FZzCfoKfRBjqZAZrzVah8%2F5VvV4iGv00G7oOw5TneRkmRCk5duSEgG8%2B%2B18%2B7bZ7JjvRqF%2B3bAOZt9AnTwoAYGpZ5FEhLk%2FQ7SZJQRSE12gdXivym20kNS0dCFEy%2F%2F6UOAiPE2%2BTk6bdRdINi2fMNBOMa02TfYahH6AFIZwqV11UyaRXyYaPwDg6g2zCsS8H%2BTxgRJSKFAhZbo6PHzHoeqjv66w%3D%3D&Expires=1781128690) - # RAG Knowledge Base Analysis — `GBL1Summarydump_Addltags_06012026.xlsx`
_Generated 2026-06-10 16:28...

8. [rag_excel_analysis_summary.md](https://ppl-ai-file-upload.s3.amazonaws.com/web/direct-files/attachments/81585645/1be9ba1a-41fd-4e57-ae59-c5dd2d016162/rag_excel_analysis_summary.md?AWSAccessKeyId=ASIA2F3EMEYEQLDD4DIT&Signature=QBgNIBmHKOVE9x1MfoUJucJhRxQ%3D&x-amz-security-token=IQoJb3JpZ2luX2VjECUaCXVzLWVhc3QtMSJGMEQCIBsEr86ePYCnJuRNWIRh0p4nY0rI0YgdkoQrC3AtV4u8AiB%2FwgGTNCdQ5%2BYYO5MyrWpdK3NYVDWme35CMTDUtmZstyr8BAju%2F%2F%2F%2F%2F%2F%2F%2F%2F%2F8BEAEaDDY5OTc1MzMwOTcwNSIMzIwBZV0%2BzBsJLdI7KtAEl4jCPEefs7Yr%2BHz3BeleoRiXQyMkP5O2967e%2BORqaR2ZW7foyZlPMrbtaGraiD6HyczmiiaIyNXgCIZPVsVeDHETwUD25zGbVE7Pvkf6ss909hIbsM7hNwjARJBltNgWV0RfbgjJF%2BmQ7Wt0SXlS2N3SJ79P036urbAj7uq60I2E1X2SQKwMGkV4Z8FbzALAf9omtSmlvQNXNMQiTg9eeUSBHPQG0P3GFidPokhPXDMTjR1Nk7ytLLO6cV%2F4qwOCCXwB2ARTCaO5%2BTyVjzkPPAMVa5M3zBMT38LEDkZ6G6lgcYl%2FKpNHoYFcTCPB4KEmVcWXoQ7WzqmNFyDI7B1kjNQ1A5R9Mrm3NBdGhMKkhJs07FzFXCwMpPTW3UCMCPv6g%2F2%2FZ9MzbAU4fEBVXhPWbcYMsAo9cPyjbJ1bBjagNF5pF842C%2BkEfW0x8sMgeiINZs8JsRUu4xL1ndgBlfsfVIqxftmTECfNUbYFRPTorg1BAZ5i4BLswWrrW190B7zsnmI%2Fm4cmoJPxM4hJTPGrwdW%2F7NxpMzzJKI9hvWCUZVosHt6JBWY2XVL4Juf9%2BbqQkoFY9HwoSYQhLXCbBsV%2BhPu0Q%2BihrakFIjmQzxQLvhQzsiOiDX%2BQC1udoc0f9kwbeHowe1msxnVU0KMLQR3r6R%2BXPr0S85bjsheLIVlOarFW32amfBXeINbPdvt0PYF1Ovkm0D049F0G%2F29lact7TpjTI2EO1qu7rAl%2FDUoY0BG1xJEmf8RamNxuulQZ%2BeQ8jNlknnW%2BznREq0ZI2iV%2FZzCfoKfRBjqZAZrzVah8%2F5VvV4iGv00G7oOw5TneRkmRCk5duSEgG8%2B%2B18%2B7bZ7JjvRqF%2B3bAOZt9AnTwoAYGpZ5FEhLk%2FQ7SZJQRSE12gdXivym20kNS0dCFEy%2F%2F6UOAiPE2%2BTk6bdRdINi2fMNBOMa02TfYahH6AFIZwqV11UyaRXyYaPwDg6g2zCsS8H%2BTxgRJSKFAhZbo6PHzHoeqjv66w%3D%3D&Expires=1781128690) - # RAG Excel Analysis — Executive Summary
_Companion to `rag_excel_knowledge_analysis.md`. Source: `G...

9. [Effective and Efficient Retrieval via Lightweight Late ...](https://jonsaadfalcon.com/papers/colbert_v2) - We introduce ColBERTv2, a retriever that couples an aggressive residual compression mechanism with a...

10. [How to Generate ColBERT Multivectors with FastEmbed](https://qdrant.tech/documentation/fastembed/fastembed-colbert/) - Late interaction models, such as ColBERT, only do the first part, generating document and query part...

11. [A General-Purpose Multilingual Late Interaction Retriever](https://aclanthology.org/2024.mrl-1.11/) - In this paper, we introduce a novel architecture and a training framework to support long context wi...

12. [Jina-ColBERT-v2 : A General-Purpose Multilingual Late ...](https://www.youtube.com/watch?v=NtYr4nfndDM) - This paper introduces Gina Colbert V2 which is a multilingual late interaction retriever that improv...

13. [late interaction retrievers - a jinaai Collection](https://huggingface.co/collections/jinaai/late-interaction-retrievers) - This collection list our ColBERT like late interaction retriever models.

14. [An Overview of Late Interaction Retrieval Models](https://weaviate.io/blog/late-interaction-overview) - For example, in ColBERTv2, aggressive quantization is performed to reduce the size of each vector fr...

15. [Precise Zero-Shot Dense Retrieval without Relevance Labels](https://arxiv.org/abs/2212.10496) - Given a query, HyDE first zero-shot instructs an instruction-following language model (e.g. Instruct...

16. [Precise Zero-Shot Dense Retrieval without Relevance Labels](https://aclanthology.org/2023.acl-long.99/) - In this paper, we recognize the difficulty of zero-shot learning and encoding relevance. Instead, we...

17. [RAG-Fusion: multi-query generation + Reciprocal Rank ...](https://github.com/Raudaschl/rag-fusion) - Query Generation — Takes a user's query and uses OpenAI's GPT to generate multiple search query vari...

18. [[2402.03367] RAG-Fusion: a New Take on Retrieval- ...](https://arxiv.org/abs/2402.03367) - RAG-Fusion combines RAG and reciprocal rank fusion (RRF) by generating multiple queries, reranking t...

19. [microsoft/graphrag: A modular graph-based Retrieval ...](https://github.com/microsoft/graphrag) - The GraphRAG project is a data pipeline and transformation suite that is designed to extract meaning...

20. [What is GraphRAG?](https://www.ibm.com/think/topics/graphrag) - GraphRAG is an advanced version of retrieval-augmented generation (RAG) that incorporates graph-stru...

21. [SELF-RAG: LEARNING TO RETRIEVE, GENERATE, AND ...](https://proceedings.iclr.cc/paper_files/paper/2024/file/25f7be9694d7b32d5cc670927b8091e1-Paper-Conference.pdf) - This work introduces Self-Reflective Retrieval-augmented Generation (SELF-RAG) to improve an ... Ra-...

22. [Self-RAG: Learning to Retrieve, Generate and Critique ...](https://selfrag.github.io) - Self-Reflective Retrieval-Augmented Generation (Self-RAG) is a new framework to enhances an LM's qua...

23. [Self-RAG](https://learnprompting.org/docs/retrieval_augmented_generation/self-rag) - Self-RAG is a breakthrough approach for making AI more factually accurate, reliable, and controllabl...

24. [Self-Reflective Retrieval-Augmented Generation (SELF-RAG)](https://www.kore.ai/blog/self-reflective-retrieval-augmented-generation-self-rag) - SELF-RAG learns to retrieve, critique and generate text passages to enhance overall generation quali...

25. [Self-RAG explained: how self-reflective retrieval boosts AI ...](https://www.meilisearch.com/blog/self-rag) - Self-RAG is an evolution of retrieval-augmented generation (RAG) that enables models to self-reflect...

