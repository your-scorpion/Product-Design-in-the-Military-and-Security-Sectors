# Contributing to Product Design in the Military and Security Sectors

Thank you for your interest in contributing! This repository is a book on user-experience and product design for the military, defense, and cybersecurity sectors, together with the AI agent skills that reason over it. This guide explains how to add and improve content so it stays consistent and useful.

## Types of Contributions

We welcome the following:

1. **Chapters** — new chapters on design, research, usable security, engineering, or analytics for security-sector products.
2. **Chapter improvements** — corrections, updated examples, clearer explanations, or new diagrams for existing chapters.
3. **Agent skills** — new or improved `SKILL.md` skills (such as `security-design-lead`) that operate over the book.
4. **Reference material** — templates, checklists, and worked examples that support the chapters.
5. **Reviews** — structured feedback on existing chapters or skills.
6. **Translations** — a chapter rendered into another language, kept in its own language folder.

## Repository Layout

```
├── README.md                     # book table of contents
├── CONTRIBUTING.md               # this file
├── references/book/              # the chapters (one file per chapter)
├── skills/                       # agent skills, one folder each
│   └── security-design-lead/
│       ├── SKILL.md
│       └── references/
├── assets/images/                # figures and diagrams
```

Chapters live in `references/book/`. Each agent skill lives in its own folder under `skills/`. Images go in `assets/images/` and are referenced with a relative path.

## How to Contribute

### Adding a New Chapter

1. Place the file in `references/book/` (use the matching language folder if the repository is organized by language).
2. Follow the naming convention: lowercase **kebab-case**, describing the topic — for example `usable-security-heuristics.md`, `client-server-interaction.md`. Avoid spaces, colons, and apostrophes in filenames; they break shell globbing and `git mv`, and complicate links.
3. Open with an H1 title and a one-paragraph statement of what the chapter covers and who it is for.
4. Fit the chapter under one of the eight book sections in the README, and add it to the README table of contents in the correct section.
5. Put any figures in `assets/images/` and reference them relatively (e.g. `../../assets/images/your-figure.png`), with alt text.

### Adding or Updating an Agent Skill

1. Create a folder under `skills/` named after the skill (kebab-case), containing a `SKILL.md`.
2. In the frontmatter, include `name`, a **pushy** `description` that states both what the skill does and when it triggers, and a `metadata` block with `version` and `reference`.
3. Keep `SKILL.md` focused (ideally under ~500 lines); move detail into a `references/` subfolder loaded on demand.
4. If the skill reasons over the book, cite chapters by their path in `references/book/`, and update the skill's routing when chapters are renamed.
5. Bump the skill `version` when you change its behavior.

### Quality Guidelines

- Ground claims in the operating reality of security users — stress, fatigue, time pressure, adversarial conditions — not the calm demo case.
- Treat usability and security as one problem: a control users predictably bypass is a design failure, not a user failure.
- Provide clear problem statements and real business or mission context.
- Include diagrams where they clarify a flow, hierarchy, or system boundary.
- Reference sources and prior work; do not present others' material as your own.
- **Never** include classified, export-controlled, or otherwise sensitive material, real operational data, or anything that could aid an attacker. Use illustrative or sanitized examples only.
- Use clear, professional language and define domain terms on first use.

## Submission Process

1. Fork the repository.
2. Create a new branch for your contribution (e.g. `add-alert-triage-chapter`).
3. Make your changes, keeping one logical change per branch.
4. Check your work against the Quality Guidelines above and confirm links and images resolve.
5. Open a pull request with a short description of what you added or changed and why.
6. Respond to review comments.

## Review Process

All submissions are reviewed for:

- Adherence to the layout, naming, and style conventions here.
- Technical and domain accuracy, and completeness.
- Clear writing and organization.
- Practical value to someone designing security-sector products.
- For skills: correct frontmatter, sensible triggering, and working chapter references.

## Style Guide

### Writing Style

- Use clear, concise language and define technical terms.
- Keep formatting consistent with existing chapters.
- Include examples where they help.
- Prefer active, imperative phrasing in skill instructions.

### Markdown Guidelines

- Use a proper heading hierarchy (one H1 per file).
- Include a table of contents for long chapters.
- Use fenced code blocks for technical content and skill snippets.
- Include alt text for every image.
- Use kebab-case filenames and relative links.

## Questions?

If you have questions about contributing, please:

1. Check existing issues.
2. Review the chapters, skills, and this guide.
3. Open a new issue for clarification.

Thank you for helping improve design practice for the security and defense sectors!
