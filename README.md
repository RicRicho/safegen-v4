# SafeGen v4 — parent-consented, privacy-preserving age assurance (design sandbox)

Buildable design artefacts for **SafeGen**, a privacy-preserving age-assurance layer that
addresses the enforcement gaps in Australia's under-16 social media minimum age regime
(Online Safety Act 2021 Part 4A) — designed backwards from the Act's **section 63F**
"ringfence and destroy" rule so that no party ever holds a database of children.

**Premise in one paragraph:** parents — the legal consent authority for under-16s — voluntarily
enrol their child's mobile number in a ~2-minute ceremony (~60 seconds per child after the
first). SafeGen converts the number, via a split-key oblivious PRF, into an unlinkable 32-byte
pseudonym that self-expires the month the child turns 16, and destroys everything else
in-session. Platforms screen numbers **they already hold** against signed membership snapshots
using double-blind queries: matching happens inside the platform, SafeGen never learns who was
checked, and a hit simply routes the account into the platform's existing age-assurance
waterfall. *A signal, not a list.*

## What's in this repo

| Path | What it is |
|---|---|
| [`docs/01-architecture.md`](docs/01-architecture.md) | Engine design: actors and trust topology, the split-key VOPRF tokenisation scheme (and why it defeats phone-hash dictionary attacks), enrolment and query data flows (mermaid + ASCII diagrams), the s63F check-and-forget compliance story, governance and the kill switch |
| [`docs/02-threat-model.md`](docs/02-threat-model.md) | Adversarial analysis: dictionary reversal of the AU number space, parent impersonation, honeypot/breach/compelled access, platform enumeration, adult-DoS, SIM recycling, the SMS delivery path, and more |
| [`docs/03-positioning.md`](docs/03-positioning.md) | One-pager mapping SafeGen to the eSafety Commissioner's stated problems: re-registration by known-underage users, cross-platform migration, self-declaration gaming, and honeypot/privacy risk |
| [`site/index.html`](site/index.html) | Parent-facing landing page mockup — trust-first design, the ~60-second enrolment flow, plain-language "what we hold / what we never hold", and an FAQ addressing privacy and honeypot fears |

## How to preview the landing page (no developer tools needed)

1. Download this repository: click the green **Code** button at the top of the GitHub page,
   then **Download ZIP**, and unzip it anywhere (e.g. your Desktop).
2. Open the unzipped folder, then the `site` folder.
3. Double-click **`index.html`** — it opens in your normal web browser. That's it: the page is
   fully self-contained (one file, no internet connection, no install, nothing to run).

The three design documents in `docs/` are plain-text Markdown — they read best directly on
GitHub (click them in the file list above), which also renders the embedded diagrams.

## Regulatory grounding (as at July 2026)

- SMMA obligation in force 10 Dec 2025; eSafety regulatory guidance (Sep 2025) is
  technology-neutral and prescribes layered "waterfall" assurance — self-declaration alone is
  insufficient, and **responsibility sits with platforms** (eSafety regulates; it operates no
  verification infrastructure).
- eSafety's March 2026 compliance update: ~4.7M accounts actioned, but roughly **7 in 10 known
  under-age users remained** on platforms — via over-16 self-declaration, repeat attempts at
  selfie age-estimation, re-registration, and cross-platform migration.
- June 2026 amendment bill: penalties doubled to A$99M plus compelled-documents powers
  (including over third-party age-assurance providers); referred to an ~8-week Senate inquiry
  on 2 Jul 2026.
- **s63F (the hard constraint this design is built around):** personal information collected
  for age assurance must be used only for the age check, ringfenced, and **destroyed after
  use** — de-identification is not a substitute, retention of individual check records is not
  authorised, and the OAIC enforces breaches. Hence SafeGen's transient check-and-forget
  architecture, not a retained register.
- Age Assurance Technology Trial final report (2025): assurance is workable, but facial
  estimation carries ±18-month error bands near the 16 threshold, with reduced accuracy for
  some demographic groups.

## Status

Design sandbox (v4). Nothing here runs in production; the artefacts are intended as the basis
for legal review (two open s63F consent questions are flagged in the architecture doc), a
cryptographic review, and a pilot conversation with one major platform.
