# Visual QA review, round 2 (fresh eyes)

Method: all ten shots viewed at full size; 2-3x PIL crops in `deck/shots/crops_r2/` (s01 chart, s02 chart/rows, s03 left/scatter, s05 chart/bottom, s06 plot, s07 bottom/A1-B1, s08). A headless-Chrome script measured effective font sizes (including SVG scale), footer geometry and the clearance between content and the footer rule on every slide. `build/assemble.py` NAV_JS and `assets/deck.css` were read.

Verdict: **ready after minor polish** (0 blocking, 2 major, about 10 minor). Most R1 majors are resolved.

## Deck-wide checks

- **Footer (`--ink-3` #5f6570, 11px):** no overflow or collision. All footers are 2-3 lines. The source block and the page number never meet (page number sits on the last line, right-aligned). The footer bottom is at y=735 on a 748 slide. Contrast is fixed. Footers now say "SYNTHESIS v1.3".
- **Text below 11px:** none, except the superscript chips on s07 (10.5px, allowed) and a few SVG labels that measure about 10.9-11.0. Rotated axis titles measure 0 only because of the rotation (false positive).
- **Colour semantics:**
  - Blue is A (s02 GPU row, s03 HBM, s05 HBM rows, s07 A cells, s08).
  - Orange is B (s02 CXL/RDMA/NVMe, s04 cards, s05 B rows).
  - Slate is DDR and the middle (s02 local DDR, s05 DDR/MRDIMM/LPDDR5X, s06 "warm → DDR").
  - Green is 3D only (s01, s03, s05).
  - s08 LPDDR5X is orange by role. The caption says "(colour = role: capacity tier)", and s02's third callout ("'Far' is a role") sets this up. This is acceptable. The s08 green box is now orange-tinted (fixed).
- **Vertical rhythm:** the footer rule is at y=680 on s01-s09 but y=695 on s10 (minor jitter). Clearance from the lowest content to the rule: s01/s03/s06/s08/s09 10px; s04 6px; s05 2px; s02 1px; s07 about 10px of text clearance. See M1.

## R1 findings: status

| R1 item | Status |
|---|---|
| V-S1 contrast, 11px footer, 3-line footers | Resolved (contrast and size). Footers are still 3 lines on s01-s09, which is acceptable. |
| V-S2 11px chart ticks | Partly open. s03 and s05 chart text is still 11px (s03 labels, legend, ticks). Readable on screen, small in a room. minor (see m3). |
| V-S3 LPDDR5X colour | Resolved by the role caption (s08 caption wraps to an orphan "tier)"; minor). |
| V-S4 s08 green box | Resolved. |
| V-S5 vertical rhythm | Open: s02, s04, s05 still sit 1-6px above the rule (M1). s07 is fixed. |
| V-S7 numeric consistency | Fixed: s07 "+~100-280 ns" and s02 "+~100-280 ns measured"; NVMe "10-40 µs" on s02 and s05; footers v1.3. |
| s02 row grouping | Partly open (M2). |
| s02 RDMA/CXL dot clusters | Open (M2). |
| s02 ticks | Fixed. Ticks read "500 ns | 1 µs | 100 µs". |
| s03 legend | Fixed. Solid, dashed, dotted and hollow are all defined. |
| s03 iso-power halo | Fixed. Labels are legible over the dotted lines. |
| s04 headline vs visual | Open, now minor (m2). |
| s05 row density, CXL label cluster | Mostly fixed. The CXL range is one label to the right. Rows are still tight (m4). |
| s06 geometry, `‹` `^` glyphs | Fixed. The bytes-only rows sit in a bounded, titled strip; qualifiers are in text (x ≤ 10%, y ≥ 50%). |
| s06 A/B brackets | Still blue/orange, but labelled "brackets illustrative" in 11px grey. minor (m5). |
| s07 superscripts | Fixed. They are now boxed chips at 10.5px. |
| s07 bottom crowding | Fixed. The callout is two lines with about 10px clear. |
| s08 balance, 17 TB contrast | Fixed. Content reaches y=670 and the label is dark. |
| s10 working-note footer | Fixed. |
| NAV_JS items | See below. |

## Open findings

**M1. major, s02 / s05 / s04 bottom clearance.**
- Elements: s02 caption "approximations, confounded by each host's cache hierarchy." (box bottom 679); s05 takeaway block (box bottom 678); s04 chart caption (674).
- Problem: the descender-to-rule gap is 1-6px, against a target of at least 12px.
- Fix: s02, shrink the plot rows by about 8px or cut the caption to one line. s05, shorten takeaway 2, which wraps to 3 lines, to 2 lines. s04, move the caption up 6px.

**M2. major, s02 left-column grouping and marks.**
- Row pitch is still about 3px between a sublabel and the next row title: "includes GPU cache hierarchy" sits against "CXL direct-attach", and "(2-4 ports)" sits against "CXL behind a switch". The separators are faint, so the sublabels can be read as belonging to the next row.
- The RDMA cluster (two dots, a square and a short bar within about 20px) still reads as a blob. The CXL direct-attach dots (250, 265) overlap the bar.
- Fix: add about 10px of padding above the CXL direct-attach and CXL behind-a-switch groups, or darken those separator rules. Offset the RDMA dots by +/-5px vertically, or enlarge the bar to 14px tall. The text list already carries the values.

**m1. minor, s02 legend and brackets.**
- Legend: the "spec / modelled range" glyph is a dashed rectangle. The Berger mark has an arrow, which is a "lower bound" shape the legend covers separately, so it works.
- Brackets: the "+1 hop" and "+switch" brackets are still about 200px from their marks. The "+switch" bracket's superscript V is faint.

**m2. minor, s04 headline.** It makes two claims ("$/bit declines slowed ~2.5×" and "37% of rack TCO"), but the chart shows only the second. The first appears as grey card text. Consider a small sparkline, or put the rates in bold.

**m3. minor, s03 and s05 text at 11px.** All s03 scatter labels, ticks and legend entries, the s05 column headers "$/GB (REL. DDR5 = 1)" and "IDLE LATENCY", and the s08 sublabels are 11px. Raise the headers to 12px where space allows.

**m4. minor, s05 rows.**
- Row pitch is about 28px, so a sublabel ("MI455X, Helios, Rubin") nearly touches the next row's title.
- The A64FX circle abuts the end of the Xeon Max hollow bar. The "Xeon Max 11-26 (STREAM-peak)" label sits on the lower line of that row and can be misread as belonging to the row below.
- Offset the circle by 4px.

**m5. minor, s06.**
- The "uniform access" note is still about 80px from the diagonal it describes.
- The Mooncake row has a dotted line from 0 to the circle at 50%. Its meaning ("x ≤ 50%") is only in the label.
- The brackets use A/B colours although no point is A or B. Switch to neutral, or keep them and make "illustrative" larger.

**m6. minor, s08.** The right panel's box is 285px tall with its content ending at about 405px, leaving the lower half empty. Shrink the box or align it with the left column's height. The caption "(colour = role: capacity tier)" wraps with an orphan "tier)".

**m7. minor, s01.** The lower-left quadrant (y 600-680) is empty, and the left column ends at about y=600. This is acceptable.

**m8. minor, s09.** "hollow = 2024 (partial year)" sits beside the x-axis title "Year", right at the 2020 tick. It is readable but crowded.

**m9. minor, s10.** The footer rule is at y=695 against 680 on the other slides. The table tops are at y=263 (left) and y=238 (right), a misalignment; align both table headers.

**m10. minor, s07.** A1's box has about 30px of empty space at its bottom, while B1 is full. Equalise the cell heights, or let A1 end at 400px.

## deck.html present mode (NAV_JS and CSS read)

- Fixed: `body.present` now has `overflow:hidden; height:100%`, so the unscaled layout box no longer produces scrollbars. `toggle()` clears slide `zoom` on every entry and exit, and `fitScroll()` re-applies it only in scroll mode, so zoom and scale no longer compound. The scroll-mode narrow-screen zoom (`(innerWidth-24)/1282`) is correct.
- Open, minor:
  - `fit()` hard-codes the sticky bar as 42px and the slide as 1280 wide. Read `deck-bar.offsetHeight` and use 1282.
  - Space is intercepted globally with `preventDefault`, so focused buttons cannot be activated with Space.
  - Escape, Home and End do nothing. Escape should leave present mode.
  - There is no touch or swipe navigation.
  - In scroll mode, `zoom` is applied to slides but `fit()` is not called on load. This is harmless because present is off.
  - Google Fonts load from the network. Offline, the fallback fonts change the widths of the tight slides (s02, s05, s07); test this before presenting offline.

## Counts

Blocking 0. Major 2 (M1, M2). Minor 10 (m1-m10) plus 5 NAV_JS items.
