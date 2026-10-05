# Q5: Critiques and failure cases of each direction, and where they converge

Sub-question: (i) strongest critiques/failure cases of (A) tight memory-compute coupling (PIM/NMP/3D/HBM) and (B) disaggregation/far memory (CXL pooling, RDMA far memory); (ii) where they converge or combine.

Compiled 2026-10-02; round-4 update 2026-10-03. Labels: [full text] = read in the primary source; [abstract only] = only abstract read; [secondary] = from a secondary source (press, survey, or search snippet), not the primary; [press, full article] = full press article read, so the article's own claims are verified but the reporter's attribution to a speaker is second-hand; [unverified] = could not confirm; [pending full text] = see FULL TEXT NEEDED; [synthesis] = my own inference, not a claim made by any source. Full-text files are in `fulltext/` (see `fulltext/MANIFEST.md`).

---

## Round-4 deltas (2026-10-03)

All three Q5 full-text items are now resolved. "To PiM or Not to PiM" was not provided; Aguilera et al. is assigned to R2c.

### D1. DIGITIMES, 27 Aug 2026 (Daniel Chiang, Seoul) — [press, full article]
File: `fulltext/SK Hynix questions PIM as thermal, packaging limits constrain HBM.mhtml`

**Who and where.** The source is **Youngpyo Joo, VP and head of System Architecture at SK hynix, in a keynote at Dell Tech Forum 2026**. It is *not* SK hynix's Hot Chips 2026 talk.

**Attributed to Joo** (reported speech; the article gives only one direct quote):
- The industry needs to "rethink the role and positioning of PIM" because LLMs and AI inference are shifting the architecture.
- The capacity argument: inference capacity demand is so extreme that silicon area spent on logic "inherently reduces the space available to store actual data". So minimizing DRAM-to-logic data movement through advanced packaging is "a far more viable industry focus".
- PIM "may serve niche workloads, it is not a broad-spectrum fix".
- SK hynix's three development vectors are bandwidth density, energy per transported bit, and utilization.
- SK hynix's roadmap target is 3D stacking of memory directly on host accelerators, for tens of TB/s. Stacking 8, 12 or 16 layers on hot GPUs creates "extreme thermal challenges". SK hynix is co-developing cooling with Dell.

**In DIGITIMES's own voice, not attributed to Joo.** "Real-world manufacturing challenges continue to plague PIM commercialization":
- compute units enlarge the DRAM die and raise cost;
- dense compute "risk[s] destabilizing the power delivery network";
- concentrated heat "worsens DRAM data leakage";
- commercialization needs better power-management ICs and package thermals.

Do not cite these as SK hynix statements.

**Net effect on these notes:**
- "SK hynix questions PIM" is now supported, at press-report level (attribution second-hand).
- It adds a **capacity-penalty** critique of PIM that I did not have before: logic area costs bits. This resolves the synthesis draft's "capacity penalty [pend]".
- The headline's "thermal" refers mainly to SK hynix's *own* 3D-on-logic roadmap, not to PIM specifically.

### D2. SK hynix Hot Chips 2026 tutorial (Jaesik Lee, "Advanced Packaging for HBM")
File: `fulltext/hotchips26/decks_text/HC2026.SK_hynix.Jaesik_FINAL1.pdf.md`. I read the extracted text for all 22 slides and viewed the slide-11 and slide-12 images myself.

- **The deck never mentions PIM.** The PIM critique comes only from the Dell-forum keynote (D1).
- **Slide 11, "HBM CHALLENGES"** [vendor slide; image viewed]: "Thermal burden : x2.2" across six HBM generation points.
  - Over the same points, bandwidth rises 1.0 → 8.0 ("x1.6 per Gen") and stack count 4 → 16 ("X2 per two Gen").
  - TSV count rises to ×5.0, with an area penalty of ×1.3.
  - The extracted PDF text for this slide contains only the headline ("HBM Power & Bandwidth ↑ Thermal Issue & TSV Area ↑"). The numbers come from the chart image.
  - This confirms the earlier Tom's Hardware "2.2x" claim, previously marked unverified.
- **Slide 12** [vendor slide; image viewed; source on slide: ISMP 2024]: relative thermal resistance is 1x (HBM2, TC-NCF), 0.55–0.65x (HBM2E/3, MR-MUF), 0.45–0.55x (HBM3E/4, Adv. MR-MUF) and 0.40x (HBM4E, hybrid bonding).
- **Slide 6** [extracted text]: HBM4 has ">2TB/S BW", "14+% Improving thermal resistance than HBM3E", up to 48 GB (12Hi in production, 16Hi under qualification) and "775um Z-Height".
- **Slide 18** [extracted text]: the base die moves to a "logic foundry process", with power TSVs "everywhere" giving a "75% PDN Improvement".
- [synthesis] Packaging cuts thermal resistance about 2.5x (1 → 0.40x) while thermal burden rises 2.2x. HBM's own bandwidth growth is using up the packaging gains, which leaves little thermal headroom to add PIM compute inside the stack. This is my inference; SK hynix does not say it.

### D3. Park et al. (Samsung / SNU / UIUC), HPCA 2024, DOI 10.1109/HPCA57654.2024.00078 — [full text]
File: `fulltext/An_LPDDR-based_CXL-PNM_Platform_*.pdf`

**Method.** The headline numbers are **simulated**, not measured.
- The FPGA prototype on a custom CXL module with a 4th-gen Xeon host is used only for *functional* verification.
- Performance comes from a cycle-level simulator, validated within 0.5% against the FPGA prototype.
- That simulator then uses timing and power parameters synthesized at **7 nm, 1.0 GHz** for the target LPDDR5X CXL-PNM. "An ASIC version of the CXL-PNM controller is under development."

**Baseline.**
- A DGX with **8× A100-40GB** (not 80 GB), 1.55 TB/s each, running FasterTransformer.
- Models are OPT-13B (single device) and OPT-66B (8 devices), with 64 input and up to 1,024 output tokens.

**Device.**
- 512 GB LPDDR5X at 1.1 TB/s.
- 4.09 TFLOPS peak.
- Power: controller ~90 W (CXL IPs plus LLM accelerator), DRAM ~40 W, about **150 W total**.
- **PCIe-card form factor, not a DIMM.**
- The paper does not state the CXL link width. If it is x16 Gen5 (~64 GB/s per direction), internal-to-link bandwidth is about 17x [synthesis; my assumption, not stated in the paper].

**Results:**
- 23% lower latency, 31% higher throughput, 2.8x energy efficiency (2.9x in §VIII) is the *model-parallel* configuration with 8 devices on OPT-66B.
- The data-parallel configuration gives 53% higher throughput and 4.4x energy efficiency, with higher per-request latency.
- Single device on OPT-13B: **10.8% lower throughput and 10.9% higher latency than one A100**, with 2.9x energy efficiency (77.1 W vs 253 W).
- The 138.8x figure for OPT-30B compares against a single 40 GB GPU paging weights from the host. That is a capacity strawman, not a fair comparison.

**Cost (Table III) — answers the coordinator's question:**
- Hardware cost is **8 × $10,000 per A100 vs 8 × $7,000 per CXL-PNM device**, i.e. 1.42x, which gives the "30% lower hardware cost".
- The paper says: "we compare the costs of only the GPU and CXL-PNM devices after excluding the cost of other appliance components such as that of the host CPU and its DRAM and SSD".
- **The CXL controller is inside the per-device price.** The device *is* the controller + accelerator + LPDDR card. However, **the $7,000 is an assumed figure with no derivation or source given**, for a product whose ASIC is still in development.
- **No CXL switch appears anywhere in the paper** (searched the full text for "switch"). The 8 devices are host-attached, so there is no switch cost to include or exclude.
- Energy: 43.2 vs 15.4 kWh/day at $0.1035/kWh (Idaho); 0.83 vs 3.54 M tokens per dollar of operating cost.

**Implication.** The CXL-PNM TCO claim is a device-level bill-of-materials comparison against a 2020-era GPU, with an assumed device price. It does not engage Levis's infrastructure-cost argument, because there is no pool and no fabric: this is single-host attached accelerator memory. It is evidence for **B1-style expansion plus compute**, not for pooling [synthesis].

### D4. BEACON (Huangfu, Malladi, Chang, Xie; UCSB + Samsung Semiconductor), MICRO 2022, DOI 10.1109/MICRO56248.2022.00057 — [full text]
File: `fulltext/BEACON_*.pdf`

**Where the processing sits.** Two variants:
- **BEACON-D:** compute inside enhanced CXL-DIMMs ("CXLG-DIMMs"). The modifications are "done to the PCB board of the CXL-DIMMs", with 128 PEs per DIMM.
- **BEACON-S:** compute inside an enhanced CXL switch, with 256 PEs per switch.
- Both keep "no modifications to the cost-sensitive DRAM dies". The PEs are fixed-function (genome seeding, k-mer counting, pre-alignment), so the user does no programming.

**Method.** **Simulated.** A modified Ramulator cycle-accurate model, PEs synthesized pre-layout in 28 nm, DRAM energy from DRAMPower, communication energy taken from the literature. Baselines are a 48-thread CPU and the prior DDR-DIMM NDP designs MEDAL and NEST, re-implemented in Ramulator.

**Results:**
- Versus the state-of-the-art DIMM NDP designs: **4.70x (BEACON-D) and 4.13x (BEACON-S)** average performance (abstract).
- Versus the 48-thread CPU: roughly 144x–572x per application, and 100x–960x lower energy.
- **Energy efficiency is *lower* than MEDAL's: 85.58–91.26%** of MEDAL's. The speedup comes from CXL's communication bandwidth and memory capacity, not from energy.
- In the unoptimized "CXL-vanilla" design, **communication is 60.68% (D) / 52.35% (S) of energy**. With data packing, placement and address mapping, this falls to about 14% (D). Computation is under 1% of energy.
- BEACON reaches about 96% of an idealized-communication design.

**Implication** [synthesis]:
- BEACON shows both convergent placements: compute in the far-memory module and compute in the fabric switch.
- It also shows the cost of escaping the DRAM die: communication becomes the dominant energy term until software and layout optimizations recover locality.

### D5. Changes inside these notes
- A4: the "SK hynix questions PIM" claim is resolved (press-level), and the capacity critique is added.
- A2: SK hynix's thermal numbers are upgraded to vendor-slide level.
- C2 table: the BEACON row is filled in.
- C3: the CXL-PNM bullet is corrected and qualified (simulated; assumed device price; no switch).
- FULL TEXT NEEDED: items 1, 2 and 5 are closed.

### D6. Proposed changes to SYNTHESIS_DRAFT.md v0.3 (not applied; for the coordinator)

| # | Location | Current text | Correction | Source |
|---|---|---|---|---|
| 1 | §4 A2 "Pays with" | "capacity penalty [pend]" | "capacity penalty: logic area displaces bits, and inference is capacity-bound (SK hynix VP Joo, Dell Tech Forum 2026) [Pr]" | DIGITIMES 2026-08-27, ¶3–4 |
| 2 | §4 A2 "Pays with", thermal | "thermal (refresh doubles every ~10 °C above 85 °C [P])" | append "; HBM's own thermal burden rose 2.2× over six generations while packaging cut thermal resistance to 0.40× [V: SK hynix HC'26 slides 11–12]". Optionally add DIGITIMES's own list (die cost, PDN, leakage) tagged [Pr, DIGITIMES's own analysis] — not attributed to SK hynix | HC2026.SK_hynix.Jaesik slides 11, 12 (images); DIGITIMES ¶6 |
| 3 | §4 A2 "Status" | "Niche. HMC dead (2018); HBM-PIM at demo stage; …" | add "SK hynix deprioritizes PIM for packaging/3D stacking ('may serve niche workloads, it is not a broad-spectrum fix') [Pr]" | DIGITIMES ¶7 |
| 4 | §0 point 5, A2 line | "Samsung/SK hynix demos; LPDDR5X-PIM productization [S]" | "Samsung demos and LPDDR5X-PIM productization [S]; SK hynix now publicly repositions PIM as niche [Pr]" | DIGITIMES ¶1–2, ¶7 |
| 5 | §5.3 Products | "Samsung CXL-PNM [P/pend: HPCA'24]" | "[P; simulated, 7 nm ASIC parameters; ASIC in development]: 8 devices vs 8× A100-40GB on OPT-66B give 23% lower latency, 31% higher throughput, 2.8× energy efficiency; one device is 10.8% slower than one A100 on OPT-13B; ~150 W PCIe card. 'Announced' → 'paper prototype'" | Park HPCA'24 §VII–VIII, Tables II–III |
| 6 | §5.3 / §0 point 82 cost framing (wherever CXL-PNM TCO appears) | (implied TCO advantage) | "30% lower hardware cost rests on an *assumed* $7k/device vs $10k/A100, device-only (host CPU/DRAM/SSD excluded), with no CXL switch. It does not test Levis's fabric-cost argument" | Park §VIII-B, Table III |
| 7 | §5.3 counter-point | "Structera A ~200 GB/s vs 64 GB/s link, about 3×… It escapes PIM's objections by giving up PIM's main benefit." | Qualify: the internal/link ratio depends on the media. LPDDR5X CXL-PNM has 1.1 TB/s internally, ~17× an assumed x16 Gen5 link [D; link width not stated in Park]. So the claim holds for DDR5-based controllers, not for LPDDR-based ones. In both cases it is channel-level bandwidth, not bank-level | Park abstract/§IV; Marvell [V] |
| 8 | §5.3 Evidence | (BEACON absent) | add: "BEACON places PEs on the CXL-DIMM PCB or in the CXL switch, with no DRAM-die change. 4.70×/4.13× vs DIMM NDP, but only 85–91% of MEDAL's energy efficiency; communication is 52–61% of energy until locality optimizations [P; simulated]". This supports the 'escape the die, pay in communication' counter-point | BEACON §I abstract, §VI energy breakdown (Fig. 17) |
| 9 | §5.1 or §4 A1 | (no SK hynix roadmap) | optional: "SK hynix's stated end-state is memory 3D-stacked directly on accelerators for tens of TB/s, with 'extreme thermal challenges' [Pr]". This is a vendor choosing A1-style proximity over A2 compute | DIGITIMES ¶8–9 |

Page/paragraph locators: DIGITIMES has no page numbers; ¶ counts start from the body text after the dateline. Park HPCA'24 (pp. 970–982): abstract p. 970; methodology §VII, Table II and the single-device results on p. 979; Table III and §VIII-B (cost) on p. 980. BEACON (MICRO'22, pp. 727–743): abstract p. 727; per-application results pp. 737–738; energy breakdown (Fig. 17) p. 739.

---

## FULL TEXT NEEDED

1. ~~DIGITIMES 27 Aug 2026~~ — **resolved in round 4** (see D1). The full article was read from the provided .mhtml.
2. ~~Park et al., HPCA 2024~~ — **resolved in round 4** (see D3).
3. **"To PiM or Not to PiM"**, ACM Queue (2023), https://queue.acm.org/detail.cfm?id=3580503 (Cloudflare-blocked; I could not get the authors either).
   - Needed for: a practitioner-facing critique of PIM adoption, as a counterweight to Mutlu-group sources, which are advocates.
   - Status: no claims in these notes depend on it. It is optional.
4. **Aguilera et al., "Memory disaggregation: why now and what are the challenges", ACM SIGOPS OSR 57(1), 2023**, https://dl.acm.org/doi/10.1145/3606557.3606563 (403).
   - Needed for: a VMware-research view of challenges (failure domains, security, performance cliffs) to triangulate against Maruf & Chowdhury.
   - Status: nothing depends on it. It is optional.
5. ~~BEACON, MICRO 2022~~ — **resolved in round 4** (see D4).

Remaining open items: 3 ("To PiM or Not to PiM"; not provided, optional) and 4 (Aguilera; assigned to R2c). No Q5 claim depends on either.

---

## (i-A) Critiques and failures of tight coupling (PIM / NMP / 3D-stacked logic)

### A1. A DRAM process is bad at logic (the hardest physical critique)
- UPMEM, Hot Chips 31 (2019), Fabrice Devaux, slide "The Hurdles on the road to the Graal" [full text, slides]: "DRAM process highly constrained • 3x slower transistors than same node digital process • Logic 10 times less dense vs. ASIC process • Routing density dramatically lower • 3 metals only for routing (vs. 10+), pitch x4 larger". To reach 500 MHz they needed "14 pipe stages". https://old.hotchips.org/hc31/HC31_1.4_UPMEM.FabriceDevaux.v2_1.pdf
- In practice the shipped UPMEM DPUs run at **350 MHz**, below the 500 MHz target. Each DPU natively supports only 32-bit integer add/sub and 8-bit multiply; 32-bit multiply/divide and floating point are emulated. There is **no direct inter-DPU communication**: everything goes through the host. Source: Gómez-Luna et al., "Benchmarking a New Paradigm: An Experimental Analysis of a Real Processing-in-Memory Architecture" (IEEE Access 2022 / arXiv 2105.03814) [full text].
  - Their key takeaways: suitable workloads are "(1) memory-bound workloads with (2) simple or no arithmetic operations" and "require little or no communication across DRAM Processing Units".
  - Fair counterpoint from the same paper: UPMEM beats CPUs on 13 of 16 PrIM benchmarks. https://arxiv.org/pdf/2105.03814
- LLM-relevance [secondary, search snippet only, primary not identified]: Samsung HBM-PIM's minimal MAC units and fixed precision modes do not support modern quantization formats (e.g., k-quants). Treat this as indicative only.

### A2. Thermal limits of logic under (or inside) DRAM stacks
- Eckert, Jayasena, Loh (AMD), "Thermal Feasibility of Die-Stacked Processing in Memory", WoNDP 2014 [full text]. https://www-old.cs.utah.edu/wondp/eckert.pdf
  - Mechanism: "Because the memory lies between the heat sink and the logic die, heat generated from the logic die raises the temperature of the memory. The typical operating temperature range for DRAM is under 85 °C. After the temperature exceeds that threshold, the refresh rate must be doubled for every ~10 °C increase."
  - Counterpoint, and their conclusion: passive cooling supports an 8.5 W PIM logic layer and high-end active cooling about 55 W, so "thermal constraints do not represent an insurmountable hurdle for PIM".
  - The budget is still small compared with GPU/CPU TDPs of hundreds of watts [synthesis]. That is why heavy compute stays on a separate logic die beside the stack, which is the HBM-on-interposer model.
- Nai et al., "CoolPIM: Thermal-Aware Source Throttling for Efficient PIM Instruction Offloading" (IPDPS 2018 [venue unverified]) [full text]. https://ramyadhadidi.github.io/files/nai-coolpim.pdf
  - On a real HMC prototype: "HMC reaches a significantly higher operating temperature, which causes thermal shutdowns with a passive cooling solution". "Even with a commodity-server cooling solution, when in-memory processing is highly utilized, HMC fails to maintain the temperature of the memory dies within the normal operating range."
  - With a passive heat sink, HMC "shuts down when the surface temperature reaches around 85 °C (in-package DRAM temperature is close to 95 °C)". Data is lost and recovery takes tens of seconds.
  - The fix (throttling PIM offload) gives only 1.37–1.4x speedup. **Thermal limits cap how much PIM compute can be used.**
- HBM itself, without PIM: SK hynix's Hot Chips 2026 talk says "as stack height rises toward 20 or more die, thermal management becomes the binding constraint" (ServeTheHome, 23 Aug 2026 [secondary]). https://www.servethehome.com/sk-hynix-hbm-packaging-at-hot-chips-2026/
  - **Round 4 [vendor slide, image viewed]:** SK hynix HC'26 tutorial, slide 11: "Thermal burden : x2.2" across six generation points, while bandwidth rises 1.0→8.0 and stack count 4→16. Slide 12: relative thermal resistance falls 1x (HBM2) → 0.40x (HBM4E, hybrid bonding). Slide 6: HBM4 has 14+% better thermal resistance than HBM3E, at a 775 µm height. Details in D2.
  - Implication [synthesis]: the base die's thermal and power budget is already contested before any compute is added to it.

### A3. Programmability, data layout, coherence, virtual memory
- Ghose, Hsieh, Boroumand, Ausavarungnirun, Mutlu, "Enabling the Adoption of Processing-in-Memory: Challenges, Mechanisms, Future Research Directions" (arXiv 1802.00320, 2018) [abstract only]: PIM logic "does not have low-latency access to common CPU structures that are essential for modern application execution, such as the virtual memory and cache coherence mechanisms".
- Mutlu et al., "A Modern Primer on Processing in Memory" (arXiv 2012.03112, 2020–2025 versions) [full text]. https://arxiv.org/abs/2012.03112
  - Why PIM failed historically: "past efforts were not widely adopted for various reasons, including (1) the difficulty of integrating processing elements with DRAM, (2) the lack of critical memory-related scaling challenges ... and (3) the large effort expended to tolerate data movement bottlenecks (via processor-centric techniques)".
  - Their own conclusion: "the shift from a processor-centric to a memory-centric mindset (and infrastructure) remains the largest adoption challenge for PIM".
  - Their fixes include SimplePIM, which needs 4.4x fewer lines of code than hand-written UPMEM code. This is evidence of how hard the baseline is to program.
- Liu, Schult, Xu, Roscoe (ETH), "Mainframe-Style Channel Controllers for Modern Disaggregated Memory Systems", APSys 2025 [full text]. https://arxiv.org/abs/2506.09758
  - Adoption verdict from systems people, not PIM advocates: "to date, very little of this work has found its way to product, and almost nothing to widespread deployment." They blame "the lack of an OS-centric perspective": no secure multiplexing, virtualization, portable abstractions, or resource management.
  - UPMEM-style designs force programmers to "partition the data for a specific hardware platform".

### A4. Commercial history: HMC died, HBM won, PIM products stay niche
- Micron, "Shift in high-performance memory roadmap", blog post, 1 Aug 2018 [full text]: "as the volume projects that drove HMC success begin to reach maturity ... we are now turning our attention to" GDDR and HBM. https://www.micron.com/about/blog/company/innovations/shift-in-performance-memory-roadmap
  - Micron gives no explicit failure reason.
  - Common explanations — proprietary/single-vendor rather than JEDEC, packet-serial link adding latency and power, logic-layer cost, the ecosystem moving to HBM — are [secondary/unverified]. Sources: Wikipedia; Intellect-Partners and CFF blog posts.
- [synthesis] The more coupled design won, but its passive form won. HMC had a smart logic layer with atomics behind a SerDes link. HBM is a dumb, very wide JEDEC interface with all compute on the host die. The market chose proximity for bandwidth over compute in memory.
- Samsung HBM-PIM (Aquabolt-XL, Hot Chips 33, 2021): demonstrated in AMD MI100 test GPUs and Xilinx boards, not in shipped high-volume accelerators [unverified for absence; no evidence of volume adoption found].
  - Samsung Hot Chips 35 (2023) slides [full text]: HBM-PIM on MI100 gives ">2x" GPT-J speedup and "~2x" system energy efficiency; LPDDR-PIM is quoted at 4.5x performance and -72% energy (simulation). https://www.hc2023.hotchips.org/assets/program/conference/day1/PIM/23_HC35_PIM_PNM_Samsung_final.pdf
- Samsung is still pushing PIM: LPDDR5X-PIM at Hot Chips 2026, aimed at mobile inference (Korea JoongAng Daily, 27 Aug 2026 [secondary]).
  - Samsung and SK hynix are reported to be jointly standardizing LPDDR6-PIM (TrendForce / DigiTimes, Dec 2024 [secondary]).
  - Lack of standardization is commonly cited as a barrier [secondary].
- SK hynix's direction: packaging and thermal work (hybrid bonding, iHBM with >30% lower thermal resistance), not PIM. Its HC'26 tutorial deck never mentions PIM [vendor slides, full text].
  - **Round 4: "SK hynix publicly questions PIM" is now supported** [press, full article]. VP Youngpyo Joo, keynote at Dell Tech Forum 2026, as reported by DIGITIMES on 27 Aug 2026: the industry must "rethink the role and positioning of PIM".
  - The new critique is **capacity**: logic area reduces the bits available, so packaging that shortens DRAM-to-logic wires is "a far more viable industry focus". PIM "may serve niche workloads". Details in D1.
  - The die-cost, power-delivery and leakage objections in that article are DIGITIMES's own framing, not Joo's.

### A5. The strongest single critique of A [synthesis]
Compute placed in or on DRAM is boxed in by two things:
- **Physics**: the DRAM process gives 3x slower transistors and 3 metal layers (A1), and heat under or inside the stack raises refresh rates and causes shutdowns (A2).
- **Ecosystem**: no virtual memory or coherence, vendor-specific data layout, no OS abstraction (A3).

What survived commercially is proximity without compute: HBM, plus logic on a separate logic-process die.

---

## (i-B) Critiques and failures of disaggregation / far memory

### B1. Levis, Lin, Tai (Google), "A Case Against CXL Memory Pooling", HotNets 2023 [full text]
https://conferences.sigcomm.org/hotnets/2023/papers/hotnets23_levis.pdf, DOI 10.1145/3626111.3628195

The thesis is three problems: **cost, complexity, utility**. "As long as these three properties hold, CXL memory pools are unlikely to be a useful technology for datacenter or cloud systems."

**Cost.** Pooling "requires a completely parallel network infrastructure to Ethernet".
- They use a $38,500 32-port 200 GbE switch (Mellanox MSN3700) as a proxy for the pool device, and DDR5 at about $3/GB.
- Break-even: the pool must save 12.6 TB of RAM. With Pond's optimistic 9% saving, the servers need 140 TB in aggregate (127 TB after Pond). That is about 4 TB per server in a 32-node pool, or more than 10 GB per Genoa vCPU, "more than high-RAM VMs provide".
- Figure 2: "Even if pool RAM is completely free, for a standard 4GB/core memory shape, the pool must be 24 nodes to break even" with the switch cost alone.
- Their conclusion: "you are better off just buying less RAM."
- The estimate excludes cabling, NICs, rack space, energy and opex.

**DIMM granularity.** You cannot cut server RAM by 7–9%. A Genoa server with 12 channels comes in 750 GB, 1.15 TB or 1.5 TB. Servers must therefore cut 25%, and the pool recovers 7–9% of that.

**Latency and complexity.**
- Local DRAM is 120–140 ns. Pond assumed CXL adds 70–90 ns. Measured CXL adds about 140 ns (2x local, versus 1.5x for remote NUMA), so roughly 140 → 280 ns, which is ">500 instructions" of stall. That figure is for a direct-attached device; a switched pool adds more.
- Good throughput needs ~8 kB bulk copies (DSA copies reach about 80% of DRAM-to-DRAM throughput). CXL then becomes "a far memory cache" that needs explicit copies and pervasive software changes, and the copies eat into the savings.
- Worked example: a max-over-array loop would need more than 26 cache-line prefetches in flight to hide 280 ns.

**Utility.**
- Using optimal vector bin-packing on the 2019 Google trace (Cell A, production priority) and the 2020 Azure packing trace, pooling across up to 16 machines gives **no utilization gain at actual VM sizes**.
- Pooling helps only if VMs grow **32x (Google)** or **8x (Azure)**. Google shows at most 1% gain even at 16x and under 5% at 32x.
- Their modelling of a pool as one large machine is a generous upper bound.

**Framing.** "Disaggregation ... was first proposed for hard disk drive storage, where seek times of milliseconds outweigh any additional system or network latency. Memory, however, is at the opposite of this spectrum. Architectures move memory closer to compute because locality is key to performance. GPUs, TPUs, and IPUs all have their own memory." Also: "a 6 foot cable adds 12ns of propagation delay in each direction."

**Concession.** CXL is valuable for NICs and GPUs, and pools "could play a role as far memory RAMdisks".

**Caveats for the reader** [synthesis]:
- The cost model uses an Ethernet switch as a proxy, at retail (MSRP) prices.
- The paper predates measured multi-headed devices.
- The traces are 2019/2020.
- It argues against pooling, not against CXL expansion.

### B2. Verifying Pond's savings (Li et al., ASPLOS 2023) [full text]
https://pages.cs.wisc.edu/~markhill/papers/asplos2023_pond.pdf

- Headline: "Pond reduces DRAM costs by 7% with performance within 1-5% of same-NUMA-node VM allocations."
- §6 detail: Pond **reduces DRAM requirements by 9% and 7% under 182% and 222% latency increases respectively**. A static allocation saves 3%. The pool spans 16 sockets.
- **Levis's "7–9%" is therefore accurate.**
- Stranding: median 6%, rising above 10% at about 85% core allocation. At the 95th percentile it reaches 25%, with outliers at 30%. The range is "3-27% of stranded memory in production at the 95th percentile".
- Untouched memory: ~50% of VMs touch less than 50% of their rented memory.
- Ideal pool savings with a fixed 50% pool: 32 sockets save 12% and 64 sockets save 13%, so returns diminish.
- Latency estimates: 8–16 socket pool adds 70–90 ns, and rack-scale adds more than 180 ns.
- Workload sensitivity (158 workloads, all memory placed in the pool):
  - At +182% latency: 26% of workloads see under 1% slowdown, a further 17% see under 5%, and **21% see over 25%**.
  - At +222% latency: **more than 37% see over 25%**. GAPBS graph workloads are the worst.
  - Azure production workloads: half are hit by 10–28%.
  - This is the performance-cliff evidence. Pond needs an ML predictor to avoid it.
- DRAM is cited as 50% of Azure server cost (40% of rack cost at Meta).
- "Azure currently does not pool memory" (as of 2023).

### B3. Microsoft's own later position: expansion yes, pooling only via cheap topologies
**Berger, Kumar, Vuppalapati, Douglas, Sathre, Robinson, Tandon, Hill, "CXL in Cloud Practice: Practical Lessons for Incrementally Scaling Deployment", IEEE Trans. Computers 75(4), 2026** [full text, author version]. https://pages.cs.wisc.edu/~markhill/papers/ieeetoc2026_cxl_practice.pdf
- "The tangible use case for CXL is available today: memory expansion."
- Rationale: socket performance scales with chip area (n²) while DDR scales with perimeter. Capacity per unit performance has "dropped by nearly 60–70%" in 10 years. Current AMD/Intel parts offer only 27% / 39% of their former capacity per unit of performance.
- "The unique value of CXL lies in memory pooling and accelerator support ... at-scale use still requires refinement."
- Recommended pathway: "solidify robust expansion first, then prototype small-scale pooling, and only then progress toward selective memory sharing".
- Latency cliff: median slowdowns are about 10–18% for the best devices, about 22% at 275 ns, and **about 45% at 390 ns** with a long tail.
- They rebut the "CXL is slow" critique by showing latency variability comes from the CPU and DRAM stack, not the CXL protocol.
- They call NVLink/NVSwitch complementary: CPU→GPU-memory paths are "on the order of ~800 ns"; "UALink and Ultra Ethernet are likely to incur even higher latency".

**Zhong, Kazhamiaka, Zardoshti, Teng, Fonseca, Hill, Berger, "Octopus: Enhancing CXL Memory Pods via Sparse Topology", NSDI 2026** [full text]. https://www.microsoft.com/en-us/research/wp-content/uploads/2026/04/nsdi2026_cxl_octopus.pdf
- Partially concedes Levis's cost point: "CXL switches increase latency and CapEx to the point where switch costs can outweigh the pooling savings"; "CXL switches always cost more, even after factoring in their pooling savings."
- A switch "adds at least 220 ns". Local DDR5 on Xeon 6 is ~115 ns; a good expander is 200–300 ns; a 2-port multi-ported device (MPD) measured 267 ns. A switch "would otherwise increase the slowdown of 25% of applications by 3x".
- Octopus's sparse MPD topology: net server cost savings of **3–5.4%** (simulation). The 5.4% assumes expansion is already deployed.
- "CXL-based memory expansion is already deployed in production in Azure."

**Deployment fact.** Azure M-series preview with Astera Leo CXL controllers, announced Nov 2025, is "the industry's first announced deployment of CXL memory expansion" [secondary: press releases via Investing.com/Yahoo; primary Azure blog cited in Berger et al.]. Leo supports up to 2 TB per controller and ">1.5x" capacity [secondary].

**Answer to "did hyperscalers deploy pooling or expansion?"**
- Expansion: deployed (Azure).
- Pooling: Alibaba claims a switch-based production deployment for PolarDB. "Unlocking the Potential of CXL for Disaggregated Memory in Cloud-Native Databases", SIGMOD 2025 Best Industrial Paper [secondary: Alibaba blog/docs; paper not read]. It reports up to 2.1x over RDMA-based pooling, single-node memory up to 8 TB, and 40x faster crash recovery. This is database-specific pooling, not VM pooling.
- Google: published the case against, and its production far memory is compressed (zswap-based "software-defined far memory", ASPLOS'19; Duraisamy et al. ASPLOS'23 tiering) [secondary for details].
- Meta: TPP tiering (ASPLOS'23) and co-authored the OCP "Hyperscale Tiered Memory Expander" spec (Chauhan, Petersen, Morris, Glisse, 2023, per Octopus refs). That is expansion and tiering, not pooling [secondary].

### B4. Measured CXL latency, tail latency, cliffs
- Sun et al. (UIUC/Intel), "Demystifying CXL Memory with Genuine CXL-Ready Systems and Devices", MICRO 2023 [full text]. https://arxiv.org/abs/2303.15375
  - CXL access is 2–4x DRAM latency, from hundreds of ns up to about 1 µs depending on the device. Emulating CXL with remote NUMA misleads.
  - μs-scale apps (KV stores / Redis): placing pages on CXL raises **tail latency by 10–82%**. ms-scale microservices are barely affected.
- Pond: 21–37% of workloads slow down by more than 25% (B2). Berger et al.: median ~45% at 390 ns (B3). Octopus: application slowdowns "significantly increase for devices at around 400 ns".
- RDMA far memory, the older generation:
  - Gao et al., "Network Requirements for Resource Disaggregation", OSDI 2016 [full text]: "Network latency in the range of 3 − 5µs is needed", with 40–100 Gbps. Endpoint software is ~66% of inter-rack and ~81% of intra-rack latency. This assumes 25% of memory stays local, and the threshold is about 5% degradation.
  - Teleport (SIGMOD'22) [full text]: unmodified MonetDB on LegoOS with 10% local memory is **5.4x slower on average and up to 52.4x** (performance cliffs).
  - Clio (ASPLOS'22) [full text]: RDMA has scalability and tail problems; Clio's hardware memory node achieves 2.5 µs median / 3.2 µs p99 and "orders of magnitude lower tail latency than RDMA".
- CXL Consortium chair Jim Pappas on pooling (The Next Platform, 23 Nov 2021) [secondary]: "With memory, it runs so fast that there is no place to hide the latency."

### B5. Blast radius and failure domains
- Maruf & Chowdhury, "Memory Disaggregation: Advances and Open Challenges", SIGOPS OSR 2023 / arXiv 2305.03943 [full text]. "Resilience to Expanded Failure Domains": "Applications relying on remote memory become susceptible to new failure scenarios such as independent and correlated failures of remote machines, evictions from and corruptions of remote memory, and network partitions. They also suffer from stragglers ... These uncertainties can lead to catastrophic failures and SLO violations."
- The fault-tolerance tax:
  - Hydra (FAST'22) uses erasure coding to get 1.6x lower memory overhead than replication [abstract only].
  - Carbink (Google, OSDI'22) [full text abstract section]: 29% lower tail latency and 48% higher performance than Hydra, "with at most 35% higher memory usage".
  - [synthesis] Fault tolerance spends part of the capacity that pooling was supposed to save.
- Berger et al. 2026 treat failure modes, link bit error rates above spec, and multi-tenant interference as operational issues. They find "system slack absorbs the resulting replay tax".

### B6. Industry graveyard
- **HPE The Machine** (Wikipedia, cross-checked against press) [secondary]:
  - Built around memristor NVM that never arrived. The 2016–17 prototype used 160 TB of battery-backed DRAM across 40 nodes with a photonic fabric and drew 24–36 kW.
  - Late 2016: Labs said the aim was "to demonstrate progress, not develop products". Martin Fink retired in 2016 [date per Wikipedia; unverified].
  - The protocol became Gen-Z.
- **Gen-Z**: in Nov 2021 signed a letter of intent to transfer its specs to the CXL Consortium; began dissolving in Jan 2022 [secondary: DCD, Next Platform, Blocks & Files]. The groups had about 80% overlapping membership (Next Platform).
- **OpenCAPI and OMI**: transferred to CXL on 1 Aug 2022 [secondary: GlobeNewswire press release, HPCwire].
  - OMI is a serial near-memory interface. Its death is a data point that serializing the DDR interface was not adopted in favour of DDR5/HBM, until CXL expansion [synthesis].
- **Intel Rack Scale Design**: last release v2.4 in April 2019; repo archived on 5 Aug 2022 with all development ceased (github.com/intel/intelRSD) [secondary]. RSD composed compute, storage and network. Memory disaggregation was aspirational [unverified].
- [synthesis] The disaggregation track has a longer graveyard than the PIM track. What survived is the least-disruptive form: CXL Type-3 expansion, which is local, single-host and needs no switch.

### B7. The strongest single critique of B [synthesis]
**At memory latencies, distance is a tax that cannot be hidden.**
- About 2x local latency already applies for direct-attached CXL, and a switch adds at least 220 ns more.
- That latency creates workload-dependent cliffs (21–37% of workloads lose more than 25%) and forces software rewrites.
- The economic case for pooling is thin. Savings are 7–9% of DRAM (Pond) or 3–5.4% net of server cost (Octopus, the best case). Switches erase the savings, and Levis finds no stranding gain at real VM sizes.

---

## (ii) Convergence and combination

### C1. Disaggregation creates the need for near-memory compute
- Miller, Neville-Neil, Benetopoulos, Mehra, Bittman, "Pointers in Far Memory", ACM Queue 21(3), May–June 2023 / CACM [full text via NSF PAR]. https://par.nsf.gov/servlets/purl/10493455
  - "offloading computations with many dependent accesses to a near-memory processor is already being seen as a way to keep the latency of memory out of the denominator of application throughput. **With far memory, this will be a required optimization.**"
  - Far-memory latencies are "150ns to more than 300ns".
  - Catch: "that near-memory processor is itself outside the translation context of the parent process ... Pointers therefore must make sense everywhere." This is exactly the virtual-memory problem from A3, now in a disaggregated setting.
- Liu et al. (ETH), APSys 2025 [full text]: disaggregated or far memory, often via CXL, "has given new impetus to this idea [NDP], since far memory incurs significantly higher access latency and delivers lower throughput than local DRAM". They propose memory channel controllers that use CXL coherence for fine-grained offload.
- Clio (UCSD, ASPLOS'22) [full text abstract]: rejects both extremes, server-based memory nodes and raw memory with no processing. "We seek a sweet spot in the middle by proposing a hardware-based memory disaggregation solution that has **the right amount of processing power at memory nodes**." It is 1.1–3.4x more energy-efficient than CPU- or SmartNIC-based memory nodes.
- Amaro et al. (VMware), "Logical Memory Pools", HotNets 2023 [full text abstract]: carve the pool out of each server's local DRAM instead of a separate box. Benefits include "lower cost, **support for near-memory computing without extra hardware**". This is a direct counter-design to Levis's cost critique.
- Maruf & Chowdhury 2023 [full text]: remote-memory runtimes "should track the availability of compute cores and efficiently perform near-memory computation".

### C2. Research systems: compute pushed into far memory
| System | Venue | Where compute sits | Key result | Read |
|---|---|---|---|---|
| Teleport (Zhang et al., Penn/Rice) | SIGMOD'22 | ships whole function calls to the memory pool | Unmodified disaggregated execution is 5.4x avg / 52.4x worst slower. Pushing down simple operators gives "an order of magnitude" speedup over LegoOS, up to 11x in one case | full text |
| Clio (Guo et al., UCSD) | ASPLOS'22 | FPGA memory node with VM, network and offload | 100 Gbps; 2.5/3.2 µs p50/p99 | full text |
| PULSE (Tang, Lee, Bhattacharjee, Khandelwal, Yale) | ASPLOS'25 | SmartNIC/FPGA pointer-traversal accelerator at memory nodes, plus programmable switch routing for distributed traversals | vs disaggregated caching: 9–34x lower latency, 28–171x higher throughput, 4.5–5x less energy than RPC. CXL simulation (300 ns): cuts CXL slowdown 3–5x (4-node) and 4.2–5.2x (1-node) | full text |
| CXL-ANNS (Jang et al., KAIST/Panmnesia) | USENIX ATC'23 | disaggregates DRAM via CXL, then lets CXL-network components search in parallel | 111.1x QPS, 93.3% lower latency vs prior billion-scale ANNS; beats an oracle DRAM-only system by 68.0% latency / 3.8x throughput | full text |
| BEACON (Huangfu, Malladi, Chang, Xie; UCSB/Samsung) | MICRO'22 | fixed-function PEs on the CXL-DIMM PCB (BEACON-D, 128 PEs/DIMM) or in the CXL switch (BEACON-S, 256 PEs); no DRAM-die changes | Simulated (Ramulator, 28 nm PEs). 4.70x (D) / 4.13x (S) vs the best DIMM NDP designs. Energy efficiency only 85–91% of MEDAL's. Communication is 52–61% of energy before optimizations | full text (round 4) |
| StRoM (Sidler et al., ETH) | EuroSys'20 | RDMA NIC kernels operating on remote memory | not retrieved [unverified] | — |

### C3. Products: compute inside CXL memory devices
- **Samsung CXL-PNM** (Hot Chips 35, 2023 [full text slides]): two variants, PNM in the CXL controller or PNM on device memory. A "512GB" card with "1.1TB/s" internal bandwidth. Numbers are in the Mutlu Primer.
  - HPCA 2024 LPDDR-based CXL-PNM, 8 devices vs 8x A100-40GB: 23% lower latency, 31% higher throughput, 2.8x energy efficiency, 30% lower hardware cost [full text, round 4]. Caveats, detailed in D3:
    - The numbers are **simulated** (7 nm parameters; FPGA prototype for function only; ASIC in development).
    - The 30% cost figure uses an **assumed $7,000/device vs $10,000/A100**, device-only, excluding host CPU/DRAM/SSD.
    - There is **no switch** in the configuration.
    - A single device is 10.8% slower than one A100 on OPT-13B.
    - The card draws about 150 W, of which the controller is about 90 W.
- **SK hynix CMS (Computational Memory Solution)**: OCP 2022; 4x128 GB DDR plus an SoC for KNN, filtering and load balancing over CXL. CMS 2.0 (OCP 2023) was applied to SK Telecom's Lightning DB spatial analytics [secondary: SK hynix newsroom via Yole/Tom's].
- **Marvell Structera A** (announced 30 Jul 2024; sampling 4Q24) [secondary: Marvell PR via STH/Futurum]: a CXL 2.0 near-memory accelerator with **16 Arm Neoverse V2 cores at 3.2 GHz in 5 nm logic**, 4x DDR5-6400 (~200 GB/s), inline LZ4 compression and AES-XTS, under 100 W, aimed at DLRM.
  - [synthesis, important] A CXL memory controller is a logic-process die. Putting compute there **sidesteps the DRAM-process critique (A1)** and the heat-under-DRAM critique (A2), because the controller sits beside the DIMMs, not under them.
  - The same expensive controller that Levis counts as pure cost (B1) becomes the host for compute. That partly answers the cost critique, if offload value exceeds controller cost [synthesis].
- **CXL memory rack for LLM KV cache**: HyMCache (Jang, Song, Kim, Noh, Kim; arXiv 2607.18141, Jul–Aug 2026) [abstract only]. Authors are probably SK hynix [affiliation unverified].
  - CXL-hybrid memory (small device DRAM in front of SSD-backed capacity) used as a rack-scale KV tier.
  - 3.0x over local LMCache. About 30% slower than a 1 TB DRAM Mooncake pool but uses 16x less DRAM.

### C4. Production systems using both ends at once (tight HBM + coherent far tier)
- **NVIDIA GB200 NVL72** (nvidia.com spec table [full text]):
  - 72 Blackwell GPUs and 36 Grace CPUs.
  - **13.4 TB HBM3E at 576 TB/s, plus 17 TB LPDDR5X at 14 TB/s**, about 30.4 TB of "fast memory" in total. NVLink is 130 TB/s.
  - HBM is the near tier, roughly 41x more bandwidth per rack than LPDDR. LPDDR attached over coherent NVLink-C2C is a capacity tier one hop away. NVLink across 72 GPUs is a memory-semantic scale-up domain, effectively disaggregation at GPU scale [synthesis].
- **NVIDIA GH200**: NVLink-C2C at 900 GB/s coherent, "7x" PCIe Gen5; 480 GB LPDDR5X plus 96 GB HBM3 or 144 GB HBM3e [secondary: NVIDIA page via Lambda/Spheron].
- **AMD MI300A**: 24 Zen 4 cores and 228 CDNA3 CUs sharing 128 GB of unified, coherent HBM3 at 5.3 TB/s [secondary: AMD datasheet via resellers]. This is the pure coupling extreme: no far tier on-package. It went to HPC (El Capitan), where the capacity limit is acceptable [synthesis].
- **NVIDIA Rubin CPX** (press release, Sep 2025 [secondary]):
  - 128 GB GDDR7, not HBM, 30 PF NVFP4, for the compute-bound prefill/context phase. HBM-equipped Rubin GPUs handle decode.
  - Vera Rubin NVL144 CPX rack: "100TB of fast memory and 1.7 petabytes per second".
  - [synthesis] Even NVIDIA disaggregates the phases and backs off tight HBM coupling where bandwidth is not the bottleneck.
- **NVIDIA Inference Context Memory Storage Platform**, BlueField-4 (CES 2026, ships 2H26) [secondary]: a standard three-tier KV cache from GPU HBM to CPU DRAM to NVMe, with a DPU doing placement. This is a smart controller at the far tier, the C3 pattern again.
- **Mooncake** (Moonshot AI/Kimi; FAST 2025 Best Paper) [secondary/abstract]:
  - Splits prefill and decode clusters. Pools the cluster's CPU DRAM, SSD and RDMA into a petabyte-scale disaggregated KV cache over RDMA up to 8x400 Gbps.
  - Up to 525% throughput in long-context scenarios; 75% more requests handled.
  - This is RDMA far memory that pays off because KV blocks are large, read-mostly and predictable. That matches Levis's own condition that bulk transfers work and fine-grained loads don't [synthesis].

### C5. Does the literature frame A and B as complementary?
- **Explicitly, yes, but in scattered position pieces rather than one framework.**
  - Miller et al. 2023: near-memory compute is "required" with far memory.
  - Liu et al. 2025: far memory gives NDP "new impetus".
  - Clio: "the right amount of processing power at memory nodes".
  - Amaro et al. 2023: logical pools give near-memory computing for free.
  - Maruf & Chowdhury 2023: runtimes should do near-memory computation.
  - Berger et al. 2026: CXL and NVLink are complementary.
  - The Mutlu Primer counts CXL-PNM and CMS as PIM instances. Mutlu et al.'s "Memory-Centric Computing" (arXiv 2412.19275) defines the goal as compute "in and near all places where data is generated and stored". That is location-agnostic: it covers far memory too.
- **The skeptic (Levis) frames them as opposites**: "Architectures move memory closer to compute because locality is key to performance." Levis treats GPU/TPU HBM as evidence against pooling.
- I did not find a canonical paper titled along the lines of "disaggregated memory meets near-data processing" that unifies both as one design axis [searched; not found]. Treat that framing as a gap or opportunity [synthesis].

### C6. The synthesis the evidence supports [synthesis]
1. **Each direction succeeded only in its least-disruptive form.**
   - A: HBM (proximity for bandwidth, compute stays on a logic die) beat HMC, and PIM remains niche.
   - B: CXL Type-3 expansion (capacity, single host, no switch) is deployed. Pooling and sharing are still "prototype" (Microsoft's own words) or narrow (Alibaba PolarDB).
   - The failures in both share a root: they required programming-model, OS or ecosystem change (A3; B1's "rewriting software"; Liu et al.'s OS-abstraction argument).
2. **The two critiques are mirror images.**
   - A is limited by heat and process when logic sits in DRAM.
   - B is limited by latency and cost when DRAM sits far from logic.
   - The convergent designs put the right amount of logic, in a logic process, next to far DRAM (CXL-PNM, CMS, Structera A, Clio, PULSE). That relieves both critiques at once: compute avoids the DRAM process, and latency-bound pointer chasing never crosses the link.
3. **Workload shape decides.**
   - Bandwidth-bound, latency-critical inner loops (decode attention/GEMV) go to the HBM side.
   - Capacity-bound, bulk, read-mostly or predictable data (KV cache reuse, cold pages, DB buffer pools) goes to the far tier, using large transfers or offloaded operators.
   - Rack-scale AI systems (GB200, ICMS, Mooncake) run both at once. This is the most concrete answer to why both are attractive.

---

## Source list (URLs)
- Levis, Lin, Tai, HotNets'23: https://conferences.sigcomm.org/hotnets/2023/papers/hotnets23_levis.pdf
- Pond, ASPLOS'23: https://pages.cs.wisc.edu/~markhill/papers/asplos2023_pond.pdf
- Berger et al., IEEE TC 2026: https://pages.cs.wisc.edu/~markhill/papers/ieeetoc2026_cxl_practice.pdf
- Octopus, NSDI'26: https://www.microsoft.com/en-us/research/wp-content/uploads/2026/04/nsdi2026_cxl_octopus.pdf
- My CXL Pool Obviates Your PCIe Switch (Zhong et al., Microsoft, HotOS'25 [venue unverified]): https://arxiv.org/abs/2503.23611. It claims pools "already have positive return on investment". This contrasts with Octopus's switch finding and is consistent only for switchless MPD pools.
- Sun et al., MICRO'23: https://arxiv.org/abs/2303.15375
- Gao et al., OSDI'16: https://www.usenix.org/system/files/conference/osdi16/osdi16-gao.pdf
- Maruf & Chowdhury 2023: https://arxiv.org/abs/2305.03943
- Carbink, OSDI'22: https://www.usenix.org/system/files/osdi22-zhou-yang.pdf ; Hydra, FAST'22: https://www.usenix.org/system/files/fast22-lee.pdf
- Mutlu et al., Modern Primer: https://arxiv.org/abs/2012.03112 ; Memory-Centric Computing: https://arxiv.org/abs/2412.19275
- Ghose et al. 2018: https://arxiv.org/abs/1802.00320
- Gómez-Luna et al. (PrIM): https://arxiv.org/abs/2105.03814
- UPMEM Hot Chips 31: https://old.hotchips.org/hc31/HC31_1.4_UPMEM.FabriceDevaux.v2_1.pdf
- Eckert et al., WoNDP'14: https://www-old.cs.utah.edu/wondp/eckert.pdf
- CoolPIM: https://ramyadhadidi.github.io/files/nai-coolpim.pdf
- Samsung Hot Chips 35 PIM/PNM: https://www.hc2023.hotchips.org/assets/program/conference/day1/PIM/23_HC35_PIM_PNM_Samsung_final.pdf
- Micron HMC→HBM blog: https://www.micron.com/about/blog/company/innovations/shift-in-performance-memory-roadmap
- Liu et al., APSys'25: https://arxiv.org/abs/2506.09758
- Miller et al., Pointers in Far Memory: https://par.nsf.gov/servlets/purl/10493455
- Teleport: https://www.cis.upenn.edu/~sga001/papers/teleport-sigmod22.pdf
- Clio: https://arxiv.org/abs/2108.03492
- PULSE: https://arxiv.org/abs/2305.02388
- CXL-ANNS: https://www.usenix.org/system/files/atc23-jang.pdf
- Logical Memory Pools: https://conferences.sigcomm.org/hotnets/2023/papers/hotnets23_amaro.pdf
- HyMCache: https://arxiv.org/abs/2607.18141
- GB200 NVL72: https://www.nvidia.com/en-us/data-center/gb200-nvl72/
- Rubin CPX: https://nvidianews.nvidia.com/news/nvidia-unveils-rubin-cpx-a-new-class-of-gpu-designed-for-massive-context-inference
- NVIDIA ICMS / BlueField-4: https://blocksandfiles.com/2026/01/06/nvidia-standardizes-gpu-cluster-kv-cache-offload-to-nvme-ssds/
- Mooncake, FAST'25: https://www.usenix.org/system/files/fast25-qin.pdf
- Marvell Structera: https://www.servethehome.com/this-cxl-memory-controller-has-16-arm-cores-marvell-structera-a/
- SK hynix CMS: https://news.skhynix.com/sk-hynix-introduces-industrys-first-cxl-based-cms-at-the-ocp-global-summit/
- Gen-Z→CXL: https://www.nextplatform.com/2021/11/23/finally-a-coherent-interconnect-strategy-cxl-absorbs-gen-z/
- OpenCAPI→CXL: https://www.hpcwire.com/2022/08/01/opencapi-to-be-folded-into-cxl/
- The Machine: https://en.wikipedia.org/wiki/The_Machine_(computer_architecture)
- Intel RSD archive: https://github.com/intel/intelRSD
- Azure CXL (Astera Leo): https://finance.yahoo.com/news/why-astera-leo-deployment-azure-160812560.html
- Alibaba PolarDB CXL: https://www.alibabacloud.com/blog/602713
