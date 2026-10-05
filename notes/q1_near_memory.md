# Q1 — The case FOR direction (A): tighter memory–compute coupling

Notes compiled 2026-10-02; updated 2026-10-03 (round 4) from full texts supplied by Kamil (`fulltext/`, see `fulltext/MANIFEST.md`). Sub-question: why do researchers and companies want PIM, near-memory compute, HBM base-die logic, and 3D DRAM-on-logic? What is the quantitative evidence, which metrics does it optimize, and for which workloads?

Provenance tags:
- **[primary-full]**: I read the full text or the vendor's own slides.
- **[primary-partial]**: I read the relevant sections of the primary source.
- **[V-deck]**: Hot Chips 2026 vendor slides, extracted PDF text or a slide image I viewed myself. Vendor claims, not independent measurements.
- **[abstract only]**: the number comes from the abstract alone.
- **[secondary]**: the number comes from press or blog coverage of a primary source I could not open.
- **[D]**: my own arithmetic on primary numbers.
- **[unverified]**: I could not confirm the number.
- **[pending full text]**: the claim depends on an item in "FULL TEXT NEEDED".

---

## Round-4 deltas (2026-10-03)

Each delta lists the source (file / page / slide) and the effect on the claim.

### Resolved

**1. AttAcc (ASPLOS'24, `fulltext/03-attacc.pdf`) [primary-full]**
- Setup: simulated with a modified Ramulator. Arithmetic units are synthesized in ASAP7 7 nm and scaled to a 1z-nm DRAM process, assuming DRAM logic is 10× less dense (§7.1, p.112).
- **Headline "up to 2.81× performance / 2.67× energy efficiency"** is DGX+AttAcc vs **DGX_Large**: a DGX A100 with HBM3 at **1,280 GB** in 40 stacks. Iso-capacity, but **DGX+AttAcc uses 80 HBM3 stacks** (40 on the GPUs + 40 AttAcc), so it is not iso-stack and not iso-cost (§7.1).
- Against DGX_Base (640 GB, 40 stacks): up to **3.49× (LLaMA-65B), 3.91× (GPT-3 175B), 5.93× (MT-NLG 530B)**. Against DGX_Large: 2.81× / 2.39× / 2.01× (§7.2, p.113).
- The model-size trend shows part of the gain is the *capacity* added for the KV cache:
  - LLaMA-65B: KV space goes from 510 GB to 1,150 GB (2.3×).
  - MT-NLG: KV space goes from 146 GB to 786 GB (5.4×).
  - Larger models' gains come mostly from bigger batches on the FC layers (§7.2).
- Energy: up to −66.7% / −65.9% / −66.8% vs DGX_Base, and −62.6% / −48.8% / −29.1% vs DGX_Large (§7.2).
- SLO: 56× throughput vs DGX_Large at a 30 ms SLO, where DGX_Large is SLO-limited to batch 1 (§7.3).
- Up to **1.72× vs 2×DGX** (§7.6).
- **The "3.19× / 3.22× vs DGX A100 640 GB" figure is not in the ASPLOS paper.** It appears to come from the earlier preprint "Unleashing the Potential of PIM" (Choi et al. 2023); I have not verified that. Do not cite it as AttAcc.
- **Capacity and area cost:** GEMV units are per bank, 16 per pseudo-channel.
  - Internal bandwidth is **9× external (242 TB/s aggregate)**, capped by the HBM3 IDD7 power budget (§4.1 p.108; §7.1).
  - Area overhead is **13.12 mm² per DRAM die = 10.84% of a 121 mm² HBM3 die**, or 14.59% with FP32 accumulate. It is "**about 25%**" if the GEMV unit is sized like the HBM-PIM/AiM units [36,37] (§7.7).
  - Softmax and accumulators on the buffer die add 1.40 mm² (7 nm).
  - Bank-level placement was chosen over the buffer die because buffer-die PIM gives "nonexistent" performance gain and "little energy benefit": the data still traverses the whole DRAM→TSV path (§4.1). This is direct support for the O'Connor point.
- GQA and MQA: the benefit diminishes as the group size grows, because the GPU can reuse KV from cache (§8 discussion). **This matters because modern models use GQA** (cf. Duplex: GQA attention Op/B 4–8).

**2. SK hynix GDDR6-AiM, ISSCC'22 paper 11.1 (`fulltext/A_1ynm_…pdf`) [primary-full; Fig. 11.1.6 table viewed as an image]**
- Die: 4 Gb with 16 PUs (one per bank) at 1 GHz, 32 GFLOPS per PU, BF16 MAC.
- **PU area = 0.19 mm² each, so ≈3.0 mm² per die [D].** The die area is not given, so the % overhead is still unknown.
- Package: 8 Gb per chip = **4 Gb dual-die package**, 64 GB/s per chip, 1 TFLOPS per chip.
- The bank-wide mantissa-shift adder tree cuts the adder tree's power, time and area by 66%, 52% and 75%.
- System evaluation: "**up to 10×**" vs a GPU with HBM2 (RNN/LSTM/MNIST), from an FPGA emulator with the interface at 2 Gb/s/pin.
- **The paper contains no energy-efficiency figure.** The press figures "16× faster / 80% lower power" do not appear in the paper; treat them as press-only.
- Thermal: a flat-lid package was needed; θJC 4.6 → 0.5 °C/W.
- **Bonus, a resolution for Kwon ISSCC'21:** the AiM paper's comparison table lists Samsung HBM2-PIM [3] as "**6GB/cube (Buffer die + 4H 4Gb core-die with PCU + 4H 8Gb core-die)**", 307 GB/s per cube, 128 PUs, 1.2 TFLOPS.
  - So each PIM core die has **half the density** of a normal die (4 Gb vs 8 Gb), and the 8-Hi stack holds 6 GB instead of 8 GB (**−25% capacity**).
  - This is a third-party primary source (an SK hynix ISSCC paper describing Samsung's part). Kwon ISSCC'21 itself is still not read.

**3. Dally/Dean GTC 2026 transcript (`fulltext/Advancing_to_AI's_Next_Frontie_transcript_en-us.txt`) [primary-full; AI-generated transcript, speakers not labelled]**
- 33:11–33:22: "multiply add… for NVFP4, it's like **10 femtojoules**, and to pull those **four and a half bits** in… from HBM4 at order of, you know, maybe **three or four**." The transcript drops the unit and the punchline.
- **The "1000×" and "SRAM read ≈ 10 fJ" figures are NOT in the transcript.** They remain chiplog paraphrase [secondary]. Arithmetic: 4.5 b × 3–4 pJ/b = 13.5–18 pJ ≈ **1,350–1,800×** a 10 fJ MAC [D], assuming the unit is pJ/bit.
- 34:55–35:16 (in context, NVIDIA's chief scientist): "most of the read energy of reading the DRAM isn't actually reading the DRAM, it's moving the bit from where you read the DRAM over to where the pins to the GPU are." Stacking DRAM on the GPU gives "**an order of magnitude more bandwidth with an order of magnitude less energy per bit**", which "winds up being the same power but with way more performance". The technology is "not quite ready". This independently corroborates O'Connor's 2.24 of 3.92 pJ/b on-die term.
- 34:32–34:39: SRAM is "**an order of magnitude more expensive [in] area per bit**" than DRAM.
- 43:02–43:22: "you could do that if you put everything in SRAM, but then it becomes super expensive… stackable DRAM on top of a chip… **much more capacity and cheaper cost per bit** and still… you don't move the data very much."
- 10:42–10:59 and 17:27–18:12: provisioning across "math vs memory bandwidth vs memory capacity vs communication". Prefill is math-heavy; decode is "extremely memory bandwidth limited and latency limited". This is the cleanest statement of the bundle-unbundling framing.

**4. d-Matrix Raptor, HC2026 (`decks_text/hc2026.dmatrix.SudeepBhoja.v3.0.pdf.md`) [V-deck; slides 6, 13, 28 viewed]**
- **0.37 pJ/bit is "Measured I/O Power" of the 3D vertical interface only**: "100 TB/s × 0.37 pJ/bit = 296 W" (s23).
  - s8 lists "3D vertical IO 0.3–0.4 pJ" against "2.5D HBM4 (system) 2.5 pJ + 3 pJ (on chip)".
  - DRAM array and activation energy are *not* included.
- **The two headline ratios use different HBM4 baselines.**
  - "6.3× lower energy, 5.6× higher BW" (s6 chart): 3D-DRAM at ≈0.37 pJ/b and 100 TB/s vs "HBM4 (projected)" at ≈2.4 pJ/b and ≈18–20 TB/s.
  - "13.5× better power per GB/s" (s28): 2.96 vs **40.0 mW/(GB/s)**, i.e. 0.37 vs **5.0 pJ/b** [D]. Silicon-area basis, effective bandwidth at 83% (Raptor) and 85% (Rubin) utilisation; Rubin R200 = 8× HBM4 24 Gb.
  - The 13.5× therefore compares an I/O-only figure with an HBM4 system figure that includes on-chip movement. That is a possible scope mismatch to flag.
- Practical HBM4 package ceiling: **~20 TB/s** (Vera Rubin, MI455) because of beachfront (s5). "At 2.4 pJ/bit, 100 TB/s requires 1.92 kW for HBM power only" (s6).
- **Capacity trade, on the silicon-area basis (s28):**
  - Capacity: **11.4 MB/mm² vs 21.9–26.3 MB/mm² for HBM4**, ≈0.43–0.52× [D].
  - Bandwidth density: 32.6 vs 1.39–1.67 GB/s/mm² (≈20×).
  - Per-card numbers: chiplet 4 GB / 12.5 TB/s; card 32 GB / 100 TB/s; rack 72 cards, 2.3 TB, 7.2 PB/s (s13).
  - **The "192 GB / one-sixth capacity" figure is not in the deck.** It comes from press (Hardware Busters).
- **d-Matrix itself adds a capacity tier.** Each chiplet carries 2× LPDDR5X ports (2×38.4 GB/s, s13 image), and s18 lists "LPDDR5X as secondary tier: data placement policy". So A creates demand for a capacity tier even inside the 3D-DRAM product.
- Thermal and reliability:
  - 1-Hi stack, logic on top, ≤0.5 W/mm², liquid-cooled (s7).
  - Tj 105 °C cuts retention from 32 ms to 4 ms (8× refresh), which costs only 1.37% of bandwidth thanks to deep banking (1,366 rows) (s25–26).
  - 840 banks with 72 spares (s20, s27).
  - 33% overfetch, fixed by "stream blocking" (s21–22).
- Workload claim: ~1000 TPS per user for a 3T-class model (Kimi K3) at 1M context, batch 8 (988 TPS/user) (s29).
- Raptor paper: **ISCA 2026** (s31). This is a new full-text candidate for the energy scope.

**5. Samsung HBM base die, HC2026 (`decks_text/HC2026.HBMBaseDie.SangwookHan.v20.pdf.md`) [V-deck; slides 6, 23 viewed]**
- **"−70%"** is a unitless "Power Efficiency" bar chart: **zHBM vs HBM5**, with HBM4 > HBM5 > zHBM and no axis values. It is a projection, not a measurement.
- **"230%"** is DRAM bandwidth for **1 GPU + 4 zHBM vs 1 GPU + 4 HBM4E**: bars ≈14 → ≈32 TB/s, i.e. 2.3× [read from chart].
- In the same comparison, DRAM power drops ≈220 → ≈120 W ("100W saving") and is reinvested as +8.3% GPU power, assuming a 1200 W GPU and 4 HBMs in the SiP (s23).
- The power reduction comes from removing SERDES / data-align / DQ I/O.
- Process table (s6): B-die is **4 nm for both HBM4 (2026) and HBM4E (2027)**, with C-die 1*nm. The chart shows pJ/b falling while MPGA power rises every generation (axes unlabelled).
  - **This contradicts my round-3 note "reportedly 2 nm for custom HBM4E" [TrendForce]**, unless that refers to a later custom variant.
- Phases:
  - **Phase 1 cHBM:** D2D PHY replaces the HBM PHY; the memory controller moves to the B-die ("actively being ported"); SRAM-based cell repair.
  - **Phase 2 (aHBM):** RAS sensors, on-die ATE, **"Memory Extension Controller/PHY"** on the outer B-die shoreline to attach external memory (s18). The motivation is context windows growing "30x/year" and the KV cache (s17). **Processing elements in the B-die** "reduce D2D bandwidth requirements" (s19–20). Challenges named: "Limited silicon area and increased thermal density from B-die PEs".
  - **Phase 3 zHBM:** wafer-on-wafer hybrid copper bonding of 4H C-die + xPU (s24).
- PHY power density rises from 0.5 to >2.0 W/mm² (sHBM4E → sHBM5). The heat-path block gives >35% lower peak temperature (s12).

**6. Samsung LPDDR5X-PIM, HC2026 (`decks_text/HC2026.Samsung.KaramHwang…md`) [V-deck]**
- One PIM unit per bank; 76.8 vs **614 GB/s (8×)** on a x64 package at 9600 Mbps.
- 16 GB, 561-ball JEDEC package, 2 ranks × 4 dies.
- 2.4 TOPS SINT4, 1.2 TOPS SINT8, 1.2 TFLOPS FP8 per package.
- Drop-in via "Address Align Mode" with a conventional controller.
- Silicon test on a Samsung edge-AI SoC, Llama-3.1-8B, 320 tokens, SINT8 activations / SINT4 weights: 27.0 → **81.3 TPS (>3.01×)**, run time 12.3 → 5.4 s (>2.28×). "Performance optimization with PIM is still ongoing" (s16).
- **No energy or power figure anywhere in the deck.** The energy question stays open.
- Context: memory rose from 52% to **63% of AI-chip component cost** (Q1'24 → Q4'25; Epoch AI, s3).
- LPDDR6-PIM: "JEDEC 1st full version spec near finalization (specific for mobile/client profile)" (s17).

**7. XCENA MX1 + Samsung, HC2026 (`decks_text/HC2026.XCENA-Samsung.Kim.v02.pdf.md`) [V-deck; slide 9 viewed]**
- **4.7× / 18.7× workloads:** six analytics kernels — compression, decompression, Parquet, filter (<), LIKE, aggregation.
  - Throughput: MX1 at 55/45/49/103/102/121 GB/s.
  - Efficiency: 1.2/1.1/0.8/1.7/1.2/2.3 GB/J.
  - Baseline: Xeon 6767P (32 cores, 4ch DDR5-6400) on local DRAM or over CXL. **Idle power is excluded.**
  - The maxima are vs host-over-CXL; vs local DRAM they are "2×" throughput and "6.2×" GB/J.
  - **Local DRAM beats MX1 on Filter (≈115 vs 103 GB/s)** (s9).
- Bandwidth behind the device: DDR5-8400 ×4 = **268.8 GB/s vs PCIe Gen6 x8 = 64 GB/s (4.2×)** (s13).
- Samsung internal analysis: **67.2%** of data-transfer energy is CXL-controller → host (s13).
- RAG (FAISS IVF-Flat, 4.5 TB vectors), 10 PNM devices vs host + CXL pool: **64× QPS** (0.23 → 14.81) and **65× queries/kJ**. Effective read bandwidth: 26 GB/s host vs 1,625 GB/s aggregate PNM (s16).
- KV hybrid attention (Llama-3.1-70B INT8, 100K context, batch 5): **3.35× tok/s, 3.84× tok/kJ** (s18).
- Rack system: 20 TB, 2.7 TB/s (s14).
- **Note:** the PNM gain here is channel-level (4.2×), not bank-level. That matches the synthesis' "escapes PIM's objections by giving up PIM's main benefit", but shows channel-level is already worth 2–5× on scan kernels.

**8. NVIDIA Groq 3 LPX / LP30, HC2026 (`decks_text/NV_HC2026_LP30_Final.pdf.md`) [V-deck, extracted text]**
- Rack: 256 LPUs, **128 GB SRAM, 40 PB/s aggregate SRAM bandwidth, 315 PF FP8**, 350 ns chip-to-chip SRAM-to-SRAM (s24).
- **Per-chip figures are not stated in the deck.** Derived: ≈0.5 GB SRAM, ≈156 TB/s, ≈1.23 PF FP8 per LP30 [D]. No energy-per-token figure is given.
- Heterogeneous mappings with VR NVL72:
  - External-drafter speculative decode.
  - **"ATTN on GPU, FFN on LPU: Weights Stored in SRAM, KV-Cache Stored in DRAM"** (s34).
  - Prefill on GPU / decode on LPU (s35).
- On GPT-OSS-2T (cached ISL 400K): ~3× throughput (TPS/MW) at matched interactivity, and ~5× interactivity (s38–42).
- Gemma-4-31B coding: 10,996 TPS/user; SPEED-bench median 4,767 TPS (s8). "4× faster" long-context decode per Artificial Analysis (s9).
- The "3,431 tok/s" figure is press-only.

**9. Micron, HC2026 (`decks_text/HC2026.MicronTechnology…md`) [V-deck; slides 3–4 viewed]**
- Memory-wall chart: normalized performance 2017–2027 on a log scale.
  - Accelerator TFLOPS points (TPU v3 … R200) fitted at **3×/2yr**; HBM BW points (HBM2E … HBM4) at **<2×/2yr**.
  - **No data sources or normalization are stated on the slide.**
- **The same slide shows Micron's evolution path: "2.5D attached memory → 2.5D advanced memory → Processing in Memory."** Micron *does* place PIM on its roadmap graphic. This resolves my round-3 "Micron's position unclear".
- Slide 4: "GPU offloading; Advanced D2D PHY; Custom features" for the base die. pJ/bit falls while cube bandwidth and density rise, 2023 → 2030 (no values).
- Slide 5: memory silicon area is **>8×** GPU (12,344 mm² for 8× HBM4 12-high). Slide 11: ~3× silicon for HBM3E capacity vs DDR5.
- HBM4 nominal (s7): 2048 I/O, 11 Gbps, **2,800 GB/s**, 24 Gb dies.

**10. SK hynix, HC2026 (`decks_text/HC2026.SK_hynix.Jaesik_FINAL1.pdf.md`) [V-deck]**
- Packaging only; **no PIM or AiM content**.
- HBM4: >2 TB/s, "40+% lower power efficiency" (sic), up to 48 GB, >20K TSVs.
- Hybrid bonding is needed for ≥20-Hi.
- An iHBM thermal path in the D2D PHY area gives >30% lower thermal resistance (s15).
- Not load-bearing for Q1.

### Corrections for SYNTHESIS_DRAFT.md v0.3 (not edited by me)

Each item gives the line number, the quoted draft text, and the correction with its source.

1. **L179 (A1 "Pays with")**: "d-Matrix Raptor 32 GB, 1/6 of a 192 GB HBM4 config, for 5.6× bandwidth [S, HC'26]"
   - "1/6 / 192 GB" is not in the deck (press only).
   - Deck s28: capacity density **11.4 vs 21.9–26.3 MB/mm²** (≈0.43–0.52×) for **32.6 vs 1.39–1.67 GB/s/mm²** (≈20×).
   - Deck s6: **5.6× bandwidth, 6.3× lower energy** vs projected HBM4 (≈2.4 pJ/b).
   - Retag as [V].
2. **L340 (Open question 4)**: "Can DRAM-on-logic's capacity penalty (1/6) be offset by CXL or LPDDR capacity tiers at system level?"
   - Change "1/6" to "≈2× lower capacity per mm²".
   - Partly answered at product level: each Raptor chiplet has 2× LPDDR5X ports, and s18 lists "LPDDR5X as secondary tier: data placement policy" [V: d-Matrix HC'26 s13, s18].
3. **L180 (A2 evidence)**: "AttAcc [pend]"
   - Replace with: up to 2.81× throughput / 2.67× energy efficiency vs an **iso-capacity (1,280 GB) DGX A100 that has half the HBM stacks** (40 vs 80); 3.49–5.93× vs the 640 GB DGX; simulated [P: AttAcc §7.1–7.2].
   - Add the caveat: part of the gain is extra KV capacity, and the benefit shrinks with GQA group size [P: AttAcc §7.2, §8].
4. **L180 ("Pays with")**: "capacity penalty [pend]"
   - Replace with: HBM2-PIM PIM dies at **half density (4 Gb vs 8 Gb)**, stack 6 GB vs 8 GB (−25%) [P: Lee et al. ISSCC'22, Fig. 11.1.6 table].
   - AttAcc's bank-level GEMV costs **10.84%** of the DRAM die (≈25% at HBM-PIM/AiM unit size) [P: AttAcc §7.7].
   - "activation energy … [pend: Kwon ISSCC'21]" stays pending.
5. **L69 and L180 (status)**: "LPDDR5X-PIM productization [S]" / "LPDDR5X-PIM productizing 2026–27 [S]"
   - Retag as [V]: silicon-measured >3.01× TPS (Llama-3.1-8B, 320 tokens) on Samsung's own edge SoC; 8× internal BW (614 vs 76.8 GB/s); drop-in 561-ball package.
   - **No energy data.** LPDDR6-PIM JEDEC spec "near finalization" [V: Samsung HC'26 s7, s16, s17].
6. **L201**: "Samsung aHBM roadmap: adds external-memory-extension PHYs to the HBM base die [S, HC'26]"
   - Retag as [V: Samsung HC'26 s18]. The stated motivation is context windows growing 30×/yr and KV capacity (s17).
   - The same Phase 2 adds B-die PEs, with "limited silicon area and increased thermal density" as the challenges (s19).
7. **L244**: "XCENA MX1 (3,072 RISC-V cores, because the link is only 64 GB/s per direction) [S]"
   - Retag as [V].
   - Bandwidth behind the link is 268.8 GB/s vs 64 GB/s through it (4.2×).
   - Analytics kernels: up to 4.7× throughput / 18.7× GB/J vs host-over-CXL, but **2× / 6.2× vs local DRAM**, with idle power excluded. Local DRAM wins on Filter.
   - RAG: 64× QPS. KV attention: 3.35× tok/s [V: XCENA HC'26 s9, s13, s16, s18].
   - This refines L248: channel-level ratios of 3–4× are already worth 2–5× on scans.
8. **L135**: "Nothing ships in volume between ~1 and ~35 GB/s per GB"
   - Needs scoping. Device-level data in my sources sit inside the band:
     - LPDDR5X x64 package: 76.8 GB/s / 16 GB = **4.8 GB/s per GB** [V: Samsung HC'26 s7; D].
     - LPDDR4: 3.2 [P: ISSCC'22 table].
     - GDDR6 (AiM chip): 64 GB/s per 1 GB [P].
   - GDDR-based GPUs and mobile SoCs ship in volume in roughly the 3–40 range [D; system-level examples from memory, verify].
   - The claim seems to hold only for **server/CPU capacity tiers**. Separately, LPDDR5X-PIM's *internal* 38 GB/s per GB is HBM3E-class [D].
9. **L117 (SRAM row)**: "~10⁵ (Groq LPU)"
   - Groq 3 LPX: 40 PB/s / 128 GB ≈ **3×10⁵ GB/s per GB** [V: NV HC'26 s24; D].
   - Optional energy cross-check: d-Matrix lists on-die SRAM at ~50 fJ/bit (s8).
10. **L118 (HBM row, energy)**: optional addition.
    - HBM4 system ≈ 2.5 pJ + 3 pJ on-chip (s8), or 40 mW/(GB/s) = 5 pJ/b (s28) [V: d-Matrix].
    - The Dally transcript supports "most DRAM read energy is moving the bit to the pins" [P: GTC'26 transcript 34:55]. This corroborates the O'Connor breakdown from an independent source.
11. **L193 (§5.1)**: supporting evidence to add.
    - NVIDIA LPX + VR NVL72: "Weights Stored in SRAM, KV-Cache Stored in DRAM" (s34). The A-extreme is explicitly paired with an HBM capacity tier [V].
    - Dally/Dean: SRAM is ~10× DRAM in area per bit, and stacked DRAM gives "much more capacity and cheaper cost per bit" [P: transcript].
12. **L351–359 (§10 Evidence quality)**:
    - Hot Chips 2026 d-Matrix, Samsung aHBM, LPDDR5X-PIM and XCENA are now read from the decks [V], not secondary.
    - AttAcc is resolved.
    - Kwon is partly resolved (capacity only).
    - Add a new pending item: Raptor ISCA'26 (energy scope).

### Still pending
- **Kwon ISSCC'21 HBM2-PIM** (not provided). Capacity is now resolved via the AiM table. Still pending:
  - PCU area per die. AttAcc gives only an indirect "~25% per DRAM die if sized like HBM-PIM/AiM units".
  - Measured pJ/bit inside vs outside.
  - Whether all-bank mode raises activation energy.
- **Raptor ISCA 2026 paper** (new). Needed for the energy scope (I/O-only vs array) behind 0.37 pJ/b and the 13.5× ratio.
- **Choi et al. 2023 preprint "Unleashing the Potential of PIM"** (optional). Only needed if anyone wants to keep citing the 3.19×/3.22× numbers.

---

## FULL TEXT NEEDED (status after round 4)

| # | Item | Status |
|---|---|---|
| 1 | AttAcc ASPLOS'24 | **Resolved** (delta 1) |
| 2 | Kwon et al. ISSCC'21 HBM2-PIM, IEEE Xplore pp. 350–352 | **Partly resolved.** Capacity: 4 PIM dies at 4 Gb + 4 normal dies at 8 Gb = 6 GB, −25% vs 8 GB, via the AiM ISSCC'22 table. **Still [pending full text]:** PCU area overhead per die, measured internal vs external pJ/bit, and all-bank activation energy. |
| 3 | Lee et al. ISSCC'22 GDDR6-AiM | **Resolved** (delta 2). The paper has no area-% or energy figure; that gap is in the source, not in our access. |
| 4a–f | HC2026 decks | **Resolved** (deltas 4–10). d-Matrix, Samsung base die, LPDDR5X-PIM, XCENA, LP30, Micron. |
| 5 | Dally GTC 2026 | **Resolved** (delta 3). The 1000× figure is not in the transcript. |
| 6 (new) | Bhoja et al., "Raptor", ISCA 2026 | Needed for the energy scope of 0.37 pJ/b (I/O only vs array + I/O) and the HBM4 5 pJ/b baseline. |
| 7 (new, optional) | Choi et al. 2023, "Unleashing the Potential of PIM…" (preprint) | Only if the 3.19×/3.22× DGX-640GB numbers are to be cited. |

---

## 1. Motivation: canonical quantitative evidence

### 1.1 Energy per operation vs per access (the "data movement dominates" argument)

**Horowitz, "Computing's Energy Problem (and what we can do about it)", ISSCC 2014 keynote paper 1.1** [primary-full]
- PDF: https://gwern.net/doc/cs/hardware/2014-horowitz-2.pdf
- I decoded the Fig. 1.1.9 table from the PDF's font-shifted text. Values are for 45 nm at 0.9 V:

| Operation | Energy |
|---|---|
| 8b int add | 0.03 pJ |
| 32b int add | 0.1 pJ |
| 8b mult | 0.2 pJ |
| 32b mult | 3.1 pJ |
| 16b FP add | 0.4 pJ |
| 32b FP add | 0.9 pJ |
| 16b FP mult | 1.1 pJ |
| 32b FP mult | 3.7 pJ |
| 64b cache access, 8 KB | 10 pJ |
| 64b cache access, 32 KB | 20 pJ |
| 64b cache access, 1 MB | 100 pJ |
| 64b DRAM access | **1.3–2.6 nJ** |

- Instruction overhead is about 70 pJ per instruction: I-cache 25 pJ, register file 6 pJ, plus control.
- Text: "The energy cost of a DRAM access (1 to 2 nJ) is a couple of orders-of-magnitude higher than the cost of an internal cache access or functional operation (10 pJ)."
- Text: DRAM I/O "takes over 20 pJ/bit" and also needs static power.
- Text: "even when the I/O is improved, the energy cost of a DRAM access will still be large (10 pJ/bit, 0.6 nJ/8B)… since requests and data still must travel a large distance on the processor and memory chips."
- That last point is exactly the PIM argument: the distance inside the DRAM chip matters, not only the I/O.
- For 1 pJ/op efficiency, Horowitz says you need "roughly a thousand operations… for each DRAM fetch."

**Dally, "Hardware for Deep Learning", Hot Chips 2023 keynote** [primary-full slides]
- https://hc2023.hotchips.org/assets/program/conference/day2/Keynote%202/Keynote-NVIDIA_Hardware-for-Deep-Learning.pdf
- "Cost of Operations" slide, reusing Horowitz's 45 nm numbers: 32b SRAM read (8 KB) 5 pJ vs **32b DRAM read 640 pJ**, against an 8b add of 0.03 pJ.
- "The Importance of Staying Local" slide: LPDDR DRAM (GB) **640 pJ/word**, on-chip SRAM (MB) 50 pJ/word, local SRAM (KB) 5 pJ/word.
- An OOO CPU instruction costs 250 pJ (99.99% overhead, ARM A-15). A 16b int add costs 32 fJ (28 nm).
- "3D memory" is listed explicitly as a future lever, together with "Lower memory energy".

**Keckler, Dally, Khailany, Garland, Glasco, "GPUs and the Future of Parallel Computing", IEEE Micro 31(5), Sep/Oct 2011** [primary-full]
- PDF: https://www.cs.toronto.edu/~pekhimenko/courses/csc2224-f19/docs/GPU.pdf
- Table 1 (40 nm → 10 nm projection):

| Item | 40 nm | 10 nm (two operating points) |
|---|---|---|
| DFMA | 50 pJ | 8.7 / 6.5 pJ |
| 64b read from 8 KB SRAM | 14 pJ | 2.4 / 1.8 pJ |
| Wire energy | 240 fJ/bit/mm | 150 / 115 fJ/bit/mm |
| 256 bits over 10 mm | 310 pJ | 200 / 150 pJ |

- Text: reading three 64b operands and writing one to 8 KB SRAM costs about 56 pJ, similar to the DFMA. The same operands from 10 mm away cost 6x more (310 pJ). "**The cost of accessing the operands from external DRAM is 200 times greater, at more than 10 nJ.**"
- At 10 nm, 10 mm global wires reach **23x DFMA energy**, because wire capacitance per mm stays constant.
- Table 2 (DRAM interface, 2010 → 2017 projection):
  - Pin bandwidth: 4 → 50 Gbps.
  - Interface energy: 20–30 → 2 pJ/bit.
  - DRAM access energy: 8–15 → 2.5 pJ/bit.
- "Accessing an on-chip memory costs 250 times less energy per bit."
- At 20 pJ/bit, running Fermi's 175 GB/s at full utilization costs about 28 W on the GPU die plus 21 W in the DRAMs. That 49 W is about 20% of card TDP.

**O'Connor, Chatterjee, Keckler et al., "Fine-Grained DRAM: Energy-Efficient DRAM for Extreme Bandwidth Systems", MICRO 2017** [primary-full]
- https://www.cs.utexas.edu/~skeckler/pubs/MICRO_2017_Fine_Grained_DRAM.pdf
- **This is a key and under-appreciated breakdown.** An HBM2 access costs **3.92 pJ/bit** (3.97 in another place in the paper), including ECC. It splits into:
  - **2.24 pJ/bit**: moving data on the DRAM die from the sense amps to the central stripe, through the TSVs, and across the base die to the bumps (about 9.9 mm).
  - **1.21 pJ/bit**: activation.
  - **only 0.3 pJ/bit**: the interposer I/O.
- GDDR5 is about 14 pJ/bit (Fig. 1).
- Within a 60 W DRAM budget, HBM2 cannot exceed about 2 TB/s. A 4 TB/s GPU would burn "upwards of 120 W" of DRAM power.
- **Implication:** once you are on an interposer, most remaining energy is *intra-DRAM* data movement and activation. Bank-level PIM (compute at the bank I/O) and DRAM-on-logic face-to-face bonding (logic directly above the banks) attack the 2.24 pJ/bit term. Merely shortening the host link does not.

**Samsung, "PIM/PNM for Transformer based AI", Hot Chips 2023** [primary-full slides]
- https://www.hc2023.hotchips.org/assets/program/conference/day1/PIM/23_HC35_PIM_PNM_Samsung_final.pdf
- Normalized energy of data transfer, citing Eyeriss (ISCA 2016): ALU 1x, RF 1x, PE-to-PE 2x, buffer 6x, **DRAM 200x**.

**Dally (with Jeff Dean), GTC 2026 session transcript** [primary-full transcript, round 4; see Round-4 delta 3]
- An NVFP4 MAC is about 10 fJ.
- Transcript: pulling "four and a half bits" from HBM4 at "maybe three or four" (unit dropped by the transcript). That gives 13.5–18 pJ, ≈1,350–1,800× the MAC [D]. **The "1000×" and "SRAM read ≈10 fJ" wording is not in the transcript** [secondary: chiplog paraphrase].
- Transcript: most DRAM read energy is moving the bit to the GPU pins. Stacked DRAM-on-GPU gives about 10× bandwidth at about 10× lower pJ/bit, but is "not quite ready". SRAM costs about 10× DRAM in area per bit.
- "Don't move data." Paraphrased via https://www.chiplog.io/p/why-stacked-3d-dram-is-the-right

### 1.2 Fraction of system energy and time spent on data movement

**Boroumand et al., "Google Workloads for Consumer Devices: Mitigating Data Movement Bottlenecks", ASPLOS 2018** [primary-partial: abstract, intro, §3.3 read]
- https://research.ece.cmu.edu/safari/pubs/Google-consumer-workloads-data-movement-and-PIM_asplos18.pdf · DOI 10.1145/3173162.3173177
- Workloads: Chrome, TensorFlow Mobile, VP9 video playback, VP9 video capture.
- "**62.7% of the total system energy, on average, is spent on data movement between main memory and the compute units.**"
- Google Docs scrolling: **77%** of system energy is data movement.
- Per-workload numbers (from the Morning Paper summary, https://blog.acolyer.org/2018/04/18/google-workloads-for-consumer-devices-mitigating-data-movement-bottlenecks/ [secondary]):
  - Texture tiling + color blitting are 41.9% of page-scrolling energy.
  - TF Mobile packing + quantization are 39.3% of system energy.
- PIM accelerators in the 3D-stacked logic layer cut energy by **55.4% on average (up to 73.5%)** and time by **54.2% (up to 2.5x)**.
- Simple PIM cores cut energy by 49.1% on average.
- Area budget: about **50–60 mm² of free logic-layer area, or 3.5–4.4 mm² per vault**. A PIM core needs ≤9.4% of the per-vault area and a PIM accelerator ≤35.4%.

**Gómez-Luna et al. (PrIM)**: see §2.

### 1.3 Bandwidth wall, pin and beachfront limits, and "capacity-proportional bandwidth"

**Gholami, Yao, Kim, Hooper, Mahoney, Keutzer, "AI and Memory Wall", IEEE Micro 2024 / arXiv 2403.14123** [primary-full, 2-page version]
- Over about 20 years, peak HW FLOPS grew **60,000x (3.0x per 2 yrs)**.
- DRAM bandwidth grew **100x (1.6x per 2 yrs)**. Interconnect bandwidth grew **30x (1.4x per 2 yrs)**.

**Micron, Hot Chips 2026 tutorial** [V-deck, round 4: slide 3 viewed; earlier via STH/Tom's]. The slide shows the 3×/2yr TFLOPS vs <2×/2yr HBM BW fits with **no stated data sources**, next to the evolution graphic "2.5D attached → 2.5D advanced memory → Processing in Memory".
- "AI accelerator TFLOPS roughly grow by 3x every two years while the bandwidth of 2.5D attached memory such as HBM climbs at under 2x per two years."
- HBM3E uses about **3x the silicon** of DDR5 for the same capacity.
- In a GPU SiP with four 16-high stacks, memory is about 90% of total silicon (Tom's/wccftech).
- Thermals now drive the architecture.

**Ahn et al., Tesseract, ISCA 2015** [primary-partial]
- https://people.inf.ethz.ch/omutlu/pub/tesseract-pim-architecture-for-graph-processing_isca15.pdf
- **Core conceptual argument: memory-capacity-proportional bandwidth.**
  - HMC 1.0 gives 320 GB/s external per cube.
  - Internally, 32 vaults × 64-bit TSV interface × 2 Gb/s gives 512 GB/s per cube.
  - With 16 cubes, a host is still capped at about 320 GB/s, while PIM sees **8 TB/s aggregate**. That is 12.8x higher than the off-chip bandwidth, and the gap grows with capacity.
- Result: **10x performance, 87% lower energy** vs a 4-socket, 32-OoO-core system with 640 GB/s, on 5 graph workloads.

**Internal vs external bandwidth ratios in shipping or sampled PIM DRAM** [primary slides]

| Device | External | Internal | Ratio |
|---|---|---|---|
| Samsung Aquabolt-XL HBM2-PIM | 1.23 TB/s | 4.92 TB/s "PIM compute bandwidth" | 4x (HC33) |
| SK hynix GDDR6-AiM (x16) | 32 GB/s per chip | 512 GB/s per chip | 16x (SC23 exhibitor forum) |
| Samsung LPDDR-PIM (HC23) | — | 102.4 GB/s peak | 8x base LPDDR |
| Samsung LPDDR5X-PIM (HC26) | 76.8 GB/s | 614 GB/s | 8x [V-deck s7] |

**Beachfront and pin evidence**
- JEDEC HBM4 (JESD270-4, Apr 16 2025): 2048-bit interface, up to 8 Gb/s, ≤2 TB/s per stack, 32 channels. [secondary: JEDEC PR via search]
- d-Matrix Raptor (HC2026) claims **32.6 GB/s per mm²** vs **1.39–1.67 GB/s per mm²** for HBM4 / Rubin R200, on a silicon-area basis with effective bandwidth. **Capacity density is 11.4 vs 21.9–26.3 MB/mm²** [V-deck s28].
- Lane counts (chiplog, [secondary]):
  - d-Matrix Raptor: about 196K data lanes per chiplet, about 800K per package.
  - Blackwell: about 8K lanes to 8 HBM3E stacks.
  - Rubin: about 16K lanes to 8 HBM4 stacks.
- **Counter-signal:** JEDEC **SPHBM4** (Dec 2025) uses the same DRAM dies as HBM4 with 512 data signals at 4:1 serialization on *organic* substrate. Its stated purpose is longer channels, more stacks, and more capacity at lower cost (https://www.storagenewsletter.com/2025/12/17/jedec-sphbm4-new-standard-to-deliver-hbm4-level-throughput-with-reduced-pin-count/ [secondary]). Even inside HBM there is a pull toward *looser* coupling for capacity.

**SK hynix, SC23 exhibitor forum** [primary slides]
- https://sc23.supercomputing.org/proceedings/exhibitor_forum/exhibitor_forum_files/exforum133s2-file2.pdf
- Example: a 175B model (350 GB) on 5 GPUs with 80 GB and 3 TB/s each, 15 TB/s total. Each token takes about 23 ms, set by bandwidth.
- Decode arithmetic intensity is about 1 FLOP/B, against a GPU ridge of about 330. The GPU therefore uses about **0.3% of peak FLOPS**.
- GEMV is 92% of the V100 GPT time breakdown.
- Samsung HC23 adds: "GEMV accounts for 82.3% of total computation time (GPU)" and "GEMV portion can be 60–80% of total generation latency."

---

## 2. Research landmarks (processing-using-memory and processing-near-memory)

**Framing paper:** Mutlu, Ghose, Gómez-Luna, Ausavarungnirun, "A Modern Primer on Processing in Memory" (arXiv 2012.03112). It splits the field into processing-*using*-memory (analog, in-array) and processing-*near*-memory (logic layer or near-bank). It argues the main barrier is adoption: the processor-centric mindset, programming, and the system stack. [abstract only; used for framing only]

| Work | Venue | Mechanism | Headline result | Provenance |
|---|---|---|---|---|
| RowClone (Seshadri et al.) | MICRO 2013 | In-subarray row copy via back-to-back ACT | 4 KB copy: **11.6x latency, 74.4x energy**; **0.01%** chip-area cost | primary-full (users.ece.cmu.edu/~omutlu/pub/rowclone_micro13.pdf) |
| Tesseract (Ahn et al.) | ISCA 2015 | In-order cores in HMC logic layer, message passing | **10x perf, 87% energy** vs 640 GB/s 32-core server on graph workloads | primary-partial |
| Ambit (Seshadri et al.) | MICRO 2017 | Triple-row activation gives MAJ → AND/OR; dual-contact cell gives NOT | Bulk bitwise throughput **44x vs Skylake, 32x vs GTX 745, 2.4x vs HMC 2.0 logic-layer**; energy **35x** lower vs DDR3; **<1% DRAM chip area** | primary-full (MSR PDF) |
| Boroumand et al. | ASPLOS 2018 | PIM cores/accelerators in 3D logic layer | 62.7% of energy is data movement; −55.4% energy, −54.2% time | primary-partial |
| Newton (He et al., SK hynix) | MICRO 2020 | Minimal per-bank MAC + row-wide buffer, HBM2E-like | **10x vs an ideal non-PIM** (infinite compute, perfect use of external BW); **54x vs a realistic GPU** (Titan-V-like). Area overhead must stay ≤~25% | primary-full (microarch.org/micro53/papers/738300a372.pdf) |
| PrIM / UPMEM (Gómez-Luna et al.) | IEEE Access 2022 / arXiv 2105.03814 | Real DIMM-based PIM: 2,556 DPUs, 350 MHz in-order cores in DRAM chips | See below | primary-full |
| AttAcc (Park et al.) | ASPLOS 2024 | HBM3 PIM, GEMV per bank (16 per pCH), softmax on buffer die; xPU does FC | Up to **2.81× throughput / 2.67× energy eff. vs DGX_Large** (1,280 GB, 40 stacks; DGX+AttAcc uses 80 stacks); up to 3.49–5.93× vs DGX_Base 640 GB; internal BW 9× external (242 TB/s); **10.84% DRAM-die area** (≈25% if sized like HBM-PIM); simulated. The 3.19×/3.22× figure is NOT in this paper | primary-full (round 4) |
| NeuPIMs (Heo et al.) | ASPLOS 2024 | NPU (GEMM) + HBM-PIM (GEMV) with dual row buffers per bank, sub-batch interleaving | **3x vs GPU-only**, 2.4x vs NPU-only, 1.6x vs naive NPU+PIM throughput; about 3k tok/s at about 76 W for GPT3-7B, batch 8 [the 76 W figure is from search snippet, unverified] | primary (arXiv 2403.00579) |
| Duplex (Yun et al.) | MICRO 2024 / arXiv 2409.01141 | Logic-PIM: more TSVs, powerful PUs on the *logic die*, for Op/B of 1–dozens (MoE, GQA attention) | **Up to 2.67x throughput, 2.57x lower E2E latency, 42% less energy** vs GPU | primary-full |
| CENT (Gu et al.) | ASPLOS 2025 / arXiv 2502.07578 | **CXL-attached** devices with GDDR6-PIM near-bank PUs, GPU-free | **2.3x throughput, 2.9x less energy, 5.2x tokens per $** vs GPU at similar avg power (baseline 4x A100 80GB, Llama2-70B) | primary-partial |
| TokenStack | arXiv 2605.05639 (2026) | Vertically heterogeneous HBM-PIM: capacity layers + PIM layers, base die as controller | **1.62x throughput, 1.70x SLO capacity over AttAcc; −30–47% energy per token** | abstract only |
| DCC compiler | arXiv 2511.15503 | Data-centric compilation for PIM | Up to 7.68x (avg 2.21x) on HBM-PIM and up to 13.17x (avg 3.92x) on AttAcc-PIM kernels vs GPU; 4.52x end-to-end GPT-3/LLaMA-2 | abstract only |

### PrIM / UPMEM details [primary-full, arXiv 2105.03814v7]

Hardware:
- UPMEM DPU: about 700 MB/s MRAM bandwidth per DPU at 350 MHz.
- Aggregate: about **1.7 TB/s** theoretical and **1.6 TB/s** sustained across 2,556 DPUs.
- Native ops are 32-bit integer add/sub and bitwise only. Mul, div, and FP run in software: FP32 add about 4.9 MOPS per DPU, FP32 mul 1.91 MOPS.

Results:
- Faster than the CPU by **93.0x (2,556 DPUs) and 27.9x (640 DPUs)** on the 13 of 16 benchmarks that avoid heavy inter-DPU sync and FP.
- Over all 16 benchmarks the speedups fall to 23.2x and 10.1x.
- Faster than a Titan V GPU by **2.54x** on 10 of 16 benchmarks.
- 640-DPU energy: **1.64x** better than the CPU on average, **5.23x** on 12 benchmarks, up to 39.14x.

**Caveat:** the CPU baseline is a 4-core Xeon E3-1240, a weak comparator.

Key takeaways, quoted from the paper:
- (1) The UPMEM architecture is **fundamentally compute-bound**: it saturates at 0.25 ops/byte for int add.
- (2) Best workloads use no arithmetic or only simple arithmetic.
- (3) Best workloads need little or no inter-DPU communication, because DPUs have no direct channel and all communication goes through the host.
- (4) It delivers large gains over CPU and GPU where (1)–(3) hold.

---

## 3. Products and industry moves

### 3.1 PIM in commodity DRAM form factors

**Samsung HBM2-PIM "Aquabolt-XL"** (ISSCC 2021 paper 25.4; Hot Chips 33 slides https://www.hc33.hotchips.org/assets/program/conference/day1/20210813_HC33_Aquabolt-XL_PIM_Jin_Kim_slide.pdf) [primary slides]

Design:
- 6 GB, 20 nm. A 16-lane FP16 SIMD PCU at each bank's I/O boundary, using bank-level parallelism.
- Drop-in JEDEC Aquabolt HBM2 form factor and timing. It works with an unmodified controller on a GPU and on a Xilinx U280.
- PIM is triggered by standard RD/WR commands in PIM mode.
- 4x the in-DRAM bandwidth (1.23 → 4.92 TB/s).

Results:
- System level: "over **2X** system performance while reducing energy consumption by more than **70%**."
- GPU evaluation: **11.2x** on a GEMV microkernel, **3.5x** on speech recognition. Energy per bit transferred falls **3.5x**; system energy efficiency rises **3.2x**.
- HBM-PIM draws only **5.4%** more power than HBM: global bus and PHY power fall while the cell, local bus, and PIM power rise.
- U280 FPGA: GEMV 2.82x, ADD 2.85x, LSTM 2.54x. RNN-T latency 2.49x lower and energy 62% lower.

LPDDR5-PIM (simulated):
- RNN-T 2.3x perf / 3.85x energy; transformer translation 1.8x / 2.17x; GPT-2 2.4x / 4.35x.
- Vision models such as DeepLab and StarGAN gain only **1.0–1.3x**. The gains are workload-selective: large for BLAS2/GEMV, small for convolution and BLAS1-heavy work.

Capacity penalty, resolved via the SK hynix ISSCC'22 comparison table [primary, round 4]: 6 GB/cube = 4H 4 Gb PIM core dies + 4H 8 Gb normal core dies. PIM dies are at half density, and the stack loses 25% vs 8 GB. PCU area % and pJ/bit are still [pending full text #2].

**Samsung HBM-PIM in AMD MI100, Hot Chips 2023** [primary slides]
- A 96-GPU cluster (12 nodes × 8 MI100-PIM).
- "PIM can reduce energy by **85%** compared with conventional HBMs" (memory-side energy).
- About **2x** system energy efficiency and more than 2x GPT performance; slides show 1.79–2.24x energy efficiency and 2.16–2.33x performance.
- MoE: **2.88–3.61x** energy efficiency, 1.2–2.71x performance.
- LPDDR-PIM: 4.51x perf and −72.5% energy (and other rows).
- Samsung's hierarchy slide places **CXL-DRAM/CXL-PNM at "~512 GB, extreme capacity, moderate cost"** and proposes a 512 GB, 1.1 TB/s CXL-PNM. Samsung is pursuing (A) and (B) at the same time and treats them as tiers of one hierarchy.

**Samsung LPDDR5X-PIM, Hot Chips 2026** [V-deck, round 4; numbers below confirmed in the deck. **The deck has no energy or power data**; "higher peak power" and "accuracy" caveats are press-only]
- 16 GB package in the standard 561-ball LPDDR5X footprint, working with a conventional controller.
- 614 GB/s internal vs 76.8 GB/s external. 2.4 TOPS SINT4, about 1.2 TFLOPS FP8 per package.
- Llama-3.1-8B (320-token context): **3.01x tokens/s** (81.3 vs 27.0) and 2.28x latency.
- Caveats:
  - Tested only on Samsung's own SoC.
  - Slight accuracy differences that Samsung has not quantified.
  - Higher peak power.
- Productization 2026. GAIA AI-PC accelerator mass production "as early as 2027".
- Samsung and SK hynix are pushing an **LPDDR6-PIM JEDEC standard** (reported Dec 2024; spec targeted before end of 2026) [secondary: TrendForce].

**SK hynix GDDR6-AiM / AiMX** [primary slides: HC34 poster https://hc34.hotchips.org/assets/program/posters/hc34.SKhynix.YongkeeKwon.v03.pdf, SC23 forum]
- 1ynm, 8 Gb, 16 Gb/s/pin, **1 TFLOPS per chip** (BF16 MAC in all 16 banks), activation-function LUTs.
- 32 GB/s external vs 512 GB/s internal per x16 chip.
- AiMX prototype: 2 FPGAs + 16 AiM packages.
- Claims about 6 TB/s "effective GEMV bandwidth" (6x the GDDR6 peak).
- Press at ISSCC'22 reported "up to 16x faster, 80% lower power" [secondary]. **The ISSCC'22 paper itself (read in round 4) gives only "up to 10×" system speedup vs a GPU with HBM2** (FPGA emulation, 2 Gb/s/pin), and **no energy figure**. PU = 0.19 mm² each, 16 per 4 Gb die. The package is an 8 Gb dual-die package.
- Newton (MICRO'20) is the architecture paper behind it.

**UPMEM**
- First commercial general-purpose PIM DIMM (DDR4, 2,560 DPUs per server).
- Reported **acquired by Qualcomm in 2025**: PitchBook and CB Insights list the deal; an arXiv 2510.15927 abstract says "recently acquired by Qualcomm". The exact date conflicts between June and Sept 24, 2025. [secondary]

### 3.2 HBM base-die logic ("custom HBM")

- **HBM4 base die moves to logic processes:**
  - TSMC N12 for mainstream (1.1 V → 0.8 V, about 1.5x efficiency) and N3P for C-HBM4E (0.75 V, about 2x efficiency).
  - In C-HBM4E, **memory controllers and the PHY move into the base die**.
  - Source: TSMC OIP Amsterdam, via TrendForce/HardwareLUXX, https://www.trendforce.com/news/2025/12/01/news-tsmc-unveils-custom-c-hbm4e-details-n3p-logic-dies-reportedly-target-2x-efficiency-gain/ [secondary]
- Vendor processes:
  - SK hynix uses TSMC 12 nm for mainstream and reportedly 3 nm for premium (NVIDIA, Google TPU) HBM4E.
  - Samsung uses its own **4 nm** for the HBM4 logic die [V-deck HC26 s6–7]. A press report says **2 nm** for custom HBM4E, but Samsung's own HC26 process table lists **4 nm B-die for HBM4E (2027)**. The 2 nm report is unconfirmed.
  - Micron uses TSMC for its HBM4E base die (2027).
  - Sources: TrendForce, EE Times CES 2026 (https://www.eetimes.com/the-state-of-hbm4-chronicled-at-ces-2026/) [secondary]
- **Samsung's Hot Chips 2026 three-phase roadmap** [V-deck, round 4; see delta 5]:
  - **(1) cHBM (custom HBM):** controller and SRAM repair move into the base die, which reclaims PHY area.
  - **(2) aHBM:** RAS sensors, on-die test, **external-memory extension PHYs**, and **processing elements for memory-bound AI ops** in the base die.
  - **(3) zHBM:** DRAM stacked directly on the XPU, which removes the interposer and the PHY. Example: 4 zHBM stacks next to a 1200 W GPU save about 100 W.
  - Headline claims, now traced to slide 23 and both projections:
    - "−70%" is unitless "Power Efficiency", **zHBM vs HBM5**.
    - "230%" is **4× zHBM vs 4× HBM4E** DRAM bandwidth, ≈14 → 32 TB/s.
    - DRAM power drops ≈220 → 120 W, assuming a 1200 W GPU.
  - Samsung says most of its customers already pursue custom HBM.
  - Phase 2 adds *capacity extension* PHYs to the base die, so the base die becomes a bridge to a further memory tier. This converges with direction (B).

### 3.3 3D DRAM-on-logic (hybrid or face-to-face bonded)

**d-Matrix Raptor, Hot Chips 2026, co-presented with Meta** [V-deck, round 4; see delta 4 for scope. Lines below were originally from press]
- TSMC N4 compute die bonded **face-to-face at 36 µm pitch** onto a custom DRAM die (1-Hi). No memory PHY.
- 32 GB per card. **>100 TB/s** target, about 33 TB/s practical after overfetch.
- **0.37 pJ/bit** (296 W of I/O at 100 TB/s), vs HBM4 at "2–3 pJ/bit".
- **2.96 mW per GB/s vs about 40 mW per GB/s for HBM (13.5x)**.
- **~20x bandwidth per mm²** vs Rubin R200 HBM4.
- 0.5 W/mm² power-density cap; 105 °C Tj.
- Trade-off: **5.6× bandwidth and 6.3× lower energy vs projected HBM4 (≈2.4 pJ/b)** [V-deck s6].
  - On silicon area: **≈0.43–0.52× the capacity per mm² for ≈20× the bandwidth per mm²** [V-deck s28; D].
  - "1/6 capacity of a 192 GB config" is press-only (Hardware Busters) and not in the deck.
  - 0.37 pJ/b is **I/O-only** (s23). The 13.5× compares it with a 5.0 pJ/b HBM4 *system* figure (40 mW/(GB/s)).
  - The chiplet also has 2× LPDDR5X as a secondary capacity tier (s13, s18).
- Earlier chiplog figures [secondary]: 0.35–0.4 pJ/bit with micro-bumps, about 0.1 pJ/bit with hybrid bonding. Thermals limit near-term stacks to about 2–4 DRAM layers.
- Still early silicon, with no shipping date. Targets NVIDIA MGX rack integration by Q4 2027 [secondary].

### 3.4 The SRAM extreme: no DRAM at all

- **Cerebras WSE-3:** 44 GB on-wafer SRAM at **21 PB/s**, 900K cores, 5 nm, 46,225 mm² (vendor datasheet via search [secondary]).
- **Groq LPU (older generation):** about 230 MB SRAM per chip and about 80 TB/s on-die [secondary]. **Groq 3 LP30 (HC26 deck):** per-chip numbers not stated. Rack totals divided by 256 give ≈0.5 GB, ≈156 TB/s and ≈1.23 PF FP8 per chip [D].
- **NVIDIA licensed Groq's technology** for about $20B on Dec 24, 2025 and hired its founder (Groq stays independent) [secondary: multiple outlets].
- At HC2026, NVIDIA presented the **Groq 3 LPX** [V-deck, round 4: rack totals and mappings confirmed in the deck]:
  - 256 LPUs per rack; **128 GB SRAM, 40 PB/s aggregate** per rack; about 315 PF FP8.
  - Pairs with Vera Rubin: GPUs do prefill and decode-attention (KV-cache-capacity-bound), LPUs do decode FFN (weight-bandwidth-bound).
  - Up to 5x at the highest tok/s per user.
  - Artificial Analysis measured 3,431 output tok/s on Gemma-4-31B at 100K context [press-only; the deck says only "4× faster" (s9)]. Deck: 10,996 TPS/user on Gemma-4-31B coding (16K ISL). On GPT-OSS-2T at 400K cached ISL: ~3× TPS/MW and ~5× interactivity (s38–42). Mapping: "Weights Stored in SRAM, KV-Cache Stored in DRAM" (s34).
- **Significance:** the market leader now runs a *heterogeneous* system where the most bandwidth-bound, capacity-light part of decode runs on SRAM and the capacity-heavy part (KV cache) stays on HBM. Direction A is being deployed as a *tier*, not a replacement.

### 3.5 Near-data compute added to CXL memory (A meets B)

**XCENA MX1 + Samsung, Hot Chips 2026** [V-deck, round 4; see delta 7 for workloads (6 analytics kernels vs Xeon 6767P, idle power excluded), RAG 64×/65×, KV-attention 3.35×/3.84×, and 268.8 vs 64 GB/s behind/through the link]
- CXL 3.2 x8 Type-3 device: up to 2 TB DDR5 plus SSD-backed byte-addressable memory.
- **3,072 RISC-V cores** on Samsung 4 nm; 40 W chip, 90 W board; about 3 TFLOPS FP32.
- Stated motivation: the CXL link gives only 64 GB/s per direction, so "MX1-side compute can avoid the constrained host interface, and avoids the power overhead of traversing the CXL link."
- Claims **up to 4.7x throughput and 18.7x energy efficiency** vs host CPU over CXL on data-analytics kernels.
- Samsung's earlier CXL-PNM (HC23) and the academic CENT (ASPLOS'25) follow the same pattern.

---

## 4. What direction (A) optimizes, and where the evidence is strongest

**Resources and metrics optimized**
1. **Energy per bit moved (pJ/bit).**
   - The scale runs: off-chip DDR, about 10–20+ pJ/bit (Horowitz, Keckler) → GDDR5 about 14 → HBM2 about 3.9 → HBM4 about 2–3 → F2F 3D DRAM about 0.37 (claimed), with about 0.1 projected for hybrid bonding → on-die SRAM at about fJ/bit.
   - Inside HBM, the dominant remaining term is *on-DRAM-die movement plus activation*, not the interface (FGDRAM).
2. **Bandwidth per unit of shoreline or area (GB/s per mm, GB/s per mm²) and per pin.**
   - Internal bank-level bandwidth is 4–16x the external pins in every PIM product (Aquabolt-XL 4x, LPDDR(5X)-PIM 8x, AiM 16x).
   - Tesseract's "capacity-proportional bandwidth" is the scaling form of the argument.
3. **Bandwidth per watt and per $ for low-arithmetic-intensity phases.**
   - TCO examples: CENT's 5.2x tokens per $, and SK hynix's "0.3% of GPU peak used in decode" argument.
4. **Latency** is a secondary motivation. It is explicit for SRAM machines (Groq: tok/s per user) and for graph pointer-chasing (Tesseract).

**Workloads where the evidence is strongest**
- Operations with roughly ≤1–8 FLOP/byte, little reuse, and *partitionable* data:
  - LLM decode GEMV and attention over the KV cache (AttAcc, NeuPIMs, Duplex, CENT, AiM, HBM-PIM, LPDDR5X-PIM).
  - Embedding and recommendation (AXDIMM).
  - Streaming and scan analytics, select/unique, histogram, bulk bitwise and bitmap indices (Ambit, PrIM).
  - Bulk copy and initialization (RowClone).
  - Graph analytics (Tesseract).
  - Consumer-device primitives: tiling, packing, quantization (Boroumand).
- **Weak cases:** compute-dense GEMM, convolution, and BLAS1 vision (Samsung's own table shows 1.0–1.3x); FP-heavy or multiply-heavy kernels on weak PIM cores (UPMEM); communication-heavy workloads such as BFS, NW, and SpMV across DPUs.

**Recurring costs and constraints of (A)**, which matter for the paradox:
- Logic in a DRAM process is slow and area-expensive, which costs memory density. CENT says so explicitly: "near-bank PUs, fabricated in the DRAM process, impose a high area overhead that reduces the memory density."
- Newton caps PIM area at about ≤25%. AttAcc's bank-level GEMV costs 10.84% of the DRAM die (≈25% at HBM-PIM-like unit size). Aquabolt-XL is 6 GB (PIM dies at half density). d-Matrix has ≈0.5× HBM4's capacity per mm² for ≈20× the bandwidth per mm². Groq/LPX holds 128 GB of SRAM per rack.
- Thermal limits: 3D stacks are limited to 2–4 layers at GPU power densities, and Micron says it is "architecting around thermals".
- Programmability and inter-unit communication (PrIM takeaways).

**Net:** (A) buys bandwidth and pJ/bit by spending capacity per mm², per $, and per watt-of-thermal-headroom. This is the hook into (B).

---

## 5. Surprising findings and open threads

1. **In HBM, the interface is not where the energy goes.** In HBM2, interposer I/O is about 0.3 of 3.92 pJ/bit; on-die movement (2.24) and activation (1.21) dominate (FGDRAM, MICRO'17). This explains why bank-level PIM and F2F DRAM-on-logic, not just "shorter wires to the host", are the real levers. Worth checking whether HBM3E and HBM4 breakdowns, for example from Micron or Samsung ISSCC papers, keep this ratio.
2. **Convergence, not opposition:**
   - Samsung puts HBM-PIM, LPDDR-PIM, and CXL-PNM on one hierarchy slide (HC23).
   - Samsung's aHBM roadmap adds external-memory-extension PHYs to the HBM base die.
   - XCENA and Samsung put 3,072 cores *inside a CXL memory expander* because the CXL link is narrow.
   - CENT builds a GPU-free LLM system from CXL-attached PIM.
   - NVIDIA pairs SRAM LPUs (A-extreme) with HBM GPUs (capacity) and splits decode across them.
   - Suggests that A and B are two ends of one capacity-vs-bandwidth hierarchy. Most systems put compute where the bytes-per-FLOP is highest.
3. **The capacity price of (A) is explicit in vendor numbers:**
   - d-Matrix: ≈0.43–0.52× HBM4's capacity per mm² (11.4 vs 21.9–26.3 MB/mm²) for ≈20× the bandwidth per mm² [V-deck s28]. The "1/6 per card" figure is press-only. d-Matrix also ships an LPDDR5X secondary tier per chiplet.
   - Aquabolt-XL: 6 GB = 4× 4 Gb PIM dies + 4× 8 Gb normal dies, versus 8 GB for an 8-Hi Aquabolt (−25%) [AiM ISSCC'22 table].
   - Micron: HBM3E needs 3x the silicon per bit vs DDR5.
   - SPHBM4: JEDEC is relaxing coupling (organic substrate, longer channels) to get more stacks and capacity.
   These are the strongest bridge to Q2 (direction B).
4. **Baselines in PIM papers are often weak:** a 4-core Xeon E3 for PrIM, "realistic GPU" vs "ideal non-PIM" for Newton (54x vs 10x). The honest number is usually the *ideal-non-PIM* or iso-bandwidth comparison. AttAcc (round 4): the headline 2.81× is iso-capacity vs a 1,280 GB DGX, but it uses **twice the HBM stacks** (80 vs 40), and part of the gain is extra KV capacity, not PIM. It is all simulated.
5. **Commercial traction is real but narrow:**
   - UPMEM was absorbed by Qualcomm.
   - HBM-PIM has not become a mainstream HBM SKU; I found no evidence of volume shipment [unverified].
   - LPDDR5X/6-PIM (on-device LLM decode) is the most product-like path for 2026–27.
   - The base-die-logic route (cHBM/aHBM) is advancing fastest because it needs no change to the DRAM array.
   - Round 4: Samsung's HC26 deck shows memory-controller offload as "actively being ported" (Phase 1). PEs in the B-die are Phase 2 (aHBM), with "limited silicon area and increased thermal density" as the named challenges. Micron lists "GPU offloading" for the base die. **No shipping HBM4/4E base die with PEs is evidenced.**
6. **Moving logic into the base die moves heat into the stack.** Micron says thermals now drive design, and Samsung's PHY power density rises from 0.5 to >2.0 W/mm² (sHBM4E → sHBM5) [V-deck s12, round 4]. Logic in the base die may limit stack height, which is again a capacity cost.
