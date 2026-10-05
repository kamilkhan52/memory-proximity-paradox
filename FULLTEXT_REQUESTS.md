# Full texts requested (drop PDFs into ./fulltext/ — any filename is fine)

## High priority (thesis-critical)
1. Hao, Zhou, Yu, Stonebraker — "Towards Buffer Management with Tiered Main Memory", SIGMOD'24, doi:10.1145/3639286 — five-minute-rule break-evens incl. CXL/RDMA (unifying framework) [Q4]
2. Kwon et al. — Samsung HBM2-PIM (FIMDRAM), ISSCC'21 — PIM area & capacity penalty, internal vs external pJ/b [Q1]
3. AttAcc — ASPLOS'24, doi:10.1145/3620665.3640422 — speedups, baselines, iso-capacity? HBM-PIM capacity cost [Q1,Q4]
4. Hot Chips 2026 slide decks (site returned 401): d-Matrix/Meta "Raptor" 3D DRAM; Samsung HBM base-die; Micron (HBM:DDR5 wafer ratio, BW-growth chart); Samsung LPDDR5X-PIM; XCENA MX1; NVIDIA/Groq LPU [Q1,Q3]
5. Berger et al. — "Design Tradeoffs in CXL-Based Memory Pools", IEEE Micro 43(2) 2023, doi:10.1109/MM.2023.3241586 [Q2]
6. Tang et al. (ByteDance) — "Exploring Performance and Cost Optimization with ASIC-Based CXL Memory", EuroSys'24, doi:10.1145/3627703.3650061 [Q2]
7. UniIC hybrid-bonded stacked DRAM — VLSI'23 ("135 Gbps/Gbit 0.66 pJ/bit") and IEDM'20 ("0.88 pJ/b") — interface-only vs full access energy [Q3]
8. Lee et al. — IEEE Access 2018, doi:10.1109/ACCESS.2018.2845861 — is DDR4 "22 pJ/bit" I/O-only or full access? [Q3]

## Medium
9. Boroumand et al. — "Google Workloads for Consumer Devices", ASPLOS'18, doi:10.1145/3173162.3173177 — per-kernel data [Q1,Q4]
10. Park et al. — Samsung LPDDR-based CXL-PNM, HPCA'24 — does the 30% HW-cost claim include controller/switch? [Q5]
11. Lee et al. — SK hynix GDDR6-AiM, ISSCC'22 — area overhead, pJ/b [Q1]
12. Amaro et al. — Fastswap "Can far memory improve job throughput?", EuroSys'20, doi:10.1145/3342195.3387522 [Q2]
13. Alibaba PolarCXLMem — SIGMOD'25 industry track [Q2]
14. BEACON — MICRO'22 (near-data processing in CXL memory for genomics) [Q5]
15. DIGITIMES 2026-08-27 "SK Hynix questions PIM as thermal, packaging limits constrain HBM" [Q5]

## Low / optional
16. HeMem, SOSP'21, doi:10.1145/3477132.3483550 [Q4]
17. Tirmazi et al., Borg traces, EuroSys'20; Alibaba trace papers (~60% memory utilization basis) [Q2]
18. Intel 10nm PCIe Gen5 SerDes (IEEE doc 9075947) — 11.4 pJ/b [Q3]
19. Dally GTC 2026 talk — "HBM4 read ~1000x NVFP4 MAC" [Q1]
20. "To PiM or Not to PiM" (ACM Queue); Aguilera et al., SIGOPS OSR 2023 [Q5]

## Added by R2a (supply economics) — mostly industry/paywalled
21. Micron Dec 2025 earnings prepared remarks (PDF) — HBM4E ">4:1" trade ratio
22. SemiAnalysis "Scaling the Memory Wall" (paywalled section)
23. TrendForce paid reports — per-wafer revenue, contract $/GB
24. Citi / Goldman DRAM notes (currently secondary only)
25. Google (Cherian) remarks — recording/transcript: are the DDR4 reuse adapters CXL?
(Micron Hot Chips 2026 deck = item 4)

## Added by R2c (history) — optional, nothing blocking
26. Ranganathan, "From Microprocessors to Nanostores", IEEE Computer 2011 — did he propose both NMP and disaggregation?
27. Kim & Chen 1999, "Assessing merged DRAM/logic technology" — ~22% speed / up to 80% area penalty [abstract only]
28. Kogge, "Short History of PIM" (1999 web page, not archived)
29. FlexRAM (ICCD'99), DIVA (SC'99), IVY (Li & Hudak), TreadMarks, Comer & Griffioen — currently via later papers' descriptions
(Aguilera et al. SIGOPS OSR'23 = item 20)

## Added by R3 (gap evidence) — low priority
30. Tremaine et al., IBM MXT, IBM J. R&D 2001 — compression ratios [abstract only]
31. ZeRO-Offload (USENIX ATC'21) — 13B on single V100 [abstract only]
32. A64FX/Fugaku capacity-constraint studies — was 32 GB HBM-only a limit for apps?
33. SwiftCache / SiDP (peer-HBM as KV capacity tier) [abstract only]
34. Micron SOCAMM2 press release / datasheet — BW & power vs RDIMM
35. Any Intel statement on HBM-Xeon roadmap after Xeon Max

## Round-4 status (2026-10-03): see fulltext/MANIFEST.md. Still outstanding:
- Kwon et al. ISSCC'21 HBM2-PIM (PCU area, measured pJ/b) — capacity part now resolved via AiM ISSCC'22 table
- NEW: d-Matrix Raptor paper (ISCA'26) — what the 0.37 pJ/b covers
- SemiAnalysis paid section; TrendForce; Citi/Goldman; Google Cherian
- Tirmazi et al. Borg EuroSys'20; Lu et al. "Imbalance in the Cloud" BigData'17
- A64FX application papers: BWA-MEM2 (TCBB 2023), VPIC (JOCS 2022) — was 32 GB a constraint?
- Kim & Chen 1999; Kogge PIM history; To PiM or Not to PiM; FlexRAM/DIVA/TreadMarks/Comer
- (opt) Choi 2023 preprint (AttAcc 3.19x)
