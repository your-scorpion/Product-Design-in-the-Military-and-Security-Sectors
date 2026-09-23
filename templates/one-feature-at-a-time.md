---
name: security-one-feature-at-a-time
description: >
  Execution discipline for agents doing hands-on work on products in cybersecurity, defense, and
  security operations — one verified feature per pass, never several partial ones. Use once an item
  has left triage as `ready-for-agent` (see `skills/triage-state-machine.md`) and work is about to
  start. Trigger on: "implement this", "start on the brief", "work through the feature list",
  "pick up where we left off", or any moment an agent is about to touch code, config, or
  infrastructure for a security-relevant product. Enforces that each pass stays inside the brief's
  Out of Bounds, verifies against its stated Verification Plan (not a generic smoke test), and
  routes anything discovered mid-pass back through triage rather than folding it into the diff.
metadata:
  version: 0.1.0
  scope: security-defense-execution-discipline
  reference: https://github.com/your-scorpion/Product-Design-in-the-Military-and-Security-Sectors
  companion_skill: security-triage-state-machine
---

# One Feature at a Time — Security Execution Discipline

An agent working unsupervised on a large task tends to start several features at once. Files
multiply, nothing is fully verifiable, and the next session inherits a pile of half-finished work
with no way to tell what's safe to build on.

In a general codebase that's a productivity problem. Here it's worse than that: **a half-finished
change to a security-relevant surface is not "in progress," it's a known-bad state left running.**
An auth check that's half-migrated, an alert rule that's half-tuned, a permission boundary mid-edit
— these aren't neutral. They're the exact class of thing this domain exists to prevent.

> **One verified feature, fully closed, beats four that "mostly work."** A pass that isn't verified
> hasn't reduced risk — it's relocated it into a diff nobody's checked yet.

This skill assumes the item already has a brief. If it doesn't, stop and run
`skills/triage-state-machine.md` first — a `ready-for-agent` brief is the input here, not optional
context.

---

## Mandatory First Step — load the brief, restate the boundary

Before writing anything, restate in one line, pulled from the triage brief:

```
Feature:       <the single thing this pass will complete>
Out of bounds: <files / systems / behaviours from the brief — verbatim>
Success:       <the specific criteria from the brief — not paraphrased into something looser>
Verification:  <the specific plan from the brief>
Blast radius:  <what breaks if this pass is wrong, and how it reverts>
```

If any of these is missing from the brief, this is not `ready-for-agent` work — stop and send it
back to triage rather than filling the gap yourself. Inventing a success criterion mid-pass is the
same failure the triage gate exists to prevent, one step downstream.

---

## The Rule

**One pass closes exactly one feature, verified, before another one starts.** Not "mostly done."
Not "the core logic is there, tests pending." Closed means: implemented, run against the brief's
actual Verification Plan, committed, and logged.

```
Without the discipline                    With the discipline
──────────────────────                    ────────────────────
Pass 1: A, B, C, D all touched            Pass 1: A only  → verified ✓ → committed
        none verified                     Pass 2: B only  → verified ✓ → committed
        session ends, state unclear       Pass 3: C only  → verified ✓ → committed
                                           Each pass leaves a working, checked state.
```

A "feature" here is sized to fit inside one pass with room left for verification and correction —
not sized to how much the brief seems to allow. If the brief describes something that doesn't fit
in one verifiable pass, split it and log the split; don't compress verification to fit the scope.

---

## What Counts as Scope Creep Here

The generic pattern calls this "while we're at it." In this domain it has two distinct shapes, and
they get different treatment:

- **Ordinary scope creep** — a nearby cleanup, an unrelated refactor, a "might as well" fix. Log it
  as a new list item. Do not touch it this pass.
- **A new security-relevant finding** — a second vulnerability noticed while fixing the first, a
  control that doesn't do what the brief assumed, a credential or secret sitting in a file you
  opened. **This does not become a fix folded into the current diff, even a small one.** Two
  unrelated security changes in one diff is exactly the state that makes a reviewer's job
  impossible and an audit trail useless.

**Any security-relevant discovery stops the pass and goes back through
`skills/triage-state-machine.md` as its own item** — never patched silently, never added to this
pass "since I'm already in the file." Note the discovery, note that the current pass is otherwise
unaffected, and continue only the original feature.

---

## The Out-of-Bounds Check Runs Twice

The triage brief's Out of Bounds list isn't advisory — it's the boundary that made this item
`ready-for-agent` instead of `ready-for-human` in the first place.

- **Before starting:** confirm the planned change doesn't touch anything on the list.
- **Before committing:** re-check the actual diff against the list — not the plan, the diff. Scope
  drifts during implementation more often than it's planned to.

If the diff has crossed a boundary — even to make the fix "more complete" — **stop, do not commit,
and return the item to `needs-triage`** with a note on exactly where the boundary was crossed and
why it seemed necessary. This is not a failure to fix quietly; it's the Autonomy Gate doing its job
one step later than ideal.

---

## Verification Is the Brief's Plan, Not a Vibe Check

"It looks right" and "the happy path works" are not verification. Run the specific
**Verification Plan** the brief names. For security-relevant features, that plan is incomplete
unless it also covers:

- **The negative case still holds.** If the change touches a control, confirm the control still
  denies what it denied before — not just that it now permits what it should permit. A permission
  change verified only on the allow path is unverified.
- **The failure mode is safe.** If the change can fail (network drop, malformed input, timeout),
  confirm it fails closed where the brief's Blast Radius says it must.
- **Nothing outside the feature moved.** A diff review against Out of Bounds, not just a test run.

If verification can't be completed — the test environment doesn't exist, the negative case can't be
exercised safely — **the pass is not closed.** Log exactly what couldn't be verified and route the
item back to `needs-triage` for a human to decide whether to proceed anyway. Do not commit an
unverifiable security change and call it done.

---

## Commit and Log

A closed pass commits with a message that names the single feature, references the brief/item id,
and states what verification was run — not just that it passed, but what was actually checked
(the specific negative case, the specific failure mode). This is what makes the commit trail useful
to a security review later; "fixed the bug" tells an auditor nothing.

Append one entry per closed pass to the item's progress log (`decisions/progress-log.md`, or the
location the brief names):

```
### Pass <n> — <feature>
- **Item:** <id / link>
- **Verified:** <what was actually run, including the negative case>
- **Out-of-bounds check:** clean | flagged — <what and why>
- **Discoveries logged separately:** <new triage item id(s), or "none">
- **Committed:** <ref>
```

**Before the next pass starts**, re-read this log and the brief. Confirm the next feature is still
the right next thing — a discovery logged two passes ago may have changed the priority.

---

## Quality Gates

Verify before calling a pass closed:

- [ ] Exactly one feature was touched this pass — not several partially.
- [ ] The diff was checked against the brief's Out of Bounds list, not just the plan.
- [ ] Verification ran the brief's actual Verification Plan, including the negative case where a
      control is involved.
- [ ] Any discovery outside the current feature was logged as a separate triage item, not folded
      into this diff.
- [ ] The commit message names what was verified, not just that "it works."
- [ ] The progress log entry is written before starting the next feature.
- [ ] If verification could not be completed, the pass is **not** marked closed, and the item was
      returned to `needs-triage` rather than shipped with a gap.

---

## Output (per pass)

```
## Pass <n>: <feature> — <item id>

**Scope this pass:** <the one thing done>
**Out-of-bounds check:** <clean | flagged — where>

**Verification run:**
- Success criteria: <met | not met — which>
- Negative case: <checked — result | n/a, because <reason>>
- Failure mode: <fails closed, confirmed | n/a>

**Discoveries (routed to triage, not fixed here):**
<new item id(s) and one-line description, or "none">

**Committed:** <ref>
**Progress log updated:** <yes>

**Next feature:** <what's next on the list, or "none — item complete, ready for review">
```

If verification failed or couldn't run, replace the last three lines with:

```
**Pass not closed.** <what couldn't be verified and why>
Routed back to needs-triage for a decision.
```
