# Security & Systems Product-Design Review

Load this reference when reviewing a product or system design that touches security governance, embedded or IoT hardware, GIS or sensor data, or interface and interaction design for a security-sensitive or physical-systems context.

A security-sensitive product design isn't just a UX design with extra caveats, and it isn't just an ML system with a compliance checklist bolted on afterward. The failure modes that actually matter tend to sit at the seams — between a UX decision and the threat model it quietly assumes away, between a sensor's real physical constraints and a classifier's assumptions about clean data, between what a standard requires on paper and what a design doc actually specifies in practice. Review the seams, not just the sections.

## Core Review Questions

- What is the actual asset being protected, and who is the design protecting it from? A one-sentence threat model, not a vague "security matters here."
- Where does human judgment sit, and where does a hard, enforced mechanism sit — and can the human judgment be bypassed under time pressure, fatigue, or a convincing-looking edge case?
- What happens when connectivity, power, or a sensor feed degrades? Does the design state a tested behavior, or does it just assume the happy path continues indefinitely?
- Which specific standard, if any, does this design's security claims actually map to — ISO/IEC 27001 or 27002, NIST CSF, PCI DSS, or a named internal control — or are "secure" and "compliant" being used as adjectives with nothing behind them?
- Is there a falsifiable metric or threshold for every safety-critical claim, or does the design lean on words like "minimal," "rare," or "as needed" in exactly the spots where a number belongs?

## Maturity Signals

Instead of a single letter grade, judge each review category below against four signals, and name the weakest one when reporting back:

- **Missing** — the design doesn't address this at all.
- **Assumed** — the design implies a safe answer without stating a mechanism ("access is restricted" with no enforcement point named).
- **Partially specified** — a real mechanism exists, but a threshold, cadence, or edge case is left undefined.
- **Verified** — a mechanism exists, its parameters are stated, and there's a stated way to test or audit it.

## Security Governance & Compliance Fit

*Grounded in: "Managing Paper Security," "Security of Computer Systems"*

- Does the design name the specific standard or control it's satisfying, rather than gesturing at "best practice"?
- Is there a documented owner for every control the design introduces — who's accountable if it fails, not just who built it?
- Does the design distinguish a policy (mandatory, high-level) from a procedure (mandatory, step-by-step) from a guideline (optional) — or does it flatten all three into "the rules"?
- Is risk expressed along severity, frequency, and criticality, or as an unscored "this could be bad"?
- Does the design have a stated incident-response and rollback path, or does "we'll figure it out" stand in for one?

**Red flags:**
- A claim of "ISO 27001 compliant" with no named control or Statement-of-Applicability reference behind it.
- A risk register that lists risks but never assigns likelihood, impact, or an owner.
- A control that exists only as a sentence in a design doc, with no corresponding enforcement mechanism named anywhere in the system.

## Attack-Surface & Threat-Model Review

*Grounded in: "Analyzing Malicious Files," "Security of Computer Systems," "Preparing a Security Report"*

- Is externally-sourced or user-supplied content treated as untrusted input throughout the system, or does trust quietly get assumed the moment content passes one initial check?
- For anything web-facing: are session handling, input validation, and output encoding addressed as distinct concerns, rather than folded into a single "we sanitize inputs" sentence?
- For anything with a network surface: is the design's exposure actually mapped (open ports, exposed admin interfaces, default credentials), or is "we have a firewall" doing all the work?
- Does the design account for an insider or a physically-present adversary, not only a remote one?
- Is there a named, independent party who tries to break the design's stated guarantees before launch, distinct from the team that built them?

**Red flags:**
- A single sentence ("we validate all inputs") standing in for a real threat model.
- No distinction between a remote attacker and someone with physical or insider access.
- The team that built a control is also the only party who tests whether it holds.

## Embedded, IoT & Physical-System Constraints

*Grounded in: "Machine Learning on ARM Microcontrollers," "The Anatomy and Diagnostics of Car Electrics"*

- Does the design state its actual power budget — instantaneous versus average draw — rather than assuming "low power" is a sufficient description?
- Is there a stated, tested behavior for degraded or lost connectivity, distinct from the normal-operation path?
- Does a predictive or anomaly-detection component account for sparse or noisy telemetry from older or lower-fidelity hardware generations, or does it implicitly assume uniform data quality across the whole fleet?
- Is physical tampering or sensor spoofing considered as a real input risk, not just a software-side one?
- Are hardware failure costs (false negative vs. false positive) stated as an explicit, justified ratio, or assumed symmetric by default?

**Red flags:**
- "The device reports its status" with no discussion of what happens when it stops reporting, or reports something physically implausible.
- A predictive-maintenance or anomaly model trained without checking whether its worst-performing segment is also its highest-risk one.
- No mention of what a compromised or tampered sensor reading would even look like.

## GIS, Spatial & Sensor-Data Review

*Grounded in: "Collecting and Visualizing GIS Data"*

- Is the spatial and spectral resolution of the data source actually matched to the claim being made from it, rather than assumed adequate because "we have satellite imagery"?
- Is ground-truth validation described for any remote-sensing-derived claim, or is a derived index (NDVI, SAVI, and similar) presented as fact without a stated confidence level?
- Does the design account for the specific limitations of the chosen sensing method (a stated resolution ceiling, a known blind spot, seasonal or tidal variation) rather than treating the data source as uniformly reliable?
- Is there a stated process for reconciling automated detection against a human field check, particularly where the automated signal drives an operational decision?

**Red flags:**
- A remote-sensing-derived number presented with no stated margin of error or validation method.
- No mention of how often, or under what conditions, a human actually verifies what the sensor or imagery claims.

## Interface, Accessibility & Interaction Design

*Grounded in: "Xcode for Interface Prototypes," "User Experience in Virtual Reality," "Composition and Grids for Web Design," "Basic Rules for Book Typesetting," "Preparing Specifications for Multiplatform Products"*

- Does the design specify behavior across device density, screen size, and platform variation explicitly, rather than assuming one reference resolution generalizes?
- For anything involving sustained attention or alerting: is alert fatigue and mode confusion addressed directly, with a stated mechanism for distinguishing severity, not just a uniform notification style?
- For anything immersive or attention-demanding: is cognitive load under real operating stress (time pressure, fatigue, high information volume) considered, rather than only under calm demo conditions?
- Where the product spans multiple languages or scripts: are typographic and localization conventions (text direction, quotation and punctuation rules, character sets) addressed as real requirements, not an afterthought?
- Is accessibility addressed against a stated baseline (a named WCAG level, for instance), rather than left implicit?

**Red flags:**
- A single reference screen size or resolution standing in for the full range of real devices.
- No distinction made between an interface's normal state and its alert or incident state.
- "It looked fine in the demo" as the only accessibility or fatigue-related validation performed.

## Data & ML System Review

*Grounded in: "Recommendation Systems: ALS, AP@k, NDCG," "Extracting and Filtering Data — SQL"*

- Is the chosen metric actually appropriate to the underlying data shape (rare events, ranked results, count data), or was a familiar metric chosen because it's familiar rather than because it fits?
- Is the validation split genuinely free of leakage — no future information reaching a training window, no label arriving before it would realistically be available?
- Where data is pulled or transformed at scale: is the query or pipeline logic reviewed for correctness (join keys, aggregation grain, null handling) with the same rigor as the model itself?
- Is there a stated baseline the more sophisticated model is actually beating, or does the design skip straight to a complex model with nothing to compare it against?

**Red flags:**
- A ranking or rare-event problem evaluated with a metric built for continuous, balanced data.
- A validation approach that isn't stated precisely enough to tell whether leakage is actually possible.
- No baseline mentioned anywhere in the design.
