# SafeGen — Threat Model

Scope: the attestation service described in [01-architecture.md](01-architecture.md). Assets,
adversaries, then numbered threats with mitigations and residual risk. The three mandated
headline threats (hash reversal, parent impersonation, honeypot/breach) are T1, T2, T3.

## Assets

| ID | Asset | Why an attacker wants it |
|---|---|---|
| A1 | The set of enrolled children's phone numbers (in clear) | Grooming target list, doxxing, extortion, resale — the nightmare headline |
| A2 | Child ↔ platform linkage ("which platforms did this child's number get checked by / match on") | Behavioural profiling of minors |
| A3 | OPRF key shares k₁, k₂ | Turns A1 recoverable from stored/leaked pseudonyms |
| A4 | Parent identity data | Identity theft, family-mapping |
| A5 | Ability to inject/remove attestations | Frame an adult as a minor (harassment/DoS) or de-list a real minor (defeat the ban) |
| A6 | Platform's evaluation quota / the filter | Enumerate the AU number space to rebuild the minor list |

## Adversaries

- **EXT** — external attacker (breach of any single component, stolen backups, cloud compromise)
- **INS-L** — malicious SafeGen insider/root
- **INS-T** — malicious Trustee insider
- **PLAT** — a curious or malicious platform (or a platform's compromised infrastructure)
- **SOC** — social attacker (bully, abusive ex-partner, prankster, non-custodial adult)
- **GOV** — over-reaching lawful access ("give us the list of minors")
- **NET** — network observer / traffic analyst

---

## T1 — Dictionary attack over the AU numbering space (hash reversal)

**Attack.** AU mobile numbers are `04xx xxx xxx` — ~10⁸ candidates. Any unkeyed digest of a phone
number (SHA-256, salted-per-dataset, scrypt, anything offline-computable) is reversible by brute
force in minutes-to-hours on commodity hardware. If SafeGen stored `SHA256(number)`, a Vault leak
**is** a cleartext leak of every enrolled child's number. The same applies to a leaked snapshot
filter if entries were unkeyed hashes.

**Design response.** Stored pseudonyms are `P = H2G(m)^(k₁·k₂)` — a keyed PRF where:
- k₁ lives in SafeGen's HSM (non-exportable, evaluate-only), k₂ in an independent Trustee's HSM
  under a different organisation. Neither party alone can evaluate the PRF.
- Offline attack: leaked Vault/snapshot data is uniformly random without **both** keys.
  EXT, INS-L, INS-T, PLAT, GOV each individually hold at most one leg. Compromise of *both*
  organisations' HSMs simultaneously is the (stated) residual assumption.
- Online attack, stated honestly: enumerating 10⁸ numbers requires 10⁸ OPRF round-trips through
  **two** independently enforced rate limiters (quarterly quota scaled to declared AU MAU, plus
  per-day burst caps) whose per-client volumes are published in the transparency log. A small
  actor cannot get near the space. A 20M-MAU platform *could* sweep it in roughly a year of
  maximal usage — within quota, so the control is not the limiter alone: it is that (a) the spend
  is permanently visible in the public volume log, and (b) **key rotation (§3.4 of the
  architecture) voids the resulting dictionary at the next annual ratchet**, capping the payoff at
  one rotation period of stale membership bits at an enormous, attributable cost.
- Per-epoch snapshot re-salting prevents cross-epoch correlation of filter contents by parties
  who never obtained pre-salt pseudonyms (leaked snapshots, passive observers). It does **not**
  bind platforms, who compute pre-salt values themselves — that capability is analysed in T4.

**Residual.** Simultaneous dual-HSM compromise; a large platform enumerating inside quota
(bounded in time-value by key rotation, and publicly attributable); a platform learning membership
for numbers it lawfully processes — which is the intended function, not a bypass.
**Severity after mitigation: Low.**

## T2 — Parent impersonation / malicious enrolment at the front door

**Attack variants.**
(a) A non-parent adult enrols a child (stalker, groomer establishing false authority, officious
relative). (b) A minor "enrols" themself as their own parent to look compliant. (c) A bully
enrols a 17-year-old or adult victim's number to get their accounts flagged (harassment-by-
attestation — this is also a DoS, see T6). (d) A parent in a custody dispute enrols/revokes
against the other parent's wishes.

**Design response — layered gate at enrolment:**
1. **Adult identity assertion** via bank-rail attestation (ConnectID-style yes/no: verified adult,
   name as declared) — raises cost from "anonymous prank" to "identity-attributable act".
2. **Control-of-number proof**: OTP delivered to the child's handset, entered during the session.
   You cannot enrol a number you cannot physically read messages from. This single control
   defeats remote variants of (a) and (c) outright.
3. **Statutory-style declaration** of parental/guardian responsibility, hash-logged; false
   declaration is attributable (the identity assertion happened, even though SafeGen doesn't
   retain it — the identity provider's own records support later investigation under warrant,
   without SafeGen holding anything).
4. **Consequence capping**: a SafeGen hit never hard-blocks — it routes the account into the
   platform's standard verification waterfall. A falsely-enrolled adult passes verification once
   and the platform records a local override. Maximum damage of a successful malicious enrolment
   ≈ one extra age check.
5. **Revocation & contest**: revocation via receipt code, *or* re-proving control of the number
   **plus** a fresh verified-adult check (the adult gate stops a child quietly unenrolling
   themself). Any adult who holds the phone wins eventually.

**Residual.** In-household abuse (a controlling adult who has both identity and the child's/
victim's handset) — partially mitigated by consequence capping and revocation-by-possession;
custody disputes are a policy/support-desk matter, not solvable in cryptography. Severity after
mitigation: **Low-Medium**, dominated by scenarios where the attacker already controls the
victim's device.

## T3 — Honeypot / breach: "you're building the database of every Australian child"

**Attack.** Full compromise (or compelled production) of SafeGen's persistent estate: the Vault,
backups, snapshots, logs. Also the slow-honeypot variant: scope creep turns a benign store into a
rich one.

**Design response — make the loot worthless, the scope unexpandable:**
- The Vault's total contents per child: 32-byte keyed pseudonym, expiry month, status bit, hash of
  a receipt code. No names, DOBs, addresses, parent identities, device IDs, or platform linkages
  exist anywhere at rest. There is nothing to breach *into*.
- A2 (child↔platform linkage) is never created: matching is local to platforms; EN/CT see only
  blinded elements; Mode A batches arrive as unordered blinded sets with count-only logging.
- GOV, ordinary process: a subpoena for "the list" can only yield random bytes.
- GOV, the serious vector — **Australian industry-assistance powers (TOLA / Assistance and Access
  Act 2018)**: a Technical Assistance or Capability Notice does not need the keys exported; it can
  compel *evaluation* (run the dictionary through your HSMs), and its secrecy provisions can gag
  the recipient. HSM non-exportability is no defence against this, and it is why "we'd tell
  everyone" is not a plan. Design responses: (a) the k₂ Trustee sits **offshore** in a
  strong-rule-of-law jurisdiction, so no single legal system can compel both legs — dual
  compulsion requires visible international process (MLAT-speed, not warrant-speed); (b) the
  transparency log carries **signed heartbeat attestations of evaluation volumes from both
  parties**, so compelled bulk evaluation shows up as either anomalous published volume or a
  halted/withheld heartbeat — a canary-style control where the *absence* of the signal is itself
  the alarm; (c) TOLA notices cannot lawfully require building a "systemic weakness", and SafeGen
  will argue bulk re-identification capability is exactly that — but the design does not rely on
  winning the argument; (d) the **kill switch** (destroy k₁,k₂ + backup key + delete Vault)
  renders every artefact ever exfiltrated permanently meaningless. Residual stated plainly:
  lawful, gagged, *targeted* evaluation of a small number of specific pseudonyms by dual-
  jurisdiction process is not fully preventable — what the architecture prevents is covert
  **bulk** re-identification.
- Scope creep is blocked structurally: the schema has no columns for enrichment, the EV has no
  persistence, and adding either is a public event (attested builds diffed in the transparency
  log; auditors sign build measurements).
- Ordinary hygiene still applies: IRAP-assessed AU hosting, HSM-backed keys, immutable
  infrastructure, no standing prod access, SOC 2 Type II.

**Residual.** Collusion of SafeGen + Trustee (the trust floor of the whole design — mitigated by
organisational independence, public key ceremonies, and both parties' evaluations being publicly
volume-logged); metadata at the EV during live sessions (seconds-long window; confidential-compute
attestation shrinks the trusted base). **Severity after mitigation: Low, and — critically —
bounded**, which is the property s63F and the Commissioner's "no honeypots" stance actually demand.

---

## T4 — Curious platform: enumeration, caching & correlation (PLAT)

Filter-in-hand enumeration needs OPRF evaluations (T1 online path — quota-bound, dual-enforced,
published, and time-bounded by key rotation). Correlation of a specific person: a platform can
test a number it holds — intended function.

**Stated capability platforms do gain (disclosed, not discovered):** the unblinded value a
platform computes is the *pre-salt* pseudonym `P`, stable across epochs within a key period.
A platform that caches `P` for its held numbers can re-test them against every future snapshot
without further quota — i.e. it can monitor the enrol/revoke/expire *timeline* of numbers it
already holds. That is deliberate (it is how ongoing screening works without re-querying), it
never extends to numbers the platform doesn't hold, and **annual key rotation is the bound**: at
each ratchet all cached `P` values stop matching and continued screening requires fresh,
quota-metered, logged queries. Cross-platform collusion to intersect cached `P`-sets reveals only
numbers *both* parties already hold — nothing an intersection of their raw customer lists
wouldn't reveal, and that act is already a Privacy Act breach independent of SafeGen.

## T5 — Malicious or coerced SafeGen service (INS-L)

Tagging attacks (returning per-platform-keyed results to segment traffic) are blocked by the
chained per-share DLEQ proofs (§3.2 of the architecture): each key holder proves its own leg
against its published share key, and the requester checks the shares compose to the single
published combined key — so *neither* EN nor CT can substitute a per-platform key undetected. Silent record injection (adding pseudonyms
without enrolments) is visible as count discrepancies in the transparency log (every snapshot
publishes record count + enrolment/revocation/expiry counters; auditors reconcile against EV
attestation logs). Withholding revocations/expiries is detectable the same way.

## T6 — Denial of service against a legitimate adult (SOC, overlaps T2c)

Covered by consequence capping + platform override + revocation (receipt code, or possession of
the number **plus** a fresh verified-adult check — the adult gate stops a child quietly
unenrolling themself). The natural rate limit on enrol/revoke churn is that *every* enrolment
re-runs the full gate (verified-adult assertion + OTP ceremony): each cycle costs an
identity-attributable act, so flip-flopping a number is expensive and attributable rather than
throttled by per-number state — deliberately, because per-number timestamps are state the Vault
schema refuses to hold.

## T7 — Network & timing analysis (NET)

All flows TLS 1.3; Mode B point checks are padded to constant size and batched with cover traffic
at the platform edge so an observer cannot distinguish hit/no-hit (the response is
size-constant and the *match happens locally* anyway, so no response even encodes the result).
EN↔CT link carries only fixed-size group elements at aggregate volumes.

## T8 — SIM recycling & number churn (data-quality attack surface)

A recycled number can carry a stale attestation to a new adult holder → at most one extra
waterfall check (T2 capping), plus revocation-by-possession. Expiry-at-16 hard-caps every
attestation's lifetime. Optional telco integration (number-quarantine feeds) can proactively
revoke on reassignment without SafeGen learning subscriber identity (telco submits the blinded
pseudonym of recycled numbers through the same OPRF pipeline).

## T9 — Compromise of the parent's device / session (EXT→Parent)

An attacker with the parent's browser session during enrolment sees one child's number — the same
exposure as the parent's SMS app. No credential persists post-session to steal (receipt code is
displayed once; losing it degrades to revocation-by-possession).

## T10 — The OTP delivery path: the SMS gateway as an accidental registry (EXT/INS)

**Attack.** Every enrolment sends an OTP to the child's raw number. A commercial SMS aggregator
logs destination numbers and timestamps as a matter of course — so after N enrolments, a party
*outside* SafeGen's audited perimeter quietly holds a timestamped cleartext list of enrolled
children's numbers: asset A1, rebuilt at the messaging layer. Left unaddressed this would falsify
the design's central claim and create a s63F disclosure problem (personal information handed to a
processor whose retention SafeGen doesn't control).

**Design response.** (a) OTPs go via **direct carrier submission** under contractual no-log /
≤24h-purge terms with audit rights — no aggregator in the path; (b) enrolment OTP traffic is
**mixed with decoy messages** to non-enrolling numbers at the carrier interface, so even carrier-
side delivery metadata doesn't cleanly enumerate enrolees; (c) the delivery payload and sender
identity are generic (no "SafeGen" branding in the SMS route metadata); (d) roadmap: replace SMS
with an **app/passkey possession proof** on the child's device, removing the cleartext number
from the delivery path entirely. The EV's own handling is already session-scoped (§4).

**Residual.** Carrier-side lawful-interception and network-operator visibility of SMS delivery is
irreducible while SMS is used — the same exposure as every OTP the child's bank sends, but stated
here rather than hidden. **Severity after mitigation: Low-Medium; the honest weak point of the
enrolment ceremony until the app-based proof ships.**

---

## Summary matrix

| Threat | Attacker | Pre-mitigation | Post-mitigation | Load-bearing controls |
|---|---|---|---|---|
| T1 dictionary reversal | EXT/PLAT/GOV | **Critical** | Low | Split-key OPRF, HSMs, dual rate limits + burst caps, annual key rotation, public volume logs |
| T2 parent impersonation | SOC | High | Low-Med | Adult ID assertion, OTP-to-child-handset, consequence capping |
| T3 honeypot/breach | EXT/INS/GOV | **Critical** | Low (bounded) | Minimal schema, split keys (offshore trustee vs TOLA), local matching, heartbeat canaries, kill switch |
| T4 platform enumeration/caching | PLAT | High | Low-Med (disclosed) | Quotas at EN **and** CT, transparency log, annual key rotation |
| T5 malicious service | INS-L | Med | Low | VOPRF proofs, count reconciliation, attested builds |
| T6 adult DoS | SOC | Med | Low | Waterfall-not-block, override, revoke-by-possession |
| T7 traffic analysis | NET | Low | Low | Local matching, constant-size responses |
| T8 SIM recycling | — | Med | Low | Expiry-at-16, possession revocation, telco feeds |
| T9 parent device | EXT | Low | Low | Session-scoped everything |
| T10 SMS delivery path | EXT/INS | High | Low-Med | Direct carrier no-log terms, decoy traffic, app-based proof (roadmap) |

**The two assumptions the whole design rests on** (state them to regulators before they find
them): (1) SafeGen and the Trustee do not collude and are not simultaneously compromised;
(2) platform quota sizing honestly tracks legitimate need. Both are organisational, both are
publicly observable in the transparency log, and both fail *loudly* rather than silently.
