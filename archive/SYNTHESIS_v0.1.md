# Closer and farther at once: why memory is moving both toward and away from compute

*Draft v0.1, 2026-10-03. Sources and provenance tags are in `notes/`. Claims tagged [pending full text] rely on items in `FULLTEXT_REQUESTS.md`. [derived] marks our own arithmetic.*

---

## 0. Short answer

The two directions look opposite only if physical distance is treated as the variable being optimized. It is not. The baseline, DDR DIMMs on a parallel bus beside the socket, **bundles bandwidth and capacity at a fixed ratio set by the pins per channel.** It also charges one price per byte regardless of how hot the byte is. Two independent walls are now breaking that bundle:

1. **The bandwidth/energy wall.** Compute grows ~3.0× every 2 years, DRAM bandwidth ~1.6× and interconnect ~1.4× (Gholami et al. 2024). Off-package DRAM costs ~20 pJ/bit, versus ~4 pJ/bit for HBM and <1 pJ/bit for hybrid-bonded or on-die memory. Low-intensity kernels (LLM decode ~1 FLOP/B, embedding gathers ~0.25 FLOP/B) need **more bandwidth per byte stored and less energy per bit**. Only shorter, wider wires provide that (direction **A**).
2. **The capacity/cost wall.** DRAM $/bit decline slowed from ~33–36%/yr to ~12–15%/yr after ~2011. Memory rose from 15.6% to 37.1% of Meta's rack TCO. Cores per socket grew 8→256 while channels grew 4→16. At the same time 20–80% of allocated memory is cold or stranded. This calls for **cheaper bytes and better utilization**, and those can only be added off the DDR bus (direction **B**).

Workload data is extremely skewed: <10% of capacity serves the P90 of bandwidth demand (Meta). The optimal memory system is therefore a spread of tiers along a single axis, **access density (GB/s demanded per GB stored)**. That axis spans ~8 orders of magnitude, from SRAM (~10⁵ GB/s/GB) to NVMe (~10⁻³). A and B are the two ends of one move: **unbundling bandwidth from capacity and widening the hierarchy at both ends.** They are complements, and increasingly the same product: HBM with capacity-extension ports, CXL expanders with compute inside, GB200 with HBM plus LPDDR on one coherent fabric.

---

## 1. "Closer" is not about latency; distance is a proxy

- Physically closer memory is *not* lower latency. Typical values:

  | Memory | Latency |
  |---|---|
  | H100 → HBM | ~300–330 ns |
  | Grace → on-package LPDDR5X | >200 ns |
  | x86 → local DDR | ~80–120 ns |

  Sources: Chips and Cheese measurements, collected in Q3 notes. Latency barely scaled anyway: tRCD fell ~30% while $/bit fell 16× (Chang 2016).
- A buys **bandwidth density and energy per bit**. B gives up latency (+50–150 ns for direct CXL; real devices 214–394 ns, tails to ~1 µs) and bandwidth per GB. In return it gets **$/GB and utilization**.
- So the real design variables are **bandwidth per byte stored**, **energy per bit moved**, **$ per byte stored** and **fraction of bytes actually used**. Distance correlates with these, but it is not the objective.

## 2. The baseline bundles bandwidth and capacity, and both walls hit the bundle

**Access density supplied per tier** [derived from vendor peak specs; see log]:

| Tier | GB/s per GB |
|---|---|
| On-die SRAM (Groq LPU, 230 MB / 80 TB/s) | ~3×10⁵ |
| HBM3E (36 GB stack / ~1.2 TB/s; B200 192 GB / 8 TB/s) | ~33–42 |
| Grace LPDDR5X (480 GB / ~512 GB/s) | ~1.1 |
| 12-ch DDR5-6400, 1 DIMM/ch, 64 GB RDIMMs | ~0.8 |
| same with 256 GB RDIMMs | ~0.2 |
| CXL Gen5 x8 / x16 expander (256–512 GB) | ~0.125 |
| RDMA far memory (400 GbE, 4 TB share) | ~0.01 |
| NVMe Gen5 SSD (15 TB) | ~0.001 |

**Key observation.** On the DDR bus, channel bandwidth is fixed by pins. **Adding capacity dilutes bandwidth per GB** (0.8 → 0.2 when going from 64 GB to 256 GB DIMMs), and **adding bandwidth costs capacity** (1 DIMM per channel at full speed; flagship CPUs held ~5 GB/s/core only by spending pins and dropping to 1 DIMM per channel, Q3). The DDR DIMM sits in what Q3 calls the "unstable middle" of the wire-length versus bits-per-pin trade-off:

- Very wide parallel interfaces work only at mm scale (HBM 1024–2048 bits; hybrid bonding ~10⁵ connections/mm²).
- Serial links give 4–8× bandwidth per pin and metre reach, at +50–70 ns per hop.

The two stable ends of that trade-off are exactly A and B.

**Trend → direction map** (Q3):

| Trend | Effect |
|---|---|
| $/bit flattening | → B strongly |
| Energy per bit | → A strongly. Within HBM2's 3.92 pJ/b only 0.3 pJ/b is I/O; the rest is inside the DRAM die (O'Connor MICRO'17). That is why A keeps pushing past HBM to bank-level PIM and DRAM-on-logic. |
| Pin wall | → both |
| AI walls (bandwidth and capacity) | → both. Parameters grew ~410× per 2 yr vs single-GPU memory ~2× per 2 yr. |
| Latency stagnation | → *enables* B, because a NUMA-sized adder is tolerable |

## 3. Why both pay off: access is skewed, and placement follows access density

- **Skew is universal and per object, not per workload** (Q4):
  - Meta MemProf: <10% of capacity supplies P90 bandwidth.
  - Google: 32% of memory cold at 120 s (1–61% across clusters).
  - Meta TPP: 55–80% idle in any 2-min window.
  - Azure: ~half of VMs touch <half their memory.
  - LLM KV: 10% of blocks get 77% of reuses (Alibaba ATC'25); >50% never reused (Mooncake).
- **Unifying framework.** The five-minute rule (capacity side) and the roofline (compute side) both reduce to an object's access density versus a tier's supplied density and price.
  - The DRAM↔flash break-even interval has *shrunk* to ~41 s (NVMe, 2017) and ~5 s on GPU hosts (Zhang 2025). So more data rationally belongs farther away.
  - Rising compute-to-bandwidth ratios mean more kernels fall below the ridge point. So more data rationally belongs closer.
  - **Both trends are true simultaneously; they push the two ends of the distribution apart.**
  - *[pending full text: Hao et al. SIGMOD'24, the CXL/RDMA-era five-minute rule]*
- **Single workloads that need both:**
  - **DLRM.** The big embedding tables are low-bandwidth and the hot tables are small. MTrainS's HBM+DRAM+SCM hierarchy cut training nodes 4–8×. RecNMP's near-memory processing gave 4.2×. TensorDIMM put near-memory-processing DIMMs inside a disaggregated pool.
  - **LLM serving.** Weights and live KV sit in HBM/PIM at ~1 FLOP/B, while reusable KV is offloaded to DRAM/SSD/RDMA pools. Mooncake reports +75% requests.
  - **Meta bandwidth tiering.** 37.5% of capacity on a 2×-bandwidth tier captured most of the speedup of making all memory fast (1.47× vs 1.55×), with much better performance/cost (ratios flagged as not reproducing exactly).
- **Refinements:**
  - Placement also depends on latency tolerance via memory-level parallelism (OSDI'25 "Beyond Hotness"). Graph workloads suffer most on far memory.
  - CXL can *add* bandwidth: +24–39% on Xeon 6900P with tuned interleave. Under load, the hottest-on-local policy can be 2.3× worse than optimal (Colloid SOSP'24).
  - The axis is therefore really three-dimensional: **access density × latency tolerance × footprint.**

## 4. What each direction buys, and what it costs

| | A: closer / tighter coupling | B: farther / disaggregation |
|---|---|---|
| Optimizes | pJ/bit, GB/s per pin and per mm², GB/s per GB, perf/W and perf/$ in low-intensity phases | $/GB, utilization, capacity per core, embodied carbon, upgrade granularity, OOM reliability |
| Strongest evidence | 62.7% of consumer-device energy is data movement (Boroumand'18); Aquabolt-XL >2× perf, >70% less energy; CENT 5.2× tokens/$; LLM decode at ~0.3% of GPU peak | Memory 37% of Meta rack TCO, 41% of Azure server embodied carbon; Vistara recycled DDR4 at 0.13× $/GB → up to 25% fewer servers; TMTS moved 25% of DRAM to a slower tier at <5% slowdown |
| Pays with | **Capacity**: d-Matrix Raptor 1/6 the capacity of HBM4 for 5.6× bandwidth [secondary]; Aquabolt-XL 6 GB; HBM ~3× wafer per bit | **Latency and bandwidth per GB**: Vistara ~10× less BW, +60% latency; 21–37% of Pond workloads >25% slower fully on CXL |
| Limited by | DRAM-process logic (3× slower transistors, 3 metal layers), thermal (refresh doubles per ~10 °C over 85 °C), no VM/coherence, programming model | latency/tails (+10–82% KV-store tails), switch cost (Octopus: switches "always cost more"), failure domains, software |
| Survived as | **Passive HBM** (HMC dead 2018; PIM still at demos/niche; base-die logic is the fastest-moving part) | **Single-host CXL expansion** (Azure M-series; Meta Vistara). Pooling: Pond 7–9% DRAM saving, Octopus 3–5% net, Levis'23 break-even ~140 TB |

**Pattern.** Each direction succeeded only in its *least-disruptive* form, the one needing no change to the programming model, and failed in its ambitious form. The two sets of critiques mirror each other: A is limited by process and heat, B by latency and switch cost.

## 5. The two directions need each other

1. **A creates demand for B (capacity).** Closer memory is scarcer memory. Every step toward compute trades capacity for bandwidth (HBM, PIM, DRAM-on-logic, SRAM machines). The capacity has to come from somewhere: GH200/GB200 add LPDDR5X behind NVLink-C2C (GB200 NVL72: 13.4 TB HBM3E at 576 TB/s plus 17 TB LPDDR5X at 14 TB/s), and LLM serving adds DRAM/SSD/RDMA KV tiers. Samsung's HBM roadmap adds capacity-extension ports to the base die.
2. **A creates demand for B (supply economics)** (R2a).
   - HBM and DDR share wafers. At a ~2.5–3:1 trade ratio, HBM took 18% → ~30% of DRAM wafers (2025 → 2027) for only 8 → 13% of bits.
   - While HBM is rationed, HBM price divided by the trade ratio sets a floor under conventional DRAM: ~$8–12/GB [derived] vs ~$3/GB in 2023. DDR5 RDIMMs went ~$7 → $14/GB from Q4'25 to Q1'26.
   - This raises the payoff of capacity tiers that need no leading-edge wafers (recycled DDR4 behind CXL, compression, SSD offload).
   - Caveats: the shortage has several causes (AI DDR5/LPDDR demand, capex discipline, DDR4 EOL, an alleged-collusion lawsuit). The far-memory designs predate it, so it amplifies rather than causes them. Yield learning and 2027–28 fabs may lower the floor.
3. **B creates demand for A (compute).**
   - Far memory is low-bandwidth per GB and high-latency, so pointer-chasing and scans across it are slow. Moving compute to the data recovers it: Teleport's unmodified disaggregated execution averages 5.4× slower, and operator pushdown recovers ~10×. PULSE gives 9–34× on pointer traversals; CXL-ANNS gives 111× QPS.
   - Miller et al. (ACM Queue'23) call offload of dependent accesses a "required optimization" for far memory.
   - Products: Samsung CXL-PNM, SK hynix CMS, Marvell Structera A (16 Neoverse V2 cores in a CXL controller), XCENA MX1 (3,072 RISC-V cores, because the link is only 64 GB/s/dir).
   - Observation (Q5): the CXL controller is already a logic-process die beside DRAM. Compute there escapes PIM's two main objections, DRAM-process logic and thermal coupling. It also turns the controller from Levis's "pure cost" into a compute host.

## 6. Historical precedent

*[pending R2c: 1990s IRAM/Active Pages versus NOW/GMS network RAM]*

## 7. Answer to the research question

Both directions attract serious people because **the baseline has stopped being a good compromise for either of the two resources it bundles.**

- For **bandwidth-dense** data, the DDR bus delivers too few bytes per second per byte stored, at too many pJ per bit, and compute keeps outgrowing it. The only lever is shorter, wider wires, ultimately compute inside or bonded onto DRAM.
- For **capacity-dense** data, the DDR bus is too expensive per byte, can't add capacity without stealing bandwidth or pins, and allocates by socket. The result is 20–80% cold or stranded bytes at a time when memory is a third of rack TCO and DRAM $/bit no longer falls fast. The levers are cheaper or recycled media and serial attach.
- Real workloads, increasingly dominated by AI, contain both kinds of data in heavily skewed proportions. So the efficient frontier is a **wider hierarchy**, not a better middle. The directions are opposite in geometry but aligned in purpose: each re-matches a tier's supplied access density and price to the data it holds.

**Points of genuine tension** (not mere complementarity):

1. **Wafer competition:** A's growth raises B's media costs.
2. **Engineering talent and software stacks:** tiering kernels versus PIM programming models.
3. **Placement decisions under latency tolerance:** per-object placement remains an open systems problem.

## 8. Implications and open questions for a memory-industry researcher

- **Products should be specified on the access-density axis** (GB/s per GB, pJ/bit, $/GB) rather than by attach point. Candidate gaps:
  - Between HBM (~35) and DDR (~0.2–0.8): LPDDR-on-package, MRDIMM, SOCAMM, SPHBM4.
  - Between DDR and SSD (~0.001): CXL, compressed tiers.
- **Compute placement follows the far tier.** Expect logic-process controllers (CXL, HBM base die) rather than DRAM-die PIM to host near-data compute first.
- **Open questions:**
  - Pooling break-even at 2026 DRAM prices (redo Levis with $14–25/GB).
  - Whether the HBM-induced price floor is structural or cyclical.
  - A placement theory unifying the five-minute rule, roofline and memory-level parallelism.
  - Whether DRAM-on-logic's capacity penalty (1/6) can be offset by CXL capacity tiers at system level.

## 9. Evidence quality

- Mostly primary sources read in full. Key secondary or pending items:
  - Hot Chips 2026 numbers (d-Matrix Raptor, Samsung base die, LPDDR5X-PIM)
  - AttAcc
  - Kwon ISSCC'21
  - Hao SIGMOD'24
  - Berger IEEE Micro'23
  - ByteDance EuroSys'24
  - DRAM price forecasts
- The access-density table is derived from vendor peak specs, not measured sustained bandwidth.
