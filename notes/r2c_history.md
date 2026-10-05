# R2c: Historical precedent. 1990s IRAM/PIM vs network RAM/DSM, and later waves

Agent: round-2 sub-agent C (history). Date: 2026-10-03.

Labels: [full text] = I read the primary source in full or the relevant sections of the full text. [TR version] = the full text is a tech-report or preprint version of the published paper. [secondary] = the claim comes via another paper or Wikipedia. [abstract only] = abstract or search-snippet level. [inference] = my synthesis, not stated by any source. [cross-ref qN] = sourced in an existing round-1 notes file.

---

## Round-4 deltas (2026-10-03)

The user supplied three full texts (fulltext/MANIFEST.md items 20, 26, 29). All three are now read in full. Changes to this file:

**D1. Ranganathan, "From Microprocessors to Nanostores," IEEE Computer 44(1):39–48, Jan 2011 [full text].** This resolves the earlier [pending] item.
- What he proposed is **near-data compute plus scale-out. It is not disaggregation.**
  - "The key property of nanostores is the colocation of processors with nonvolatile storage, eliminating many intervening levels of the storage hierarchy. All data is stored in a single-level nonvolatile memory datastore that replaces traditional disk and DRAM layers" (p. 43).
  - The nanostores are then "networked through onboard connectors to form a large-scale distributed system or cluster" (p. 43), as a "distributed shared-nothing system" (p. 44).
  - Architecturally this is IRAM plus NOW again: tight coupling per node, scale-out across nodes. It is not a memory pool.
- So **Ranganathan backed both directions across two papers**: memory blades in Lim ISCA'09 (disaggregation) and nanostores in 2011 (colocation).
  - The acknowledgments thank Kevin Lim, Trevor Mudge and Jichuan Chang (p. 47). That is the same HP Labs/Michigan group as the memory-blade paper.
  - The nanostores paper itself does **not** propose disaggregation, and it does not cite Lim'09.
- Relevant content:
  - In 2011 he already noted "a slowing of DRAM growth due to scaling challenges for charge-based memories" (p. 42). His answer was to replace DRAM with PCM or memristors. That bet failed (The Machine [cross-ref q5]).
  - Claimed nanostore access latency is "competitive with DRAM (within about a factor of two)" at "2 to 10 picojoules/bit compared to 24 picojoules/bit for DRAM" (p. 45).
  - He proposes a "hierarchy of computes" that backs up on-chip computation with more powerful compute elements (p. 43, Fig. 4, p. 45). This treats near and far compute as one design space.
  - He gives three designs: traditional, side-stacked and 3D-stacked nanostorage. Workloads with orders-of-magnitude hot/cold working-set differences "work well" on the traditional design (Fig. 5, p. 46).
  - The capacity-per-node problem recurs: "Given the smaller capacities of per-socket storage, the number of individual elements in the system increases dramatically. This can potentially increase the stress on the networking subsystem" (p. 45). This is the same failure mode as IRAM and the 1990s PIM chips (NDP'14 Top-10 #8).

**D2. Aguilera et al., "Memory disaggregation: why now and what are the challenges," SIGOPS OSR 57(1):38–44, 2023 [full text; page numbers from the PDF footers; refs on pp. 44–46].**
- **The "why now" argument is a two-requirement test drawn from earlier disaggregations** (mainframes→clusters, storage→SAN, power/cooling→blades/OCP, GPUs→Bitfusion/composable): "their success depended on two requirements: addressing a burning issue and being technically feasible" (abstract; §2.5, p. 40).
  - Their burning issue for memory: demand surging, per-server DIMM limits, strict population rules, and a three-vendor oligopoly. They quote "memory can constitute 50% of server cost [Pond] and 37% of total cost of ownership [TPP]" (p. 40).
  - Their feasible technology: RDMA/RoCE commoditised, plus CXL (p. 40–41).
- **History as they tell it:**
  - Disaggregation "originated with paging to remote memory in the early 1990s" (Felten & Zahorjan 1991).
  - "Full-fledged memory disaggregation was first proposed in [Lim ISCA'09], where a processor can issue loads and stores directly to disaggregated memory, without incurring page faults."
  - "Intel proposed a Rack Scale Architecture in 2013 ... the proposal did not succeed because it was based on the technology of silicon photonics, which has not yet prevailed" (p. 40).
- **Prediction:** "We anticipate CXL memory disaggregation to come soon after (say, in 3–5 years) following a similar trajectory as storage disaggregation: beginning with multi-ported memory connected to a few hosts (say, 2–8), followed by CXL switches ... and ultimately ... convergence of the network and memory fabric" (p. 41). As of 2026-10 this is within its window. Whether switched pooling has arrived: see Q5's Octopus/Levis evidence [cross-ref q5].
- **DSM lesson:** "cache coherence is extremely hard to scale. We thus believe cache-coherent disaggregated memory systems will be limited to a rack or a few racks of servers" (§5, p. 44). This matches Hennessy et al. 1999 on >64-processor DSM.
- **Does it frame near-memory compute as complementary? No.** The paper never mentions PIM, near-data processing or compute offload. The pool controller's role is limited to address translation ("the pool controller will map physical addresses to pool addresses", p. 42) and isolation/encryption mechanisms (p. 43). Its vision is OS-level: transparent versus targeted allocation, tiering "akin to NUMA migration", shared kernel state and failures. **It cannot be cited for A/B complementarity.** Its silence is weak evidence that the systems community treats B independently of A [inference].
- Also: they expect "the pool can be provisioned with older DDR3 and DDR4 memory" (p. 41). This is the commodity-reuse argument (cf. §3.5).

**D3. Li & Hudak, "Memory Coherence in Shared Virtual Memory Systems" (IVY), ACM TOCS 7(4):321–359, Nov 1989 [full text].** Software-DSM claims below are upgraded from [secondary] to [full text] where IVY speaks directly.
- **The first software DSM was motivated by the same aggregate-capacity-versus-disk argument as NOW and GMS.** IVY got *super-linear* speedup on a 3-D PDE because "the data structure for the problem is greater than the size of physical memory on a single processor, so when the program is run on one processor there is a large amount of paging between the physical memory and disk ... the shared virtual memory can indeed exploit the combined physical memories" (p. 344).
- **Where it fails is exactly where near-data compute wins** [inference from p. 346, 349]. For the dot-product, "If the two vectors are located on one processor, there is no speedup at all ... because the ratio of the communication cost to the computation cost in this program is large. For programs like dot-product, a shared virtual memory system is not likely to provide significant speedup" (p. 346). The conclusion conjectures poor performance for "frequent updates to shared data or ... excessively large data sets that are only read once (such as the dot-product example)" (p. 349).
  - Low-reuse streaming data is the case that moving memory *away* (B) handles worst and moving compute *to* memory (A) handles best. The 1989 paper already states the workload boundary between A and B (cf. Q4).
- Granularity: "a page size of about 1K bytes is suitable with respect to contention" (p. 326). They had experience only with 1 KB and 32 KB pages, and only up to 8 processors on an Apollo ring (p. 349). So the scalability claim was untested. The later failure of software DSM (Grappa's characterisation) is consistent with this.

**D4. Not provided, kept [secondary]:** Kim & Chen 1999, Kogge PIM history, FlexRAM, DIVA, TreadMarks, Comer & Griffioen.

**D5. SYNTHESIS_DRAFT v0.3 §6–§7 (and two linked lines): corrections recommended.** I did not edit the draft. See the table at the end of this file, "## Round-4: SYNTHESIS_DRAFT corrections".

---

## FULL TEXT NEEDED

None of the central claims below depends on a missing full text. Items 1, 3 and 6 (IVY part) were resolved in round 4.

| # | Citation | URL / locator | Dependent claim | Priority |
|---|----------|---------------|-----------------|----------|
| 1 | ~~Ranganathan, Nanostores, IEEE Computer 2011~~ | — | **RESOLVED R4 [full text]**, see D1 | — |
| 2 | Y.-B. Kim and T. W. Chen, "Assessing merged DRAM/Logic technology," Integration 27(2), 1999, pp. 179-194 | https://www.sciencedirect.com/science/article/abs/pii/S0167926099000061 | Quantitative logic-in-DRAM-process penalty: ~22% slower logic and up to 80% more area [abstract only]. The Mutlu primer cites it as reason #1 that 1990s PIM failed. The IRAM authors' own estimates (full text) are used as the main evidence instead. Not provided in R4, so it stays [abstract only]. | low |
| 3 | ~~Aguilera et al., SIGOPS OSR 2023~~ | — | **RESOLVED R4 [full text]**, see D2 | — |
| 4 | P. Kogge, "A Short History of PIM at Notre Dame," July 1999, www.cse.nd.edu/pim/projects.html (not in Wayback). Also Kogge's "forthcoming article" that his arXiv:2601.09002 bibliography supports. | n/a | Kogge's own explanation of why 1990s PIM did not commercialise. Not provided in R4. | low |
| 5 | FlexRAM (Kang et al., ICCD'99) and DIVA (Hall et al., SC'99) | not retrieved | No claim depends on them beyond their existence. Not provided in R4. | optional |
| 6 | IVY: **RESOLVED R4** (D3). TreadMarks (Amza et al., IEEE Computer 1996) and Comer & Griffioen (1990/91) remain unread. | — | Software DSM's later failure rests on Grappa ATC'15 [full text], Lim ISCA'09 [full text] and Aguilera'23 [full text]. Comer & Griffioen numbers come via Markatos'96 [secondary]. | optional |

---

## 0. Sources actually read (full text unless noted)

- Patterson, Anderson, Cardwell, Fromm, Keeton, Kozyrakis, Thomas, Yelick, "A Case for Intelligent RAM: IRAM," IEEE Micro 17(2), 1997. Preprint dated 2/10/97: http://iram.cs.berkeley.edu/papers/IRAM.micro.pdf [full text, preprint]
- Kozyrakis, Perissakis, Patterson, Anderson, Asanović et al., "Scalable Processors in the Billion-Transistor Era: IRAM," IEEE Computer, Sept 1997: http://iram.cs.berkeley.edu/papers/IRAM.computer.pdf [full text]
- Patterson et al., "Intelligent RAM (IRAM): the Industrial Setting, Applications, and Architectures," ICCD 1997 (citeseerx PDF) [full text]
- Fromm et al., "The Energy Efficiency of IRAM Architectures," ISCA 1997: https://www2.eecs.berkeley.edu/~yelick/papers/iram-isca97.pdf [full text]
- Patterson, "The Future of Microprocessors Embedded in Memory," talk slides, ~1998: https://people.eecs.berkeley.edu/~pattrsn/talks/Cadence.pdf [full text]
- Gebis et al., "VIRAM1: A Media-Oriented Vector Processor with Embedded DRAM," DAC 2004: http://csl.stanford.edu/~christos/publications/2004.dac.iram.pdf [full text]
- Anderson, Culler, Patterson and the NOW team, "A Case for NOW (Networks of Workstations)," IEEE Micro 15(1), Feb 1995. I read the 20-page Dec 9, 1994 version: https://homes.cs.washington.edu/~tom/pubs/caseNOW.ps [TR version, full text; extracted from PostScript]
- Feeley, Morgan, Pighin, Karlin, Levy, Thekkath, "Implementing Global Memory Management in a Workstation Cluster," SOSP 1995. Wayback copy of http://www.cs.ubc.ca/~feeley/papers/1995-1.pdf [full text]
- Markatos & Dramitinos, "Implementation of a Reliable Remote Memory Pager," USENIX ATC 1996: https://www.usenix.org/legacy/publications/library/proceedings/sd96/full_papers/markatos.txt [full text]
- Flouris & Markatos, "The Network RamDisk," FORTH TR226 / Cluster Computing 1999: http://archvlsi.ics.forth.gr/OS/html_papers/TR226/nrd.html [abstract and TOC only]
- Oskin, Chong, Sherwood, "Active Pages: A Computation Model for Intelligent Memory," ISCA 1998. Wayback copy of http://cs.ucsb.edu/~chong/papers/AP-isca98.pdf [full text]
- Hennessy, Heinrich, Gupta, "Cache-Coherent Distributed Shared Memory: Perspectives on Its Development and Future Challenges," Proc. IEEE 87(3), 1999: http://csl.cs.ucf.edu/~heinrich/papers/Perspectives.pdf [full text, preprint dated June 22, 1998]
- Lim, Chang, Mudge, Ranganathan, Reinhardt, Wenisch, "Disaggregated Memory for Expansion and Sharing in Blade Servers," ISCA 2009: https://safari.ethz.ch/architecture/fall2021/lib/exe/fetch.php?media=isca09-disaggregate.pdf [full text]
- Balasubramonian, Chang, Manning, Moreno, Murphy, Nair, Swanson, "Near-Data Processing: Insights from a MICRO-46 Workshop," IEEE Micro 34(4), 2014: https://users.cs.utah.edu/~rajeev/pubs/ieeemicro14b.pdf [full text]
- Mutlu, Ghose, Gómez-Luna, Ausavarungnirun, Sadrosadati, Oliveira, "A Modern Primer on Processing-in-Memory," arXiv:2012.03112v5 (2025) [full text; history passages read]
- Kogge, "Annotated PIM Bibliography," arXiv:2601.09002 (Jan 2026) [full text]
- Myer & Sutherland, "On the Design of Display Processors," CACM 11(6), 1968: http://cva.stanford.edu/classes/cs99s/papers/myer-sutherland-design-of-display-processors.pdf [full text]
- Wulf & McKee 1995 (via Wayback of the UVa PDF) and McKee, "Reflections on the Memory Wall," CF 2004 (Wayback of the Cornell PDF) [full text]
- Nelson et al. (incl. M. Oskin), "Latency-Tolerant Software Distributed Shared Memory" (Grappa), USENIX ATC 2015 [full text; intro/related work]
- Gao et al., "Network Requirements for Resource Disaggregation," OSDI 2016 [full text; intro/results]
- Gu et al., "Efficient Memory Disaggregation with Infiniswap," NSDI 2017 [full text; abstract/intro/related work]
- Maruf & Chowdhury, "Memory Disaggregation: Advances and Open Challenges," arXiv:2305.03943 / SIGOPS OSR 2023 [full text; intro]
- Wikipedia "eDRAM" (product list) [secondary]

---

## 1. Q1: Did the same people hold both positions, and how did they reconcile them?

### 1.1 Yes. The overlap in authorship is literal and recurring

| Person | Tight-coupling paper | Far/shared-memory paper | Verified |
|---|---|---|---|
| David Patterson | IRAM (Micro'97, Computer'97, ICCD'97) | NOW (Micro'95) | both [full text] |
| Thomas E. Anderson | IRAM Micro'97 and Computer'97 co-author | NOW (Micro'95) first author | both [full text] |
| Mark Oskin | Active Pages (ISCA'98, PIM) | Grappa (ATC'15, software DSM over a cluster) | both [full text] |
| Jichuan Chang | NDP MICRO-46 report (IEEE Micro'14) | Disaggregated memory blades (ISCA'09) | both [full text] |
| Parthasarathy Ranganathan | "Nanostores": compute colocated with 3D-stacked NVM, networked shared-nothing (Computer'11) | Memory blades (ISCA'09) | both [full text] (R4). Nanostores is near-data compute plus scale-out, not disaggregation (see D1). |

[inference] The same people repeatedly backed both moves. This argues against reading A and B as rival camps. They are two answers to two different bottlenecks, and the same analysts saw both.

### 1.2 What each was optimising, relative to which baseline

**IRAM (tight coupling).** Baseline: the off-chip processor–DRAM bus.
- Latency. The AlphaServer 8200 main-memory latency was **253 ns versus ≈60 ns raw DRAM access**, "a factor of four larger than the raw DRAM access time". The overhead came from pins, address multiplexing, bus turnaround, controller and SIMM connectors. (IRAM Micro'97, Table 1) [full text]
- Bandwidth. The paper states: "A DRAM naturally has extraordinary internal bandwidth, essentially fetching the square root of its capacity each DRAM clock cycle." A gigabit IRAM's internal bandwidth was estimated at **100–200 GB/s versus 1.2 GB/s** sustained on the AlphaServer 8400. [full text]
- Headline claims: "a factor of 5 to 10 reduction in latency, a factor of 50 to 100 increase in bandwidth, a factor of 2 to 4 advantage in energy efficiency" (IRAM Micro'97, Conclusion). [full text]
- Energy per access (Fromm et al., ISCA'97, Table 5):
  - Off-chip main-memory access for an L1 line: **98.5 nJ**, versus **4.55 nJ** on-chip (≈22×).
  - Off-chip access for an L2 line: **316–318 nJ**. [full text]
- Die-area cost of hiding latency, which the paper calls the "Memory Gap Penalty". Caches and latency-hiding logic took 37–61% of die area and 77–94% of transistors (Alpha 21164, StrongARM, Pentium Pro). [full text]
- A capacity/granularity motive appears as well, and it is often forgotten. Because DRAM chip capacity grew faster than minimum PC memory, IRAM argued that "soon many PC customers may require only a single DRAM chip." Bus width then forced wasteful minimum memory increments (Figs. 2–4). [full text]

**NOW / network RAM / GMS (loose coupling).** Baseline: **disk**, not local DRAM.
- NOW: "On emerging switch-based local area networks ... the remote memory access time is an order of magnitude faster than disk." An 8 KB remote-memory fetch over ATM took **1,450 µs versus 16,250 µs** for remote disk; over Ethernet the figures were 6,900 µs versus 21,700 µs (NOW Table 2). Simulation result: "programs run 10% to 30% slower using network RAM than if the program fit entirely in local DRAM; using network RAM is 5 to 10 times faster than thrashing to disk." [TR version]
- The NOW cost argument was commodity volume. "In January 1994 the cost per MB of DRAM memory was $40 for a PC and $600 for the Cray M90 family, a price multiplier of 15." Gordon Bell's rule: "doubling the volume reduces the unit cost to 90%." [TR version]
- The NOW utilisation argument was idle memory. "Most engineering workstations have a huge amount of memory and very fast processors, both of which sit idle most of the time." "More than 60% of workstations are available 100% of the time" even in daytime (53-machine, 2-month trace). [TR version]
- GMS (SOSP'95):
  - "A local hit is over three orders of magnitude faster than a global memory or disk access, while a global memory hit is only two to ten times faster than a disk access."
  - Measured getpage hit was **1,440 µs** (non-shared 8 KB page, 155 Mb/s AN2 ATM, 225 MHz Alpha). Disk was **3,600–14,300 µs**. Speedup was **1.5–3.5×** on memory-intensive apps. [full text]
- Markatos & Dramitinos (USENIX'96): remote paging over 10 Mb/s Ethernet was "up to 96% faster execution" than paging to local disk. The lab's 16 workstations with 800 MB total always had more than 300 MB unused. The paper also concluded that "The benefits of paging to remote memory will only increase with time" because network bandwidth was outpacing disk. [full text]

**DSM / CC-NUMA (loose physical coupling, shared address space).** Baseline: the bus-based SMP, which "do[es] not scale", and message-passing MPPs, whose different programming model "has severely limited the success" of such machines (Hennessy, Heinrich, Gupta 1999). [full text]
- Remote/local latency ratios in commercial CC-NUMA, 32 processors, uncontended (Fig. 10):

  | System | Local (ns) | Remote, clean (ns) | Remote, dirty (ns) | Ratio (clean) |
  |---|---|---|---|---|
  | SGI Origin | 200 | 710 | 805 | ≈3.6× |
  | HP Exemplar | 450 | 1315 | 1955 | ≈2.9× |
  | HaL S-1 | 240 | 1065 | 1365 | ≈4.4× |
  | DG NUMALiine | 165 | 2400 | 3400 | ≈14.5× |

  The paper adds: "the ratio of remote to local access time has increased in several systems." [full text]

### 1.3 How they reconciled the two positions

1. **Different bottleneck, different level.** IRAM attacked the CPU↔DRAM bus inside a node: latency, bandwidth and energy per access. NOW/GMS attacked the DRAM↔disk gap across nodes: capacity overflow and idle-resource utilisation. The NOW paper even tells users to apply them in sequence: "First, avoid going to disk by using all the DRAM on the network. If ... still not fast enough, try using all the disks ... If it is still not fast enough, parallelize." [TR version]
2. **IRAM's own designers said tight coupling needs a far tier for capacity.**
   - Kozyrakis et al. 1997: "A more serious architectural consideration is the bounded amount of DRAM that can fit on a single IRAM chip ... A potential solution is to back up a single IRAM chip with commodity-external DRAM, using the off-chip memory as secondary storage with pages swapped between on-chip and off-chip memory. Alternatively, multiple IRAMs could be interconnected with a high-speed network." [full text]
   - IRAM Micro'97 lists "Scaling a system beyond a single IRAM" as disadvantage #4 and asks "What is the architectural and operating system solution for IRAM when applications need more memory than is found on-chip?" [full text]
   - So the 1997 IRAM vision was already a two-ended hierarchy: fast on-die DRAM plus paged far memory or networked nodes. [inference] Structurally this is GB200 (HBM plus LPDDR over C2C) or HBM plus CXL [cross-ref q5].
3. **IRAM plus NOW as one system.** The ICCD'97 paper proposes IRAM nodes with serial links into a switched cluster ("Scalable, Low-Cost, Data-Server Cluster"; "Low-Cost TeraFLOPS Cluster"). Compared with ASCI Red, the proposed 1000-IRAM cluster had **16–24 GB of memory versus ASCI Red's 600 GB**, with much higher I/O bandwidth. The authors write: "The sort benchmark was able to trade off higher I/O bandwidth for smaller memory. Whether this would be true for supercomputing remains to be seen." [full text] [inference] The reconciliation was explicit. Couple tightly for bandwidth per node, then scale capacity and throughput through the network. Its open risk was capacity per node.
4. **The capacity critique was made at the time, from inside the PIM camp.**
   - Active Pages (ISCA'98) argued against IRAM: "If IRAM chips are fabricated at processor costs, systems will be limited to a few IRAM chips and to applications with smaller data." Also: "IRAM technology ... is intended to compete with commodity processors ... but ever-growing applications may always demand larger memories and multiple chips." [full text]
   - Active Pages' answer was to keep the commodity CPU and a standard memory bus, put reconfigurable logic in DRAM, and build at DRAM cost and yield. "Processor chips cost ten times as much as memory chips."
5. **A 1968 precedent.** Myer & Sutherland already framed the placement question as "local memory vs shared memory".
   - "If the display must be located far from the main computer, then the problems of data transmission dictate that it have at least a local memory."
   - "A remote display with its own memory ... has unjustifiable disadvantages unless communication bandwidths force it."
   - Their conclusion was to couple closely to shared core. The "wheel of reincarnation" also comes from this paper: "The display processor starts simple and grows until it has become a full computer. Then it gives birth to a second processor which in turn begins to grow." [full text]
   - [inference] The modern analogue is that whether compute gets its own near memory is decided by link bandwidth relative to the access rate. That is the same criterion separating HBM (A) from far tiers (B).

---

## 2. Q2: What happened to each wave, and why?

### 2.1 IRAM / 1990s PIM (EXECUBE, Terasys, M32R/D, IRAM/VIRAM, Active Pages, FlexRAM, DIVA)

**What happened**
- EXECUBE (IBM Federal Systems, 1992): 4 Mb DRAM split into 8 partitions, each with a 16-bit core. Systems of up to 64 chips were built (Kogge bib) [full text].
- Terasys (1995): a 128 Kb SRAM PIM chip; "a half dozen SUN Sparcstation-2 workstations were constructed" [full text].
- Mitsubishi M32R/D (1997): 2 MB DRAM with a 32-bit RISC core for portable multimedia [full text, Kogge].
- DIVA: a "55-million transistor PIM chip ... demonstrated ... July 31, 2002" [full text, Kogge].
- VIRAM1: 0.18 µm, 6 metal layers, **125 M transistors, 325 mm², 13 MB DRAM, 2 W at 200 MHz, 9.6 GOPS (16-bit)**. Taped out late 2002, parts received summer 2003, built by "three full-time and three part-time graduate students" (DAC'04) [full text]. It never became a product.
- NDP report, 2014: "After a dormant decade". "Although PIM research yielded a number of promising results, widespread commercial adoption remained elusive." [full text]

**Why, in contemporaries' and retrospective words**
1. **Process mismatch.** IRAM's own estimate: "the area cost might be 30% to 70%, and the speed cost today might be 30% to 100%" for logic in a DRAM process (Micro'97). Merged processes added "10% to 30%" to wafer cost (ICCD'97). DRAM processes had fewer metal layers, plastic packages at under 2 W versus 20–50 W CPUs, and "refresh rates go up with operating temperature, approximately doubling for every 10 degrees C" [full text]. Kim & Chen 1999 give ~22% slower logic and up to 80% area [abstract only].
2. **Business model and commodity interchangeability.**
   - "Today's DRAMs are second sourced commodities that are interchangeable ... adding a processor would stratify IRAMs and effectively reduce interchangeability" (Micro'97).
   - "DRAMs are 'generic' parts, used in many places without impacting the software. Putting a processor in the DRAM limits the software that can run on the IRAM" (ICCD'97). [full text]
   - "DRAM companies ... generally have little experience in the microprocessor market" [full text].
3. **Yield and test.** "Microprocessors traditionally have no redundant logic ... the on-chip logic may effectively determine the yield of the IRAM." Testing on both logic and memory testers raised cost (ICCD'97) [full text].
4. **DRAM commoditisation and price collapse.**
   - "Between January 1996 and December 1996 the price of a 16 Mbit DRAM fell from about $40 per chip to $6 per chip ... overall DRAM sales fell from $16.5B in 3Q95 to $7B in 1Q97" (ICCD'97) [full text].
   - Patterson's slide (~1998): "Cost of 16 Mbit DRAM $1.78 in June 1998: How compete?" [full text]
   - [inference] Cheap commodity DRAM made "just buy more off-chip DRAM" the default, which undercut both IRAM's cost story and network RAM's capacity story (2.2).
5. **The general-purpose von Neumann machine outran PIM economically.** NDP'14 sidebar "Everything Old is New Again": "These machines did not fade away because of lack of performance gains or energy efficiency, but simply because Moore's law allowed the basic von Neumann machines to outrun them with economic affordability." [full text]
6. **Workloads of the day were cache-friendly.** IRAM's own Alpha estimate had SPEC92 running **1.2–1.8× slower** on IRAM, database 0.85–1.10×, and sparse Linpack **1.2–1.8× faster** (Micro'97, Table 5). [full text] Patterson's slide: "IRAM systems with existing architectures provide moderate performance benefits." Mutlu primer: "the lack of critical memory-related scaling challenges that current technology and applications face today", plus "the large effort expended to tolerate data movement bottlenecks (via processor-centric techniques)" [full text]. McKee (2004): "Many types of applications have not yet observed a memory wall problem" [full text].
7. **Programming model.** NDP'14: "exploitation of PIM required programmers to grapple with a new programming model" [full text]. IRAM Micro'97 notes that early PIM researchers "were lured by the promise of scalable multiprocessing ... [and] neglected the difficulty of programming such machines, especially when memory is limited" [full text].
8. **Capacity per device.** NDP'14 "Top 10 reasons": "early NDP designs were limited by small device capacities that forced too much fine-grained parallelism and interdevice data movement" [full text]. Patterson's slide: "Commercial IRAM highway is governed by memory per IRAM?" (graphics 2 MB → PDA 8 MB → laptop 32 MB) [full text].

**What did survive from the IRAM wave** [secondary/inference]
- Patterson predicted IRAM's "beachhead in graphics ... to the game, embedded" markets (Micro'97 conclusion) [full text]. Embedded DRAM did ship there: PlayStation 2 (4 MB framebuffer), Xbox 360 (10 MB), Wii U (32 MB). It also shipped as large caches: IBM POWER7 and z-series, Intel Haswell–Coffee Lake eDRAM 64–128 MB. (Wikipedia "eDRAM") [secondary]
- [inference] DRAM survived inside logic dies as a passive cache or framebuffer with no programming-model change, in markets where a single vendor controlled the software stack (consoles, IBM mainframes). This is the same "least-disruptive form survives" pattern Q5 found for HBM versus HMC [cross-ref q5].

### 2.2 Network RAM / remote paging / GMS (1990–1999)

**What happened.** Research prototypes worked: GMS 1.5–3.5×; Markatos up to 96% faster than disk; Network RamDisk "usually four to eight times faster than the magnetic disk" [abstract only, TR226]. The technique was **not adopted in mainstream OSes** [inference; I found no commercial adoption]. It returned 20 years later as RDMA paging (Infiniswap 2017) and later CXL.

**Why it faded**
1. **The baseline it beat was disk.** Remote memory in 1995 was "only two to ten times faster than a disk access" and >1000× slower than local DRAM (GMS) [full text]. [inference] Once a working set fit in local DRAM, network RAM gave nothing. Local DRAM per node kept growing as cost/bit fell ~33–36%/yr until about 2010 [cross-ref q3: AI Impacts, Memory Guy]. Over a decade that is roughly a 50× fall [inference: (0.66)^10 ≈ 0.016]. NOW itself notes that "To run a larger program, typically one needs to buy more DRAM" [TR version]. Cheap DRAM made that the dominant answer.
2. **It did not fix the aggregate imbalance.** Lim et al. 2009 on the 1990s line of work: "These approaches do not fundamentally address the compute-to-memory capacity imbalance: the total memory capacity relative to compute is unchanged when all the servers need maximum capacity at the same time." [full text]
3. **Invasiveness and reliability.** Lim'09 lists "changes to the host operating system and device drivers ... reduced reliability in the face of remote server crashes ... and/or impractical access latencies" [full text]. GMS kept only clean pages in global memory for crash safety. It also had to assume that "nodes trust each other", with encryption as the fallback, and it noted that "a single badly-behaving program on one node could cause enormous paging activity" [full text].
4. **Slow networks and CPU overheads.** Infiniswap (2017): earlier remote paging was "often limited by slow networks and high CPU overheads. Moreover, they rely on central coordination" [full text]. NOW measured ATM's overhead plus latency at 626 µs per message, worse than Ethernet's 456 µs despite 8× bandwidth [TR version]. Maruf & Chowdhury (2023): "the idea of leveraging remote machines' memory is decades old ... only during the past few years, the latency and bandwidth gaps between memory and communication technologies have come close enough to make it practical" [full text].
5. **The idle-workstation premise disappeared** [inference]. NOW/GMS harvested idle desktops in a building. The successor environment, dedicated server clusters, had no idle desktops. When the problem returned at hyperscale it came back as **stranding and peak-provisioning waste** in packed servers (Lim'09 sum-of-peaks; Pond/Azure [cross-ref q2]), not as idle desktops.

**What survived** [inference]. The application-level form of "use other nodes' DRAM": cooperative caching evolved into distributed in-memory caches and stores, and NOW evolved into commodity clusters. OS-transparent paging did not survive. This matches the 2020s pattern in which application-managed remote memory, such as LLM KV-cache pools (Mooncake [cross-ref q5]), gains ground faster than transparent pooling.

### 2.3 DSM: hardware CC-NUMA versus software DSM

- **Hardware CC-NUMA won at small scale.** Hennessy, Heinrich & Gupta (1998/99) predicted: "It is likely that cache-coherent DSM will soon be the dominant architectural approach for multiprocessors with as few as four or more processors." [full text] [inference/secondary] This came true. Multi-socket x86 servers are ccNUMA, and so is CXL.mem expansion, which is load/store, cache-line granularity and hardware-coherent on the host side.
- **Large hardware DSM (>64 CPUs) stayed niche**, which they also anticipated: "Economic issues will also play a key role in the future of very large DSM multiprocessors (above 64 processors). The key question ... is whether the market is significantly large to justify the engineering investment." [full text]
- **Software DSM failed.** Grappa (2015): "Historically, these systems performed poorly, largely due to limited inter-node bandwidth, high inter-node latency, and the design decision of piggybacking on the virtual memory system ... applications were only suitable ... if they exhibited significant locality, limited sharing and coarse-grain synchronization." [full text; secondary for 1990s systems] Lim'09: hardware DSM needs "specialized interconnects and non-commodity components", and software DSM has "practical limitations to functionality, generality, software transparency, total costs, and performance". [full text]
  - R4 [full text, IVY TOCS'89]: the founding software DSM was itself justified by aggregate capacity versus disk. It got super-linear speedup when a dataset exceeded one node's memory (p. 344). It conceded failure for low-reuse streaming (dot-product, "no speedup at all", p. 346) and for write-sharing (p. 349). Its scaling was untested beyond 8 processors (p. 349).
  - R4 [full text, Aguilera'23]: "cache coherence is extremely hard to scale ... limited to a rack or a few racks" (p. 44).

### 2.4 Blade-era disaggregation (Lim et al., ISCA'09)

- Motive: a capacity wall rather than disk. Cores were doubling every ~2 years while socket pins stayed constant. DIMM density growth was slowing to "2X every three years versus 2X every two years", and DIMMs per channel fell "two DIMMs per channel on DDR3 versus eight for DDR". The projection was that "memory capacity per core [would drop] by 30% every two years". Memory was "about 25%" of system cost and power and rising. [full text]
- **New baseline: worst-case-provisioned local DRAM (TCO), with disk only for the capacity-constrained case.**
  - "Up to 87%" perf/$ via sharing; "on average 10X" in memory-constrained settings versus 8 ms disk paging.
  - Provisioning at 70–85% of sum-of-peaks.
  - Modelled PCIe 2.0 x2 link: 120 ns, 1 GB/s, plus 330 ns VMM trap. [full text]
- Lim'09 named the tension with tight coupling explicitly: "3D die-stacking allows DRAM to be placed on-chip ... this approach further constrains the extensibility of memory capacity." [full text]
- Fate: not productised in that form [inference]. It anticipated Pond and CXL pooling [cross-ref q2]. Its PCIe-based design and "memory-side accelerators ... compression and encryption" option are what CXL Type-3 devices with compute later look like [cross-ref q5].

### 2.5 2010s waves: HMC logic layer, The Machine, Gen-Z [cross-ref q5, q2]

- The HMC serial, packetised interface with a logic layer died in 2018; Micron shifted to HBM/GDDR [cross-ref q5].
- NDP'14 cited HMC as the interface that "addresses both of these challenges by moving to an abstracted serialized network interface" [full text]. [inference] The 2014 bet that serial, abstracted memory interfaces plus NDP would win as a near-memory standard lost to passive, parallel HBM. A serial memory interface did win later, but on the far side: CXL on the PCIe PHY.
- HPE The Machine and Gen-Z did not reach products, and Gen-Z and OpenCAPI were folded into CXL in 2021–22 [cross-ref q5, q2].

### 2.6 Conditions that killed or enabled each wave (summary table)

| Wave | Enabling condition at the time | Killing condition | Survived as |
|---|---|---|---|
| IRAM/PIM (1992–2004) | Gigabit DRAM made "whole programs" fit; processor–memory gap of 50%/yr; DRAM vendors seeking margin after the 1996 crash | Merged-process penalty; commodity interchangeability lost; programming model; small capacity per device; cache-friendly workloads; cheap off-chip DRAM; general-purpose CPUs outran PIM | Passive eDRAM caches and framebuffers (consoles, POWER/z, Crystalwell) |
| Network RAM / GMS (1990–1999) | Switched LANs (ATM, Myrinet) made remote memory ~10× faster than disk; idle desktops | Beat only disk; DRAM cost/bit fell ~33–36%/yr, so buying local DRAM was cheaper; OS changes, trust and failure handling; idle-desktop premise gone | Application-level distributed caches; the NOW cluster model itself |
| Software DSM (1986–2000) | Clusters cheaper than SMPs | Latency and bandwidth; page-granular VM coupling; only coarse-grain workloads fit | Revived with RDMA (Grappa, FaRM) as niche |
| Hardware CC-NUMA (DASH 1991 → Origin 1996) | Bus SMPs did not scale; remote/local only ~3–4× | Above 64 CPUs, custom engineering versus cluster economics | Every multi-socket server; now also the CXL.mem host model |
| Blade disaggregation (2009) | Capacity-per-core wall; memory ~25% of cost | No standard fabric; PCIe latency; needed hypervisor changes | Idea reborn as CXL expansion and pooling |
| HMC / NDP 2.0 (2011–2018) | 3D stacking separated the logic and DRAM processes | Thermal limits, cost, ecosystem; HBM was "good enough" and passive | HBM (passive); logic base die now returning |

---

## 3. Q3: What is structurally different now, and does history predict survivors?

### 3.1 The far tier moved from "faster disk" to "slower DRAM"

- 1995 remote memory: 1,440 µs per 8 KB page, >1000× local DRAM and 2–10× faster than disk (GMS) [full text].
- 2009 PCIe blade model: 120 ns link plus 330 ns software trap per access (Lim) [full text].
- 2016 target: 3–5 µs and 40–100 Gb/s were needed to keep degradation low (Gao et al., OSDI'16) [full text].
- Today, CXL.mem expansion is roughly 2× local DRAM latency [cross-ref q2/q5; e.g., Levis 140→280 ns].
- [inference] **CXL.mem's remote/local ratio (~2×) is lower than SGI Origin's (~3.6×)** [full text for Origin]. Structurally, CXL expansion is a reincarnation of the CC-NUMA branch, which survived, not the network-RAM branch, which did not. RDMA far memory (µs, page- or object-granular, OS- or runtime-managed) is the network-RAM and software-DSM reincarnation. History predicts that branch stays application-specific.
- Infiniswap (2017) still reported gains **"over disk" (4–15.4×)**, the same baseline as 1995 [full text]. The CXL-era case (Pond, TPP) is argued against **local DRAM TCO and stranding** [cross-ref q2]. [inference] That change of baseline is itself a structural difference.

### 3.2 The capacity escape valve is gone, and this strengthens both A and B

- 1997 IRAM assumption: "historical trends indicate that the end-user demand for memory will scale at a lower rate than the available capacity per chip. So, over time a single IRAM chip will be sufficient for increasingly larger systems" (Computer'97) [full text]. The Micro'97 paper gives the evidence that minimum memory grew at "about half to three-quarters the rate of DRAM chip capacity" [full text].
- Now the reverse holds:
  - DRAM density is ~2× per decade versus ~100× historically, and cost/bit falls ~12–15%/yr versus 33–36%/yr [cross-ref q3].
  - AI model and KV-cache demand outgrows per-package capacity [cross-ref q4, q1: d-Matrix 1/6 capacity; HBM ~3× $/bit].
- [inference] In the 1990s, cheap and fast-scaling commodity DRAM was the common killer of both IRAM, which got outrun on cost, and network RAM, which got outrun on capacity. Today that killer is absent, so both directions get stronger at once. This is the strongest historical explanation for the "paradox".

### 3.3 Process decoupling through packaging

- 1990s PIM needed a merged logic+DRAM process, with 30–70% area and 30–100% speed penalties (IRAM) [full text].
- NDP'14 Top-10 #2: "3D and 2.5D die-stacking technologies have matured to enable the integration of computing and data stores ... without the previous disadvantages of merged logic+memory fabrication ... These two reasons make the strongest case for NDP." [full text]
- Today HBM base dies are moving to foundry logic processes and hybrid bonding [cross-ref q1].
- [inference] This removes kill condition #1 for A. It does not remove kill conditions #2 (interchangeability and software) or the thermal concerns in Q5.

### 3.4 An anchor workload with a vendor-controlled software stack

- NDP'14 Top-10 #9: "A commercially viable anchor market is critical" [full text]. In 1997 the anchors were games, PDAs and graphics, which were small-memory markets. In 2026 the anchor is AI inference: bandwidth-bound decode at ~1 FLOP/B, huge volume, and a software stack controlled by a few vendors (CUDA and the inference runtimes) [cross-ref q1, q4].
- [inference] The case where 1990s near-memory did ship (console eDRAM) had the same property, a closed vendor-controlled stack. That suggests PIM can succeed in AI accelerators even if it fails in general-purpose servers.

### 3.5 Standardisation and commodity reuse

- NOW's core argument was commodity volume ("large scale systems must exploit the desktop system ... as a building block, rather than compete with it") [TR version]. IRAM's core weakness was breaking commodity interchangeability [full text]. Active Pages explicitly designed for "standard memory busses" [full text].
- 1990s remote-memory fabrics were fragmented: SCI, Memory Channel, Telegraphos, Hamlyn, SHRIMP, all listed in Markatos'96 [full text]. Today CXL has absorbed Gen-Z, OpenCAPI and OMI [cross-ref q2, q5], and JEDEC standardises HBM.
- [inference] History predicts that standard, transparent forms win: passive JEDEC HBM, and CXL Type-3 expansion that reuses commodity DDR, which the hyperscalers' DDR4-reuse cost argument relies on [cross-ref q2].

### 3.6 Hyperscaler ownership of the OS and hypervisor

- The 1990s barrier was "changes to the host operating system and device drivers" (Lim'09 on 1990s work) [full text]. NOW deliberately "glu[ed] together local UNIXs" to avoid OS changes [TR version].
- Today hyperscalers modify the hypervisor and kernel themselves (Pond, TPP upstreamed into Linux) [cross-ref q2]. [inference] This lowers the adoption cost of B, and of A in accelerators, compared with the 1990s.

### 3.7 Serial links: one idea, two outcomes

- IRAM already proposed replacing buses with "multiple high-speed, point-to-point, serial lines" for I/O (ICCD'97) [full text].
- HMC applied serial links to near memory and died. CXL applied them to far memory (PCIe PHY) and is succeeding [cross-ref q5].
- [inference] Serialisation pays where pin-efficiency and reach matter (capacity and distance, B). It does not pay where energy per bit dominates (A), because SerDes costs pJ/bit [cross-ref q3: HMC 10 pJ/b versus HBM ~3.9].

### 3.8 Predictions from history [inference, flagged as such]

1. **The least-disruptive form of each direction survives.** This held for every wave above:
   - eDRAM survived as a cache, not as IRAM-as-CPU.
   - CC-NUMA survived; software DSM did not.
   - HBM survived; HMC did not.
   - CXL expansion is surviving; pooling is still contested [cross-ref q5].

   The prediction is that passive HBM plus logic base dies, and single-host CXL expansion, persist. Bank-level PIM and switched pooling stay niche unless they ride inside vendor-controlled stacks.
2. **Each wave dies when its "escape valve" reopens.**
   - If 3D DRAM restores steep cost/bit declines, the economics of far-memory pooling weaken. This is the 1990s network-RAM failure mode, and Levis HotNets'23 already shows pooling is marginal [cross-ref q5].
   - If HBM bandwidth per stack keeps growing fast (HBM4's 2048-bit interface, custom base dies), in-DRAM PIM may again be "outrun ... with economic affordability" (NDP'14). The 2026 version of the "von Neumann outran PIM" risk is "HBM plus the GPU outruns bank-PIM".
3. **The pairing itself is stable.**
   - From IRAM's 1997 "on-chip DRAM + paged off-chip DRAM / networked IRAMs", to Lim'09's local DIMMs plus memory blade, to GB200's HBM plus LPDDR [cross-ref q5], the stable architecture is two-ended: near for bandwidth and energy, far for capacity and cost.
   - What changed is that the two ends used to be decades apart in adoption. Now both are economically forced at the same time (3.2).
4. **Wheel of reincarnation applied to far memory.**
   - Lim'09 put "memory-side accelerators" on the memory blade. Today's CXL controllers gain compute (CXL-PNM, Marvell Structera [cross-ref q5]).
   - [inference] Following Myer & Sutherland, the far-memory controller "starts simple and grows until it has become a full computer". At that point it is a near-memory processor for the far tier. This is the convergence Q5 noted, and history suggests it is the predictable next step, not a coincidence.

---

## 4. Q4: Retrospectives and essays that discuss these cycles

| Source | What it offers | Status |
|---|---|---|
| Balasubramonian et al., "Near-Data Processing: Insights from a MICRO-46 Workshop," IEEE Micro 2014 | Best single retrospective on why 1990s PIM failed (cost/bit, merged process, programming model, "Moore's law allowed ... von Neumann machines to outrun them") and why NDP might be "real this time" (Top-10 list: stacking, MapReduce, interfaces, NVM capacity, anchor workloads) | [full text] |
| Mutlu et al., "A Modern Primer on PIM," 2020–2025 | Three reasons past PIM was not adopted: integration difficulty, lack of memory scaling pressure, investment in processor-centric latency hiding | [full text, history sections] |
| Kogge, "Annotated PIM Bibliography," arXiv 2601.09002 (2026) | 60-year design history (1969 Kautz, 1970 Stone, STARAN, CM-1/2, EXECUBE, Terasys, M32R/D, IRAM, DIVA, HMC, UPMEM, CXL). Descriptive, no verdicts. Supports a forthcoming Kogge article | [full text]; article [not yet available] |
| Kogge, "A Short History of PIM at Notre Dame" (1999 web page) | Cited by NDP'14 | not archived |
| Hennessy, Heinrich, Gupta, "Cache-Coherent DSM: Perspectives ...," Proc. IEEE 1999 | Retrospective on DASH/FLASH and commercial CC-NUMA, with remote/local latency table and predictions (DSM down to 4 processors; >64 processors is an economic question) | [full text] |
| McKee, "Reflections on the Memory Wall," CF 2004 (with the 1995 Wulf & McKee note) | How the memory-wall framing aged. Wulf & McKee: "The most 'convenient' resolution ... would be the discovery of a cool, dense memory technology whose speed scales with that of processors." Also that most fixes "provide one-time boosts" | [full text] |
| Myer & Sutherland, CACM 1968 | Original "wheel of reincarnation", plus the explicit local-versus-shared-memory placement argument | [full text] |
| Maruf & Chowdhury, SIGOPS OSR 2023 (Infiniswap retrospective) | "Idea ... is decades old"; why it became practical now | [full text] |
| Grappa (Nelson et al., ATC'15) | Why software DSM failed historically and why RDMA revived it | [full text] |
| Lim et al. ISCA'09, related work | Why 1990s network memory and DSM did not solve the capacity problem | [full text] |
| Aguilera et al., "Memory disaggregation: why now ...," SIGOPS OSR 2023 | "Why now" for B: burning issue plus feasible technology, drawn from mainframe, storage, power and GPU disaggregation. Says Intel RSA failed on silicon photonics. Predicts CXL pooling in 3–5 years. Silent on near-memory compute | [full text] (R4, D2) |
| Ranganathan, "From Microprocessors to Nanostores," IEEE Computer 2011 | Compute colocated with NVM plus scale-out. "Hierarchy of computes". Noted DRAM scaling slowing in 2011 and bet on NVM | [full text] (R4, D1) |

I found no single essay that treats the IRAM↔NOW pairing, or the A↔B pairing, as one cycle [inference; consistent with Q5's finding of a literature gap]. R4 qualification: some earlier papers treat near-compute and scale-out/capacity as *one design space*, though not as a historical cycle. These are IRAM Computer'97 (on-chip DRAM backed by paged off-chip DRAM or networked IRAMs, p. 77), Ranganathan's nanostores (a "hierarchy of computes", Fig. 4 p. 45, and a traditional vs side-stacked vs 3D-stacked comparison, Fig. 5 p. 46) and NDP'14 Top-10 #6 "Balance".

---

## 5. Key quotes (verbatim, for reuse)

- IRAM Micro'97: "the access latency of an IRAM processor does not need to be limited by the same constraints as a standard DRAM part."
- IRAM Micro'97 (disadvantage 5): "Today's DRAMs are second sourced commodities that are interchangeable ... adding a processor would stratify IRAMs and effectively reduce interchangeability."
- IRAM Computer'97: "A potential solution is to back up a single IRAM chip with commodity-external DRAM, using the off-chip memory as secondary storage with pages swapped between on-chip and off-chip memory."
- IRAM Computer'97: "historical trends indicate that the end-user demand for memory will scale at a lower rate than the available capacity per chip."
- NOW (TR 1994): "Network RAM can fulfill the original promise of virtual memory." Also: "programs run 10% to 30% slower using network RAM than if the program fit entirely in local DRAM; using network RAM is 5 to 10 times faster than thrashing to disk."
- GMS SOSP'95: "a global memory hit is only two to ten times faster than a disk access."
- Active Pages ISCA'98: "ever-growing applications may always demand larger memories and multiple chips."
- Hennessy et al. 1999: "It is likely that cache-coherent DSM will soon be the dominant architectural approach for multiprocessors with as few as four or more processors."
- Lim ISCA'09: "[1990s remote-memory approaches] do not fundamentally address the compute-to-memory capacity imbalance." Also: "3D die-stacking ... further constrains the extensibility of memory capacity."
- NDP'14: "These machines did not fade away because of lack of performance gains or energy efficiency, but simply because Moore's law allowed the basic von Neumann machines to outrun them with economic affordability."
- Myer & Sutherland 1968: "A remote display with its own memory ... has unjustifiable disadvantages unless communication bandwidths force it."

---

## Round-4: SYNTHESIS_DRAFT corrections (v0.3, §6–§7 plus linked lines; draft NOT edited)

Format: draft quote (line no.) → correction → source and page.

| # | Draft (v0.3) | Correction | Source / page |
|---|---|---|---|
| C1 | L265 "**Both waves were killed by the same thing: fast commodity DRAM scaling** [P]". The same claim appears at L83 and L318. | Overstated and wrongly tagged [P]. Cheap DRAM is a **common factor I inferred**; no primary source says it killed both waves. Each wave also had its own primary-sourced kill conditions. For PIM: process penalty, loss of commodity interchangeability, programming model, cost per bit, small capacity per device. For network RAM: no fix for aggregate imbalance, OS changes, reliability, latency, CPU overhead, central coordination. Suggested rewrite: "A common factor in both declines was fast commodity DRAM scaling [Syn]; each also had specific kill conditions [P]." | IRAM Micro'97 preprint p. 11 (30–70% area, 30–100% speed); ICCD'97 §1; NDP'14 p. 37 (cost/bit, programming model) and p. 41 (Top-10 #8, small capacities); Lim ISCA'09 §2 p. 2; Infiniswap NSDI'17 related-work section |
| C2 | L267 "'Moore's law allowed the basic von Neumann machines to outrun them.'" | The quote is from a **sidebar** ("Everything Old is New Again"), not the report body, and it refers to DIVA, Terasys and EXECUBE. Restore the first clause: "These machines did not fade away because of lack of performance gains or energy efficiency, but simply because ... outrun them with economic affordability." The omitted clause says the failure was economic, not technical, which strengthens §6. | Balasubramonian et al., IEEE Micro 34(4) 2014, p. 39 |
| C3 | L268 "Network RAM only beat disk, and cheap local DRAM (−33–36% $/bit per year) won." | The first clause is [P]. The second is [Syn]. The −33–36%/yr figure is a 1991–2010 fitted average (q3), not a documented cause of network RAM's decline. Tag it [Syn]. | GMS SOSP'95 §3 ("only two to ten times faster than a disk access"); q3 §1 (AI Impacts) |
| C4 | L270 "while software DSM died." | Use "stayed niche (revived with RDMA)". IVY shows the boundary: super-linear speedup when data exceeds one node's memory; "no speedup at all" for read-once streaming (dot-product); poor results with frequent write-sharing; tested on ≤8 processors only. Aguilera: coherence limited to "a rack or a few racks". | Li & Hudak TOCS 7(4) 1989 pp. 344, 346, 349; Aguilera SIGOPS OSR 57(1) 2023 p. 44; Grappa ATC'15 §1 |
| C5 | L274 "The 1990s split did *not* follow market lines: the same Berkeley group pursued both. That favours the resource-pressure explanation..." | **Factually wrong as stated.** It was the same group, but it aimed at **different markets**. IRAM was aimed at games, PDAs, embedded/mobile multimedia and intelligent disks; Patterson's slide says "Post-PC Era: PDA >> desktop"; Computer'97 says 96 MB "may be sufficient for portable computers, but not for high-end workstations". NOW was aimed at building-wide "science and engineering" computing. History therefore shows the explanations are **compatible**: resource pressure decides which market adopts which direction. It does not show resource pressure beating the market explanation. Also fix §7(a) L289 "History also shows one group pursuing both" to add "for different target markets". | ICCD'97 §3; Patterson "Future of Microprocessors Embedded in Memory" slides 26 and 28; Kozyrakis et al., Computer Sept 1997 p. 77; NOW Micro'95 abstract |
| C6 | L277 "**The common killer is gone** ... best single explanation..." | Add a precedent caveat. "End of DRAM scaling" was declared before: Ranganathan 2011 cited "a slowing of DRAM growth due to scaling challenges" and bet on PCM or memristor replacing DRAM. That bet failed (The Machine). Wulf & McKee 1995 likewise warned "Our prediction of the memory wall is probably wrong too." The current claim is stronger because it rests on measured post-2010 price data (q3), not a forecast. State that distinction explicitly. | Ranganathan, IEEE Computer 44(1) 2011 p. 42; McKee CF'04 §2 (reprinting the 1995 note); q3 §1 |
| C7 | L257 (same-people list) | Add Ranganathan: memory blades (ISCA'09) and nanostores (2011). Nanostores is compute colocated with NVM plus networked scale-out, **not** disaggregation, so it is an IRAM+NOW-style pairing. Its acknowledgments thank Lim, Mudge and Chang. | Ranganathan 2011 pp. 43–44, 47; Lim ISCA'09 author list |
| C8 | L278/L281 (far tier; standardised ecosystem) | Optional support. Aguilera independently frames "why now" as two requirements, a burning issue and a feasible technology, drawn from earlier disaggregations. They date the idea to remote paging in the early 1990s (Felten & Zahorjan 1991) and credit Lim'09 with the first load/store design. They predicted CXL pooling "in 3–5 years" (from 2023) via 2–8-host multi-ported memory, then switches. Check that against Q5's switch-cost evidence. **Do not cite Aguilera for A/B complementarity**: it never mentions near-memory compute, and its pool controller only translates addresses and enforces isolation. | Aguilera 2023 pp. 40, 41, 42–43 |
| C9 | §7(b) L295 "...Intel RSD ... shows incentives alone don't make a direction work." | Add the stated cause: Intel's 2013 Rack Scale Architecture "did not succeed because it was based on the technology of silicon photonics, which has not yet prevailed". Note RSA vs RSD naming. The Machine depended similarly on memristors (HP's NVM bet, Ranganathan 2011). The common lesson is that waves tied to a not-yet-arrived device technology died. | Aguilera 2023 p. 40; Ranganathan 2011 pp. 42–45; q5 §B6 |
| C10 | §7(c) L299 "No paper we found treats near vs far as one design axis [P: Q5 search]." | Qualify. Older work does treat near-compute and scale-out/capacity as one design space: IRAM Computer'97 (on-chip DRAM backed by paged off-chip DRAM or networked IRAMs); nanostores' "hierarchy of computes" and its three-design comparison; NDP'14 Top-10 #6 "Balance". None gives a single quantitative near↔far memory-placement axis or cost model. Suggested rewrite: "No paper we found treats near vs far as one *quantitative* design axis; earlier position papers (IRAM 1997, nanostores 2011) sketched it qualitatively." | Computer'97 p. 77; Ranganathan 2011 p. 43 (text), Fig. 4 p. 45, Fig. 5 p. 46; NDP'14 p. 41 (Top-10 #6) |
| C11 | §6 new point (optional) | IVY (1989) already marks the A/B workload boundary. Shared memory across nodes wins when the dataset exceeds one node's memory; it fails on low-reuse, read-once streaming, which is the case where near-data compute wins. This supports §3 and Q4 with a 1989 primary source. | Li & Hudak 1989 pp. 344, 346, 349 |

Lines verified as correct against sources: L255–256 (authorship); L260–261 (98.5 vs 4.55 nJ, Fromm ISCA'97 Table 5; 1.44 ms vs 3.6–14.3 ms, GMS Table 1); L266 numbers (IRAM Micro'97; ICCD'97 §2.3 for $40→$6); L270 Origin ~3.6× (Hennessy et al. 1999 Fig. 10, 200 vs 710 ns); L278 (inference consistent with the sources).
