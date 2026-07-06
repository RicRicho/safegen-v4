# SafeGen v4 — parent-consented, privacy-preserving age assurance (design sandbox)

Buildable design artefacts for **SafeGen**, a privacy-preserving age-assurance layer that
addresses the enforcement gaps in Australia's under-16 social media minimum age regime
(Online Safety Act 2021 Part 4A) — designed backwards from the Act's **section 63F**
"ringfence and destroy" rule so that no party ever holds a linkable or reversible database of children.

**Premise in one paragraph:** parents — the consenting authority for an under-16's enrolment —
voluntarily register their child's mobile number in a ~2-minute ceremony (~60 seconds per child
after the first). Adult confirmation is an on-device **FaceIQ** check plus one-time codes to the
parent's and the child's phones — no bank rail, no documents. SafeGen converts the number, via a
split-key oblivious PRF, into an unlinkable 32-byte pseudonym carrying a **status flag only**
(*Child* / *Child-with-consent* — no age, no birthday, no expiry) and destroys everything else
in-session; when a child becomes an adult the record is **deleted, not converted**, so the list
holds only children. Platforms screen numbers **they already hold** against signed membership
snapshots using double-blind queries: matching happens inside the platform, SafeGen never learns
who was checked, and a hit routes the account into the platform's existing age-assurance waterfall.

**v4 is an active instrument, not a passive shield.** Register a child, and either platforms
respect the signal (protection) or the parent's own guided, consent-based test proves they don't
(evidence the eSafety Commissioner can use). The model's three powers: parents get a voice, the
regulator gets ammunition, and platforms feel real pressure — a parent-verified number a platform
still hosts is *provable* non-compliance. A "wall of shame" (held back until a real body of proven
cases exists, with an evidence standard and a right of reply) is balanced by a "wall of fame" that
celebrates the **first platform to integrate as the hero — "the first platform that chose to
protect kids"** — following the guiding principle *"love your neighbour as yourself"*: firm on
evidence, generous in redemption. Platforms integrate via a quiet API-access link at **1¢ AUD per
call**. *A signal, not a list. A voice, not a hope.*

## What's in this repo

| Path | What it is |
|---|---|
| [`docs/01-architecture.md`](docs/01-architecture.md) | Engine design: actors and trust topology, the split-key VOPRF tokenisation scheme (and why it defeats phone-hash dictionary attacks), enrolment and query data flows (mermaid + ASCII diagrams), the s63F check-and-forget compliance story, governance and the kill switch |
| [`docs/02-threat-model.md`](docs/02-threat-model.md) | Adversarial analysis: dictionary reversal of the AU number space, parent impersonation, honeypot/breach/compelled access, platform enumeration, adult-DoS, SIM recycling, the SMS delivery path, and more |
| [`docs/03-positioning.md`](docs/03-positioning.md) | One-pager mapping SafeGen to the eSafety Commissioner's stated problems: re-registration by known-underage users, cross-platform migration, self-declaration gaming, and honeypot/privacy risk |
| [`site/index.html`](site/index.html) | Parent-facing landing page mockup (v4 redesign) — active-instrument framing ("register, then test the platforms yourself"), the FaceIQ + status-only registration flow, the two-outcome "protection or evidence" story, the parent-run guided test with its legitimacy design, the three strategic powers, the accountability ledger (wall of shame balanced by a wall of fame, under the "love your neighbour" principle), a quiet 1¢-AUD platform-API section, plain-language "what we hold / what we never hold", and an updated FAQ |

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
  authorised, and the OAIC enforces breaches. Hence SafeGen's transient
  check-and-forget architecture: the only long-term artefact is an unlinkable, self-expiring
  pseudonym retained under the Act's consent pathway — not a register of identities.
- Age Assurance Technology Trial final report (2025): assurance is workable, but facial
  estimation carries ±18-month error bands near the 16 threshold, with reduced accuracy for
  some demographic groups.

## Status

Design sandbox (v4). Nothing here runs in production; the artefacts are intended as the basis
for legal review (two open s63F consent questions are flagged in the architecture doc), a
cryptographic review, and a pilot conversation with one major platform.
