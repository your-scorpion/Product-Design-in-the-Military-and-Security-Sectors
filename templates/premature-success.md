---
name: security-premature-success
description: >
  Anti-pattern guard for products in cybersecurity, defense, and security operations — catches
  "done" claims that rest on passing tests, green builds, or a 200 response rather than on the
  actual security-relevant scenario run end-to-end. Use whenever something is declared fixed,
  shipped, or verified — a PR, a status update, a brief marked complete, a compliance evidence
  package. Trigger on: "this is done", "tests pass, ready to merge", "the control is in place",
  "mark this verified", "close this out", or any completion claim about a security-relevant
  change. Complements `skills/one-feature-at-a-time.md`'s Verification Plan requirement by naming
  the specific ways teams convince themselves a control works when it doesn't. Treats a passing
  test suite as evidence of nothing beyond what the suite actually checked.
metadata:
  version: 0.1.0
  scope: security-defense-verification-discipline
  reference: https://github.com/your-scorpion/Product-Design-in-the-Military-and-Security-Sectors
  companion_skill: security-one-feature-at-a-time, security-triage-state-machine
---

# Premature Success — Security Verification Discipline

Unit tests pass. `curl` returns 200. The build is green. Nobody ran the actual scenario end to end.

The source anti-pattern names a productivity failure: a demo or a user discovers the integration
gap first, and someone has to re-diagnose a "done" feature from scratch. That's real, but it's not
the worst version of this failure. It's the *tame* version.

> **In this domain, "200 OK" and "the control works" are different claims, and the gap between them
> is exactly where a control fails silently.** A login endpoint that returns 200 after a malformed
> MFA token was submitted didn't pass a test — it disclosed a bypass. The test suite that only
> checks the happy path will report that as success.

The worst version of this anti-pattern isn't a demo finding the gap. It's an attacker finding it,
in production, after the item was marked `Verified` and closed.

---

## Why "Verified" Gets Claimed Too Early

The source pattern lists test myopia, environmental friction, and scope creep in success metrics.
All three apply here, plus two that are specific to this domain:

- **A synthetic test of a control is not the control.** A unit test that calls the authorization
  function directly and asserts it returns `false` proves the function works. It proves nothing
  about whether the route in front of it actually calls that function, in that order, before the
  handler runs.
- **Staging parity is assumed, not checked.** A control gated by a feature flag, a WAF rule only
  attached to the production listener, a rate limit configured per-environment — "it works in
  staging" and "it works" are different claims whenever the control's *configuration*, not just its
  code, is the thing under test.

Both produce the same shape of failure the source pattern describes — status says done, behaviour
disagrees — but here the discovery event is a breach report, not a confused product manager.

---

## The Rule

**A security-relevant change is not `Verified` until the actual scenario has been run through the
real interface, including the case where someone is trying to defeat it.** Not the unit test for
the function underneath it. Not a curl call to the happy-path endpoint. The scenario, as an
attacker or a legitimate user would actually trigger it.

```
Before                                      After
──────                                      ─────
"Auth fix ready, all 14 tests pass"         Logged in through the real UI with a valid
                                             credential → succeeded. Attempted the same
                                             login with the previously-working bypass
                                             token → denied, logged, alerted. Screenshots
                                             and the alert-queue entry attached as evidence.
                                             Marked Verified only after both.
```

This is the same "execute the real scenario" fix the source pattern names — the addition here is
that the scenario is incomplete unless it includes the adversarial path, not just the intended one.

---

## What "Done" Actually Requires

Before anything is marked `Verified` — in a triage brief, a PR, a status update, or a compliance
evidence package — check every line:

- [ ] **The scenario ran through the real interface** — the UI, the actual API surface a client
      calls, the actual CLI — not a direct call to the function or handler underneath it.
- [ ] **The negative case ran, not just the positive one.** If this is a control, someone actually
      tried the thing it's supposed to block, through the real interface, and it was blocked.
- [ ] **It ran in an environment with the same configuration as production** — the same feature
      flags, the same network rules, the same WAF/rate-limit config — or the gap between that
      environment and production is named explicitly, not silently assumed away.
- [ ] **The evidence is the scenario's actual output** — a screenshot, a transcript, a log line, an
      alert that fired — not a test-runner summary that says "14 passed."
- [ ] **A passing build or green CI is treated as a precondition for verification, not as
      verification itself.** It means you're allowed to run the scenario next, not that you're done.

A brief field, a PR, or a compliance claim that skips any of these is at **Partially specified**,
not **Verified** — using the same four-tier signal `skills/triage-state-machine.md` and the review
skills already use. Say so in exactly those terms; don't let "tests pass" get silently upgraded to
"verified."

---

## Catching It in Someone Else's Claim

This skill is as often applied to a claim someone else made as to your own work — a PR that says
"ready to merge," a status update that says "fixed," a vendor's compliance attestation.

Ask, specifically:

- What was actually run, and through what interface? ("Tests pass" is not an answer to this.)
- Was the case where this is supposed to fail actually attempted?
- Is the environment this was checked in configured the same way production is?
- What's the artifact — the actual output — not the runner's pass count?

If the answer to any of these is missing, the claim is premature, regardless of how confident it
sounds or how green the build is. Say which specific check is missing — "no evidence the negative
case was run" — not a vague "needs more testing."

---

## Consequences of Getting This Wrong

The source pattern's list holds, with the security-specific tail added:

- A demo or user discovers the integration gap first — the tame case.
- **An attacker discovers the gap first, after the control was marked working.** The worst case,
  and the one a synthetic-only test suite cannot distinguish from success.
- Cascading diagnostic complexity: by the time the gap surfaces, other work has been built on top of
  the assumption that this control held.
- A compliance or audit record now states a control was verified when it was not — which is itself
  a finding, independent of whether the control turns out to work.

---

## Quality Gates

Verify before accepting or issuing any completion claim on a security-relevant change:

- [ ] The scenario was run through the real interface, not a synthetic call to the code underneath.
- [ ] The negative case was run, where a control is involved — not just the intended path.
- [ ] The verification environment's configuration parity with production is stated, not assumed.
- [ ] The evidence attached is the scenario's actual output, not a test-runner summary.
- [ ] A green build or passing suite is named as a precondition, never presented as the verification
      itself.
- [ ] Any claim that skips one of the above is graded `Partially specified`, not `Verified` — said
      in those words.

---

## Output

Use this when reviewing a completion claim, or before issuing your own.

```
## Verification check: <item / PR / claim>

**Claim:** <what was said — "tests pass, ready to merge" etc., quoted>

**What was actually run:**
- Interface: <real UI/API/CLI | synthetic call to underlying code — specify>
- Positive case: <run, with evidence | not run>
- Negative case: <run, with evidence | not run | n/a — no control involved>
- Environment parity with production: <stated and matching | stated gap: <what> | not addressed>

**Evidence attached:** <screenshot / transcript / log / alert entry | test-runner summary only | none>

**Signal:** <Verified | Partially specified | Assumed>
**Because:** <the specific missing check, not "needs more testing">

**If not Verified — what closes the gap:**
<the specific scenario, environment, or evidence still needed>
```
