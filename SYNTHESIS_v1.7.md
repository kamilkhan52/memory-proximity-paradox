# Closer and farther at once: why memory is moving both toward and away from compute

*v1.7, 2026-10-03. This is a literature synthesis that went through two rounds of adversarial review. The tag legend, revision history and outstanding items are in Appendix D.*

---

## Executive summary

**Question.** Why are two seemingly opposite directions both attractive to researchers and companies?
- **(A) Tighter memory–compute coupling:** HBM, 3D DRAM–logic stacking, PIM/NDP.
- **(B) Looser coupling:** disaggregation, far memory, CXL pooling and expansion.

**Answer.**
- **Distance is not the variable being optimized.** Both directions move away from the DDR DIMM's narrow region on a two-axis cost frontier:
  - bandwidth supplied per GB stored;
  - cost per GB;
  - both under a latency constraint.
- **Access is heavily skewed.** It is skewed per object, not per workload, so a mix of tiers beats any single tier. A serves the bandwidth-dense head of the data; B serves the cold, capacity-dense tail.
- **The middle stays, and is improving.** The hierarchy is widening *around* a persistent middle.

**Two distinct pressures** (they are coupled through shared wafers, §5.2):

| Pressure | Evidence |
|---|---|
| **Bandwidth across a boundary** → A | Compute scales with the number of transistors; bandwidth scales with the number of wires that cross a boundary × their rate, and each bit's energy scales with wire length (§2.0). Compute grew ~3.0× every 2 years vs ~1.6× for DRAM bandwidth [P: Gholami]. For CPU DDR the binding limit is wires and per-wire rate. Moving closer buys orders of magnitude more wires, after which energy per bit becomes the binding limit: a 60 W DRAM budget allows ~2 TB/s of HBM2 [P: O'Connor], and at 2.4 pJ/b, 100 TB/s would need 1.92 kW [V: d-Matrix]. |
| **Capacity cost** → B | DRAM $/bit decline slowed from ~33–36%/yr to ~12–15%/yr after ~2011 [S]. Memory is 37% of rack TCO in Meta's latest generation [P: TPP]. Production systems move ~20–25% of DRAM to cheaper tiers at <5% slowdown [P: zswap ~20%, TMTS 25%, Vistara 25% of capacity]; TPP shows 33% at ≤1% on pre-production CXL hardware [P]. |

**Deployed versus ambitious forms.** This distinction is the most useful lens.

| Cell | Driver | Status | Best evidence | Main cost |
|---|---|---|---|---|
| **A1** Proximity for bandwidth (HBM, on-package, stacked) | Bandwidth density; pJ/bit caps bandwidth | Deployed at scale (accelerators) | HBM3E/HBM4 at ~33–73 GB/s per GB | Capacity per $ and per mm² (~3× wafer per bit) |
| **A2** Compute at memory (PIM/NDP) | Internal bandwidth 4–16× the pins; in-die energy | Demos and niche | Aquabolt-XL >2× performance, >70% less energy [V]; most results simulated | Capacity (HBM-PIM stack 6 vs 8 GB), process, heat, programmability |
| **B1** Cheaper or extended bytes (compression, recycled DDR4, single-host CXL) | $/GB, capacity per core | Deployed (Meta, Google, Azure) | Vistara: 0.13× $/GB nominal, up to 25% fewer servers [P] | Latency (+~100–280 ns), bandwidth |
| **B2** Sharing across hosts (pooling, RDMA far memory) | Statistical multiplexing | Niche | Mooncake KV pool in production [P] | Switch cost: "most switch-based [CXL] configurations are ROI negative" [P: Berger] |

**How the directions are coupled:**
- **A creates demand for capacity tiers.** GB200 pairs 13.4 TB of HBM with 17 TB of LPDDR5X. d-Matrix pairs its 3D DRAM with LPDDR5X. ZeRO and MemoryX do the same in training.
- **A competes with B for wafers.** HBM needs ~3× the wafer per bit [P/V: Micron]. It out-earned DDR per wafer in 2024–25, pulling wafers to HBM, and is one of three named causes of the 2026 shortage [V: Handy].
- **B pulls compute outward.** Training runs the optimizer at the capacity tier in production. Elsewhere this is still prototypes.

**Why both, and why now.**
- In the 1990s, the same split occurred (IRAM vs network RAM), often among the same people. Commodity DRAM, fast-scaling and cheap, let the middle outrun both waves.
- Since ~2011 it can't, so both ends are pursued at once [Syn].
- The two directions also come from different markets with different binding constraints: AI accelerators vs CPU cloud, as in the 1990s.
- Vendors also want an escape from commodity pricing.

**Confidence.**
- High: A1 and B1.
- Medium: the coupling claims.
- Low: A2 and B2 economics, which are mostly simulated or modelled.

**What would change our mind.** See §8.

---

## 1. Distance is a proxy

- **Wire flight time is nanoseconds.** A 6 ft cable adds ~12 ns each way [P: Levis]. Latency lives in device timing, controllers, SerDes, switches and queues. DRAM tRCD fell only ~30% while $/bit fell 16× [P: Chang 2016].
- **A does not reduce latency.** On Intel Xeon Max the same cores and mesh reach both HBM2e and DDR5. HBM latency is ~20% *higher* than DDR, while STREAM bandwidth is ~700 vs ~200 GB/s [P: arXiv 2505.14294].
- **B's latency cost comes from protocol hops, not distance.**
  - Direct CXL measures +~100–280 ns over local DDR [P].
  - A switched CXL pool measures 549 ns vs 146 ns to local-socket DRAM [P: PolarCXLMem].
  - Details are in Appendix A.
- **"Far" names a role, not a distance.** GB200's capacity tier (LPDDR5X) sits on the Grace superchip module next to its CPU [V].

**What each direction buys:**

| Direction | Buys |
|---|---|
| A | Bandwidth per byte stored, and lower pJ/bit (which caps bandwidth under a power budget) |
| B1 | $/GB, utilization, and media independence (e.g. DDR4 reuse behind a CXL controller) |
| B2 | Multiplexing across hosts |

## 2.0 First principles: what limits memory bandwidth

**Basic relations** [Syn]. For any memory interface:

- **Bandwidth** = (number of parallel wires crossing a boundary) × (bit rate per wire).
- **Memory power** = bandwidth × (energy per bit).

Each factor is set by the physics of the boundary:

1. **Wire count** scales with boundary size ÷ pitch. Through an edge it grows with the perimeter; through a face, with area ÷ pitch².

   | Interconnect | Pitch | Source |
   |---|---|---|
   | Package or board ball | ~1 mm | [background] |
   | Interposer microbump | ~36–55 µm | [V: d-Matrix 36 µm F2F; S: UCIe 45 µm] |
   | Hybrid bond | 3 µm, 110,000 vias/mm² | [P: UniIC] |

   Lane counts follow the pitch: Blackwell has ~8K lanes to 8 HBM3E stacks, Rubin ~16K, d-Matrix Raptor ~800K per package [S].
2. **Per-wire rate** is limited by channel loss, reflections and crosstalk. These grow with length and with discontinuities. DDR's connectorized, multi-drop bus is the worst case, hence the standard fixes: one DIMM per channel, MRDIMM. Serial links reach high rates by spending energy on equalization: 11.4 pJ/b per lane at 37 dB [P: Intel SerDes].
3. **Energy per bit** is roughly the switched capacitance × V². Capacitance scales with wire length, plus termination and equalization on long lossy channels.

**Why the gap between compute and bandwidth growth is structural.** Compute grows with the number of transistors, i.e. with area. Wires cross a perimeter, or an area at a slowly shrinking pitch. Berger et al. put it this way: "socket performance scales with chip area while DDR scales with perimeter" [P: Berger TC'26]. Rent's rule generalizes the same observation [background].

**Where the power budget actually binds** (the limit is layered):

| Setting | Binding limit | Evidence |
|---|---|---|
| **CPU + DDR** | Wires and per-wire rate (pins, signal integrity). Power is not the limiter. | Every fix adds wires or rate: 4 → 16 channels, 1DPC, MRDIMM, CXL at ~4× bandwidth per pin [P/V]. |
| **GPU + HBM** | One limit, not two: **wires per mm of die edge at a given reach**. HBM's unterminated parallel link reaches only ~5–7 mm, so stacks must abut a host-die edge [P: O'Connor §3.5]. HBM4 doubled DQ to 2,048 inside the same 8 mm PHY by going deeper (≈256 DQ/mm of PHY, ≈190/mm of die edge) [V: Samsung s11; D]. A reticle die (26×33 mm) holds at most ~6 stacks of 11×12.8 mm [V: Samsung s9–11]; more dies hit interposer size/warpage [V]. Practical HBM4 package ≈20–25 TB/s [V: d-Matrix; AMD]. Power is a co-limit and a budget, not a later wall: 100 TB/s at 2.4 pJ/b = 1.92 kW [V]; ~1.2 kW even on trend [D]. | MI455X: 12 stacks on 2 base dies, 23.3 TB/s [V]. Samsung example: ~220 W DRAM beside a 1,200 W GPU; zHBM saving reinvested in GPU [V: s23]. Explainer: `explainers/hbm-beachfront-power.html`. |
| **Inside the DRAM device** | Array bandwidth is 4–16× the pins, but device current and heat cap how many banks can work at once. | AttAcc's 9× internal bandwidth is "capped by the HBM3 IDD7 power budget" [P]. JEDEC tFAW/tRRD limit activation current [background]. Heat: retention 32 → 4 ms at 105 °C [V: d-Matrix]; refresh doubles every ~10 °C above 85 °C [P]. |

**The principle repeats at every scale.** Even within an HBM2 access, 2.24 of 3.92 pJ/b is moving data across the DRAM die, and only 0.3 crosses the interposer [P: O'Connor]. Each step closer relaxes the currently binding limit and exposes the next: pins and channel rate (DDR) → wires per mm of die edge at a given reach, with power as a co-limit (HBM) → DRAM-core energy (activation and on-die movement, 88% of an HBM2 access), device power and heat (PIM, 3D stacking) → capacity per area. This is why the "closer" direction does not stop at HBM. [Syn]

## 2. The cost frontier: every tier is a bundle

All rows below are peak, per-direction bandwidth from vendor specs [D] unless noted. Sustained figures are lower: commercial CXL reaches 10–80% of local DRAM bandwidth [P: Wang et al.]. Energy rows state their scope.

| Tier (example) | GB/s per GB | $/GB (rel. DDR5 = 1) | Energy, pJ/bit (scope) | Idle latency |
|---|---|---|---|---|
| HBM4 (MI455X: 432 GB at 23.3 TB/s) | ~54–73 [D from V] | higher still [Pr] | ~3–4, unit unstated, operand fetch to MAC [V: Dally GTC'26 remark]; d-Matrix cites 2–3 I/O and ~5 system [V] | — |
| HBM3E (B200: 192 GB at 8 TB/s) | ~33–42 [V] | ≥3 pre-shortage [S]; ~3× wafer per bit [P/V: Micron] | HBM2 3.92 full = 1.21 activation + 2.24 on-die + 0.3 wire I/O; ~1.5 interface incl. PHY [P: O'Connor; IEDM'20] | ~300 ns from GPU [S] |
| 3D DRAM–logic stacking, hybrid/F2F bonded (d-Matrix Raptor: logic on top; SeDRAM: DRAM on its logic die) | ~20× HBM4's GB/s per mm² at ~½ its capacity per mm² [V] | n/a | 0.37 I/O measured [V]; 0.66–0.88 I/O [P: UniIC]; no full-access figure | — |
| CPU-attached LPDDR5X (Grace; Vera 1.5 TB SOCAMM at 1.2 TB/s) | ~0.8–1.1 [V/D] | ≈1 [V] | ~12 full, LPDDR device [P: Lee 2018] | >200 ns [S] |
| MRDIMM-12800, 16 ch (Diamond Rapids, Venice: 1.6 TB/s) | 1.6 / 0.8 / 0.4 at 1 / 2 / 4 TB [D from V] | >1 [unverified] | — | ~DDR |
| DDR5-6400 RDIMM, 12 ch, 1DPC, 64 → 256 GB DIMMs | 0.8 → 0.2 [D] | 1 (3DS 256 GB at a premium) [P] | DDR4 value: ~16.5–21 full, device-level (DDR5 not measured) [P: Lee 2018] | ~100–150 ns measured (DDR5 97–146; DDR4 from 78) [P] |
| CXL expander (Gen5 x8/x16) | 0.125 peak per direction [D]; Vistara spec 0.225 (Table V) [D from P] | new DDR5: ~1 + controller; recycled DDR4: 0.13 nominal, 0.3–0.6 all-in [P/D] | ~22–44 direct, ~28–67 switched, excl. controller [D from P: Intel Gen5 SerDes 11.4 pJ/b/lane] | 214–394 ns measured [P] |
| NVMe SSD | ~0.001 [D] | ≪1 [unverified] | n/a | 10–40 µs measured [P: TPP Fig. 2]; ~50 µs end unverified |

Footnotes:
- On-die SRAM (Groq 3 LPX, ~0.5 GB at ~156 TB/s per chip [D from V]) reaches ~3×10⁵ GB/s per GB. It is a scratchpad, not a capacity tier.
- RDMA far memory is ~0.01–0.1 GB/s per GB, depending on configuration, at ~3.5–4.6 µs (64 B–4 KB) [P].

**Three observations** [Syn]:

1. **Deployed CXL is not a new point on the density axis.**
   - Meta's Vistara tier (0.225) equals capacity-maxed DDR (0.2). What separates them is $/GB.
   - B1 moves *down* the cost axis at DDR-like density. A moves *right* along the density axis at higher $/GB.
   - Per bit, CXL costs ~1.3–2× local DDR energy. Its energy case exists only at system level: fewer idle DIMMs, and reused media.

2. **The middle improved; on server CPUs it caught up with HBM.**
   - A 16-channel MRDIMM-12800 socket gives 1.6 TB/s. That matches Xeon Max's HBM *peak* (1.64 TB/s), and is ~2× its measured STREAM, at many times the capacity [V/P].
   - The two HBM server CPUs (Xeon Max, A64FX) were not continued. Their successors use a faster middle, a large or stacked SRAM LLC, and CXL:
     - Diamond Rapids: 16× DDR5-8000/MRDIMM-12800, a 1.28 GB LLC, CXL 3.0.
     - MONAKA: 12× DDR5-8000+, an LLC on a stacked SRAM die, CXL 3.0 [V: Hot Chips 2026].
   - Vendors give no reason for dropping HBM. Competing explanations:
     - Bandwidth-hungry HPC work moved to attached GPUs. Fujitsu's deck pairs MONAKA-X with NVLink Fusion.
     - HBM is rationed and priced for GPU buyers (§5.2).
     - Intel cut its roadmap.
     - HBM-on-CPU survives in APU form (MI300A) [unverified].
   - The pattern is *consistent with* a hierarchy that widens around the middle. It is not proof of a market preference.

3. **DDR is the squeezed middle, not an unstable one.**
   - On the DDR bus, capacity and bandwidth trade off: bigger DIMMs dilute bandwidth per GB, and full speed needs 1 DIMM per channel.
   - Flagship CPUs held ~5 GB/s per core only by spending pins. Over 2012–26, cores grew 8 → 256 and channels 4 → 16 [P/V].
   - The two stable wiring regimes:
     - wide parallel wiring at millimetre scale (HBM's 1024–2048 bits; hybrid bonding at 110,000 vias/mm² [P: UniIC]);
     - serial links, ~4× bandwidth per pin per direction, metre reach, +50–70 ns per hop.
   - Serial memory attach has a long graveyard (FB-DIMM, RDRAM, HMC, OMI). CXL Type-3 is the first to stick.

## 3. Placement: three properties per object

| Property | Definition |
|---|---|
| **Access density ρ** | Bytes per second demanded per byte stored |
| **Arithmetic intensity I** | FLOPs per byte moved |
| **Latency tolerance** | Set by memory-level parallelism (MLP) [P: OSDI'25 "Beyond Hotness"] |

| Object type | Placement | Examples and notes |
|---|---|---|
| High ρ, low I | A | Decode attention over live KV: ~1 FLOP/B for MHA, × the GQA group factor, independent of batch size. Also bulk scans and bitwise ops. |
| High ρ, high I | On-chip SRAM | Prefill (~1000 FLOP/B), dense GEMM. Samsung's HBM-PIM gains only 1.0–1.3× on compute-bound CNN vision models [V: Samsung]. |
| Low ρ, high MLP | B1/B2 | Cold pages, most embedding rows, reusable KV. |
| Low ρ, low MLP | Stays on the DDR bus | Pointer chasing, B-trees, graph workloads. GAPBS suffers most on CXL [P: Pond]. |

**Placement subtleties:**
- **Low I alone does not decide placement.** Embedding gathers have the lowest FLOP/B (0.25), yet the big tables have low bandwidth demand [P: MTrainS].
- **Read and write density differ.** HeMem moves write-heavy pages back to DRAM first [P].
- **Skew is pervasive but not universal.** It is absent for dense model weights, which are read in full every decode step.

**Economic rules.** The five-minute rule (capacity side) and the roofline (compute side) both compare an object's ρ with a tier's supplied density and price.
- DRAM↔flash break-even intervals have shrunk to tens of seconds [P].
- For remote memory, Hao et al. compute no interval. They argue the rule doesn't fit, and instead add remote memory "until it saturates the remote memory bandwidth" [P: SIGMOD'24 §4.9, emulated]. In other words, the middle tier needs a bandwidth term (Appendix C).

## 4. The 2×2 in more detail

### A1. Proximity for bandwidth (HBM, on-package, stacked)

**Evidence:**
- The pJ/bit and density numbers in §2.
- A large share of DDR4's extra energy is inside the die: about half or more, depending on configuration. Device-level, I/O is ~5 of ~20 pJ/bit [P: Lee 2018 Fig. 2a]. At channel level, I/O is 46% of dynamic power with 8 ranks [P: Lee 2018 p. 31391].
- So wider, shorter wiring helps, but so does a die organized for bandwidth.

**Cost:**
- HBM3E takes ~3× the wafer per bit, including packaging complexity [P/V: Micron]. HBM4E takes >4:1 [P/V].
- d-Matrix Raptor has ~½ the capacity per mm² of HBM4 [V]. Dally, by contrast, calls stacked DRAM "much more capacity and cheaper cost per bit" [V: GTC'26]. The vendors disagree.
- Thermal burden rose 2.2× across the six generations shown on SK hynix's slide, which has no axis labels. Hybrid bonding (research stage) cuts thermal resistance to 0.40×; production MR-MUF reaches 0.45–0.55× [V].

**Status:**
- Deployed on accelerators.
- Base-die logic is moving fastest: the SK hynix–TSMC base die; Samsung's 4 nm HBM4E base die [V].
- Dally's "~10× bandwidth at ~10× lower energy" for DRAM stacked on compute is a roadmap item he calls "not quite ready" [V].

### A2. Compute at memory (PIM/NDP)

**Evidence** (mostly simulated):
- Aquabolt-XL: >2× performance and >70% less energy [V].
- AttAcc [P, simulated]:
  - up to 1.72× vs 2×DGX with the same 80 HBM stacks;
  - 2.01–2.81× vs a 1,280 GB DGX that has half the stacks;
  - 3.49–5.93× vs the 640 GB DGX, where part of the gain is KV capacity;
  - the gain shrinks with GQA.
- LPDDR5X-PIM: 3.01× tokens/s on Samsung's own SoC, no energy data [V].
- UPMEM: 23× vs a weak 4-core CPU [P].
- Boroumand: 62.7% of mobile-SoC energy is data movement [P]. Its PIM results are gem5-simulated: accelerators 55.4%/54.2%, PIM cores 49.1%/44.6% [P].

**Cost:**
- Capacity:
  - Samsung's HBM2-PIM stack is 6 GB vs 8 GB [P: AiM ISSCC'22 comparison table].
  - AttAcc's per-bank PIM takes 10.84% of the die (~25% if sized like HBM-PIM) [P].
  - SK hynix's VP of system architecture called PIM likely niche, because logic displaces bits while inference is capacity-bound [Pr: DIGITIMES]. SK hynix is an interested party betting on packaging.
- DRAM-process logic: 3× slower transistors, 3 metal layers [V: UPMEM].
- Refresh doubles per ~10 °C above 85 °C [P].
- No virtual memory or coherence.
- PIM area and measured pJ/bit [pend: Kwon ISSCC'21].

**Status:**
- Niche. HMC is dead. HBM-PIM is at demo stage.
- UPMEM was acquired by Qualcomm [S].
- Baselines are often weak. Newton: 54× vs a real GPU but 10× vs an ideal non-PIM design [P]. AiM: "up to 10×" from FPGA emulation [P].

### B1. Cheaper or extended bytes

**Evidence:**
- IBM MXT compression: 2:1–6:1; halving installed memory cut system cost >40% at near-equal SPECint [P: IBM 2000].
- Google zswap: ~20% of data, 4–5% memory TCO [P].
- TMTS: 25% of DRAM on a slower tier at <5% slowdown [P].
- Vistara recycled DDR4: up to 25% fewer servers, ~50% fewer OOMs [P].

**Cost:**
- Vistara's tier has ~10× less absolute bandwidth (2.7–3.6× less per GB) and +92% idle / +60% loaded latency [P/D].
- ByteDance: Spark 1.4–9.8× slower when interleaved across DRAM and CXL. Its "26% TCO" figure is modelled [P].
- Equal local capacity beats far memory by 2.5–9.7% [P: Fastswap].
- GreenSKU's CXL carbon gain is only +1% [P].

**Status and lesson:**
- Deployed: Meta [P], Google [P], Azure M-series [Pr].
- Optane was wound down in 2022 ($559M impairment) [P]. It reportedly sold below DRAM $/GB while costing more to make [S]. A B1 tier must be cheaper to *make*, not just to sell.

### B2. Sharing across hosts

**Evidence:**
- Pond: 7–9% less DRAM [P].
- Berger: small switchless (multi-headed) CXL pools are often ROI-positive [P].
- Mooncake: production RDMA KV pool serving +75% requests [P].
- PolarCXLMem (one-rack prototype): 2.1× vs RDMA. Its authors call switch latency "negligible in cloud database scenarios" [P].

**Cost:**
- Switched CXL pooling of VM memory is mostly ROI-negative. A switched 8-socket pool costs ~2.5× a switchless one, and large pools may be "counterproductive" [P: Berger, modelled].
- Switches add ≥220 ns [V: XConn, cited by Octopus]; our derived switched-pool adder is +284–305 ns [D from P: PolarCXLMem].
- Tail latency +10–82% [P].
- Fault tolerance costs memory: Carbink uses up to 35% more memory than Hydra, its fault-tolerant baseline, in exchange for 29% lower tail latency [P].

**Status:**
- Niche, where data is bulk and predictable: KV pools; database buffer pools (prototype).
- The Machine, Gen-Z and Intel RSA are dead. RSA hinged on silicon photonics [P: Aguilera].

**Pattern** [Syn]:
- The deployed cells (A1, B1) need no programming-model change.
- The ambitious cells reached volume only where a vendor controls the software stack, or where data is bulk and predictable.
- The critiques mirror each other: A2 is limited by process, heat and capacity; B2 by latency and switch cost.

## 5. Coupling

### 5.1 A creates demand for capacity tiers

At fixed cost and beachfront, moving toward compute trades capacity for bandwidth. The capacity has to come from a second tier:

| System | Fast tier | Capacity tier | Source |
|---|---|---|---|
| GB200 NVL72 | 13.4 TB HBM3E at 576 TB/s | 17 TB LPDDR5X at 14 TB/s, coherent over NVLink-C2C | [V] |
| d-Matrix Raptor | 3D DRAM on chiplets | LPDDR5X on each chiplet | [V] |
| SOCAMM2 racks | — | >50 TB CPU-attached LPDDR per rack; >80% lower time-to-first-token from KV offload on GH200 | [V] |
| ZeRO-Offload / ZeRO-Infinity (training) | GPU HBM | CPU DRAM and NVMe; optimizer on CPU; 13B params on one V100; 32T params on 512 V100s | [P] |
| Cerebras CS-3 | 44 GB on-wafer SRAM | 1.5 TB–1.2 PB MemoryX | [V] |

**GPU scale-up domains are memory-semantic disaggregation inside the A market:**
- NVL72 runs at 1.8 TB/s per GPU; Rubin's NVLink 6 at 3.6 TB/s [V].
- AMD Helios offers a "distributed shared memory model" [V].
- UALink spans up to 1,024 accelerators [V].
- Peer HBM is used in production to *move* KV (NIXL). As a capacity tier it is research: SiDP 1.8× KV capacity (offline only) [P]; SwiftCache 3.98× longer context [P].

### 5.2 A competes with B for wafers

**Mechanism:**
- HBM3E needs ~3× the wafer per bit of DDR5, "only increas[ing] with future generations" (Micron, Dec 2025, p.5). HBM4E exceeds 4:1 (Mar 2025, p.6) [P/V].
- The ratio rises because TSV count grew 5.0× from HBM1 to HBM3E [V: SK hynix].

**Reallocation pressure:**
- In 2024–25, HBM out-earned DDR per wafer. TrendForce put the HBM:DDR price ratio at >4×, above r ≈ 3 [S]. SemiAnalysis called HBM "margin accretive" [S]. Wafers moved to HBM.
- Handy names HBM's ~3× die area as one of three drivers of the 2026 shortage [V]. Other contributors: AI demand for DDR5 and LPDDR, capex discipline, DDR4 end-of-life, and Samsung HBM yields [Pr/S].
- Whether HBM sets a true price *floor* under DDR in 2027 depends on HBM4-era prices holding (Appendix B).

**Who benefits:**
- Only B tiers that need no leading-edge wafers gain relative ground: recycled DDR4 (bounded by server retirements) and compression.
- New-DDR5 CXL and SSD offload are not spared: NAND rose +55–60% QoQ in 1Q26 [Pr].
- The shortage *amplifies* B's payoff but did not cause B. The B1 designs predate it. The adoption link rests on one interested vendor (Marvell) plus press reports.

### 5.3 B pulls compute toward the far tier

**Why:** far memory is low in bandwidth per GB and high in latency. Offloading dependent accesses is "required" [P: Miller et al.].

**Deployed (training):** ZeRO-Offload, ZeRO-Infinity and MemoryX all run the optimizer at the capacity tier [P/V].

**Research results:**

| System | Result | Caveats |
|---|---|---|
| Teleport | Up to 11× better than LegoOS | [P] |
| PULSE | 9–34× lower pointer-traversal latency | [P] |
| CXL-ANNS | 3.8× vs an all-DRAM oracle | [P] |
| BEACON | 4.70× vs best DIMM-NDP | Simulated. Compute on the CXL board or switch. Only 85–91% of the comparison design's energy efficiency; communication 52–61% of energy before locality optimizations [P] |

**Products and prototypes:**
- Samsung CXL-PNM is a paper prototype [P, simulated]. Its cost case uses an *assumed* $7,000 per card vs $10,000 per A100, with no switch.
- XCENA MX1: 4.7× throughput vs a host reading over CXL, but only 2× vs local DRAM [V].
- Marvell Structera A: 16 Neoverse V2 cores in a CXL controller [V].

**Counter-point** [Syn]:
- A CXL controller, board or switch is already logic-process silicon. Compute there escapes A2's process and thermal objections.
- But it sees only channel-level bandwidth: ~3× the link for DDR5 controllers, ~17× for LPDDR5X CXL-PNM [D]. It escapes PIM's problems by giving up most of PIM's benefit, and pays in communication energy (BEACON).
- Precedent: computational storage had products, but programmability and missing standards "prevented the broad usage" [P: CIDR'21].

## 6. History in brief

**Same split, often the same people** [P]:
- Patterson and Anderson: IRAM (1997) and NOW (1995).
- Oskin: Active Pages and Grappa.
- J. Chang: memory blades and the 2014 NDP report.
- Ranganathan: memory blades and Nanostores (compute next to stacked NVM, with scale-out; a "hierarchy of computes").

**Different markets and baselines:**
- IRAM targeted the off-chip bus (98.5 vs 4.55 nJ per access), aimed at games, PDAs and embedded devices.
- Network RAM targeted *disk* (remote page 1.44 ms vs 3.6–14.3 ms), aimed at science clusters [P].
- IVY (1989) showed the workload boundary already: super-linear speedup when data outgrew one node, "no speedup at all" on read-once streaming [P].

**Kill conditions:**
- Each wave had its own.
  - PIM: DRAM-process logic, the commodity model, programmability.
  - Network RAM: it only beat disk.
- Cheap DRAM was the common factor [Syn]. The 2014 NDP report says the 1990s designs did not fade for lack of performance or efficiency: "Moore's law allowed the basic von Neumann machines to outrun them" [P].
- The least-disruptive forms survived: hardware CC-NUMA, eDRAM caches, HBM over HMC.

**Why now differs** [Syn]:
- Since ~2011, DRAM $/bit falls ~12–15%/yr, not ~33–36%/yr, so neither end can be outrun by buying more DDR.
- "End of DRAM scaling" was declared before (Wulf & McKee 1995; Ranganathan 2011, who bet on PCM/memristors and lost). The difference now is a decade-long *measured* $/bit slowdown, not a forecast or the 2026 spike.
- Also new:
  - packaging that decouples DRAM and logic processes (SeDRAM 25 nm DRAM on 55 nm periphery logic [P]);
  - an anchor workload on vendor-controlled stacks (AI);
  - CXL standardization;
  - hyperscalers that own their OS.
- Aguilera et al.'s "pressing problem + feasible technology" test [P] fits B, but does not address A.

## 7. Alternative explanations, weighed

**(a) Different markets.** True and first-order, then and now. A's evidence comes mostly from accelerators and mobile; B's from CPU cloud.
- The strongest both-at-once cases are single workloads: LLM serving, DLRM, Chrome's ZRAM (far memory whose compression is a PIM target [P: Boroumand]), and GB200.
- Markets differ *because* their binding constraint differs. The market explanation and the resource explanation are compatible.

**(b) Vendor incentives.** Real and complementary.
- Memory makers want out of commodity pricing: custom HBM base dies; Samsung's PIM and aHBM; SK hynix's packaging bet.
- Controller vendors and startups gain a product category.
- Hype residue (HMC, The Machine, Gen-Z, RSA) shows incentives alone don't make a direction work.

**(c) Different communities.** Context, not cause.
- PIM papers lean on simulation with weak baselines.
- Disaggregation papers lean on production traces, but their TCO numbers are usually modelled.
- No paper we found treats near and far as one *quantitative* axis. Qualitative sketches exist: IRAM, Nanostores, the 2014 NDP report.

## 8. Implications, open questions, and what would change our mind

**Implications:**
- Specify memory products by frontier position: GB/s per GB, $/GB, pJ/bit with scope, and idle and loaded latency. Attach point is the wrong axis.
- On server CPUs, any re-entry into the band between DIMM and HBM must beat "MRDIMM-12800 + large SRAM LLC + CXL".

**Prediction, hedged** [Syn]:
- Passive HBM with logic base dies, and single-host CXL expansion, persist.
- A2 and B2 stay niche outside vertically integrated AI stacks.
- Compute at the far tier grows where software is vertically integrated, as it already has in training.

**Open questions:**
1. Pooling break-even (Levis; Berger's %-of-BOM model) at 2026–27 DRAM prices.
2. Does an HBM-parity floor bind in 2027?
3. A quantitative placement model ρ × I × MLP that includes Hao's bandwidth-saturation rule.
4. Can 3D DRAM–logic stacking's capacity cost be offset by LPDDR or CXL tiers at system level? (d-Matrix already pairs them.)
5. What workload, if any, would justify HBM on a server CPU again?

**What would count against this account:**
- **CXL capacity tiers keep growing even after DRAM $/GB returns to its long-run trend.** That would mean B is driven by something other than capacity cost (e.g., fault isolation or lifecycle).
- **CPU HBM returns while MRDIMM keeps scaling.** That would contradict "the middle caught up".
- **PIM wins at iso-stack, iso-capacity baselines in silicon.** That would make A2's case independent of the A1 trade-offs.

---

## Appendix A: Latency detail

**Measured or modelled latencies:**

| Path | Latency | Source |
|---|---|---|
| H100 → HBM | ~300–330 ns | [S] (confounded by GPU hierarchy) |
| Grace → LPDDR5X | >200 ns | [S] |
| x86 → DDR | ~78–146 ns measured (DDR4/DDR5) | [P] |
| Direct CXL, measured | +~100–280 ns over local; tails ~1 µs at p99.99+ | [P: Octopus, Vistara, Melody, Levis] |
| Switched CXL pool | 549 ns vs 146 ns local-socket (3.76×); 651 vs 231 ns remote-socket (2.82×) | [P: PolarCXLMem Table 1] |
| 8–16-socket multi-headed pools | 155–180 ns absolute (≈+75–90 ns over local) | [P: Berger, modelled] |
| 64-socket pools | 270–345 ns | [P: Berger, modelled] |

Spec-based estimates of +50–90 ns are optimistic.

**Local memory still needed.** In every deployed tiered system, the DDR middle still holds most capacity [D from P]:

| System | DDR share of capacity |
|---|---|
| Vistara | 75% |
| TMTS | 75% |
| TPP | 67% |
| Memstrata | 50% |

Berger's pools also require >25% of memory to stay local [P].

## Appendix B: HBM price floor

**Definition.** While HBM supply is rationed, a vendor is indifferent between HBM and DDR when

  P_DDR ≈ P_HBM / [r(1+c)]

- r = wafer trade ratio, which already includes yield.
- c = extra non-wafer cost (TSV, stacking, test, foundry base die), assumed 0–0.2.

It is a long-run arbitrage bound, not a short-run price floor: HBM output is also gated by back-end capacity.

**Estimates:**

| HBM price input | r | Implied DDR parity price |
|---|---|---|
| HBM3E, 2025 prices [S] | 3 | ~$4.2–5.0/GB |
| HBM3E, 2026 prices ($10–16/GB [S]) | 3 | ~$2.8–5.3/GB |
| HBM4, 2027 ($32–36/GB [Pr]) | 3–4 | ~$7–12/GB |

**Reading:**
- 2024–25: DDR traded below parity, so the reallocation incentive pushed wafers to HBM.
- 2026: DDR overshot parity in the shortage. Handy's chart shows the cheapest branded spot DRAM rising ~7× from ~$2.3/GB (Jul–Aug 2025) to ~$16/GB by late July 2026. That is legacy spot, so illustration only [V].
- 2027: the floor binds only if HBM4-era prices hold.

## Appendix C: Five-minute rule and Hao et al.

**Break-even history.** DRAM↔flash went from ~15 min (2007) to ~41 s (NVMe, 2017, 4 KB pages) [P]. A 2025 preprint gives ~34 s for CPU+DDR at 512 B and ~5 s on GPU hosts with high-IOPS SSDs [P: Zhang].

**Hao et al. (SIGMOD'24).** Remote memory is "not cheap enough to be a storage device, nor … fast enough to be a performance device" [P: §4.9].

**Provisioning rule:**
1. Size local DRAM by the classic rule.
2. Substitute remote memory until its bandwidth saturates.

The paper's introduction states the rule in the reverse direction.

**Evidence quality:**
- Emulated: 0.1–5 µs latency, 5 GB/s, one node, zipf-0.9.
- Relative prices only.
- The "up to 10×" gain is vs DRAM+disk.
- Can lose at 0.8× price.
- Skew carries it: local memory at 1/6 the size of remote serves more than half of accesses, and throughput stays ~70% at 50× remote latency [P].

## Appendix D: Evidence, tags, revision history

**Provenance tags:**

| Tag | Meaning |
|---|---|
| [P] | Primary, read in full |
| [V] | Vendor claim or slides (Hot Chips 2026 decks read from slide text and images) |
| [P/V] | Primary but from an interested seller |
| [Pr] | Press |
| [S] | Secondary |
| [D] | Our arithmetic |
| [pend] | Awaiting full text |
| [Syn] | Our synthesis |

Energy scopes: full = DRAM-side access; I/O = interface; system = board level.

**Read in full:**
- **Round 1–3, online by agents:** Pond, TPP, Vistara, Memstrata, GreenSKU, Octopus, Levis, Gholami, O'Connor, Horowitz, PrIM, Teleport, Mooncake, ZeRO-Infinity, Xeon Max (arXiv 2505.14294), Mahar, Berger TC'26, historical IRAM/NOW/GMS papers.
- **Round 4, supplied by Kamil:** Berger IEEE Micro'23, ByteDance, PolarCXLMem, Fastswap, Lee 2018, UniIC ×2, Intel SerDes, AttAcc, AiM, CXL-PNM, BEACON, Boroumand, Hao, HeMem, ZeRO-Offload, MXT, SiDP, SwiftCache, IVY, Nanostores, Aguilera, Micron remarks, Dally GTC'26 transcript, DIGITIMES, and the Hot Chips 2026 decks.

**Pending:**
- Kwon ISSCC'21 (PIM area and pJ/bit).
- d-Matrix Raptor ISCA'26 (scope of the 0.37 pJ/bit figure).
- Borg and "Imbalance in the Cloud" traces (the "~60% utilization" figure is illustrative only).
- A64FX application papers.
- The paid SemiAnalysis section.
- TrendForce, Citi and Goldman.

**Known weaknesses:**
- PIM speedups are often simulated against weak or mismatched baselines.
- Far-memory TCO is often modelled.
- The Meta bandwidth-tiering result [P: Mahar] is emulated and covers one workload.
- The density table uses peak specs.
- Hot Chips bar-chart readings are approximate.

**Revision history:**

| Version | Change |
|---|---|
| v0.1 | Initial draft |
| v0.2 | Review 1 (`notes/r2d_review.md`): 2-D frontier, 2×2, latency as a constraint |
| v0.3 | Gap evidence |
| v0.4 | Full texts (`ROUND4_DELTAS.md`) |
| v1.2 | §2.0 GPU+HBM row corrected after the beachfront explainer's three review rounds: edge density at a given reach; power as co-limit; DRAM-core energy dominates after the interposer |
| v1.1 | §2.0 first principles of bandwidth limits; executive-summary pressure (i) reworded (Kamil's question on the power budget) |
| v1.0 | Review 2 (`notes/r5_review_v04.md`): AttAcc baselines fixed; B2 scoped; "floor" → reallocation pressure; vacated-band claim hedged; energy-gap hedge; Dally retagged; restructured with an executive summary |
