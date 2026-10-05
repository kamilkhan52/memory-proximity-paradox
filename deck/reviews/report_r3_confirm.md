# Report R3 confirmation review

**Verdict: ready.** 0 blocking, 0 major, 7 minor. Round-2 majors M1-M5 are fixed.

## Round-2 majors
- **M1 (stale data):** all 9 embedded JSON blocks equal deck/data/*.json (parsed and compared). The false "data file still..." caveats are gone. App. A now states the dram_price_trend metadata fix.
- **M2 (versions and critiques):** the report cites v2.1, and the deck headlines read ~2010 ("Handy 2011 or so" is attributed to Handy). No v1.9 remains.
- **M3 (half-duplex):** §5 says "per direction for serial links, while HBM and DDR buses are half-duplex"; the Fig. 5 caption matches.
- **M4 (Wang):** "denominator undefined; §III gives 17.1% and 46.4% of remote DRAM". Full text confirms 9.8-80.3% and 17.1/46.4% of RDRAM, so the point is not plotted.
- **M5 (recycled DDR4):** two controllers per 256 GB gives ~$4/GB hardware plus $5-9 resale = $9-13/GB = 0.36-0.93x, reported as ~0.4-0.9x all-in. The arithmetic checks.

## Numbers checked (~60, all agree with ORCH_NOTES, data and full text unless listed below)
- **CXL latency:**
  - Adders recompute: Octopus +115-155, Vistara +120, PolarCXLMem +119, Melody +100-160 (FPGA +280).
  - Switch +284-305 (549-265.2; 651-345.9); ratios 3.76x and 2.82x; Octopus "at least 220 ns" confirmed in its text.
  - NVMe is labelled "typical, not a measurement" throughout.
- **Pond:** 9% and 7% at 182% and 222% of local latency (Finding 9); abstract 7%.
- **TPP:** FPGA cards used for "functional validation" only.
- **Mooncake:** the 75% is labelled trace replay, the 525% simulation.
- **Micron:** the >4:1 claim is on p.6 and labelled an expectation.
- **Google zswap:** 4-5% TCO, 20% cold, 1-61% across clusters.
- **Berger:** 155/180 ns = 182/212% of an 85 ns baseline.
- **Vistara Table V:** 256 GB, 57.6 GB/s, 30 W, 0.13.
- **Interface energy:** no blanket 2-4x claim; it is scoped as 0.37 against 0.80 (50% toggle) or ~1.5 (with PHY), "no lower than the real-toggle 0.3".
- **Other recomputes:** zHBM labelled a lower bound (218 W/13.9 TB/s = 1.96; 124 W/31.8 TB/s = 0.49); $/bit ratios 2.4/2.75/3.1; HBM tier GB/s per GB; Gholami powers; Appendix E parity.

## Minor findings
1. **TPP framing (§4, §6).** "TPP reaches 33% (2:1) at 99.5%". Table 1 gives 99.5-99.9% for the 2:1 rows. Its 1:4 configs (80% on CXL) keep 99.5% (Cache1) and 95.0% (Cache2). So "local DRAM keeps 50-75%" excludes TPP's own 20% case. Fix: say "at least 33%" and add one clause on the 1:4 rows, or limit "local keeps 50-75%" to production systems plus the 2:1 emulation.
2. **Round counts.** The header says "rounds 1-5", App. A says "four deck review rounds" (l.441) and "five review rounds" (l.446), and reviews/ holds r1-r7. Unify.
3. **Slide 2 vs report on GB200.** The slide says the capacity tier "sits on the Grace module"; the report marks that [unv.]. Soften the slide to "beside its CPU".
4. **Slide 6.** "<5% slowdown" is applied to all three production systems. The report discloses this; it is a slide fix.
5. **§2 link adder.** The 57 ns total uses the 21 ns common-clock figure; the independent-clock case is 65 ns. Add "(65 ns with independent clocks)".
6. **§5, Raptor.** 32.6/1.67 = 19.5, so "≈20-22x" should be "≈19.5-21.6x".
7. **§8, NAND/DRAM revision.** The body holds an unresolved note ("so this figure should be re-checked"). Resolve or drop it.

## Consistency, overclaims, render
- **Deck consistency:** the headlines match the report section claims. Numbers agree on ~100-280 ns, ≥220 ns, ~2.5-3x, 33-37%, 20-25%, >4:1 and 7-9%. Slide footers cite SYNTHESIS v1.6, which is acceptable per ORCH_NOTES.
- **Contradictions:** none substantive. Hedging is consistent with the tags.
- **Render:** headless Chromium (Playwright) at 1100 px and 420 px showed no page errors, console errors or failed requests. 12 SVGs rendered and there was no horizontal overflow.
