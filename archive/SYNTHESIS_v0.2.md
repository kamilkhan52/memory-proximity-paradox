# Closer and farther at once: why memory is moving both toward and away from compute

*Draft v0.2, 2026-10-03. This revision follows the adversarial review (`notes/r2d_review.md`); v0.1 is in `archive/`. Supporting notes are in `notes/`. Outstanding full texts are listed in `FULLTEXT_REQUESTS.md`.*

**Provenance tags**

| Tag | Meaning |
|---|---|
| [P] | Primary source, read in full |
| [V] | Vendor claim or slides |
| [Pr] | Press report |
| [S] | Secondary source |
| [D] | Our own arithmetic |
| [pend] | Depends on a full text not yet read |
| [Syn] | Our synthesis or inference |
| [R3] | Being verified (round 3) |

---

## 0. Short answer

The two directions look opposite only if physical distance is treated as the variable being optimized. It is not.

**1. The baseline sits in a narrow region.**

- The DDR DIMM on a parallel bus next to the socket offers a narrow, pin-limited region of the memory cost frontier:
  - roughly 0.2–1 GB/s of supplied bandwidth per GB stored,
  - at about 1× cost per GB,
  - at about 100 ns idle latency.
- One channel's bandwidth is capped by its pins, and its capacity is capped by its slots.
- Capacity and bandwidth can only be bought together, and every byte costs the same however hot it is. [Syn]

**2. Two independent pressures push demand out of that region.**

*(i) Bandwidth under a power and beachfront budget.*

- Compute grew about 3.0× every two years, DRAM bandwidth about 1.6×, and interconnect about 1.4× [P: Gholami 2024].
- Energy per bit caps the bandwidth a power budget can buy. For HBM2, a 60 W DRAM budget allows about 2 TB/s [P: O'Connor MICRO'17].
- Bandwidth-dense data with low arithmetic intensity cannot be absorbed by on-chip SRAM. The clean case is LLM decode attention over a live KV cache, which stays near 1 FLOP/B at any batch size.
- For such data, the only lever that improves bandwidth per mm² and pJ/bit *together* is shorter, wider wiring (A1), and ultimately compute at the DRAM itself (A2).

*(ii) Capacity cost.*

- The fall in DRAM cost per bit slowed from about 33–36% a year to about 12–15% a year after roughly 2011 [S: Objective Analysis / AI Impacts].
- Capacity per unit of socket performance fell about 60–70% in a decade [P: Berger et al. TC'26].
- Memory reached 37% of rack TCO in Meta's latest generation [P: TPP].
- Within this, about 6% of DRAM is stranded at the median (up to 25% at p95) [P: Pond]. A further 32% (Google) to 55–80% (Meta) of allocated memory sits idle over minutes [P].
- Production systems actually move 20–33% of DRAM to cheaper tiers at ≤1–5% slowdown [P: TMTS, TPP, Memstrata].

**3. Access is skewed per object.**

- About 90% of bandwidth comes from under 10% of capacity [P: Mahar et al., preprint, 9 Meta services]. In LLM serving, 10% of KV blocks get 77% of reuses [P: Alibaba ATC'25].
- So a *mix* of tiers is cheaper than any single tier. The *system* unbundles bandwidth from capacity by combining tiers whose bundles differ.
- Pressure (i) pulls the hot head toward A. Pressure (ii) pushes the cold tail toward B.
- Latency tolerance, which depends on memory-level parallelism, decides how far the tail can go.

**4. The deployed forms of both directions are tiering moves.**

- A1: passive HBM and on-package memory.
- B1: compression, cheaper or recycled media, and single-host CXL expansion.
- Neither deployed B form is very "far". Google's far memory is compressed DRAM on the same node; Meta's CXL tier sits at NUMA-like distance.
- The DDR "middle" still holds 50–75% of capacity in every deployed tiered system [D from P: Vistara, TMTS, TPP, Memstrata]. The hierarchy is widening around a persistent middle, not replacing it.

**5. The ambitious forms rest on additional arguments.**

- A2 (PIM/NDP) relies on internal-vs-pin bandwidth ratios of 4–16× and on offloading dependent accesses.
- B2 (pooling and RDMA far memory) relies on statistical multiplexing across hosts.
- Both have shipped only in niches:
  - B2: bulk, read-mostly KV caches. Mooncake's production RDMA pool [P]; NVIDIA's KV tiering [V].
  - A2: narrow kernels such as GEMV and attention. Samsung/SK hynix demos; LPDDR5X-PIM productization [S].

**6. Both directions also attract investment for non-physical reasons.**

- Memory makers and controller vendors are escaping commodity pricing: custom and aHBM base dies, PIM, CXL modules [V/Pr].
- Two markets with different binding constraints are involved: AI accelerators are bandwidth-bound, general-purpose cloud is capacity- and cost-bound.
- Different research communities and evaluation norms play a part [Syn].
- These forces explain *who* pursues each direction. The frontier argument explains *why the split falls where it does*.

**7. The directions are complements and are converging.**

- A's capacity scarcity creates demand for capacity tiers. GB200 pairs HBM with LPDDR5X over a coherent fabric [V].
- A's wafer appetite raises the price of B's inputs and the payoff of its cheapest forms [P/Pr].
- B's low bandwidth per GB and high latency make compute placed next to far memory attractive (CXL-PNM, Structera A, Teleport-style pushdown) [V/P].
- History explains *why now*. In the 1990s both waves (IRAM and network RAM) were killed by the same thing: commodity DRAM getting cheap fast. That escape valve has closed, so both directions are forced at once [P: R2c].

---

## 1. Distance is a proxy, for both directions

- **Wire flight time is nanoseconds** (a 6 ft cable adds ~12 ns each way [P: Levis HotNets'23]). Latency lives elsewhere:
  - DRAM device timing: core ~50 ns; tRCD fell only ~30% while $/bit fell 16× [P: Chang 2016].
  - Controllers, SerDes, switches and queues.
- **The same mechanism cuts both ways.**
  - A does not reduce latency.
  - B's latency cost comes from protocol hops, not distance.
  - CXL adds a measured +~100–270 ns over local DDR. Spec and emulation estimates of +50–90 ns are optimistic [P: Octopus, Vistara, Melody, Levis]. Tails reach ~1 µs at p99.99+ [P: Melody].
- **Illustrations (confounded by each host's hierarchy):**

  | Host | Memory | Latency |
  |---|---|---|
  | H100 | HBM | ~300–330 ns |
  | Grace | on-package LPDDR5X | >200 ns |
  | x86 | local DDR | ~80–120 ns |

  Sources: [S: Chips and Cheese].
- **GB200's "far" capacity tier (LPDDR5X) sits on the Grace package**, physically closer to its CPU than any DIMM [V]. "Far" names a role in the hierarchy, not a distance.
- **What A actually buys** is bandwidth per byte stored, and pJ/bit as the limiter of bandwidth under a power budget.
- **What B1 actually buys** is $/GB, utilization, and media independence (DDR4 reuse behind a CXL controller).
- **What B2 additionally buys** is multiplexing across hosts.

## 2. The cost frontier: every tier is a bundle

All bandwidth figures are peak, per direction, from vendor specs [D] unless noted. Sustained figures are lower: commercial CXL devices reach 10–80% of local DRAM bandwidth [P: Wang et al.]. Energy entries state their scope.

| Tier | GB/s per GB | $/GB (relative to DDR5 = 1) | Energy per bit (scope) | Idle latency |
|---|---|---|---|---|
| On-die SRAM (scratchpad, not a capacity tier) | ~10⁵ (Groq LPU) [V] | ≫100 | 0.16–1.6 pJ, full access [P: Horowitz/Dally] | ns |
| HBM3E (36 GB stack ~1.2 TB/s; B200 192 GB / 8 TB/s) | ~33–42 [V] | ≥3× pre-shortage [S]; ~3× wafer per bit [P: Micron] | 3.92 pJ, full access (HBM2: 1.21 act + 2.24 on-die + 0.3 I/O) [P: O'Connor] | ~300 ns from GPU [S] |
| On-package LPDDR5X (Grace, GB200) | ~0.8–1.1 [V/D] | ≈1 [V] | vendor claims 1/8 of DDR5 per GB/s [V] | >200 ns [S] |
| MRDIMM-8800, 12 ch, 64 GB | ~1.1 [D] | >1 [R3] | — | ~DDR |
| DDR5-6400 RDIMM, 12 ch, 1DPC (64 → 256 GB DIMMs) | 0.8 → 0.2 [D] | 1 (3DS 256 GB at a premium) [P: Memstrata] | ~20 pJ I/O alone [P: Horowitz]; full-access scope [pend: Lee 2018] | 80–120 ns [P] |
| CXL expander (Gen5 x8/x16) | 0.125 peak/dir [D]; Vistara measured 0.225 [D from P] | new DDR5: ~1 + controller; recycled DDR4: 0.13 nominal, 0.3–0.6 all-in [P: Vistara; D: R2a] | DDR + SerDes, ~+10–25 [D, est.] | 214–394 ns measured [P] |
| RDMA far memory | ~0.01–0.1, configuration-dependent [D] | ~1 (gain comes from utilization) | — | ~3.5–3.9 µs per 4 KB [P] |
| NVMe SSD | ~0.001 [D] | ≪1 [unverified] | n/a | 10–50 µs [P] |

**What the table shows** [Syn]:

1. **Deployed CXL is not a new point in bandwidth density.** Meta's Vistara tier (0.225 GB/s per GB) equals capacity-maxed DDR (0.2). What separates it is $/GB.
   - B1 moves *down* the cost axis at DDR-like density.
   - A moves *right* along the density axis at higher $/GB.
2. **On-package LPDDR and MRDIMM also sit at DDR's density.** They lower $/GB or pJ/bit, or raise the ceiling slightly; they are the middle improving.
3. **Nothing ships in volume between ~1 and ~35 GB/s per GB**, i.e. between DIMM-class and HBM-class. SPHBM4 may be the first product aimed there [V]. This gap is a finding in its own right and a possible product opportunity.
4. **On the DDR bus, capacity and bandwidth trade against each other.**
   - Bigger DIMMs dilute bandwidth per GB.
   - Full speed requires one DIMM per channel.
   - Flagship CPUs held ~5 GB/s per core only by spending pins: cores 8 → 256 vs channels 4 → 16, 2012–26 [P/V: Q3]. Dense-core parts fell to ~3 GB/s per core.
   - The squeeze therefore shows up mainly as lost *capacity* and platform cost [P: Q3, Berger TC'26].
5. **Wiring geometry.**
   - Very wide parallel interfaces work at millimetre scale: HBM is 1024–2048 bits wide [V], and hybrid bonding reaches ~10⁵ connections/mm² [S/pend].
   - Serial links give ~4× bandwidth per pin per direction (~8× bidirectional) and metre reach, at about +50–70 ns per hop [P: COAXIAL, Das Sharma].
   - DDR is the *squeezed* middle, not an unstable one. Serial memory attach has a long graveyard: FB-DIMM, RDRAM, HMC, OMI. CXL Type-3 is the first serial memory attach to stick.

## 3. Placement: three properties per object, not one

- **Access density ρ** = bytes per second demanded per byte stored.
- **Arithmetic intensity I** = FLOPs per byte moved.
- **Latency tolerance**, set by memory-level parallelism (MLP) [P: OSDI'25 "Beyond Hotness"].

**How they combine:**

| Combination | Where the data belongs |
|---|---|
| High ρ, low I | A. Decode attention over live KV (~1 FLOP/B at any batch size; ×GQA group). Bulk scans and bitwise ops. |
| High ρ, high I | On-chip SRAM/cache. Prefill at ~1000 FLOP/B, dense GEMM. A2 gains only 1.0–1.3× here [V: Samsung]. |
| Low ρ, high MLP | B1/B2. Cold pages, most embedding rows, reusable KV. |
| Low ρ, low MLP (pointer chasing, B-trees, graph) | Stays on the DDR bus. GAPBS suffers most on CXL [P: Pond]. This is the latency-critical middle. |

**Low I alone does not decide placement.** Embedding gathers have the lowest FLOP/B (0.25), yet the large tables have *low* bandwidth demand and live in capacity tiers. The hot small tables are high-ρ [P: MTrainS]. Batched weight GEMVs scale with batch size, so the "~0.3% of GPU peak" decode figure is a batch-1 number from a PIM vendor [V: SK hynix SC23].

**Skew is pervasive but not universal.**
- Present:
  - fleet memory: 32% cold at 120 s at Google (range 1–61%); 55–80% idle per 2 min at Meta [P]
  - KV caches: >50% of blocks never reused [P: Mooncake]
  - embeddings [P]
- Absent: dense model weights, which are read in full every decode step. HBM capacity is sized to them.

**Economic placement rules.** The five-minute rule (capacity side) and the roofline (compute side) are both comparisons of an object's ρ against a tier's supplied density and price.
- The DRAM↔flash break-even interval shrank from ~15 min (2007) to ~41 s (NVMe 2017, 4 KB pages) [P]. A 2025 preprint gives ~34 s for CPU+DDR at 512 B, and ~5 s for GPU hosts with high-IOPS SSDs [P: Zhang 2025].
- *Tier boundaries* move: cheaper per-access far tiers make farther placement rational for more data, while a rising compute:bandwidth ratio puts more kernels below the roofline's ridge point.
- The CXL/RDMA rung of the five-minute rule is [pend: Hao et al. SIGMOD'24].

## 4. The 2×2: deployed vs ambitious forms of each direction

| | Driver | Strongest evidence | Pays with | Status |
|---|---|---|---|---|
| **A1. Proximity for bandwidth** (HBM, on-package, 3D-stacked) | Bandwidth under power and beachfront limits; pJ/bit as a bandwidth cap | O'Connor: HBM2 3.92 pJ/b with only 0.3 I/O [P]; FLOPs 3.0× vs bandwidth 1.6× per 2 yr [P]; LLM decode attention ~1 FLOP/B [P] | Capacity per $ and per mm² (HBM ~3× wafer per bit [P]); d-Matrix Raptor 32 GB, 1/6 of a 192 GB HBM4 config, for 5.6× bandwidth [S, HC'26]. The trade holds at *fixed cost and beachfront*: HBM per GPU still grew 80 → 288 GB | Deployed at scale. Base-die logic (TSMC N12/N3; Samsung 4 nm) is the fastest-moving part [V/S] |
| **A2. Compute at memory** (PIM/NDP, DRAM-on-logic compute) | Internal bandwidth 4–16× the pins [V]; HBM's 2.24 pJ/b on-die term is untouched by any link [P] | Aquabolt-XL >2× performance, >70% less energy [V]; UPMEM 23× vs a weak 4-core CPU [P]; NeuPIMs 3×, Duplex 2.67× (simulated) [P]; AttAcc [pend] | DRAM-process logic (3× slower transistors, 3 metal layers [V: UPMEM HC'19]); thermal (refresh doubles every ~10 °C above 85 °C [P]); no VM or coherence; activation energy stays (and may rise in all-bank mode [pend: Kwon ISSCC'21]); capacity penalty [pend] | Niche. HMC dead (2018); HBM-PIM at demo stage; LPDDR5X-PIM productizing 2026–27 [S]; UPMEM acquired by Qualcomm [S]. Baselines are often weak (Newton: 54× vs a real GPU, 10× vs an ideal non-PIM) [P] |
| **B1. Cheaper or extended capacity** (compression, tiering, single-host CXL, recycled DDR4) | $/GB, capacity per core, carbon, upgrade granularity, OOM reliability | Meta Vistara: recycled DDR4 at 0.13× $/GB nominal (0.3–0.6× all-in [D]); up to 25% fewer servers for capacity-bound ML inference; ~50% fewer OOMs [P]. Google zswap: ~20% of data, 4–5% memory TCO [P]. TMTS: 25% of DRAM on a slower tier at <5% slowdown [P] | Absolute bandwidth (Vistara tier ~10× lower; ~2.7–3.6× lower per GB [D]); latency +92% idle / +60% loaded [P]; tiering CPU cost (TPP used most of a core per VM [P: Memstrata]). Carbon gain can be thin: GreenSKU's CXL-reused DDR4 nets only +1% over an efficient-CPU baseline, and only 3% of traces touch the tier [P] | Deployed (Azure M-series [Pr]; Meta [P]; Google zswap and formerly Optane [P]). Optane discontinued 2022 [R3] |
| **B2. Sharing across hosts** (pooling, RDMA far memory, rack-scale) | Statistical multiplexing of demand (sum of peaks vs peak of sums) [P: Lim ISCA'09]; stranding | Pond: 7–9% less DRAM at 1–5% slowdown [P]; Mooncake RDMA KV pool in production (+75% requests) [P] | Switch cost and latency: Octopus finds switches add ≥220 ns and "always cost more" [P]; best switchless design nets 3.0–5.4% (simulated) [P]. Levis: break-even ~140 TB of server RAM; no gain at real VM sizes [P]. Tails +10–82% [P: Sun MICRO'23]; fault tolerance costs up to +35% memory [P: Carbink] | Niche. Bulk, read-mostly, predictable data (KV pools; PolarDB [pend]). The Machine, Gen-Z, Intel RSD dead; Gen-Z and OpenCAPI absorbed into CXL [P/Pr] |

**Pattern** [Syn]:
- The deployed cells (A1, B1) are the forms that need no change to the programming model. The ambitious cells (A2, B2) have reached volume only where a vendor controls the software stack, or where data is bulk and predictable.
- The critiques mirror each other: A2 is limited by process and heat; B2 by latency and switch cost.
- The same pattern held in the 1990s (§6).

## 5. Coupling: the two directions need each other

### 5.1 A creates demand for capacity tiers

- At fixed cost and beachfront, every step toward compute trades capacity for bandwidth: SRAM machines, HBM, DRAM-on-logic.
- Capacity has to come from a second tier:
  - **GB200 NVL72:** 13.4 TB HBM3E at 576 TB/s plus 17 TB LPDDR5X at 14 TB/s over coherent NVLink-C2C [V].
  - **GH200:** 480 GB LPDDR5X [V].
  - **LLM KV offload** to DRAM, SSD or RDMA pools [P].
  - **Training-side offload** (ZeRO-Infinity) [R3].
  - **Cerebras MemoryX** weight streaming [R3].
  - **Samsung aHBM roadmap:** adds external-memory-extension PHYs to the HBM base die [S, HC'26].
- **GPU scale-up domains are memory-semantic disaggregation inside the A market.** NVL72 makes 13.4 TB of HBM load/store-accessible across 72 GPUs. This is arguably the largest deployed far-memory system by bytes [R3].

### 5.2 A raises the price of B's inputs, and the payoff of B's cheapest forms

*Amplifier, not cause.*

**Mechanism** [P/D]:
- HBM and conventional DRAM share fab wafers.
- Micron puts the HBM:DDR5 wafer-per-bit ratio at ">2×" (Dec 2023) [P] and "3:1" (Dec 2025 call) [P/transcript].
- TrendForce shares show HBM rising from 18% to ~30% of wafers (end-2025 → 2027 forecast) for only 8% → 13% of bits [S]. This is *consistent with* Micron's ratio, not independent evidence of it.

**Price floor.**
- While HBM is supply-rationed, vendors are indifferent at P_DDR ≈ P_HBM / [r(1+c)], where r is the wafer ratio and c the extra back-end/test/base-die cost [D, after R2a].
- Floor in $/GB:

  | | r=2.5, c=0 | r=3, c=0 | r=3, c=0.2 | r=4, c=0.2 |
  |---|---|---|---|---|
  | HBM3E 2026, $10–16/GB [S] | 4.0–6.4 | 3.3–5.3 | 2.8–4.4 | 2.1–3.3 |
  | HBM4 2027, ~$34/GB [Pr] | 13.6 | 11.3 | 9.4 | 7.1 |

**Reading the floor:**
- At 2026 HBM3E prices the floor (~$3–6/GB) sits *below* actual DDR5 contract prices (~$14/GB in Q1'26 [Pr]). DDR5 RDIMMs out-earned HBM per wafer from Q1'26 [S: TrendForce].
- So the 2026 spike is a shortage above the floor. Its causes are several: AI demand for DDR5 and LPDDR, capex discipline, DDR4 end-of-life, and an alleged-collusion class action [Pr].
- The floor becomes structurally relevant only if HBM4-era prices hold. That makes it a forward risk, not today's driver.

**Who benefits.**
- Only B tiers that need no leading-edge wafers gain relative ground: recycled DDR4 behind CXL (bounded by server retirements; resale value $5–9/GB [S]) and compression.
- CXL with new DDR5 doesn't escape the price rise.
- SSD offload isn't spared either: NAND rose +55–60% QoQ in 1Q26 [Pr].

**Strength of the causal link to adoption:** two vendor statements (Marvell, an interested party) and press reports. No hyperscaler primary source ties CXL deployment to the shortage, and the B1 designs (Pond, GreenSKU, Vistara) predate it [P].

### 5.3 B creates demand for compute at the far tier

**Evidence:**
- Far memory has low bandwidth per GB and high latency, so dependent access chains and scans across it are slow. Miller et al. call offloading dependent accesses a "required optimization" for far memory [P: ACM Queue'23].
- **Teleport:** unmodified disaggregated execution is 5.4× slower on average, and pushdown improves on LegoOS by up to 11× [P].
- **PULSE:** 9–34× lower latency for pointer traversals vs disaggregated caching [P].
- **CXL-ANNS:** 3.8× throughput over an oracle all-DRAM system [P]. Its 111× figure is against SSD-based prior work.
- **Products, announced or sampling rather than deployed:** Samsung CXL-PNM [P/pend: HPCA'24], SK hynix CMS [V], Marvell Structera A (16 Neoverse V2 cores in a 5 nm CXL controller) [V], XCENA MX1 (3,072 RISC-V cores, because the link is only 64 GB/s per direction) [S].

**Synthesis and counter-point** [Syn]:
- A CXL controller is already a logic-process die beside DRAM. Compute there escapes A2's process and thermal objections.
- But it sees only channel-level bandwidth: Structera A has ~200 GB/s from 4× DDR5 against a 64 GB/s link, about 3×. It does not get bank-level internal bandwidth (4–16×). **It escapes PIM's objections by giving up PIM's main benefit.**
- The closest precedent, compute following the far tier in storage (computational storage, SmartSSD), saw limited adoption [R3].

## 6. History: the same split in the 1990s, and why the outcome differs now

The same apparent paradox occurred in the 1990s, often among the same people [P: R2c]:

- Patterson and Anderson co-authored both IRAM (1997) and NOW (1995).
- Oskin wrote Active Pages (1998) and later Grappa (2015).
- J. Chang co-authored memory blades (ISCA'09) and the 2014 NDP workshop report.

**The two sides attacked different baselines** [P]:
- IRAM targeted the off-chip bus: 98.5 nJ per off-chip access vs 4.55 nJ on-chip.
- Network RAM targeted *disk*. A GMS remote 8 KB page took 1.44 ms vs 3.6–14.3 ms from disk, and was >1000× slower than local DRAM.

**They were explicitly reconciled as a hierarchy.** IRAM papers specified backing on-chip DRAM with off-chip DRAM, paging, or networked IRAMs. The near-plus-far vision is the original one, not a recent synthesis.

**Both waves were killed by the same thing: fast commodity DRAM scaling** [P]:
- DRAM-process logic cost 30–70% in area and 30–100% in speed. PIM also broke the commodity model and needed a new programming model. DRAM prices crashed ($40 → $6 per 16 Mb part in 1996).
- In the 2014 NDP report's words: "Moore's law allowed the basic von Neumann machines to outrun them."
- Network RAM only beat disk, and cheap local DRAM (−33–36% $/bit per year) won.
- Least-disruptive forms survived:
  - Hardware CC-NUMA survived (SGI Origin, remote ~3.6× local) while software DSM died.
  - Embedded DRAM survived as caches and console framebuffers.
  - HBM beat HMC.

**Which kind of explanation the history supports.** The 1990s split did *not* follow market lines: the same Berkeley group pursued both. That favours the resource-pressure explanation over "different markets/communities" as the root cause [Syn].

**What is structurally different now:**
1. **The common killer is gone.** DRAM $/bit now falls ~12–15% a year and density grows ~2× per decade, while AI demand outgrows a package. IRAM's 1997 premise, that demand scales slower than capacity per chip, has reversed. **This is the best single explanation for why both directions are pursued *simultaneously*, rather than one being outrun.** [Syn]
2. **The far tier changed what it competes with.** It moved from "faster disk" to "slower DRAM": CXL at ~2× local latency is nearer than Origin's 3.6×. CXL expansion revives the NUMA branch that survived; RDMA far memory repeats the network-RAM/DSM branch.
3. **Packaging decoupled the processes.** 2.5D/3D and hybrid bonding remove the DRAM-process logic penalty, at least for base-die logic.
4. **An anchor workload exists.** AI inference runs on vendor-controlled software stacks, like the consoles where 1990s near-memory compute shipped.
5. **The ecosystem is standardised and owned.** CXL has absorbed the competing fabrics, and hyperscalers control their OS and hypervisor.

## 7. Alternative explanations, weighed

**(a) Different markets and buyers.** *Partly true, and first-order for who pursues what.*
- Most A evidence comes from AI accelerators and mobile SoCs. Boroumand's 62.7% data-movement energy share is a 2018 mobile-SoC figure [P/partial].
- Most B evidence comes from CPU cloud fleets.
- But the strongest "both at once" cases sit inside single workloads: LLM serving, recommendation (DLRM), and GB200's two memory tiers.
- History also shows one group pursuing both.
- So the frontier argument explains *why* the markets split the way they do.

**(b) Vendor incentives and hype.** *Real, and complementary.*
- Memory makers want out of commodity $/bit pricing. Base-die logic is moving to foundries (TSMC N12/N3), which shifts who captures value [V/S; R3]. Hence Samsung's push for PIM and aHBM.
- Controller vendors and startups (Astera, Marvell, Montage, XCENA, d-Matrix, Panmnesia, UnifabriX) gain a whole product category.
- Hype-cycle residue on both sides (HMC, The Machine, Gen-Z, Intel RSD) shows incentives alone don't make a direction work. HBM and CXL expansion, though, are deployed at scale.

**(c) Different communities and evaluation norms.** *Context.*
- PIM papers lean on simulation with weak baselines. Disaggregation papers lean on production traces and TCO.
- This partly explains why the two look "opposite" from outside. No paper we found treats near vs far as one design axis [P: Q5 search].
- But ASPLOS publishes both (Pond, TPP, AttAcc, NeuPIMs, CENT), and Samsung and Microsoft work on both.

## 8. Answer to the research question

Both directions attract serious researchers and companies because **the baseline DDR DIMM has stopped being a good compromise for the two resources it couples**, and because real data is skewed enough that splitting the difference is wasteful.

- **For bandwidth-dense, low-intensity data:**
  - The DDR bus supplies too few bytes per second per byte stored.
  - Energy per bit caps how much bandwidth a power budget can buy.
  - Compute keeps outgrowing it.
  - Shorter, wider wires (A1) improve bandwidth density and pJ/bit together. Compute at the DRAM (A2) goes after the energy that remains inside the die.
- **For capacity-dense, cold data:**
  - The DDR bus is too expensive per byte.
  - It can't add capacity without diluting bandwidth or spending pins.
  - It allocates memory per socket.
  - All this at a time when memory is ~37% of rack TCO and DRAM $/bit no longer falls fast.
  - Cheaper media, compression and serial attach (B1) fix cost. Pooling (B2) fixes stranding, but pays only at scale.
- **"Closer" and "farther" are geometric labels for moves along two cost axes**, bandwidth density and $/GB, under a latency constraint. They are opposite in geometry and aligned in purpose. The result is **a wider hierarchy around a persistent, latency-critical middle**.
- **Why both, and why now:** in the 1990s, cheap and fast-scaling commodity DRAM let the middle outrun both ends. That no longer happens.

**Genuine tensions, not mere complementarity:**
1. **Wafers:** A's growth raises B's media cost while HBM is rationed.
2. **PCIe lanes:** CXL bandwidth expansion needs ~64 Gen5 lanes [P/V: Micron/Intel], the same lanes GPUs and NICs use.
3. **Rack power:** Vistara's CXL tier draws 30 W per 256 GB [P], and HBM GPUs dominate the rack budget.
4. **Software investment:** tiering kernels vs PIM programming models.

## 9. Implications and open questions

**Specify memory products by frontier position.** The useful coordinates are GB/s per GB, $/GB, pJ/bit with scope stated, and idle and loaded latency, rather than attach point. The empty band between ~1 and ~35 GB/s per GB is a candidate opportunity; SPHBM4 may be the first product aimed at it.

**Prediction, hedged** [Syn]:
- Passive HBM with logic base dies, and single-host CXL expansion, persist.
- A2 and B2 stay niche outside vertically integrated AI stacks.
- Compute at the far tier (CXL controllers) will grow, but computational storage is a cautionary precedent. DRAM-die LPDDR5X-PIM may still productize first, in mobile.
- Each direction weakens if its escape valve reopens. 3D DRAM restoring steep $/bit declines would hurt B. Continued HBM bandwidth scaling would "outrun" in-bank PIM again.

**Open questions:**
1. Pooling break-even at 2026–27 DRAM prices: redo Levis with $14–25/GB and current switch costs.
2. Is the HBM price floor structural (HBM4-era prices) or cyclical?
3. A placement theory joining the five-minute rule, the roofline and MLP: the ρ × I × MLP model.
4. Can DRAM-on-logic's capacity penalty (1/6) be offset by CXL or LPDDR capacity tiers at system level?
5. Does anything belong in the 1–35 GB/s per GB gap?

## 10. Evidence quality

**Mostly primary, full-text reads:**
- Pond, TPP, Vistara, Memstrata, GreenSKU, Octopus
- Levis, Gholami, O'Connor, Horowitz, PrIM, Teleport, Mooncake
- Berger TC'26 and the historical papers

**Secondary or vendor (tagged inline):**
- Hot Chips 2026: d-Matrix Raptor, Samsung aHBM, LPDDR5X-PIM, XCENA
- Aquabolt-XL slides
- DRAM price data and forecasts
- Groq and NVIDIA specs

**Pending full text, which would change specific claims:**
- Hao SIGMOD'24: CXL rung of the five-minute rule
- Kwon ISSCC'21: PIM capacity, area and activation energy
- AttAcc
- Lee 2018 and UniIC: energy scope
- Micron ">4:1"
- Berger IEEE Micro'23
- ByteDance EuroSys'24
- PolarCXLMem

**Known weaknesses:**
- PIM/NDP speedups are often measured against weak baselines.
- The Meta bandwidth-tiering result [P: Mahar] is emulated, covers one workload, and uses an assumed cost. Its "fast" tier is a better DDR DIMM, i.e. the middle improving.
- The density table uses peak specs. Measured CXL bandwidth is lower.
