# Visual QA review, round 4 (fresh eyes)

Method: all ten shots viewed at full size; 2-3x PIL crops in `deck/shots/crops_r4/` (s02 chart, s03 left chart and scatter, s05 rows, s06 plot, s08 mini chart, s09 chart top). Clearance measured by pixel scan (lowest dark pixel to y=680).

Verdict: **ready for presentation, minor polish optional.** Blocking 0, major 0, minor 6.

## R3 items verified
- M1 clearance: resolved. Gaps: s01 36, s02 14, s03 20, s04 33, s05 25, s06 23, s07 15, s08 37, s09 25, s10 31 (all >= 14).
- M2 s02 CXL dot vs "Pond" label: resolved (dot now clear; "Das Sharma 170 ns" label sits below). RDMA cluster partly open (m1).
- m1 s03 y-axis title vs ticks: resolved (no contact).
- m2 s06 Meta label on frame: resolved. Hao second line no longer touches the diagonal.
- m3 s09 "slowdown" line vs "hollow = 2024": resolved (text right-aligned, clear of the line).
- m5 s07 A1 empty space: still about 20px, now tolerable (see m5 below).
- m6 s01 manual break: resolved; A and B paragraphs separated.
- m8 s08 duplicate captions: resolved (one caption).

## Colour semantics and consistency
Blue = A (s02 GPU, s03, s05 HBM, s07, s08 HBM3E, s09 "A aimed at"); orange = B incl. s08 LPDDR5X by role (caption says so); slate = DDR/middle (s02, s05, s06 "warm", s09 "Middle", s08 "CXL link"); green only for 3D/convergence (s01, s03, s05). No violations. Smallest text is the 10.5px s07 chips (allowed). Footer rule at y=680 everywhere.

## Minor
- m1 (s02): RDMA marks (two dots above the bar, square below, bar about 8px tall) are still a tight cluster; the dot/square key is only in the text at right. Offset the dots 4-6px or taller bar. Not blocking: the text label explains it.
- m2 (s05): sublabels ("MI455X, Helios, Rubin", "B200, GH200, GB200", "Diamond Rapids, Venice") sit about 1px above the row separators, and the A64FX circle straddles the HBM3E/HBM-on-CPUs separator. Raise row pitch 2px or drop sublabels 1px; move A64FX 3px down.
- m3 (s05): caption "Peak per-direction specs..." sits tight under the x-axis title (about 4px). Add 4px margin.
- m4 (s03): scatter labels "trend ~1.4-1.5 pJ/bit", "zHBM 3D", "HBM4 (proj.)" lie over dotted constant-power lines. Readable; a 2px white text halo would clean it.
- m5 (s07): A1 card has about 20px empty at bottom while B1 is full; equalise top-row heights or accept.
- m6 (s08): bottom-right mini chart text is small (about 11px) and has no x-axis title; unit "x" appears only on ticks and caption. Acceptable at presentation size; add "x CXL link" axis label if space allows.

## Counts
Blocking 0. Major 0. Minor 6.
