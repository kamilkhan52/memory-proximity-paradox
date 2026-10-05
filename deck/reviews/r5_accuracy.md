# Deck review r5: technical accuracy and traceability (fresh eyes)

Reviewer: accuracy/traceability, round 5, 2026-10-04.

**Snapshot.** The slide sources were being edited while this review ran: s01, s02, s03, s05, s06 and s09 changed at 06:20:37–06:21:12. This review covers the source state as of 06:21:12.
- Shots s01, s02, s03, s06 and s09 were re-rendered at 06:20–06:21. **s05.png was not re-rendered** (23:48) and still shows the old NVMe "measured".
- **deck.html and memory-closer-farther-deck.html are stale** (23:48). They differ from the sources on s01, s02, s05, s06 and s09. Re-run `assemble.py --shots` before anything ships.

**Inputs**
- All 10 slide sources: body text, JSON data blocks, tooltips, aria-labels and footers.
- shots/s01–s10.png.
- ORCH_NOTES.md; r1–r4 decisions; r4_accuracy.md; report_r1_technical.md.
- data/*.json (not *.b.json).
- SYNTHESIS_v1.9.md.

**Full texts and Hot Chips sources re-read this round**
- Vistara: Tables III, V and VII, and §MRS.
- Berger IEEE Micro'23: Figs. 2–3 and p.6.
- Melody: Table 1.
- Xeon Max arXiv 2505.14294: §I.
- O'Connor MICRO'17: §1 and Table 3.
- CXL-PNM HPCA'24: §I, Table I and §IV.
- d-Matrix HC'26: s6, s17 and s23 (text).
- Samsung HBM base-die HC'26 s23: image viewed directly.
- GB200 page extract (gb200_tiers.json).

**Arithmetic recomputed**
- s09 fits: −37.1 and −12.0%/yr. A 2011–24 window gives −10.5%.
- Slowdown ratios. From the published pairs: 36/15 = 2.4 and 33/12 = 2.75 (percent); log-rate 2.75–3.13. From our nominal pair −37/−12: 3.08 (percent) and 3.62 (log).
- 3.0^10 = 59,049; 1.6^10 = 110; 1.4^10 = 29.
- 100 TB/s × 8 × 2.4 = 1,920 W; × 1.44–1.54 = 1,152–1,232 W.
- Raptor: 100 × 8 × 0.37 = 296 W.
- Samsung s23:
  - zHBM: 124 W ÷ (31.8 × 8) = 0.49 pJ/bit.
  - HBM4E baseline: ~225 W ÷ (~14 × 8) ≈ 2.0 pJ/bit.
- Vistara: 0.8 / 0.25 = 3.2 and 0.8 / 0.225 = 3.56. Table III: 0.65 / 0.19 = 3.4.
- s05 rows:
  - HBM3E: 4,800/144 = 33.3; 8,000/192 = 41.7; 576/13.4 = 43.0.
  - DDR5: 614.4/768 = 0.8 and 614.4/3,072 = 0.2.
  - MRDIMM: 1.6 / 1, 2, 4 TB.
  - RDMA: 12.5/1,000 to 12.5/128 = 0.0125–0.098.
  - NVMe: 14/30,000 to 14/8,000 = 0.0005–0.0018.
- s08: 200/64 = 3.1 and 1,088/64 = 17.
- TrendForce wafer/bit consistency, HBM vs non-HBM: 2.5× (2025), 2.85× (2026), 2.87× (2027). This is consistent with Micron's ~3×.

**Verdict: needs revision (light).** Blocking 0 · Major 1 · Minor 11.

---

## Part 1: Status of earlier items

### report_r1_technical items that affect the deck

| Item | Status on the slides |
|---|---|
| NVMe "typical", not measured (M2) | ✓ s02 row and caption: "typical (TPP Fig. 2)", dashed "typical" style. ✓ s05 **source**: "10–40 µs typical" (06:20:37). ✗ **Not yet in s05.png or deck.html**: rebuild needed. Residue: SYNTHESIS v1.9 §2 l.121 and tier_frontier.json still say "measured" (m9, m10). |
| TPP 33% in emulation (M7) | ✓ s06: "TPP: 33% in emulation"; footer "TPP ASPLOS'23 (emulated)". No "pre-production" left anywhere (grep). |
| $/bit slowdown 2.5–3× (M11) | ✓ s04 headline "~2.5–3×"; footer "2.4–3.1× depending on % vs log-rate [D]". s01, s04 and s09 now all say ~2010 (06:20 edit), consistent with SYNTHESIS "after ~2010". s09 table-note residue: m7. |
| Interface energy, no blanket 2–4× (M3) | ✓ No "2–4×" on any slide (grep). The s03 takeaway and footer follow ORCH. However, the zHBM point on the same chart still contradicts "core energy is the next limit": report M4 is unresolved. See **M1**. |
| Mooncake +75% is trace replay (M1) | ✓ s07 B2: "(+75% requests [trace replay])"; tooltip: "load test replaying real Kimi traces (10P+10D vs vLLM-[20M])". |

### r4_accuracy items

| r4 item | Status |
|---|---|
| M1 Wang 10–80% | ✓ Dropped from the s05 caption and footer. SYNTHESIS §2 l.110 still carries it (m10). |
| M2 s03 toggle qualifier | ~ The r4-decided wording "0.3–0.8 pJ/bit **by toggle rate**" was shortened at 06:21 to "HBM I/O is already 0.3–0.8 pJ/bit vs Raptor's 0.37". The footer keeps "0.3 (real toggle) / 0.80 (50% toggle)", so this is traceable, but the slide body has lost the qualifier. Folded into M1. |
| m1 B1 ratio | ✓ "~3.2–3.6× [D]"; the tooltip gives both the link-capped spec and the Table III measured 3.4×. Verified against Vistara Table III (497 vs 48 GB/s, all reads) and Table V (57.6 GB/s / 256 GB). |
| m2 HBM3E 33–43 | ✓ s05 bar, s05 aria-label, s07 tooltip (GB200 added) and tier_frontier hi = 43.0. SYNTHESIS l.113 still says "~33–42" (m10). |
| m3 RDMA 4 KB on s02 | ✓ ■ at 5.58 µs; label "■ 4 KB page: 3.9, 5.58 µs"; aria-label updated. |
| m4 s02 data block (Pond row) | ✗ open: m2. |
| m5 s08 footer | ✓ d-Matrix removed; bar inputs added. ✗ "assumed x16" not stated: m6. |
| m6 zHBM 4-stack | ✓ "zHBM = 4-stack projection (others 8-stack)". |
| m7 cable 12 ns | ✗ open (now "6 ft (~2 m)"): m3. |
| m8 s02 axis title | ✓ |
| m9 Azure tag | ✗ open: m5. |
| m10 "per direction" | ✗ open: m4. |
| m11 SRAM naming | ✗ open: m11. |
| m12 60–70% missing from SYNTHESIS | ✗ open on the synthesis side. Berger TC'26 was read in full online (notes/q5 l.256–261, author version), so the s04 [P] tag is defensible, but the PDF is not in fulltext/. |
| m13 data-file residue | ✗ open: m9. |
| m14 SYNTHESIS CXL x8/x16 | ✗ open: m10. |

---

## Part 2: Findings

### Major

#### M1 · s03: the zHBM point (0.49 pJ/bit "total DRAM power") contradicts the takeaway "core energy is the next limit"

This is report_r1_technical M4, still unresolved.

**Element**
- Chart point "zHBM 3D (Samsung proj.)" at 0.49 pJ/bit and 31.8 TB/s.
- Caption: "zHBM 0.49 = projected total DRAM power ÷ peak BW".
- Takeaway (06:21 edit): "After the interposer, most energy sits in the DRAM core (O'Connor's HBM2 model: 3.45 of 3.92 pJ/bit…). 3D stacking removes the edge limit, but HBM I/O is already 0.3–0.8 pJ/bit vs Raptor's 0.37: core energy is the next limit."

**Source.** Samsung HBM base-die HC'26 s23 (image read directly):
- The baseline is "1x GPU + 4x HBM4E" at ~14 TB/s and ~225 W DRAM power.
- zHBM is ~32 TB/s ("230%") at ~125 W ("100W saving").
- Text: "the primary advantage is a dramatic decrease in I/O power… removes SERDES (Data align/DQ I/O)".
- The slide does not state the utilisation or access mix behind the power bars.

**What is wrong**
- The slide plots a 3D DRAM whose *total* DRAM power (core included) is 0.49 pJ/bit. It then concludes that core energy, 3.45 pJ/bit in the HBM2 model, is what remains.
- Both cannot hold at full bandwidth. At HBM2-model core energy, 31.8 TB/s would need 31.8 × 8 × 3.45 ≈ **880 W** for the core alone, ~7× Samsung's 124 W. Samsung's own HBM4E baseline on the same basis is ~2.0 pJ/bit total, also below 3.45.
- So one of two things is true:
  - (a) Samsung's power is at an unstated sub-peak utilisation, so 0.49 is a **lower bound**, not a full-access energy; or
  - (b) per-bit core energy in HBM4E-class and 3D parts is far below O'Connor's 2017 HBM2 model.
- The slide says neither. A memory expert will see the takeaway refuted by the chart beside it.
- Related scope gap: "HBM I/O is already 0.3–0.8 pJ/bit" is HBM2 interposer-wire I/O and excludes the PHY and base-die SERDES. Samsung attributes ~100 W of HBM4E's ~225 W to I/O. That is ≈0.9 pJ/bit at 14 TB/s, which is not "already small".
- The 06:21 edit also dropped the r4-decided "by toggle rate", so the 0.3–0.8 range is now unexplained in the body.

**Fix** (keeps the binding r4 point; adds the scope)
- **Caption.** Replace "zHBM 0.49 = projected total DRAM power ÷ peak BW" with: "zHBM 0.49 = Samsung's projected DRAM power (utilisation unstated) ÷ peak BW, so a lower bound; Samsung's HBM4E baseline on the same basis ≈ 2.0 [D from V]."
- **Takeaway.** "…3D stacking removes the edge limit, but HBM2's interposer I/O is only 0.3–0.8 pJ/bit by toggle rate (PHY excluded) vs Raptor's 0.37: the DRAM core is the next limit, though HBM4-era core energy is not published (→ PIM, slide 7)."
- **Alternative.** Make the zHBM marker hollow, with "vendor projection, utilisation unstated" in its tooltip, and keep the caption sentence.

### Minor

1. **m1 · s05 NVMe: rebuild only.**
   - The source is fixed ("10–40 µs typical", 06:20:37), but s05.png (23:48) and deck.html / memory-closer-farther-deck.html still render "measured".
   - Fix: re-run `assemble.py --shots` and confirm s05 in deck.html.
   - This is listed so the fix is not lost; it is not a new error.

2. **m2 · s02 data block: stale Pond row (r4 m4).**
   - `est_pond_row5` still has:
     - `"path":"Pond projection (spec/emulated)"`;
     - `"target_row":"cxl_direct"`;
     - loc "MHD 2-8 socket 155 ns (182%)…".
   - The JS overrides it, so the render is correct, but the block feeds the report.
   - Berger p.6 (l.410 of pdftotext) says "increase latencies to 182-212% (155-180ns)" for 8–16-socket pools.
   - Fix: set `target_row: "cxl_mhd"`, path "Berger/Pond 8–16-socket MHD pool (modelled)", and loc "Fig. 3; p.6 '182–212% (155–180 ns)'".

3. **m3 · s02 subtitle: "A 6 ft (~2 m) cable adds ~12 ns each way" (r4 m7).**
   - 1.83 m at typical twinax velocity (~0.7c, ~4.8 ns/m) is ≈9 ns. 12 ns implies ~0.5c.
   - 6 ft also exceeds passive PCIe 5 reach (≤1.5 m, Octopus p.2), so a retimer adds ~10 ns round trip (Das Sharma Table 2).
   - The figure is Levis's [P], so it is traceable, and r4 decided to keep it.
   - Optional: "~9–12 ns".

4. **m4 · s05 sub and caption: "Peak, per-direction vendor specs" (r4 m10).**
   - HBM, DDR5, LPDDR5X and MRDIMM are half-duplex buses, so "per direction" applies only to the CXL and RDMA links.
   - No number changes.
   - Fix: "Peak vendor specs (CXL, RDMA: per direction)".

5. **m5 · s07 B1 pill and s10 cell: "Deployed (Meta, Google, Azure)": Azure is untagged (r4 m9).**
   - No s07 tooltip supports Azure.
   - Octopus [P], already cited on s02 and s07, states: "CXL-based memory expansion is already deployed in production in Azure" (notes/q5 §B3).
   - Fix: add that sentence to the `s07-b1-ev` tooltip with "Octopus arXiv 2501.09020 [P]". Optionally add "Azure M-series preview [Pr]".

6. **m6 · s08 footer: "~17× CXL-PNM ~1.1 TB/s vs x16 Gen5 64 GB/s [D from P]".**
   - The CXL-PNM full text (HPCA'24) gives "512GB of capacity and 1.1TB/s" (8 × LPDDR5X ×128 at 136 GB/s) but never states the link width or generation; grep finds no "Gen5", "x16" or "lanes".
   - Fix: write "vs an assumed x16 Gen5 link (64 GB/s)".
   - Similarly, "~3× Structera A… [V]": the ratio is ours, so tag it [D from V].

7. **m7 · s09 table note no longer matches the table.**
   - The note says "Table: published AI Impacts fits (2020$…)". But the Middle cell, worded per the r4 decision, reads "~33–37%/yr", and the 37 is our nominal 1990–2010 fit, not an AI Impacts 2020$ figure.
   - Fix: "Table: AI Impacts series averages (2020$), 33–36%/yr, plus our nominal fit (−37%); chart: our nominal fits, 1990–2010 and 2010–24."

8. **m8 · s04 footer ratio range.**
   - "2.4–3.1× depending on % vs log-rate" is computed from the published pairs (36/15, 33/12).
   - The box beside it uses 33–37%, and our nominal pair (−37 vs −12) gives 3.1× (%) or 3.6× (log).
   - The headline "~2.5–3×" is still fair.
   - Fix: add "(published pairs)" after 2.4–3.1×, or widen it to "2.4–3.6×".

9. **m9 · Data-file residue (r4 m13, still open).**
   - `tier_frontier.json`:
     - the DDR5 row `basis` still reads "spec (Vistara Table V: 256 GB / 57.6 GB/s…)", copied from the CXL row;
     - the RDMA `tag` is "P (as carried in synthesis)" but should be D (assumption-based);
     - the NVMe `idle_latency` says "measured" and should say "typical";
     - `sources.dm_s23.locator` places "HBM4 2–3 pJ/bit I/O" on slide 6, but it is on **s17** ("I/O energy | 2–3 pJ/bit | 0.37 pJ/bit").
   - `dram_price_trend.json`: the title says "1990-2017", and caveat 1 says "Series ends 2017… do not extend the line", while the rows (and s09) run to 2024.
   - The data feeds the report, so align it with the slides.

10. **m10 · SYNTHESIS v1.9 residue.** These are not visible on the deck, but the deck must "reflect" the synthesis.
    - l.110: "commercial CXL reaches 10–80% of local DRAM bandwidth [P: Wang]". This is the single-channel-card-vs-full-socket scope error (r4 M1), removed from s05 but kept here.
    - l.121: NVMe "10–40 µs measured [P: TPP Fig. 2]" should be "typical".
    - l.113: HBM3E "~33–42" should be 33–43.
    - l.119: CXL "0.06–0.25 … (x8/x16, 128–512 GB)" should be "(x8, …)".
    - Berger TC'26 "60–70%" (used on s04) is still absent from the synthesis exec summary.

11. **m11 · Small traceability polish.**
    - s05 footer "NV LP30 HC'26 s24" vs data block "NVIDIA Groq 3 LPX" (r4 m11). Use "NVIDIA Groq 3 LPX (LP30 deck) HC'26 s24".
    - s09 caption: the McCallum series consists of retail module prices, not contract prices (dram_price_trend caveat 2). Add "retail module prices" to "DRAM $/GB, nominal", since a DRAM-industry reader will ask.
    - s07 A1 tooltip: GH200 "~4.8 TB/s [S]". NVIDIA's 144 GB GH200 is commonly specified at 4.9 TB/s (34.0 GB/s per GB). This is unverified because no NVIDIA document is in fulltext/, and it does not change "~33–43".

---

## Part 3: Cross-slide consistency and spot checks (✓ = traced this round)

| Quantity | Slides | Status |
|---|---|---|
| First CXL hop "+~100–280 ns measured"; ASIC +100–160; 280 FPGA | s02 box, bracket and tooltip; s07 B1 and tooltip | ✓ Melody Table 1: local 114; CXL-A 214, CXL-B 271, CXL-C 394 (FPGA). Vistara 250 vs 130; Polar 265.2 − 146 = 119 |
| 214–394 ns "(394 = FPGA)" | s02, s05 | ✓ both |
| Switch ≥220 [V]; +284–305 [D from P] | s02, s07 | ✓ 549 − 265.2 = 283.8 |
| MHD 260–300 measured vs 155–180 modelled | s02 | ✓ Berger p.6 "182–212% (155–180ns)"; data-block residue m2 |
| Berger >270 / >345 | s02 | ✓ Fig. 3 "318%" / "405%" |
| Remote socket 142–255 | s02 | ✓ Berger/Pond Fig. 2: "Local 78, remote 142 (182%)"; "Local 115, remote 255 (222%)" |
| Pond −7–9% at 182/222% | s07 | ✓ simulated, 16-socket |
| Xeon Max ~20%, ~700 vs ~200 GB/s, 409.6 GB/s per stack | s02, s05 | ✓ arXiv 2505.14294 §I |
| DDR5 ~100–150 ns; NVMe typical; RDMA ~3.5–5.6 µs | s02, s05 | ✓ in source (s05 render stale: m1) |
| HBM3E 33–43; HBM4 54–73; MRDIMM 1.6 TB/s; DDR5 0.2–0.8; CXL 0.06–0.25, ring 0.225 spec | s05, s07, s08 | ✓ |
| B1 ~3.2–3.6× [D]; Vistara 0.13× nominal; 25% fewer servers | s07 | ✓ Table V; Table VII MRS "Reduced number of servers by 25%"; §ML PS "20–25% less servers". Tagging the 25% as "meas." is defensible (production) |
| Gholami 3.0 / 1.6 / 1.4; ~1.9× | s03 | ✓ |
| 1.92 kW at 2.4 pJ/bit | s03 | ✓ d-Matrix s6 text "requires 1.92KW for HBM power only (excluding fabric power)" [V] |
| Raptor 0.37 measured I/O; 296 W; 100 TB/s per card | s03, s05 | ✓ d-Matrix s23, s17 (HBM4 I/O 2–3 vs 0.37; 18 vs 100+ TB/s per card) |
| zHBM 31.8 TB/s, 124 W, 4 stacks | s03 | ✓ values; scope: M1 |
| O'Connor 3.45 of 3.92; I/O 0.3 real / 0.80 at 50% | s03 | ✓ §1 (1.21 activation + 2.24 on-die; 3.92 incl. ECC) and Table 3 ("∗ at 50% activity") |
| Meta TCO 15.6 → 37.1; power 14.6 → 33.3 | s04 | ✓ TPP Fig. 3 (two extractions, 0 disagreement) |
| Google 32% cold at 120 s; TPP 55–80% idle per 2 min | s04, s06 | ✓ |
| Production tiering ~20–25%; TPP 33% emulation; local 50–75% | s06 | ✓ ORCH and SYNTHESIS v1.9 exec |
| GB200 13.4 TB / 576 TB/s; 17 TB / 14 TB/s | s05, s08 | ✓ NVIDIA page (the 36 × 512 GB/s = 18.4 inconsistency is noted in the data file; rack figure used) |
| TrendForce 18/22/30 and 8/9/13 | s08 | ✓ consistent with ~3× wafer per bit (2.5–2.9× HBM vs non-HBM) |
| CXL-PNM 512 GB, 1.1 TB/s, LPDDR5X | s08 | ✓ §I, Table I; link width unstated: m6 |
| ~3× and >4:1 (expected) | s07, s08 | ✓ Micron FQ2-25 p.6 |
| s09 fits −37 / −12; break 2010.5 labelled ~2010 | s01, s04, s09 | ✓ consistent after the 06:20 edit |
| "Do not say" sweep: no "2–4×", "off DDR", "pre-production", "10–80%", "33–42", "50–70 ns", "passive HBM", "3D bonding", "DRAM-on-logic" | all | ✓ (grep over all slide sources) |

**Footers and tags.** Every number I checked carries its qualifier (measured / sim. / modelled / spec / V / P/V / D / S), with the exceptions in M1 (zHBM utilisation), m5 (Azure) and m6 (assumed link width). Footer version citations (v1.6) are acceptable under the r3 decisions.
