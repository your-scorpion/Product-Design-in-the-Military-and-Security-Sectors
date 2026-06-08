# Heuristics for Usable Information Security

> How a UX practitioner thinks about changing employee security behavior — goals and risk, behavior-change models, usability heuristics for security interfaces, metrics, and the balance between security and convenience.

**Author:** Maksim Tsvetkov · **Originally published:** 5 July 2024 · Translated from Russian.

---

## Table of Contents

- [Goals and Risk Assessment](#goals-and-risk-assessment)
- [Models of Behavior Change](#models-of-behavior-change)
- [A Personal Behavior-Change Model](#a-personal-behavior-change-model)
- [Artifacts](#artifacts)
- [The Security-Management Model](#the-security-management-model)
  - [A practical aside: what raises an individual's threat profile](#a-practical-aside-what-raises-an-individuals-threat-profile)
- [Heuristics](#heuristics)
  - [Understandability and trust](#understandability-and-trust)
  - [Authentication](#authentication)
  - [User control and freedom](#user-control-and-freedom)
  - [Error recognition, diagnosis, and recovery](#error-recognition-diagnosis-and-recovery)
  - [User support and documentation](#user-support-and-documentation)
  - [Accessibility](#accessibility)
  - [Nielsen's heuristics](#nielsens-heuristics)
- [Metrics](#metrics)
  - [A worked example: anatomy of a SIEM metric](#a-worked-example-anatomy-of-a-siem-metric)
  - [The universal trio: effectiveness, efficiency, satisfaction](#the-universal-trio-effectiveness-efficiency-satisfaction)
  - [A practical aside: is this email phishing?](#a-practical-aside-is-this-email-phishing)
  - [A practical aside: correlating domains](#a-practical-aside-correlating-domains)
- [What to Pay Attention To](#what-to-pay-attention-to)
- [A Note on Behavior, Triggers, and Nudges](#a-note-on-behavior-triggers-and-nudges)

---

Creating a need, changing people's behavior and habits, and helping people form motivations is one of the UX practitioner's tasks. And the moment we are handed the task of bringing employee behavior into line with a company's security requirements, the first thing that comes to mind is to throw some courses at the staff, followed by a test phishing campaign. But it is not that simple — let us work through it.

## Goals and Risk Assessment

We want to change employees' behavior, and to do that we must understand which behavior is considered expected. In other words, we need to define goals. Goals should be as concrete as possible rather than vague, and described in SMART terms. We are usually interested in short-term goals with a positive outcome, judged by actual results rather than by formal metrics. And most importantly, goals are built up from a risk assessment. Risk is assessed along three dimensions: severity, frequency, and criticality.

## Models of Behavior Change

The best-known model of behavior change — now more than forty years old — is the transtheoretical model. It consists of six stages describing an employee's evolution from unconscious, undesirable behavior toward the desired behavior:

- **Precontemplation** — does not want to change their behavior and is unaware of its consequences.
- **Contemplation** — wants to change within the next six months; aware of both the benefits and the difficulties of the process.
- **Preparation** — preparing to change within the next month, having taken preparatory steps such as joining support groups, finding a coach, buying books, or subscribing to channels.
- **Action** — has made noticeable changes over the past six months.
- **Maintenance** — sustains the new habits.
- **Termination** — has no desire to return to the old habits.

There are other models too — the Information–Motivation–Behavioral Skills model, the Behavior Change Wheel, or the COM-B model.

But all these models share certain key principles, and without grasping them you will not change people's behavior. For example, people are usually quite willing to sacrifice security for a reward, or simply for convenience. Suppose we require a strong password of at least *n* characters. Seems simple and clear enough. But a person may have trouble with memory or eyesight, and after ten attempts to invent a strong password they may simply lose their temper — and hand the job of inventing it to their child. In my experience, the use of password managers and easily memorable passwords leads to better overall protection. Encouraging passphrases instead of complex passwords strikes a balance between security and usability.

There is no universal model. Every company is unique and demands an individual approach. Laws, regulations, and social and cultural values exert a significant influence on security practice. Paying close attention to how these factors are interpreted will tell you a great deal about why certain security behaviors prevail in an organization.

## A Personal Behavior-Change Model

My own behavior-change model looks like this:

1. **Identify the problem.** What problem do we want to solve, and what behavior do we want to achieve? To begin, we observe current behavior, analyze it, and on that basis form a vision of the new behavior.
2. **Context and environment.** We look more closely at current behavior, study the effect of our potential intervention, account for cultural context and usability nuances, and always factor in employees' limits of skill, motivation, and knowledge.
3. **Build the solution.** This might be as little as talking with employees and identifying security champions.
4. **Apply the solution** — with metrics.
5. **Determine the impact and effect** of our intervention.

## Artifacts

In the course of this work a great many artifacts usually take shape, which I divide into the following groups:

1. **Conceptual** — ideas, hypotheses.
2. **Procedural** — instructions, methods.
3. **Regulatory** — rules and standards.
4. **Empirical data** — observations from research.
5. **Directive** — how to arrive at a specific result.
6. **Facts.**
7. **Incentives.**

## The Security-Management Model

Any security-management model consists of people, processes, and policies. The diagram below illustrates a typical security-management decision flow. On the left are the three principal inputs to security-management decisions: (i) regulatory inputs, comprising laws, rules, and values; (ii) business risks, comprising the cybersecurity risks to the company's strategic goals, brand, and reputation; and (iii) operational risks, comprising the cybersecurity risks that arise from the use, management, and deployment of technology. These inputs are assessed and prioritized by a committee of the organization's stakeholders. The resulting prioritization and risk assessment shape the security-management processes and technologies shown on the right side of the diagram.

![Security-management decision flow](https://your-scorpion.ru/wp-content/uploads/2024/07/Frame-1000006119.png)

My favorite part of the diagram is operational risks. Once they are assessed, it becomes easy to articulate the problems tied to everyday human–computer interaction. When individuals and groups do not use security controls as intended, it can signal a divergence in security priorities.

### A practical aside: what raises an individual's threat profile

Risk assessment is not only about systems; it is also about people, and it is worth understanding what elevates a particular person's risk of being targeted — for instance, what might prompt an advanced persistent threat (APT) actor to begin tracking someone's mobile phone. Reported indicators include:

1. being a high-profile individual;
2. visiting a particular religious institution more than 200 times a year;
3. using a VPN;
4. receiving or sending religious texts over SMS or social media;
5. attempting to reach social networks after they have been blocked at the national level;
6. taking part in protests.

The point of cataloguing such indicators is not paranoia but calibration: who is actually a likely target shapes how much friction a given security control can reasonably impose.

## Heuristics

A heuristic is a mental shortcut — you might call it a checklist of ready-made rules for evaluating an interface, an idea, or some code. Much has already been worked out for us. Before rushing off to run yet another study, generate hypotheses, and hire an agency for the entire annual budget, you can read the usability criteria in ISO 9241-11.

### Understandability and trust

The first block of heuristics for information-security interfaces is about making everything clear to the user, which builds trust.

1. The user can always access security-relevant information.
2. The system notifies the user of relevant changes to security measures.
3. The system directly informs the user of mandatory security actions they must perform.
4. The system provides multiple levels of security detail if it is used by both novice and experienced users.
5. The system shows only the security information needed for the current activity.
6. Security-relevant information is concise, easy to understand, and descriptive.
7. It is clearly stated for what purposes users' personal information is used.
8. It is clearly stated for what purposes users' personal information is used.
9. The system informs the user if it is in an insecure state.
10. Security-related terminology is expressive and consistent.
11. Security-related terminology conforms to standards.
12. The system informs the user of progress if security-related functionality introduces delays.

### Authentication

1. Authentication is a prerequisite for accessing protected or confidential sections.
2. The user can choose between different authentication options.
3. The authentication options are designed so as not to place an excessive cognitive load on users.
4. The system enforces a strong password policy that is nevertheless simple to understand.
5. The user can change their password.
6. The system informs the user of the password policy when they set a password.
7. By default, the system never displays the password.
8. The system tells the user whether authentication was successful.
9. The system locks out repeated failed authentication attempts after *n* tries.

### User control and freedom

1. The user must accept the terms of use and the privacy policy.
2. The user must confirm the transfer of data to third parties.
3. The user can choose between an icon and a text display of security-status information.
4. The system provides shortcuts for frequent tasks.
5. The user can configure security preferences.
6. The user can undo their security-related operations.
7. The user can cancel security-related operations they have initiated.
8. The user must confirm actions with drastic, negative, or irreversible consequences.
9. The user can update or delete personal information.
10. The system implements the principles of "Security by default" and "Privacy by default."
11. All privacy settings apply across the entire system.

### Error recognition, diagnosis, and recovery

1. Error messages always indicate the cause of the problem.
2. Error messages inform the user of the severity of the problem.
3. Error messages contain information about the actions the user should take.
4. Error messages are written in plain human language.

### User support and documentation

1. Accessing the documentation does not require suspending the current task.
2. Help and documentation follow the user's sequence of actions.
3. Users are informed of updates to help and documentation.
4. Help and documentation are reachable from every screen.

### Accessibility

1. The system allows graphical passwords for users who have difficulty reading text.
2. The system allows text passwords for visually impaired users.
3. The system is designed for people with the widest range of characteristics.

### Nielsen's heuristics

We cannot pass over Nielsen's heuristics. Yes, they are old and may seem obvious, but they are the foundation — recast here as questions for cybersecurity:

- **Visibility of system status** — Are there clear indicators of potential threats or breaches?
- **Match between the system and the real world** — Does the cybersecurity system use familiar language and concepts? Are security warnings and prompts intelligible to non-specialists?
- **User control and freedom** — How much control do users have over security parameters and configurations?
- **Consistency and standards** — Are security protocols standardized across different platforms or devices?
- **Error prevention** — How do systems help users avoid mistakes that could compromise security?
- **Recognition rather than recall** — How can cybersecurity minimize users' cognitive load while preserving security?
- **Flexibility and efficiency of use** — Is the product adapted to users with differing levels of knowledge?
- **Aesthetic and minimalist design** — How does the cybersecurity interface's design affect user engagement and perception?
- **Help users recognize, diagnose, and recover from errors** — Are there clear instructions or support mechanisms for users facing security problems?
- **Help and documentation** — Is convenient documentation available, with support channels accessible to non-technical users?

## Metrics

We need metrics. Most importantly, we count metrics only relative to a specific environment. A good metric has a quantitative expression (not "high/low"). Examples: the number of blocked connections or attacks, the number of devices with out-of-date antivirus definitions, the number of security-related support requests. By contrast, something like "percentage reduction in security risk" is a poor metric, even if some SIEM hands it to you. A better metric is something more granular — say, a system's resistance to XSS before and after introducing taint checking. A list of metrics can be found at [securitymetrics.org](https://www.securitymetrics.org/); I usually try to add such metrics to the SLA requirements.

You cannot rely on observation alone — it is a very limited method for cybersecurity. Ideally, metrics should be free of expert opinion. Sending staff to every office to gather the necessary data can also prove costly for an international corporation. And there is the risk that people will warn one another about the observer's next round, urging colleagues to lock their screens, hide their flash drives, come back to the office for a day from remote work, and peel the password sticky notes off before the observer reaches their desk. Outsiders brought in to assess change can also make employees feel awkward and watched as they go about their daily work. Even so, combining observation with self-reports can smooth over some of the shortcomings of both measurement methods for cybersecurity behavior that cannot be measured directly with technical means.

Since users almost certainly interact with some system, you can automate the collection of certain data — for example, the percentage of clicks on phishing emails and, if opened, whether they entered sensitive data or launched an attached file. How much time elapsed between opening the email and launching the attachment? Another metric is how many times the system locked itself due to user inactivity.

### A worked example: anatomy of a SIEM metric

Let us unpack one metric in a little more detail — a SIEM event, for instance.

**Source-data structure**

- What underlies the dump.
- How much of it is synthetic.
- How it was synthesized.
- The median (1Q) message size.
- What stream was generated and how it was fed into the SIEM (pushed onto the bus, collected by agents).

**Platform structure**

- How many servers.
- Their technical specifications.
- How and by what they are connected (the network).

**Content**

- How many normalization rules.
- How many normalization rules actually fired during the test.
- The average number of fields normalized across all rules (otherwise you can write a rule that normalizes a single field and show insane throughput).
- How many enrichment rules.
- How many enrichment rules use table lists.
- The average volume of data in those tables (in bytes and in count).
- How many correlation rules.
- The average depth of the rules (the depth of the event chain).
- How many correlation rules use tables.
- The average volume of data in those tables (in bytes and in count).
- How many correlation rules fired.
- How many correlation rules generated incidents.

**Storage**

- Whether raw events were stored, or only normalized ones.
- Whether storage was configured to a database, or simply to `/dev/null`.
- Whether the database was empty at the start.

### The universal trio: effectiveness, efficiency, satisfaction

If metrics this elaborate and detailed are beyond your organization's current maturity, there is always the universal trio: **effectiveness, efficiency, and satisfaction.**

**Effectiveness** is whether people can successfully reach their goals — for example, whether an employee can quickly and easily report a phishing email to the cybersecurity team. The psychological dimension matters too: I have noticed that when a user knows their computer is being monitored, it affects how effectively they complete tasks. Remember that, for an employee, thinking about security is only ever a secondary task.

**Efficiency** is the resources — time, cognitive effort — used to reach those goals: for example, how long it takes an employee to authenticate into an application successfully. Employees usually find it easy to make an equivalent one-way choice between two or more options — install updates or not. But if the security guidance is contradictory, no one will follow it — take, for instance, the rules on whether to share your password with colleagues.

**Satisfaction** is the intersection of the user's physical, cognitive, and emotional responses while using a system, product, or service, and how well their needs and expectations are met. In a cybersecurity context, satisfaction can be hurt by the irritation a user feels at repeated security warnings. Good examples of usable security: two-factor authentication, where if the code arrives quickly and the token gives no trouble, security has improved; allowing password managers, which is good; and having 2FA with Face ID, which helps lower the cognitive effort.

This way we cover all three aspects: product, process, and context.

### A practical aside: is this email phishing?

A natural question that follows from the phishing-click metrics above is how to actually check whether an email is phishing. Here is the approach using Kali Linux.

First, save the email to your computer. Then pull the routing headers:

```bash
grep -E 'Received:|From:|Return-Path:' name.eml
```

You can locate the file with `ls -l test_email.eml`, get its full path with `readlink -f test_email.eml`, or search for it with `pwd` (to show the current folder), `find . -name test_email.eml`, or `find /home -type f -name '*.eml'`.

Then work through it. To extract every link from the message:

```bash
grep -Eo '(http|https)://[^"]+' /path/to/file
```

To check whether a tracking pixel has been embedded:

```bash
grep -E '<img.*src="http' /path/to/file
```

For something more interesting, take a look at RSPAMD — in many places the rspamd/SpamAssassin combination is the backbone of anti-spam checking. In Kali:

```bash
sudo apt install rspamd
sudo systemctl enable --now rspamd
```

And to analyze the message itself:

```bash
rspamc '/home/kali/test_email.eml'
```

### A practical aside: correlating domains

The SIEM discussion above turns on correlation rules; a related, hands-on question is how to find correlations between different domains. The `WHOIS` command will surface the servers a domain lives on. For Yandex, for example:

```text
nserver:  ns1.yandex.ru. 213.180.193.1, 2a02:6b8::1
nserver:  ns2.yandex.ru. 93.158.134.1, 2a02:6b8:0:1::1
```

Running `WHOIS` against those name servers in turn —

```bash
WHOIS ns1.yandex.ru
```

— helps surface related domains. And if a site will not open, `traceroute` shows at which hop the connection drops: unanswered hops are marked with `*`, which can be caused by a firewall, network loss, or a router failure.

## What to Pay Attention To

These are the key principles I hold to when dealing with hardened security specialists — the ones who always want to cut and forbid everything under the sun. Given their way, they would lock down every rule through Polkit or Active Directory. The gist is that security must not add time to a task or reduce the convenience of doing it. And you do not need too much security — it is a service function.

- **First, any product must be easy to install.** Usability and accessibility ease the adoption of, and compliance with, security measures. When security tools are convenient and accessible, people are more likely to start using them.
- **Second, usability.** If security measures such as 2FA are designed so that the user need not constantly interrupt their work, users are more likely not to resist them — for example, using passphrases instead of complex passwords.
- **Third, do not overdo access-policy configuration.** Usability takes priority when we are trying to balance security and ease of use.
- **Accessibility of materials.** All regulations and documents must be written in plain human language and be easily available to every employee; only then do we have the right to require employees to understand security protocols and follow them.
- **Account for existing behavior.** Any change must take the organization's existing behavior into account.
- **Copywriting.** Presenting security information in the organization's own language, rather than in technical security jargon, makes it more convenient and accessible to employees.
- **Remove barriers.** Before rolling out a change, think about removing "capability barriers" — the factors working against changes meant to improve security behavior.
- **Variety of access methods.** Offering different access methods ensures that everyone can interact effectively with security systems, including those with special needs — among them older people.
- **Reduce effort.** Simplify security behaviors by reducing the complexity and the number of required steps.

As a UX practitioner, I never blame users. But I do strive to educate them. Here the users are the company's employees, and communicating with them regularly is a mandatory part of the information-security team's job. Your security people, too, should not suffer from the "curse of knowledge"; they should communicate without jargon and without complex technical nuance. Do not ask employees to memorize NIST — it is too technical. A good example is the English-language [plainlanguage.gov](https://plainlanguage.gov/). Bear in mind, too, that poor usability creates threats: after you impose a rule that locks the screen after a minute of inactivity, users will start making artificial mouse movements. And even if your company's security is perfectly usable, employees will still make mistakes — just less often.

I understand perfectly well that criminals attack people first and technology only second. And the security folks hate losing control by giving employees freedom. It is always about finding the balance. Simply throwing a security course at employees and expecting flawless adherence to the rules is nonsense: the mere possession of knowledge after a course does nothing to change behavior. If you want users to report phishing emails, at the very least add a "report phishing" button to their email client.

## A Note on Behavior, Triggers, and Nudges

And a little about behavior. Reduced to its simplest, it is about pressing the right buttons and not pressing the wrong ones. For the right button to be pressed, the user must have motivation (the desire) and ability (the necessary rights); then the coveted button gets clicked. There is one more component — the trigger. The term "trigger" was originally applied to people with PTSD who could not control their emotions and behavior; in their case the trigger could be any stimulus — a sound, a smell, a set of words. So a trigger is a kind of external stimulus. The commonly cited "triggers" in interface work should be treated as hypotheses — and tested.

A trigger should be timely; it should not hang on every screen as a notification. A trigger can be any signal, instruction, or some internal realization on the user's part. A person's individual characteristics bear on this. Berger wrote that our perception is shaped by age, sex, sexual orientation, race, faith, linguistic traditions, civil status, and ideology. There is, again, a methodology for this — it is called SCENE, whose main goal is to involve stakeholders in creating and evaluating behavioral incentives. To work out which nudging mechanisms to use, SCENE draws on the MINDSPACE framework.

---

<sub>Translated from the Russian original by Maksim Tsvetkov. The author's three reader Q&A replies — on inspecting suspected phishing emails, on indicators that raise an individual's surveillance-targeting profile, and on correlating domains via WHOIS/traceroute — have been woven into the relevant sections above. The duplicated item in the "Understandability and trust" checklist is reproduced as in the source.</sub>
