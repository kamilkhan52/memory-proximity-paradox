# R5: Review of SYNTHESIS_DRAFT v0.4

Reviewer: R5 (fresh reader, ISCA/ASPLOS PC stance), 2026-10-03.

**Inputs**
- SYNTHESIS_DRAFT.md v0.4
- r2d_review.md and ROUND4_DELTAS.md
- notes/r2a and notes/r3
- Full texts, checked with pdftotext: Berger IEEE Micro'23, PolarCXLMem, Hao SIGMOD'24, AttAcc, Lee 2018 (pages rendered to check Fig. 2a and Fig. 3), Micron Dec-2025 and Mar-2025 remarks, Intel Gen5 SerDes, UniIC IEDM'20, MXT, ZeRO-Offload, ByteDance, Fastswap, Boroumand, HeMem
- DIGITIMES (mhtml) and SemiAnalysis (mhtml, free part)
- HC'26 deck text: Handy, SK hynix, d-Matrix, DMR, MONAKA, MI400, Rubin, Vera
- Slide images: Handy slide 6, SK hynix slide 11

**Not checkable here.** These have no full text in `fulltext/`, so I did not re-verify them: Gholami, O'Connor, Pond, TPP, Vistara, Mooncake, ZeRO-Infinity, Teleport, Xeon Max arXiv 2505.14294. I did no new literature search. Items that rest on my own background are marked **[reviewer knowledge, unverified]**.

---

## Verdict

**Minor revision. No blocking issue for the thesis, but six must-fix items before the draft is circulated.**

The core answer now holds up and is mostly hedged well. In outline:
- The draft frames the question as a two-axis cost frontier: GB/s per GB against $/GB, under a latency constraint.
- Skew in access makes a mix of tiers cheaper than any single tier.
- It separates the deployed forms (A1, B1) from the ambitious ones (A2, B2).
- It treats market and vendor explanations as complements to the frontier argument.

All three fatal items from r2d (F1–F3) are fixed. M1–M12 are fixed or mostly fixed.

The remaining problems are concentrated in material **new in v0.4**:
- one inverted full-text reading (AttAcc);
- one claim with no evidence (SX-Aurora "successor");
- one §0 sentence that contradicts §5.2 (the "floor");
- one §0/§8 generalisation contradicted by §4 and §5.1 (B2 "pays only in small switchless pools");
- an energy headline that is more configuration-sensitive than stated ("gap mostly inside the die");
- a scope and tag error on Dally's HBM4 number.

The "vacated band" reading is plausible but presented as stronger than it is.

---

## 1. Did v0.4 fix r2d's issues?

| Item | Status | Residual or regression |
|---|---|---|
| F1 ρ vs I conflation | **Fixed** (§3 ρ/I/MLP table, "Low I alone does not decide placement") | — |
| F2 fixed-ratio bundling | **Fixed** (§0.1–0.3, "the system unbundles", §2.1 Vistara 0.225 vs 0.2) | — |
| F3 "only off the DDR bus" | **Fixed** (B1 includes compression and zswap, "neither B1 form is very far") | — |
| M1 2×2 | **Fixed** (§4) | — |
| M2 markets | **Fixed** (§7a, including the corrected 1990s history) | — |
| M3 vendor incentives | **Fixed** (§0.6, §7b) | — |
| M4 latency; persistent middle | **Fixed** | §0.4 says "50–75% … in *every deployed* tiered system". Mahar and Memstrata are not deployed, and GB200 has no DDR at all. Scope it to "CPU tiered systems in the notes", or count LPDDR as the middle and say so. |
| M5 energy scopes | **Mostly fixed** | (a) The HBM4 "three or four" is labelled scope "system"; it is not (see T7). (b) The §2 DDR5-6400 row carries a DDR4 energy figure (Lee 2018). (c) "Gap mostly inside the die" is configuration-sensitive (Major 5). |
| M6 batch caveat | **Fixed** | — |
| M7 floor | **Fixed, then regressed.** The sensitivity table and the "amplifier" heading are good. The new §0.7 "It set a floor under DDR prices in 2024–25" contradicts §5.2 (Major 3). | |
| M8 tags | **Mostly fixed** | Some tags are stronger than the evidence: Dally GTC'26 tagged [P] (a keynote remark → [V]); "the market chose…" in §2.3 untagged (→ [Syn]); SRAM row "≫100 $/GB" untagged; §0.1 "~100 ns" untagged. |
| M9 niche not dead | **Mostly fixed** | §0.5 "Both have *shipped* only where … kernels are narrow (GEMV, attention)". No attention PIM has shipped. HBM-PIM is at demo stage, LPDDR5X-PIM is measured on Samsung's own SoC, and UPMEM shipped general-purpose DIMMs. Use "been demonstrated or sampled". |
| M10 Mahar | **Fixed** | — |
| M11 table | **Fixed**, plus a new error | SX-Aurora (Major 4). The band's lower edge depends on configuration: 16× MRDIMM-12800 with 32 GB DIMMs gives ≈3.1 GB/s per GB, so the "~1–35" band is not a fixed interval. |
| M12 "only lever" | **Partly** | §0.2(i) still reads "needs wider, shorter wiring … and *ultimately* compute at the DRAM itself (A2)". "Ultimately" implies A2 is inevitable, which §4 and §9 deny. The other levers (more channels or MRDIMM, batching, quantization, larger SRAM) appear only implicitly. |
| r2d minors 1–24 | Almost all fixed | #21 overcorrected: see Minor 6 (GB200/Vera "on-package"). |

---

## 2. Blocking

None for the thesis. Majors 1–4 are factual or internal-consistency errors in reader-facing sections (§0, §4, §8). Fix them before anyone outside the project reads the draft.

---

## 3. Major

### Major 1. AttAcc's iso-capacity confound is inverted (§4 A2, §10)
- **Draft:** "2.81× vs a DGX A100 at the *same* 1,280 GB capacity, but with half the HBM stacks, so part of the gain is KV capacity."
- **Source (AttAcc full text, §7 setup, extracted text line ~864):** "Both DGX_Base and DGX_Large are composed of 40 HBMs, but their total memory capacity is 640GB and 1280GB … DGX and AttAcc_s in DGX+AttAcc_s *also consist of 40 HBMs each* with a total memory capacity of 1,280GB (same as DGX_Large)."
- What this means:
  - DGX+AttAcc has **80 stacks** against DGX_Large's **40**. At iso-capacity, the PIM system has *twice* the stacks. The confound is therefore stack count and external bandwidth, not KV capacity.
  - KV capacity is the confound only against the 640 GB DGX_Base (3.49–5.93×).
  - 2.81× is "up to" (LLaMA-65B). The range against DGX_Large is 2.01–2.81×.
- The paper has a fairer iso-stack baseline that the draft omits: **"DGX+AttAcc_s shows up to 1.72× higher throughput than 2×DGX"** (§7.6, Fig. 17). Here 2×DGX means 80 stacks and twice the GPUs.
- **Fix:** "Simulated. Up to 1.72× vs 2×DGX (same 80 stacks); 2.01–2.81× vs a 1,280 GB DGX that has half the stacks; 3.49–5.93× vs the 640 GB DGX, where part of the gain is KV capacity. The gain shrinks with GQA." Update §10's "Known weaknesses" ("AttAcc's half-stack DGX") to match.

### Major 2. The §0 and §8 claim that "B2 pays only in small switchless pools" contradicts §4 and §5.1, and misreads PolarCXLMem
- **§0.5 and §8:** "B2 pays only in small switchless pools, at single-digit-% savings."
  - Berger's ROI model is about **CXL pooling of general-purpose VM memory** (§4.4, Fig. 5). Its words are: "most switch-based configurations are ROI negative, while many MHD-based configurations are ROI positive, especially for smaller pool sizes." That is verified, and the 2.5× figure is verified ("over two-and-a-half times").
- The draft's own B2 evidence contradicts the generalisation:
  - Mooncake: a large RDMA KV pool in production (§4 B2).
  - NVL72 and UALink scale-up domains (§5.1).
- **PolarCXLMem is cited only for its latency penalty.** The paper (§2, below Table 1) adds that "the additional latency introduced by the CXL switch proves to be negligible in cloud database scenarios", and (§1) that "storing pages in CXL memory achieves performance on par with local memory".
  - The draft's "2.1× vs RDMA, not vs local" reads as if the authors made no claim against local. They did; it is an unquantified claim in a one-rack prototype.
- **Fix:**
  - §0.5 and §8: "CXL pooling of general VM memory pays only in small switchless pools (Berger, modelled). B2 succeeds where data is bulk and predictable: KV pools (Mooncake) and, in a prototype, database buffer pools (PolarCXLMem, whose authors report switch latency as negligible for that workload)."
  - Keep the 549 ns figure as a latency datum, and fix its ratio (see T5).

### Major 3. §0.7 "[HBM] set a floor under DDR prices in 2024–25" contradicts §5.2, and the "floor binding" story needs re-wording
- §5.2 says DDR traded *below* the HBM3E floor in 2024–mid-2025. A floor that observed prices sit beneath is not a floor under prices. §8's tension 1 ("The floor was binding in 2024–25") repeats the problem.
- What the evidence supports [r2a; Handy slide 6 image checked; SemiAnalysis free text checked]:
  - In 2024–mid-2025, HBM out-earned DDR per wafer. TrendForce Q2-2025 put the HBM:DDR price ratio at ">4×", above r ≈ 3 [S]. SemiAnalysis (2025-08-11) called HBM "margin accretive" for SK hynix and Micron.
  - So the wafer **reallocation incentive** was active, and wafers shifted to HBM.
  - That shift is the mechanism behind one of Handy's three named shortage drivers (slide 4: "Bandwidth requirements consume inordinate wafer volume – HBM takes 3X the die area of DDR").
- **Problems with the current wording:**
  1. **Price series mismatch.** Handy's series is "DRAM *Lowest Spot* $/GB (Branded DRAM)": the cheapest spot part, likely a legacy node. The floor is defined against DDR5 through Micron's DDR5 trade ratio. Use the like-for-like TrendForce ratio as the main evidence, and present Handy's chart as illustration only.
  2. **Price year mismatch.** The "HBM3E at 2025 prices" floor (~$15/GB, Goldman via X [S, relayed]) is applied back to 2024.
  3. **Why DDR could sit below the "floor" for ~18 months.** HBM output was gated by back-end capacity (TSV, stacking, qualification), not only by wafers. The indifference condition is a long-run arbitrage bound, not a short-run price floor. Say so.
  4. **Alternatives the draft does not name.** SemiAnalysis also notes that Samsung's low HBM yields "tighten up the total DRAM wafer supply". DDR4 end-of-life explains part of the mid-2025 spot rise.
- **Fix:**
  - §0.7: "HBM out-earned DDR per wafer in 2024–25, pulling wafers to HBM, and is one of three named drivers of the 2026 shortage."
  - §5.2: rename "Floor binding" to "Reallocation pressure (DDR below the HBM-parity price)". Keep "floor" for the forward-looking 2027 case only.
  - §8 tension 1: same wording change.

### Major 4. The "vacated band on server CPUs" interpretation is overstated, and one of its three data points is wrong (§0.4, §2.2–2.3, §4 A1, §9)
- **SX-Aurora:**
  - It is not a server CPU. It is a PCIe vector-engine card hosted by an x86 server with DDR (r3 notes §1).
  - The notes contain **no successor evidence** for it.
  - Remove it from "have successors without it [V]", or move it to "discontinued [unverified]". That leaves n = 2: Xeon Max and A64FX, both HPC-niche, low-volume parts.
- **Alternative explanations the draft should weigh:**
  - *HBM moved to the companion accelerator rather than leaving the node.*
    - MONAKA's own deck (slide 22) pairs it with "NVLink Fusion for AI/HPC" for FugakuNEXT.
    - Aurora paired Xeon Max with GPUs. FugakuNEXT pairs MONAKA-X with NVIDIA GPUs [reviewer knowledge, unverified].
    - The HPC bandwidth-bound codes that justified CPU-HBM have largely moved to GPUs. That is consistent with §5.1 (A creates demand for capacity tiers; CPUs become the capacity host), but it is not evidence that "the market preferred a fast middle for CPU workloads".
  - *HBM-on-CPU continued in APU or superchip form.* AMD MI300A (128 GB HBM3 as the only memory, El Capitan) is a counter-example to "vacated" if the scope is "CPUs with HBM" rather than "discrete server CPUs" [reviewer knowledge, unverified; r2d minor 20 already mentioned MI300A].
  - *Supply and price.* By §5.2's own mechanism, while HBM is rationed and priced for GPU buyers, a CPU SKU must pay GPU-level HBM prices for a small market.
  - *Vendor-specific strategy.* Intel cut its roadmap. MONAKA states **no reason** for dropping HBM (r3 notes).
- **A stronger, cleaner fact the draft does not use:** Diamond Rapids' 16-channel MRDIMM-12800 gives **1.6 TB/s per socket**. That matches Xeon Max's HBM *peak* (1.64 TB/s) and is about 2× its measured STREAM (~0.7 TB/s), at many times the capacity. On server CPUs, the middle caught up in *absolute* bandwidth within two generations. That supports "the middle improved enough for CPU workloads" without needing a market-preference claim.
- **Fix:**
  - §2.3: "Two HBM CPUs (Xeon Max, A64FX) were not continued. Their successors use a faster middle, a large or stacked SRAM LLC, and CXL, and MRDIMM sockets now match Xeon Max's HBM peak bandwidth. The bandwidth-hungry HPC work has largely moved to attached GPUs, and HBM-on-CPU survives in APUs (MI300A) [unverified]. Vendors give no reason [Syn]."
  - Delete "That is direct evidence for a hierarchy that widens *around* the middle"; say "consistent with".
  - Apply the same edits to §0.4, §8 ("On server CPUs the middle even displaced HBM" → "outgrew"), §9 and open Q5.

### Major 5. "The per-bit gap is mostly inside the die" is a §0 headline, and it depends on configuration
- The DDR4 decomposition (≈9 on-chip + ≈5 I/O + ≈5 ACT/PRE ≈ 20 pJ/b) comes from Lee Fig. 2a. That is a *device-level* datasheet model. I rendered p. 31389 and the bar reading is plausible.
- But Lee's own text on p. 31391 (Fig. 3, 8-rank channel, termination reflected) says: "In a DDR4-2400 device, the data I/O consumes **46% of total dynamic power** when it transfers data at peak bandwidth."
  - At 46%, DDR4 I/O is ≈7.5–9.5 pJ/b.
  - The I/O difference to HBM2 (0.3 wire, or ~1.5 with PHY) is then ≈6–9 pJ/b of a 12.6–17 pJ/b gap. That is roughly half, not "mostly".
- The robust statement is weaker: "a large share of the gap, about half or more depending on rank and termination configuration, is inside the die (activation and on-die datapath)". That still motivates A2's 2.24 pJ/b on-die term.
- **Fix:** hedge §0.2(i), the line under the energy table, §4 A1, and §8. Cite both Lee Fig. 2a (device level) and Fig. 3/p. 31391 (channel level, 46%).

### Major 6. Dally GTC'26: wrong scope label, inflated tag, and an aspirational claim listed as evidence
- **What the transcript says (33:18–35:16):** "to pull those four and a half bits in … from HBM4 at order of, you know, maybe three or four". No unit is spoken. The context is the energy to bring operand bits from HBM4 to the MAC. That is a full access plus transport. It is **not** "system" (board-level) scope.
- **The "~10× bandwidth at ~10× lower energy/bit" remark** refers to DRAM stacked on the compute die, a technology Dally calls "not quite ready". §4 A1 lists it under "Strongest evidence". It is a projection from a vendor's chief scientist: tag it [V], and move it to "Status/roadmap".
- **A note in the same transcript the draft could use.** At 43:16 Dally says stacked DRAM-on-chip gives "much more capacity and cheaper cost per bit". This conflicts with d-Matrix's ½ capacity per mm² (deck: 11.4 vs 21.9–26.3 MB/mm²). Present it as a disagreement between vendors, not as settled.
- **Fix:**
  - HBM4 row: "~3–4 (unit unstated, read as pJ/bit; operand fetch to MAC) [V: Dally GTC'26 remark]".
  - d-Matrix's own HBM4 figures are "2–3 pJ/bit I/O" and 40 mW/(GB/s) = 5 pJ/b. Add them to the same cell so the reader sees the spread.

---

## 4. Minor

1. **§0.2 "Two *independent* pressures."** §5.2 shows they are coupled through shared wafers. Use "two distinct pressures".
2. **PolarCXLMem latency ratio (§1).** "549–651 ns vs 146 ns local (3.76×)": 3.76× is 549/146 (local socket). 651 ns is remote socket, which is 2.82× remote DRAM's 231 ns (Table 1, p. 4). Write "549 ns vs 146 ns local-socket (3.76×); 651 vs 231 ns remote-socket (2.82×)".
3. **"30% memory saving" (PolarCXLMem).** The paper's wording is that RDMA with a 30% local buffer pool "incurs a 30% memory overhead compared to PolarCXLMem", and PolarCXLMem still leads it by 16–30%. Say "avoids a 30% local buffer pool" so it is not read as savings against local DRAM.
4. **Berger stranding "median 6–10%".** The paper says 6% when 75% of cores are scheduled and >10% at about 85% (p. 2–3). P95 reaches 25% and outliers exceed 30%. "Median" is not the paper's word; use its phrasing, as §0 already does.
5. **SK hynix slide 11.** The x-axis is unlabelled (checked in the image). The six points (B/W 1.0→8.0; stacks 4→16) are *read* as HBM1→HBM3E, but the last point (16-high, 8.0) may be HBM4. Write "over six generations shown (labels not on slide)". For slide 12: 0.40× is hybrid bonding at the HBM4E research stage; production Adv. MR-MUF is 0.45–0.55×. The draft's "packaging that cuts thermal resistance to 0.40×" reads as if shipping.
6. **"On-package" LPDDR5X.**
   - The Vera deck says "1.5TB **SOCAMM** LPDDR5X": socketed modules, not on-package. Relabel the §2 row "CPU-attached LPDDR5X (on-board / SOCAMM)".
   - §1's "The LPDDR5X is on the Grace package" should be checked. Grace's LPDDR5X is, to my knowledge, on the superchip board beside the CPU [reviewer knowledge, unverified].
   - The point ("far" is a role, not a distance) survives either way.
7. **§2 DDR5-6400 row energy.** "~16.5–21 [Lee 2018]" is a DDR4 figure. Label it "(DDR4 value; DDR5 not measured)".
8. **Handy chart dates.** The x-axis ends at about 7/10/26, with the line continuing slightly past it, so "~$16/GB by late July 2026". The deck's 7.1× arrow runs from about $2.3 (Jul–Aug 2025), not from the $1.0 trough. Keep "$1–2.3/GB" but cite the arrow's base correctly.
9. **SK hynix PIM statement (§0.5, §4 A2).** The DIGITIMES text paraphrases the speaker (Joo, VP and head of System Architecture, Dell Tech Forum 2026 keynote). Two caveats are worth a clause:
   - The die-cost, PDN and leakage points are DIGITIMES's own framing, not Joo's.
   - SK hynix is Samsung's PIM rival here and its strategic bet is packaging, so this is an interested party, not neutral evidence. SK hynix also invested in PIM itself ("has invested heavily in R&D").
   The draft's paraphrase ("likely niche … logic displaces bits while inference is capacity-bound") is otherwise accurate.
10. **Hao in §0.3.** The claim "Bandwidth also limits the far tier itself" rests on an emulated single-node study that §3 calls weak. Either append "(emulated)" in §0, or drop it from §0. Note also that the paper's intro states the rule in the opposite direction (start from remote memory, add local until the SLO is met) from §4.9 (start local, substitute remote until bandwidth saturates). The draft quotes §4.9 correctly; just cite the section.
11. **Micron "[P]".** Prepared remarks are primary but come from an interested seller. Use "[P/V]" for the 3:1 and >4:1 ratios. The quotes and pages are verified: Dec-2025 p. 5 "3-to-1 trade ratio with DDR5 … only increases with future generations"; Mar-2025 p. 6 "exceed 4 to 1" for HBM4E.
12. **§10 "Read in full".** ZeRO-Infinity, Teleport, Mooncake, Pond, TPP, Vistara, Gholami, O'Connor and the Xeon Max paper are not in `fulltext/MANIFEST.md`. If they were read in earlier rounds, say where (for example "round 1, online full text"). Otherwise move them.
13. **§2.2 "near tier moved from HBM to stacked SRAM".** That fits MONAKA (LLC on an SRAM die). Diamond Rapids' deck shows a 1.28 GB LLC but does not say it is stacked. Say "large or stacked SRAM".
14. **Falsifiability.** The frontier framing can absorb almost any outcome. Add one or two observations that would count against it, for example: CXL capacity tiers keep growing after DRAM $/GB falls back to trend; or CPU-HBM returns while MRDIMM keeps scaling.

---

## 5. Traceability spot-check

| # | Draft text (§) | Source says | Verdict |
|---|---|---|---|
| T1 | DDR4 ~16.5–21 pJ/b full; I/O ~5 (§0, §2) | Lee Fig. 2a p. 31389: DDR4 bar ≈20, with white/blue/navy segments ≈10/≈5/≈5 (image checked). Text p. 31391: data I/O = 46% of DDR4-2400 dynamic power at peak (8-rank channel). | **Number OK; "I/O ~5 → gap mostly in-die" is configuration-dependent** (Major 5) |
| T2 | LPDDR ~12 (§2) | Fig. 2a LP4 bar ≈10–12 | OK (bar reading) |
| T3 | HBM interface incl. PHY ~1.5; hybrid bond 0.66–0.88 I/O; 110,000 vias/mm² (§2) | IEDM'20 Table 1 "~1.5[2]"; text "maximum via density of 110,000/mm²" | OK |
| T4 | CXL ~22–44 pJ/b direct; SerDes 11.4 pJ/b/lane (§2) | Intel: "11.4pJ/bit including PLL and clocking". The 22–44 is DDR 16.5–21 plus 6–23 for the link [D]. | OK as [D]. State that 23 = two SerDes ends. |
| T5 | PolarCXLMem 549–651 vs 146 (3.76×) (§1) | Table 1: 146 / 231 / 265.2 / 345.9 / 549 / 651. 3.76× = local; 2.82× = remote. Authors: switch latency "negligible in cloud database scenarios". | **Ratio misattached; authors' conclusion omitted** (Minor 2, Major 2) |
| T6 | Berger: "Most switch-based configurations are ROI negative"; 2.5×; ≤16-socket MHD often ROI+; 155–180 ns; 270–345 ns; >25% local; "counterproductive" (§4 B2) | p. 6–7: all verbatim or close ("over two-and-a-half times"; "182-212% (155-180ns)"; "318-405% (270-345ns)"; "more than 25%"; "large pools counterproductive"). Cost model assumes DRAM ≈ half of server cost. | OK. Scope is CXL/VM (Major 2). |
| T7 | HBM4 "three or four (system) [P: Dally]" (§2) | Transcript 33:18: "from HBM4 at order of … maybe three or four". No unit; operand-fetch context. | **Scope label wrong; tag should be [V]** (Major 6) |
| T8 | "Stacked DRAM ~10× BW at ~10× lower energy/bit [P: Dally]" as strongest A1 evidence (§4) | 35:05–35:16 "order of magnitude more bandwidth with an order of magnitude less energy per bit", about tech "not quite ready" | **Projection, not evidence** (Major 6) |
| T9 | HBM4 row: MI455X 432 GB @ 23.3 TB/s; Rubin 11 PB @ 800 PB/s → ~54–73 (§2) | MI400 decks: "12x HBM4 Memory 432 GB at 23.3TB/s". Rubin deck: "HBM4 Memory 11 PB, Bandwidth 800 PB/s" for a 100 MW AI factory. | OK. "Rubin fleet" means a 100 MW AI-factory spec; say so. |
| T10 | d-Matrix ~½ capacity/mm², ~20× BW/mm² of HBM4 (§2, §4) | Deck table: 11.4 vs 21.9–26.3 MB/mm²; 32.6 vs 1.39–1.67 GB/s/mm²; "≈20x the bandwidth per mm²" | OK |
| T11 | d-Matrix 0.37 pJ/b "I/O, measured" (§2) | One slide: "I/O energy … 0.37 pJ/bit" vs HBM4 "2–3". Another: "Power per GB/s 2.96 mW/GB/s" (= 0.37 pJ/b) vs 40.0 for HBM4. "Measured" is not visible in the slide text I checked. | Scope still ambiguous; keep [pend] (already listed) |
| T12 | AttAcc 2.81× at same 1,280 GB "with half the HBM stacks" (§4 A2) | §7: DGX_Large = 40 HBMs/1,280 GB; DGX+AttAcc = 40 + 40 HBMs/1,280 GB. Up to 1.72× vs 2×DGX. 10.84% of a 121 mm² HBM3 die verified; 9× internal verified. | **Inverted** (Major 1) |
| T13 | SK hynix PIM statement (§0.5, §4 A2) | DIGITIMES 2026-08-27: paraphrase of Joo; "PIM may serve niche workloads, it is not a broad-spectrum fix"; capacity rationale as stated | OK; add interested-party caveat (Minor 9) |
| T14 | Hao rule; "not cheap enough … nor fast enough"; 1/6 → >½; ~70% at 50×; emulated 5 GB/s, 1 µs; zipf 0.9; "up to 10×" vs 2-tier (§3) | §4.9 and §5.1/5.5 verbatim. Default remote 1 µs / 5 GB/s / zipf 0.9; latency sweep 0.1–5 µs. | OK |
| T15 | Micron 3:1 Dec-25 p. 5; >4:1 HBM4E Mar-25 p. 6 (§5.2) | Verbatim, pages verified | OK (tag P/V) |
| T16 | Handy: three drivers incl. HBM 3× die area; spot $1–2.3 → ~$16 (§5.2) | Slide 4 verbatim. Slide 6 image: "DRAM Lowest Spot $/GB, Branded DRAM"; ≈$1.0 trough Feb–Mar 2025; ≈$2.3 in Jul–Aug 2025; ≈$16.3 late Jul 2026; 7.1× arrow from ≈$2.3. | Numbers OK; **spot-vs-DDR5 mismatch for the floor** (Major 3, Minor 8) |
| T17 | SK hynix TSV ×5.0, area penalty ×1.3 "HBM1→HBM3E"; thermal ×2.2; 0.40× (§4 A1, §5.2) | Slide 11 image: x5.0, x1.3, "Thermal burden: x2.2", x-axis unlabelled. Slide 12: 0.40× = HyB, HBM4E research. | Numbers OK; generation mapping and 0.40× status overstated (Minor 5) |
| T18 | SemiAnalysis "margin accretive" Aug 2025; HBM vs DDR/SSD offload "do not compete with each other directly" (§5.1, §5.2) | Verbatim, dated Aug 11 2025 | OK |
| T19 | Diamond Rapids 16 ch DDR5-8000/MRDIMM-12800, 1.28 GB LLC, CXL 3.0, no HBM, 1.6 TB/s (§2) | DMR slide 15 verbatim; "Memory Subsystem with up to 1.6 TB/s"; "HBM" appears 0 times | OK |
| T20 | MONAKA 12 ch DDR5-8000+, CXL 3.0, LLC on 3D SRAM die, A64FX successor (§2) | Slide 5 verbatim; "HBM" appears 0 times; slide 22 "NVLink Fusion for AI/HPC" | OK; the NVLink Fusion pairing undercuts the interpretation (Major 4) |
| T21 | "SX-Aurora … successor without it [V]" (§0.4) | r3 notes: Wikipedia only, no successor evidence; it is an accelerator card | **Unsupported** (Major 4) |
| T22 | Vera 1.5 TB at 1.2 TB/s, "on-package" (§2) | Vera deck: "1.5TB SOCAMM LPDDR5X 1.2TB/s" | Numbers OK; **SOCAMM is not on-package** (Minor 6) |
| T23 | IBM MXT 2:1–6:1; >40% cost cut (§4 B1) | "typical ratios range between 2:1 - 6:1"; "over 40% savings" | OK |
| T24 | ZeRO-Offload ≥10× PCIe, 8×, 13B on one V100 (§5.1) | Verified (lines 41, 124, 340–341) | OK |
| T25 | ByteDance 1.4–9.8×; 56.7 GB/s; 25.98% modelled. Fastswap 2.5–9.7% (§4 B1) | Verified | OK |
| T26 | Boroumand 62.7%; 55.4/54.2; 49.1/44.6. HeMem 32/11.2 vs 107/80 (§3, §7) | Verified | OK |

**Summary.** 26 items checked:
- 15 clean.
- 5 with scope or wording problems.
- 3 substantive mismatches: T12 AttAcc, T7/T8 Dally, T21 SX-Aurora.
- 3 where the number is right but the interpretation needs hedging: T1, T5, T16.

---

## 6. Logic: is the core answer well-supported?

**Supported.**
- The frontier-plus-skew argument for the *deployed* forms is well evidenced:
  - HBM's bandwidth density and the O'Connor power cap.
  - Vistara at DDR density with lower $/GB.
  - zswap, TMTS and MXT as same-box cheaper bytes.
- The deployed/ambitious split is the paper's most useful contribution. Keep it front and centre.
- §7's "markets differ *because* binding constraints differ" is appropriately modest.

**Overclaims to fix.** These are Majors 2–4 plus:
- §0.5 "shipped" (A2).
- §0.2 "ultimately" (A2).
- §0.4 "every deployed".
- §2.3 "the market chose … That is direct evidence".
- §8 "the middle even displaced HBM".

**"Why now" (§0.7, §6).** The claim that "the escape valve has closed" is [Syn], and it is tagged as such. The supporting fact is that DRAM $/bit decline slowed to ~12–15%/yr after ~2011 [S]. But both directions were pursued well before the 2026 shortage, and the B1 designs predate it, as the draft itself says. The 2011 slowdown is the relevant evidence, not 2026 prices. "The difference from earlier claims is measured price data" should cite the post-2011 $/bit series specifically, not the 2026 spike, which is cyclical.

**Is the floor story sound?** Directionally, yes: wafer reallocation toward a higher-margin product is real and documented by vendors and analysts. Two things are unsound:
- the word "floor" for 2024–25;
- the use of a legacy-spot series as the DDR price.

See Major 3. The 2027 forward case is fine as a conditional.

**Is the vacated-band reading sound?** As a description (two HBM CPUs not continued), yes. As evidence of market preference for a fast middle on CPU workloads, no: it cannot be separated from (a) HBM moving to attached GPUs, (b) HBM rationing and pricing, and (c) Intel's roadmap cuts. The absolute-bandwidth catch-up (1.6 TB/s MRDIMM socket ≈ Xeon Max HBM peak) is the defensible core. See Major 4.

---

## 7. Readability (target: memory-industry researcher)

The draft is ~6,600 words. Its "Short answer" (§0) is ~900 words, with seven numbered points, nested bullets and an embedded table, so it is not short. §0 and §8 overlap by about 70%. The density of numbers is right for the notes, but too high for a first read.

**Specific cuts (roughly 6,600 → 3,500 words in the body):**
1. **Merge §0 and §8.** Replace both with the one-page summary below, and delete §8.
2. **§1 (latency).** Keep three bullets: flight time is ns; A does not lower latency (Xeon Max); B's cost is protocol hops. Move both tables to an appendix.
3. **§2 table.** Keep it, but move the conventions, and the SRAM and RDMA rows, into footnotes. Cut "What the table shows" from five items to three: CXL is not new in density; the middle improved; DDR is squeezed, not unstable.
4. **§3.** Cut the Hao discussion (eight bullets) to two lines. Cut the five-minute-rule history to one sentence.
5. **§4 2×2.** Convert to a single table with columns for driver, best evidence (≤2 items), cost, and status. The prose "Pays with" lists can go to the appendix.
6. **§5.2.** Keep the mechanism plus one sentence on reallocation. Move the floor formula, sensitivity table and history to the appendix.
7. **§6 (history).** A five-line box: same people; different markets; each wave's kill condition; what differs now.
8. **§10, revision history, tag table.** Move to the end matter. Put the tag legend in a footnote.

**Suggested one-page executive summary:**

> **Question.** Why are closer coupling (A) and disaggregation (B) both attractive?
>
> **Answer (3 sentences).** Distance is not the variable being optimized. Both directions move off the DDR DIMM's narrow spot on a two-axis cost frontier: bandwidth per GB stored, and $/GB, under a latency limit. Access is skewed, so a mix of tiers beats any single tier.
>
> **Two pressures, with three numbers each.**
> - Bandwidth: compute 3.0×/2 yr vs DRAM bandwidth 1.6×; 60 W ≈ 2 TB/s for HBM2; DDR4 ≈ 4–5× HBM2 pJ/bit (full access).
> - Capacity cost: $/bit decline slowed from 33–36%/yr to 12–15%/yr; memory is 37% of Meta rack TCO; 20–33% of DRAM is moved to cheaper tiers at ≤5% slowdown.
>
> **2×2 table, one line per cell:**
>
> | Cell | Driver | Status | Best evidence | Main cost |
> |---|---|---|---|---|
> | A1 (HBM) | Bandwidth density | Deployed | … | … |
> | A2 (PIM) | In-die energy, internal bandwidth | Demo/niche | … | … |
> | B1 (cheaper bytes) | $/GB | Deployed | … | … |
> | B2 (pooling) | Multiplexing | Niche: KV pools, small switchless CXL | … | … |
>
> **Coupling (3 bullets).**
> - A creates capacity-tier demand (GB200 LPDDR, d-Matrix + LPDDR, ZeRO).
> - A competes with B for wafers (3:1; reallocation 2024–25; a shortage driver).
> - B pulls compute outward (training offload; prototypes elsewhere).
>
> **Why both, now.** Commodity DRAM no longer outruns either end. Markets differ because their binding constraints differ. Vendors also want out of commodity pricing.
>
> **What would change our mind (2 bullets).**
>
> **Confidence.** High for A1 and B1; medium for the coupling claims; low for A2 and B2 economics (simulated or modelled).

The rest of the document becomes the supporting evidence, and each section opens with its one-line claim.
