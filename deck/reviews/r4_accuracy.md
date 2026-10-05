# Deck review r4: technical accuracy and traceability

Fresh accuracy reviewer, round 4, 2026-10-03.

**Inputs**
- `slides/s01–s10.html`: body text, JSON data blocks, tooltip strings, aria-labels and footers.
- `shots/s01–s10.png` (23:35).
- `reviews/ORCH_NOTES.md` and `r1/r2/r3_decisions.md` (binding). `r3_accuracy.md` for context.
- `data/*.json` (reconciled files only).
- `SYNTHESIS_v1.8.md`, plus a diff against v1.7.

**Full texts re-read this round** (pdftotext, unless noted):
- Wang arXiv 2405.14209: §I, Table I, §III.
- Vistara: Tables III, V and VI, and §§ on production.
- Pond: Finding 9 and Fig. 21.
- Berger IEEE Micro'23: Fig. 3 and p.6.
- Melody: Table 1 and §3.2.
- Octopus: Fig. 2 and pp.1–3.
- PolarCXLMem: Tables 1 and 2.
- Xeon Max arXiv 2505.14294: §I.
- O'Connor: §1 and Table 3.
- Das Sharma: Table 2.
- Mooncake §5, Mahar §5.1, TPP §1/§3, Hao §5.5, Wang ATC'25 §1.
- AttAcc §7.6, CXL-PNM §I, AiM 11.1.
- Micron Mar-25 and Dec-25 remarks; TrendForce 2026-06-02.

**Hot Chips 2026**
- Text checked: AMD MI400 s3/s5, Rubin s8, Vera s28, DMR s10/s15/s20, LP30 s24, Samsung KaramHwang (8×), d-Matrix s6/s13/s17/s18/s23, Samsung base-die s9–s11/s23.
- Images viewed directly: d-Matrix s6 and Samsung base-die s23.

**Arithmetic recomputed**
- s09 fits: −37.1 and −12.0%/yr. A 2011–24 window would give −10.5%.
- HBM trend extrapolation to 100 TB/s: 1.44–1.54 pJ/bit.
- zHBM: 124 W ÷ (31.8 TB/s × 8) = 0.487 pJ/bit.
- MI455X 23.3 TB/s ÷ 432 GB = 53.9; Helios 54.8; Rubin 72.7.
- MRDIMM: 16 × 12,800 MT/s × 8 B = 1,638 GB/s.
- CXL-PNM 1.1 TB/s ÷ 64 GB/s = 17.
- Das Sharma: 100 + 50 + 10 + 10 = 170.
- Berger: 40 + 25 + 5 + 25 + 15 + 45 = 155.

**Verdict: needs revision (light).** Blocking 0 · Major 2 · Minor 14.
- All six r3 majors are resolved.
- Of the ten r3 minors, seven are resolved and three remain open as data or synthesis residue (m11–m13 below).
- The two new majors are scope and qualifier problems, and each has a one-line fix.

---

## Part 1: r3 items checked against the current text

| r3 item | Status | Evidence |
|---|---|---|
| **M1** s03 interface 2–4× provenance | ✓ (tag) | The footer now gives the full derivation "[D from V/P]: Raptor 0.37 [V] vs 0.80 (HBM2, 50% toggle; O'Connor Table 3) to ~1.5 (UniIC IEDM'20 Table 1)". One residual qualifier problem remains in the takeaway: see **M2** below. |
| **M2** s02 Pond 155–180 bar in wrong row | ✓ | The bar is drawn in the MHD row as "Berger/Pond 8–16-socket MHD pool, modelled". The tooltip component sum is 40+25+5+25+15+45 = 155, matching Berger Fig. 3. Data-block residue remains: m4. |
| **M3** s06 "off DDR" | ✓ | The line now reads "production moves ~20–25% of DRAM to cheaper tiers (zswap, TMTS's Optane, Vistara's CXL)… local DRAM keeps 50–75%". This matches ORCH_NOTES and SYNTHESIS v1.8 exec. |
| **M4** RDMA at 4 KB | ✓ | s05: "~3.5–5.6 µs (64 B–4 KB page)". SYNTHESIS v1.8 §2 matches (PolarCXLMem Table 2: 4 KB 5.58 µs). Cross-slide gap with s02: m3. |
| **M5** Wang full text | ✓ read; **new scope problem** | The full text is in `fulltext/`. The 9.8–80.3% figure is real, but it compares a single-channel card with a whole socket: see **M1** below. The locator is also wrong. |
| **M6** s08 HBM4E expected | ✓ | "(HBM4E expected >4:1)". Micron Mar-25 p.6: "we expect it to exceed 4 to 1". |
| m1 HBM3E 33–43 / GB200 | ✓ on s05 bar and data block | Residue remains in the s05 aria-label and the s07 tooltip: m2. |
| m2 s07 tags D, P/V | ✓ | |
| m3 NVMe "[unverified]" | ✓ | |
| m4 data-file residue | **open** | See m13. |
| m5 SYNTHESIS residue | ✓ | v1.8 has "HBM without in-stack compute"; Newton is "vs a simulated GPU (GPGPU-Sim)"; CXL row is 0.06–0.25. |
| m6 SRAM naming | **open** | See m11. |
| m7 Handy tag | ✓ | SYNTHESIS v1.8 exec now gives "[S: Handy (Objective Analysis), HC'26]", matching s08 [S]. |
| m8 s02 bracket scope | ✓ | "+1 hop (DDR → CXL direct):". |
| m9 s04 60–70% in SYNTHESIS | **open** | See m12. |
| m10 two "Wang et al." | ✓ | s05 gives the arXiv id; s06 gives ATC'25. |

**Decision compliance.** Every r3 per-slide decision I checked is applied verbatim, and B is phrased per G3-1 on s01 and s07. On the s05 headline "or shared" was dropped, which the decision permits.
- s02: headline and "pointer-chase".
- s03: headline, takeaway and footer.
- s05: RDMA cell, 33–43, [P] tag and annotation 1.
- s06: production line.
- s07: pattern line, D and P/V tags, G3-1.
- s08: "expected" and "the shortage … did not cause B".
- s10: coupling evidence cell.

---

## Part 2: New findings

### Major

#### M1 · s05 caption: "CXL sustained BW is 10–80% of local DRAM" compares one single-channel card with a whole multi-channel socket
- **Element.**
  - s05 caption: "CXL sustained BW is 10–80% of local DRAM."
  - Footer: "Wang et al. arXiv 2405.14209, abstract and §III [P]".
  - SYNTHESIS §2 uses the same figure.
- **Source** (`fulltext/wang-cxl-2405.14209.pdf`):
  - **§I Introduction, p.1, right column.** This is where the figure appears; the abstract does not contain it: "Depending on the memory vendors, the peak bandwidth of the CXL memory varies a lot, ranging from 9.8% to 80.3% of the peak bandwidth of local DRAM."
  - **Table I (p.2).** Each CXL device is a **single DDR5 channel**:
    - CXL A: DDR5-4800, 38.4 GB/s;
    - CXL B: DDR5-8000, 64 GB/s.
  - **Local DRAM is a full socket:**
    - System A: 12 × DDR5-4800, 460.8 GB/s;
    - Systems B and C: 8 × DDR5-4800, 307.2 GB/s.
  - **§III.** It reports CXL as 17.1% and 46.4% of **remote-socket** DRAM. The 9.8–80.3% figure is not restated there.
- **What is wrong.**
  - The ratio mostly measures **channel count**: 1 channel vs 8–12. It does not measure a CXL efficiency penalty. A 1-of-12-channel card is ~8% of a socket by construction.
  - A memory expert will read "CXL sustained BW is 10–80% of local DRAM" as a per-channel or per-GB penalty, and that is not what the paper shows.
  - The sentence sits under a GB/s-per-GB chart, which strengthens that misreading.
  - "Sustained" is also not the paper's word: it reports measured peak, MLC maximum.
  - The locator "abstract and §III" is wrong.
- **Fix.** Pick one.
  - (a) Scope it: "Measured: one single-channel CXL card reaches 10–80% of a full socket's local-DRAM peak (Wang et al., 3 cards)." Footer: "Wang et al. arXiv 2405.14209, §I p.1, Table I [P]".
  - (b) Drop the sentence. The s05 point (CXL sits at or below capacity-maxed DDR in GB/s per GB) is already made by the bar and the Vistara ring.
  - Mirror the change in SYNTHESIS §2.

#### M2 · s03 takeaway: "cuts interface energy ~2–4×" omits the toggle-rate qualifier, and the same sentence's own O'Connor model contradicts it
- **Element.** The s03 takeaway reads: "…in O'Connor's HBM2 model, 3.45 of 3.92 pJ/bit (full access, modelled). 3D DRAM–logic stacking removes the edge limit and cuts interface energy ~2–4× [D], not core energy."
- **Source.**
  - O'Connor MICRO'17 §1 (PDF p.2): "transferring the data … 0.3 pJ/bit, considering actual application data toggle rates … each HBM2 access incurs 3.92 pJ/bit". So 0.3 pJ/bit is the I/O term inside the 3.92 the sentence quotes.
  - O'Connor Table 3 gives I/O 0.80 pJ/b "at 50% activity". The footer's 2–4× uses this value.
  - Raptor 0.37 pJ/bit is "Measured I/O Power" (d-Matrix s23). Its activity factor is not stated, and s10's data-notes list "scope of 0.37 pJ/bit" as pending full text.
- **What is wrong.**
  - The ratio holds only at O'Connor's 50% toggle assumption.
  - At the application toggle rate behind the 3.92 split quoted in the same sentence, HBM2's I/O is 0.3 pJ/bit, already below Raptor's 0.37.
  - A reader who knows O'Connor will see the contradiction inside one sentence. The footer discloses "50% toggle", but the claim on the slide carries no qualifier (G-8).
  - The r3 decision fixed the provenance tag; this toggle-rate qualifier is a separate gap.
- **Fix** (minimal wording).
  - "…cuts interface energy ~2–4× at equal 50% toggle [D from V/P]…", or
  - "…and brings interface energy to ~0.37 pJ/bit measured [V] (HBM I/O: 0.3–0.8 by toggle rate, ~1.5 incl. PHY), not core energy."
  - Either version keeps the slide's point: 3D stacking removes the edge limit, while core energy dominates.

### Minor

1. **s07 B1: the low end of "2.7–3.6× less bandwidth per GB" uses media-side bandwidth that the CXL links cannot deliver.**
   - The tooltip gives "CXL 57.6–76 GB/s / 256 GB = 0.225–0.30".
   - Vistara Table VI's "CXL Peak BW ≈76 GB/s" is the DDR4-2400 media peak: 4 channels × 19.2 = 76.8.
   - It sits behind "2× Vistara ASICs, PCIe Gen5 x8 each", which carry at most 2 × 32 = 64 GB/s per direction. Reads flow one way, so read bandwidth per GB is ≤0.25, and Table V's 57.6 → 0.225.
   - Measured, Table III all-reads gives 48 / 256 = 0.19 vs 497 / 768 = 0.65, a ratio of **3.4×**.
   - Fix: write "~3.2–3.6× [D]" (link-capped spec), or "~3.5× (measured, Table III)". Update the tooltip, and SYNTHESIS §4 B1 l.234 ("2.7–3.6×").
2. **Stale HBM3E "33–42".**
   - Two places still say 33–42: the s05 aria-label ("HBM3E at 33 to 42") and the s07 A1-evidence tooltip ("HBM3E ~33–42 (B200…; GH200…)", which omits GB200 43.0).
   - `tier_frontier.json` HBM3E `gbps_per_gb_hi` is still 41.7.
   - Align all three to 33–43, adding GB200 NVL72 576 / 13.4.
   - Optional, and unverified because no NVIDIA document is in `fulltext/`: GH200-144 GB is commonly specified at 4.9 TB/s (34.0), not 4.8, and shipping B200 is 180 GB (8 / 0.18 = 44.4). Neither changes "~33–44" materially.
3. **The RDMA 4 KB values differ between s02 and s05.**
   - s05's upper end 5.6 µs is PolarCXLMem Table 2's 4 KB read (5.58).
   - s02 draws only Fastswap's 4 KB square (3.9 µs) and labels "■ 4 KB page: 3.9 µs".
   - Fix: add a second ■ at 5.58 µs, or label it "■ 4 KB page: 3.9, 5.58 µs", so the s05 range traces to a mark on s02.
4. **The s02 data block is stale for the moved Pond bar.**
   - `est_pond_row5` still has `"path":"Pond projection (spec/emulated)"`, `"target_row":"cxl_direct"` and loc "MHD 2-8 socket".
   - The drawing code overrides it, so nothing renders wrong, but the data block feeds the report.
   - Set `target_row: "cxl_mhd"`, path "Berger/Pond 8–16-socket MHD pool (modelled)", and loc "Fig. 3 (2–8 and 16-socket MHD rows); p.6 text '8- and 16-socket pools … 155–180ns'".
5. **s08 footer has an orphan citation and is missing the inputs for ~3–17×.**
   - "d-Matrix HC'26 s13, s18 [V]" no longer supports any s08 text, since the rewrite cut "(d-Matrix too)". Drop it, or restore a short "(d-Matrix pairs Raptor with LPDDR5X)".
   - The ~3× and ~17× bars cite only "[D]". Add the inputs:
     - ~3× = Marvell Structera A, 4 × DDR5 ≈200 GB/s vs x16 64 GB/s [V] (SYNTHESIS §5.3, notes/r2d l.185);
     - ~17× = CXL-PNM 1.1 TB/s (HPCA'24 §I) vs an assumed x16 Gen5 64 GB/s link.
   - The link width is not stated in our extract of CXL-PNM, so say "assumed x16".
6. **The s03 caption does not give zHBM's package size.**
   - Samsung s23 compares "1x GPU + 4x HBM4E" with "1x GPU + 4x zHBM". The 31.8 TB/s is a **4-stack** figure, while the caption says "HBM = 8-stack package". Raptor's 100 TB/s is one card.
   - Absolute bandwidth positions therefore differ in scope.
   - Append "zHBM = 4 stacks; Raptor = one 8-chiplet card".
7. **s02 subtitle: "A 6 ft cable adds ~12 ns each way".**
   - 1.83 m at a typical twinax velocity (~0.7c, ~4.8 ns/m) is ~9 ns. 12 ns implies ~0.5c.
   - Also, 6 ft exceeds the ≤1.5 m passive PCIe5 reach (Octopus p.2), so a retimer adds ~10 ns round trip (Das Sharma Table 2).
   - The figure is Levis's own [P], so it is traceable. Writing "~10 ns" or "~9–12 ns" would pre-empt an expert's objection.
8. **s02 axis title: "Idle load-to-use latency"** also covers RDMA 4 KB page reads and NVMe block I/O, which are not load-to-use.
   - Fix: "Idle access latency (load-to-use; RDMA ■ and NVMe are page or block reads)", or add this to the caption.
9. **"Deployed (Meta, Google, Azure)" (s07 B1 pill, s10 confidence cell) is untagged.**
   - SYNTHESIS v1.8 §4 B1 l.240 tags Azure as "Azure M-series [Pr]". No s07 tooltip mentions Azure.
   - Add "Azure M-series [Pr]" to the s07 B1 evidence tooltip. On the slide, Meta and Google rest on [P].
10. **s05 footer: "Peak per-direction specs [D from V]".** HBM and DDR buses are half-duplex, so for them "per direction" is the total bandwidth. Only the CXL bar is truly per-direction.
    - No number changes.
    - Optional wording: "peak (CXL: per direction)".
11. **SRAM naming (r3 m6, open).** The s05 footer reads "NV LP30 HC'26 s24", the data block reads "NVIDIA Groq 3 LPX HC'26 s24", and the slide title is "NVIDIA Groq 3 LPX Rack Overview". Use "NVIDIA Groq 3 LPX (LP30 deck) HC'26 s24".
12. **s04 "capacity per unit of CPU performance fell ~60–70% in 10 years" (r3 m9, open).**
    - It is still absent from SYNTHESIS v1.8, which cites Berger TC'26 only for area vs perimeter (l.96).
    - It traces to notes/q5 l.261: "dropped by nearly 60–70%", read online.
    - Add it to SYNTHESIS exec "Capacity cost".
13. **Data-file residue (r3 m4, mostly open).**
    - `tier_frontier.json`:
      - `sources.synth` still cites SYNTHESIS_v1.3;
      - the DDR5 row `basis` still reads "spec (Vistara Table V: 256 GB / 57.6 GB/s…)";
      - the RDMA row `tag` is "P (as carried in synthesis)" but should be D;
      - `sources.dm_s23.locator` says "slide 6 HBM4 2–3 pJ/bit I/O". It is on **s17**: "I/O energy | 2–3 pJ/bit | 0.37 pJ/bit".
    - `dram_price_trend.json` caveat 1 still says "Series ends 2017", but the rows run to 2024.
14. **SYNTHESIS §2, l.120, CXL row.** It says "0.06–0.25 peak per direction (x8/x16, 128–512 GB)", but 0.06–0.25 is the x8 range only; x16 on 128 GB gives 0.5. s05 is internally consistent ("x8 Gen5 card"). Change SYNTHESIS to "(x8, 128–512 GB)".

---

## Part 3: Cross-slide consistency and spot verification (✓ = traced this round)

| Quantity | Slides | Status |
|---|---|---|
| First-hop CXL adder "+~100–280 ns measured" (ASIC +100–160; 280 FPGA) | s02 box, bracket and tooltip; s07 B1 | ✓ Melody Table 1 (local 114; CXL-A 214, CXL-B 271, CXL-C 394 FPGA); Octopus 230–270 vs 115; Vistara 250 vs 130; Polar 265.2 − 146 |
| Switch ≥220 ns [V]; +284–305 [D from P] | s02, s07 | ✓ Octopus p.3 "adds at least 220 ns [50]"; Polar 549 − 265.2, 651 − 345.9 |
| MHD 260–300 measured vs 155–180 modelled | s02 | ✓ Octopus Fig. 2 "CXL 2/4-port MPD 260–300 ns"; Berger p.6 |
| Berger >270 / >345 on the switch row | s02 | ✓ Both Fig. 3 rows include a switch (MHD + switch 32–64 socket; switch-only) |
| Melody tails | s02 | ✓ §3.2: "CXL-A and CXL-D exceed 700ns, while CXL-B and CXL-C reach 1µs" |
| Xeon Max ~20% / ~700 vs ~200 GB/s; peak 1,638.4 GB/s, 64 GB | s02, s05 | ✓ arXiv 2505.14294 §I: 4 tiles × 16 GB at 409.6 GB/s; "single-core pointer chase" |
| Local DDR 78–146; DDR5 ~100–150 | s02, s05 | ✓ |
| HBM4 54–73 | s05, s07 | ✓ MI455X "432 GB at 23.3TB/s" (MI400 deck); Helios 31 TB / 1.7 PB/s; Rubin 11 PB / 800 PB/s (fleet, footnoted) |
| HBM3E 33–43 | s05 (bar) vs s05 aria-label and s07 tooltip (33–42) | ✗ residue: m2 |
| MRDIMM 1.6 TB/s, 16 ch | s05, s10 | ✓ DMR s15 "16 channels … up to 12800MT/s MRDIMM"; s20 "1.6TB/s"; DMR LLC 1.28 GB supports s10 "large SRAM LLC" |
| Vera 1.5 TB / 1.2 TB/s | s05 | ✓ Vera s28 |
| SRAM 40 PB/s / 128 GB | s05 | ✓ LP30 s24 |
| Raptor 32 GB / 100 TB/s; 0.37 pJ/bit → 296 W | s03, s05 | ✓ d-Matrix s13 (card), s23 ("Measured I/O Power … 296 W"); s17 HBM4 I/O 2–3 pJ/bit |
| d-Matrix s6 replot points; 1.92 kW | s03 | ✓ image viewed; HBM1–HBM4 and DDR points within ±5% |
| zHBM 31.8 TB/s, 124 W → 0.49 | s03 | ✓ Samsung s23 image (BW bar ~32; DRAM power ~125 W vs ~225 W; "100W saving"); 4-stack scope: m6 |
| HBM4 stack 11 × 12.8 mm; PHY >8 × 4 mm; 26 × 33 reticle; 6 per die | s03 | ✓ Samsung s9 (2 xPU × 6 HBM; 26 mm × 33 mm), s10, s11 |
| O'Connor 3.45 of 3.92 | s03 | ✓ §1; toggle qualifier: M2 |
| Gholami 3.0 / 1.6 / 1.4; ~1.9× | s03 | ✓ (3.0^10 = 59,049; 1.6^10 = 110; 1.4^10 = 29) |
| Vistara 0.13× nominal; 0.225 spec; up to 25% fewer servers | s05, s07 | ✓ Table V "CXL 256 57.6 30 0.7 0.13"; abstract "up to a 25% reduction in server count for disaggregated ML inference"; Table VII MRS. 2.7× low end: m1 |
| Pond −7–9% at 182/222% | s07 | ✓ Finding 9: "16 sockets … 9% and 7% under 182% and 222%" (simulated) |
| Mooncake +75% | s07 | ✓ §5: replayed traces, 10P+10D vs vLLM-[20M], "approximately 75% more requests while adhering to the SLOs" |
| AttAcc 1.72× | s07 | ✓ §7.6 |
| PIM internal 4 / 8 / 16× | s07 | ✓ Samsung HC'26 LPDDR5X-PIM "76.8 GB/s, 614 GB/s (8x)"; notes/q1 Aquabolt 4×, AiM 16× |
| Micron ~3× and HBM4E >4:1 (expected) | s05, s07, s08 | ✓ Mar-25 p.6 quote matches the s07 tooltip verbatim; Dec-25 "3-to-1 trade ratio" |
| TrendForce 18/22/30 and 8/9/13 | s08 | ✓ "HBM wafer input among the top three suppliers … by the end of 2025, 2026, and 2027" |
| GB200 13.4 TB / 576 TB/s; 17 TB / 14 TB/s | s05, s08 | ✓ |
| CXL-PNM ~17× | s08 | ✓ "512GB of capacity and 1.1TB/s" ÷ 64 GB/s (x16 assumed: m5) |
| Skew statistics | s04, s06 | ✓ Mahar "nine workloads … 90%-tile bandwidth is contributed by less than 10%"; Alibaba "77 % of the reuses"; Hao "local memory size is only one sixth of remote … serves more than half of the tuple accesses"; TPP "four popular applications … 55-80% … idle within any two minutes"; Mooncake ">50% of cache blocks" unused |
| Production tiering ~20–25%; TPP 33% pre-production; local 50–75% | s06 | ✓ SYNTHESIS v1.8 exec and App. A table (Vistara 75%, TMTS 75%, Memstrata 50%) |
| $/bit 33–36 → 12–15%/yr; ~2.5×; nominal fits −37 / −12 | s01, s04, s09 | ✓ |
| IRAM 98.5 vs 4.55 nJ; GMS 1.44 vs 3.6–14.3 ms; NDP'14 quote with first clause | s09 | ✓ notes/r2c l.117, l.185, l.368 |
| Synthesis version in footers (v1.6) | s01, s06, s07, s10 | ✓ acceptable per r3 decisions |
| B wording "cheaper, extended or shared GB" | s01, s07 sub; s05 drops "shared" | ✓ per G3-1 allowance |

**"Do not say" lists (BRIEF §D).** I swept all ten slides for the binding phrases and found no violations. In particular:
- "closer cuts latency" does not appear.
- No 50–90 ns CXL adder is used.
- Das Sharma and Pond appear only as hollow marks.
- RDMA granularities are labelled.
- No energy values appear on s05.
- "0.13×" is always paired with "nominal".
- The s07 B1 line puts the 0.13× and 25% claims in separate clauses, and the tooltip states that the 25% comes from capacity, not price.
- "PIM is deployed" does not appear.
- The NDP'14 quote keeps its first clause.
