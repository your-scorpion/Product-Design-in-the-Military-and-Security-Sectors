## XIII. Robustness Hardening — Adversarial Review Findings

*This section documents findings from a structured adversarial review of the design above, run specifically to stress-test the safety claims in Sections II, III, and IX rather than the system's general functionality. Each finding below is paired with a concrete mechanism, not a restated intention — a design doc that says "we will ensure X" without saying *how* X is enforced hasn't actually specified X.*

### i. Classification-tag enforcement is a database constraint, not an application convention

Section III.iv states that an unclassified document is "held out of the retrieval index entirely." As written, this is an assertion about desired behavior, not a specified mechanism, and desired behavior that depends on every code path remembering to check for it eventually fails to check for it.

**Hardening:** The vector store's chunk schema defines `classification_tier` as a `NOT NULL` field with no default value, enforced at the database layer. Any write attempt without this field set fails at the database, not at whichever application code happened to remember to validate it. Any schema migration or bulk re-index operation requires a pre-migration integrity check — a count of chunks with a null or invalid classification tier, which must be zero before the migration is permitted to proceed.

### ii. Classification tagging happens at the section level for mixed-sensitivity documents

The original chunking strategy assumes one document carries one classification tier, inherited uniformly by every chunk within it. This breaks the moment a single document contains sections of differing sensitivity — a Restricted appendix attached to an otherwise Internal-tier policy, for instance — since document-level tagging would either mis-tag the whole document at the more permissive tier (a leak) or the more restrictive one (an availability problem, but at least a safe failure).

**Hardening:** Any document exceeding three pages, or explicitly flagged as mixed-sensitivity at ingestion, requires section-level classification tagging rather than a single document-level tag. Document-level-only tagging is only permitted where a human explicitly confirms, at ingestion, that the document is genuinely single-classification throughout. This confirmation is itself a required, logged step — not a default assumption.

### iii. Retrieved content is treated as untrusted input to the generation prompt

Every chunk fed into the LLM's context originates from a document that some human authored, edited, or scanned — any of which is a plausible vector for text that attempts to manipulate the model's behavior, whether through malicious intent or an accidental artifact of OCR or copy-paste. The current design treats retrieved content purely as helpful context, with no distinction between "information to answer from" and "instructions to obey."

**Hardening:** Retrieved chunks are wrapped as clearly-delimited, labeled data in the prompt, with explicit framing that content inside this boundary is reference material, not instructions to the model. The generation step includes an output-side check for the specific failure pattern of an answer appearing to follow a directive found within retrieved content rather than responding to the user's actual question. The adversarial validation subset (Section IV.ii) is expanded to include documents with deliberately embedded injection attempts, specifically to test this defense rather than assume it holds.

### iv. Session-level cumulative-disclosure tracking, alongside per-query access control

Per-query classification filtering is necessary but not sufficient: a sequence of individually-permitted answers can, taken together, reconstruct information the asker isn't cleared to see as a whole — a well-known aggregation/inference problem in access-controlled systems generally, not unique to this one.

**Hardening:** As a near-term mitigation (full aggregation-detection is a harder, longer-term research problem), sessions are monitored for a high volume of related queries clustered on a narrow topic, with a defined threshold that triggers a hold for human review rather than continued automatic answering. Query clusters are logged for periodic security review independent of the per-query safety metrics.

### v. The citation-verification component is validated against its own held-out test set

Citation Accuracy Rate depends entirely on an "automated entailment check" that the original design treats as a settled, reliable proxy. If that component itself has a meaningful false-negative rate — approving a citation that doesn't actually support the claim made — the entire safety case built on this metric is resting on an unaudited piece of software.

**Hardening:** The entailment/citation-verification component is validated against its own dedicated test set, built specifically to include subtle "sounds correct but doesn't actually follow" cases, not just clearly-correct and clearly-wrong pairs. Its false-negative rate is tracked as a distinct, first-class metric — separate from and reported alongside Citation Accuracy Rate itself — and reviewed on a quarterly basis by someone independent of the team that built it.

### vi. Shadow-mode review runs to a specified protocol, not an implied one

Section IX.ii describes shadow-mode review as "compliance officers review a sample of shadow answers," which specifies neither sample size, cadence, nor reviewer count — meaning the entire pre-launch safety gate is currently a promise rather than a process.

**Hardening:** During shadow mode, 100% of shadow answers touching Restricted or Client-Confidential content are reviewed, by two independent compliance officers each, with any disagreement between reviewers treated as a finding in its own right rather than resolved by simple majority. A 10% random sample of Public/Internal-tier shadow answers is reviewed on the same cadence. Review happens weekly throughout the shadow period, and the system does not exit shadow mode until a defined number of consecutive clean review cycles has passed.

### vii. Access-Violation Rate has a stated, falsifiable target

"As close to zero as testing can verify" cannot be tested against, because it isn't a number.

**Hardening:** The stated target is zero confirmed access violations across a minimum of 2,000 stratified production answers per quarter, with Restricted and Client-Confidential-tier retrievals reviewed at 100%, not sampled, given both their rarity and their stakes. Any single confirmed violation halts serving for the affected query pattern immediately and requires a documented root-cause finding before that pattern resumes.

### viii. Clearance lookups are live, not cached indefinitely

The design as written implicitly assumes a user's clearance tier is always current at query time. In practice, clearance changes — a demotion or role change, in particular — need to take effect immediately, not whenever a cache happens to expire.

**Hardening:** Clearance-tier lookups query the live identity/HR system directly at request time. Where caching is genuinely required for latency reasons, a maximum TTL is explicitly stated (5 minutes as a starting point) rather than left as an undefined implementation detail.

### ix. On-premise capacity overload defaults to refuse-and-escalate, stated explicitly

The original design never states what happens when the on-premise serving path (required for Restricted/Client-Confidential queries) is at capacity. Left unstated, this is exactly the kind of decision someone under delivery pressure could quietly resolve toward "queue indefinitely" or, worse, an unauthorized fallback to the vendor path.

**Hardening:** Under on-premise capacity overload, the explicit, stated default is refuse-and-escalate to a human compliance officer — never an indefinite queue, and never a fallback to the vendor-API path for content that isn't permitted to reach it. Capacity planning is sized against expected peak concurrent load for this path specifically, with headroom reviewed on the same cadence as the rest of production monitoring.

### x. Vendor model drift is a release gate, not an assumed non-event

Vendor-hosted LLMs update on the vendor's own schedule, and the current CI/CD process (Section VII.v) only gates changes to the internally-owned pipeline — it says nothing about the generation model itself changing underneath the system.

**Hardening:** A fixed set of canary queries with known-good answers is run against the vendor API on a defined schedule. Any detected drift in output — a canary answer that no longer matches its expected result within tolerance — triggers the same full re-validation gate required for an internal pipeline change, rather than assuming vendor-side updates are transparent or strictly improvements.

### xi. Adversarial validation is authored independently of the build team

The current design describes adversarial access-probing questions as "crafted by the security team," without specifying whether that team is independent of the team building the retrieval and generation pipeline — a meaningful distinction, since a team testing its own system's assumptions has an inherent blind spot for exactly the failure modes it didn't think to guard against.

**Hardening:** The adversarial validation subset is authored and maintained by a security function organizationally independent of the build team, with results reported to a stakeholder outside that build team as well — not folded silently into the same dashboard the building team already owns.

### xii. Retrieval quality is explicitly tested on domain-specific vocabulary

Compliance documents are dense with control IDs, legal boilerplate, and contract-specific terminology — exactly the kind of vocabulary a general-purpose embedding model is likeliest to handle poorly, and exactly the kind of degradation that wouldn't show up in an aggregate relevance metric until someone specifically checks for it.

**Hardening:** At least one general-purpose and one domain-adapted embedding option are evaluated before committing to dense retrieval. A stratified test slice specifically targeting control-ID lookups (e.g., "control A.9.2") and contract/legal terminology is added to the validation set from Section IV, evaluated separately from general query performance rather than averaged into it.
