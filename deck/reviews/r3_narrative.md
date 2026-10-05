# Round 3: narrative and editorial review (fresh eyes)

*Reviewer: narrative/editorial, round 3, 2026-10-03.*

*Inputs:*
- shots/s01–s10.png (render of 23:23) and slides/s01–s10.html (visible text extracted and counted)
- BRIEF.md §A, §B and the §D do-not-say lists
- r1_decisions.md and r2_decisions.md (binding); r2_narrative.md (checked item by item); ORCH_NOTES.md
- SYNTHESIS_v1.7.md (diffed against v1.6: four wording fixes, none of which touch the deck's claims)
- notes/q4_workloads.md, notes/q2_disaggregation.md and notes/r2d_review.md (TMTS and zswap mechanisms)

**Verdict: needs revision (light).** Blocking 0 · Major 2 · Minor 9.

The deck answers the research question convincingly in 10 slides.
- **Arc.** s01 gives the answer first. s02 sets distance aside as a proxy. s03 and s04 give the two pressures, labelled → A and → B. s05 puts them on one frontier, and s06 shows why a mix pays. s07 says which forms work, s08 shows the coupling, s09 explains why now, and s10 gives implications and refutations.
- **Headlines.** Each headline carries one supported claim. Nine are ≤16 words; s03 has 17 (m1).
- **Earlier rounds.** Every round-2 narrative item is resolved or applied. SYNTHESIS v1.7 now agrees with the deck on all six points R2-M3 raised.

Two problems remain, and each is one sentence on a slide:
- **s06:** the production-tiering line says data moves "off DDR", which is wrong for zswap and TMTS.
- **s07:** the "Pattern" line says the ambitious cells "reach volume", which contradicts the headline and both cell badges.

---

## 1. Round-2 items: verification

| r2 item | Status | Evidence in current render |
|---|---|---|
| R2-M1 s08 three-block rewrite | **Applied, but grew** | Text matches the rewrite, but the qualifiers added since ("both sim.", "a vendor product claim", "only … -level") bring the body to ~74 words against the ~60–64 target (m2) |
| R2-M2 "B's cheapest forms" | **Resolved** | "favours wafer-free B (recycled DDR4, compression); it did not cause B" |
| R2-M3 SYNTHESIS contradictions | **Resolved** | v1.7 has "3D DRAM–logic stacking" (Raptor logic on top), NVMe 10–40 µs, DDR5 ~100–150 ns, RDMA 3.5–4.6 µs and switch ≥220 ns [V]. Footers cite v1.6, which ORCH_NOTES accepts |
| m1 s01 point 4 | **Resolved** | "…since ~2011 slower $/bit declines no longer let the middle outrun both." |
| m2 s01 point 2 term | **Resolved** | "3D DRAM–logic stacking" |
| m3 s03 headline | **Resolved, but 17 words** | "…each step closer buys wires and pJ/bit until the next limit binds." (m1) |
| m4 s03 "3D bonding" | **Resolved** | Takeaway says "3D DRAM–logic stacking" |
| m5 s05 row label | **Resolved** | "3D DRAM–logic stacking" |
| m6 s04/s06 redundancy | **Resolved** | s04 box 4 is now a pointer to slide 6 |
| m7 s07 Newton placement | **Resolved** | Newton caveat ends A2 Best evidence; Main cost is one line |
| m8 Optane lesson | **Resolved** | B1 Main cost: "Optane: a cheaper tier must be cheaper to make" |
| m8b s08 far/capacity | **Resolved** | Header: "B → compute at the far (capacity) tier" |
| m9 NDP'14 quote | **Resolved** | Full first clause quoted; scoped to PIM |
| m10 1990s enablers | **Resolved** | "—" |
| m11 s09 table note | **Resolved** | "Table: published AI Impacts fits (2020$; windows and datasets differ)…" |
| m12 s10 rival box | **Resolved** | "first-order, yet single workloads (LLM serving, GB200) use both…" |
| m13 s10 footer list | **Resolved** | "(AttAcc, CXL-PNM, Berger, Pond)" |

---

## 2. Majors

**R3-M1 · s06 · major · "~20–25% of DRAM moves off DDR in production (zswap, TMTS, Vistara)" misstates two of the three cited mechanisms.**

- **Element.** The `.s06-line` paragraph: "Mixing pays: ~20–25% of DRAM moves off DDR in production (zswap, TMTS, Vistara) at <5% slowdown, 33% in TPP's pre-production CXL tests; DDR still holds 50–75% of capacity."
- **What is wrong.**
  - **zswap.** zswap compresses cold pages *within the same DDR DRAM*. Nothing moves off DDR.
  - **TMTS.** TMTS's second tier is Optane DIMMs on the DDR bus. notes/q4_workloads.md l.128 says "tier2 = Optane variant", and notes/r2d_review.md l.49 says "Google's tier-2 was Optane DIMMs on the DDR bus".
  - **Earlier review.** notes/r2d_review.md l.258 already marked the claim that capacity "can only be added off the DDR bus" as *contradicted*, citing zswap and TMTS.
  - **Only Vistara** (recycled DDR4 behind CXL) actually leaves the local DDR bus, and even its tier is DDR media.
- **Why it matters.**
  - A memory-industry reader will spot "zswap moves data off DDR" at once.
  - The sentence also blurs the deck's own term "DDR" (the middle, the local DIMM bus) with DDR media.
  - This is the line that carries the headline's "a mix of tiers beats any single tier".
- **Source wording.** SYNTHESIS v1.7 says "Production systems move ~20–25% of DRAM to *cheaper tiers*" (exec. summary, Capacity-cost row). G2-1 says "moved to *slower tiers*". Neither says "off DDR". The r2_decisions s06 line introduced "off DDR".
- **Fix** (28 words, shorter than the current 33):
  > Mixing pays: production moves ~20–25% of DRAM to cheaper tiers (zswap, TMTS's Optane, Vistara's CXL) at <5% slowdown, 33% in TPP's pre-production CXL tests; local DRAM keeps 50–75%.
- **Footer.** Change "DDR share:" to "local-DRAM share:"; the sources stay the same.

**R3-M2 · s07 · major · The Pattern callout says the ambitious cells "reach volume"; the headline and both badges say they are niche.**

- **Element.** The callout under the 2×2: "Pattern: ambitious cells reach volume only inside vendor-controlled stacks or for bulk, predictable data."
- **What is wrong.**
  - The headline on the same slide says "the ambitious forms remain niche".
  - The A2 badge says "Demos and niche"; the B2 badge says "Niche (KV pools, prototypes)".
  - No A2 product has reached volume anywhere. SYNTHESIS v1.7 §4 A2 Status: "Niche. HMC is dead. HBM-PIM is at demo stage."
  - So "reach volume … inside vendor-controlled stacks" implies an A2 deployment the evidence does not contain. A reader who compares the callout with the headline sees a contradiction.
- **Source.** The wording copies SYNTHESIS v1.7 §4 Pattern l.263 ("reached volume only where…"), so the synthesis has the same flaw (see m9).
- **Fix** (same length; the second sentence is unchanged):
  > Pattern: ambitious forms find use only inside vendor-controlled stacks (PIM demos) or on bulk, predictable data (KV pools). The critiques mirror each other: A2 is limited by process, heat and capacity; B2 by latency and switch cost.
- **Consistency with s10.** This matches s10's prediction, "PIM (A2) and pooling (B2) stay niche outside vertically integrated AI stacks".

---

## 3. Minors

**m1 · s03 · headline is 17 words (BRIEF §B.1 ≤16), and "buys … pJ/bit" reads backwards.**
- **Count.** The r2-approved text has 17 words. r2_narrative m3 counted 16.
- **Ambiguity.** "Buys pJ/bit" can be read as *spending* energy, but the claim is that each step closer *lowers* pJ/bit.
- **Fix** (16 words): "Bandwidth must cross a boundary; each step closer adds wires, cuts pJ/bit, until another limit binds."

**m2 · s08 · body ~74 words against the r2 target of ~60–64; the Catch line repeats the new bar chart below it.**
- **Count.** col 1: 12 · col 2: 25 · col 3 bullets: 22 · Catch: 15. Headers and figure labels are excluded.
- **Redundancy.** The right-panel chart already shows 1× / ~3× / ~17×, so the Catch line does not need the numbers.
- **Antecedent problem in col 2.** In "That favours wafer-free B …; it did not cause B", "it" has to mean the shortage, but the shortage is not the subject of the previous sentence.
- **Fix** (≈64 words in total):
  - **Col 1:** "Closer trades capacity for bandwidth, so a capacity tier follows." ("d-Matrix" stays in the footer.)
  - **Col 2:** "HBM3E takes ~3× the wafer per bit (HBM4E >4:1), a named 2026-shortage driver. The shortage favours wafer-free B (recycled DDR4, compression) but did not cause B."
  - **Col 3, bullet 2:** "Elsewhere: simulations (CXL-PNM, BEACON) or vendor claims (XCENA^V)."
  - **Catch:** "Catch: a CXL controller sees channel-level, not bank-level, bandwidth."

**m3 · s02 · the headline "closer is not faster" can be misread; "load latency" is ambiguous.**
- **Headline.** To a memory audience, HBM *is* faster in the bandwidth sense, which is the whole of s03. The chart shows the claim is about latency, but the headline itself does not say so.
- **Headline fix** (14 words): "Distance is a proxy: closer is not lower-latency, and farther costs hops, not metres."
- **Callout.** "load latency" can be read as *loaded* latency, which is a term of art. The source measures pointer-chase latency (notes/r3_gap_evidence.md l.205).
- **Callout fix:** "~20% higher pointer-chase latency from HBM2e than from DDR5".

**m4 · s01, s05 vs s07 · B is described two ways, and s01 lists "CXL" as cheaper GB.**
- **Two descriptions.** s01 and s05 say B buys "cheaper or shared GB"; s07 names B1 "Cheaper or extended bytes" with driver "capacity per core".
- **Contradiction with s05.** New-DDR5 CXL is not cheaper per GB: s05's table gives "new DDR5 ≈1 + controller".
- **Fix, s01 point 2:** "**B** answers capacity cost: cheaper, extra or shared GB (compression, CXL, RDMA pools)." This adds 1 word.
- **s05 headline.** It can stay at 16 words, provided the s05 sub or caption carries the "extended" sense, which it does via the CXL row.

**m5 · Hand-offs are inconsistent.**
- **Current state.** Only s07 has an explicit "Next: are A and B independent? →". The other slides hand off through kickers or "(slide N)" pointers, which work.
- **Problem.** The single "Next:" line looks like an orphan.
- **Fix.** Either remove it from s07, or add matching lines where there is room:
  - s02, under the three callouts: "Next: if not distance, then what? Two pressures (slides 3–4) →". This is the r1 wording, and the right column has space.
  - s08, if m2 frees space: "Next: why now? →".

**m6 · s05 annotation 1 · "It moves down in $/GB." has an unclear antecedent** (Vistara? CXL? DDR?).
- **Fix:** "Deployed CXL is not a new density point: Vistara's tier (0.225) sits at capacity-maxed DDR (0.2). B1 moves down in $/GB instead."

**m7 · s10 · the Evidence column for prediction 3 says "coupling claims", which is a category, not evidence.**
- **Fix:** "GB200, d-Matrix; wafer ratio (slide 8)". This parallels "deployed only in training" in the next row.

**m8 · s02 · faithfulness to SYNTHESIS v1.7 App. A.**
- **What the slide shows.** The hollow estimate "Pond 155–180 ns (modelled)" sits on the *direct-attach* row. Its data source is `berger` Fig. 3 (MHD 2–16 sockets).
- **What v1.7 says.** App. A now attributes 155–180 ns to "8–16-socket multi-headed pools … [P: Berger, modelled]" (≈+75–90 ns over local).
- **Fix.** Relabel it "Berger MHD pools 155–180 ns (modelled)". Consider moving it to the "CXL multi-headed device" row. Both are for the accuracy reviewer to confirm.

**m9 · SYNTHESIS-side (orchestrator, not builders).** If a v1.8 is issued:
- **§4 Pattern l.263:** "reached volume only where…" → "found use only where…". This matches R3-M2.
- **§8 Prediction:** "Passive HBM with logic base dies" → "HBM without in-stack compute, on logic base dies". This matches G-3 and s10.
- **App. A heading** "DDR share of capacity" → "local-DRAM share". This matches R3-M1.

---

## 4. Headline audit (≤16 words, one claim, supported)

| Slide | Words | Claim supported on slide? |
|---|---|---|
| s01 | 15 | Yes: schematic and answer points; values are deferred to s02–s05, as stated |
| s02 | 14 | Yes: latency chart and the Xeon Max callout (wording: m3) |
| s03 | **17** | Yes: panels (a)–(c) and the takeaway (m1) |
| s04 | 16 | Yes: TPP Fig. 3 at 37.1%; 33–36 → 12–15%/yr ≈ 2.2–3.0× |
| s05 | 16 | Yes: density chart and $/GB column |
| s06 | 15 | Yes: skew points and the production line (wording: R3-M1) |
| s07 | 13 | Yes: 2×2 badges and Software rows (callout contradiction: R3-M2) |
| s08 | 16 | Yes: three columns, one coupling each |
| s09 | 15 | Yes: price chart and 1990s / since-2011 table |
| s10 | 14 | Two clauses (implication + refutations). Acceptable: s10 is the close |

## 5. Density (body ≤ ~60 words; tables, captions, legends and figure labels excluded)

| Slide | Words | Note |
|---|---|---|
| s01 | 64 | OK; m4 adds 1 word |
| s02 | 54 | OK |
| s03 | ~67 | Slightly over. The panel (b) sentence repeats the figure's "6 stacks per die / die edge = the limit". Optional cut: "At most ~6 HBM4 stacks fit a reticle die's long edges." (−6 words) |
| s04 | 54 | OK |
| s05 | 52 | OK. The sub is long (31 words) but carries the scope statement |
| s06 | 54 | OK; R3-M1 saves 5 words |
| s07 | 33 + 2×2 | The 2×2 counts as a table. Densest slide, but every line is evidence |
| **s08** | **~74** | Over the r2 target; m2 brings it to ≈64 |
| s09 | 28 | OK |
| s10 | ~59 | OK |

## 6. Terminology

| Term | Status |
|---|---|
| A / B; A1, A2, B1, B2 | Consistent (s01 sub, s07 heads, s10 rows) |
| 3D DRAM–logic stacking | Consistent on s01, s03 and s05, and in SYNTHESIS v1.7 |
| access density ρ | Defined on s06; s05 caption points to it |
| far tier / capacity tier | s08 header "far (capacity) tier"; s10 "far tier". Consistent |
| "DDR" (the middle) vs DDR media | Conflated on the s06 production line (R3-M1) |
| B = "cheaper or shared GB" vs "cheaper or extended bytes" | Two descriptions (m4) |
| HBM without in-stack compute | s10 ✓; SYNTHESIS §8 still says "Passive HBM" (m9) |
| CXL adder +~100–280 ns; switch ≥220 ns [V]; 214–394 ns (394 = FPGA) | Consistent across s02, s05 and s07 |

## 7. Faithfulness to SYNTHESIS v1.7

- **Covered.** Every element of the executive summary appears in the deck:
  - proxy (s02)
  - two pressures (s03, s04)
  - frontier, and the middle improving (s05)
  - skew, mix and placement (s06)
  - the 2×2 and the Optane lesson (s07)
  - the three couplings, including "amplifies, did not cause" (s08)
  - why now, the NDP'14 quote and the forecast caveat (s09)
  - rivals (a) and (b), confidence, implications and refutations (s10)
- **Hedges carried over:**
  - "consistent with … not proof" (s10 row 2)
  - Newton baselines (s07)
  - XCENA [V] and BEACON sim. (s08)
  - TPP pre-production (s06)
- **Acceptable omissions:**
  - §3 economic rules
  - §5.1 GPU scale-up domains
  - §7(c) different communities
  - §2 obs. 1 CXL energy per bit
- **Divergences:**
  - "off DDR" (R3-M1), which goes beyond the synthesis wording
  - "reach volume" (R3-M2), which inherits a synthesis flaw
  - the s02 Pond/Berger label (m8)

## 8. Majors, one line each
- **R3-M1 (s06):** "~20–25% of DRAM moves off DDR (zswap, TMTS, Vistara)" is wrong for zswap (in-DRAM compression) and for TMTS (Optane on the DDR bus). Use "moves ~20–25% of DRAM to cheaper tiers (zswap, TMTS's Optane, Vistara's CXL) … local DRAM keeps 50–75%."
- **R3-M2 (s07):** The Pattern callout's "ambitious cells reach volume" contradicts the headline and the A2/B2 badges, and no A2 form is at volume. Use "ambitious forms find use only inside vendor-controlled stacks (PIM demos) or on bulk, predictable data (KV pools)."
