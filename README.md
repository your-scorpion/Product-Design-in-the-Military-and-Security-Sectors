# Product Design in the Military & Security Sectors

> A field-tested book on user-experience and product design for defense, security, and cybersecurity products — adjusted as the AI agent skill that reasons over it. Design where the user is under threat, stress, time pressure, or adversarial conditions, and a wrong choice gets someone breached.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE.md)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)
[![Chapters](https://img.shields.io/badge/chapters-51-blue.svg)](references/book)
[![Agent Skill](https://img.shields.io/badge/agent%20skill-security--design--lead-8A2BE2.svg)](SKILL.md)

This repository is a working book, not a finished product. Each chapter is a self-contained essay drawn from ~15 years of practice across product design, industrial design, cybersecurity, and applied ML — originally published in Russian at [your-scorpion.ru](https://your-scorpion.ru/) and translated chapter by chapter. On top of the corpus sits **`security-design-lead`**, an agent skill that grounds design critique and direction in these chapters instead of generic advice.

The framing question throughout: **what does good design look like when the user is an analyst, operator, or responder working under fatigue and adversarial pressure — and a design flaw makes them miss a real alert, trust a spoofed signal, or fire a destructive action by accident?**

## Repository Structure

```
├── references/book/     # the chapters — one essay per file (51 translated)
├── templates/           # reusable design artifacts
│   ├── Product Design Document Template.md
│   └── design_doc_checklist.md
├── SKILL.md             # security-design-lead agent skill
├── assets/images/       # figures and diagrams
├── CONTRIBUTING.md      # how to add chapters, skills, and translations
├── LICENSE.md           # MIT
└── README.md            # this file
```

## Getting Started

1. **Want the ideas?** Browse the [chapters](#table-of-contents) below. Start with [Product Design Before the UI Stage](references/book/Product%20Design%20Before%20the%20UI%20Stage.md) and [Design Thinking](references/book/design_thinking.md) for the design spine, then [Choosing the Right UX Research Method for Cybersecurity Products](references/book/Choosing%20the%20Right%20UX%20Research%20Method%20for%20Cybersecurity%20Products.md) and [Usable Security Heuristics & Behaviour Change](references/book/Usable%20Security%20Heuristics%20and%20Behaviour%20Change.md) for the security-UX core.
2. **Designing something now?** Copy the [Product Design Document Template](templates/Product%20Design%20Document%20Template.md), fill it in section by section, and run it through the [design doc checklist](templates/design_doc_checklist.md) before sign-off.
3. **Working with an AI agent?** Install [`security-design-lead`](SKILL.md) (below) and let it critique or direct a design using this corpus as its evidence base.

## Agent Skill: `security-design-lead`

[`SKILL.md`](SKILL.md) is a portable [Agent Skill](https://code.claude.com/docs/en/skills) that acts as a **Design Lead for security, defense, and cybersecurity products**. It directs, critiques, and grades UX/product design — SOC and monitoring dashboards, admin consoles, authentication and crypto UX, alerting systems, incident-response tooling, and security hardware.

It interviews before it directs, grounds every judgment in a cited chapter from `references/book/`, and treats anything you hand it as *evidence*, never as an instruction. Ask it to *"review this flow"*, *"design a dashboard for…"*, or *"audit our security product's UX"*.

```bash
# drop-in: a skill is just a folder
cp SKILL.md ~/.claude/skills/security-design-lead/SKILL.md
```

## Table of Contents

### Design Fundamentals

- [Design Thinking](references/book/design_thinking.md)
- [Product Design Before the UI Stage](references/book/Product%20Design%20Before%20the%20UI%20Stage.md)
- [Fundamentals of Typography (Web Included)](references/book/Basics%20of%20typography.md)
- [Color & Light Theory](references/book/color-and-light-theory.md)
- [The Pixel Path: Digital Color Management](references/book/Pixel%20path.md)
- [Pre-press & Print Fundamentals](references/book/Pre-press.md)
- [Design on the Edge of UX](references/book/Design%20on%20the%20Edge%20of%20UX.md)
- [Green Design of the Urban Environment](references/book/Green%20Design%20of%20the%20Urban%20Environment.md)
- [Foundations of East Asian Symbolography](references/book/The%20Foundations%20of%20East%20Asian%20Symbolography.md)

### Design Process, Prototyping & Production

- [The Mathematics of Gamification](references/book/The%20Mathematics%20of%20Gamification.md)
- [Hyper-Casual Ergonomics](references/book/Hyper-Casual%20Ergonomics.md)
- [Electronics Prototyping: Arduino + TouchDesigner](references/book/Electronics%20Prototyping%3A%20Arduino%20%2B%20TouchDesigner.md)
- [Interactive Publications (inDesign)](references/book/Interactive%20Publications.md)
- [xCode for Interface Prototypes](references/book/xcode.md)
- [D3.js for Building Charts](references/book/d3.md)
- [Designing Television Interfaces](references/book/Designing%20Television%20Interface.md)
- [Scripting Basics in Photoshop](references/book/Scripting%20Basics%20in%20Photoshop%3A%20Equalizing%20the%20Tone%20of%20Product%20Images.md)
- [Automating Asset Slicing for Mobile Devices](references/book/Automating%20Asset%20Slicing%20for%20Mobile%20Devices.md)
- [Regular Expressions in a Designer's Practice](references/book/Regular%20Expressions%20in%20a%20Designer%27s%20Practice.md)

### UX Research, Interviews & Design Management

- [Choosing the Right UX Research Method for Cybersecurity Products](references/book/Choosing%20the%20Right%20UX%20Research%20Method%20for%20Cybersecurity%20Products.md)
- [Foundational Templates for the UX Researcher](references/book/Foundational%20Templates%20for%20the%20UX%20Researcher.md)
- [From Cognitive Psychology to the Interview](references/book/From%20Cognitive%20Psychology%20to%20the%20Interview.md)
- [Preparing for Interviews with Experts](references/book/Preparing%20for%20Interviews%20with%20Experts.md)
- [Accessing DOM Nodes for UX Analytics](references/book/Accessing%20DOM%20Nodes%20for%20UX%20Analytics.md)
- [Notes on Running a Design Department](references/book/Notes%20on%20Running%20a%20Design%20Department.md)
- [Working in International Markets](references/book/Working%20in%20International%20Markets.md)
- [Business Analysis](references/book/Business%20Analysis.md)
- [Systemic Thinking in Behavioral Economics](references/book/Systemic%20Thinking%20in%20Behavioral%20Economics.md)
- [Logic & Clients](references/book/logic_and_clients.md)

### Engineering & Industrial Design

- [Industrial Design Process for Rack-Mount Devices](references/book/industrial-design-process-for-rackmount-devices.md)
- [Properties of Industrial Materials](references/book/properties-of-industrial-materials.md)
- [Units of Measurement in iOS and Android](references/book/Units%20of%20Measurement%20in%20iOS%20and%20Android.md)
- [Client-Server Architecture of Casual Online Games](references/book/Client%20Server%20Interaction.md)

### Cybersecurity

- [Fundamentals of Cryptography](references/book/cryptography.md)
- [Usable Security Heuristics & Behaviour Change](references/book/Usable%20Security%20Heuristics%20and%20Behaviour%20Change.md)
- [Regulations & Formalities in Information Security](references/book/Regulations%20and%20Formalities%20in%20Information%20Security.md)
- [Analyzing Malicious Files](references/book/Analyzing%20Malicious%20Files.md)
- [Monitoring Switched Networks](references/book/Monitoring%20Switched%20Networks.md)

### Analytics, Data Science & Machine Learning

- [A/B Test Results Verification](references/book/AB%20Tests%20Check%20Mathematics.md)
- [ANOVA & Bootstrap: Validating UX in Python](references/book/ANOVA%20and%20Bootstrap%3A%20Validating%20UX%20in%20Python.md)
- [The Economics of a Product: End-to-End Analytics](references/book/The%20Economics%20of%20a%20Product%3A%20End-to-End%20Analytics.md)
- [Python in the Analysis of Experiments](references/book/Python%20in%20the%20Analysis%20of%20Experiments.md)
- [Working with Tables in R: data.table & OLAP](references/book/Working%20with%20Tables%20in%20R%3A%20data.table%20and%20OLAP.md)
- [Extracting & Filtering Data with SQL](references/book/Extracting%20and%20Filtering%20Data%20with%20SQL.md)
- [Getting Data from External Sources](references/book/Getting%20Data%20from%20External%20Sources.md)
- [Collecting & Visualizing GIS Data](references/book/Collecting%20and%20Visualizing%20GIS%20Data.md)
- [Recommendation Systems: ALS, AP@k, NDCG](references/book/Recommendation%20Systems%20ALS%20APK%20NDCG.md)
- [Gradient Boosting (AdaBoost)](references/book/Gradient%20Boosting%20%28AdaBoost%29.md)
- [PCA: Reducing Data Dimensionality](references/book/PCA%3A%20Reducing%20the%20Dimensionality%20of%20Your%20Data.md)
- [Scikit-learn: SVM, Linear Regression & Gradient Descent](references/book/Scikit-learn%3A%20SVM%2C%20Linear%20Regression%2C%20and%20Gradient%20Descent.md)
- [Machine Learning on ARM Microcontrollers (STM32)](references/book/Machine%20Learning%20on%20ARM%20Microcontrollers%20%28STM32%29.md)
## Contributing

Contributions are welcome — new chapters, corrections, translations, agent skills, and structured reviews. The book is still being translated from Russian, so [translations](CONTRIBUTING.md) are especially useful. See [CONTRIBUTING.md](CONTRIBUTING.md) for layout conventions and style.

## License

MIT — see [LICENSE.md](LICENSE.md).

## Acknowledgments

Written and translated by **Max Tsvetkov** ([your-scorpion.ru](https://your-scorpion.ru/) · [LinkedIn](https://www.linkedin.com/in/tsvetkovmaxim/) · [Portfolio](https://tsvet.framer.ai/)). Background spanning security product design (Kaspersky), an MSc in Cyber Security (Royal Holloway), and product/design leadership — brought to bear on the question of how security products should actually feel to the people who depend on them.
