# Round-3 decisions (orchestrator): binding

These supersede r1 and r2 decisions where they conflict. Source reviews: r3_accuracy.md (M/m), r3_narrative.md (R3-M/m), r3_visual.md (M/m). Canonical values: ORCH_NOTES.md, updated this round. SYNTHESIS is now v1.8; footers that cite a version should say v1.6 or v1.8. Either is acceptable, but use v1.8 if you touch the footer.

## Global
- **G3-1. Description of B.** Use "cheaper, extended or shared GB" wherever B is summarised (s01, s05 headline/sub, s07).
- **G3-2. Clearance.** All content must sit ≥12 px above the footer rule (y=680 in the screenshot).

## Per slide

**s01**
- Apply G3-1.
- Fix the ragged manual line break in point 2 (visual minor).

**s02**
- New headline: "Distance is a proxy: closer is not lower-latency, and farther costs hops, not metres."
- "load latency" → "pointer-chase latency".
- Move the modelled 155–180 ns estimate (Berger p.6, citing Pond) from the CXL direct-attach row to the multi-headed-device row, beside the measured 260–300 ns. Label it "Berger/Pond 8–16-socket MHD pool, modelled".
- Clearance ≥12 px.
- The lower CXL dot must not touch the label. The RDMA dots, bar and square must read as separate marks.

**s03**
- New headline: "Bandwidth must cross a boundary; each step closer adds wires, cuts pJ/bit, until another limit binds."
- Takeaway: "cuts interface energy ~2–4× [D]". Footer: "interface ~2–4×: Raptor 0.37 pJ/bit measured I/O [V] vs HBM I/O 0.80 (HBM2, 50% toggle; O'Connor Table 3) to ~1.5 (HBM1-era incl. PHY; UniIC IEDM'20 Table 1) [D from V/P]".
- Visual minors: the y-axis title must not touch the ticks; give scatter labels halos.
- Body is about 67 words. Cut to about 60 if possible without losing content (the narrative review gives suggestions).

**s05**
- RDMA latency cell: "~3.5–5.6 µs (64 B–4 KB page)".
- HBM3E range: "33–43", to cover GB200's ~43.
- The Wang 10–80% figure: the full text is now read (fulltext/wang-cxl-2405.14209.pdf, abstract and §III: "9.8% to 80.3% of the peak bandwidth of local DRAM"). Tag [P], with locator.
- Apply G3-1 to the headline: "Each tier bundles bandwidth with capacity: A buys GB/s per GB; B, cheaper, extended or shared GB." Keep it ≤16 words; drop "or shared" if it overflows.
- Annotation 1: replace the unclear "It".
- The A64FX marker must not cross a separator.

**s06**
- Production line becomes: "Mixing pays: production moves ~20–25% of DRAM to cheaper tiers (zswap, TMTS's Optane, Vistara's CXL) at <5% slowdown; TPP: 33% on pre-production CXL; local DRAM keeps 50–75%." Never say "off DDR".
- Visual minors: the Meta label is on the frame; the Hao label touches the diagonal.

**s07**
- Pattern line: "Pattern: ambitious forms find use only inside vendor-controlled stacks (PIM demos) or on bulk, predictable data (KV pools). The critiques mirror each other: A2 is limited by process, heat and capacity; B2 by latency and switch cost."
- Fix the inline tags: our arithmetic → D; Micron → P/V.
- Apply G3-1.
- Clearance ≥12 px.
- Reduce dead space in A1 if easy.

**s08**
- "HBM4E >4:1" → "HBM4E expected >4:1".
- Adopt the r3_narrative density rewrite (≈64 words). Make "it did not cause B" unambiguous, e.g. "the shortage did not cause B".
- Remove the caption duplication under the right chart.

**s09**
- The slowdown line must not run through the "hollow = 2024" text.

**s10**
- Confidence evidence cell for coupling: "coupling claims" → "GB200, wafer ratio, ZeRO (slide 8)".
