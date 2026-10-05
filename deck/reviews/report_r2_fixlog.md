# Report R2 fix log (checklist)

Legend: DONE = already in the report before this pass; FIXED = edited now; NOT FIXED = reason given.

## Citations
- Stale JSON blocks / byte-for-byte claim: DONE (blocks verified unchanged by this pass; caveats removed)
- SYNTHESIS v2.1: DONE
- Berger 75-90 ns: DONE
- Ref 82, 105 retagged [S via notes]: DONE. Refs 116-117 sentence: FIXED (Ranganathan clause now tagged [S via notes; P])
- FULL TEXT NEEDED for ~34 [P, online] sources: NOT FIXED (sources not stored; disclosed in App. B/C)
- d-Matrix retention 8x vs doubling rule: DONE
- SK hynix "six generations": DONE (phrase absent; x5.0 endpoints marked inferred)
- HBM1 6.8 pJ/bit and Lee values tagged chart read: DONE
- Melody local DDR5 111-117: DONE
- Handy $1-2 vs $2.3: FIXED (reworded: 7.1x label implies ~$2.3; $1-2 refers to earlier curve)

## Technical (majors M1-M5): DONE (M1 data, M2 versions, M3 half-duplex wording, M4 Wang denominator, M5 0.36-0.93x / 0.4-0.9x)
## Technical minors
- Das Sharma 57 ns / 170-250 estimates / switch 62-70: DONE
- 330 ns = H100 PCIe, GH200 <300; Fig. 2 row label: DONE in visible label; FIXED aria-label ("GPU to HBM ...")
- Pond 70-90 intro vs 75-90 captions, 85 ns baseline: DONE
- Melody +100-160 / Levis +140-160: DONE
- Octopus 75-170 ns gloss: FIXED (now: share of a 200-300 ns CXL read, CPU share not itemised; 65 ns port/flight, 25 ns device)
- Fig. 7 B1 cell +100-160: DONE
- 6-23 pJ/bit low end anchored to Intel Fig. 10 comparators: DONE
- 1.3-2x line like-for-like exception, switched ~3.2x: DONE
- FGDRAM I/O 0.77 (QB-HBM and FGDRAM) vs HBM2 0.80: FIXED (verified Table 3)
- Lee 46% p.31392 at peak bandwidth: DONE
- UniIC 48x55 um HBM2E ISSCC'20, ~1.5 incl. PHY inference: DONE
- Samsung DQ [background], 2048/11=186: DONE
- d-Matrix ~10x scope mix: DONE
- Retention reword: DONE
- SK hynix x5.0 inferred: DONE
- CPU+DDR "not the bandwidth limiter": FIXED (hedged, tagged [Syn])
- MXT SPECint separate, DL360 hypothetical: DONE
- Fastswap steps: FIXED (+25%, then +20%, +50% only from 24x8 GB; verified in paper)
- HBM2-PIM 8 GB inference [D]: DONE
- TPP overhead hedged: DONE
- Five-minute rule reads vs writes: DONE
- Boroumand 44.6% performance: DONE
- DIGITIMES paraphrase: DONE
- "<5%" is TMTS; zswap SLIs; Vistara <1% IPC: DONE
- GDDR6-AiM baseline/FPGA estimate: DONE
- "r includes yield" is our assumption: DONE
- Mahar 90%-tile interpretation: DONE
- Vistara power 30 W / ~50 W: DONE
- Berger added cost wording: DONE
- Section 10 attach-point wording: DONE ("poorly")
- "I/O I/O" typo: DONE
## Figures
- Fig. 1 assumed 3D $/GB: DONE; Fig. 2 RDMA square caption: DONE; Fig. 3c zHBM lower bound: DONE
- Fig. 5 caption per-direction convention, HBM3E label incl. GB200: DONE
- Fig. 9 label "slowdown (~2011)": FIXED (now ~2010-11, matching text)
## Visual
1 Narrow SVG text <11 px: DONE (measured: scale 1.0, 0 text nodes <11 px at 500 and 1100 px)
2 Scroll hint only when overflowing: DONE (hidden at 1100 px)
3 Sup anchors 11.5 px: DONE
4 TOC appendix wrapping: DONE
5 Back-to-top: DONE (floating "Contents" link); sticky nav / h3 anchors: NOT FIXED (low, optional)
6 Fig. 3c label halos: DONE (rendered check)
7 Fig. 2 glyph crowding: DONE at 1100 px (rendered); narrow clipping is by design (scroll)
8 Colour semantics: FIXED for Fig. 3b caption (slate); Fig. 9 has a single series, no caption needed
9 Long paragraphs 98-112 words: NOT FIXED (low; arithmetic chains kept inline)

No embedded JSON block was modified (verified byte-equal before/after).
