# SafeGen — Positioning One-Pager

**The missing layer in Australia's under-16 social media framework: a parent-consented,
self-expiring known-minor signal that platforms adopt — with no party holding a linkable or
reversible database of children.**

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
party with both the knowledge and the motivation — voluntarily enrol their child's mobile number. SafeGen converts it, via a
split-key oblivious PRF, into an unlinkable pseudonym with a built-in expiry on the child's 16th
birthday, then destroys everything else in-session. Platforms screen the phone numbers **they
already hold** against signed membership snapshots using blinded queries: matching happens inside
the platform; SafeGen never learns who was checked or who matched; platforms learn nothing about
numbers they don't already lawfully process. A hit routes the account into the platform's
existing age-assurance waterfall — SafeGen signals, platforms decide.

## Mapping to the Commissioner's stated problems

| eSafety's problem (Mar 2026 update / guidance) | What SafeGen does about it |
|---|---|
| **Re-registration by known under-age users** | The child's number persists across account deletions. A removed child re-registering hits the signal on day zero, at signup — before any gameable check runs. Coverage is bounded by parent enrolment and by the platform collecting a mobile number at signup; email-only paths remain with the platform's waterfall. |
| **Cross-platform migration** | One parental enrolment is recognised by every participating platform. The signal travels; the child's data doesn't. |
| **Gamed self-declaration & repeat-attempt estimation** | SafeGen is immune to coached selfies, borrowed faces and birthday edits: the attestation was made by a verified adult, out-of-band, and can't be re-rolled by the child. Slots in as the *deterministic first layer* of the waterfall the 18 Sep 2025 guidance already prescribes. |
| **Privacy / honeypot fears (the reason a central register was ruled out)** | There is no usable list to steal: 32-byte keyed pseudonyms + expiry month, reversible only via simultaneous compromise of two independent organisations' HSMs — or by slow, publicly volume-logged online evaluation that annual key rotation renders stale. No biometrics, no documents, no government ID — ever, from anyone. Every record self-deletes the month the child turns 16. The design is built backwards from s63F ringfence-and-destroy: everything identifying is destroyed in-session (the raw number exists for seconds, in one RAM-only component), and retention of the derived pseudonym is intended to rest on the Act's express-consent pathway — subject to formal advice on consent given by a parent on a child's behalf. |
| **eSafety must not operate infrastructure** | Correct — and it doesn't. SafeGen is commercial infrastructure **platforms adopt** as part of their s63D "reasonable steps", exactly like any third-party age-assurance provider under the technology-neutral guidance. eSafety's relationship is supervisory: audit access, transparency reports, and — if the amendment bill is enacted as introduced — compelled-documents powers over third-party providers, all answerable with artefacts containing zero personal information. **Nothing flows to government but statistics.** |

## Why this survives scrutiny that alternatives don't

- **vs. facial estimation:** the AATT reported error bands of up to ~18 months near the threshold,
  with reduced accuracy reported for some demographic groups (Part D); SafeGen's signal has no
  demographic error surface and no biometric collection.
- **vs. ID/document checks:** the Act bars platforms from making government ID the *only* pathway
  (reasonable alternatives must be offered); SafeGen needs no ID from the child at all, and only a
  yes/no bank-rail assertion from the parent, destroyed in-session.
- **vs. a central register:** SafeGen is the *engineering proof* that the register's benefit
  (memory of known minors) is achievable with none of its risk — double-blind, quota-bound,
  publicly volume-logged, and equipped with a kill switch that renders all historic data
  permanently meaningless.

## The ask

Pilot with one major platform's AU signup flow + a parent enrolment campaign through schools,
under ACCS/ISO 27566 certification and OAIC engagement from day one — positioned for the Senate
inquiry window as the constructive answer to "the ban is leaking, but nobody wants a database."

*Details: architecture & crypto — 01-architecture.md; adversarial analysis — 02-threat-model.md;
parent experience — site/index.html.*
