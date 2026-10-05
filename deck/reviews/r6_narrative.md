# Round 6: narrative and editorial review (fresh eyes)

*Reviewer: narrative/editorial, round 6, 2026-10-04.*

*Inputs:*
- shots/s01–s10.png (rendered 06:26:36–44, newer than every slide file; last slide edit s02 06:26:27)
- slides/s01–s10.html (visible text extracted by script; SVG/JS figure text excluded; counts by hand)
- deck.html; memory-closer-farther-deck.html (compared, see §6)
- r1–r5 decisions (binding), r5_narrative.md (checked item by item), ORCH_NOTES.md
- SYNTHESIS_v2.0.md (diffed against v1.9: §2 intro "per direction for serial links; HBM/DDR buses are half-duplex", Wang reframed, HBM3E 33–43, NVMe "typical")

**Verdict: needs revision (two one-line fixes).** Blocking 0 · Major 1 · Minor 5 · plus one orchestrator action (§6).

The deck answers the research question convincingly in 10 slides. The arc holds:
- s01: the answer. Closer and farther are two ways out of DDR's narrow spot on one GB/s-per-GB × $/GB frontier.
- s02: distance is ruled out.
- s03 and s04: the two pressures, labelled → A and → B. s03's takeaway now names the next limit ("core energy is the next limit"), which closes the gap left by the headline's "until another limit binds".
- s05: every tier on one frontier.
- s06: why a mix wins.
- s07: deployed forms versus niche forms.
- s08: how the two directions are coupled.
- s09: why now.
- s10: the implication, hedged predictions, observations that would count against the account, and rival explanations.

Every headline is ≤16 words, makes one claim, and its slide supports that claim (§4). The break year is now "~2010" on every slide. The NVMe qualifier is consistent. Nothing in the deck contradicts SYNTHESIS v2.0.

The single major is an r5 decision that was only half applied: the s05 chart caption still calls every bar "per-direction".

---

## 1. Round-5 items: verification

| r5 item | Status | Evidence in current render |
|---|---|---|
| M1 s05 NVMe "measured" | **Resolved** | s05 latency cell "10–40 µs typical"; s02 "typical (TPP Fig. 2)"; SYNTHESIS v2.0 l.121 now "typical" too |
| m1 break year ~2010/~2011 | **Resolved** | s01 pt 4 "since ~2010"; s09 headline "until ~2010", marker "slowdown (~2010)", header "SINCE ~2010", cell "before ~2010"; s04 "before ~2010". "Ranganathan 2011" kept as a citation year. No "~2011" left in deck.html (grep) |
| m2 s03 takeaway names the limit | **Mostly resolved** | "…vs Raptor's 0.37: core energy is the next limit, though newer generations lower it (→ PIM, slide 7)". The hand-off now works. Length went up (see m1 below) |
| m3 s02 units | **Resolved** | "A 6 ft (~2 m) cable adds ~12 ns each way." |
| m4 s08 density | **Open (accepted)** | Unchanged. Still ~69 words (see m3 below) |
| m5 s02 aria-label | **Resolved** | Includes load-to-use vs page/block read, the 4 KB 3.9/5.58 µs points, and NVMe "typical" |
| m6 s06 footer "SYNTH." | **Resolved** | "placement: SYNTHESIS v1.6 §3 [Syn]" |

---

## 2. Major

### M1 · s05 · chart caption "Peak per-direction specs [D from V]" contradicts the subtitle directly above it, and is wrong for the HBM/DDR rows

**Where it is**
- s05.html l.31: `<p class="tiny s05-cap">Peak per-direction specs [D from V]; hollow = …`

**What is wrong**
- r5 decision (s05) required the change from "per-direction" to "Peak vendor specs (per direction for serial links)" in the subtitle *and* caption. Only the subtitle (l.27) was changed.
- The reader now sees "per direction for serial links" in the sub and "Peak per-direction specs" in the caption under the same chart.
- For 7 of the 10 rows (HBM4, HBM3E, CPU HBM, MRDIMM, LPDDR5X, DDR5, 3D stacking) "per-direction" is wrong. SYNTHESIS v2.0 §2 l.110 says: "per direction for serial links; HBM/DDR buses are half-duplex".
- REVIEWER.md classes a wrong qualifier as major.

**Fix (s05.html l.31):**
> Peak vendor specs [D from V] (per direction for serial links); hollow = discontinued tier or single-product point (vendor peak or spec). Objects are placed by matching access density (slide 6).

---

## 3. Minors

**m1 · s03 · the takeaway is ~55 words, over the r5 cap ("at or under ~50 words"). The trailing "it" is ambiguous.**
- **Current text** (55 words, excluding the slide pointer): "After the interposer, most energy sits in the DRAM core (O'Connor's HBM2 model: 3.45 of 3.92 pJ/bit, full access, modelled). 3D stacking removes the edge limit, but HBM2 interposer I/O is already 0.3–0.8 pJ/bit by toggle rate (PHY excluded) vs Raptor's 0.37: core energy is the next limit, though newer generations lower it (→ PIM, slide 7)."
- **Why it matters.**
  - s03 is still the densest slide: about 76 body words, plus a ~70-word caption under chart (c).
  - "though newer generations lower it" sits after "the next limit", so "it" can read as "the limit" rather than "core energy".
- **Fix.** Move the generational caveat next to the number it qualifies, and echo the headline's verb ("binds"). This keeps every r4/r5-mandated element: the 3.45/3.92 figure, "modelled", the HBM2 scope, the newer-generations caveat, "by toggle rate", "PHY excluded", Raptor 0.37 and the PIM pointer. Result: 50 words.
  > After the interposer, most energy sits in the DRAM core (O'Connor's HBM2 model: 3.45 of 3.92 pJ/bit, full access, modelled; newer generations lower it). 3D stacking removes the edge limit, but HBM2 interposer I/O is already 0.3–0.8 pJ/bit by toggle rate (PHY excluded), vs Raptor's 0.37: core energy binds next (→ PIM, slide 7).

**m2 · s09 · the Middle row mixes sign conventions in adjacent cells.**
- **Current text:**
  - 1990s cell: "$/bit before ~2010: ~33–37%/yr"
  - Since cell: "$/bit −12–15%/yr"
- **Why it matters.**
  - One reads as a decline rate and the other as a signed change.
  - s04 box 1 uses unsigned decline rates for both ("~33–37%/yr … ~12–15%/yr").
- **Fix (since cell):** "$/bit: ~12–15%/yr". This matches s04 and the "before" cell. Alternatively, sign both cells.

**m3 · s08 · body is still ~69 words, above the ~60 target (carried over from r5 m4).**
- **Word counts:**
  - col 1 takeaway: 10
  - col 2: 31
  - col 3: 28
  - "≈43 vs ≈0.82" line: 7 (not included in the ~69)
- **Fix, only if one more pass is made:** in col 3, change "Deployed only in training (optimizer at the capacity tier: ZeRO, MemoryX)." to "Deployed only in training (ZeRO, MemoryX optimizer offload)." This saves 3 words and keeps both sources. Otherwise accept as is.

**m4 · s09 · "Caveat: Memory crises were forecast before…" uses a capital after the colon.**
- **Why it matters.** Every other run-in label in the deck continues in lower case: "Catch: a CXL controller…" (s08), "Pattern: ambitious forms…" (s07), "Mixing pays: production…" (s06).
- **Fix:** "Caveat: memory crises were forecast before …".

**m5 · s05 · the headline drops "shared" while the chart includes RDMA far memory, which is B2 (sharing).**
- **Why it matters.** This is allowed by r3 (G3-1: "drop 'or shared' if it overflows"), and the headline is at 16 words. Noted only because s01 and s07 use the full "cheaper, extended or shared GB".
- **Fix, if wanted at 16 words:** "Each tier bundles bandwidth with capacity: A buys GB/s per GB; B, cheaper, extended or shared GB." That is 17 words, so it does not fit. **No change recommended.** Logged for completeness only.

---

## 4. Headline audit (≤16 words, one claim, supported)

| Slide | Words | Claim supported on slide? |
|---|---|---|
| s01 | 15 | Yes. Schematic plus four answer points; values deferred to s02–s05 |
| s02 | 14 | Yes. Latency ladder; Xeon Max callout (closer ≠ faster); hop brackets; cable sub |
| s03 | 16 | Yes. (a) 3.0 vs 1.6 growth, (b) die edge, (c) pJ/bit vs power; takeaway names the next limit |
| s04 | 16 | Yes. TPP Fig. 3 Gen5 37.1; box 1 33–37 → 12–15%/yr; footer ratio 2.4–3.1× |
| s05 | 16 | Yes. Density chart plus $/GB column (recycled DDR4 0.13 nominal; new-DDR5 CXL = "extended") |
| s06 | 15 | Yes. Skew points, placement table, production line (20–25% moved at <5%) |
| s07 | 13 | Yes. Software rows (A1/B1 none or transparent; A2 new model; B2 app changes); status chips; Pattern |
| s08 | 16 | Yes. "B pulls compute outward" is the weakest clause, but col 3 says "deployed only in training" and s10 rates it Medium |
| s09 | 15 | Yes. Chart break at ~2010; 1990s vs since table; NDP'14 quote |
| s10 | 15 | Yes. Two clauses (implication and counter-observations); acceptable for the close |

## 5. Density (body ≤ ~60 words; tables, captions, legends, figure labels, headers, subs and footers excluded)

| Slide | Words | Note |
|---|---|---|
| s01 | ~65 + 12 (note) | Four answer points; accept (as r5) |
| s02 | ~55 (three callouts) | OK |
| s03 | ~76 (panel labels 21 + takeaway 55) | Over; m1 brings the takeaway to 50 |
| s04 | ~50 | OK |
| s05 | ~55 (three annotations) | OK |
| s06 | ~55 | OK |
| s07 | 34 (Pattern) + 2×2 | The 2×2 counts as a table |
| s08 | ~69 | Over; m3 |
| s09 | 25 (caveat) + table | OK |
| s10 | ~60 (callout + rivals) + table | OK |

## 6. Orchestrator action (not a slide finding)

**memory-closer-farther-deck.html is stale.**
- **Evidence.**
  - Its timestamp is 06:16; deck.html is 06:26.
  - The named copy still contains "10–40 µs measured" and "since ~2011" (2 hits).
  - It lacks "core energy is the next limit".
  - deck.html has none of these residues.
- **Action.** If the named file is the deliverable, re-copy it from deck.html after the final build, and diff to confirm the two match.

## 7. Terminology and cross-slide consistency

| Item | Status |
|---|---|
| A / B; A1, A2, B1, B2 | Consistent (s01, s07, s10) |
| B = "cheaper, extended or shared GB" | s01, s07 sub ✓; s05 "cheaper or extended GB" (allowed, m5); s07 B1 "Cheaper or extended bytes" (B1 only) ✓ |
| 3D DRAM–logic stacking | s01, s05 ✓; s03 "3D stacking" / "Raptor 3D" / "zHBM 3D" as short forms in a chart context, acceptable |
| far / capacity tier / the middle | s02 "'Far' is a role", s08 "far (capacity) tier", s10 "far tier", s01/s05/s09 "the middle" ✓ |
| local DRAM vs DDR | s06 "local DRAM keeps 50–75%"; never "off DDR" ✓ |
| CXL adder / switch / 214–394 ns | s02, s05, s07 consistent (G-1, G-2, G2-6) |
| RDMA | s02 64 B 3.55/4.55 and 4 KB 3.9/5.58; s05 "~3.5–5.6 µs (64 B–4 KB page)" ✓ |
| NVMe | s02 and s05 "typical" ✓ (r5 M1 fixed) |
| HBM3E 33–43, HBM4 54–73, A1 "~33–73" | s05, s07 ✓; SYNTHESIS v2.0 now 33–43 ✓ |
| Peak-spec scope | s05 sub ✓, s05 caption ✗ (**M1**) |
| Break year ~2010 | s01, s04, s09 ✓ |
| $/bit rate sign | s04 unsigned; s09 mixed (m2) |
| Wafer ~3×, HBM4E "expected >4:1" | s05, s07, s08 ✓ |
| TPP 33% | s06 "in emulation" ✓ |
| Synthesis version in footers | v1.6 throughout (s01, s06, s07, s10); allowed by r3/r4 |

## 8. Faithfulness to SYNTHESIS v2.0

**Covered.** Every exec-summary element is on a slide:
- distance is a proxy (s02)
- both pressures, with 3.0/1.6 and 1.92 kW (s03) and 33–37 → 12–15%/yr, 37% TCO and 20–25% tiered (s04, s06)
- the frontier and the improving middle (s05 annotations 1–3; s10 callout)
- skew within workloads, i.e. per object (s01 pt 3, s06)
- the 2×2 with status, costs and software (s07)
- three couplings, including "the shortage did not cause B" (s08)
- the 1990s split, the NDP'14 quote and the forecast caveat (s09)
- confidence High/Medium/Low, hedged predictions, the three counter-observations and rivals (a)/(b) (s10)

**v2.0 deltas reflected:**
- NVMe "typical" (s02, s05)
- HBM3E 33–43 (s05)
- Wang's 10–80% is absent from the deck, consistent with v2.0's reframing of it as a channel-count ratio
- "per direction for serial links": s05 sub only (M1)

**Hedges carried:**
- Newton baselines (s07)
- XCENA [V]; BEACON and CXL-PNM sim. (s08)
- 2026 est. / 2027 forecast (s08)
- modelled and sim. tags (s07)
- trace replay (s07)
- [S] on the Grace and GPU latency rows (s02, s05)
- zHBM "lower bound" (s03)

**Acceptable omissions:**
- §2.0 tRCD
- §5.1 GPU scale-up domains
- §7(c) different communities
- §8 open questions

**Divergences:** none of substance.

## 9. Major, one line

- **M1 · s05:** the caption "Peak per-direction specs" contradicts the corrected sub and is wrong for the HBM/DDR rows. Change it to "Peak vendor specs [D from V] (per direction for serial links)".
