# Deck review r1: technical accuracy and traceability

Reviewer: fresh accuracy reviewer, round 1, 2026-10-03.
Inputs: `slides/s01–s10.html` (body, data blocks, tooltips, footers); `shots/s01–s10.png`; `BRIEF.md` §D; `data/*.json` (reconciled); `reviews/ORCH_NOTES.md`; `SYNTHESIS_v1.3.md`; notes. I re-checked these full texts with pdftotext: Melody, Octopus (arXiv), Vistara, Pond, TPP, O'Connor, Gholami, Micron Mar-25 and Dec-25, Mooncake and AttAcc. I also checked the TrendForce HTML and the Hot Chips text and images: d-Matrix s6 and s13, Samsung s23, and Handy s4.

**Verdict: needs revision.** Blocking 1 · Major 9 · Minor 16.

---

## Blocking

### B1 · s02 (and s07, data): the reconciled first-hop CXL adder "+~115–280 ns" understates the low end
- **Element:** s02 `.box.far` "One CXL hop adds +~115–280 ns (measured)". s02 bracket label "+~115–280 ns measured" and its tooltip "+115–155 (Octopus), +120 (Vistara), +119 (PolarCXLMem), up to +280 (Melody vs 114 ns local)". Also `latency_ladder.json` `extraction.cross_check.resolved` and ORCH_NOTES item 1.
- **What is wrong:** the reconciliation takes Melody's upper end and drops its lower end. Melody Table 1 (fulltext/melody-asplos25.pdf, PDF p.3) gives these values:
  - Local DDR5: SPR2S 114 ns, EMR2S 111 ns.
  - CXL-A (ASIC, on SPR2S/EMR2S): 214 ns, which is **+100 to +103 ns**.
  - CXL-D: 239 ns vs EMR2S' 117 ns, which is +122.
  - CXL-B: 271 ns, which is +157 to +160.
  - CXL-C (an **FPGA**): 394 ns, which is +280 to +283.

  The measured range is therefore about **+100 to +280 ns**, not +115 to +280. The upper end is also an FPGA device. ASIC expanders measure +100–160 ns: Melody +100–160, Octopus +115–155, PolarCXLMem +119, Vistara +120, ByteDance 250.42 − 97 = +153.
- **Fix:** use **"+~100–280 ns (measured)"** everywhere: the s02 callout, the s02 bracket and its tooltip ("Melody +100 (CXL-A) to +280 (CXL-C, FPGA) vs 114 ns local"), s07 B1, the s07 tooltip, the `latency_ladder.json` cross_check and ORCH_NOTES. Optionally, the tooltip can add "ASIC devices +100–160 ns; +280 is an FPGA prototype". This is the slide-2 claim the s07 fix (M1) will copy, so it needs settling before M1 is applied.

---

## Major

### M1 · s07: latency adder inconsistent with s02 (ORCH item 1 not fixed)
- **Element:** B1 "Main cost: +~100–270 ns meas.". Tooltip `s07-b1-co`: "Melody up to ~+270 ns".
- **Wrong:** s02 says +~115–280. Melody's maximum is 394 − 114 = 280, not 270.
- **Fix:** "+~100–280 ns" (per B1). Tooltip: "Octopus +115–155, PolarCXLMem +119, Vistara +120, Melody +100–280 (280 = FPGA device)".

### M2 · s02: switch adder "≥220 ns" carries no vendor flag (ORCH item 3 half-fixed)
- **Element:** s02 callout "a switch adds ≥220 ns more". Bracket label "≥220 ns more". Bracket tooltip "Octopus: 'adds at least 220 ns' [P]".
- **Wrong:** Octopus (arXiv 2501.09020, PDF p.3) cites the figure to ref [50], "J. Jiang, CXL Switch … FMS presentation, XConn, 2024". It is a vendor number relayed by Octopus, not an Octopus measurement. s07 already tags it V, so the two slides disagree.
- **Fix:**
  - Add `<sup class="tag">V</sup>` after "≥220 ns" in the s02 callout and the bracket label.
  - Tooltip: "Octopus p.3, citing XConn FMS'24 [V]. Consistent with PolarCXLMem measured 549 − 265.2 = 284 ns (local), 651 − 345.9 = 305 ns (remote) [D from P]."

### M3 · s05: NVMe idle latency "10–50 µs" (ORCH items 7–8 not fixed)
- **Element:** s05 table, NVMe row, "10–50 µs" (data block `lat`).
- **Wrong:** the only full-text source is TPP Fig. 2 (PDF p.2), which prints 10–40 µs. The 50 µs end is a Micron spec snippet and is unverified. s02 already plots 10–40.
- **Fix:** "10–40 µs" (TPP Fig. 2 [P]).

### M4 · s05: rest of the latency column inconsistent with s02's measured ladder
- **Element:** s05 table, DDR5 RDIMM "80–120 ns" and RDMA "~3.5–3.9 µs".
- **Wrong (DDR5):** the measured DDR5 idle latencies in `latency_ladder.json` are ByteDance ~97, Melody 111/114/117, Octopus 115, Vistara ~130 and PolarCXLMem 146 ns. "80–120" sits at the DDR4 end: Pond Skylake 78 ns.
  - SYNTHESIS v1.3 has the same value at §2 (tagged [P]) and App. A (tagged [S]), so the tag is inconsistent there too.
- **Wrong (RDMA):** s02 shows 3.55–4.55 µs. s05 drops PolarCXLMem's 4.55 µs (64 B read, Table 2).
- **Fix:** DDR5 "~100–150 ns" (measured DDR5, slide 2) or "78–146 ns (slide 2)". RDMA "~3.5–4.6 µs".

### M5 · s08: the 2026 HBM wafer and bit shares are also projections, but only 2027 is marked
- **Element:** col 2 chart x-label "2026". Caption: "TrendForce, June 2026 press release [S]; 2027 is a forecast."
- **Wrong:** the release (fulltext/trendforce-20260602-13074.html) says "will account for approximately 18%, 22%, and 30% … by the end of 2025, 2026, and 2027" and "bit supply is expected to represent approximately 8%, 9%, and 13%". The end-2026 value was an estimate when published in June 2026. `hbm_wafer_bit_share.json` conventions also say "2026 end of year (estimate)".
- **Fix:** x-labels "2025", "2026 (e)", "2027 (f)". Caption: "TrendForce, 2 June 2026 press release [S]: end-of-year wafer share, same-period bit share; 2026 estimated, 2027 forecast."

### M6 · s03(c): the zHBM point's energy scope is not stated, and it sits beside an I/O-only point
- **Element:** the diamond "zHBM 3D (Samsung proj.)" at 0.49 pJ/bit. The caption states the scopes of HBM4 and Raptor ("Raptor 0.37 = measured I/O only") but not zHBM's.
- **Wrong:** 0.49 = 124 W ÷ (31.8 TB/s × 8). That 124 W is Samsung's projected **DRAM power** for 4 stacks in SiP (Samsung HC'26 s23: "DRAM Power … Assumption: GPU Power 1200W, 4 HBMs in SIP"). So it is a whole-DRAM projection, not I/O.
  - Placed next to Raptor's I/O-only 0.37, the chart implies the two can be compared, which BRIEF §B5 forbids.
- **Fix:** add to caption line 2: "zHBM 0.49 = Samsung's projected DRAM power ÷ peak BW (4 stacks) [D from V]; not comparable with Raptor's I/O-only 0.37."

### M7 · s01: "Measured positions: slide 5" uses the wrong qualifier
- **Element:** SVG corner note "Schematic, not to scale. Measured positions: slide 5." Footer: "Schematic only; measured values on slides 2–5."
- **Wrong:**
  - s05 plots peak vendor specs [D from V]. Only Xeon Max STREAM is measured, and Vistara 0.225 is now marked spec.
  - s05 has no $/GB coordinates at all: $/GB appears only as a table, and those values are mostly [S] or unverified.
  - s03(c) is a vendor chart.
- **Fix:** corner note: "Schematic, not to scale. Spec-derived GB/s per GB and $/GB: slide 5." Footer: "values on slides 2–5".

### M8 · s09: "outran them" is quoted without its qualifier (violates a do-not-say item)
- **Element:** table, Middle / 1990s: `$/bit −33–36%/yr: commodity machines "outran them"`.
- **Wrong:** BRIEF §D s09 "Do not say" forbids quoting "outran them" without the first clause. The NDP'14 sidebar (notes/r2c C2, line 185) says the machines "did not fade away because of lack of performance gains or energy efficiency, but simply because Moore's law allowed the basic von Neumann machines to outrun them **with economic affordability**". Quoted bare, "outran them" reads as a performance claim.
- **Fix:** `commodity machines "outrun them with economic affordability"`. Alternatively, paraphrase without quotation marks: "cheap commodity machines won on cost, not speed".

### M9 · s09: the caveat attributes "the end of DRAM scaling" to 1995
- **Element:** "Caveat: 'the end of DRAM scaling' was declared before (1995, 2011)."
- **Wrong:** the 1995 source (Wulf & McKee, footer) predicted the memory wall, a processor–DRAM speed gap. It did not predict the end of DRAM density or $/bit scaling. Only Ranganathan 2011 (IEEE Computer p.42, "a slowing of DRAM growth due to scaling challenges") matches the quoted phrase (notes/r2c C6).
- **Fix:** "Caveat: memory 'walls' were declared before: the memory wall (1995) and a DRAM-scaling slowdown (2011). The difference now is a decade of measured $/bit slowdown, not the 2026 price spike."

---

## Minor

1. **s01, s07, s10 footers** cite "SYNTHESIS v1.2". The reviewed answer is v1.3. The §4 and §8 numbering is unchanged, so only the version string needs to change.
2. **s07 tooltip `s07-a1-co`:** ">4:1 with HBM4E (Micron Dec-2025 p.5)". The >4:1 figure is in Micron Mar-2025 p.6 only. Dec-2025 p.5 says "3-to-1 trade ratio … only increases with future generations". Fix the locator; s08's footer already has it right (ORCH item 5).
3. **s07 tooltip `s07-b2-ev`:** "DRAM needs −9% / −7% at +182% / +222% latency". Pond's 182% and 222% are latency **relative to local** (142/78 ns, 255/115 ns; Pond pp.4, 10), not "+182%". Write "at 182% / 222% of local latency (16-socket pool, simulated, Fig. 21)".
4. **s02 callout:** "adds +~115–280" has a doubled plus sign. Write "adds ~100–280 ns" (see B1).
5. **s05 Vistara marker:** the dot is drawn with a filled centre, the look of a measured value, while the label says "(spec)". Also, the caption rule "hollow = discontinued or vendor-peak only" is applied unevenly: Raptor (announced, vendor peak) is hollow, but HBM4 (announced, vendor peak) is filled. Either draw Vistara as an open ring or reword the caption: "hollow = discontinued, or single-vendor unshipped peak".
6. **s05 footer:** no [D] tag for the RDMA "assumed range". The row label says "(assumed range)" and the tooltip has [D], but the footer is silent. A64FX 1 TB/s / 32 GB is [S] in the data but untagged on the slide. Add "RDMA range: our assumption [D]; A64FX [S]".
7. **`data/tier_frontier.json` (feeds the report):** the CXL row still has `point_label: "Vistara, measured"` and a note saying "57.6 GB/s is Vistara's measured tier". Vistara Table V (PDF p.4) is a configuration spec; the platform table gives "CXL Peak BW ≈76 GB/s", and the measured all-reads figure is 48 GB/s (Table III). Fix the data before the report reuses it.
   - SYNTHESIS v1.3 §2 also says "Vistara measured 0.225" and "+~100–270 ns". The report writer should not copy either.
8. **s06 chart:** the Mooncake and TPP rows are drawn above the y = 100 line, outside the plot frame, offset for legibility. This is disclosed ("y = 100 by definition", "top rows offset"), but the marks sit at y values they do not have. Consider a separate strip under a "y = 100 by definition" header, with leader lines.
9. **s09 chart vs table:** the chart's fit label "−37%/yr" (nominal, 1990–2010) falls outside the table's "−33–36%/yr" (AI Impacts, 2020$, different window). The footer explains it, but the mismatch is visible. Also, 2018–2024 come from a single extraction (B only, Wayback), and the −12% fit depends on them. Add "2018–24 single extraction" to the tooltip, or cross-check those years.
10. **s07 B2:** "+75% requests meas.". Mooncake's 75% comes from a trace-replay load test (10P+10D vs vLLM-[20M], mooncake-2407.00079 PDF p.~20), not a production measurement. The tooltip is correct. Consider "(trace replay)" in place of "meas.".
11. **s07 A2 driver "4–16×":** the tooltip cites LPDDR5X-PIM 8× (HC'26) and GDDR6-AiM 16× (SK hynix SC23). The footer lists neither; add them.
12. **s10 vs s07:** s10 rates B1 "High: deployed at scale". The s07 B1 pill says "Deployed (Meta, Google, Azure)", without "at scale", while A1 says "Deployed at scale". Align the wording.
13. **s04 headline:** "memory is 37% of Meta's rack TCO" is the Gen5 value only. The chart shows 15.6–37.1, and SYNTHESIS says "in Meta's latest generation". The headline is BRIEF-exact, so this is the orchestrator's call; "37% of Meta's latest rack TCO" fits.
14. **s08 footer:** Handy HC'26 s4 is tagged [V]. Handy is an analyst (Objective Analysis), not a vendor, so [S] fits better. This is consistent with SYNTHESIS, so it is the orchestrator's call.
15. **s01 tag legend** omits [pend] and [unverified], which s02 (NVMe tooltip) and s05 ("unverified") use.
16. **s04 "fell ~60–70% in 10 years" (Berger TC'26):** not in fulltext/ (BRIEF G1), so it can be traced only to the notes quote (q5 l.261: "dropped by nearly 60–70%"; AMD/Intel at 27% / 39% of former capacity per performance). It is consistent with Octopus §1 ("per-socket compute up by 5–10× … DRAM capacity grown only 2–3×"). No change needed; store the PDF.

---

## ORCH_NOTES verification

| Item | Status |
|---|---|
| Reconciled first-hop adder "+~115–280" on s02; s07 to match | s02 done; **s07 not fixed** (M1). **The reconciled value itself is wrong at the low end** (B1): it should be +~100–280. |
| Pond 7–9% simulated | s07 "(sim.)" ✓, tooltip "trace-driven simulation (Fig. 21)" ✓. No other slide cites the saving. Pond's own Fig. labels "8-socket Pond (155ns, 182%)" and "16-socket Pond (180ns, 212%)", so the s02 hollow label "Pond 155–180 ns (modelled)" is correctly attributed. |
| Octopus switch ≥220 ns is [V] (XConn ref [50]) | s07 ✓ (sup V + tooltip). **s02 not fixed** (M2). Confirmed in Octopus PDF p.3 and ref [50]. |
| Vistara 0.225 spec-based, Table V | s05 label "Vistara (spec) 0.225" ✓. Marker style (minor 5) and data-file label (minor 7) remain. Confirmed: Table V prints 256 GB / 57.6 GB/s as configuration. |
| HBM4E >4:1 Mar-2025 p.6 only | s08 footer ✓. **s07 tooltip wrong** (minor 2). Confirmed: Mar-2025 p.6 "exceed 4 to 1"; Dec-2025 p.5 "3-to-1 … only increases". |
| A64FX 31.25 | s05 "A64FX ~31", data 31.25 ✓. |
| NVMe 10–40 µs measured | s02 ✓ (tooltip flags the 50 µs end as [pend]). **s05 not fixed** (M3). |
| s05 RDMA range is [D], not [P] | Row label "(assumed range)" and tooltip [D] ✓; the footer does not say so (minor 6). |

---

## Verified, no change needed

- **s02:**
  - Values checked against `latency_ladder.json` and PolarCXLMem Tables 1–2: 78/115/146; 142–255 and 231; 214–394 with whiskers to 1 µs (Melody p.5 tails); 260–300 (Octopus Fig. 2); 490–600, 549, 651; 3.55 / 3.9 (4 KB) / 4.55 µs (64 B).
  - Das Sharma 170 and 250 ns (spec) and Berger >270 to >345 ns (modelled, lower bounds): hollow, correctly qualified.
  - Xeon Max "about 20% higher latency" and "~700 vs ~200 GB/s": arXiv 2505.14294 p.1–2.
- **s03:**
  - Gholami 3.0×/1.6×/1.4× per 2 yr and 60000×/100×/30× per 20 yr: PDF p.1, Fig. 1 legend.
  - d-Matrix s6 points spot-checked against the slide image: HBM1, HBM3E, DDR5-6400 and GDDR6 within about ±5%.
  - 1.92 kW text on s6.
  - O'Connor: 1.21 + 2.24 pJ/bit and 3.92 incl. ECC (PDF lines ~199–203).
- **s04:** TPP Fig. 3 labels (12 values match); "55–80% … idle within any two minutes" (TPP p.3); ~2.5× = 36/15 to 33/12 [D].
- **s05:**
  - Raptor card 32 GB / 100 TB/s, confirmed on the d-Matrix s13 image, giving 3,125.
  - HBM4 53.9–72.7; HBM3E 33.3–41.7; Xeon Max 10.9–25.6.
  - MRDIMM 0.4–1.6, with the 1–4 TB capacities flagged in the tooltip as our assumption.
  - DDR5 0.2–0.8, matching Vistara's local 768 GB / 614.4 GB/s.
  - CXL 0.0625–0.25; "10–80%" (Wang 9.8–80.3%).
- **s06:** the six restatements match the quoted sentences in `skew_stats.json`; the Hao x-value of 14.3% (range 13–16.7%) is drawn as a bar. The Mahar label "(preprint)" and the Hao label "emulated" are present.
- **s07:** AttAcc "up to 1.72× higher throughput than 2×DGX" (§7.6) is simulated ✓. Vistara 0.13× (Table V) ✓. "Up to 25% reduction in server count for disaggregated inference" (abstract; Table VII MRS) is production ✓. 2.7–3.6× = 0.8 ÷ 0.30–0.225 ✓. Berger "most switch-based configurations are ROI negative" (p.~7) ✓.
- **s08:** GB200 13.4 TB / 576 TB/s and 17 TB / 14 TB/s, giving 43.0 and 0.82 ✓. TrendForce 18/8, 22/9, 30/13 ✓. Micron "three times the amount of silicon" (Mar-25 p.6) ✓. Handy s4 "3 Drivers of DRAM Shortage … HBM takes 3X the die area of DDR" ✓.
- **s09:** 98.5 vs 4.55 nJ (Fromm Table 5) and 1.44 ms vs 3.6–14.3 ms (GMS Table 1), both via r2c §1.2. Series values match `dram_price_trend.json`. Recomputed fits are −37.1% (1990–2010) and −12.0% (2010–2024), matching the rendered labels.
- **s10:** the predictions and confidence levels match SYNTHESIS v1.3 exec. summary "Confidence" and §8.
