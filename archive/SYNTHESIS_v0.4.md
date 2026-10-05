# Closer and farther at once: why memory is moving both toward and away from compute

*Draft v0.4, 2026-10-03.*

**Revision history**

| Version | Change | Where to find it |
|---|---|---|
| v0.2 | Applied the adversarial review | `notes/r2d_review.md` |
| v0.3 | Added round-3 gap evidence | `notes/r3_gap_evidence.md` |
| v0.4 | Applied round-4 full-text reading | `ROUND4_DELTAS.md`; "Round-4 deltas" sections in each `notes/*.md` |

Earlier versions are in `archive/`. Outstanding full texts are listed in `FULLTEXT_REQUESTS.md`.

**Provenance tags**

| Tag | Meaning |
|---|---|
| [P] | Primary source, read in full |
| [V] | Vendor claim or slides (Hot Chips 2026 decks read from the slide text) |
| [Pr] | Press report |
| [S] | Secondary source |
| [D] | Our own arithmetic |
| [pend] | Depends on a full text not yet read |
| [Syn] | Our synthesis or inference |

All energy figures state their scope:

| Scope label | What it covers |
|---|---|
| full | Whole DRAM-side access |
| I/O | Interface only |
| system | Board or system level |

---

## 0. Short answer

The two directions look opposite only if physical distance is treated as the variable being optimized. It is not.

**1. The baseline occupies a narrow region of the memory cost frontier.** The DDR DIMM on a parallel bus next to the socket supplies about 0.2–1.6 GB/s per GB stored, at about 1× $/GB and about 100 ns idle latency.
- A channel's bandwidth is capped by its pins and its capacity by its slots.
- Capacity and bandwidth can only be bought together.
- Every byte costs the same however hot it is. [Syn]

**2. Two independent pressures push demand out of that region.**

*(i) Bandwidth under a power and beachfront budget.*
- **Compute outgrows memory bandwidth.** Peak compute grew about 3.0× every 2 years, against about 1.6× for DRAM bandwidth and about 1.4× for interconnect [P: Gholami 2024].
- **Energy per bit caps achievable bandwidth.** O'Connor shows that for HBM2 a 60 W DRAM budget allows about 2 TB/s [P: O'Connor MICRO'17].
- **The per-bit gap is mostly inside the die.**

  | Access type (full) | pJ/bit | Source |
  |---|---|---|
  | DDR4 | ~16.5–21 | [P: Lee 2018] |
  | HBM2 | 3.92 | [P: O'Connor] |

  The I/O share is only ~5 pJ/bit for DDR4 vs 0.3 for HBM2.
- **What A1 and A2 do.** Bandwidth-dense data that on-chip SRAM cannot absorb needs wider, shorter wiring and a different die organization (A1), and ultimately compute at the DRAM itself (A2). The clean example is LLM decode attention over a live KV cache: about 1 FLOP/B for multi-head attention, ×GQA group factor, independent of batch size.

*(ii) Capacity cost.*
- **DRAM got expensive relative to its old trend.**
  - The fall in DRAM cost per bit slowed from about 33–36%/yr to about 12–15%/yr after roughly 2011 [S].
  - Capacity per unit of socket performance fell about 60–70% in a decade [P: Berger TC'26].
  - Memory reached 37% of rack TCO in Meta's latest generation [P: TPP].
- **A lot of DRAM sits unused.**
  - About 6% of DRAM is stranded when 75% of cores are scheduled, rising past 10% at about 85% [P: Pond].
  - A further 32% (Google) to 55–80% (Meta) of allocated memory is idle over minutes [P].
  - Production systems move 20–33% of DRAM to cheaper tiers at ≤1–5% slowdown [P].

**3. Access is skewed per object, so a mix of tiers beats any single one.**
- About 90% of bandwidth comes from under 10% of capacity [P: Mahar, preprint]. In LLM serving, 10% of KV blocks get 77% of reuses [P].
- The *system* unbundles bandwidth from capacity by combining tiers whose bundles differ.
- Pressure (i) pulls the hot head toward A. Pressure (ii) pushes the cold tail toward B.
- Latency tolerance, which depends on memory-level parallelism, limits how far the tail can go.
- Bandwidth also limits the far tier itself. Hao et al. provision remote memory only until its bandwidth saturates [P: SIGMOD'24].

**4. The deployed forms of both directions are tiering moves, around a persistent middle.**
- *A1* is passive HBM and on-package memory.
- *B1* is compression, cheaper or recycled media, and single-host CXL expansion. Neither B1 form is very "far".
- The DDR middle still holds 50–75% of capacity in every deployed tiered system [D from P].
- The middle is also *improving*: 16-channel MRDIMM-12800 reaches 1.6 TB/s per socket [V: Intel, AMD, Hot Chips 2026].
- Server CPUs that tried HBM (Xeon Max, A64FX, SX-Aurora) have successors without it [V].

**5. The ambitious forms rest on extra arguments and remain niche.**
- *A2 (PIM/NDP)* rests on internal bandwidth 4–16× the pin bandwidth, and on offloading dependent accesses.
- *B2 (pooling, RDMA far memory)* rests on statistical multiplexing across hosts.
- B2 pays only in small switchless pools, at single-digit-% savings. "Most switch-based configurations are ROI negative" [P: Berger IEEE Micro'23].
- A2's capacity cost is now argued publicly by a memory maker: SK hynix's VP of system architecture called PIM a likely niche because logic displaces bits while inference is capacity-bound [Pr: DIGITIMES].
- Both have shipped only where data is bulk and predictable (KV pools) or kernels are narrow (GEMV, attention) [P/V].

**6. Non-physical reasons for investment.**
- Memory makers and controller vendors want to escape commodity pricing.
- The AI-accelerator and general-cloud markets have different binding constraints. This was also true of the 1990s split.
- Community norms differ.
- These explain *who* pursues each direction. The frontier argument explains *why the split falls where it does*. The two kinds of explanation are compatible. [Syn]

**7. Complements, and converging.**
- **A creates demand for capacity tiers.** Examples: GB200's LPDDR5X; d-Matrix pairing its 3D DRAM with LPDDR5X; ZeRO; MemoryX.
- **A's wafer appetite raises B's input prices.** HBM takes ~3× the wafer per bit [P: Micron]. It set a floor under DDR prices in 2024–25, and is one of three named causes of the 2026 shortage [V: Handy].
- **B's low bandwidth per GB and high latency pull compute toward far memory.** In production this happens in training: optimizer at the capacity tier in ZeRO-Offload, ZeRO-Infinity and MemoryX. Elsewhere it is still prototypes.
- **Why now.** In the 1990s, cheap and fast-scaling commodity DRAM let the conventional middle "outrun" both waves. That escape valve has closed. The difference from earlier "end of scaling" claims is measured price data rather than forecasts. [Syn]

---

## 1. Distance is a proxy, for both directions

- **Wire flight time is nanoseconds.** A 6 ft cable adds about 12 ns each way [P: Levis]. Latency comes from device timing (core about 50 ns; tRCD fell only about 30% while $/bit fell 16× [P: Chang 2016]), plus controllers, SerDes, switches and queues.
- **A does not reduce latency.** On Intel Xeon Max, the same cores and mesh reach both HBM2e and DDR5. HBM latency is about 20% *higher* than DDR, while STREAM bandwidth is about 700 vs 200 GB/s [P: arXiv 2505.14294].
- **GPU and Grace illustrations** (confounded by each host's own memory hierarchy) [S]:

  | Host → memory | Latency |
  |---|---|
  | H100 → HBM | ~300–330 ns |
  | Grace → LPDDR5X | >200 ns |
  | x86 → DDR | ~80–120 ns |

- **B's latency cost comes from protocol hops, not distance.**

  | Path (measured) | Latency | Source |
  |---|---|---|
  | Direct CXL | +~100–270 ns over local; tails ~1 µs at p99.99+ | [P: Octopus, Vistara, Melody, Levis] |
  | Switched CXL pool | 549–651 ns vs 146 ns local (3.76×) | [P: PolarCXLMem, SIGMOD'25] |

  Microsoft's *modelled* estimates are 155–180 ns added for 8–16-socket switchless pools and 270–345 ns for 64-socket pools [P: Berger IEEE Micro'23]. Spec-based estimates of +50–90 ns are optimistic.
- **GB200's "far" capacity tier sits physically close.** The LPDDR5X is on the Grace package, closer to its CPU than any DIMM. "Far" names a role in the hierarchy, not a distance.
- **What each direction actually buys:**

  | Direction | Buys |
  |---|---|
  | A | Bandwidth per byte stored; pJ/bit as the limit on bandwidth under a power budget |
  | B1 | $/GB, utilization, media independence (DDR4 reuse behind a CXL controller) |
  | B2 | All of B1, plus multiplexing across hosts |

## 2. The cost frontier: every tier is a bundle

**Conventions.** Bandwidth figures are peak, per direction, from vendor specs [D] unless noted. Sustained bandwidth is lower; commercial CXL devices reach 10–80% of local DRAM bandwidth [P: Wang et al.].

| Tier (example config) | GB/s per GB | $/GB (rel. DDR5 = 1) | Energy, pJ/bit (scope) | Idle latency |
|---|---|---|---|---|
| On-die SRAM: Groq 3 LPX, ~0.5 GB at ~156 TB/s per chip (scratchpad, not a capacity tier) | ~3×10⁵ [D from V] | ≫100 | 0.16–1.6 (full) [P: Horowitz, Dally] | ns |
| HBM4: MI455X 432 GB at 23.3 TB/s; Rubin fleet 11 PB at 800 PB/s | ~54–73 [D from V] | higher still [Pr] | "three or four" (system) [P: Dally GTC'26] | — |
| HBM3E: 36 GB stack at ~1.2 TB/s; B200 192 GB at 8 TB/s | ~33–42 [V] | ≥3 pre-shortage [S]; ~3× wafer per bit [P: Micron] | HBM2 3.92 (full) = 1.21 activation + 2.24 on-die + 0.3 wire I/O; interface incl. PHY ~1.5 [P: O'Connor; IEDM'20] | ~300 ns from GPU [S] |
| Hybrid-bonded DRAM-on-logic: d-Matrix Raptor, SeDRAM | ~20× HBM4 bandwidth per mm², ~½ its capacity per mm² [V] | n/a | 0.37 (I/O, measured) [V]; 0.66–0.88 (I/O) [P: UniIC]; no full-access figure | — |
| *Discontinued CPU HBM*: Xeon Max, A64FX, SX-Aurora | ~26 peak / ~11 sustained; ~32; ~25–32 [P/V] | — | — | Xeon Max HBM ~20% above DDR [P] |
| On-package LPDDR5X: Grace, Vera 1.5 TB at 1.2 TB/s | ~0.8–1.1 [V/D] | ≈1 [V] | ~12 for LPDDR (full; short bus, few devices) [P: Lee 2018]; NVIDIA: 5× bandwidth/W vs DDR5 [V/S] | >200 ns [S] |
| MRDIMM-12800, 16 ch, 1.6 TB/s (Diamond Rapids, Venice) | 1.6 / 0.8 / 0.4 at 1 / 2 / 4 TB [D from V] | >1 [unverified] | — | ~DDR |
| DDR5-6400 RDIMM, 12 ch, 1DPC, 64 → 256 GB DIMMs | 0.8 → 0.2 [D] | 1 (3DS 256 GB at a premium) [P] | ~16.5–21 (full DRAM-side; ~5 of it I/O) [P: Lee 2018] | 80–120 ns [P] |
| CXL expander (Gen5 x8/x16) | 0.125 peak/dir [D]; measured 0.225 (Vistara), 56.7 GB/s per device (ByteDance) [P] | new DDR5: ~1 + controller; recycled DDR4: 0.13 nominal, 0.3–0.6 all-in [P/D] | ~22–44 direct, ~28–67 switched, excl. controller (SerDes 11.4 pJ/b/lane) [D from P: Intel Gen5 SerDes] | 214–394 ns measured [P] |
| RDMA far memory | ~0.01–0.1, configuration-dependent [D] | ~1 (gain is utilization) | — | ~3.5–3.9 µs per 4 KB [P] |
| NVMe SSD | ~0.001 [D] | ≪1 [unverified] | n/a | 10–50 µs [P] |

**What the table shows** [Syn]

1. **Deployed CXL is not a new point in bandwidth density.**
   - Meta's Vistara CXL tier (0.225) equals capacity-maxed DDR (0.2). What separates them is $/GB.
   - B1 moves *down* the cost axis at DDR-like density. A moves *right* along the density axis at higher $/GB.
   - On energy, CXL costs about 1.3–2× local DDR per bit moved. Its energy case exists only at the system level: fewer idle DIMMs and reused media.

2. **The middle is improving, and on server CPUs it displaced HBM.**
   - On-package LPDDR, MRDIMM and SOCAMM2 sit at or slightly above DDR's density. SOCAMM2 is 192 GB per module and claims >⅔ better power efficiency than RDIMM [V].
   - Intel's Diamond Rapids has 16 channels of DDR5-8000 or MRDIMM-12800, CXL 3.0 and a 1.28 GB LLC, with no HBM in the deck. Fujitsu's MONAKA, A64FX's successor, has 12 channels of DDR5-8000+, CXL 3.0, and an LLC on a 3D-stacked SRAM die [V: Hot Chips 2026].
   - The CPU pattern: the near tier moved from HBM to stacked SRAM, the middle got faster, and capacity moved to CXL.

3. **The band between DIMM-class (~1) and HBM-class (~35+) was occupied, then vacated, on server CPUs.**
   - Xeon Max, A64FX and SX-Aurora sat in it. Their successors don't.
   - Outside servers the band is occupied:
     - phones and laptops with on-package LPDDR5X, about 4–5 GB/s per GB [D, unverified specs];
     - GDDR graphics cards, about 40–60 [D, unverified].
   - For server CPUs, the market chose "fast middle + SRAM + CXL" over "HBM on the CPU". That is direct evidence for a hierarchy that widens *around* the middle.

4. **On the DDR bus, capacity and bandwidth trade off directly.**
   - Bigger DIMMs dilute bandwidth per GB, and full speed requires one DIMM per channel.
   - Flagship CPUs held about 5 GB/s per core only by spending pins. From 2012 to 2026, cores went from 8 to 256 while channels went from 4 to 16.
   - So the squeeze shows up mainly as lost capacity and platform cost [P: Q3 notes; Berger TC'26].

5. **Wiring geometry: DDR is the squeezed middle, not an unstable one.**
   - Very wide parallel interfaces work at millimetre scale: HBM uses 1024–2048 bits, and hybrid bonding reaches 110,000 vias/mm² at 3 µm pitch [P: UniIC].
   - Serial links give about 4× bandwidth per pin per direction, and reach metres, at +50–70 ns per hop [P].
   - Serial memory attach has a long graveyard: FB-DIMM, RDRAM, HMC, OMI. CXL Type-3 is the first to stick.

## 3. Placement: three properties per object, not one

Placement depends on three properties of each object:
- **Access density ρ**: bytes per second demanded per byte stored.
- **Arithmetic intensity I**: FLOPs per byte moved.
- **Latency tolerance**: set by memory-level parallelism (MLP) [P: OSDI'25 "Beyond Hotness"].

| Object properties | Placement | Examples and evidence |
|---|---|---|
| High ρ, low I | A | Decode attention over live KV; bulk scans and bitwise operations |
| High ρ, high I | Absorbed by on-chip SRAM | Prefill (~1000 FLOP/B), dense GEMM. A2 gains only 1.0–1.3× here [V: Samsung] |
| Low ρ, high MLP | B1/B2 | Cold pages, most embedding rows, reusable KV |
| Low ρ, low MLP | Stays on the DDR bus | Pointer chasing, B-trees, graph workloads; GAPBS suffers most on CXL [P: Pond] |

**Read and write density differ.** Optane reads at 32 GB/s and writes at 11.2 GB/s, against DRAM's 107 and 80. HeMem therefore moves write-heavy pages back to DRAM first [P: HeMem SOSP'21].

**Low I alone does not decide placement.**
- Embedding gathers have the lowest FLOP/B (0.25), yet the big tables have low bandwidth demand; only the small hot tables are high-ρ [P: MTrainS].
- Batched weight GEMVs scale with batch size. The "~0.3% of GPU peak" decode figure is a batch-1 number from a PIM vendor [V: SK hynix SC23].

**Skew is pervasive but not universal.**
- Present in fleet memory, KV caches (more than 50% of blocks are never reused [P: Mooncake]) and embeddings [P].
- Absent for dense model weights, which are read in full every decode step. HBM capacity is sized to the weights.

**The economic placement rules need a bandwidth term.** The five-minute rule (capacity side) and the roofline (compute side) both compare an object's ρ against a tier's supplied density and price.
- DRAM↔flash break-even intervals shrank from ~15 min (2007) to ~41 s (NVMe 2017, 4 KB) [P]. A 2025 preprint puts them at ~34 s for CPU+DDR at 512 B and ~5 s for GPU hosts with high-IOPS SSDs [P: Zhang 2025].
- For the DRAM→remote-memory step, Hao et al. compute **no** break-even interval. They argue the five-minute rule doesn't fit, since remote memory is "not cheap enough to be a storage device, nor … fast enough to be a performance device".
- Their provisioning rule is to size local DRAM by the classic rule, then substitute remote memory "until it saturates the remote memory bandwidth" [P: SIGMOD'24 §4.9]. That is a ρ rule: the five-minute rule assumes unlimited fast-tier bandwidth, and the middle tier breaks that assumption.
- Hao's evidence is weak:
  - The remote tier is emulated: 0.1–5 µs latency, 5 GB/s, one node, zipf-0.9.
  - Prices are relative only. The "up to 10×" gain is against DRAM+disk.
  - At 0.8× price, three tiers can lose.
- Skew still carries it: with local memory one-sixth the size of remote, local serves more than half of accesses, and throughput is about 70% at 50× remote latency [P].

## 4. The 2×2: deployed vs ambitious forms of each direction

### A1. Proximity for bandwidth (HBM, on-package, 3D-stacked)

**Driver:** bandwidth under power and beachfront limits.

**Strongest evidence:**
- DDR4 full access costs ~16.5–21 pJ/b vs 3.92 for HBM2; the gap is mostly inside the die [P].
- Compute grew 3.0× every 2 years vs 1.6× for DRAM bandwidth [P].
- Stacked DRAM gives "~10× bandwidth at ~10× lower energy/bit" [P: Dally GTC'26].
- Decode attention runs at about 1 FLOP/B [P].

**Pays with:**
- Capacity per $ and per mm². HBM3E takes ~3× the wafer per bit, including packaging complexity [P: Micron].
- d-Matrix Raptor has about ½ the capacity per mm² of HBM4 for about 20× the bandwidth per mm², and carries LPDDR5X as a capacity tier [V: Hot Chips 2026].
- The trade holds at fixed cost and beachfront: HBM per GPU still grew from 80 GB to 432 GB.

**Status:**
- Deployed at scale on GPUs and accelerators.
- Vacated on server CPUs (§2.2–2.3).
- Base-die logic is the fastest-moving part: Samsung's table shows a 4 nm base die for HBM4E [V], and the SK hynix–TSMC deal moves base dies to a foundry [V].
- Thermal is a growing cost: SK hynix shows a 2.2× thermal burden over six HBM generations, against packaging that cuts thermal resistance to 0.40× [V].

### A2. Compute at memory (PIM/NDP, DRAM-on-logic compute)

**Driver:** internal bandwidth of 4–16×, or 9× for AttAcc's design; HBM's 2.24 pJ/b on-die term, which no link improvement touches [P].

**Strongest evidence:**
- **Aquabolt-XL:** more than 2× performance and more than 70% less energy [V].
- **AttAcc** (simulated) [P]:
  - 2.81× vs a DGX A100 at the *same* 1,280 GB capacity, but with half the HBM stacks, so part of the gain is KV capacity.
  - 3.49–5.93× vs the 640 GB DGX.
  - The gain shrinks with GQA.
- **LPDDR5X-PIM:** 3.01× tokens/s measured, with no energy data [V].
- **UPMEM:** 23× vs a weak 4-core CPU [P].

**Pays with:**
- **Capacity.** Samsung's HBM2-PIM stack is 6 GB, 25% less than 8 GB [P: via the ISSCC'22 AiM comparison table]. AttAcc's per-bank PIM takes 10.84% of the DRAM die (about 25% if sized like HBM-PIM) [P]. SK hynix publicly calls PIM a niche because logic displaces bits [Pr].
- **DRAM-process logic:** 3× slower transistors, 3 metal layers [V: UPMEM].
- **Thermal:** refresh doubles every ~10 °C above 85 °C [P].
- **No virtual memory or coherence.**
- **Activation energy remains.** Area and pJ/b need Kwon ISSCC'21 [pend].

**Status:**
- Niche. HMC is dead (2018), and HBM-PIM is at demo stage.
- LPDDR5X-PIM is measured on Samsung's own SoC [V], and UPMEM was acquired by Qualcomm [S].
- Baselines are often weak: Newton reports 54× vs a real GPU but 10× vs an ideal non-PIM system [P]. AiM's "up to 10×" is FPGA-emulated [P].

### B1. Cheaper or extended capacity (compression, tiering, single-host CXL, recycled DDR4)

**Driver:** $/GB, capacity per core, carbon, OOM reliability.

**Strongest evidence:**
- **Compression (IBM MXT):** typically 2:1–6:1, within one point on SPECint2000; halving installed memory cut system cost by more than 40% [P: IBM 2000]. IBM Power AME gets 2.83× at about 12% CPU [V].
- **Meta Vistara:** recycled DDR4 at 0.13× $/GB nominal (0.3–0.6× all-in [D]); up to 25% fewer servers for capacity-bound ML inference; about 50% fewer OOMs [P].
- **Google zswap:** about 20% of data, 4–5% of memory TCO [P].
- **TMTS:** 25% of DRAM on a slower tier at under 5% slowdown [P].

**Pays with:**
- **Bandwidth and latency.** The Vistara tier has ~10× less absolute bandwidth (2.7–3.6× less per GB) [D], and latency +92% idle / +60% loaded [P].
- **Uneven results.** ByteDance measured 1.4–9.8× slower Spark with DRAM/CXL interleaving. Its "26% TCO saving" comes from a cost model, not a measurement [P].
- **Upgrade granularity is weak.** Equal local capacity beats Fastswap's far memory by 2.5–9.7% [P].
- **Thin carbon gain.** GreenSKU nets only +1%, and only 3% of traces touch the CXL tier [P].

**Status:**
- Deployed: Azure M-series [Pr]; Meta [P]; Google zswap, and formerly Optane [P].
- **Optane** was wound down in 2022 with a $559M impairment and no stated reason [P]. An analyst estimates it sold at about ½ DRAM's $/GB while costing more to make [S].
- Lesson: a B1 tier must be cheaper to *make*.

### B2. Sharing across hosts (pooling, RDMA far memory, rack-scale)

**Driver:** statistical multiplexing (sum of peaks vs peak of sums) [P: Lim ISCA'09]; stranding of up to 30%, median 6–10%, p95 25% [P: Berger].

**Strongest evidence:**
- **Pond:** 7–9% less DRAM at 1–5% slowdown [P].
- **Berger:** small switchless pools (≤16 sockets) are often ROI-positive [P].
- **Mooncake:** an RDMA KV pool in production, serving +75% requests [P].

**Pays with:**
- **Switch cost.** "Most switch-based configurations are ROI negative." A switched 8-socket pool costs about 2.5× a switchless one, and large pools may be "counterproductive" [P: Berger]. Octopus finds switches add ≥220 ns and "always cost more" [P]. Levis's break-even (about 140 TB) assumes a switch [P].
- **Local memory still needed.** More than 25% of memory must stay local [P: Berger].
- **Tail latency** rises 10–82% [P].
- **Fault tolerance** costs up to +35% memory [P].

**Status:**
- Niche: bulk, read-mostly, predictable data (KV pools).
- Alibaba's PolarCXLMem is a one-rack prototype: 2.1× vs RDMA, not vs local; 30% memory saving. Production use is press-only [P/Pr].
- The Machine, Gen-Z and Intel RSA are dead. RSA hinged on silicon photonics [P: Aguilera OSR'23].

**Pattern** [Syn]
- The deployed cells (A1, B1) need no change to the programming model.
- The ambitious cells (A2, B2) reached volume only where a vendor controls the software stack, or where the data is bulk and predictable.
- The critiques mirror each other: A2 is limited by process, heat and capacity; B2 by latency and switch cost.

## 5. Coupling: the two directions need each other

### 5.1 A creates demand for capacity tiers

At fixed cost and beachfront, every step toward compute trades capacity for bandwidth. The capacity must come from a second tier:
- **GB200 NVL72:** 13.4 TB HBM3E at 576 TB/s plus 17 TB LPDDR5X at 14 TB/s, over coherent NVLink-C2C [V].
- **d-Matrix Raptor** chiplets carry LPDDR5X [V].
- **SOCAMM2:** more than 50 TB of CPU-attached LPDDR per rack. Micron reports more than 80% lower time-to-first-token from KV offload on GH200 [V].
- **NVIDIA** offers a separate KV-cache storage rack [V].
- **LLM KV offload** to DRAM, SSD or RDMA pools [P].

**Training offload**
- **ZeRO-Offload / ZeRO-Infinity** [P]:
  - CPU memory bandwidth is ≥10× PCIe, and GPU memory another 10× on top.
  - The optimizer runs on the CPU, saving 8× GPU memory (13B parameters on one V100).
  - ZeRO-Infinity reaches 32T parameters on 512 V100s.
  - Its per-tier bandwidth model is a worked instance of §3: required bandwidth = peak FLOPs ÷ the arithmetic intensity of each data class.
- **Cerebras** pairs 44 GB of on-wafer SRAM (CS-3) with MemoryX holding 1.5 TB–1.2 PB, streaming weights layer by layer [V].
- **SemiAnalysis** (free section): HBM and DDR/SSD KV offload "do not compete with each other directly" [S].

**GPU scale-up domains are memory-semantic disaggregation inside the A market.**
- NVL72 makes 13.4 TB of HBM accessible across 72 GPUs at 1.8 TB/s per GPU; Rubin's NVLink 6 reaches 3.6 TB/s per GPU [V].
- AMD Helios exposes a "distributed shared memory model" over scale-up [V].
- UALink 1.0 specifies load/store/atomic access across up to 1,024 accelerators [V].
- Peer HBM is used in production to *move* KV caches (NIXL) [V]. As a *capacity tier* it is research:
  - SiDP: 1.8× KV capacity, 1.5× throughput, offline batch only, 12% slower at batch size 1 [P].
  - SwiftCache: up to 69% lower P99 time-to-first-token, 3.98× longer context [P].

### 5.2 A raises the price of B's inputs, and the payoff of B's cheapest forms

**Mechanism.** HBM and conventional DRAM share fab wafers.
- HBM3E needs about 3× the wafer per bit of DDR5. Micron says the ratio "only increases with future generations" (Dec 2025 remarks p.5) [P]. Its Hot Chips slide 11 says "~3X more silicon", including packaging and manufacturing complexity [V].
- HBM4E is ">4:1" (Micron, Mar 2025 remarks p.6) [P]. HBM4 has no primary number.
- The ratio rises because TSV count grew 5.0× and TSV area penalty 1.3× from first-generation HBM to HBM3E [V: SK hynix Hot Chips 2026].
- TrendForce shares (HBM going from 18% to ~30% of wafers for 8% to 13% of bits, end-2025 → 2027 forecast) are *consistent with* this ratio, not independent evidence [S].

**Floor.** While HBM is supply-rationed, a vendor is indifferent between HBM and DDR when

  P_DDR ≈ P_HBM / [r(1+c)]

  - r is the wafer ratio, which already includes yield.
  - c is the extra non-wafer cost: TSV, stacking, test and the foundry base die. c = 0–0.2 is our assumption [D].

| HBM price input | r | Floor ($/GB) |
|---|---|---|
| HBM3E at 2025 prices [S] | 3 [P] | ~4.2–5.0 |
| HBM3E at 2026 prices ($10–16/GB [S]) | 3 [P] | ~2.8–5.3 |
| HBM4 in 2027 ($32–36/GB [Pr]) | 3–4 (4 = HBM4E case) | ~7–12 |

**History of the floor** [D/V]
1. *2024–mid-2025: floor binding.* DDR traded *below* the HBM3E floor: Handy's chart has DRAM spot at about $1–2.3/GB in early to mid 2025. HBM therefore earned more per wafer, and wafers moved to HBM. SemiAnalysis called HBM "margin accretive" for SK hynix and Micron in August 2025.
2. *2026: overshoot.* DDR overshot the floor. Spot reached about $16/GB by July–August 2026 [V: Handy, chart reading], and DDR5 RDIMMs out-earned HBM per wafer from 1Q26 [S].
3. *2027: binds again only if HBM4-era prices hold.*

**Causes of the shortage.** Handy names three, one of which is HBM's ~3× die area [V]. HBM is therefore a direct cause of the shortage. Others named elsewhere:
- AI demand for DDR5 and LPDDR
- capex discipline
- DDR4 end-of-life
- an alleged-collusion class action [Pr]

**Who benefits.**
- Only B tiers that need no leading-edge wafers gain relative ground: recycled DDR4 behind CXL (bounded by server retirements; resale value $5–9/GB [S]) and compression.
- CXL with new DDR5 does not escape the price rise.
- NAND rose +55–60% QoQ in 1Q26 [Pr], so SSD offload is not spared either.

**Causal link to adoption: an amplifier, not a cause.** The evidence that the shortage drives CXL adoption is two vendor statements from an interested party (Marvell) plus press reports. No hyperscaler primary source makes the link, and the B1 designs predate the shortage [P].

### 5.3 B creates demand for compute at the far tier

**Mechanism.** Far memory has low bandwidth per GB and high latency, so dependent access chains and scans are slow. Miller et al. call offloading dependent accesses a "required optimization" [P: ACM Queue'23].

**Deployed, in training.** ZeRO-Offload, ZeRO-Infinity and Cerebras MemoryX all run the optimizer update at the capacity tier, because optimizer state is too large and too low-intensity to stream [P/V]. This is exactly what §3 predicts.

**Research systems:**
- **Teleport:** 5.4× average slowdown when unmodified; up to 11× better than LegoOS with operator pushdown [P].
- **PULSE:** 9–34× lower pointer-traversal latency [P].
- **CXL-ANNS:** 3.8× vs an oracle all-DRAM system [P].
- **BEACON** (simulated): compute on the CXL-DIMM board or in the switch, with no DRAM-die change. 4.70×/4.13× vs the best DIMM-based NDP, but only 85–91% of MEDAL's energy efficiency. Communication is 52–61% of energy before locality optimizations [P].

**Prototypes and products, not deployed:**
- **Samsung CXL-PNM** is a *paper prototype* [P; simulated]: 7 nm parameters, FPGA used only to check function, ASIC in development. Its cost advantage rests on an *assumed* $7,000 per card vs $10,000 per A100, device-only and with no switch. A single card is 10.8% slower than one A100 on OPT-13B.
- **XCENA MX1:** up to 4.7× throughput and 18.7× bytes/J vs the host reading over CXL, but only 2× and 6.2× vs local DRAM, idle power excluded [V].
- **Marvell Structera A:** 16 Neoverse V2 cores in a 5 nm CXL controller [V].
- **SK hynix CMS** [V].

**Synthesis and counter-point** [Syn]
- A CXL controller (or switch, or board) is already logic-process silicon beside the DRAM, so compute there escapes A2's process and thermal objections.
- But it sees only channel-level bandwidth. For DDR5 controllers that is about 3× the link (Structera A). For LPDDR5X CXL-PNM it is about 17× [D, assuming an x16 Gen5 link].
- So compute there escapes PIM's objections by giving up most of PIM's benefit. BEACON shows it then pays in communication energy.
- Precedent: computational storage had products (SmartSSD, NGD, ScaleFlux, Eideticom), but programmability and missing standards "prevented the broad usage" [P: CIDR'21].

## 6. History: the same split in the 1990s, and why the outcome differs now

**The same split, often the same people** [P]:
- Patterson and Anderson co-authored both IRAM (1997) and NOW (1995).
- Oskin wrote Active Pages (1998) and Grappa (2015).
- J. Chang co-authored memory blades (ISCA'09) and the 2014 NDP workshop report.
- Ranganathan co-authored memory blades (2009) and Nanostores (2011).
  - Nanostores puts compute next to 3D-stacked NVM in a shared-nothing scale-out design. That is closer-plus-scale-out, *not* disaggregation, and it sketches a "hierarchy of computes" (Fig. 4).

**Different baselines, different markets** [P]:
- IRAM targeted the off-chip bus (98.5 nJ per off-chip access vs 4.55 nJ on-chip) and was aimed at games, PDAs and embedded devices.
- Network RAM targeted *disk*: a GMS remote 8 KB page took 1.44 ms vs 3.6–14.3 ms from disk, and was more than 1000× slower than local DRAM. NOW was aimed at building-scale science computing.
- So the 1990s split *did* follow market lines as well as resource lines.

**Explicitly reconciled as a hierarchy.** IRAM papers backed on-chip DRAM with off-chip DRAM, paging, or networked IRAMs. Nanostores, IRAM and the 2014 report all sketch near+far qualitatively.

**The workload boundary was already visible in 1989.** IVY's shared virtual memory gave super-linear speedup when data outgrew one node, but "no speedup at all" on read-once streaming such as a dot product [P: Li & Hudak].

**Each wave had its own kill conditions** [P], with cheap commodity DRAM as a common factor [Syn]:
- **PIM:** DRAM-process logic cost 30–70% in area and 30–100% in speed [S]; it broke the commodity model and needed a new programming model; DRAM prices crashed from $40 to $6 per 16 Mb part in 1996 [P].
  - The 2014 NDP report's sidebar says the 1990s designs did not fade for lack of performance or energy efficiency. Rather, "Moore's law allowed the basic von Neumann machines to outrun them" [P].
- **Network RAM** only beat disk, and cheap local DRAM won [Syn].
- **Hardware CC-NUMA** survived (SGI Origin, remote memory about 3.6× local).
- **Software DSM** stayed niche [P: IVY; Aguilera OSR'23 p.44].
- **Least-disruptive forms survived:** eDRAM as caches and console framebuffers; HBM over HMC.

**What is structurally different now:**
1. **The escape valve has closed.** DRAM $/bit falls about 12–15%/yr and density grows about 2× per decade, while AI demand outgrows a package. So neither direction can be outrun by "just buy more DDR". This is our best explanation for why both are pursued *simultaneously* [Syn].
   - Caveat: "end of DRAM scaling" was declared before (Wulf & McKee 1995; Ranganathan 2011). Ranganathan's 2011 response was a failed bet on PCM or memristors replacing DRAM [P].
   - The difference now is measured price behaviour, not forecasts.
2. **The far tier changed what it competes with.** It went from "faster disk" to "slower DRAM". CXL at about 2× local is nearer than Origin's 3.6×. CXL expansion revives the surviving NUMA branch; RDMA far memory repeats the network-RAM/DSM branch.
3. **Packaging decoupled the processes.** SeDRAM bonds a 25 nm DRAM die to a 55 nm logic die [P: UniIC]. That logic die is DRAM periphery, though, not compute.
4. **An anchor workload exists.** AI inference runs on vendor-controlled software stacks.
5. **Standards and ownership.** CXL absorbed the competing fabrics, and hyperscalers own their OS and hypervisor.
   - Aguilera et al.'s "why now" test for disaggregation is "pressing problem + feasible technology" [P]. They predicted CXL pooling within 3–5 years of 2023. They do not discuss near-memory compute.

## 7. Alternative explanations, weighed

**(a) Different markets and buyers.** *True and first-order, then as now.*
- Today A's evidence comes mostly from AI accelerators and mobile SoCs. Boroumand's 62.7% data-movement share is a 2018 mobile-SoC figure [P], and its PIM results are gem5-simulated: 55.4%/54.2% for accelerators, 49.1%/44.6% for PIM cores [P].
- B's evidence comes mostly from CPU cloud fleets. In the 1990s, IRAM targeted embedded and consumer devices while NOW targeted science clusters.
- But the strongest both-at-once cases are single workloads: LLM serving, DLRM, Chrome's ZRAM (far memory whose compression is itself a PIM target [P: Boroumand]), and GB200.
- Market and resource-pressure explanations are compatible. Markets differ *because* their binding constraint differs.

**(b) Vendor incentives and hype.** *Real, and complementary.*
- Memory makers want out of commodity pricing. Base-die logic is moving to foundries: SK hynix–TSMC, "customized HBM" [V]. Value capture is unquantified.
- Signals from the memory makers:
  - Samsung pushes PIM and aHBM [V].
  - Micron's Hot Chips 2026 roadmap graphic ends at "Processing in Memory" [V].
  - SK hynix publicly backs packaging and stacking memory on accelerators over PIM [Pr].
- Controller vendors and startups gain a whole product category.
- Hype residue (HMC, The Machine on memristors, Gen-Z, Intel RSA on photonics) shows that incentives alone don't make a direction work.

**(c) Different communities and evaluation norms.** *Context.*
- PIM papers lean on simulation with weak baselines: AttAcc, CXL-PNM, BEACON and Boroumand's PIM are all simulated. Disaggregation papers lean on production traces, though their TCO numbers are often modelled (ByteDance, Berger, Octopus).
- No paper we found treats near vs far as one *quantitative* design axis. Qualitative sketches exist: IRAM 1997, Nanostores 2011, the 2014 NDP report.

## 8. Answer to the research question

Both directions attract serious researchers and companies for two reasons. First, the baseline DDR DIMM has stopped being a good compromise for the two resources it couples. Second, real data is skewed enough that splitting the difference is wasteful.

**Bandwidth-dense, low-intensity data**
- The DDR bus supplies too few bytes per second per byte stored, at roughly 4–5× HBM's full-access energy per bit, and compute keeps outgrowing it.
- Wider, shorter wiring with a die organized for bandwidth (A1) improves bandwidth density and pJ/bit together.
- Compute at the DRAM (A2) goes after the remaining in-die energy, at a capacity cost the memory makers themselves now cite.

**Capacity-dense, cold data**
- The DDR bus is too expensive per byte. It can't add capacity without diluting bandwidth or spending pins, and it allocates memory per socket.
- This at a time when memory is about 37% of rack TCO and DRAM $/bit no longer falls fast.
- Cheaper media, compression and serial attach (B1) fix cost. Pooling (B2) fixes stranding, but pays only in small switchless pools.

**"Closer" and "farther" are geometric labels for moves along two cost axes**, bandwidth density and $/GB, under a latency constraint. They are opposite in geometry and aligned in purpose. The result is **a wider hierarchy around a persistent, improving middle**. On server CPUs the middle even displaced HBM.

**Why both, and why now.** In the 1990s, fast-scaling commodity DRAM let the middle outrun both ends. Today it cannot. The markets that pursue each end differ because their binding constraints differ.

**Genuine tensions:**
1. **Wafers.** A's growth raises B's media cost while HBM is rationed. The floor was binding in 2024–25.
2. **PCIe lanes.** CXL bandwidth expansion needs about 64 Gen5 lanes [P/V], which compete with GPUs and NICs.
3. **Rack power.** Vistara's CXL tier uses 30 W per 256 GB [P], and HBM GPUs dominate the rack. CXL also costs about 1.3–2× DDR energy per bit [D].
4. **Software investment.** Tiering kernels compete with PIM programming models for effort.

## 9. Implications and open questions

**Specify products by frontier position.** The relevant coordinates are GB/s per GB, $/GB, pJ/bit with scope, and idle and loaded latency, rather than attach point.

**The DIMM-to-HBM density band on server CPUs was vacated, not unexplored.** Any re-entry must beat "fast middle (MRDIMM-12800) + stacked SRAM + CXL".

**Prediction** [Syn], hedged:
- Passive HBM with logic base dies, and single-host CXL expansion, persist.
- A2 and B2 stay niche outside vertically integrated AI stacks.
- Compute at the far tier grows where the software is vertically integrated, as already happened in training. Computational storage is the cautionary precedent.
- Each direction weakens if its escape valve reopens:
  - 3D DRAM restoring steep $/bit declines would hurt B.
  - Continued HBM bandwidth scaling would "outrun" in-bank PIM.

**Open questions:**
1. Redo the pooling break-even (Levis, and Berger's % of server BOM) at 2026–27 DRAM prices.
2. Will the HBM price floor bind again in 2027 (HBM4-era prices)?
3. Build a quantitative placement theory joining the five-minute rule, the roofline, MLP and Hao's bandwidth-saturation rule: the ρ × I × MLP model.
4. Can DRAM-on-logic's capacity cost (about ½ capacity per mm²) be offset by LPDDR or CXL capacity tiers at system level? d-Matrix's design already pairs them.
5. Is there a server CPU workload class that justifies re-entering the 1–35 GB/s per GB band?

## 10. Evidence quality

**Read in full (primary):**
- Pond, TPP, Vistara, Memstrata, GreenSKU, Octopus, Levis.
- Berger IEEE Micro'23 and TC'26; ByteDance EuroSys'24; PolarCXLMem; Fastswap.
- Gholami; O'Connor; Horowitz; Lee 2018; UniIC IEDM'20 and VLSI'23; Intel Gen5 SerDes.
- PrIM; AttAcc; AiM ISSCC'22; CXL-PNM; BEACON; Boroumand; Hao SIGMOD'24; HeMem.
- Teleport, Mooncake, ZeRO-Offload, ZeRO-Infinity, MXT, SiDP, SwiftCache.
- Micron prepared remarks; Dally GTC'26 transcript.
- Historical papers: IRAM, NOW, GMS, IVY, Nanostores, Aguilera.

**Vendor, from slide text:** Hot Chips 2026 decks from d-Matrix, Samsung (base die, LPDDR5X-PIM), XCENA, Micron, SK hynix, Handy, Intel Diamond Rapids, Fujitsu MONAKA, NVIDIA Rubin/Vera/LP30 and AMD MI400.

**Secondary or press:**
- DRAM price data and forecasts (TrendForce, Citi/Goldman).
- SemiAnalysis (paid section unread).
- DIGITIMES (SK hynix keynote).
- Azure M-series.

**Pending:**
- Kwon ISSCC'21: PIM area and measured pJ/b.
- d-Matrix Raptor ISCA'26: scope of the 0.37 pJ/b figure.
- Borg and "Imbalance in the Cloud" traces: the "~60% utilization" figure is illustrative only.
- A64FX application papers: whether 32 GB of HBM was a constraint.

**Known weaknesses:**
- PIM/NDP speedups are often simulated against weak or capacity-mismatched baselines (AttAcc's half-stack DGX; CXL-PNM's assumed card price).
- TCO figures on the far-memory side are often modelled (ByteDance, Berger, Octopus).
- The Meta bandwidth-tiering result [P: Mahar] is emulated, covers one workload, and uses a DDR-bus "fast" tier.
- The density table uses peak specs.
- The Hot Chips bar-chart readings (Handy price chart, NVIDIA bandwidth/W) are approximate.
