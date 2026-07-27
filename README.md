# Product Design in the Military and Security Sectors

A working knowledge base on product and UX design for security-sensitive and defense-adjacent contexts — spanning design fundamentals, engineering and industrial constraints, cybersecurity governance, and applied machine learning. The article-length material started as posts on the author's own blog, [your-scorpion.ru](https://your-scorpion.ru), translated and expanded here into a single reference under `references/book/`. Beyond the articles, the repo also includes reusable design-doc templates, fully worked examples, and a review skill.

## Repository Structure

```
├── references/
│   └── book/                          # All article content lives here
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
├── assets/images/                     # Images referenced by the articles
└── README.md
```

## Getting Started

**Reading the material?**
- Browse by topic in the Table of Contents below — every entry links directly into `references/book/`.
- Each article is standalone — no required reading order.

**Writing your own design doc?**
- Start from `templates/design_doc_template.md`.
- Check it against `templates/design_doc_checklist.md` before requesting review.
- Look at `Design_Doc_Examples/EN/` for what a filled-out version actually looks like.

**Reviewing someone else's design?**
- Load `skills/security-product-design-review/references/security-and-systems-review.md` for the review categories and questions.
- Use `output-templates.md` for how to structure findings, and `praise-patterns.md` / `red-flags-and-fixes.md` for how to phrase them.

## Table of Contents

### 1. Fundamentals of Design
- [Design Thinking](references/book/design_thinking.md)
- [Composition and Grids for Designing Websites](<references/book/Composition and Grids for Designing Websites.md>)
- [Fundamentals of Typography (including Web)](<references/book/Basics of typography.md>)
- [Fundamentals of Technical Drawing](<references/book/Fundamentals of Technical Drawing.md>)
- [Product Design Before the UI Stage](<references/book/Product Design Before the UI Stage.md>)
- [Automation with Adobe Software](<references/book/Scripting Basics in Photoshop: Equalizing the Tone of Product Images.md>)
- [Digital Color Management](references/book/color-and-light-theory.md)

### 2. Product Design Process & Production
- [Mathematics in Gamification](<references/book/The Mathematics of Gamification.md>)
- [Hyper-Casual Ergonomics](<references/book/Hyper-Casual Ergonomics.md>)
- [Transition from Static Prototypes to Interactive](<references/book/Moving from Static Prototypes to Interactive Ones.md>)
- [Building Ideas Beyond the Static Frame](<references/book/Building Ideas Beyond the Static Frame.md>)
- [Design on the Edge of UX](<references/book/Design on the Edge of UX.md>)
- [Designing Television Interface](<references/book/Designing Television Interface.md>)
- [Interactive Publications in InDesign](<references/book/Interactive Publications inDesign.md>)
- [Business Analysis](<references/book/Business Analysis.md>)
- [Systemic Thinking in Behavioral Economics](<references/book/Systemic Thinking in Behavioral Economics.md>)

### 3. Engineering & Technical Implementation
- [The Anatomy and Diagnostics of Car Electrics](<references/book/The Anatomy and Diagnostics of Car Electrics.md>)
- [Industrial Design Process of Rack-Mount Devices](references/book/industrial-design-process-for-rackmount-devices.md)
- [Properties of Industrial Materials](references/book/properties-of-industrial-materials.md)
- [Measurement Units in iOS and Android](<references/book/Units of Measurement in iOS and Android.md>)
- [Client-Server Architecture of Casual Online Games](<references/book/Client Server Interaction.md>)
- [Electronics Prototyping: Arduino + TouchDesigner](<references/book/Electronics Prototyping: Arduino + TouchDesigner.md>)
- [Pixel Path](<references/book/Pixel path.md>)
- [Pre-press](<references/book/Pre-press.md>)

### 4. Cyber-Security Foundations
- [Security of Computer Systems](<references/book/Security of Computer Systems.md>)
- [Fundamentals of Cryptography](references/book/Cryptography.md)
- [Usable Security Heuristics and Behaviour Change](<references/book/Usable Security Heuristics and Behaviour Change.md>)
- [Regulations and Formalities in Information Security](<references/book/Regulations and Formalities in Information Security.md>)
- [Managing Paper Security](<references/book/Managing Paper Security.md>)
- [Analyzing Malicious Files](<references/book/Analyzing Malicious Files.md>)
- [Preparing a Security Report](<references/book/Preparing a Security Report.md>)
- [Choosing the Right UX Research Method for Cybersecurity Products](<references/book/Choosing the Right UX Research Method for Cybersecurity Products.md>)

### 5. Cyber-Security Practices & Analytics
- [Monitoring Switched Networks](<references/book/Monitoring Switched Networks.md>)
- [ANOVA and Bootstrap: Validating UX in Python](<references/book/ANOVA and Bootstrap: Validating UX in Python.md>)
- [The Economics of a Product: End-to-End Analytics](<references/book/The Economics of a Product: End-to-End Analytics.md>)
- [Working with Tables in R: data.table and OLAP](<references/book/Working with Tables in R: data.table and OLAP.md>)
- [Python in the Analysis of Experiments](<references/book/Python in the Analysis of Experiments.md>)
- [Accessing DOM Nodes for UX Analytics](<references/book/Accessing DOM Nodes for UX Analytics.md>)

### 6. AI / Machine-Learning Foundations
- [AB Tests Check Mathematics](<references/book/AB Tests Check Mathematics.md>)
- [Recommendation Systems: ALS, AP@k, NDCG](<references/book/Recommendation Systems ALS APK NDCG.md>)
- [Machine Learning on ARM Microcontrollers (STM32)](<references/book/Machine Learning on ARM Microcontrollers (STM32).md>)
- [Gradient Boosting (AdaBoost)](<references/book/Gradient Boosting (AdaBoost).md>)

### 7. Data Science & Advanced Techniques
- [PCA: Reducing the Dimensionality of Your Data](<references/book/PCA: Reducing the Dimensionality of Your Data.md>)
- [Scikit-learn: SVM, Linear Regression, and Gradient Descent](<references/book/Scikit-learn: SVM, Linear Regression, and Gradient Descent.md>)

### 8. AI Application & Integration
- [Extracting and Filtering Data with SQL](<references/book/Extracting and Filtering Data with SQL.md>)
- [Getting Data from External Sources](<references/book/Getting Data from External Sources.md>)
- [D3.js for Building Diagrams](references/book/D3.md)
- [Regular Expressions in a Designer's Practice](<references/book/Regular Expressions in a Designer's Practice.md>)
- [Automating Asset Slicing for Mobile Devices](<references/book/Automating Asset Slicing for Mobile Devices.md>)

### 9. Working With People
*(Not present in the original 8-section outline — grouped here rather than left unlisted.)*
- [Notes on Running a Design Department](<references/book/Notes on Running a Design Department.md>)
- [Foundational Templates for the UX Researcher](<references/book/Foundational Templates for the UX Researcher.md>)
- [From Cognitive Psychology to the Interview](<references/book/From Cognitive Psychology to the Interview.md>)
- [Preparing for Interviews with Experts](<references/book/Preparing for Interviews with Experts.md>)
- [The Researcher's Toolkit: Templates That Actually Survive Contact With a Project](<references/book/The Researcher's Toolkit: Templates That Actually Survive Contact With a Project.md>)
- [Working in International Markets](<references/book/Working in International Markets.md>)
- [The Foundations of East Asian Symbolography](<references/book/The Foundations of East Asian Symbolography.md>)
- [Selling Your Experience in a Brutally Competitive Market](<references/book/Selling Your Experience in a Brutally Competitive Market.md>)

### 10. Immersive & Cross-Platform Interfaces
*(A second uncategorized group — spans VR/AR and multiplatform specification work.)*
- [User Experience in Virtual Reality](<references/book/User Experience in Virtual Reality.md>)
- [Xcode for Interface Prototypes](<references/book/Xcode for Interface Prototypes.md>)
- [Preparing Specifications for Multiplatform Products](<references/book/Preparing Specifications for Multiplatform Products.md>)
- [Variable Data in Adobe Illustrator](<references/book/Variable data in adobe illustrator.md>)
- [Basic Rules for Book Typesetting](<references/book/Basic Rules for Book Typesetting.md>)
- [Green Design of the Urban Environment](<references/book/Green Design of the Urban Environment.md>)

## Templates

Two templates live in `templates/`:

- **`design_doc_template.md`** — an 11-section template covering problem statement, context and constraints, personas, normal/degraded/alert states, alternatives considered, risks with mandatory paired mitigations, validation plan, and rollout.
- **`design_doc_checklist.md`** — a review checklist matching the template's structure, extended with security-governance depth (SMART hypotheses, standards mapping, threat modeling, team leadership, presentation) and ending in an explicit sign-off block.

## Worked Examples

Three fully worked design docs in `Design_Doc_Examples/EN/`:

- **A UX design doc** — an alert-triage dashboard for a security operations center.
- **An ML systems design doc** — predictive maintenance for a distributed physical-security sensor fleet.
- **A RAG/LLM systems design doc** — an internal compliance and security-policy assistant, centered on classification-gated retrieval and citation verification.

Both systems-design examples include an adversarial "Robustness Hardening" section, added after a structured multi-perspective review.

## Review Skill

`skills/security-product-design-review/` is a loadable skill for reviewing product and system designs in security-sensitive contexts. It uses a four-tier maturity signal (Missing / Assumed / Partially specified / Verified) rather than a numeric grade, with explicit stage adjustment so an early concept isn't judged against production-readiness standards, and vice versa.

## Contributing

No `CONTRIBUTING.md` yet — worth adding if this repo is meant to take outside contributions rather than stay a personal reference.

## License

Not yet specified. Since this repository is your own original writing and translated work, worth deciding deliberately and adding a `LICENSE` file to match.

## Acknowledgments

- Source material for all articles: [your-scorpion.ru](https://your-scorpion.ru)
- Review skill and worked examples built from this repository's own content
