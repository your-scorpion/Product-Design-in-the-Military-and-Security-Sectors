# Aegis Compliance Assistant — RAG System Design

## I. Problem definition

### i. Origin

Aegis Facilities Group's compliance and security-operations staff need fast, accurate answers about the company's own internal policies, procedures, and regulatory obligations — ISO 27001/27002 control implementations, the current Statement of Applicability, client-specific SLA terms, incident-response runbooks, and data-handling and classification rules. That material currently lives across roughly 2,000 documents: internally authored wiki pages (Markdown), scanned regulatory filings and signed client contracts (PDF/image), and a smaller set of structured control-mapping spreadsheets.

Today, getting an answer means either reading through the relevant document directly or tracking down whichever compliance officer happens to know the answer from memory — which doesn't scale, and leaves no record of what was actually asked or what the system said in response.

The stakes here are categorically different from an ordinary internal-wiki search tool. A technician told the wrong escalation path during an active incident, or an operator told the wrong data-retention period for a specific client's classification tier, isn't a minor inconvenience — it's a compliance violation or a delayed incident response with real contractual consequences. Every design decision in this system needs to be made with that asymmetry in mind: an unhelpful but safe answer is vastly preferable to a helpful but wrong one.

Every document in the corpus carries a classification tag — **Public**, **Internal**, **Restricted**, or **Client-Confidential** — and not every user is cleared to see every tier. Unlike a general document-sharing platform where every user can see every document, this system has to treat retrieval itself as a security boundary, not just a ranking problem.

### ii. Relevance & Reasons

**ii.i. Existing flow**

Today, a compliance question gets answered one of three ways: the asker searches the wiki's built-in keyword search (which only works if they already know roughly the right term), they ask a compliance officer directly (which doesn't scale past a handful of concurrent questions), or — worst case — they guess based on what they remember from onboarding, which is exactly the failure mode this system needs to close off.

None of these three paths leave any record of what was asked, what was answered, or whether the answer was actually correct — which matters enormously the first time an auditor asks "how did staff know what to do in this situation."

**ii.ii. Why this matters now**

A recent internal audit found three cases, across a six-month sample, where an operator's stated understanding of a data-handling requirement didn't match the actual current policy — in each case because they were recalling a policy version that had since been superseded. None of the three led to an actual client-facing incident, but each one represents exactly the failure mode this project exists to close: an operator confidently acting on a stale or incorrect understanding, with no built-in check against the current source of truth.

**ii.iii. Other reasons**

- If this works well for internal compliance staff, the same retrieval-and-citation approach could plausibly extend to helping account managers answer client questions about Aegis's own certifications and controls, which currently also routes through a compliance officer's inbox.
- A well-built citation-verification layer here is reusable infrastructure — the same "does this answer's claim actually match its cited source" check would be useful anywhere else in the company that ships an LLM-backed feature.

### iii. Expectations

Answers need to be:

- **Traceable.** Every answer must include a direct citation to the specific policy, clause, or control ID it drew from. An answer with no verifiable citation is not shown to the user at all — it's treated as a failure to answer, not a lower-confidence answer.
- **Current.** An answer must never be drawn from a superseded document version without the system knowing it's superseded. If policy X was revised last month, an answer can't quietly cite the old version.
- **Access-appropriate.** An answer must never surface content the asking user isn't cleared to see, regardless of how directly relevant that content might otherwise be.
- **Honest about its limits.** Where the system can't produce a fully-cited, high-confidence answer, it needs to say so and route the question to a human compliance officer — not attempt a more "helpful" hedge.

Use-case framing, following the same addressable/non-addressable split as a general RAG system, but with a category that doesn't exist in a flat-access system:

- **1a — Addressable, in-scope.** "What's our data retention requirement for Client-Confidential incident logs?" — answerable, with a citation, for a user cleared to see that tier.
- **2a — Addressable, requires escalation by design.** A question whose answer depends on a clause under active legal review, which the system should recognize and route to a human rather than answer confidently either way.
- **1na — Out of scope entirely.** General, non-compliance questions unrelated to the document corpus at all.
- **2na — In corpus, but the asking user isn't cleared to see it.** This is the case with no equivalent in a flat-access system: the answer exists, is well-supported, and simply isn't something this particular user should be shown. The correct response here isn't "I don't know" — it's a clear, honest "this needs a compliance officer with the right clearance," since silently returning nothing looks identical to the system being broken.

### iv. Previous work

- The existing wiki search is a plain keyword match with no ranking beyond term frequency — useful only when the asker already knows close to the exact wording used in the source document.
- No prior attempt has been made at anything resembling retrieval-augmented generation here; this is a genuinely new capability, not an upgrade to an existing pipeline.
- Worth checking early: does the compliance team already maintain an informal FAQ or internal knowledge base outside the wiki that should feed into this corpus, rather than being duplicated effort?

### v. Usage volumes and patterns

- Roughly 150 compliance and security-operations staff have a plausible need to use this — a far smaller and more specialized user base than a general company-wide tool, which affects how much we can lean on human-in-the-loop review of early answers before wider rollout.
- The document corpus is roughly 2,000 documents today, growing by an estimated 30–50 new or revised documents per month.
- Roughly 15% of the corpus exists only as scanned PDFs — older regulatory filings and signed contracts that predate the internal wiki.
- Classification-tier distribution across the corpus: roughly 20% Public, 45% Internal, 25% Restricted, 10% Client-Confidential.

### vi. Other details

- Existing documents live in a mix of the internal wiki platform and a document-management system for scanned/signed materials; neither currently exposes a unified API, which is itself a piece of integration work this project depends on.
- OCR is not currently implemented for the scanned portion of the corpus.
- Client-Confidential and a meaningful share of Restricted-tier documents are covered by contractual terms that prohibit sending their content to any third-party service, vendor LLM APIs included — a constraint that shapes the architecture far more than it would for a general-purpose document platform, and is addressed directly in Section III and Section VII.

Reviewing a design document is just as important as writing one. See the review checklist in [`design_doc_checklist.md`](../../../templates/design_doc_checklist.md).

## II. Metrics and losses

### i. Metrics

Retrieval and generation can be evaluated independently, the same as in any RAG system — but the metrics that matter most here are specific to what can go wrong in a compliance context, not general-purpose relevance.

**a. Retrieval metrics**

Recall@k and NDCG, evaluated the standard way against a set of expert-labeled query-to-relevant-chunk pairs — this part is genuinely generic RAG evaluation practice and doesn't need reinventing. What does need to be specific to this system is evaluating recall *within* the correct classification tier only, since a retrieval step that would have found the right answer in a tier the user isn't cleared for should score as a miss, not a hit, from that user's perspective.

**b. Citation Accuracy Rate**

For every answer that includes a citation, does the cited clause actually support the specific claim made? This is checked by comparing the generated claim against the cited source text, either through expert review on a sampled basis or through an automated entailment check as a faster proxy. An answer with a citation that doesn't actually support its claim is a more dangerous failure than an answer with no citation at all, since it looks trustworthy while being wrong — so this metric is tracked separately from, and weighted more heavily than, plain answer relevance.

```
citation_accuracy_rate = (answers where cited source verifiably supports the claim) / (total answers with a citation)
```

**c. Access-Violation Rate**

The percentage of answers that surface content — directly or by clear implication — from a classification tier the asking user isn't cleared for. Given the consequences, the target for this metric isn't "as low as reasonably achievable," it's as close to zero as testing can verify, and any confirmed violation during testing halts rollout for the affected query pattern until fixed.

```
access_violation_rate = (answers surfacing content outside the user's clearance) / (total answers)
```

**d. Staleness Rate**

The percentage of answers drawn from a document version that has since been superseded, without the system flagging that supersession. This is distinct from citation accuracy — an answer can correctly cite an old document and still be wrong, because the document itself is no longer the operative one.

```
staleness_rate = (answers drawn from a superseded version, unflagged) / (total answers citing a versioned document)
```

**e. Escalation Appropriateness**

Since a mandatory refuse-and-escalate path is a first-class requirement here (not a fallback of last resort), we need to measure both directions of getting this wrong: escalating when a confident, correct answer was actually available (an over-cautious system that becomes unusable), and *not* escalating when it should have. Both are tracked, with a manual review pass on a sample of escalations and a sample of non-escalations each cycle.

**i.ii. Metrics to pick**

Access-Violation Rate and Citation Accuracy Rate are the two headline safety metrics, reported before anything else in every model review. Recall@k and Staleness Rate are the two headline correctness metrics. Escalation Appropriateness is tracked as a usability check on top of the safety metrics above, since a system that's safe but escalates constantly isn't actually solving the underlying problem.

Online metrics of interest during eventual rollout: time-to-answer, escalation rate, and — the metric that most directly reflects whether this is actually working — the rate at which compliance officers report having to correct or override a system-provided answer after the fact.

### ii. Loss functions

The generation model itself is a vendor or self-hosted LLM used off-the-shelf (see Section VII), so there's no custom loss function to design for generation directly. Where custom training does apply is the entailment/citation-verification component described above — if built as a smaller fine-tuned classifier rather than relying purely on expert review, it would be trained as a binary classification task (does this cited passage support this claim), with a deliberately asymmetric loss that penalizes false "yes, this is supported" predictions more heavily than false "no" predictions, mirroring the same asymmetry the whole system is built around.

## III. Dataset

The atomic object is a (query, user, timestamp) tuple at inference time, and a (document, version, classification tier, chunk) tuple on the ingestion side.

### i. Data sources

#### Inner sources

1. **Internal policy wiki**, authored in Markdown, covering the majority of internally written procedures and control documentation.
2. **Scanned regulatory filings and signed client contracts**, PDF or image format, covering roughly 15% of the corpus and requiring OCR before anything else can happen to them.
3. **Structured control-mapping spreadsheets**, tying specific ISO 27001/27002 controls to the internal procedures that implement them and to the Statement of Applicability's stated justification for each control's inclusion or exclusion.
4. **Classification metadata**, attached to every document at the point it enters the corpus — this is not optional metadata the way a document's author might be; it's a hard gate the retrieval layer depends on.

#### Outer sources

Given the sensitivity of this corpus, outer/purchased data sources are deliberately limited. The one genuinely relevant outer source is publicly available ISO and NIST standard text itself, where a document references a specific numbered control — having the standard's own public language available as context helps the system correctly interpret what a cited control actually requires, without needing that public standard text to be classification-tagged at all, since it's public by definition.

### ii. Data labeling

Unlike a target label that already exists in historical data, there's no natural "ground truth" query set for this problem — it has to be built deliberately, covering every classification tier and a representative spread of question types, described further in Section IV.

### iii. Available metadata

#### Documents
- Document ID, title, classification tier, and version number
- Effective date and, where applicable, supersession date (the date a later version took over)
- Owning department (compliance, security operations, legal)
- Linked control ID(s), where the document maps to a specific ISO 27001/27002 control

#### Users
- Role and clearance tier — this is the piece of metadata that doesn't exist at all in a flat-access system, and it's the single most important join key in the entire retrieval pipeline

### iv. Data cleaning

Cleaning runs automatically on every new or revised document, the same as any ingestion pipeline — but classification tagging is treated as a required field with no default value, rather than an optional piece of metadata that falls back to some assumed-safe tier if missing. A document that arrives without an explicit classification tag is held out of the retrieval index entirely until a human assigns one, rather than being auto-classified or defaulted to "Internal" — an incorrect default in either direction (too permissive, or too restrictive) creates a real problem, and an unclassified document sitting outside the index is a far safer failure state than a misclassified one sitting inside it.

### v. Data chunking strategy

Documents are chunked at the paragraph level as the default granularity, consistent with how most compliance answers are actually scoped to a single clause or requirement rather than a whole document or a single sentence fragment. The detail specific to this system: **every chunk inherits its parent document's classification tier and version metadata directly**, and that inheritance is enforced at chunking time, not left to be reconstructed later at query time. A chunk with no classification tag attached to it is, by construction, not a valid object anywhere downstream in the pipeline.

### vi. Data enhancing

- **Control-ID linking.** Where a chunk's source document maps to a specific ISO 27001/27002 control, that control ID is attached directly to the chunk, so a query about "control A.9.2" or "access control policy" can be matched on either the explicit control ID or the semantic content, whichever the user's phrasing happens to use.
- **Supersession chain.** Every document version is linked to the version it superseded and, where applicable, the version that superseded it — this chain is what powers the staleness check in Section II, and is treated as required metadata rather than a nice-to-have.

### vii. Metadata handling and access enforcement

Classification tier is stored as a first-class, indexed field on every chunk in the vector database, not embedded only in the document's own text or a loosely-joined side table. At query time, the user's clearance tier is used as a **hard pre-filter** applied before similarity ranking runs at all — the system never ranks a Client-Confidential chunk against a Public-clearance user's query and then decides not to show it; that chunk is excluded from the candidate set before ranking begins. This is a meaningfully different retrieval architecture from a soft "prefer documents this user is likely to want to see" ranking signal, and the distinction matters: a ranking signal can, in principle, still be overridden or leak through an edge case; a hard pre-filter applied at the database query level cannot.

## IV. Validation schema

### i. Requirements

- New documents and revisions arrive on an ongoing basis, not in a fixed daily batch — a validation set needs periodic refresh rather than a one-time build.
- The validation set needs to be stratified by classification tier as well as by question type, since a model that performs well on Public-tier questions but poorly on Restricted-tier ones would look fine in an unstratified aggregate metric and be genuinely unsafe in practice.
- Access-violation testing specifically requires deliberately adversarial queries — questions phrased to try to surface Restricted or Client-Confidential content through indirect wording, asked by users with lower clearance — since a validation set built only from "normal" questions would never actually exercise the boundary this system most needs to hold.

### ii. Validation set construction

The validation set is built from three sources: compliance-officer-authored questions covering common real scenarios, auto-generated question/context/answer triplets (following the same general RAG-evaluation pattern of generating synthetic Q&A from source documents, then having a human review and correct them), and a deliberately adversarial set specifically targeting the access-control boundary — questions crafted by the security team to probe whether indirect phrasing can surface content that direct phrasing correctly withholds.

Stratification dimensions:

- Classification tier of the correct answer's source (Public / Internal / Restricted / Client-Confidential)
- Question type (direct factual, cross-document, "is this still current," and adversarial access-probing)
- Asking user's clearance tier, crossed against the correct answer's classification tier, so that both "correctly answered" and "correctly refused" cases are represented and measured separately

### iii. Update cadence

The validation set is refreshed monthly to absorb newly added or revised documents, with the adversarial access-probing subset reviewed and expanded quarterly by the security team specifically, since that's the part of the validation set most likely to go stale as new query patterns get discovered.

## V. Baseline solution

### i. Baseline retrieval

A sparse, keyword-based retrieval baseline (the same general BM25-style approach that's the sensible starting point for essentially any RAG system) gives us a fast, cheap, fully-explainable floor to beat — and critically, it's easy to apply the hard classification pre-filter to a sparse index just as directly as to a dense one, so building the access-control boundary correctly doesn't have to wait for the more complex embedding-based retrieval to be ready.

### ii. Baseline generation

The simplest complete version of the system: baseline retrieval, feed the top results into an off-the-shelf LLM prompt, require a citation in the output format, and reject (rather than show) any response that doesn't include one. No dense retrieval, no fine-tuned citation-verification model yet — just enough to validate that the mandatory-citation and hard-access-filter requirements are architecturally sound before investing in a more sophisticated retrieval layer on top.

### iii. Where complexity gets added, and why

The baseline above is deliberately not attempting semantic/dense retrieval, dialogue-aware context construction, or automated citation verification beyond format-checking. Each of those is a real improvement, but each also adds a place where the access-control guarantee could be weakened if it isn't threaded through carefully — so the sequencing here is: validate the hard access-filter and mandatory-citation architecture works correctly against the simplest possible retrieval and generation, and only then layer in dense retrieval and automated verification, re-validating the access-control guarantee at each step rather than assuming it carries over automatically.

## VI. Error analysis

### i. Where errors can enter the pipeline

- **Classification tagging.** An incorrectly tagged document is the single highest-leverage failure point in the entire system — every downstream guarantee depends on this being right, and no amount of retrieval or generation sophistication fixes a wrong classification tag upstream of it.
- **Retrieval.** A correct classification filter but a poor semantic match still produces a bad (though safe) answer — the failure here is unhelpfulness, not a safety violation.
- **Generation.** The model may state a claim not actually supported by the retrieved chunk, even when the retrieved chunk itself was correctly scoped and access-appropriate — this is exactly what the Citation Accuracy Rate metric exists to catch.
- **Citation verification.** If this component itself has false negatives (approving a citation that doesn't actually support the claim), it undermines the entire safety case the system rests on, which is why it's held to a stricter, separately-tracked metric rather than folded into general answer quality.

### ii. Diagnosing failures

Given the multi-stage pipeline, a failed test case gets walked backward through each stage individually: was the correct chunk retrieved at all (a retrieval failure), was it retrieved but filtered out for a wrong classification reason (a metadata failure), was it retrieved and passed the filter but the model didn't use it correctly (a generation failure), or did the model use it correctly but the citation-verification step wrongly rejected or wrongly approved the result (a verification failure). Each of the four gets its own isolated test harness rather than only being diagnosed through the end-to-end answer.

### iii. Segment-specific review

Every model version's error report breaks results down by classification tier specifically, in addition to the usual document-type and question-type breakdowns, since a model that performs acceptably in aggregate but poorly on Restricted-tier queries specifically represents exactly the kind of risk an aggregate number would hide.

## VII. Training pipeline

### i. Overview

As with most RAG systems, there's no custom-trained generation model here — the pipeline's job is stable ingestion, correct classification and chunking, and reliable citation verification, not model training in the traditional sense. The one place genuine model training may apply is the citation-verification component, if it moves from expert-review-only to a trained classifier over time.

### ii. Toolset

- Python as the core language.
- A vector database that supports metadata pre-filtering as a first-class query operation (not merely post-hoc filtering of an already-ranked result set) — this requirement rules out any vector-store option that treats metadata filtering as a lower-priority ranking signal rather than a hard gate, and needs to be validated explicitly during tool selection rather than assumed.
- **Two separate LLM serving paths**, driven directly by classification tier: Public and Internal-tier queries may be served by a vendor LLM API under an appropriate no-training data-use agreement; Restricted and Client-Confidential-tier queries must be served by a self-hosted, on-premise model, since contractual terms prohibit that content leaving company infrastructure at all. This is a materially different and more consequential build-vs-buy decision than a general-purpose RAG system typically faces, since it isn't optional or purely cost-driven — a meaningful share of queries architecturally cannot use the vendor path regardless of how well that path performs.
- Docker and a container orchestration layer for both serving paths.
- An OCR component for the scanned portion of the corpus, evaluated the same way any OCR tool would be (word error rate against a manually verified sample), since a misread word in a compliance document could easily change its meaning.

### iii. Data preprocessing

- OCR for scanned documents, with output quality checked before anything downstream trusts it.
- Chunking with mandatory classification and version inheritance, per Section III.
- Control-ID and supersession-chain linking, per Section III.

### iv. Routing logic

Every query is routed to the appropriate serving path based on which classification tiers its retrieved context set actually contains — not based on the user's clearance alone, since a Restricted-cleared user asking a purely Public-tier question shouldn't unnecessarily route through the more expensive, more constrained on-premise path. The routing decision happens after retrieval and filtering, not before.

### v. Continuous integration and deployment

Every pipeline change is validated against the full stratified validation set from Section IV before deployment, with the Access-Violation Rate and Citation Accuracy Rate specifically required to pass their thresholds — unlike most quality metrics, these two are treated as hard release gates rather than soft targets that can be waived under time pressure.

### vi. Monitoring and maintenance

Ongoing production monitoring is detailed in Section XI; the pipeline itself is responsible for surfacing pipeline-level failures (an ingestion job failing, a document arriving with no classification tag) as immediate alerts rather than silent skips.

## VIII. Features

Given that generation runs on an off-the-shelf LLM, "features" here mean the structured context assembled into the prompt, not features feeding a trained model.

Selection criteria:

1. **Traceability.** Every piece of context included in a prompt needs to be traceable back to a specific document, version, and classification tier — untraceable context isn't usable here regardless of how relevant it might be.
2. **Access-safety.** No feature or enrichment step is allowed to bypass the hard classification pre-filter established in Section III, even indirectly (e.g., a cross-document summary that blends content from multiple classification tiers without preserving which parts came from where).
3. **Currency.** Any feature drawing on a document's content needs to carry that document's effective-date and supersession status forward, so a stale document can't quietly feed a "current" answer.

Prompt components:

- **Role and task framing**, instructing the model explicitly that an uncited claim is not an acceptable output format, not merely a style preference.
- **Retrieved context**, each chunk carrying its source document ID, clause reference, classification tier, and effective date directly in the prompt, not just its raw text.
- **Supersession warnings**, where a retrieved chunk's document has a newer version, that fact is surfaced directly in the context rather than silently omitted, letting the model account for it rather than being unaware of it entirely.
- **Escalation instructions**, defining explicitly what "insufficient context to answer confidently" looks like and what the required fallback response is, rather than leaving that judgment call to the model's own discretion.

## IX. Measuring and reporting

### i. Measuring results

Initial offline testing against the stratified validation set shows the baseline (Section V) clears the Access-Violation Rate target cleanly, since the hard pre-filter is a deterministic database-level operation rather than a probabilistic one — which is itself worth noting as a design validation: the safety-critical part of this system doesn't depend on model quality at all, only on the filter being wired correctly, and that's a deliberate choice rather than an accident.

Citation Accuracy Rate on the baseline is meaningfully lower than the Access-Violation Rate result, as expected, since it depends on the LLM correctly grounding its claim in the provided context rather than on a deterministic filter — this is where most of the near-term improvement effort should concentrate.

### ii. Rollout approach

Given the compliance stakes, this system does not launch through a conventional randomized A/B test against the status quo. Instead, it launches in **shadow mode** first: for a defined period, every real question asked through the existing manual channels also gets run through the new system in parallel, with its answer logged but never shown to the asker. Compliance officers then review a sample of shadow answers against what they themselves would have said, which lets us measure real-world Citation Accuracy and Access-Violation performance without any risk of a bad answer actually reaching an operator.

Only once shadow-mode performance clears the same thresholds required for release (Section VII.v) does the system move to showing answers directly, starting with a single team rather than the full 150-person user base.

### iii. Reporting results

- Access-Violation Rate and Citation Accuracy Rate, reported first and separately from every other metric, with confidence intervals.
- Escalation rate and Escalation Appropriateness, to track whether the refuse-and-escalate path is calibrated sensibly rather than uselessly cautious or dangerously permissive.
- A qualitative summary of every confirmed access violation or citation failure found during the reporting period, regardless of how rare — given the stakes, these are reported individually, not folded silently into an aggregate rate.

## X. Integration

### i. Fallback strategies

- **Primary fallback:** if the citation-verification step can't confirm an answer's citation with sufficient confidence, the system returns a "this requires a compliance officer" response rather than the uncertain answer itself.
- **Secondary fallback:** if the on-premise LLM serving path (required for Restricted and Client-Confidential queries) is unavailable, those queries are routed directly to a human compliance officer rather than falling back to the vendor API path — under no circumstances does a Restricted or Client-Confidential query silently fail over to a serving path it isn't permitted to use.

### ii. API design

```http
POST /compliance-query
{
  "query": "<user question>",
  "user_id": "<requesting user>",
  "clearance_tier": "<Public|Internal|Restricted|Client-Confidential>"
}
```

```json
{
  "answer": "<generated answer, or null if escalated>",
  "citations": [
    {"document_id": "<id>", "clause": "<clause reference>", "effective_date": "<date>"}
  ],
  "escalated": "<true|false>",
  "escalation_reason": "<reason, if escalated>"
}
```

### iii. Non-engineering considerations

- A dashboard for the compliance team, showing escalated questions awaiting a human answer, and — critically — feeding any human-provided answer back into the system as a candidate future training or validation example, so escalations make the system better over time rather than being a dead end.
- An audit-log export, since the existence of a queryable record of every question asked and every answer given (or escalation triggered) is itself one of the project's original goals, not just an operational nicety.

## XI. Monitoring

### i. What's new here versus general infrastructure monitoring

Standard service health monitoring (latency, uptime, error rate) applies here the same as to any service and isn't the interesting part. What's specific to this system is monitoring the safety guarantees directly in production, continuously, not only at validation time.

### ii. Safety-critical monitoring

- **Live Access-Violation sampling.** A sample of production answers, across every clearance tier, is reviewed on an ongoing basis specifically for access-appropriateness — not just at the validation-set stage before launch, since production query patterns will inevitably include phrasings the validation set didn't anticipate.
- **Classification-tag completeness.** Any document that enters the ingestion pipeline without a valid classification tag triggers an immediate alert and is held out of the index, per Section III — this is monitored as a leading indicator, since an unclassified-document backlog quietly growing is exactly the kind of thing that looks fine until it very much isn't.
- **Supersession-chain gaps.** A document revision that arrives without a link back to the version it supersedes breaks the staleness check entirely for that document, and is flagged the same way a missing classification tag is.

### iii. Model-quality monitoring

Citation Accuracy Rate, tracked on an ongoing sampled basis in production the same way it's measured offline, since a model or prompt change elsewhere in the pipeline could degrade citation grounding without necessarily showing up in any other metric first.

### iv. Business metrics

Escalation rate, time-to-answer, and the rate at which a compliance officer reports having to correct a system-provided answer after the fact — tracked continuously, not only during the initial shadow-mode and pilot phases.

## XII. Serving and inference

### i. Serving architecture

Two parallel serving paths, as established in Section VII: a vendor-API path for Public/Internal-tier queries, and a self-hosted, on-premise path for Restricted/Client-Confidential-tier queries. Routing between them happens after retrieval and classification-filtering, based on the actual content of the retrieved context set for that specific query — not as a static, user-level assignment.

### ii. Infrastructure

The vendor-API path is a straightforward request/response integration with no special infrastructure of its own. The on-premise path requires dedicated GPU-backed serving infrastructure, sized for the smaller but non-trivial share of total query volume expected to require it (roughly a third of the corpus sits in the Restricted or Client-Confidential tiers, though not every query touches that content, so actual on-premise query volume should run meaningfully lower than that share).

### iii. Monitoring

Alongside standard latency and throughput metrics for both serving paths independently: the rate at which a query intended for the vendor path ends up needing to route to the on-premise path instead (a signal worth watching for its own sake, since a rising rate could indicate classification tags drifting toward over-caution, or genuinely reflect a growing share of sensitive content in the corpus) and the reverse case, checked just as carefully given the stakes of getting it wrong in that direction.
## XIII. Robustness Hardening — Adversarial Review Findings

*This section documents findings from a structured adversarial review of the design above, run specifically to stress-test the safety claims in Sections II, III, and IX rather than the system's general functionality. Each finding pairs a gap with a concrete mechanism — a design doc that says "we will ensure X" without saying how X is enforced hasn't actually specified X.*

### i. Classification-tag enforcement is a database constraint, not an application convention

Section III.iv states that an unclassified document is "held out of the retrieval index entirely." As written, this is an assertion about desired behavior, not a specified mechanism, and desired behavior that depends on every code path remembering to check for it eventually fails to check for it.

**Hardening:** The vector store's chunk schema defines `classification_tier` as a `NOT NULL` field with no default value, enforced at the database layer, not left to application-code discipline.

**Components:**
- Schema-level constraint: `classification_tier NOT NULL`, no default value.
- Write-path validation: any insert or update lacking the field fails at the database, not in application code that may or may not have remembered to check.
- Migration guard: any schema migration or bulk re-index operation requires a pre-migration integrity check — a count of chunks with a null or invalid classification tier — which must return zero before the migration is permitted to proceed.

### ii. Classification tagging happens at the section level for mixed-sensitivity documents

The original chunking strategy assumes one document carries one classification tier. This breaks the moment a single document contains sections of differing sensitivity — a Restricted appendix attached to an otherwise Internal-tier policy, for instance.

**Pros & Cons**

*Section-level tagging:*

Pros:
- Prevents a whole document from inheriting the tier of its single most (or least) sensitive section
- Matches how compliance documents are actually authored — a Restricted appendix on an Internal-tier policy is common, not an edge case

Cons:
- Meaningfully more ingestion effort per document, since a human has to review and confirm section boundaries rather than accept one document-level default
- Requires the chunking pipeline to respect section boundaries precisely, or a chunk could still straddle two differently-tagged sections

*Document-level-only tagging (permitted only after explicit human confirmation):*

Pros:
- Fast, low-effort ingestion for the — more common — case of a genuinely single-classification document

Cons:
- Entirely dependent on the confirmation step actually happening and being correct; a single missed confirmation reintroduces the exact leakage risk this hardening exists to close

**Hardening:** Any document exceeding three pages, or explicitly flagged as mixed-sensitivity at ingestion, requires section-level tagging. Document-level-only tagging requires a human to explicitly confirm, at ingestion, that the document is genuinely single-classification throughout — a logged, required step, not a default assumption.

### iii. Retrieved content is treated as untrusted input to the generation prompt

Every chunk fed into the LLM's context originates from a document some human authored, edited, or scanned — any of which is a plausible vector for text attempting to manipulate the model's behavior. The current design draws no distinction between "information to answer from" and "instructions to obey."

**Hardening steps:**

1. Retrieved chunks are wrapped as clearly-delimited, labeled data in the prompt, with explicit framing that content inside this boundary is reference material, not instructions.
2. The generation step includes an output-side check for the specific pattern of an answer appearing to follow a directive found within retrieved content rather than the user's actual question.
3. The adversarial validation subset (Section IV.ii) is expanded to include documents with deliberately embedded injection attempts, to test this defense rather than assume it holds.

### iv. Session-level cumulative-disclosure tracking, alongside per-query access control

Per-query classification filtering is necessary but not sufficient: a sequence of individually-permitted answers can, taken together, reconstruct information the asker isn't cleared to see as a whole — a well-known aggregation/inference problem in access-controlled systems generally.

**Objectives:**
- Detect, without full aggregation-modeling (a harder, longer-term research problem in its own right), the specific pattern of many narrowly-related queries clustered within one session.
- Avoid blocking ordinary multi-question sessions that simply cover a broad range of unrelated topics, which should never trip this check.

**Hardening:** Sessions are monitored for a high volume of related queries clustered on a narrow topic, with a defined threshold that triggers a hold for human review rather than continued automatic answering. Query clusters are logged for periodic security review independent of the per-query safety metrics.

*TODO: the cumulative-disclosure threshold — how many related queries on a narrow topic triggers a hold — needs to be set empirically once real session-level query data exists. An initial conservative value should be chosen for shadow mode and revisited after the first full review cycle.*

### v. The citation-verification component is validated against its own held-out test set

Citation Accuracy Rate depends on an "automated entailment check" the original design treats as a settled proxy. If that component has its own false-negative rate, the entire safety case built on this metric rests on unaudited software.

**Pros & Cons, by verification method:**

*Automated entailment check, as the primary method:*

Pros:
- Scales to full production volume without a compliance officer reviewing every single answer
- Applies the same standard consistently across every checked citation

Cons:
- Only as reliable as its own validation (exactly why this finding requires a dedicated held-out test set for the checker itself)
- May miss subtle cases where wording is technically supported but meaningfully misleading in context — a case a human reviewer is likelier to catch

*Expert-only review, as the primary method:*

Pros:
- Higher per-case reliability, particularly on subtle or context-dependent claims

Cons:
- Doesn't scale to full production volume; realistically usable only for the sampled review described in finding vi, not every generated answer

**Hardening:** The entailment/citation-verification component is validated against its own dedicated test set, built to include deliberately subtle "sounds correct but doesn't actually follow" cases, not only clearly-correct and clearly-wrong pairs. Its false-negative rate is tracked as a distinct, first-class metric, reviewed quarterly by someone independent of the team that built it.

### vi. Shadow-mode review runs to a specified protocol

Section IX.ii describes shadow-mode review as "compliance officers review a sample of shadow answers," specifying neither sample size, cadence, nor reviewer count.

**Terminology**

- **Review cycle:** one full pass through that week's shadow-mode answers by the assigned reviewers.
- **Double-review:** two independent compliance officers each reviewing the same answer without seeing the other's judgment first.
- **Confirmed finding:** an access-violation or citation-inaccuracy result both reviewers agree on (for double-reviewed answers), or a single reviewer's finding for sampled-only answers.

**Effort estimate**

```
weekly_review_hours = (restricted_plus_answers × 2 reviewers × avg_minutes_per_review / 60)
                     + (public_internal_answers × 0.10 sample_rate × 1 reviewer × avg_minutes_per_review / 60)
```

This should be calculated against actual shadow-period volume once available, rather than estimated in the abstract.

**Hardening:** 100% of shadow answers touching Restricted or Client-Confidential content are reviewed, by two independent compliance officers each, with any disagreement between reviewers treated as a finding in its own right rather than resolved by simple majority. A 10% random sample of Public/Internal-tier shadow answers is reviewed on the same weekly cadence. The system does not exit shadow mode until a defined number of consecutive clean review cycles has passed.

### vii. Access-Violation Rate has a stated, falsifiable target

"As close to zero as testing can verify" cannot be tested against, since it isn't a number.

```
access_violation_rate = confirmed_violations / total_reviewed_answers
```

Pass/fail threshold: `confirmed_violations = 0` across the minimum 2,000-answer quarterly sample. Unlike most quality metrics on this project, there is no "acceptable" nonzero value here — any confirmed violation is a hard fail.

**Hardening:** The stated target is zero confirmed access violations across a minimum of 2,000 stratified production answers per quarter, with Restricted and Client-Confidential-tier retrievals reviewed at 100%, not sampled, given both their rarity and their stakes. Any single confirmed violation halts serving for the affected query pattern immediately and requires a documented root-cause finding before that pattern resumes.

### viii. Clearance lookups are live, not cached indefinitely

The design implicitly assumes a user's clearance tier is always current at query time. In practice, clearance changes — a demotion or role change, in particular — need to take effect immediately.

```
max_staleness_window = cache_ttl + identity_system_lookup_latency
```

This window is the true worst-case upper bound on how long a revoked clearance could remain effective, and needs to be reviewed against whatever staleness tolerance the security team actually finds acceptable, not assumed acceptable by default.

**Hardening:** Clearance-tier lookups query the live identity/HR system directly at request time. Where caching is genuinely required for latency reasons, a maximum TTL is stated explicitly rather than left undefined.

*TODO: confirm a working cache TTL figure (5 minutes as a starting point) against the identity system's actual latency budget — this may need to be tighter for Restricted/Client-Confidential-tier lookups specifically.*

### ix. On-premise capacity overload defaults to refuse-and-escalate

The original design never states what happens when the on-premise serving path (required for Restricted/Client-Confidential queries) is at capacity — left unstated, this is exactly the kind of decision someone under delivery pressure could quietly resolve toward an indefinite queue, or worse, an unauthorized fallback to the vendor path.

**Algorithm**

Input: Query requiring on-premise serving (Restricted or Client-Confidential tier)
Output: Generated answer, or escalation to a human compliance officer

1. Capacity Check
   1.1 `available_capacity` ← check current on-premise serving load
   1.2 If `available_capacity` is sufficient:
       1.2.1 Proceed to standard retrieval and generation (Section XII.i)
   1.3 Else:
       1.3.1 `queue_wait_estimate` ← estimate time to available capacity
       1.3.2 If `queue_wait_estimate` exceeds the defined maximum wait (e.g., 30 seconds):
             1.3.2.1 Return escalation to a human compliance officer
             1.3.2.2 Log the overload event for capacity-planning review
       1.3.3 Else:
             1.3.3.1 Queue the request up to the defined maximum wait
             1.3.3.2 If capacity becomes available within that window: proceed to 1.2.1
             1.3.3.3 Else: proceed to 1.3.2.1

End Algorithm

Note: under no circumstances does step 1.3 fail over to the vendor-API serving path — a Restricted or Client-Confidential query that cannot be served on-premise is escalated, never rerouted to a path it isn't contractually permitted to use.

### x. Vendor model drift is a release gate

Vendor-hosted LLMs update on the vendor's own schedule. The current CI/CD process (Section VII.v) only gates changes to the internally-owned pipeline.

```
canary_drift_rate = mismatched_canary_answers / total_canary_answers
```

Any `canary_drift_rate` above a defined tolerance (to be set once a canary baseline exists — see TODO below) triggers the full re-validation gate described below, the same as an internal pipeline change would.

**Hardening:** A fixed set of canary queries with known-good answers is run against the vendor API on a defined schedule. Any detected drift — a canary answer that no longer matches its expected result within tolerance — triggers the same full re-validation gate required for an internal pipeline change.

*TODO: define the exact canary-query set size and refresh cadence, and the drift-rate tolerance above which the gate fires. Too small a set risks missing a real drift; too large adds unnecessary latency and cost to every scheduled check.*

### xi. Adversarial validation is authored independently of the build team

The current design describes adversarial access-probing questions as "crafted by the security team," without specifying whether that team is independent of the team building the retrieval and generation pipeline — a team testing its own assumptions has an inherent blind spot for exactly the failure modes it didn't think to guard against.

**Applicability:** This independence requirement applies specifically to the adversarial access-probing subset of the validation set (Section IV.ii) — not the full validation set. Expert-authored and auto-generated question sets can still originate from the build team, since their purpose is coverage of ordinary use cases, not adversarial pressure-testing of the access-control boundary itself.

**Hardening:** The adversarial validation subset is authored and maintained by a security function organizationally independent of the build team, with results reported to a stakeholder outside that build team as well.

### xii. Retrieval quality is explicitly tested on domain-specific vocabulary

Compliance documents are dense with control IDs, legal boilerplate, and contract-specific terminology — exactly the kind of vocabulary a general-purpose embedding model is likeliest to handle poorly.

| Aspect | General-purpose embedding | Domain-adapted embedding |
| --- | --- | --- |
| Setup effort | Low — off-the-shelf, no additional training | Higher — requires fine-tuning or retrieval-augmented prompting on compliance vocabulary |
| Control-ID / legal-term retrieval quality | Unverified, likely weaker against this specific vocabulary | Expected stronger, but must be verified against the stratified test slice below, not assumed |
| Maintenance | Vendor-maintained, updates transparently | Requires ongoing maintenance as vocabulary or document types expand |
| Cost | Lower, standard per-token vendor pricing | Higher — training/fine-tuning cost on top of standard usage |

**Hardening:** At least one general-purpose and one domain-adapted embedding option are evaluated before committing to dense retrieval. A stratified test slice specifically targeting control-ID lookups (e.g., "control A.9.2") and contract/legal terminology is added to the validation set from Section IV, evaluated separately from general query performance rather than averaged into it.
