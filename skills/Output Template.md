# Output Template

One template for every review, in two parts:

1. **Scorecard** — a short, shareable summary carrying the verdict. Every review produces one.
2. **Findings** — the detailed write-up that follows. A quick pass may stop at the scorecard; a full review fills every category that actually applies to the design under review.

Findings are ordered by how much they'd actually cost to get wrong, not by the order the categories happen to appear in the review reference.

## Verdict

The verdict is read off the Maturity Signals recorded for each review category (see `security-and-systems-review.md`), never assigned independently of them:

- **Blocked** — at least one category the reviewer has flagged as safety-critical for this specific design is at **Missing**. Nothing past that category should be considered settled until it's addressed.
- **Needs hardening** — no safety-critical category is Missing, but several categories sit at **Assumed** — a mechanism is implied but not actually named or enforced anywhere.
- **Workable, with open items** — most categories are at **Partially specified** or better, with a stated, trackable list of what's still open.
- **Ready for the next review stage** — every applicable category is at **Verified**, meaning each has a named mechanism and a stated way to test or audit it.

Name the weakest category driving the verdict in one sentence. The verdict summarizes the review; it doesn't soften a finding that's actually critical — a Blocked verdict with one severe finding still leads with that finding, not with the overall summary.

## Part 1: Scorecard

A self-contained block the team can share on its own — in a PR, a wiki page, or a chat thread. Fill every field.

Also save the filled scorecard as its own file so it's easy to find later: default to `security-review-<project-slug>.md` in the working directory. Name the saved path at the end of the review. Skip saving only if there's no writable location available, or the person doing the review asks not to.

```
## Security & Systems Design Review: <project name>

**Verdict:** <blocked | needs hardening | workable, with open items | ready for next stage>
**Weakest category:** <category name> — <one-line reason>
**Safety-critical findings:** <count, or none>

| Category | Maturity signal | Why |
|---|---|---|
| Security Governance & Compliance Fit | <Missing / Assumed / Partially specified / Verified> | <short reason> |
| Attack-Surface & Threat-Model Review | <signal> | <short reason> |
| Embedded, IoT & Physical-System Constraints | <signal, or n/a if not applicable> | <short reason> |
| GIS, Spatial & Sensor-Data Review | <signal, or n/a if not applicable> | <short reason> |
| Interface, Accessibility & Interaction Design | <signal> | <short reason> |
| Data & ML System Review | <signal, or n/a if not applicable> | <short reason> |

**Single highest-leverage fix:** <the one change that closes the most risk for the least effort>
```

Mark a category `n/a` only when it genuinely doesn't apply to this design (a pure UX doc with no ML component doesn't need a Data & ML System Review row) — not when the design simply hasn't addressed it yet, which is a Missing signal, not an inapplicable category.

## Part 2: Findings

Follows the scorecard (kept separate from the saved scorecard file, since it's meant to be read in full rather than screenshotted).

```
Evidence reviewed: <design docs, repo paths, or "no formal design doc available">
Review basis: <full design doc provided | partial doc, gaps inferred from repo | no doc, reviewed from repo/code alone>

Assumptions made, where evidence was incomplete
- <assumption> — based on <what was actually available to review>

Safety-critical findings
- <category> — <what's actually there, or missing>. <why it matters, concretely>. Fix: <specific next action>.

Notable findings
- <category> — <what's there>. <why it matters>. Fix: <specific next action>.

Minor findings
- <category> — <what's there>. Fix: <specific next action>.

Quick wins
- <a cheap, concrete fix> -> <what risk it actually closes>.

Worth keeping as-is
- <something the design genuinely got right, tied to the specific mechanism, not generic praise>.

Open questions for whoever owns this design
- <a question whose answer would change the design> — recommended default if no answer is given: <default>.

Suggested order of fixes
1. <first, and why it has to come before the others>
2. <second>
3. <third>
```

Skip a subsection entirely if there's genuinely nothing to put in it — an empty "Notable findings" heading with nothing underneath doesn't help anyone.

When the review is being read by the people who wrote the design, rather than by a separate approving team, open the findings with "Worth keeping as-is" instead of burying it near the end, and phrase every fix as a next step rather than a past-tense criticism.
