# Visual QA review, round 1

Reviewer role: visual QA. Method: all ten shots viewed at full size, with 2x PIL crops of the dense charts (saved in `deck/shots/crops/`). `assets/deck.css` and `build/assemble.py` NAV_JS were read, not executed. No blocking items. Verdict: **needs revision** (0 blocking, 16 major, about 35 minor; counted below).

## Systemic findings (all slides)

- **V-S1 major, contrast and size.** `--ink-3` (#7a808a) is about 3.9:1 on white. It is used for 10.5-12px text: footers and source lines, captions, chart legends, sublabels, and the s06 `‹ ^` key. This is below 4.5:1 and, for the footer, below the 11px floor.
  - Fix: darken `--ink-3` to roughly #5f6670 (about 5.8:1).
  - Fix: raise `.slide-foot` from 10.5px to 11px.
  - Fix: shorten the 3-line footers on s03, s05, s07 and s09.
  - Evidence: `.slide-foot` is `font-size: 10.5px` in `assets/deck.css`.
- **V-S2 major, tick-label size.** In the SVG charts, 28 text elements are 11px and `.axis` is 12px. This is at the floor and unreadable from the back of a room. Where space allows (s02, s05, s06), use 12-13px for row sublabels and ticks.
- **V-S3 major, colour semantics.** LPDDR5X is slate on s02 (Grace row) and s05 (CPU-attached LPDDR5X), but orange on s08 (the "capacity tier" bars and heading). Either s08 uses slate for the tier, or the deck states that "capacity tier" is a B-role colour. As it stands, one object carries two meanings.
- **V-S4 minor, green.** On s08 the green panel ("B → compute at the far tier") is not 3D and is not shown as convergence. The panel heading is orange, so green here reads as an arbitrary fill. Use `.box.far`, or neutral if the intent is "both".
- **V-S5 minor, vertical rhythm.** Slides end at very different heights. s04, s08, s09 and s10 leave 90-130px empty above the footer. s05, s07 and s09 touch the footer rule. Specifically, the s07 callout bottom (about y=680), the s05 takeaways (about y=675) and the s09 caption (about y=680) all sit within 3-8px of the rule at y=683. Target at least 12px clear above the rule.
- **V-S6 minor, headline blocks.** All h2s are 34px (consistent). s06 is a single line and the rest are two lines, so the content tops vary (y=190 on s06, 195 on s08, 230 on s04, 235 on s05). Acceptable, but s05 and s04 have a larger gap under the subtitle than s06 and s08.
- **V-S7 minor, carried-over inconsistencies still visible in the screenshots (ORCH_NOTES not fixed).**
  - s07 B1 reads "+~100-270 ns", but s02 reads "+~115-280 ns".
  - s05 NVMe idle latency reads "10-50 µs", but s02 reads "10-40 µs".
  - The footers on s01, s07 and s10 say "SYNTHESIS v1.2", but the reviewed answer is v1.3.

## Per-slide findings

### s01
- **minor.** The right half is a schematic with no ticks, which is fine because it is labelled. Its y-axis label "$/GB →" is about 11px and tiny against the x label.
- **minor.** The lower-left quadrant (y 560-690) is empty and the left column ends at y=545, so the slide is bottom-light. Move the caption "Latency is a separate constraint..." up or enlarge the schematic.
- **minor.** The 3-line key "HBM / PIM / 3D" at the arrow tip has a thin vertical bar on its left that reads as a bracket. The green on "3D DRAM-on-logic" is correct (3D). Good: A/B/DDR colours and the claim box (item 1) dominate.

### s02
- **major, row grouping.** In the left column the sublabel "includes GPU cache hierarchy" (blue row) sits about 3px above the title "CXL direct-attach" (orange row). The same happens with "(2-4 ports)" above "CXL behind a switch". Row separators are very faint, so sublabels can be read as belonging to the next row. Add about 10px between rows or draw a rule above each group title.
- **major, illegible marks.** The RDMA cluster `● ■ ●` occupies about 15px on the bar, and the glyphs overlap the bar and each other (64 B dots at 3.55/4.55 µs and the 4 KB square at 3.9 µs). The CXL direct-attach dots (250, 265) are likewise merged into one blob. Fix: offset the dots vertically (jitter) or enlarge the bar's height, and keep the text list as is.
- **minor, legend.** The legend shows a "hollow dashed" rectangle, but the Das Sharma marks are dashed circles and Berger's is a dashed rectangle with an arrow. These glyphs are about 10px and not in the legend. Their intervals are barely visible against the 11px label text.
- **minor, bracket placement.** The "+1 hop" and "+switch" brackets sit at x≈680, about 200px from the marks they describe, with only faint gridlines between. The eye has to cross empty space. Move them nearer the data (for example the empty area at x 520-640), or drop the connector to the row.
- **minor, ticks.** Ticks run `50 100 200 500` (no unit) then `1 µs`. The axis title says "ns", so the µs ticks are inconsistent. Suggest `50 ns ... 500 ns | 1 µs`.
- **minor, scale.** The plot uses about 55% of the slide width and the text is 11-13px. The callouts on the right take the remaining space. The chart could be 15-20% wider.
- Log axis is marked, direct labels are present, nothing else is misleading.

### s03
- **major, density and hierarchy.** This is the densest slide: a mini line chart, a die diagram, a log-log scatter with four iso-power lines, and three captions plus a closing paragraph. The headline claim has two halves ("die edge limits the wires" and "energy per bit the watts"), but the die-edge half sits in the smallest, bottom-left element. The scatter dominates the eye. Either scale up the die diagram or reduce the captions.
- **minor, orphan.** The left chart caption wraps with an orphan "1.4×." (line 2).
- **minor, collision.** "HBM4 (proj.)" is crossed by a dotted iso-power line, and the "trend ~1.4-1.5 pJ/bit → ~1.2 kW" label starts on a dotted line. Add `.halo` to those labels.
- **major, legend incomplete.** The solid blue connector, the dotted hollow link and the dashed vertical line from HBM4 to the 100 TB/s hollow are all used, but the legend only lists "constant power" and "hollow: extrapolated". Say what solid, dotted and dashed mean (generation trend, extrapolation to 100 TB/s at trend energy, extrapolation at 2.4 pJ/bit).
- **minor.** In the left chart the "interconnect" line label uses `--ink-3` and recedes. The data are fitted lines, not data points, and are labelled as such (good). Log axis is labelled; the iso-power lines are labelled at the right edge (2 kW, 1 kW, 300 W, 100 W; fine).
- Colours: blue = HBM/A, green = 3D, slate = DDR. Consistent. GDDR6 in slate is acceptable.

### s04
- **major, headline versus visual.** The headline carries two claims: "$/bit decline slowed ~2.5×" and "37% of rack TCO". The chart shows only the second. The first is a single text line in a grey card at top right with no visual, while s09 has the data. A reader's eye goes to the chart and the "37.1". Either add a small $/bit sparkline or reduce the headline to the claim the chart carries.
- **minor, alignment.** The chart legend sits at y=242 and the first card top at y=233; card stack ends at y=582 and the chart axis at y=600. Align tops and bottoms.
- **minor.** Both bar series are neutral (dark / pale slate), which is correct, since the bars are neither A nor B. The orange cards ("Idle bytes", "Captured in production") are acceptable as B-motivation, but they also contain the 32% / 55-80% numbers, which have the lowest visual weight in the card. Bold the numbers.
- Chart integrity: the axis starts at 0, there is a unit, data labels are printed, and there is no dual axis.

### s05
- **major, row density.** Rows are about 27px high, and 2-line labels (name plus 11px sublabel) make adjacent rows touch. "HBM4 / MI455X, Helios, Rubin" runs straight into "HBM3E". The table columns on the right are 11.5-12px grey. This is the key slide for the claim and it is the smallest in type. Increase the row pitch to 32-34px (cut one takeaway to make room, or move the takeaways to the callout column).
- **major, CXL expander label cluster.** In the CXL expander row, "0.06" and "0.25" float above the ends of a 12px bar and "Vistara (spec) 0.225" sits below it. Three labels in a 27px row, with "0.25" detached from its bar end. Place the range label to the right of the bar ("0.06-0.25") and the Vistara dot label to the right of that.
- **minor.** The "HBM on CPUs" hollow bar and the A64FX circle touch at 26/31 (circle overlaps the bar end), so the circle reads as the bar's end cap. Add 4px of gap or offset the circle vertically.
- **minor.** The shaded "DDR5 RDIMM range" band has a header label but no numeric range (0.2-0.8). Add it in the label.
- **minor.** The "3D DRAM-on-logic" row's long grey note "on-die SRAM ~3×10⁵ (scratchpad...) →" is 11px and its arrow points off the axis; clear, but the row reads as empty until the circle at x≈3,100. Good that green stripe and blue stripes match the semantics.
- **minor.** The headline's second line starts "A buys ... B buys ...", where "A" starts a sentence after a semicolon. Lower-case or rephrase.
- Log axis is marked, tick values name the scale (0.0001 ... 10k), and there is no dual axis.

### s06
- **major, chart geometry.** Two of the three "y = 100 by definition" rows (Mooncake, Meta TPP) are drawn above the plot frame, outside the 0-100 axis, "offset for legibility", and Google fleet sits on the y=100 line. A reader sees points above 100%, and the key line "top rows offset for legibility" is 11px grey. The labels are also outside the frame, with dotted leaders that run about 170px across the frame's right edge into the legend column. Option: draw all three at y=100 as ticks on the top edge with labels on the leader only, or move them to a separate strip clearly bounded and titled "Bytes-only statistics (y = 100 by definition)", with its own x axis.
- **major, glyph annotations.** The `‹` and `^` marks that mean "at most" and "more than" are about 8px, ambiguous (Meta services `‹`, Hao `^` over the diamond). Replace them with a labelled arrow, or put the qualifier in the text label ("at most 10%").
- **minor, A/B brackets.** The blue "hot head → A" (0-15) and orange "cold tail → B" (45-100) brackets are marked "illustrative", but they use A/B colours on a chart where no point is A or B, so they read as data-derived. Put them in neutral colour, or move this mapping to the table (which already has it).
- **minor, balance.** The plot is only about 330px wide. The right table dominates and the right column is empty below y=545, while the left column is crowded to y=670. Enlarge the plot to the left column's full width.
- **minor.** The "uniform access: no skew (e.g., dense weights...)" note sits 80px from the diagonal it describes; move it next to the line.

### s07
- **major, legibility of superscripts.** Superscript tags (`V`, `meas.`, `sim.`, `spec`, `D`) render at about 8-9px next to 14-16px text, for example "~3×V the wafer", "up to 25%meas.", "(sim.)". They carry the measured/simulated/vendor qualifiers the brief requires, so they are load-bearing. Use 11px tags with a visible chip style (`.tag`), not sup.
- **major, bottom crowding.** The "Pattern:" callout wraps to three lines and its last line ends within about 3px of the footer rule. The legend block at right (11px mono, grey) is bottom-aligned to a different baseline from the callout. Reduce the callout to two lines, or shrink the four cells by about 20px (A1 has about 30px of empty space at the bottom of its box, B1 has none).
- **minor.** The vertical row labels "DEPLOYED" and "AMBITIOUS" are 11px mono, rotated, and low contrast.
- **minor.** The B1 line "+~100-270 ns" mixes the "+~" operator; see V-S7 for the value.
- Colours: A blue, B orange, filled versus dashed. Good, and the dashed/filled legend is stated.

### s08
- **major, balance.** The slide has a content block ending at y=550 and about 130px of empty space above the footer. The left column holds two mini bar charts, the middle one chart and a paragraph, and the right a short box. Three columns of different heights and different visual weight; the eye goes to the left column. Make the charts taller (about 20% more height) or add a bottom takeaway line.
- **major, colour (see V-S3).** The LPDDR5X bars are orange but the earlier slides show slate.
- **minor, contrast.** White bold 13px label "17 TB" on orange #eb6834 is about 3.3:1. Use dark text or a darker orange, or place the label outside the bar like "14 TB/s".
- **minor.** The middle chart's solid versus hatched (forecast) fills are explained only by the caption "2027 is a forecast"; add a legend entry. Bars start at zero; the axis has a label.
- **minor.** The green box (see V-S4).
- **minor.** The two left-column charts use unrelated x scales (0-20 TB, 0-600 TB/s) stacked directly; fine as separate panels, but align the two bars' x-origins and make the "≈43 vs ≈0.82 GB/s per GB" line, the point of the panel, bolder.

### s09
- **minor.** The line chart is good: a log axis marked ("log scale", $ ticks), a dashed fit with in-chart labels, a vertical marker at ~2011 and a hollow last point. The hollow marker (partial year) is explained only in the caption; add "2024 (partial)" next to it.
- **minor, caption length.** The 3-line grey caption at the foot of the chart ends about 3px above the footer rule (V-S5).
- **minor.** The table text is 14px with generous cell padding, but the "New enablers" row overflows to three lines, making that row twice the height of the others. The 1990s column has a lone "-" for New enablers, which reads as a minus.
- Colour use: "A" blue, "B" orange, "Middle" slate. Consistent.

### s10
- **minor, hierarchy.** The headline's second clause ("three observations would refute this account") is the more actionable claim, yet the refutation table is on the right at smaller type and lighter weight than the predictions table. The reading order goes left then right, so the point lands last. Align both tables' widths and bring a visual marker (for example a rule or heading in the same style) to the right table.
- **minor.** The confidence pills (High / Medium / Low) are all identical grey, so confidence is carried only by the word. Fine for semantics (does not reuse A/B/green), but consider a three-step fill.
- **minor.** The bottom third of the slide is empty. The callout "On server CPUs, any product between the DIMM and HBM must beat MRDIMM-12800 + a large SRAM LLC + CXL." is a strong statement but visually minor; it would work as the claim box.
- **minor, footer.** The footer says "Pending full texts: Kwon ISSCC'21 ..., d-Matrix Raptor ISCA'26". This is working-note language in a presentation, so move it to speaker notes or the review file.

## deck.html present mode: NAV_JS notes (read only, not run)

1. **major, scaling.** `fit()` uses `transform: scale(s)` on `.deck` and sets no explicit width or height. The unscaled layout box stays 1282x720+ px, so with s<1 the document still overflows (scrollbars in the viewport) and with s>1 the slide is cut at the bottom. Add `body.present{overflow:hidden}` (plus `.deck{height:100vh}`) and centre with `transform-origin: top center` and `margin: 0 auto`, or use `zoom` instead of `transform`.
2. **minor, double scaling.** `fitScroll()` sets `slide.style.zoom` in scroll mode on narrow windows, then returns early when `present` is on, without clearing the zoom. Entering present mode on a narrow window therefore compounds zoom and `scale()`. Clear `zoom` in `toggle()` when entering present mode.
3. **minor, hard-coded sizes.** `fit()` assumes the sticky bar is exactly 42px tall (`innerHeight - 42`) and a width of 1280 (the slide with its border is 1282). The bar's height depends on the font and zoom. Read `deckBar.offsetHeight`.
4. **minor, input.** `Space` is intercepted globally with `preventDefault()`, so a focused button cannot be activated by Space. `Home` / `End` / `Escape` do not exist (Escape should leave present mode). No touch/swipe support.
5. **minor, tooltip.** The `#tip` position uses `clientX` and clamps to `innerWidth - 310`, so under `transform: scale()` it is correct (it is fixed and outside `.deck`). Check that it does not appear for `hidden` toggling in present mode: `tip.hidden` is only changed by `attach`, fine.
6. **minor, initial hash.** `show()` uses `history.replaceState` inside try/catch; fine. The initial `fit()` is not called at load, but present is off initially, so that is correct.
7. **minor, offline.** `FONTS` is loaded from Google Fonts. Offline, the slides fall back to Georgia/Helvetica, whose widths differ, so the layouts (which are tight, for example s05 and s07) may overflow. Embed the fonts or test the fallback.

## Counts

- Blocking: 0.
- Major (16): V-S1, V-S2, V-S3; s02 row grouping, s02 illegible marks; s03 density, s03 legend; s04 headline versus visual; s05 row density, s05 CXL labels; s06 geometry, s06 glyph annotations; s07 superscripts, s07 bottom crowding; s08 balance; NAV_JS scaling.
- Minor: all other items.
