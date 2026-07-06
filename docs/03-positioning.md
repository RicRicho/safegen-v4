# SafeGen — Positioning One-Pager

**The missing layer in Australia's under-16 social media framework: a parent-consented
known-minor signal that platforms adopt — with no party holding a linkable or reversible database
of children. Not a passive shield: an active instrument. Register a child's number, and either
platforms respect it (protection) or the parent's own guided test proves they don't (evidence).**

---

## The gap, in the Commissioner's own terms

The SMMA obligation commenced 10 December 2025. eSafety's March 2026 compliance update reported
~4.7M accounts removed or restricted — and it was reported that roughly **seven in ten known
under-age users remained** on major platforms, with named compliance concerns that are all
*statelessness* problems: children re-attempting the same age check until it passes,
re-registering with fresh accounts, migrating to platforms that have never seen them, and
self-declaration still operating as a real gate. The June 2026 amendment bill — proposing doubled
penalties (to A$99M) and compelled-documents powers reaching platforms *and third-party
age-assurance providers*, now before an ~8-week Senate inquiry — raises the cost of failure if
enacted, but adds no new detection capability. Every
current check answers "does this face/document look 16?" — none answers **"is this a child we
already know about?"**

## What SafeGen is

Parents — the consenting authority for an under-16's enrolment (the ceremony also captures the
child's own assent via the code on their phone, pending formal s63F advice for 13-15s), and the
party with both the knowledge and the motivation — voluntarily enrol their child's mobile number.
Adult confirmation is a **FaceIQ check on the parent's own device** (no bank rail, no documents,
no data off the phone) plus a one-time code to the parent's phone and a one-time code to the
child's phone — nothing else. SafeGen converts the number, via a split-key oblivious PRF, into an
unlinkable pseudonym carrying a **status flag only** — *Child* (under 16) or *Child-with-consent*
(16+, parent-flagged) — then destroys everything else in-session. **No age, no birthday, no
expiry date, nothing derived from a birthday is stored.** When a child becomes an adult (parent
flags it, or the child moves to a new number) the record is **deleted, not converted** — the list
holds only children. Platforms screen the phone numbers **they already hold** against signed
membership snapshots using blinded queries: matching happens inside the platform; SafeGen never
learns who was checked or who matched; platforms learn nothing about numbers they don't already
lawfully process. A hit routes the account into the platform's existing age-assurance waterfall —
SafeGen signals, platforms decide.

## Mapping to the Commissioner's stated problems

| eSafety's problem (Mar 2026 update / guidance) | What SafeGen does about it |
|---|---|
| **Re-registration by known under-age users** | The child's number persists across account deletions. A removed child re-registering hits the signal on day zero, at signup — before any gameable check runs. Coverage is bounded by parent enrolment and by the platform collecting a mobile number at signup; email-only paths remain with the platform's waterfall. |
| **Cross-platform migration** | One parental enrolment is recognised by every participating platform. The signal travels; the child's data doesn't. |
| **Gamed self-declaration & repeat-attempt estimation** | SafeGen is immune to coached selfies, borrowed faces and birthday edits: the attestation was made by a verified adult, out-of-band, and can't be re-rolled by the child. Slots in as the *deterministic first layer* of the waterfall the 18 Sep 2025 guidance already prescribes. |
| **Privacy / honeypot fears (the reason a central register was ruled out)** | There is no usable list to steal: 32-byte keyed pseudonyms + expiry month, reversible only via simultaneous compromise of two independent organisations' HSMs — or by slow, publicly volume-logged online evaluation that annual key rotation renders stale. No biometrics, no documents, no government ID — ever, from anyone. The stored record carries a status flag only (no age, no birthday, no expiry date); when the child becomes an adult the record is deleted, not converted, so the list holds only children. The design is built backwards from s63F ringfence-and-destroy: everything identifying is destroyed in-session (the raw number exists for seconds, in one RAM-only component), and retention of the derived pseudonym is intended to rest on the Act's express-consent pathway — subject to formal advice on consent given by a parent on a child's behalf. |
| **eSafety must not operate infrastructure** | Correct — and it doesn't. SafeGen is commercial infrastructure **platforms adopt** as part of their s63D "reasonable steps", exactly like any third-party age-assurance provider under the technology-neutral guidance. eSafety's relationship is supervisory: audit access, transparency reports, and — if the amendment bill is enacted as introduced — compelled-documents powers over third-party providers, all answerable with artefacts containing zero personal information. **Nothing flows to government but statistics.** |

## Why this survives scrutiny that alternatives don't

- **vs. facial estimation:** the AATT reported error bands of up to ~18 months near the threshold,
  with reduced accuracy reported for some demographic groups (Part D); SafeGen's signal has no
  demographic error surface and no biometric collection.
- **vs. ID/document checks:** the Act bars platforms from making government ID the *only* pathway
  (reasonable alternatives must be offered); SafeGen needs no ID from the child at all, and only an
  on-device FaceIQ "an adult is present" pass/fail from the parent — no documents, no bank rail,
  nothing leaving the device, destroyed in-session.
- **vs. a central register:** SafeGen is the *engineering proof* that the register's benefit
  (memory of known minors) is achievable with none of its risk — double-blind, quota-bound,
  publicly volume-logged, and equipped with a kill switch that renders all historic data
  permanently meaningless.

## v4 direction — active instrument, not passive shield

The earlier framing sold a *shield*: register a child, and platforms restrict them — which quietly
depends on platform adoption that doesn't exist yet. The v4 direction keeps the shield but leads
with the stronger, honest pitch: **register your child, and either platforms respect it or you've
just helped prove they don't.** SafeGen works from day one, adoption or not.

**Three strategic powers of the model:**

1. **Parents get a voice** — registration is an action, not a wish; each number joins a body of
   parent-verified evidence.
2. **The regulator gets ammunition** — consistently collected, parent-run tests are exactly the
   "reasonable steps" evidence the eSafety Commissioner needs.
3. **Platforms feel real pressure** — a parent-verified number a platform still hosts is a
   *provable* instance of non-compliance, demonstrated by using the number in a factor-authentication
   check (parent-run, on the parent's own child's phone, with consent) that links an account to it.

**Parent-run testing (the legitimacy design).** Evidence is gathered by the parent themselves —
guardian + consent + physically holding the phone that receives the platform's SMS/notification.
SafeGen guides and records; it never touches an account and never acts on a number the parent
doesn't hold. This is *documented parental discovery*, not covert enumeration, and it sits inside
SafeGen's standing **hard rule: authorised, consented pathways only — never covert scraping or
circumvention.** Trained volunteers and how-to videos scale it while standardising every case.

**Accountability with honour (the moral spine).** A "wall of shame" surfaces proven
non-compliance — but held back until there is a real body of proven cases (target ~100 across
platforms), against a defined evidence standard, with a right of reply, aimed at the Commissioner
and parents as evidence rather than at the press. It is balanced by a "wall of fame": the **first
platform to integrate is celebrated as the hero — "the first platform that chose to protect
kids"** — and any platform crosses from one ledger to the other the moment it does the right thing.
This follows the guiding principle *"love your neighbour as yourself"*: treat others, even
platforms, with respect and consideration when they do the right thing. Firm on evidence, generous
in redemption.

**Audience & commercial framing.** Parents are the primary audience; the eSafety Commissioner a
close second (evidence and methodology). Platforms are served quietly — a menu/footer **API-access
link at 1¢ AUD per call** — pricing set so low that *not* integrating is indefensible and feeds the
best-efforts argument directly. **Success metric: the first platform paying for API access within
six months**, positioned as a hero, not a loser.

*(Two open questions carried forward for ruling before operational use, flagged but not blocking:
the legal exposure of the factor-authentication test method under account-authorization law, and
the defamation/accuracy bar for anything published on the wall of shame. The parent-run,
consent-based design is intended to answer the first; the evidence-standard + right-of-reply
discipline the second.)*

## The ask

Pilot with one major platform's AU signup flow + a parent enrolment campaign through schools,
under ACCS/ISO 27566 certification and OAIC engagement from day one — positioned for the Senate
inquiry window as the constructive answer to "the ban is leaking, but nobody wants a database."

*Details: architecture & crypto — 01-architecture.md; adversarial analysis — 02-threat-model.md;
parent experience — site/index.html.*
