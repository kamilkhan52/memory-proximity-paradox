# Round 7: narrative and editorial review (final confirmation, fresh eyes)

*Reviewer: narrative/editorial, round 7, 2026-10-04.*

*Inputs:*
- shots/s01–s10.png, rendered 06:34:57–06:35:05. Every shot is newer than every slide file; the last slide edit was s06 at 06:34:56.
- slides/s01–s10.html. Headline words were counted by script from the `<h2>`. Body words were counted by hand.
- deck.html, compared with memory-closer-farther-deck.html.
- r1–r5 decisions (binding); r6_narrative.md, checked item by item; r6_accuracy.md, to explain wording changes; ORCH_NOTES.md.
- SYNTHESIS_v2.0.md: executive summary, §2 table, §3, §5, §8, confidence.

**Verdict: ready.** Blocking 0 · Major 0 · Minor 4 (all optional polish).

## The deck answers the research question

The deck answers the research question convincingly in 10 slides. The arc holds:

| Slide | Role in the argument |
|---|---|
| s01 | The answer: closer and farther both leave DDR's narrow spot on one cost frontier |
| s02 | Rules out distance |
| s03 → A, s04 → B | The two pressures. s03's takeaway now ends "core energy binds next", which echoes the headline's "until another limit binds" |
| s05 | Places every tier on the frontier |
| s06 | Why a mix wins |
| s07 | Deployed forms versus niche forms |
| s08 | How the two directions are coupled |
| s09 | Why now |
| s10 | Implication, hedged predictions, what would count against the account, and rival explanations |

The hand-offs are explicit:
- s01 → "slide 2 / slide 6"
- s03 → "PIM, slide 7"
- s04 → "slide 6", "slide 9"
- s05 → "slide 6"
- s10 → "slide 8"

The cross-slide numbers agree, and nothing in the deck contradicts SYNTHESIS v2.0.

---

## 1. Round-6 items: verification

| r6 item | Status | Evidence in the current render |
|---|---|---|
| **M1** s05 caption "Peak per-direction specs" | **Resolved** | Caption: "Peak vendor specs [D from V] (per direction for serial links); hollow = …". The sub and caption now agree. `grep -c per-direction deck.html` = 0 |
| m1 s03 takeaway length and ambiguous "it" | **Resolved** | "…full access, modelled; newer generations lower it). … vs Raptor's 0.37: core energy binds next (→ PIM, slide 7)." 51 words, excluding the pointer. "It" now sits beside "DRAM core", so the reference is clear. "(PHY excluded)" became "(interposer wires only)" under r6_accuracy m2; see m1 below |
| m2 s09 sign convention | **Resolved** | Since cell "$/bit ~12–15%/yr"; before cell "~33–37%/yr". Both unsigned, matching s04 |
| m3 s08 density | **Open (accepted)** | Col 3 is unchanged. The body is still ~69 words. Acceptable, see §5 |
| m4 s09 "Caveat: Memory" | **Resolved** | "Caveat: memory crises were forecast before …" |
| m5 s05 "shared" | No change needed (r3 G3-1 allows it) | "B, cheaper or extended GB" (16 words) |
| §6 stale named copy | **Resolved** | `cmp deck.html memory-closer-farther-deck.html`: identical. No "~2011" in either file outside the data and citation ("Ranganathan 2011") |

r6_accuracy items that touch the narrative also check out:
- The s09 aria-label is now "~2010", so no "about 2011" is left.
- The s03 caption now reads "(HBM points 8-stack)".
- The s06 footer is split into table, tiering and local-DRAM sources.
- The s08 footer's ~3× is tagged [D from V].
- The s04 footer says "from the published AI Impacts rate pairs".

---

## 2. Blocking and major

None.

---

## 3. Minors (optional polish; none blocks release)

**m1 · s03 · takeaway: "interposer" appears twice in one clause.**
- **Current text:** "but HBM2 interposer I/O is already 0.3–0.8 pJ/bit by toggle rate (interposer wires only), vs Raptor's 0.37"
- **Problem:** the parenthetical repeats the word it qualifies. The r6_accuracy m2 scope point was that this is O'Connor's interposer-wire term, not driver or PHY circuitry. The current wording reads as a stutter rather than a scope note.
- **Fix (keeps the scope, saves a word, 50 words):** "but HBM2 I/O is already 0.3–0.8 pJ/bit by toggle rate (interposer-wire term only), vs Raptor's 0.37".

**m2 · s05 · the subtitle and the chart caption now carry the same qualifier.**
- **Current text:**
  - Sub: "Peak vendor specs (per direction for serial links) unless marked."
  - Caption, about 350 px below: "Peak vendor specs [D from V] (per direction for serial links); …"
- **Problem:** the duplication is harmless and correct. It came from the r5 decision ("subtitle/caption").
- **Fix (only if one more pass is made):** shorten the caption to "Specs [D from V]; hollow = discontinued tier or single-product point (vendor peak or spec). Objects are placed by matching access density (slide 6)."
- **Otherwise:** accept as is.

**m3 · s09 · the table note mixes signed and unsigned rates.**
- **Current text:** "'before ~2010' combines AI Impacts averages (33–36%, 2020$) with our nominal fit (−37%); 'since' is AI Impacts (12–15%)."
- **Problem:**
  - The note puts "(−37%)" beside unsigned "(33–36%)" and "(12–15%)".
  - The table cells are unsigned since the r6 fix.
  - The chart labels are signed slopes ("−37%/yr"). That is fine for a fit line.
- **Fix:** "(37%)" in the note, or "nominal fit, −37%/yr" if the reader should connect it to the chart label.
- **Severity:** trivial.

**m4 · s04 box 2 · faithfulness trace: "Capacity per unit of CPU performance fell ~60–70% in 10 years" is not in SYNTHESIS v2.0.**
- **Source:**
  - The claim comes from Berger et al., IEEE TC'26: notes/q5_critiques_convergence.md l.261, "dropped by nearly 60–70%", marked as read in full from an author version.
  - The PDF is not in fulltext/.
- **Status on the deck:** the deck claim is sourced and tagged [P]. No deck change is needed.
- **Action:** this is a synthesis residue (the same as r6_accuracy m8). Add one clause to SYNTHESIS §2 or to the executive summary's "Capacity cost" row, and optionally save the PDF to fulltext/, so the deck and synthesis stay one-to-one.

---

## 4. Headline audit (≤16 words, one claim, supported)

Counted by script from each slide's `<h2>`.

| Slide | Words | Claim supported on the slide? |
|---|---|---|
| s01 | 15 | Yes. Schematic plus four answer points; values deferred to slides 2–5 |
| s02 | 14 | Yes. Latency ladder; "Closer ≠ faster" (Xeon Max); hop brackets; cable sub |
| s03 | 16 | Yes. Panel (a) 3.0 vs 1.6 ([D] ~1.9×), (b) die edge, (c) pJ/bit vs power; the takeaway names the next limit |
| s04 | 16 | Yes. TPP Fig. 3 Gen5 37.1; box 1 33–37 → 12–15%/yr; ratio in the footer |
| s05 | 16 | Yes. Density chart plus $/GB column. The recycled-DDR4 annotation carries "cheaper"; new-DDR5 CXL carries "extended" |
| s06 | 15 | Yes. Skew points, placement table, production tiering line |
| s07 | 13 | Yes. Software rows (A1/B1 transparent; A2 new model; B2 app changes); status chips; Pattern line |
| s08 | 16 | Yes. "B pulls compute outward" is the weakest clause, but it is hedged in col 3 ("deployed only in training") and rated Medium on s10 |
| s09 | 15 | Yes. Chart break at ~2010; 1990s vs since table; NDP'14 quote |
| s10 | 15 | Yes. Two clauses (implication and counter-observations), acceptable for the close |

## 5. Density (body ≤ ~60 words)

Tables, captions, legends, figure labels, kicker, sub and footer are excluded.

| Slide | Words | Note |
|---|---|---|
| s01 | ~68 | Four answer points. Unchanged since r5/r6; accepted |
| s02 | ~55 | Three callouts |
| s03 | ~71 | Panel labels 20 + takeaway 51. Still the densest slide. The takeaway is now at the r5 ~50 cap; accept |
| s04 | ~50 | |
| s05 | ~57 | Three annotations (25 / 22 / 10) |
| s06 | ~49 | |
| s07 | 34 (Pattern line) | The 2×2 counts as a table |
| s08 | ~69 | Carried over from r5 m4 / r6 m3; accepted. If a final pass is made, "(ZeRO, MemoryX optimizer offload)" saves 3 words |
| s09 | 27 (caveat) | Plus the table |
| s10 | ~62 | Callout 17 + rivals ~45, plus tables |

## 6. Terminology and cross-slide consistency

| Item | Status |
|---|---|
| A / B; A1, A2, B1, B2 | Consistent (s01, s07, s10) |
| B = "cheaper, extended or shared GB" | s01, s07 ✓. s05 headline "cheaper or extended GB" (allowed). B1 label "Cheaper or extended bytes" ✓ |
| 3D DRAM–logic stacking | s01, s05 ✓. s03 short forms ("3D stacking", "Raptor 3D", "zHBM 3D") in chart context, acceptable |
| far / capacity tier / middle | s02 "'Far' is a role"; s08 "far (capacity) tier"; s10 "far tier"; s01, s05 and s09 "the middle" ✓ |
| CXL +~100–280 ns; switch ≥220 ns [V]; 214–394 ns (394 = FPGA) | s02, s05, s07 ✓ |
| RDMA 3.55/4.55 (64 B) and 3.9/5.58 (4 KB) → "~3.5–5.6 µs" | s02, s05 ✓ |
| NVMe "typical" | s02, s05 ✓ |
| HBM3E 33–43, HBM4 54–73, A1 "~33–73" | s05, s07 ✓ |
| Peak-spec scope "(per direction for serial links)" | s05 sub and caption ✓ (duplicated, m2) |
| Break year ~2010 | s01, s04, s09 (headline, marker, header, cells, note, aria) ✓ |
| $/bit rates unsigned | s04, s09 cells ✓; s09 note (m3) |
| Wafer ~3×, HBM4E "expected >4:1" | s05, s07, s08 ✓ |
| Production tiering 20–25%, <5% slowdown; TPP 33% "in emulation"; local DRAM 50–75% | s06 ✓; s04 points to slide 6 (no repeat) ✓ |
| MRDIMM-12800 1.6 TB/s ≈ Xeon Max HBM peak | s05 annotation 2 ↔ s10 callout ✓ (SYNTHESIS §2 l.135, §8 l.377) |
| Synthesis version in footers | v1.6 (allowed by r3/r4) |

## 7. Faithfulness to SYNTHESIS v2.0

**Every executive-summary element is on a slide:**
- Distance is a proxy: s02.
- Bandwidth pressure (Gholami 3.0 vs 1.6; 1.92 kW at 2.4 pJ/bit): s03.
- Capacity-cost pressure (33–37 → 12–15%/yr, ~2.5–3×; 37% TCO): s04.
- 20–25% production tiering: s06.
- The frontier and the improving middle: s05; s10 callout.
- Per-object skew: s01 point 3; s06.
- The 2×2 with status, best evidence, costs and software: s07.
- The three couplings, including "the shortage did not cause B": s08.
- The 1990s split, the NDP'14 quote and the forecast caveat: s09.

**s10 matches §8 and Confidence:**
- The implication.
- The MRDIMM + SRAM LLC + CXL bar.
- The three hedged predictions. Row 3, the coupling row, is a [Syn] restatement of §5.
- The High/Medium/Low ratings match "High: A1, B1; Medium: coupling; Low: A2/B2 economics".
- The three counter-observations, worded as §8.
- Rivals from §7.

**Placement table:** s06 matches §3 l.164–167.

**Acceptable omissions:**
- §2.0 tRCD
- §5.1 GPU scale-up domains
- §7(c)
- §8 open questions

**Divergence:** one deck claim (s04 Berger 60–70%) has no line in the synthesis (m4). Otherwise none.

## 8. One-line summary

Ready. Every r6 item is resolved or accepted. Four optional minors:
- s03 repeated "interposer"
- s05 duplicated qualifier
- s09 note sign
- s04 Berger claim missing from SYNTHESIS (synthesis-side)
