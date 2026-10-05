# R3: Gap evidence (reviewer-flagged items), fact-check

Date: 2026-10-03. Method: WebSearch was **unavailable** (session search budget exhausted), so everything here comes from WebFetch/curl on known URLs: vendor pages, arXiv/OpenAlex/Crossref APIs, sitemaps, and Wikipedia (used as a pointer only).
Tags: [primary] = paper, spec, or company filing read directly. [vendor] = vendor marketing or press release. [press] = trade press. [secondary] = analyst blog, Wikipedia, or a commissioned white paper. [abstract only] = only the abstract was read. [derived] = my own arithmetic.
Where a fetch summary (not raw text) was the only access, it is noted.

---

## Round-4 deltas (full texts provided 2026-10-03; supersede the round-3 tags below)

Files are in `fulltext/` (see MANIFEST.md). For Hot Chips 2026 (HC'26) decks, the extracted PDF slide text is treated as [primary-vendor]. The model-generated vision description is [secondary] and is used only where noted.

### D1. HBM-on-CPU was not continued by either vendor; both went back to DDR/MRDIMM + CXL (+ big SRAM)

**Intel Diamond Rapids** (HC'26, Sistla, `DMR_at_Hot_Chips_2026_FINAL.pdf.md`):
- Slide 15 "SoC Overview":
  - Up to 256 cores; up to **1.28 GB of LLC**.
  - **16 channels, up to 8000 MT/s DDR5, up to 12800 MT/s MRDIMM**.
  - 128 lanes of PCIe Gen 6 / **CXL 3.0** / UPI 3.
- Slide 10: "Memory Subsystem with up to **1.6 TB/s** bandwidth".
- Slide 11: the Memory Value Function "supports CXL memory (1LM or Flat2LM) and mirroring"; the encryption engine "supports DDR or CXL memory encryption".
- Slide 19: the directory moves on-die (snoop filter), which "eliminat[es] directory storage in memory".
- **HBM appears nowhere in the deck's slide text.**
- So "no HBM-Xeon successor" now holds for two generations (Granite Rapids, Diamond Rapids). It is backed by a full memory-subsystem description rather than by absence from Wikipedia [V].
- The [D from S] figure of 1.64 TB/s for 16 × MRDIMM-12800 is now [V] (Intel says "up to 1.6 TB/s").

**AMD EPYC Venice** (HC'26 MI400 system deck, slide 3): "96 High Frequency Cores … **1.6 TB/s Memory Bandwidth**" [V]. This corroborates the same figure from the AMD side.

**Fujitsu MONAKA**, the successor of A64FX (HC'26, Okazaki):
- Slide 5:
  - 144 cores × 2 sockets.
  - **"DDR5 12 channels 8000+MT/s"**.
  - **"PCI Express 6.0 (CXL3.0) 96 lanes"**.
  - 3D chiplet: 2 nm core die on a **5 nm SRAM die that holds all of the LLC**.
  - Samples now; products in 2027.
- Slide 21: STREAM Triad ~**500 GB/s (estimated)** per socket.
- Slide 22: "Selected for FugakuNEXT", plus "NVLink Fusion for AI/HPC".
- **HBM appears nowhere.** A64FX's HBM2-only design (32 GB, 1 TB/s) was replaced by DDR5 + CXL at about half the bandwidth and much higher capacity [V].
- Slide 6 gives a reason only at the chip level: slowing "cache memory capacity" scaling and "drastic increase in cost" at 2 nm, which is why the SRAM die is separate. **No stated reason for dropping HBM** [V].

**Pattern [Syn]:** both HBM CPUs (Xeon Max, A64FX) gave way to:
- (i) wider/faster DDR (MRDIMM-12800, 16 channels);
- (ii) **stacked or huge SRAM LLC** (DMR 1.28 GB; MONAKA SRAM die);
- (iii) **CXL 3.x for capacity**.
The CPU version of "A" moved from HBM to SRAM-on-logic, and capacity moved to B.

### D2. A64FX capacity constraint: still unsupported, but the comparison point is now sharper

Kang, Ghosh, Kandemir, Márquez, "Studying CPU and memory utilization of applications on Fujitsu A64FX and Nvidia Grace Superchip", MEMSYS '24 [P, full text read]:
- The paper **does not identify 32 GB as a constraint**. Its inputs (Rodinia; graphs up to ~18M vertices and ~584M edges) were chosen to fit, and capacity is never tested.
- Platform figures:
  - A64FX: 4 × 8 GB HBM2, 1 TB/s.
  - Grace superchip: 2 × 240 GB LPDDR5X (480 GB).
- "Grace exhibits about 30% better overall bandwidth than A64FX" on default STREAM. With A64FX's zero-fill option enabled, A64FX comes within ~3% of Grace.
- Grace's LLC is 15× larger than A64FX's per-CMG L2 (117 MiB per NUMA node).
- So LPDDR5X on-package matched HBM2's STREAM bandwidth at **15× the capacity** [D from P].
- **Verdict:** keep "A64FX had no DDR" as a fact. Do **not** claim capacity was a documented constraint for Fugaku apps [unverified].

### D3. IBM MXT [P, full text; note on edition]

The provided PDF is Tremaine, Smith, Wazlowski, "IBM Memory eXpansion Technology (MXT)" (IBM T.J. Watson, dated 10/30/00). Its abstract is identical to the 7-author IBM JRD 45(2) 2001 article. Cite it as the IBM technical paper, or check the page numbers against JRD.

Numbers:
- Typical compression **2:1–6:1** (bounded 1:1–64:1). "serve[s] to **halve the main memory cost**, without any significant degradation".
- **Architecture:** a large shared L3 cache (32 MB in 0.25 µm, 64 MB in 0.18 µm; directory on-chip, data in external DDR SDRAM, $50–60) sits in front of compressed main memory.
  - 1 KB lines, stored in 1–4 × 256 B sectors.
  - Compression: 4 parallel LZ77 engines, 256 cycles per line.
  - Decompression: 4 engines × 2 B/cycle (16 B/cycle at 2× clock).
- **Shipped** in the ServerWorks "Pinnacle" north bridge (Pentium III/Xeon).
- **Results:**
  - Shared-cache hit rate ~98% typical, as low as 94% for TPC-C, SAP, and Lotus Notes.
  - **SPECint2000 within one point** of a conventional system (IBM 1U, 512 MB, 733 MHz PIII).
  - Compaq DL360 configuration with half the memory: **$13,081 → $7,103 (>40% system saving)**.
- **Framing quote (relevant to the thesis):** the shared cache "architecturally insulates the overall system performance from access latency to the main memory; thereby opening opportunities for trading off increased memory access latency for greater function. For example, remote/distributed … features may be incorporated."
- So MXT is itself a **near-cache + cheap-far-tier** design. That is the A+B pairing, in 2000 [Syn].

### D4. ZeRO-Offload [P, full text; USENIX ATC'21]

- **13B parameters on one V100** at 40 TFLOPS (vs 30 TFLOPS at 1.4B for PyTorch, the largest that fits).
- **>70B on one DGX-2** with model parallelism (4.5×).
- Near-linear scaling to 128 GPUs.
- **Placement rule:** computations of O(M·B) (forward/backward) stay on the GPU; O(M) work (norm, optimizer update) may go to the CPU.
- "CPU memory bandwidth is at least an order of magnitude faster than the PCI-E bandwidth … GPU memory is another order of magnitude faster than even the CPU memory."
- **Minimum CPU–GPU traffic is 4M** (M = parameter count). The only partitions achieving it put fp16 parameters with forward/backward on the GPU.
- Offloading fp16 gradients and the update gives **8× memory savings** (Table 1).
- CPU-Adam is up to **6× faster** than the PyTorch CPU optimizer.
- One-step delayed parameter update overlaps CPU and GPU work.
- **Verdict:** confirms §5.3's "compute follows the far tier" (the optimizer runs where the optimizer state lives) [P].

### D5. Peer HBM as a capacity tier: now [P]

**SiDP** (Zhao & He, arXiv 2605.28095, full text):
- Framed explicitly as "resource arbitrage: … high-speed interconnects often remain underutilized, while HBM capacity is extremely scarce".
- Each GPU owns a disjoint subset of layers. Peers fetch weights on demand over NVLink ("Weight-as-a-Service") or send activations to the owner ("Compute-as-a-Service").
- Testbeds: 8× H20/H200 (NVLink 4) and 8× B200 (NVLink 5).
- Results: **up to 1.8× usable KV capacity and up to 1.5× throughput** vs vLLM.
- Limits:
  - Offline/batch inference only; intra-node only.
  - At B = 1 it is **12% slower** than vLLM.

**SwiftCache** (Hu et al., arXiv 2606.16135, full text):
- Testbed: 4× H20 (96 GB), NVLink 400 GB/s bidirectional, with all four GPUs sharing **32 GB/s PCIe 4.0** to 128 GB of host memory.
- The PCIe reload of historical KV takes ~75 ms vs 27 ms of compute at 5k tokens. Transfer is 73.2% → 86.2% of latency as history grows to 50k tokens.
- Mechanism: co-located models donate idle HBM over NVLink, and only the active layer's KV stays local.
- Results: **P99 TTFT up to −69%**, **max context up to 3.98×** vs vLLM/SGLang.

**AMD Helios / MI455X** (HC'26 MI400 system deck) [V]:
- Slide 11: "**Distributed shared memory model**". An exporter's HBM buffer is mmap'ed by an importer over UALoE with a "memory registration key"; DMA engines sit next to the UALoE links (slide 12).
- Per rack: 72 GPUs, **31 TB HBM4, 1.7 PB/s**, 260 TB/s scale-up.
- Per GPU: 1.8 TB/s per direction over UALoE.
- Peer-HBM mapping is thus a first-class platform feature at AMD as well as NVIDIA.

**NVIDIA Rubin** (HC'26) [V]:
- Slide 16: NVLink 6 at **3.6 TB/s per GPU** all-to-all.
- Slide 15: "Counted Writes Lower NVLink Latency" for GPU-to-GPU stores (removes the MEMBAR + ack round trip).

### D6. SOCAMM2 [V, Micron press release 22 Oct 2025, parsed from mhtml]

- **192 GB per module** (sampling), "**speeds up to 9.6 Gbps**", 1γ DRAM.
- "50% more capacity in the same compact footprint" than SOCAMM.
- ">20% improvement in power efficiency" vs the previous LPDDR5X generation.
- "improves power efficiency by **more than two-thirds** compared with equivalent RDIMMs", "a module **one-third the size**".
- Full-rack AI systems "can include **greater than 50 terabytes of CPU-attached low-power DRAM**" (footnote: NVL144 rack).
- Capacity → latency claim: the added capacity "can significantly reduce **TTFT by more than 80%**". Footnote: Llama 3 70B on GH200 NVL2 (288 GB HBM3E + 1 TB LPDDR5X) **using LMCache**, i.e. KV offload to the LPDDR tier.
  - This is a vendor's direct claim that a **capacity tier behind HBM** is what pays [V].
- The Micron product page (round 3) says 256 GB. The two are consistent (later SKU).

### D7. 2026-platform rows for the §2 access-density table [D from V, HC'26 slide text]

| Platform / tier | Capacity | Peak BW | GB/s per GB | Source |
|---|---|---|---|---|
| NVIDIA Rubin, HBM4 (fleet-level) | 11 PB | 800 PB/s | **~73** | Rubin slide 8 ("100 MW AI factory") |
| AMD MI455X, HBM4 | 432 GB | 23.3 TB/s | **~54** | MI400 system slide 3 |
| AMD Helios rack, HBM4 | 31 TB | 1.7 PB/s | **~55** | MI400 system slide 5 |
| NVIDIA Vera, SOCAMM LPDDR5X-9600 | 1.5 TB | 1.2 TB/s | **~0.8** | Vera slide 28 |
| Intel Diamond Rapids, 16 × MRDIMM-12800 | 1 / 2 / 4 TB (64 / 128 / 256 GB DIMMs, 1DPC) [D] | 1.6 TB/s | **1.6 / 0.8 / 0.4** | DMR slides 10, 15 |
| AMD Venice | n/a | 1.6 TB/s | same as DMR at equal capacity | MI400 system slide 3 |
| Fujitsu MONAKA, 12 × DDR5-8000 | n/a | 768 GB/s peak [D]; 500 GB/s STREAM (estimated) | ~0.4–0.8 at 1–2 TB [D] | MONAKA slides 5, 21 |
| Xeon Max (historical), HBM2e | 64 GB | 1.64 TB/s peak; ~0.7 sustained | **~26 peak / ~11 sustained** | arXiv 2505.14294 [P] |
| A64FX (historical), HBM2 | 32 GB | 1 TB/s | **~32** | Wikipedia / MEMSYS'24 |

- **HBM-class density rose from ~33–42 (HBM3E) to ~54–73 GB/s per GB (HBM4)**. The gap above DDR-class (~0.4–1.6) widened.
- **Vera's LPDDR tier sits at ~0.8, the same as DDR**, so LPDDR on-package is a *power* play, not a density play.
  - Vera slide 20: bandwidth/W of **5×** for Vera LPDDR5X-9600 vs 1× for 16-channel DDR5-8000 and 12-channel DDR5-6400, and **0.8× for 16-channel MRDIMM-12800**. The bar-to-label mapping comes from the vision description [V/secondary].
- Vera also has: 88 cores, 164 MB L3, NVLink-C2C 1.8 TB/s, PCIe 6 x16 at 256 GB/s, **CXL 3.1** (slide 28).
- Rubin and Vera decks: the platform adds a separate "**Context Memory Storage**" rack (Vera BlueField-4 STX), a dedicated KV-cache tier [V].

### D8. Claims to change in SYNTHESIS_DRAFT.md v0.3 (not edited; quote → correction → source)

1. **§2 table, HBM3E row ("~33–42 [V]").**
   - Correction: add an HBM4 row at **~54–73 GB/s per GB** (MI455X 432 GB / 23.3 TB/s; Helios 31 TB / 1.7 PB/s; Rubin fleet 11 PB / 800 PB/s) [D from V].
   - Source: HC'26 MI400 system slides 3 and 5; Rubin slide 8.
2. **§2 table, LPDDR5X row ("~0.8–1.1 [V/D]; vendor claims 1/8 of DDR5 per GB/s").**
   - Correction: add Vera at **1.5 TB / 1.2 TB/s ≈ 0.8** [D from V].
   - Add NVIDIA's HC'26 claim: **5× bandwidth/W vs 16-channel DDR5-8000 and 12-channel DDR5-6400; 16-channel MRDIMM-12800 at 0.8×** [V; bar mapping from the vision description].
   - Source: Vera slides 20 and 28.
3. **§2 table, MRDIMM row ("MRDIMM-8800, 12 ch, 64 GB ~1.1").**
   - Correction: add a 2026 row: **16 × MRDIMM-12800, up to 1.6 TB/s** (Diamond Rapids; Venice "1.6 TB/s"). That gives **1.6 / 0.8 / 0.4 GB/s per GB** at 1 / 2 / 4 TB [D from V].
   - Source: DMR slides 10 and 15; MI400 system slide 3.
4. **§2 item 2: "Intel's HBM CPU … had no HBM successor in Granite Rapids … [V; absence of evidence, no Intel statement]" and "MRDIMM-12800 … ~1.64 TB/s [D from S]".**
   - Correction: "Neither HBM CPU was continued. Diamond Rapids (HC'26) specifies 16 × DDR5-8000 / MRDIMM-12800 (up to 1.6 TB/s), CXL 3.0 with 1LM/Flat2LM, and 1.28 GB of LLC, with no HBM. Fujitsu's A64FX successor MONAKA (FugakuNEXT) uses 12 × DDR5-8000+ and CXL 3.0, with LLC on a 3D-stacked SRAM die."
   - Change the tag to [V: HC'26 slide text]. The 1.6 TB/s figure becomes [V].
   - Add the [Syn]: on CPUs, A migrated from HBM to stacked SRAM, and capacity went to CXL.
5. **§2 item 3, §9 (l.328), and open question 5 (l.341): "Nothing ships in volume between ~1 and ~35 GB/s per GB".**
   - Correction: the HBM CPUs **did** ship there and were discontinued:
     - Xeon Max ~26 peak / ~11 sustained;
     - A64FX ~32 (Fugaku, 158,976 nodes);
     - SX-Aurora ~25–32 [D].
   - Rephrase as: "the band was occupied by HBM CPUs and then *vacated*". That is a stronger finding.
   - Also check GDDR accelerators and Apple-class on-package LPDDR, which likely sit at ~4–20 GB/s per GB [background knowledge, **unverified**; e.g. 48 GB GDDR6 cards at ~0.9 TB/s ≈ 18].
6. **§4 B1 cell: "IBM MXT ~2:1 [abstract only]".**
   - Correction: "IBM MXT (2000, ServerWorks Pinnacle): typical 2:1–6:1; SPECint2000 within 1 point of a conventional server; a 32–64 MB shared cache in front of compressed DRAM; half the memory gave >40% system cost saving [P: Tremaine et al.]."
   - Optionally add to §1 or §5 MXT's own framing: the cache "insulates … performance from access latency to the main memory … remote/distributed … features may be incorporated". This is an A+B pairing in 2000 [P].
7. **§5.1, "Xeon Max ships 64 GB … A64FX ships 32 GB of HBM2 with no DDR [V]".**
   - Correction: add "…and its successor MONAKA reverts to DDR5 + CXL [V: HC'26]".
   - Add: "Grace's on-package LPDDR5X matches A64FX's HBM2 STREAM bandwidth (+30% default, −3% with A64FX zero-fill) at 15× the capacity [P: Kang et al. MEMSYS'24]."
   - Do **not** state that capacity constrained Fugaku apps [unverified].
8. **§5.1, "Using it as a capacity tier is still research (SwiftCache, SiDP) [abstract only]".**
   - Correction: tag [P]. Add "SiDP: up to 1.8× KV capacity / 1.5× throughput, offline only, 12% slower at batch 1; SwiftCache: NVLink 400 GB/s vs shared PCIe 32 GB/s, −69% P99 TTFT, 3.98× context."
   - Add that **AMD Helios exposes a 'distributed shared memory model' (mmap of peer HBM over UALoE)** [V: HC'26 MI400 slide 11], and that **Rubin NVLink 6 is 3.6 TB/s per GPU** [V].
9. **§5.1, add the strongest vendor evidence for "A creates demand for capacity tiers":**
   - Micron SOCAMM2: 192 GB per module, 9.6 Gbps, ">50 TB of CPU-attached LPDRAM" per NVL144 rack.
   - ">80% TTFT reduction" from the added capacity (GH200, LMCache KV offload) [V: Micron PR 22 Oct 2025].
   - NVIDIA's separate "Context Memory Storage" rack [V: HC'26].
10. **§5.1 ZeRO bullet / §5.3 "Deployed examples come from training".**
    - Add ZeRO-Offload [P]: "CPU memory bandwidth ≥10× PCIe, GPU memory another 10×". O(M) work goes to the CPU, giving 8× GPU memory savings at the minimum 4M traffic. CPU-Adam is 6× faster than the PyTorch CPU optimizer. 13B parameters on one V100.
    - This strengthens "compute follows the far tier" with a second primary source.
11. **§1 table (H100 HBM ~300–330 ns, etc.)** — no change.
    - Optional: add Vera's CXL 3.1 and Diamond Rapids' CXL 3.0 Flat2LM as evidence that CXL is now a default CPU attach on all three 2026 server CPUs [V].

---

## 1. HBM-on-CPU: Xeon Max, A64FX, SX-Aurora

**Status: mostly verified.** "No HBM successor" is supported only by absence of evidence. The A64FX capacity constraint is **unverified**.

- **Xeon Max (SPR-HBM)** [vendor]
  - 64 GB HBM2e in 4 stacks, ">1 GB per core".
  - Intel says it can "eliminate DDR … for workloads that fit within 64GB" (HBM-only operation).
  - Source: https://www.intel.com/content/www/us/en/products/details/processors/xeon/max-series.html
- **Measured bandwidth (Xeon Max 9468, flat SNC4 mode)** [primary, full text read]
  - Each of the 4 tiles has 1 HBM2e stack (16 GB, 409.6 GB/s peak) plus 2 DDR5 channels (32 GB, 76.8 GB/s peak).
  - Socket peak: ~1.64 TB/s HBM vs ~307 GB/s DDR [derived].
  - STREAM achieves only **~700 GB/s HBM vs ~200 GB/s DDR**.
  - HBM pointer-chase latency is **~20% higher** than DDR.
  - Only **60–75% of data needs to be in HBM** to reach 90% of attainable performance.
  - Source: Vaverka, Vysocky, Riha, "Heterogeneous Memory Pool Tuning", arXiv 2505.14294.
  - The flat (NUMA) mode is shown there, and HBM-only is referenced. Cache mode is not described in what I read: **[mode list = background knowledge, not re-verified]**.
- **Launch and deployment** [secondary]
  - Launched 10 Jan 2023 (Wikipedia, Sapphire Rapids).
  - Aurora: 10,624 nodes × 2 Xeon Max.
- **Did HBM continue in later Xeons? No evidence that it did.** [secondary, absence of evidence]
  - The Wikipedia Granite Rapids article has no HBM SKU. Its tables list **MRDIMM-8000/8800** instead ("up to DDR5-8800 across 12 channels").
  - The Diamond Rapids entry mentions only PCIe 6, CXL 3, and APX.
  - I found **no primary Intel statement** cancelling or continuing HBM-Xeon: **[unverified]**.
  - Interpretation [synthesis]: the CPU-HBM slot was refilled by MRDIMM, i.e. the "middle improving".
- **Fujitsu A64FX** [secondary]
  - 32 GB HBM2 at 1 TB/s, 4 NUMA CMGs, no DDR.
  - Fugaku: 158,976 nodes × 32 GiB, ≈ 5.1 PB in total (Wikipedia A64FX and Fugaku).
  - Was **capacity a noted constraint for Fugaku apps?** **[unverified]**. Wikipedia is silent, the arXiv API returned nothing, and I could not open the candidate papers (BWA-MEM2 on A64FX, TCBB 2023, https://doi.org/10.1109/tcbb.2023.3264514; the VPIC memory-usage study, JOCS 2022, https://doi.org/10.1016/j.jocs.2022.101566).
  - I could not verify the successor memory choice (MONAKA / FugakuNEXT; DDR5 + CXL is my background belief) because the pages 404'd.
- **NEC SX-Aurora TSUBASA** [secondary, Wikipedia]
  - PCIe vector-engine cards hosted by an x86 server with DDR.
  - 24–96 GB HBM2 per VE, up to 2.45 TB/s.
  - Launched Feb 2018.
  - Architecturally it is HBM-only near compute, with the host's DDR as the "far" tier.

## 2. Cerebras MemoryX / weight streaming

**Status: verified** [vendor].

- **Announcement** (24 Aug 2021): MemoryX **4 TB – 2.4 PB**, "200 billion to 120 trillion" parameters; SwarmX links up to **192 CS-2** (163M cores).
  - Source: https://www.cerebras.ai/press-release/cerebras-systems-announces-worlds-first-brain-scale-artificial-intelligence-solution/
- **Mechanism** (Cerebras blog): activations stay on the wafer.
  - Forward pass: "MemoryX streams weights to the CS-2".
  - Backward pass: the CS-2 streams gradients back.
  - **The optimizer update runs in MemoryX**, which holds the optimizer state.
  - SwarmX is a broadcast/reduce fabric, so the cluster runs pure data parallelism.
  - A single CS-2 holds models up to ~10B parameters on the wafer.
  - Source: https://www.cerebras.ai/blog/linear-scaling-made-possible-with-weight-streaming
- **CS-3 / WSE-3** (2024 press):
  - 44 GB on-wafer SRAM, 125 PF.
  - External memory **1.5 TB, 12 TB, or 1.2 PB**, for up to 24T parameters.
  - Up to 2,048 CS-3s.
  - Source: https://www.cerebras.ai/press-release/cerebras-announces-third-generation-wafer-scale-engine
  - WSE-2 had 40 GB SRAM [secondary].
- Note [synthesis]: this is the extreme form of the "near SRAM plus far bulk store" split. The far tier also hosts compute (the optimizer).

## 3. ZeRO-Offload / ZeRO-Infinity

**Status: verified** [primary, ZeRO-Infinity full text read from the arXiv PDF].

- **ZeRO-Offload** [abstract only], arXiv 2101.06840 (USENIX ATC'21):
  - Over 13B parameters on a single V100 (10× PyTorch).
  - 40 TFLOPS/GPU on a 10B model.
  - Near-linear scaling to 128 GPUs.
  - It offloads optimizer state, gradients, and the optimizer step to the CPU.
- **ZeRO-Infinity** (Rajbhandari et al., SC'21, arXiv 2104.07857):
  - **Bandwidth-requirement model** (Sec. 4): efficiency = ait·bw / (ait·bw + peak_tp).
  - Compute per iteration is 2·4·bsz·seq·params (with activation checkpointing).
  - **AIT by data class:**
    - Parameters and gradients: **seq × bsz**.
    - Optimizer states: **seq × bsz / 4** (2×16 B/param moved).
    - Activation checkpoints: **24 × hd × ci**.
  - **Derived per-tier requirements** (V100 DGX-2, peak_tp = 70 TFLOPS/GPU):
    - Parameters and gradients: **>70 GB/s** for >50% efficiency even at the smallest batch, "close to the GPU-GPU bandwidth".
    - Optimizer states need ~4× more. 90% efficiency at batch size 2 per GPU needs **~1.5 TB/s**, "greater than even the GPU memory bandwidth". This is why the optimizer step goes to CPU or NVMe, where the data lives.
    - Activation checkpoints: **~2 GB/s** gives >50% efficiency at hd = 2K; **<1 GB/s** at hd ≥ 8K.
    - Summary line: "the bandwidth must be greater than 70GB/s, 1.5TB/s, and 1-4 GB/s" for the three classes.
  - **Available per-tier bandwidth** (DGX-2):
    - A single GPU from CPU or NVMe is PCIe-limited to ~12 GB/s.
    - Bandwidth-centric partitioning (parallel all-gather) gives ~48 GB/s CPU and ~25 GB/s NVMe per node (3.0 / 1.6 GB/s per GPU).
    - At 64 nodes: **>3 TB/s aggregate CPU-memory bandwidth and >1.5 TB/s aggregate NVMe bandwidth**.
    - Capacity: 1.5 TB CPU DRAM per DGX-2; a 100T-parameter model's states fit in the aggregate NVMe of 96 nodes.
  - **Results:**
    - **32T parameters on 512 V100s** (32 DGX-2), 50× 3D parallelism.
    - **>25 PFLOPS** sustained.
    - **1T parameters fine-tunable on one DGX-2**.
    - 49 TFLOPS/GPU peak; drops to 43 (10T) and 34 (20T) TFLOPS/GPU.
  - **Table 3 (future hardware)**, for 512 devices to stay efficient:

    | Compute vs V100 | Slow-memory BW per device | Aggregate | GPU-GPU |
    |---|---|---|---|
    | 1× | 3 GB/s | 1.5 TB/s | 70 GB/s |
    | 10× | 30 GB/s | 15 TB/s | 700 GB/s |
    | 100× | 300 GB/s | 150 TB/s | 7 TB/s |

  - Key quote (Conclusion): "It is no longer necessary to fit DL training on ultra-fast but expensive and limited memory like HBM2… leveraging cheap and slow, but massive, CPU or NVMe memory in parallel across multiple devices to achieve the aggregate bandwidth necessary."
- Implication [synthesis]: far memory works when **AIT per byte is high** (parameters, activation checkpoints). The low-AIT class (optimizer state) needs either more bandwidth than HBM provides or **moving the compute to the data** (CPU Adam). This is one paper that motivates both A and B at once.

## 4. Optane discontinuation

**Status: verified (date and charge). The economic analysis is secondary.**

- **Intel Q2-2022 earnings release** [primary], 28 Jul 2022:
  - "In Q2 2022, we initiated the winding down of our Intel Optane memory business."
  - **$559M inventory impairment**.
  - The release gives **no stated reason**.
  - Source: https://www.intc.com/news-events/press-releases/detail/1563/intel-reports-second-quarter-2022-financial-results
- **Micron exit, 16 Mar 2021** [primary]:
  - "insufficient market validation to justify the ongoing high levels of investments required".
  - Resources shifted "to focus on accelerating market introduction of **CXL-enabled memory products**".
  - The Lehi fab was sold to Texas Instruments for $900M [secondary, Wikipedia].
  - Source: https://investors.micron.com/news-releases/news-release-details/micron-updates-data-center-portfolio-strategy-address-growing
- **Analyst economics (Jim Handy, Objective Analysis)** [secondary]:
  - Optane DIMMs were priced at **~half of DRAM $/GB** while costing more than DRAM to make, so they were sold at a loss. "3D XPoint must be less expensive than DRAM. If it were not, Optane's target customers would simply purchase more DRAM." https://thememoryguy.com/intels-optane-dimm-price-model/
  - Estimated Intel losses of **~$2B (2017), ~$2B (2018), ~$1.5B (2019)**. Break-even was reached around Q2-2020 only through scale. https://thememoryguy.com/did-3d-xpoint-costs-reach-break-even/
  - Micron: ~**$400M/yr** underloading losses. https://thememoryguy.com/micron-bows-out-of-3d-xpoint-business/
- **Link to DRAM pricing**:
  - Handy's framing makes DRAM $/GB the ceiling on the cheaper-byte tier's price. Any tier must undercut DRAM by about 2× and still beat it on cost, which requires DRAM-like volume.
  - I found **no source tying the July-2022 decision specifically to the 2022 DRAM price decline**: **[unverified]**.

## 5. Hardware memory compression ("cheaper bytes without distance")

- **IBM MXT** (Tremaine et al., IBM JRD 45(2), 2001, doi:10.1147/rd.452.0271) [abstract only, via OpenAlex]:
  - A large shared cache sits between the processor bus and compressed main memory.
  - Real-time HW compression/decompression; sector-based allocation.
  - Ratio is bounded 1:1–64:1, with **typical ratios 2:1–6:1** in real-world applications.
  - Claims to "more than double the effective size of the installed main memory without significant added cost".
  - Wikipedia notes a 5–20% system performance gain [secondary].
- **IBM Power Active Memory Expansion (AME)** [vendor docs]:
  - The OS keeps compressed and uncompressed pools.
  - Example: expansion factor 2.0 makes 20 GB → 40 GB.
  - Example cost: factor 2.83 needs ~0.5 physical cores of overhead on a 4-core LPAR (~12%).
  - Hardware 842 accelerators date from POWER7+ (2012) [secondary, Wikipedia].
  - Source: https://www.ibm.com/docs/en/aix/7.3?topic=management-active-memory-expansion-ame
- **POWER9 NX-gzip / z15 Integrated Accelerator for zEDC**: these are **data/storage compression accelerators, not transparent main-memory compression** [background knowledge, not fetched]. The reviewer's framing should be narrowed to MXT and AME.
- **Google zswap far memory**: already in the notes (~20% of data, 4–5% memory TCO, ~6 µs). Cross-ref q2/q4.
- **Marvell Structera A/X**: already in the notes (inline LZ4; "effective cost per GB roughly half"; 1.8–2× compression) [press, vendor claim]. Cross-ref q2, q5, r2a.

## 6. GPU scale-up domains as memory-semantic disaggregation

- **GB200 NVL72** [vendor]:
  - 72 GPUs in "a 72-GPU NVLink domain that acts as a single, massive GPU".
  - **13.4 TB HBM3E, 576 TB/s**; NVLink **130 TB/s** total (= 1.8 TB/s × 72).
  - 17 TB LPDDR5X at 14 TB/s behind NVLink-C2C.
  - Source: https://www.nvidia.com/en-us/data-center/gb200-nvl72/
- **NVLink 5** = 18 links × 50 GB/s = **1.8 TB/s per GPU** [secondary, Wikipedia].
- **NVLink 6 (Vera Rubin NVL72)**: 3.6 TB/s per GPU, 260 TB/s per rack [secondary, Wikipedia].
- **NVLink Fusion** was announced 18–19 May 2025 (licensing NVLink to third-party CPUs and accelerators) [secondary: Wikipedia; UALink white paper].
- **UALink 200G 1.0**:
  - Published **April 2025**.
  - **200G per lane, up to 1,024 accelerators per pod** [vendor, consortium spec page https://ualinkconsortium.org/specification/].
  - Memory semantics: **read, write, atomics** in 64–256 B transactions.
  - **57-bit fabric address space (128 PB)**. "any accelerator [can] directly address memory on any other accelerator".
  - ~1 µs round-trip target; 93% bandwidth-efficiency target.
  - Source: the TASK Consultancy white paper commissioned by the consortium [secondary/vendor]: https://ualinkconsortium.org/wp-content/uploads/2026/01/UALink_White_Paper_Publication_Candidate_FINAL_VERSION.pdf
  - UALink 2.0 (Common) is listed with in-network compute; Wikipedia gives April 2026.
- **Is peer HBM used as far memory in practice?** Partly yes.
  - **NVIDIA NIXL** (used by Dynamo) [vendor, GitHub README] moves KV cache between HBM, CPU DRAM, and storage over UCX, NVLink, and GDS for disaggregated inference. This is KV movement, not load/store pooling. https://github.com/ai-dynamo/nixl
  - **SwiftCache** (arXiv 2606.16135, Jun 2026) [abstract only]: co-located models lend idle HBM over NVLink to hold other models' prefix KV caches. Up to 69% lower P99 TTFT; 3.98× longer max context vs vLLM/SGLang.
  - **SiDP** (arXiv 2605.28095, May 2026) [abstract only]: each layer's weights live on one GPU, and peers stream them over NVLink ("weight-as-a-service"). Up to 1.8× more KV capacity and 1.5× throughput vs vLLM (H20, H200, B200).
  - NCCL and NVSHMEM P2P load/store over NVLink is standard practice, but I **could not fetch the NVSHMEM docs** (empty page): **[unverified here, background knowledge]**.
  - Assessment: peer-HBM-as-capacity is **research and emerging practice (2025–26)**. Production use is mainly KV transfer for disaggregated prefill/decode, plus the "one big GPU" marketing.

## 7. "The middle improving": MRDIMM, CAMM2, SOCAMM

- **MRDIMM gen 1** [vendor, Micron]: **8800 MT/s**, 32–256 GB, Xeon 6 only.
  - "39% bandwidth upgrade" vs DDR5 RDIMM data rates.
  - "up to 40% lower latency" (loaded).
  - Uses an on-DIMM mux across ranks.
  - Gen 2 is "under JEDEC standardization".
  - Source: https://www.micron.com/products/memory/dram-modules/mrdimm
- **Granite Rapids**: up to DDR5-8800 MRDIMM on 12 channels [secondary, Wikipedia]. That is 70.4 GB/s per channel, ~845 GB/s per socket [derived].
- **AMD EPYC 9006 (Venice)**: 16 channels; RDIMM up to 8000, **MRDIMM up to 12800 MT/s** [secondary, Wikipedia DDR5]. That is 102.4 GB/s per channel, ~1.64 TB/s per socket [derived], about equal to Xeon Max's HBM *peak* (and above its ~0.7 TB/s achieved) [derived].
- **CAMM2**: JEDEC standard on **5 Dec 2023** [secondary, Wikipedia].
- **LPCAMM2** [vendor, Micron]: LPDDR5X up to **9600 MT/s**; "up to 61% less active power" and "80% less standby power" than DDR5 SODIMM; up to 64% space saving. https://www.micron.com/products/memory/lpddr-modules/lpcamm2
- **SOCAMM/SOCAMM2** (Micron and NVIDIA, LPDDR5X server modules):
  - Micron page: **256 GB SOCAMM2** on 32 Gb 1γ dies, "33% more capacity" than its predecessor (192 GB implied). https://www.micron.com/products/memory/lpddr-modules/socamm [vendor]
  - Wikipedia: ~90 × 14 mm, 694 pins, 128 GiB (256 GiB with denser chips) [secondary].
  - The often-quoted "2.5× RDIMM bandwidth at one-third power / one-third size" claims **could not be fetched**: **[unverified]**.

## 8. Computational storage (precedent for "compute follows the far tier")

**Status: verified as "commercially available but limited adoption". Product outcomes are only partly verified.**

- **Barbalace & Do, "Computational Storage: Where Are We Today?", CIDR 2021** [primary, full text read] https://www.cidrdb.org/cidr2021/papers/cidr2021_paper29.pdf
  - Notes that Samsung SmartSSD, NGD, ScaleFlux, and Eideticom NoLoad were on sale.
  - "these devices are cumbersome to program and reason with, which may hinder their wide adoption".
  - "no software nor hardware support for heterogeneous resource management… security, consistency and general usability".
  - "several challenges prevented the broad usage and adoption of computational SSDs": weak embedded processors or FPGAs, high-latency in-SSD DRAM, and no programming model.
  - "showing improved performance and energy reductions is not enough to persuade the widespread adoption".
- **Standards exist**:
  - SNIA Computational Storage Architecture & Programming Model **v1.1** and CS API v1.1 [vendor/standards body] https://www.snia.org/computational
  - NVMe lists the **Computational Programs Command Set** and **Subsystem Local Memory (SLM) Command Set** (versions and dates not shown on the page) https://nvmexpress.org/specifications/
- **Outcome evidence I could NOT verify**: NGD Systems' shutdown; ScaleFlux pivoting to transparent-compression CSDs as its main product (scaleflux.com returned 403); SmartSSD volumes. **[unverified]**
- [synthesis] The pattern parallels CXL-side compute. The functions that stuck are **fixed-function and transparent** (compression, encryption), not user-programmable offload. Structera A's inline LZ4 and AES fit that pattern.

## 9. Custom HBM base dies at foundries / value capture

- **SK hynix and TSMC MOU, 19 Apr 2024** [vendor]:
  - The HBM4 base die moves from SK hynix's own process "through HBM3E" to **TSMC logic**, with CoWoS integration.
  - Rationale: "pack additional functionality into limited space" and "customized HBM… meeting diverse customer demands".
  - Justin Kim: "strengthen our market leadership as the total AI memory provider".
  - Source: https://news.skhynix.com/en/sk-hynix-partners-with-tsmc-to-strengthen-hbm-technological-leadership/
- **SK Group and TSMC chairmen, 3 Jun 2026** [vendor]: "strengthen their position in the **custom AI memory market**, targeting… global big tech customers". https://news.skhynix.com/en/ai-partnership-tsmc-2026/
- **SK hynix HBM4 completion, 12 Sep 2025** [vendor]: 2,048 I/O; >10 Gb/s per pin (JEDEC 8 Gb/s); >40% better power efficiency than HBM3E. The fetch summary said "base die 1bnm", which is probably the core-DRAM node and was **not verified as the base-die node**.
- **JEDEC HBM4**, Apr 2025: 2048-bit, up to 2 TB/s per stack, up to 64 GB, 4–16-high [secondary, Wikipedia].
- **Node details** (TSMC N12 mainstream, N3P for C-HBM4E with controller and PHY in the base die; Samsung 4 nm, "most customers pursue custom HBM"): already in q1 §3.2 [secondary].
- **Value-capture analysis**: I found **no primary or analyst source quantifying the shift of base-die value to TSMC** or explicitly tying it to memory makers' PIM or custom-HBM push. The vendor statements show the *motive* (customization, "total AI memory provider") but not the economics: **[unverified / synthesis only]**. r2a notes that the base die leaves the DRAM-wafer term for HBM4.

---

## FULL TEXT NEEDED (status after round 4)

| Citation | Status |
|---|---|
| IBM MXT | **Resolved** (D3). The provided file is the Tremaine/Smith/Wazlowski 10/30/00 IBM paper, not the 7-author JRD print version. Page numbers are not checked against JRD. |
| Ren et al., ZeRO-Offload, USENIX ATC'21 (round 3 mis-attributed this to "Rao") | **Resolved** (D4) |
| A64FX capacity constraint | **Not resolved.** MEMSYS'24 (D2) does not test capacity. BWA-MEM2/TCBB'23 and VPIC/JOCS'22 are still unread. Claim stays [unverified]. |
| SiDP, SwiftCache | **Resolved** (D5) |
| Micron SOCAMM2 | **Resolved** (D6, Oct 2025 release). The Mar 2025 SOCAMM v1 release is still unread; not needed. |
| Intel HBM-Xeon roadmap | **Resolved as far as possible** (D1). The DMR deck shows a DDR5/MRDIMM + CXL memory system with no HBM. Intel made no explicit statement about cancelling HBM. |
