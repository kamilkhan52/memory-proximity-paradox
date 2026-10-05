# Deck review r2: technical accuracy and traceability

Reviewer: fresh accuracy reviewer, round 2, 2026-10-03.
Inputs:
- `slides/s01–s10.html`: body text, JSON data blocks, tooltips and footers.
- `shots/s01–s10.png`, regenerated 23:12, after the 23:10–23:12 slide edits.
- `reviews/r1_accuracy.md` and `reviews/r1_decisions.md`. The decisions are binding and supersede the BRIEF.
- `data/*.json` (reconciled versions only) and `SYNTHESIS_v1.3.md`.

Full texts re-read with pdftotext this round:
- Octopus arXiv 2501.09020, including the rendered p.2 Fig. 2 table.
- Melody, PolarCXLMem, Vistara, Pond, TPP and Lagar-Cavilla.
- O'Connor FGDRAM, Mahar, Wang (KV$) and CXL-PNM.

Hot Chips 2026 deck text and vision descriptions checked:
- d-Matrix s6, s17, s18 and s23.
- Samsung HBMBaseDie s9–s11 and s23.
- AMD MI400 s3 and s5.
- Rubin s8, Vera s28, DMR s10/s15/s20 and LP30 s24.

Arithmetic recomputed:
- s09 fits: −37.1% and −12.0%/yr.
- s03 HBM trend: 1.44 and 1.54 pJ/bit from the two regression directions, giving 1,152–1,232 W.
- s03 zHBM: 124/(31.8×8) = 0.487 pJ/bit.
- PolarCXLMem switch adders: 549 − 265.2 = 283.8 and 651 − 345.9 = 305.1.
- Melody first-hop adders:
  - CXL-A 214 − 114 = +100 (ASIC);
  - CXL-B 271 − 111 = +160 (ASIC);
  - CXL-C 394 − 114 = +280 (FPGA, Table 1 "FPGA, CXL 1.1×8").
- Octopus first-hop adder: Fig. 2 "CXL expansion 230–270 ns" against "local DDR5 read … about 115 ns", giving +115–155.

**Verdict: needs revision (light).** Blocking 0 · Major 2 · Minor 11. Every r1 blocking and major item is resolved. The two new majors are scope and qualifier issues, and each needs a one-line text fix.

---

## Part 1: Round-1 items, verified against current text

| r1 item | Status | Current text (quoted) |
|---|---|---|
| **B1** first-hop adder +~100–280 | ✓ | s02 callout: "One CXL hop adds ~100–280 ns (measured)". s02 bracket: "+~100–280 ns measured". Bracket tooltip: "Melody +100 (CXL-A) to +280 (CXL-C, FPGA) vs 114 ns local; Octopus +115–155; Vistara +120; PolarCXLMem +119 [P]. ASIC devices add ~100–160 ns; +280 is an FPGA prototype." `latency_ladder.json` cross_check has a ROUND-1 note. Values re-verified (see header). |
| **M1** s07 adder | ✓ | s07 B1: "+~100–280 ns meas.". Tooltip: "Octopus +115–155, PolarCXLMem +119, Vistara +120, Melody +100–280 ns (280 = FPGA device; ASIC controllers +100–160)". |
| **M2** s02 switch V tag | ✓ | Callout: "a switch adds ≥220 ns more<sup>V</sup>". Bracket: "≥220 ns more" + tspan V. Tooltip: "XConn figure cited by Octopus … [V]. Derived from PolarCXLMem: +284–305 ns (549 − 265.2 = 283.8) [D from P]." Footer: "switch ≥220 ns: XConn via Octopus ref. [50] [V]". |
| **M3** s05 NVMe | ✓ | "10–40 µs measured". |
| **M4** s05 DDR5 / RDMA | ✓ | "~100–150 ns measured" and "~3.5–4.6 µs (64 B–4 KB)". Both match G-4 and s02 (DDR5 points 111–146; RDMA 3.55–4.55 µs). |
| **M5** s08 2026 estimate | ✓ | x-labels: "2025", "2026 est.", "2027 forecast". Caption: "TrendForce, 2 June 2026 press release [S]: end-of-year wafer share; 2026 est. (June 2026), 2027 forecast." |
| **M6** s03 zHBM scope | ✓ | Caption: "Raptor 0.37 = measured I/O only; zHBM 0.49 = projected total DRAM power ÷ peak BW." Data src: "derived from Samsung HC'26 s23 chart (31.8 TB/s, 124 W) [D from V]". Samsung s23 chart: 13.9 → 31.8 TB/s (≈ "230%") and 218 → 124 W (≈ "100W saving"); consistent. |
| **M7** s01 qualifier | ✓ | "Schematic, not to scale. Spec-derived positions: slide 5." Footer: "Schematic only; values on slides 2–5." |
| **M8** s09 "outran them" | ✓ | `Moore's law "allowed the basic von Neumann machines to outrun them with economic affordability"`. |
| **M9** s09 caveat | ✓ | "Caveat: scaling limits were forecast before (memory wall 1995; Ranganathan 2011 bet on PCM/memristors). The difference now is a decade of measured slowdown, not the 2026 price spike." |
| m1 v1.2 → v1.3 | ✓ | s01, s06, s07 and s10 footers all say v1.3. |
| m2 HBM4E locator | ✓ | s07 tooltip: "… expects the ratio to 'exceed 4 to 1' with HBM4E (Micron Mar-2025 p.6)". |
| m3 Pond % | ✓ | "at 182% / 222% of local latency (16-socket pool, simulated, Fig. 21)". Pond p.~12: "overall DRAM requirements by 9% and 7% under 182% and 222% latency increases". |
| m4 doubled plus sign | ✓ | Callout now reads "adds ~100–280 ns". |
| m5 s05 hollow rule | **partial** | The Vistara marker is now an open ring ✓. The caption rule is still uneven; see new minor m1. |
| m6 s05 footer tags | ✓ | "RDMA range [D, assumption-based]; A64FX [S]." |
| m7 tier_frontier.json | **partial** | CXL point_label is now "Vistara (spec)" ✓. Other fields are stale; see new minor m2. |
| m8 s06 y = 100 rows | ✓ | Separate strip: "Cold-fraction statistics: y = 100 by definition (not a measured activity share)". No marks are drawn outside a frame. |
| m9 s09 fit labels | ✓ | "nominal-dollar fit, 1990–2010: −37%/yr" and "2010–24: −12%/yr". Note: "Table rates: published AI Impacts fits, CPI-adjusted (2020$). Chart fits: nominal dollars." Tooltip: "(2018–24: single extraction)". |
| m10 Mooncake | ✓ | "(+75% requests trace replay)". |
| m11 A2 driver sources | ✓ | Footer: "LPDDR5X-PIM HC'26, AiM SC23". |
| m12 s07/s10 wording | ✓ | Both read "Deployed (Meta, Google, Azure)"; A1 reads "deployed at scale" on both. |
| m13 s04 "37%" | accepted | The decisions keep the headline "37% of Meta's rack TCO". The caption shows all six generations. No action. |
| m14 Handy tag | ✓ | s08 footer: "Handy HC'26 s4 [S]". |
| m15 tag legend | ✓ | s01 footer adds "[pend] awaiting full text · [unverified]". |
| m16 Berger TC'26 | unchanged | Still not in fulltext/; traceable via notes only. No change requested. |

**Decision compliance (r1_decisions).**
- Headlines: s01, s03, s04, s05 and s06 match the approved text exactly. Kickers carry "→ A" and "→ B".
- Per-slide decisions: all applied. This covers:
  - s03 panel (a) label, "(→ PIM, slide 7)" and legend rows;
  - the s05 row label, sub, annotation 3, caption and footer;
  - the s06 legend line, N-M2 line, "warm → DDR" bracket and ≤/≥ text;
  - s07 Newton, Software lines and 10.5 px tags;
  - s08 "est." label, the "did not cause B" line and the colour-role caption;
  - the s09 quote, caveat and nominal labels;
  - the s10 rivals box, "HBM without in-stack compute", and refutation row 2.
- Outstanding exceptions: the s05 SVG aria-label still says "3D DRAM-on-logic point" (G-3), and the data-file fields in minor m2.

---

## Part 2: New findings

### Major

#### MA1 · s03: the takeaway gives HBM2 a different energy than the chart, without a scope or a "modelled" qualifier
- **Element:** the takeaway under panel (c): "After the interposer, energy sits in the DRAM core: 3.45 of HBM2's 3.92 pJ/bit (full access)." The same panel plots HBM2 at **5.6 pJ/bit** (d-Matrix s6, ±5% read; tooltip "5.6 pJ/bit · 2.4 TB/s").
- **What is wrong:**
  1. A reader sees HBM2 at 5.6 pJ/bit on the chart and 3.92 "full access" in the text. A "full access" figure that is smaller than d-Matrix's "data-movement" figure looks contradictory.
     - The caption explains the scopes of HBM4, Raptor and zHBM, but not HBM2.
     - `hbm_bw_energy.json` notes that d-Matrix's scope is inconsistent across its deck (s6 "Data-Movement Energy", s8 "system", s17 "I/O").
  2. O'Connor's 3.92 is a model output, not a measurement. FGDRAM §1, PDF p.2: "Using a detailed energy model based on physical floorplan and DRAM process characteristics … each HBM2 access incurs 3.92 pJ/bit of energy (including ECC overhead)". This breaks G-8: every number keeps its qualifier.
  3. Minor part of the same item: the 2.24 pJ/bit "on-DRAM data movement" includes the TSV and base-layer path to the I/O bumps (~9.9 mm, O'Connor §1). "DRAM core" is therefore slightly broad; "inside the stack" is exact.
- **Fix:** rewrite the takeaway as: "After the interposer, energy sits inside the stack: 3.45 of HBM2's 3.92 pJ/bit (O'Connor, modelled full access; d-Matrix's 5.6 point uses a broader, unstated scope)." Alternatively, add "HBM2 5.6 (d-Matrix) vs 3.92 (O'Connor model): scopes differ" to caption line 2.

#### MA2 · s04 and s06: "in production" covers a range whose upper end (33%) was measured on pre-production hardware
- **Element:**
  - s04 box: "**Captured in production:** 20–33% of DRAM moved to cheaper tiers at ≤1–5% slowdown."
  - s06: "In production, tiering moves 20–33% of DRAM at ≤1–5% slowdown; DDR still holds 50–75% of capacity."
- **What is wrong:** SYNTHESIS sources the range as zswap ~20%, TMTS 25% and TPP 33%.
  - TPP's 33% is its 2:1 local:CXL configuration. TPP §6 "Experimental Setup" (PDF p.~9) says: "We deploy a number of pre-production x86 CPUs with FPGA-based CXL-Memory expansion card … current FPGA-based CXL cards have around 250ns higher latency than our eventual target".
  - The workloads ran in the production fleet, but the tier was a pre-production FPGA prototype, not a deployed CXL tier.
  - zswap (Lagar-Cavilla p.1: "on average, 20%"; "can migrate 20–30%") and TMTS 25% are deployed. Vistara's 256 of 1,024 GB (25%) is also deployed (ISCA'26 Table V, production).
- **Fix (either option):**
  - (a) "In production, tiering moves 20–25% of DRAM at ≤5% slowdown (zswap, TMTS, Vistara); TPP reaches 33% on pre-production CXL."
  - (b) Keep 20–33%, but change "in production" to "with production workloads", and on s04 "Captured in production" to "Captured at scale".
  - Apply the same wording on both slides. Flag the change for SYNTHESIS v1.3 exec summary row "Capacity cost", which has the same wording.

### Minor

1. **s05 caption, hollow rule (r1 m5 remainder).**
   - Text: "hollow = discontinued, vendor-peak or spec only".
   - Problem: the sub says every row is a vendor peak spec unless marked. Yet the HBM4 and MRDIMM bars (announced, vendor peak) are filled, while the Raptor and A64FX points are hollow.
   - Fix: "hollow = discontinued tier, or a single-product point (vendor peak or spec)".

2. **Stale data and notes files (they feed the report).**
   - In `data/tier_frontier.json`:
     - DDR5 `idle_latency` is "80-120 ns" (should be "~100–150 ns measured", G-4);
     - RDMA is "~3.5-3.9 us" (should be "~3.5–4.6 µs");
     - NVMe is "10-50 us" (should be "10–40 µs"; 50 is unverified);
     - the row-0 `tier` is "3D DRAM-on-logic" (should be "3D DRAM–logic stack", G-3).
   - The s05 aria-label also still says "3D DRAM-on-logic point".
   - `reviews/ORCH_NOTES.md` item 1 still says "+~115–280".
   - SYNTHESIS v1.3 still has "+~100–270 ns" (exec table B1; App. A) and "x86 → DDR ~80–120 ns" (App. A). The deck departs from these by decisions G-1 and G-4, while s07's footer points readers to "SYNTHESIS v1.3 §4". Update SYNTHESIS, or note the supersession, before the report is built.

3. **s03 "interface 2–4×" is our arithmetic but is tagged [P].**
   - Footer: "interface 2–4×: O'Connor Table 3, UniIC IEDM'20 Table 1 [P]".
   - The ratio is Raptor 0.37 [V, measured I/O] against HBM2's whole I/O channel at 0.80 (50% toggle, O'Connor Table 3), which gives 2.2×. Against an HBM interface at ~1.5 including PHY (UniIC Table 1), it gives 4×.
   - Against O'Connor's toggle-adjusted interposer figure (0.3 pJ/bit, §1), there is no reduction. The scopes differ: wire-only vs I/O including PHY.
   - Fix: footer "interface 2–4× [D from P/V]: Raptor 0.37 vs HBM2 whole I/O 0.80 at 50% toggle (O'Connor Table 3) and ~1.5 incl. PHY (UniIC Table 1)". Optionally write "up to ~2–4×" in the takeaway.

4. **s02 and s05: the CXL direct-attach range "214–394 ns" is labelled measured without noting that 394 is the FPGA device.**
   - The bracket tooltip now says so; the row tooltip (s02) and the s05 latency cell do not.
   - Fix: append "394 = Melody CXL-C (FPGA); ASIC devices 214–271" to the s02 row tooltip, and optionally write "214–394 ns (394 FPGA)" on s05.

5. **s05 latency column: [S] values are untagged.**
   - "~300 ns from GPU" comes from Chips and Cheese [S], measured on H100 / GH200 HBM3 (s02 row "GPU → HBM3"), while the s05 row is HBM3E (B200, GH200).
   - ">200 ns (Grace)" is also [S].
   - Fix: "~300 ns from GPU (HBM3) [S]" and ">200 ns (Grace) [S]".

6. **s07 A2 Newton "sim." qualifier is not traceable locally.**
   - Newton MICRO'20 is not in fulltext/. notes/q1 l.376 gives "10x vs an ideal non-PIM … 54x vs a realistic GPU (Titan-V-like)" but not the evaluation method.
   - The numbers match the notes. The "sim." tag (slide, tooltip "[P, sim.]" and footer) needs the full text: **FULL TEXT NEEDED** (Newton §evaluation) to confirm it was simulated.
   - Placement: the item sits under "Main cost" but is a baseline caveat. "Best evidence" or a "Caveat" line fits better (narrative reviewer's call).

7. **s08 col 3 "Catch" wording drops the key qualifier.**
   - Text: "a CXL controller sees ~3–17× its link bandwidth, not bank-level bandwidth."
   - SYNTHESIS §5.3: "it sees only channel-level bandwidth: ~3× the link for DDR5 controllers, ~17× for LPDDR5X CXL-PNM [D]". The 17× assumes an x16 Gen5 link, which Park et al. do not state (notes/q5 l.124). Their 1.1 TB/s internal figure is printed (CXL-PNM PDF p.~5).
   - Fix: "Catch: a CXL controller sees only channel-level bandwidth (~3–17× its link [D]), not bank-level bandwidth."

8. **s03 panel (a) label "~2× every 2 years [D: 3.0/1.6]".** 3.0 / 1.6 = 1.875. "~1.9×" is exact at the same length. Optional.

9. **s05 caption "Sustained CXL bandwidth is 10–80% of local DRAM" (Wang et al. 9.8–80.3%).**
   - `tier_frontier.json` says "intro only read".
   - The value is the paper's own summary of its measurements, but under the full-text rule, confirm it in Wang's results section before the report reuses it.

10. **Source labels.**
    - s02 footer says "Octopus NSDI'26 P50 [P]", but the extraction used arXiv 2501.09020 ("NSDI'26 camera-ready not compared"). Write "Octopus arXiv 2501.09020 (NSDI'26)". s07's footer has the same issue.
    - The s05 SRAM tooltip says "NVIDIA Groq 3 LPX HC'26 s24" while the footer says "NV LP30 HC'26 s24". Use one name.

11. **s03 Raptor source slide.** The point src "d-Matrix HC'26 s23 [V]; measured I/O only" is correct (s23 "Measured I/O Power … 100 TB/s × 0.37 pJ/bit = 296 W"). s17 prints the same 0.37 as "I/O energy" beside HBM4's "2–3 pJ/bit". No change is needed; adding "s17, s23" to the footer would let readers find the side-by-side. Optional.

---

## Part 3: Cross-slide consistency of repeated numbers

| Quantity | Slides | Status |
|---|---|---|
| First-hop CXL adder +~100–280 ns measured | s02 callout, bracket and tooltip; s07 B1 and tooltip | ✓ consistent (SYNTHESIS still says 270; minor m2) |
| Switch ≥220 ns [V]; derived +284–305 [D from P] | s02, s07 | ✓ |
| CXL direct absolute 214–394 ns | s02, s05 | ✓ (FPGA note: minor m4) |
| Local DDR 78–146 (DDR4/5) vs DDR5 ~100–150 | s02, s05 | ✓ compatible |
| RDMA 3.55–4.55 vs ~3.5–4.6 µs | s02, s05 | ✓ |
| NVMe 10–40 µs | s02, s05 | ✓ |
| Xeon Max ~20% latency, ~700 vs ~200 GB/s, peak 1.64 TB/s | s02, s05 | ✓ |
| HBM GB/s per GB 33–42 / 54–73 / "~33–73" | s05, s07 | ✓ (MI455 23.3 TB/s / 432 GB, Helios 1.7 PB/s / 31 TB, Rubin 800 PB/s / 11 PB all confirmed in HC'26 text) |
| GB200 43 vs 0.82 GB/s per GB; 13.4 / 17 TB | s05 tooltip, s08 | ✓ |
| Vistara 0.225 spec; 0.13× nominal; 2.7–3.6× | s05, s07 | ✓ (0.8 / 0.30 = 2.67; 0.8 / 0.225 = 3.56) |
| ~3× wafer per bit; >4:1 HBM4E | s05, s07, s08 | ✓ |
| $/bit −33–36 → −12–15%/yr (CPI-adj.); ~2.5× | s04, s09 | ✓ (34.5 / 13.5 = 2.56) |
| 20–33% at ≤1–5% | s04, s06 | consistent with each other; qualifier issue in MA2 |
| Google 32% cold at 120 s; 1–61% range | s04, s06 (x = 68, range 39–99) | ✓ (Lagar-Cavilla p.3: "32% of memory usage is cold"; "1% to 61%") |
| Meta 55–80% idle per 2 min | s04, s06 | ✓ (TPP p.3) |
| Pond 7–9% sim. | s07 only | ✓ |
| "Deployed (Meta, Google, Azure)" / "deployed at scale" | s07, s10 | ✓ |
| HBM4 2.4 pJ/bit → 1.92 kW at 100 TB/s | s03 (d-Matrix s6 text) | ✓ |

## Verified this round, no change needed (spot list)

**s02**
- Octopus Fig. 2 table: expansion 230–270, MPD 260–300, switch 490–600, RDMA 3550 ns.
- PolarCXLMem Table 1/2: 146 / 231 / 265.2 / 345.9 / 549 / 651 ns; 4.55 µs.
- Vistara ≈130 / ≈250 ns.
- Pond 155 ns (182%) and 180 ns (212%) Fig. labels.
- Melody tails: "CXL-A and CXL-D exceed 700ns, while CXL-B and CXL-C reach 1µs".

**s03**
- Samsung s9: 26 × 33 mm die with 6 HBM.
- Samsung s10: stack 11 × 12.8 mm, PHY ">8mm x 4mm".
- d-Matrix s6: "At 2.4pJ/bit, 100TB/s requires 1.92KW for HBM power only".
- HBM4 18 TB/s matches the s17 "5.6× higher BW" (100 / 5.6 = 17.9).

**s05**
- MRDIMM 1.6 TB/s and 16 ch / 12800 MT/s (DMR s10/s15/s20).
- Vera 1.5 TB / 1.2 TB/s (s28).
- LP30 128 GB / 40 PB/s (s24).

**s06**
- Mahar: "less than 10% of the memory capacity", nine workloads, 30 s.
- Alibaba: "77 % of the reuses".
- TPP: "55-80% … idle within any two minutes".

**s07**
- Vistara: "up to a 25% reduction in server count for disaggregated ML inference"; Table V 57.6 GB/s, 0.13; "CXL Peak BW ≈76 GB/s".
- Pond Fig. 21: 9% / 7%.
- AiM 6 GB = 4 × 4 Gb + 4 × 8 Gb.

**s08**
- CXL-PNM: FPGA prototype for functional verification, with results from a simulator validated against it, so "sim." is fair.
- CXL-PNM 1.1 TB/s / 512 GB.

**s10**
- Rivals text matches SYNTHESIS §7 (a) and (b).
- Refutation rows match §8; predictions and confidence match the exec summary.
