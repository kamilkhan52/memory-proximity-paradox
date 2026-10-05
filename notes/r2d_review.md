# R2d: Adversarial review of SYNTHESIS_DRAFT v0.1

Reviewer: R2d sub-agent, 2026-10-03. Stance: a tough ISCA/ASPLOS PC member.
Inputs: SYNTHESIS_DRAFT.md v0.1, notes q1–q5, r2a, RESEARCH_LOG.md. One extra primary check: I fetched Mahar et al. arXiv 2303.08396 in full (pdftotext) to check the "<10% of capacity" claim and the bandwidth-tiering experiment.
Limits: the web-search budget for this session ran out on my first query, so I could not check any outside work online. Items marked **[reviewer knowledge, unverified]** come from my own background and need a source before they go into the draft. Items that need a full text we don't have are marked **FULL TEXT NEEDED**.

---

## Verdict

**Major revision.** The draft is well researched, and most of its numbers trace to the notes. The main gaps are not in the facts but in the **core thesis as worded in §0/§7**. That wording is stronger than the evidence, and in three places the project's own notes contradict it:

1. Arithmetic intensity (FLOP per byte moved) is treated as if it were access density (GB/s per GB stored).
2. The claim that "the DDR bus bundles at a fixed ratio and A/B unbundle" is contradicted by the draft's own table. On that table, deployed CXL sits at the *same* access density as capacity-maxed DDR.
3. The claim that "cheaper bytes can only be added off the DDR bus" is contradicted by Google's zswap and Optane deployments, which are the largest far-memory deployments in the notes.

A defensible thesis survives: **a two-dimensional cost frontier (supplied GB/s per GB versus $/GB, plus a latency-tolerance constraint) on which the DDR bus covers only a narrow, pin-limited segment.** It needs restating, with the ambitious forms of each direction (PIM, pooling) explained separately from the deployed forms (passive HBM, single-host CXL expansion). Several alternative explanations (different markets, memory vendors trying to escape commodity pricing, separate research communities) are partly right. The draft should absorb them, not ignore them.

---

## FATAL (must fix before the thesis is defensible)

### F1. Arithmetic intensity is conflated with access density (§0 item 1, §7)
- Draft: "Low-intensity kernels (LLM decode ~1 FLOP/B, embedding gathers ~0.25 FLOP/B) need **more bandwidth per byte stored**."
- FLOP/B is FLOPs per byte *moved*. A low value means "needs more bandwidth per FLOP". It says nothing about bandwidth per byte *stored*.
- The draft's own §3 gives the counter-example. MTrainS (Q4 §3.1, verbatim): "the majority of the larger capacity embedding tables have a relatively **low bandwidth requirement**". Embedding gathers have the lowest FLOP/B in the draft (0.25), yet most embedding bytes have *low* access density and belong in the capacity tier (MTrainS uses SCM; Bandana uses NVM).
- Access density and arithmetic intensity are separate axes. Q4 §5.3 defines both (ρ and I) and maps the quadrants correctly: high ρ with low I goes to A; high ρ with high I is absorbed by SRAM; low ρ goes to B. §0 collapses them into one.
- **Fix:** rewrite §0 item 1 as two conditions:
  - A pays when data has **high ρ *and* low I** (it cannot be absorbed on-chip).
  - Low I alone does not decide placement.
  - Use LLM decode attention over live KV as the clean example. Use embeddings as the example of a workload that *splits*: hot small tables are high ρ, big tables are low ρ.

### F2. "Fixed ratio" bundling is contradicted by the draft's own table, and the single axis cannot order the tiers without $/GB (§0, §2)
- §0 says the DDR bus bundles bandwidth and capacity "at a fixed ratio set by the pins per channel". §2's table shows DDR at **0.2–0.8 GB/s per GB**, a 4× range. The bus fixes a bandwidth *ceiling* and a capacity *cap*. It does not fix a ratio.
- **Every tier bundles.** An HBM stack's bandwidth is fixed by its interface and its capacity by stack height and die density (~33–42 GB/s per GB). HBM is if anything *more* rigidly bundled than DDR, because you cannot add capacity without adding stacks and beachfront. A tier does not "unbundle". Only a *system with several tiers* lets you buy bandwidth and capacity independently. The draft should say "the system unbundles by combining tiers with different bundles", not "A and B unbundle".
- **Deployed CXL is not a new point on the access-density axis.** Vistara's production CXL tier (Q2 §1.4, Table V) is 57.6 GB/s for 256 GB = **0.225 GB/s per GB** (0.3 using the 76 GB/s peak) [derived]. The draft's "DDR with 256 GB RDIMMs" row is **0.2**. They are equal. The difference is **$/GB**: 0.13× nominal, or 0.3–0.6× all-in per R2a. The axis that separates B from the baseline is cost per GB at a given density, not density.
- The same issue appears at the other end. GB200's Grace LPDDR5X works out to 14 TB/s ÷ 17 TB ≈ **0.82** [derived from the Q5 C4 spec numbers]. That equals the 64 GB-DIMM DDR row (0.8), so on-package LPDDR is not a new point either.
- **Fix:** restate the unifying object as a **2-D frontier: supplied GB/s per GB (x) versus $/GB (y)**, plus a latency or MLP feasibility constraint. The five-minute rule already has this form: $ of the fast tier versus $ per access/s of the slow tier. The thesis becomes:
  - Each tier offers a point on this frontier.
  - DDR offers a narrow, pin-limited segment of it (~0.2–1 GB/s/GB at ~1× $/GB).
  - A buys points far to the right at high $/GB.
  - B buys points at or below DDR's density at lower $/GB.
  - Skew makes a mix of points cheaper than any single point.
- Drop "single axis … 8 orders of magnitude" from §0. §3 already concedes three dimensions.

### F3. "Cheaper bytes … can only be added off the DDR bus" is false per the notes, and deployed B is not "farther" (§0 item 2, §7; attack g)
- Q2 §2.4 and §4.4, "Far memory mostly isn't disaggregated":
  - Google's deployed far memory is **zswap (compressed DRAM, same node, same DDR bus)**: ~20% of data, 4–5% memory TCO.
  - Google's tier-2 was **Optane DIMMs on the DDR bus** (TMTS: 25% of DRAM, <5% slowdown).
  - These are the two largest far-memory deployments in the notes, and neither leaves the DDR bus.
- The deployed winner the draft names (§4, "Survived as single-host CXL expansion") is a memory slot on PCIe lanes in the same box, ~1 hop and +~120 ns away (Vistara 130 → 250 ns idle). Physically it is about as "far" as a remote NUMA socket.
- So the deployed form of B is **"cheaper or more flexible bytes in the same box"**. Its key properties are media independence (the controller lives on the device, so DDR4 can be reused) and capacity beyond the DIMM-slot cap. Distance is not one of them.
- This *strengthens* the draft's §1 claim that distance is a proxy, but §0 and §7 still frame B as "farther".
- **Fix:**
  - Rewrite §0 item 2 as "…calls for cheaper bytes and better utilization; the deployed means are compression, cheaper or recycled media, and serial attach that decouples media from the CPU's DDR generation".
  - State plainly that the user's B as conceived (disaggregation or pooling) is mostly *not* what is deployed.
  - Add zswap to §4's "Survived as" row for B.

---

## MAJOR

### M1. Separate deployed forms from ambitious forms; the thesis explains only the deployed ones (attack c, plus structure)
- The access-density/cost frontier explains **tiering**: HBM vs DDR vs CXL/compressed vs flash. It does *not* explain:
  - **Pooling (B2).** Its value is statistical multiplexing (sum-of-peaks vs peak-of-sums, Lim ISCA'09) and stranding. That is a demand-variance argument across hosts and is independent of access density. Levis's critique is also about multiplexing gain at real VM sizes.
  - **PIM/NDP (A2).** Its value is the internal-vs-external bandwidth ratio (4–16×), plus offloading dependent accesses. Tesseract's "capacity-proportional bandwidth" *does* fit the axis. Bulk bitwise (Ambit) and RowClone fit only loosely.
  - **Reliability (OOM), failure isolation, lifetime decoupling.**
- The user defined A as PIM/NDP/3D-on-logic. The draft quietly widens A to "the bandwidth tier" (HBM, LPDDR on-package, HB-DIMM, SRAM machines). It then reports that "A survived as passive HBM". That is a rhetorical slide: the part of A the user asked about (compute in or near memory) is the part that has *not* reached volume.
- **Fix:** use a 2×2:
  - A1 = proximity for bandwidth (HBM, 3D, on-package).
  - A2 = compute at memory (PIM/NDP).
  - B1 = cheaper or extended capacity (expansion, tiering, compression).
  - B2 = sharing across hosts (pooling, RDMA far memory).
  - Show which drivers explain each cell. The thesis covers A1 and B1 strongly, A2 partly, and B2 weakly. Then explain why A2 and B2 remain *attractive to researchers* despite thin deployment (see M2, M3).

### M2. Alternative explanation (a): different markets and buyers. This partly lands.
- Most evidence for A comes from **AI accelerators and mobile SoCs**: HBM, LLM decode PIM, d-Matrix, Groq, LPDDR5X-PIM, Boroumand (consumer devices). Most evidence for B comes from **general-purpose CPU cloud fleets**: Pond, TPP, TMTS, Vistara, Azure M-series, Levis. Different buyers face different binding constraints.
- The draft does have evidence that this is not the whole story:
  - DLRM (Meta, both directions).
  - LLM KV offload (B inside AI: Mooncake, NVIDIA ICMS).
  - GB200's HBM + LPDDR.
  - Mahar's general-cloud bandwidth tiering.
  - CXL-PNM and Structera A.
- **Fix:** add a paragraph saying the market split is the *first-order* reason two communities pursue each direction, and that the frontier argument explains *why* the markets split that way. The strongest "both at once" cases are in AI inference and recommendation, the workloads that sit at both ends of the frontier at the same time. Cite GPU scale-up domains (NVL72: 13.4 TB of HBM shared across 72 GPUs over NVLink) as the largest memory-semantic disaggregation in production. That is B *inside* the A market (Q5 C4 already has the numbers).

### M3. Alternative explanation (b): vendor and investor incentives are missing, and they matter for a memory-industry reader
- Both directions give **memory makers a way out of commodity $/bit pricing**: custom HBM base dies, HBM-PIM, LPDDR-PIM, CXL CMM modules, CMS. They also give **controller vendors** (Astera, Marvell, Montage, XCENA) and **startups** (d-Matrix, UPMEM, Panmnesia, UnifabriX, Liqid, Groq) a product category.
- The notes contain this evidence but the draft doesn't use it:
  - Samsung says most customers pursue custom HBM.
  - Marvell's CEO links CXL demand to the shortage (an interested party).
  - Samsung pushes PIM while SK hynix's HC26 talk focuses on packaging.
- Hype-cycle evidence is also in the notes:
  - A: IRAM → HMC/Tesseract → HBM-PIM, each wave with weak baselines (Newton 54× vs 10× ideal; PrIM vs a 4-core Xeon E3).
  - B: The Machine, Intel RSD, Gen-Z, all dead.
- **Fix:** add a short section, "Why both attract investment even where deployment is thin". Cover decommoditization, standards consolidation (CXL absorbed Gen-Z and OpenCAPI, so B now has one ecosystem to bet on), and fertile ground for simulation-based papers. This is complementary to the physics, not a rival thesis. Without it, "attract serious people" is answered only on technical merit, which the deployment record does not fully support.

### M4. Latency matters more than the draft admits (attack d), and the middle is not disappearing
- The draft's latency adder is understated:
  - §1 says "+50–150 ns for direct CXL". That range comes from **spec and emulation estimates** (Pond 70–90 ns; Das Sharma 57 ns).
  - **Measured** adders in the notes are +115–155 ns (Octopus: 230–270 vs 115), +120 ns (Vistara: 250 vs 130 idle), ~+140 ns (Levis), and up to ~+270 ns (Melody: 214–394 absolute vs ~80–120 local).
  - Berger TC'26 reports a median slowdown of ~45% at 390 ns.
  - Use "+~100–270 ns measured (+50–90 ns spec estimates)".
- **The DDR middle still holds most of the capacity in every deployed tiered system** [derived from the notes]:

  | System | Local DDR share of capacity |
  |---|---|
  | Vistara | 768 GB of 1 TB = 75% |
  | TMTS | 75% DRAM |
  | TPP | 2:1 = 67% |
  | Memstrata | 1:1 = 50% |
  | Mahar tiered config | 62.5% on the DDR-like "far" tier |

  §7's "the efficient frontier is a wider hierarchy, **not a better middle**" is contradicted. The correct statement is "a wider hierarchy **around a persistent, latency-critical middle**". MRDIMM (DDR5-12800) is the middle improving, not dying.
- For CPUs, latency-bound, low-MLP data (pointer chasing, B-trees, GAPBS) is exactly what keeps DDR on the bus. "Beyond Hotness" (OSDI'25) and Pond's GAPBS results show this.
- The draft relegates latency to a §3 "refinement". It should be a first-class constraint in §0. It is the reason the ambitious form of B (switched pools: +220 ns or more, Octopus) failed while expansion succeeded.
- **The §1 evidence that "closer is not lower latency" is confounded.** H100→HBM ~300–330 ns is a GPU pointer chase through a throughput-optimized cache hierarchy and NoC. Grace's LPDDR5X >200 ns reflects LPDDR device timing and the Grace mesh.
  - The clean argument is physical: wire flight time is ~ns (Levis: a 6 ft cable adds 12 ns each way). Latency lives in device timing (~50 ns core), controllers, SerDes, and queues.
  - That argument cuts both ways. B's latency cost is not distance either. It is protocol hops: SerDes, controller, and switch.
  - Rewrite §1 around that mechanism, and keep the GPU and Grace numbers as illustrations with the confound stated.

### M5. The energy argument for A is apples-to-oranges and framed at the wrong level (attack f)
- "~20 pJ/bit off-package vs ~4 for HBM vs <1 for hybrid-bonded/on-die" mixes scopes:
  - **~20 pJ/bit.** Horowitz's "DRAM I/O over 20 pJ/bit" is **I/O only**. Dally's 640 pJ per 32b is a full LPDDR read. Keckler 2010 gives 20–30 pJ/bit interface **plus** 8–15 access. The DDR4 22 pJ/bit figure is pending F1 (scope unknown).
  - **HBM2 3.92 pJ/bit** is a **full access**: activation, on-die movement, and I/O.
  - **<1 pJ/bit.**
    - d-Matrix's 0.37 pJ/bit is **I/O power**: 296 W of I/O at 100 TB/s (Q1 §3.3) [secondary].
    - UniIC's 0.66–0.88 pJ/bit is likely interface-only (pending F2).
    - MB-scale SRAM is ~1.6 pJ/bit (Q3), so "<1 for on-die" is cherry-picked from KB-scale SRAM.
  - Like-for-like I/O comparison: HBM2's I/O is 0.3 pJ/bit and d-Matrix's is 0.37, so they are the same. The real gain from DRAM-on-logic would have to come from cutting HBM's 2.24 pJ/bit on-die movement term. That is plausible but **not shown by any number in the notes**.
- **Activation energy does not shrink with distance.** HBM2 spends 1.21 pJ/bit on activation. PIM keeps it. All-bank PIM operation may raise it, because it activates many rows at once [reviewer inference; Kwon ISSCC'21 FULL TEXT NEEDED to check].
- **System level.** At ~4 pJ/bit, an H100's 3.35 TB/s at full bandwidth is ≈107 W of a ~700 W board, about 15% [derived; H100 specs recalled, unverified]. Halving DRAM energy saves ~7% of board power. The *strong* form of the energy argument is O'Connor's: **pJ/bit caps bandwidth under a DRAM power budget** (60 W → ~2 TB/s for HBM2; 4 TB/s would need >120 W). So energy per bit is a bandwidth limiter, not mainly an electricity-bill item. Reframe §0 item 1 and the §4 "Optimizes" row this way.
- Boroumand's 62.7% is a **2018 mobile-SoC** number (Chrome, TF-Mobile, VP9). The notes disagree about its provenance: Q1 says primary-partial, while Q4 says it was verified only against the slides and is pending full text. Labelling it "Strongest evidence" for A in a datacenter-dominated argument is a mismatch. Keep it, label it consumer/mobile, and put O'Connor first.

### M6. The LLM-decode bandwidth case is overstated (batch-1 numbers)
- "LLM decode ~1 FLOP/B" (Yuan et al., Table 1) and "~0.3% of GPU peak" (SK hynix SC23) are **batch-1** figures. The 0.3% comes from a vendor that sells AiM.
- Q4 §2: batched decode GEMMs have intensity ≈ batch size B. Production serving batches heavily (vLLM, Splitwise). What *stays* at ~1 FLOP/B regardless of batch is **decode attention over the per-sequence KV cache**, raised by the GQA group factor. That is why AttAcc and NeuPIMs target attention.
- **Fix:** state the batch caveat. Make "attention over live KV" the anchor example for A, with weight GEMVs as batch-dependent.
- **Skew is not universal.** Dense-model decode reads **all weights every step**, which is maximal uniformity. MoE introduces skew. Dense training touches weights, activations, and optimizer state every step. Q4's "skew is pervasive" covers datacenter *fleet* memory and KV caches, not every byte. Change "universal" to "pervasive in fleet memory, KV caches and embeddings; absent for dense weights, which are why HBM capacity is sized to the model".

### M7. Supply economics (§5.2): the floor estimate cherry-picks inputs, and the causal heading overstates
- **Floor.** $8–12/GB uses the **2027 HBM4 press price ($32–36/GB)** and r = 3–4, where 4 depends on the pending ">4:1" (FT2).
  - With the notes' **HBM3E $10–16/GB** and r = 3, the floor is **~$3–5/GB**, about the 2023 level.
  - R2a's indifference condition also has a **(1 + back-end/test/base-die cost share)** term. The draft drops it. With HBM4 base dies made at TSMC N12/N3, that term is material and lowers the floor.
  - **Fix:** present the floor as P_HBM / [r(1+c)], with a small sensitivity table (HBM3E 2026 vs HBM4 2027; r = 2.5/3/4; c = 0/0.2). Label it as valid only "while HBM is rationed".
- **Circularity.** "At a ~2.5–3:1 trade ratio, HBM took 18%→30% of wafers for 8→13% of bits." The 2.5–2.9 ratio is *computed from* those shares (R2a §1.3), so it cannot also be the premise. Cite Micron's primary "3:1" as the independent figure and the shares as consistent with it. Also note R2a's caveats: end-of-year wafer share vs annual bit share, and 2027 values are forecasts.
- **Heading.** "A creates demand for B (supply economics)" is stronger than R2a's verdict ("amplifies, not causes"). The draft's own caveat says the same. Retitle it: "A raises the price of B's inputs, and the payoff of B's cheapest forms".
- **Dropped qualifications from R2a:**
  - NAND rose 55–60% QoQ in 1Q26, partly because cleanroom space moved to DRAM, so **SSD offload is not spared**.
  - CXL with *new* DDR5 does not escape the price rise.
  - Recycled DDR4 is not free (worth $5–9/GB on resale) and its supply is bounded by server retirements. Google says recycling "remains insufficient".
  - HBM prices rise too (+50–121% for 2027), so A gets dearer as well.
  - Overall: only *recycled or compressed* B gains relative ground. "B" in general does not.
- **Weak evidence for the causal link to CXL adoption:** the Marvell CEO (interested party), Google (press only, adapter not confirmed as CXL), ServeTheHome and Tom's (press), Meta capex (memory *pricing*, not CXL). The Vistara paper does not mention the shortage, and Azure gives no link. The draft should say "two vendor statements and press reports; no hyperscaler primary source ties CXL deployment to the shortage".

### M8. Provenance tags were stripped
- The notes carefully tag [secondary], [vendor claim], [press], [pending]. The draft drops almost all of these inline. Only d-Matrix carries [secondary], and the floor carries [derived]. §9's "Mostly primary sources read in full" is true of the literature, but not of many §0/§4/§5 *numbers*.
- Untagged secondary, press, or pending numbers in the draft:
  - Groq 230 MB / 80 TB/s
  - HBM3E 1.2 TB/s per stack ([unverified] in Q3)
  - hybrid bonding 10⁵/mm² (pending F2)
  - DDR 20 pJ/bit (pending F1)
  - <1 pJ/bit
  - Samsung capacity-extension ports (HC26 [secondary])
  - XCENA MX1 (secondary)
  - Structera A (vendor PR)
  - Azure M-series (press release)
  - DDR5 $7→$14/GB (press)
  - HBM4 price behind the floor (press)
  - Aquabolt-XL figures (vendor slides)
  - Aquabolt-XL 6 GB as a capacity *penalty* (composition pending Kwon ISSCC'21)
  - Boroumand (partial)
- **Fix:** restore inline tags on every number, at least [sec] / [vendor] / [press] / [pending] / [derived]. Given the user's rules this is not cosmetic.

### M9. "Each direction … failed in its ambitious form" overclaims; the "B creates demand for A" evidence is supply push (§4, §5.3)
- **Ambitious forms are niche, not dead:**
  - Ambitious B in production: Mooncake runs an RDMA, petabyte-scale disaggregated KV pool in production at Kimi; NVIDIA ICMS ships a three-tier KV hierarchy; Alibaba PolarDB uses a switch-based CXL pool (pending full text).
  - Ambitious A shipping: LPDDR5X-PIM productization in 2026, GAIA mass production from 2027 [secondary]; UPMEM was bought by Qualcomm.
  - The pattern is "**bulk, read-mostly, predictable data** tolerates far memory (Levis's own condition), and **narrow kernels** tolerate PIM". That is more precise and more useful than "failed".
  - Change "failed" to "has not reached volume outside narrow niches", and name the niches.
- **"B creates demand for A" lists products that are announced or sampling, not deployed:** CXL-PNM, CMS, Structera A, XCENA. That is the same deployment status the draft criticizes for PIM. Say so.
- **Several baselines are cherry-picked:**
  - **CXL-ANNS 111× QPS** is against *prior billion-scale ANNS* (largely SSD-based). The fair comparison in the notes is **3.8× throughput over an oracle DRAM-only system** (Q5 C2). Use that.
  - **Teleport "pushdown recovers ~10×"**: the notes say "an order of magnitude over LegoOS, **up to 11× in one case**". That is a best case, not an average. "Recovers ~10×" from a 5.4× average slowdown also reads wrongly.
  - **PULSE 9–34×** is *latency* against disaggregated caching. Name the metric.
- **The §5.3 synthesis needs a [synthesis] tag and a counter-point.** Compute at a CXL controller sees only DDR-channel bandwidth (Structera A: ~200 GB/s from 4×DDR5 vs 64 GB/s link, ~3×). It does not get bank-level internal bandwidth (4–16×). It escapes PIM's objections by **giving up PIM's main benefit**. Also, Levis's cost model prices a *switch/pool device* (a $38.5k Ethernet-switch proxy), not the expander controller, so "Levis's 'pure cost' controller" misattributes the critique.

### M10. The "most direct evidence" (Mahar/Meta bandwidth tiering) is weaker and different from what the draft implies
I verified this against the full text of arXiv 2303.08396, §5.2.
- **Emulated, not real tiers.** "HB-DIMMs … we model HB-DIMMs using DDR-5 based near memory." "Far Memory [the other socket] represents a CXL memory tier." Bandwidth was set by enabling channels.
- **One workload.** Only the Reader microservice was evaluated "due to … limited and geo-restricted availability" of the server.
- **Assumed cost.** HB-DIMM cost was an "early assessment" of 2× DDR5. The paper's own headline is "13% improvement in performance/cost".
- **Not direction A.** HB-DIMMs are MCR-type DDR-bus DIMMs ("not to be confused with HBM"). The "close" tier here is a better DDR DIMM, i.e. *the middle improving* (see M4).
- **Ratios.** The throughput/cost ratios still don't reproduce from the table (as Q4 already noted).
- **Skew wording.** The paper says "the 90%-tile bandwidth is contributed by less than 10% of the memory capacity" over 30 s, across 9 services. The draft's "serves the P90 of bandwidth demand" reads as a latency-style percentile. Paraphrase as "~90% of bandwidth comes from <10% of capacity (30 s windows, 9 Meta services; preprint)".
- **Fix:** keep it as supporting evidence for *bandwidth tiering in general*. Move it out of the "both A and B" bullet list, or relabel it "bandwidth tiering on the DDR bus + CXL-like tier (emulated)".

### M11. The access-density table mixes non-comparable numbers (attack e). This lands.
- **Unidirectional vs bidirectional.** CXL rows use **per-direction** peak (32/64 GB/s), giving 0.125. DDR rows use half-duplex total. For mixed read/write the CXL figure would be 0.25. State the convention.
- **Peak vs sustained.** Measured CXL is 18–52 GB/s (Q3), and commercial CXL reaches 9.8–80.3% of local DRAM bandwidth (Wang 2405.14209). Sustained HBM and DDR reach roughly 70–90% of peak [reviewer knowledge]. The 0.125 could be 0.07–0.2 in practice.
- **Configuration choices set the numbers:**
  - RDMA "4 TB share" is arbitrary, since BW/GB scales inversely with however much memory sits behind one NIC.
  - The 256 GB DDR row needs 3DS RDIMMs, which carry a $/GB premium (Memstrata).
  - Grace is ~0.82 on the GB200 spec vs 1.1 in the draft.
- **Scope.** SRAM is a scratchpad/cache, not a capacity tier. Including it stretches the span to "8 orders of magnitude". DRAM-class tiers span ~3.5 orders (HBM ~40 to RDMA ~0.01). Q4 says workload *demand* spans ~4 orders. The draft cites the supply span as if it were the demand span.
- **Missing columns.** §8 recommends specifying products by "GB/s per GB, pJ/bit, $/GB", but the table has no $/GB or pJ/bit columns. Add them, with tags, or n/a.
- **§8's "gap" claim fails on the draft's own numbers.** LPDDR on-package (~0.8–1.1), MRDIMM-8800 12-ch with 64 GB (845/768 ≈ 1.1 [derived]) and SOCAMM sit *at* DDR's density, not between DDR (~0.8) and HBM (~35). Only SPHBM4 plausibly lands in the gap. Rewrite §8: these products lower $/GB or pJ/bit at roughly DDR density, and the 0.8–35 gap is largely **empty**. That emptiness is itself a finding: nothing ships between "DIMM" and "HBM" in access density.

### M12. "Only shorter, wider wires" contradicts §3 and ignores the dominant industry levers
- §0 item 1 and §7 say "the only lever is shorter, wider wires". The draft's own §3 says **CXL adds bandwidth** (+24–39%).
- Other levers in the notes: more channels and MRDIMM (Q3: P-core BW/core held flat this way); batching (M6); quantization (fewer bytes per parameter); larger on-chip SRAM. Industry has used all of these more than PIM.
- **Fix:** "The only lever that improves **pJ/bit and BW per mm² together** is shorter, wider wires. Bandwidth alone can also be bought with more pins (channels, CXL lanes), at a cost in package pins and power."

---

## MINOR

1. §0: "Cores per socket grew 8→256 while channels grew 4→16". Q3's own interpretation is that per-channel bandwidth also grew 8×, so **P-core GB/s per core stayed ~flat (4.5–6.5)**. Only dense parts fell (~3). Read alone, the sentence implies bandwidth starvation. For the capacity wall, cite Berger TC'26 instead ("capacity per unit performance dropped ~60–70% in 10 years", Q5 B3), together with 1DPC.
2. §0: "Memory … a third of rack TCO" (also §7). This is 37.1% for Meta Gen5 only, and the series is non-monotonic (21.2% at Gen3). The Azure "50%" traces to a 2020 Next Platform article (Q2 FT item 6). Say "37% at Meta's latest generation".
3. §0: "20–80% of allocated memory is cold or stranded". Stranded memory is *unallocated* memory that can't be rented (median 6%, p95 up to 25%). Cold memory is allocated but idle (Google 32% average, range 1–61%; Meta 55–80% for 4 apps). Production *captures* 20–33%. Split the three numbers.
4. §0: "The optimal memory system is therefore…" "Optimal" is not shown, and "therefore" skips tiering costs (migration; TPP used nearly a whole host core per VM, per Memstrata). Use "a cost-efficient".
5. §0: "increasingly the same product: HBM with capacity-extension ports…" That item is Samsung's aHBM *roadmap* [secondary], not a product. Change "the same product" to "converging in roadmaps and some products".
6. §1: "tails to ~1 µs". Say p99.99–p99.999 (Melody).
7. §2: "Serial links give 4–8× bandwidth per pin". It is 4× per direction vs DDR's total, and ~8× only when counting both directions or using COAXIAL's projection.
8. §2: "The two stable ends … are exactly A and B." The serial end has a long graveyard: FB-DIMM, RDRAM [reviewer knowledge], HMC (serial and stacked), OMI absorbed into CXL (Q3 §6, Q5 B6). CXL Type-3 is the first serial memory attach to stick. DDR has been the stable mainstream for 25 years. Change "unstable middle" to "squeezed middle".
9. §2 trend map: "Parameters grew ~410× per 2 yr". Fitted 2018–22 (Q3 caveat), and frontier dense parameter counts have since flattened into MoE [reviewer knowledge]. Hedge it. Gholami's DRAM line does not say which products it uses (Q3 caveat).
10. §3: "~41 s (NVMe, 2017) and ~5 s on GPU hosts (Zhang 2025)". The two use different page sizes (4 KB vs **512 B**), and Zhang's 5 s assumes GPU+GDDR and **"Storage-Next" high-IOPS SSDs**. It is also a preprint (v2 May 2026). The 34 s CPU+DDR figure at 512 B is the like-for-like number.
11. §3: "push the two ends of the distribution apart". The tier *boundaries* move; the data distribution does not. Reword.
12. §3: "Weights and live KV sit in HBM/PIM". PIM is not deployed in LLM serving; write "HBM (PIM proposed)".
13. §4 B "Strongest evidence": "41% of Azure server embodied carbon". Pair it with GreenSKU's result that CXL-reused DDR4 nets only **+1%** and that only 3% of traces touch the CXL tier (Q2 §1.4). Otherwise it is cherry-picked.
14. §4: "Vistara 0.13× $/GB → up to 25% fewer servers". The 25% is for disaggregated ML inference (recsys parameter servers) and comes from *capacity*, not from the price ratio. 0.13× is nominal; R2a puts the all-in cost at 0.3–0.6×.
15. §4 B "Pays with … bandwidth per GB: Vistara ~10× less BW, +60% latency". The ~10× is **absolute** tier bandwidth (57.6–76 vs 614 GB/s). Per GB it is ~2.7–3.6× [derived]. The +60% is **loaded** latency (372 vs 234 ns). Idle is +92% (250 vs 130 ns).
16. §4: "21–37% of Pond workloads >25% slower fully on CXL". This is at +182%/+222% latency, NUMA-*emulated*. Sun MICRO'23 warns that NUMA emulation misleads.
17. §4: "Octopus 3–5% net". It is 3.0–5.4%, from simulation, and the 5.4% assumes expansion is already deployed.
18. §4 A "Pays with: Aquabolt-XL 6 GB". The 6 vs 8 GB composition is unverified (Kwon ISSCC'21, FULL TEXT NEEDED). Don't present it as a measured penalty yet.
19. §4: "CENT 5.2× tokens/$". CENT is **CXL-attached** GDDR6-PIM, i.e. A and B together. It is better used in §5 than as pure-A evidence.
20. §5.1: "Every step toward compute trades capacity for bandwidth". HBM capacity per GPU rose 80 → 288 GB, and MI300A has 128 GB. The trade holds at **fixed cost and beachfront**. Say that.
21. §5.1: In GB200 the "far" capacity tier (LPDDR5X) sits **on the Grace package**, physically closer to its CPU than any DIMM. This is a nice illustration of "distance is a proxy". Use it.
22. §7 tension list is missing two items:
    - **PCIe-lane competition.** CXL bandwidth expansion needs ~64 Gen5 lanes (Micron/Intel), the same lanes GPUs and NICs use in AI servers.
    - **Rack power and thermal envelope.** Vistara's CXL tier is 30 W per 256 GB, and HBM GPUs dominate rack power.
23. §8: "Expect logic-process controllers … rather than DRAM-die PIM to host near-data compute first". DRAM-die LPDDR5X-PIM is reportedly the first to productize (2026–27, mobile) [secondary]. The analogous "compute follows the far tier" bet in storage (computational storage, SmartSSD) saw lukewarm adoption [reviewer knowledge, unverified], a cautionary precedent. Hedge the prediction.
24. §9: add "PIM and NDP speedups are often against weak baselines" (Newton 54× vs 10× vs ideal; PrIM vs a 4-core Xeon E3; CXL-ANNS vs SSD-based ANNS).

---

## Traceability table (draft text → what the notes say)

| # | Draft text (section) | Notes say | Problem type | Fix |
|---|---|---|---|---|
| 1 | "Off-package DRAM costs ~20 pJ/bit" (§0) | Horowitz: I/O ">20 pJ/bit" (I/O only); Dally 640 pJ/32b full LPDDR read; Keckler 2010: 20–30 interface + 8–15 access; DDR4 22 [pending F1] (Q1, Q3) | scope mismatch (I/O vs full access); pending | "~20 pJ/bit (I/O; full access higher) [pending F1]" |
| 2 | "~4 pJ/bit for HBM" (§0) | HBM2 3.92–3.97 full access incl. ECC; I/O 0.3 (Q3) | full-access figure set against an I/O-only one | state scope |
| 3 | "<1 pJ/bit for hybrid-bonded or on-die" (§0) | 0.66–0.88 [pending F2, scope unknown]; d-Matrix 0.37 = 296 W **I/O** at 100 TB/s [secondary]; SRAM 0.16 (8 KB) to ~1.6 (MB) | I/O vs full; cherry-picked SRAM | "0.4–0.9 pJ/bit interface [sec/pending]; SRAM 0.2–1.6" |
| 4 | "LLM decode ~1 FLOP/B" (§0, §3) | Yuan Table 1, batch 1; batched GEMM ≈ B FLOP/B; attention ~1 per KV head × GQA (Q4 §2) | batch caveat dropped | add batch caveat |
| 5 | "need more bandwidth per byte stored" (§0) | Q4 §5.3: ρ and I are separate axes; MTrainS: big tables low BW | category error | see F1 |
| 6 | "20–80% of allocated memory is cold or stranded" (§0) | stranding median 6%, p95 3–27%; cold 32% (1–61%); idle 55–80% (4 Meta apps); captured 20–33% | category merge; range cherry-pick | split |
| 7 | "<10% of capacity serves the P90 of bandwidth demand" (§0, §3) | "90%-tile bandwidth is contributed by less than 10% of the memory capacity", 30 s windows, 9 services, arXiv preprint (verified full text) | ambiguous paraphrase | "~90% of bandwidth from <10% of capacity" |
| 8 | "spans ~8 orders of magnitude" (§0) | supply side incl. SRAM; Q4: workloads span ~4 orders | supply vs demand span | state both |
| 9 | "can only be added off the DDR bus" (§0) | zswap in-node; TMTS Optane DIMMs; "far memory mostly isn't disaggregated" (Q2 §4.4) | contradicted | see F3 |
| 10 | "+50–150 ns for direct CXL" (§1) | spec/emulation 50–90; measured +115–155 (Octopus), +120 (Vistara), ~+140 (Levis), up to ~+270 (Melody) | estimate vs measured | "+~100–270 measured" |
| 11 | "tails to ~1 µs" (§1) | p99.99–p99.999 (Melody) | percentile missing | add |
| 12 | "H100 → HBM ~300–330 ns" (§1) | <300 GH200, ~330 H100, GPU pointer chase incl. hierarchy | confounded | note confound |
| 13 | Grace LPDDR5X 480 GB / ~512 GB/s → ~1.1 (§2) | Q3: 384–500+ GB/s per CPU; GB200 spec 14 TB/s ÷ 17 TB ≈ 0.82 [derived] | config choice | give 0.8–1.1 |
| 14 | HBM3E 36 GB / ~1.2 TB/s (§2) | Q3: HBM3E per-stack BW [unverified] | untagged | tag |
| 15 | Groq 230 MB / 80 TB/s (§2) | Q1 [secondary] | untagged | tag |
| 16 | CXL Gen5 ×8/×16 → ~0.125 (§2) | per-direction peak; measured 18–52 GB/s; Vistara 57.6/256 = 0.225; Q4 gives 0.05–0.25 | uni/bi; peak vs measured; overlaps DDR | range 0.07–0.25 + convention |
| 17 | RDMA 400 GbE, 4 TB share → ~0.01 (§2) | no source for "4 TB share" | arbitrary config | say it scales with share size |
| 18 | "Serial links give 4–8× bandwidth per pin" (§2) | 4× per direction; ~8× counting both directions or COAXIAL 2025 projection (Q3) | uni/bi | "4× per direction" |
| 19 | "hybrid bonding ~10⁵ connections/mm²" (§2) | 110k/mm² at 3 µm [secondary, pending F2]; d-Matrix uses 36 µm F2F | untagged | tag |
| 20 | "Parameters grew ~410× per 2 yr" (§2) | fitted 2018–22 (Q3 caveat) | caveat dropped | add |
| 21 | "~41 s (NVMe, 2017) and ~5 s on GPU hosts" (§3) | 41 s at 4 KB; Zhang: 34 s → 5 s at **512 B**, GPU+GDDR, "Storage-Next" SSDs, preprint | page-size and assumption mismatch | add conditions |
| 22 | Meta bandwidth tiering 1.47× vs 1.55× (§3) | emulated tiers, 1 workload, assumed 2× cost, HB-DIMM is DDR-bus (Mahar full text) | evidence strength; not A | see M10 |
| 23 | "CXL +24–39% on Xeon 6900P" (§3) | 24% read-only 3:1; 39% 1R:1W 5:2; 64 lanes; naive 1:1 loses (Q2) | OK; add lane cost | add |
| 24 | "62.7% of consumer-device energy" (§4) | Q1 primary-partial vs Q4 slides-only [pending]; mobile 2018 | provenance conflict | tag [pending FT #9]; demote |
| 25 | "Aquabolt-XL >2× perf, >70% less energy" (§4) | HC33 vendor slides, system-level | vendor claim untagged | tag [vendor] |
| 26 | "LLM decode at ~0.3% of GPU peak" (§4) | SK hynix SC23 slide (vendor), batch-1 | vendor, batch-1 | tag, caveat |
| 27 | "Aquabolt-XL 6 GB" as capacity cost (§4) | 6 vs 8 GB composition [unverified, pending Kwon] | pending | tag |
| 28 | "HBM ~3× wafer per bit" (§4) | Micron 3:1 [transcript]; >2× [primary Dec'23]; decomposition 2.1–3.7× [calc] | OK | — |
| 29 | "Vistara recycled DDR4 at 0.13× $/GB → up to 25% fewer servers" (§4) | 0.13× nominal (Table V); 25% for disaggregated ML inference from capacity; all-in 0.3–0.6× (R2a) | implied causal link; nominal vs all-in | split; add R2a |
| 30 | "Vistara ~10× less BW, +60% latency" under "bandwidth per GB" (§4) | 57.6–76 vs 614 GB/s absolute; per GB ~2.7–3.6×; +60% = loaded (372 vs 234); idle 250 vs 130 = +92% | absolute vs per-GB; loaded vs idle | correct both |
| 31 | "21–37% of Pond workloads >25% slower" (§4) | at +182/+222% latency, NUMA-emulated | emulation caveat | add |
| 32 | "Octopus 3–5% net" (§4) | 3.0–5.4%, simulation, 5.4% assumes expansion present | rounding/conditions | add |
| 33 | "41% of Azure server embodied carbon" (§4) | Memstrata citing GreenSKU; GreenSKU-CXL nets +1% | cherry-pick | pair |
| 34 | "GB200 NVL72: 13.4 TB HBM3E at 576 TB/s plus 17 TB LPDDR5X at 14 TB/s" (§5) | Q5 C4, NVIDIA spec [full text] | OK | — |
| 35 | "Samsung's HBM roadmap adds capacity-extension ports" (§5) | HC26 [secondary], aHBM phase 2 roadmap | untagged; roadmap | tag |
| 36 | "At a ~2.5–3:1 trade ratio, HBM took 18%→~30% … 8→13% of bits" (§5) | ratio derived from these shares; end-of-year wafer vs annual bit; 2027 forecast (R2a) | circular | cite Micron 3:1 independently |
| 37 | "floor … ~$8–12/GB [derived]" (§5) | P_HBM/r with HBM4 2027 $32–36 [press], r 3–4 (4 pending); formula has (1+c) term; HBM3E $10–16 gives $3–5 | input cherry-pick; dropped term | sensitivity table |
| 38 | "DDR5 RDIMMs went ~$7 → $14/GB" (§5) | Counterpoint via EE Times Asia [press], contract tracker | untagged | tag |
| 39 | "SSD offload" as beneficiary (§5) | NAND +55–60% QoQ 1Q26 (R2a Q3) | caveat dropped | add |
| 40 | "Teleport … pushdown recovers ~10×" (§5) | "order of magnitude over LegoOS, up to 11× in one case" | best case presented as typical | "up to 11×" |
| 41 | "PULSE gives 9–34×" (§5) | latency vs disaggregated caching; throughput 28–171× | metric missing | name metric |
| 42 | "CXL-ANNS gives 111× QPS" (§5) | vs prior billion-scale ANNS; vs oracle DRAM-only: 3.8× throughput, −68% latency | baseline cherry-pick | use oracle comparison |
| 43 | "XCENA MX1 (3,072 RISC-V cores … 64 GB/s/dir)" (§5) | HC26 [secondary] | untagged | tag |
| 44 | "Marvell Structera A (16 Neoverse V2…)" (§5) | Marvell PR via STH [secondary]; no deployment evidence | untagged; status | tag; say "sampling" |
| 45 | "turns the controller from Levis's 'pure cost'" (§5) | Levis prices a switch/pool device ($38.5k Ethernet-switch proxy), not an expander controller (Q5 B1) | misattribution | reword |
| 46 | "Cores 8→256 while channels 4→16" (§0) | per-channel BW also 8×; P-core BW/core flat (Q3 §2) | misleading framing | add or replace with Berger TC'26 |
| 47 | "Memory … a third of rack TCO" (§7) | 37.1% Meta Gen5; 50% Azure via a 2020 article | single datapoint | qualify |
| 48 | "Between HBM (~35) and DDR (~0.2–0.8): LPDDR-on-package, MRDIMM, SOCAMM" (§8) | own table: LPDDR ~0.8–1.1; MRDIMM-8800 ≈ 1.1 [derived] | contradicts own numbers | see M11 |

Numbers that trace correctly (no change needed beyond tags): Gholami 3.0/1.6/1.4×; $/bit 33–36% → 12–15%/yr; Meta 15.6 → 37.1%; Chang tRCD −30% vs 16×; O'Connor 3.92 = 1.21 + 2.24 + 0.3; Google 32% at 120 s (1–61%); TPP 55–80%; Pond ~half of VMs <half; Alibaba 10%/77%; Mooncake >50% unused and +75% requests; MTrainS 4–8×; RecNMP 4.2×; Colloid 2.3×; UPMEM 3× slower transistors / 3 metal layers; refresh doubling per ~10 °C over 85 °C; Sun +10–82% tails; Octopus "always cost more"; Levis ~140 TB; Pond 7–9%; TMTS 25% at <5%; trade-ratio shares 18 → 30% / 8 → 13% (TrendForce); Miller "required optimization".

---

## Thesis attacks scorecard

| Attack | Lands? | Notes |
|---|---|---|
| (a) Different markets and buyers | **Partly** | First-order explanation for who pursues what. The frontier argument explains why the markets split. Both-at-once evidence is concentrated in AI inference and recsys. Add, don't replace (M2). |
| (b) Hype cycles / fundraising / vendor incentives | **Partly, and missing** | The decommoditization motive for memory makers and controller vendors is absent and important for this reader (M3). Pure hype doesn't explain HBM or CXL expansion, which are deployed at scale. |
| (c) Different communities, different norms | **Partly** | Explains why "opposite" is mostly an outsider's framing; Q5 C5 found no unifying paper. But ASPLOS hosts both (Pond, TPP, AttAcc, NeuPIMs, CENT), and Samsung and Microsoft work on both. Mention as context; also note that PIM papers lean on simulation with weak baselines, while disaggregation papers lean on production traces and TCO. |
| (d) Latency matters more | **Lands** | Measured adder understated. Latency is why pooling failed and why DDR still holds 50–75% of capacity in deployed tiered systems. "Not a better middle" is wrong (M4). |
| (e) Access-density table not comparable | **Lands** | Uni vs bi, peak vs measured, arbitrary configs, SRAM stretching the span, no $/GB column, the §8 gap claim wrong (M11). Deployed CXL ≈ capacity-maxed DDR in BW/GB (F2). |
| (f) Energy overstated | **Lands, partly** | Scope mixing (I/O vs full access); activation is distance-independent; system-level share ~15%. Reframe as "pJ/bit caps bandwidth under a power budget" (M5). The PIM motivation from the 2.24 pJ/b on-die term survives. |
| (g) Is single-host CXL really "farther"? | **Lands strongly** | Deployed B is "cheaper/flexible bytes in the same box": zswap, Optane DIMMs, CXL expansion at NUMA-like distance. The thesis should say so and treat disaggregation proper (B2) separately (F3, M1). |
| Core thesis "fixed-ratio bundle; A/B unbundle; access density is the axis" | **Needs restating, not discarding** | Every tier is a bundle; the *system* unbundles by mixing tiers; one axis can't separate DDR from deployed CXL without $/GB. A 2-D cost frontier plus a latency constraint survives every attack above (F1–F2). |
| HBM → shortage → CXL causation | **Weak as causal chain** | Mechanism (shared wafers) is solid; adoption link rests on interested-vendor and press statements; designs predate the shortage; the floor is input-sensitive (M7). |

---

## Missing evidence or perspectives a memory-industry expert would expect

The web-search budget for this session ran out, so everything below comes from reviewer knowledge and needs sourcing before use.

1. **CPU-side "both in one socket" precedents:**
   - **Intel Xeon Max** (Sapphire Rapids + 64 GB HBM2e, with flat, cache and HBM-only modes).
   - **Fujitsu A64FX** (Fugaku: 32 GB HBM2 only, no DDR; its capacity limit was a known constraint).
   - **NEC SX-Aurora** (HBM vector engine).
   - These are the cleanest evidence for "A creates demand for capacity" outside GPUs, and for whether HBM-on-CPU continued in later Xeons. [reviewer knowledge; verify current status]
2. **Cerebras MemoryX / weight streaming.** On-wafer SRAM (the A extreme) paired with an external DRAM/flash weight store (B) streaming weights. It is a direct "both ends at once" design, a stronger example than Groq. Q1 mentions WSE-3 but not MemoryX. [reviewer knowledge]
3. **ZeRO-Offload / ZeRO-Infinity (SC'21)** for training: HBM for active compute, with optimizer state offloaded to CPU DRAM and NVMe. It is the training-side counterpart to KV offload, and it answers the objection that the thesis is all about inference. [reviewer knowledge]
4. **Optane's discontinuation (2022) as a lesson for B.** A cheaper-byte tier died because it wasn't cheap enough relative to DRAM at the time. In 2026, DRAM got dearer. This links directly to R2a: the economics of B tiers depend on the DRAM price cycle. The notes have TMTS-on-Optane but the draft never mentions the discontinuation.
5. **Hardware memory compression as a third lever orthogonal to distance:** IBM MXT [reviewer knowledge], Google zswap (in notes), Marvell Structera inline LZ4 (in notes). Compression achieves "cheaper bytes" with no distance at all.
6. **GPU scale-up domains as disaggregated memory:** NVLink/NVL72, NVLink Fusion, UALink. Peer-HBM access within a scale-up domain is memory-semantic far memory with far higher access density than CXL. It is arguably the biggest deployed "B" by bytes. Q5 C4 has the numbers but the draft omits the point.
7. **The middle improving:** MRDIMM/MCR DIMM (in Q3 but only in §8 of the draft), CAMM2/SOCAMM, and Mahar's HB-DIMM. These are counter-evidence to the "unstable middle" claim.
8. **Computational storage (SmartSSD, NGD, Samsung SmartSSD)** as a precedent for "compute follows the far tier", with limited adoption. Needed to hedge the §8 prediction. [reviewer knowledge]
9. **Memory-vendor business perspective:** custom HBM shifts base-die logic to foundries (TSMC N12/N3, Q1 §3.2), changing who captures value. That is a strategic reason memory vendors push PIM and aHBM: to keep logic value in-house. [synthesis from Q1 data]
10. **FULL TEXT NEEDED** (existing items whose resolution would change the draft): F1 (DDR4 22 pJ/b scope), F2 (UniIC scope), Kwon ISSCC'21 (PIM capacity and area penalty, activation energy under all-bank mode), FT2 (Micron ">4:1"), Hao SIGMOD'24 (CXL rung of the five-minute rule, which is central to §3), HC26 d-Matrix deck (whether 0.37 pJ/b is interface-only, and the HBM4 baseline).

---

## Suggested restructure

**§0 Short answer (rewrite):**
1. The baseline DDR bus offers a **narrow, pin-limited segment** of the memory cost frontier (supplied GB/s per GB ≈ 0.2–1 at ~1× $/GB, ~100 ns).
2. Two independent pressures push demand off that segment:
   - **(i) Bandwidth under a power and beachfront budget.** pJ/bit caps achievable bandwidth; compute grows ~3× per 2 yr vs DRAM bandwidth ~1.6×.
   - **(ii) Capacity cost.** $/bit decline slowed; capacity per unit performance fell 60–70%; DRAM is ~37% of Meta rack TCO and is now price-coupled to HBM wafers.
3. Data is skewed **per object**, so a *mix* of tiers beats any single point. The system unbundles bandwidth from capacity by combining tiers with different bundles.
4. **Deployed** forms of both directions are tiering moves (A1: passive HBM/on-package; B1: compression, cheaper or recycled media, single-host CXL), and the DDR middle still holds most capacity.
5. The **ambitious** forms (A2: PIM; B2: pooling) rest on extra arguments: internal bandwidth and offload, and statistical multiplexing. They have won only where data is bulk and predictable (KV pools) or kernels are narrow (GEMV, attention).
6. Both directions are also attractive for **business reasons**: memory and controller vendors escaping commodity pricing.

**Body:**
- §1 Distance is a proxy. Lead with the physics (flight time ~ns; latency lives in device, controller, SerDes and queues), with the GPU/Grace numbers as confounded illustrations. Apply it to both A and B.
- §2 The frontier. Table with **GB/s per GB (stated convention: per-direction or total; peak and measured), $/GB, pJ/bit (scope stated), and idle and loaded latency**. Explicitly show that deployed CXL ≈ capacity-maxed DDR in density and differs in $/GB. Show that the HBM–DDR gap (≈1 → ≈35) is empty.
- §3 Skew and placement. Keep ρ and I as separate axes plus MLP. Keep the counter-examples (dense weights are uniform; embeddings split).
- §4 The 2×2 (A1/A2/B1/B2). For each cell: driver, evidence, cost, deployment status, with provenance tags.
- §5 Coupling: capacity demand from A; supply economics with sensitivity table and "amplifier" framing; compute at the far tier with the "gives up internal bandwidth" counter-point.
- §6 History (pending R2c). Use it to test whether the 1990s IRAM vs NOW split followed the same market and community lines (attacks a and c).
- §7 Alternative explanations, weighed: markets, vendor incentives, communities. Say what each explains that the frontier argument doesn't.
- §8 Answer, rewritten as above, with "a wider hierarchy around a persistent middle".
- §9 Implications. Fix the gap claim; hedge the compute-placement prediction; add PCIe-lane and rack-power tensions.
- §10 Evidence quality. Restore inline tags; list secondary numbers explicitly; note weak PIM baselines.
