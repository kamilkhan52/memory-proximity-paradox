# Round-5 decisions (orchestrator): binding

Sources: r5_accuracy.md, r5_narrative.md. SYNTHESIS is now v2.0.

- **s03 (accuracy M1).**
  - **zHBM caption:** "zHBM = Samsung 4-stack projection: ~0.49 pJ/bit of total DRAM power at 31.8 TB/s; utilisation unstated, so treat it as a lower bound. Samsung's own HBM4E baseline is ≈2.0 pJ/bit."
  - **Takeaway I/O phrase:** scope it as "HBM2 interposer I/O is 0.3–0.8 pJ/bit by toggle rate (PHY excluded)".
  - **Takeaway caveat:** add a short clause saying newer generations cut core energy. Suggested: "(HBM2 model; newer generations lower it)". Keep the takeaway at or under ~50 words.
- **s02.** Remove the stale Pond row from the data block (the 155–180 ns estimate now sits on the MHD row as Berger/Pond). Keep the cable at "~12 ns (Levis)".
- **s05.** Change the subtitle/caption from "Peak, per-direction vendor specs" to "Peak vendor specs (per direction for serial links)".
- **s07.** B1 status chip "Deployed (Meta, Google, Azure)": give Azure a source in the tooltip or footer. Use Octopus [P], which describes Azure deployment, and the Azure M-series press release [Pr].
- **s08.** The ~17× bar: label it "[D, assuming x16 Gen5]" in the footer.
- **s09.** Table note: say the "before ~2010: ~33–37%/yr" range combines the published AI Impacts averages (33–36%, 2020$) with our nominal fit (−37%).
- After these fixes, re-render all slides and check s05.png shows "typical" for NVMe.
