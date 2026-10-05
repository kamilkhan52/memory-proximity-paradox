# Report R2 citation check (Sonnet). Saved by orchestrator: the agent's file write was refused.

118 refs; ~100 numbers spot-checked. All 9 R1 serious mismatches are resolved. Refs 1–118 resolve, with no orphans or duplicates, and every path exists. Derived values recompute.

## Findings
1. **Two embedded JSON blocks are stale.** 7 of 9 match the current data files; these two do not (data files edited 06:28, after the report):
   - latency_ladder row 9: path, target_row and kind.
   - tier_frontier row 6: basis.
   - tier_frontier row 9: NVMe "measured".

   Fix: re-embed both blocks. Delete the three "data file still…" caveats (Fig. 2, Fig. 5, App. A); the "byte-for-byte" claim is currently false.
2. **SYNTHESIS version.** Cite v2.0, not v1.9.
3. **Berger latency.** Change "+70–95 ns" to the paper's caption value, 75–90 ns. The absolutes, 155/180 ns, are correct.
4. **Tags on sources read only via notes.**
   - Ref 82 (Ma) and ref 105, and the sentence on refs 116–117, are tagged [P, online] but were read only through notes. Retag them [S via notes].
   - FULL TEXT NEEDED for the ~34 sources tagged [P, online]: Levis, Berger TC'26, TMTS, Memstrata, GreenSKU, Colloid, Chang, and others. These are disclosed in App. C but not stored.
5. **Minor fixes.**
   - d-Matrix retention: 32 ms is at 85 °C, and the slide says 8× more refresh at 105 °C. This conflicts with the "doubles per 10 °C" rule; reword.
   - SK hynix "six generations" is not in the deck text.
   - d-Matrix HBM1 6.8 pJ/bit is read off a chart; tag it as such.
   - Lee 2018 pJ/bit values are read off a chart; tag them as such.
   - Melody local DDR5: prose says 111–117 ns, the table says 114–117. Unify.
   - Handy start price: described as $1–2/GB, but the report derives $2.3.
