# Round 5: narrative and editorial review (fresh eyes)

*Reviewer: narrative/editorial, round 5, 2026-10-04.*

*Inputs:*
- shots/s01–s10.png (rendered 23:48:17–30, newer than every slide file; last slide edit s02 23:48:04)
- slides/s01–s10.html (visible text extracted by script, SVG/JS figure text excluded; counts checked by hand)
- deck.html (identical to memory-closer-farther-deck.html)
- r1–r4 decisions (binding), r4_narrative.md (checked item by item), ORCH_NOTES.md, r5_visual.md
- SYNTHESIS_v1.9.md (diffed against v1.8: exec-summary $/bit and TPP wording, Mooncake trace replay, +50–70 ns spec vs measured hop, §4 Pattern, §6 memory-wall wording, App. A header)

**Verdict: needs revision (one one-word fix).** Blocking 0 · Major 1 · Minor 6.

The deck answers the research question convincingly in 10 slides, and the arc holds:
- s01 gives the answer: closer and farther are two exits from DDR's narrow spot on one GB/s-per-GB × $/GB frontier.
- s02 removes distance as the explanation.
- s03 and s04 give the two pressures, labelled → A and → B.
- s05 puts every tier on one frontier.
- s06 shows why a mix wins.
- s07 separates deployed forms from niche ones.
- s08 couples the directions.
- s09 explains why now.
- s10 gives the implication, hedged predictions, counter-observations and rival explanations.

Every headline is ≤16 words and makes one claim that its slide supports. All eight r4 narrative minors landed in the render (§1).

The one major is a qualifier regression. r4 relabelled NVMe latency "typical", not measured, on s02, but s05 still says "measured". This is a one-word fix.

---

## 1. Round-4 items: verification

| r4 item | Status | Evidence in current render |
|---|---|---|
| m1 s05 annotation 1 | **Resolved** | "…sits at capacity-maxed DDR (0.2). Its recycled DDR4 moves it down in $/GB." |
| m2 s08 col 2 | **Resolved** | "New-DRAM B tiers pay more; wafer-free B (recycled DDR4, compression) gains. The shortage did not cause B." |
| m3 s10 "refute" | **Resolved** | Headline: "…three observations would count against this account." This matches the panel title and v1.9 §8 |
| m4 s04 "reached" | **Resolved** | "…slowed ~2.5–3× and memory reached 37% of Meta's rack TCO." (16 words) |
| m5 s09 caveat | **Resolved** | "Memory crises were forecast before (memory wall 1995; …)…measured $/bit slowdown." |
| m6 s02 line break | **Resolved** | U+2011 is in the source; "lower‑latency" wraps as a unit |
| m7 s03 Raptor label | **Resolved** | The label now sits beside its own diamond |
| m8 SYNTHESIS-side | **Mostly adopted in v1.9** | Adopted: §4 Pattern, App. A header and §6. Still open: §5.2 l.294 "Handy names … [V]" (see m6 below) |

---

## 2. Majors

### M1 · s05 · idle-latency cell for NVMe SSD reads "10–40 µs measured"; s02 and the canonical notes say this value is typical, not measured

**Where it is wrong**
- s05 data, `"lat":"10–40 µs measured"` (s05.html l.20). It renders in the IDLE LATENCY column, NVMe row.

**Why it matters**
- The r4 decision for s02 relabelled this value "typical (TPP Fig. 2)" and changed its drawing style. The s02 caption now says "NVMe is a typical value". The s02 tooltip says "Typical value from a generic technology figure, not a measurement."
- ORCH_NOTES says: "NVMe 10–40 µs typical (TPP Fig. 2 technology figure — NOT a measurement)".
- So the same number now carries two different qualifiers three slides apart.
- REVIEWER.md classes a wrong qualifier and a cross-slide inconsistency as major. G-8 requires every number to keep its qualifier.

**Fix:** change `"lat":"10–40 µs measured"` to `"lat":"10–40 µs typical"`.
- Optional: add "NVMe: TPP Fig. 2 (typical) [P]" to the s05 footer, which currently gives no source for any latency cell other than [S]. Leave it out if the footer must stay at three lines.

**Not a problem:** the DDR5 cell "~100–150 ns measured" and the CXL cell "214–394 ns measured (FPGA at 394)" on the same column are correctly tagged.

---

## 3. Minors

**m1 · s01, s04, s09 · the break year appears as both "~2010" and "~2011" for the same $/bit slowdown.**
- **Where each form appears:**
  - s01 point 4: "since ~2011"
  - s09 headline: "until ~2011"
  - s09 chart marker: "slowdown (~2011)"
  - s09 table header: "SINCE ~2011"
  - s09 table, 1990s column: "$/bit before ~2010"
  - s04 box 1: "before ~2010; ~12–15%/yr since"
  - fits on s09 and s04 footer: "1990–2010 / 2010–24"
- **Why it matters.** On s09 the reader sees "before ~2010" and "SINCE ~2011" in adjacent table cells. SYNTHESIS v1.9 is split the same way: the exec summary says "after ~2010", while l.44 and l.348 say "Since ~2011".
- **Fix: use one year.**
  - "~2010" matches the fit boundary and the v1.9 exec summary. Wherever "~2011" stands for the break, change it to "~2010":
    - s01 point 4
    - s09 headline (word count unchanged)
    - s09 chart marker
    - s09 table header
  - Keep "Ranganathan 2011", which is a citation year.
  - SYNTHESIS-side: align l.44 and l.348 with the exec summary.
- If the orchestrator prefers "~2011", change s04 box 1 and the s09 table cell to "before ~2011" instead. The fits can stay 1990–2010.

**m2 · s03 · body ≈70 words (panel labels 22 + takeaway 48), the densest slide; the takeaway does not name the "other limit" the headline promises.**
- **Why it matters.**
  - The headline ends "until another limit binds".
  - The takeaway ends "so core energy remains", which leaves the reader to connect the two.
  - "By toggle rate" is opaque on screen, and the footer already gives the 0.3 / 0.80 toggle split.
- **Fix** (keeps every r4 number and qualifier; 45 words):
  > After the interposer, most energy sits in the DRAM core (O'Connor's HBM2 model: 3.45 of 3.92 pJ/bit, full access, modelled). 3D stacking removes the edge limit, but HBM I/O is already 0.3–0.8 pJ/bit vs Raptor's 0.37: core energy is the next limit (→ PIM, slide 7).

**m3 · s02 · the headline says "not metres", but the sub says "A 6 ft cable".**
- **Why it matters.** Mixed units in two adjacent lines. The source (Levis) says 6 ft.
- **Fix:**
  > A 6 ft (~2 m) cable adds ~12 ns each way. Latency lives in device timing, controllers, SerDes, switches and queues.

**m4 · s08 · body ≈69 words, over the ~60 target.**
- **Word counts:**
  - col 1 takeaway: 10
  - col 2: 31
  - col 3 bullets and catch: 28
- **Why it matters.** Every clause is load-bearing after r2–r4, so this is not a cut list. It is the one slide where a presenter will be talking over text.
- **Fix, only if a trim is wanted:** in col 3 change "Deployed only in training (optimizer at the capacity tier: ZeRO, MemoryX)." to "Deployed only in training (ZeRO, MemoryX optimizer offload)." This saves 3 words without losing a source.
- Otherwise accept as is.

**m5 · s02 · the aria-label is stale after the r4 changes.**
- **Why it matters.** It says "idle load-to-use latency" and "RDMA 3.55 to 4.55 microseconds". The axis now says load-to-use for memory tiers and page/block read for RDMA 4 KB and NVMe. The chart now plots the 5.58 µs 4 KB point. NVMe is typical.
- **Fix:**
  > Log-scale range chart of idle access latency by path, 50 ns to 100 µs: load-to-use for memory tiers, page or block read for RDMA 4 KB and NVMe. … RDMA 3.55 to 4.55 µs for 64 B and 3.9 to 5.58 µs for a 4 KB page; NVMe SSD 10 to 40 µs, typical. Hollow dashed bars mark spec, modelled or typical values.

**m6 · footers and SYNTHESIS-side (orchestrator, not builders).**
- **s06 footer.** "SYNTH. v1.6" → "SYNTHESIS v1.6", to match every other footer. This carries over r4 §6.
- **SYNTHESIS v1.9, if a v1.10 is issued:**
  - §5.2 l.294: "Handy names … [V]" → "[S: Handy (Objective Analysis)]". This matches exec l.39 and the s08 footer, and carries over r4 m8.
  - l.348: "not ~33–36%/yr" → "not ~33–37%/yr". This matches exec l.26 and s04/s09.
  - Break year: see m1.

---

## 4. Headline audit (≤16 words, one claim, supported)

| Slide | Words | Claim supported on slide? |
|---|---|---|
| s01 | 15 | Yes. Schematic + four answer points; values deferred to s02–s05 as stated |
| s02 | 14 | Yes. Latency ladder; Xeon Max callout (closer ≠ faster); hop brackets |
| s03 | 16 | Yes. (a) 3.0 vs 1.6 growth, (b) die edge, (c) pJ/bit vs power; takeaway names the next limit (wording: m2) |
| s04 | 16 | Yes. TPP Fig. 3 Gen5 37.1 (Gen0 15.6 → "reached"); box 1 33–37 → 12–15%/yr; footer ratio 2.4–3.1× |
| s05 | 16 | Yes. Density chart + $/GB column (recycled DDR4 0.13; "extended" covers new-DDR5 CXL) |
| s06 | 15 | Yes. Skew points, placement table, production line ("<5% slowdown", 20–25% moved) |
| s07 | 13 | Yes. Software rows (A1 none, B1 transparent/NUMA vs A2 new model, B2 app changes); badges; Pattern |
| s08 | 16 | Yes. "B pulls compute outward" is the weakest of the three clauses (training-only deployment, rest sim./vendor), but col 3 states this and s10 rates it Medium. Acceptable |
| s09 | 15 | Yes. Chart break + 1990s vs since table + NDP'14 quote (break year: m1) |
| s10 | 15 | Yes. Two clauses (implication + counter-observations); acceptable for the close |

## 5. Density (body ≤ ~60 words; tables, captions, legends, figure labels, headers, subs and footers excluded)

| Slide | Words | Note |
|---|---|---|
| s01 | ~65 + 13 (note) | Four answer points; accept |
| s02 | 57 | OK |
| s03 | ~70 | Over; m2 brings it to ~67 and sharpens the hand-off |
| s04 | 50 | OK |
| s05 | 55 | OK (sub 29) |
| s06 | 47 | OK |
| s07 | 37 (Pattern) + 2×2 | The 2×2 counts as a table |
| s08 | ~69 | Over; see m4 |
| s09 | 24 (caveat) + table | OK |
| s10 | 59 (callout + rivals) + table | OK |

## 6. Terminology and cross-slide consistency

| Item | Status |
|---|---|
| A / B; A1, A2, B1, B2 | Consistent (s01, s07, s10) |
| B = "cheaper, extended or shared GB" | s01 and s07 sub ✓. s05 "cheaper or extended GB" (allowed by G3-1). s07 B1 "Cheaper or extended bytes" (B1 alone) ✓ |
| 3D DRAM–logic stacking | s01, s05 ✓. s03 uses "3D stacking" in the r4-mandated takeaway and "Raptor 3D" / "zHBM 3D" as chart labels; acceptable short forms |
| far tier / capacity tier / the middle | s02 "'Far' is a role", s08 "far (capacity) tier", s10 "far tier", s01/s05 "the middle" ✓ |
| local DRAM vs DDR | s06 "local DRAM keeps 50–75%" (media) and "DDR" for the middle ✓; never "off DDR" ✓ |
| CXL adder / switch / 214–394 ns | s02, s05, s07 consistent (G-1, G-2, G2-6) |
| RDMA | s02 64 B 3.55/4.55, 4 KB 3.9/5.58; s05 "~3.5–5.6 µs (64 B–4 KB page)" ✓ |
| NVMe | s02 "typical" ✗ s05 "measured" (**M1**) |
| HBM3E 33–43, HBM4 54–73, A1 "~33–73" | s05, s07 ✓ |
| Wafer ~3×, HBM4E "expected >4:1" | s05, s07, s08 ✓ |
| Xeon Max ~20% higher latency, ~700 vs ~200 GB/s | s02, s05 ✓ (v1.9 §2) |
| TPP 33% | s06 "in emulation" ✓ (v1.9 exec) |
| Break year | ~2010 vs ~2011 (m1) |

## 7. Faithfulness to SYNTHESIS v1.9

**Covered.** Every exec-summary element is on a slide:
- proxy (s02)
- both pressures with 3.0/1.6 and 1.92 kW (s03, s04)
- frontier and improving middle (s05)
- skew and production numbers (s06)
- 2×2 with status, costs and software (s07)
- three couplings incl. "did not cause B" (s08)
- 1990s split, NDP'14 and forecast caveat (s09)
- confidence, predictions, counter-observations and rivals (a)/(b) (s10)

**v1.9 deltas reflected:**
- s04/s09 33–37%/yr and ~2.5–3×
- s06 TPP "in emulation"
- s07 Mooncake "trace replay"
- s07 Pattern "find use only inside vendor-controlled stacks … or on bulk, predictable data (KV pools)"
- s09 "Memory crises were forecast before"
- s06 footer "local-DRAM share"

**Hedges carried:**
- "consistent with … not proof" (in s10's framing via "would count against" and Low/Medium ratings)
- Newton baselines (s07)
- XCENA [V], BEACON/CXL-PNM sim. (s08)
- 2026 est. / 2027 forecast (s08)
- modelled / sim. tags (s07)
- [S] on Grace/GPU latency (s02, s05)

**Acceptable omissions:**
- §2.0 tRCD
- §5.1 GPU scale-up domains
- §7(c) different communities
- §8 open questions

**Divergences:** none of substance. M1 is a deck-internal regression, not a synthesis divergence.

## 8. Majors, one line each

- **M1 · s05:** the NVMe idle-latency cell "10–40 µs measured" contradicts s02 and ORCH_NOTES ("typical, not a measurement"). Change it to "10–40 µs typical".
