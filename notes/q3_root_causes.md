# Q3: Root causes. Which technology and economic trends make the DDR-DIMM baseline inadequate, and which way does each one push (A = closer, B = farther)?

Agent: Q3 sub-agent, 2026-10-02. Method: primary sources read in full where possible (PDFs fetched and text-extracted).
Labels used:
- **[primary]**: read in the full text of the original source.
- **[secondary]**: number taken from a paper that cites the original; the original was not read.
- **[vendor claim]**: manufacturer marketing or whitepaper.
- **[snippet]**: seen only in a search-engine summary; the page was not read.
- **[abstract only]**: taken from an abstract.
- **[derived]**: my own arithmetic from cited inputs.
- **[unverified]**: from memory or not checked.
- **[pending full text]**: depends on an item in the FULL TEXT NEEDED list below.

---

## Round-4 deltas (2026-10-03, full texts F1-F3 read)

Full texts read from `fulltext/`: Lee et al. *IEEE Access* 2018 (F1), UniIC IEDM'20 and VLSI'23 (F2), Intel PCIe Gen5 SerDes (F3). F4 (Micron wafer ratio) is handled by agent R2a.

**D1. DDR4 "22 pJ/bit" is a full device access, not I/O alone.** Lee et al. 2018 never prints "22". The closest values are below.
- **Fig. 2a reading [primary, my reading of the figure].** DDR4 (×4 device, manufacturer A, ~2.4 Gb/s/pin, 85 °C, row-activation-to-read ratio δ = 0.27, datasheet IDD-based) shows a stacked dynamic read energy of **≈19-20 pJ/b**. My bar reading is ±1-2 pJ/b:
  - ≈9 "RD (on-chip)": array, global datalines, internal datapath;
  - ≈5 "RD (off-chip)": the DRAM's output drivers and I/O;
  - ≈5 ACT/PRE.
- **Table 3 derivation [derived].** E_RD/WR = 6.31 nJ and E_ACT+PRE = 15.9 nJ for manufacturer A; 4.52 and 14.5 nJ for B. If each value is per 64-B rank access, which the table does not state:
  - A: 12.3 + 8.4 = **20.7 pJ/b**;
  - B: 8.8 + 7.6 = **16.5 pJ/b**.
- **Scope.** DRAM-side dynamic energy only. It excludes:
  - static, standby and refresh power, which are reported separately in mW/Gb;
  - the host PHY and controller;
  - the RCD/LRDIMM buffer.
- So Sun et al.'s 22 is a reasonable reading of a full-access number. **It is like-for-like in scope with FGDRAM's HBM2 3.92** (act + on-die + I/O, DRAM side).
  - Term by term (DDR4 vs HBM2): ACT/PRE ≈5-8 vs 1.21; on-chip datapath ≈9-12 vs 2.24; I/O ≈5 vs 0.3. Total ≈ **16.5-21 vs 3.9, i.e. ~4-5x**.
  - Caveat: activation assumptions differ. DDR4 uses δ = 0.27 with SPEC CPU and an 8 KB rank row; HBM2 uses measured GPU row locality and a 1 KB row.
- **Correction to my earlier framing.** Horowitz's "DRAM I/O over 20 pJ/bit" (ISSCC'14, citing Malladi ISCA'12, DDR3 era, including termination and static power) is **not** a DDR4 I/O-only figure. DDR4's DRAM-side I/O dynamic energy is ≈5 pJ/b (Lee Fig. 2a).
- **LPDDR4 in Fig. 2a is ≈12 pJ/b.**
  - Lee attributes LPDDR's better I/O efficiency to "the better signal integrity of mobile systems as only few DRAM devices are connected ... through a bus with a distance of up to few millimeters" (p. 31391) [primary].
  - This supports reading NVIDIA's "1/8 power per GB/s" (LPDDR5X vs DDR5) as mostly a distance and buffering effect.
  - In Lee's server simulation, LPDDR4' had *worse* system EDP than DDR4.

**D2. Hybrid-bonded SeDRAM 0.66/0.88 pJ/b is interface energy, not full access. The "77-83% vs HBM" claim is not like-for-like.**
- **IEDM'20 (Bai et al., UniIC) [primary].**
  - The title and conclusion define 0.88 pJ/b as the **"logic-to-memory interface"** energy. That is the hybrid-bonded link between the DRAM array die (25 nm DRAM process) and the product's *own* logic/periphery die (55 nm logic process).
  - Interface: 4096 bits at 266 Mb/s, 136 GB/s per 4 Gb chip, 110,000 vias/mm² at 3 µm pitch, ~2 µm connection length.
  - The product externally is a standard LPDDR4/4X (4266 Mb/s, 200-ball FBGA).
  - Their Table 1 compares this against an **HBM interface of "~1.5 pJ/b"** (~5 mm microbump + interposer path, PHY needed).
  - The number excludes activation, array datapath, and the external LPDDR4X I/O.
- **VLSI'23 (Wang et al., UniIC) [primary].**
  - 0.66 pJ/b "power efficiency"; 2048 IO/Gbit at 541 Mb/s; 135 GB/s/Gbit; two array layers stacked by hybrid bonding plus mini-TSV.
  - **Scope is not defined in the paper.** Table I gives energy only in normalized a.u.: GDDR6 100%, HBM2E 80%, HBM3 53%, SeDRAM'20 12%, this work 9%.
  - Because 0.88/0.66 = 12/9, the scale implies GDDR6 ≈ 7.3, HBM2E ≈ 5.9 and HBM3 ≈ 3.9 pJ/b [derived]. The source and scope of those reference values are not given.
  - "83% vs HBM3" is simply 1 − 9/53.
- **Like-for-like.**
  - **Interface only:** hybrid bond 0.66-0.88 vs the HBM interface. HBM is 0.3 in FGDRAM (toggle-adjusted wire energy) or ~1.5 including PHY (IEDM'20's own comparison). Hybrid bonding is ≈1.7-2.3x better than an HBM PHY link, and not better than FGDRAM's 0.3 wire-only term.
  - **Full access:** no SeDRAM number is reported. HBM2's activation plus on-die terms (3.45 of 3.92 pJ/b) are untouched by the bond.
  - Therefore **the 3D-on-logic tier must not be listed as "0.66-0.88 pJ/b" against HBM's 3.92**. Li et al.'s (arXiv 2604.08044) "77-83% reduction over HBM" inherits the a.u. comparison of unclear scope.
- **Also note:** SeDRAM's "logic" die is the DRAM's own periphery die, not a processor. It demonstrates the interface and process decoupling (DRAM array process + logic process, bonded), not compute-on-DRAM.

**D3. PCIe Gen5 SerDes energy (Intel, Bichan et al., IEEE 2020; ISBN 978-1-7281-6031-3, likely CICC'20 [venue unverified]) [primary].**
- Measured: 32 Gb/s NRZ, 10 nm, 37 dB channel loss at 16 GHz (long reach), **326 mW/lane excluding PLL, 365 mW including PLL → 11.4 pJ/b = 365 mW / 32 Gb/s**, per lane (one TX + one RX at one end) (Fig. 10).
- In the same Fig. 10, comparison designs include 14 nm 195 mW at 28 Gb/s (≈7 pJ/b) and 28 nm 247 mW at 56 Gb/s (≈4.4 pJ/b).
- **Per bit moved across one link, counting both ends [derived].**
  - ≈11.4 pJ/b if both directions carry data. The TX share is at the sender and the RX share at the receiver, assuming TX ≈ RX power.
  - Up to ≈23 pJ/b per *useful* bit if traffic is one-directional (read-dominant) and no partial-width low-power state is used.
  - Shorter-reach, newer-node CXL PHYs should be lower. Sun et al. cite PCIe4 ≈6 pJ/b [secondary].
  - **Range for the CXL link adder: ≈6-23 pJ/b per link traversal, plus the CXL controller (unquantified).**
- **CXL direct-attach access [derived]:** ≈16-21 (DDR device) + 6-23 (link) ≈ **22-44 pJ/b**, plus controller. That is ≈1.3-2x local DDR and ≈6-11x HBM2 full access.
- **CXL switched [derived]:** add a second link traversal (+6-23) ≈ **28-67 pJ/b**, plus switch core.
- The earlier estimate "DDR + ~10-25" stands roughly, now with a primary anchor.

**Synthesis-draft corrections:** see the end of this file ("Round-4: corrections for SYNTHESIS_DRAFT.md v0.3").

---

## FULL TEXT NEEDED

| # | Source | Needed for | Why what I have is insufficient |
|---|--------|-----------|------------------------------------|
| F1 | S. Lee, H. Cho, Y.H. Son, Y. Ro, N.S. Kim, J.H. Ahn, "Leveraging Power-Performance Relationship of Energy-Efficient Modern DRAM Devices," *IEEE Access* 6:31387-31398, 2018. DOI 10.1109/ACCESS.2018.2845861 | The **DDR4 = 22 pJ/bit** figure in the energy ladder. It is the main off-package DDR number. | I have it only as cited by Sun et al. (MICRO'23). I need to know what the 22 pJ/bit covers (I/O only, or I/O + activation + core? which DDR4 speed and configuration? does it include static/termination power?) before putting it next to HBM2's 3.9 pJ/bit full-access figure. Status: **RESOLVED round 4.** Full-access DRAM-side dynamic energy ≈16.5-21 pJ/b; see D1. |
| F2 | (UniIC / Xi'an group) "A 135 Gbps/Gbit 0.66 pJ/bit Stacked Embedded DRAM with Multilayer Arrays by Fine Pitch Hybrid Bonding and Mini-TSV," *VLSI Technology & Circuits* 2023. Also the predecessor IEDM 2020: "A Stacked Embedded DRAM Array for LPDDR4/4X using Hybrid Bonding 3D Integration with 34GB/s/1Gb 0.88pJ/b Logic-to-Memory Interface" (IEEE doc 9372039) | The **3D DRAM-on-logic tier** energy (0.66-0.88 pJ/bit) and the claimed "77-83% reduction vs HBM". | I have only the paper titles and a secondary citation (Li et al., arXiv 2604.08044). I need to know whether 0.66 pJ/bit is interface-only or full access including activation, and at what row-hit rate. Otherwise the comparison with HBM2's 3.92 pJ/bit (full access) may be apples-to-oranges. Status: **RESOLVED round 4.** Interface-only; not like-for-like; see D2. |
| F3 (optional) | "A 32Gb/s NRZ 37dB SerDes in 10nm CMOS to Support PCI Express Gen 5 Protocol," IEEE (doc 9075947), ~2020 (Intel) | The PCIe5/CXL SerDes energy figure (11.4 pJ/bit including PLL/clocking). | Seen only in a search snippet. The IEEE page returned no content. Needed to pin down the CXL link-energy adder. Status: **RESOLVED round 4.** 365 mW/lane = 11.4 pJ/b; see D3. |
| F4 (optional) | Micron, Hot Chips 2026 talk on HBM "wafer penalty" / memory wall | Whether Micron gave a numeric HBM4-vs-DDR5 wafer trade ratio or BW-vs-compute growth numbers. | The Tom's Hardware article body was truncated and igorslab returned 403. I have only the headline claim "~3x silicon vs DDR5, gap widening". Status: handed to agent R2a (round 4). |

Sources I first feared I couldn't get but **did** obtain in full:
- Wulf & McKee 1995: the original note is reprinted inside McKee, "Reflections on the Memory Wall," CF'04.
- Gholami et al. 2024: arXiv HTML and PDF.
- Pond, Pond IEEE Micro, TPP, COAXIAL, Melody, Sun et al. (Demystifying CXL), Das Sharma et al. (CXL intro), FGDRAM, Keckler 2011, Horowitz 2014, Fastswap, Gao OSDI'16, GreenSKU, Levis HotNets'23, Chang thesis 2016.

---

## 1. DRAM scaling slowdown: cost/bit and density

**Cost-per-bit trend broke around 2010-2011.**
- Jim Handy (Objective Analysis, "DRAM Prices Hit Historic Low," Memory Guy blog, 2019-11-20). Over 1991-2019, DRAM followed a "Moore's Law annual cost decrease of about 32%" until "2011 or so". After that, prices moved "along a horizontal line". [primary-blog] https://thememoryguy.com/dram-prices-hit-historic-low/
- AI Impacts (2020) fitted price per GB in two datasets [primary-analysis] https://aiimpacts.org/trends-in-dram-price-per-gigabyte/
  - Pre-2010: McCallum dataset −36%/yr (10x every 5.1 yr); Objective Analysis dataset −33%/yr (10x every 5.8 yr).
  - Post-2010: −15%/yr (10x every 14 yr) and −12%/yr (10x every 18.5 yr).
  - **So the cost/bit decline slowed from ~33-36%/yr to ~12-15%/yr, roughly 2.5-3x slower.**
- SemiAnalysis, "The Memory Wall: Past, Present, and Future of DRAM" (2024) [secondary-analysis] https://newsletter.semianalysis.com/p/the-memory-wall
  - Density: ~100x per decade historically (2x / 18 months), versus **only ~2x in the last decade**.
  - Price per bit: 3 orders of magnitude over 20 years historically, versus ~1 order of magnitude per decade recently.
  - Logic gains 30-40% density every 2 years, while DRAM needs ~10 years for 2x.
  - 16Gb dies were introduced ~8 years before 2024 and are still the volume part; they cost ~$3/GB at launch, peaked near $5, and were back to ~$3.
  - "HBM costs 3x or more per GB than standard DDR5."
- TechInsights die bit-density data points [snippet; TechInsights blog pages]:

  | Die | Bit density (Gb/mm²) |
  |-----|----------------------|
  | Micron 16Gb D1z DDR4 | 0.247 |
  | Micron 16Gb D1z DDR5 | 0.241 |
  | Samsung 8Gb D1z DDR4 | 0.299 |
  | SK hynix 16Gb D1z DDR4 | 0.296 |
  | Micron D1α 16Gb | 0.315 |
  | Samsung D1b 16Gb LPDDR5X | 0.436 |

  So it took several node steps (1z → 1a → 1b, ~2019-2023) to gain ~1.5x density. The TechInsights Q2-2024 roadmap expects D1c in ~2025, D1d as the "final 10nm-class" node in ~2026-27, and 0a/0b/0c single-digit nodes plus 3D DRAM (VCT, VS-CAT) toward 2030. https://www.techinsights.com/blog/dram-memory-technology-roadmap-update-q2-2024
- **Latency did not scale either.** Over an 11-year interval in which DRAM cost/bit fell 16x, tRCD fell only 30.5% and tRC only 26.3% (K. Chang, CMU PhD thesis, arXiv 1604.08041, 2016) [primary]. In other words, DRAM never "bought" latency; it bought cost/bit, and that is now slowing too.
- **The economic consequence: DRAM's share of server cost has risen.**
  - Meta (TPP, ASPLOS'23, Fig. 3) [primary, read from the figure]: memory as a share of rack TCO went **15.6% (Gen0) → 25.5 → 21.6 → 21.2 → 31.8 → 37.1% (Gen5)**. Memory share of rack power went **14.6% → 33.3%**.
  - Azure (Pond, ASPLOS'23): "DRAM can be 50% of server cost" [primary, citing their ref 16]. Levis et al. (HotNets'23) quote 40% for Meta and 50% for Azure [primary].
  - GreenSKU (ISCA'24, CMU/Microsoft/UW) [primary], for an AMD Genoa server: among compute-server emissions DRAM is the **largest contributor at 35%**, ahead of SSD 28% and CPU 24%. Embodied carbon is DRAM- and SSD-dominated (12 DIMMs). Their remedy is to **reuse old DRAM via CXL**.
- **The 2025-26 shock reinforces the trend** [primary-news].
  - The Register, 2025-12-20, quoting TechInsights' James Sanders: memory pricing +88% in 2024, roughly 3x within a few months of late 2025, "wafers are being redirected toward HBM", and a peak expected in 2026. https://www.theregister.com/2025/12/20/memory_prices_dram/
  - Micron, earnings call 2024-03-21 and FQ1-2026 prepared remarks: **HBM3E uses ~3x the wafer supply per bit of DDR5**, "and this trade ratio only increases with future generations of HBM" [secondary/snippet of Micron statements; widely reported, e.g. Tom's Hardware on Hot Chips 2026].
  - So the A-direction (HBM) now directly raises the cost of baseline DDR bits, which strengthens the B-direction incentive to utilize bits better.

**Push.**
- **Mainly B.** If bits stop getting cheap, the lever is utilization: pooling against stranding, tiering cold data to cheaper media, reusing old DIMMs.
  - Pond: 25% of DRAM stranded at p95, up to 30% in outliers. ~50% of VMs touch <50% of their rented memory. Pooling across 8-16 sockets cuts DRAM by 7-9%.
- **Also A, indirectly.** Bandwidth can no longer be bought "for free" via more capacity (TPP: "limited bandwidth vs. capacity points per DRAM generation... forces higher memory capacity in order to get more bandwidth"). Decoupling bandwidth from capacity is what HBM and PIM do on one side and CXL bandwidth expansion does on the other.

---

## 2. Core count vs memory channels, pins and DIMMs-per-channel

**Bandwidth per core across flagship x86 server parts** [derived: channels × MT/s × 8 B ÷ cores; specs are vendor ark/launch values. Recalled specs: E5-2690 (verified via Intel ark snippet), 9965 / 6980P / 6780E / Venice (verified via search results). Others recalled, **[unverified]** individually]:

| Year | Part | Cores | Ch × MT/s | Peak BW (GB/s) | GB/s/core | Cores/ch |
|---|---|---|---|---|---|---|
| 2012 | Xeon E5-2690 (SNB-EP) | 8 | 4 × DDR3-1600 | 51.2 | **6.4** | 2 |
| 2017 | Xeon Platinum 8180 | 28 | 6 × DDR4-2666 | 128 | 4.6 | 4.7 |
| 2017 | EPYC 7601 | 32 | 8 × DDR4-2666 | 171 | 5.3 | 4 |
| 2019 | EPYC 7742 | 64 | 8 × DDR4-3200 | 205 | 3.2 | 8 |
| 2021 | Xeon 8380 | 40 | 8 × DDR4-3200 | 205 | 5.1 | 5 |
| 2022 | EPYC 9654 (Genoa) | 96 | 12 × DDR5-4800 | 461 | 4.8 | 8 |
| 2023 | Xeon 8490H (SPR) | 60 | 8 × DDR5-4800 | 307 | 5.1 | 7.5 |
| 2023 | EPYC 9754 (Bergamo) | 128 | 12 × DDR5-4800 | 461 | **3.6** | 10.7 |
| 2024 | Xeon 6780E (Sierra Forest) | 144 | 8 × DDR5-6400 | 410 | **2.8** | 18 |
| 2024 | Xeon 6980P (Granite Rapids-AP) | 128 | 12 × DDR5-6400 (MRDIMM-8800) | 614 (845) | 4.8 (6.6) | 10.7 |
| 2024 | EPYC 9965 (Turin dense) | 192 | 12 × DDR5-6000 | 576 | **3.0** | 16 |
| 2026 | EPYC Venice (Zen 6) | 256 | 16 × DDR5-8000 RDIMM (MRDIMM-12800) | 1024 (1638) | 4.0 (6.4) | 16 |

- COAXIAL (SC'24) independently states Sierra Forest at 2.8 GB/s/core and Bergamo at 3.6, and gives "4-12 cores per memory channel" [primary].
- **Interpretation (my synthesis).** For performance-core flagships, GB/s per core has stayed roughly flat at ~4.5-6.5 since 2012. It has *not* collapsed. It collapsed only for dense/E-core parts (~3). The flat line was bought at a price:
  1. **Channels went 4 → 16**, which costs package pins and board area.
  2. **Per-pin rate went 1600 → 12800 MT/s**, which costs signal integrity and DIMMs-per-channel.
  3. **New module types** (MRDIMM: a mux buffer on the DIMM, already a step toward a buffered/serial architecture).
- Per-channel peak BW: DDR3-1600 12.8 GB/s → MRDIMM-12800 102.4 GB/s, which is 8x in 14 years ≈ **1.6x per 2 years**. That matches Gholami's DRAM-BW rate.
- Cores per socket: 8 → 256, which is 32x in 14 years ≈ 1.64x per 2 years. The two rates are about equal, so **all of the "extra" came from adding channels (4x)**, i.e., from pins.

**Pins.**
- COAXIAL [primary]:
  - A DDR4/DDR5 DIMM interface is 288 pins wide, with **160+ processor-side pins** per ECC DDR4 channel ("likely more for DDR5").
  - "The pin-count on processor packages has only been doubling about every six years" (their ref 55).
  - **PCIe5 vs DDR5-4800 bandwidth per pin is 4x**, "may grow to 8x by 2025". Example: a DDR5-4800 channel uses 160 pins for 38.4 GB/s. A ×8 PCIe5 link uses 20% of those pins (32) for 32 GB/s *per direction*.
- Das Sharma, Blankenship, Berger, "An Introduction to the CXL Interconnect" (arXiv 2306.11227, 2023/24) [primary]: "DDR5-6400 offers 50 GB/s with ~200 signal-pins". They also write "a x16 Gen5 PCIe port at 32 GT/s offers 256 GB/s with 64 signal pins".
  - **Caution:** by arithmetic, ×16 at 32 GT/s is 64 GB/s per direction, or 128 GB/s bidirectional. The 256 GB/s figure appears to be 2x high, or to be a Gen6 number. [derived]
  - Even using 128 GB/s / 64 pins = 2 GB/s/pin against 0.25 GB/s/pin for DDR5-6400, **the serial link is ~8x better per pin** counting both directions, or ~4x per direction.
- Socket pin counts (the socket names are the pin counts) [unverified dates, recalled]:
  - Intel: LGA2011 (2011) → LGA3647 (2017) → LGA4677 (2023) → LGA7529 (2024). That is 3.7x in ~13 yr, i.e., doubling every ~7 yr, consistent with COAXIAL's "~6 years".
  - AMD: SP3 4094 (2017) → SP5 6096 (2022).
  - Over the same period cores went 8 → 128-288 (16-36x).
- IBM OMI (POWER10), as a precedent for serial memory [snippet: Electronic Design article and IBM Hot Chips summary seen only via search results]:
  - A DDR4-3200 channel uses "over 300 pins" for 25 GB/s; an OMI channel uses ~75 pins for the same.
  - POWER10: 16 × 8-lane OMI at 32 GT/s (≈1 TB/s raw). IBM claims "6x bandwidth per mm² compared to DDR4 signaling".

**DIMMs per channel (DPC): the capacity casualty of speed.**
- Historically DDR3 allowed up to 3DPC [unverified detail], DDR4 typically 2DPC, and DDR5 is designed around 1DPC at rated speed.
- Sapphire Rapids drops from DDR5-4800 at 1DPC to 4400 at 2DPC. Genoa launched 1DPC-only; early 2DPC ran ~DDR5-3600 to 4000 (−16.7%).
- ServeTheHome (2023-04-07) [primary-news]: a 12-channel 2DPC dual-socket (48 DIMMs) does not fit the width of a 19" chassis. Offset layouts add 5.5-6" of depth (+16-18%). They name CXL / E3 memory as the way out. https://www.servethehome.com/why-2-dimms-per-channel-will-matter-less-in-servers/
- TPP (ASPLOS'23) [primary]: today's memory subsystem is "completely dependent on the underlying memory technology support in the CPUs":
  - (a) a controller supports a single DRAM generation;
  - (b) capacity comes in power-of-two granularity;
  - (c) there are limited BW-vs-capacity points per generation.
  - "Such tight coupling ... leads to stranded compute, network, and/or memory resources."

**Push: BOTH.**
- **Bandwidth per pin and beachfront → A.** Put the DRAM where pins are cheap: in-package HBM or on-package LPDDR5X with massive parallelism. Or don't move the data at all (PIM).
- **Bandwidth per pin → B as well.** Serial CXL gives 4-8x BW per pin. COAXIAL argues for replacing *all* DDR with CXL for bandwidth, not capacity.
- **Capacity per socket, lost to 1DPC and board area → B.** CXL expanders and E3 memory add capacity off the DDR bus. Pooling fixes the fixed core:memory ratio.

---

## 3. Energy per bit across interfaces

Primary anchors:
- **Horowitz, ISSCC 2014 plenary** [primary]:
  - "energy cost of a DRAM access (1 to 2 nJ)" for 64 bits, "a couple of orders-of-magnitude higher than ... an internal cache access or functional operation (10 pJ)".
  - DRAM I/O "takes over 20 pJ/bit" plus static power.
  - "Even when the I/O is improved, the energy cost of a DRAM access will still be large (10 pJ/bit, 0.6 nJ/8B)".
  - His Fig. 1.1.9 (45 nm) is the source of the widely quoted 64-bit cache read numbers: 8KB ≈ 10 pJ, 32KB ≈ 20 pJ, 1MB ≈ 100 pJ, DRAM ≈ 1.3-2.6 nJ. The figure text is garbled in the PDF, so these values are taken from the standard citation [unverified exact].
- **Dally, Hot Chips 2023** (citing Horowitz) [primary slides]:
  - 32b SRAM read (8KB) = 5 pJ, i.e., ~0.16 pJ/bit.
  - 32b DRAM read = 640 pJ, i.e., **20 pJ/bit**.
  - "LPDDR DRAM GB: 640 pJ/word; on-chip SRAM MB: 50 pJ/word; local SRAM KB: 5 pJ/word".
- **Keckler et al., "GPUs and the Future of Parallel Computing," IEEE Micro 2011** [primary], table for 2010 (45 nm):
  - DRAM interface energy **20-30 pJ/bit**, DRAM access energy **8-15 pJ/bit**.
  - Projection for 2017 (16 nm): 2 pJ/bit interface and 2.5 pJ/bit access.
  - Off-chip DRAM access is 200x a DFMA. At 20 pJ/bit, Fermi's 175 GB/s costs ~49 W, about 20% of card TDP.
  - **Hindsight:** the 2 pJ/bit interface target was met only in-package (HBM2 I/O 0.3 pJ/bit). Off-package DDR stayed ~20.
- **O'Connor et al., "Fine-Grained DRAM," MICRO 2017 (NVIDIA/UT)** [primary]:
  - **HBM2 ≈ 3.92-3.97 pJ/bit = activation 1.21 + on-die data movement 2.24 + I/O over interposer 0.3** (including ECC).
  - **GDDR5 ≈ 14.0 pJ/bit.**
  - Within a 60 W DRAM budget, HBM2 caps at ~2 TB/s. 4 TB/s would need >120 W, so future DRAM needs ~2 pJ/bit. Their FGDRAM reaches 1.95 pJ/bit. HMC is quoted at 10 pJ/b.
  - **Key point: once the DRAM is next to the logic, I/O is <10% of access energy. The remaining ~90% is inside the DRAM die (bank to I/O wires, activation).** The next step is therefore to change the DRAM internals or compute at the banks, which is the PIM argument.
- **DDR4 ≈ 16.5-21 pJ/bit, full DRAM-side dynamic access** (activation/precharge + on-chip datapath + DRAM output I/O; excludes static/refresh, host PHY and buffers). Lee et al., *IEEE Access* 2018, Fig. 2a (≈19-20 read off: ≈9 on-chip + ≈5 off-chip I/O + ≈5 ACT/PRE at δ = 0.27) and Table 3 (derived 20.7 for manufacturer A, 16.5 for B) [primary; see D1]. Sun et al. MICRO'23 quote it as 22 pJ/bit [secondary reading]. **DRAM-side I/O alone is only ≈5 pJ/b.**
  - Sun et al. contrast this with PCIe 4.0 ≈ 6 pJ/bit (citing a SemiWiki article on Samsung PCIe SerDes IP) [secondary].
  - They also note: PCIe4 16 Gb/s/lane vs DDR4-3200 3.2 Gb/s/pin, and link latency PCIe4 ~40 ns vs DDR4 <1 ns.
- **LPDDR5X vs DDR5** [vendor claim]: NVIDIA Grace whitepaper / tech blog. The Grace LPDDR5X subsystem gives "53% more bandwidth at one-eighth the power per gigabyte per second" vs 8-channel DDR5, at "similar cost". HBM2e would be ">3x the cost-per-gigabyte and only one-eighth the maximum capacity". https://developer.nvidia.com/blog/nvidia-grace-cpu-superchip-architecture-in-depth
  - Caveat: this compares on-package LPDDR (mm-scale traces, no RCD/DB) against RDIMMs. Much of the 8x is *distance and buffering*, not LPDDR versus DDR protocol.
- **Hybrid-bonded stacked DRAM (UniIC SeDRAM): 0.88 pJ/b (IEDM'20) and 0.66 pJ/b (VLSI'23), INTERFACE energy only.** The interface is array die ↔ the DRAM's own logic die: 110,000 vias/mm² at 3 µm pitch, 135-136 GB/s per Gbit or per chip [primary; see D2].
  - Like-for-like comparison: the HBM interface is ~1.5 pJ/b including PHY (IEDM'20 Table 1) or 0.3 wire-only (FGDRAM). No full-access number is reported.
  - The "77-83% reduction vs HBM3" uses normalized a.u. of undefined scope (implied HBM3 ≈ 3.9). **Not like-for-like with full-access figures.**
- **UCIe die-to-die:** 0.5 pJ/b (standard package), 0.25 pJ/b (advanced package), bandwidth density up to 1.35 TB/s/mm² at 45 µm bump pitch [snippet of spec/Nature Electronics 2024 paper; unverified].
- **PCIe5/CXL SerDes:** Intel 10 nm PCIe Gen5 at 37 dB channel loss: 365 mW/lane including PLL = **11.4 pJ/b** [primary; D3]. Short-reach or newer designs are ~4-7 pJ/b (comparison table in the same paper; Sun et al.'s PCIe4 ≈6 [secondary]).
  - **CXL memory access energy [derived]** = host SerDes + device SerDes + CXL controller + the device-side DDR access. Per link traversal: ≈6-23 pJ/b (the upper end is for read-dominant one-way traffic). Direct-attach total ≈22-44 pJ/b; switched ≈28-67 pJ/b; controller and switch core not included. **CXL is strictly energy-additive per access.** Its energy case is system-level: fewer DIMMs provisioned, less idle and refresh power, reuse of DDR4 (embodied carbon, per GreenSKU).
- **RDMA far memory [derived, unverified]:** NIC + switch + optics/DAC at both ends plus DRAM at both ends. This is plausibly tens of pJ/bit, but I found no reliable per-bit measurement. Do not cite as a number.

---

## 4. Latency ladder (measured where possible)

| Tier | Unloaded latency | Source and notes |
|---|---|---|
| On-die SRAM (L1-L3) | ~1-40 ns; Grace L3 >38 ns | TPP Fig. 2 "Cache 1-40ns" [primary]; Chips and Cheese, Grace Hopper [primary-measurement] |
| DRAM device core | ~50 ns worst-case unloaded at the DRAM | COAXIAL [primary]. tRCD/tRC barely improved (Chang 2016) |
| Local DDR (DDR4/DDR5) | 78 ns (Skylake DDR4), 115 ns (EPYC 7452), ~85 ns (Genoa, Pond Fig. 7 assumption), 80-140 ns range (TPP), ~100 ns (TPP Fig. 5) | Pond [primary]. **Loaded:** DDR5-4800 at 60% utilization = **160 ns average, 285 ns p90** (COAXIAL Fig. 2a); latency 3-4x at 50-60% load |
| Remote NUMA | 142 ns (Skylake 2S), 255 ns (EPYC 7452), ~180 ns (TPP), 193 ns (Melody config) | Pond, TPP, Melody [primary]. Pond: CXL "comparable to a CPU-CPU coherent link" |
| On-package LPDDR5X (Grace) | >200 ns | Chips and Cheese [primary-measurement]. Physically closer, yet *slower* than DDR5 RDIMM on x86 |
| HBM3 from GPU (pointer chase) | <300 ns (GH200), ~330 ns (discrete H100) | Chips and Cheese [primary-measurement]. **Tight coupling buys BW and energy, not latency.** Grace CPU → HBM3 over NVLink-C2C ≈ 800 ns |
| 3D DRAM-on-logic | no public server-class measurement found | — |
| CXL direct-attach (Type-3) | **Estimates:** 170 ns (Das Sharma Table 2: CPU load-to-use <100 ns + 2×21-25 ns ports + flight); Pond +70-90 ns over local (155-180 ns, 182-212%). **Measured:** 214-394 ns across 4 devices (Melody, ASPLOS'25, SPR/EMR, Intel MLC); tails p99.99-p99.999 >700 ns to ~1 µs. Sun et al. MICRO'23: CXL-A ~1.35x DDR5-remote, others ~2x and 3x | Port round trip 21-25 ns each side; retimer ~10-20 ns. Measured ASIC devices sit well above the 170 ns spec-based estimate |
| CXL via switch / pooled | **Estimates:** 250 ns (Das Sharma: <100 + 70 switch and flight + <80 device); switch adder 62-70 ns per hop; Pond/IEEE Micro: 8-16 socket MHD pools 155-180 ns, rack-scale 64-socket 270-345 ns, multi-rack >395 ns. **Measured:** 220.5 ns idle, 47.2 GB/s (MemChannel, NSDI'26, Emerald Rapids + switched pool, Intel MLC); **230 → 1,624 ns** under cross-host contention; Melody cites ~600 ns for CXL+switch | [primary] |
| RDMA far memory (page granularity) | 4 KB RDMA read = 3.9 µs average (Fastswap testbed, ConnectX-3 40 Gb); Fastswap remote page access <5 µs; TPP: network-attached disaggregated memory 2-4 µs. Gao et al. OSDI'16: 3-5 µs end-to-end network latency *needed* to keep apps within 5%, and endpoint software is 66-81% of the latency | [primary]. Add page-fault/kernel overhead of several µs (Fastswap) |
| NVMe SSD | 10-40 µs (TPP Fig. 2); ~50 µs typical 4 KB random read (Micron 4600 spec) | TPP [primary]; Micron [snippet] |

Latency rules of thumb, with sources:
- **Serial link adder ≈ 50-70 ns per CXL hop.** Das Sharma: 57 ns end-to-end; spec target 80 ns pin-to-pin. COAXIAL: ~50 ns. Intel: 25 ns per port round trip.
- **Each switch level adds ≈ 60-90 ns.**
- **Sensitivity** (Pond, 158 workloads, +182% latency): 26% of workloads see <1% slowdown, 17% see <5%, and ~21% see >25%. At +222%, 37% of workloads see >25%.

---

## 5. The memory wall: 1995 and the AI-era version

- **Wulf & McKee, "Hitting the Memory Wall: Implications of the Obvious," SIGARCH CAN 23(1), March 1995** [primary, read via the reprint in McKee CF'04]. Assumptions: DRAM speed +7%/yr; microprocessor performance +80%/yr (Baskett's estimate). Average cycles per memory access would then be 1.52 (2000), 8.25 (2005), and 98.8 (2010), so "the wall is less than a decade away". McKee 2004 restates processor +~75%/yr vs DRAM +7%/yr.
  - **This was a latency wall.** Caches, out-of-order execution and memory-level parallelism postponed it. Single-thread scaling then stalled (2004-05) and multicore converted it into a **bandwidth-per-core and capacity-per-core wall** (§2).
- **Gholami et al., "AI and Memory Wall," IEEE Micro 2024 (arXiv 2403.14123)** [primary, full text]:
  - Peak server hardware FLOPS **3.0x per 2 yrs**; **DRAM BW 1.6x per 2 yrs**; **interconnect BW 1.4x per 2 yrs**.
  - Over 20 years: FLOPS **60,000x**, DRAM BW **100x**, interconnect **30x**.
  - FLOPS are normalized to the R10000 used for LeNet-5 in 1998.
  - Transformer parameters grew **410x per 2 yrs** and training compute **750x per 2 yrs**, while **single-GPU memory scaled 2x per 2 yrs**.
  - Verified: the user's numbers are correct.
  - **Caveat:** the paper does not state which DRAM products (GDDR, HBM, DDR) form the 1.6x line, and the 410x rate is fitted mostly on the 2018-2022 Transformer scaling era.
- **Model size vs HBM capacity [derived, unverified product specs]:**
  - Per-GPU HBM went 80 GB (H100) → 141 GB (H200) → 192 GB (B200) → 288 GB (B300); recalled vendor specs.
  - A 405B-parameter model in BF16 needs ~810 GB for weights alone, i.e., more than one 8-GPU node of H100s before KV cache.
  - Capacity therefore forces either multi-GPU sharding over the 1.4x/2yr interconnect, or a second, cheaper tier. NVIDIA's own answer is both: GH200/GB200 put HBM *close* and 480 GB of LPDDR5X *farther*, behind NVLink-C2C at 900 GB/s (450 GB/s/dir; Chips and Cheese).
- **Bytes per FLOP on accelerators [unverified, recalled datasheet values]:** V100 ~125 TF FP16 tensor / 0.9 TB/s ≈ 139 FLOP/B; A100 312 TF BF16 / 2.0 TB/s ≈ 153; H100 SXM ~989 TF BF16 dense / 3.35 TB/s ≈ 295. The machine balance roughly doubled in two generations. LLM decode (GEMV, arithmetic intensity ~1-2 FLOP/B at batch 1) is therefore bandwidth-bound, which is the A-direction motivation (HBM, PIM; see Q1).

**Push.**
- The **bandwidth wall** pushes A.
- The **capacity wall** (parameters 410x vs memory 2x) pushes B: tiered or pooled memory, KV-cache offload to CXL or LPDDR.
- The **interconnect wall** (1.4x) works *against* naive B for bandwidth-hungry data, but favors B for capacity-hungry, low-intensity data.

---

## 6. Serial vs parallel memory interfaces

- **Parallel, source-synchronous DDR** is very low latency at the PHY (<1 ns; Sun et al.). But it is pin-hungry (160-200 signal pins per channel), reach-limited (one board, tight SI budget), and its rates are capped by multi-drop loading, hence 1DPC.
- **Serial SerDes (PCIe/CXL, OMI, earlier FB-DIMM)**:
  - 4-8x more bandwidth per pin; full duplex (Melody notes the DDR bus is half duplex).
  - Reach via retimers and cables (Das Sharma; Pond's 8-16-socket pools); media independence (the controller lives on the device, so DDR4 can be reused, per GreenSKU/Pond).
  - Costs: ~21-25 ns per port round trip (×2 per access), ~10-20 ns per retimer, SerDes power of ~4-11 pJ/b per transceiver lane (Intel PCIe5 10 nm: 365 mW/lane = 11.4 pJ/b [primary]), and a buffer/controller chip (Pond cost model: 2×8 CXL controller ≈ 0.4% of server BOM, 8-headed ≈ 0.8%, 16-port switch ≈ 1.6%).
- **History:** FB-DIMM (DDR2 era, AMB buffer) failed largely on AMB power and latency [unverified, no source fetched]. IBM Centaur (POWER8/9) and OMI (POWER10) succeeded in a vertically integrated system. CXL is the first *industry-standard* serial memory attach. Note also that MRDIMM is a *parallel* bus with an on-DIMM data mux: an intermediate step.
- **The two ways out of the pin wall (my synthesis):**
  1. **Shorten the wire to millimetres and go massively parallel.** HBM uses 1024 bits per stack (2048 in HBM4) on an interposer at 0.3 pJ/bit I/O (wire-only; ~1.5 including PHY). Hybrid bonding reaches 110k connections/mm² at 3 µm pitch, with a 0.66-0.88 pJ/b interface [primary: UniIC IEDM'20 / VLSI'23].
  2. **Lengthen the wire and serialize.** CXL: ×8 = 32 GB/s/dir on 32 pins, with metres of reach.
  - The DDR DIMM sits in the stranded middle. Its wires are too long and lossy for cheap massive parallelism, yet too short and parallel to get serial's per-pin efficiency or reach. **A and B are the two stable endpoints of the wire-length vs bits-per-pin trade-off.**

---

## 7. Consolidated tier table

Unloaded latency; energy per bit moved (pJ/bit) **with scope stated**; peak bandwidth per device or link; relative $/GB with DDR5 = 1.

Scope key:
- **FA** = full access on the memory side (activation + array/on-die datapath + memory-side I/O). Static and refresh power, the host controller and the host PHY are excluded unless stated.
- **IF** = interface/link only.
- **+** = additive to the memory's FA.

| Tier | Latency | Energy (pJ/bit, scope) | BW (typical unit) | $/GB (rel.) | Notes / sources |
|---|---|---|---|---|---|
| On-die SRAM (KB-MB) | 1-40 ns | FA: ~0.16 (8 KB) to ~1.6 (MB-scale), 45 nm figures | TB/s per core cluster (Grace L3 >2 TB/s) | ≫100 (area) [unverified] | Horowitz 2014; Dally HC'23; TPP |
| 3D DRAM-on-logic (hybrid bonded) | n/a (no server-class data) | IF only: 0.66 (VLSI'23) to 0.88 (IEDM'20). FA not reported; HBM2-like act + on-die terms (~3.45) are not addressed | 135-136 GB/s per Gbit or per 4 Gb chip (internal); 110k vias/mm² | n/a | UniIC IEDM'20, VLSI'23 [primary]. The "logic" is the DRAM's own periphery die |
| HBM2 / HBM3(E) | ~300-330 ns from GPU (incl. GPU hierarchy) | FA: 3.92 (HBM2: 1.21 act + 2.24 on-die + 0.3 IF, wire-only). IF incl. PHY ~1.5 (per IEDM'20) | 256 GB/s (HBM2) to ~1.2 TB/s (HBM3E) per stack [HBM3E unverified] | ≥3 (SemiAnalysis; NVIDIA HBM2e >3x LPDDR5X); ~3x wafer per bit (Micron) | FGDRAM MICRO'17; Chips and Cheese |
| GDDR5 (reference) | — | FA: 14 | — | — | FGDRAM |
| LPDDR5X on-package | >200 ns (Grace) | System-level, vendor: 1/8 of DDR5 per GB/s [vendor]. LPDDR4 device FA ≈12 (Lee 2018) | 384-500+ GB/s per CPU | ≈1 ("similar cost") | NVIDIA Grace; Chips and Cheese |
| Local DDR4/DDR5 RDIMM | ~80-120 ns unloaded; 160 ns avg / 285 ns p90 at 60% load | FA (DRAM side): ≈16.5-21 for DDR4 (Lee 2018 [primary]), of which IF (DRAM-side I/O) ≈5. Dally 20 (32b read). Host PHY, RCD/DB and static excluded. Keckler 2010 "20-30 I/O" is DDR3-era, host+DRAM and incl. termination | 25.6 (DDR4-3200) to 51.2 GB/s (DDR5-6400) per channel; MRDIMM-12800 102 GB/s | 1 (~$3-5/GB pre-2025 for 16Gb dies; spiked 2025-26) | Pond, COAXIAL, Das Sharma |
| Remote NUMA DDR | 140-255 ns | DDR FA + inter-socket link (+, not quantified) | link-limited | 1 | Pond, TPP |
| CXL direct (Type-3) | 170 ns est.; 214-394 ns measured; tails to ~1 µs | DDR FA + link IF 6-23 ≈ 22-44 total, + controller [derived from Intel PCIe5 11.4 pJ/b per lane] | ×8 Gen5: 32 GB/s/dir; measured 18-52 GB/s | ~1 + controller (~0.4% BOM each); can reuse DDR4 (<1) | Melody, Sun, Das Sharma, Pond, GreenSKU |
| CXL switched / pooled | 250 ns est.; 220 ns measured idle; 270-345 ns rack-scale est.; 1.6 µs under contention | + a second link traversal (6-23) ≈ 28-67 total, + switch core [derived] | per-port ×8/×16 | pool saves 7-9% of DRAM, but switch ≈1.6% BOM plus infrastructure | Pond, IEEE Micro'23, MemChannel, Levis (critique) |
| RDMA far memory | 2-5 µs (4 KB page: 3.9 µs + fault overhead) | not quantified (NIC + switch + DRAM at both ends) [no source] | 40-400 Gb/s per NIC | ~1 (remote DRAM) but improves utilization | Fastswap, Gao, TPP |
| NVMe SSD | 10-50+ µs | n/a (per-bit not comparable) | ~7-14 GB/s per Gen4/5 ×4 [unverified] | ≪1 (~1/20-1/50 [unverified]) | TPP; Micron spec [snippet] |

---

## 8. Trend → direction map

| Trend (with number) | → A (closer) | → B (farther) | Mechanism |
|---|---|---|---|
| T1. DRAM $/bit decline slowed from ~33-36%/yr to ~12-15%/yr after ~2010; density ~2x per decade; DRAM 37% of Meta rack TCO, 50% of Azure server cost; 2025-26 price spike from HBM wafer diversion (3:1) | weak/indirect | **strong** | If bits are dear, raise utilization (de-strand 25%, pool, tier, reuse). A is indirectly helped because BW can't be bought via more capacity |
| T2. Cores 8 → 256 per socket vs channels 4 → 16, pins doubling ~6-7 yr; 1DPC at speed; P-core BW/core held ~5 GB/s only by spending pins; dense parts ~3 GB/s | **strong** (on-package BW: HBM on CPU, LPDDR5X, MRDIMM) | **strong** (CXL 4-8x BW/pin; capacity off the DDR bus; fixes the fixed core:GB ratio) | The pin/beachfront wall hits both BW and capacity |
| T3. Energy (full access): off-package DDR4 ≈16.5-21 pJ/b vs HBM2 3.9 (I/O just 0.3) vs SRAM ~0.2-1.6. Interface only: hybrid bond 0.66-0.88 vs HBM PHY ~1.5. CXL link adds ≈6-23 | **strong** (and within HBM, the leftover energy is in-DRAM, which argues for PIM) | negative per access (adds SerDes); positive system-wide (fewer idle DIMMs, DDR4 reuse, embodied carbon: DRAM 35% of server emissions) | Distance is energy |
| T4. DRAM latency stagnant (tRCD −30% while $/bit fell 16x); loaded latency dominated by queuing | neutral (HBM is not lower latency) | **enabler** (a +50-100 ns adder is comparable to NUMA; 26% of workloads <1% slowdown) | Latency stopped being the scaling axis, which made added latency tolerable |
| T5. AI memory wall: FLOPS 3.0x / DRAM BW 1.6x / interconnect 1.4x per 2 yr; parameters 410x vs GPU memory 2x per 2 yr | **strong** (BW wall: HBM, PIM for decode) | **strong** (capacity wall: LPDDR/CXL tiers, KV offload) | Two different walls, two answers. GH200 does both |
| T6. Serial vs parallel: serial 4-8x BW/pin plus reach, +50-70 ns per hop; parallel wins only at mm distance | yes: massive parallel at mm (interposer/bonding) | yes: serial at m (CXL/OMI) | DDR DIMM is the unstable midpoint |

---

## 9. Surprises and caveats

1. **Bandwidth per core on P-core flagships has not collapsed** (6.4 GB/s/core in 2012; 4.8-6.6 in 2024-26). It was maintained by quadrupling channels (pins), pushing to 1DPC, and adding MRDIMM muxing. So the squeeze shows up as **lost capacity per channel, board area, and platform cost**, more than as lost BW/core. Dense-core SKUs (2.8-3.6 GB/s/core) are where the BW/core decline is real.
2. **In HBM2, I/O is only 0.3 of 3.9 pJ/bit.** Moving DRAM next to logic has largely exhausted the interface lever. The remaining ~90% is activation plus intra-die wires, which is exactly what PIM and fine-grained DRAM attack. So A has a built-in "next step" logic.
3. **Physical proximity ≠ latency.** HBM from H100 is ~300-330 ns and on-package LPDDR5X from Grace is >200 ns, against ~80-120 ns for x86 local DDR5. A is a bandwidth/energy play. Conversely, B's 50-100 ns adder is in NUMA territory, because DRAM latency stopped improving anyway (Chang) and loaded latency is queuing-dominated (COAXIAL).
4. **Measured CXL is worse than the spec story.** The spec-based estimate is 170 ns, but four real devices measured 214-394 ns, with p99.99 tails of 0.7-1 µs. A switched pool degrades to 1.6 µs under cross-host contention (MemChannel). Pond's +70-90 ns was a projection.
5. **Das Sharma et al.'s "×16 Gen5 = 256 GB/s with 64 pins" looks 2x high** (arithmetic: 128 GB/s bidirectional). The per-pin argument holds regardless (~4x per direction).
6. **Keckler's 2011 projection** of 2 pJ/bit DRAM interface by 2017 came true only in-package. Off-package DDR4 full access stayed at ≈16.5-21 pJ/b, about 4-5x HBM2. Most of that gap is *in-die* (activation with 8 KB rank rows; long on-chip datapath), not I/O: DRAM-side I/O is ≈5 vs 0.3 (Lee 2018; FGDRAM). That gap is the energy case for A.
9. **(Round 4) Hybrid-bond energy claims are interface-only.** "0.66 pJ/b, 83% below HBM3" compares an interface number with an a.u. reference of undefined scope. Against a PHY-inclusive HBM interface (~1.5) the gain is ≈2x; against HBM2's full access it is not a valid comparison.
10. **(Round 4) CXL is roughly 1.3-2x local DDR energy per bit** at the device+link level (≈22-44 vs 16.5-21 pJ/b), with Intel's PCIe5 lane at 11.4 pJ/b. Its energy argument must be system-level (fewer provisioned DIMMs, DDR4 reuse), never per access.
7. **HBM (A) now makes baseline DRAM more expensive.** A ~3:1 wafer trade ratio diverts supply and raises DDR prices, which raises the payoff from B-style utilization improvements. The two directions are coupled economically, not just technically.
8. **Critique to carry forward to Q5:** Levis et al. (HotNets'23) argue that pool savings (7-9%) don't cover a parallel CXL network fabric, and that RAM savings are quantized by channel and DIMM sizes.

---

## Source list

- Wulf & McKee 1995, via McKee, "Reflections on the Memory Wall," CF'04: http://svmoore.pbworks.com/w/file/fetch/59055930/p162-mckee.pdf
- Gholami et al., "AI and Memory Wall," arXiv 2403.14123: https://arxiv.org/html/2403.14123v1
- Li et al. (Pond), ASPLOS'23: https://arxiv.org/pdf/2203.00241
- Berger et al., "Design Tradeoffs in CXL-Based Memory Pools for Public Cloud Platforms," IEEE Micro 2023: https://www.microsoft.com/en-us/research/wp-content/uploads/2023/06/2023-CXL-DesignTradeoffs-IEEE-Micro.pdf
- Maruf et al. (TPP), ASPLOS'23 (local copy in scratch; arXiv id 2206.02878 [unverified])
- Cho, Daglis et al. (COAXIAL), SC'24: https://faculty.cc.gatech.edu/~adaglis/files/papers/coaxial_sc24.pdf
- Das Sharma, Blankenship, Berger, "An Introduction to the CXL Interconnect": https://arxiv.org/pdf/2306.11227
- Sun et al., "Demystifying CXL Memory...," MICRO'23: https://arxiv.org/pdf/2303.15375
- Liu et al. (Melody), ASPLOS'25: https://people.cs.vt.edu/~jinshu/docs/papers/Melody_ASPLOS.pdf
- Guo, Shriver, Liu (MemChannel), NSDI'26: https://pages.cs.wisc.edu/~mgliu/papers/MemChannel-nsdi26.pdf
- O'Connor et al. (FGDRAM), MICRO'17: https://research.nvidia.com/sites/default/files/pubs/2017-10_Fine-Grained-DRAM:-Energy-Efficient/oconnor_and_chatterjee.micro2017.pdf
- Keckler et al., IEEE Micro 2011, "GPUs and the Future of Parallel Computing" (local copy)
- Horowitz, ISSCC 2014 plenary (local copy)
- Dally, Hot Chips 2023 keynote slides (local copy)
- Amaro et al. (Fastswap), EuroSys'20: https://amyousterhout.com/papers/cfm_eurosys20.pdf
- Gao et al., "Network Requirements for Resource Disaggregation," OSDI'16 (local copy)
- Wang et al. (GreenSKU), ISCA'24 (local copy)
- Levis et al., "A Case Against CXL Memory Pooling," HotNets'23: https://sing.stanford.edu/site/assets/publications/cxl-hotnets23.pdf
- K. Chang, PhD thesis 2016: https://arxiv.org/pdf/1604.08041
- Handy (Memory Guy): https://thememoryguy.com/dram-prices-hit-historic-low/
- AI Impacts: https://aiimpacts.org/trends-in-dram-price-per-gigabyte/
- SemiAnalysis: https://newsletter.semianalysis.com/p/the-memory-wall
- TechInsights roadmap: https://www.techinsights.com/blog/dram-memory-technology-roadmap-update-q2-2024
- The Register (TechInsights quote): https://www.theregister.com/2025/12/20/memory_prices_dram/
- NVIDIA Grace blog: https://developer.nvidia.com/blog/nvidia-grace-cpu-superchip-architecture-in-depth
- Chips and Cheese, Grace Hopper: https://chipsandcheese.com/p/grace-hopper-nvidias-halfway-apu
- ServeTheHome on DPC: https://www.servethehome.com/why-2-dimms-per-channel-will-matter-less-in-servers/
- Li et al., 3D-DRAM LLM evaluation infrastructure, arXiv 2604.08044 (secondary for hybrid-bonded DRAM)
- Electronic Design on OMI pins: https://www.electronicdesign.com/technologies/embedded-revolution/article/21808373/attacking-the-memory-bottleneck

---

## Round-4: corrections for SYNTHESIS_DRAFT.md v0.3

I did not edit the draft. Each entry gives the quote, the correction, and the source with page.

1. **§2 table, DDR5 row, energy cell.**
   - Quote: "~20 pJ I/O alone [P: Horowitz]; full-access scope [pend: Lee 2018]"
   - Correction: "≈16.5-21 pJ, full DRAM-side access (DDR4; ≈5 of it DRAM-side I/O; static, refresh, host PHY and RCD excluded) [P: Lee 2018]".
   - Horowitz's ">20 pJ/bit" is DDR3-era I/O including termination and static power (it cites Malladi ISCA'12). It is not "I/O alone" for current DDR.
   - Source: Lee et al., *IEEE Access* 6, Fig. 2a p. 31389; Table 3 p. 31395 (derived 20.7 / 16.5 pJ/b); Horowitz ISSCC'14, DRAM-energy paragraph (page not rechecked).
2. **§2 table, HBM3E row, energy cell.**
   - Quote: "3.92 pJ, full access (HBM2: 1.21 act + 2.24 on-die + 0.3 I/O)"
   - Correction: keep it, but label the 0.3 as *wire-only interface*. Add "interface incl. PHY ~1.5 [P: IEDM'20 Table 1]" so interface-only comparisons have a like-for-like anchor.
   - Source: O'Connor MICRO'17 §1, Fig. 1b; Bai IEDM'20 p. 6.6.4, Table 1.
3. **§2 table, CXL row, energy cell.**
   - Quote: "DDR + SerDes, ~+10–25 [D, est.]"
   - Correction: "DDR FA + link ≈6–23 pJ/b per traversal ⇒ ≈22–44 pJ/b direct, ≈28–67 switched, excluding controller and switch core [D from P: Intel PCIe5 SerDes 365 mW/lane = 11.4 pJ/b]".
   - Source: Bichan et al. (Intel), IEEE 2020, abstract and Fig. 10, p. 4.
4. **§2 table, LPDDR5X row.**
   - Quote: "vendor claims 1/8 of DDR5 per GB/s [V]"
   - Correction: keep it, and add "device-level LPDDR4 ≈12 vs DDR4 ≈20 pJ/b; LPDDR's I/O advantage is attributed to mm-scale, few-device buses [P: Lee 2018]". So most of the 8x is distance and buffering, not the protocol.
   - Source: Lee 2018 Fig. 2a p. 31389; text p. 31391.
5. **§2 "What the table shows", item 5.**
   - Quote: "hybrid bonding reaches ~10⁵ connections/mm² [S/pend]"
   - Correction: "110,000 vias/mm² at 3 µm pitch, ~2 µm connection length [P: UniIC IEDM'20, VLSI'23]".
   - If any energy is attached, it must read "interface 0.66–0.88 pJ/b (vs HBM interface ~1.5 incl. PHY)". It must never be set against HBM's 3.92 full access.
   - Source: IEDM'20 pp. 6.6.1–6.6.4 (Table 1); VLSI'23 T18-2, p. 1 and Table I.
6. **§4 2×2 table, A1 row ("3D-stacked"), and any future text claiming "77–83% less energy than HBM".**
   - Correction: the UniIC figures are interface-only. VLSI'23 gives the HBM comparison only in normalized a.u. of undefined scope (GDDR6 100%, HBM2E 80%, HBM3 53%, this 9%; implied HBM3 ≈3.9 pJ/b). No full-access energy for 3D DRAM-on-logic exists in these sources.
   - Source: VLSI'23 Table I.
7. **§4 A2 row.**
   - Quote: "HBM's 2.24 pJ/b on-die term is untouched by any link [P]"
   - Correction: none needed. It is reinforced: even hybrid bonding (0.66–0.88 interface) leaves the activation and on-die terms (≈3.45 of 3.92) in place. A parallel DDR4 datum also exists: ≈9 on-chip + ≈5 ACT of ≈20 [P: Lee 2018 Fig. 2a].
8. **§6, item 3.**
   - Quote: "2.5D/3D and hybrid bonding remove the DRAM-process logic penalty, at least for base-die logic."
   - Correction: add the primary evidence: SeDRAM bonds a 25 nm DRAM-process array die to a 55 nm logic-process die [P: IEDM'20 Fig. 8].
   - Caveat: the logic die there is DRAM periphery, not compute.
9. **§0 point 2(i) and §8.**
   - Quote: "Shorter, wider wires (A1) improve bandwidth density and pJ/bit together."
   - Correction: none needed, but add the scale. Full access is ≈4–5x lower for HBM2 than for DDR4 (3.9 vs ≈16.5–21 pJ/b, same DRAM-side scope), and most of the gap is in-die rather than I/O: I/O ≈5 vs 0.3; ACT plus on-chip datapath ≈14–20 vs 3.45.
   - Source: Lee 2018 Fig. 2a; O'Connor MICRO'17 Fig. 1b.
10. **§10 Evidence quality, pending list.**
    - Quote: "Lee 2018 and UniIC: energy scope"
    - Correction: move it to read in full [P]. Resolved round 4 (see D1, D2, D3 at the top of this file).
