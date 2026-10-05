# Deck review r7: technical accuracy and traceability (final confirmation, fresh eyes)

Reviewer: accuracy/traceability, round 7, 2026-10-04.

**Snapshot.**
- Slide sources: s01 06:20, s02 06:26, s03 06:34:38, s04 06:34:14, s05 06:30, s06 06:34:56, s07 06:26, s08 06:34, s09 06:34, s10 23:47.
- deck.html was built at 06:34:56. All ten slide sources appear in it verbatim; I checked this by substring match.
- memory-closer-farther-deck.html is byte-identical to deck.html.
- shots/s01–s10.png were rendered at 06:34:57–06:35:05, after every source edit.

**Inputs.**
- All 10 slides: visible text, JSON data blocks, tooltips, aria-labels and footers.
- All 10 screenshots.
- ORCH_NOTES; r1–r5 decisions; r6_accuracy.
- SYNTHESIS_v2.0 (exec. summary, l.96, l.120, l.336–349, l.486–487).
- BRIEF §D "do not say" lists.
- deck/data/tier_frontier.json and dram_price_trend.json.

**Full texts re-read this round.**
- Melody ASPLOS'25: Table 1 (local 114; CXL-A 214, CXL-B 271, CXL-C 394 FPGA, CXL-D 239) and §3.2 tails ("p99.99 and p99.999, CXL-A and CXL-D exceed 700ns, while CXL-B and CXL-C reach 1µs").
- Octopus arXiv 2501.09020:
  - Fig. 2: expansion 230–270, MPD 260–300, switch 490–600, RDMA 3,550 ns. These are measured P50 on Xeon 6 / Turin; the "discussions with supply-chain experts" refer to pricing, not latency.
  - "expansion adds latency (≈230 ns vs 115 ns)".
  - "adds at least 220 ns [50]".
  - "already deployed in production in Azure".
- Vistara ISCA'26:
  - Table III: 497 / 48 GB/s.
  - Table V: Local 768 GB, 614.4 GB/s; CXL 256 GB, 57.6 GB/s; relative cost/GB 0.13.
  - Table VII, MRS row: "Reduced number of servers by 25%".
  - Abstract: "up to a 25% reduction in server count".
  - Table II: "PCIe Gen5 x16 (deployed as x8)".
- Hot Chips 2026 text:
  - AMD MI400: "432 GB at 23.3TB/s"; Helios 31 TB / 1.7 PB/s.
  - Rubin s8: 11 PB / 800 PB/s "at scale AI factory".
  - Samsung HBM base die: 26 mm × 33 mm; sHBM4 MPGA 11 × 12.8 mm, PHY 8 × 4 mm. The PHY sits on the 11 mm, host-facing edge, so 3 stacks fit per 33 mm edge.
- NVIDIA GB200 NVL72 page: 13.4 TB / 576 TB/s HBM3E; 17 TB / 14 TB/s LPDDR5X; 372 GB / 16 TB/s per superchip.

**Arithmetic recomputed.**
- HBM energy trend at 100 TB/s, from both regression directions: 1.44 / 1.54 pJ/bit, giving 1,152 / 1,231 W. This matches the label "~1.4–1.5 pJ/bit → ~1.2 kW" and the tooltip "1.15–1.25 kW".
- 100 × 8 × 2.4 = 1,920 W.
- 3.0 / 1.6 = 1.875, so "~1.9×" is right.
- s09 OLS fits on the embedded series: −37.1 and −12.0%/yr.
- GB/s per GB:
  - MI455X 53.9; Helios 54.8; Rubin 72.7.
  - GB200 43.0; LPDDR5X 0.82.
  - B200 41.7; GH200 33.3.
  - Vera 0.8; GH200 LPDDR5X 1.07.
  - MRDIMM 0.4 / 1.6; DDR5 0.2 / 0.8.
  - CXL x8 0.0625 / 0.25; Vistara 0.225.
  - RDMA 0.0125–0.098; NVMe 0.00047–0.00175.
  - Raptor 3,125; SRAM 312,500.
- Samsung baseline: 225 / (14 × 8) = 2.0. zHBM: 124 / (31.8 × 8) = 0.49.
- Slowdown ratio from the published pairs: 36/15 = 2.4 and 33/12 = 2.75 (percent); ln(.64)/ln(.85) = 2.75 and ln(.67)/ln(.88) = 3.13 (log-rate). So 2.4–3.1 is right.
- MRDIMM-12800 × 16 × 8 B = 1,638.4 GB/s, the same as the Xeon Max HBM peak (4 × 409.6).

**Verdict: ready.** Blocking 0 · Major 0 · Minor 6. All are polish or traceability items carried over or new; none changes a number, a qualifier or a conclusion on a slide.

---

## Part 1: Status of r6 minors 1–6

| r6 item | Status | Evidence |
|---|---|---|
| m1 s09 aria-label "about 2011" | ✓ Fixed | aria-label now ends "…marks the slowdown at about 2010", matching the chart label "slowdown (~2010)" and `break` 2010.5. |
| m2 s03 "(PHY excluded)" and the "others 8-stack" wording | ✓ Fixed | Takeaway: "HBM2 interposer I/O is already 0.3–0.8 pJ/bit by toggle rate (interposer wires only)". This matches O'Connor §1 "I/O wires on the silicon interposer… 0.3 pJ/bit". Caption: "zHBM = Samsung 4-stack projection (HBM points 8-stack)". |
| m3 s03 tooltip scope "implied memory power" | ✓ Fixed where it mattered | Raptor's tooltip now reads "implied I/O power only ≈ 296 W". zHBM's "implied memory power ≈ 125 W" matches its scope (total DRAM power, 124 W). The d-Matrix points keep "memory power", which is consistent with d-Matrix's own "1.92KW for HBM power only". Nothing left to do. |
| m4 s06 footer attribution order | ✓ Fixed | Three groups now: "table: SYNTHESIS §3 [Syn], Yuan T1, Gupta HPCA'20, MTrainS, Pond, Liu OSDI'25 [P]"; "tiering: zswap, TMTS, Vistara, TPP (emulated) [P]"; "local-DRAM share: Vistara, TMTS, TPP, Memstrata [D from P]". |
| m5 s08 "~3× … [V]" | ✓ Fixed | "~3× Structera A, 4× DDR5 (~200 GB/s) vs 64 GB/s link [D from V]" (204.8 / 64 = 3.2). |
| m6 s04 slowdown-ratio provenance | ✓ Fixed | "slowdown ratio 2.4–3.1× from the published AI Impacts rate pairs, depending on % vs log-rate [D]". Recomputed above. The headline "~2.5–3×" stays inside that range. |

The other r6 items, m7–m9, were outside the requested scope (1–6). Their status is in Part 2.

---

## Part 2: Findings (all minor)

1. **m1 · GH200 HBM3E bandwidth "~4.8 TB/s [S]"** (s05 data `src`, s07 `s07-a1-ev` tooltip). Carried from r6 m9.
   - 4.8 TB/s is the H200 figure (141 GB). NVIDIA lists the 144 GB HBM3E GH200 at 4.9 TB/s, which gives 34.0 GB/s per GB, not 33.3.
   - A GPU-memory specialist may notice this in a tooltip. It is not visible on a slide face, and the range "33–43" would at most become "34–43".
   - Unverified, because no NVIDIA GH200 document is in fulltext/.
   - Fix (optional): "GH200 144 GB / 4.9 TB/s → 34". Alternatively keep 33 and leave the [S] tag as is.

2. **m2 · Small traceability polish, carried from r6 m9 and still open.**
   - s05 footer says "NV LP30 HC'26 s24" while the data block says "NVIDIA Groq 3 LPX". Fix: "NVIDIA Groq 3 LPX (LP30 deck) HC'26 s24".
   - s09 caption says "DRAM $/GB, nominal", but McCallum's points are retail module prices (dram_price_trend caveat 2). Fix: "DRAM retail $/GB, nominal".
   - s07 `s07-b1-co` tooltip: "Optane: wound down in 2022 ($559M impairment) [P]" names no document. Fix: add the filing, e.g. "Intel Q2-2022 10-Q".

3. **m3 · s07 B1 "up to 25%<sup>meas.</sup> fewer servers for ML inference": optional scope precision.**
   - In Vistara's Table VII the 25% row is "MRS · ML Parameter Server · Reduced number of servers by 25%".
   - The abstract frames it as "disaggregated inference", and the §ML-parameter-server text gives "20–25% less servers" as the model size scales (Fig. 12).
   - The "meas." tag is defensible, since Table VII reports production improvements, and the tooltip correctly says the 25% comes from capacity, not price.
   - Optional fix: "fewer servers for ML parameter serving". This avoids a reader taking "ML inference" to mean LLM serving.

4. **m4 · Data-file residue (feeds the report, not the deck).** Carried from r6 m7 and unchanged.
   - `tier_frontier.json`:
     - the RDMA `tag` reads "P (as carried in synthesis)", but the deck has it as [D, assumption-based];
     - the HBM3E `note` still says "Bar 33-42";
     - `dm_s23.locator` still reads "slide 6 HBM4 2-3 pJ/bit I/O"; the I/O label is on s17;
     - the CXL row's `unc` still carries the dropped Wang 9.8–80.3% sentence.
   - `dram_price_trend.json`: the title reads "1990-2017" and caveat 1 says "Series ends 2017", while the series and s09 run to 2024.

5. **m5 · SYNTHESIS v2.0 residue (not deck-visible).** Carried from r6 m8.
   - l.120, CXL row: "(x8/x16, 128–512 GB)". The 0.06–0.25 arithmetic is x8 only.
   - Berger TC'26 "~60–70% capacity per unit performance" (s04 box 2, [P]) is still absent from the synthesis. l.96 cites Berger TC'26 only for the area-vs-perimeter quote. The figure traces to notes/q5 l.261 ("dropped by nearly 60–70%"; AMD/Intel at 27% / 39% of their former capacity per performance). The paper was read online (SYNTHESIS l.486), so the [P] tag stands.

6. **m6 · Wording variants that are allowed, noted so no one "fixes" them into inconsistency.**
   - s05 headline: "B, cheaper or extended GB". Elsewhere B is "cheaper, extended or shared GB" (s01, s07). r3 decisions explicitly allow dropping "or shared" on s05 for length.
   - s03 panel (a): "~1.9× per 2 years [D: 3.0/1.6]". r1 had "~2×"; 1.875 is the exact quotient, so the current text is the more accurate one.
   - s02 local-DDR row: "78–146 ns". The canonical "DDR5 ~100–150 ns" applies to s05's DDR5 row; s02's row is DDR4/DDR5 and includes Pond's 78 ns DDR4. These are consistent.
   - No change needed.

---

## Part 3: Cross-slide consistency and spot checks (✓ = traced this round)

| Quantity | Slides | Status |
|---|---|---|
| First CXL hop "+~100–280 ns measured"; ASIC +100–160; 280 = FPGA | s02 bracket and tooltip, s02 box, s07 B1 and tooltip | ✓ Melody 214−114 = 100, 271−114 = 157, 394−114 = 280; Octopus 230–270 vs 115; Vistara 250 vs 130; Polar 265.2 − 146 = 119 |
| CXL 214–394 ns "(394 = FPGA)" | s02, s05 | ✓ Melody Table 1 |
| Tails to ~1 µs | s02 | ✓ Melody §3.2 l.329–331 |
| Switch ≥220 [V]; +284–305 [D from P] | s02, s07 | ✓ Octopus l.177, l.832; 549 − 265.2 |
| MHD 260–300; switch 490–600; RDMA 3.55 µs (measured P50) | s02 | ✓ Octopus Fig. 2 caption ("Load-to-use read latency (P50)… on Intel Xeon 6 and AMD Turin") |
| Hollow estimates: Berger/Pond 155–180 (MHD row), Das Sharma 170/250, Berger >270/>345 | s02 | ✓ Labelled modelled or spec; legend and caption agree |
| DDR5 ~100–150 measured; NVMe 10–40 µs typical; RDMA ~3.5–5.6 µs (64 B–4 KB) | s02, s05 | ✓ Consistent in both places |
| Xeon Max ~20% higher latency; ~700 vs ~200 GB/s; 11–26 GB/s per GB | s02, s05 | ✓ 700/64, 1,638.4/64 |
| Gholami 3.0 / 1.6 / 1.4; 60,000 / 100 / 30 | s03 | ✓ |
| 26 × 33 mm die; HBM4 11 × 12.8 mm; PHY 8 × 4 mm; 6 per die | s03 | ✓ Samsung HC'26 s9–s11 text; host-facing edge is the 11 mm side |
| 1.92 kW at 2.4 pJ/bit; trend ~1.2 kW [D] | s03 | ✓ Recomputed |
| O'Connor 3.45 of 3.92; I/O 0.3–0.8 "interposer wires only"; Raptor 0.37 I/O [V]; zHBM 0.49 lower bound; Samsung ≈2.0 | s03 | ✓ No I/O-vs-full-access comparison (BRIEF "do not say" respected) |
| Meta TCO 15.6…37.1, power 14.6…33.3; "37% of Meta's rack TCO" | s04 | ✓ "Meta's" present; non-monotonic series is not called "rising every generation" |
| $/bit ~33–37 → ~12–15%/yr, ~2010, ~2.5–3× | s01, s04, s09 (chart, table, note, aria) | ✓ Consistent everywhere, including the aria-label |
| Google 32% cold at 120 s; strip 39–99%; TPP 55–80% idle per 2 min | s04, s06 | ✓ |
| Mahar ≤10% → ~90% BW (30 s, 9 services, preprint); Alibaba 10% → 77%; Hao emulated; Mooncake ≤50% | s06 | ✓ Qualifiers on the chart |
| Production ~20–25% to cheaper tiers, <5% slowdown; TPP 33% in emulation; local 50–75% | s06 | ✓ Matches ORCH; Vistara l.746 "CXL spill increases to 20–25%" |
| HBM4 54–73; HBM3E 33–43; LPDDR5X 0.8–1.1; MRDIMM 0.4–1.6; DDR5 0.2–0.8; CXL 0.06–0.25 + 0.225 spec; RDMA [D, assumed]; NVMe | s05, s07 A1 | ✓ All recomputed (GH200 nit: m1) |
| MRDIMM 1.6 TB/s "matches Xeon Max's HBM peak" | s05 | ✓ 1,638.4 = 1,638.4 GB/s |
| Vistara 0.13× nominal; 25% fewer servers; ~3.2–3.6× [D]; 64 GB/s from 2 × x8 | s05, s07 | ✓ Table V / III / II; Table VII (wording nit: m3) |
| AttAcc 1.72× sim.; Newton 54× / 10× sim.; HBM2-PIM 6 vs 8 GB; 4–16× internal BW [V] | s07 | ✓ Tags present |
| Mooncake +75% trace replay; Pond −7–9% sim.; Berger "ROI negative" modelled | s07, s10 | ✓ |
| "Deployed (Meta, Google, Azure)" | s07 pill tooltip, s10 | ✓ Octopus l.222 for Azure |
| GB200 13.4 TB / 576 TB/s; 17 TB / 14 TB/s; ≈43 vs ≈0.82 | s05, s08 | ✓ NVIDIA page |
| TrendForce 18/22/30 wafer, 8/9/13 bits; 2026 est., 2027 forecast | s08 | ✓ (r6 trace; labels on the chart) |
| ~3× wafer per bit [P/V]; HBM4E "expected >4:1" (Mar-2025 p.6 only) | s07, s08 | ✓ |
| ~3× / ~17× link multiples [D from V] / [D, assumes x16 Gen5] | s08 | ✓ 204.8/64; 1,088/64 |
| IRAM 98.5 vs 4.55 nJ; GMS 1.44 ms vs 3.6–14.3 ms; NDP'14 quote with first clause | s09 | ✓ notes/r2c l.117, l.368; BRIEF evidence |
| "Do not say" sweep: 2–4×, off DDR, pre-production, 10–80, 33–42, passive HBM, 3D bonding, DRAM-on-logic, 50–70 ns, PHY excluded, "0.13× gives 25%", "SRAM is a tier", "compute at far tier deployed" outside training | all slide sources | ✓ grep clean ("2011" appears only as a data year and in "Ranganathan 2011") |

**Footers, tags and screenshots.**
- Every on-slide number checked carries its qualifier (measured / sim. / modelled / emulated / spec / typical / est. / forecast / [V] / [P/V] / [S] / [D]).
- The screenshots match the sources. Every value above renders legibly, and no overlap changes a reading: s02 RDMA marks, s03 labels and legend, s05 CXL row, s06 Hao label.
- Footer version citations (v1.6) are acceptable under r3 decisions.
