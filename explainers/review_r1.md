# Review r1: hbm-beachfront-power.html

Reviewer: technical-accuracy pass (memory systems / packaging), 2026-10-03.
Inputs checked: page source and a 1x/2x headless render (`shots/review.png`, `shots/review2x.png`); d-Matrix HC'26 slides 5, 6, 8, 17, 23, 28 (images) and deck text; AMD MI400 deck text; Samsung HBM base-die deck text (s5, s9, s10, s11, s21–24) and s23 image; notes q1, q3; Lee 2018 PDF (pdftotext).
Tags in this file: [V] vendor slide, [P] primary paper, [D] my arithmetic, [RK] reviewer knowledge, not verified against a file in `fulltext/`.

## Blocking

1. **blocking — Samsung "1.2 kW package budget" (§3 body; Figure 3 legend "1.2 kW: whole package power (Samsung example)"; Figure 3 caption "The 1.2 kW line is the whole-package power"; table row "~220 W of 1,200 W"; §4 "more than a whole package's power").**
   - What's wrong: 1,200 W is Samsung's **GPU** power assumption, not the package total. The slide footnote reads "Assumption : GPU Power 1200W, 4 HBMs in SIP". DRAM power is a separate bar (≈220 W). The "100W saving" arrow moves power into the GPU bar (1,200 → ≈1,300 W, "8.3%"). So the SiP total is ≈1.42 kW in both cases (1,200 + 220 ≈ 1,300 + 124).
   - Correct values: GPU 1,200 W; DRAM ≈218 W (HBM4E) and ≈124 W (zHBM); GPU ≈1,300 W after reinvestment; DRAM BW ≈13.9 and ≈31.8 TB/s. These are my pixel reads against the labelled axes (0–1400 W, 0–40 TB/s) [V: Samsung HC'26 s23 image].
   - Fix: relabel the line "1.2 kW: GPU compute power in Samsung's example (DRAM is extra)". Alternatively, draw the line at ≈1.4 kW and label it "≈1.4 kW SiP total (GPU 1.2 kW + DRAM), Samsung example". Change the table cell to "~220 W DRAM beside a 1,200 W GPU". The §3 sentence becomes "…1.92 kW for HBM alone, more than the 1.2 kW GPU in Samsung's example and more than its ≈1.4 kW GPU+DRAM total". The qualitative point survives.

2. **blocking — "Same, with Samsung's zHBM base die" (table row 2).**
   - What's wrong: zHBM is not a base-die variant. Samsung defines it as "true 3D vertical integration of the xPU and C-die stack, eliminating the 2.5D interposer" (s21). It uses wafer-on-wafer hybrid copper bonding of "4H C-die + xPU (or interlayer)" (s24), and it "eliminates conventional 2D interfaces (e.g., HBM PHY or D2D)" (s22). The s23 drawing shows the four zHBM stacks sitting **on top of** the GPU. The base-die (cHBM) work is Samsung's Phase 1–2. zHBM is Phase 3 [V: Samsung HC'26 s21–s24].
   - Consequence: this row is a 3D DRAM-on-logic data point, the page's "ESCAPE" step. It is not a 2.5D one. It is actually good evidence for the page's thesis: 4 stacks reach ≈32 TB/s once the edge is no longer the interface.
   - Fix: rename the row "Same GPU, 4× zHBM (DRAM hybrid-bonded on the xPU, no interposer/PHY) — Samsung projection". Move or colour it with the 3D tier. Mention it in the ESCAPE card or in the §4 note.

3. **blocking — §2 energy argument for edge placement: "Every millimetre of interposer trace costs about 0.5 pJ/bit [V: d-Matrix s8], so a stack moved 10 mm farther out would add roughly 5 pJ/bit [D], about double HBM4's whole budget."**
   - Attribution: the 500 fJ/mm figure is correctly quoted as per-bit-per-mm. d-Matrix s8's table column is "Energy / bit" and the row is "Interposer trace ~500 fJ/mm".
   - Problem 1, other sources disagree: the figure conflicts with the page's own other sources, by 3–10×.
     - O'Connor (FGDRAM, MICRO'17) puts the **entire** HBM2 interposer I/O at 0.3 pJ/bit (wire-only, toggle-adjusted). The page cites that in §4.
     - UniIC IEDM'20 Table 1 puts the HBM interface at "~1.5 pJ/b" for a ~5 mm microbump + interposer path **including PHY**, i.e. ≈0.3 pJ/bit/mm with PHY [P, via notes q3 D2].
     - d-Matrix's own s8 gives "2.5D HBM4 (system) 2.5 pJ". Samsung gives sHBM4 channel depth as 5.5 mm (s11). At 0.5 pJ/bit/mm, the interposer alone would use 2.75 pJ, more than d-Matrix's whole HBM4 figure.
     - First-principles check [RK]: α·C·V² with C ≈ 0.2 pF/mm and HBM4 VDDQ ≈ 0.7–0.9 V gives roughly 0.05–0.08 pJ/bit/mm for the wire.
   - Problem 2, wrong mechanism: energy is not the main reason the stack must abut the die. HBM DQs are single-ended ("1024 IOs (Single-ended)" [V: SK hynix HC'26]), unterminated and unequalized parallel links on RC-dominated fine-pitch lines. The channel is reach-limited by signal integrity and timing, and real channel depths are 2–5.5 mm (Samsung s11: HBM2 3.5 mm, HBM3 4.5 mm, sHBM4 5.5 mm, cHBM4 D2D 2 mm). A stack 10 mm farther out does not pay 5 pJ more. It cannot work with an HBM PHY at all without termination, equalization or serialization. That is the SPHBM4 trade: 512 signals, 4:1 serialization, organic substrate, longer channels [S: notes q1].
   - Fix: replace the sentence with roughly: "Each stack's PHY must face a matching host PHY across a short interposer or bridge channel (2–5.5 mm in Samsung's floorplans [V: Samsung s11]). The link is wide, single-ended and unterminated, so it only works over a few millimetres. Going farther needs a different, serialized, higher-energy PHY. d-Matrix quotes ~0.5 pJ/bit/mm for interposer trace [V: s8]; other sources put the whole HBM interposer link at 0.3 pJ/bit wire-only [P: O'Connor] to ~1.5 pJ/bit with PHY over ~5 mm [P: UniIC IEDM'20], so treat the per-mm figure as an upper bound." Drop "about double HBM4's whole budget". Also change the Figure 1 label "~0.5 pJ/bit per mm [V]" to "2–5.5 mm channel [V: Samsung s11]".

## Major

4. **major — §3 "Getting to 100 TB/s along the HBM trend at 2.4 pJ/bit would take 1.92 kW"; Figure 3 vertical dashed extrapolation.**
   - What's wrong: "along the HBM trend" and "at 2.4 pJ/bit" contradict each other. The trend moves left as bandwidth rises. d-Matrix says only "At 2.4pJ/bit, 100TB/s requires 1.92KW for HBM power only (excluding fabric power)" [V: s6].
   - Trend value: a log-log fit of the five HBM points gives BW ∝ E^−2.78. Extrapolating to 100 TB/s gives ≈1.3–1.45 pJ/bit and **≈1.0–1.15 kW** [D].
   - Fix: write "at HBM4's 2.4 pJ/bit (no further energy improvement), 100 TB/s needs 1.92 kW [V]; even if the historical trend continued (~1.4 pJ/bit), it would still be ≈1.1 kW [D]." Optionally draw the trend extrapolation as a second hollow marker.

5. **major — Scope of d-Matrix's HBM4 "2.4 pJ/bit" is inconsistent across its own deck, and the page treats it as total HBM energy.**
   - d-Matrix uses three different scopes for the same figure:
     - s17 table: "I/O energy: HBM4 2–3 pJ/bit vs 3D-DRAM 0.37".
     - s6: "Data-Movement Energy" axis, plus a callout "HBM power only (excluding fabric power)".
     - s8: "2.5D HBM4 (system) 2.5pJ + 3pJ (on chip)".
   - s28 uses 40.0 mW/(GB/s), which is **5.0 pJ/bit** [D], for HBM4 and Rubin.
   - Sanity check: if 2.4 were I/O only, full-access HBM4 energy would be higher (HBM2's in-DRAM terms alone are 3.45 pJ/bit [P: O'Connor]). If it is full access, it cannot also be "I/O energy".
   - Fix: add one sentence to the Figure 3 caption. "d-Matrix labels the HBM4 2–3 pJ/bit figure as 'I/O energy' (s17), 'data-movement energy' (s6) and 'system' (s8). Its like-for-like table (s28) uses 5 pJ/bit (40 mW per GB/s). The 1.92 kW figure therefore excludes the ~3 pJ/bit on-chip fabric, and at 5 pJ/bit 100 TB/s would be ≈4 kW [D]." **FULL TEXT NEEDED**: Raptor ISCA 2026 paper, for the scope of 0.37 pJ/b and of the HBM4 baseline (already listed as pending in notes q1).

6. **major — Raptor is logic-on-DRAM, not "DRAM-on-logic"; the heat statement is wrong for it.**
   - Elements: §4 note "heat that has to pass through the DRAM (retention falls from 32 ms to 4 ms at 105 °C)"; legend "3D DRAM-on-logic"; ESCAPE card.
   - d-Matrix s17 says "Top die: TSMC N4 logic • Bottom die: 3D DRAM", topology "F2F (below)". The logic sits on top, next to the cold plate. The DRAM sits underneath and gets heated to Tj up to 105 °C. Heat does not pass through it [V: d-Matrix s17, s25–26]. Samsung zHBM is the opposite stack, with C-dies above the xPU, where heat does cross the DRAM.
   - Fix: use "3D DRAM–logic stacking" as the category. Write: "…and in heat: the DRAM sits in the hot logic's thermal footprint (d-Matrix: Tj up to 105 °C; retention 32 ms at 85 °C → 4 ms, 8× refresh [V]); in DRAM-over-logic designs such as Samsung zHBM the heat must also cross the DRAM."

7. **major — The interposer "solves wires per stack" (claim box, §1 heading, §4 "Right").**
   - What's wrong: the vendor primary source says per-stack width was bought with beachfront and channel length.
     - Samsung s11: "Conventional trend (HBM2→sHBM4): PHY footprint and CH length continuously expanded for higher BW." The PHY grew from 6 × 1.2 mm (HBM2) to 8 × 3 mm (HBM3) to 8 × 4 mm (sHBM4), and channel depth from 3.5 to 4.5 to 5.5 mm.
     - Samsung s5 names "TSV counts & pitch limits" and "B-die PHY I/O count and speed" as the primary per-stack bottlenecks.
   - So wires per stack and stacks per edge are one constraint, **wires per mm of die edge** (≈2,048 DQ over an ~8–11 mm PHY/stack edge, ≈190–260 DQ/mm [D]), and not two sequential ones. The page half-says this in its §4 bullet "Bandwidth per stack is not fully solved", and in the note "Wires grow with the length of the boundary divided by the connection pitch".
   - Fix: add to §4 Loose: "'Solves wires per stack' overstates it. Doubling to 2,048 DQ widened the PHY (6→8 mm) and lengthened the channel (3.5→5.5 mm) [V: Samsung s11], so wires per stack and beachfront are the same limit: wires per mm of edge. Custom base dies (cHBM) attack exactly this by replacing the HBM PHY with a D2D PHY (8.5 × 1.5 mm, 2 mm channel) [V: Samsung s10–11]."

8. **major — The root constraint under beachfront is missing: reticle and interposer size limits, and competition for edge.**
   - Elements: §2, "Making the interposer larger to expose more edge runs into 'interposer size, warpage'".
   - A larger interposer does not create die edge. Die edge comes from more or bigger compute dies, and a monolithic die is reticle-limited (26 × 33 mm). Samsung states both limits: "Larger monolithic die: reaching reticle size limits; Multi-chiplet interposer: interposer size reaching its physical limit". It draws 6 HBM per 26 × 33 mm xPU, 12 per two-die package [V: Samsung s9]. The edge is also shared with die-to-die links and off-package SerDes. MI455X puts IODs on two sides and HBM4 on the other two [V: AMD MI400 floorplan].
   - Fix: "A die cannot exceed the ~26 × 33 mm reticle, so one die's two long edges hold about six stacks [V: Samsung s9]. More stacks need more dies, and so a larger interposer or bridge package, which hits size and warpage limits [V: d-Matrix s5; Samsung s9]. The edge is also shared with die-to-die links and off-package I/O." Optional [RK]: CoWoS-S interposers are about 3.3× reticle and CoWoS-L about 5.5×; MI455X uses CoWoS-L [V: AMD deck].

9. **major — "Each stack's PHY has to sit against a compute-die edge".**
   - What's wrong: it is the host-side HBM PHY/controller that occupies the edge, facing the stack's base-die PHY (d-Matrix s5 draws both PHYs). In MI455X the HBM4 attaches to the Fabric & Cache Dies ("192 Channel HBM4 Interface"), and the compute XCDs are 3D hybrid-bonded on top [V: AMD MI400 deck]. So stacking compute vertically is already how AMD frees edge. The host PHY also costs compute-die area: the sHBM PHY is "the largest block on the B-die", >8 × 4 mm [V: Samsung s10].
   - Fix: "The host's HBM PHY must sit at the edge of the die the stack attaches to (the SoC or base die, not necessarily the compute die: MI455X attaches HBM4 to its fabric/cache dies and 3D-stacks compute on top [V: AMD])." Add a host-PHY block at the GPU's facing edge in Figure 1.

10. **major — Interface-energy comparison is not like-for-like (ESCAPE card "~0.37 pJ/bit I/O"; note "drops interface energy to ~0.3–0.4 pJ/bit"; §4 "0.3 for the interposer wire").**
    - As written, a reader sees 3D I/O at 0.37 pJ/bit and the HBM2 interposer wire at 0.3, i.e. no gain.
    - The scopes differ. O'Connor's 0.3 is wire-only and toggle-adjusted. Raptor's 0.37 is "Measured I/O Power" [V: s23] and presumably includes drivers. The like-for-like HBM figure including PHY is ~1.5 pJ/b [P: IEDM'20 Table 1, notes q3 D2], so the gain is ~4×, not d-Matrix's "~10× below HBM".
    - Fix: add a sentence with the scopes in the §4 note. Tag 0.37 as "I/O only; scope to be confirmed (FULL TEXT NEEDED: Raptor ISCA'26)".

11. **major — Figure 1 microbump label "microbumps ~36–55 µm pitch [V/S]" and caption "(d-Matrix face-to-face 36 µm [V]…)".**
    - What's wrong: 36 µm is the pitch of Raptor's logic-to-DRAM **F2F 3D** bump [V: d-Matrix s17], not an HBM-to-interposer microbump. Placing it on the 2.5D HBM panel mis-tags it. UCIe "45 µm" is a die-to-die advanced-package figure [S, unverified in notes].
    - Fix: for HBM, give a sourced HBM bump pitch, or "~40–55 µm [RK]". Move the 36 µm value to the 3D discussion.

## Minor

12. **minor — §2 "Today's packages carry 8–12 stacks, which d-Matrix puts at a practical limit of about 20 TB/s with HBM4."**
    - d-Matrix s5 says "8 stacks per package" and "A Practical limit w/ HBM4 (Vera Rubin, MI 455): ~20TB/s". It does not tie 8–12 stacks to 20 TB/s. AMD's own MI455X figure (23.3 TB/s) already exceeds d-Matrix's "limit".
    - Fix: "d-Matrix puts the practical HBM4 package limit at ~20 TB/s [V: s5]; AMD lists MI455X at 23.3 TB/s from 12 stacks [V], so treat this as ≈20–25 TB/s."

13. **minor — Figure 2 caption "Stack counts are as labelled on the original slide".** The slide's labels read "8 HBM edges / 4 HBM edges / 6 HBM edges". The legend calls each block an "HBM stack (beachfront)". The counts and geometry match the slide exactly: (a) top and bottom of each die; (b) left of D0/D2 and right of D1/D3; (c) the same plus one stack between D0–D1 and one between D2–D3. "long, narrow interposer" is the page's own inference. Fix: "counts match the slide (which labels them 'HBM edges')"; tag the interposer-shape remark [D].

14. **minor — Sources item 4: "(1.21 activation + 2.24 on-die + 0.3 I/O = 3.92 pJ/bit)".** These add to 3.75, not 3.92. Notes q3 attribute the 3.92–3.97 total to "including ECC". 60 W at 2 TB/s works out to 3.75 pJ/bit [D]. Fix: "1.21 + 2.24 + 0.3 = 3.75 pJ/bit; 3.92 with ECC overhead". **FULL TEXT NEEDED** to confirm what makes up the 0.17. FGDRAM is not in `fulltext/`. Notes q1 say it was read online, and r5 flagged that its location is undocumented, so either add the PDF or state where it was read before keeping the [P] "read in full" tag.

15. **minor — Figure 3 caption DDR scope inference.** "Its DDR values (~26 pJ/bit for DDR5) are higher than Lee et al.'s device-level 16.5–21 pJ/bit for DDR4". The like-for-like comparison is d-Matrix DDR4-3200 ≈31 against Lee's DDR4 16.5–21, and d-Matrix HBM2 ≈5.6 against O'Connor's 3.92, both ≈1.4–1.9× [D]. The 16.5–21 is itself derived: a Fig. 2a read plus a Table 3 derivation that assumes 64-B rank accesses, which Lee does not state [notes q3 D1; Lee 2018 Table 3 confirmed present]. It is DRAM-side dynamic energy (×4 devices, 2.4 Gb/s, 85 °C, δ = 0.27), not "device-level" in a strict sense. Fix: compare DDR4 with DDR4 and HBM2 with HBM2, tag the value [D from P], and say "DRAM-side dynamic, excl. host PHY/RCD/static".

16. **minor — Samsung table note: "read from an unlabeled bar chart".** The bars have no value labels, but the axes are labelled (0–40 TB/s, 0–1400 W), and my reads agree with the page: 13.9/31.8 TB/s, 218/124 W, so ≈1.96 and ≈0.47 pJ/b [D]. Samsung states no utilisation, so the pJ/bit values assume DRAM power is quoted at peak bandwidth. Fix: "read against the chart axes (no data labels); assumes DRAM power is at the stated peak BW".

17. **minor — §4 "That is why HBM4 doubled the interface to 2,048 bits instead of only raising the rate."** This is a causal inference, so tag it [D]. Supporting [V]: Samsung s5 names B-die PHY I/O count and speed as the primary bottlenecks. Also worth saying: the 38×/32× ratio implies HBM4 on MI455X runs at only ≈7.6 Gb/s/pin against DDR5-6400's 6.4 [D]. Nearly all of the per-attachment gain is width, not rate, which is a clean point for the §1 argument.

18. **minor — Thermal coupling of edge-attached stacks is omitted.** HBM sits beside a >1 kW die and has its own temperature limits. PHY hotspots now need dedicated heat paths (Samsung HPB, s12; SK hynix iHBM "hot D2D PHY area" [V: SK hynix HC'26]). Fix: one clause in §2 noting that edge-adjacent stacks also share the compute die's thermal envelope.

19. **minor — Energy binding is not only "after" beachfront.** O'Connor already argued in 2017, in the 4–6-stack era, that power caps HBM bandwidth: "60 W DRAM budget → ~2 TB/s for HBM2; 4 TB/s needs >120 W" [P: O'Connor, notes q3]. That strengthens the page's "logical, not time order" point. Fix: cite it in the §4 first Loose bullet.

20. **minor — Unsourced vendor figures.**
    - "NVIDIA B200 (HBM3E) 8 stacks, 8 TB/s [V] vendor spec" has no document named, and it is not in the decks read. Cite the NVIDIA datasheet or tag it [background].
    - "NVIDIA Rubin also uses 8 HBM4 stacks [V: d-Matrix s28]" is d-Matrix's statement about a competitor ("Rubin R200 = 8× HBM4 24 Gb SoC"), so say "per d-Matrix". The NVIDIA Rubin deck gives only factory-level HBM4 bandwidth (800 PB/s), which confirms "not in the decks we read".

21. **minor — Figure 3 replot accuracy (checked, OK).** Pixel reads of s6 against the log axes (370 px per x-decade, 90.5 px per y-decade) give: 3D 0.38/100; HBM4 2.40/18.2; HBM3E 3.72/9.9; HBM3 4.21/6.6; HBM2 5.64/2.5; HBM1 6.84/1.0; GDDR6 14.2/1.1; LPDDR5 8.2/0.052; DDR5 26.2/0.055; DDR4 31.4/0.026; DDR3 38.6/0.0113. All page values fall within ±5%, and the iso-power math P[W] = BW[TB/s] × 8 × E[pJ/bit] is correct (100 × 8 × 2.4 = 1,920 W; HBM1 ≈54 W; HBM4 ≈346 W). One mismatch: d-Matrix uses distinct markers for GDDR (diamond) and LPDDR (pentagon), while the page merges them into "DDR / LPDDR / GDDR" squares. That is acceptable, but GDDR6 is discrete on-board memory, not "off-package DDR".

22. **minor — Checked and correct.**
    - 32× wires (2,048/64) and 38× bandwidth (1.94 TB/s ÷ 51.2 GB/s = 37.9).
    - DDR5 = two 32-bit subchannels, +16 ECC on EC8 RDIMMs. [RK]: 72-bit EC4 RDIMMs also exist; optional "(+8 or +16 ECC)".
    - 160–200 pins [notes q3: COAXIAL; Das Sharma "~200"].
    - MI455X 12 × HBM4, 432 GB, 23.3 TB/s [V: AMD].
    - JEDEC HBM4 2,048 × 8 Gb/s ≈ 2 TB/s.
    - Raptor 100 TB/s × 0.37 = 296 W [V: s23].
    - Capacity density 11.4 vs 21.9 MB/mm² [V: s28].
    - "+3 pJ (on chip)" [V: s8].
    - The "SOTA packages neither have beachfront, nor power…" quote [V: s6].

## Visual

23. **minor — Figure 1, DDR panel:** the orange board trace (y = 262) runs through the label "motherboard: centimetres of trace", striking out "of trace". Move the label below the board strip or start the trace after the text.

24. **minor — Figure 1, HBM panel:** there is no host PHY on the GPU die, although the text's argument depends on it (and d-Matrix s5 draws both PHYs). Add a small "PHY" block at the GPU's left edge facing the base-die PHY.

25. **minor — Figure 3, iso-power labels:** "2 kW", "1.2 kW", "300 W" and "100 W" are each struck through by their own line at the right edge. "1.2 kW" (bold, dashed line) looks garbled at 1× and 2×. Place the labels just outside the plot frame (in the right margin at each line's exit y), or give them a white halo (`paint-order: stroke; stroke: #fff; stroke-width: 3`).

26. **minor — Figure 3, data labels:** the 300 W dotted line crosses "3D DRAM (d-Matrix Raptor, I/O only)" and "HBM4 (proj.)". The halo fix in item 25 solves both.

27. **minor — Figure 3 legend:** the hollow 100 TB/s marker and the dashed vertical extrapolation are not in the legend. Add "extrapolation at constant 2.4 pJ/bit", and the trend-extrapolated point if item 4 is adopted.
