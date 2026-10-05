# Round-2 decisions (orchestrator): binding

Reviews: r2_accuracy.md (MA-items), r2_narrative.md (R2-M and m items), r2_visual.md (M and m items). These decisions add to r1_decisions.md and take precedence over it where the two conflict.

## Global
- **G2-1. Production share (accuracy MA2).**
  - Do not say "in production … 20–33%". TPP's 33% comes from pre-production CPUs with FPGA CXL.
  - Use this wording: "20–25% of DRAM moved to slower tiers in production at <5% slowdown (zswap ~20%, TMTS 25%, Vistara 25% of capacity); TPP 33% on pre-production CXL."
  - On a slide, the short form is: "~20–25% in production (zswap, TMTS, Vistara); 33% in TPP's pre-production CXL tests".
- **G2-2. Synthesis version.** SYNTHESIS is now v1.6. Cite v1.6 in footers that cite a version.
- **G2-3. Terminology.** Use "3D DRAM–logic stacking" everywhere, including row labels. Don't use "3D bonding", "3D DRAM–logic stack" or "DRAM–logic" without "stacking".
- **G2-4. Spacing.** The lowest content must sit at least 12 px above the footer rule (visual M1). The footer rule must be at the same y on every slide; s10 currently sits lower, so fix it.
- **G2-5. Interface 2–4×.** This figure is our arithmetic. Tag it [D from P], not [P]. It holds against HBM2 I/O at 50% toggle (0.80) or an HBM1-era interface with PHY (~1.5).
- **G2-6. CXL 394 ns.** The 394 ns end of the CXL range comes from an FPGA device (Melody). Say so wherever the 214–394 ns range appears (s02 row, s05 cell).

## Per slide
- **s01**
  - Point 4: "The directions are coupled, and since ~2011 slower $/bit declines no longer let the middle outrun both."
  - Point 2: "(HBM, 3D DRAM–logic stacking, PIM)". Point 1 wording: "Distance is not what is optimized."
  - Footer: v1.6.
- **s02**
  - Visual M1: spacing above the footer.
  - Visual M2: sublabels must clearly belong to their own row, and the RDMA and CXL dot clusters must not merge.
  - G2-6.
- **s03**
  - New headline: "Bandwidth must cross a boundary; each step closer buys wires and pJ/bit until the next limit binds."
  - Takeaway (MA1 + m4): "After the interposer, most energy sits in the DRAM core: in O'Connor's HBM2 model, 3.45 of 3.92 pJ/bit (full access, modelled). 3D DRAM–logic stacking removes the edge limit and cuts interface energy 2–4× [D], not core energy (→ PIM, slide 7)."
  - Add a caption note: "d-Matrix's HBM2 point (≈5.6 pJ/bit) uses its own, different scope."
  - Apply G2-5.
- **s04**
  - Replace box 4 ("Captured in production") with a pointer, so the claim is not repeated on s06: "Idle bytes become cheaper bytes: production tiering is on slide 6." Alternatively, delete the box and rebalance.
  - G2-1 wording appears only on s06.
  - Spacing: G2-4.
- **s05**
  - Row label: "3D DRAM–logic stacking".
  - G2-6.
  - Tag the Chips and Cheese latency cells [S].
  - Make the hollow-marker rule consistent and say it in the caption: hollow means discontinued, vendor-peak or spec only.
  - The A64FX circle must not touch the Xeon Max bar.
  - Update the aria-label.
  - Spacing: G2-4.
- **s06**
  - Production line: "Mixing pays: ~20–25% of DRAM moves off DDR in production (zswap, TMTS, Vistara) at <5% slowdown, 33% in TPP's pre-production CXL tests; DDR still holds 50–75% of capacity."
  - Footer sources follow from that line.
  - Bracket colours: follow the visual minor finding, while keeping A blue and B orange.
  - Move the "uniform access" note next to the diagonal.
- **s07**
  - Move the Newton caveat to the end of A2 Best evidence: "…; gains shrink vs strong baselines (Newton 54× vs GPU, 10× vs ideal, sim.)". A2 Main cost becomes "HBM2-PIM stack 6 vs 8 GB spec; DRAM-process logic; heat".
  - Optional, only if it fits: add "Optane: a cheaper tier must be cheaper to make" to the end of B1 Main cost.
  - Footer: v1.6.
- **s08**
  - Adopt the R2-M1 three-block rewrite from r2_narrative.md, about 60–64 words. Cut "(d-Matrix too)" if needed to reach about 60.
  - The rewrite must use "favours wafer-free B (recycled DDR4, compression); it did not cause B" (R2-M2).
  - Col 3 header: "B → compute at the far (capacity) tier".
  - The Catch line keeps "channel-level bandwidth".
  - Call XCENA a vendor product claim, not a prototype; BEACON is simulated.
  - Fill the half-empty right panel sensibly, without adding claims.
- **s09**
  - Middle cell (m9): `$/bit −33–36%/yr. PIM "did not fade away because of lack of performance gains or energy efficiency"; commodity machines could "outrun them with economic affordability" (NDP'14).`
  - 1990s "New enablers": "—".
  - Note under the table (m11): "Table: published AI Impacts fits (2020$; windows and datasets differ). Chart: our nominal-dollar fits, 1990–2010 and 2010–24."
- **s10**
  - Rival box, Different markets: "first-order, yet single workloads (LLM serving, GB200) use both, so markets differ because their binding constraint differs."
  - Footer evidence list for "mostly simulated or modelled": "(AttAcc, CXL-PNM, Berger, Pond)".
  - Footer rule position: G2-4.
  - Footer: v1.6.
