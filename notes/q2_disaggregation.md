# Q2: The case FOR direction (B): memory disaggregation, far memory, CXL expansion and pooling

Compiled 2026-10-02. Provenance tags:
- **[full text]**: number checked against the paper's PDF text (pdftotext + grep or read by me).
- **[abstract only]**: taken from the abstract or landing page only.
- **[secondary]**: taken from press, news, or a search-engine summary. Not verified against a primary source.
- **[unverified]**: plausible but not traced to any source.
- **[pending full text]**: depends on an item listed under FULL TEXT NEEDED.

---

## Round-4 deltas (2026-10-03, after full texts were supplied)

All five FULL TEXT NEEDED items were read in full from `fulltext/`, except the Borg/Tirmazi trace paper (not provided). "p." below means the PDF page; for proceedings page numbers, add the offset noted per paper.

**D1. Berger et al., IEEE Micro'23 (`05-cxl-pools.pdf`) [full text]**
- Not new data. It restates Pond's stranding and latency-sensitivity analysis (p.3–4) and adds a hardware **latency + cost/ROI model**.
- **"50%" claim is circular.** "DRAM can be **over** 50% of server cost [2]", where [2] = Pond (p.1), and Pond cites the 2020 Next Platform article. The cost model then *assumes* "DRAM memory … approximately half of the total cost" (p.7).
- **Stranding.** "up to 30% of DRAM becomes stranded" (p.1). Median 6% at 75% of cores scheduled, >10% at ~85%. P95 25%, individual outliers >30% (p.3). VM memory bandwidth is generally low: "average bandwidth utilization below 10 GB/s" (p.3).
- **Latency model (Fig. 3, p.5–6).**
  - 8/16-socket pools on multi-headed devices (MHDs): **155–180 ns load-to-use (182–212% of NUMA-local)**, i.e. +75–90 ns.
  - Switch-only designs: a further +23–38%.
  - 64-socket rack pools: **270–345 ns (318–405%)**.
  - Multi-rack: >395 ns (>465%).
  - Each switch adds ≥70 ns; each retimer adds ~10 ns each way.
- **Observation 1 (p.5).** ">25% of datacenter memory needs to remain local". It explicitly does "not recommend fully disaggregating compute and memory". Pond's real system put on average **35–44%** of DRAM on pools (p.8).
- **Cost model (p.6–7).** Component costs as % of a non-pooled server's bill of materials: MC 0.4%, MHD8 0.8%, MHD16 2.0%, 16-port switch 1.6%, retimer 0.02%. Enclosure/PCB/cables at a 0.52× multiplier.
  - Switched 8-socket pool ≈ **2.5×** the cost of an MHD 8-socket pool.
  - "**most switch-based configurations are ROI negative, while many MHD-based configurations are ROI positive, especially for smaller pool sizes**" (p.7).
  - Read off Fig. 5 [approx.]: the savings line sits at ≈3–4% (25% pool memory) to ≈6–7% (50% pool memory) for 16–64 sockets.
- **Conclusion (p.8).** "small pools, spanning up to 16 sockets, can lead to significant DRAM savings … large pools [may be] counterproductive". The authors also note that DDR4 reuse behind CXL is a separate saving outside their model.

**D2. Tang et al. (ByteDance), EuroSys'24 (`26913_…pdf`; proceedings p. = PDF p. + 817) [full text]**
- **Scope.** CXL 1.1 **single-host expansion** on 2 servers with Astera A1000 cards, versus a baseline server. Not production fleet data, and not pooling.
- **Device measurements (p.5–6).** Idle latency 250.42 ns. Max bandwidth 56.7 GB/s at 2:1 read:write. From the remote socket: 485 ns and 20.4 GB/s (a Sapphire Rapids snoop-filter limit).
- **KeyDB (p.10).** Entirely on CXL: read latency +9–27% and throughput −12.5%.
- **Illustrative VM revenue example (p.10).** At a 1:3 vCPU:GB server, CXL lets the provider sell the stranded 25% of vCPUs at a 20% discount. That gives "~27% improvement in total revenue". This is arithmetic, not measured.
- **Spark TPC-H (p.9).** Two CXL servers vs three DRAM-only servers. MMEM/CXL interleaving is **1.4–9.8× slower** than MMEM-only, but faster than spilling to SSD. Kernel hot-page promotion: >34% slowdown.
- **LLM CPU inference (p.11).** 3:1 interleave **+95%** serving rate at 60 threads. Caveat: this ran with **SNC-4, i.e. only 2 DDR5-4800 channels per domain** (p.3), so the gain is inflated relative to a full socket.
- **Correction to "ByteDance TCO savings".** The "**25.98% TCO saving / 32.71% fewer servers**" (p.13) comes from the *Abstract Cost Model* run with **example parameters** (Rd=10, Rc=8, C=2, Rt=1.1). It is not a measured or production figure. The model is single-application, and it excludes controller, switch and PCB costs.

**D3. PolarCXLMem, SIGMOD-Companion'25 (`3722212.3724460.pdf`, DOI 10.1145/3722212.3724460; proceedings p. = PDF p. + 688) [full text]**
- **Testbed (p.4, p.7).** An "**in-house built physical prototype rack**". One XConn XC50256 CXL 2.0 switch (256 lanes, 2 TB/s), "8 DDR5 memory modules, totaling 2TB". Hosts have 2× Xeon 8575C. Baseline: PolarDB's RDMA disaggregated memory over 100 Gb ConnectX-6.
- **"Commercially deployed" refers to PolarDB, the database, not to the CXL pool.** The paper reports **no production deployment, fleet scale, or TCO**. The production claim ("PolarDB DMP feature") exists only in Alibaba's blog [secondary].
- **Latency, Table 1 (p.4), MLC.**
  - DRAM: 146 ns local / 231 ns remote.
  - CXL without switch: 265 / 346 ns.
  - **CXL with switch: 549 ns local / 651 ns remote (3.76× / 2.82× DRAM).**
  - The blog's "sub-100 ns" is **not supported**.
  - RDMA vs CXL transfer, 64 B: ~4.5 µs vs ~0.78 µs (Table 2).
- **Memory saving vs RDMA.** **30%** in the evaluated configuration, because RDMA keeps a local buffer at 30% of disaggregated memory and CXL needs none (p.8). The "50%" is the overhead of an RDMA configuration with a 50% local buffer (p.4). Both comparisons are **relative to RDMA disaggregation, not to conventional servers**.
- **Throughput.** Up to **2.1×** (pooling) and **1.55×** (sharing) vs RDMA (abstract, p.1). The win comes from removing RDMA page amplification and NIC limits: RDMA saturates at ~11 GB/s (p.8). CXL-resident buffer pool vs local DRAM: within ~7–10% (p.6).
- **Topology figure (p.6).** A memory box of "up to 64 memory devices (16TB)". This is a design envelope, not a measured deployment.

**D4. Amaro et al., Fastswap/CFM, EuroSys'20 (`12-fastswap.pdf`) [full text]**
- **Testbed, 9 nodes, Table 2 (p.10).** Converting one compute node into a memory server (**Far +0%**, same total memory) improves makespan **5–15%**. Far +33% improves it up to **28%**.
- **Job slowdown (p.10).** Median 0.2–13.4%, p99 28–45%.
- **40-server rack simulation (p.11).**
  - CPU-bound mixes (m2c 0.75–0.85) **lose 6%**.
  - Far +0% gains a median 3% (m2c ~1.2) and 11% (m2c ~1.6).
  - Gains plateau at a median 12% (m2c ~1.2) and reach **47%** with Far +37.5% (m2c ~1.6).
- **Key negative result (p.11).** For equal purchased capacity, **adding local DRAM beats far memory**: makespan is 2.5% better (+25%) and 9.7% better (+50%). Far memory's advantage is *granularity and cost of the increment*, not efficiency.
- **Granularity (p.2–3).** The smallest balanced upgrade of a 192 GB (12×16 GB) server is +25%. From 24×8 GB, the next step is +50%, which means buying 192 GB and discarding 96 GB. One DIMM per controller gives 35% of maximum bandwidth.
- The slides' "10–40%" is replaced by the paper numbers above.

**D5. "~60% average memory utilization (Google, Alibaba)": provenance [partial]**
- AIFM cites **Tirmazi et al. EuroSys'20 (Borg 2019 trace; not provided)** and **Lu et al., "Imbalance in the cloud", IEEE BigData'17 (not provided)**. Carbink cites the same two.
- The Alibaba figure rests on **cluster-trace-v2017: ~1,300 machines over 12 hours** (alibaba/clusterdata README, parsed from the supplied mhtml).
- Cheng et al. 2018 (arXiv 1808.02919) [full text] analyses the same trace (1,313 machines). Memory utilization is "above 50% in majority of the time". Machines have >50% memory usage "over 55% of the time". CPU is mostly 10–30%. On 74% of machines, 60–80% of memory is *reserved* for containers. It cites Google's 2011 trace as CPU and memory "capped at 60% and 50%".
- **Implication.** "~60%" is a loose rounding from a 12-hour, single-cluster, colocated 2017 snapshot plus the Borg 2019 trace. Treat it as **illustrative, not a fleet statistic**.
- Still pending: Tirmazi'20 and Lu'17.

**Net effect on Q2 conclusions**
- The pooling case is weaker and more specific than the CXL literature implies.
  - Microsoft's own model says **only small (≤16-socket), MHD-based, switchless pools have positive ROI**, at single-digit-% savings.
  - Alibaba's switched pool is a **prototype benchmarked against RDMA**, not against local memory.
  - ByteDance's "26% TCO" is a parametrised example.
- The expansion/tiering conclusion stands.

---

## FULL TEXT NEEDED (status after round 4)

1. Berger et al. IEEE Micro'23: **RESOLVED** (D1).
2. Tang et al. EuroSys'24: **RESOLVED** (D2).
3. PolarCXLMem SIGMOD'25: **RESOLVED** (D3). Production status remains [secondary].
4. Fastswap EuroSys'20: **RESOLVED** (D4).
5. ~60% utilization basis: **PARTIAL** (D5). Still needed: Tirmazi et al., "Borg: the Next Generation", EuroSys'20 (DOI 10.1145/3342195.3387517), and Lu et al., "Imbalance in the Cloud: An Analysis on Alibaba Cluster Trace", IEEE BigData'17. Needed for the exact average memory-utilization numbers and their definition (usage vs allocation).
6. "50% of server cost" primary basis: **UNRESOLVED but characterised.** Berger → Pond → Next Platform 2020. No Azure primary data exists in any of the three. Treat it as a citation chain, not a measurement.

## 1. Stated motivations, with quantitative evidence

### 1.1 Memory is a large and growing share of cost, power, and carbon

| Claim | Number | Source | Tag |
|---|---|---|---|
| DRAM share of server cost (Azure) | "DRAM can be 50% of server cost" (Pond). Berger IEEE Micro'23 says "over 50%", citing Pond, and its cost model *assumes* DRAM ≈ half of server cost | Pond, ASPLOS'23, §1 cites a Next Platform article (2020). Berger p.1, p.7 | [full text]. Circular citation chain, no primary Azure data (D1) |
| Memory share of rack TCO and power at Meta, across 6 HW generations | Cost: 15.6 → 25.5 → 21.6 → 21.2 → 31.8 → **37.1%** (Gen0→Gen5). Power: 14.6 → 19.8 → 19.7 → 20.7 → 28.8 → **33.3%** | TPP (Maruf et al., ASPLOS'23), Fig. 3. I rendered the figure to confirm that the orange series is Cost and the black is Power | [full text] |
| Memory share of server cost per HotNets'23 critique | "40% for Meta, 50% for Azure" (citing TPP and Pond) | Levis, Lin, Tai, HotNets'23 | [full text] |
| Google | "DRAM is a large and growing portion of infrastructure spend… growing faster than other system components including CPU and storage. By some estimates, DRAM will be what limits cost-effective compute capacity" | TMTS (Duraisamy et al., ASPLOS'23), §1 | [full text] |
| Google (2019) | "cost per GB of DRAM" no longer falling, plus "explosive growth in DRAM demand"; memory TCO is a primary concern | Lagar-Cavilla et al., ASPLOS'19, §1 | [full text] |
| DIMMs as single biggest server cost item (Azure) | "These DIMMs are the single biggest contributor to server cost". For 128–288-core CPUs or large VMs, 3D-stacked (3DS) DIMMs are required, "which adds a multiplicative factor to per-GB memory cost" | Zhong et al. (Memstrata), OSDI'24, §2 | [full text] |
| Carbon: DIMMs share of server embodied carbon (Azure) | **41%** | Memstrata OSDI'24, §2 (citing GreenSKU and others) | [full text] |
| Carbon: DRAM share of compute-server emissions (Azure) | DRAM **35%**, SSD 28%, CPU 24% of compute-server emissions. Compute servers are 57% of DC emissions at Azure's 40–80% renewable mix | Wang et al., "Designing Cloud Servers for Lower Carbon" (GreenSKU), ISCA'24, §2 | [full text] |
| Carbon: DIMM share of server lifecycle emissions (Meta) | **69%** of per-server lifecycle emissions are DIMMs (CPU 16%) | Vistara (Gholkar et al., Meta), ISCA'26, Fig. 3 | [full text] |
| Historic baseline (2009) | Memory "trending higher from its current value of about 25%" of total cost and power | Lim et al., ISCA'09, §1 | [full text] |
| 2026 market context | DRAM contract prices +80–90% QoQ in Q1 2026 (Counterpoint). Hyperscalers expected to spend ~30% of budgets on memory in 2026, up from ~8% in 2023–24. Google confirmed reusing DDR4 from retired servers (Nikhil Cherian, supply-chain sr. director) | Cryptopolitan, 2026-09-03, quoting Counterpoint, Marvell, TrendForce | [secondary]. The 30%-vs-8% figure is especially unverified |

**Interpretation:** the motivation has moved from *$ (capex)* (2009–2023) toward *$ + embodied carbon* (2023–2026), and in 2026 toward *DRAM supply scarcity*. Meta's and Microsoft's carbon accounting both put DRAM as the largest single server component.

### 1.2 Stranding, low utilization, and imbalance (the core pooling argument)

| Claim | Number | Source | Tag |
|---|---|---|---|
| Azure stranded DRAM | Median snapshot **6%** stranded when 75% of cores are rented. Median >**10%** at ~85% of cores. 95th percentile: **3–27%** across clusters, some outliers at 30%. "Up to 25% of DRAM becomes stranded as more cores become allocated" | Pond §3.1 (100 clusters, 75 days) | [full text] |
| Azure untouched memory | "~50% of all VMs touch less than 50% of their rented memory". Even in the cluster with the least untouched memory, >50% of VMs have >20% untouched | Pond §3.2 | [full text] |
| Pooling savings, trace simulation | With a fixed 50% pool fraction: 32-socket pool saves **12%** of DRAM and 64-socket pool saves **13%**. Diminishing returns; 8–16 sockets get most of the benefit | Pond §3.1, Fig. 3 | [full text] |
| Pond end-to-end | Reduces DRAM needs by **7%** (16-socket pool) at 182%-latency CXL, and by **9%** / **7%** under 182% / 222% latency. Performance within 1–5% of NUMA-local. "hundreds of millions of dollars" for a large cloud | Pond abstract, §1, §6.5 | [full text] |
| Google and Alibaba cluster memory utilization | Average server ~**60%** memory utilization (vs ~40% CPU), "with substantial variance" | AIFM (OSDI'20) §1 and Carbink (OSDI'22) §1, both citing Tirmazi'20 (Borg 2019) and Lu'17 (Alibaba v2017 trace: ~1,300 machines, 12 h). Cheng'18 on the same Alibaba trace: memory "above 50% in majority of the time" | [full text] of citing papers and Cheng'18. Primary Borg/Lu not read. Illustrative only (D5) |
| Google and Alibaba traces (LegoOS) | "only around half of the CPU and memory are utilized" | LegoOS (Shan et al., OSDI'18) §2, Fig. 4 | [full text] |
| Imbalance | ">70% of the time there exists severe imbalance in memory utilizations across machines" (two production clusters) | Infiniswap (Gu et al., NSDI'17) §1 | [full text] |
| Cold memory, Google | **32%** of memory cold at a T=120 s threshold (fleet average). Varies **1%–52%** across machines in the same cluster. Deployed far memory (zswap) stores **~20%** of data on average, at a 67%+ cost reduction for that tier, ~6 µs access latency. **4–5% memory TCO savings**, "millions of dollars" | Lagar-Cavilla et al., ASPLOS'19 | [full text] |
| Cold memory, Meta | **55–80%** of an application's allocated memory idle within any 2-minute window (four production apps) | TPP §1, §3 | [full text] |
| Cold memory, Meta 2026 | Page idle-time P50: Ads 28.3 min, Cache 19.4 min, Web1 2.1 min, Web2 1.7 min. "most pages are cold" | Vistara Table I | [full text] |
| OOM kills (Google) | 790k jobs per month had ≥1 instance killed, often due to memory pressure | AIFM §1 (citing Borg trace) | [full text] of citing paper |
| HotNets critique | "modern servers are large relative to most VMs; even simple VM packing algorithms strand little memory". On Google 2019 trace, pooling across ≤16 machines gives no utilization gain until VMs are ≥32× larger, and then <5%. Azure needs ~8× larger VMs | Levis et al., HotNets'23, §4 | [full text] |

**Interpretation:** the stranding argument is real but modest for VM-packing clouds (Pond's realised saving is 7–9% of DRAM). Cold or untouched memory (30–80%) is a much bigger lever, and it can be exploited **without disaggregation**: by tiering (Optane, CXL expansion) or in-node compression (zswap). This is why the industry pivoted from pooling to expansion and tiering (§2.4).

### 1.3 Capacity per core and the DIMM granularity / pin wall

- **Lim et al., ISCA'09 [full text]**: projected memory capacity per core "dropping by **30% every two years**", from cores-per-socket growth outpacing DRAM capacity per socket and fewer DIMMs per channel. Memory blade gives **~10× average speedup** in memory-constrained environments, and **up to 87% better performance-per-dollar** via sharing across servers ("sum of peaks" vs "peak of sums" provisioning).
- **Memstrata OSDI'24 [full text]**: 60–96-core CPUs with SMT need 8–12 dual-rank 64/96 GB DIMMs to reach 4–8 GB per vCPU. 128–288-core CPUs force 3DS DIMMs, which carry a multiplicative $/GB premium. CXL lets you add capacity with "multiple smaller and cheaper DIMMs".
- **Vistara ISCA'26 [full text]**: "approximately 40% of servers … are memory-capacity bound". Fig. 1: **43.7–43.8%** of servers are bottlenecked by memory and 50.0% by cores. On older SKUs, memory-bound services "strand 25–40% of available CPU resources". Utilization for memory-bound jobs drops to 25–35% on 64 GB SKUs vs 128 GB SKUs.
- **Sun et al., MICRO'23 [full text]**: "DDR5 requires 288 pins per channel". A PCIe 5.0 ×8 CXL device uses "~3× fewer pins than DDR5". Max-bandwidth DDR configs force 1 DIMM per channel. Capacity and bandwidth are both functions of channels × DIMMs/channel × rate, and all three are hitting limits.
- **Fastswap/CFM, EuroSys'20 [full text]**:
  - Balanced-DIMM rules make local upgrades coarse: +25% or +50% steps, the latter discarding DIMMs. One DIMM per controller gives 35% of max bandwidth.
  - Same total memory, one compute node converted to a memory server: makespan +5–15% (9-node testbed).
  - Rack simulation: median +3–11%, up to +47% for heavily memory-bound mixes, and −6% for CPU-bound mixes.
  - For equal purchased capacity, local DRAM beats far memory by 2.5–9.7% makespan. Far memory wins on increment size and cost, not efficiency (D4).

### 1.4 Independent scaling, lifetime decoupling, reuse

- **Lifetime mismatch**: DRAM's useful life is **7–10 years** vs servers' **3–5 years** (Vistara §1) [full text]. CXL decouples the memory technology from the CPU's DDR generation (TPP §2, Sun §1, Memstrata §2) [full text].
- **DDR4 reuse economics (Meta, production)**: Vistara Table V. CXL tier = 256 GB DDR4-2400 behind 2 ASICs; 57.6 GB/s; 30 W. Relative to local DDR5: **Cost/GB 0.13×, Power/GB 0.7×**. Local tier: 768 GB DDR5-6400, 614 GB/s, 132 W [full text].
- **DDR4 reuse carbon (Microsoft)**: GreenSKU-CXL replaces 30% of memory with reused 32 GB DDR4 via CXL. Result: **+11% more per-core embodied savings** but **−6% operational savings** (controller power). **Net improvement only +1%** over GreenSKU-Efficient. The headline 28% per-core emissions cut comes mostly from efficient CPUs and the memory:core ratio (9.6→8), not from CXL. The full GreenSKU reduces Azure net emissions by ~8% [full text]. Memstrata cites "attaching 40% of memory by reusing DDR4 can save over 20% of server embodied carbon" [full text, citing GreenSKU].
- **Liveness of the CXL tier**: in GreenSKU's Azure traces, "only 3% of traces have a memory utilization that would require using CXL". Max memory utilization is mostly <60% [full text]. The CXL tier is insurance capacity, rarely touched.

### 1.5 Failure isolation, elasticity, reliability

- **LegoOS OSDI'18 [full text]**: splitkernel. Goals are "resource utilization, elasticity, heterogeneity, and failure isolation". vs. monolithic Linux servers that hold the whole working set, LegoOS is "only 1.3× to 1.7× slower with 25% of application working set available as DRAM cache at processor components" (Phoenix, TensorFlow) [full text].
- **AIFM OSDI'20 [full text]**: "Memory is the most constrained… and the most inelastic resource". Up to **61×** faster than Fastswap via object-granular far memory.
- **Carbink OSDI'22 (Google) [full text]**: fault tolerance is a precondition for far memory. Erasure coding gives 29% lower tail latency and 48% higher app performance than Hydra, at ≤35% more remote memory.
- **Vistara [full text]**: CXL expansion gave **~50% reduction in OOMs** for certain ML training and serving workloads. Fleet-level job-failure reduction of **33%** [secondary; The Register].

### 1.6 Network and latency feasibility (when is far memory viable at all?)

- **Gao et al., OSDI'16 [full text]**: with 25% local memory, keeping degradation ≤~5% needs **3–5 µs end-to-end latency and 40–100 Gbps**. Bandwidth is "easily" met; latency is the hard constraint.
- **Infiniswap NSDI'17 [full text abstract + body]**: RDMA paging gives 4–15.4× throughput and 5.4–6× lower median/tail latency vs disk paging. This is the baseline for page-granular far memory over RDMA.
- **Load-to-use latency ladder, P50 on Xeon 6 / AMD Turin (Octopus, 2025) [full text]**: local DDR5 ~115 ns. CXL direct expansion **230–270 ns**. 2/4-port multi-headed device **260–300 ns**. CXL switch **490–600 ns**. RDMA via ToR **3,550 ns**.
- **Pond's projections [full text]**: 8–16-socket pools add **70–90 ns** over NUMA-local. Rack-scale adds >180 ns. Workload sensitivity at +182% latency: 26% of 158 workloads <1% slowdown, a further 17% <5%, and **21% >25% slowdown**. At +222%: 23% + 14% <5%, **>37% suffer >25%**.
- **Memstrata/Intel Flat Memory Mode [full text]**: hardware (cache-line) tiering gives ≤5% degradation for >82% of workloads (73% with HW tiering alone, 82% with Memstrata). Outliers degrade up to 34%.
- **Vistara production latencies [full text]**: idle local ≈130 ns vs CXL ≈250 ns. ASIC idle round-trip ≈50 ns. ≈150 ns extra from controller/PHY/bridge. CXL loaded latency 372 ns vs native 234 ns at the measured utilization. They report tail latency "comparable to local DRAM even under high contention". TPP overhead <0.5%.

---

## 2. Industry, products, deployments

### 2.1 Standards lineage
- **CXL 1.0/1.1 (2019)**: device attach, Type-3 memory expanders.
- **CXL 2.0 (2020)**: single-level switching, multi-logical devices, pooling.
- **CXL 3.0 (2022)**: fabrics, multi-level switching, memory sharing, PCIe 6.
- **CXL 3.1 / 3.2**: 2023 / 2024. [unverified exact dates, standard knowledge]
- **CXL 4.0, released 18 Nov 2025 at SC25**: PCIe 7.0, 128 GT/s. "Bundled ports" let one device attach via several ×16 links: 768 GB/s per direction, ~1.5 TB/s total per device. Native ×2 width for fan-out. Up to 4 retimers for longer reach (multi-rack). [secondary: CXL Consortium release, Blocks&Files]
- **Consolidation**: Gen-Z signed an LOI in Nov 2021, and its assets transferred to CXL in early 2022 (DCD, Wikipedia). OpenCAPI and OMI transferred to the CXL Consortium on **1 Aug 2022** (HPCwire, The Register). [secondary] CXL absorbed every competing coherent/memory-semantic fabric.
- **Earlier attempts**:
  - Lim et al. memory blade (HP Labs + Michigan, ISCA'09) [full text].
  - **HPE The Machine**: May 2017 prototype with **160 TB** shared memory across 40 nodes, Gen-Z based, never productised [secondary: Top500, The Register].
  - Intel Optane as a cheaper tier: TMTS put **25% of memory on Optane** in Google production with <5% degradation [full text]. Optane was discontinued in 2022 [unverified date here, widely known], which left CXL-DRAM as the tier-2 medium.

### 2.2 Controllers and modules
- **Astera Labs Leo** (CXL 2.0, up to 2 TB per controller, ">1.5×" capacity scaling). Used in **Azure M-series CXL preview, announced 18 Nov 2025**, billed as the "industry's first announced deployment of CXL-attached memory" in a public cloud. Targets: in-memory DBs, analytics, KV cache, recommendation. [secondary: Astera press release]
- **Meta Vistara** (in-house ASIC; ISCA'26) [full text]:
  - CXL 2.0/1.1 Type-3, PCIe Gen5 ×16 (×8 per device in the MemServer).
  - 2× 72-bit DDR4 channels, up to 3200 MT/s (production: 2400), up to 256 GB/chip, RS chipkill ECC.
  - MemServer: AMD Turin, 768 GB DDR5 + 256 GB DDR4-via-CXL = 1 TB; CXL peak ≈76 GB/s vs local 614 GB/s.
  - Deployed across "millions of servers" in production. Up to **25% fewer servers** for disaggregated ML inference (recsys parameter servers). **29% lower average latency** for distributed caches.
- **Micron CZ120/CZ122**: E3.S, PCIe5 ×8, ~36 GB/s per module (MLC 2:1) [secondary: Micron PR / StorageReview].
- **Samsung CMM-D**: 2.0 samples at 128/256 GB, 36 GB/s. Samsung claims "+50% capacity, up to +100% bandwidth". CXL 3.1 version targeted for late 2025 or 2026; production possibly slipping into 2027 [secondary: TrendForce, Korea Herald].
- **SK hynix CMM-DDR5**: 96 GB customer validation in Apr 2025. "+50% capacity, +30% bandwidth" vs conventional DDR5 modules, 36 GB/s. A 256 GB CXL 3.2 sample was shown in June 2026 [secondary].
- **Marvell Structera** [secondary: Marvell, ServeTheHome]:
  - **Structera X**: DDR4-reuse expander, 4 channels × 3 DIMMs/channel = up to 12 DIMMs per controller.
  - **Structera A**: CXL controller **with 16 Arm Neoverse V2 cores**, LZ4 compression, and encryption; 4× DDR5-6400; ~200 GB/s. A near-memory-compute device sitting on the disaggregation interface.
- **Montage** CXL MXC controllers [not researched in depth].

### 2.3 Pooling and fabric appliances
- **Liqid EX-5410C**: CXL 2.0 memory platform, up to 40 TB per chassis, >100–160 TB pools [secondary].
- **UnifabriX MAX**: CXL 1.1/2.0, 16+ hosts [secondary].
- **Panmnesia**: CXL 3.1 switch with port-based routing (PBR) and multi-level cascade; claims sub-100 ns ("double-digit ns") controller IP; PCIe 6.4 / CXL 3.2 fusion switch sampling 2026 [secondary].
- **Octopus (UW + Azure + Columbia, 2025) [full text]**: a sparse topology of 4-port multi-headed devices. Net server cost savings **3.0%** (no prior CXL) to **5.4%** (if CXL expansion already present). "CXL switches always cost more, even after factoring in their pooling savings". RPCs over the pod are 3.2× faster than in-rack RDMA.
- **Alibaba PolarCXLMem (SIGMOD-Companion'25) [full text]**:
  - Built on one XConn XC50256 CXL 2.0 switch with 2 TB of DDR5, in an *in-house prototype rack*.
  - Switched CXL latency 549 / 651 ns (local / remote), 3.76× local DRAM. Not "sub-100 ns".
  - Up to 2.1× (pooling) and 1.55× (sharing) throughput **vs RDMA-based disaggregation**. 30% less memory than RDMA because there is no local buffer.
  - No production scale or TCO reported. Production use ("PolarDB DMP") and "16–32 TB per instance" rest only on Alibaba's blog [secondary] (D3).

### 2.4 What actually got deployed
1. **Google**: in-node compressed far memory (zswap, since ~2016; 4–5% memory TCO) and an Optane tier (TMTS, 25% of DRAM replaced). Both are "far memory" without disaggregation. A Google author (Levis) published "A Case Against CXL Memory Pooling". In 2026 Google is recycling DDR4 [secondary; mechanism unspecified].
2. **Meta**: TPP upstreamed to Linux 5.18. Vistara CXL **expansion**, not pooling, deployed at multi-million-server scale with recycled DDR4.
3. **Microsoft**: Pond / Octopus research on **pooling**. Productised CXL as **expansion** first (Azure M-series preview, Nov 2025). GreenSKU studies reuse for carbon.
4. **Alibaba**: switch-based pooling prototyped for one system (PolarDB) and benchmarked vs RDMA. Production deployment is claimed only in a blog [secondary] (D3).

**Net:** the variant of (B) that shipped at scale is *CXL-attached capacity expansion and tiering with cheap or recycled DRAM*, not rack-scale pooling. Pooling survives in niche, high-value cases (shared DB buffer pools, KV-cache tiers, composable HPC).

---

## 3. Which metrics and resources (B) optimizes, and for which workloads

| Metric | Evidence | Direction of effect |
|---|---|---|
| **$/GB of provisioned capacity** | Vistara CXL tier 0.13× cost/GB (recycled). Lagar-Cavilla: zswap tier ≥67% cheaper per GB. TMTS: 25% on cheaper media | Primary target |
| **Total DRAM purchased (utilization)** | Pond: −7–9% DRAM. Octopus: −3–5.4% net server cost. Lim: +87% perf/$ | Primary for pooling. Modest numbers |
| **Capacity per core / per server** | Vistara: up to 1 TB per server; 40% of fleet capacity-bound. Leo: >1.5× capacity | Primary for expansion |
| **Embodied carbon** | DIMMs are 41% (Azure) or 69% (Meta) of server carbon. GreenSKU-CXL net +1% only | Secondary. Contested magnitude |
| **Server count (TCO)** | Vistara: −25% servers for ML inference | Strong where working sets don't fit |
| **Reliability (OOM)** | Vistara: ~50% fewer OOMs for some ML workloads | Secondary |
| **Bandwidth per socket** (pin-limited) | Micron/Intel arXiv 2412.12491 [full text]: Xeon 6900P, 12× DDR5-6400 (556 GB/s read) plus 8× CZ122 ×8 CXL (205 GB/s, i.e. 64 PCIe lanes). Weighted interleave 3:1 gives **690 GB/s, +24%** read-only. 5:2 on 1R:1W gives **621 vs 446 GB/s, +39%**. Geomean +24% on HPC/AI apps (OpenFOAM, LLM inference, vector search). Naïve 1:1 interleave *reduces* BW (394 GB/s). Sun MICRO'23 (Caption): +24% for BW-bound apps. Pin argument: CXL ×8 uses ~3× fewer pins than a DDR5 channel | Real but only with many lanes and tuned interleave. CXL is bidirectional, so it is better on mixed R/W |
| **Upgrade granularity / elasticity** | Fastswap (+25%/+50% minimum local steps; far memory gives finer steps but loses 2.5–9.7% vs equal local capacity); LegoOS | Secondary |
| **Latency / bandwidth per GB** | Gets *worse*: Vistara CXL ≈10× lower BW and ≈60% higher latency than local. Commercial CXL BW is 9.8–80.3% of local DRAM BW by vendor and saturates at ~4 threads (Wang et al., arXiv 2405.14209 [full text]) | Explicitly sacrificed |

**Workloads where (B) wins:**
- **Capacity-bound, cold-heavy services**: caches (Meta CacheA/B, 29% latency cut), web, data warehouse and Spark/Hive, databases, CI/CD.
- **Recsys embedding tables and parameter servers**: −25% servers.
- **Large-memory VMs**: in-memory DBs (Azure M-series).
- **LLM KV-cache tiers**:
  - ByteDance + Yale (MLSys workshop at NeurIPS'24) [full text]: KV cache on CXL allows a 30% larger batch and up to 87% fewer GPUs vs KV recompute under a TTFT SLO.
  - Samsung: 1 TB CXL pool at ~92% of DRAM-level inference performance over 8 GPUs [secondary: Korea Herald].
  - SK hynix: +35.7% throughput [secondary].
  - Several 2025–26 arXiv proposals (PNM-KV, CXL-SpecKV, SAC) [abstract only / search summary].
- **Bandwidth-bound HPC/AI on lane-rich CPUs** with interleaving (+24% geomean).

**Workloads where (B) loses:** latency-sensitive workloads with low idle time.
- Pond: 21–37% of workloads suffer >25% slowdown when fully on CXL.
- Sun MICRO'23: Redis p99 +10–82% on CXL.
- Vistara disables CXL for workloads with low P75/P99 idle time.

---

## 4. Surprising findings and open threads

1. **The expansion/pooling split.** The literature motivates (B) with *stranding* (pooling), but production deployments (Meta Vistara, Azure M-series) are *expansion*. Pooling's measured upside is 7–13% of DRAM (Pond) or 3–5.4% of server cost (Octopus). Levis et al. calculate that a $38.5k switch-equivalent at $3/GB DDR5 needs 12.6 TB of RAM savings to break even. Even free pool RAM needs a 24-node pool at 4 GB/core. Microsoft's own model (Berger IEEE Micro'23) agrees that switched pools are mostly ROI-negative. But it finds *small, switchless MHD pools (≤16 sockets)* ROI-positive at single-digit-% savings. So the Levis–Berger disagreement is mostly about topology (switch vs MHD), not about pooling per se. Open thread: these break-evens were computed at 2023 DRAM prices. With 2026 contract prices up ~80–90% QoQ [secondary], the break-even pool size shrinks substantially. Nobody has redone the analysis.
2. **The "50% of server cost" figure** used across the CXL literature traces (via Pond) to a 2020 Next Platform article. Meta's measured figure is 37.1% of rack TCO (Gen5).
3. **Carbon arguments are smaller than headlines suggest.** In GreenSKU, adding CXL-reused DDR4 gains only +1% net per-core carbon over an efficient-CPU baseline, because controller power eats most of the embodied gain. Only 3% of traces even touch the CXL tier. Meta's 69% DIMM-carbon share is much higher than Azure's 41% or 35%, likely due to accounting boundary and renewable mix. Worth reconciling.
4. **"Far memory" mostly isn't disaggregated.** Google's deployed far memory is compressed DRAM in the same node, and its tier-2 was Optane DIMMs. The economic driver is *cheaper bytes for cold data*, not *remote bytes*. CXL is attractive because it is a vendor-neutral, technology-agnostic slot for cheaper bytes.
5. **Convergence with direction (A).** CXL devices are absorbing compute: Marvell Structera A (16 Neoverse V2 cores in the memory controller), the CXL-PNM KV-cache proposals, and Samsung CMM variants with compute. The disaggregation interface is becoming a near-memory-processing site. Relevant to the overall "paradox" question.
6. **Feedback loop with (A).** The 2026 DRAM shortage is driven largely by AI and HBM demand (direction A consuming wafer capacity). It raises the value of DDR4 recycling via CXL (direction B). Meta and Google both cite reuse, and Marvell markets Structera X into the shortage. Treat this causal link as plausible but [secondary].
7. **Bandwidth expansion is real but conditional.** It needs ~64 dedicated PCIe5 lanes for +24–39%, and the right interleave ratio (naïve 1:1 loses 29%). Meta uses CXL at deliberately low BW/GB. So "CXL for bandwidth" is an HPC/AI-node story, and "CXL for capacity" is the hyperscale fleet story.
8. **Open:** no public production data yet on CXL 3.x *sharing* (multi-host coherent) at scale. The latency of switched pools (490–600 ns) puts them closer to "fast far memory" than "slow NUMA".

---

## 5. Source list

- Li, Berger et al. **Pond**. ASPLOS'23. https://www.microsoft.com/en-us/research/wp-content/uploads/2022/10/Pond-ASPLOS23.pdf (DOI 10.1145/3575693.3578835) [full text]
- Maruf et al. **TPP**. ASPLOS'23. https://arxiv.org/abs/2206.02878 [full text]
- Lagar-Cavilla et al. **Software-Defined Far Memory in WSCs**. ASPLOS'19. https://storage.googleapis.com/gweb-research2023-media/pubtools/5649.pdf [full text]
- Duraisamy et al. **TMTS**. ASPLOS'23. https://www.micahlerner.com/assets/pdf/adaptable.pdf (DOI 10.1145/3582016.3582031) [full text]
- Wang et al. **GreenSKU: Designing Cloud Servers for Lower Carbon**. ISCA'24. https://www.microsoft.com/en-us/research/uploads/prod/2024/03/2024-GreenSKU-ISCA2024.pdf [full text]
- Gholkar et al. (Meta) **Vistara: Making CXL Real**. ISCA'26. https://aisystemcodesign.github.io/papers/isca26/vistara_camera_ready.pdf [full text]
- Zhong et al. **Memstrata / Intel Flat Memory Mode**. OSDI'24. https://www.usenix.org/system/files/osdi24-zhong-yuhong.pdf [full text]
- Zhong et al. **Octopus**. arXiv 2501.09020 (2025). [full text]
- Levis, Lin, Tai. **A Case Against CXL Memory Pooling**. HotNets'23. https://conferences.sigcomm.org/hotnets/2023/papers/hotnets23_levis.pdf [full text]
- Lim et al. **Disaggregated Memory for Expansion and Sharing in Blade Servers**. ISCA'09. https://safari.ethz.ch/architecture/fall2021/lib/exe/fetch.php?media=isca09-disaggregate.pdf [full text]
- Gao et al. **Network Requirements for Resource Disaggregation**. OSDI'16. https://www.usenix.org/system/files/conference/osdi16/osdi16-gao.pdf [full text]
- Gu et al. **Infiniswap**. NSDI'17. https://www.usenix.org/system/files/conference/nsdi17/nsdi17-gu.pdf [full text]
- Shan et al. **LegoOS**. OSDI'18. https://www.usenix.org/system/files/osdi18-shan.pdf [full text]
- Ruan et al. **AIFM**. OSDI'20. https://www.usenix.org/system/files/osdi20-ruan.pdf [full text]
- Zhou et al. **Carbink**. OSDI'22. https://www.usenix.org/system/files/osdi22-zhou-yang.pdf [full text]
- Amaro et al. **Can far memory improve job throughput? (Fastswap/CFM)**. EuroSys'20. DOI 10.1145/3342195.3387522. `fulltext/12-fastswap.pdf` [full text]
- Cheng, Chai, Anwar. **Characterizing Co-located Datacenter Workloads: An Alibaba Case Study**. arXiv 1808.02919 (2018). `fulltext/1808.02919v2.pdf` [full text]. Alibaba clusterdata README (mhtml) [full text]
- Sun et al. **Demystifying CXL Memory with Genuine CXL-Ready Systems**. MICRO'23. https://arxiv.org/abs/2303.15375 [full text]
- Sehgal, Tanna, Petrucci (Micron), Godbole (Intel). **Optimizing System Memory Bandwidth with Micron CXL… Xeon 6**. arXiv 2412.12491 [full text]
- Wang et al. (UC Merced / MemVerge). **Exploring and Evaluating Real-world CXL**. arXiv 2405.14209 [full text, intro only]
- Tang et al. (ByteDance). **Exploring CXL-based KV Cache Storage for LLM Serving**. MLSys workshop @ NeurIPS'24. https://mlforsystems.org/assets/papers/neurips2024/paper17.pdf [full text]
- Liu et al. **SupMario / Dissecting CXL Memory Performance at Scale**. arXiv 2409.14317 [abstract only]
- Berger, Ernst, Li, Zardoshti, Shah, Rajadnya, Lee, Hsu, Agarwal, Hill, Bianchini. **Design Tradeoffs in CXL-Based Memory Pools for Public Cloud Platforms**. IEEE Micro 43(2), 2023. `fulltext/05-cxl-pools.pdf` [full text]
- Tang et al. (Yale/ByteDance). **Exploring Performance and Cost Optimization with ASIC-Based CXL Memory**. EuroSys'24. DOI 10.1145/3627703.3650061. `fulltext/26913_Exploring_Performance_an.pdf` [full text]
- Yang et al. (Alibaba/XConn). **Unlocking the Potential of CXL for Disaggregated Memory in Cloud-Native Databases (PolarCXLMem)**. SIGMOD-Companion'25. DOI 10.1145/3722212.3724460. `fulltext/3722212.3724460.pdf` [full text]
- Press and news [secondary]:
  - Astera/Azure M-series PR (2025-11-18)
  - The Register on Vistara (2026-06-29)
  - Cryptopolitan on the DRAM shortage (2026-09-03)
  - Korea Herald on CXL for AI
  - TrendForce on Samsung CMM-D
  - SK hynix newsroom
  - ServeTheHome and Marvell on Structera
  - CXL Consortium CXL 4.0 release
  - HPCwire and DCD on OpenCAPI and Gen-Z
  - Top500 on HPE The Machine
  - Blocks&Files on Liqid, UnifabriX, Panmnesia
