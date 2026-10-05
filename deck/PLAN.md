# Presentation and companion report: plan

**Scope.** The deliverables cover the whole research question: *why are tighter memory–compute coupling (A) and disaggregation / far memory (B) both attractive?* The source of truth is `SYNTHESIS_v1.3.md` plus the `notes/` and `fulltext/` directories.

## Deliverables

1. **`deck/deck.html`: 10 slides.**
   - 16:9 slides, white background, technical rather than marketing tone. Keyboard navigation and a print-style overview mode.
   - Every number is tagged with its source.
   - Figures are replotted from paper or slide data, never screenshots.
2. **`deck/report.html`: companion report.**
   - Mirrors the deck slide by slide, with depth for each: derivations, caveats, counter-evidence, full references, and the same figures (shared data).
3. **Publication.** Both files are published as private Artifacts as well as kept locally.

## Proposed storyline (one claim per slide)

| # | Claim | Main figure (replotted from source data) |
|---|---|---|
| 1 | Two "opposite" directions; one answer. The question, and the answer in one sentence | Hierarchy sketch with A and B arrows from the DDR baseline |
| 2 | Distance is a proxy: closer is not faster, and farther costs hops, not metres | Measured latency ladder (Xeon Max HBM vs DDR; CXL direct and switched; RDMA) |
| 3 | Pressure 1: bandwidth crosses a boundary (wires × rate; edge vs area) | Gholami compute vs DRAM vs interconnect growth; d-Matrix bandwidth vs pJ/bit with constant-power lines |
| 4 | Pressure 2: capacity cost (DRAM $/bit slowdown; memory share of TCO; idle memory) | TPP memory share of rack TCO by generation; $/bit trend |
| 5 | The cost frontier: every tier is a bundle (GB/s per GB vs $/GB, latency annotated) | Log-log tier scatter; DDR region; arrows for A (right) and B (down) |
| 6 | Skew makes a mix of tiers optimal; placement by ρ × I × MLP | Skew statistics (Meta: <10% of capacity carries 90% of bandwidth; KV reuse); placement matrix |
| 7 | Deployed vs ambitious: the 2×2 (A1 / A2 / B1 / B2) | 2×2 with status, best evidence, main cost |
| 8 | The two directions need each other (capacity demand, wafer competition, compute pulled outward) | GB200 two-tier memory; HBM wafer vs bit share; ZeRO / CXL-PNM |
| 9 | Why both, and why now: the 1990s split, and what changed | DRAM $/bit decline before and after ~2011; 1990s vs 2026 table |
| 10 | Implications, predictions, and what would change our mind | Compact table with confidence levels |

## Pipeline and model allocation

| Phase | Work | Agents |
|---|---|---|
| 0. Architecture | `deck/BRIEF.md`: thesis, audience, per-slide spec (claim, evidence with tags, figure spec, word budget ≤60 words per slide), design system (tokens, type, slide grid, chart rules), provenance rules, file conventions | 1 Opus (deck architect) + orchestrator check |
| 1. Figure data | One agent per replotted figure. It extracts values from the full text or slide image into `deck/data/<fig>.json` with page/slide reference and uncertainty. A second independent extraction cross-checks each figure; disagreements are resolved against the source | Sonnet ×2 per figure (~8 figures) |
| 2. Slides | One agent per slide builds `deck/slides/sNN.html` (HTML + inline SVG) from BRIEF + data JSON | Sonnet builders. Slides 1, 7 and 10 are argument-heavy, so Opus |
| 3. Assembly | Merge fragments, shared CSS, navigation; headless-Chrome screenshots per slide | Orchestrator (scripted) |
| 4. Deck review loop | Each round runs three fresh reviewers in parallel: (a) technical accuracy and traceability against notes and full texts; (b) narrative and editorial (does it answer the question in 10 slides?); (c) visual QA from screenshots (overlap, legibility, consistency, chart integrity). Fixes go back to the slide's original builder, resumed with its context | (a), (b) Opus; (c) Sonnet |
| 5. Report | `report.html` written from the synthesis, notes and the shared figure data. Mirrors the slides, with depth | 1 Opus writer + Sonnet figure/HTML implementation |
| 6. Report review loop | Each round: fresh Opus technical reviewer; Sonnet citation and traceability checker (every number → note/full text); Sonnet visual QA | Opus + Sonnet |
| 7. Delivery | Publish both as Artifacts; final summary with links and remaining caveats | Orchestrator |

**Stopping rule for each loop:** two consecutive rounds of fresh reviewers with no blocking or major issues, capped at 4 rounds. Anything still open is listed in the delivery notes.

**Accuracy rules carried into every agent prompt:**
- Every number carries a provenance tag: [P], [V], [S], [Pr], [D] or [Syn].
- Never substitute an abstract for a needed full text; flag FULL TEXT NEEDED instead.
- Replotted values cite page, figure or slide, and state their read-off uncertainty.
- No dual axes.
- Chart colours and marks follow the dataviz rules; palettes are validated with the script.

## Open choices (defaults in bold)

- Slide format: **fixed 16:9 slides with keyboard navigation** (vs one long scrolling page).
- Publish: **private Artifacts + local files** (vs local files only).
- The DRAM $/bit series for slide 9 needs a data source we have read in full. The AI Impacts and Objective Analysis data are secondary today. We may need a full text, or we label it [S].

## Lessons from the beachfront explainer (2026-10-03), applied to the deck and report

- **Three accuracy rounds were needed.** Round 1 found 3 blocking errors: Samsung's 1.2 kW is GPU power; zHBM is 3D; reach, not energy, pins stacks to the edge. Round 2 found 5 major ones: PHY growth wrongly attributed, scope mismatches, and the claim that core energy is unaffected. Round 3 confirmed. Budget for at least three rounds per artifact, and keep each reviewer fresh.
- **Reviewers must check images, not only text.** Several errors came from reading vendor charts and footnotes; the 1,200 W "GPU Power" footnote is one. Reviewers get slide JPEGs and full-text PDFs.
- **The explainer's figures become slide 3 material.** Fig. 2 (edge to scale) and Fig. 3 (bandwidth vs pJ/bit with constant-power lines) are already triple-reviewed.
- **Every energy figure states its scope.** I/O, full access and system level are never compared across scopes.
