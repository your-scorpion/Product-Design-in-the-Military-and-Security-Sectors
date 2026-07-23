# Red Flags and Fixes

Use this file to prioritize. A finding earns its place here when it explains why the gap matters and names a concrete next action — not when it just restates that something could theoretically be improved.

## Critical Red Flags

- A security or classification boundary is enforced only by an application-level check or human discipline, with no hard mechanism — a database constraint, a physical gate — backing it up.
- A design assumes uniform sensitivity for something that can plausibly contain mixed sensitivity within a single unit: a document with both public and restricted sections, a site with both public and secure zones, a record with some fields more sensitive than others.
- External or field-sourced input — a retrieved document, a sensor reading, a user upload — feeds directly into a downstream decision with no distinction drawn between trusted and untrusted content.
- A safety-critical metric target is stated in words ("as low as possible," "rare," "minimal") rather than as a number with a stated sample size and a pass/fail threshold.
- A feedback loop that retrains, reconfigures, or adjusts system behavior trusts an unverified human report as ground truth, with no corroboration step before it's used.
- A system has a genuinely dangerous overload or failure state, and the design is silent on what happens — rather than a deliberately chosen, stated default for that state.

## Major Red Flags

- A cost ratio, threshold, or business-loss estimate is stated once as an "initial estimate" with no stated plan to revisit or refine it as real data accumulates.
- Metadata a security or access decision depends on — a classification tag, a clearance level, an environmental classification — is assumed static, with no process for re-verifying it over time.
- Two competing approaches (two models, two verification methods, two design options) are described as running in parallel with no stated rule for what happens when they disagree.
- A validation or adversarial-testing activity is performed by the same team that built the thing being tested, with no independent check.
- A monitoring metric is named without saying who looks at it, how often, or what action follows a bad reading.
- Two confounded variables (device age and firmware version; client tier and data richness; hardware generation and installation era) are both used as model features with no attempt to separate their effects.

## Access Control and Trust-Boundary Checklist

Walk this list wherever a design has anything resembling a sensitivity level, a permission, or a trust boundary — a classification tag, a clearance level, a "this content is safe to show" assumption of any kind.

- Sensitivity is enforced at the lowest technical layer available (a database constraint, a hardware interlock), not only checked by application logic sitting on top of it.
- A single source object can plausibly contain mixed sensitivity internally — check whether tagging happens at the right granularity (section, zone, field) rather than only at the object's outermost level.
- Whatever grants access is checked against a live, current source at the moment of use, not a cached value that could be stale by the time it actually matters.
- A sequence of individually-safe actions (queries, views, permissions, partial answers) is checked for whether it can be combined to reconstruct something none of them exposed on its own.
- Content crossing a trust boundary — retrieved text entering a prompt, sensor data entering a model, an uploaded file entering a pipeline — is treated as data by whatever consumes it next, never as an instruction or as verified ground truth.
- The enforcement mechanism's own failure mode is defined: if the access check itself is unavailable, slow, or errors, does the system fail open (dangerous) or fail closed (safe)? Silence on this question defaults to whatever the infrastructure happens to do, which is rarely the safe answer.

## Low-Hanging Fixes

- Add a one-line, falsifiable target — a number and a sample size — for every safety-critical claim currently expressed as an adjective.
- Add a stated default behavior for every overload or failure state the design otherwise leaves silent.
- Add an owner and a review cadence to every monitoring metric already listed, rather than leaving "someone will check this" implicit.
- Add a note on whether validation or red-teaming happens independently of the build team; if it doesn't yet, list that as an open item rather than letting it pass unmentioned.
- Add an explicit maximum-staleness figure wherever caching sits anywhere near an access or classification decision.
- Add a one-sentence rule for what happens when two parallel models, checks, or reviewers disagree, rather than leaving the reconciliation undefined.

## Fix Plan Ordering

Prefer this sequence:

1. Close any access or classification enforcement gap that currently depends only on an application check or human memory.
2. Replace every safety-critical target currently expressed in words with a real number and a way to test it.
3. Define the failure or overload default for anything the design currently leaves silent.
4. Add independence to any validation or red-teaming step currently performed only by the build team.
5. Only then, invest further effort in modeling sophistication or architectural elaboration.
