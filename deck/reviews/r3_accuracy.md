# Deck review r3: technical accuracy and traceability

Reviewer: fresh accuracy reviewer, round 3, 2026-10-03.

**Inputs**
- `slides/s01–s10.html`: body text, JSON data blocks, tooltip strings and footers.
- `shots/s01–s10.png` (23:23, after the last slide edits at 23:22–23:23).
- `reviews/ORCH_NOTES.md`, `r1_decisions.md` and `r2_decisions.md`. The decisions are binding.
- `reviews/r2_accuracy.md`, for context only.
- `data/*.json` (reconciled files only).
- `SYNTHESIS_v1.7.md`.

**Full texts re-read with pdftotext this round**
- O'Connor FGDRAM: §1 and Table 3.
- UniIC IEDM'20: Table 1.
- PolarCXLMem: Tables 1 and 2.
- Pond, and Berger IEEE Micro'23 (Fig. 3 and p.6 text).
- Octopus arXiv 2501.09020.
- Newton MICRO'20. It is now in `fulltext/`, which it was not in round 2.
- Vistara, TPP, Lagar-Cavilla, Mahar, Wang (ATC'25 KV$), Hao SIGMOD'24.
- Xeon Max arXiv 2505.14294, Gholami.
- Micron Mar-2025 and Dec-2025 remarks.

**Other sources checked**
- TrendForce 2026-06-02 HTML and the GB200 NVL72 spec HTML.
- Hot Chips 2026: d-Matrix s6, s8, s13, s17, s18 and s23 (text, plus the s6 and s13 images viewed directly), and NV LP30 s24.

**Arithmetic recomputed**
- s09 fits: −37.1%/yr and −12.0%/yr.
- O'Connor split: 1.21 + 2.24 = 3.45 of 3.92 pJ/bit. The remaining 0.47 is 0.3 interposer I/O plus ECC.
- Interface ratios: 0.80 / 0.37 = 2.2; 1.5 / 0.37 = 4.1; 0.80 / 0.88 = 0.9; 1.5 / 0.88 = 1.7; 0.80 / 0.66 = 1.2; 1.5 / 0.66 = 2.3.
- GB200: 576 / 13.4 = 43.0 and 14 / 17 = 0.82.
- Vistara: 0.8 / 0.30 = 2.67 and 0.8 / 0.225 = 3.56.
- All s05 bar endpoints, and the s03 extrapolations (1,152–1,232 W; Raptor 296 W; zHBM 0.487 pJ/bit).

**Verdict: needs revision (light).** Blocking 0 · Major 6 · Minor 10.
- All r2 majors are resolved, and nine of the eleven r2 minors are resolved.
- The six new majors are scope, qualifier or traceability problems. Each has a one-line text fix, except M2, which moves one hollow bar.

---

## Part 1: r2 items, checked against the current text

| r2 item | Status | Current text / evidence |
|---|---|---|
| **MA1** s03 HBM2 energy scope | ✓ | Takeaway: "in O'Connor's HBM2 model, 3.45 of 3.92 pJ/bit (full access, modelled)". Caption: "its HBM2 point (≈5.6 pJ/bit) uses its own, different scope." Matches the G2 s03 decision. Arithmetic checks against O'Connor PDF p.2 ("2.24 pJ/bit … 0.3 pJ/bit … 1.21 pJ/bit … 3.92 pJ/bit (including ECC overhead)"). |
| **MA2** "in production 20–33%" | ✓ (wording) | s06: "~20–25% of DRAM moves off DDR in production (zswap, TMTS, Vistara) at <5% slowdown, 33% in TPP's pre-production CXL tests". s04 box 4 is now a pointer. TPP p.~9 confirms pre-production FPGA CXL. *New problem with "off DDR": see M3.* |
| m1 s05 hollow rule | ✓ | "hollow = discontinued tier or single-product point (vendor peak or spec)". Marks are consistent with this rule. |
| m2 stale data files | mostly ✓ | `tier_frontier.json` now has ~100–150 ns, ~3.5–4.6 µs, 10–40 µs and "3D DRAM–logic stacking". The s05 aria-label reads "3D DRAM–logic stacking point". ORCH_NOTES gives +~100–280. SYNTHESIS v1.7 gives +~100–280 and App. A ~78–146. Residue remains: see minor m4. |
| m3 "interface 2–4×" tag | applied, but see **M1** | The footer now reads [D from P]. The 2–4× cannot be reproduced from the two cited primary sources. |
| m4 394 = FPGA | ✓ | s02 row label "214–394 ns (394 = FPGA)" and row tooltip "394 ns = Melody CXL-C (FPGA device); ASIC devices 214–271 ns." s05: "214–394 ns measured (FPGA at 394)". |
| m5 s05 [S] latency tags | ✓ | "~300 ns (GPU, HBM3) [S]" and ">200 ns (Grace) [S]". |
| m6 Newton "sim." | ✓ **verified** | `fulltext/newton-micro20.pdf` p.~7: "GPU simulation: We use GPGPUsim … configure GPGPUsim as a Titan-V"; "Newton simulator is based on the cycle-level DRAMsim2". Also: "Our simulations show: (1) Newton achieves 54x speedup over a Titan V-like GPU; (2) Newton is 10x faster than … Ideal Non-PIM". The caveat has moved to A2 Best evidence. |
| m7 s08 Catch wording | ✓ | "a CXL controller sees only channel-level bandwidth (~3–17× its link [D]), not bank-level bandwidth." |
| m8 ~1.9× | ✓ | "~1.9× per 2 years [D: 3.0/1.6]". |
| m9 Wang 10–80% | **open** | Still "intro only read" (tier_frontier.json `wang_cxl.path`), but cited as [P]. See **M5**. |
| m10 source labels | partly ✓ | Octopus is "arXiv 2501.09020 (NSDI'26)" on s02 and s07 ✓. LP30 / "Groq 3 LPX" naming is still split; see m6. |
| m11 Raptor s17 | optional, not done | No action needed. |

**Decision compliance (r1 + r2).** Every G-1…G-8, G2-1…G2-6 item and per-slide decision I checked is applied verbatim:
- headlines;
- the s01 points;
- the s03 headline, takeaway and caption note;
- the s04 pointer box;
- the s05 row label and [S] tags;
- the s06 production line;
- the s07 Newton placement and A2 main cost;
- the s08 three-block rewrite, col-3 header and XCENA wording;
- the s09 middle cell, "—" and table note;
- the s10 rivals box and evidence list.

---

## Part 2: New findings

### Major

#### M1 · s03: "cuts interface energy 2–4× [D]" cannot be reproduced from the cited primary sources; it depends on the vendor Raptor figure
- **Element.** The takeaway reads: "3D DRAM–logic stacking … cuts interface energy 2–4× [D]". The footer reads: "interface 2–4× [D from P]: O'Connor Table 3, UniIC IEDM'20 Table 1."
- **What is wrong.**
  - The two cited tables give:
    - HBM2 I/O 0.80 pJ/b at 50% activity (O'Connor Table 3, PDF p.6);
    - HBM ~1.5 pJ/b including PHY, and SeDRAM 0.88 pJ/b (UniIC IEDM'20 Table 1, "Energy efficiency (pJ/b) ~1.5[2] … 0.88").
  - From these primary numbers alone the 3D cut is **0.9–1.7×** (0.80 or 1.5 ÷ 0.88). Using UniIC VLSI'23's 0.66 it is **1.2–2.3×**.
  - "2–4×" appears only when the 3D value is d-Matrix's 0.37 pJ/bit, which is [V] (s23, "Measured I/O Power"): 0.80 / 0.37 = 2.2 and 1.5 / 0.37 = 4.1.
  - Against O'Connor's toggle-adjusted interposer wire energy (0.3 pJ/b, §1) there is no cut at all.
  - The [D from P] tag (G2-5) therefore misstates the provenance. Readers following the footer will get ~1–2×.
- **Fix (pick one).**
  - (a) Keep the claim and tag it honestly. Takeaway: "…cuts interface energy ~2–4× [D from V/P]". Footer: "interface 2–4× [D from V/P]: Raptor 0.37 [V, s23] vs HBM2 I/O 0.80 (O'Connor Table 3, 50% toggle) and ~1.5 incl. PHY (UniIC IEDM'20 Table 1); primary-only 3D (UniIC 0.66–0.88) gives ~1–2×."
  - (b) State the range conservatively: "cuts interface energy ~1–4× (≥2× only on d-Matrix's vendor figure)".
- **Note.** SYNTHESIS §2.0 makes no "2–4×" claim. This is deck-only arithmetic, so it should carry its full derivation.

#### M2 · s02: the "Pond 155–180 ns (modelled)" hollow bar is drawn in the CXL **direct-attach** row, but the figure is for **multi-headed-device (MHD) pools**
- **Element.** The hollow dashed bar under "CXL direct-attach" is labelled "Pond 155–180 ns (modelled)". Data: `est_pond_row5`, `target_row: "cxl_direct"`.
- **Source.**
  - Berger IEEE Micro'23 p.~6: "small 8- and 16-socket pools using MHDs increase latencies to 182–212% (155–180ns)".
  - Fig. 3 rows read "MHD … 155ns, 182%" and "180ns, 212%".
  - Pond Fig. 2 labels: "8-socket Pond (155ns, 182%)" and "16-socket Pond (180ns, 212%)".
- **What is wrong.** The topology is mislabelled. The estimate belongs beside the measured "CXL multi-headed device (2–4 ports) 260–300 ns" row (Octopus). There it would also show the point SYNTHESIS App. A makes: spec- and model-based estimates are optimistic, 155–180 modelled vs 260–300 measured.
- **Fix.**
  - Move the bar to the `cxl_mhd` row, labelled "Pond/Berger 8–16-socket MHD pool 155–180 ns (modelled)".
  - Set `target_row: "cxl_mhd"` and give the MHD row the taller height.
  - Optionally add to its tooltip: "vs 260–300 ns measured for 2–4-port MHDs (Octopus)".

#### M3 · s06: "~20–25% of DRAM moves **off DDR** in production (zswap, TMTS, Vistara)" is wrong for two of the three cited systems
- **Element.** The s06 production line: "Mixing pays: ~20–25% of DRAM moves off DDR in production (zswap, TMTS, Vistara) … DDR still holds 50–75% of capacity."
- **What is wrong.**
  - **zswap** compresses cold pages into a pool in the same DRAM (Lagar-Cavilla p.1: "proactively compresses cold memory pages"). The data never leaves DDR DIMMs.
  - **TMTS's** tier 2 was Optane DIMMs on the DDR bus (notes/r2d_review l.49; notes/q2 l.179 "25% of memory on Optane").
  - **Vistara's** tier is recycled **DDR4** behind CXL.
  - So "off DDR" is false for zswap under either reading (media or bus). Vistara is still DDR media, and TMTS is still on the DDR bus.
  - G2-1's approved wording was "moved to slower tiers". "Off DDR" was introduced in the s06 rewrite.
- **Fix.** "Mixing pays: ~20–25% of DRAM moves to cheaper tiers in production (compressed DRAM, Optane, CXL-attached DDR4: zswap, TMTS, Vistara) at <5% slowdown, 33% in TPP's pre-production CXL tests; **local DRAM** still holds 50–75% of capacity." If space is tight, "moves to cheaper tiers" plus "local DRAM still holds" is enough.

#### M4 · s05 (also ORCH_NOTES, SYNTHESIS §2 footnote): RDMA "~3.5–4.6 µs (64 B–4 KB)" is contradicted at 4 KB by a cited source
- **Element.** s05 latency cell "~3.5–4.6 µs (64 B–4 KB)" (G-4 wording).
- **Source.** PolarCXLMem Table 2 (PDF p.4), RDMA read: 64B 4.55 · 512B 4.79 · 1KB 4.91 · **4KB 5.58 µs**; write 4KB 5.06.
  - The 4.6 upper end is PolarCXLMem's **64 B** value.
  - The only 4 KB value in the range is Fastswap's 3.9 µs (40 Gb ConnectX-3).
  - With both sources the 64 B–4 KB span is **~3.5–5.6 µs**.
- **What is wrong.** The parenthetical implies the range covers 4 KB transfers. One of the two 4 KB measurements we cite sits outside it.
- **Fix.** On s05, write "~3.5–5.6 µs (64 B–4 KB)". Alternatively write "~3.5–4.6 µs (64 B); 3.9–5.6 µs (4 KB)". s02 is fine as drawn: it plots 64 B points plus the Fastswap 4 KB square. Mirror the change in ORCH_NOTES and in SYNTHESIS §2 footnote 2.

#### M5 · s05 caption and footer: "CXL sustained BW is 10–80% of local DRAM … Wang et al. arXiv 2405.14209 [P]" rests on an introduction-only read (FULL TEXT NEEDED)
- **Element.** The s05 caption sentence and its footer source tagged [P]. SYNTHESIS §2 also has "[P: Wang et al.]".
- **What is wrong.**
  - `tier_frontier.json` → `sources.wang_cxl.path`: "notes/q2_disaggregation.md line 229 (intro only read)".
  - The paper is not in `fulltext/` and is not on SYNTHESIS App. D's "Read in full" list.
  - The tag legend defines [P] as "primary, read in full", so the tag misstates the evidence. The issue has been open since r2 (m9).
- **Fix.**
  - **FULL TEXT NEEDED: Wang et al., "Exploring and Evaluating Real-world CXL", arXiv 2405.14209.** Confirm 9.8–80.3% in the results section.
  - Until then, retag it as "[P, intro only]" or "[pend]", or drop the sentence. Its only role is to say that sustained bandwidth is below peak.

#### M6 · s08: "HBM4E >4:1" is stated as fact; Micron gives it as an expectation
- **Element.** s08 col-2 text: "HBM3E takes ~3× the wafer per bit (HBM4E >4:1), a named 2026-shortage driver."
- **Source.** Micron Mar-2025 remarks p.6: "we expect the trade ratio to increase with HBM4 and then again with HBM4E when we expect it to exceed 4 to 1." HBM4E is not shipping.
- **What is wrong.** The projected qualifier is missing (G-8). The s07 A1 tooltip correctly says "expects", so s07 and s08 also disagree.
- **Fix.** On s08 write "(HBM4E expected >4:1)". Optionally make the same change in SYNTHESIS §4 A1 ("HBM4E takes >4:1") and §5.2 ("HBM4E exceeds 4:1").

### Minor

1. **s05 vs s08: the HBM3E bar stops below GB200.**
   - s05 plots HBM3E at 33–42 (B200 41.7). s08 shows GB200 NVL72 HBM3E at ≈43 (576 / 13.4; also GB200 superchip 16 TB/s / 372 GB = 43.0, same spec table).
   - Fix: extend the bar to 43 and add "GB200" to the example line, or keep 33–42 and say why GB200 is excluded.
   - The GH200 input (144 GB at "~4.8 TB/s", tagged [S]) should also be checked against NVIDIA's own GH200 spec. I recall 4.9 TB/s (34.0), but did not verify it. Either way it rounds to "~33–34".
2. **s07 inline tags do not match the values.**
   - A1 Best evidence shows `V` on "~33–73 GB/s per GB". That value is our arithmetic; the tooltip says "Derived [D] from vendor peak specs". Use `D`.
   - A1 Main cost shows `V` on "~3×". The footer gives Micron as [P/V]. Use `P/V`, or add P/V to the column key.
3. **s02 NVMe tooltip** reads "Upper end up to ~50 µs [pend]". G-4 and SYNTHESIS §2 say "unverified". Use "[unverified]" for consistency (`latency_ladder.json` also says FULL TEXT NEEDED for a Micron spec).
4. **Data-file residue (feeds the report):**
   - `tier_frontier.json` `sources.nv_spec.path` and `sources.synth` cite SYNTHESIS_v1.3. Update them to v1.7.
   - In the same file, the DDR5 RDIMM row `basis` reads "spec (Vistara Table V: 256 GB / 57.6 GB/s…)". That text was copied from the CXL row; it should say "JEDEC 6400 MT/s × 12 ch [D]".
   - The RDMA row has `tag: "P (as carried in synthesis)"`, but `extraction.cross_check.resolved` already reclassifies it as [D, assumption-based]. Set the tag to "D".
   - The HBM4 row `energy_scope` reads "I/O (d-Matrix s6 label for HBM4)", and `sources.dm_s23.locator` says "slide 6 HBM4 2–3 pJ/bit I/O". The s6 axis is "Data-Movement Energy"; the "I/O energy 2–3 pJ/bit" label is on **s17** (image description), and s8 says "(system)". Correct both to s17.
   - `dram_price_trend.json` caveat 1 still says "Series ends 2017 … do not extend the line". The rows run to 2024 (extractor B, Wayback). Update the caveat.
5. **SYNTHESIS v1.7 residue (the deck must reflect it):**
   - §8 still says "Passive HBM with logic base dies…". G-3 replaced this with "HBM without in-stack compute", and s10 uses the new wording.
   - §4 A2 says "Newton: 54× vs a **real** GPU". Newton's GPU is a GPGPU-Sim Titan V-like model (see the r2 m6 evidence above). Write "vs a simulated Titan V-like GPU".
   - §2 table gives CXL expander "0.125 peak per direction". s05 plots 0.0625–0.25 (32 GB/s over 128–512 GB). Align the two, or note that 0.125 is the 256 GB midpoint.
   - The revision history stops at v1.2.
6. **SRAM naming.** The s05 data block reads `"NVIDIA Groq 3 LPX HC'26 s24"`, the s05 footer "NV LP30 HC'26 s24", and SYNTHESIS "Groq 3 LPX". The slide itself (NV_HC2026_LP30_Final s24) is titled "NVIDIA Groq 3 LPX Rack Overview". Use "NVIDIA Groq 3 LPX (LP30 deck) HC'26 s24" in the footer.
7. **Handy tag.** The s08 footer has "Handy HC'26 s4 [S]" (r1 m14 decision), while SYNTHESIS §5.2 and the exec summary have "[V: Handy]". Pick one. [S] fits an analyst's tutorial; update SYNTHESIS to match.
8. **s02 "+1 hop" bracket.** It spans from Local DDR down to the CXL direct-attach row, so it also encloses the Remote-socket, Grace and GPU rows. A reader can take "+~100–280 ns" as applying to those paths. Start the bracket at Local DDR, but draw a light tick only at the two endpoint rows (or skip the in-between rows). Alternatively, add "(local DDR → CXL direct)" to line 2.
9. **s04 "Capacity per unit of CPU performance: fell ~60–70% in 10 years" (Berger TC'26 [P]).**
   - It is traceable only via notes/q5 l.261 (read online; not in `fulltext/`).
   - It is absent from SYNTHESIS v1.7.
   - Add it to SYNTHESIS §0/exec "Capacity cost", or tag the slide "[P, read online]". No change to the number.
10. **Two different "Wang et al." in the deck.** s06 cites Wang et al. ATC'25 (Alibaba KV$, arXiv 2506.02634) and s05 cites Wang et al. arXiv 2405.14209 (CXL bandwidth). Disambiguate with a venue or arXiv id in both footers; s06 has the venue, s05 has the id. Optionally add "(KV$)" and "(CXL)".

---

## Part 3: Cross-slide consistency of repeated numbers

| Quantity | Slides | Status |
|---|---|---|
| First-hop CXL adder "+~100–280 ns measured" (ASIC +100–160; 280 FPGA) | s02 callout, bracket and tooltip; s07 B1 and tooltip | ✓. SYNTHESIS v1.7 §1/§4/App. A match. |
| Switch ≥220 ns [V]; +284–305 [D from P] | s02, s07 | ✓. Octopus p.3 "adds at least 220 ns of latency [50]". PolarCXLMem 549 − 265.2, 651 − 345.9. |
| CXL direct 214–394 ns (394 FPGA) | s02, s05 | ✓ |
| Local DDR 78–146 vs DDR5 ~100–150 | s02, s05 | ✓. Pond p.~10 "78ns NUMA-local … 142ns remote", "115ns … 255ns". |
| RDMA 3.55–4.55 (64 B) + 3.9 (4 KB) vs "~3.5–4.6 µs (64 B–4 KB)" | s02, s05 | **✗ at 4 KB: M4** |
| NVMe 10–40 µs | s02, s05 | ✓ |
| Xeon Max ~20% latency; ~700 vs ~200 GB/s; peak 1.64 TB/s | s02, s05 | ✓. arXiv 2505.14294 p.1: "about 700 GB/s and 200 GB/s"; "about 20 % higher memory latency". |
| HBM GB/s per GB 33–42 / 54–73 / ~33–73; GB200 43 | s05, s07, s08 | ✓ within rounding, but the s05 bar misses GB200 (m1) |
| GB200 13.4 TB / 576 TB/s; 17 TB / 14 TB/s | s08 (s05 tooltip 0.82) | ✓. nvidia.com spec table. |
| Vistara 0.225 spec; 0.13× nominal; 2.7–3.6×; 25% fewer servers | s05, s07 | ✓. Vistara Table V "CXL 256 57.6 30 0.7 0.13". Text: "up to a 25% reduction in server count"; Table "MRS … Reduced number of servers by 25%". |
| ~3× wafer per bit; HBM4E >4:1 | s05, s07, s08 | Numbers ✓ (Micron Mar-25 p.6; Dec-25 p.5 "3-to-1 trade ratio"). s08 lacks "expected": M6. |
| $/bit −33–36 → −12–15%/yr; ~2.5× | s01, s04, s09 | ✓ (34.5 / 13.5 = 2.6). Chart fits −37 / −12 recomputed. |
| Production tiering ~20–25%; TPP 33% pre-production | s06 only (s04 points to s06) | Wording follows G2-1 except "off DDR": M3 |
| Google 32% cold at 120 s; 1–61% | s04, s06 | ✓. Lagar-Cavilla p.3 "At … T = 120 s … 32% of memory usage is cold"; p.2 "1% to 61% across different clusters". |
| Meta 55–80% idle per 2 min | s04, s06 | ✓ (TPP p.3) |
| Mahar ≤10% bytes → 90%-tile bandwidth, 30 s, nine workloads | s06 | ✓. Mahar p.9: "90%-tile bandwidth is contributed by less than 10% of the memory capacity". |
| Alibaba 10% → 77% reuses | s06 | ✓. Wang ATC'25 p.2. |
| Hao ~1/6 local serves >half of accesses | s06 | ✓. Hao p.31:16 "serves more than half of the tuple accesses". |
| Pond 7–9% sim.; MHD pool 155–180 modelled | s07; s02 | ✓ values; s02 row placement: M2 |
| Gholami 3.0 / 1.6 / 1.4 ×/2 yr; 60,000 / 100 / 30 × | s03 | ✓. Fig. 1 legend. |
| d-Matrix replot points; 2.4 pJ/b → 1.92 kW; Raptor 0.37 → 296 W | s03 | ✓. s6 image viewed; s23 text. |
| Raptor card 32 GB, 100 TB/s → ~3,100 | s05 | ✓. s13 image viewed: "Card … 32 GB … 100 TB/s". |
| SRAM 40 PB/s / 128 GB → ~3×10⁵ | s05 | ✓. LP30 s24 text. |
| TrendForce wafer 18 / 22 / 30, bits 8 / 9 / 13 | s08 | ✓. Press release: "approximately 18%, 22%, and 30% … by the end of 2025, 2026, and 2027 … 8%, 9%, and 13% … during the same period". |
| "Deployed (Meta, Google, Azure)" / "deployed at scale" | s07, s10 | ✓ |
| Synthesis version in footers | s01, s06, s07, s10 cite v1.6 | ✓ (ORCH: acceptable) |

## Verified this round, no change needed (spot list)

- **s01.** The four points match SYNTHESIS exec. "Within each workload" equals SYNTHESIS's "per object, not per workload". The schematic directions match the s05 placement.
- **s04.**
  - TPP Fig. 3 six-generation labels (`meta_memory_share.json`, 0 disagreement).
  - The pointer box replaces the duplicated production claim.
- **s07.**
  - AttAcc 1.72×.
  - AiM 6 vs 8 GB.
  - Mooncake "+75% … trace replay".
  - Berger "most switch-based configurations are ROI negative" (modelled).
  - B1 Software and Optane lines match SYNTHESIS §4.
- **s09.**
  - The quote and paraphrase follow the G2 s09 decision.
  - The caveat follows G-1 (Wulf & McKee = memory wall).
  - The chart break at 2010.5 is labelled "~2011".
- **s10.** The predictions, confidence levels, refutations and rivals match SYNTHESIS §7–§8 and the exec summary. Data-notes pendings match App. D.
