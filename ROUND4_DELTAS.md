# Round-4 deltas to apply to SYNTHESIS v0.3 -> v0.4

## Q4 (Hao, Boroumand, HeMem) — received
1. §3 Hao pending -> Hao computes NO CXL break-even; says remote memory "not cheap enough to be a storage device, nor fast enough to be a performance device" (§4.9 p.31:12); provisioning rule = size local DRAM by classic rule, then substitute remote "until it saturates the remote memory bandwidth" = a rho/bandwidth stopping rule. Evidence weak: emulated 0.1-5us, 5 GB/s, one node, zipf-0.9; relative prices 0.1/0.3/0.8x; "up to 10x" vs DRAM+disk; at 0.8x price 3-tier can lose (p.31:21). Skew: local 1/6 of remote serves >half of accesses; ~70% throughput at 50x remote latency (p.31:16).
2. §3 add: five-minute rule breaks for the middle tier because it assumes unlimited fast-tier bandwidth -> needs a bandwidth term (Hao p.31:12).
3. Boroumand: 62.7% [P]; PIM results gem5-simulated; 55.4/54.2% = PIM accelerators, PIM cores 49.1/44.6% (p.1-2, 11). Chrome ZRAM = far memory whose compression is a PIM target (both directions in one workload).
4. Remove Hao from §10 pending list.
5. Pond stranding: "6% when 75% of cores scheduled (daily avg), >10% at ~85%" (Pond §3.1) — replace "about 6% at the median".
6. §0 decode attention "~1 FLOP/B at any batch size" -> add "(MHA; xGQA group factor)".
7. HeMem: Optane read 32 / write 11.2 GB/s vs DRAM 107/80 -> read vs write access density differ (write-heavy pages prioritized to DRAM).

## R2a (Micron remarks, Micron/Handy/SK hynix HC'26 decks; SemiAnalysis free part only) — received
1. Micron ratio: "3-to-1 ... only increases with future generations" Dec-2025 prepared remarks p.5 [P]; HBM4E ">4:1" is Mar-20-2025 prepared remarks p.6 [P] (not Dec-2025). HBM4: no number ("higher"). Micron HC'26 slide 11: "~3X more silicon" HBM3E vs DDR5 incl. packaging/mfg complexity; no HBM4/4E number in deck.
2. Remove "Micron >4:1" from §10 pending.
3. c = non-wafer cost only (r already includes yield) — TSV/bump/stack/test, foundry base die; c=0-0.2 is assumption.
4. Floor table: HBM3E r=3 primary -> ~$4.2-5.0/GB at 2025 prices; $2.8-5.3 at 2026; HBM4 2027 (r 3-4, $32-36 press) ~$7-12/GB; label r=4 as HBM4E; drop r=2.5 for HBM4.
5. Replace "forward risk, not today's driver": 2024-mid-2025 DDR priced BELOW HBM3E floor -> wafers shifted to HBM (floor binding); 2026 DDR overshot floor (shortage); 2027 binds again only if HBM4-era prices hold.
6. Causes: Handy HC'26 slide 4 lists HBM "3X the die area of DDR" as one of three shortage drivers -> HBM is a cause of the shortage itself (even if only an amplifier of B adoption). Handy slide 6 (chart, approx): DRAM lowest spot ~$1-2.3/GB early/mid-2025 -> ~$16/GB Jul-Aug 2026 (7.1x).
7. Why r rises: SK hynix HC'26 slide 11 TSV count x5.0, TSV area penalty x1.3 from HBM1 to HBM3E.
8. §2 HBM3E row: cite Micron HC'26 slide 11 / prepared remarks.
9. (opt §5.1) SemiAnalysis free part: HBM and DDR/SSD KV offload "do not compete with each other directly"; HBM "margin accretive" for SK hynix & Micron Aug-2025.
10. (opt §5.1) Micron HC'26 slide 5: memory silicon >8x GPU silicon in an 8-stack HBM4 package.
11. §10: add Micron, Handy, SK hynix HC'26 decks as read. SemiAnalysis paid section still pending.

## Q5 (DIGITIMES, SK hynix HC'26, CXL-PNM HPCA'24, BEACON) — received
1. §4 A2 "capacity penalty [pend]" -> SK hynix VP System Architecture Youngpyo Joo (Dell Tech Forum 2026 keynote, via DIGITIMES 2026-08-27 [Pr]): PIM logic area displaces bits while inference is capacity-bound; packaging "a far more viable industry focus"; PIM "may serve niche workloads". (Die cost/power/leakage points are DIGITIMES's framing.) Kwon ISSCC'21 still not provided.
2. §4 A2 thermal: SK hynix HC'26 slide 11 "Thermal burden: x2.2" over 6 HBM generations; slide 12 thermal resistance -> 0.40x of HBM2 [V, slide images].
3. §4 A2 status & §0 pt5: SK hynix publicly calls PIM niche [Pr]; SK hynix HC'26 deck doesn't mention PIM. Stated end-state: memory stacked directly on accelerators [Pr] (opt §5.1).
4. §5.3 CXL-PNM: [P; simulated] — 7nm params, FPGA functional prototype only, ASIC in development -> "paper prototype", not announced product. Baseline 8x A100-40GB.
5. CXL-PNM cost: assumed $7,000/card vs $10,000/A100 (no derivation), device-only, no switch -> doesn't test Levis's fabric argument (Park Table III p.980). Single card 10.8% slower than one A100 on OPT-13B; ~150 W.
6. §5.3 counter-point: "~3x internal/link" holds for DDR5 controllers (Structera); LPDDR5X CXL-PNM ~17x [D, assumes x16 Gen5].
7. §5.3 add BEACON (MICRO'22, simulated): compute on CXL-DIMM board (BEACON-D) or in CXL switch (BEACON-S), no DRAM die change; 4.70x/4.13x vs best DIMM-NDP, but 85-91% of MEDAL's energy efficiency; communication 52-61% of energy pre-optimization -> "escape DRAM die, pay in communication energy" (pp.727, 737-739).

## Q2 (Berger IEEE Micro'23, ByteDance EuroSys'24, PolarCXLMem SIGMOD'25, Fastswap, Alibaba traces) — received
1. B2 "pays only at scale" -> pays only in SMALL SWITCHLESS pools (<=16 sockets, multi-headed devices), single-digit-% savings; "most switch-based configurations are ROI negative"; switched 8-socket pool ~2.5x cost of switchless; large pools may be "counterproductive" (Berger p.7-8). Levis's >=24-node break-even assumes a switch.
2. Berger latency (modelled): 8-16 socket MHD pools +155-180 ns; switch-only +23-38% more; 64-socket 270-345 ns; >25% memory must stay local (p.5-6). Stranding up to 30%, median 6-10%, P95 25%.
3. "50% of server cost" is circular: Berger cites Pond, Pond cites Next Platform 2020; Berger's cost model ASSUMES ~half (p.1, p.7).
4. PolarCXLMem: in-house prototype rack, one XConn switch, 2 TB; switched latency 549/651 ns vs 146 local (3.76x) (Table 1 p.4) — measured switched-pool number; 2.1x/1.55x vs RDMA (not local); 30% memory saving (not 50%); NO production deployment reported -> PolarDB production = press only.
5. ByteDance: "25.98% TCO / 32.71% fewer servers" = model on example params (p.13), not measured; measured CXL idle 250 ns, 56.7 GB/s; Spark interleave 1.4-9.8x slower; LLM +95% with only 2 DDR5 channels/domain (inflated, p.3).
6. Fastswap: makespan +5-15% testbed; sim median 3-11%, up to 47%, -6% CPU-bound; equal local capacity beats far memory by 2.5-9.7% (p.11) -> "upgrade granularity" claim weak.
7. ~60% utilization: Alibaba 2017 trace ~1,300 machines/12 h; Cheng'18 "above 50%" -> illustrative only. Borg/Imbalance-in-the-Cloud not provided.
8. Open Q1: also redo Berger's model (costs as % of server BOM) at 2026 DRAM prices.
9. §10: Berger, ByteDance, PolarCXLMem now [P].

## R2c (Nanostores, Aguilera OSR'23, IVY) — received
C1. "both waves killed by the same thing [P]" -> [Syn]; cheap DRAM = common factor; each wave also had its own sourced kill conditions (L265, L83, L318).
C2. 2014 workshop quote is from a sidebar (p.39); restore first clause: 1990s designs did not fade for lack of performance/energy efficiency.
C3. -33-36%/yr in history section -> [Syn].
C4. "software DSM died" -> "stayed niche" (IVY; Aguilera p.44).
C5. FACTUALLY WRONG (my claim): "1990s split did NOT follow market lines ... favours resource-pressure". Berkeley aimed IRAM at games/PDAs/embedded (ICCD'97 §3; Computer'97 p.77) and NOW at building-scale science computing -> split DID follow market lines. History shows market + resource-pressure explanations are compatible, not that resource pressure wins. Fix §6 and §7a.
C6. Add caveat: "end of DRAM scaling" declared before (Ranganathan 2011 p.42; Wulf & McKee 1995) -> failed bet on PCM/memristor replacement. Difference now: measured price data, not forecast.
C7. Same-people list: add Ranganathan (memory blades 2009 + nanostores 2011 = compute next to 3D-stacked NVM + shared-nothing scale-out, NOT disaggregation; "hierarchy of computes" Fig.4 p.45).
C8. (opt) Aguilera "why now" two-part test: pressing problem + feasible technology (p.40); predicted CXL pooling in 3-5 yrs of 2023 (p.41); coherence scales only to rack(s) (p.44). Aguilera does NOT mention near-memory compute -> don't cite for complementarity.
C9. §7b: Intel RSA failed on silicon-photonics dependency (Aguilera p.40); The Machine on memristors.
C10. §7c "no paper treats near vs far as one design axis" -> "one QUANTITATIVE axis" (IRAM'97, nanostores, NDP'14 sketched it qualitatively).
C11. (opt §6) IVY 1989: super-linear speedup when data outgrew one node; "no speedup at all" on read-once streaming (dot product) (p.346) -> workload boundary between moving memory away vs moving compute to memory, already in 1989.

## Q3 (Lee IEEE Access 2018, UniIC IEDM'20/VLSI'23, Intel Gen5 SerDes) — received
1. §2 DDR5 row energy -> "~16.5-21 pJ/b, full DRAM-side access (~9 on-chip + ~5 DRAM I/O + ~5 activation), excl. static/refresh/host PHY/DIMM buffer [P: Lee 2018 Fig.2a p.31389, Table 3 p.31395]". Same scope as HBM2 3.92 -> DDR4 ~4-5x HBM2 per bit; I/O part only ~5 vs 0.3; most of gap is INSIDE the die. Horowitz ">20 pJ/b I/O" is DDR3-era incl. termination/static.
2. §2 HBM row: 0.3 = wire-only interface; interface incl. PHY ~1.5 pJ/b [P: IEDM'20 Table 1].
3. §2 CXL row: link ~6-23 pJ/b per crossing (Intel 10nm Gen5 SerDes 365 mW/lane = 11.4 pJ/b, 37 dB) -> direct CXL access ~22-44 pJ/b, switched ~28-67, excl. controller/switch [D]. CXL ~1.3-2x local DDR energy/bit -> CXL energy case only at system level.
4. §2 LPDDR5X: Lee 2018: LPDDR ~12 pJ/b, attributed to mm-scale buses with few devices -> supports NVIDIA "1/8" as distance/buffering effect.
5. Hybrid bonding: 110,000 vias/mm2 at 3 um pitch [P]; 0.66/0.88 pJ/b are INTERFACE only (logic die = DRAM periphery 55nm, not processor). vs PHY-inclusive HBM interface ~1.5 -> ~2x, not 77-83%. No full-access number; HBM's ~3.45 of 3.92 (act + on-die) untouched.
6. §4 A1: never compare 0.66-0.88 interface to HBM 3.92 full access.
7. §4 A2 "2.24 on-die term untouched" reinforced.
8. §6 item 3: SeDRAM (25nm DRAM die bonded to 55nm logic die) as primary evidence of process decoupling — but logic die is periphery, not compute.
9. §0/§8 "shorter, wider wires": scale ~4-5x full-access, mostly in-die.
10. §10: Lee 2018, UniIC read.

## R3 (MXT, ZeRO-Offload, A64FX/Grace, SiDP, SwiftCache, SOCAMM2, HC'26 platform decks) — received
1. §2 table new HBM4 row ~54-73 GB/s/GB: MI455X 432 GB @ 23.3 TB/s; Rubin fleet 11 PB @ 800 PB/s [V HC'26]. (HBM3E 33-42.)
2. §2 LPDDR row: Vera 1.5 TB @ 1.2 TB/s ~0.8 [V]; NVIDIA slide: LPDDR 5x BW/W vs DDR5, MRDIMM-12800 0.8x (bar-label mapping from vision description -> [S]).
3. §2 MRDIMM row: 2026 16-ch MRDIMM-12800 "up to 1.6 TB/s" (Intel DMR; AMD Venice also 1.6 TB/s [V]) -> 1.6/0.8/0.4 GB/s/GB at 1/2/4 TB.
4. §2 item 2 & §5.1: replace "absence of evidence" with slide evidence: Intel Diamond Rapids = 16ch DDR5-8000/MRDIMM-12800, CXL 3.0, 1.28 GB LLC, no HBM in deck; Fujitsu MONAKA (A64FX successor) = 12ch DDR5-8000+, CXL 3.0, LLC on 3D-stacked SRAM die, no HBM [V HC'26]. CPU pattern: near tier moved HBM -> stacked SRAM; capacity -> CXL; middle = MRDIMM.
5. CORRECTION: "nothing ships between ~1 and ~35 GB/s/GB" WRONG -> Xeon Max (~26 peak, ~11 sustained), A64FX (~32), SX-Aurora (~25-32) shipped in that band and were DISCONTINUED -> "band occupied, then vacated" (on CPUs). (GDDR cards / Apple on-package LPDDR may sit there — unverified.) Fix §2 item 3, §9, open Q5.
6. §4 B1: MXT [P, 2000 IBM tech paper]: typical 2:1-6:1; SPECint2000 within 1 point of conventional; halving installed memory cut system cost >40%.
7. §5.1: SiDP [P] up to 1.8x KV capacity, 1.5x throughput, offline batch only, 12% slower at bs=1; SwiftCache [P] up to 69% lower P99 TTFT, 3.98x longer context; AMD Helios "distributed shared memory model" maps peer HBM over scale-up [V]; Rubin NVLink 6 3.6 TB/s/GPU [V]; SOCAMM2 192 GB/module, 9.6 Gbps, >2/3 better power efficiency vs RDIMM, >50 TB CPU-attached LPDDR/rack, >80% lower TTFT from KV offload on GH200 [V]; NVIDIA separate KV-cache storage rack [V]. Don't claim Fugaku capacity-limited (MEMSYS'24 doesn't test; Grace LPDDR5X ~= A64FX HBM2 STREAM with 15x capacity).
8. §5.3: add ZeRO-Offload (Ren et al. ATC'21) [P]: CPU mem BW >=10x PCIe, GPU mem another 10x; optimizer on CPU; 8x GPU memory saving; 13B on one V100.

## Q1 (AttAcc, AiM, Dally GTC'26, HC'26 d-Matrix/Samsung/LPDDR5X-PIM/XCENA/LP30/Micron) — received
1. AttAcc [P, simulated]: 2.81x/2.67x vs DGX A100 at SAME 1,280 GB but half HBM stacks (40 vs 80) -> part of gain = more KV capacity, not PIM; vs 640 GB DGX 3.49-5.93x; per-bank PIM = 10.84% of DRAM die (~25% if sized like HBM-PIM); internal BW 9x external; gain shrinks with GQA group size. Drop "3.19x/3.22x".
2. AiM ISSCC'22 [P]: "up to 10x" vs HBM2 GPU, FPGA emulation, no energy number; press 16x/80% not in paper. Its comparison table: Samsung HBM2-PIM = 6 GB (4 PIM dies at half density + 4 normal) = 25% less than standard 8 GB -> resolves Kwon capacity part [P via AiM table]. Kwon still needed for PCU area & pJ/b.
3. Dally GTC'26 transcript [P]: ~10 fJ per MAC; HBM4 "maybe three or four" (pJ/b, unit dropped); "1000x" not said (arith ~1,350-1,800x); most DRAM read energy is moving bit to pins; stacked DRAM ~10x BW at ~10x lower energy/bit.
4. d-Matrix Raptor [V HC'26]: 0.37 pJ/b = measured interface I/O only; 6.3x lower energy vs ~2.4 pJ/b HBM4; 13.5x vs 5.0 pJ/b (interface vs whole-system — apples/oranges); capacity/mm2 ~half HBM4 (11.4 vs 21.9-26.3 MB/mm2) for ~20x BW/mm2; "1/6 of 192 GB" press-only; each chiplet carries LPDDR5X secondary capacity tier.
5. Samsung base die [V]: "-70%" = unitless chart zHBM vs HBM5; "230%" = 4 zHBM vs 4 HBM4E stacks (~14 -> 32 TB/s, ~100 W DRAM power saved) — projections; Samsung table says 4 nm base die for HBM4E (contradicts 2 nm press).
6. LPDDR5X-PIM [V]: 3.01x tok/s measured on Samsung SoC; no energy data.
7. XCENA MX1 [V]: up to 4.7x throughput / 18.7x bytes per J vs host-over-CXL; only 2x / 6.2x vs local DRAM; idle power excluded.
8. Groq 3 LPX [V/D]: no per-chip figures; rack totals -> ~0.5 GB SRAM, ~156 TB/s per chip (~3x10^5 GB/s/GB).
9. Micron 3x/2yr vs <2x/2yr chart cites no sources; roadmap ends at "Processing in Memory".
10. Band claim: device-level LPDDR5X 4.8 GB/s/GB, GDDR6 64/GB per device -> scope the gap claim to server CPU-attached memory.
11. New request: Raptor ISCA'26 paper (scope of 0.37). Optional: Choi 2023 preprint (3.19x).
