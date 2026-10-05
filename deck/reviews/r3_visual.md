# Visual QA review, round 3 (fresh eyes)

Method: all ten shots viewed at full size; 2x PIL crops in `deck/shots/crops_r3/` (s02 chart, s03 scatter and left column, s05 chart, s06 plot, s07 cards, s08 right panel). Footer rule and lowest-content row measured by pixel scan on every shot. Font sizes taken from the CSS and SVG attributes (smallest is 10.5px, the s07 chip; all other body text is 11px or more; SVG charts are scaled but read as 11px or more).

Verdict: **needs minor polish before presenting** (0 blocking, 2 major, 8 minor). R2's two majors are mostly resolved; the clearance target of 12px is still missed on two slides.

## Deck-wide checks

- **Footer rule:** y=680 on all ten slides (measured). The s10 jitter from R2 is fixed.
- **Clearance above the rule (lowest dark pixel to y=680):**

| Slide | Gap (px) |
|---|---|
| s01 | 35 |
| s02 | **9** |
| s03 | 20 |
| s04 | 33 |
| s05 | 25 |
| s06 | 22 |
| s07 | **9** |
| s08 | 40 |
| s09 | 25 |
| s10 | 31 |

  s02 and s07 miss the 12px target (see M1).
- **Colour semantics:**
  - Blue is A: s02 GPU row, s03 HBM, s05 HBM rows, s07 A cells, s08 HBM3E bars and wafer bars, s09 "A aimed at".
  - Orange is B: s02 CXL/RDMA/NVMe, s04 idle-bytes card, s05 B rows, s07 B cells, s08 right panel.
  - Slate is DDR and the middle: s02 local DDR and Grace LPDDR5X, s05 DDR/MRDIMM/CPU-LPDDR5X, s06 "warm → DDR", s09 "Middle".
  - Green appears only for 3D / convergence: s01, s03, s05.
  - s08 LPDDR5X is orange by role. The caption says so, and s02's third callout sets it up. This is consistent. The caption fits on one line now.
  - s05 and s02 draw Grace/CPU-attached LPDDR5X in slate, while s08 draws it orange. This is explained by role, not an error.
  - s08 bottom-right mini chart: "CXL link" is slate, the controller bars are orange. This is acceptable.
- **R2 items:**
  - Resolved: s05 and s04 clearance; s10 rule and table alignment; s08 right-panel height; s08 caption orphan; s02 sublabel/row-title spacing (now about 10px); s06 brackets (labelled "illustrative").
  - Still open: s02 and s07 clearance, the s02 RDMA cluster, the s07 A1 empty space (see below).

## Major

**M1. s02 and s07, clearance above the footer rule.**
- s02: the caption's second line ("approximations, confounded by each host's cache hierarchy.") ends at y=671, so the gap is 9px.
- s07: the pattern bar's second line ends at y=671 (gap 9px). The "Next: are A and B independent?" text sits at the same height.
- Fix: s02, make the caption one line (cut "Platforms differ; compare within a row.") or shift the plot up 4px. s07, tighten the cards (see m5) by 6px, or reduce the pattern-bar top margin.

**M2. s02, marks and labels collide.**
- The black CXL direct-attach dots (250, 265 ns) sit above and below the bar. The lower dot (about x=405, y=372) touches the "Pond" label's hollow glyph and text line.
- The RDMA group (two dots above, a bar, a square below, all within about 20x25px) still reads as a blob. The dot/square meaning is only in the text at right.
- Fix: nudge the lower CXL dot 4px left or lower the Pond line by 6px. For RDMA, offset the 3.55 and 4.55 µs dots by 6px horizontally and enlarge the bar to 14px tall.

## Minor

**m1. s03, left mini chart.** The rotated y-axis title "Growth (×, log scale)" runs into the tick labels ("100k", "10k"): the glyphs touch at x about 45. Move the title 6px left or shorten it to "Growth (×)".

**m2. s06, plot.**
- The "Meta services · x ≤ 10%, y ≈ 90%" label sits on the top frame of the plot (y=100 line). Move it down 8px.
- The "Hao et al." second line ("emulated, zipf 0.9") touches the dashed diagonal. Shorten it or move it 10px left.
- The "uniform access" note is still a fair distance from its line (carried over from R2, low priority).

**m3. s09, chart.** The vertical "slowdown (~2011)" line passes through the start of the "hollow = 2024 (partial year)" text (the "h"). Shift the text 8px right or end the line below it.

**m4. s05, rows.**
- Row pitch is about 28px, with sublabels one pixel above the separators ("MI455X, Helios, Rubin", "Diamond Rapids, Venice"). Increase the pitch to 30px, or drop 1px of the sublabel size.
- The "A64FX ~31" circle and label cross the separator between the HBM3E and "HBM on CPUs" rows. Move them 4px down.

**m5. s07, A1 card.** It has about 30px of empty space at the bottom while B1 is full. Set both top cells to the same content-driven height. This also recovers the room for M1.

**m6. s01, left column.**
- Item 2 has a manual break after "(HBM," which leaves a short ragged line. Remove the break.
- A and B sit in one paragraph. A blank gap between them would help scanning.

**m7. s03, scatter.** The "HBM4 (proj.)", "zHBM 3D (Samsung proj.)" and "trend ~1.4-1.5 pJ/bit" labels lie over dotted constant-power lines. They are readable. A white 2px text halo would remove the noise.

**m8. s08, right chart.** The two stacked captions ("Bank-level bandwidth: not visible to the controller" and "Seen by a CXL controller, × its link [D]; CXL-PNM sim.") carry overlapping information. Merge them. The chart is not given an x-axis title, so the "×" unit is only in the caption.

## Counts

Blocking 0. Major 2 (M1, M2). Minor 8 (m1-m8).
