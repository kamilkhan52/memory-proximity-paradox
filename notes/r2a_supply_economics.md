# R2a: Supply economics. Does the bandwidth tier (A) make the capacity tier (B) more attractive?

Round-2 sub-agent notes. Compiled 2026-10-03. The hypothesis under test:

> Growth of HBM (A) structurally raises the cost of conventional DRAM capacity. That strengthens the economic case for cheaper, recycled or farther capacity tiers (B): CXL expansion, DDR4 reuse and KV-cache offload.

Labels:
- **[primary]**: company filing, prepared remarks, call transcript, peer-reviewed paper, or analyst-firm press release read directly.
- **[transcript]**: third-party transcript of an earnings call.
- **[press]**: journalist report.
- **[secondary]**: analyst number relayed by press, social media or an aggregator.
- **[unverified]**: no traceable origin.
- **[my calc]**: my own arithmetic on the cited numbers.

---

## Round-4 deltas (2026-10-03, after full texts supplied)

One correction to round 2: I attributed Micron's "HBM4E >4:1" to Murphy on the Dec-2025 call. It is in the **March 20, 2025 (FQ2-25) prepared remarks, p.6**. The Dec-2025 prepared remarks (p.5) say only "3-to-1 … only increases with future generations".

### D1. Trade ratio r: primary statements, now read in full

| Source | Exact text | Tag |
|---|---|---|
| Micron FQ2-25 prepared remarks, 2025-03-20, p.6 (fulltext/21-micron-mar2025.pdf) | "HBM3E consumes three times the amount of silicon compared to D5 to produce the same number of bits. Looking ahead, we expect the trade ratio to increase with HBM4 and then again with HBM4E when we expect it to exceed 4 to 1. This sustained and significant increase in silicon intensity … constrains capacity for non-HBM products." | [primary] |
| Micron FQ1-26 prepared remarks, 2025-12-17, p.5 (fulltext/21-micron-dec2025.pdf) | "aggregate industry supply will remain substantially short of the demand for the foreseeable future. The dramatic increase in HBM demand is further challenging the supply environment due to the 3-to-1 trade ratio with DDR5, and this trade ratio only increases with future generations of HBM." | [primary] |
| Micron, Hot Chips 2026, slide 11 (extracted PDF text) | "Combined overhead from design architecture, advanced packaging, and manufacturing complexity results in ~3X more silicon being consumed for delivering HBM3E capacity relative to DDR5". HBM3E has 128 banks and 256 GB/s core bandwidth per die; DDR5 has 32 banks and 8 GB/s. | [vendor slide, primary text] |
| Micron, HC'26 slide 5 | Memory (HBM4 12-high, incl. base die) silicon area is ">8x" the GPU's, in a package with 8 HBM4 stacks. The extracted text also contains "12,344" (mm²?), which is not visible on the rendered slide (viewed JPEG), so I do not use it. | [vendor slide] |
| Objective Analysis (J. Handy), HC'26 tutorial, slide 4 | "3 Drivers of DRAM Shortage: AI spending more than anticipated; Bandwidth requirements consume inordinate wafer volume (HBM takes 3X the die area of DDR); DRAM wafer volume static for >10 years (New capacity takes >2 years to install)" | [analyst slide, primary text] |
| SK hynix (Jaesik Lee), HC'26 slide 11. Read from the slide image; the numbers are not in the extracted text | Across generations from 1 → 8 Gbps/pin (HBM1 → HBM3E): **# of TSVs ×1.4 → ×2.5 → ×2.9 → ×5.0**; **TSV "Area Penalty" ×1.1 → ×1.3** (relative to first-gen HBM, not to DDR5). Stack count ×2 per two generations; thermal burden ×2.2. Slide 6 (text): HBM4 has ">20K TSVs" and "16148 Base Micro-Bumps". | [vendor slide; image-read] |

**What changes:**
- **r for HBM3E ≈ 3 is now primary,** from three independent Micron documents plus Handy.
- **HBM4E ">4:1" is primary** (Micron, Mar-2025). **HBM4 has no primary number.** The only primary statement is "higher than HBM3E", so 3 < r(HBM4) < 4+.
- Hot Chips adds no HBM4/4E number. "Widening every generation" is Micron's direction, not a figure.
- **Scope of r.** Micron's ~3× explicitly *includes* "advanced packaging, and manufacturing complexity" (i.e., yield). So in P_DDR ≈ P_HBM/[r(1+c)], **c must exclude yield.** c covers only non-wafer incremental cost: TSV/bump/stack/test processing, and for HBM4 a foundry base die. No primary number for c exists. I keep c = 0–0.2 as an assumption [my calc].
- **The mechanism behind a rising r has primary support.** SK hynix shows TSV count up ×5 and TSV area penalty up ×1.3 through HBM3E. HBM4 doubles I/O to 2,048 and banks to 256 (Micron slides 7 and 9).

### D2. Re-derived price floor P_DDR ≈ P_HBM / [r(1+c)] [my calc]

| HBM price input | r | c=0 | c=0.1 | c=0.2 |
|---|---|---|---|---|
| HBM3E 2025, ~$15/GB [secondary: Goldman via X] | 3 [P] | $5.0 | $4.5 | $4.2 |
| HBM3E 2026, $10–16/GB [secondary] | 3 [P] | $3.3–5.3 | $3.0–4.8 | $2.8–4.4 |
| HBM4 2027, $32–36/GB [press] | 3.5 (between 3 [P lower bound] and >4) | $9.1–10.3 | $8.3–9.4 | $7.6–8.6 |
| HBM4 2027, $32–36/GB [press] | 3 (bound) / 4 (bound) | $10.7–12.0 / $8.0–9.0 | — | $8.9–10.0 / $6.7–7.5 |
| HBM4E (no price yet) | >4 [P] | ≤ P/4 | | |

**New primary-analyst price anchor.** Objective Analysis, HC'26 slide 6 (chart read from the JPEG; approximate):
- "DRAM lowest spot $/GB" was ~$1.5/GB through 2024 and ~$1.0/GB at the Feb–Mar 2025 trough.
- It reached ~$2.3/GB at the 7/4/25 marker and **~$16/GB by Jul/Aug 2026**: "7.1X". NAND was ~$0.05 → ~$0.35/GB: "6.9X".
- Title: "Most of Growth is from Pricing".

**Reading the floor against actual prices [my calc]:**
- **2024–mid-2025: the floor was binding.** Conventional DRAM (lowest spot $1–2.3/GB) sat *below* the HBM3E floor (~$4–5/GB). Per wafer, HBM was more profitable, and wafers moved to HBM.
  - This matches TrendForce (Q2-25: HBM3E premium ">4×" DDR5, above r=3).
  - It also matches SemiAnalysis (2025-08-11, free section): "For SK and Micron, yield loss is more than made up for by high pricing, and hence, HBM is margin accretive" [secondary-analyst, full free text].
- **2026: overshoot.** DDR rose to ~$14/GB contract (Counterpoint Q1'26) and ~$16/GB lowest spot (Handy). That is well above the HBM3E floor ($3–5). DDR5 RDIMM out-earned HBM per wafer (TrendForce, 1Q26).
- **2027: the floor rises to ~$7–12/GB** if HBM4 contract prices of $32–36/GB hold [press]. That is roughly 5–10× the 2024 DRAM spot level.
- **Net.** The floor is not just a forward risk. It *was* the binding constraint that drove the 2024–25 wafer reallocation. The 2026 spike overshot it. Whether it binds again depends on HBM4-era prices and on r. My round-2 statement "$8–12/GB for 2027" stands, narrowed to **~$7–12/GB**, and now rests on a primary r.

### D3. SemiAnalysis .mhtml (free portion only; FT3 still pending)

New, citable free-section items (Aug 11, 2025):
- "we think yield is more of a problem at the front end".
- HBM capacity is "quoted in terms of TSV capacity", since TSV and bumping tools are the incremental step that "converts" DDR wafer capacity to HBM.
- On KV offload: "Just like how DRAM is not cannibalistic to L1/L2/L3 cache demand for CPU, HBM and DDR/SSD offload do not compete with each other directly." Hot KV stays in HBM, infrequent KV goes to DDR, rarely used KV to NVMe. This is direct support for A→B complementarity (synthesis §5.1).

### D4. Claims in SYNTHESIS_DRAFT.md v0.3 to change. I did not edit that file.

| # | Location | Current text (quote) | Correction | Source / page |
|---|---|---|---|---|
| 1 | §5.2 Mechanism, bullet 2 (line ~212) | "Micron puts the HBM:DDR5 wafer-per-bit ratio at ">2×" (Dec 2023) [P] and "3:1" (Dec 2025 call) [P/transcript]." | Upgrade to [P] and add the HBM4E figure: "… '3:1' (Dec 2025 prepared remarks) [P]. HBM3E 'three times the amount of silicon … same number of bits', with HBM4 higher and HBM4E expected to 'exceed 4 to 1' (Mar 2025) [P]." | Micron FQ1-26 remarks p.5; FQ2-25 remarks p.6; FQ1-24 remarks (2023-12-20) |
| 2 | §10 Pending list | "Micron ">4:1"" | Remove from pending. Resolved: [P] FQ2-25 remarks, 2025-03-20, p.6. Note: not the Dec-2025 call | 21-micron-mar2025.pdf p.6 |
| 3 | §5.2 Price floor, definition of c | "c the extra back-end/test/base-die cost" | Add: "Micron's r already includes packaging and manufacturing-complexity (yield) overhead [V: HC'26 slide 11], so c is non-wafer cost only." | Micron HC'26 slide 11 |
| 4 | §5.2 floor table, column "r=4, c=0.2" | Unlabelled r=4 | Label r=4 as the **HBM4E** case (>4:1 [P]). Label HBM4 as 3<r<4 with **no primary number**. HBM4 row becomes **$7–12/GB** (r 3–4, c 0–0.2, $32–36/GB). Current $7.1–13.6 is fine as a range, but its r=2.5 column is below Micron's primary HBM3E r and should be dropped for HBM4. | D2 above |
| 5 | §5.2 Reading the floor, bullet 3 | "The floor becomes structurally relevant only if HBM4-era prices hold. That makes it a forward risk, not today's driver." | Replace with: "The floor *was* binding in 2024–mid-2025. DRAM lowest spot was ~$1–2.3/GB against an HBM3E floor of ~$4–5/GB, so HBM out-earned DDR per wafer and wafers moved to HBM. In 2026 DDR prices overshot it (~$16/GB lowest spot by Jul–Aug 2026, 7.1× [V/analyst: Handy HC'26 slide 6]). It binds again only if HBM4-era prices hold." | Handy HC'26 slide 6 (chart read from image, approximate); TrendForce 2025-10-29; SemiAnalysis 2025-08-11 free section |
| 6 | §5.2 Reading the floor, bullet 2 (causes) | "Its causes are several: AI demand for DDR5 and LPDDR, capex discipline, DDR4 end-of-life, and an alleged-collusion class action [Pr]." | Add an analyst primary: Handy lists 3 drivers, namely AI spend above expectations, HBM "3X the die area of DDR", and "DRAM wafer volume static for >10 years" with >2-year capacity lead times [V/analyst: HC'26 slide 4]. Micron (Dec-2025) adds cleanroom lead times [P p.5]. Note that **Handy names HBM a driver of the shortage itself**. Keep "amplifier" for *B adoption* but do not imply HBM is peripheral to the shortage. | Handy HC'26 slide 4; Micron FQ1-26 p.5 |
| 7 | §5.2 Mechanism | (absent) | Add the mechanism for a rising r: SK hynix shows TSV count ×5.0 and TSV area penalty ×1.3 (vs first-gen HBM) by HBM3E; HBM4 has >20K TSVs [V, image-read]. Micron: HBM4 has 256 banks/die and 2K I/O vs DDR5's 32 banks [V] | SK hynix HC'26 slides 6 and 11; Micron HC'26 slides 9 and 11 |
| 8 | §2 table, HBM3E row, $/GB column | "~3× wafer per bit [P: Micron]" | Correct as is. Make the citation precise: [P: Micron FQ2-25 p.6; V: HC'26 slide 11] | as above |
| 9 | §5.1 (KV offload bullet) | "LLM KV offload to DRAM, SSD or RDMA pools [P]" | Optionally add SemiAnalysis: HBM and DDR/SSD offload "do not compete with each other directly" [S, free section] | SemiAnalysis 2025-08-11 |
| 10 | §5.1 / §2 | (absent) | Optional: Micron HC'26 slide 5. Memory silicon area is >8× GPU silicon in an 8×HBM4-12H package [V]. This quantifies A's wafer appetite per accelerator | Micron HC'26 slide 5 |
| 11 | §10 Evidence quality | "Hot Chips 2026: d-Matrix …" under secondary | Add Micron, SK hynix and Objective Analysis HC'26 tutorial decks as read [V] (extracted text primary; SK hynix slide-11 and Handy slide-6 numbers read from slide images) | — |

No change needed to: the [S] TrendForce wafer/bit shares, the Meta Vistara numbers, the "no hyperscaler primary source ties CXL deployment to the shortage" statement (still true), or the [S]/[Pr] HBM price inputs (still not primary).

---

## FULL TEXT NEEDED

| # | Citation / URL | Claim that depends on it | Status |
|---|---|---|---|
| FT1 | Micron, Hot Chips 2026 talk (R. Sreeramaneni, Aug-2026) | Numeric HBM4/HBM4E trade ratio? | **RESOLVED (R4).** Deck read (fulltext/hotchips26/decks_text/HC2026.MicronTechnology...md). It gives only "~3X more silicon … HBM3E capacity relative to DDR5" (slide 11). **No HBM4/4E number** is in the deck; "widening" is press framing. See Round-4 deltas |
| FT2 | Micron prepared remarks | "HBM4E >4:1" | **RESOLVED (R4).** The statement is in **FQ2-25 prepared remarks (2025-03-20), p.6**, not the Dec-2025 call. Dec-2025 remarks (p.5) say "3-to-1 … only increases". See Round-4 deltas |
| FT3 | SemiAnalysis, "Scaling the Memory Wall" (2025-08-11) | SemiAnalysis trade-ratio model | **STILL PENDING.** The supplied .mhtml has only the free portion; it ends at "This post is for paid subscribers". New free-section quotes are in Round-4 deltas |
| FT4 | TrendForce paid reports, "HBM Industry Analysis 4Q25" (RP251029MY) and "DRAM Contract Price" monthly (RP260130VM, RP260430QO) | Exact HBM wafer starts (k wpm), per-wafer revenue of HBM vs 64GB RDIMM, and contract $/GB by product. I have only the press-release summaries. | Per-wafer revenue crossover is press-release level only |
| FT5 | Citi memory note (2026-05-12), Goldman Sachs memory report (~2025-11) | 64GB RDIMM $873 → $1,586 (Q1→Q4'26). HBM3E $15 → $10/GB (2025→2026). | Relayed via X and blogs. **[secondary]** |
| FT6 | Event or recording of Google's Nikhil Cherian remarks (probably SEMICON Taiwan 2026, early Sept; the speaker page returned 403) | Google's DDR4 reuse "with custom adapters". Is the adapter CXL? Is the "memory >75% of AI server BOM" quote accurate? | **[press/secondary]**. Adapter type is not confirmed as CXL in primary text |

No claim below rests on an abstract alone.

---

## 1. The HBM "wafer trade ratio": what is actually being claimed

### 1.1 Primary statements, in date order

| Date | Source | Statement | What it measures |
|---|---|---|---|
| 2023-12-20 | Micron FQ1-24 prepared remarks [primary, read in full] | "the HBM3E die is roughly **twice the size of equivalent-capacity D5**. Additionally, the HBM product includes a **logic interface die** and has a substantially more complex **packaging stack that impacts yields**. These factors result in HBM consuming **more than two times the wafer supply as D5** to produce a given number of bits." | Decomposition: die area per bit (~2×), plus base die, plus packaging yield. Total >2× |
| 2024-03-18 | TrendForce press release [primary-analyst] | "die size of HBM is generally **35–45% larger than DDR5 of the same process**". Yield "**20–30% lower** than that of DDR5". Production cycle "1.5 to 2 months longer". The industry allocates "~**250K/m (14%)** of total capacity to … HBM TSV" by end-2024. | Die area (not per bit), front-end yield, cycle time |
| 2024-06 | Micron investor material [press, via mlq.ai] | "HBM4 trade ratio would be higher because of increased performance and packaging complexity" | Direction only |
| 2025-12-17 | Micron FQ1-26 call (Mehrotra) [transcript, Motley Fool] | "the **three-to-one trade ratio with DDR5**, and this trade ratio only increases with future generations of HBM" | Wafer per bit, all-in |
| 2025-03-20 | Micron FQ2-25 prepared remarks p.6 [primary, R4] | HBM3E "three times the amount of silicon … same number of bits"; HBM4 higher; HBM4E "exceed 4 to 1" | Wafer per bit, all-in (corrected attribution, see Round-4 deltas) |
| 2026-06-24 | Micron FQ3-26 prepared remarks [primary, read in full] | "HBM's growth and **increasing trade ratio with every new generation** further pressures non-HBM supply" | Direction. Note it is listed alongside other causes (§2.4) |
| 2026-08-23 | Micron, Hot Chips 2026 [press: wccftech slide table] | Silicon area HBM vs DDR5 "**~3×**". HBM4 has 256 banks per die vs 32 for DDR5 (128 for HBM3E). Memory is "~90% of the total silicon" in a GPU SiP with four 12-high stacks (memory silicon is 8× GPU silicon) | Area per bit |
| 2026-09 | Samsung EVP Kim Taewoo, via Reuters/TrendForce [press] | HBM is "about **20%**" of industry DRAM wafer capacity now and "nearly **30%** next year". The article adds: one HBM die uses the wafer area of ~3 GB of DDR5 | Wafer share |
| 2025-12-26 | Commercial Times via TrendForce [press] | "1GB of HBM consumes **4×** the capacity of standard DRAM". GDDR7 needs **1.7×** | All-in, unspecified generation |
| 2024-12 (approx.) | Nomad Semi (citing SemiAnalysis) [secondary] | HBM4 "can consume up to **5×**" DDR5 wafer supply. HBM3E yield "below 70%" | Upper-bound claim |

### 1.2 Reconciling "35–45% larger die" with "3×"

These are different quantities. They multiply:

1. **Area per bit.** TrendForce gives +35–45% die area "of the same process". Micron says ~2× "equivalent-capacity" die. SemiAnalysis (free section) gives a density comparison: SK hynix D1z DDR4 at 0.296 Gb/mm² vs SK hynix HBM3 at 0.16 Gb/mm², a factor of **1.85** [secondary; note the nodes and generations may differ]. Plausible range is **1.4–2.0× area per bit**. The difference comes from TSV keep-out zones, wider internal data paths, more banks (256 vs 32 for HBM4) and power delivery.
2. **Front-end yield.** TrendForce says HBM is 20–30% lower. That means dividing by ~0.7–0.8, i.e. **×1.25–1.43**.
3. **Stacking and assembly yield.** SemiAnalysis illustrates 99% per layer, giving 92% for 8-high and 87% for 12-high, i.e. **×1.09–1.15**. Known-good-die losses after stacking scrap all the DRAM in the stack.
4. **Base (logic) die.** On HBM3E from SK hynix and Micron the base die is made on a DRAM-family process: one per 8–12 core dies, **×~1.08–1.12** of DRAM wafers. For HBM4 the base die moves to a foundry (TSMC) logic process for many products, so this term **leaves the DRAM-wafer budget** but adds foundry cost.

Product [my calc]: 1.4×1.25×1.09×1.08 ≈ **2.1×** (low end) up to 2.0×1.43×1.15×1.12 ≈ **3.7×** (high end). That brackets Micron's ">2×" (Dec 2023) and "~3×" (2025–26). The ratio grows with generation because bank count, I/O and TSV count grow and stacks get taller (lower stack yield). It shrinks with yield learning. **The ratio is not a physical constant.** About a third to a half of it is yield, which improves over each product's life.

### 1.3 Market-implied trade ratio from TrendForce's own shares [my calc]

TrendForce (press release 2026-06-02 [primary-analyst]) gives HBM shares of DRAM, top three suppliers:

| End of year | HBM share of wafer input | HBM share of bit supply |
|---|---|---|
| 2025 | 18% | 8% |
| 2026 | 22% | 9% |
| 2027 | 30% | 13% |

Implied ratio = (non-HBM bits / non-HBM wafers) ÷ (HBM bits / HBM wafers):

| Year | Implied ratio |
|---|---|
| 2025 | (92/82)/(8/18) = **2.5×** |
| 2026 | (91/78)/(9/22) = **2.85×** |
| 2027 | (87/70)/(13/30) = **2.9×** |

This roughly matches the "~3×" claim. The match is loose: end-of-year wafer shares vs annual bit shares, and non-HBM wafers include older, less dense nodes, which pushes the same-node ratio somewhat above these values. Jim Handy (Objective Analysis) gives the same heuristic: "If HBM represents 10% of … volume, it consumes 30% of wafer capacity" (Network World, 2026-01-20) [press].

---

## 2. HBM share of capacity and revenue; prices; the shortage story

### 2.1 HBM share of DRAM over time

| Metric | 2023 | 2024 | 2025 | 2026 | 2027 | Source |
|---|---|---|---|---|---|---|
| Wafer / TSV capacity share | — | 14% (~250k wpm TSV, end-24) | 18% (end-25) | 20% "currently" (Samsung, Sep-26); 22% (end-26, TrendForce) | ~30% | TrendForce 2024-03-18 and 2026-06-02 [primary-analyst]. Samsung via Reuters [press] |
| Bit share | 2% | 5% | 8% | 9% | 13% | TrendForce [primary-analyst; 2023 figure via secondary] |
| Revenue share (TrendForce) | 8.4% | 20.1% (proj.) | 33% (proj.) | 41% (proj., 2024 vintage) | — | TrendForce 2024-03-18; 2025/26 projections via secondary aggregator |
| Revenue share (Gartner) | — | — | **23%** ($33B / $143B) | — | **21%** ($86B / $401B) | Gartner (2026-03-26), quoted in SK hynix 424B4 prospectus [primary filing] |

**Key point.** In Gartner's 2026 forecast, HBM's share of DRAM *revenue* **falls** from 2025 to 2027. The 2026–27 explosion in DRAM revenue is mostly *conventional* DRAM prices. TrendForce's older 2024-vintage path (33% → 41%) predates the conventional-DRAM price spike. The two series also differ in definition. Do not mix them.

### 2.2 Prices by tier, with dates

All values are per GB unless noted. Contract and spot prices differ by 2–3×, so always check which one a number is.

| Tier | Price | Basis / date | Source |
|---|---|---|---|
| DDR5 (2023 baseline) | ~$3/GB | 2023 analysis assumption | Levis et al. HotNets'23 (round-1 q2 notes) [primary] |
| DDR5 64GB RDIMM | mid-$300s (~$5.5/GB) | open market, Sep-2025 | Fusion Worldwide blog [secondary] |
| DDR5 64GB RDIMM | **$450 (~$7/GB)** in Q4'25 → **>$900 (~$14/GB)** in Q1'26 → ">$1,000" in Q2'26 (proj.) | contract-level tracker | Counterpoint via EE Times Asia, 2026-02-10 [press] |
| DDR5 64GB RDIMM | $873 (Q1'26) → **$1,586 (~$25/GB)** in Q4'26 | forecast, 2026-05-12 | Citi note via blogs [secondary, FT5] |
| DDR5 64GB RDIMM | $2,841–2,972 (~$44–46/GB) | open market, Jun-2026 | Fusion Worldwide [secondary] |
| DDR5 64GB RDIMM | $1,968 (~$31/GB), +455% YoY | spot index, Sep-2026 | memoryindex.io (from DRAMeXchange/MemWatcher) [secondary] |
| DDR5 RDIMM 32GB | $2,163.50 session avg (~$68/GB) | module spot, 2026-09-28 | DRAMeXchange home page [primary-analyst, spot] |
| DDR5 16Gb chip | **$58.00** (~$29/GB) | chip spot, 2026-10-02 | DRAMeXchange [primary-analyst, spot] |
| DDR4 16Gb chip | **$83.59** (~$42/GB), above DDR5 | chip spot, 2026-10-02 | DRAMeXchange [primary-analyst, spot]. DDR4 has been dearer than DDR5 since mid-2025 (end-of-life driven) |
| DDR4 RDIMM (mostly refurbished) | median **~$9.2/GB** (range $4.6–12.5/GB). Same page gives DDR5 RDIMM median $41/GB | reseller listings, read 2026-10-03 | datacenterdisk.com [secondary, retail] |
| LPDDR5X 16Gb chip | $30.19 (~$15/GB) | spot, Sep-2026 | memoryindex.io [secondary] |
| HBM3E | ~**$15/GB** (2025). Forecast ~$10/GB for 2026 (−28%), made before the spike | contract | Goldman Sachs via X [secondary, FT5] |
| HBM3E | "price premium **more than four times** that of DDR5" | Q2-2025 | TrendForce 2025-10-29 [primary-analyst] |
| HBM3E 12-high 36GB | ~$300/stack (~$8.3/GB) "factory-gate"; $13–18/GB "contract, revenue-implied" | model estimate, 2026 | siliconanalysts.com [secondary, model] |
| HBM4 12-high 36GB | "mid-$500s" (~$15–16/GB) in 2026; **~$32–36/GB** for 2027 supply | contract reports | Notebookcheck/TrendForce/Seoul Economic Daily [press] |
| HBM (blended) 2027 | ASP **+121% YoY** (TrendForce); +79% (UBS); ">50%" (others) | forecast | TrendForce news 2026-09-29; others [press/secondary] |
| CXL controller ASIC | **$300** ("B1 sample price") | Mar-2026 | Ma et al., arXiv 2603.10087, Table 4 [primary, read] |
| CXL x16 add-in card | $210. XConn 32-port switch: $5,800. DDR5 RDIMM assumed at $15/GB | Mar-2026 | same paper. Cites Counterpoint's $700 target for 64GB RDIMM by Mar-2026 |
| Recycled DDR4 behind CXL (Meta) | **0.13× cost/GB** of local DDR5; 0.7× power/GB | production; price date unstated | Vistara, ISCA'26, Table V [primary, full text] |
| DDR4 behind Marvell Structera X with compression | "effective cost per GB is **roughly half**" (1.8–2× LZ4) | Dec-2025 | ServeTheHome, quoting Marvell [press, vendor claim] |

**Derived observations [my calc]:**

- **Contract-price convergence.** By 1H26, DDR5 RDIMM contract ($14–16/GB, then $25/GB forecast for Q4'26) reached or passed HBM3E contract ($10–16/GB). Per GB, the "capacity" tier briefly cost as much as the "bandwidth" tier.
  - TrendForce (2026-06-02): "**HBM wafer revenue was overtaken by DDR5 64GB RDIMM in 1Q26**", and HBM profitability fell below 64GB RDIMM the same quarter [primary-analyst].
  - Counterpoint (2026-02-10): in Q4'25, "for the first time margins for general-purpose DRAM have surpassed those of HBM" [press].
  - Micron FQ4-26 (2026-09-30) [primary]:
    - CMBU margin was "offset by higher HBM mix".
    - Its 2027 HBM agreements include "significant price increases … **narrowing the gross margin gap with conventional DRAM**".
    - This confirms that HBM was *lower*-margin than conventional DRAM at Micron in 2026.
- **The CXL tier's cost.** A 256 GB recycled-DDR4 card at ~$510 hardware ($300 ASIC + $210 card) is ~$2/GB. Against DDR5 at $15/GB (Feb-2026) that is ≈0.13×, the same as Vistara's figure. Against $25/GB (Citi Q4'26) it is ≈0.08×.
  - **But the DDR4 is not free.** Its opportunity cost is the secondary-market value of the pulled DIMMs (~$5–9/GB retail, Oct-2026).
  - All-in, recycled-DDR4-behind-CXL ≈ $7–11/GB vs new DDR5 RDIMM at $14–25/GB (contract) or $30–45/GB (open market). That is roughly **0.3–0.6× economic cost**, not 0.1×.
- **The DDR4 paradox.** DDR4 *chips* (new, end-of-life) cost more than DDR5 chips. Used DDR4 *RDIMMs* cost ~4× less per GB than DDR5 RDIMMs.
  - The reason: DDR5-only platforms (Turin, Granite Rapids) cannot use DDR4 natively, so used DDR4 RDIMMs have no host to plug into.
  - CXL expanders are the device that removes this stranding. The DDR4-chip vs DDR4-RDIMM spread is the arbitrage that Meta, Google and Marvell are exploiting.

### 2.3 How large the shortage is, and the timeline

| Date | Datum | Source |
|---|---|---|
| 2024 | memory pricing +88% | TechInsights (J. Sanders) via The Register 2025-12-20 [press] (round-1) |
| Jun–Jul 2025 | Micron issues DDR4/LPDDR4 EOL notices (June). 8GB DDR4 modules pass DDR5 in price in July. Consumer DDR4 contract +40–45% forecast for 3Q25. Samsung and SK hynix delay DDR4 EOL to 2026 | TrendForce news 2025-07-09 and 2025-09-02 [press] |
| 4Q25 | DDR5 contract forecast raised from +8–13% to **+18–23%** QoQ. Prediction that DDR5 profitability will pass HBM3E in 1Q26 | TrendForce 2025-10-29 [primary-analyst] |
| 1Q26 | Conventional DRAM contract **+90–95% QoQ** (revised from 55–60%). Server DRAM ~+90%. PC DRAM >+100%. LPDDR4X and LPDDR5X ~+90%. NAND +55–60% | TrendForce 2026-02-02 [primary-analyst] |
| 1Q26 actual / 2Q26 forecast | ~+93–98% actual, then **+58–63%** | TrendForce via Fusion [secondary] |
| 3Q26 | server DRAM +13–18% QoQ (forecast) | via search snippet [secondary] |
| FQ4-26 (Jun–Aug 2026) | Micron DRAM prices up "high-teens %" QoQ. Gross margin **87%**. DRAM revenue $39.8B, +343% YoY | Micron FQ4-26 remarks, 2026-09-30 [primary] |
| 2026 | DRAM sufficiency ratio "approximately **−1% to −2%**". 2027 more negative. New capacity not meaningful "until 2028" | TrendForce 2026-07-30 [primary-analyst] |
| CY26 / CY27–28 | Industry DRAM bit shipments grow mid-20s% in 2026 and low-20s% in 2027–28. "Supply constrained in both years." "No line of sight" to balance | Micron FQ4-26 [primary] |
| Medium term | Micron can meet "only about **50% to two-thirds**" of demand from several key customers | Micron FQ1-26 call [transcript] |
| Hyperscaler capex | Meta raised its 2026 capex guide from $115–135B to **$125–145B**: "Most of that is due to higher component costs, **particularly memory pricing**" (Zuckerberg). The CFO repeats "higher component pricing" | Meta Q1-2026 call, 2026-04-29 [primary, transcript PDF] |
| Server content | "strong server unit growth is supported by a **modestly lower rate of content growth** than prior expectations, amid tight memory supply" | Micron FQ4-26 [primary]. Demand-side thrifting of DRAM per server |

**Elasticity [my calc].** A sufficiency shortfall of only **1–2%** of bits came with **+90% QoQ** contract prices. DRAM demand is very price-inelastic in the short run. So a reallocation of a few percent of bits can move prices by multiples. This matters for judging whether HBM is "big enough to matter" (§5).

### 2.4 Stated causes of the shortage, including counter-explanations

**Micron (primary).**
- FQ1-26 (Dec-2025) names three causes:
  1. Data-center/AI server demand (server units up "high teens" in 2025).
  2. HBM's 3:1 trade ratio.
  3. Cleanroom constraints, with longer build times.
- FQ3-26 (Jun-2026) adds:
  - greenfield lead times, skilled-trade shortages, permitting and energy infrastructure;
  - "technology transitions are driving slower bit growth over time", and wafer growth needs are "significantly increasing cleanroom space … requirements";
  - "HBM's growth and increasing trade ratio";
  - in NAND, "suppliers redirecting cleanroom space from NAND to DRAM".
- **HBM is one cause among four or five, not the only one.**

**TrendForce (2026-07-30).** Three causes: new capacity delayed to 2028; HBM wafer intensity ("higher starts don't translate proportionally to additional bit output"); strong AI-server and next-gen CPU platform demand.

**Demand for conventional DRAM from AI itself.** AI servers use host DDR5, LPDDR5X (Grace/Vera SOCAMM) and GDDR7, not only HBM.
- Commercial Times (via TrendForce, Dec-2025) estimates AI will take ~20% of global DRAM wafer capacity in 2026 counting HBM and GDDR7. Cloud "high-speed memory" demand is 3 EB vs 40 EB global DRAM capacity.
- Counterpoint (Feb-2026) names "general-purpose server DRAM" as "the primary driver" of the 1Q26 rise.
- **So part of the conventional-DRAM squeeze is direct demand for capacity, which is part of (B)'s own market, not only crowding-out by (A).**

**DDR4 end-of-life (vendor-driven).** Micron (June 2025 notice), Samsung and SK hynix (planned late-2025, later delayed) and CXMT (halted DDR4) all exited or announced exits.
- This caused the **2025 DDR4 spike and the DDR4 > DDR5 price inversion**.
- It is a product-line decision. HBM is only part of the motive (freeing capacity for DDR5 and HBM).
- Micron later started 1α DDR4 in Manassas for legacy auto, industrial and defense customers (FQ3-26).

**Capex discipline after the 2022–23 downturn.** Production cuts in 2023 and tools moved to leading-edge nodes.
- Micron (Dec-2023) [primary]: redeploying tools to leading-edge nodes "meaningfully reduces our overall wafer capacity", "an industry-wide practice that is likely to constrain industry supply in 2024".
- So wafer capacity was deliberately reduced *before* HBM's share became large.

**Alleged coordination (litigation, unproven).**
- Putative US antitrust class action filed **2026-06-25** (N.D. Cal.) by indirect purchasers of conventional DRAM against SK hynix and "two other memory semiconductor manufacturers".
- It alleges a conspiracy "to restrict the supply of and inflate prices for conventional DRAM beginning in approximately October 2022, through coordinated production cuts, **capacity shifts toward HBM production**, product-line exits and other allegedly coordinated conduct".
- Source: SK hynix 424B4 prospectus [primary filing; allegations only].
- This reframes HBM capacity shifts as possibly *strategic* rather than physically forced. Either way, the effect on conventional supply is the same.

**Cyclical view.** Jim Handy (Objective Analysis, Jan-2026): "supercycle" is overused. This "looks more like a classic shortage that usually lasts a year or two" [press, Network World].
- Historical cycles (1996–97, 2018–19, 2022–23) all ended in 50–65% price falls (UncoverAlpha, partly paywalled) [secondary].

---

## 3. Testing the causal chain quantitatively

### 3.1 How many conventional bits does HBM's wafer penalty remove? [my calc]

Counterfactual: the HBM wafer share is produced as conventional DRAM instead. Using TrendForce shares and the implied ratios from §1.3:

| Year | HBM wafer share | Conventional bits those wafers would yield (as % of total bits) | HBM bits actually produced | Lost bits from trade ratio | Lost bits as % of actual conventional supply |
|---|---|---|---|---|---|
| 2025 | 18% | 18×1.12 = 20.2% | 8% | ~12% | **~13%** |
| 2026 | 22% | 22×1.17 = 25.7% | 9% | ~17% | **~18%** |
| 2027 | 30% | 30×1.24 = 37.3% | 13% | ~24% | **~28%** |

**Incremental effect 2025→2026.** HBM gains +4 points of wafer share. Those wafers would have yielded ~4.7% of total bits as conventional DRAM. They yield ~1.6% as HBM. Net loss is **~3% of total bits in one year**.
- That is larger than TrendForce's whole **2026 sufficiency gap of −1% to −2%**.
- On these numbers, HBM's growth *alone* is enough to tip a roughly balanced market into the observed deficit. Given DRAM's inelasticity, that is enough to explain multiples on price.

**Caveat on the counterfactual.** It assumes the AI bandwidth demand would vanish rather than be served another way. The correct reading is narrower: "per unit of AI memory bits, the bandwidth tier costs 2.5–3× the wafers". It is not "without AI, DRAM would be cheap".

### 3.2 Price coupling through the shared wafer: a structural floor [my calc / argument]

HBM and conventional DRAM are made in the **same DRAM fabs from the same front-end wafers** (only TSV and back-end steps differ). A supplier is roughly indifferent between the two when:

  P_HBM / P_conv ≈ r × (1 + extra back-end/test cost share)

where r is the wafer trade ratio.

The observed price ratio moved around this break-even:

| Period | P_HBM / P_conv | vs r ≈ 3 | Consequence |
|---|---|---|---|
| Q2-2025 | ">4×" (TrendForce) | above | HBM more profitable per wafer, so wafers shifted to HBM |
| Q4-2025 / 1Q26 | conventional prices doubled, ratio fell below r | below | DDR5 64GB RDIMM out-earned HBM per wafer (TrendForce, Counterpoint) |
| 2027 contracts | HBM repriced up 50–121% (Micron: "narrowing the gross margin gap") | approaching r again | arbitrage restoring the ratio |

**The structural claim.** While HBM demand is supply-constrained and fabs are shared, AI buyers' willingness to pay for bandwidth sets an **opportunity-cost floor on conventional DRAM** of roughly P_HBM / r.
- With HBM4 at ~$32–36/GB for 2027 [press] and r ≈ 3–4, the floor is roughly **$8–12/GB**.
- That is ~3–4× the 2023 DDR5 level (~$3/GB).
- This part is *not* cyclical, unless HBM prices fall, r falls through yield learning, or cleanroom capacity grows enough that HBM demand is no longer rationed.

**The causation runs both ways.**
- The bandwidth tier raises the floor for the capacity tier (crowding out).
- Conventional prices pull HBM prices up (Micron FQ4-26).
- So (A) and (B) are *price-linked commodities* of the same wafer. That is a stronger and more precise form of the hypothesis.

### 3.3 Does the shortage feed back into decisions about tiering and reuse?

Explicit links, strongest first:

1. **Marvell CEO Matt Murphy** [transcript, Motley Fool]:
   - FQ4-26 call, 2026-03-05: "CXL demand is accelerating, **partly driven by tight memory supply**. Our custom CXL expanders enable customers to **reuse prior-generation DRAM** with new XPUs, GPUs, and CPUs."
   - FQ2-27 call, 2026-08-27: CXL is "getting deployed at multiple hyperscalers … in extremely high volumes". Drivers: "the demand of inferencing require it. The other is what we're seeing is **as a result of the scarcity that's out there in memory** … Customers are modifying and adjusting their plans to actually use more of this type of technology." Additional design wins "in the last couple of quarters".
   - *Interested party (sells CXL silicon).*
2. **Google (Nikhil Cherian, Sr. Director, Supply Chain Infrastructure)**, Sep-2026 [press: Cryptopolitan, Digital Today, wccftech, memorymarket.com; event probably SEMICON Taiwan, FT6]:
   - DRAM supply "extremely tight". Google pulls DDR4 from retired servers into new AI servers through "specially designed hardware adapters".
   - The recycling "remains insufficient". The "most sustainable solution is for memory makers to expand production capacity faster".
   - Memory is reportedly ">75% of AI server BOM" **[unverified]**.
   - The adapter technology is **not confirmed as CXL** in any text I could read. Only Digital Today says CXL, and that is the journalist's gloss.
3. **Meta**:
   - Capex was raised because of "memory pricing" (Q1-2026 call) [primary].
   - The **Vistara paper itself (ISCA'26) does not cite the shortage or HBM.** Its stated motivations, read in full: DRAM lives 7–10 years vs servers' 3–5 years; ~40% of servers are memory-capacity-bound; OOM and job-restart reduction (−33%); up to −25% server count for disaggregated inference; "near zero-cost memory expansion through recycling"; carbon.
   - The shortage framing in The Register and SDxCentral is the **journalists'**, confirmed by reading both articles.
4. **ServeTheHome** (2025-12-08), "Hyper-scalers Are Using CXL to Lower the Impact of DDR5 Supply Constraints" [press]: hyperscalers are recycling DDR4 behind CXL because "AI data centers are putting significant demand pressure on the DRAM supply". Marvell claims 1.8–2× compression.
5. **Tom's Hardware on Marvell at FMS 2026** (2026-08-04) [press; body truncated]: in 2024 DDR4 reuse was pitched as "a sustainability line item". By 2026 "the memory crunch has turned it into a capex line item". The figure "memory ≈30% of hyperscaler capex in 2026 vs ~8% in 2023–24" is quoted there **[unverified origin]**.
6. **Micron FQ4-26** [primary]: memory content per server is growing more slowly "amid tight memory supply". That is demand-side thrifting, which is what tiering and expansion enable. NAND "AI context memory storage used for **KV cache offload**" is expanding the SSD market; data-center SSD revenue was ~$10B in the quarter, >10× YoY.

**No primary evidence found** that Microsoft (Azure M-series CXL preview, Nov-2025, Astera Leo) links its CXL rollout to the shortage. GreenSKU (ISCA'24) is motivated by carbon.

---

## 4. Verdict on the hypothesis

**Supported, with three qualifications.**

### What the evidence supports

1. **The mechanism is real and primary-sourced.**
   - HBM uses **~2.5–3×** (2025–26) the wafers per bit of DDR5, rising with each generation.
   - HBM takes **18% → 22% → ~30%** of DRAM wafers (2025 → 2026 → 2027) while delivering only **8% → 9% → 13%** of bits.
   - The 2025→26 increment alone removes ~3% of industry bits [my calc], more than the whole 2026 deficit (−1 to −2%).
2. **Conventional capacity prices rose to near or above HBM per GB in 1H26.**
   - DDR5 RDIMM went from ~$7/GB (Q4'25) to ~$14/GB (Q1'26) to a forecast ~$25/GB (Q4'26).
   - HBM3E is at ~$10–16/GB.
   - DDR5 out-earned HBM per wafer from 1Q26.
3. **Vendors and some hyperscalers now explicitly connect scarcity to CXL and reuse.**
   - Marvell CEO, two earnings calls.
   - Google supply-chain director, press only.
   - Meta's *capex* raise is due to memory pricing.

### Qualifications

- **(Q1) HBM is a co-cause, not the sole cause.**
  - Other causes: AI-server demand for *conventional* DDR5 and LPDDR5X (a capacity demand); 2023 capex cuts and tool redeployment; cleanroom lead times; slowing node bit-growth; DDR4 EOL decisions.
  - Possible coordination is now alleged in litigation.
  - Micron itself lists HBM's trade ratio as one of ~4 factors.
- **(Q2) The (B) designs predate the shortage.**
  - Pond (2023, stranding), GreenSKU (2024, carbon) and Vistara (lifetime mismatch, capacity-bound fleet, OOMs) were all motivated without reference to HBM or to prices. Vistara's paper never mentions the shortage.
  - The shortage *amplifies* the payoff of (B). It did not create it. Marvell's "sustainability line item → capex line item" framing captures this.
- **(Q3) The shortage also raises the cost of (B)'s own tiers.**
  - CXL expansion with *new* DDR5 does not escape DRAM prices. Only reuse and compression do.
  - KV-cache offload to SSD faces NAND that is also +55–60% QoQ (1Q26), partly because cleanroom space moved from NAND to DRAM.
  - Recycled DDR4 is not free: used RDIMMs are worth ~$5–9/GB on the open market. The supply of pulls is finite and set by decommissioning rates.
  - So the *relative* advantage of recycled-DDR4-behind-CXL rises, from ~0.13× nominal to perhaps 0.3–0.6× all-in economic cost. Its absolute supply does not.

**The precise form the evidence supports.** (A) and (B) are coupled through the **shared DRAM wafer**. The HBM price divided by the trade ratio sets an opportunity-cost floor on conventional $/GB, roughly $8–12/GB for 2027 vs ~$3/GB in 2023. That floor lasts as long as HBM demand is rationed and r stays at ≥3. It raises the value of every technique that serves *cold* capacity without new leading-edge DRAM wafers: recycled DDR4 behind CXL, compressed tiers, and SSD-based KV offload. The cyclical part (+90% QoQ spikes) will probably fade as greenfield fabs arrive in 2027–28. The structural part (the floor tied to HBM pricing × r) should not, unless r falls or cleanroom capacity overtakes demand.

---

## 5. Counter-evidence and open risks

- **HBM's bit share is small (8–13%), but its wafer share is not (18–30%).** The "too small to matter" argument fails on wafers. On the margin it fails even more clearly, given the −1 to −2% sufficiency ratio and inelastic demand.
- **The trade ratio is partly transient.** 30–50% of r is yield (front-end 20–30% lower, stack yield 87–92%). Yield learning lowers r within a generation. HBM4 moves the base die to foundry logic, which removes one DRAM-wafer term. Generation steps (more banks, TSVs, 16–20-high) push the other way. Micron says the net is upward.
- **Supply response.** Coming capacity:
  - Micron ID1 (mid-2027), ID2 (late 2028), Tongluo (mid-2027), New York (2030);
  - SK hynix M15X (wafer input from 1Q26) and Yongin;
  - Samsung HBM wafers 180k → 250k wpm (2027, press).
  - CXMT expansion is not researched here.
  - Handy expects a "classic" 1–2-year shortage.
  - Historically every DRAM upcycle ended in a 50%+ price collapse.
  - If cleanroom additions overtake demand in 2028+, the floor argument weakens because HBM stops being rationed.
- **HBM-specific softness is possible.** In late 2025 Goldman forecast HBM3E −28% in 2026. TrendForce (Oct-2025) expected HBM3E YoY *declines* in 2026 from three-supplier competition. If HBM prices fall, the floor P_HBM/r falls with them.
- **Demand-side substitution is not only a tiering story.** Some of the response is cutting memory content per server (Micron) or unit declines in PCs and phones (Micron: possible double-digit unit declines). That is demand destruction, not a move to far tiers.
- **The recycled-DDR4 supply is bounded.** It is set by fleet retirement. Hyperscalers lengthening server useful lives (e.g., Meta's move to ~5.5 years in 2025; [unverified here]) shrinks the flow. Google says recycling "remains insufficient".

---

## 6. Sources (read in this round)

**Primary, read in full or in the relevant sections:**
- Micron FQ1-2024 prepared remarks, 2023-12-20 (s25.q4cdn.com/621799436/files/doc_financials/2024/q1/Q1-FY2024-Prepared-Remarks.pdf).
- Micron FQ3-2026 prepared remarks, 2026-06-24 (…/2026/q3/Q3-FY26-Prepared-Remarks.pdf).
- Micron FQ4-2026 prepared remarks, 2026-09-30 (…/2026/q4/Q4-FY26-Prepared-Remarks.pdf).
- Micron FQ1-2026 call transcript, 2025-12-17 (fool.com) [transcript].
- Marvell FQ4-26 (2026-03-05) and FQ2-27 (2026-08-27) call transcripts (fool.com) [transcript].
- Meta Q1-2026 earnings call transcript, 2026-04-29 (s21.q4cdn.com/399680738/files/doc_financials/2026/q1/META-Q1-2026-Earnings-Call-Transcript.pdf).
- SK hynix Form 424B4 prospectus (SEC, 2026). Gartner DRAM/HBM revenue forecast (2026-03-26). Antitrust class-action description. "Greater wafer input" for HBM.
- Gholkar et al., "Vistara", ISCA'26 (camera-ready PDF).
- Ma et al., "Pooling Engram Conditional Memory in LLMs using CXL", arXiv 2603.10087 (Table 4 costs).
- TrendForce press releases:
  - 2024-03-18 (20240318-12081);
  - 2024-07-22 (20240722-12228);
  - 2025-10-29 (20251029-12758);
  - 2026-02-02 (20260202-12911);
  - 2026-06-02 (20260602-13074);
  - 2026-07-30 (20260730-13158).
- DRAMeXchange spot board, 2026-10-02.

**Press and secondary:**
- TrendForce news:
  - 2025-07-09 and 2025-09-02 (DDR4 EOL);
  - 2025-12-18 (Micron's three culprits);
  - 2025-12-26 (Commercial Times, AI ≈20% of wafers);
  - 2026-09-29 (Samsung 20% → 30%).
- EE Times Asia / Counterpoint (2026-02-10). Network World (2026-01-20, Handy).
- wccftech and mlq.ai on Hot Chips 2026. ServeTheHome (2025-12-08).
- The Register (2026-06-29) and SDxCentral on Vistara.
- Digital Today, wccftech and memorymarket.com on Google DDR4 reuse (Sep-2026).
- Fusion Worldwide; memoryindex.io; datacenterdisk.com; siliconanalysts.com (model estimates).
- Nomad Semi; SemiAnalysis (free section only); UncoverAlpha (partly paywalled).
