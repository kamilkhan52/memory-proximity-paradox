# Q4 — Workload characterization: which data/access properties make "closer" (A) vs "farther" (B) memory pay off?

Agent: Q4 sub-agent, 2026-10-02. Method: primary sources pulled as PDFs (arXiv / author / conference copies) and
text-searched with pdftotext; numbers below are quoted from the full text unless labeled otherwise.

Labels: [abstract only] = taken only from an abstract; [pending full text] = depends on a source listed below;
[derived] = my own arithmetic from cited specs; [unverified] = secondary source / could not trace to a primary;
[secondary] = taken from a vendor/reseller summary, not the primary datasheet.

---

## Round-4 deltas (2026-10-03; full texts supplied by Kamil, fulltext/MANIFEST.md)

**1. Hao, Zhou, Yu, Stonebraker, SIGMOD'24 (fulltext/3639286.pdf). This changes how the five-minute rule is framed.**
- **The paper does NOT compute a break-even interval for CXL/RDMA memory.** There is no "CXL rung" number to cite.
- It argues the classic rule does not fit remote memory (§4.9, p.31:12): the rule "assumes unlimited bandwidth for the
  performance device (i.e., local memory) and unlimited capacity for the storage device (i.e., disk). Remote memory,
  however, cannot fit neatly into either of these categories, as it is not cheap enough to be a storage device, nor is it
  fast enough to be a performance device."
- Its provisioning rule (§4.9, p.31:12):
  - Size local-only memory with the conventional five-minute rule.
  - Then "gradually replace local memory with remote memory until it saturates the remote memory bandwidth."
  - Rationale: "for workloads that do not necessitate local-memory level bandwidth, we can use remote memory to replace
    local memory without sacrificing performance."
  - This is a **bandwidth-per-capacity (ρ) stopping rule**, which directly supports the §5.3 access-density framing.
- Assumptions:
  - Remote memory is *emulated* by injecting no-ops; no CXL or RDMA hardware (§5.2, p.31:13).
  - Latency: local 100 ns, remote 1 µs default (swept 0.1–5 µs), disk 100 µs (§5.1, p.31:12). Remote bandwidth 5 GB/s default.
  - SATA SSD at 75k IOPS.
  - Workload: synthetic YCSB-like, zipf 0.9, 80/20 read/update, 23 GB dataset, 3 GB local + 18 GB remote.
  - Pricing is per GB-hour (EC2-like). Remote price is 0.3× local in §5.9 ("similar to PM price"), swept at 0.1×/0.3×/0.8× in §5.10 (p.31:20–21).
  - **No absolute $/GB is given.**
- Results:
  - Three-tier "up to 10×" more cost-effective "than conventional two-tier systems" (p.31:2, 31:24). Two-tier here means
    local DRAM + disk, so much of the 10× is avoided disk I/O, not the DRAM-vs-CXL price gap.
  - At 0.8× price, "some configurations of the three-tier system can be more expensive than two-tier systems, especially
    when remote memory is slow" (§5.10, p.31:21).
  - Skew makes the far tier tolerable: most designs keep ~70% of throughput when remote latency rises 50×. With local
    memory at 1/6 the size of remote, "local memory serves more than half of the tuple accesses" (§5.5, p.31:16).
- Net: the five-minute rule alone cannot place data between DRAM and CXL. The middle-tier decision needs a bandwidth term.
  This strengthens the ρ × I × MLP synthesis. The only published DB-level evidence is emulated, single-node and synthetic.

**2. Boroumand et al., ASPLOS'18 (fulltext/09-google-workloads.pdf). Headline numbers verified, with one attribution fix.**
- 62.7% verified (p.1): "across all of the applications we study, 62.7% of the total system energy, on average, is spent on
  data movement between main memory and the compute units."
- **Correction:** 55.4% energy and 54.2% performance are averages for **PIM accelerators** (up to 73.5% and 2.5×).
  **PIM cores** give 49.1% and 44.6% (up to 59.4% and 2.2×) (p.2).
- Method: characterized on a real Chromebook; PIM evaluated in gem5 simulation (p.11–12). The PIM is HMC-like
  logic-layer PIM in 3D-stacked DRAM. Area budget: PIM core ≤9.4%, accelerators ≤35.4% of per-vault logic area.
- Per-kernel data (all simple, streaming, low-reuse primitives: "memcopy, memset, basic arithmetic … bitwise operations"):
  - Chrome page scrolling: data movement is 77% of energy; 41.9% of scrolling energy goes to texture tiling + color blitting.
  - Texture tiling: data movement from tiling is 25.7% of system energy; only 18.5% of tiling energy is computation.
  - Color blitting: 19.1% of system energy, 63.9% of it data movement ("streaming access pattern and the large sizes of
    the bitmaps"). All pages show high LLC MPKI.
  - TensorFlow Mobile: packing + quantization are 39.3% of system energy on average (packing up to 40% of energy and
    31% of inference time); quantization 16.1% / 16.8% of energy / time on ResNet.
  - VP9: reference-frame reads in motion compensation are up to 75.5% (HD) / 59.6% (4K) of decoder traffic and
    71.3% / 69.2% of hardware-decoder energy.
- **New "both directions" example:** Chrome tab switching compresses inactive tabs into ZRAM, a compressed in-DRAM pool,
  i.e., B-style far memory like Google's zswap. Measured 11.7 GB swapped out at up to 201 MB/s and 7.8 GB swapped in at up to
  227 MB/s over 50 tabs. Compression/decompression is itself a PIM target (§4.3). So the B mechanism's data-movement
  cost is offloaded with A.
- Status: [P] (was [P/partial]).

**3. HeMem, SOSP'21 (fulltext/3477132.3483550.pdf). Adds a read/write-asymmetry axis.**
- Table 1 (p.392):

  | Memory | Latency R/W | Bandwidth R/W | Capacity |
  |---|---|---|---|
  | DDR4 | 82 ns | 107 / 80 GB/s | 1× |
  | Optane DC | 175 / 94 ns | 32 / 11.2 GB/s | 8× |

- Unmanaged tiering "can diminish memory bandwidth by up to 7× and inflate latency by up to 63%".
- At scale, DRAM random/sequential *write* throughput is 10.7× / 16.5× Optane's. Random read is 2.7×.
- Policy: write-heavy pages are migrated to DRAM before read-heavy pages. Small, ephemeral structures stay in DRAM.
- Results: up to 50% lower GAP runtime, +13% TPC-C (Silo), up to 10× less NVM wear.
- Implication: the far tier's bandwidth per GB (and its write bandwidth) bounds what can go there. Placement depends on
  ρ_write separately from ρ_read. This supports the §6 caveat that hotness alone is insufficient.

**AttAcc:** reassigned to the Q1 agent. The AttAcc numbers in §3.2 stay [abstract only] here.

## FULL TEXT NEEDED

None outstanding for Q4. Items 1, 2 and 4 were resolved in Round 4; item 3 (AttAcc) was handed to Q1.

Untraceable claim (no specific source to request): "Alibaba: 45% of memory stranded fleet-wide on average." It
appears only in search-engine snippets attached to Pond-related pages. → **[unverified]; do not use.**

---

## 0. Bottom line (for synthesis)

1. **Skew is pervasive and large.** In every fleet characterization I found, a minority of allocated bytes carries most
   accesses. Google: 32% of memory is cold at T=120 s, ranging 1–61% across clusters. Meta TPP: 55–80% of allocated
   memory is idle in any 2-minute window. Azure Pond: median VM leaves 50% of rented memory untouched. Meta MemProf: <10% of capacity
   supplies the 90th-percentile of memory bandwidth. Alibaba KV$: 10% of KV blocks get 77% of reuses. Mooncake:
   >50% of KV blocks are never reused. DLRM embeddings: power-law access.
2. **The same skew determines both directions.** The high-density head (lots of bytes moved per stored byte per
   second, with little on-chip reuse) is where bandwidth and energy per bit dominate, so (A) applies. The long tail (few
   accesses per byte per minute) is where $/GB dominates, so (B) applies.
3. **Several single workloads need both at once.** These are the strongest evidence for the hypothesis:
   - DLRM embeddings. MTrainS (Meta): "the majority of the larger capacity embedding tables have a relatively low
     bandwidth requirement, and the tables that contribute to the most bandwidth have small sizes."
   - LLM decode. Weights and KV are read at ~1 FLOP/B (HBM / PIM), while KV that waits for reuse is cold and offloaded to DRAM/SSD.
   - Meta's general services, via "memory bandwidth tiering": an HB-DIMM tier plus a CXL tier.
   - TensorDIMM, which places NMP DIMMs inside a *disaggregated pool*.
   - TokenStack (2026), which splits a single HBM stack into PIM layers for hot KV and dense layers for cold KV.
4. **Cleanest unifying framework:** the five-minute rule (break-even re-reference interval) for the capacity axis,
   plus roofline arithmetic intensity for the compute axis. Both reduce to a per-object **access density** (bytes
   moved per second per byte stored, i.e., BW/GB) compared against each tier's BW/GB and $/GB. See §5.
5. **Caveats (against a too-clean version of the hypothesis).** These are covered in §6.
   - CXL is also bought for *bandwidth* (Colloid: under load the "fast" tier's latency inflates 2.5× and
     hottest-in-fast-tier placement can be 2.3× worse than optimal).
   - Latency tolerance depends on memory-level parallelism, not just hotness ("Beyond Hotness", OSDI'25).
   - Workload class predicts far-memory slowdown poorly (Pond: within-class variability exceeds across-class).
   - KV$ lifetimes can be short enough that ~2× HBM capacity suffices (Alibaba ATC'25), which limits the size of the far tier for KV.

---

## 1. Datacenter memory temperature / skew (the case for B)

| Source | Setting | Key numbers (verbatim or near-verbatim) |
|---|---|---|
| **Lagar-Cavilla et al., "Software-Defined Far Memory in Warehouse-Scale Computers," ASPLOS'19** (Google). PDF: https://storage.googleapis.com/gweb-research2023-media/pubtools/5649.pdf | zswap-based far memory (compressed DRAM), fleet since 2016 | Cold = not accessed in T seconds. "At T = 120 s, we observe that **32% of memory usage is cold**" (fleet-wide avg). Cold memory "ranges from **1% to 61%** across different clusters and from **1% to 52%** even within the same cluster". At T=120 s, apps touch **15%** of their cold memory each minute. Target promotion rate SLO **0.2%/min**. Deployed result: stores ~**20%** of data in far memory, **67%+** memory-cost reduction for that data, ~**6 µs** access. The top 10% of jobs have ≥43% cold, the bottom 10% <9% (from search-result summary of the same paper; consistent with Fig. 3). |
| **Maruf et al., "TPP: Transparent Page Placement for CXL-Enabled Tiered-Memory," ASPLOS'23** (Meta). arXiv 2206.02878 | 4 production apps (Web, Cache1/2, Data Warehouse, Ads) | "**55-80%** of an application's allocated memory remains idle within any two minutes interval." Web/Cache/Ads use 95–98% of server memory capacity but touch only 22–80% of allocated memory per 2-min window. Data Warehouse: "only **20%** of the accessed memory is hot within a two-minute interval". **Anon pages run hotter than file pages**: Web 35–60% of anons hot per 2 min vs much less for file; Cache1 40% anon vs 25% file; Cache2 at 1 min 43% anon vs 30% file. Re-access: for Web ~80% of pages are re-accessed within 10 min, so naive offload hurts. CXL latency assumed 170–250 ns. TPP result: within **<1%** of all-local-DRAM, 18% better than default Linux. Throughput vs all-local is 99.5% (Web1, 2:1 local:CXL), 99.5% (Cache1 1:4), 95.0% (Cache2 1:4). |
| **Li et al., "Pond: CXL-Based Memory Pooling Systems for Cloud Platforms," ASPLOS'23** (Azure). arXiv 2203.00241 | 100 Azure clusters; 158 workloads under emulated CXL latency | Stranding: daily average stranding exceeds 10% when ~85% of cores are rented. At the **95th percentile, stranding reaches 25%**; outliers reach 30%. Summary: "3-27% of stranded memory". Untouched: "**~50% of all VMs touch less than 50%** of their rented memory"; median VM 50% untouched; even in the best cluster >50% of VMs have >20% untouched. Sensitivity (+182% latency): **26%** of 158 workloads <1% slowdown, a further 17% <5%, **21% >25%**. At +222% latency, 23% <1%. GAPBS (graph) is generally worse. "the variability within each workload class is typically much higher than across workload classes". Azure proprietary workloads: 6/13 <1%, 2 ~5%, the rest 10–28%. Pooling saves 7% of DRAM at 1–5% configurable slowdown [abstract number; full text available but not re-derived]. |
| **Zhong et al., "Managing Memory Tiers with CXL in Virtualized Environments" (Memstrata / Intel Flat Memory Mode), OSDI'24.** https://www.usenix.org/system/files/osdi24-zhong-yuhong.pdf | Hardware-managed cacheline-granularity tiering, 1:1 local:CXL | "no more than **5%** degradation for more than **82%** of workloads". Outliers reach up to 34% slowdown, or up to 50% with naive co-location. The 1:1 ratio cuts local DRAM provisioned by 50%. Cites Meta's memory share of server cost and Google's 25%. DIMMs are 41% of server embodied carbon. |
| **Duraisamy et al., "Towards an Adaptable Systems Architecture for Memory Tiering at Warehouse-Scale" (TMTS), ASPLOS'23** (Google). PDF: https://www.micahlerner.com/assets/pdf/adaptable.pdf | Deployed 2 yrs; tier2 = Optane variant | "sustains less than **5%** overall performance degradation while replacing **25%** of DRAM with a much slower medium". The 25% design point is set by measured cold-memory ratios at 2/4/8-minute ages. Goal met at tier2 access ratio (STAR) **≤0.5%**; p95 STAR within 1.5%. tier2 bandwidth is tiny: 6.9 GB/s sequential read, **1.8 GB/s random read**, 0.5 GB/s random write per DIMM; idle read latency ~325 ns. More than 98% of pages are anon/tmpfs at Google. |
| **Maruf et al., "Memtrade," SIGMETRICS'23.** arXiv 2108.06893 | Harvesting idle VM memory | Harvested idle memory is on average **1.1–51.4%** of the workload, at **0–1.6%** degradation (memcached 51.4%, MySQL 21.7%). Cluster utilization rises from 56.8% to 97.9%. Cites Google "up to 61% of allocated memory remains idle". |
| **Mahar, Wang, Shu, Dhanotia, "Workload Behavior Driven Memory Subsystem Design for Hyperscale," arXiv 2303.08396 (2023)** (Meta/UCSD). | MemProf on 9 Meta production services | "over 30 seconds, the active memory footprint is **less than 25%** of the total memory capacity, while the **90%-tile bandwidth is contributed by less than 10%** of the memory capacity". This holds across 30/60/120 s windows. Workloads with >60% bandwidth utilization are classed "bandwidth bound", and the fleet is trending more bandwidth-bound. **See §3.3. The same paper proposes HB-DIMM plus a CXL tier.** |
| Memtis (Lee et al., SOSP'23) | Tiering using whole access histogram + huge-page split | Exploits *intra-hugepage skew*. If only a few 4K subpages are hot, it splits the page and promotes only those. Reported gains: +15.9% / 21.2% / 7.1% over the next best at 1:2 / 1:8 / 1:16 fast:slow ratios [from search-result summary; Memtis full text downloaded but this specific number not re-verified]. Memory is ~37.1% of Meta server cost and ~50% of Azure's (Memtis intro, citing others). |
| Nomad (Xiang et al., OSDI'24) | Non-exclusive tiering | Up to 6× over TPP under memory pressure [abstract only]. Mechanism paper, not characterization. |

**What fraction can tolerate far latency, and at what cost?** The production deployments converge on roughly
**20–33% of DRAM moved to a slower tier at ≤1–5% slowdown**: Google zswap ~20% at 0.2%/min promotion SLO; Google TMTS
25% at <5%; Meta TPP 33% (2:1) at <1%; Azure Pond 7% DRAM saving at 1–5%. Characterization alone suggests 50–80% is
idle on minute timescales, but production systems capture much less because of re-access (TPP: 80% of Web pages are
re-touched within 10 min), migration cost, and tail-latency SLOs.

---

## 2. Bandwidth-boundness / arithmetic intensity (the case for A)

- **AI memory wall (Gholami et al., IEEE Micro 2024, arXiv 2403.14123).** Over 20 years, peak server FLOPS grew **3.0×/2 yr**
  vs DRAM bandwidth **1.6×/2 yr** and interconnect **1.4×/2 yr**. That is 60,000× vs 100× vs 30× over 20 years.
  Transformer FLOPs grew 750×/2 yr (2018–22). LLM parameters grew 410×/2 yr, while single-GPU memory grew only 2×/2 yr.
- **Splitwise (Patel et al., ISCA'24, arXiv 2311.18677).** H100 vs A100: 3.43× compute and 1.75× power, but "memory bandwidth only
  grew by **1.6×**, with no increase in memory capacity" (80 GB → 80 GB). The token-generation phase is "memory bandwidth and
  capacity bound, despite state-of-the-art batching". Phase split gives 1.4× throughput at 20% lower cost, or 2.35×
  throughput at the same power and cost.
- **DistServe (Zhong et al., OSDI'24, arXiv 2401.09670).** Prefill of a 512-token prompt on a 13B model makes an A100 "near
  compute-bound". Decode "incurs a similar level of I/O to the prefill phase, making it constrained by the GPU's memory
  bandwidth". Disaggregation serves 7.4× more requests or meets a 12.6× tighter SLO.
- **LLM roofline (Yuan et al., "LLM Inference Unveiled," arXiv 2402.16363), Table 1, Llama-2-7B on A6000, seq 2048, batch 1.**
  - Prefill projections: ~1024–1215 OPs/B, compute-bound. Prefill attention qk/sv: 114 OPs/B, memory-bound.
  - **Decode: all projection GEMVs are 1 OP/B, attention qk/sv 0.99 OP/B, softmax 1.25, norm 1.75, add 0.25.
    All memory-bound.**
- [derived] Ridge points: H100 SXM ≈ 989 TFLOPS dense FP16 ÷ 3.35 TB/s ≈ 295 FLOP/B. A100 ≈ 312 ÷ 2.04 ≈ 153
  FLOP/B. Batch-B decode GEMMs have intensity ≈ B FLOP/B, so they need B in the hundreds to become compute-bound. Decode
  attention over the KV cache stays near ~1 FLOP/B per KV head regardless of batch, because each sequence has its own
  KV; GQA raises it by roughly the group factor (e.g., 8 for Llama-3-70B). KV size, Llama-3-70B FP16 [derived]:
  2 × 80 layers × 8 KV heads × 128 × 2 B = 320 KiB/token, so ≈40 GiB per 128K-token sequence.
- **DLRM (Gupta et al., HPCA'20, "Architectural Implications of Facebook's DNN-based Personalized Recommendation," arXiv
  1906.03109).** Recommendation is "over **79%** of AI inference cycles" at Facebook. Embedding operational intensity is
  **0.25 FLOPs/Byte**, vs RNN 5.5, FC 18, CNN 141. Tables range from tens of MB to several GB each and total up to many GB.
- **RecNMP (Ke et al., ISCA'20, arXiv 1912.12953).** Recommendation models are >72% of AI inference cycles at Facebook. SLS (gather-reduce)
  is 37.2–50.6% of time at batch 8 and up to 73.5% / 68.9% for large models. Tables are "tens to hundreds of GBs". Locality:
  random traces hit <5% in an 8–64 MB cache, while **production traces hit 20–60%** (temporal reuse, skew). Spatial locality
  is near zero (hit rate falls as line size grows). NMP DIMM: **9.8× memory-latency speedup, 4.2× end-to-end throughput**,
  and "lifting the roofline by 8×" for the bandwidth-bound region.
- **Boroumand et al., ASPLOS'18 (Google consumer workloads).** 62.7% of total system energy goes to data movement (real
  Chromebook). Simulated PIM (gem5): accelerators give −55.4% energy and +54.2% performance; PIM cores give −49.1% and
  +44.6%. Targets are simple streaming primitives. Per-kernel data is in Round-4 deltas §2. [P, full text 2026-10-03]
- Graph analytics: in Pond, GAPBS is the worst class under CXL latency, so graphs are latency-bound (pointer chasing, low
  MLP) as well as bandwidth-bound. This makes graphs a poor fit for B and a classic target for A (e.g., Tesseract;
  not re-checked here).

---

## 3. Single workloads that motivate BOTH directions

### 3.1 Recommendation (DLRM) embeddings: the canonical "both" workload
- **Capacity side (B).** Tables reach hundreds of GB to TB. Meta deployed SCM/flash tiers:
  - **Bandana** (Eisenman et al., SysML'19, arXiv 1811.05922): NVM as primary embedding store with a small DRAM cache. Hypergraph
    partitioning raises effective NVM read bandwidth 2–3×.
  - **Software Defined Memory for DLRM inference** (Ardestani et al., arXiv 2110.11489): DLRMs "grow by more than 1.5x per
    year". Nand flash/3DXP tiers give **5–29% power savings**.
  - **MTrainS** (Kassa et al., arXiv 2305.01515): heterogeneous HBM + DRAM + byte- and block-addressable SCM cuts training nodes **4–8×**.
- **Bandwidth side (A).** RecNMP (above, 4.2×). **TensorDIMM** (Kwon, Lee, Rhu, MICRO'19, arXiv 1908.03072): NMP DIMMs
  "populated inside a GPU-centric system interconnect as a **remote memory pool**". Gives 6.2–17.6× over CPU-only or hybrid
  baselines, which are 7.3–20.9× slower than an ideal infinite-GPU-memory system. *This is literally near-memory compute
  placed inside a disaggregated pool.*
- **The key observation in MTrainS (Meta), verbatim:** "the majority of the larger capacity embedding tables have a relatively
  low bandwidth requirement, and the tables that contribute to the most bandwidth have small sizes. By taking advantage of a
  hierarchy … HBM, DRAM, and SCM, it is possible to address both capacity and bandwidth requirements." The per-table metric is
  pooling factor × embedding dim × QPS, i.e., **bandwidth demand per GB stored**. This is exactly the access-density axis of §5.
- Embedding row skew: power-law access, e.g., ~80% of accesses from 10–40% of rows in most tables, and 94% from 10% of
  rows in MovieLens [unverified: search-result summaries of arXiv 2511.08568 / ISC'23 cached-embeddings paper; not read].

### 3.2 LLM serving: weights and live KV close, reusable KV far
- **A (closer).** Decode is ~1 FLOP/B (§2). Products and proposals put attention next to HBM: AttAcc (ASPLOS'24, 2.81× perf /
  2.67× energy for 175B [abstract only; full text assigned to Q1]), NeuPIMs (ASPLOS'24), and HBM-PIM.
- **B (farther), KV-cache tiers.**
  - **Mooncake** (Moonshot AI/Kimi, arXiv 2407.00079, FAST'25): "leverages the underutilized CPU, DRAM, and SSD resources of the GPU
    cluster to implement a disaggregated cache of KVCache". Up to 525% throughput in simulation; **75% more requests** under
    real workloads. Trace: average input 7,590 tokens, output 182. Cache hit ratio rises 30%→50% as capacity grows 1k→50k
    blocks, then flattens. "**over 50% of cache blocks remaining unused while certain blocks are accessed tens of
    thousands of times**". Theoretical max reuse ~50%.
  - **CachedAttention / AttentionStore** (Gao et al., USENIX ATC'24): KV generation for LLaMA-65B runs at ~13.9 GB/s and **fills 190 GB of
    free HBM in 14 s**. Up to 99% of prefill cost in multi-turn chat is recomputing KV. A host-DRAM + SSD hierarchy gives TTFT −87%,
    prefill throughput 7.8×, end-to-end cost −70%.
  - **LMCache** (arXiv 2510.09665): "the total KV cache stored by users has grown rapidly, far exceeding the capacity of GPU
    memory". Up to 15× throughput with vLLM.
  - **FlexGen** (Sheng et al., ICML'23, arXiv 2303.06865): offloads weights and KV to CPU DRAM (208 GB) and SSD from a 16 GB T4. 100×
    higher max throughput for OPT-175B, aimed at latency-insensitive batch work.
- **KV reuse skew in production: Wang et al., "KVCache Cache in the Wild," USENIX ATC'25 (Alibaba Tongyi), arXiv 2506.02634.**
  - "**10% of KV$ blocks contribute to 77% of the reuses**". 19% of users generate 50% of requests.
  - Ideal hit ratio is 62% / 54% on two traces, lower than synthetic benchmarks suggest.
  - Single-turn requests are 97% of reuses in to-B (API) traffic.
  - **"The lifespan of KV$ is ephemeral: the P99 lifespan of KV$ in to-B workloads is 97 seconds." A KV$ of 2× per-GPU HBM
    capacity is enough to approach the ideal hit rate.**
  - This is a counterweight to LMCache: needs vary by workload category (API vs chat vs long-doc/agentic).
- **Hardware converging on both.** NVIDIA GH200 has 96 GB HBM3 (4 TB/s) or 144 GB HBM3e (~4.8–4.9 TB/s) next to 480 GB LPDDR5X
  (~500–512 GB/s), joined by NVLink-C2C at 900 GB/s, for 576–624 GB of coherent memory [secondary: reseller/aggregator
  pages; NVIDIA datasheet not opened]. This is a two-tier "close + far(ther)" design in one package: high-BW/GB HBM for
  weights and active KV, and capacity LPDDR for KV/optimizer overflow.
- **TokenStack (Li et al., arXiv 2605.05639, 2026), the hypothesis *inside* a single HBM stack.**
  - "decode-phase attention is bandwidth-bound yet touches only a small fraction of total memory".
  - "only **23.8–24.5%** of occupied HBM is PIM-useful active KV. The remaining 75.5–76.2% is cold KV, weights, activations…".
  - Uniform PIM halves die capacity (2 GiB→1 GiB in their illustrated 8-high stack, leaving 40 GiB per GPU).
  - AttAcc-style dedicated PIM keeps 32 GiB dense + 24 GiB PIM but exposes only 2/5 of stack bandwidth to the GPU.
  - Their fix: PIM layers for hot KV and dense layers for cold data. Reports 1.62× throughput and 1.70× SLO capacity vs AttAcc, and −30–47% energy/token.
  - (Preprint, simulation-based; treat numbers as provisional.)

### 3.3 General hyperscale services: "memory bandwidth tiering" (Meta, Mahar et al. 2023)
- Proposal: a small high-bandwidth tier (HB-DIMM, ~2× DDR5 bandwidth at ~2× power and cost) for the few pages that carry
  most bandwidth, plus a CXL-like tier for capacity.
- Measured on Meta's Reader microservice, relative to baseline (100% capacity at 100 GB/s):

  | Config | Capacity / bandwidth | Throughput | Throughput/cost |
  |---|---|---|---|
  | Baseline | 100% at 100 GB/s | 1.0 | 1.0 |
  | "Ideal": all memory 2× BW | 100% at 200 GB/s | **1.55×** | 0.73 (cost 2.0) |
  | **Tiered** | 37.5% at 200 GB/s + 62.5% at 100 GB/s CXL-like | **1.47×** | **1.13** (cost 1.375) |

  Caveat: the reported throughput/cost ratios do not reproduce exactly from the table's own columns (1.55/2.0=0.78;
  1.47/1.375=1.07). Possibly a different normalization; record as reported.
- **This is the most direct quantitative evidence for the hypothesis:** one server, one workload, where the
  bandwidth-dense head goes to a "closer/higher-BW" tier and the tail goes to a "farther/capacity" tier. That beats both a
  uniform fast tier and the baseline on perf/$.

---

## 4. Time-scales and granularity: the two directions act on different axes
- (B) decisions are made at **seconds to minutes**, at page granularity: Google T=120 s, TPP 1–2 min intervals, TMTS cold
  ages of 2/4/8 min, KV$ P99 lifespan 97 s.
- (A) pays off per access, at **ns to µs**, whenever the bytes-per-FLOP of a kernel exceeds the machine's balance.
- A datum can be cold on the minute scale but streamed at high bandwidth when touched (e.g., a long-context KV block
  re-read once per decode step while its request is live, then idle for minutes). Placement must therefore be dynamic,
  hence Mooncake, LMCache, CachedAttention, and TokenStack's "workload-aware eviction".

---

## 5. Unifying frameworks

### 5.1 Five-minute rule (Gray & Putzolu 1987) and its revisits: the capacity/economics axis
Break-even re-reference interval:
T_be = (Pages per MB of fast tier / Accesses-per-second per slow device) × (Price per slow device / Price per MB of fast tier).
Keep a page in the faster tier iff its expected re-reference interval < T_be.

| Revisit | DRAM ↔ HDD | DRAM ↔ flash | Notes |
|---|---|---|---|
| Gray & Putzolu 1987 | 400 s for 1 KB pages, rounded to "5 min" | — | (from Appuswamy et al.) |
| Gray & Graefe 1997 | 266 s (4 KB) | — | (from Appuswamy et al.) |
| Graefe 2007 (CACM 2009) | 1.5 h | 15 min | |
| **Appuswamy, Borovica-Gajic, Graefe, Ailamaki, ADMS'17 / CACM Nov 2019** (https://renata.borovica-gajic.com/data/adms2017_5minuterule.pdf) | **4 hours** (4 KB); "the five-minute rule is valid today for 512KB pages" | SATA SSD 4 KB read: **7 min**. PCIe NVMe: **41 s / ~1 min**. DRAM↔3D XPoint: **47 s / 52 s** | SSD↔HDD: 1 day. "From 15 minutes in 2007 to mere 40 seconds today" |
| **Duwe et al., "The Five-Minute Rule for the Cloud: Caching in Analytics Systems," CIDR'25** (https://vldb.org/cidrdb/papers/2025/p4-duwe.pdf) | 2024 HDD 108 h (vs 12 h for 2018 HDD) | — | Cloud object stores (per-request pricing): caching pays at **2 requests/hour** (latency-sensitive) or **7 requests/s** (non-latency-sensitive) |
| **Zhang, Mailthody, …, Newburn, Hwu, "Five-Minute Rule 40 Years Later: A First-Principles Revisit for Modern Memory Hierarchy," arXiv 2511.03944 (v2 May 2026; ScaleFlux + NVIDIA + Stanford)** | — | **DRAM↔flash collapses from minutes to seconds**: at 512 B, ~34 s (CPU+DDR) → **~5 s (GPU+GDDR)**, a 7× reduction, with "Storage-Next" high-IOPS SSDs | Adds host IOPS, DRAM bandwidth/capacity, tail-latency feasibility. For coarse KV-tensor blocks the break-even is "even shorter". Explicitly says the method extends to CXL tiers but does not compute them. |
| Hao, Zhou, Yu, Stonebraker, SIGMOD'24 | — | — | **No CXL/RDMA break-even interval is computed.** Argues the 5-minute rule doesn't fit remote memory. Provisioning rule: size DRAM by the 5-minute rule, then swap DRAM for remote memory "until it saturates the remote memory bandwidth". Emulated remote memory at 0.1–5 µs and 5 GB/s, priced at 0.1/0.3/0.8× local. Up to 10× cost-effectiveness vs DRAM+disk, but that gain shrinks or reverses at 0.8× price. See Round-4 deltas §1. |

Interpretation for the CXL-era hierarchy:
- The economic boundary between "fast" and "slow" is set by **$/GB of the fast tier vs $/(access/s) of the slow tier**.
- When the slow tier's cost per access falls (high-IOPS SSDs, CXL with load/store at ~2–3× DRAM latency), the boundary shifts so
  that *more* data rationally lives farther away. Hence the case for B grows as far tiers get better per access.
- For HBM vs DDR the same logic applies with *bandwidth* as the scarce resource. Data whose required bandwidth per stored GB
  exceeds what DDR/CXL can supply per GB must be in HBM/PIM.

### 5.2 Roofline (Williams, Waterman, Patterson 2009): the compute axis
- Performance = min(peak FLOPS, I × BW), with I = FLOPs per byte moved from memory.
- Low I (decode ≈1, DLRM SLS ≈0.25, streaming kernels) sits in the bandwidth-bound region, so the payoff from (A) is
  ∝ internal/external bandwidth ratio (RecNMP: "lifting the roofline by 8×").
- High I (prefill ≈1000) is compute-bound; memory proximity barely matters.

### 5.3 A combined "access-density" map (my synthesis, built from 5.1 + 5.2 + MTrainS/MemProf metrics)
For each data object define:
- **Access density** ρ = bytes moved per second ÷ bytes stored (units 1/s, equivalently GB/s per GB). This is
  what MTrainS (per-table BW requirement vs size) and MemProf (bandwidth vs capacity CDF) measure, and what the five-minute rule
  thresholds (1/re-reference interval × bytes per access/page size).
- **Arithmetic intensity** I = FLOPs per byte moved.

Tier "supply" ratios [derived, order of magnitude; configs stated]:
| Tier | BW/capacity (GB/s per GB) |
|---|---|
| HBM3e (4.8 TB/s ÷ 144 GB) | ≈ 33 |
| GH200 LPDDR5X (512 GB/s ÷ 480 GB) | ≈ 1.1 |
| DDR5 server (12 ch × DDR5-4800 ≈ 460 GB/s ÷ 768 GB–1.5 TB) | ≈ 0.3–0.6 |
| CXL x8 Gen5 expander (~25–32 GB/s ÷ 128–512 GB) | ≈ 0.05–0.25 |
| Enterprise NVMe (~14 GB/s ÷ 8–30 TB) | ≈ 0.0005–0.002 |

Rule: put each object in the cheapest-$/GB tier whose BW/GB (and latency/MLP tolerance) covers its ρ.
Hao et al. SIGMOD'24 use exactly this stopping rule for the DRAM→remote step: replace local memory with remote memory
until remote bandwidth saturates. The five-minute rule is kept only for the memory↔disk step (§4.9). HeMem adds that
for asymmetric media, ρ_write and ρ_read must be checked separately (Optane write BW 11.2 vs DRAM 80 GB/s).
- High ρ & low I (streamed, low reuse, can't be cached on-chip) → HBM / PIM / NMP **(A)**.
- High ρ & high I → on-chip caches/SRAM absorb it; any DRAM tier works.
- Low ρ (cold, skewed tail) → CXL / pooled / compressed / flash **(B)**.
- Skewed workloads span this axis by ~4 orders of magnitude, so they need both ends.

This explains why the two directions are complementary rather than opposite: the conventional DDR DIMM is a single fixed
BW/GB point (~0.3–0.6) that is wrong for both ends of a skewed distribution.

---

## 6. Evidence against / complications for the hypothesis

1. **Far tiers are also bandwidth sources** (blurs "B = capacity only").
   - **Colloid** (Vuppalapati & Agarwal, SOSP'24, https://www.cs.cornell.edu/~ragarwal/pubs/colloid.pdf): Intel Cascade
     Lake / Ice Lake / Sapphire Rapids cores can generate 2.98× / 3.31× / 4.5× the memory interconnect bandwidth, and AMD
     Genoa 9.37×. Under moderate load "the default tier access latency can inflate to be **2.5×** larger than the latency of
     alternate tiers", and hottest-pages-in-fast-tier placement can be "**2.3×** worse than the optimal". Principle:
     balance *loaded* latencies across tiers. So for bandwidth-bound workloads CXL adds bandwidth, i.e., B helps the A problem.
   - **Wang et al., "Exploring and Evaluating Real-world CXL," arXiv 2405.14209 (MemVerge/UC Merced).** LLM inference benefits
     from DRAM+CXL page interleaving. Their object-level interleaving beats Linux uniform interleave by 65% on average.
   - Implication: the clean split is on **ρ relative to each tier's BW/GB**, not "hot → near, cold → far". When
     the near tier saturates, even hot data should spill to the far tier.
2. **Hotness ≠ criticality; MLP matters.** "Tiered Memory Management Beyond Hotness" (Liu et al., OSDI'25,
   https://www.usenix.org/system/files/osdi25-liu.pdf): "hot data is not always performance-critical". The amortized offcore latency
   metric (latency × MLP) beats four state-of-the-art tiering systems by up to 12.4×.
   - Streaming, high-MLP data tolerates far latency if far bandwidth suffices.
   - Pointer-chasing, low-MLP data (graphs, B-trees) does not tolerate far latency even when only lukewarm (Pond: GAPBS worst).
   - So a third axis, **latency sensitivity (MLP)**, cuts across the hypothesis.
3. **Workload class is a weak predictor.** Pond: within-class variability exceeds across-class variability, and every class has
   both <5% and >25% slowdown cases. The hypothesis must be stated per data object or page, not per workload type.
4. **Captured savings are far below measured idleness.** 50–80% idle (TPP, Pond) vs 20–33% actually moved in production
   (Google, TMTS, TPP). Re-access, migration overhead (Memstrata measured TPP using nearly an entire host core for one FASTER/YCSB VM), and tail
   SLOs eat the gap. This limits B's economic payoff (Q5 will cover "A Case Against CXL Memory Pooling", HotNets'23).
5. **KV-cache far tier may be modest.** Alibaba ATC'25: P99 KV lifespan 97 s for API traffic, and 2× HBM capacity approaches
   the ideal hit rate. Contrast LMCache's "far exceeding GPU memory" and Mooncake's multi-tier use for long-context Kimi
   traffic. The answer depends on workload category (API vs chat vs long-document vs agentic).
6. **PIM has a capacity cost**, so A itself has to be tiered (TokenStack: PIM-useful bytes are ~24% of HBM). This reinforces
   the hypothesis (skew everywhere) but also shows that "A for bandwidth-bound data" is true only for the *hot slice* of that data.

---

## 7. Source list (all accessed 2026-10-02)
- Lagar-Cavilla et al., ASPLOS'19. https://storage.googleapis.com/gweb-research2023-media/pubtools/5649.pdf
- Maruf et al., TPP, ASPLOS'23. https://arxiv.org/abs/2206.02878
- Li et al., Pond, ASPLOS'23. https://arxiv.org/abs/2203.00241
- Duraisamy et al., TMTS, ASPLOS'23. https://www.micahlerner.com/assets/pdf/adaptable.pdf
- Zhong et al., Memstrata, OSDI'24. https://www.usenix.org/system/files/osdi24-zhong-yuhong.pdf
- Maruf et al., Memtrade, SIGMETRICS'23. https://arxiv.org/abs/2108.06893
- Lee et al., Memtis, SOSP'23. https://multics69.github.io/pages/pubs/memtis-lee-sosp23.pdf
- Xiang et al., Nomad, OSDI'24. https://www.usenix.org/conference/osdi24/presentation/xiang
- Vuppalapati & Agarwal, Colloid, SOSP'24. https://www.cs.cornell.edu/~ragarwal/pubs/colloid.pdf
- Liu et al., Beyond Hotness, OSDI'25. https://www.usenix.org/system/files/osdi25-liu.pdf
- Mahar et al., Workload Behavior Driven Memory Subsystem Design for Hyperscale. https://arxiv.org/abs/2303.08396
- Wang et al., Real-world CXL. https://arxiv.org/abs/2405.14209
- Gholami et al., AI and Memory Wall. https://arxiv.org/abs/2403.14123
- Patel et al., Splitwise. https://arxiv.org/abs/2311.18677
- Zhong et al., DistServe. https://arxiv.org/abs/2401.09670
- Yuan et al., LLM Inference Unveiled (roofline). https://arxiv.org/abs/2402.16363
- Qin et al., Mooncake. https://arxiv.org/abs/2407.00079
- Gao et al., CachedAttention, ATC'24. https://www.usenix.org/system/files/atc24-gao-bin-cost.pdf
- LMCache tech report. https://arxiv.org/abs/2510.09665 ; https://lmcache.ai/tech_report.pdf
- Sheng et al., FlexGen. https://arxiv.org/abs/2303.06865
- Wang et al., KVCache Cache in the Wild, ATC'25. https://arxiv.org/abs/2506.02634
- Li et al., TokenStack (preprint). https://arxiv.org/abs/2605.05639
- Gupta et al., DLRM architectural implications, HPCA'20. https://arxiv.org/abs/1906.03109
- Ke et al., RecNMP, ISCA'20. https://arxiv.org/abs/1912.12953
- Kwon et al., TensorDIMM, MICRO'19. https://arxiv.org/abs/1908.03072
- Eisenman et al., Bandana, SysML'19. https://arxiv.org/abs/1811.05922
- Ardestani et al., SDM for DLRM inference. https://arxiv.org/abs/2110.11489
- Kassa et al., MTrainS. https://arxiv.org/abs/2305.01515
- Boroumand et al., ASPLOS'18. Full text: fulltext/09-google-workloads.pdf (slides: https://pdfs.semanticscholar.org/72e0/be5e9325607876a8ded66c1a06ddb5bf5f1f.pdf)
- Hao et al., SIGMOD'24. Full text: fulltext/3639286.pdf
- Raybuck et al., HeMem, SOSP'21. Full text: fulltext/3477132.3483550.pdf
- Appuswamy et al., Five-minute rule 30 years later. https://renata.borovica-gajic.com/data/adms2017_5minuterule.pdf
- Duwe et al., Five-Minute Rule for the Cloud, CIDR'25. https://vldb.org/cidrdb/papers/2025/p4-duwe.pdf
- Zhang et al., Five-Minute Rule 40 Years Later. https://arxiv.org/abs/2511.03944
- GH200 specs [secondary]: https://www.spheron.network/blog/nvidia-gh200-guide/ (NVIDIA datasheet not opened)
