# Report R1 technical review (Opus). Saved by orchestrator: the agent's write was refused.

**Verdict:** major revision. 0 blocking, 14 major, 21 minor. Arithmetic mostly correct (~60 load-bearing numbers checked against full texts).

## Major
- **M1. Mooncake +75% is not production.** It is called "production" in 3 places (B2 cell, B2 table, pooling paragraph). The paper's results are all trace replay with a dummy LLaMA2-70B. Deck already says "trace replay". SYNTHESIS has the same error.
- **M2. NVMe 10–40 µs is not measured.** It is drawn and tagged as measured, but comes from TPP's generic technology figure (Fig. 2), "not a measurement platform". Fix latency_ladder.json and ORCH as well.
- **M3. "3D cuts interface energy 2–4×" is not reconciled.**
  - The report's own HBM2 figure is 0.3 pJ/bit at real toggle, and Raptor's 0.37 is not lower than that.
  - The 2–4× holds only against 0.80 (50% toggle) or ~1.5 (with PHY).
- **M4. Samsung's pJ/bit clashes with the core-energy claim.**
  - Samsung's figures imply ~1.96 pJ/bit (HBM4E baseline) and ~0.49 pJ/bit (zHBM) for total DRAM power.
  - Both are far below HBM2's 3.45 pJ/bit of core energy alone, which clashes with "energy sits in the DRAM core".
  - Either the power is at partial load, so it is not energy per bit, or core energy has fallen; say which.
  - The baseline is 4× HBM4E, not a generic 2.5D package.
- **M5. "+50–70 ns per serial hop" is a spec estimate** (§3 table, §5). The measured adder is +100–160 ns on production controllers, per the report's own §2.
- **M6. Vistara fleet size.** "Millions of servers" is the fleet size, not the number of CXL servers.
- **M7. Production-tiering share.**
  - It omits Vistara's 25% (canonical G2-1).
  - On TPP, the report is right: its performance results are emulated on dual-socket machines; the FPGA CXL was for functional validation only. ORCH and s06 should say "emulation", not "pre-production CXL".
- **M8. Stale version and decisions.** The report says it is built from SYNTHESIS v1.4 and round-1 decisions only. Current is v1.8, and round-2/3 decisions are not applied.
- **M9. Embedded tier_frontier data is stale.**
  - Contains "3D DRAM-on-logic" and the retracted "33 TB/s practical".
  - Latency values are old.
  - Contradicts the "verbatim copies" claim.
- **M10. Out-of-date critiques.** The "What the slide simplified" paragraphs in §4 and §6 criticise slide content the round-2 deck no longer has.
- **M11. $/bit slowdown is stated inconsistently.**
  - "~2.5×" in §4 vs "a third" in §9.
  - Log-rate ratio is ~2.7–3.1×.
  - 36%/33% are AI Impacts whole-series averages (McCallum from 1957; OA 1991–2019), not strictly pre-2011 rates.
- **M12. Pond.** "+182%" should be "182% of local (+82%; 78 → 142 ns)".
- **M13. Wang 9.8–80.3%.** Intro-only read. Now resolved: the full text is in fulltext/wang-cxl-2405.14209.pdf (abstract and §III).
- **M14. Energy scopes.** The CXL energy row is labelled "system" but sums device-level DDR energy and SerDes energy. Fig. 3c mixes scopes against the report's own rule. State the scopes.

## Minor (21)
- **Berger adder:** +70–95 ns, not +75–90.
- **Das Sharma:** 57 ns omits the 15 ns retimer flight time.
- **HBM per GPU:** include 432 GB (MI455X).
- **Boroumand:** 54.2% is a cut in execution time, not "+54.2% performance".
- **Raptor:** its comparison range wrongly includes Rubin's 1.39.
- **SemiAnalysis:** density claim cites the wrong document.
- **Newton:** stored in fulltext/, not "online".
- **Appendix E:** HBM4 r = 3, but Micron says the ratio rises.
- **Tags:**
  - Handy should be [S], not [V].
  - Optane pricing should be [unv.].
- **Figure fixes:**
  - Fig. 2: 214–394 needs "394 = FPGA".
  - Fig. 5: "3D DRAM–logic stacking"; hollow rule must include "spec only".
  - DDR5 row basis was copied from the Vistara row (data file too).
  - Fig. 6: explain the strip styles.
- **Unsourced claims:** NIXL; TrendForce 4× premium; GB200 placement.
- **Other:**
  - Chang thesis was read online only.
  - Appendix F wording.
  - AttAcc baseline is DGX A100.
  - §10 adds little beyond the slide.

**Verified correct:** PolarCXLMem, Melody, Octopus, Pond, Vistara, O'Connor, UniIC, d-Matrix, Micron, TrendForce, GB200, the HC decks, Gholami, AttAcc. Figs. 3b, 3c, 4, 8a, 8b and 9 match the data; the Fig. 9 fits reproduce.
