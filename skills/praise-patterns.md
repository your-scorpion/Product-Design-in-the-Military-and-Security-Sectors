# Praise Patterns

Praise should reinforce good engineering judgment, not flatter effort or pad out a report with review theater.

## Rule

Good praise names the actual mechanism, the specific risk or cost it addresses, and the condition under which that tradeoff was the right call:

```
This holds up because <specific mechanism> closes off <specific risk>, which matters given <specific condition of this design>.
```

If a sentence of praise would still make sense with the mechanism deleted, it isn't praise — it's decoration.

## Good Examples

- The access filter holds up because classification is enforced as a database constraint, not an application-layer convention — a check that depends on every code path remembering to run it eventually stops running.
- The severity design is strong because position and size carry the primary signal and color is only reinforcing, which matters given that color-only signaling would have failed colorblind operators outright.
- The rule-based fallback is a real strength because it keeps running underneath the ML model rather than being retired once the model ships — a fallback that only exists on paper isn't a fallback.
- The on-premise routing decision is reviewable because it's made after retrieval, based on what was actually retrieved, rather than as a static per-user assignment made in advance.
- The shadow-mode rollout is credible because it defines what gets reviewed and by whom before a single answer reaches a real user, not after.
- The persona work holds up because it separates a daily power user from a rare emergency-only user explicitly, rather than assuming one composite user covers both.
- The metric choice is strong where it's tied to an actual asymmetric cost, not treated as a single number to optimize in isolation.
- The escalation design is credible because it names both the trigger and the exact fallback behavior, not just "the system will handle it."

## Framework-Tied Praise

Use sparingly — the goal is to show the review categories doing real work, not to congratulate the design for existing.

- This is exactly what the Attack-Surface & Threat-Model category exists to catch early: the mechanism is named, and it's testable before launch rather than assumed to hold.
- The design earns real credit under Embedded & Physical-System Constraints specifically because it treats degraded connectivity as a designed-for state, not an edge case.
- This is the kind of finding the maturity-signal framework is built to surface: a category that looked Verified on the surface turned out to be Assumed once the actual enforcement mechanism was asked for.

## Verdict Lines

Use one verdict line after the scorecard, not scattered throughout the findings. Ground it in what was actually found — a category name and the specific gap or strength that drove the overall verdict — not a general impression of the design's quality.

- Good: "Ready for the next stage specifically because the access-control boundary is enforced at the database layer, not because every category is polished."
- Bad: "This is a strong, well-thought-out design overall."

## Hard Avoids

- Great job.
- Robust and scalable, unless the specific load and failure conditions tested are named.
- Enterprise-ready, battle-tested, or production-grade, unless release, monitoring, fallback, and ownership are all actually covered in the design.
- Military-grade or bank-grade anything, used as a substitute for naming the actual mechanism.
- Zero-trust, defense-in-depth, or similar architecture terms used as a label rather than a described mechanism.
- Comprehensive approach, unless the review names the concrete coverage that makes it so.
- Innovative or cutting-edge, unless the specific novelty is stated and justified against the alternatives it beat.
- Praise that could be pasted unchanged onto a completely different design and still sound true.

## Shareable Takeaway Patterns

- A classification tag is not an access control until something enforces it that isn't a human remembering to check.
- The cheapest fix is rarely a smarter model; it's usually making an already-stated assumption something the system actually verifies.
- A fallback that's never been exercised in production isn't a fallback — it's a hope with a diagram.
- Access control reviewed only per-query misses the failure mode that only shows up across a whole session.
- A design doc earns its keep when it states what would have to be true for it to fail, not just what it's supposed to do when it works.
