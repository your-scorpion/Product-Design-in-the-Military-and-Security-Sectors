# Lead Product Designer (Cybersecurity) — Design Review Checklist

*Structured like [MLSystemDesign's design doc checklist](https://github.com/ML-SystemDesign/MLSystemDesign/blob/main/templates/design_doc_checklist.md), populated with material from [Product Design in the Military and Security Sectors](https://github.com/your-scorpion/Product-Design-in-the-Military-and-Security-Sectors/tree/main/references/book) (Notes on Running a Design Department, Choosing the Right UX Research Method for Cybersecurity Products, Heuristics for Usable Information Security, Regulations and Formalities in Information Security).*

## Problem Definition
- [ ] Stated the security/business problem as one or more falsifiable, SMART hypotheses (not a feeling — resolves to yes/no or a number)
- [ ] Traced each hypothesis through the chain: need → real problem → ideal customer → why we beat alternatives → what the solution must include → monetization → willingness to pay
- [ ] Confirmed the hypothesis isn't a moonshot — attainable within one to two weeks of validation effort
- [ ] Scored hypotheses by impact/effort and tagged which product layer they belong to (Value, Feature, Design, Feasibility)
- [ ] Identified stakeholders on both sides — security team, business/compliance, and end users/employees — and where their goals conflict
- [ ] Assessed risk along severity, frequency, and criticality before defining target behavior
- [ ] Defined what "done" looks like in concrete, non-vague terms (a Definition of Done for the initiative, not just the ticket)

## Threat & Regulatory Context
- [ ] Identified which standards apply (ISO/IEC 27001 & 27002, NIST SP 800-53, PCI DSS, HIPAA/HITRUST, ISO 31000) and what they actually require versus merely recommend
- [ ] Distinguished policy (mandatory, high-level) from standard (mandatory, prescriptive) from procedure (mandatory, step-by-step) from guideline (optional, best practice) for any new rule being designed
- [ ] Checked the Statement of Applicability / ISMS scope so the design doesn't contradict an existing risk treatment decision
- [ ] Defined threat, asset, vulnerability, control, and risk for the specific surface being designed
- [ ] Considered confidentiality, integrity, and availability explicitly, not just "security" in the abstract
- [ ] Flagged where a design decision could create an incident-response or evidentiary problem (logging, audit trail, non-repudiation)
- [ ] Checked what raises a target user's individual threat profile before calibrating how much friction a control is allowed to add

## User & Stakeholder Research
- [ ] Chose a research method that matches the hypothesis, not habit (interview vs. survey vs. usability test vs. field research vs. desk research)
- [ ] Screened respondents deliberately — decided what a cert (CEH, OSCP, ISO 27001 lead auditor) actually proves about skill before using it as a filter
- [ ] Wrote interview questions around What / How / Why, avoided leading and future-tense questions, kept closed questions for later in the script
- [ ] Used projective questions ("why do people do X") for sensitive security-behavior topics instead of asking respondents to self-incriminate
- [ ] Ran a problem interview before a solution interview; didn't mention the product/idea until the interview was over
- [ ] Captured insights as job stories (situation → motivation → outcome), not personas alone
- [ ] Built or updated a CJM with an explicit "key conflict" section, not just an as-is description
- [ ] Considered field research / contextual inquiry for understanding how security incidents and workarounds actually happen on the ground
- [ ] Recorded every interview and stored it somewhere that meets the same confidentiality bar the product itself is held to

## Security UX Heuristics
- [ ] Security-relevant information is accessible, concise, and shown only when relevant to the current task
- [ ] The system tells the user when it's in an insecure state and what mandatory action is required
- [ ] Authentication flows minimize cognitive load while enforcing a strong, understandable password/passphrase policy
- [ ] Users can undo, cancel, or confirm irreversible security actions, and can configure their own security preferences
- [ ] Security by default and privacy by default are the actual defaults, applied consistently across the whole system
- [ ] Error messages state cause, severity, and the action the user should take, in plain language
- [ ] Help and documentation are reachable from every screen without interrupting the current task
- [ ] Accessibility for authentication and security flows covers visual, motor, and literacy constraints, not just WCAG contrast ratios
- [ ] Re-ran Nielsen's heuristics through a security lens (status visibility, real-world match, error prevention, recognition over recall)
- [ ] Verified nothing in the flow trains users toward unsafe workarounds (sticky-note passwords, artificial mouse movement to dodge a lock screen)

## Metrics & Measurement
- [ ] Defined metrics with a quantitative expression, not "high/low" or "% risk reduction"
- [ ] Covered the universal trio — effectiveness, efficiency, satisfaction — for every security-facing flow
- [ ] Specified what's actually being measured underneath a metric (source data, platform, content, storage) before quoting a number to stakeholders
- [ ] Set up automated behavioral signals where observation alone would be too slow, too costly, or would change behavior by being noticed
- [ ] Tied metrics to the SLA / risk-treatment plan, not just to a dashboard nobody acts on
- [ ] Checked readability and legibility of security copy separately (Flesch–Kincaid / Gunning Fog for wording, actual type and layout for presentation)

## Design Process & Artifacts
- [ ] Ran ideation/co-creation with the people who actually have the problem (security champions, SOC analysts, end users) before locking a direction
- [ ] Validated the design against a behavior-change model (identify problem → context → build → apply with metrics → measure impact), not just against aesthetics
- [ ] Named the specific "capability barriers" the design removes, and which ones it leaves standing
- [ ] Confirmed the design doesn't ask users to trade convenience for security without a clear, communicated reason
- [ ] Checked the design against existing design-system patterns before introducing a new security-specific pattern
- [ ] Prototyped and pilot-tested with internal experts before the first real respondent session

## Standards, Compliance & Documentation
- [ ] Every mandatory rule the design implies is written as a policy/standard/procedure a real person can find and follow
- [ ] Regulations and documentation are in plain language (plainlanguage.gov bar), not security jargon, and included in onboarding
- [ ] Confirmed who owns the artifact after ship (research owns the finding, PM owns the roadmap decision, security owns the control)
- [ ] Checked that any new control or flow has a documented incident-response and rollback path
- [ ] Verified data privacy and third-party data-sharing consent flows are covered, not assumed

## Cross-Functional & Technical Fluency
- [ ] Understand enough of the client-server/network stack (TCP/TLS/HTTP, MITM, cert validation tiers DV/OV/EV) to have a real conversation with security engineers, not just translate their words
- [ ] Can explain the design's implications to both a non-technical employee and a hardened security specialist without changing the underlying facts
- [ ] Checked that any friction added by security does not simply shift cost onto a downstream team or onto the user's unmanaged workaround
- [ ] Identified where "the security team would lock everything down given the chance" tension exists, and designed the negotiation, not just the interface

## Team & Department Leadership
- [ ] Delegated by matching person → responsibility → growth direction → capacity check → access/contacts, with an agreed Definition of Done
- [ ] Placed the team's environment on Cynefin (clear / complicated / complex / chaotic) and matched the management style to it, not the other way around
- [ ] Set OKRs that trace goal → metric → project → resource, and checked cross-team dependencies are written into both sides' OKRs
- [ ] Calibrated steering style to team maturity (directing → coaching → supporting → delegating), with micromanagement reserved for genuine crisis
- [ ] Reviewed the competency matrix (asset creation, technical knowledge, decision-making, industry understanding, problem-solving) for each grade on the team
- [ ] Ran regular 1:1s, performance reviews, and exit interviews, with ratings backed by criteria (product impact, technical mastery, self/team management) rather than gut feel
- [ ] Checked hiring questions probe for real signal (achievements, feedback given/received, reasons for leaving) rather than rehearsed answers
- [ ] Verified the department budget model (fixed-price vs. T&M) and headcount math match the actual risk being carried
- [ ] Held a post-mortem/retro after major releases or incidents, and fed findings back into the regulation/procedure docs

## Presentation & Stakeholder Communication
- [ ] Structured the pitch as a story (audience → problem → size → solution → economics → team → ask), not a slide-by-slide read-through
- [ ] Spent disproportionate effort on the first slide (hook) and last slide (contacts, memorable fact, since it's on screen during Q&A)
- [ ] Backed every claim to leadership with a number, and pre-empted the "why should security trust this" objection
- [ ] Confirmed the deck stands on its own if it's sent rather than presented

## Ethics, Privacy & Accessibility
- [ ] Never designed a flow that blames the user for a security failure caused by poor usability
- [ ] Confirmed consent, NDA, and data-handling terms are explicit wherever research or product data touches sensitive information
- [ ] Checked the design doesn't disproportionately burden users with disabilities, low literacy, or limited access to modern devices
- [ ] Considered cultural, legal, and linguistic context for any control rolled out across multiple countries or business units

## Launch, Monitoring & Continuous Improvement
- [ ] Defined the metric that will tell you within weeks, not quarters, whether the behavior change actually took
- [ ] Set up a feedback loop from real incidents (phishing reports, lockouts, support tickets) back into design iteration
- [ ] Scheduled the next validation cycle instead of treating ship as done
- [ ] Planned for drift — how the design ages as threats, regulations, and organizational behavior change
