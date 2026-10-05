# Closer and farther at once: why memory is moving both toward and away from compute

A literature study of a single question:

> Why are **tighter memory–compute coupling** (HBM, 3D DRAM-on-logic stacking, processing-in-memory) and **looser coupling** (CXL expansion and pooling, disaggregation, far memory) *both* attracting serious research and investment, when they look like opposite bets?

**Short answer.** Distance is not the variable being optimized. Both directions leave the DDR DIMM's narrow region on a frontier of **bandwidth per GB stored**, **cost per GB**, and **latency tolerance**. Because access is skewed per object rather than per workload, a mix of tiers beats any single tier: proximity serves the bandwidth-dense head of the data, while cheaper/farther tiers serve the capacity-dense cold tail. The DDR middle persists and keeps improving. The study separates what is deployed at scale (HBM on accelerators; cheaper single-host capacity tiers) from what is still ambitious (general PIM, cross-host pooling). The full argument is in [`SYNTHESIS_v2.1.md`](SYNTHESIS_v2.1.md).

## What's here

| Path | Contents |
|---|---|
| [`SYNTHESIS_v2.1.md`](SYNTHESIS_v2.1.md) | Final reviewed synthesis (earlier versions v1.1–v2.0 alongside, v0.x in `archive/`) |
| [`RESEARCH_LOG.md`](RESEARCH_LOG.md) | Running log: sub-questions, agent rounds, full-text requests, decisions |
| [`notes/`](notes/) | Per-sub-question research notes (Q1–Q5, round-2/3 follow-ups, review notes) |
| [`FULLTEXT_REQUESTS.md`](FULLTEXT_REQUESTS.md), [`fulltext/MANIFEST.md`](fulltext/MANIFEST.md) | Which sources were read in full and which could not be obtained |
| [`deck/deck.html`](deck/deck.html) | 10-slide technical deck (self-contained HTML; open locally, press `P` to present) |
| [`deck/report.html`](deck/report.html) | In-depth companion report |
| [`deck/data/`](deck/data/) | Canonical figure data; `*.b.json` are the independent second extractions used for cross-checking |
| [`deck/slides/`](deck/slides/), [`deck/build/`](deck/build/) | Per-slide fragments, assembler (`python3 deck/build/assemble.py --shots`), builder/reviewer briefs |
| [`deck/reviews/`](deck/reviews/) | Accuracy, narrative and visual review rounds with decision records; `ORCH_NOTES.md` holds canonical values |
| [`explainers/hbm-beachfront-power.html`](explainers/hbm-beachfront-power.html) | Illustrated explainer: why HBM bandwidth is bounded by both wiring density and power |

## Conventions

- **Provenance tags** on every number: `[P]` primary paper, `[V]` vendor, `[S]` secondary, `[D]` derived by us, with measured / simulated / emulated / projected scope stated.
- **Full-text rule.** Claims that needed a full read were not taken from abstracts. Where a full text could not be obtained, the dependent claim is marked pending or withheld.
- Figures are replotted from values extracted twice, independently, and reconciled; no figures are copied from the sources.

## Not included

The source PDFs, vendor decks and saved articles used for the study are **not** redistributed here (copyright). `fulltext/MANIFEST.md` lists them so they can be located.

## How it was made

Produced with Claude Code using an orchestrator plus sub-agents per sub-question, followed by adversarial review rounds until the synthesis survived; the deck and report were built one slide per agent with independent accuracy, narrative and visual reviewers. Content reflects sources available as of October 2026.
