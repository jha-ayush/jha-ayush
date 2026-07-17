<h1 align="center">Ayush Jha</h1>

<p align="center">
  <strong>I build systems that are not allowed to be confident about what they don't know.</strong>
</p>

<p align="center">
  <a href="https://uas-skycheck.app">
    <img src="https://img.shields.io/badge/Live-uas--skycheck.app-FF6B35?style=for-the-badge&logo=vercel&logoColor=white" alt="UAS SkyCheck"/>
  </a>
  &nbsp;
  <a href="https://github.com/jha-ayush/uas-skycheck-architecture">
    <img src="https://img.shields.io/badge/Read-Architecture%20%26%20Design-88a8c8?style=for-the-badge" alt="Architecture"/>
  </a>
  &nbsp;
  <img src="https://img.shields.io/badge/Domain-FAA%20Part%20107%20%2F%20UAS-64748b?style=for-the-badge" alt="Domain"/>
</p>

---

## The rule

> **Unknown is not neutral. Unknown is a caution.**

Most systems treat "I couldn't determine this" as an error to route around: catch it, log it, fall back to something plausible, carry on. That is fine for a dashboard. It is dangerous for anything a person makes a decision on, because the fallback is indistinguishable from an answer.

So I build the other way. **Unknown has to be a first-class value in the domain's own type**, sitting next to the real answers, with the same standing to change the outcome:

```python
# RedEdge Readiness -- field readiness for multispectral camera crews
RANK = {"GO": 1, "CHECK": 2, "UNKNOWN": 2, "NO-GO": 3}
```

`UNKNOWN` is not `None`. It is not an exception. It is not a missing key. It has a number, and that number is the same as `CHECK`, because a reading you could not take is a reason to look, not a reason to relax.

This matters for a reason that is easy to miss: **a value that exists in the type cannot be silently dropped by a consumer that forgot to handle it.** Discipline does not survive a refactor. Types do.

---

## The same rule, a different domain

[**UAS SkyCheck**](https://uas-skycheck.app) is FAA Part 107 preflight intelligence: a drone pilot standing in a field, wind rising, deciding whether it is legal and safe to fly right now. 14,000+ airports, 11,000+ restricted zones, weather, TFRs, density altitude, civil twilight, in one screen.

Its governing invariant is one sentence:

> **A false all-clear must be structurally impossible.**

Four conditions hard-cap the safety score at **69**, the top of CAUTION, one point below GOOD: simulated weather, Class B proximity, stale TFR data, unknown elevation. Not a penalty. A statement: *I cannot certify this, so I will not.* The cap is itemized in the breakdown with its reason, in the pilot's language, so the pilot can see exactly which piece of the answer is missing and go resolve it.

The service also **refuses to start** if its polygon engine cannot build. Most systems degrade gracefully. This one degrades loudly, because a silently degraded preflight verdict is worse than no tool at all: the pilot has already outsourced the judgment.

Two products, unrelated domains, same rule. [The full reasoning is public.](https://github.com/jha-ayush/uas-skycheck-architecture)

---

## The time I broke it

The best evidence a principle is real is what happens when you violate it.

SkyCheck's elevation lookup returned `0` on failure, with a comment calling it a "safe-side underestimate." Both halves of that were wrong, and the second one dangerously so:

**Underestimating a hazard metric is optimistic, not conservative.** Elevation feeds density altitude, and it is *high* DA that draws the penalty and the warning. A dropped lookup at a 7,000 ft site computed DA as though the pilot were at sea level: no penalty, no warning. A pilot in Leadville (10,150 ft) read identically to a pilot in Miami.

The deeper defect was a typing failure, exactly where my own rule predicts: **0 ft is a real elevation.** Miami is 0 ft. Using `0` as the error value made "sea level" and "lookup failed" the same value, so unknown was not sayable, so nothing downstream could act on it. Not the scorer. Not the advisory gate. Not even the test that claimed to cover it, which passed while being unable to detect what it asserted.

It returns `None` now, resolves from three sources, and caps into CAUTION if all three fail. The lesson generalizes past this bug: **an error value indistinguishable from a valid reading is not a fallback, it is a silent corruption.**

---

## What I'm building

| | |
|---|---|
| **UAS SkyCheck** | FAA Part 107 preflight intelligence. Next.js 14 PWA, FastAPI, Supabase, Shapely/STRtree geometry over 9,000+ polygon-backed zones. Deterministic scoring, 1,500+ tests. Solo. [Live](https://uas-skycheck.app) / [Architecture](https://github.com/jha-ayush/uas-skycheck-architecture) |
| **RedEdge Readiness** | Zero-dependency field-readiness checks for MicaSense RedEdge and Altum multispectral crews. Unconfirmed reads `CHECK`, lost link reads `NO-GO`, never a false pass. |
| **Part 107 Exam Prep** | Standalone study app for the FAA Remote Pilot certificate. |

Founder, SudoKodes LLC. Currently working toward my own Part 107 certificate, because a tool that tells pilots what is safe should be built by someone who has sat the same exam. I also volunteer with the Las Positas College drone program, coordinating crews and field operations across a flight season, which is where most of what I know about pilots under time pressure actually came from.

---

## What I care about in code

- **Regulatory accuracy is not a feature of a compliance tool. It is the tool.** Advisories cite the controlling reg (14 CFR 107.51, 107.29, 49 U.S.C. 44809) so a pilot can verify the claim instead of trusting me.
- **Asymmetric failure modes deserve asymmetric design.** A false alarm costs a phone call. A false all-clear costs more. Design accordingly, and pay the false alarms on purpose.
- **Tests should encode invariants, not behavior.** The useful ones fail when someone later optimizes a safety property away.
- **Dead code rots.** A recent audit deleted 5,565 lines from SkyCheck. Documentation that describes code that no longer exists is worse than no documentation.

---

<p align="center">
  <a href="https://uas-skycheck.app"><strong>UAS SkyCheck</strong></a>
  &nbsp;&nbsp;|&nbsp;&nbsp;
  <a href="https://github.com/jha-ayush/uas-skycheck-architecture"><strong>Architecture</strong></a>
  &nbsp;&nbsp;|&nbsp;&nbsp;
  <a href="https://www.linkedin.com/in/jhaayush"><strong>LinkedIn</strong></a>
  &nbsp;&nbsp;|&nbsp;&nbsp;
  <a href="mailto:admin@sudokode.co"><strong>admin@sudokode.co</strong></a>
</p>
