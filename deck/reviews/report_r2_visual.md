# Report R2 visual / readability review

Method: Playwright Chromium, 1100 px (page 50,852 px tall) and 500 px (61,886 px). Crops in deck/shots/report_crops_r2/ (d_/n_fig01-13, d_/n_page_top, glossary). Not all figures were eyeballed at both widths (viewed: d_fig02, 04, 05, 06, 09, n_fig02, d_page_top).

## R1 items resolved
- 1 scroll cue: every figure shows "Scroll sideways to see the full figure" (13 cues). Narrow still clips content, which is now signposted.
- 3 tags: .sc chips are 11.5 px, colour rgb(74,80,89) (about 7.9:1, AA pass).
- 4 navigation: TOC is directly under title; Sections and Figures lists (3a-d, 8a/b) are aligned; Terms/How-to-read/Appendix links added.
- 5 hierarchy: How to read is h2; A1/B1/A2/B2 are h3 "in detail".
- 6 abstract: now a lead plus 4 bullets; longest paragraph is 112 words (was 180).
- 16 glossary ("Terms used") present. 17 tag key present. 11 "+~" gone, p99.99 tail now in legend; RDMA markers separated (still tight).
- 19 @media print exists.
- No horizontal page scroll at 500 px (scrollWidth 500).

## Remaining
1. MEDIUM | Narrow SVG text: scale is 0.90 for all figures; 211 SVG text nodes render below 11 px (Fig 2: 35, Fig 9: 32, Fig 7: 23, Fig 3: 22). Fix: lift min-width or use bigger base font in SVG.
2. LOW | The scroll hint is shown at 1100 px even where figures fit (Figs 3b, 6, 9 do not scroll). Show only when scrollWidth > clientWidth, and add an edge fade.
3. LOW | Citation links in superscripts are 10.9 px (103 anchors). Raise to 11.5 px+.
4. LOW | TOC appendix line wraps mid-item ("C / Pending", "F Five- / minute rule"). Put each appendix on its own line or use nowrap.
5. LOW | No sticky nav, back-to-top links or h3 anchors across 50k px.
6. LOW | Fig 3c: "trend -> 1.15-1.25 kW", "HBM4 (proj.)" and the Raptor 3D label still sit on dotted power lines; add a halo. 
7. LOW | Fig 2: RDMA 64 B and 4 KB glyphs still touch; "dots ..." labels crowd the CXL rows. At 500 px right-hand labels and the CXL-hop bracket are cut off (by design, scroll).
8. LOW | Colour semantics: Fig 3b uses slate for "host die" and Fig 9/3d use slate/greys for non-DDR series (3d is captioned "greys carry no A/B meaning"; 3b and 9 are not). Add one caption line or use a distinct neutral. Purple not seen outside neutral use.
9. LOW | Longest paragraphs still 98-112 words ("Economic rules...", "Printed values were read...", "For scale, Samsung's example GPU...") with arithmetic chains inline.
10. INFO | Chart integrity: log axes labelled (Figs 2, 3c, 9); Fig 6 strips are explained in the caption; no truncated axes seen.
