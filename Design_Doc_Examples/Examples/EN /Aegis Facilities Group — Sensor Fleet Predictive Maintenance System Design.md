# Aegis Facilities Group — Sensor Fleet Predictive Maintenance System Design

## I. Problem definition

### i. Origin

Aegis Facilities Group provides physical-security monitoring services to several thousand client sites — hospitals, data centers, industrial plants, ports, and government buildings — under long-term service contracts. Each site runs a fleet of sensor hardware: motion detectors, door and window contact sensors, glass-break acoustic sensors, and battery-backed alarm panels. A typical mid-sized site runs around 200 such devices.

These devices fail, and the failure mode that matters most isn't the loud, obvious kind — it's the quiet kind. A battery drains past its usable threshold. A motion sensor's calibration drifts until it stops reliably triggering. A door contact's reed switch corrodes and sticks. None of these announce themselves. The device keeps sending a heartbeat ping — "I'm alive" — right up until the moment it's actually needed and doesn't fire.

For a rough sense of scale: across roughly 3,000 client sites at 200 devices each, and an estimated annual per-device failure rate of about 4%, that works out to somewhere in the neighborhood of 24,000 device-level failure events fleet-wide per year. The question this project exists to answer is when Aegis actually finds out about each one.

1. **Best case.** Telemetry-based monitoring flags a device as degrading, a technician is dispatched, and the device is serviced or replaced within days — before it goes fully dark. The blind-spot window is short.
2. **Bad case.** The device fails silently and isn't caught until the next scheduled quarterly walkthrough — up to 90 days later. The site has been running with an undetected gap in coverage for up to three months.
3. **Worst case.** The device fails silently, isn't caught at scheduled inspection either (because it happens to fail shortly after one), and the first anyone learns of it is during an actual security incident, when the sensor should have triggered and didn't. This is the scenario the whole project exists to prevent.

A complicating constraint: a meaningful share of the fleet — glass-break sensors and a portion of the door-contact units — ship as sealed, battery-powered devices with no field-serviceable battery. For those, "maintenance" doesn't mean repair; it means proactively scheduling replacement before end-of-life, which means the prediction problem is really two related problems: *is this device already degrading* and *when will this device's battery run out*.

The project goal is to predict device-level failure risk far enough ahead of the actual failure that a technician visit can be scheduled proactively, converting the current model — discover failures reactively, on a fixed quarterly cadence or by accident — into a proactive one, with a defined service-level target for how much warning the system needs to give.

### ii. Relevance & Reasons

*This section lays out why the problem is worth solving, grounded in what we already know about current losses.*

**ii.i. Existing flow analysis**

What actually happens today, end to end?

1. **Contract structure with clients:**
   - Most site contracts run three years, with an annual service-level review.
2. **Device fleet composition:**
   - Roughly six device families in active service, spanning four hardware generations; older generations are being phased out but remain in service at a meaningful share of sites for years after a newer generation ships.
3. **Current monitoring granularity:**
   - Every device sends a basic heartbeat ping once every 24 hours. A missed heartbeat triggers a ticket. Nothing about *degrading* performance — battery trend, response-time drift, self-test results — is currently analyzed; it's collected in raw form by some device generations but never looked at.
4. **Inspection cadence:**
   - Scheduled technician walkthroughs happen quarterly per site, checking every device by hand.
5. **Who owns the decision to dispatch a technician outside the regular schedule:**
   - Currently, an off-cycle dispatch only happens in response to a missed-heartbeat ticket or a client-reported problem. There is no proactive dispatch process today.
6. **Forecast horizon needed:**
   - The useful prediction horizon is roughly 7–30 days out — enough lead time to schedule a routine (not emergency) technician visit, since emergency dispatch costs meaningfully more than routine dispatch.
7. **Business owners of this process:**
   - Field operations (technician scheduling and dispatch).
   - Client success (owns the SLA relationship and any penalty exposure).
   - Engineering (owns the device telemetry pipeline).

**ii.ii. What does the current blind-spot cost Aegis?**

The clearest cost to quantify is emergency-dispatch premium: an emergency technician visit (same-day, outside the routine schedule) costs noticeably more than a routine one, largely due to overtime rates and route disruption. The harder cost to quantify is the one that actually matters most: contract penalty exposure when a device failure contributes to (or is discovered during) an actual incident at a client site, since a meaningful share of contracts carry an uptime guarantee with a defined penalty clause.

Using dispatch cost data plus a conservative estimate of penalty-clause exposure from the incidents where a device was later found to have been silently failed at the time, **an initial rough estimate puts total annual exposure at around $40M** across the fleet. This number should be treated as directional rather than precise — it's built from a small number of known incidents extrapolated across the full fleet, and refining it is itself one of the first things this project should do.

**ii.iii. Other reasons**

- Could the same telemetry pipeline and modeling approach extend to other device families Aegis monitors — fire-safety sensors, environmental monitoring units — without much additional engineering?
- Is there a version of this system worth offering as a value-add to the facility-management subcontractors Aegis works with at some sites, rather than keeping it purely internal?

### iii. Previous work

*Has anything like this been tried, and what would repeating it look like?*

- Has anyone already tried to get ahead of this with heuristics? Field technicians already informally flag "this battery type tends to die around month 18" for certain device generations — is that pattern actually reliable, or just folklore that survives because nobody's checked it against the data?
- What device generations already have enough historical telemetry to make a model worth building, versus generations too new or too sparse in the data to support one yet?
- Is a hybrid rollout the right call — start with the device families and sites where the current blind-spot problem is worst, rather than trying to cover the whole fleet on day one?
- If an early version of this system gets a false positive wrong (flags a healthy device), the downside is a wasted routine visit — not dangerous, just costly. That asymmetry matters for how cautiously we can afford to roll this out.
- None of this replaces a fresh exploratory pass over the existing heartbeat and ticket data before committing to anything.

### iv. Other issues & risks

- The telemetry pipeline needed to actually collect and centralize the richer signal (battery voltage trend, self-test response latency, temperature exposure) doesn't fully exist yet for every device generation — some of the oldest hardware may need a firmware update just to report the data this system would need.
- What's the fallback if the model's confidence is low, or a device's telemetry feed is incomplete? A system that goes silent exactly when it's least confident is worse than no system at all.
- How much of the current blind-spot problem is genuinely predictable from device telemetry, versus driven by things telemetry can't see at all (installation quality, physical tampering, environmental damage)?
- The cost of a missed failure and the cost of a false alarm are not remotely symmetric, and any metric chosen later needs to reflect that directly rather than treating both error types as equally bad.
- What prediction granularity does field operations actually need to act on this — a daily risk score per device, or an alert only when risk crosses some threshold?

Reviewing a design document is just as important as writing one. See the review checklist in [`design_doc_checklist.md`](../../../templates/design_doc_checklist.md) for practical guidance on reviewing a doc like this one.

## II. Metrics and losses

### i. Metrics

This is fundamentally a rare-event prediction problem: even at a 4% annual per-device failure rate, on any given day the overwhelming majority of devices are healthy. That imbalance rules out some metrics before we even get to the harder question of what the business actually cares about.

**a. Why plain accuracy is the wrong starting point**

A model that predicts "healthy" for every device would be right well over 99% of the time on any given day and would be worthless. Any metric that doesn't account for the class imbalance directly is misleading here.

**b. Precision, Recall, and the tradeoff between them**

Precision tells us, of everything we flagged as at-risk, how much genuinely was. Recall tells us, of everything that genuinely failed, how much we caught in advance. Given the cost asymmetry described above — a missed failure risks an SLA penalty and a genuine security gap, while a false alarm only costs a routine technician visit that would likely have happened eventually anyway — recall matters considerably more to us than precision, though driving precision too low would create its own problem: field technicians stop trusting the system's flags if most of them turn out to be nothing.

**c. Why PR-AUC over ROC-AUC**

Under severe class imbalance, ROC-AUC can look deceptively good even for a fairly weak model, because the true-negative count is so large it drowns out the false-positive rate in the calculation. Precision-Recall AUC doesn't have that blind spot, since it's built entirely from the minority class's perspective, and it's the more honest summary statistic for this kind of problem.

**d. A cost-weighted metric specific to our business**

None of the above captures the actual dollar cost of getting this wrong, so alongside precision/recall/PR-AUC we'll define a direct expected-cost function:

```
Expected Cost = (C_missed × False Negatives) + (C_false_alarm × False Positives)
```

where `C_missed` is the estimated cost of an undetected failure (weighted average of emergency-dispatch premium and expected SLA-penalty exposure) and `C_false_alarm` is simply the cost of a routine technician visit that turns out to be unnecessary. Early estimates put `C_missed` at roughly 15–20× `C_false_alarm`, which should directly inform the classification threshold we choose rather than defaulting to the standard 0.5 cutoff.

**e. Detection lead time**

None of the metrics above capture *how much warning* we actually gave. A model that correctly flags a device one day before failure is far less useful operationally than one that flags it two weeks out, even if both count identically under precision/recall. We'll track the distribution of lead time — days between first flag and actual failure — as a first-class metric, not an afterthought, since the whole point of this project is buying field operations enough notice to schedule a routine (not emergency) visit.

**i.ii. Metrics to pick**

Precision, Recall, and PR-AUC as the primary offline metrics, reported per device family and per site environmental class (indoor / outdoor / coastal — since corrosion and temperature swings affect device families very differently). The expected-cost function above will drive threshold selection specifically, rather than being reported as a separate abstract number nobody acts on. Lead-time distribution will be reported alongside every model version as a required, not optional, chart.

Online metrics of interest during the eventual A/B rollout:

- SLA breach rate (expected to decrease)
- Emergency (non-routine) dispatch rate (expected to decrease)
- Mean detection lead time for confirmed failures (expected to increase)

### ii. Loss functions

Standard binary cross-entropy is the natural starting loss for the classifier, but given the cost asymmetry above, we'll also weight the loss directly — assigning a higher penalty to false negatives during training, roughly proportional to the `C_missed`/`C_false_alarm` ratio established above, rather than tuning only the decision threshold after the fact. We'll treat the exact weighting as a hyperparameter to sweep rather than fixing it from the initial cost estimate alone, since that estimate itself carries real uncertainty.

As a second line of experimentation, since this is fundamentally a time-to-event problem rather than a plain classification problem, we'll evaluate a survival-analysis framing (a Cox proportional hazards model) alongside the classifier. A hazard-based model naturally outputs something closer to what field operations actually wants — an estimated time until failure, with a confidence band — rather than a binary flag, and may be more sample-efficient given how few labeled failure events we currently have.

## III. Dataset

The atomic object is a bundle of (date, device_id, site_id), and the target is whether that device fails within the next N days (N to be fixed based on the validation results in Section IV, with 14 days as the working assumption).

### i. Data sources

#### Inner sources

1. **Device telemetry.** Battery voltage, self-test response latency, signal strength to the site's gateway, and (for newer device generations only) an onboard temperature reading. This is our primary predictive signal, and its completeness varies significantly by hardware generation — a real constraint we'll need to design around rather than assume away.
2. **Maintenance ticket history.** Technician-logged service records, including the failure events that form our target label. Ticket categorization was inconsistent and largely free-text before a structured taxonomy was introduced roughly 18 months ago, which limits how far back we can reliably pull clean labels.
3. **Device and site metadata.** Hardware generation, firmware version, install date, device family, site environmental classification, and the client's contracted SLA tier.

#### Outer sources: purchased or gathered data

1. **Local weather history**, for outdoor-installed device families, since heat and humidity cycling measurably accelerates battery degradation in sealed units.
2. **Manufacturer errata and recall bulletins**, since a firmware defect or a bad battery batch can produce a failure spike that has nothing to do with normal wear and everything to do with a specific manufacturing run.

### ii. Data labeling

The target label — did this device fail within the next N days — comes directly from the maintenance ticket history described above, once filtered down to tickets that were coded as an actual hardware failure rather than a false-alarm callout, a scheduled replacement unrelated to degradation, or a client-requested relocation.

### iii. Available metadata

#### Devices
- Device ID, hardware generation, and device family (motion, door-contact, glass-break, panel)
- Install date and, where known, battery installation or replacement date
- Firmware version
- Whether the unit is field-serviceable or sealed

#### Sites
- Site ID and environmental classification (indoor / outdoor / coastal / high-dust industrial)
- Client SLA tier, since higher tiers carry larger penalty exposure and may warrant a more conservative (higher-recall) threshold
- Site criticality classification (hospital and data-center sites are weighted differently than, say, a warehouse)

#### Tickets
- Timestamp, device ID, site ID, and structured failure category (post-taxonomy only)

### iv. Available history

The telemetry pipeline is roughly 18 months old — considerably shorter than we'd like, and a real constraint on how much we can lean on pure historical-pattern learning versus device-physics-informed features (e.g., known battery discharge curves for a given chemistry, rather than only "what this exact model has done historically"). Ticket history goes back further, but only the post-taxonomy portion (the last ~18 months) is reliably clean enough to use as labels without significant manual re-coding.

### v. Data quality issues

- Telemetry from the oldest device generation is intermittent, since that generation's firmware wasn't originally built with this kind of reporting in mind — some fields are simply absent for those units.
- Pre-taxonomy tickets (anything older than ~18 months) require manual re-coding before they can be trusted as labels, and we don't plan to invest in that for the initial version.
- Weather data coverage is good for the large majority of outdoor sites, but has gaps for a small number of remote installations.

### vi. Final ETL pipeline

1. Telemetry is aggregated daily per device.
2. New daily partitions are appended to the aggregate table; the previous 2 days are also recomputed to absorb telemetry that arrived late.
3. Device and site metadata are joined in.
4. Ticket-derived labels are joined in with an appropriate lag, since a ticket is only closed (and thus labeled) some time after the underlying failure actually began.
5. Features are computed on the joined dataset.

## IV. Validation schema

### i. Requirements

- New telemetry arrives daily, but can lag by up to 72 hours for devices on weaker cellular coverage.
- Failure labels lag further still, since a ticket is only closed once a technician has physically confirmed the issue — sometimes days after the underlying degradation began.
- The device fleet's composition changes over time as new hardware generations roll out and old ones are retired, meaning the population a model is trained on will not exactly match the population it's later scored against.
- Because failures are rare, any validation split needs to guarantee a minimum number of positive (failure) examples in every fold — an ordinary fixed-size rolling window, sized the way it might be for a high-volume forecasting problem, risks landing folds with too few failures to evaluate meaningfully.

### ii. Inference

Once a model configuration is fixed, we train on the full available history (currently ~18 months) and predict failure risk over the next 14 days. As with the labeling lag above, there needs to be a buffer between the end of the training window and the start of the validation window — 5 days in this case, reflecting the realistic delay between a device beginning to degrade and that degradation being confirmed and logged.

### iii. Inner and outer loops

Given how sparse positive examples are, we can't simply mirror a fixed weekly-step rolling window the way a high-frequency retail-forecasting problem might. Instead, the outer loop uses expanding validation windows sized dynamically to guarantee a minimum of roughly 50 confirmed failure events per fold — meaning fold length will vary depending on how failure-dense that period happens to be, rather than being fixed at a constant number of days. We use K=4 outer folds given the 18-month history currently available.

The inner loop, used for hyperparameter tuning within each outer training set, follows the same dynamically-sized-window logic at a smaller scale, requiring a minimum of roughly 15 failure events per inner fold.

If a given device family simply doesn't have enough historical failures yet to support this validation approach at all, we fall back to the rule-based baseline (Section V) for that family rather than deploying an undertrained model.

### iv. Update frequency

Given how new this telemetry pipeline is, we'll re-run the full validation and retrain monthly, rather than weekly, until we have enough history that monthly retraining stops meaningfully changing the model — at which point we'll reassess the right cadence. A separate quarterly holdout set will track long-term drift independent of the retraining cycle.

## V. Baseline solution

### i. Rule-based baseline

Field technicians already work from informal thresholds — "flag if battery voltage drops below X," "flag if self-test response time exceeds Y milliseconds." We'll formalize these into an explicit rule-based baseline rather than treating them as folklore to be discarded, since codifying them costs almost nothing and gives us an honest floor to beat.

### ii. Survival-analysis baseline

Since this is fundamentally a time-to-failure problem, a Kaplan-Meier survival curve per device family gives us a baseline hazard estimate — what fraction of devices of this type, this age, have historically survived to this point — without needing any of the richer telemetry features at all. A Cox proportional hazards model, layered on top, lets us bring in a small number of covariates (device age, environmental class) cheaply before committing to a full machine-learning pipeline.

### iii. Machine-learning baseline

Gradient-boosted trees on the engineered telemetry features described in Section VIII, trained with the cost-weighted loss described in Section II. Given the data is tabular and moderately sized rather than the kind of high-volume sequential data that favors deep learning, gradient boosting is the natural first serious model here rather than a stretch goal.

### iv. Feature baselines

Static device and site attributes (hardware generation, environmental class, SLA tier), device age in days, and days since last service ticket, all available immediately with no telemetry-specific engineering required — a useful check on how much the richer telemetry features in Section VIII actually add over what we already know about a device without looking at its live signal at all.

## VI. Error analysis

### i. Learning curve analysis

**Convergence.** Once we move past the rule-based and survival baselines into gradient boosting, we'll track the loss curve by iteration count to confirm the model is actually converging and not simply overfitting the (currently limited) set of positive examples.

**Model complexity.** With only 18 months of telemetry and a fairly small number of confirmed failures to learn from, model complexity needs to be tuned conservatively — we'd rather under-fit slightly than over-fit to noise in a dataset this constrained by its short history. We'll use a model-wise learning curve (varying tree depth and the number of trailing days used for rolling telemetry features) to find the point past which additional complexity stops earning its keep on the validation folds.

**Dataset size.** A sample-wise learning curve will tell us whether we're already near a plateau given our current history, or whether the model is still meaningfully data-starved — which would argue for prioritizing telemetry-pipeline coverage on older device generations over further modeling effort in the near term.

### ii. Error analysis by segment

Given the cost asymmetry established in Section II, a missed failure (false negative) is categorically worse than a false alarm (false positive), and error analysis needs to reflect that rather than treating the confusion matrix as symmetric. We'll break the confusion matrix down by:

- **Device family** — do glass-break sensors fail more predictably than door contacts?
- **Hardware generation** — older generations with sparser telemetry are the likeliest source of missed failures, and that's useful to know explicitly rather than only as an aggregate number.
- **Site environmental class** — coastal, high-corrosion sites may need a lower alert threshold than climate-controlled indoor sites, given the physically faster degradation curve.
- **Firmware version cohort** — a firmware bug affecting a specific version could look, statistically, exactly like a hardware failure signal if we're not careful to separate the two.

### iii. Best-case, worst-case, and corner-case reporting

With every model version, we'll surface: which devices the model is most confidently (and correctly) flagging, which failures it missed entirely and what those devices had in common, and which device categories have too little data for the model to say anything confident about at all — the last of those being exactly where the rule-based fallback should keep doing the work instead.

## VII. Training pipeline

### i. Overview

The pipeline needs to ingest daily telemetry, join it against metadata and lagged failure labels, engineer features, train and validate the classifier and survival model in parallel, and score the full active device fleet on a recurring basis.

### ii. Toolset

- Python as the core language.
- A streaming layer (Kafka, feeding a small stream-processing job) for near-real-time telemetry ingestion, since device health signals matter more the sooner they're available — this differs meaningfully from a purely batch-oriented pipeline, since we want the option of same-day alerting for a device that crosses an obvious hard threshold, layered on top of the daily-batch risk score.
- XGBoost or LightGBM for the primary gradient-boosted classifier, given the tabular nature of the data.
- A survival-analysis library (e.g. `lifelines`) for the Cox proportional hazards baseline and ongoing comparison.
- MLflow for experiment tracking.
- Docker for reproducibility, with cloud-based training (a managed ML platform) for the periodic retraining job.

### iii. Data preprocessing

- Cleaning: handling telemetry gaps (particularly from older hardware generations), deduplicating tickets, and resolving pre- vs. post-taxonomy label inconsistency.
- Feature engineering: rolling statistics over telemetry (trend, not just point-in-time value), device age, days since last service.
- Normalization: scaling telemetry features appropriately given they arrive on very different natural scales (millivolts vs. milliseconds vs. degrees).
- Train/validation split: per the dynamically-sized, failure-count-guaranteeing windows described in Section IV, never allowing future information to leak backward.

### iv. Model training

- Rule-based and survival baselines, per Section V.
- Gradient-boosted classifier as the primary model, trained with the cost-weighted loss from Section II.
- Hyperparameter search via the inner validation loop, focused on tree depth, minimum leaf size, and the false-negative cost-weighting factor.

### v. Model evaluation

Precision, Recall, PR-AUC, the expected-cost function, and the lead-time distribution, all reported per the segments identified in Section VI, not only in aggregate.

### vi. Experiment tracking and model management

MLflow tracks model parameters, feature sets, evaluation metrics per segment, and the serialized model artifact for every training run, alongside the survival-model comparison run in parallel.

### vii. Continuous integration and deployment

Retraining runs monthly (per Section IV), triggered on a schedule rather than ad hoc, with automated evaluation against the holdout set before any new model version is promoted.

### viii. Monitoring and maintenance

Production model performance and telemetry-pipeline health are both monitored on an ongoing basis (see Section XI), with retraining or rollback triggered automatically if performance on recent confirmed failures drops meaningfully below the holdout benchmark.

### ix. Future work

As telemetry history lengthens past its current 18 months, we expect the case for a sequence model (an LSTM or similar, trained directly on raw telemetry sequences rather than hand-engineered rolling features) to strengthen — but building that now, on this little history, would likely just overfit.

## VIII. Features

Selection criteria, in order of priority:

1. **Prediction quality.**
2. **Interpretability.** Field operations and client-success teams need to trust and act on a flag, which means we prefer features a non-data-scientist can reason about over an opaque embedding.
3. **Computation time.** Given the daily batch-scoring requirement across the full fleet, features with heavy computational cost need to earn their place.
4. **Stability and risk.** Features dependent on a single external data source (weather, in particular) are more fragile than features derived purely from our own telemetry, and that fragility should weigh against including them unless the quality gain is clearly worth it.

Candidate features for initial experimentation:

1. Rolling trend (not just current value) of battery voltage over the trailing 7/30/90 days.
2. Rolling trend of self-test response latency.
3. Device age in days, and days since last confirmed service.
4. Firmware version cohort's historical failure rate.
5. Site environmental classification.
6. Signal-strength variability over the trailing 30 days (a proxy for physical or antenna-related degradation, distinct from battery or sensor-element issues).
7. Local temperature exposure accumulation, for outdoor-installed device families only.
8. Whether the device belongs to a manufacturer batch flagged in an errata bulletin.

Each is framed as an explicit hypothesis before being added — for example: *rolling battery-voltage trend will predict impending failure earlier than a single point-in-time reading, since a slow decline is a more physically meaningful signal than any one low reading, which could simply be temporary interference.*

Feature importance will be assessed with both model-agnostic methods (SHAP) and the gradient-boosted model's own built-in split statistics, and any feature that doesn't earn a meaningful contribution gets dropped rather than kept out of inertia.

## IX. Measuring and reporting

### i. Measuring results

Initial offline results, comparing the gradient-boosted classifier against both the rule-based and survival-analysis baselines: the ML model shows a meaningful PR-AUC improvement over the rule-based baseline across every device family, with the largest gains on device families that have the richest telemetry (motion sensors, which report the most granular self-test data) and the smallest gains on the oldest hardware generation, where the fallback rule-based baseline remains close to competitive simply because there isn't much telemetry richness for the ML model to exploit yet.

### ii. A/B test

**Hypothesis:** proactive dispatch based on model-flagged risk will reduce emergency-dispatch rate without a corresponding rise in overall technician labor cost.

**Key metric:** emergency (non-routine) dispatch rate per site per month.

**Splitting strategy:** by site, since devices within a site share environmental conditions and a single dispatch route — treatment sites get proactive dispatch recommendations layered on top of the existing quarterly schedule; control sites keep the existing process unchanged.

**Additional metrics:** routine dispatch rate (should rise modestly, by design), total technician labor hours (should stay roughly flat — proactive routine visits replacing some emergency ones, not simply adding more visits), and SLA breach rate.

**Statistical approach:** given the low base rate of emergency dispatches per site, we'll use a rate-based test appropriate to count data (e.g., a Poisson or negative-binomial comparison) rather than assuming a normal approximation holds at this volume.

**Error rates:** significance level of 5%, and given how costly missing a genuine improvement would be here, we'll target a lower type II error rate (5% rather than the more typical 10–20%) even at the cost of a longer test.

**Duration:** given the relative rarity of emergency dispatches per site per month, we expect to need a longer test than a typical high-frequency product metric would require — an initial estimate is three months, to accumulate enough emergency-dispatch events per arm for the comparison to be meaningful.

### iii. Reporting results

- Results as confidence intervals for the primary and secondary metrics above.
- A time-series chart of emergency-dispatch rate for both arms over the test period.
- Absolute counts — number of sites per arm, total dispatches of each type, confirmed failures caught proactively vs. missed.
- Methodology appendix covering how sites were matched between arms.
- A clear recommendation on next steps, including whether the rollout should extend to the device families and hardware generations not yet covered by the initial model.

## X. Integration

### i. Fallback strategies

- **Primary fallback:** the rule-based threshold system described in Section V, which continues running underneath the ML model rather than being retired — if the ML model's confidence for a given device is low (due to sparse telemetry, an unfamiliar firmware cohort, or a data-quality flag), the rule-based flag takes over for that device.
- **Secondary fallback:** the existing quarterly inspection schedule remains in place for every device regardless of model output — this system is additive, not a replacement for the baseline process, at least until it has a long enough track record to justify otherwise.

### ii. API design

```http
GET /device-risk?device_id=<device_id>&site_id=<site_id>&as_of=<date>
```

```json
{
  "device_id": "<device_id>",
  "site_id": "<site_id>",
  "risk_score": "<0.0-1.0>",
  "estimated_days_to_failure": "<estimate or null>",
  "confidence": "<low|medium|high>",
  "recommended_action": "<none|routine_dispatch|priority_dispatch>"
}
```

### iii. Release cycle

Model releases (monthly retraining, per Section IV) and infrastructure releases (the telemetry pipeline and scoring service itself) are treated as separate processes, coordinated but not coupled — a firmware-driven change to what telemetry a device reports would require both a pipeline update and a model retrain, while a pure infrastructure change (a new cloud region, a dependency bump) shouldn't require touching the model at all.

### iv. Operational concerns

Field technicians need a way to flag a false positive back into the system — if a device the model flagged turns out, on inspection, to be perfectly healthy, that feedback needs to make it back into the training data, not just get logged as a closed ticket and forgotten.

### v. Non-engineering considerations

- An internal dashboard for field-operations managers, showing flagged devices by site and priority.
- Integration with the existing technician work-order and scheduling system, so a flagged device becomes a routine work order automatically rather than a separate manual step.
- A client-facing summary, for the subset of clients whose contracts include visibility into preventive-maintenance activity.

## XI. Monitoring

### i. Existing infrastructure

This is a new capability for Aegis — there's no existing ML monitoring infrastructure to build on, which means logging, data-quality checks, and drift detection all need to be built alongside the model itself rather than bolted onto something already in place.

### ii. Logging

Every prediction is logged with its full input feature vector, output score, and timestamp, alongside the eventual outcome once known (did the device actually fail within the predicted window). Basic service metrics — requests per second, latency percentiles, error rate — are tracked separately from model-quality metrics.

### iii. Data quality

- Missing-telemetry rate, tracked per device generation, since we already know some older hardware reports incompletely — a sudden spike here on a generation that previously reported reliably is itself a signal worth alerting on.
- Schema compliance checks on incoming telemetry, since a firmware update on any device generation could silently change what fields are reported.
- Sanity-range checks on telemetry values (e.g., battery voltage within a physically plausible range for the device's chemistry).

### iv. Model quality

Precision, Recall, PR-AUC, and the lead-time distribution are tracked on an ongoing basis against confirmed outcomes as they arrive (with the inherent lag from ticket closure accounted for). Given how much ticket-closure lag there naturally is, model-quality monitoring will always run somewhat behind real time — a known, accepted limitation rather than something to be engineered away.

### v. Data drift

Fleet composition changes as new hardware generations roll out and old ones retire, which will naturally shift the input feature distribution over time regardless of anything going wrong. We'll track distributional distance between the training population and the currently-scored population, generation by generation, so a genuine drift (a bad batch, a firmware regression) doesn't get masked by ordinary generational turnover.

### vi. Business metrics

SLA breach rate, emergency-dispatch rate, and mean detection lead time — the same metrics introduced in Section II — tracked continuously in production, not just during the initial A/B test.

## XII. Serving and inference

### i. Serving architecture

The full active device fleet is scored once daily in a batch job, feeding the risk scores described in Section X into the work-order system. Separately, a small set of hard telemetry thresholds (the rule-based fallback from Section V) are evaluated continuously as telemetry arrives, so an unambiguous, urgent signal doesn't have to wait for the next daily batch run.

### ii. Infrastructure

The daily batch job runs on auto-scaled compute, sized for the full fleet's daily scoring volume; the continuous threshold-check path runs as a lightweight streaming job against the incoming telemetry feed, since it only needs to evaluate simple rules rather than run a full model.

### iii. Monitoring

Job success rate and duration for the daily batch, latency for the continuous threshold path, and — most importantly for a system whose entire value proposition is early warning — the gap between when a device's risk score first crossed the alerting threshold and when the corresponding work order was actually created, since a system that predicts correctly but doesn't translate that into a timely dispatch hasn't actually solved the problem.
