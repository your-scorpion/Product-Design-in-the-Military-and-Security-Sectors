# Product Design in the Military and Security Sectors

A working knowledge base on product and UX design for security-sensitive and defense-adjacent contexts — spanning design fundamentals, engineering and industrial constraints, cybersecurity governance, and applied machine learning. Most of the article-length material started as posts on the author's own blog, [your-scorpion.ru](https://your-scorpion.ru), translated and expanded here into a single reference. Beyond the articles, the repo also includes reusable design-doc templates, fully worked examples, and a review skill built from the material itself.

## Repository Structure

```
├── templates/
│   ├── design_doc_template.md         # Reusable template for a product/security design doc
│   └── design_doc_checklist.md        # Review checklist for a filled-out design doc
├── Design_Doc_Examples/
│   └── EN/
│       ├── security-operations-dashboard-example.md      # UX design doc: SOC alert-triage dashboard
│       ├── Aegis_Sensor_Predictive_Maintenance_Design.md # ML system design doc: sensor fleet predictive maintenance
│       └── Compliance_Assistant_RAG_Design.md            # RAG/LLM system design doc: internal compliance assistant
├── skills/
│   └── security-product-design-review/
│       └── references/
│           ├── security-and-systems-review.md  # Review categories, questions, red flags, stage adjustment
│           ├── output-templates.md              # Scorecard + findings report structure
│           ├── praise-patterns.md                # Mechanism-specific praise, no hollow phrases
│           └── red-flags-and-fixes.md            # Prioritized severity tiers, fix ordering
├── assets/images/                     # Images referenced by the articles below
├── [article files, listed by topic below]
└── README.md
```

## Getting Started

**Reading the material?**
- Browse by topic in the Table of Contents below.
- Each article is a standalone piece — no required reading order.

**Writing your own design doc?**
- Start from `templates/design_doc_template.md`.
- Check it against `templates/design_doc_checklist.md` before requesting review.
- Look at `Design_Doc_Examples/EN/` for what a filled-out version actually looks like — one UX-focused example, two systems-design examples (one classic ML, one RAG/LLM).

**Reviewing someone else's design?**
- Load `skills/security-product-design-review/references/security-and-systems-review.md` for the review categories and questions.
- Use `output-templates.md` for how to structure the findings.
- Use `praise-patterns.md` and `red-flags-and-fixes.md` for how to phrase what you find.

## Table of Contents

### 1. Fundamentals of Design
- [Design Thinking](design_thinking.md)
- **Composition and Grids for Web Design** — translated, not yet added; file pending
- [Fundamentals of Typography (including Web)](Basics%20of%20typography.md)
- **Fundamentals of Technical Design** — translated as "Fundamentals of Technical Drawing," not yet added; file pending
- [Product Design up to the UI Stage](Product%20Design%20Before%20the%20UI%20Stage.md)
- **Useful Techniques in 3ds Max + V-Ray** — not yet written
- [Automation with Adobe software](Scripting%20Basics%20in%20Photoshop%3A%20Equalizing%20the%20Tone%20of%20Product%20Images.md)
- [Digital Color Management](color-and-light-theory.md)

### 2. Product Design Process & Production
- **Ideation & Concept Development** — not yet written
- **Prototyping & Rapid Iteration** — not yet written
- [Mathematics in Gamification](The%20Mathematics%20of%20Gamification.md)
- [Hyper-Casual Ergonomics](Hyper-Casual%20Ergonomics.md)
- **Transition from Static Prototypes to Interactive** — translated as "Moving from Static Prototypes to Interactive Ones," not yet added; file pending
- **Basic HTML + CSS** — not yet written
- [Design on the Edge of UX](Design%20on%20the%20Edge%20of%20UX.md)
- [Designing Television Interface](Designing%20Television%20Interface.md)
- [Interactive Publications](Interactive%20Publications.md)

### 3. Engineering & Technical Implementation
- **Vehicle Electrical System Design and Diagnostics** — translated as "The Anatomy and Diagnostics of Car Electrics," not yet added; file pending
- [Industrial Design Process of Rack-Mount Devices](industrial-design-process-for-rackmount-devices.md)
- [Properties of Industrial Materials](properties-of-industrial-materials.md)
- [Measurement Units in iOS and Android](Units%20of%20Measurement%20in%20iOS%20and%20Android.md)
- [Client-Server Architecture of Casual Online Games](Client%20Server%20Interaction.md)
- [Electronics Prototyping: Arduino + TouchDesigner](Electronics%20Prototyping%3A%20Arduino%20%2B%20TouchDesigner.md)
- [Pixel Path](Pixel%20path.md)
- [Pre-press](Pre-press.md)

### 4. Cyber-Security Foundations
- **Computer-Systems Security** — translated as "Security of Computer Systems," not yet added; file pending
- [Fundamentals of Cryptography](cryptography.md)
- [Heuristics of User-Friendly Information Security](Usable%20Security%20Heuristics%20and%20Behaviour%20Change.md)
- **Regulations and Formalities in Information Security** — not yet written (see also Paper-Security Management below, which may cover the same ground)
- **Paper-Security Management** — translated as "Managing Paper Security," not yet added; file pending
- **Malicious File Analysis** — translated as "Analyzing Malicious Files," not yet added; file pending
- **Preparing an Information-Security Report** — translated as "Preparing a Security Report," not yet added; file pending
- [Choosing the Right UX Research Method for Cybersecurity Products](Choosing%20the%20Right%20UX%20Research%20Method%20for%20Cybersecurity%20Products.md)

### 5. Cyber-Security Practices & Analytics
- [Monitoring Switched Networks](Monitoring%20Switched%20Networks.md)
- [ANOVA and Bootstrap: Testing UX in Python](ANOVA%20and%20Bootstrap%3A%20Validating%20UX%20in%20Python.md)
- [Product ROI Analytics](The%20Economics%20of%20a%20Product%3A%20End-to-End%20Analytics.md)
- [Working with Tables in R: Data.table and OLAP](Working%20with%20Tables%20in%20R%3A%20data.table%20and%20OLAP.md)
- [Python in Test Analysis](Python%20in%20the%20Analysis%20of%20Experiments.md)
- [Accessing DOM Nodes for UX Analytics](Accessing%20DOM%20Nodes%20for%20UX%20Analytics.md)

### 6. AI / Machine-Learning Foundations
- [A/B Test Results Verification](AB%20Tests%20Check%20Mathematics.md)
- [Recommendation Systems: ALS, AP@k, NDCG](Recommendation%20Systems%20ALS%20APK%20NDCG.md)
- [Machine Learning on ARM Microcontrollers (STM32)](Microcontrollers%20ARM%20STM32%20Tinyml.md)
- [Gradient Boosting (AdaBoost)](Gradient%20Boosting%20(AdaBoost).md)

### 7. Data Science & Advanced Techniques
- [PCA: Data Dimensionality Reduction](PCA%3A%20Reducing%20the%20Dimensionality%20of%20Your%20Data.md)
- [Scikit-learn: SVM, Linear Regression, Gradient Descent](Scikit-learn%3A%20SVM%2C%20Linear%20Regression%2C%20and%20Gradient%20Descent.md)
- **Creating LLM Agents and Using MCP** — not yet written (see `Design_Doc_Examples/EN/Compliance_Assistant_RAG_Design.md` for a related worked example in the meantime)

### 8. AI Application & Integration
- **Data Extraction and Filtering – SQL** — translated as "Extracting and Filtering Data — SQL," not yet added; file pending
- [Acquiring Data from External Sources](Getting%20Data%20from%20External%20Sources.md)
- [d3.js](d3.md)
- [Regular Expressions in a Designer's Practice](Regular%20Expressions%20in%20a%20Designer's%20Practice.md)
- [Automating Asset Slicing for Mobile Devices](Automating%20Asset%20Slicing%20for%20Mobile%20Devices.md)

### 9. Working With People
*(Not present in the original 8-section outline — folding in here rather than leaving these six files unlisted.)*
- [Notes on Running a Design Department](Notes%20on%20Running%20a%20Design%20Department.md)
- [Foundational Templates for the UX Researcher](Foundational%20Templates%20for%20the%20UX%20Researcher.md)
- [From Cognitive Psychology to the Interview](From%20Cognitive%20Psychology%20to%20the%20Interview.md)
- [Preparing for Interviews with Experts](Preparing%20for%20Interviews%20with%20Experts.md)
- [The Researcher's Toolkit: Templates That Actually Survive Contact With a Project](The%20Researcher's%20Toolkit%3A%20Templates%20That%20Actually%20Survive%20Contact%20With%20a%20Project.md)
- [Working in International Markets](Working%20in%20International%20Markets.md)
- [The Foundations of East Asian Symbolography](The%20Foundations%20of%20East%20Asian%20Symbolography.md)

*Note on `xcode.md`: a fuller translation ("Xcode for Interface Prototypes") exists separately and covers more ground than the current file — worth checking whether to replace it before relying on this section's listing.*

## Templates

Two templates live in `templates/`:

- **`design_doc_template.md`** — an 11-section template covering problem statement, context and constraints, personas, normal/degraded/alert states, alternatives considered, risks with mandatory paired mitigations, validation plan, and rollout.
- **`design_doc_checklist.md`** — a review checklist matching the template's structure, extended with security-governance depth (SMART hypotheses, standards mapping, threat modeling, team leadership, presentation) and ending in an explicit sign-off block.

## Worked Examples

Three fully worked design docs in `Design_Doc_Examples/EN/`, each demonstrating the template and checklist applied to a real (fictional, illustrative) scenario:

- **A UX design doc** — an alert-triage dashboard for a security operations center.
- **An ML systems design doc** — predictive maintenance for a distributed physical-security sensor fleet, structurally analogous to a classic demand-forecasting design doc but built around a genuinely different problem: rare-event failure prediction rather than continuous demand.
- **A RAG/LLM systems design doc** — an internal compliance and security-policy assistant, centered on classification-gated retrieval and citation verification rather than the general document-chat problem a typical RAG example covers.

Both systems-design examples include an adversarial "Robustness Hardening" section, added after a structured multi-perspective review — worth reading as a model for critiquing your own designs, not just as reference content.

## Review Skill

`skills/security-product-design-review/` is a loadable skill for reviewing product and system designs in security-sensitive contexts. It uses a four-tier maturity signal (Missing / Assumed / Partially specified / Verified) rather than a numeric grade, with explicit stage adjustment so an early concept isn't judged against production-readiness standards, and vice versa. Its six review categories, red flags, and praise patterns are all grounded in the articles above and in the findings from the worked examples' own robustness reviews — not a generic checklist imported from elsewhere.

## Contributing

No `CONTRIBUTING.md` yet — worth adding if this repo is meant to take outside contributions rather than stay a personal reference. Until then: keep new articles consistent with the existing topic sections above, and run new design docs through `templates/design_doc_checklist.md` before adding them as examples.

## License

Not yet specified. Since this repository is your own original writing and translated work, worth deciding deliberately — even "all rights reserved" is better stated explicitly than left silent — and adding a `LICENSE` file to match.

## Acknowledgments

- Source material for most articles: [your-scorpion.ru](https://your-scorpion.ru)
- Review skill and worked examples built from this repository's own content
