# Product Design Document Template — Lead Product Designer, Cybersecurity


## Executive Summary
- **Initiative Name**: [Name]
- **Problem Statement**: [Brief description of the security/UX problem]
- **Business & Security Impact**: [Expected impact, KPIs, risk reduction]
- **Timeline**: [High-level timeline]

## I. Problem Definition

### i. Origin
- What is the core problem, stated as a falsifiable hypothesis rather than a feeling?
- Who are the stakeholders on both sides — security/compliance and business/end users?
- What is the current workflow, and where does it actually break down today?
- What behavior are we trying to change, and in whom?

### ii. Relevance and Risk
- Why does this problem matter now?
- What is the risk if we do nothing, assessed along severity, frequency, and criticality?
- What does the current problem cost — in incidents, support load, or lost trust?
- Is the hypothesis attainable within a short validation cycle, or is it a moonshot?

### iii. Previous Work
- What existing controls, flows, or campaigns have been tried?
- What worked, what didn't, and why?
- What did prior research (interviews, audits, incident post-mortems) already tell us?
- Can an existing solution be improved rather than replaced?

### iv. Stakeholders, Conflicts, and Risk
- Where do security's goals and users' goals genuinely conflict?
- What is the cost of a mistake here — for the user, for the business, for compliance?
- What checks and balances (review, sign-off, audit) does this initiative need?
- What is the Definition of Done, in concrete terms?

## II. Threat & Regulatory Context

### i. Applicable Standards
- Which standards apply (ISO/IEC 27001 & 27002, NIST SP 800-53, PCI DSS, HIPAA/HITRUST, ISO 31000)?
- What do they actually require versus merely recommend?
- Does this fall under an existing ISMS scope or Statement of Applicability?

### ii. Policy, Standard, Procedure, or Guideline
- Is what we're designing a mandatory policy, a prescriptive standard, a step-by-step procedure, or optional guidance?
- Who is accountable for keeping it current once shipped?
- Is it written so a non-specialist could actually follow it?

### iii. Risk Model
- What is the threat, asset, vulnerability, control, and risk for this specific surface?
- Does the design address confidentiality, integrity, and availability, or only one of the three?
- What raises a target user's individual threat profile, and how much friction can we justify as a result?

### iv. Incident Response & Evidentiary Requirements
- Does this design create or close a logging/audit-trail gap?
- If something goes wrong, can we reconstruct what happened (non-repudiation)?
- Who owns incident response for this flow, and do they have what they need to act quickly?

## III. User & Stakeholder Research

### i. Research Method Selection
- What hypothesis are we testing, and which method actually answers it — interview, survey, usability test, field research, or desk research?
- Is a single method enough, or does this need triangulation (e.g., interview + usability test in the same session)?

### ii. Respondent Screening
- Who is the right respondent, and what does a claimed credential (CEH, OSCP, ISO 27001 lead auditor) actually prove about their skill?
- How will we verify expertise rather than confidence?
- What incentive fits a security-professional audience?

### iii. Interview & Question Design
- Are questions built around What / How / Why, avoiding leading and future-tense phrasing?
- Where sensitive behavior is involved, are we using projective questions instead of asking respondents to self-incriminate?
- Have we piloted the script with internal experts first?

### iv. Synthesis
- Are insights captured as job stories (situation → motivation → outcome), not personas alone?
- Does the CJM include an explicit "key conflict" section, or is it just an as-is description?
- Who owns the research artifact after the study ends?

## IV. Security UX Heuristics

### i. Understandability & Trust
- Can the user always find security-relevant information, and is it shown only when relevant to the current task?
- Does the system tell the user when it's in an insecure state, and what to do about it?
- Is security terminology plain, consistent, and standards-aligned?

### ii. Authentication & Control
- Does authentication minimize cognitive load while still enforcing a strong, understandable policy?
- Can users configure, undo, or cancel their own security-related actions?
- Are "security by default" and "privacy by default" the actual defaults, applied consistently?

### iii. Error Recovery & Support
- Do error messages state cause, severity, and required action, in plain language?
- Is documentation reachable from every screen without interrupting the current task?
- Are users informed of changes to security measures before those changes bite them?

### iv. Accessibility
- Does the flow work for users with visual, motor, or literacy constraints — not just WCAG contrast minimums?
- Are alternative authentication paths (graphical, text, assisted) available where needed?
- Does anything in the flow quietly train users toward unsafe workarounds?

## V. Metrics & Measurement

### i. Metric Definition
- Is each metric quantitative, not "high/low" or an unfalsifiable "% risk reduction"?
- Does the metric map to the universal trio — effectiveness, efficiency, satisfaction?
- What's actually underneath the number (source data, platform, content, storage) before it goes in front of stakeholders?

### ii. Measurement Method
- Can this be measured automatically, or does it require observation — and if observation, what does that distort?
- How will we combine self-report and automated signal to cover each method's blind spots?

### iii. Reporting & SLA Alignment
- Is the metric tied to an SLA or risk-treatment plan, or just a dashboard nobody acts on?
- Have we checked readability (wording) and legibility (presentation) of security copy separately?

## VI. Design Process & Artifacts

### i. Ideation & Co-creation
- Have the people who actually have the problem (security champions, SOC analysts, affected employees) been brought into the working session?
- Did co-creation produce something more concrete than a workshop output — a card sort, a tree test, a prototype?

### ii. Behavior-Change Validation
- Does the design follow a behavior-change model: identify problem → context → build → apply with metrics → measure impact?
- Which specific capability barriers does the design remove, and which does it deliberately leave standing?
- Is there a clear, communicated reason wherever the design trades convenience for security?

### iii. Prototyping & Pilot Testing
- Was the design piloted with internal experts before the first real respondent session?
- Does it fit or deliberately extend the existing design system, and is that extension justified?

## VII. Standards, Compliance & Documentation

### i. Documentation Requirements
- Is every mandatory rule this design implies written down as a findable policy/standard/procedure?
- Is it in plain language, and part of onboarding rather than a buried wiki page?

### ii. Ownership
- Who owns this artifact after ship — research, PM, or security — and is that agreed, not assumed?
- What happens when the artifact goes stale?

### iii. Rollback & Incident Paths
- Does the new control or flow have a documented rollback path?
- Are data-privacy and third-party data-sharing consent flows covered explicitly?

## VIII. Cross-Functional & Technical Fluency

### i. Technical Literacy
- Do we understand enough of the relevant stack (TCP/TLS/HTTP, MITM, certificate tiers) to have a real conversation with security engineers, not just relay their words?

### ii. Communication Across Audiences
- Can this be explained, without changing the underlying facts, to both a non-technical employee and a hardened security specialist?

### iii. Negotiating Security-vs-Usability Tension
- Where does "the security team would lock everything down given the chance" tension show up here?
- Have we designed the negotiation between security and usability, not just the interface?
- Does added friction shift cost onto a downstream team or onto the user's unmanaged workaround?

## IX. Team & Department Leadership

### i. Delegation & Capacity
- Who is this delegated to, do they have matching responsibility, growth direction, and actual capacity?
- Do they have the access and contacts they need, and is the Definition of Done agreed?

### ii. Environment & Management Style
- Where does this team/initiative sit on Cynefin (clear, complicated, complex, chaotic)?
- Does the management style (directing → coaching → supporting → delegating) match the team's actual maturity, or is it defaulting to habit?

### iii. OKRs & Cross-Team Dependencies
- Does the OKR trace goal → metric → project → resource?
- Are cross-team dependencies written into both sides' OKRs, or assumed?

### iv. Competency, Reviews & Hiring
- Where does each person sit on the competency matrix (asset creation, technical knowledge, decision-making, industry understanding, problem-solving)?
- Are performance ratings backed by criteria (product impact, technical mastery, self/team management), not gut feel?
- Do hiring questions probe for real signal instead of rehearsed answers?

### v. Budget & Risk
- Does the budget model (fixed-price vs. T&M) match the actual risk being carried?
- What single point of failure (a key person, a client dependency) deserves personal attention rather than delegation?

## X. Presentation & Stakeholder Communication

### i. Narrative Structure
- Does the pitch run as a story — audience, problem, size, solution, economics, team, ask — rather than a slide-by-slide read-through?
- Is disproportionate effort going into the first slide (hook) and the last slide (contacts, memorable fact)?

### ii. Evidence & Objections
- Is every claim to leadership backed by a number?
- Have we pre-empted the "why should security trust this" objection before someone else raises it?
- Does the deck stand on its own if it's sent rather than presented live?

## XI. Ethics, Privacy & Accessibility

### i. Consent & Data Handling
- Is consent, NDA, and data-handling explicit wherever research or product data touches sensitive information?
- Does the design avoid blaming the user for a failure that was actually a usability failure?

### ii. Inclusive Design
- Does the design disproportionately burden users with disabilities, low literacy, or limited access to modern devices?

### iii. Cultural & Legal Context
- Has cultural, legal, and linguistic context been considered for any control rolled out across multiple countries or business units?

## XII. Launch, Monitoring & Continuous Improvement

### i. Success Metrics & Feedback Loops
- What metric will tell us within weeks, not quarters, whether the behavior actually changed?
- Is there a feedback loop from real incidents (phishing reports, lockouts, support tickets) back into design iteration?

### ii. Drift & Re-validation
- When is the next validation cycle scheduled, rather than treating ship as done?
- How will the design be revisited as threats, regulations, and organizational behavior change?
