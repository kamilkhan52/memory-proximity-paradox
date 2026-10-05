# Deck review r6: technical accuracy and traceability (fresh eyes)

Reviewer: accuracy/traceability, round 6, 2026-10-04.

**Snapshot.** s03, s05 and s09 were edited at 06:30:23 while this review ran. The review covers that final state.
- All ten shots were re-rendered at 06:30:24–06:30:32.
- deck.html (06:30:23) and memory-closer-farther-deck.html (06:30:32) both contain the 06:30 text: the new s03 takeaway, the new s05 caption, s09's "~12–15%/yr" and s05's "10–40 µs typical". I grepped for each.

**Inputs**
- All 10 slide sources: text, JSON data blocks, tooltips, aria-labels and footers.
- shots/s01–s10.png at 06:30.
- ORCH_NOTES; r1–r5 decisions; r5_accuracy.
- data/tier_frontier.json and data/dram_price_trend.json.
- SYNTHESIS_v2.0.md.

**Full texts re-read this round (pdftotext)**
- O'Connor MICRO'17: §1 and Table 3.
- Berger IEEE Micro'23: Fig. 3 and p.6.
- Vistara ISCA'26: Tables II, III and V; the 25% claim.
- Lagar-Cavilla ASPLOS'19: §2.2 and the introduction.
- TPP ASPLOS'23: §1 and Fig. 3 labels.
- Wang ATC'25 (77%); Mooncake (>50% unused; 75%); Mahar (<10% → 90%-tile BW).
- Pond (9% / 7% at 182 / 222%); AttAcc (1.72×); Newton (10× / 54×); Octopus (Azure); Gholami (rates).
- Xeon Max arXiv 2505.14294 (~20%; 700 / 200 GB/s).
- AMD MI400 HC'26 s3 and s5 (432 GB, 23.3 TB/s; 31 TB, 1.7 PB/s).
- Rubin HC'26 s8 (11 PB, 800 PB/s).
- GB200 NVL72 NVIDIA page (13.4 TB / 576 TB/s; 17 TB / 14 TB/s).
- TrendForce 2026-06-02 (18 / 22 / 30; 8 / 9 / 13).

**Images viewed directly**
- Samsung HBM base-die HC'26 s23: HBM4E ~14 TB/s and ~225 W; zHBM "230%" and "100W saving".
- d-Matrix HC'26 s6.

**Arithmetic recomputed**
- HBM trend at 100 TB/s: 1.44 / 1.54 pJ/bit from the two regression directions → 1,152–1,232 W.
- s09 fits: −37.1 and −12.0%/yr.
- MI455X 53.9; Helios 54.8; Rubin 72.7; GB200 43.0 and 0.82.
- Vistara: 0.8 / 0.25 = 3.2 and 0.8 / 0.225 = 3.56. Table III: 0.65 / 0.19 = 3.4.
- s08: 204.8 / 64 = 3.2 and 1,088 / 64 = 17.
- Samsung baseline: 225 / (14 × 8) = 2.0.

**Verdict: ready.** Blocking 0 · Major 0 · Minor 9 (polish or traceability only; none changes a number or a conclusion on a slide).

---

## Part 1: Status of r5 items

| r5 item | Status |
|---|---|
| **M1** s03 zHBM vs "core energy" | ✓ **Resolved.** Caption: "0.49 = total DRAM power ÷ peak BW, utilisation unstated, so a lower bound; Samsung's HBM4E baseline ≈2.0" (verified against the s23 image: ~225 W / ~14 TB/s → 2.0). Takeaway: "(…full access, modelled; newer generations lower it)… HBM2 interposer I/O is already 0.3–0.8 pJ/bit by toggle rate (PHY excluded)". The r4 "by toggle rate" qualifier is restored. A residual wording nit is in m2. |
| m1 s05 NVMe render | ✓ s05.png and both assembled decks show "10–40 µs typical". |
| m2 s02 Pond data row | ✓ `est_pond_row5` is now "Berger/Pond 8–16-socket MHD pool, modelled", `src: berger`, `target_row: cxl_mhd`. Berger p.6 reads "small 8-and 16-socket pools… 182-212% (155-180ns)", and Fig. 3 gives "2-8 socket… 155ns, 182%" and "16 socket… 180ns, 212%". |
| m3 cable 12 ns | Kept (r4/r5 decision). Traceable to Levis [P]. |
| m4 "per direction" | ✓ Sub: "Peak vendor specs (per direction for serial links)". Caption: "Peak vendor specs [D from V] (per direction for serial links)". |
| m5 Azure | ✓ The s07 B1 pill carries a `title` tooltip: "Azure: CXL deployment described in Octopus arXiv 2501.09020 [P] and the Azure M-series announcement [Pr]". Octopus p.2: "expansion is already deployed in production in Azure [8, 12]". |
| m6 s08 x16 assumption | ✓ "[D, assumes x16 Gen5]". The "~3× … [V]" half remains: m5 below. |
| m7 s09 table note | ✓ It now says the before-~2010 range combines AI Impacts 33–36% (2020$) with our −37% nominal fit. The 06:30 edit also made the sign convention consistent ("~12–15%/yr"). |
| m8 s04 ratio range | Not adopted. The headline "~2.5–3×" is still fair: m6. |
| m9 data-file residue | ~ Partly fixed: the DDR5 `basis` is corrected and NVMe now says "typical". Still open: m7. |
| m10 SYNTHESIS residue | ~ "10–80%" is gone, NVMe says "typical", and "33–42" is gone. Still open: l.120 "x8/x16", and Berger TC'26 "60–70%" is still absent (m8). |
| m11 small polish | Open: "NV LP30" naming; McCallum "retail module prices"; GH200 4.8 vs 4.9 TB/s. See m9. |

---

## Part 2: Findings (all minor)

1. **m1 · s09 aria-label contradicts the visible label.**
   - The aria-label says "A vertical line marks the slowdown at about 2011."
   - The chart label reads "slowdown (~2010)", `break` is 2010.5, and s01, s04 and s09 all say ~2010.
   - Fix: "…at about 2010".

2. **m2 · s03 takeaway: "(PHY excluded)" is our reading, not O'Connor's wording.**
   - O'Connor §1 says only "transferring the data over the I/O wires on the silicon interposer requires an additional 0.3 pJ/bit". In his model, the TSV and base-layer path to the bumps sit in the 2.24 pJ/bit "data movement" term, and Table 3's I/O row (0.80 at 50% activity) is the interposer I/O.
   - Whether driver and PHY circuitry are inside that term is not stated.
   - The qualifier is defensible as a contrast with Samsung's base-die "SERDES (Data align/DQ I/O)" power, but a DRAM-circuit reader may ask where it comes from.
   - Optional: "(interposer-wire I/O term; base-die SERDES not counted)", or cite it as "[D from P]".
   - Also in the s03 caption: "zHBM = Samsung 4-stack projection (others 8-stack)" reads as if Raptor and the DDR points were 8-stack. Make it "(HBM points 8-stack)".

3. **m3 · s03 point tooltips: "implied memory power ≈ … W [D]" is applied to every point regardless of scope.**
   - For Raptor, 100 × 8 × 0.37 = 296 W is d-Matrix's "Measured I/O Power" (s23), not memory power.
   - For the d-Matrix points, the energy is "Data-Movement Energy" (s6 axis title).
   - The source line under it does say "measured I/O only", but the bold first line misstates the scope.
   - Fix: "implied power at this point's scope ≈ … W [D]".

4. **m4 · s06 footer: the attribution order misleads.**
   - "local-DRAM share: Vistara, TMTS, TPP, Memstrata [D from P], SYNTHESIS App. A; Yuan 2402.16363 T1; Gupta HPCA'20; MTrainS 2305.01515; Pond; Liu OSDI'25 [P]" reads as if Gupta, Yuan, MTrainS and Pond support the 50–75% local-DRAM share.
   - They are the placement-table sources: Gupta HPCA'20 for embeddings at 0.25 FLOP/B (notes/q4 l.163–165), Yuan for decode intensity, Pond for "GAPBS worst".
   - Fix: split into "local-DRAM share: Vistara, TMTS, TPP, Memstrata [D from P], SYNTHESIS App. A. Table: Yuan 2402.16363 T1 (decode), Gupta HPCA'20 (0.25 FLOP/B), MTrainS, Pond (GAPBS), Liu OSDI'25 [P]."

5. **m5 · s08 footer: "~3× Structera A, 4× DDR5 (~200 GB/s) vs 64 GB/s link [V]".**
   - The inputs are vendor figures, but the ratio is ours (204.8 / 64 = 3.2).
   - Fix: tag it [D from V], matching the ~17× entry.
   - This is the leftover half of r5 m6.

6. **m6 · s04 footer: "slowdown ratio 2.4–3.1× depending on % vs log-rate [D]".**
   - This range comes from the published pairs (36/15, 33/12).
   - Box 1 beside it uses ~33–37%, and our nominal pair gives 3.1× (%) or 3.6× (log).
   - The headline "~2.5–3×" is fine.
   - Fix: append "(published AI Impacts pairs)".
   - Carried from r5 m8, not adopted.

7. **m7 · Data-file residue (feeds the report, not the deck).**
   - `tier_frontier.json`:
     - RDMA `tag` is "P (as carried in synthesis)", but s05 and the data `src` say [D, assumption-based];
     - HBM3E `note` still says "Bar 33-42";
     - `sources.dm_s23.locator` cites "slide 6 HBM4 2-3 pJ/bit I/O", but the I/O-labelled 2–3 pJ/bit is on **s17** (the s6 axis says "Data-Movement Energy");
     - the CXL row's `unc` still carries "Sustained CXL BW is 9.8-80.3% of local DRAM [Wang]", which s05 dropped as misleading under r4.
   - `dram_price_trend.json`: the title says "1990-2017" and caveat 1 says "Series ends 2017… do not extend the line", while the series and s09 run to 2024.

8. **m8 · SYNTHESIS v2.0 residue.**
   - l.120, CXL row: "0.06–0.25 peak per direction (x8/x16, 128–512 GB)". The arithmetic and s05 use x8 (32 GB/s), so drop "/x16".
   - Berger TC'26 "60–70% capacity per unit performance" (s04 box 2, tagged [P]) is still absent from the synthesis, and the PDF is not in fulltext/. Its only trace is notes/q5 l.261.
   - Neither is visible on the deck.

9. **m9 · Small traceability polish (carried from r5 m11 and new).**
   - s05 footer "NV LP30 HC'26 s24" vs data block "NVIDIA Groq 3 LPX". Use "NVIDIA Groq 3 LPX (LP30 deck) HC'26 s24".
   - s09 caption: the McCallum points are retail module prices, not contract prices (dram_price_trend caveat 2). Add "retail module" to "DRAM $/GB, nominal".
   - s07 B1 tooltip: "Optane: wound down in 2022 ($559M impairment) [P]" names no document. Add "Intel Q2-2022 10-Q", or whichever notes/r3_gap_evidence l.297 cites.
   - GH200 HBM3E bandwidth "~4.8 TB/s [S]" (s05 data, s07 tooltip): NVIDIA's 144 GB GH200 is commonly specified at 4.9 TB/s (34.0 GB/s per GB). This is unverified because no NVIDIA document is in fulltext/, and it does not change "33–43".

---

## Part 3: Cross-slide consistency and spot checks (✓ = traced this round)

| Quantity | Slides | Status |
|---|---|---|
| First CXL hop "+~100–280 ns measured"; ASIC +100–160; 280 = FPGA | s02 box, bracket and tooltip; s07 B1 and tooltip | ✓ Melody 214 − 114 = 100, 394 − 114 = 280; Polar 265.2 − 146 = 119; Vistara 250 vs 130 |
| 214–394 ns "(394 = FPGA)" | s02, s05 | ✓ |
| Switch ≥220 [V]; +284–305 [D from P] | s02, s07 | ✓ (549 − 265.2) |
| MHD 260–300 measured; Berger/Pond 155–180 modelled; Berger >270 / >345 | s02 | ✓ Fig. 3; component sum 40+25+5+25+15+45 = 155 |
| Das Sharma 170 / 250 spec | s02 | ✓ tooltip arithmetic |
| Xeon Max ~20%, ~700 vs ~200 GB/s | s02, s05 | ✓ arXiv 2505.14294 l.28, l.117 |
| DDR5 ~100–150 ns; NVMe typical; RDMA ~3.5–5.6 µs | s02, s05 | ✓ |
| Gholami 3.0 / 1.6 / 1.4; 60,000 / 100 / 30 | s03 | ✓ Fig. 1 legend |
| d-Matrix replot; 1.92 kW | s03 | ✓ s6 image (HBM1 6.8 / 1.0 … HBM4 2.4 / 18; DDR3-1600 at 0.011 as plotted; "requires 1.92KW for HBM power only (excluding fabric power)") |
| O'Connor 3.45 of 3.92 (1.21 + 2.24; ECC incl.); I/O 0.3 real / 0.80 at 50% | s03 | ✓ §1 l.196–203; Table 3 |
| zHBM 31.8 TB/s, 124 W → 0.49 (lower bound); baseline ≈2.0 | s03 | ✓ s23 image |
| Meta TCO 15.6…37.1; power 14.6…33.3 | s04 | ✓ TPP Fig. 3 labels |
| Google 32% cold at 120 s; TPP 55–80% idle per 2 min | s04, s06 | ✓ Lagar-Cavilla l.188; TPP l.232 |
| Google strip 39–99% (cold 1–61% across clusters) | s06 | ✓ intro l.102 |
| Mahar ≤10% → ~90% BW; Alibaba 10% → 77%; Mooncake >50% unused | s06 | ✓ |
| Production tiering ~20–25%; TPP 33% in emulation; local 50–75% | s06, SYNTHESIS l.26 | ✓ matches ORCH |
| HBM4 54–73; HBM3E 33–43; MRDIMM 1.6 TB/s; LPDDR5X 0.8–1.07; DDR5 0.2–0.8; CXL 0.06–0.25 with ring 0.225 spec | s05, s07 | ✓ AMD s3/s5, Rubin s8, NVIDIA page, Vistara Table V |
| Vistara 0.13× nominal; up to 25% fewer servers; ~3.2–3.6× [D]; 2 × x8 links = 64 GB/s | s07 | ✓ Table II "PCIe Gen5 x16 (deployed as x8)", two ASICs per board; Table III 497 / 48; Table V |
| AttAcc 1.72× sim.; Newton 54× / 10× sim.; HBM2-PIM 6 GB | s07 | ✓ |
| Pond −7–9% sim.; Berger "switch-based… ROI negative" | s07 | ✓ Pond l.1092; Berger l.506–509 |
| Mooncake +75% (trace replay) | s07 | ✓ "Under real workloads… 75% more requests"; tag per r4 decision |
| GB200 13.4 TB / 576 TB/s; 17 TB / 14 TB/s; ≈43 vs ≈0.82 | s05, s08 | ✓ NVIDIA page |
| TrendForce 18/22/30 wafer, 8/9/13 bits; 2026 est., 2027 forecast | s08 | ✓ press release text ("by the end of 2025, 2026, and 2027") |
| ~3× wafer per bit; >4:1 expected (HBM4E) | s07, s08 | ✓ Micron FQ2-25 p.6 |
| $/bit ~33–37 → ~12–15%/yr; ~2010; ~2.5–3× | s01, s04, s09, SYNTHESIS l.26, l.348 | ✓ consistent (aria-label nit: m1) |
| s09 fits −37 / −12 nominal | s09 | ✓ recomputed −37.1 / −12.0 |
| "Deployed (Meta, Google, Azure)" | s07, s10, SYNTHESIS l.34, l.240 | ✓ consistent and now sourced |
| "Do not say" sweep: no "2–4×", "off DDR", "pre-production", "10–80", "33–42", "passive HBM", "3D bonding", "DRAM-on-logic", "50–70 ns" | all slide sources | ✓ grep clean |

**Footers and tags.** Every on-slide number I checked carries its qualifier (measured / sim. / modelled / spec / typical / V / P/V / D / S). The remaining tag issues are m3 (tooltip scope wording) and m5 (one ratio tagged [V] rather than [D from V]). Footer version citations (v1.6) are acceptable under the r3 decisions.
