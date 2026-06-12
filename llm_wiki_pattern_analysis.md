# LLM-Wiki Pattern Analysis — Applicability to the Enterprise RAG Use Case
_2026-06-12. Evaluates three externally supplied sources against the consolidated plan in `FINAL_retrieval_strategy.md` (4,900-article Salesforce Knowledge corpus, EN/FR, Azure AI Search, single-hop **and** multi-turn). Key claims were independently verified before judging; see §5._

---

## 1. What the three sources are

All three describe **one pattern through three lenses** — they are not three independent strategies:

| Source | Nature | Evidence character |
|---|---|---|
| [Karpathy gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) — "LLM Wiki" (Apr 2026, authorship verified) | Pattern essay: instead of retrieve-at-query-time RAG, have an LLM agent **compile and maintain a persistent markdown wiki**, then answer from context | Intentionally abstract; no benchmarks; one concrete decision tree (below) |
| [nashsu/llm_wiki](https://github.com/nashsu/llm_wiki) | Desktop implementation of the pattern: ingest pipeline, 4-signal knowledge graph, hybrid BM25+LanceDB vector+RRF, multi-turn chat, human-review write gates | Self-reported benchmark only (recall 58.2%→71.4% with vector enabled; README, no corpus stated) |
| [rohitg00 gist](https://gist.github.com/rohitg00/2067ab416f7bbe447c1977edaaa681e2) — "LLM Wiki v2" | Extension essay adding **memory-lifecycle** ideas from agent-memory systems: confidence decay, supersession, consolidation tiers (working→episodic→semantic→procedural), typed relationships, event hooks | No benchmarks; credibility rests on the author's agentmemory project (22.5K GitHub stars, verified) |

**The pattern's own scope statement** (Karpathy's decision tree, corroborated by independent commentary):
- **< ~50–100k tokens**: wiki-in-context wins — 100% retrieval reliability, no chunking loss, global reasoning, near-zero infra.
- **Millions of tokens**: **RAG mandatory**.
- **In between / production**: hybrid — stable core knowledge in context, dynamic/large corpora behind RAG.

## 2. The scale decision — wiki-as-replacement is ruled out by the sources themselves

At a conservative ~1,000 tokens/article, the 4,900-article corpus is **~5M tokens — roughly 50× past the pattern's own ~100k ceiling**, before counting versions and bilingual duplication. Karpathy's decision tree places this use case unambiguously in "RAG mandatory" territory. This is also consistent with two verdicts already locked in `FINAL_retrieval_strategy.md` §2: long-context-instead-of-RAG measured flat (8k→120k no help), and CAG/context-stuffing degrades via context rot at scale.

**Conclusion: nothing in these sources challenges the finalized architecture.** The repo's own retrieval stack (BM25 + vector + RRF, with qmd's hybrid+rerank design endorsed in the gist) *is* the finalized backbone, independently re-invented at personal scale. The sources' value to this project is therefore not "replace RAG" but a handful of component ideas — almost all on the **multi-turn axis**, where the finalized plan is thinnest.

## 3. Technique-by-technique applicability

| Technique (source) | Single-hop relevance | Multi-turn relevance | Evidence grade | **Verdict** |
|---|---|---|---|---|
| Wiki replaces RAG / corpus-in-context (Karpathy) | None — corpus 50× over the pattern's own threshold | Same | Verified (the sources' own decision tree excludes us) | ❌ **Skip** |
| Hybrid BM25+vector+RRF (gist's qmd; repo's stack) | Corroborates existing backbone | Same | Self-reported (repo 58.2%→71.4% recall, conditions unstated); our T2-RAGBench/Azure evidence is stronger | ✅ **Already adopted — no change** |
| **Conversational query contextualization** (the production translation of the sources' "context injection"/memory ideas) | None | **High — the gap.** Fast path (~80% of traffic) does zero turn contextualization; "what about for Quebec?" retrieves nothing useful. Slow path handles history only via agentic retrieval | **Strong, from literature not the sources**: TREC CAsT / QReCC / TopiOCQA; rewriting beats naive history-concatenation, ~10–20% retrieval gains | ✅ **Adopt — new Phase 3 item** (see §4) |
| Bounded-history policy + session summarization (rohitg00 episodic compression) | None | Medium — guards long sessions | "Lost in the Middle" (arXiv 2307.03172, peer-reviewed): mid-context degradation is real; summarization unquantified for bounded support sessions | ✅ **Adopt as policy, not infrastructure**: full history ≤ ~15 turns; summarize older turns beyond that. No memory store needed |
| Memory consolidation tiers, confidence decay, forgetting curve (rohitg00) | None | Low for bounded 5–30-turn support sessions — designed for unbounded, long-lived personal agents (MemGPT lineage, arXiv 2310.08560) | Conceptually grounded (MemGPT), but zero evidence for short-session support bots | ⬇️ **Backlog, named trigger**: product adds persistent cross-session user memory / returning-user personalization |
| Typed knowledge graph; 4-signal relevance: direct links ×3.0, source overlap ×4.0, Adamic-Adar ×1.5, type affinity ×1.0 (repo) | Low — single-hop FAQ wins are already captured by hybrid+rerank | Medium — entity-link traversal serves multi-hop | Self-reported weights, no ablation published | ⬇️ **Folds into the existing GraphRAG backlog row** — if that trigger ever fires, this lightweight typed-graph (no community summaries, ~none of GraphRAG's 40–57× indexing cost) is the preferred first implementation, seeded from existing `llm_entities` |
| Supersession + provenance metadata (repo "never delete", rohitg00 confidence fields) | Low — largely already covered: latest-`VersionNumber` filter, freshness scoring profile, version logging in governance | Low | Reasonable practice; no numbers | ✅ **Minor extension only**: surface article version + `LastPublishedDate` to the groundedness gate so stale-version citations are detectable in logs. No new fields beyond what's indexed |
| Contradiction flagging at ingest (Karpathy/repo) | Low-medium — corpus has known near-duplicates | Low | Opinion / design practice | ✅ **Cheap Phase 0 extension**: when near-duplicate suppression finds conflicting articles, route to content owners instead of suppressing silently |
| Stable-core-in-context hybrid (Karpathy's "in between" bullet) | Low-medium — a curated ~5–10k-token canonical-facts/glossary page in the system prompt could short-circuit top-FAQ traffic | Low | Pattern-level only; CAG verdict already warns against large-scale stuffing | ⬇️ **Backlog, named trigger**: traffic logs show heavy concentration on a small stable FAQ core; pilot gated on golden set |
| Event hooks, self-healing lint, human write-review gates (repo/rohitg00) | N/A — these govern **LLM-authored** wiki content; this KB is Salesforce-authored with its own editorial workflow | N/A | — | ❌ **Not applicable** (the analog — ingest pipeline + golden-set CI gate — is already in the plan) |
| "Compile once, reuse" knowledge compilation (all three) | Already paid for: `llm_summary` / `llm_faqs` / `llm_category` enrichment is exactly this pattern applied per-article | Same | — | ✅ **Already adopted** — the sources validate the contextual-enrichment sprint |

## 4. Amendments accepted into FINAL_retrieval_strategy.md

1. **New Phase 3 item — multi-turn conversation handling on the fast path** (the one substantive addition):
   - Rewrite the latest user turn into a self-contained query using conversation history *before* retrieval (LLM rewrite, cheap model; the language-detect step already in front feeds it).
   - History policy: full history to the generator up to ~15 turns; rolling summary of older turns beyond that. No persistent memory store.
   - The agentic slow path already receives conversation history natively (`messages` parameter, verified in Azure docs 2026-06) — this item closes the gap for the ~80% fast path.
2. **Groundedness gate logging** gains article version + publish date (minor extension of existing items 7/11).
3. **Phase 0 extension**: contradiction flagging to content owners during near-duplicate suppression.
4. **Backlog rows added**: persistent cross-session user memory (MemGPT-style tiers); stable-core-in-context pilot. **GraphRAG row annotated**: lightweight typed-graph is the preferred first implementation if triggered.

Everything else in the three sources is either already in the plan, ruled out by scale, or not applicable to a non-LLM-authored corpus. No existing verdict in FINAL changes.

## 5. Claim verification appendix

| Claim | Status | Note |
|---|---|---|
| Karpathy gist authorship & ~Apr 2026 date | **Verified** | Independent commentary (gist comments, HN, derivative tools incl. qmd, 26.4K stars) confirms |
| Context-beats-RAG below ~50–100k tokens | **Plausible, directionally supported** | Independent practitioner commentary concurs; no formal A/B. Irrelevant to our scale either way |
| Repo recall 58.2%→71.4% with vector enabled | **Verified as self-reported** | README only; corpus, query set, and metric definition unstated; no reproduction |
| "95.2% on LongMemEval-S" (appeared in initial source digest) | **Untraceable — do not cite** | Found in neither the repo nor the gist; treated as a summarization artifact and excluded from all verdicts |
| agentmemory "20K+ stars" | **Verified** | 22,490 stars at github.com/rohitg00/agentmemory (2026-06-12); "built on iii-engine" only partially substantiated (iii.dev affiliation confirmed, no public repo) |
| Conversational query rewriting gains | **Verified (literature)** | TREC CAsT; QReCC (arXiv 2010.04898); TopiOCQA; ~10–20% retrieval improvement over naive history handling |
| Mid-context degradation of long histories | **Verified (peer-reviewed)** | "Lost in the Middle", arXiv 2307.03172 (TACL) |
| MemGPT memory tiers | **Verified concept, misaligned use case** | arXiv 2310.08560 targets unbounded assistant memory, not bounded support sessions. (LongMemEval primary paper not independently re-located this pass — medium confidence on benchmark details) |
| Azure agentic retrieval consumes conversation history in query planning | **Verified** | Azure docs (updated 2026-06-08): `messages` input; reasoning-effort levels minimal/low/medium |

### Sources
Karpathy LLM Wiki gist · nashsu/llm_wiki (README + docs) · rohitg00 LLM Wiki v2 gist · tobi/qmd · QReCC arXiv 2010.04898 · Lost in the Middle arXiv 2307.03172 · MemGPT arXiv 2310.08560 · Azure AI Search agentic-retrieval docs (learn.microsoft.com, 2026-06) · supplementary repo deep-dive in `~/.claude/plans/llm-wiki-repo-analysis.md`.
