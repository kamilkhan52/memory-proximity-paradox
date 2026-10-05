# Round 4: narrative and editorial review (fresh eyes)

*Reviewer: narrative/editorial, round 4, 2026-10-03.*

*Inputs:*
- shots/s01–s10.png (render of 23:35, newer than every slide file) and slides/s01–s10.html (visible text extracted; words counted by script and then checked by hand)
- BRIEF.md §A, §B and §D
- r1, r2 and r3 decisions (binding); r3_narrative.md (checked item by item); ORCH_NOTES.md
- SYNTHESIS_v1.8.md (diffed against v1.7: six edits — Handy tag, CXL density range, RDMA 3.5–5.6 µs, Newton baseline, "HBM without in-stack compute")

**Verdict: ready.** Blocking 0 · Major 0 · Minor 8.

The deck answers the research question convincingly in 10 slides. A memory-industry reader gets the answer up front on s01: distance is not the variable; both directions leave DDR's narrow region of one bandwidth-per-GB × $/GB frontier. The slides that follow prove it in order:
- s02 removes distance as the explanation.
- s03 and s04 give the two pressures, labelled → A and → B in the kickers.
- s05 puts both on one frontier.
- s06 shows why a mix wins.
- s07 sorts the forms that work from the niche ones.
- s08 couples the two directions.
- s09 explains why now.
- s10 gives falsifiable implications and weighs rival explanations.

Both round-3 majors (R3-M1, R3-M2) are resolved in the render, and so are all but one of the round-3 minors. Every headline is ≤16 words and makes one supported claim.

The minors below are wording polish. None is needed before presenting. The two worth doing are m1 (s05 "The CXL tier moves down in $/GB" also reads as covering new-DDR5 CXL, which costs more per GB than DIMMs) and m2 (s08 says "A ↔ B share wafers" but shows only the wafer-free side of B).

---

## 1. Round-3 items: verification

| r3 item | Status | Evidence in current render |
|---|---|---|
| R3-M1 s06 "off DDR" | **Resolved** | "production moves ~20–25% of DRAM to cheaper tiers (zswap, TMTS's Optane, Vistara's CXL) at <5% slowdown; TPP: 33% on pre-production CXL; local DRAM keeps 50–75%." Footer says "local-DRAM share" |
| R3-M2 s07 "reach volume" | **Resolved** | "Pattern: ambitious forms find use only inside vendor-controlled stacks (PIM demos) or on bulk, predictable data (KV pools)…" This matches the headline, both badges and s10 prediction 5 |
| m1 s03 headline | **Resolved** | 16 words, with "adds wires, cuts pJ/bit" |
| m2 s08 density rewrite | **Resolved** | Body ~64 words. "The shortage favours wafer-free B … but did not cause B" is unambiguous. The caption is no longer duplicated |
| m3 s02 headline / pointer-chase | **Resolved** | "closer is not lower-latency"; "pointer-chase latency" (but see m6 on the line break) |
| m4 B description | **Resolved** | s01 and the s07 sub say "cheaper, extended or shared GB". The s05 headline says "cheaper or extended GB", which G3-1 allows to keep it at 16 words |
| m5 hand-offs | **Resolved** | The orphan "Next:" line has gone from s07. Hand-offs now run consistently through the → A / → B kickers and "(slide N)" pointers |
| m6 s05 "It" | **Partly resolved** | Now reads "The CXL tier moves down in $/GB". The antecedent is clear, but the claim is now too general (m1) |
| m7 s10 evidence cell | **Resolved** | "GB200, wafer ratio, ZeRO (slide 8)" |
| m8 s02 Berger/Pond | **Resolved** | Now on the MHD row: "Berger/Pond 8–16-socket MHD pool, modelled" |
| m9 SYNTHESIS-side | **Partly adopted in v1.8** | Adopted: §8 now says "HBM without in-stack compute". Still open: §4 Pattern (l.263) says "reached volume", and App. A's table header says "DDR share of capacity". See m8 |

---

## 2. Majors

None.

---

## 3. Minors

**m1 · s05 · annotation 1 · "The CXL tier moves down in $/GB." also reads as covering new-DDR5 CXL, which the same slide's $/GB column contradicts.**
- **What is wrong.** Taken alone, the sentence says CXL is a cheaper tier.
  - The slide's own $/GB cell says "new DDR5 ≈1 + controller", which is above DIMM cost. Only recycled DDR4 is cheaper: "0.13 nominal, 0.3–0.6 all-in".
  - SYNTHESIS v1.8 §5.2 says the same: "New-DDR5 CXL and SSD offload are not spared."
  - r3 m4 flagged exactly this contradiction. The r3 fix for "It" swapped one ambiguity for an overgeneralisation.
- **Fix** (same length; it names the media that makes the tier cheap):
  > Deployed CXL is not a new density point: Vistara's tier (0.225) sits at capacity-maxed DDR (0.2). Its recycled DDR4 moves it down in $/GB.

**m2 · s08 · col 2 · the header says A and B "share wafers", but the text shows only wafer-free B benefiting. The competition side is missing.**
- **What is wrong.**
  - The header "A ↔ B share wafers" and the headline "competes for wafers" set up a contest.
  - The body then names only B's *wafer-free* forms, which do not compete for wafers at all.
  - A careful reader will ask: compete with which B?
  - SYNTHESIS v1.8 §5.2 "Who benefits" gives both sides. New-DRAM B tiers (new-DDR5 CXL, and LPDDR capacity tiers like GB200's) pay more. Only wafer-free B gains ground.
- **Fix** (col 2 text: 29 words → 31):
  > HBM3E takes ~3× the wafer per bit (HBM4E expected >4:1), a named 2026-shortage driver. New-DRAM B tiers pay more; wafer-free B (recycled DDR4, compression) gains. The shortage did not cause B.

**m3 · s10 · the headline says "would refute"; the panel says "would count against".**
- **What is wrong.**
  - The panel title is "What would count against this account", and the synthesis §8 heading is "What would count against this account".
  - "Refute" is stronger than the evidence supports. Each "Would mean" cell describes a partial reattribution (e.g. "B is driven by something else"), not a falsification of the whole account.
- **Fix** (13 words):
  > Specify memory by frontier position, not attach point; three observations would count against this account.

**m4 · s04 · headline "memory is 37% of Meta's rack TCO" drops the trend the chart shows.**
- **What is wrong.**
  - 37% is the latest generation only (Gen5 = 37.1; Gen0 = 15.6). SYNTHESIS v1.8 exec. summary says "in Meta's latest generation".
  - "Is" reads as a fleet-wide constant.
  - A verb that implies rising is both more accurate and stronger evidence for "now bites".
- **Fix** (16 words):
  > Capacity cost now bites: $/bit declines slowed ~2.5× and memory reached 37% of Meta's rack TCO.

**m5 · s09 · caveat · "scaling limits were forecast before (memory wall 1995; …)" still pairs the memory wall with a $/bit argument.**
- **What is wrong.**
  - Wulf & McKee forecast a processor–memory *performance* gap (r1 decision s09), not slowing DRAM cost scaling.
  - The sentence's contrast, "the difference now is a decade of measured slowdown", is about $/bit.
  - As written, "scaling limits" makes the memory wall sound like a cost forecast that failed.
- **Fix** (27 words):
  > Caveat: memory crises were forecast before (the memory wall, 1995; Ranganathan 2011 bet on PCM/memristors). The difference now is a decade of measured $/bit slowdown, not the 2026 price spike.
- **SYNTHESIS-side.** v1.8 §6 still says '"End of DRAM scaling" was declared before (Wulf & McKee 1995; …)'. That is the same error r1 corrected on the slide (see m8).

**m6 · s02 · the headline wraps at the hyphen: "closer is not lower-" / "latency".**
- **What is wrong.** The break splits the compound word that carries the slide's claim. Read line by line, "closer is not lower-" is briefly meaningless.
- **Fix.** Write `lower&#8209;latency`, which uses a non-breaking hyphen (U+2011), so the compound wraps as a unit. The wording stays the same.

**m7 · s03 panel (c) · the label "Raptor 3D (I/O only)" sits closer to the zHBM diamond than to its own marker.**
- **What is wrong.**
  - The Raptor label offset is `[10, 20]` from the Raptor diamond (0.37 pJ/bit, 100 TB/s). That puts the text directly above-left of the zHBM diamond (0.49, 31.8 TB/s).
  - A reader can read Raptor's 0.37 as the lower point, which inverts the two 3D points. This matters because the s03 caption contrasts their scopes ("Raptor 0.37 = measured I/O only; zHBM 0.49 = projected total").
- **Fix.** Anchor the label beside its own marker, e.g. offset `[10, 4]` (right of the upper diamond, same height). Keep the halo. For the visual reviewer to confirm.

**m8 · SYNTHESIS-side (orchestrator, not builders).** If a v1.9 is issued, align these four places with the deck:
- **§4 Pattern** (l.263): "reached volume only where…" → "found use only where…". This carries over r3 m9 and matches s07.
- **App. A table header:** "DDR share of capacity" → "local-DRAM share of capacity". This carries over r3 m9 and matches s06.
- **§6:** '"End of DRAM scaling" was declared before (Wulf & McKee 1995; …)' → "Memory crises were forecast before (the memory wall, Wulf & McKee 1995; …)". This matches m5.
- **§5.2:** "Handy names … [V]" → "[S: Handy (Objective Analysis)]". This matches the v1.8 exec. summary and the s08 footer.

---

## 4. Headline audit (≤16 words, one claim, supported)

| Slide | Words | Claim supported on slide? |
|---|---|---|
| s01 | 15 | Yes. Schematic plus four answer points; values deferred to s02–s05, as the note says |
| s02 | 14 | Yes. Latency ladder plus the Xeon Max callout (line break: m6) |
| s03 | 16 | Yes. (a) compute vs BW growth, (b) die edge, (c) pJ/bit and power, takeaway on core energy |
| s04 | 16 | Yes. TPP Fig. 3 Gen5 37.1; 33–36 → 12–15%/yr ≈ 2.2–3.0× (wording: m4) |
| s05 | 16 | Yes. Density chart and $/GB column |
| s06 | 15 | Yes. Skew points, placement table, production line |
| s07 | 13 | Yes. Software rows, badges, Pattern line now consistent |
| s08 | 16 | Yes. Three columns, one coupling each (col 2 gap: m2) |
| s09 | 15 | Yes. $/GB chart with ~2011 break; 1990s vs since-~2011 table |
| s10 | 14 | Two clauses (implication + refutations); acceptable for the close ("refute": m3) |

## 5. Density (body ≤ ~60 words; tables, captions, legends, figure labels, headers and subs excluded)

| Slide | Words | Note |
|---|---|---|
| s01 | ~65 | Slightly over, but these are the four answer points; leave as is |
| s02 | ~55 | OK |
| s03 | ~60 | OK (two panel labels + takeaway) |
| s04 | ~53 | OK |
| s05 | ~54 | OK. The sub is 29 words, and its "Peak, per-direction vendor specs" repeats the caption's "Peak per-direction specs [D from V]". Optional: cut that clause from the caption |
| s06 | ~48 | OK |
| s07 | 37 (Pattern) + 2×2 | The 2×2 counts as a table; every line is evidence |
| s08 | ~64 | OK; m2 adds 2 words |
| s09 | 28 | OK (table carries the content) |
| s10 | ~57 | OK |

## 6. Terminology

| Term | Status |
|---|---|
| A / B; A1, A2, B1, B2 | Consistent (s01 sub, s07 heads, s10 rows) |
| B = "cheaper, extended or shared GB" | s01 and s07 sub ✓. s05 "cheaper or extended GB" is allowed by G3-1. s07 B1 "Cheaper or extended bytes" is correct for B1 alone |
| 3D DRAM–logic stacking | s01, s03, s05 ✓; SYNTHESIS v1.8 ✓ |
| HBM without in-stack compute | s10 ✓; SYNTHESIS v1.8 §8 ✓ (now aligned) |
| access density ρ | Defined on s06 as the "demand-side twin of slide 5's axis"; the s05 caption points to it ✓ |
| far tier / capacity tier | s02 "'Far' is a role", s08 "far (capacity) tier", s10 "far tier" ✓ |
| DDR (the middle) vs local DRAM | s06 now says "local DRAM keeps 50–75%" (media-accurate) and keeps "DDR" for the middle in the table and bracket. Intentional, and correct |
| CXL adder +~100–280 ns; switch ≥220 ns [V]; 214–394 ns (394 = FPGA) | s02, s05, s07 consistent |
| RDMA latency | s05 "~3.5–5.6 µs (64 B–4 KB page)" matches v1.8. s02 plots 3.55–4.55 µs plus 3.9 µs (Fastswap 4 KB) and does not show PolarCXLMem's 5.58 µs 4 KB point. Not a contradiction (different sources and granularities), but the accuracy reviewer may want the 5.58 point on s02 |
| Footer synthesis citation | v1.6 throughout (allowed). s06 abbreviates it "SYNTH. v1.6"; write "SYNTHESIS v1.6" for consistency (trivial) |

## 7. Faithfulness to SYNTHESIS v1.8

**Covered.** Every element of the v1.8 executive summary appears in the deck:
- proxy (s02)
- two pressures, including the 3.0 vs 1.6 growth rates and the 1.92 kW point (s03, s04)
- the frontier, and the middle improving (s05, annotation 2)
- skew within a workload, and the 20–25% / 33% / 50–75% production numbers (s06)
- the 2×2 with its status and costs (s07)
- the three couplings, including "amplifies, did not cause" (s08)
- 1990s history, NDP'14, and the forecast caveat (s09)
- confidence levels, §8 implications and predictions, the three counter-observations, and rivals (a) and (b) (s10)

**v1.8 deltas are reflected:**
- RDMA 3.5–5.6 µs (s05)
- CXL expander density 0.06–0.25 (s05 row)
- Handy [S] (s08 footer)
- "HBM without in-stack compute" (s10)
- The Newton change is a synthesis clarification ("vs a simulated GPU"). The s07 text "Newton 54× vs GPU, 10× vs ideal (sim.)" is consistent with it, because the sim. tag covers both numbers.

**Hedges carried over:**
- "consistent with … not proof" (s10)
- Newton baselines (s07)
- XCENA [V] and BEACON/CXL-PNM sim. (s08)
- TPP pre-production (s06)
- trace replay and modelled tags (s07)
- 2026 est. / 2027 forecast (s08)

**Acceptable omissions:**
- §2.0 tRCD
- §5.1 GPU scale-up domains
- §7(c) different communities
- §8 open questions

**Divergences:** m1 and m2 are under-statements of §5.2's nuance, not contradictions. The deck is deliberately ahead of the synthesis on "find use" and "local-DRAM share" (m8).

## 8. Majors, one line each

None. The most useful minors are m1 (s05: "The CXL tier moves down in $/GB" → "Its recycled DDR4 moves it down in $/GB") and m2 (s08: say that new-DRAM B tiers pay more while wafer-free B gains).
