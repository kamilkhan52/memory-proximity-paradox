# Research log: Why are "move memory closer" (PIM/NMP/3D DRAM-on-logic) and "move memory farther" (disaggregation/far memory) BOTH attractive?

Started 2026-10-02.

## Ground rules
- (User, 2026-10-02) If a source needs to be read in full and the full text can't be obtained, do NOT rely on the abstract. Pause that thread, log it under "Full texts needed" below, and wait for the user to supply the PDF; then resume the agent. Abstract-only claims are labeled [abstract only].

## Full texts needed (waiting on user)
| Source | Needed for | Requested by | Status |
|--------|-----------|--------------|--------|
| Berger et al., Design Tradeoffs in CXL-Based Memory Pools, IEEE Micro 43(2) 2023, 10.1109/MM.2023.3241586 | MS pool topology cost/latency numbers | Q2 | needed |
| Tang et al. (ByteDance), ASIC-Based CXL Memory, EuroSys'24, 10.1145/3627703.3650061 | ByteDance cost model / TCO | Q2 | needed |
| Alibaba PolarCXLMem, SIGMOD'25 industry (DOI unknown) | 2.1x / 50% savings vs RDMA baseline | Q2 | needed |
| Amaro et al., Fastswap, EuroSys'20, 10.1145/3342195.3387522 | far-memory job-throughput numbers | Q2 | needed |
| Hao, Zhou, Yu, Stonebraker, Buffer Mgmt w/ Tiered Main Memory, SIGMOD'24, 10.1145/3639286 | five-minute-rule break-evens incl CXL/RDMA | Q4 | needed (high pri) |
| Boroumand et al., Google Workloads for Consumer Devices, ASPLOS'18, 10.1145/3173162.3173177 | per-kernel data-movement energy (62.7% verified only via slides) | Q4 (Q1 may overlap) | needed |
| AttAcc, ASPLOS'24, 10.1145/3620665.3640422 | 2.81x/2.67x [abstract only]; HBM-PIM capacity cost; baselines, iso-capacity? | Q4 + Q1 | needed |
| Kwon et al., ISSCC'21 HBM2-PIM (Samsung) | PCU area, capacity penalty (6 vs 8 GB), internal vs external pJ/bit | Q1 | needed |
| Lee et al., ISSCC'22 GDDR6-AiM (SK hynix) | area overhead, pJ/bit | Q1 | needed |
| Hot Chips 2026 decks: d-Matrix/Meta Raptor, Samsung HBM base die, Micron, Samsung LPDDR5X-PIM, XCENA MX1, NVIDIA/Groq LPU | conditions behind 0.37 pJ/bit, 70%/2.3x baselines, etc. | Q1 | needed (site 401) |
| DIGITIMES 2026-08-27 "SK Hynix questions PIM as thermal, packaging limits constrain HBM" (paywalled) | whether SK hynix publicly doubts PIM | Q5 | needed |
| Park et al., Samsung LPDDR CXL-PNM, HPCA'24 | numbers only via Mutlu primer; does 30% HW-cost claim include controller/switch | Q5 | needed |
| BEACON, MICRO'22 | speedup/energy, where processing sits [abstract only] | Q5 | needed |
| (opt) "To PiM or Not to PiM" ACM Queue; Aguilera et al. SIGOPS OSR'23 | context | Q5 | optional |
| Lee et al., IEEE Access 2018, 10.1109/ACCESS.2018.2845861 | DDR4 22 pJ/b — I/O only or full access? | Q3 | needed |
| UniIC hybrid-bonded stacked DRAM, VLSI'23 (0.66 pJ/b) & IEDM'20 (0.88 pJ/b) | interface-only vs full-access energy (vs HBM2 3.9) | Q3 | needed |
| (opt) Intel 10nm PCIe Gen5 SerDes, IEEE 9075947 | 11.4 pJ/b snippet only | Q3 | optional |
| (opt) Micron Hot Chips 2026 slides | HBM:DDR5 wafer ratio | Q3 (dup of HC'26 item) | optional |
| Dally GTC 2026 talk (video/slides) | HBM4 read ~1000x NVFP4 MAC claim (paraphrase only) | Q1 | low priority |
| (opt) HeMem, SOSP'21, 10.1145/3477132.3483550 | tiering numbers | Q4 | optional |
| (low pri) Tirmazi et al. Borg EuroSys'20; Alibaba trace papers | ~60% memory utilization basis | Q2 | low priority |

## Core question
Two seemingly opposite departures from the baseline (DIMMs on a DDR bus next to a CPU socket):
- (A) tighter coupling: 3D DRAM on logic, HBM base-die logic, near-memory compute, PIM, NDP
- (B) looser coupling: memory disaggregation, far memory, CXL pooling/expansion, RDMA far memory
Why does each attract credible researchers and companies? Are they actually opposite?

## Working hypotheses (to be tested)
H1. They optimize different scarce resources: (A) bandwidth & energy per bit moved (data-movement wall);
    (B) capacity, cost/GB and utilization (capacity/cost wall, stranding). The baseline sits in a
    compromise point that is increasingly bad at both.
H2. Common root cause: the DDR-bus-attached DIMM is pin/beachfront-, distance- and channel-count-limited;
    DRAM cost/bit scaling has slowed; core counts grew faster than per-core BW/capacity.
H3. They act on different data: hot, high-intensity-reuse-poor (streaming) data -> closer; cold,
    capacity-dominated, skewed data -> farther. Access skew makes far tiers viable.
H4. They are complements, not opposites: hierarchy is deepening at both ends; and convergence exists
    (compute near disaggregated memory: CXL-PNM, memory-side offload).
H5. Each has known failure modes (PIM programmability/process; far-memory latency/tail, pooling ROI)
    which explains partial adoption.

## Sub-questions -> agents (round 1)
| ID | Sub-question | Notes file | Status |
|----|--------------|-----------|--------|
| Q1 | Case FOR closer coupling (PIM/NMP/3D DRAM-on-logic): motivations, quantitative evidence, products | notes/q1_near_memory.md | DONE (5 full-text items pending) |
| Q2 | Case FOR disaggregation/far memory: motivations, quantitative evidence, deployments | notes/q2_disaggregation.md | DONE (4 full texts pending) |
| Q3 | Root causes / technology & economic trends shared by both (DRAM scaling, pins, BW/core, cost) | notes/q3_root_causes.md | DONE (2 full texts + 2 optional) |
| Q4 | Workload characterization: what data properties make each direction pay off | notes/q4_workloads.md | DONE (3-4 full texts pending) |
| Q5 | Critiques, failures, and convergence (compute-near-far-memory, CXL-PNM, skeptic papers) | notes/q5_critiques_convergence.md | DONE (3 full texts pending) |

## Round 2 (launched 2026-10-02)
| ID | Sub-question | Notes file | Status |
|----|--------------|-----------|--------|
| R2a | Supply coupling: does HBM growth raise conventional DRAM cost and push capacity to cheaper/farther tiers? | notes/r2a_supply_economics.md | DONE (6 FT items, mostly industry/paywalled) |
| R2c | Historical precedent: 1990s IRAM/Active Pages vs NOW/GMS network RAM; what decided survival | notes/r2c_history.md | DONE (5 optional FT items) |
| R2b | Access-density tier table (GB/s per GB) | SYNTHESIS_DRAFT.md §2 | DONE by orchestrator [derived from vendor peak specs] — key insight: on DDR bus adding capacity dilutes BW/GB (0.8->0.2 for 64->256 GB DIMMs) => DDR bundles BW+capacity |
| R2d | Adversarial review of SYNTHESIS_DRAFT v0.1 | notes/r2d_review.md | DONE — major revision; 3 fatal (FLOP/B vs access density conflation; "fixed ratio"/single axis contradicted -> 2-D frontier GB/s/GB x $/GB + latency constraint; deployed B isn't "farther"), 12 major. Applied in v0.2. |
| R3 | Verify reviewer's missing evidence (Xeon Max, A64FX, MemoryX, ZeRO-Infinity, Optane EOL, HW compression, NVLink/UALink, MRDIMM/SOCAMM, computational storage, base-die value capture) | notes/r3_gap_evidence.md | DONE — 6/9 verified, 2 partial, Optane-DRAM-pricing link unverified; integrated in v0.3 |

## Findings so far
### R2c (history) — 2026-10-03
- Same paradox in 1990s, often same people: Patterson + Anderson on both IRAM (1997) and NOW (1995); Oskin Active Pages + Grappa; J. Chang memory blades (ISCA'09) + NDP workshop (Micro'14).
- Different baselines: IRAM vs off-chip bus (98.5 nJ off-chip vs 4.55 nJ on-chip; 50-100x BW target); network RAM vs DISK (GMS 1.44 ms remote 8KB page vs 3.6-14.3 ms disk; >1000x slower than local DRAM).
- Reconciled explicitly in 1997: IRAM backed by off-chip DRAM/paging or networked IRAMs => near+far hierarchy was the original vision. Active Pages attacked IRAM on capacity.
- Killers: PIM — DRAM-process logic (30-70% area, 30-100% speed), broke commodity, programming model, caches sufficed, DRAM price crash ($40->$6 per 16Mb in 1996); "Moore's law allowed von Neumann machines to outrun them". Network RAM — only beat disk; cheap local DRAM (-33-36%/yr) won. Hardware CC-NUMA survived (Origin remote ~3.6x local); SW DSM died. Least-disruptive forms survived (eDRAM caches, HBM over HMC).
- KEY: the common killer (fast commodity DRAM cost scaling) is gone -> both directions now forced simultaneously. IRAM's 1997 premise (demand scales slower than capacity/chip) reversed.
- Far tier moved from "faster disk" to "slower DRAM": CXL ~2x local (< Origin's 3.6x) -> CXL expansion = rebirth of surviving NUMA branch; RDMA far memory = network-RAM/DSM branch.
- Packaging (2.5D/3D, hybrid bonding) decouples logic and DRAM processes; AI = anchor workload w/ vendor-controlled stack (like consoles, where 1990s NMP did ship); CXL standard; hyperscalers modify OS.
- Predictions: passive HBM + logic base dies and single-host CXL persist; in-bank compute & switched pooling niche outside vertically-integrated AI stacks; escape valves (3D DRAM restoring $/bit decline; HBM BW scaling) would weaken each; wheel of reincarnation -> CXL controllers gain compute.

### R2a (supply coupling) — 2026-10-03
- Verdict: supported, narrowed. Shared wafers -> HBM price / trade ratio sets a floor on conventional DRAM price while HBM is supply-rationed (~$8-12/GB 2027 [calc] vs ~$3/GB 2023). Raises payoff of capacity tiers needing no leading-edge wafers (recycled DDR4 via CXL, compression, SSD offload).
- Trade ratio: Micron ">2x" (Dec'23, primary), "3:1" (Dec'25 call transcript); TrendForce decomposition 2.1-3.7x [calc]. HBM4/4E 4-5x unconfirmed.
- HBM share: wafers 18%->22%->~30% (end-2025->2026->2027) but bits only 8->9->13% (TrendForce Jun'26). HBM penalty on conventional bit supply ~13% (2025) -> ~28% (2027) [calc].
- Prices: DDR5 RDIMM ~$7/GB Q4'25 -> ~$14 Q1'26 -> ~$25 forecast Q4'26 [secondary]; HBM3E ~$10-16/GB; DDR5 RDIMM out-earned HBM per wafer from 1Q26. DDR4 spot > DDR5 spot (EOL).
- CXL recycled tier: ~$2/GB hardware (controller ~$300) ~0.13x matches Vistara; counting DDR4 resale value 0.3-0.6x.
- Explicit links: Marvell CEO (interested party), Meta capex "mostly memory pricing", Google DDR4 reuse [press].
- Caveats: multi-cause shortage (DDR5/LPDDR AI demand, capex discipline, DDR4 EOL, alleged collusion class action); far-memory designs PREDATE shortage (amplifier not cause); shortage also hits NAND & new-DDR5 CXL; yield learning + new fabs 2027-28 may lower floor ("classic 1-2 yr shortage" — Handy).

### Q3 (root causes) — 2026-10-02
- DRAM $/bit decline: ~33-36%/yr pre-2010 -> ~12-15%/yr since (~2011 break, Handy). Density ~100x/decade -> ~2x last decade. Latency (tRCD) -30% while $/bit -16x.
- Pin wall: cores/socket 8->256, channels 4->16 (2012-2026); package pins double ~every 6-7 yrs; DDR5 channel 160-200 pins; PCIe5/CXL ~4x BW/pin/direction (COAXIAL); DDR5 1DPC at full speed.
- Energy ladder: SRAM 0.16-1.6 pJ/b; HBM2 3.9 (I/O <10%); GDDR5 14; DDR ~20-22 (needs F1 to settle); CXL +~10-25 [estimate].
- Latency ladder (measured): local DDR 78-115 ns idle (160 avg/285 p90 at 60% load); NUMA 142-255; CXL real devices 214-394 (tails 0.7-1us) vs 170 spec; switched 220 idle/1.6us contended; RDMA 4KB ~3.9us; SSD 10-50us.
- SURPRISE (big): physical closeness != lower latency. H100 HBM ~300-330 ns; Grace LPDDR5X >200 ns; x86 DDR 80-120 ns. => Direction A is NOT about latency; it's about bandwidth & pJ/bit. "Distance" is a proxy; the real axes are BW density/energy vs $/bit/utilization.
- BW/core on flagship P-core parts roughly flat (6.4 GB/s/core 2012 -> 4.8-6.6 2024-26) by spending pins, 1DPC, MRDIMM; dense-core parts dropped to 2.8-3.6. The squeeze manifests as lost CAPACITY and platform cost.
- Mapping: $/bit flattening -> B strongly; pin wall -> both; pJ/bit -> A strongly; latency stagnation -> enables B (+50-100ns is NUMA-like); AI walls -> both; serial vs parallel -> DDR DIMM is the unstable middle of wire-length vs bits/pin trade; A (mm-scale ultra-wide parallel/hybrid bonding ~110k/mm2) and B (serial, meters) are the two stable ends.
- Gholami verified full text: 3.0x / 1.6x / 1.4x per 2yr; params 410x/2yr vs single-GPU mem 2x/2yr.
- Possible source error: Das Sharma x16 Gen5 "256 GB/s" with 64 pins -> 128 GB/s bidirectional by arithmetic.

### Q1 (near-memory / PIM) — 2026-10-02
- Energy: 64b DRAM access 1.3-2.6 nJ vs 8b add 0.03 pJ (Horowitz ISSCC'14); DRAM operands ~200x a DP FMA (Keckler'11); Dally HC'23: 32b DRAM read 640 pJ vs local SRAM 5 pJ. Boroumand'18: 62.7% of system energy = data movement.
- KEY: HBM2 access 3.92 pJ/b = 2.24 on-die data movement + 1.21 activation + only 0.3 interposer I/O (O'Connor MICRO'17). Shortening the *link* has diminishing returns; the remaining energy is inside the DRAM die -> motivates bank-level PIM & fine-grained DRAM-on-logic, not just "closer packages".
- FLOPS 3.0x/2yr vs DRAM BW 1.6x/2yr vs interconnect 1.4x (Gholami).
- Internal BW >> pin BW: 4x (Aquabolt-XL), 8x (LPDDR5X-PIM), 16x (GDDR6-AiM); Tesseract 8 TB/s internal vs 320 GB/s.
- Silicon results: Aquabolt-XL >2x perf, >70% energy cut, +5.4% power; UPMEM 23x avg vs weak CPU; LPDDR5X-PIM 3.01x tok/s Llama-3.1-8B [secondary].
- LLM decode ~1 FLOP/B -> ~0.3% GPU peak util; NeuPIMs 3x, Duplex 2.67x, CENT 5.2x tokens/$.
- 3D DRAM-on-logic: d-Matrix Raptor 0.37 pJ/b vs HBM4 2-3; ~20x BW/mm2; BUT 32 GB vs 192 GB (1/6 capacity) [secondary, HC'26].
- A optimizes pJ/bit, BW/pin, BW/mm2, BW per W and $ for low-intensity phases. Weak on dense GEMM/conv (1.0-1.3x).
- CAPACITY PRICE OF A is explicit: d-Matrix 1/6, Aquabolt-XL 6 GB, HBM3E ~3x silicon per bit vs DDR5 (Micron) -> bridge to B. JEDEC SPHBM4 relaxes coupling to fit more stacks.
- Convergence: Samsung roadmap (HBM-PIM, LPDDR-PIM, CXL-PNM in one hierarchy; HBM base die w/ compute + capacity-extension ports); XCENA 3072 RISC-V cores in CXL expander because link only 64 GB/s/dir; NVIDIA+Groq split decode (SRAM FFN, HBM KV).
- Caveat: PIM baselines generous (Newton 54x vs real GPU, 10x vs ideal non-PIM).

### Q5 (critiques & convergence) — 2026-10-02
- Mirror-image critiques: A limited by process (UPMEM: 3x slower transistors, 10x lower logic density, 3 metal layers; 350 MHz, no FP), heat (refresh doubles per ~10C above 85C; HMC PIM thermal shutdowns, CoolPIM), ecosystem (no VM/coherence; "very little ... found its way to product" — Roscoe APSys'25). HMC dead 2018; passive HBM won.
- B limited by cost/latency: Levis HotNets'23 — ~140 TB server RAM to break even with $38.5k switch proxy; 2x latency (140->280ns); no pooling gain at realistic VM sizes (VMs must grow 32x Google / 8x Azure). Octopus NSDI'26: switches +>=220ns, "always cost more". Berger IEEE TC'26: expansion first. Tail latency +10-82% (Sun MICRO'23). Carbink FT +35% memory.
- PATTERN: each direction survived only in its least-disruptive, no-software-change form (passive HBM; single-host CXL expansion).
- Convergence: far memory REQUIRES near-memory compute (Miller et al. ACM Queue'23: offloading dependent accesses "required optimization"). Teleport 5.4x avg slowdown unmodified -> ~10x recovered with pushdown; PULSE 9-34x pointer traversal; CXL-ANNS 111x QPS. Products: Samsung CXL-PNM, SK hynix CMS, Marvell Structera A.
  - Insight (agent's inference): CXL controller is already a logic-process die beside DRAM -> compute there avoids both DRAM-process and thermal critiques, and converts Levis's "pure cost" controller into a compute host.
- Hybrid production: GB200 NVL72 13.4 TB HBM3E @576 TB/s + 17 TB LPDDR5X @14 TB/s coherent; GH200; NVIDIA HBM->DRAM->NVMe KV tiering (BlueField-4); Mooncake RDMA KV pool; Rubin CPX (GDDR7 for compute-bound prefill — i.e., LESS coupling where intensity is high). MI300A = pure coupling contrast.
- Literature framing: complementarity in scattered position pieces; Levis frames as opposites; NO paper treats near<->far as one design axis -> gap (our synthesis can fill).

### Q2 (disaggregation) — 2026-10-02
- Deployed reality = CXL *expansion* w/ cheap/recycled DRAM, not rack-scale pooling. Pooling ROI modest: Pond 7-9% DRAM reduction; Octopus net 3.0-5.4% server cost; switches net-negative; Levis HotNets'23 skeptical.
- Driver is cost/GB, capacity-per-core, carbon: memory 37.1% of rack TCO (Meta TPP), 41% embodied carbon (Azure Memstrata), 69% lifecycle (Meta Vistara ISCA'26). "50% of server cost" in Pond is a secondary citation.
- Cold memory is the big lever: 32% cold @120s (Google), 55-80% idle per 2 min (Meta) -> but exploited mostly IN-NODE (zswap, Optane) — "far" = cheaper bytes, not necessarily remote bytes.
- Vistara: recycled DDR4 over CXL 0.13x $/GB, up to 25% fewer servers; cost ~10x lower BW, +60% latency.
- CXL as BW expansion: +24-39% on Xeon 6900P with tuned interleave; x8 CXL ~3x fewer pins than DDR5 channel (Sun MICRO'23).
- Latency ladder (Octopus P50): DDR5 115ns, CXL 230-270, MHD 260-300, switch 490-600, RDMA 3550.
- Convergence: Marvell Structera A (16 Neoverse V2 cores in CXL controller); CXL KV-cache papers with PNM.
- Coupling: AI/HBM demand -> DRAM shortage 2026 -> raises value of DDR4 reuse via CXL [press]. (A) feeds (B) economically!


### Q4 (workloads) — 2026-10-02
- Hypothesis H3 supported but refined: split is per *data object*, not per workload. Skew everywhere: <10% of capacity serves P90 of bandwidth (Meta MemProf, Mahar'23); 10% of KV blocks get 77% of reuses (Alibaba ATC'25); Mooncake >50% KV blocks never reused.
- Production moves 20-33% of DRAM to slower tiers at <=1-5% slowdown (TMTS 25%, TPP 2:1, Memstrata) vs 50-80% measured idle — gap from re-access + migration cost.
- Memory-bound kernels: LLM decode ~1 FLOP/B, DLRM embeddings 0.25 FLOP/B; FLOPS 3.0x/2yr vs DRAM BW 1.6x/2yr.
- Single workloads needing both: DLRM (MTrainS: big tables low-BW, high-BW tables small; RecNMP 4.2x; TensorDIMM = NMP DIMMs in a disaggregated pool), LLM serving (HBM/PIM for hot, KV offload to DRAM/SSD; Mooncake +75% requests), Meta memory-BW tiering (37.5% on 2x-BW tier gets 1.47x vs 1.55x all-fast; perf/cost 1.13 vs 0.73 — ratios don't reproduce exactly, flagged).
- UNIFYING METRIC (candidate): access density = GB/s per GB stored. Tier supply ratios [derived]: HBM3e ~33, DDR5 ~0.3-0.6, CXL ~0.05-0.25, NVMe ~0.001. Baseline DIMM = one fixed BW/capacity ratio; skewed workloads want both ends. Five-minute rule (capacity side) + roofline (compute side) both reduce to this. DRAM<->flash break-even shrinking (41s NVMe 2017; ~5s GPU hosts, Zhang 2025) -> more data rationally moves farther.
- Complications: CXL also adds BW (Colloid SOSP'24: loaded local tier latency 2.5x; hottest-on-local can be 2.3x worse); latency tolerance depends on MLP not just hotness (OSDI'25 "Beyond Hotness"); workload class poor predictor (Pond); far KV tier may only need ~2x HBM capacity (Alibaba P99 KV lifetime 97s).
- Refined H3 -> three axes: access density, latency tolerance (MLP), capacity footprint.

## Open branches / round 2 candidates
- [R2 candidate] Supply-side coupling: HBM wafer trade ratio (bits/wafer vs DDR5), 2025-26 DRAM shortage -> does the bandwidth tier's growth structurally push capacity to cheaper/recycled/farther tiers? (from Q2)
- [R2 candidate] Redo Levis'23 pooling break-even at 2026 DRAM prices (sensitivity analysis; can be done analytically once Levis full text read).
- [R2 candidate] Verify/derive the tier BW/capacity ratio table rigorously (GB/s per GB, $/GB, pJ/bit) — central to synthesis; cross-check with Q3 table.
- [R2 candidate] "Far" vs "cheap": separate distance from media cost — Google's far memory is in-node compression. Is the real axis $/GB & bandwidth/GB rather than physical distance?

## Decisions
- 2026-10-03 v0.2: thesis restated as a 2-D cost frontier (supplied GB/s per GB vs $/GB) + latency/MLP constraint; 2x2 (A1 proximity / A2 compute-at-memory / B1 cheaper capacity / B2 cross-host sharing); "wider hierarchy around a persistent middle"; supply coupling = amplifier, floor P_HBM/[r(1+c)] with sensitivity (floor below 2026 DDR5 price -> forward risk, not today's driver); history used to argue resource-pressure > market-split as root cause. Empty band 1-35 GB/s/GB noted as finding.
- 2026-10-03 v0.3: added Xeon Max as unconfounded "closer != lower latency" case (HBM ~20% higher latency than DDR on same cores); middle catching up (Granite Rapids no HBM SKU, MRDIMM-12800 ~= Xeon Max HBM peak); ZeRO-Infinity per-tier BW model as worked instance of placement rule; ZeRO-Infinity & MemoryX optimizer-at-capacity-tier = deployed compute-follows-far-tier (training); Optane lesson (must be cheaper to make, not just sell); computational storage limited (CIDR'21); compression narrowed to MXT/AME.
- Web search budget for the session appears exhausted (R2d, R3 reported); further web work must use WebFetch on known URLs.

## Round 4 — full texts received (2026-10-03)
- Kamil supplied ~30 items + Hot Chips 2026 slide KB (zip). Mapping in fulltext/MANIFEST.md. Not provided: Kwon ISSCC'21, TrendForce, Citi/Goldman, Cherian, Kim&Chen, Kogge, To-PiM-or-Not, Borg traces, FlexRAM/DIVA/TreadMarks/Comer.
- Hot Chips decks dumped to fulltext/hotchips26/decks_text/ (extracted PDF text = primary; vision descriptions = secondary).
- Resuming 8 agents (Q1-Q5, R2a, R2c, R3) to resolve [pending full text] claims and report deltas vs SYNTHESIS v0.3.
- 2026-10-03 v0.4: applied all round-4 deltas (ROUND4_DELTAS.md). Key changes: energy scopes fixed (DDR4 full 16.5-21 vs HBM2 3.92; hybrid-bond 0.37/0.66/0.88 are I/O only; CXL 22-67 pJ/b); "empty band" -> "band vacated on server CPUs" (Xeon Max/A64FX/SX-Aurora -> DMR/MONAKA no HBM: SRAM + MRDIMM + CXL); pooling pays only in small switchless pools (Berger); PolarCXLMem is a prototype; Hao gives bandwidth-saturation rule not a CXL five-minute interval; HBM floor was binding 2024-25 then overshot 2026; SK hynix calls PIM niche (capacity); AttAcc iso-capacity but half stacks; CXL-PNM paper prototype w/ assumed prices; 1990s split DID follow market lines (my v0.2 claim was wrong) -> market + resource explanations compatible.
- Next: second adversarial review of v0.4 (fresh agent), then finalize.
- 2026-10-03 R5 review of v0.4: minor revision; 6 must-fix (AttAcc baseline inverted; B2 over-generalised; "floor" wording; vacated-band overstated + SX-Aurora not a CPU; energy-gap "mostly in-die" config-dependent; Dally tag/scope). All applied in v1.0 (SYNTHESIS_v1.0.md), restructured with 1-page exec summary + appendices + falsifiability.
- Status: research complete pending optional full texts (Kwon ISSCC'21, Raptor ISCA'26, Borg traces, A64FX apps, SemiAnalysis paid, TrendForce).
- 2026-10-03 v1.1: Kamil asked where the 'power budget' is. Answer: bandwidth = wires x rate across a boundary; power = BW x E_bit; layered binding limit (CPU DDR: wires/rate; GPU HBM: beachfront then energy; in-device: IDD7/tFAW current + thermal). Added §2.0 to synthesis.
- 2026-10-03 Explainer 'HBM Bandwidth Limits' (explainers/hbm-beachfront-power.html) published after 3 Opus accuracy rounds; artifact https://claude.ai/artifact/5CxmJMTajZha4JqK5X6Luy. FGDRAM full text now in fulltext/. Synthesis v1.2.

## Deck + report build (started 2026-10-03, Kamil approved: 16:9 slides, private artifacts + local; slide-9 $/bit tagged [S])
- Phase 0: deck/assets/deck.css (design system, colour semantics A=blue, B=orange, DDR=slate, conv=green), deck/build/assemble.py (fragment assembly + headless previews). Opus architect writing deck/BRIEF.md.
- Next: Phase 1 figure data (Sonnet x2 per figure), Phase 2 slide builders, Phase 3 assemble, Phase 4 review loop (Opus accuracy + Opus narrative + Sonnet visual), Phase 5-6 report + loop, Phase 7 publish.
- Phase 1 status: E4 meta_memory_share reconciled (identical); E7 gb200_tiers reconciled (identical; NVIDIA page per-superchip vs rack LPDDR inconsistency noted); E8 hbm_wafer_bit_share reconciled (identical; HBM4E >4:1 = Mar-2025 p.6 only). E3 copied from explainer.
- Phase 2: builders launched s01, s07, s10 (Opus), s04, s08 (Sonnet).
- Phase 1 complete: all 9 figure datasets reconciled (E1 latency, E2 growth, E3 copied, E4 TPP, E5 frontier, E6 skew, E7 GB200, E8 wafer/bit, E9 DRAM price: A/B within 3.1%, 2018-24 single-extraction). Phase 2: slides s01-s08, s10 built; s09 building. Cross-slide items in deck/reviews/ORCH_NOTES.md.
- Phase 4 round 1 launched (accuracy Opus, narrative Opus, visual Sonnet). Phase 5 report writer (Opus) launched in parallel -> deck/report.html.
- Round 1 deck review: accuracy 1 blocking/9 major; narrative 17 major; visual 16 major. Decisions in deck/reviews/r1_decisions.md (CXL adder +~100-280; new headlines s01,s03,s04,s05,s06; rival explanations on s10; etc). Fixes dispatched to all 10 builders; report writer notified. Synthesis v1.4 (adder, Vistara spec).
- Round-1 fixes applied by all 10 builders; deck.css footer 11px. Round 2 reviews launched (fresh accuracy Opus, narrative Opus, visual Sonnet).
- Round-2 fixes applied by all 10 builders; footer rule pinned at y=680 on all slides. Synthesis v1.7 (report-flagged: Berger absolute latency, Samsung 1.0-1.3x = CNNs, Carbink vs Hydra, MONAKA-X). Report drafted (~8.8k words, 13 figs, 105 refs); report review R1 launched (Opus technical, Sonnet citations, Sonnet visual). Deck round 3 launched.
- Round-3 fixes applied (all 10 slides done). Report R1 visual + citation reviews in (citation findings saved by orchestrator). Waiting report technical review.
- Deck R4: visual clean (0 major), narrative clean (ready), accuracy 2 major (Wang caption misleading; s03 interface 2-4x self-contradiction). r4_decisions dispatched incl. report-review-derived deck fixes (NVMe typical, TPP emulation, $/bit 2.5-3x, Mooncake trace replay). Synthesis v1.9. Report revision R1 dispatched to report writer.
- 2026-10-04: Opus agents hit session limit (R5 accuracy/narrative, report revision). Published current deck (https://claude.ai/artifact/Q7BmAB45BpaufHpSWbu3QF) and report draft w/ banner (https://claude.ai/artifact/QxyFbeuQNxk3MGjAM98zTA) at Kamil's request. Relaunched R5 accuracy+narrative; resumed report revision. R5 visual clean (2nd consecutive) -> visual done.
- R5: visual clean (2nd consecutive, DONE); narrative 1 major (NVMe label) + accuracy 1 major (zHBM scope) fixed by orchestrator/builders; r5_decisions applied. Synthesis v2.0. Round 6 (accuracy+narrative) next.
- Deck FINAL: accuracy r6+r7 clean, narrative r7 clean (r6 one-line caption major fixed), visual r4+r5 clean. Post-r7 corrections from report R2 technical (recycled DDR4 all-in ~0.4-0.9x, B1 adder +100-160, Das Sharma estimates, GPU->HBM label, PIM 6 GB vs 8 GB std [D]) applied; all slides clear footer >=13px. Synthesis v2.1.
- 2026-10-04 08:50: computer restart; Opus weekly limit hit (resets Oct 6 4am PT) during report R2 revision. Report revision ~mostly done on disk (embeds match, v2.1, Wang/recycled fixes). Orchestrator fixed residual v1.9/+~; Sonnet agent finishing remaining R2 items with fixlog.
- 2026-10-04: Report R3 confirmation (Sonnet; Opus weekly limit) = ready, 0 major; 7 minors fixed (TPP 33-80% emulation, review-round counts, Das Sharma 57/65 ns, Raptor 19.5-21.6x, TrendForce NAND provisional). Deck s02 Grace wording softened. FINAL published: deck https://claude.ai/artifact/Q7BmAB45BpaufHpSWbu3QF (v4), report https://claude.ai/artifact/QxyFbeuQNxk3MGjAM98zTA (v3). Open: ~34 [P, online] sources without stored full text; report's final technical round was Sonnet, not Opus.
- 2026-10-0x: Skill eval iteration-1 launched (8 runs: 2 evals x with/without skill per skill) in ~/research/skill-evals/. Usage limit reached during runs; only "chiplets-vs-wafer-scale/without_skill" confirmed complete (timing saved). Remaining: wait for/re-run other 7 runs, write assertions into eval_metadata.json, grade, aggregate (skill-creator scripts.aggregate_benchmark), launch eval-viewer generate_review.py.
