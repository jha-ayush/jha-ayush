<h1 align="center">Ayush Jha</h1>

<p align="center">
  <strong>I build systems that can't pretend to know things they don't.</strong>
</p>

<p align="center">
  <a href="https://uas-skycheck.app">
    <img src="https://img.shields.io/badge/Live-uas--skycheck.app-FF6B35?style=for-the-badge&logo=vercel&logoColor=white" alt="UAS SkyCheck"/>
  </a>
  &nbsp;
  <a href="https://github.com/jha-ayush/uas-skycheck-architecture">
    <img src="https://img.shields.io/badge/Read-Architecture%20%26%20Design-88a8c8?style=for-the-badge" alt="Architecture"/>
  </a>
</p>

---

## The rule

> **Unknown is not neutral. Unknown is a caution.**

Most systems treat "I couldn't determine this" as an error to route around. Catch it, log it, substitute something plausible, move on. That's fine for a dashboard. It's dangerous for anything a person makes a decision on, because the substitute looks exactly like an answer.

So I build the other way. Unknown gets to be a real value, sitting alongside the real answers, able to change the outcome:

```python
# RedEdge Readiness: field readiness for multispectral camera crews
RANK = {"GO": 1, "CHECK": 2, "UNKNOWN": 2, "NO-GO": 3}
```

`UNKNOWN` is not `None`. It's not an exception. It's not a missing key. It has a rank, and that rank is the same as `CHECK`, because a reading you couldn't take is a reason to look, not a reason to relax.

Here's why that matters more than it sounds: a value that exists in the type can't be quietly dropped by code that forgot to handle it. Discipline doesn't survive a refactor. Types do.

[The code is public.](https://github.com/jha-ayush/rededge-readiness)

---

## The same rule, different domain

[**UAS SkyCheck**](https://uas-skycheck.app) is FAA Part 107 preflight intelligence. A drone pilot is standing in a field, wind is picking up, and they need to know whether it's legal and safe to fly right now. Airspace, restricted zones, weather, TFRs, density altitude, civil twilight, one screen.

One rule governs the whole thing:

> **A false all-clear has to be impossible by construction.**

Four conditions hard-cap the safety score at 69, the top of CAUTION and one point below GOOD: simulated weather, Class B proximity, stale TFR data, unknown elevation. Not a penalty that a good day can outweigh. A cap, which says something different: I can't certify this, so I won't. Each one is itemized in the breakdown with its reason, in plain language, so the pilot can see which piece of the answer is missing and go get it.

The service also refuses to start if its polygon engine can't build. Most systems degrade gracefully. This one degrades loudly, because a quietly degraded preflight verdict is worse than no tool at all. The pilot has already handed over the judgment.

Two products, unrelated domains, same rule. [Full reasoning is public.](https://github.com/jha-ayush/uas-skycheck-architecture)

---

## The time I broke it

The best test of a principle is what happens when you violate it.

SkyCheck's elevation lookup returned `0` on failure. The comment called it a "safe-side underestimate." Both halves of that were wrong.

**Underestimating a hazard is optimistic, not conservative.** Elevation feeds density altitude, and it's *high* DA that draws the penalty and the warning. A dropped lookup at a 7,000 ft site computed DA as if the pilot were at sea level. No penalty. No warning. A pilot in Leadville, Colorado at 10,150 ft read identically to a pilot in Miami.

The deeper problem was a typing mistake, in exactly the place my own rule should have caught: **0 ft is a real elevation.** Miami is 0 ft. Using `0` as the error value made "sea level" and "lookup failed" the same value. Unknown had no way to be expressed, so nothing downstream could act on it. Not the scorer. Not the advisory gate. Not even the test that claimed to cover it, which passed while being structurally unable to detect what it asserted.

It returns `None` now, resolves from three sources, and caps into CAUTION if all three fail.

The lesson is bigger than the bug: **an error value you can't tell apart from a valid reading isn't a fallback. It's silent corruption.**

---

## What I'm building

| | |
|---|---|
| **[UAS SkyCheck](https://uas-skycheck.app)** | FAA Part 107 preflight intelligence. Next.js 14 PWA, FastAPI, Supabase, Shapely/STRtree geometry over polygon-backed zones. Deterministic scoring, no model in the path between input and score. Built solo. [Architecture](https://github.com/jha-ayush/uas-skycheck-architecture) |
| **[RedEdge Readiness](https://github.com/jha-ayush/rededge-readiness)** | Zero-dependency field readiness checks for MicaSense RedEdge and Altum multispectral crews. Unconfirmed reads `CHECK`, lost link reads `NO-GO`, never a false pass. Three clients, one evaluation contract, no hardware needed to test it. |
| **Part 107 Exam Prep** | Standalone study app for the FAA Remote Pilot certificate. |

Founder, SudoKodes LLC.

I'm working toward my own Part 107 certificate, because a tool that tells pilots what's safe should be built by someone who has sat the same exam. I also volunteer with the Las Positas College drone program, coordinating crews and field operations across a flight season. That's where most of what I know about pilots under time pressure actually came from.

---

## What I care about in code

- **In a compliance tool, regulatory accuracy isn't a feature. It's the product.** Advisories cite the controlling reg (14 CFR 107.51, 107.29, 49 U.S.C. 44809) so a pilot can check the claim instead of trusting me.
- **Asymmetric failure deserves asymmetric design.** A false alarm costs a phone call. A false all-clear costs more. Pay the false alarms on purpose.
- **Tests should encode invariants, not behavior.** The useful ones fail when someone later optimizes a safety property away.
- **Dead code rots.** A recent audit deleted 5,565 lines. Documentation describing code that no longer exists is worse than no documentation.

---

<p align="center">
  <a href="https://uas-skycheck.app"><strong>UAS SkyCheck</strong></a>
  &nbsp;&nbsp;|&nbsp;&nbsp;
  <a href="https://github.com/jha-ayush/rededge-readiness"><strong>RedEdge Readiness</strong></a>
  &nbsp;&nbsp;|&nbsp;&nbsp;
  <a href="https://www.linkedin.com/in/jhaayush"><strong>LinkedIn</strong></a>
  &nbsp;&nbsp;|&nbsp;&nbsp;
  <a href="mailto:write2ayushjha@gmail.com"><strong>write2ayushjha@gmail.com</strong></a>
</p>
