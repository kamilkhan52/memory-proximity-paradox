# Round-4 decisions (orchestrator): binding

These supersede earlier decisions. Sources: r4_accuracy.md, r4_narrative.md, r4_visual.md, plus report_r1_technical.md items that affect the deck. Canonical values are in ORCH_NOTES.md (updated). SYNTHESIS is now v1.9; footers may cite v1.9.

## s02
- NVMe row: label it "typical (TPP Fig. 2)", not measured, because TPP Fig. 2 is a generic technology figure. Draw it in the estimate style or a typical style, not as a measured bar, and update the legend if needed.
- Add the 4 KB RDMA read at 5.58 µs (PolarCXLMem Table 2) as a mark on the RDMA row.
- Axis title: "Idle access latency (log scale): load-to-use for memory tiers; page/block read for RDMA 4 KB and NVMe".
- Headline: use a non-breaking hyphen in "lower‑latency" (U+2011).
- Cable: keep "~12 ns (Levis)".
- Optional polish from r4_visual: the RDMA cluster.

## s03
- Replace the takeaway with: "After the interposer, most energy sits in the DRAM core (O'Connor's HBM2 model: 3.45 of 3.92 pJ/bit, full access, modelled). 3D stacking removes the edge limit, but HBM I/O is already only 0.3–0.8 pJ/bit by toggle rate vs Raptor's 0.37, so core energy remains (→ PIM, slide 7)."
- Footer: replace the 2–4× derivation with "HBM2 I/O 0.3 (real toggle) / 0.80 (50% toggle): O'Connor §1, Table 3 [P]; Raptor 0.37 measured I/O [V]".
- Caption: add "zHBM = 4-stack projection (others 8-stack)".
- Move the "Raptor 3D (I/O only)" label next to its own marker (r4 narrative m7). Halo the scatter labels.

## s04
- Headline: "Capacity cost now bites: $/bit declines slowed ~2.5–3× and memory reached 37% of Meta's rack TCO."
- Box 1: "~33–37%/yr before ~2010; ~12–15%/yr since (chart: slide 9)."
- Footer: "$/bit: AI Impacts 2020 series averages [S]; our nominal 1990–2010 fit [D]; slowdown ratio 2.4–3.1× depending on % vs log-rate [D]".

## s05
- Caption: drop the Wang "10–80%" sentence; it is misleading under a GB/s-per-GB chart. Remove Wang from the footer.
- Annotation 1: "Its recycled DDR4 moves it down in $/GB."
- aria-label: HBM3E is 33–43.
- Optional polish from r4_visual: sublabels touching separators.

## s06
- Change "TPP: 33% on pre-production CXL" to "TPP: 33% in emulation". Footer: "TPP ASPLOS'23 (emulated)".

## s07
- B1 bandwidth ratio: "~3.2–3.6× less bandwidth per GB" (x8 Gen5 read cap 64 GB/s; Vistara Table III measured ~3.4×). Update the tooltip.
- HBM3E tooltip: 33–43.
- Mooncake: keep "trace replay".

## s08
- Col 2 text (r4 narrative m2): "New-DRAM B tiers pay more; wafer-free B (recycled DDR4, compression) gains. The shortage did not cause B."
- Footer: remove d-Matrix slides the text no longer uses. Add the inputs for the bars:
  - ~3×: Structera A, 4× DDR5 (~200 GB/s) vs a 64 GB/s CXL link [V].
  - ~17×: LPDDR5X CXL-PNM ~1.1 TB/s vs x16 Gen5 64 GB/s [D from P].
- Optional polish from r4_visual: the mini chart's text and axis title.

## s09
- Caveat: "Memory crises were forecast before (memory wall 1995; Ranganathan 2011 bet on PCM/memristors); the difference now is a decade of measured $/bit slowdown."
- Table: "before ~2010: ~33–37%/yr".

## s10
- Headline: "Specify memory by frontier position, not attach point; three observations would count against this account."
