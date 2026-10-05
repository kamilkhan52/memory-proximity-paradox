# Review r3 (confirmation): hbm-beachfront-power.html

Reviewer: fresh technical-accuracy pass, 2026-10-03.

Checked against:
- FGDRAM full text (pdftotext): §1, §3.5, Table 3 and the results section.
- d-Matrix HC'26 text: s5, s6, s8, s17, s23, s25, s26, s28.
- Samsung HC'26 text: s5, s21–24.
- notes/q3_root_causes.md.
- A render at 1100 px (`shots/r3.png`, tiles `r3_0..5.png`).

**Verdict: ready.** One small source mis-attribution should be fixed. Three optional wording tweaks follow it.

## Part 1: r2 findings

| r2 | Status | Current text, with evidence |
|---|---|---|
| 1 | Resolved | Fig. 1: "unterminated link: reach ~5–7 mm (O'Connor, HBM2)". The table column is now "Stack-side channel depth". Fig. 2 caption: "'CH depth' is measured from the stack edge to the far side of its PHY; it is not the full PHY-to-PHY channel." §2 quotes O'Connor §3.5, verbatim at lines 997–1009 of the full text. |
| 2 | Resolved | "HBM2 → HBM3: the PHY grew from 6 mm to 8 mm long with the same 1,024 DQ. HBM3 → HBM4: DQ doubled to 2,048 inside the same 8 mm…". The "decrease proportionally" quote is verbatim. |
| 3 | Resolved | "At the 40 mW per effective GB/s (~4.25–5 pJ/bit) that d-Matrix's s28 table uses for HBM4: about 3.4–4 kW. Its scope is not stated…". s28 does contain "Effective-BW basis". |
| 4 | Resolved | "HBM2's whole I/O channel costs 0.80 pJ/bit at 50% toggling and about 0.3…". "An HBM1-era interface including its PHY costs about 1.5…". "…roughly 2–4× lower". The s8 "2.5pJ+ 3pJ (on chip)" and "~10×" are verbatim. |
| 5 | Resolved | What to remember: "removes the edge limit and most interface energy, but not DRAM-core energy". §4 adds a DRAM-core paragraph: 3.45/3.92 = 88%, and 3.45 × 800 = 2.76 kW, which checks. FGDRAM 1.95 is verbatim, and its I/O is "the same for both" architectures, so "not I/O" holds. |
| 6 | Resolved | "about 256 DQ per mm of PHY length, or about 190 per mm of die edge … Command, ECC, DBI and strobe signals come on top". |
| 7 | Resolved | "at most about three stacks each (3 × 11 mm = 33 mm, with no spacing) … MI455X matches". |
| 8 | Resolved | The value is 0.49 in the caption, the table and the chart data (`e: 0.49`). |
| 9 | Resolved | "~1.4–1.5 pJ/bit … 1.15–1.25 kW [D: … depending on regression direction; a 5.5× extrapolation]". The chart label agrees. |
| 10 | Resolved | Three changes: "[S: COAXIAL; Das Sharma et al., via notes q3]"; "unterminated" is tagged [P: O'Connor §3.5]; Raptor carries "confirming its exact scope needs the Raptor ISCA'26 full text". |
| 11 | Resolved | Sources: "3.92 pJ/bit 'including ECC overhead' (§1)", which is verbatim at line 311. |
| 12 | Resolved | "HBM microbump pitch 48 × 55 µm in an HBM1-era design [P: UniIC IEDM'20 Table 1]". |
| 13 | Resolved | "Edge stacks also sit in the hot host die's thermal footprint, and smaller PHYs concentrate heat [V: Samsung s11]". |
| 14 | Resolved | The Fig. 2 label is "host die (SoC or base die)". |
| 15 | Resolved | The PHY is inset 1 mm in code: px = sx + 7.8 s on the left and sx + 1 s on the right. The render confirms it, and the caption states it. |
| 16 | Resolved | "for longer channels and more stacks [S]". |
| 17 | Resolved | "it is a budget rather than a wall … Samsung's zHBM example moves the 100 W it saves into the GPU". s23 shows "100W saving" and GPU power going 1,200 → 1,300 W (+8.3%), which checks. |
| 18 | Resolved | `.fig svg { min-width: 680px; }`. |
| 19 | OK | |

## Part 2: Fresh read

**A. Fix (minor, mis-attribution).**
- The Sources entry reads "DDR5 channel organization and pin counts: research notes q3 (COAXIAL; Sun et al. MICRO'23)".
- The ~200-pin figure in q3 is from Das Sharma et al., not Sun et al. Fig. 1's caption already cites Das Sharma.
- Fix: "…research notes q3 (COAXIAL SC'24: 160 pins per DDR5-4800 channel; Das Sharma et al., arXiv 2306.11227: ~200 signal pins for DDR5-6400)".

**B. Optional: "most interface energy".**
- This phrase appears in the ladder card 4, in §4 ("most of the interface energy") and in What to remember.
- On the page's own like-for-like numbers, Raptor's 0.37 against HBM2's 0.80 at equal activity is about a 54% cut; against HBM1's 1.5 it is about 75%.
- "Most" is defensible but thin. Suggest "much of the interface energy (2–4× less)".

**C. Optional: Fig. 1 host label.**
- The host is labelled "GPU die" next to "≈1.94 TB/s per stack (MI455X)". On MI455X the HBM attaches to the fabric/cache base die.
- Suggest relabelling it "GPU / base die".

**D. Optional: "unterminated" generalized to HBM4.**
- The lede ("very wide, unterminated parallel bus") and the Fig. 1 header apply "unterminated" to HBM4. The only source for it is HBM2 (O'Connor).
- Suggest adding "(as in HBM2)", or softening to "short-reach single-ended parallel bus".

**Other numbers re-checked and correct:**
- 32× / 38× / 7.6 Gb/s.
- 1.92 kW (s6 verbatim).
- 3.4–4 kW.
- ≈450 W.
- 296 W.
- 11.4 vs 21.9–26.3 MB/mm².
- 105 °C, 32 → 4 ms.
- s17's "I/O energy 2–3 pJ/bit".
- s5's "B-die PHY I/O count and speed" and TSV bottlenecks.
- s22's "eliminates conventional 2D interfaces (e.g., HBM PHY or D2D)", which supports the table's "no interposer or PHY".
- s24's WoW/HCB bonding, which supports "hybrid-bonded".
- O'Connor's 60 W → 2 TB/s (Fig. 1a text).

## Part 3: Visual (1100 px)

- Fig. 1: PHYs face each other. The reach label is clean.
- Fig. 2: the to-scale die is correct: 6 stacks, 1 mm PHY inset, no overlaps.
- Fig. 3: the trend and 2.4 pJ/b extrapolation labels are legible. The zHBM point sits at 0.49 / 31.8, and Raptor's point is correct. The legend is complete. The 1.92 kW hollow point sits just under the 2 kW line.
- Tables: render cleanly.
- No layout defects.
