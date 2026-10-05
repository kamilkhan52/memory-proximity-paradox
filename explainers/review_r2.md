# Review r2: hbm-beachfront-power.html

Reviewer: fresh technical-accuracy pass (memory systems / packaging), 2026-10-03.

Inputs checked:
- Page source, plus a headless render at 1100 px (`shots/r2.png`, crops `r2_0..5.png`) and a narrow-window render (`r2_mobile.png`).
- Samsung HC'26 base-die deck: s5, s9, s10, s11, s23 (text), and the s9, s10, s11 images.
- d-Matrix HC'26: s5, s6, s8, s17, s23, s25, s26, s28 (text), and the s5 image.
- SK hynix HC'26 s2 image, pulled from the handover zip.
- AMD MI400 deck text.
- UniIC IEDM'20 full text (pdftotext, Table 1).
- **O'Connor et al., FGDRAM, MICRO'17, full text.** I downloaded it from the NVIDIA URL recorded in notes q3 and read it in full. It is now at `scratchpad/fgdram.pdf`. Copy it into `fulltext/`, because it settles r1 #14.
- Notes q1 and q3.

Tags: [V] vendor slide, [P] primary paper read in full, [D] my arithmetic, [RK] reviewer knowledge (not file-verified).

---

## Part 1: Status of round-1 findings

| r1 | Status | Comment |
|---|---|---|
| 1 Samsung 1.2 kW = GPU power | Resolved | §3 and the table now read "1,200 W GPU" plus "≈220 W DRAM". |
| 2 zHBM is 3D, not a base die | Resolved | The table row and §4 are correct. |
| 3 Energy-vs-reach mechanism | Mostly resolved | The 0.5 pJ/bit/mm sentence is gone and reach is now the argument. However, the "2–5.5 mm PHY-to-PHY channel" carried over from r1 misreads Samsung's "CH depth". See #1 below. |
| 4 Trend vs constant 2.4 pJ/b | Resolved | The fit is verified. There is a minor method sensitivity (#9). |
| 5 d-Matrix energy scope | Partly resolved, one error introduced | The scope caption was added. But r1 called s28 a "like-for-like table", and that is wrong. The page now repeats it, adding "including on-chip delivery", which no source supports. See #3. |
| 6 Raptor is logic-on-DRAM | Resolved | |
| 7 Wires per stack = wires per mm of edge | Resolved in substance | It carries a factual slip inherited from r1: the PHY grew 6→8 mm at HBM2→HBM3, not with the doubling to 2,048 DQ. See #2. |
| 8 Reticle and interposer limits | Resolved | |
| 9 Host PHY on the host die | Resolved | The text and Fig. 1 now have the host PHY. Nit: the Fig. 2 die is labelled "compute die" (#14). |
| 10 Interface energy like-for-like | Not resolved correctly | "0.3 = wire alone" is the wrong scope. The "~1.5 incl. PHY" figure is an HBM1-generation number. The "~4×" therefore rests on mismatched generations. See #4. |
| 11 36 µm bump pitch | Resolved | It is now "[background]". A sourced HBM value is available (#12). |
| 12 ~20 TB/s practical limit | Resolved | |
| 13 Fig. 2 caption | Resolved | |
| 14 FGDRAM 3.75 vs 3.92 | Can now be closed | FGDRAM §1, verbatim: "each HBM2 access incurs 3.92 pJ/bit of energy (including ECC overhead)". Remove "confirming that needs the full text" (#11). |
| 15 DDR scope vs Lee 2018 | Resolved by removal | |
| 16 Samsung chart reads | Resolved | |
| 17 HBM4 doubling tagged [D] | Resolved | Now the per-pin rate point in §1. |
| 18 Thermal coupling of edge stacks | Not addressed | Still minor; see #13. |
| 19 O'Connor 2017 co-limit | Resolved | Verified in the full text (#11). |
| 20 Unsourced vendor figures | Resolved | |
| 21 Fig. 3 replot | OK | GDDR is no longer called "DDR". |
| 23–27 Visual | Resolved | The trace no longer strikes its label. The host PHY is drawn. Iso-power labels sit outside the frame. Halos are on. The legend has the extrapolation marker. |

---

## Part 2: New and remaining findings

### Major

**1. Major: Samsung's "CH depth" is read as the PHY-to-PHY channel length.**
- Elements:
  - Fig. 1 label: "PHY-to-PHY channel: 2–5.5 mm (Samsung s11)".
  - Fig. 1 caption: "faces the stack's base-die PHY across a 2–5.5 mm channel [V: Samsung HC'26 s11]".
  - Table column "Channel depth".
- Issue 1: in the s10 drawing ("B-die Floorplan of sHBM4"), the red 5.5 mm arrow runs from the base die's 11 mm edge to the far side of the HBM PHY block. The PHY is inset about 1 mm from that edge (pixel read: PHY spans ≈1.0–5.2 mm from the edge).
  - The HBM2 thumbnail in s11 is consistent with this. Its PHY box sits ≈2.2–3.7 mm from the die edge, against the stated CH depth of 3.5 mm.
  - So "CH depth" is the stack-side routing depth, from the stack edge to the back of the PHY bump field. It is not the full host-PHY-to-stack-PHY channel. The real channel also includes the die-to-stack gap and the host-side escape.
- Issue 2: "2 mm" is cHBM4's D2D interface, not an HBM PHY channel. Putting it on the HBM-PHY drawing mixes interface types.
- Correct statement: "Stack-side channel depth (edge to far side of PHY bumps): 3.5 mm HBM2, 4.5 mm HBM3, 5.5 mm sHBM4; 2 mm for a cHBM4 D2D interface [V: Samsung s10–11]."
- Better primary for reach: O'Connor §3.5 [P]. "HBM2 relies on simple unterminated signaling … the I/O currently employed by HBM2 can travel roughly 5 to 7 mm on an interposer while maintaining unterminated signaling at 2 Gb/s … a copper wire 1.5 µm thick, 1.0 µm wide, spaced 2.5 µm … is limited to about 5 mm."
- Fix:
  - Relabel Fig. 1 as "stack-side channel depth 3.5–5.5 mm (Samsung s11); total reach ~5–7 mm (O'Connor, HBM2)".
  - Rename the table column "Stack-side channel depth".
  - In §2, cite O'Connor §3.5 for "only works over a few millimetres" and for "unterminated". SK hynix s2 shows only "1024 IOs (Single-ended)", for HBM3E; it says nothing about termination.

**2. Major: "Doubling HBM to 2,048 DQ meant widening the PHY from 6 mm to 8 mm and lengthening the channel from 3.5 to 5.5 mm, going from HBM2 to standard HBM4 [V: Samsung s11]."**
- Issue: HBM2 and HBM3 both have 1,024 DQ.
  - The PHY length grew 6→8 mm at HBM2→HBM3, with no change in DQ count.
  - The doubling to 2,048 DQ (HBM3→sHBM4) happened at a **constant 8 mm PHY length**. The PHY got deeper (3→4 mm), the channel deeper (4.5→5.5 mm), and the footprint 10.75×10.75 → 11×12.8 mm [V: s11].
  - DQ per mm of PHY length went 171 (HBM2) → 128 (HBM3) → 256 (sHBM4) [D].
- Why it matters: this is the stronger, more precise version of the page's thesis. The doubling bought edge density with depth (more bump rows behind the same edge), and depth costs reach (#1). The binding quantity is therefore wires per mm of edge **at a given reach**.
  - O'Connor §3.5 states the trade directly: "Thicker and wider wires that use increased spacing can reach longer distances, but the achievable bandwidth density along the edge of the processor will decrease proportionally" [P].
- Fix: "From HBM2 to HBM3 the PHY grew from 6 to 8 mm long at the same 1,024 DQ; HBM4 then doubled DQ inside the same 8 mm by making the PHY deeper (3→4 mm) and the channel longer (4.5→5.5 mm) [V: Samsung s11]. Edge density is bought with depth, and depth is limited by reach: wider, longer-reach interposer wires reduce bandwidth per mm of edge [P: O'Connor §3.5]."

**3. Major: "At 5 pJ/bit, the figure d-Matrix uses for HBM4 in its own like-for-like table (40 mW per GB/s, including on-chip delivery): about 4 kW [D from V: s28]". Also "What to remember": "(≈4 kW at d-Matrix's like-for-like figure)".**
- Issue 1, not like-for-like: in the same s28 row, Raptor's 2.96 mW/(GB/s) ÷ 8 = **0.37 pJ/bit**, which is exactly the I/O-only figure of s23. So s28 sets 3D I/O-only against an HBM4 figure 13.5× larger. 40/2.96 = 13.5 is the slide's own "13.5x better power per GB/s".
- Issue 2, scope of the 5.0 is unstated: "including on-chip delivery" appears nowhere on s28. s8's "2.5 pJ + 3 pJ (on chip)" sums to 5.5, not 5.0.
- Issue 3, utilization basis: s28 footnote, "Effective-BW basis (Raptor 83%, Rubin 85% util.)". If 40 mW is per *effective* GB/s, the per-peak value is ≈34 mW/(GB/s) ≈ 4.25 pJ/b, giving ≈3.4 kW at 100 TB/s peak [D].
- Fix: "At 5 pJ/bit, the figure d-Matrix's s28 table uses for HBM4 (40 mW per effective GB/s; scope not stated, and set against Raptor's I/O-only 0.37), 100 TB/s would need ≈3.4–4 kW [D from V: s28]." In "What to remember", replace "like-for-like figure" with "s28 figure". Keep the FULL TEXT NEEDED pointer to Raptor ISCA'26.

**4. Major: interface-energy comparison in §4: "The HBM2 interposer wire alone is ~0.3 pJ/bit [P: O'Connor]. An HBM interface including its PHY is ~1.5 pJ/bit [P: UniIC IEDM'20]. Measured against the full HBM interface, the I/O gain is about 4×, not d-Matrix's '~10×'."**
- Issue 1, the 0.3 is not "wire alone":
  - FGDRAM §1 says "transferring the data over the I/O wires on the silicon interposer requires an additional 0.3 pJ/bit, considering actual application data toggle rates".
  - Table 3 gives HBM2 **I/O = 0.80 pJ/b at 50% activity**, the energy of "traversing the I/O channel between the DRAM and GPU".
  - Unterminated CMOS signalling energy is C·V²·activity, so 0.3 is the whole HBM2 I/O at real toggle rates, not a wire-only subset [P: O'Connor §1, Table 3].
- Issue 2, the 1.5 is HBM1-generation:
  - UniIC Table 1's HBM column is 4-high, 2 Gb/die, 1,000 Mb/s/pin, 128 GB/s, microbump 48 × 55 µm, ~5 mm path, "PHY needed: Yes", "~1.5 [2]".
  - This is an HBM1 interface quoted from UniIC's ref [2] [P: UniIC IEDM'20 Table 1].
- Consequence: both HBM figures are 2015–2017 generation and differ in scope. At equal activity (≈50% random data), Raptor 0.37 vs HBM2 0.80 is about 2×. Against HBM1's 1.5 it is about 4×. No HBM4 interface figure is in our sources.
- Fix:
  - "HBM2's whole I/O channel is 0.80 pJ/bit at 50% toggling and ~0.3 at real application toggle rates [P: O'Connor Table 3, §1]."
  - "An HBM1-generation interface including PHY is ~1.5 pJ/bit [P: UniIC IEDM'20 Table 1, citing its ref. 2]."
  - "Against these, Raptor's 0.37 pJ/bit measured I/O is ~2–4× lower, not d-Matrix's '~10×', which compares against its HBM4 'system' figure (2.5 pJ, or 5.5 with on-chip) [V: s8]. HBM4's own interface energy is not in our sources."
  - Tag Raptor's 0.37 with FULL TEXT NEEDED (Raptor ISCA'26) at this spot, not only in Sources.

**5. Major (logic): "3D stacking swaps edge for area and escapes both limits" (What to remember).**
- Issue: this contradicts the page's own §4 sentence, "Most of an HBM access is spent inside the DRAM anyway".
  - In HBM2, activation (1.21) plus on-die movement (2.24) is 3.45 of 3.92 pJ/b, i.e. 88% [P: O'Connor].
  - 3D bonding removes the edge limit and cuts the I/O term. It does not by itself remove the core terms.
  - At 100 TB/s, HBM2-like core energy alone would be ≈2.8 kW [D].
- Whether Raptor's DRAM (deep banking, 1,366-row subarrays, s26) also cuts activation energy is not stated in the slides. FULL TEXT NEEDED: Raptor ISCA'26.
- The expert point the page should make in one line: after the interposer, the power limit is mainly a DRAM-core problem (row activation and the on-die datapath), not an interface problem. Moving closer helps power only to the extent the DRAM die is redesigned (FGDRAM's 1.95 pJ/b came from shrinking activation and datapath, not I/O [P]).
- Fix: "3D stacking escapes the edge limit and cuts interface energy; the power limit only eases as far as the DRAM core itself (activation, on-die datapath) gets cheaper." Apply the same softening to the ESCAPE card if needed.

### Minor

**6. Minor: "About 2,048 DQ across an ~8 mm PHY is roughly 256 DQ per mm of die edge [D]" (§2).**
- Issue: 256 is per mm of *PHY length*, which is what the ladder card correctly says. Per mm of die edge, a stack occupies 11 mm, so the figure is 2,048/11 ≈ 186 DQ/mm, before any inter-stack spacing [D from V: s10–11]. Also, DQ are not all the signals: CA, ECC/parity, DBI and strobes add more.
- Fix: "≈256 DQ per mm of PHY, ≈190 per mm of die edge once the full 11 mm stack width is counted [D]."

**7. Minor: "A die's two 33 mm edges therefore hold about three stacks each."**
- 3 × 11 = 33 mm exactly, which leaves zero inter-stack spacing and a full-reticle die (real maximum dies are slightly under 26 × 33 mm). Six is therefore a ceiling, not a typical count.
- Optional supporting fact: MI455X's 12 stacks sit on 2 fabric/cache dies, i.e. 6 per base die [V: AMD "2x Fabric and Cache Die … 12 HBM4 stacks"], which is consistent with the page.
- Fix: "at most about three stacks each (3 × 11 mm = 33 mm, with no spacing)". Optionally add the AMD consistency note.

**8. Minor: zHBM energy "≈0.47 pJ/b".**
- 124 W ÷ (31.8 TB/s × 8) = **0.49 pJ/b** [D]. This appears in the table, the Fig. 3 point (`e: 0.47`) and the hover text.
- Fix: change to 0.49. The plotted position barely moves.

**9. Minor: trend fit "~1.44 pJ/bit, ~1.15 kW [D]".**
- Verified: least squares of log BW on log E over the five HBM points (6.8/1.0, 5.6/2.4, 4.2/6.5, 3.7/9.8, 2.4/18) gives slope −2.78, E(100 TB/s) = 1.44 pJ/b and 1.15 kW. r1's pixel reads give the same.
- Regressing log E on log BW instead (BW is the variable being targeted) gives 1.54 pJ/b and 1.23 kW.
- Fix: "~1.4–1.5 pJ/bit, ≈1.15–1.25 kW [D: log-log fit; depends on regression direction]". Also note that this extrapolates 5.5× beyond the last point.

**10. Minor: tags.**
- Fig. 1 caption "DDR5 pins per channel: 160–200 [P: notes q3]" tags a notes file as primary. Use "[S: COAXIAL SC'24; Das Sharma et al., via notes q3]".
- "single-ended, unterminated" (§2 and Fig. 1). SK hynix s2 supports "single-ended" for HBM3E only. "Unterminated" needs [P: O'Connor §3.5] (HBM2).
- Fig. 3 "3D points": "Raptor's 0.37 pJ/bit is measured I/O energy only [V]". Add "(scope: FULL TEXT NEEDED)".

**11. Minor: Sources item 5, FGDRAM: "(3.75 total) … The 3.92 commonly quoted includes overhead our notes attribute to ECC; confirming that needs the full text."**
- Now confirmed from the full text: "3.92 pJ/bit of energy (including ECC overhead)" (§1). The paper also gives "approximately 3.97 pJ/bit" earlier in §1, and HBM2 I/O 0.80 pJ/b at 50% activity (Table 3). The 60 W / 2 TB/s point and ">120 W at 4 TB/s" are verbatim.
- Fix: "1.21 + 2.24 + 0.3 = 3.75; 3.92 including ECC overhead [P: §1]". Drop "needs the full text", and store the PDF in `fulltext/`.

**12. Minor: microbump pitch "[background]" (Fig. 1 caption).**
- A sourced value exists: UniIC IEDM'20 Table 1 gives HBM "microbump, 48 × 55" µm pitch [P]. That table describes HBM1.
- Fix: "microbump pitch ~48 × 55 µm (HBM1-era, [P: UniIC IEDM'20 Table 1])". Alternatively keep [background].

**13. Minor (r1 #18, still open): edge-attached stacks share the host die's thermal envelope.**
- Samsung s11: "higher power density in smaller PHY areas creates thermal hotspots" [V].
- Fix: add one clause to §2's list: "Edge stacks also sit in the hot die's thermal footprint, and shrinking PHYs concentrate heat (Samsung s11)."

**14. Minor: Fig. 2 left panel labels the host "compute die".**
- §2 and its note correctly say that the HBM may attach to a non-compute base die (MI455X).
- Fix: label it "host die (SoC or base die)", or keep "compute die" and add "or base die" to the caption.

**15. Minor: Fig. 2, PHY drawn flush with the stack edge.**
- s10 shows the sHBM4 PHY inset ≈1 mm from the host-facing edge. The 5.5 mm depth is measured to the PHY's far side (#1).
- In HBM2 and HBM3E the stack PHY sits in a central stripe [P: O'Connor §3.5 "PHYs are located in a stripe near the middle of the base layer die"; V: SK hynix s2 floorplan].
- Fix: inset the orange block by 1 mm (6 px), or say "PHY position schematic" in the caption. The orientation is correct: the 11 mm side faces the die, the 8 mm PHY length runs along it, and three stacks sit on each 33 mm edge, exactly as in Samsung s9 and s10.

**16. Minor (nuance, optional, one clause): SPHBM4.**
- Its stated purpose includes "more stacks" as well as longer channels and capacity [S: notes q1]. Fewer, faster signals per stack (512 at 4:1) shorten each stack's share of the edge.
- This directly supports "one limit: wires per mm of edge" (here traded through rate per wire), at a SerDes energy cost.
- Fix: in §2, "…on an organic substrate, explicitly to allow longer channels and more stacks [S]".

**17. Minor (nuance, optional): kind of limit.**
- Beachfront is a hard geometric cap. Power is a budget that can be traded: Samsung's s23 moves the 100 W saving into the GPU.
- One sentence in §3 would make "co-limit" precise: "Power is a budget, not a wall: every watt of DRAM is a watt the package cannot give to compute (Samsung s23 reinvests the 100 W zHBM saving in the GPU)."

### Visual

**18. Minor: Figs 2 and 3 are illegible at phone width.**
- The SVGs scale to 100% width, so 11 px labels in a 940-unit viewBox render at about 4 px on a 360 px screen.
- `.fig` already has `overflow-x: auto`. Give the SVGs `min-width: 640px` (Fig. 3 perhaps 700px) so that they scroll instead of shrinking.
- The tables already scroll inside `.tablewrap`.

**19. Checked, OK at 1100 px.**
- Fig. 1: the board label is no longer struck. Host PHY and stack PHY face each other. The channel leader line is clean.
- Fig. 2: geometry matches the code (s = 6 px/mm; stack 76.8 × 66 px; PHY 24 × 48 px; die 156 × 198 px). No overlaps.
- Fig. 3: iso-power labels are outside the frame. The 1.92 kW hollow point sits just under the 2 kW line, as it should (1,920 W). Halos keep the "HBM4 (proj.)" and Raptor labels readable. Legend complete.

---

## Part 3: Numbers verified correct

| Item | Check |
|---|---|
| Wires and bandwidth per attachment | 32× wires (2,048/64); 38× BW (1.94 TB/s ÷ 51.2 GB/s = 37.9); 7.58 Gb/s/pin (1.94 TB/s × 8 / 2,048) |
| MI455X | 12 × HBM4, 432 GB, 23.3 TB/s; HBM4 on 2 fabric/cache dies, "192 Channel HBM4 Interface"; 3D hybrid-bonded XCDs; CoWoS-L [V: AMD] |
| Constant-2.4 pJ/b extrapolation | 100 TB/s × 8 × 2.4 = 1,920 W, matching s6 verbatim |
| Per-generation power | HBM points 54, 108, 218, 290 and 346 W, so "≈50 to ≈350 W" holds |
| MI455X at 2.4 pJ/b | 23.3 × 8 × 2.4 = 447 W, matching "≈450 W" |
| Samsung s23 | 1,200 W GPU assumption; HBM4E 218/(13.9 × 8) = 1.96, matching "≈2.0" |
| Raptor | 100 × 0.37 = 296 W [V s23]; 105 °C, 32 → 4 ms, 8× refresh [V s25–26]; logic on top, F2F 36 µm [V s17] |
| Capacity density | 11.4 vs 21.9–26.3 MB/mm² [V s28] |
| Samsung s9, s10–11 | s9: 6 HBM per 26 × 33 mm xPU, 11 mm stack side facing the die. s10–11 PHY sizes: 6×1.2, 8×3, 8×4, D2D 8.5×1.5; depths 3.5 / 4.5 / 5.5 / 2 |
| d-Matrix s5 | "2048 wires", "beachfront limited", "interposer size, warpage", "~20TB/s" |
