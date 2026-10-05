# Instructions for deck reviewers (Phase 4 review loop)

You are a fresh reviewer of a 10-slide technical deck. Audience: a memory-industry researcher. Research question: why are tighter memory–compute coupling (A) and disaggregation / far memory (B) both attractive?

**Materials**

| What | Where |
|---|---|
| Slides (HTML fragments) | /Users/kamil/research/memory-proximity-paradox/deck/slides/s01.html … s10.html |
| Assembled deck | /Users/kamil/research/memory-proximity-paradox/deck/deck.html |
| Rendered screenshots (1280×720 slide on a 1340×760 canvas) | /Users/kamil/research/memory-proximity-paradox/deck/shots/sNN.png (view with Read; regenerate all with `python3 /Users/kamil/research/memory-proximity-paradox/deck/build/assemble.py --shots`) |
| Deck brief | /Users/kamil/research/memory-proximity-paradox/deck/BRIEF.md: §A thesis/arc, §B rules, §D per-slide spec with Evidence and "do not say" lists |
| Figure data, reconciled from two blind extractions | /Users/kamil/research/memory-proximity-paradox/deck/data/*.json (see each file's extraction.cross_check and caveats; ignore *.b.json) |
| Known cross-slide items the orchestrator already logged | /Users/kamil/research/memory-proximity-paradox/deck/reviews/ORCH_NOTES.md (verify they are fixed; don't just repeat them) |
| Reviewed answer the deck must reflect | /Users/kamil/research/memory-proximity-paradox/SYNTHESIS_v1.3.md |
| Evidence base | /Users/kamil/research/memory-proximity-paradox/notes/*.md; full texts in /Users/kamil/research/memory-proximity-paradox/fulltext/ (MANIFEST.md; `pdftotext -layout`, `pdftoppm -r 200 -png` for pages); Hot Chips 2026 text in fulltext/hotchips26/decks_text/, images under fulltext/hotchips26/lightrag_server/slide_assets_vision/ |
| Earlier reviews with pitfalls | /Users/kamil/research/memory-proximity-paradox/notes/r2d_review.md, r5_review_v04.md; explainers/review_r1–r3.md |

**Rules**
- Be specific and actionable. Every finding gives: slide id; severity (blocking / major / minor); the exact element or text; what is wrong; the correct value or wording with its source and locator; and a concrete fix.
- Blocking: a factual error, a misleading chart, or a claim the evidence does not support.
- Major: a scope mismatch, a missing or wrong qualifier (measured / simulated / modelled / vendor / projected), a cross-slide inconsistency, a chart-integrity problem, an illegible or overlapping element, or a narrative gap.
- Minor: polish.
- Never rely on an abstract where a full text is needed. Flag "FULL TEXT NEEDED" instead.
- Do not edit slides. Write your review file and return a summary.

**Output**
- Write your review to the path given in your task.
- Return ≤300 words: verdict ("ready" / "needs revision"), counts by severity, and the blocking and major items in one line each.
