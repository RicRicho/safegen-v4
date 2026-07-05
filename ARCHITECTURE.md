# Architecture

The full engine design lives at **[`docs/01-architecture.md`](docs/01-architecture.md)**:
the split-key VOPRF tokenisation scheme, the query-path evaluation (bloom filter vs
cryptographic accumulator vs PSI, §3.5), enrolment and query flows with diagrams, the s63F
check-and-forget compliance story, key rotation, governance and the kill switch.

Companion documents:

- [`docs/02-threat-model.md`](docs/02-threat-model.md) — adversarial analysis, including
  dictionary reversal of the AU numbering space (T1) and why a full database breach yields
  nothing usable (T3)
- [`docs/03-positioning.md`](docs/03-positioning.md) — mapping to eSafety's stated problems
  and the buyer reality
- [`site/index.html`](site/index.html) — parent-facing landing page (openable directly)
