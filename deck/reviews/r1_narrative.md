# Round 1: narrative and editorial review

*Reviewer: narrative/editorial (fresh), 2026-10-03. Inputs: shots/s01–s10.png, slides/s01–s10.html, BRIEF.md §A/§B/§D/§G, SYNTHESIS_v1.3.md, reviews/ORCH_NOTES.md, notes/q1 (Newton baselines), explainers/review_r1.md #6.*

**Verdict: needs revision.** Blocking 0 · Major 17 · Minor 23.

The arc matches BRIEF §A.2 slide for slide, and the deck does answer the question. The weak points are these:
- **No rival explanations.** The deck never weighs the obvious alternatives: different markets, vendor incentives, and PIM's weak baselines.
- **Missing links between slides.** Several slides state their claim without showing how it connects. s03 never says "→ A". The s05 supply axis and the s06 demand ρ are not tied together. s06 says mixed tiers "beat" a single tier, but the evidence for that sits on s04. s07 claims "no new programming model" but the slide shows nothing about software.
- **Thin support for "the DDR middle persists".** s01 promises this point, but the deck barely backs it up.
- **ORCH items still open.** Two of the orchestrator's cross-slide items are not fixed yet.

Headline word counts are all within 16 words; I checked each one. Body text is within about 60 words on every slide. s03 is the densest slide, but it stays legible.

---

## 1. ORCH_NOTES verification (cross-slide items)

| Item | Status | Where |
|---|---|---|
| CXL adder "+~115–280 ns measured" everywhere | **NOT FIXED.** s02 says +~115–280; s07 B1 "Main cost" still says "+~100–270 ns" | s07 → M7 |
| NVMe 10–40 µs measured (50 unverified) | **NOT FIXED on s05.** s02 shows 10–40 µs; s05 table (`s05-data`, NVMe `lat`) still says "10–50 µs" | s05 → M8 |
| Octopus "switch ≥220 ns" tagged V | Fixed on s07 (superscript V). **Not on s02**: the callout "a switch adds ≥220 ns more" and the chart bracket "+switch: ≥220 ns more" carry no V. The s02 footer cites Octopus only as [P] | s02 → M2 |
| Pond 7–9% (sim.) | Fixed on s07 | — |
| Vistara 0.225 spec-based | Fixed on s05 chart ("Vistara (spec) 0.225") | — |
| HBM4E >4:1 = Micron Mar-2025 p.6 only | Fixed in s08 footer | — |
| A64FX ~31 | Fixed on s05 | — |

---

## 2. Deck-level findings

**M1 · deck (s09/s10) · major · Rival explanations are missing.**
- **What is wrong.**
  - SYNTHESIS §7 weighs three rivals: (a) different markets, (b) vendor incentives, (c) different communities.
  - The executive summary also lists "Vendors also want an escape from commodity pricing" under *Why both, and why now*.
  - The deck has none of this. Only s09's "Markets" row hints at (a), and nothing interprets it.
  - A memory-industry researcher's first objection will be "these are just two markets, AI accelerators vs CPU cloud, each doing its own thing." The second will be "vendors want out of commodity pricing." The deck doesn't answer either.
- **Fix.** s10's right column has about 200 px of empty space under the refutation table. Add a small `.box` titled "Rival explanations, weighed" (~38 words):
  > **Different markets:** true, and compatible: markets differ because their binding constraint differs. **Vendor incentives:** real (escaping commodity pricing), but HMC, Gen-Z and The Machine show incentives alone don't make a direction work.
- **Footer.** Add "Rival explanations: SYNTHESIS v1.3 §7 [Syn]".
- **Word budget.** s10 body goes from ~20 to ~58 words, which is still within budget.

**M2 · deck (s01 → s06) · major · "The DDR middle persists" is asserted but barely evidenced.**
- **What is wrong.**
  - s01 point 3 makes this claim, and it is the thesis's third leg.
  - After s01, the only support is s05 annotation 2 (MRDIMM matches Xeon Max's HBM peak) and the s06 "Low ρ, low MLP → DDR bus" row.
  - The strongest evidence is missing. SYNTHESIS App. A says that in every deployed tiered system, DDR still holds most capacity: Vistara 75%, TMTS 75%, TPP 67%, Memstrata 50% [D from P]. Berger's pools also need more than 25% of memory local [P].
  - The s06 brackets make this worse (see M10).
- **Fix.** Add one line under the s06 table (≈18 words):
  > In production, tiering moves 20–33% of DRAM at ≤1–5% slowdown; DDR still holds 50–75% of capacity.
- **Footer.** Add "DDR share: Vistara, TMTS, TPP, Memstrata [D from P], SYNTHESIS App. A".
- **Bonus.** This one line also supplies the evidence for the s06 headline's "beats any single tier" (M9).

**M3 · deck (s07) · major · PIM's weak baselines are not acknowledged.**
- **What is wrong.**
  - SYNTHESIS §4 A2 and §7(c), plus notes/q1 l.376 and l.574: Newton is 54× vs a realistic GPU but only 10× vs an ideal non-PIM design. AiM's "up to 10×" comes from FPGA emulation. PrIM's baseline is a 4-core Xeon E3.
  - s07 shows A2's best numbers and marks AttAcc "(sim.)", which is good. But nothing tells the audience that the headline PIM gains shrink against strong baselines.
  - s10's "Low: economics mostly simulated or modelled" covers B2 better than A2.
- **Fix.** Append to the s07 A2 "Main cost" line: "; gains shrink vs strong baselines (Newton 54× vs GPU, 10× vs ideal)".
- **Footer.** Add "Newton MICRO'20 [P]".
- **If space is tight.** Drop "heat" from Main cost; "DRAM-process logic" already implies it.

---

## 3. Per-slide findings

### s01 · Question and answer

**M4 · major · The opening answer omits the two pressures, the coupling and "why now", so it does not preview the deck.**
- **What is wrong.**
  - Points 1–3 say *what* the directions are (positions on a frontier) but not *why* each is attractive. "Why" is the research question.
  - The SYNTHESIS answer has five parts: frontier; skew/mix/middle; two pressures (bandwidth across a boundary → A; capacity cost → B); coupling; why now.
  - Point 1 also restates the headline almost word for word ("narrow spot" / "narrow region").
- **Fix.** Replace the three points with these four (≈61 words):
  1. Distance is not the variable being optimized.
  2. A answers the bandwidth pressure: more GB/s per GB, fewer pJ/bit (HBM, 3D DRAM–logic, PIM). B answers capacity cost: cheaper or shared GB (CXL, compression, RDMA pools).
  3. Skew is per object, so mixed tiers win; the DDR middle persists.
  4. The directions are coupled, and since ~2011 cheap DRAM no longer lets the middle outrun both.
- **Note.** The schematic already carries the axes ("GB/s per GB stored", "$/GB"), so point 1 can drop them.

**M5 · major · The schematic note "Measured positions: slide 5" and the footer "measured values on slides 2–5" overstate the evidence.**
- **What is wrong.** s05 plots peak per-direction vendor specs [D from V]; only Vistara is measured, and even that is spec-based per ORCH. s03 is a vendor replot. This is a wrong qualifier.
- **Fix.**
  - Schematic note: "Schematic, not to scale. Spec-derived positions: slide 5."
  - Footer: "Schematic only; values on slides 2–5."

**m1 · minor · "Escapes" in the headline sits awkwardly with point 3's "the DDR middle persists".** A reader can take "escape" to mean DDR is being abandoned. Optional headline (15 words):
> Closer and farther are not opposites: both leave DDR's narrow spot on one cost frontier.

This also states the deck's anti-"opposites" point (BRIEF do-not-say).

**m2 · minor · The footer cites "SYNTHESIS v1.2".** s07 and s10 do too. Change all three to v1.3; it is the reviewed answer, and its only change is the G6 tag fix.

### s02 · Distance is a proxy

The headline is supported and the chart does the work. This is the strongest slide.

**M2b (see ORCH table) · major · The vendor tag is missing on "a switch adds ≥220 ns more".**
- **Fix.** Callout: "One CXL hop adds ~115–280 ns (measured); a switch adds ≥220 ns more<sup>V</sup>." This also removes the redundant "adds +~". Apply the same tag to the chart bracket label.
- **Footer.** Add "switch ≥220 ns: XConn via Octopus ref. [50] [V]".

**m3 · minor · "Far is a role" is never called back.**
- **What is wrong.** On s02, Grace→LPDDR5X is slate (CPU main memory). On s08, the same LPDDR5X is orange (GPU capacity tier). That colour change *is* the "far is a role" point, but nowhere does the deck say so.
- **Fix.** On s08, change the row label to "LPDDR5X behind NVLink-C2C (capacity tier by role, slide 2)", or add a `.tiny` note.

**m4 · minor · No hand-off to s03.** BRIEF §A.2 wants "If not distance, then what? Two pressures…". See m16 for a cheap fix.

### s03 · Pressure 1: bandwidth

**M6 · major · The slide never connects the pressure to direction A.**
- **What is wrong.**
  - The slide's job (BRIEF §A.2) is "That explains A."
  - The headline "Bandwidth crosses a boundary: die edge limits the wires, energy per bit the watts" states a limit, not why moving closer is attractive.
  - "Die edge limits the wires" is also HBM-specific. For CPU+DDR the binding limit is pins and per-wire rate (SYNTHESIS §2.0 table).
  - Panel (b) shows the limit *of* A1 (beachfront), not the reason for A.
- **Fix.**
  - Headline (16 words): "Bandwidth must cross a boundary; closer means more wires and fewer pJ/bit, until the die edge."
  - Kicker: "03 · Pressure 1: bandwidth → A".
- **Optional.** If budget allows, put the pitch ladder in the sub instead of the two formulas: "Pitch: ~1 mm ball → ~40 µm microbump → 3 µm hybrid bond". Source: SYNTHESIS §2.0 [V/S/P]. It is the single most direct evidence that closer buys wires.

**m5 · minor · Panel (a)'s label line repeats the line-end labels.** "Compute 3.0× per 2 years; DRAM bandwidth 1.6×; interconnect 1.4×" duplicates labels already on the chart. Replace it with the inference: "Compute outgrows DRAM bandwidth ~2× every 2 years." That is 3.0/1.6 ≈ 1.9 [D]. Or cut it to save ~11 words on the densest slide.

**m6 · minor · Point forward from the takeaway.** The takeaway ("energy sits in the DRAM core … not core energy") is exactly A2's motivation (s07 Driver: "in-die energy"). Append " (→ PIM, slide 7)" so the hand-off is visible.

### s04 · Pressure 2: capacity cost

**M7a · major · The headline's "Capacity got costly" is literally untrue for 2011–2024.**
- **What is wrong.** $/bit kept *falling*, ~12–15%/yr (box 1; s09 chart). A memory-industry audience will object at once. The headline also carries two claims, which breaks the one-claim-per-slide rule.
- **Fix.** Choose one:
  - Minimal change (16 words): "Capacity cost now bites: $/bit declines slowed ~2.5× and memory is 37% of Meta's rack TCO."
  - B-oriented (16 words): "Bits stopped getting cheap fast: memory reached 37% of Meta's rack TCO, much of it idle."
- **Kicker.** "04 · Pressure 2: capacity cost → B".

**m7 · minor · The sub "while cores and models kept growing" has no on-slide evidence for "models".** Fix: "Bits stopped getting cheap fast while core counts kept growing, and much of the memory bought sits idle." Box 2 supports this through capacity per performance.

**m8 · minor · The rising TCO share could reflect provisioning choices as well as price.** Meta may have put more GB per rack. The chart cannot separate the two, and it is non-monotonic. Say so in the tooltip or the report; there is no room on the slide.

**m9 · minor · The $/bit slowdown is s04's lead claim, but its chart is on s09.** Fix: append "(chart: slide 9)" to box 1.

### s05 · The cost frontier

**M8 · major · NVMe latency "10–50 µs" contradicts s02's "10–40 µs" (ORCH item not fixed).** Fix: in `s05-data`, set the NVMe `lat` to "10–40 µs (measured)", per TPP Fig. 2 [P]; 50 µs is unverified.

**M9b · major · The s05 x-axis and the s06 ρ are never connected (and "access density" is never named).**
- **What is wrong.**
  - s05's x-axis is *supplied* bandwidth per GB stored. s06's ρ is "bytes/s demanded per byte stored". These are the same unit, and the match between them is the deck's central mechanism: SYNTHESIS §3 "Economic rules", the five-minute rule and the roofline.
  - The deck never says this. The term "access density" (SYNTHESIS §3) never appears.
  - So s05 and s06 read as two separate facts, not one argument.
- **Fix.** Replace the s06 legend line (`.tiny`) with:
  > ρ (access density) = GB/s demanded per GB stored: the demand-side twin of slide 5's axis. I = FLOP per byte moved · MLP = memory-level parallelism.

  Optionally, add to s05 annotation 1 or the caption: "Objects are placed by matching their access density (slide 6)."

**M10a · major · The row label "3D DRAM-on-logic · d-Matrix Raptor card" gets Raptor's stacking wrong.**
- **What is wrong.** Raptor is *logic on top of DRAM*: explainer review_r1 #6 and review_r2 table, "logic on top, F2F 36 µm [V s17]". BRIEF s03 lists "Raptor is DRAM-on-logic" as do-not-say. BRIEF s05's own row spec contradicts that, and SYNTHESIS §2 inherits the error.
- **Fix.**
  - Use the colour-semantics name "3D DRAM–logic stack" (hybrid/F2F bonded) on s05 (row label) and s01 (schematic label, point 2).
  - Keep "DRAM-on-logic" only for SeDRAM.
  - Flag to the orchestrator that BRIEF §D s05 row 1 and SYNTHESIS §2 need the same fix.

**m10 · minor · The headline drops B2.** "B buys cheaper GB" omits sharing, while s01 says "cheaper or shared GB". Fix (16 words): "Each tier bundles bandwidth with capacity: A buys GB/s per GB; B, cheaper or shared GB."

**m11 · minor · Annotation 3 rebuts a claim nobody made.** "DDR is squeezed, not unstable" answers a straw man; "unstable" appears nowhere earlier. Fix: "DDR is squeezed: on one bus, bigger DIMMs dilute GB/s per GB."

**m12 · minor · The $/GB axis is the weaker axis of the frontier, and the slide doesn't say so.**
- **What is wrong.** The frontier has two axes, but only one is plotted. The $/GB column is mostly "—", "n/a" or "unverified". That was the right choice (G2), but the audience should hear it.
- **Fix.** Append to the sub: "$/GB is mostly secondary or unverified, so it is tabulated, not plotted."

**m13 · minor · The RDMA latency range is inconsistent with s02 and mixes transfer sizes.**
- **What is wrong.** s05 gives "~3.5–3.9 µs". s02 shows 64 B reads at 3.55 and 4.55 µs and 4 KB pages at 3.9 µs. So the s05 range mixes transfer sizes and drops 4.55 µs.
- **Fix.** "~3.5–4.6 µs (64 B–4 KB)", or refer to s02.

**m14 · minor · The HBM-on-CPU row's "discontinued" invites a causal reading.**
- **What is wrong.** s10 refutation row 2 ("The middle did not catch up") assumes the middle *caused* CPU HBM's exit. SYNTHESIS §2 obs. 2 lists four competing explanations and calls the pattern "consistent with … not proof".
- **Fix.** On s10, set "Would mean" to "A fast middle is not why CPU HBM stopped". The middle numerically *did* catch up, so "did not catch up" is not a refutable statement.

### s06 · Skew and placement

**M9 · major · The headline's "so … beats any single tier" has no evidence on this slide, and "per object" is not what the chart shows.**
- **What is wrong.**
  - The chart shows skew across bytes *within* workloads. The "beats" evidence (20–33% moved at ≤1–5% slowdown) is on s04.
  - The point that matters (SYNTHESIS: "skewed per object, not per workload") is that you cannot place a whole workload on one tier.
- **Fix.**
  - Headline (15 words): "Access is skewed within each workload, so a mix of tiers beats any single tier."
  - Add the M2 production line under the table.

**M10 · major · The "hot head → A" bracket sits over CPU-cloud data whose hot head stays in local DDR.**
- **What is wrong.**
  - Meta services, Google fleet and TPP are CPU servers; their hot pages go to DDR, not HBM.
  - The bracket contradicts the table's DDR row and the "middle persists" thesis.
  - The 15–45% band is unlabelled, so the middle disappears from the picture.
- **Fix.** Use three brackets:
  - "hot head → A (accelerators) or local DDR (CPUs)"
  - "warm → DDR"
  - "cold tail → B"
- **Alternative.** Label only "hot head" / "cold tail" and let the table assign tiers.

**m15 · minor · The three "y = 100 by definition" points are close to tautological.**
- **What is wrong.** Google, TPP and Mooncake are cold fractions restated as "the rest carries 100%". A sceptical researcher will notice.
- **Fix.** The caption already says "restates". Add "three are cold-fraction statistics, plotted at y = 100" so it doesn't look like a gotcha.

### s07 · Deployed vs ambitious

**M11 · major · The headline's key claim, "no new programming model", is not evidenced on the slide.**
- **What is wrong.** The cells list Driver / Best evidence / Main cost; none mentions software. The SYNTHESIS §4 "Pattern" is the source [Syn], but the audience sees no support. A2's "no VM or coherence" is the only software-related item, and it sits under Main cost.
- **Fix, option A (preferred).** Add a fourth labelled line, **Software**, to each cell:
  - A1: "unchanged (HBM is device memory)"
  - B1: "unchanged (OS tiering, zswap)"
  - A2: "new kernels/runtime; no VM or coherence" (move it out of Main cost)
  - B2: "fabric manager; KV-pool runtime (Mooncake)"

  Sources: SYNTHESIS §4 Pattern [Syn]; TPP and zswap are kernel mechanisms [P].
- **Fix, option B.** Reword the headline to what the grid shows (12 words): "Deployed forms are transparent to software; compute-at-memory and cross-host sharing remain niche." This still needs option A's evidence.

**M7 · major · B1 Main cost "+~100–270 ns (meas.)" contradicts s02's "+~115–280 ns measured" (ORCH item not fixed).** Fix: "+~115–280 ns".

**m16 · minor · Hand-offs are only implicit.**
- **What is wrong.** BRIEF §A.2 defines one hand-off per slide, but none is rendered. A standalone reader (the deck will be shared as an Artifact) loses the "why next" thread.
- **Fix.** Make the kickers carry direction (see s03/s04). Optionally add a `.tiny` right-aligned "Next: …" line above the footer on s02, s05 and s07, where the turn is least obvious:
  - s02: "Next: what A and B buy"
  - s05: "Next: why mix tiers?"
  - s07: "Next: are A and B independent?"

**m17 · minor · The legend line "D our arithmetic · spec product spec" reads as garbled.** Fix: "D our arithmetic · spec = product spec". Also, the superscript qualifiers mix styles ("meas." superscript vs "(sim.)" inline); pick one.

**m18 · minor (optional content) · The Optane lesson is missing.** SYNTHESIS §4 B1: "A B1 tier must be cheaper to *make*, not just to sell" (Optane, $559M impairment [P]). This is the most memorable line for a memory-industry audience. It fits the B1 Main cost or the report.

### s08 · Coupling

**M12 · major · The "A ↔ B share wafers" column never says what wafer competition does to B, and leaves out the hedge.**
- **What is wrong.**
  - The column states the ratio and "one of three named drivers of the 2026 shortage", then stops.
  - SYNTHESIS §5.2 gives the crux: the shortage *amplifies* B's payoff (mainly for tiers that need no new wafers, i.e. recycled DDR4 and compression), but did not cause B, since B1 designs predate it.
  - Without this, "coupled" reads as mere co-occurrence, and the BRIEF's own do-not-say ("CXL adoption is driven by the shortage") is left for the audience to infer, possibly wrongly.
- **Fix.** Col 2 text (≈27 words):
  > ~3× the wafer per bit (HBM3E), >4:1 expected (HBM4E): one of three named drivers of the 2026 shortage. That raises B's payoff (recycled DDR4, compression); it did not cause B.

**m19 · minor · Col 1 text doesn't state the mechanism.**
- **What is wrong.** "d-Matrix and the training stacks pair tiers the same way" names examples but not why.
- **Fix.** "Moving closer trades capacity for bandwidth, so a capacity tier follows; d-Matrix and training stacks do the same." (18 words; it is SYNTHESIS §5.1's first sentence.)

### s09 · Why both, why now

**M13 · major · The decline rates conflict within s09 and with s04.**
- **What is wrong.**
  - The chart labels are our nominal fits: "−37%/yr" (1990–2010) and "−12%/yr" (2010–2024).
  - The table on the same slide says "−33–36%/yr" and "−12–15%/yr". s04 box 1 and the s04 headline's "~2.5×" use the published fits.
  - Implied slowdown: chart 37/12 ≈ 3.1×; headline ≈ 2.5×.
  - `data/dram_price_trend.json` says "Deck uses the published −36". The slide doesn't.
- **Fix, preferred.** Draw the published-rate segments, labelled "−33 to −36%/yr" and "−12 to −15%/yr (AI Impacts, real $)". Put our nominal fits in the tooltip.
- **Fix, alternative.** Keep our fits, but label them "nominal fit: −37%/yr" and add to the caption: "published (real-$) rates −33 to −36 and −12 to −15%/yr; both bracket the ~2.5–3× slowdown". Then change s04's "~2.5×" to "~2.5–3×".

**m20 · minor · The headline presents a synthesis claim as fact.**
- **What is wrong.** "Commodity DRAM let the middle outrun both" is [Syn]. The NDP'14 quote supports only the PIM wave; network RAM's kill condition was "it only beat disk". The footer marks [Syn], which is acceptable, but a hedge reads better to researchers.
- **Fix (15 words).** "The 1990s had the same split; cheap commodity DRAM let the middle outrun both—until ~2011."
- **Also.** In the table's "Middle" cell, quote the first clause too, per the BRIEF do-not-say: "not for lack of performance: commodity machines 'outran them'".

**m21 · minor · "Stranding" appears only here (B row, since ~2011) and is never defined.** s04 uses "idle" and "cold". Fix: "DRAM $/GB, idle memory, capacity per core".

### s10 · Implications

**M1 (above)** Add "Rival explanations, weighed" in the empty right-column space.

**m22 · minor · "Passive HBM" is a new term.** It appears here for the first time. Fix: "HBM without in-stack compute, on logic base dies (A1)".

**m23 · minor · The deck has no closing restatement of the answer.** The deck opens with the answer but ends on predictions. If M1's box doesn't fit, the alternative is a one-line bottom line under the left table's callout:
> Both directions leave DDR's narrow spot; skew makes the mix pay; the middle persists.

Don't add both, or the slide will be overloaded.

---

## 4. Terminology check

| Term | Status |
|---|---|
| A / B | Consistent: defined on s01 and used throughout |
| A1 / A2 / B1 / B2 | Introduced on s07, reused on s10. s08's content is A1-specific but unlabelled (acceptable) |
| "frontier" | s01, s05 kicker, s10: consistent. s05 itself is a 1-D projection; see m12 |
| "access density" | **Absent.** ρ is defined on s06 without the name and without its link to s05's axis (M9b) |
| "3D DRAM-on-logic" | Wrong for Raptor (M10a). Use "3D DRAM–logic stack" |
| "far tier" / "capacity tier" / "far memory" | Used interchangeably on s02, s08 and s10; acceptable because s02 defines "far" as a role |
| "passive HBM", "stranding" | Undefined one-offs (m21, m22) |
| CXL adder | s02 +~115–280 vs s07 +~100–270 (M7) |
| NVMe latency | s02 10–40 vs s05 10–50 µs (M8) |
| $/bit decline rates | s04 and s09 table 33–36 / 12–15 vs s09 chart 37 / 12 (M13) |

---

## 5. Major items, one line each

- **M1 (s10/deck):** Rival explanations (markets, vendor incentives) are missing; add a "Rival explanations, weighed" box on s10.
- **M2 (s06/deck):** "The DDR middle persists" lacks evidence; add the line "DDR still holds 50–75% of capacity" (App. A).
- **M2b (s02):** "switch ≥220 ns" lacks its V tag in the callout and chart bracket.
- **M3 (s07):** PIM's weak baselines are unacknowledged; add "Newton 54× vs GPU, 10× vs ideal".
- **M4 (s01):** The opening answer omits the pressures, coupling and why-now; rewrite the points.
- **M5 (s01):** "Measured positions: slide 5" is wrong; s05 is spec-derived.
- **M6 (s03):** The slide never links bandwidth pressure to A; reword the headline and kicker.
- **M7 (s07):** CXL adder +~100–270 vs +~115–280 (ORCH, not fixed).
- **M7a (s04):** The headline "Capacity got costly" is literally untrue ($/bit still fell); reword.
- **M8 (s05):** NVMe 10–50 µs vs 10–40 µs measured (ORCH, not fixed).
- **M9 (s06):** The "beats any single tier" evidence is off-slide; use "per object" → "within each workload".
- **M9b (s05/s06):** Supplied GB/s per GB and demanded ρ are never linked; name "access density".
- **M10 (s06):** The "hot head → A" bracket sits over CPU data; add a DDR/warm bracket.
- **M10a (s05/s01):** "3D DRAM-on-logic" is wrong for Raptor (logic on top).
- **M11 (s07):** "No new programming model" is unevidenced on the slide; add a Software line.
- **M12 (s08):** The wafer column omits the effect on B and the "did not cause B" hedge.
- **M13 (s09/s04):** −37%/yr chart fit vs −33–36%/yr table and s04's ~2.5×.

