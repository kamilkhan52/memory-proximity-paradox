# Report R2 technical review (Opus). Saved by the orchestrator because the agent's write was refused. The full text is in the orchestrator's message to the report writer.

**Verdict:** minor revision. 0 blocking, 5 major, 31 minor. Of the 14 R1 majors, 12 are resolved. M8 and M9 regressed after the round-5 and data-file changes, and M10 partly regressed.

## Majors
- **M1 – Stale embedded data.** The embedded latency, tier and price data no longer match the current files, and three caveat sentences are now false. The dram_price_trend.json metadata is also stale.
- **M2 – Stale versions and critiques.**
  - The report cites SYNTHESIS v1.9 and rounds 1–4.
  - The §3 and §7 slide critiques describe slides that have since changed.
  - Headlines should read ~2010, not ~2011.
- **M3 – Bandwidth wording.** "All per direction" is wrong for HBM and DDR, which are half-duplex buses.
- **M4 – Wang "counts channels" reading.** The paper's own numbers contradict it: its 96.8 GB/s best device is ≤31% of a 307 GB/s socket. The denominator is undefined, and §III gives 17.1% and 46.4% of remote DRAM.
- **M5 – Recycled-DDR4 all-in cost.** It was built from a one-controller configuration. With Vistara's two controllers per 256 GB, the range is 0.36–0.93×.

## Minors (31)
These cover:
- Das Sharma: retimers, estimates, switch 62–70 ns
- H100 PCIe at 330 ns
- Pond: intro vs captions; Melody and Levis adders; Octopus gloss
- Fig. 7 B1 +100–160
- SerDes low end; scope chip
- FGDRAM I/O; Lee page reference
- UniIC pitch and generation; Samsung DQ
- d-Matrix "~10×"; retention vs refresh; SK hynix endpoints
- "Not the bandwidth limiter"
- MXT; Fastswap; PIM 8 GB; TPP overhead
- Five-minute rule reads vs writes; Boroumand 44.6%
- DIGITIMES; "<5%" attribution; AiM baseline; "r includes yield"; Mahar
- Vistara power; Berger cost wording; §10 too strong; "I/O I/O" typo

## Figures
- Fig. 1: 3D $/GB position is assumed.
- Fig. 2: RDMA 5.58 µs falls outside the bar.
- Fig. 3c: zHBM lower-bound caveat.
- Fig. 5: caption; HBM3E label should include GB200.

## FULL TEXT NEEDED
Levis, Berger TC'26, ZeRO-Infinity, Ma et al.
