# Round 2: narrative and editorial review (fresh eyes)

*Reviewer: narrative/editorial, round 2, 2026-10-03.*

*Inputs:*
- shots/s01–s10.png (render of 23:12) and slides/s01–s10.html, with visible text extracted
- BRIEF.md §A, §B and the §D do-not-say lists
- reviews/r1_narrative.md and r1_decisions.md
- SYNTHESIS_v1.4.md, diffed against v1.3: the only change is the CXL adder, now +~100–280 ns
- data/dram_price_trend.json and notes/r2c_history.md, for the NDP'14 quote

**Verdict: needs revision (light).** Blocking 0 · Major 3 · Minor 14.

The deck now answers the research question in 10 slides with a clean arc:
- s01: proxy → s02: two pressures (s03, s04) → s05: one frontier → s06: skew makes a mix pay → s07: which forms work → s08: coupling → s09: why now → s10: implications and refutations.
- Each headline carries one supported claim. All are ≤16 words; I counted them: 15, 14, 16, 16, 16, 15, 13, 16, 15, 14.
- All 17 round-1 narrative majors are resolved.

What is left is concentrated in three places:
- **s08:** the slide is over the word budget, and one qualifier ("B's cheapest forms") is wrong.
- **SYNTHESIS v1.4:** it still contradicts several corrections the deck has already made.
- **Polish:** a handful of terminology and wording items.

---

## 1. Round-1 majors: verification

| r1 item | Status | Evidence in current render |
|---|---|---|
| M1 rival explanations | **Resolved** | s10 box "Rival explanations, weighed" (wording polish: m9 below) |
| M2 DDR middle evidence | **Resolved** | s06 line "…DDR still holds 50–75% of capacity" + footer sources (redundancy with s04: m6) |
| M2b switch V tag | **Resolved** | s02 callout and bracket both read "≥220 ns more^V"; footer "XConn via Octopus ref. [50] [V]" |
| M3 PIM weak baselines | **Resolved** | s07 A2 "Newton 54× vs GPU, only 10× vs ideal non-PIM ^sim." (placement: m7) |
| M4 s01 answer points | **Resolved** | Four points: proxy; A↔bandwidth / B↔capacity cost; skew and middle; coupling and why now |
| M5 "Measured positions" | **Resolved** | "Spec-derived positions: slide 5"; footer "values on slides 2–5" |
| M6 s03 → A link | **Resolved** | Kicker "Pressure 1: bandwidth → A"; new headline (tension with the takeaway: m3) |
| M7 CXL adder | **Resolved** | s02 callout, s02 bracket and s07 B1 all read +~100–280 ns; matches SYNTHESIS v1.4 §1, App. A |
| M7a s04 headline | **Resolved** | "Capacity cost now bites: $/bit declines slowed ~2.5×…" |
| M8 NVMe | **Resolved** | s02 10–40 µs; s05 "10–40 µs measured" |
| M9 s06 headline and evidence | **Resolved** | "skewed within each workload…"; production line under table |
| M9b access density | **Resolved** | s06 legend names ρ as access density, "the demand-side twin of slide 5's axis"; s05 caption points to slide 6 |
| M10 s06 brackets | **Resolved** | "hot head → A (accel.) or local DDR (CPU)" · "warm → DDR" · "cold tail → B" |
| M10a Raptor stacking | **Resolved in deck** | s05 row "3D DRAM–logic stack"; SYNTHESIS still wrong (R2-M3) |
| M11 Software line | **Resolved** | s07: a Software row in each of the four cells |
| M12 wafer → B | **Resolved, but new wording error** | "raises the payoff of B's cheapest forms" (R2-M2) |
| M13 decline rates | **Resolved** | s09 chart labels say "nominal-dollar fit"; note under table distinguishes them (wording: m11) |

---

## 2. Majors

**R2-M1 · s08 · major · Body text is ~85 words, about 40% over budget (BRIEF §B.2).**
- **Count.** Body text excluding captions, legends and the "≈43 vs ≈0.82" figure label:
  - col 1: 19 words
  - col 2: 35 words
  - col 3: 31 words
  - total: **85**
- **Redundancy.** "the training stacks" (col 1) repeats col 3's "ZeRO, MemoryX".
- **Fix: replace the three text blocks** (≈64 words in total).
  - **Col 1** (12 words), replacing "Moving closer trades… do the same.":
    > Closer trades capacity for bandwidth, so a capacity tier follows (d-Matrix too).
  - **Col 2** (23 words), replacing the paragraph below the wafer chart; this also fixes R2-M2:
    > HBM3E takes ~3× the wafer per bit (HBM4E >4:1), a named 2026-shortage driver. That favours wafer-free B (recycled DDR4, compression); it did not cause B.
  - **Col 3** (29 words), as two bullets plus the catch line:
    > - Deployed only in training (optimizer at the capacity tier: ZeRO, MemoryX).
    > - Elsewhere, simulations or early products (CXL-PNM, BEACON, XCENA^V).
    >
    > Catch: controller compute sees channel bandwidth (~3–17× the link), not bank bandwidth.
- **Accuracy notes.**
  - "Channel bandwidth (~3–17× the link)" restates SYNTHESIS §5.3, "~3× the link for DDR5 controllers, ~17× for LPDDR5X CXL-PNM", more exactly than "~3–17× its link bandwidth".
  - Calling XCENA a "prototype" is wrong. XCENA MX1 is a vendor product claim (SYNTHESIS §5.3 "Products and prototypes"), and BEACON is a simulation, not a prototype. "Simulations or early products" fixes both.
- **If ~64 words is still too many:** drop "(d-Matrix too)" from col 1; the footer still cites d-Matrix HC'26 s13, s18. That gives ≈60.

**R2-M2 · s08 col 2 · major · "raises the payoff of B's cheapest forms" uses the wrong criterion.**
- **What is wrong.** SYNTHESIS v1.4 §5.2, "Who benefits":
  - "Only B tiers that need no leading-edge wafers gain relative ground: recycled DDR4 … and compression."
  - "New-DDR5 CXL and SSD offload are not spared: NAND rose +55–60% QoQ in 1Q26."
- **Why it matters.** SSD offload is B's *cheapest* form per GB, so the slide's wording implies the opposite of the source.
- **BRIEF.** The s08 do-not-say lists "SSD offload escapes the shortage".
- **Fix.** "That favours wafer-free B (recycled DDR4, compression); it did not cause B." This is already folded into R2-M1. If R2-M1 is not adopted, change only "B's cheapest forms" → "wafer-free B (recycled DDR4, compression)".

**R2-M3 · SYNTHESIS v1.4 (affects deck footers) · major · The reviewed answer still contradicts corrections the deck already made, and the deck footers cite v1.3.**
- **What is wrong.**
  - The deck is right in each case below (r1_decisions G-2/G-3/G-4).
  - Every deck footer that says "SYNTHESIS v1.3" points the reader to a document that disagrees with the slide.
  - v1.4 is now the current version.

| SYNTHESIS v1.4 location | Says | Deck (correct) says |
|---|---|---|
| Exec. summary l.10; §8 Open Q4 l.388 | "3D DRAM-on-logic" / "DRAM-on-logic's capacity cost" | "3D DRAM–logic stacking" (Raptor is logic on top) |
| §2 table row l.116 | "Hybrid-bonded DRAM-on-logic (d-Matrix Raptor; SeDRAM)" | s05 "3D DRAM–logic stack" |
| §2 table NVMe l.121 | "10–50 µs [P]" | s02, s05: 10–40 µs measured |
| §2 table DDR5 l.119; App. A l.406 | "80–120 ns" | s05: ~100–150 ns measured (G-4) |
| §2 footnote l.125 | RDMA "~3.5–3.9 µs per 4 KB [P]" | s05: ~3.5–4.6 µs (64 B–4 KB), [D, assumption-based] |
| §4 B2 l.253 | "Octopus: switches add ≥220 ns [P]" | s02, s07: ≥220 ns [V] (XConn via Octopus) |

- **Fix (orchestrator, not slide builders).**
  - Issue SYNTHESIS v1.5 with these six edits.
  - Then change the footers to "SYNTHESIS v1.5" in five places:
    - s01 footer
    - s06 footer ("placement: SYNTHESIS v1.3 §3")
    - s07 footer
    - s10 footer, twice
  - If no v1.5 is planned, cite v1.4 and add to the s01 footer: "deck values supersede SYNTHESIS where they differ (r1_decisions G-2–G-4)."

---

## 3. Minors (per slide)

### s01
**m1 · Point 4: "cheap DRAM no longer lets the middle outrun both".**
- **What is wrong.** DRAM is still cheap. What changed is the *rate* of decline (s04, s09), so the wording misstates the mechanism.
- **Fix (17 words).** "The directions are coupled, and since ~2011 slower $/bit declines no longer let the middle outrun both."

**m2 · Point 2 says "3D DRAM–logic"; the schematic on the same slide says "3D DRAM–logic stacking" (G-3 term).**
- **Fix.** "(HBM, 3D DRAM–logic stacking, PIM)". This adds 1 word; body goes from 63 to 64. To compensate, cut "the variable being optimized" to "what is optimized" in point 1 (SYNTHESIS wording); body becomes 62.

### s03
**m3 · The headline "…until the die edge" sits uneasily with the takeaway on the same slide.**
- **What is wrong.**
  - The takeaway says "3D bonding removes the edge limit", and SYNTHESIS §2.0 says "each step closer relaxes the currently binding limit and exposes the next … This is why the 'closer' direction does not stop at HBM."
  - Read alone, the headline implies A ends at the die edge, which undercuts the hand-off to 3D stacking and A2 (s07).
- **Status.** The headline is approved in r1_decisions; this is a recommendation only.
- **Fix (16 words).** "Bandwidth must cross a boundary; each step closer buys wires and pJ/bit until the next limit binds."

**m4 · Takeaway: "3D bonding" is a fourth name for direction A's stacking.** Fix: "3D DRAM–logic stacking removes the edge limit and cuts interface energy 2–4×, not core energy (→ PIM, slide 7)."

### s05
**m5 · The row label "3D DRAM–logic stack" deviates from the G-3 term.**
- **Fix.** Use "3D DRAM–logic stacking" (row sublabel unchanged: "d-Matrix Raptor card (32 GB)").
- **Alternative.** Ratify "stack" for tier-name contexts in G-3. Do one or the other, so the deck has one term.

### s06
**m6 · The production line repeats s04's "Captured in production" box almost verbatim.**
- **What is wrong.** Both read "20–33% of DRAM … at ≤1–5% slowdown". s06 is where the evidence for "a mix beats any single tier" belongs; on s04 it adds a fourth box to a capacity-cost argument that already stands without it.
- **Fix, preferred.** Keep the s06 line and make it point back: "Mixing pays: production moves 20–33% of DRAM off DDR at ≤1–5% slowdown (slide 4), yet DDR still holds 50–75% of capacity." (20 words; s06 body goes from 37 to 41.)
- **Alternative.** Delete s04's fourth box. s04 body goes from 56 to 43; the s04 footer keeps the zswap/TMTS/TPP sources only if s06 cites them.

### s07
**m7 · The Newton weak-baseline caveat sits under "Main cost", but it is an evidence-quality caveat, not a cost.**
- **Fix.** Move it to the end of A2 "Best evidence": "…; gains shrink vs strong baselines (Newton 54× vs GPU, 10× vs ideal ^sim.)". Main cost becomes "HBM2-PIM stack 6 vs 8 GB ^spec; DRAM-process logic; heat".
- **Effect.** This also balances the cell heights; the A2 Main cost line currently wraps to 2 lines.

**m8 · Optional: the Optane lesson is still absent.** r1 m18 was optional; I flag it again because it is the line a memory-industry audience remembers. If B1 Main cost has room after R2-M1-style trimming, append: "must be cheaper to *make*, not just sell (Optane)". Otherwise put it in report.html.

### s08
**m8b · "far tier" vs "capacity tier" in one box.**
- **What is wrong.** The col 3 header says "compute at the far tier"; the body says "optimizer runs at the capacity tier". s02 established that far is a role, so these are the same thing under two names, side by side.
- **Fix.** Change the header to "B → compute at the far (capacity) tier", or use "far tier" in the body. The R2-M1 col 3 text keeps "capacity tier", so pair it with this header.

### s09
**m9 · The "Middle" cell still quotes NDP'14 without its first clause, which BRIEF s09's do-not-say forbids.**
- **What is wrong.**
  - The full sentence (NDP'14 sidebar, IEEE Micro 34(4) p.39, notes/r2c_history.md l.185) is: "These machines did not fade away because of lack of performance gains or energy efficiency, but simply because Moore's law allowed the basic von Neumann machines to outrun them with economic affordability."
  - It refers to PIM machines (DIVA, Terasys, EXECUBE) only.
- **Fix (cell text).** `$/bit −33–36%/yr. PIM "did not fade away because of lack of performance gains or energy efficiency"; commodity machines could "outrun them with economic affordability" (NDP'14).`
- **Footer.** Footer already marks "middle outran both" [Syn], which covers the extension to network RAM.

**m10 · The 1990s "New enablers: none" overstates.**
- **What is wrong.** IRAM depended on merged DRAM–logic processes; that was an enabler, and it became a kill condition (SYNTHESIS §6).
- **Fix.** "— (merged DRAM–logic process was a handicap)". Or simply "—".

**m11 · "Table rates: … CPI-adjusted (2020$). Chart fits: nominal dollars." implies the gap is inflation.**
- **What is wrong.** Inflation would make real-dollar declines *steeper* than nominal, yet the table (−33–36) is shallower than the chart (−37). The real cause is different fit windows and datasets (dram_price_trend.json cross_check: "AI Impacts fit window not stated"). A careful reader will catch the inversion.
- **Fix.** "Table: published AI Impacts fits (2020$; windows and datasets differ). Chart: our nominal-dollar fits, 1990–2010 and 2010–24."

### s10
**m12 · Rival box, "Different markets: true, and compatible: markets differ…": double colon, and the rebuttal is asserted, not evidenced.**
- **What is wrong.** SYNTHESIS §7(a)'s actual evidence is that single workloads need both at once.
- **Fix (19 words).** "**Different markets:** first-order, yet single workloads (LLM serving, GB200) use both, so markets differ because their binding constraint differs."
- **Fit.** The box has room; s10 body goes from 52 to ~58.

**m13 · Footer: "Octopus" is listed as evidence that A2/B2 economics are "mostly simulated or modelled".** Octopus's latency figures are measured, and the ≥220 ns switch value is [V]. Fix: "(AttAcc, CXL-PNM, Berger, Pond)". Pond is simulated, which fits the claim.

---

## 4. Density (body ≤ ~60 words; captions, legends, tables and figure labels excluded)

| Slide | Words | Note |
|---|---|---|
| s01 | 63 | OK (62 after m2) |
| s02 | 57 | OK |
| s03 | 61 | OK; panel (a) label + panel (b) statement + takeaway |
| s04 | 56 | OK (43 if m6 alternative) |
| s05 | 52 | OK |
| s06 | 37 | OK |
| s07 | 39 | Plus the 2×2 grid (~150 words), which counts as a table. It is the densest *visual* slide, but every line is evidence. Leave it. |
| **s08** | **85** | **Over. R2-M1 brings it to ≈64, or ≈60 without "(d-Matrix too)".** |
| s09 | 28 | OK |
| s10 | 52 | OK (~58 after m12) |

---

## 5. Terminology

| Term | Status | Action |
|---|---|---|
| A / B | Consistent: s01 sub defines them; s07 column heads "A · closer", "B · farther" | none |
| A1/A2/B1/B2 | Introduced on s07, reused on s10 with the cell labels in brackets | none |
| access density (ρ) | Named on s06 legend; s05 caption points to it | none |
| 3D DRAM–logic stacking | s01 schematic ✓; s01 point 2 "3D DRAM–logic" ✗; s05 "3D DRAM–logic stack" ~; s03 "3D bonding" ✗; SYNTHESIS v1.4 "DRAM-on-logic" ✗ | m2, m4, m5, R2-M3 |
| CXL adder +~100–280 ns | s02, s07, SYNTHESIS v1.4 all agree | none |
| far tier / capacity tier / "outward" | Mixed within s08 col 3 | m8b |
| middle / DDR middle | Consistent | none |
| "passive HBM" | Gone (s10 says "HBM without in-stack compute") | none |
| "stranding" | Gone (s09 says "idle memory") | none |

---

## 6. Faithfulness to SYNTHESIS v1.4

**Covered.**
- **Executive summary:** every element appears.
  - proxy (s02)
  - two pressures (s03, s04)
  - frontier (s05)
  - skew / mix / middle (s06)
  - 2×2 (s07)
  - three couplings (s08)
  - why now (s09)
  - rivals (a) and (b), confidence, implications and refutations (s10)
- **Hedges carried over:**
  - "consistent with … not proof" for CPU HBM (s10 row 2)
  - shortage "did not cause B" (s08)
  - Newton baselines (s07)

**Deliberate omissions (acceptable).**
- §3 economic rules (five-minute rule, Hao)
- §5.1 GPU scale-up domains as disaggregation inside A
- §7(c) different communities
- Optane lesson (m8)

**Divergences.** All six are SYNTHESIS-side errors that the deck already corrects (R2-M3).

---

## 7. Majors, one line each
- **R2-M1 (s08):** Body ~85 words against a ~60 budget. Replace the three text blocks with the ≈64-word rewrite (≈60 if "(d-Matrix too)" is cut). This also stops calling XCENA a prototype.
- **R2-M2 (s08):** "raises the payoff of B's cheapest forms" contradicts SYNTHESIS §5.2: SSD offload is not spared. Use "favours wafer-free B (recycled DDR4, compression)".
- **R2-M3 (SYNTHESIS v1.4 / footers):** v1.4 still says DRAM-on-logic for Raptor, NVMe 10–50 µs, DDR 80–120 ns, RDMA 3.5–3.9 µs and Octopus switch [P]. Issue v1.5 and update the 5 footer citations from v1.3.
