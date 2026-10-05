# Deck + report brief: "Closer and farther at once"

*Architect's brief, 2026-10-03. Single source of truth for slide builders (Phase 2), figure-data agents (Phase 1) and the report writer (Phase 5).*
*Built from `SYNTHESIS_v1.2.md` (reviewed answer), `deck/PLAN.md`, `explainers/hbm-beachfront-power.html` + `review_r1–r3.md`, `notes/*.md` (Round-4 deltas take precedence), `notes/r2d_review.md`, `notes/r5_review_v04.md`, and spot checks of `fulltext/` and Hot Chips 2026 deck text/images.*
*Audience: a memory-industry researcher. Tone: technical, precise, no marketing.*

Where this brief departs from the synthesis or from PLAN.md, it says so with **FLAG** and the reason. All flags are collected in §G.

---

## A. Thesis and narrative arc

### A.1 Thesis (3 sentences)

1. Memory is moving both closer to compute and farther from it because distance is not the quantity being optimized: both directions leave the DDR DIMM's narrow region of a cost frontier defined by bandwidth supplied per GB stored and $/GB, under a latency constraint.
2. Bandwidth must cross a boundary (wires × rate, with energy per bit turning bandwidth into watts), which pushes the bandwidth-dense head of the data toward compute (A); slowing $/bit declines push the cold, skewed tail onto cheaper bytes (B); because skew is per object, a mix of tiers beats any single tier and the DDR middle persists.
3. The deployed forms (passive HBM; compression, recycled DDR4 and single-host CXL) need no programming-model change while compute-at-memory and cross-host pooling stay niche, and the two directions are coupled through capacity demand, shared DRAM wafers and compute pulled toward the far tier.

### A.2 Arc (one line per slide; each sets up the next)

| # | Section | What it establishes | Hand-off to next slide |
|---|---|---|---|
| 01 | Question and answer | Poses the paradox; one-sentence answer; schematic frontier with A arrow right, B arrow down from the DDR region | "First, drop the word 'distance'." |
| 02 | Distance is a proxy | Measured latencies: closer is not faster (Xeon Max), farther costs protocol hops, "far" is a role | If not distance, then what? Two pressures: bandwidth and capacity cost |
| 03 | Pressure 1: bandwidth | Bandwidth crosses a boundary; die edge caps the wires, pJ/bit turns bandwidth into watts; compute outgrows bandwidth | That explains A. What explains B? |
| 04 | Pressure 2: capacity cost | $/bit decline slowed; memory is 37% of Meta's rack TCO; much bought memory is idle | Both pressures can be put on one picture |
| 05 | The cost frontier | Every tier is a bundle (GB/s per GB × $/GB × latency); A moves right, B moves down at DDR density; the middle improved | Why buy several bundles instead of one? |
| 06 | Skew and placement | Skew is per object; head → A, tail → B, low-MLP → DDR; placement by ρ × I × MLP | Which forms of A and B actually work? |
| 07 | Deployed vs ambitious | 2×2: A1, B1 deployed; A2, B2 niche; mirror-image critiques | Are A and B independent? No. |
| 08 | Coupling | A creates capacity-tier demand; A competes with B for wafers; B pulls compute outward | Why is all of this happening now? |
| 09 | Why both, why now | Same split in the 1990s; commodity DRAM let the middle outrun both; since ~2011 it cannot | So what should a memory researcher do or watch? |
| 10 | Implications | Specify by frontier position; hedged predictions with confidence; three observations that would refute the account | End |

---

## B. Global rules for builders

1. **One claim per slide.** The `h2` headline is that claim: a full sentence, ≤16 words, copied exactly from §D. Do not reword.
2. **Body text ≤ ~60 words per slide**, excluding figure labels, axis titles, captions, legends and tables. Count it. If over, cut body text, never the footer.
3. **Provenance tags live in the footer `.sources` line, not in the body.** Tags: `[P]` primary read in full, `[P/V]` primary but from an interested seller, `[V]` vendor (slides, specs, keynotes), `[Pr]` press, `[S]` secondary, `[D]` our arithmetic, `[Syn]` our synthesis, `[unverified]`, `[pend]` awaiting full text. Footer lines must give page / figure / table / slide locators as written in §D.
4. **Flag vendor numbers in the body/figure** with a trailing `<sup class="tag">V</sup>` (one per number), or the words "vendor", "projected", "claimed". Mark **simulated** results "sim." and **modelled** results "modelled" right next to the number. Never present a modelled or simulated result as measured. Peak specs say "peak"; measurements say "measured".
5. **Energy scope is always stated** next to any pJ/bit value: "I/O", "full access" (DRAM-side: activation + on-die + I/O), or "system". **Never compare values across scopes**, including by placing them in the same column without scope chips. The only cross-scope plot allowed is the d-Matrix replot on slide 3, whose caption states the scope problem.
6. **Colour semantics are fixed** (deck.css): `--near` blue = A (closer / tighter coupling), `--far` orange = B (farther / disaggregation / capacity tier by role), `--mid` slate = DDR baseline / CPU main memory / the middle, `--conv` green = convergence / 3D DRAM–logic stacking. Use `-soft` variants for fills. Never use these four for anything else. Neutral data (growth rates, generic stats) uses `--ink`, `--ink-2`, `--ink-3` with dash patterns. In SVG write colours as `style="fill:var(--near)"` / `style="stroke:var(--far)"`.
7. **No dual axes.** Two quantities with different units go in two small panels.
8. **Log scales are labelled** in the axis title ("…, log scale") and use decade ticks (1, 10, 100 or 1, 2, 5 per decade).
9. **Every figure has a one-line caption** (`.tiny` or `.small`) that states what is plotted and its source, e.g. "Replotted from d-Matrix HC'26 s6, ±5% log-axis read."
10. **Replot real data; never paste screenshots.** Data come from `deck/data/<id>.json` (copied into the slide's `<script type="application/json" id="sNN-data">`). If a value is not in the JSON, it is not on the slide.
11. **Direct labels beat legends.** Label series at line ends or next to marks. Minimum SVG text size 11px at 1280-px slide width; use the `.halo` class on labels that cross gridlines or marks.
12. **Typography:** fonts only through CSS variables (`--font-display`, `--font-body`, `--font-mono`). Numbers in `.num` or `.mono`. Use "~" for approximate, "–" (en dash) for ranges, "×" for ratios, "µs", "ns", "GB/s per GB", "pJ/bit".
13. **Hover tooltips (optional)** via `window.DeckTip.attach(el, html)`. Tooltip text carries the full provenance (source, locator, tag, uncertainty). The slide must read correctly without hover.
14. **Accessibility:** each SVG gets `role="img"` and an `aria-label` sentence describing the chart.
15. **Self-check before handing back:** run the preview command (§C), view `deck/shots/sNN.png` with Read, and confirm: no overflow or clipping at 1280×720; nothing overlaps; headline fits in ≤2 lines; body word count ≤ ~60; every number on the slide appears in the slide's Evidence list (§D) or data JSON; footer fits (≤3 lines).
16. **"Do not say" lists in §D are binding.** They record errors earlier reviewers caught.

---

## C. Fragment contract (copy exactly)

each slide is /Users/kamil/research/memory-proximity-paradox/deck/slides/sNN.html (NN = 01..10) containing exactly one `<section class="slide" id="sNN" data-title="…">` with `<header class="slide-head">` (div.kicker "NN · Section", h2 headline, optional div.sub), `<div class="slide-body">`, `<footer class="slide-foot"><div class="sources">…</div><div class="pageno">NN / 10</div></footer>`, optionally followed by ONE `<script>` wrapped in an IIFE that only queries inside `#sNN`; scripts may use `window.DeckTip.attach(el, html)` for hover tooltips; charts as inline SVG (static or JS-built from a `<script type="application/json" id="sNN-data">` block holding the slide's data copied from deck/data/*.json). Slide canvas 1280×720 with 60px side padding: usable body area ≈1160×~520px. Fonts via CSS variables only. No external assets. Preview/screenshot command: `python3 /Users/kamil/research/memory-proximity-paradox/deck/build/assemble.py --preview sNN.html` → deck/shots/sNN.png (view with Read).

**Clarifications (do not conflict with the contract):**
- The `<script type="application/json" id="sNN-data">` block sits inside the `<section>` (it is data, not the one executable script).
- Slide-specific CSS: use inline `style=""` attributes, or one `<style>` element inside the `<section>` whose every selector starts with `#sNN`. Do not edit `deck/assets/deck.css`.
- All element ids inside a slide are prefixed `sNN-`.
- Reusable layout classes already in deck.css: `.cols.c2`, `.cols.c-6-4`, `.cols.c-4-6`, `.box.near|far|mid|conv`, `.callout`, `table.t`, `.small`, `.tiny`, `.tag`, `.num`, `.mono`, `.label-near|far|mid|conv`, and SVG classes `.axis`, `.lbl`, `.muted`, `.faint`, `.halo`.
- The header takes ~110 px when a `.sub` is present; plan the body for ~1160 × 500 px.

---

## D. Per-slide specification

Each slide lists: kicker · headline (exact) · sub (exact) · body copy (exact) · layout · figure spec · footer (exact) · builder model · do-not-say · Evidence (**do not render**; for reviewers and for the builder's self-check).

---

### Slide 01 — Question and answer

- **Kicker:** `01 · Question and answer`
- **Headline (exact):** Closer and farther are both escapes from the DDR DIMM's narrow spot on one cost frontier.
- **Sub (exact):** Why are tighter memory–compute coupling (A) and memory disaggregation / far memory (B) both attractive?
- **Body copy (exact), left column, under a small label "Answer" (`.kicker` style):**
  1. Distance is not what is optimized. Both directions leave the DDR DIMM's narrow region of a two-axis frontier, GB/s per GB stored and $/GB, under a latency limit.
  2. A buys GB/s per GB (HBM, 3D DRAM-on-logic, PIM). B buys cheaper or shared GB (CXL, compression, RDMA pools).
  3. Access is skewed per object, so mixed tiers beat any single tier; the DDR middle persists.
- **Layout:** `.cols.c-4-6`. Left (~440 px): the three numbered points at 18–19 px, first point set as a `.callout`. Right (~690 × 470 px): schematic SVG.
- **Figure (schematic, no data file):** "the frontier, schematically".
  - Axes: x "GB/s per GB stored →" , y "$/GB →". No tick values. Corner note in `.tiny`: "Schematic, not to scale. Measured positions: slide 5."
  - A rounded box filled `--mid-soft`, stroked `--mid`, centred left-middle, labelled "DDR DIMM (the middle)".
  - **A arrow** (`--near`, 3 px) from the DDR box toward the upper right, ending at three labelled tier names stacked along it: "HBM", "PIM (compute in the stack)" in `--near`, and "3D DRAM-on-logic" in `--conv`.
  - **B arrow** (`--far`, 3 px) from the DDR box straight down, ending at "compression · recycled DDR4 behind CXL"; a second, thinner `--far` arrow down-left to "RDMA pools · SSD".
  - Under the plot, one `.tiny` line: "Latency is a separate constraint (slide 2); skew decides the mix (slide 6)."
  - No numbers anywhere in the schematic.
- **Footer (exact):** `Answer: SYNTHESIS v1.2, executive summary [Syn]; frontier framing after review r2d F2 [Syn]. Schematic only; measured values on slides 2–5. Tags: [P] primary, read in full · [P/V] primary, interested seller · [V] vendor · [Pr] press · [S] secondary · [D] our arithmetic · [Syn] our synthesis.`
- **Builder model:** opus.
- **Do not say:** "A lowers latency"; "B is about distance"; "DDR is dying" or "unstable"; "optimal memory system"; "PIM is deployed"; "pooling is deployed at scale"; any number on the schematic; "A and B are opposites" (the deck's point is that they are not).
- **Evidence (do not render):** no numbers on this slide. Framing: SYNTHESIS v1.2 exec. summary "Answer"; r2d_review.md F1–F3 and "Suggested restructure §0".

---

### Slide 02 — Distance is a proxy

- **Kicker:** `02 · Distance is a proxy`
- **Headline (exact):** Distance is a proxy: closer is not faster, and farther costs hops, not metres.
- **Sub (exact):** A 6 ft cable adds ~12 ns each way. Latency lives in device timing, controllers, SerDes, switches and queues.
- **Body copy (exact), right column, three `.box` callouts:**
  - `.box.near`: **Closer ≠ faster.** On Xeon Max, the same cores see ~20% higher load latency from HBM2e than from DDR5, yet stream ~700 vs ~200 GB/s.
  - `.box.far`: **Farther = hops.** One CXL hop adds ~100–270 ns (measured); a switch adds ≥220 ns more.
  - `.box.mid`: **"Far" is a role.** GB200's capacity tier (LPDDR5X) sits on the Grace module, beside its CPU.
- **Layout:** two columns, `grid-template-columns: 7fr 3fr` (inline style). Left: chart ~780 × 470. Right: the three callouts stacked, 15–16 px text.
- **Figure: measured latency ladder** (data `deck/data/latency_ladder.json`).
  - Form: horizontal range bars on a log x-axis; one row per access path, sorted by latency (fastest at top).
  - X: "Idle load-to-use latency (ns, log scale)", range 50 ns – 100 µs, ticks 50, 100, 200, 500, 1 µs, 2 µs, 5 µs, 10 µs, 20 µs, 50 µs, 100 µs.
  - Rows (label · colour · marks):
    1. Local DDR (DDR4/DDR5), x86 · `--mid` · bar 78–146 ns (Pond Skylake DDR4 78 → PolarCXLMem MLC 146), dot at 115 (Octopus P50, DDR5).
    2. Remote-socket DDR · `--mid` · bar 142–255 ns; dot 231 (PolarCXLMem).
    3. Grace CPU → LPDDR5X · `--mid` · open-ended bar from 200 ns with a right-pointing arrowhead, label ">200 ns [S]".
    4. GPU → HBM3 (H100, GH200) · `--near` · bar ~300–330 ns; row note `.faint`: "includes GPU cache hierarchy".
    5. CXL direct-attach · `--far` · bar 214–394 ns (Melody, 4 devices); dots at 250 (Vistara), 265 (PolarCXLMem); thin dotted whisker to 1,000 ns labelled "p99.99+ tails".
    6. CXL multi-headed device (2–4 ports) · `--far` · bar 260–300 ns.
    7. CXL behind a switch · `--far` · bar 490–600 ns (Octopus); dots 549 (local socket) and 651 (remote socket), PolarCXLMem.
    8. RDMA far memory · `--far` · dots at 3,550 ns (Octopus, via ToR), 3.9 µs (Fastswap, 4 KB page read), 4.55 µs (PolarCXLMem, 64 B read); bar spanning them.
    9. NVMe SSD · `--far` · bar 10–50 µs.
  - Estimates overlay: hollow outlined bars (stroke `--ink-3`, dash `3 3`, no fill) on rows 5–7: Pond projection 155–180 ns (row 5, "spec/emulated"), Das Sharma 170 ns (row 5) and 250 ns (row 7), Berger modelled 270–345 ns rack-scale pool (row 7).
  - Annotations (thin brackets at right of the plot, `.lbl`): between rows 1 and 5 "+1 hop: +~100–270 ns"; between rows 5 and 7 "+switch: ≥220 ns".
  - Legend (top right of plot, small): filled = measured · hollow dashed = spec estimate or modelled.
  - Caption (exact): "Measured idle latency (filled) vs spec or modelled estimates (hollow). Platforms differ; compare within a row."
  - Tooltips: each dot → source, platform, tag.
- **Footer (exact):** `Octopus NSDI'26 P50 [P]; Melody ASPLOS'25 [P]; Vistara ISCA'26 [P]; PolarCXLMem SIGMOD'25 Tables 1–2, p.4 [P]; Fastswap EuroSys'20 p.13 fn.7 [P]; Pond ASPLOS'23 [P]; Xeon Max: arXiv 2505.14294 [P]; H100/GH200/Grace: Chips and Cheese [S]; NVMe: TPP Fig. 2 [P]; cable: Levis HotNets'23 [P]; GB200 [V]. Hollow: Pond, Das Sharma arXiv 2306.11227, Berger IEEE Micro'23 pp.5–6 (modelled).`
- **Builder model:** sonnet.
- **Do not say:** "HBM is low-latency" or "closer cuts latency"; "CXL adds 50–90 ns" (spec-based, optimistic); "549–651 ns vs 146 ns (3.76×)" (3.76× is local-socket 549/146; remote is 651/231 = 2.82×); compare GPU→HBM latency with CPU→DDR as like-for-like (GPU hierarchy confound); "Vera LPDDR5X is on-package" (it is SOCAMM); present Pond's +70–90 ns or Das Sharma's 170/250 ns as measured; mix the RDMA numbers without saying which is a 4 KB page and which a 64 B transfer; do not plot MemChannel's 220.5 ns switched figure (FLAG G4).
- **Evidence (do not render):**
  - 6 ft cable ≈ 12 ns each way → notes/q5 §B1 (Levis HotNets'23, read online); SYNTHESIS §1.
  - Xeon Max HBM ~20% higher latency; STREAM ~700 vs ~200 GB/s → notes/r3 §1 (arXiv 2505.14294, read online in R3, not in fulltext/); SYNTHESIS §1.
  - +~100–270 ns measured adder → notes/r2d M4 (Octopus 230–270 vs 115; Vistara 250 vs 130; Levis ~+140; Melody 214–394 vs 80–120); SYNTHESIS §1, App. A.
  - Switch ≥220 ns → notes/q5 §B3 (Octopus NSDI'26 "adds at least 220 ns"); consistent with PolarCXLMem 549 − 265.2 = 284 ns [D].
  - 146/231/265.2/345.9/549/651 ns; 3.76× local, 2.82× remote → fulltext/3722212.3724460.pdf, Table 1, PDF p.4 (proc. p. = PDF p. + 688); verified by pdftotext.
  - RDMA 64 B read 4.55 µs; 4 KB read 5.58 µs → same file, Table 2.
  - Fastswap 3.9 µs 4 KB RDMA read → fulltext/12-fastswap.pdf p.13, footnote 7.
  - Octopus 115 / 230–270 / 260–300 / 490–600 / 3,550 ns → notes/q2 §1.6.
  - Melody 214–394 ns, tails >700 ns to ~1 µs at p99.99–p99.999 → notes/q3 §4.
  - Vistara idle 130/250 ns → notes/q2 §1.6.
  - Pond local 78 (Skylake) / 115 (EPYC 7452); remote 142 / 255 → notes/q3 §4.
  - H100 ~330, GH200 <300, Grace LPDDR5X >200 ns → notes/q3 §4 [S: Chips and Cheese].
  - NVMe 10–40 µs (TPP Fig. 2) and ~50 µs (Micron spec, snippet) → notes/q3 §4; SYNTHESIS §2 table "10–50 µs".
  - Estimates: Pond +70–90 ns; Das Sharma 170/250 ns; Berger 155–180 and 270–345 ns modelled → notes/q3 §4; notes/q2 D1; fulltext/05-cxl-pools.pdf pp.5–6.
  - GB200 LPDDR5X on the Grace superchip module → SYNTHESIS §1 [V]; r5 Minor 6 (keep the wording "on the Grace module", not "on-package").

---

### Slide 03 — Pressure 1: bandwidth

- **Kicker:** `03 · Pressure 1: bandwidth`
- **Headline (exact):** Bandwidth crosses a boundary: die edge limits the wires, energy per bit the watts.
- **Sub (exact):** Bandwidth = wires crossing a boundary × rate per wire. Memory power = bandwidth × energy per bit.
- **Body copy (exact):**
  - Panel (a) label line: Compute 3.0× per 2 years; DRAM bandwidth 1.6×; interconnect 1.4×.
  - Panel (b) label line: At most ~6 HBM4 stacks fit a reticle die's two long edges; more stacks need more dies.
  - Takeaway under panel (c): After the interposer, energy sits in the DRAM core: 3.45 of HBM2's 3.92 pJ/bit (full access). 3D bonding removes the edge limit and cuts interface energy 2–4×, not core energy.
- **Layout:** two columns, `grid-template-columns: 400px 1fr`. Left column stacks panel (a) (~400 × 190) above panel (b) (~400 × 270). Right column: panel (c) (~730 × 430) with the takeaway line beneath (16 px, `.small`).
- **Figures:**
  - **(a) Compute vs memory-bandwidth growth** (data `deck/data/compute_bw_growth.json`).
    - Three straight lines on a log y-axis from 1× at year 0 to the stated 20-year totals: compute 60,000× (`--ink`, solid, 2 px), DRAM bandwidth 100× (`--ink-2`, solid), interconnect 30× (`--ink-3`, dashed).
    - X: "Years (Gholami's 20-year window)", ticks 0, 5, 10, 15, 20. Y: "Growth (×, log scale)", ticks 1, 10, 100, 1k, 10k, 100k.
    - Direct labels at line ends: "compute 3.0×/2 yr", "DRAM BW 1.6×/2 yr", "interconnect 1.4×/2 yr".
    - Caption: "Fitted rates as stated by Gholami et al.; lines, not data points."
    - If `compute_bw_growth.json` contains cross-checked device points (`points_verified: true`), plot them as small dots on the lines; otherwise lines only.
  - **(b) Die edge to scale** (data `deck/data/hbm_bw_energy.json`, block `die_geometry`). Reuse the explainer's Fig. 2 left panel exactly in geometry: 6 px per mm; host die 26 × 33 mm filled `--mid`, label "host die (SoC or base die) · 26 × 33 mm reticle"; three HBM4 stacks 11 × 12.8 mm (`--near`) on each 33 mm edge with the 11 mm side facing the die; PHY 8 × 4 mm (`--near-soft` with `--near` stroke; the explainer used an orange PHY, which is not allowed here), inset 1 mm from the host-facing edge; label "3 × 11 mm = 33 mm per edge"; bold `--near` label "6 stacks per die". Caption: "To scale, after Samsung HC'26 s9–s11. More dies → bigger interposer (size, warpage limits)."
  - **(c) Bandwidth vs energy per bit, with constant-power lines** (data `deck/data/hbm_bw_energy.json`, block `points`). Reuse the explainer's Fig. 3 exactly (same 12 points, same 2 hollow extrapolations, same axes and iso-power lines), with deck colours:
    - X: "Energy per bit (pJ/bit, log scale; scope differs by point, see caption)", range 0.2–50, ticks 0.2, 0.5, 1, 2, 5, 10, 20, 50.
    - Y: "Peak bandwidth (TB/s, log scale)", range 0.005–300, ticks 0.01, 0.1, 1, 10, 100.
    - Iso-power diagonals at 100 W, 300 W, 1 kW, 2 kW (`--ink-3`, dash `2 4`), labelled outside the right frame. P[W] = BW[TB/s] × 8 × E[pJ/bit].
    - HBM1–HBM4 (proj.) circles in `--near`, joined by a faint `--near` polyline; DDR3/DDR4/DDR5/LPDDR5/GDDR6 squares in `--mid`; Raptor and zHBM diamonds in `--conv`.
    - Hollow `--near` circles: "100 TB/s at 2.4 pJ/bit → 1.92 kW" (dashed vertical from HBM4) and "trend ~1.4–1.5 pJ/bit → ~1.2 kW" (dotted).
    - Labels: as in the explainer (HBM1…HBM4 (proj.), DDR3-1600, DDR4-3200, DDR5-6400, LPDDR5-6400, GDDR6 (384-bit), "Raptor 3D (I/O only)", "zHBM 3D (Samsung proj.)").
    - Caption (exact, two `.tiny` lines): "Replotted from d-Matrix HC'26 s6 (±5% read). HBM = 8-stack package; DDR/LPDDR ≈ one 64-bit channel; GDDR6 = 384-bit bus. d-Matrix labels HBM4's 2–3 pJ/bit 'I/O', 'data-movement' and 'system' on different slides; Raptor 0.37 = measured I/O only."
    - Tooltips: each point → name, pJ/bit, TB/s, implied W, source.
- **Footer (exact):** `(a) Gholami et al., IEEE Micro'24 / arXiv 2403.14123, stated fitted rates [P]. (b) Samsung HC'26 s9–s11 [V], drawn to scale [D]. (c) d-Matrix HC'26 s6 replot [V], Raptor s23 [V]; zHBM from Samsung s23 [D from V]; extrapolations [D]. HBM2 energy split: O'Connor MICRO'17 §1, Fig. 1 [P]; interface 2–4×: O'Connor Table 3, UniIC IEDM'20 Table 1 [P]. Details: explainer "HBM Bandwidth Limits".`
- **Builder model:** sonnet.
- **Do not say:** "power is the next wall after beachfront" (it is a co-limit and a budget all along); "the interposer solves wires per stack" or "wires per stack and stacks per die are two limits" (one limit: wires per mm of die edge at a given reach); "1.2 kW package budget" (Samsung's 1,200 W is GPU power; DRAM ≈220 W is extra); "zHBM is a base-die variant" (it is 3D, DRAM on the xPU); "Raptor is DRAM-on-logic" (logic on top); "3D cuts HBM energy 10× / 6.3× / 13.5×" as like-for-like (interface vs system scope); any comparison of 0.37 or 0.66–0.88 pJ/bit (I/O) with 3.92 pJ/bit (full access); "HBM4 is unterminated" (source is HBM2); "the chart compares packages" for the DDR points (they are one channel); "practical HBM4 limit is 20 TB/s" without noting MI455X at 23.3 TB/s; "HBM bandwidth grows 1.6× per 2 years" (Gholami does not say which DRAM products form that line); do not use Micron's HC'26 memory-wall chart (no data sources stated).
- **Evidence (do not render):**
  - 3.0× / 1.6× / 1.4× per 2 yr; 60,000× / 100× / 30× over 20 yr → notes/q3 §5; notes/q1 §1.3 (Gholami, arXiv 2403.14123, read online; not in fulltext/ — FLAG G1). Check: 3.0^10 = 59,049; 1.6^10 = 110; 1.4^10 = 28.9 [D].
  - 26 × 33 mm reticle; stack 11 × 12.8 mm; 3 per 33 mm edge; 6 per die; PHY 8 × 4 mm, inset 1 mm → explainer §2, Fig. 2 code (`s = 6`); Samsung HC'26 s9–s11 (decks_text/HC2026.HBMBaseDie.SangwookHan.v20.pdf.md); review_r2 #7, #15; review_r3 #7, #15.
  - Points (pJ/bit, TB/s): DDR3-1600 38/0.011; DDR4-3200 31/0.026; DDR5-6400 26/0.055; LPDDR5-6400 8.0/0.052; GDDR6 14/1.1; HBM1 6.8/1.0; HBM2 5.6/2.4; HBM3 4.2/6.5; HBM3E 3.7/9.8; HBM4 (proj.) 2.4/18; Raptor 0.37/100; zHBM 0.49/31.8 → explainer Fig. 3 `pts` array; pixel reads verified ±5% in review_r1 #21.
  - DDR points = one channel [D]: DDR3-1600 ×64 = 12.8 GB/s ≈ 0.011–0.013 TB/s; DDR4-3200 = 25.6 GB/s ≈ 0.026; DDR5-6400 = 51.2 GB/s ≈ 0.055; HBM1 8 × 128 GB/s ≈ 1.0; HBM2 8 × 307 GB/s ≈ 2.4 (FLAG G3).
  - 1.92 kW = 100 × 8 × 2.4 → d-Matrix s6 text "At 2.4pJ/bit, 100TB/s requires 1.92KW for HBM power only (excluding fabric power)".
  - Trend 1.44–1.54 pJ/bit → 1.15–1.25 kW [D] → review_r2 #9.
  - Raptor 0.37 pJ/bit, 296 W → d-Matrix s23 "Measured I/O Power … 100 TB/s × 0.37 pJ/bit = 296 W".
  - zHBM 31.8 TB/s, 124 W → 0.49 pJ/bit [D] → review_r2 #8; Samsung s23.
  - 3.45 of 3.92 pJ/bit (1.21 activation + 2.24 on-die; 0.3 I/O; 3.92 incl. ECC) → fulltext/oconnor-fgdram-micro17.pdf §1, Fig. 1; review_r2 #11.
  - Interface 2–4×: Raptor 0.37 vs HBM2 I/O 0.80 at 50% toggling (2.2×) → O'Connor Table 3; vs HBM1-era ~1.5 incl. PHY (4×) → fulltext UniIC IEDM'20 Table 1; explainer §4.
  - MI455X 12 × HBM4, 23.3 TB/s; d-Matrix "~20 TB/s practical" → explainer table; decks_text MI400 s3; d-Matrix s5.

---

### Slide 04 — Pressure 2: capacity cost

- **Kicker:** `04 · Pressure 2: capacity cost`
- **Headline (exact):** Capacity got costly: the $/bit decline slowed ~2.5×, and memory is 37% of Meta's rack TCO.
- **Sub (exact):** Bits stopped getting cheap fast while cores and models kept growing, and much of the memory bought sits idle.
- **Body copy (exact), right column, four stat blocks (bold lead-in + one line each):**
  1. **$/bit decline:** ~33–36%/yr until ~2011; ~12–15%/yr since.
  2. **Capacity per unit of CPU performance:** fell ~60–70% in 10 years.
  3. **Idle bytes:** 32% of Google fleet memory is cold at 120 s; 55–80% of Meta app memory is idle per 2 min.
  4. **Captured in production:** 20–33% of DRAM moved to cheaper tiers at ≤1–5% slowdown.
- **Layout:** `.cols.c-6-4`. Left: chart ~680 × 450. Right: the four stat blocks, each a `.box` (block 1–2 `.box`, blocks 3–4 `.box.far` because they motivate B), 16 px text.
- **Figure: memory share of Meta rack TCO and power by hardware generation** (data `deck/data/meta_memory_share.json`).
  - Grouped vertical bars, Gen0…Gen5 on x ("Meta hardware generation"); y "Share of rack (%)", linear 0–40, ticks 0, 10, 20, 30, 40.
  - Two bars per generation: "TCO" filled `--mid`; "power" filled `--mid-soft` with `--mid` stroke. Direct legend above the plot.
  - Value labels on every bar (the paper's printed data labels): TCO 15.6, 25.5, 21.6, 21.2, 31.8, 37.1; power 14.6, 19.8, 19.7, 20.7, 28.8, 33.3.
  - Caption (exact): "Memory share of rack TCO and power across six Meta generations (data labels as printed). TPP, ASPLOS'23, Fig. 3."
- **Footer (exact):** `Chart: Maruf et al., TPP, ASPLOS'23, Fig. 3, p.2, printed data labels [P]. $/bit rates: AI Impacts 2020 fits of McCallum and Objective Analysis data [S]; ~2.5× [D]. Capacity per performance: Berger et al., IEEE TC'26 [P]. Cold/idle: Lagar-Cavilla ASPLOS'19 [P]; TPP §3 [P]. Captured: zswap (ASPLOS'19), TMTS ASPLOS'23, TPP [P].`
- **Builder model:** sonnet.
- **Do not say:** "DRAM is 50% of server cost" (circular citation chain: Berger → Pond → Next Platform 2020); "memory share rises every generation" (non-monotonic: 25.5 → 21.2 → 37.1); "37% of rack TCO" without "Meta's"; merge stranded, cold, idle and captured into one number; "DRAM prices rose 7×" (2026 spot spike is cyclical and not this slide's argument); "bandwidth per core collapsed" (P-core flagships held ~5 GB/s per core by adding channels); "50–80% of memory can be offloaded" (production captures 20–33%).
- **Evidence (do not render):**
  - TPP Fig. 3 data labels → scratchpad copy `/private/tmp/claude-501/-Users-kamil/ba12f6ed-f246-4808-b610-1c1bf1dfb934/scratchpad/p/tpp.pdf` p.2 (pdftotext lines ~141–159 show the labels); series mapping (orange = Cost, black = Power) verified by rendering → notes/q2 §1.1 table; SYNTHESIS exec. summary "37% of rack TCO". FLAG G1 (copy to fulltext/).
  - 33–36 → 12–15 %/yr → notes/q3 §1 (AI Impacts: McCallum −36 → −15; Objective Analysis −33 → −12) [S]. ~2.5× [D]: 36/15 = 2.4, 33/12 = 2.75.
  - ~60–70% in 10 years → notes/q5 §B3 (Berger et al., IEEE TC 75(4) 2026, read online).
  - 32% cold at T = 120 s → notes/q4 §1, notes/q2 §1.2 (Lagar-Cavilla ASPLOS'19).
  - 55–80% idle per 2 min → notes/q4 §1 (TPP §1, §3).
  - 20–33% at ≤1–5% → notes/q4 §1 closing paragraph: zswap ~20% (0.2%/min promotion SLO), TMTS 25% at <5%, TPP 33% (2:1) at <1%. FLAG G6 (synthesis exec. summary cites "Memstrata"; use zswap/TMTS/TPP).

---

### Slide 05 — The cost frontier

- **Kicker:** `05 · The cost frontier`
- **Headline (exact):** Each tier bundles bandwidth with capacity; A buys GB/s per GB, B buys cheaper GB.
- **Sub (exact):** Supplied bandwidth per GB stored, with relative $/GB and idle latency. Peak, per-direction vendor specs unless marked.
- **Body copy (exact), three numbered annotations placed under the chart (16 px):**
  1. Deployed CXL is not a new density point: Vistara's tier (0.225) sits at capacity-maxed DDR (0.2). It moves down in $/GB.
  2. The middle improved: a 16-channel MRDIMM-12800 socket (1.6 TB/s) matches Xeon Max's HBM peak at many times the capacity.
  3. DDR is squeezed, not unstable: bigger DIMMs dilute GB/s per GB.
- **Layout:** full-width figure ~1160 × 400, then the three annotations in a 3-column row (~1160 × 90). The figure is a row-aligned composite: row labels (~200 px) | range plot (~560 px) | table (~400 px, two columns).
- **Figure: tier frontier** (data `deck/data/tier_frontier.json`). **FLAG G2:** this replaces PLAN.md's log-log $/GB scatter because most $/GB values are [S] or unverified; plotting them as coordinates would imply precision we do not have.
  - Range plot, log x: "Supplied bandwidth per GB stored (GB/s per GB, log scale)", 10⁻⁴ to 10⁴, decade ticks labelled 0.0001, 0.001, 0.01, 0.1, 1, 10, 100, 1k, 10k.
  - Rows, top to bottom (label · colour · mark):
    1. 3D DRAM-on-logic (d-Matrix Raptor card) · `--conv` · dot at ~3,100, hollow (vendor peak), label "vendor peak".
    2. HBM4 (MI455X, Helios, Rubin) · `--near` · bar 54–73.
    3. HBM3E (B200, GH200) · `--near` · bar 33–42.
    4. HBM on CPUs, discontinued (Xeon Max, A64FX) · `--near` hollow · Xeon Max bar 11 (STREAM) – 26 (peak), A64FX dot 32; row label suffix "discontinued".
    5. MRDIMM-12800 ×16 (Diamond Rapids, Venice) · `--mid` · bar 0.4–1.6, end labels "4 TB" / "1 TB".
    6. CPU-attached LPDDR5X (Vera SOCAMM, Grace) · `--mid` · bar 0.8–1.1.
    7. DDR5-6400 RDIMM ×12, 1DPC · `--mid` · bar 0.2–0.8, end labels "256 GB DIMMs" / "64 GB DIMMs".
    8. CXL expander, Gen5 · `--far` · bar 0.06–0.25 (peak per direction), filled dot 0.225 labelled "Vistara, measured".
    9. RDMA far memory · `--far` · bar 0.01–0.1, label "config-dependent".
    10. NVMe SSD · `--far` · bar 0.0005–0.002.
  - A pale vertical band (`--mid-soft`, 40% opacity) spanning x = 0.2–0.8 across all rows, labelled at top "DDR5 RDIMM range".
  - Off-scale marker at the right edge of row 1's area: "→ on-die SRAM ~3×10⁵ (scratchpad, not a capacity tier)" in `.faint`.
  - Table (right, `table.t`, 13–14 px, rows aligned with plot rows): columns "$/GB (rel. DDR5 = 1)" and "Idle latency":
    1. Raptor: n/a · —
    2. HBM4: above HBM3E · —
    3. HBM3E: ≥3 pre-shortage; ~3× wafer per bit · ~300 ns from GPU
    4. HBM on CPUs: — · DDR + ~20% (Xeon Max)
    5. MRDIMM: >1 (unverified) · ~DDR
    6. LPDDR5X: ≈1 · >200 ns (Grace)
    7. DDR5 RDIMM: 1 (256 GB 3DS at a premium) · 80–120 ns
    8. CXL: new DDR5 ≈1 + controller; recycled DDR4 0.13 nominal, 0.3–0.6 all-in · 214–394 ns
    9. RDMA: — · ~3.5–3.9 µs
    10. NVMe: ≪1 (unverified) · 10–50 µs
  - No energy column on this slide (scopes differ; energy is slide 3 and the report).
  - Caption (exact): "Peak per-direction specs [D from V] unless marked; hollow = discontinued or vendor-peak only. Sustained CXL bandwidth is 10–80% of local DRAM."
- **Footer (exact):** `Derived from vendor specs [D from V]: AMD MI400 HC'26 s3, s5; NVIDIA Rubin HC'26 s8; Vera HC'26 s28; Intel DMR HC'26 s10, s15; d-Matrix HC'26 s13; NV LP30 HC'26 s24; GB200 spec. Vistara ISCA'26 Table V [P]; Xeon Max arXiv 2505.14294 [P]; recycled-DDR4 all-in cost: notes r2a §2.2 [D]; HBM $/GB [S]; Micron FQ2-25 p.6 [P/V]; CXL sustained BW: Wang et al. arXiv 2405.14209 [P].`
- **Builder model:** sonnet.
- **Do not say:** "nothing ships between DDR and HBM" (Xeon Max and A64FX shipped there and were discontinued; GDDR cards and mobile LPDDR may sit there, unverified); "CXL adds density"; "MRDIMM displaced HBM" (it matched Xeon Max's HBM peak in absolute bandwidth); "the market chose a fast middle" (vendors gave no reason); "SRAM is a memory tier"; "spans 8 orders of magnitude" as a demand claim; present Raptor's ~3,100 as measured; mix per-direction and bidirectional figures; "recycled DDR4 costs 0.13×" without "nominal"; any HBM4 $/GB number (no primary); any energy value.
- **Evidence (do not render):**
  - HBM4: MI455X 432 GB at 23.3 TB/s → 53.9; Helios 31 TB at 1.7 PB/s → 54.8; Rubin "100 MW AI factory" 11 PB at 800 PB/s → 72.7 [D from V] → notes/r3 D7; SYNTHESIS §2 table.
  - HBM3E: B200 192 GB at 8 TB/s → 41.7 [V; NVIDIA spec, not in our files — FLAG G7]; GH200 144 GB at ~4.8 TB/s → 33 [S] → notes/q4 §5.3; SYNTHESIS §2.
  - Raptor card 32 GB at 100 TB/s → 3,125 [D from V] → d-Matrix HC'26 s13 ("Chiplet 4 GB 12.5 TB/s; Card 32 GB 100 TB/s"; decks_text line ~232); FLAG G8 (new derived number, not in synthesis).
  - Xeon Max 1.64 TB/s peak, ~0.7 TB/s STREAM, 64 GB → 25.6 / ~11 [D from P]; A64FX 1 TB/s / 32 GB → 32 → notes/r3 D7.
  - MRDIMM 16 × 12800 = 1.6 TB/s "up to" (DMR s10; Venice MI400 system s3) at 1/2/4 TB → 1.6/0.8/0.4 → notes/r3 D7.
  - Vera 1.5 TB SOCAMM at 1.2 TB/s → 0.8 (decks_text Vera line ~710); GB200 Grace 17 TB at 14 TB/s → 0.82; GH200 480 GB at ~512 GB/s → 1.07 [S] → notes/r2d F2, q4 §5.3.
  - DDR5-6400 ×12 = 614.4 GB/s; 12 × 64 GB = 768 GB → 0.8; 12 × 256 GB → 0.2 [D] → SYNTHESIS §2.
  - CXL Gen5 ×8 = 32 GB/s per direction ÷ 128–512 GB → 0.06–0.25; Vistara 57.6 GB/s ÷ 256 GB = 0.225 [D from P] → notes/q4 §5.3; notes/r2d F2; notes/q2 §1.4.
  - RDMA 0.01–0.1 → SYNTHESIS §2 footnote [P]; NVMe ~14 GB/s ÷ 8–30 TB → 0.0005–0.002 [D; specs unverified] → notes/q4 §5.3.
  - SRAM ~3×10⁵: LP30 rack 40 PB/s ÷ 128 GB [D from V] → notes/q1 delta 8.
  - $/GB: HBM ≥3 pre-shortage [S: SemiAnalysis]; ~3× wafer per bit [P/V: Micron FQ2-25 p.6]; LPDDR5X ≈1 [V: NVIDIA Grace "similar cost"]; DDR5 3DS premium [P: Memstrata]; recycled DDR4 0.13 nominal [P: Vistara Table V], 0.3–0.6 all-in [D: notes/r2a §2.2] → SYNTHESIS §2 table.
  - Latency column → slide 2 evidence; Xeon Max +~20% → notes/r3 §1.
  - 1.6 TB/s ≈ Xeon Max 1.64 TB/s HBM peak, ~2× its STREAM → SYNTHESIS §2 obs. 2; notes/r5 Major 4.
  - CXL sustained 10–80% (9.8–80.3%) of local DRAM BW → notes/q2 §3 table (Wang et al.); SYNTHESIS §2 preamble.

---

### Slide 06 — Skew and placement

- **Kicker:** `06 · Skew and placement`
- **Headline (exact):** Access is skewed per object, so a mix of tiers beats any single tier.
- **Sub (exact):** A small hot head of the bytes carries most of the traffic; the tail stays cold for minutes.
- **Body copy (exact):**
  - Under the table: Low arithmetic intensity alone does not decide placement: embedding gathers are 0.25 FLOP/B, yet the big tables have low bandwidth demand.
  - Table legend line (`.tiny`): ρ = bytes/s demanded per byte stored · I = FLOP per byte moved · MLP = memory-level parallelism.
- **Layout:** `.cols.c-6-4` (or 55/45). Left: chart ~620 × 460. Right: placement table, then the body line.
- **Figure: concentration of activity in the hottest bytes** (data `deck/data/skew_stats.json`).
  - Form: point chart on a unit square. X: "Share of bytes, hottest first (%)", 0–100 linear, ticks every 20. Y: "Share of activity (%)", 0–100 linear, ticks every 20.
  - Reference: dashed diagonal (`--ink-3`) labelled "uniform access: no skew (e.g., dense weights, read every decode step)".
  - Points (`--ink` markers, each labelled with source + metric + window in two short lines, `.lbl` 11–12 px):
    1. Meta services: x ≤ 10, y ≈ 90 → dot at (10, 90) with a small left-pointing chevron for "≤"; label "bandwidth · 30 s · 9 services (preprint)".
    2. Alibaba KV$: (10, 77); label "KV$ reuses · production trace".
    3. Hao et al.: (14, >50) → dot at (14, 50) with an upward chevron; label "tuple accesses · emulated, zipf 0.9".
    4. Google fleet: (68, 100) with a horizontal bar 39–99 (cluster range); label "accesses within 120 s".
    5. Meta TPP: horizontal bar 20–45 at y = 100; label "accesses within 2 min · 4 apps".
    6. Mooncake: x ≤ 50 at y = 100 (dot at 50 with left chevron); label "KV block reuses".
  - Along the x-axis, two thin brackets: 0–15% in `--near` labelled "hot head → A", 45–100% in `--far` labelled "cold tail → B". `.tiny` note: "brackets illustrative".
  - Caption (exact): "Each point restates one published statistic as (share of bytes, share of activity). Metrics and windows differ; do not compare points with each other. [D]"
- **Table (right, `table.t`), exact:**

  | Object | Goes to | Examples |
  |---|---|---|
  | High ρ, low I | A: HBM (PIM proposed) | Decode attention over live KV (~1 FLOP/B × GQA group); bulk scans |
  | High ρ, high I | On-chip SRAM | Prefill (~1000 FLOP/B); dense GEMM |
  | Low ρ, high MLP | B: CXL, compressed, pooled | Cold pages; most embedding rows; reusable KV |
  | Low ρ, low MLP | DDR bus | Pointer chasing, B-trees, graphs (GAPBS worst on CXL) |

  Colour the "Goes to" cells' text with `.label-near`, plain ink, `.label-far`, `.label-mid` respectively.
- **Footer (exact):** `Mahar et al., arXiv 2303.08396 [P, preprint]; Wang et al., ATC'25 (Alibaba KV$) [P]; Hao et al., SIGMOD'24 §5.5, p.31:16 [P, emulated]; Lagar-Cavilla ASPLOS'19 [P]; TPP ASPLOS'23 §3 [P]; Mooncake FAST'25 [P]; placement: SYNTHESIS §3 [Syn]; Yuan et al. arXiv 2402.16363 Table 1; Gupta HPCA'20 (0.25 FLOP/B); MTrainS arXiv 2305.01515; Pond (GAPBS); Liu et al. OSDI'25 (MLP) [P]. Points restated [D].`
- **Builder model:** sonnet.
- **Do not say:** "skew is universal" (absent for dense weights); "<10% of capacity serves the P90 of bandwidth" (the paper: ~90% of bandwidth from <10% of capacity, 30 s windows); "low arithmetic intensity means put it in HBM" (needs high ρ too); "50–80% of memory can be offloaded"; "hot → near, cold → far" without the MLP/latency row; "decode is ~1 FLOP/B at any batch size" (weight GEMVs scale with batch; attention over per-sequence KV stays ~1 × GQA factor); "PIM serves decode in production".
- **Evidence (do not render):**
  - Meta MemProf "90%-tile bandwidth is contributed by less than 10% of the memory capacity", 30 s, 9 services → notes/q4 §1; wording per notes/r2d M10 (full text checked by r2d).
  - Alibaba KV$ 10% of blocks → 77% of reuses → notes/q4 §3.2 (Wang et al. ATC'25, arXiv 2506.02634).
  - Hao: local 1/6 the size of remote serves >½ of tuple accesses (emulated, zipf 0.9) → fulltext/3639286.pdf PDF p.16 (= p.31:16), §5.5; notes/q4 Round-4 delta 1. x = 1/7 ≈ 14% [D].
  - Google 32% cold at 120 s; 1–61% across clusters → notes/q4 §1. x = 68 (39–99) [D].
  - TPP 55–80% idle within 2 min → notes/q4 §1. x = 20–45 [D].
  - Mooncake ">50% of cache blocks remaining unused" → notes/q4 §3.2. x ≤ 50 [D].
  - Decode ~1 FLOP/B (MHA), × GQA group factor; prefill ~1000–1215 OPs/B → notes/q4 §2 (Yuan et al. Table 1); SYNTHESIS §3.
  - Embedding gathers 0.25 FLOP/B; big tables low bandwidth → notes/q4 §2 (Gupta HPCA'20), §3.1 (MTrainS verbatim).
  - GAPBS worst on CXL → notes/q4 §2, §6 (Pond).
  - MLP as the latency-tolerance axis → notes/q4 §6.2 (Liu et al. OSDI'25 "Beyond Hotness").

---

### Slide 07 — Deployed vs ambitious

- **Kicker:** `07 · Deployed vs ambitious`
- **Headline (exact):** The deployed forms need no new programming model; the ambitious forms remain niche.
- **Sub (exact):** Split each direction in two: proximity vs compute at memory (A), cheaper bytes vs sharing across hosts (B).
- **Body copy (exact), one line below the grid:** Pattern: ambitious cells reach volume only inside vendor-controlled stacks or for bulk, predictable data. The critiques mirror each other: A2 is limited by process, heat and capacity; B2 by latency and switch cost.
- **Layout:** 2 × 2 grid (~1160 × 400), gap 16 px. Column headers above: "A · closer" (`.label-near`) and "B · farther" (`.label-far`). Row labels at left, rotated or as small caps: "Deployed" (top), "Ambitious" (bottom). Cells: `.box.near` (A1, A2) and `.box.far` (B1, B2); bottom-row boxes use a dashed border to signal "not at volume". Each cell has: title (bold, 17 px), a status pill (mono 11 px, rounded, filled `--*-soft`), then three labelled lines at 14–15 px: **Driver**, **Best evidence**, **Main cost**. The pattern line sits below the grid in `.callout` style.
- **Cell content (exact; grid content counts as table text, not body):**
  - **A1 · Proximity for bandwidth** (HBM, on-package, stacked) — pill: `Deployed at scale (accelerators)`
    - Driver: bandwidth per GB; pJ/bit caps bandwidth under a power budget
    - Best evidence: HBM3E/HBM4 at ~33–73 GB/s per GB
    - Main cost: ~3× the wafer per bit; capacity per $ and per mm²
  - **B1 · Cheaper or extended bytes** (compression, recycled DDR4, single-host CXL) — pill: `Deployed (Meta, Google, Azure)`
    - Driver: $/GB; capacity per core
    - Best evidence: Vistara: 0.13× $/GB nominal; up to 25% fewer servers for ML inference
    - Main cost: +~100–270 ns; 2.7–3.6× less bandwidth per GB than local DDR
  - **A2 · Compute at memory** (PIM / NDP) — pill: `Demos and niche`
    - Driver: internal bandwidth 4–16× the pins; in-die energy
    - Best evidence: AttAcc up to 1.72× vs 2×DGX at equal stacks (sim.); Aquabolt-XL >2× perf, >70% less energy<sup>V</sup>
    - Main cost: HBM2-PIM stack 6 vs 8 GB; DRAM-process logic; heat; no VM or coherence
  - **B2 · Sharing across hosts** (pooling, RDMA far memory) — pill: `Niche (KV pools, prototypes)`
    - Driver: statistical multiplexing
    - Best evidence: Mooncake RDMA KV pool in production (+75% requests); Pond −7–9% DRAM
    - Main cost: switched CXL pooling of VM memory mostly ROI-negative (modelled); switch ≥220 ns
- **Footer (exact):** `SYNTHESIS v1.2 §4 [Syn]. A1: AMD, NVIDIA HC'26 [V]; Micron FQ2-25 p.6 [P/V]. A2: AttAcc ASPLOS'24 §7.6 [P, sim.]; Aquabolt-XL HC33 [V]; AiM ISSCC'22 Fig. 11.1.6 table [P]; UPMEM HC31 [V]. B1: Vistara ISCA'26 [P]; Lagar-Cavilla ASPLOS'19 [P]. B2: Mooncake FAST'25 [P]; Pond ASPLOS'23 [P]; Berger IEEE Micro'23 p.7 [P, modelled]; Octopus NSDI'26 [P].`
- **Builder model:** opus.
- **Do not say:** "PIM failed" / "pooling failed" (niche, not dead); "AttAcc 2.81× at iso-capacity shows PIM's benefit" (that baseline has half the stacks); "3.19× / 3.22×" (not in the AttAcc paper); "B2 pays only in small switchless pools" as a general rule (that is Berger's model for CXL pooling of VM memory); "Vistara's 0.13× $/GB gives 25% fewer servers" (the 25% comes from capacity, for disaggregated ML inference); "LPDDR5X-PIM has shipped" (measured on Samsung's own SoC, no energy data); "attention PIM has shipped"; "SK hynix says PIM is dead" (a VP called it likely niche; SK hynix is an interested party); "~10× bandwidth at ~10× lower energy for stacked DRAM" as evidence (Dally roadmap remark, "not quite ready").
- **Evidence (do not render):**
  - ~33–73 GB/s per GB → slide 5 evidence (notes/r3 D7).
  - ~3× wafer per bit → fulltext/21-micron-mar2025.pdf p.6; fulltext/21-micron-dec2025.pdf p.5; notes/r2a D1.
  - Internal bandwidth 4–16× pins (Aquabolt-XL 4×, LPDDR5X-PIM 8×, AiM 16×) → notes/q1 §1.3 table, §4.
  - AttAcc up to 1.72× vs 2×DGX (80 stacks each), simulated → notes/r5 Major 1; fulltext/03-attacc.pdf §7.6, Fig. 17; SYNTHESIS §4 A2.
  - Aquabolt-XL >2× system perf, >70% less energy [V] → notes/q1 §3.1 (HC33 slides).
  - HBM2-PIM 6 GB vs 8 GB → notes/q1 delta 2 (fulltext AiM ISSCC'22, Fig. 11.1.6 comparison table).
  - DRAM-process logic (3× slower transistors, 3 metal layers) → notes/q5 §A1 (UPMEM HC31).
  - Vistara 0.13× $/GB nominal (Table V); up to 25% fewer servers for disaggregated ML inference → notes/q2 §1.4, §2.2; notes/r2d minor 14.
  - +~100–270 ns → slide 2 evidence. 2.7–3.6× less per GB: 614/768 = 0.8 vs 57.6–76/256 = 0.225–0.30 [D] → notes/r2d minor 15; SYNTHESIS §4 B1.
  - Mooncake +75% requests → notes/q4 §3.2. Pond 7–9% → notes/q5 §B2.
  - "most switch-based configurations are ROI negative" → fulltext/05-cxl-pools.pdf p.7; notes/q2 D1.
  - Switch ≥220 ns → notes/q5 §B3 (Octopus).

---

### Slide 08 — Coupling

- **Kicker:** `08 · Coupling`
- **Headline (exact):** The directions are coupled: A needs capacity tiers and competes for wafers; B pulls compute outward.
- **Sub:** none.
- **Body copy (exact), one short paragraph under each column's figure:**
  - Col 1: d-Matrix and the training stacks pair tiers the same way.
  - Col 2: ~3× the wafer per bit (HBM3E), >4:1 expected (HBM4E); one of three named drivers of the 2026 shortage.
  - Col 3 (list, no figure): Deployed in training: the optimizer runs at the capacity tier (ZeRO, MemoryX). Elsewhere, prototypes: CXL-PNM, BEACON (both sim.), XCENA<sup>V</sup>. Catch: a CXL controller sees ~3–17× its link bandwidth, not bank-level bandwidth.
  - (Body total ≈ 58 words; do not add.)
- **Layout:** three equal columns (`grid-template-columns: 1fr 1fr 1fr`, gap 28 px), each ~370 px wide. Column headings (17 px, bold): "A → capacity tiers" (`.label-near` "A" + arrow + `.label-far` "capacity tiers"), "A ↔ B share wafers", "B → compute at the far tier" (`.label-far`). Columns 1–2: figure (~370 × 260) then text. Column 3: text list in a `.box.conv` (convergence), with the "Catch" sentence set apart in `.small`.
- **Figures:**
  - **Col 1: GB200 NVL72 two-tier memory** (data `deck/data/gb200_tiers.json`). Two stacked mini-panels with shared row labels ("HBM3E" `--near`, "LPDDR5X behind NVLink-C2C" `--far`):
    - Panel "Capacity (TB)": horizontal bars, linear 0–20, values 13.4 and 17 labelled.
    - Panel "Bandwidth (TB/s)": horizontal bars, linear 0–600, values 576 and 14 labelled.
    - Note line: "≈43 vs ≈0.82 GB/s per GB [D]".
    - Caption: "GB200 NVL72 rack, per NVIDIA spec [V]."
  - **Col 2: HBM share of DRAM wafers vs bits** (data `deck/data/hbm_wafer_bit_share.json`). Grouped vertical bars for 2025, 2026, 2027 (forecast): "wafer input" solid `--near`, "bit output" `--near-soft` with `--near` stroke; y "Share of DRAM, top-3 suppliers (%)" linear 0–35; value labels 18/8, 22/9, 30/13. Mark 2027 bars hatched or with "(f)" label. Caption: "TrendForce, June 2026 press release [S]; 2027 is a forecast."
- **Footer (exact):** `GB200 NVL72 spec, nvidia.com [V]; d-Matrix HC'26 s13, s18 [V]; ZeRO-Offload ATC'21 [P]; ZeRO-Infinity SC'21 [P]; Cerebras MemoryX [V]. Shares: TrendForce 2026-06-02 [S]. Wafer ratio: Micron FQ2-25 remarks p.6, FQ1-26 p.5 [P/V]; shortage drivers: Handy HC'26 s4 [V]. CXL-PNM HPCA'24 [P, sim.]; BEACON MICRO'22 [P, sim.]; XCENA HC'26 s9, s13 [V]; ~3–17× [D].`
- **Builder model:** sonnet.
- **Do not say:** "HBM caused the shortage" (one of several causes; it amplifies B's payoff); "HBM set a floor under DDR prices in 2024–25" (it was reallocation pressure; a floor is only a conditional 2027 case); present the 2.5–2.9 ratio implied by the shares as independent confirmation of Micron's 3:1 (it is computed from them); "CXL adoption is driven by the shortage" (B1 designs predate it; the link rests on Marvell plus press); "CXL-PNM is a product" or "30% cheaper" (simulated; assumed $7,000 per card); "BEACON is more energy-efficient" (85–91% of MEDAL's efficiency); "compute at the far tier is deployed" outside training; "SSD offload escapes the shortage" (NAND +55–60% QoQ in 1Q26); "the LPDDR5X is far away" (it is a capacity tier by role).
- **Evidence (do not render):**
  - GB200 13.4 TB HBM3E at 576 TB/s; 17 TB LPDDR5X at 14 TB/s → notes/q5 §C4 (nvidia.com spec, read in full); notes/r3 §6. 576/13.4 = 43.0; 14/17 = 0.82 [D].
  - d-Matrix LPDDR5X secondary tier → d-Matrix s13 image (2 × 38.4 GB/s ports), s18 text "LPDDR5X as secondary tier" → notes/q1 delta 4.
  - ZeRO-Offload: optimizer on CPU, 13B on one V100 → fulltext/atc21-ren-jie.pdf; notes/r3 D4. ZeRO-Infinity 32T on 512 V100s → notes/r3 §3 (online). MemoryX optimizer in the external store → notes/r3 §2 [V].
  - Shares 18/8, 22/9, 30/13 (end-2025, end-2026, 2027) → notes/r2a §1.3 (TrendForce press release 2026-06-02, read online). Circularity → notes/r2d M7.
  - ~3× HBM3E, >4:1 HBM4E → fulltext/21-micron-mar2025.pdf p.6; fulltext/21-micron-dec2025.pdf p.5.
  - Three drivers incl. "HBM takes 3X the die area of DDR" → decks_text/2026-08-20_Handy…md slide 4; notes/r2a D1.
  - CXL-PNM simulated, assumed $7,000 vs $10,000 → fulltext/An_LPDDR-based_CXL-PNM…pdf p.980 Table III; notes/q5 D3.
  - BEACON 4.70×/4.13×, 85.58–91.26% of MEDAL's efficiency, simulated → notes/q5 D4.
  - XCENA: 4.7× vs host-over-CXL, 2× vs local DRAM; 268.8 vs 64 GB/s → notes/q1 delta 7.
  - ~3× (Structera A ~200 GB/s vs 64 GB/s link) to ~17× (CXL-PNM 1.1 TB/s vs assumed x16 Gen5 64 GB/s) [D] → notes/q5 D6 #7; SYNTHESIS §5.3.

---

### Slide 09 — Why both, why now

- **Kicker:** `09 · Why both, why now`
- **Headline (exact):** The 1990s had the same split; commodity DRAM let the middle outrun both, until ~2011.
- **Sub (exact):** IRAM vs network RAM, often from the same people: Patterson and Anderson; later Oskin, J. Chang, Ranganathan.
- **Body copy (exact), under the table:** Caveat: "the end of DRAM scaling" was declared before (1995, 2011). The difference now is a decade of measured slowdown, not the 2026 price spike.
- **Layout:** `.cols.c-4-6` reversed weighting: left chart (~470 × 430), right table (~660 wide) with the caveat below it.
- **Figure: DRAM $/GB over time** (data `deck/data/dram_price_trend.json`).
  - Preferred form: annual DRAM $/GB points (`--mid` dots joined by a thin `--mid` line), log y "DRAM price ($/GB, log scale)", x "Year", 1990–latest year in the data.
  - Overlay the two fitted rates as dashed `--ink-2` segments (pre- and post-break), labelled "−33 to −36%/yr" and "−12 to −15%/yr"; a thin vertical `--ink-3` line at 2010–2011 labelled "slowdown (~2011)".
  - **Fallback if the series is not available (`series_available: false` in the JSON):** draw only the two fitted segments, indexed to 1 at 2010, with the label "fitted trends, not data" in the caption, and narrow the panel to ~380 px.
  - Caption (exact, preferred): "DRAM $/GB, log scale. Data: McCallum (jcmit) [S]; fitted rates: AI Impacts 2020 [S]." (Fallback: "Fitted DRAM $/GB trends indexed to 2010 = 1 (not data). AI Impacts 2020 [S].")
- **Table (right, `table.t`), exact:**

  | | 1990s | Since ~2011 |
  |---|---|---|
  | **A** aimed at | the off-chip bus: 98.5 vs 4.55 nJ per access (IRAM) | bandwidth capped by pJ/bit and die edge |
  | **B** aimed at | disk: remote page 1.44 ms vs 3.6–14.3 ms | DRAM $/GB, stranding, capacity per core |
  | **Middle** | $/bit −33–36%/yr: commodity machines "outran them" | $/bit −12–15%/yr |
  | **Markets** | games, PDAs, embedded vs science clusters | AI accelerators vs CPU cloud |
  | **New enablers** | — | packaging decouples DRAM and logic processes; AI on vendor-controlled stacks; CXL standard; hyperscalers own the OS |

  Shade the A row label `.label-near`, B row `.label-far`, Middle `.label-mid`.
- **Footer (exact):** `Fromm et al. ISCA'97 Table 5 [P]; GMS SOSP'95 Table 1 [P]; NOW IEEE Micro'95 [P]; IRAM Micro'97, ICCD'97 [P]; NDP workshop, IEEE Micro'14 p.39 sidebar [P]; Wulf & McKee 1995 [P]; Ranganathan, IEEE Computer'11 p.42 [P]; SeDRAM IEDM'20 Fig. 8 [P]; $/GB: McCallum data, AI Impacts fits [S]; "middle outran both" and enabler list [Syn].`
- **Builder model:** sonnet.
- **Do not say:** "both waves were killed by the same thing" as a sourced fact (it is [Syn]; each wave had its own kill conditions); "the 1990s split did not follow market lines" (it did); "the 2026 shortage explains why now"; quote "Moore's law … outran them" without its first clause (the 1990s designs did not fade for lack of performance or efficiency, but on economic affordability); "software DSM died" (stayed niche); cite Aguilera et al. for A/B complementarity (they never mention near-memory compute); "Ranganathan's Nanostores proposed disaggregation" (it is near-data compute plus scale-out).
- **Evidence (do not render):**
  - 98.5 vs 4.55 nJ → notes/r2c §1.2 (Fromm et al. ISCA'97 Table 5).
  - 1.44 ms vs 3.6–14.3 ms → notes/r2c §1.2 (GMS SOSP'95: 1,440 µs vs 3,600–14,300 µs).
  - Markets (games/PDAs/embedded vs science clusters) → notes/r2c C5.
  - Same people → notes/r2c §1.1 table; Ranganathan → r2c D1.
  - "outran them … with economic affordability", with first clause → notes/r2c C2 (NDP'14 p.39 sidebar).
  - −33–36 / −12–15 %/yr → notes/q3 §1 [S]; break "2011 or so" (Handy blog) vs AI Impacts 2010 split → notes/q3 §1.
  - Wulf & McKee 1995; Ranganathan 2011 p.42 → notes/r2c C6.
  - SeDRAM 25 nm DRAM on 55 nm logic → notes/q3 D2; fulltext/A_Stacked_Embedded_DRAM_Array…pdf Fig. 8.
  - Enablers list → SYNTHESIS §6 "Why now differs" [Syn].

---

### Slide 10 — Implications

- **Kicker:** `10 · Implications`
- **Headline (exact):** Specify memory by frontier position, not attach point; three observations would refute this account.
- **Sub (exact):** A useful spec sheet: GB/s per GB, $/GB, pJ/bit with its scope, idle and loaded latency.
- **Body copy (exact), one line under the left table:** On server CPUs, any product between the DIMM and HBM must beat MRDIMM-12800 + a large SRAM LLC + CXL.
- **Layout:** `.cols.c-6-4`. Left: predictions table. Right: "What would count against this account" table. Both `table.t` at 15 px.
- **Left table (exact):**

  | Hedged prediction [Syn] | Evidence confidence |
  |---|---|
  | Passive HBM with logic base dies persists (A1) | High: deployed at scale |
  | Single-host CXL expansion and compression persist (B1) | High: deployed at scale |
  | A keeps creating capacity tiers and competing for wafers | Medium: coupling claims |
  | Compute at the far tier grows where software is vertically integrated, as in training | Medium: deployed only in training |
  | PIM (A2) and pooling (B2) stay niche outside vertically integrated AI stacks | Low: economics mostly simulated or modelled |

  Render the confidence cell as a pill: High `--conv-soft` bg, Medium `--mid-soft`, Low `--far-soft` (these are fills only, carrying no A/B meaning; if the orchestrator prefers, use neutral `--panel` for all three and bold text).
  **FLAG G9:** colour on confidence pills reuses semantic soft fills; default to neutral `--panel` unless approved.
- **Right table (exact):**

  | Observation | Would mean |
  |---|---|
  | CXL capacity tiers keep growing after DRAM $/GB returns to trend | B is driven by something else (fault isolation, lifecycle) |
  | CPU HBM returns while MRDIMM keeps scaling | The middle did not catch up |
  | PIM wins in silicon at iso-stack, iso-capacity baselines | A2 stands on its own, apart from A1's trade-offs |

- **Footer (exact):** `SYNTHESIS v1.2 §8 and Confidence [Syn]. Evidence basis: A1/B1 deployed (slides 5, 7); coupling (slide 8); A2/B2 economics mostly simulated or modelled (AttAcc, CXL-PNM, Berger, Octopus). Pending full texts: Kwon ISSCC'21 (PIM area, pJ/bit), d-Matrix Raptor ISCA'26 (scope of 0.37 pJ/bit).`
- **Builder model:** opus.
- **Do not say:** "will" without hedging; "PIM will win" / "pooling is dead"; price forecasts; "optimal"; "the middle displaced HBM" (say "caught up" or "outgrew").
- **Evidence (do not render):** SYNTHESIS §8 (implications, prediction, what would count against); exec. summary "Confidence"; MRDIMM-12800 + SRAM LLC + CXL → SYNTHESIS §8 implications, notes/r3 D1.

---

## E. Figure data specs

**Common rules for every figure-data agent.**
- Two independent extractions per figure (PLAN Phase 1). The second agent works blind from the same sources; disagreements beyond the stated tolerance go back to the source, and the resolution is recorded in the JSON (`extraction.cross_check`).
- Never use an abstract where the full text is needed. If a needed full text is not readable, write `"status": "FULL TEXT NEEDED: <item>"` and stop for that row.
- Copy online-read sources into `fulltext/` when you fetch them (PDF or HTML), and record the path.
- Values read from a chart image must state the axis type and the read method, and carry an uncertainty.

**Common JSON envelope** (`deck/data/<id>.json`):

```json
{
  "id": "<id>",
  "version": 1,
  "slides": ["sNN"],
  "title": "…",
  "units": {"<field>": "<unit>"},
  "conventions": "…",
  "sources": {
    "<key>": {"cite": "Author, venue year", "path": "fulltext/… or URL", "locator": "p./fig./table/slide", "tag": "P|P/V|V|Pr|S|D"}
  },
  "rows": [
    {"…fields…": "…", "src": "<key>", "loc": "…", "tag": "…", "unc": "exact (printed value) | ±x% (log-axis pixel read) | range as published | derived: <formula>", "note": "…"}
  ],
  "extraction": {"agent_a": "…", "agent_b": "…", "method": "…", "cross_check": {"max_disagreement": "…", "resolved": "…"}},
  "caveats": ["…"]
}
```

| # | Data file | Slides | Fields (units) | Source paths and locators | Read from image? (axis type) | Uncertainty field |
|---|---|---|---|---|---|---|
| E1 | `deck/data/latency_ladder.json` | s02 | rows: `path` (label), `role` (`mid`/`near`/`far`), `kind` (`measured`/`estimate`/`modelled`), `lo_ns`, `hi_ns`, `points_ns` [array], `open_ended` (bool), `platform`, `granularity` (e.g. "64 B", "4 KB page") | PolarCXLMem `fulltext/3722212.3724460.pdf` Tables 1–2 p.4; Fastswap `fulltext/12-fastswap.pdf` p.13 fn.7; ByteDance `fulltext/26913_Exploring_Performance_an.pdf` pp.5–6; Berger `fulltext/05-cxl-pools.pdf` pp.5–6. Online (copy to fulltext/): Octopus arXiv 2501.09020 / NSDI'26; Melody (VT PDF in notes/q3 sources); Vistara ISCA'26 camera-ready; Pond ASPLOS'23 (scratchpad `p/pond.pdf`); TPP Fig. 2 (scratchpad `p/tpp.pdf`); Xeon Max arXiv 2505.14294 (**extract absolute HBM vs DDR pointer-chase ns if the paper prints them; else leave the row out and keep the slide callout relative**); Das Sharma arXiv 2306.11227 Table 2; Chips and Cheese H100/GH200/Grace [S]. Notes: q3 §4, q2 §1.6, r2d M4 | No (text and tables). If any value comes from a plot, state log/linear axis | per row: "exact (printed)" or "range as published"; Chips and Cheese rows "[S] approx." |
| E2 | `deck/data/compute_bw_growth.json` | s03 | `series`: [{`name`, `rate_per_2yr` (×), `total_20yr_stated` (×)}]; optional `points`: [{`device`, `year`, `series`, `value_norm` (× vs R10000 1998)}]; `points_verified` (bool) | Gholami et al., IEEE Micro 2024 / arXiv 2403.14123 (**fetch PDF to fulltext/**), text for the rates; Fig. 1 for points. Notes q3 §5, q1 §1.3 | Rates: no (stated in text). Points: yes, **log y-axis, linear time x**; read in log space | rates "exact (stated)"; points "±10% (log-axis read)"; set `points_verified: true` only if both extractions agree within ±15% |
| E3 | `deck/data/hbm_bw_energy.json` | s03 | `points`: [{`name`, `pj_per_bit`, `tbps`, `kind` (`ddr`/`hbm`/`d3`), `scope_note`}]; `extrapolations`: [{`label`, `pj_per_bit`, `tbps`, `watts`}]; `iso_power_w`: [100, 300, 1000, 2000]; `die_geometry`: {`reticle_mm`: [26, 33], `stack_mm`: [11, 12.8], `stacks_per_long_edge`: 3, `phy_mm`: [8, 4], `phy_inset_mm`: 1, `px_per_mm`: 6} | **Copy verbatim** from `explainers/hbm-beachfront-power.html` Fig. 3 `pts` array and the two `hol(...)` calls; Fig. 2 geometry constants. Originals: d-Matrix HC'26 s6 image `fulltext/hotchips26/lightrag_server/slide_assets_vision/source-tutorials-hc2026-dmatrix-sudeepbhoja-v3-0-2fb8d890/slide-006.jpg`; s23 text; Samsung HC'26 s9–s11, s23 (`decks_text/HC2026.HBMBaseDie.SangwookHan.v20.pdf.md`) | Already read: s6 is a **log-log** chart, pixel reads ±5% (review_r1 #21). No re-extraction; agent B re-checks 3 points against the image | `unc`: "±5% (log-log pixel read, review_r1 #21)"; zHBM "derived from chart axes ±5%"; extrapolations "derived" |
| E4 | `deck/data/meta_memory_share.json` | s04 | rows: `gen` ("Gen0"…"Gen5"), `tco_pct`, `power_pct` | TPP, ASPLOS'23 (arXiv 2206.02878), Fig. 3, p.2. Local copy: `/private/tmp/claude-501/-Users-kamil/ba12f6ed-f246-4808-b610-1c1bf1dfb934/scratchpad/p/tpp.pdf` (**copy to fulltext/tpp-asplos23.pdf**) | Printed data labels, so no read error. **Verify series mapping by rendering p.2** (`pdftoppm -f 2 -l 2 -r 200`): orange = Cost (TCO), black = Power (notes/q2 §1.1) | "exact (printed data label)"; note "non-monotonic" |
| E5 | `deck/data/tier_frontier.json` | s05 (also report) | rows: `tier`, `example`, `role` (`near`/`mid`/`far`/`conv`), `status` (`shipping`/`discontinued`/`announced`), `gbps_per_gb_lo`, `gbps_per_gb_hi`, `gbps_per_gb_point` (+ `point_label`), `basis` ("peak per direction" / "measured" / "vendor peak"), `inputs` (capacity, bandwidth, formula), `usd_per_gb_rel` (string), `usd_tag`, `idle_latency` (string), `lat_tag`, `energy_pj_bit` + `energy_scope` (report only, not plotted) | Deck texts: AMD MI400 system s3, s5; Rubin s8; Vera s28; DMR s10, s15; d-Matrix s13; LP30 s24 (`fulltext/hotchips26/decks_text/`). Vistara Table V (online). Xeon Max arXiv 2505.14294. Notes: r3 D7, q4 §5.3, r2d F2, r2a §2.2; SYNTHESIS §2 table | No (spec arithmetic). Every value is `[D]` with its formula | per row: "derived: cap ÷ BW from <source>"; ranges "config-dependent: <configs>" |
| E6 | `deck/data/skew_stats.json` | s06 | rows: `label`, `source`, `metric` (bandwidth / reuses / accesses), `window` (e.g. "30 s"), `population` (e.g. "9 services"), `x_bytes_pct_lo`, `x_bytes_pct_hi`, `x_bound` ("≤"/"="/"range"), `y_activity_pct`, `y_bound` ("≥"/"≈"/"="), `measured_or_emulated`, `restatement` (how x,y follow from the published sentence) | Mahar arXiv 2303.08396 (online; copy); Wang et al. ATC'25 arXiv 2506.02634; Hao `fulltext/3639286.pdf` PDF p.16 §5.5; Lagar-Cavilla ASPLOS'19 (Google PDF); TPP §3; Mooncake arXiv 2407.00079. Notes q4 §1, §3.2, Round-4 delta 1; r2d M10 | No. Each point is a **restatement** of a published sentence; quote the sentence in `note` | "derived restatement; source sentence quoted"; Google range 1–61% cold |
| E7 | `deck/data/gb200_tiers.json` | s08 | rows: `tier`, `capacity_tb`, `bandwidth_tbps`, `gbps_per_gb` [D], `attach` | NVIDIA GB200 NVL72 spec page (nvidia.com; save HTML to fulltext/); notes q5 §C4, r3 §6 | No | "exact (vendor spec)"; derived ratios "derived" |
| E8 | `deck/data/hbm_wafer_bit_share.json` | s08 | rows: `year`, `wafer_share_pct`, `bit_share_pct`, `basis` ("end of year" / "forecast"); `micron_ratio`: {`hbm3e`: 3, `hbm4e`: ">4", `locators`} | TrendForce press release 2026-06-02 (online; save to fulltext/) [S]; Micron `fulltext/21-micron-mar2025.pdf` p.6, `fulltext/21-micron-dec2025.pdf` p.5. Notes r2a §1.3, D1 | No | "exact (as published) [S]"; flag 2027 "forecast"; caveat on circularity (r2d M7) |
| E9 | `deck/data/dram_price_trend.json` | s09 (also s04 text) | `series`: [{`year`, `usd_per_gb`, `src`}] (annual, 1990–latest); `fits`: [{`period`, `rate_pct_per_yr`, `dataset`}] (McCallum −36 / −15; Objective Analysis −33 / −12); `break_year`: "2010–2011"; `series_available` (bool) | Data: John C. McCallum, "Memory Prices" (jcmit.net) [S] via WebFetch; fits: AI Impacts 2020 "Trends in DRAM price per gigabyte" [S]. Notes q3 §1. Kamil approved tagging [S] (RESEARCH_LOG). **If neither page is retrievable, set `series_available: false` and include only `fits`.** | If values come from a table: no. If only from a chart: **log y, linear x**, ±10% | "[S] as published" or "±10% (log-axis read)" |

Nine figures. Slide 01 and slide 07 use no data file; slide 10 uses none.

---

## F. Companion report outline (`deck/report.html`)

Mirrors slides 1–10; each section opens with the slide's headline as its claim. Shared figures are rebuilt from the same `deck/data/*.json`. Every number tagged inline in the report (unlike the deck). Target ~6–8k words.

**R1. The question and the answer**
- The paradox as posed; definitions of A (HBM, 3D DRAM-on-logic, NMP/PIM) and B (CXL expansion and pooling, compression tiers, RDMA far memory).
- The answer: two-axis frontier (GB/s per GB, $/GB) under a latency constraint; skew per object; persistent middle.
- How the thesis evolved: v0.1 "single access-density axis" → r2d F1–F3 (ρ vs I conflation; every tier bundles; deployed B is not far) → 2-D frontier and the 2×2.
- Confidence statement (High A1/B1; Medium coupling; Low A2/B2 economics).

**R2. Distance is a proxy**
- Physics: flight time (~5 ns/m); device core ~50 ns; controllers, SerDes (21–25 ns per port round trip), retimers (~10–20 ns), switches (≥70 ns each by spec; ≥220 ns measured adder).
- Full latency table (SYNTHESIS App. A) with measured vs spec vs modelled columns; loaded latency (DDR5 at 60% load: 160 ns avg, 285 ns p90, COAXIAL); CXL tails (p99.99+ ~1 µs); switched pool under contention (MemChannel 230 → 1,624 ns, flagged G4).
- Xeon Max as the unconfounded case; why GPU and Grace numbers are confounded.
- DRAM never bought latency: tRCD −30.5%, tRC −26.3% while $/bit fell 16× (Chang 2016).
- Counter-evidence: Colloid (loaded local tier can be 2.5× the alternate tier's latency; CXL as bandwidth source); Berger TC'26 latency-cliff data (median slowdown ~45% at 390 ns).
- "Far is a role": GB200 LPDDR5X; SOCAMM (socketed, not on-package).

**R3. Pressure 1: bandwidth crosses a boundary**
- First principles (SYNTHESIS §2.0): BW = wires × rate; P = BW × E_bit; wire count ∝ edge/pitch or area/pitch²; pitches (1 mm ball, ~36–55 µm bump, 3 µm hybrid bond at 110,000 vias/mm²).
- Layered binding limit table: CPU+DDR (pins, rate: 4 → 16 channels, 1DPC, MRDIMM, CXL ~4× per pin per direction); GPU+HBM (wires per mm of die edge at a given reach, power as co-limit); in-device (IDD7, tFAW; AttAcc's 9× internal BW capped by IDD7; refresh doubling per ~10 °C).
- Per-attachment arithmetic: 32× wires, ~38× bandwidth, ~7.6 Gb/s per pin on MI455X vs 6.4 for DDR5-6400.
- Reach: O'Connor §3.5 (5–7 mm unterminated); Samsung PHY floorplans (6 → 8 mm at HBM2→HBM3; DQ doubled inside 8 mm at HBM4); SPHBM4 trade.
- Power: 1.92 kW at 2.4 pJ/bit; trend fit 1.15–1.25 kW (regression-direction sensitivity); 3.4–4 kW at d-Matrix's s28 figure (scope unstated); Samsung 1,200 W GPU + ~220 W DRAM; zHBM reinvests ~100 W.
- Energy scopes, like-for-like: DDR4 full 16.5–21 vs HBM2 full 3.92 (~4–5×); I/O ≈5 vs 0.3 (device-level, Lee Fig. 2a) but 46% of dynamic power at channel level with 8 ranks (Lee p.31391) → "about half or more of the gap is inside the die"; interface-only comparisons (0.37, 0.66–0.88, HBM2 0.80@50%, HBM1-era ~1.5 incl. PHY).
- DRAM-core energy after the interposer (88% of HBM2); FGDRAM 1.95 pJ/bit from core redesign; 2.8 kW of core energy at 100 TB/s.
- Gholami rates and caveats (product mix of the DRAM line unstated; 410× parameter growth fitted 2018–22); why Micron's HC'26 chart is not used.
- d-Matrix chart caveat (G3): DDR points are per channel.
- Pending: Raptor ISCA'26 (scope of 0.37); HBM4's own interface energy not in sources.

**R4. Pressure 2: capacity cost**
- $/bit series and fits (AI Impacts; Handy "2011 or so"); density ~2× per decade vs ~100× historically [S]; TechInsights die densities [snippet; do not plot].
- Meta TPP shares (non-monotonic); Azure "50%" circular chain; GreenSKU/Memstrata carbon shares (35%, 41%) and Vistara 69% lifecycle, with the GreenSKU-CXL +1% net result.
- Capacity per performance (Berger TC'26); DIMMs per channel at speed; 3DS DIMM premium; Fastswap upgrade granularity (+25%/+50%).
- Stranded vs cold vs idle vs captured, kept separate: Pond stranding (6% at 75% cores, >10% at 85%, p95 25%); Google 32% cold (1–61%); TPP 55–80% idle; production 20–33% (zswap, TMTS, TPP) — FLAG G6.
- Why captured ≪ idle: re-access (80% of Web pages within 10 min), migration cost (TPP near one host core per VM, Memstrata), tail SLOs.
- The 2025–26 shortage as context only (cyclical spike vs structural slowdown).

**R5. The cost frontier**
- Full tier table with all columns (GB/s per GB, $/GB, pJ/bit with scope, idle latency), conventions (peak vs sustained; per direction vs bidirectional; configuration choices).
- Derivations for every [D] row (formulas in tier_frontier.json).
- Observations: CXL not a new density point (Vistara 0.225 vs 0.2); energy per bit for CXL ~1.3–2× local DDR (22–44 direct, 28–67 switched; Intel Gen5 SerDes 11.4 pJ/bit per lane) so its energy case is system-level only; the middle improved (MRDIMM 1.6 TB/s ≈ Xeon Max HBM peak, ~2× its STREAM); DDR squeezed, not unstable (wiring regimes; serial-attach graveyard: FB-DIMM, RDRAM, HMC, OMI; CXL Type-3 the first to stick).
- HBM CPUs not continued (Diamond Rapids, MONAKA) and the four competing explanations (work moved to GPUs; HBM rationed; Intel roadmap cut; HBM-on-CPU survives as APU [unverified]); "consistent with", not proof.
- Raptor ~3,100 GB/s per GB as vendor peak (G8); SRAM as scratchpad.
- Five-minute rule as the capacity-side version of the frontier.

**R6. Skew and placement**
- Each skew statistic with its window, population, metric and status (measured, emulated, preprint).
- ρ × I × MLP placement table with worked examples: decode attention (MHA vs GQA; batch dependence of weight GEMVs); prefill; embeddings (MTrainS: big tables low bandwidth); graphs; KV reuse (Alibaba P99 lifespan 97 s; ~2× HBM capacity approaches ideal hit rate vs LMCache "far exceeding").
- Read vs write density (HeMem: Optane 32/11.2 GB/s vs DRAM 107/80).
- Economic rules: five-minute-rule history (15 min 2007 → ~41 s NVMe 2017 at 4 KB; Zhang 2025 ~34 s at 512 B CPU+DDR, ~5 s GPU+GDDR, preprint) and Hao's bandwidth-saturation rule (emulated; intro states it in reverse; can lose at 0.8× price).
- ZeRO-Infinity's per-data-class bandwidth model as a worked instance of the placement rule.
- Counter-evidence: Colloid (CXL as bandwidth); Pond within-class variability; dense weights uniform.

**R7. Deployed vs ambitious**
- Each 2×2 cell expanded (SYNTHESIS §4): evidence, cost, status, with tags.
- A2 detail: AttAcc baselines (1.72× vs 2×DGX; 2.01–2.81× vs half-stack DGX_Large; 3.49–5.93× vs 640 GB DGX; GQA erosion; 10.84% die area, ~25% at HBM-PIM sizing); Newton 54× vs realistic GPU but 10× vs ideal; PrIM vs a 4-core Xeon E3; AiM "up to 10×" from FPGA emulation; LPDDR5X-PIM 3.01× on Samsung's SoC, no energy data; Boroumand 62.7% (mobile, 2018; PIM results gem5-simulated); SK hynix VP remarks (interested party); UPMEM → Qualcomm [S].
- B1 detail: MXT (2:1–6:1; >40% system cost cut); zswap (~20% of data, 4–5% memory TCO); TMTS; Vistara costs (10× less absolute bandwidth; +92% idle, +60% loaded latency); ByteDance (Spark 1.4–9.8× slower interleaved; 26% TCO is modelled); Fastswap (local beats far by 2.5–9.7%); GreenSKU +1%; Optane lesson ($559M impairment; must be cheaper to make).
- B2 detail: Pond 7–9%; Berger ROI model (switched 8-socket ~2.5× cost; small MHD pools ROI-positive; >25% local); Levis break-even (12.6 TB savings; 24-node pool even with free RAM); Octopus 3.0–5.4% (simulated); PolarCXLMem prototype (2.1× vs RDMA; authors call switch latency negligible for cloud DB); Mooncake; tail +10–82%; Carbink ≤35% memory for fault tolerance; graveyard (The Machine, Gen-Z, Intel RSA on silicon photonics).
- The pattern and the mirror-image critiques; computational-storage precedent (CIDR'21).

**R8. Coupling**
- 5.1 A creates capacity demand: GB200, d-Matrix LPDDR5X tier, SOCAMM2 (>50 TB per rack; >80% lower TTFT from KV offload on GH200 [V]), ZeRO-Offload/Infinity, Cerebras MemoryX; GPU scale-up domains as memory-semantic disaggregation inside A (NVL72 1.8 TB/s per GPU; NVLink 6 3.6 TB/s; Helios "distributed shared memory"; UALink 1,024 accelerators; SiDP, SwiftCache as research); "at fixed cost and beachfront" qualifier (HBM per GPU still rose 80 → 288 GB).
- 5.2 Wafers: Micron ratios (primary, interested seller); decomposition 2.1–3.7× (area, front-end yield, stack yield, base die); TSV ×5.0 (SK hynix s11); shares and the circularity caveat; reallocation pressure in 2024–25 (HBM:DDR price ratio >4× [S]; "margin accretive" [S]); 2026 overshoot; price-parity formula P_DDR ≈ P_HBM/[r(1+c)] with the sensitivity table (App. B); who benefits (recycled DDR4, compression) and who does not (new-DDR5 CXL, SSD offload); causal link to CXL adoption rests on Marvell + press.
- 5.3 Compute pulled outward: Miller et al. "required optimization"; Teleport (up to 11×), PULSE (9–34× lower latency), CXL-ANNS (3.8× vs oracle DRAM-only); CXL-PNM, BEACON, XCENA, Structera A; counter-point (channel-level bandwidth only; communication energy; computational-storage precedent).

**R9. Why both, why now**
- 1990s detail: IRAM (latency 253 vs ~60 ns; 100–200 GB/s internal vs 1.2 GB/s; 30–70% area and 30–100% speed penalty), NOW/GMS (baseline disk; 10–30% slower than local; 5–10× faster than disk), DSM/CC-NUMA (remote/local ratios 2.9–14.5×).
- Kill conditions per wave (table from notes r2c §2.6); what survived (eDRAM caches, CC-NUMA, HBM over HMC).
- What differs now ([Syn]): $/bit slowdown (measured, decade-long); packaging decouples processes (SeDRAM; base dies on foundry logic); AI anchor workload on vendor-controlled stacks; CXL standard absorbing Gen-Z/OpenCAPI/OMI; hyperscalers own the OS; CXL.mem's remote/local ratio (~2×) is below SGI Origin's (~3.6×), so CXL expansion reincarnates the branch that survived.
- Precedent caveat (Wulf & McKee 1995; Ranganathan 2011); Aguilera's "burning issue + feasible technology" test fits B, not A.
- Alternative explanations weighed (SYNTHESIS §7): different markets (first-order, compatible); vendor incentives (decommoditization, product categories; hype residue); different communities (simulation vs traces; no quantitative unifying paper).

**R10. Implications**
- Spec sheet recommendation; the server-CPU re-entry bar (MRDIMM-12800 + large SRAM LLC + CXL).
- Hedged predictions with confidence; what would count against.
- Open questions (SYNTHESIS §8): pooling break-even at 2026–27 prices; does an HBM-parity floor bind in 2027; a quantitative ρ × I × MLP model with Hao's rule; can DRAM-on-logic's capacity cost be offset by LPDDR/CXL tiers; what workload would justify HBM on a server CPU again.
- Tensions to watch: PCIe-lane competition (CXL bandwidth expansion needs ~64 Gen5 lanes); rack power and thermal envelope.

**Appendices**
- **App. A: Method and provenance.** Research rounds (Q1–Q5, R2a, R2c, R2d, R3, R5), two adversarial reviews, the beachfront explainer's three review rounds; tag legend; energy-scope legend; how chart values were read (pixel reads in log space, two independent extractions, tolerances); what "read in full" means and where each source was read (fulltext/ vs online).
- **App. B: HBM price-parity arithmetic.** Formula, r and c definitions (r already includes yield; c non-wafer only), sensitivity table (SYNTHESIS App. B), reading by year, Handy spot-series caveat (legacy spot, illustration only).
- **App. C: Five-minute rule and Hao et al.** Break-even history; Hao's provisioning rule and evidence quality.
- **App. D: Full reference list.** Grouped by topic; each with venue, year, locator used, tag, and storage (fulltext/ path or "read online").
- **App. E: Pending full texts.** Kwon ISSCC'21 (PCU area, measured pJ/bit, all-bank activation energy); d-Matrix Raptor ISCA'26 (scope of 0.37 pJ/bit and the HBM4 baseline); Tirmazi Borg EuroSys'20 and Lu "Imbalance in the Cloud" BigData'17 ("~60% utilization" is illustrative only); A64FX application papers (BWA-MEM2 TCBB'23, VPIC JOCS'22); SemiAnalysis paid section; TrendForce paid reports; Citi/Goldman notes; Cherian recording; Choi 2023 preprint (AttAcc 3.19×); MemChannel NSDI'26 (switched-pool latency, G4). Also list sources read online but not stored (G1).
- **App. F: What would change our mind.** The three falsifiers, plus weaker signals (HBM prices fall and r falls with yield learning; 3D DRAM restores steep $/bit declines; switched CXL pools show positive ROI in production).

---

## G. Flags and open issues for the orchestrator

| # | Issue | What the brief does | Decision needed? |
|---|---|---|---|
| G1 | Several sources used as [P] were read online and are **not in `fulltext/`**: Gholami, Pond, TPP, Vistara, Octopus, Melody, Mooncake, Xeon Max (arXiv 2505.14294), ZeRO-Infinity, Mahar, Berger TC'26. TPP and Pond PDFs exist only in this session's scratchpad (`scratchpad/p/tpp.pdf`, `pond.pdf`). | Figure agents are told to fetch/copy them into `fulltext/`. | Approve copying `scratchpad/p/{tpp,pond}.pdf` into `fulltext/` before Phase 1 (the scratchpad is session-scoped). |
| G2 | PLAN slide 5 asks for a log-log GB/s-per-GB vs $/GB scatter. Most $/GB values are [S] or unverified, so a scatter would fake precision. | Slide 5 uses a log range plot of GB/s per GB with an aligned $/GB and latency table; the 2-D picture stays schematic on slide 1. | Approve the change. |
| G3 | In the d-Matrix chart (explainer Fig. 3), DDR/LPDDR points are **one 64-bit channel** and GDDR6 one 384-bit bus, while HBM points are 8-stack packages. Not flagged in the three explainer reviews. | Slide 3 caption states it; "do not say" covers it. | Consider adding the same sentence to the published explainer's Fig. 3 caption. |
| G4 | notes/q3 §4 lists MemChannel (NSDI'26) measuring a **switched** pool at 220.5 ns idle (230 → 1,624 ns under contention). This sits below Octopus's "switch adds ≥220 ns" and PolarCXLMem's 549 ns and was never reconciled in the reviews. | Excluded from slide 2; noted in the report as unreconciled. | Optional: check MemChannel's topology (FULL TEXT NEEDED). |
| G5 | Xeon Max absolute latencies (ns) are not in the notes; only "~20% higher". | Slide 2 uses the relative statement; E1 asks the agent to extract absolute values if printed. | None. |
| G6 | SYNTHESIS exec. summary attributes "20–33% of DRAM at ≤1–5% slowdown" to "[P: TMTS, TPP, Memstrata]". Memstrata's design point is 1:1 (50% local); the 20–33% figures are zswap ~20%, TMTS 25%, TPP 33% (notes/q4 §1). | Slide 4 cites zswap, TMTS, TPP. | Fix the synthesis tag in v1.3 (minor). |
| G7 | HBM3E row uses B200 192 GB / 8 TB/s [V], but no NVIDIA document is in our files (review_r1 #20). | Kept as [V] with the note; GH200 [S] gives the low end. | None, unless a stricter tag is wanted. |
| G8 | New derived number: Raptor card 32 GB at 100 TB/s → ~3,100 GB/s per GB [D from V, s13], vendor peak (press says ~33 TB/s practical after overfetch [S]; s28 uses 83% effective). Not in the synthesis. | Plotted hollow on slide 5 as "vendor peak". | Approve, or drop the row. |
| G9 | Slide 10 confidence pills would reuse semantic soft fills for non-A/B meaning. | Default to neutral `--panel` fills with bold text. | Approve default. |
| G10 | Slide 9 DRAM $/GB series needs a web fetch (McCallum jcmit / AI Impacts) [S]; Kamil approved [S] tagging. | E9 has a fallback (fitted trends only, labelled "not data"). | None. |
| G11 | PLAN.md still names `SYNTHESIS_v1.1.md` as the source of truth; v1.2 is current (its §2.0 GPU+HBM row was corrected after the explainer reviews). | Brief uses v1.2 throughout. | Update PLAN.md reference (cosmetic). |
