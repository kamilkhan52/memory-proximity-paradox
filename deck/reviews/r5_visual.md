# Visual QA review, round 5 (fresh eyes)

Method: all ten shots viewed at full size; 2-4x PIL crops in `deck/shots/crops_r5/` (s01 chart, s02 chart and RDMA, s03 scatter, s05 rows, s06 plot, s08 mini chart). Clearance measured by pixel scan (lowest dark pixel above the y=680 rule).

Verdict: **ready for presentation.** Blocking 0, major 0, minor 6.

## Clearance (px to footer rule)
s01 36, s02 18, s03 31, s04 33, s05 25, s06 23, s07 15, s08 38, s09 26, s10 31. All >= 15. No clipping or overflow on any slide.

## Colour semantics and consistency
Blue = A (s02 GPU row, s03 HBM, s05 HBM rows, s07 A cards, s08 HBM3E, s09 "A aimed at"); orange = B incl. s08 LPDDR5X by role (captioned) and the DDR5-controller bar in the s08 mini chart (B tier, ok); slate = DDR/middle/CXL link (s02, s05, s06 "warm", s08 baseline, s09); green only for 3D/convergence (s01, s03, s05). Footer rule and page number position consistent. No violations. Smallest text: s07 chips about 10.5px (allowed); s08 mini-chart labels about 11px.

## Minor
- m1 (s02): RDMA far-memory marks (two dots over the bar, two squares under it, an isolated tick/square at 5.58 us to the right) form a small cluster; the dot = 64 B / square = 4 KB key exists only in the adjacent text. Acceptable; a 4-6px spread or a tiny key would help.
- m2 (s05): row sublabels (e.g. "MI455X, Helios, Rubin", "B200, GH200, GB200", "Diamond Rapids, Venice") sit about 1px above the row separators; the "HBM on CPUs" row title nearly touches the separator above. The A64FX hollow circle sits on the HBM3E / HBM-on-CPUs separator and "Xeon Max 11-26" box hugs the next separator. Add 2px row pitch or nudge down 1-2px.
- m3 (s05): the caption "Peak per-direction specs..." is about 4px under the x-axis title; add margin.
- m4 (s03): scatter labels "trend ~1.4-1.5 pJ/bit", "HBM4 (proj.)", "zHBM 3D" cross dotted constant-power lines; readable, a white text halo would tidy it. The "HBM4 (proj.)" label is grey while its point is blue; fine, but left of the solid point the line and label nearly touch.
- m5 (s07): A1 card has about 20px empty at the bottom while B1 is full; equalise or accept.
- m6 (s08): bottom-right mini chart is small relative to its neighbours (about 11px text, bars 14px tall, row labels crowd the bar above). It does have an axis title now ("Bandwidth seen, x CXL link"). Acceptable at presentation size; give it another 10-15px height if the layout allows.

## R4 items re-checked
m1 (RDMA cluster) persists as m1; m2/m3 (s05) persist as m2/m3; m4 (s03) persists; m5 (s07) persists; m6 (s08) improved (axis title present).

## Counts
Blocking 0. Major 0. Minor 6.
