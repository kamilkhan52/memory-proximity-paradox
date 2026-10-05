# Round-1 decisions (orchestrator): binding for all builders

These override BRIEF §D where they conflict. Reviews: r1_accuracy.md (A-items), r1_narrative.md (N-items), r1_visual.md (V-items).

## Global
- **G-1. CXL first-hop latency adder.**
  - Wording: "+~100–280 ns measured". Production-type CXL controllers add +100–160 ns; the +280 ns comes from an FPGA device (Melody). This follows accuracy B1.
  - Per-source values:
    - Melody +100 (CXL-A) to +280
    - Octopus +115–155
    - Vistara +120
    - PolarCXLMem +119
- **G-2. Switch adder.**
  - "≥220 ns" is [V]: XConn, cited by Octopus.
  - Our derived value is "+284–305 ns" [D from P: PolarCXLMem].
- **G-3. Terminology.**
  - Use "3D DRAM–logic stacking" (or "3D-stacked DRAM–logic") everywhere.
  - Raptor is logic on top of DRAM. Keep "DRAM-on-logic" only for SeDRAM/UniIC.
  - "Passive HBM" becomes "HBM without in-stack compute (A1)".
- **G-4. Accuracy fixes from Round 1.**
  - NVMe latency: 10–40 µs measured; the 50 µs end is unverified.
  - Vistara 0.225 is spec-based (Table V).
  - DDR5 idle latency: ~100–150 ns measured (DDR5 97–146), not 80–120.
  - RDMA: ~3.5–4.6 µs (64 B–4 KB).
- **G-5. Footers.** Cite SYNTHESIS v1.3, not v1.2.
- **G-6. Contrast.** deck.css `--ink-3` is darkened to #5f6570, so footers and captions get more contrast automatically. Builders still fix any text under 11 px. On s07, superscript qualifier tags are ≥10 px.
- **G-7. Colour by role.** On s08, LPDDR5X stays orange because it is GB200's capacity tier by role. Add "(colour = role: capacity tier)" to the s08 caption.
- **G-8. Qualifiers.** Every number keeps its measured / simulated / modelled / vendor / projected / spec qualifier.

## Headlines (approved rewrites; each ≤16 words)
| Slide | Headline |
|---|---|
| s01 | Closer and farther are not opposites: both leave DDR's narrow spot on one cost frontier. |
| s03 | Bandwidth must cross a boundary; closer means more wires and fewer pJ/bit, until the die edge. |
| s04 | Capacity cost now bites: $/bit declines slowed ~2.5× and memory is 37% of Meta's rack TCO. |
| s05 | Each tier bundles bandwidth with capacity: A buys GB/s per GB; B, cheaper or shared GB. |
| s06 | Access is skewed within each workload, so a mix of tiers beats any single tier. |

Kickers for s03 and s04 gain "→ A" and "→ B" respectively.

## Per-slide content decisions
- **s01**
  - Replace the three answer points with the four points in N-M4. Write "3D DRAM–logic" in point 2, and "Skew is within each workload" in point 3.
  - Schematic note: "Schematic, not to scale. Spec-derived positions: slide 5."
  - Footer: "values on slides 2–5".
- **s02**
  - Apply G-1 and G-2 (V tag on ≥220 ns in the callout, the bracket and the tooltip).
  - Fix the visual items from V-review: sublabels must not read as belonging to the next row; separate overlapping dots.
  - Add a hand-off line only if space allows: "If not distance, then what? Two pressures (slides 3–4)."
- **s03**
  - New headline and kicker.
  - Replace panel (a)'s label line with "Compute outgrows DRAM bandwidth ~2× every 2 years [D: 3.0/1.6]".
  - State the zHBM energy scope: "projected total DRAM power ÷ peak BW".
  - Append "(→ PIM, slide 7)" to the takeaway.
  - Add the missing connector styles to the legend.
  - Reduce density where possible (V-review).
- **s04**
  - New headline and kicker.
  - Box 1: append "(chart: slide 9)".
- **s05**
  - New headline.
  - Row label: "3D DRAM–logic stack".
  - Apply G-4 to the latency column.
  - Sub: append "$/GB is mostly secondary or unverified, so it is tabulated, not plotted."
  - Annotation 3: "DDR is squeezed: on one bus, bigger DIMMs dilute GB/s per GB."
  - Caption: add "Objects are placed by matching their access density (slide 6)."
  - Fix row crowding and the CXL label cluster (V-review); taller rows are allowed if the table still fits.
  - Footer: tag the RDMA range [D, assumption-based].
- **s06**
  - New headline.
  - Legend line becomes: "ρ (access density) = GB/s demanded per GB stored: the demand-side twin of slide 5's axis. I = FLOP per byte moved · MLP = memory-level parallelism."
  - Add the N-M2 line under the table: "In production, tiering moves 20–33% of DRAM at ≤1–5% slowdown; DDR still holds 50–75% of capacity." Add its footer sources.
  - Add a "warm → DDR" bracket between the hot and cold brackets (N-M10).
  - Fix the y = 100 rows. Do not draw points above the 100% frame. Put the three definitional rows inside the frame at y = 100, with small vertical offsets *inside* the plot band and clear labels. An alternative is a separate inset strip labelled "y = 100 by definition". Replace the ‹ and ^ glyphs with explicit text ("≤", "≥") in labels.
- **s07**
  - Apply G-1 ("+~100–280 ns measured").
  - Tooltip HBM4E >4:1 → Micron Mar-2025 p.6 only.
  - Add PIM weak baselines in A2: "Newton 54× vs GPU, only 10× vs ideal non-PIM (sim.)" (N-M3).
  - Add a "Software" line to each cell (N-M11), ≤8 words each:
    - A1: no change (transparent memory)
    - A2: new programming model, no VM/coherence
    - B1: transparent (OS tiering, zswap) or NUMA node
    - B2: pool management, failure domains, often app changes
  - Make superscript tags ≥10 px.
  - Fix the callout crowding the footer.
- **s08**
  - Mark 2026 shares "est. (June 2026)" as well as 2027 "forecast".
  - Add to the wafer column: "This raises the payoff of B's cheapest forms but did not cause B (designs predate the shortage)."
  - Apply G-7.
  - Rebalance the columns and the empty space (V-review).
- **s09**
  - Quote must read: "Moore's law allowed the basic von Neumann machines to outrun them with economic affordability".
  - Fix the caveat: Wulf & McKee 1995 predicted the memory wall, not the end of DRAM scaling. Rephrase as "Scaling limits were forecast before (memory wall 1995; Ranganathan 2011 bet on PCM/memristors); the difference now is a decade of measured slowdown."
  - Make the chart fits explicit as nominal: "nominal-dollar fit −37%/yr (1990–2010), −12%/yr (2010–24)". Note under the table that the published CPI-adjusted rates are −33–36 and −12–15%/yr.
- **s10**
  - Add the box "Rival explanations, weighed" (N-M1 text, ≤40 words). Footer: "Rival explanations: SYNTHESIS v1.3 §7 [Syn]".
  - "Passive HBM" → "HBM without in-stack compute".
  - Refutation row 2 "Would mean": "A fast middle is not why CPU HBM stopped" (N-m14).
