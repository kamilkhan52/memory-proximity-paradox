# Report R1 citation & traceability check (Sonnet). Saved by orchestrator from the agent's returned text; the agent's write tool was refused.

## Counts
- 288 numeric sentences or cells (~612 numbers); 181 tagged inline. 107 untagged, mostly table cells whose tag is in the next column.
- ~140 values checked against full texts or slide text. All derived ratios recomputed. Fig. 9 fits reproduce.
- Appendix B: 105 references, all resolve; every "where read" path exists on disk.

## Serious mismatches
1. B2 table, Pond row: says "77% DRAM saving in the abstract". Pond says 7%.
2. TMTS is Google, not Meta: fix "Meta's TMTS" in the text and ref 44.
3. The SemiAnalysis "density doubling per decade" claim is cited to ref 42, which doesn't contain it. The quote comes from a different 2024 SemiAnalysis article, which is not in the reference list.
4. Vistara has no dollar figures. "$15/GB", "$300 ASIC" and "$210 card" come from notes/r2a and Ma et al. arXiv 2603.10087. That paper is not in the reference list.
5. Appendix A says all nine data files were extracted twice, blind. hbm_bw_energy.json was copied from the triple-reviewed explainer, and its extraction note still says "pending spot-check".
6. "~4× per pin per direction" is cited to Das Sharma (ref 9), whose figures give ~16×. The 4× is COAXIAL's, which is not stored (FULL TEXT NEEDED, or keep it secondary via notes).
7. Handy "$2.3/GB Jul–Aug 2025" is inferred from 16/7.1. The chart ends 10 July 2026, not late July. SK hynix TSV ×5.0 and thermal ×2.2, and the d-Matrix 38.4 GB/s LPDDR5X ports, come only from image or vision descriptions and are not flagged.
8. These have no reference entry: TrendForce NAND +55–60%, the HBM3E >4× premium, HBM4 $32–36/GB, and UALink "1,024 accelerators".
9. The report cites SYNTHESIS v1.4; the current version is v1.8.

## Tags and references
- Ref 16 (notes only) and ref 19 (intro only) are tagged [P]. Change to [S] or [unv.].
- 43 online-only references carry about 70 [P] tags. These were checked against notes only.
- Ref 70 (Newton) is listed as online, but fulltext/newton-micro20.pdf now exists. Its 54× and 10× figures are correct.
- Ref 82 (AIFM) is orphaned. Ref 5's title is the system name.
- A64FX 1 TB/s and 32 GB are in the uncited fulltext/3695794.3695813.pdf. Cite it and tag [P].
- 7 Hot Chips references use directory or glob paths.
- These items have a tag but no reference entry: Structera, Azure M-series, OMI, Optane pricing, Gen-Z, UPMEM, Oskin, memory blades.
- TrendForce is tagged [S] in some places and [Pr] in others. Unify as [S].
