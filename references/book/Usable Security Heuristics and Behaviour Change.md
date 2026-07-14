# Heuristics for Usable Security
### On changing behavior instead of just handing out rules

*Maksim Tcvetkov · Product Design and Security Lead*

Creating a need, changing people's behavior and their habits, helping them build motivation — this is one of the jobs of a UX specialist. And the moment we're handed the task of changing employee behavior to fit a company's security requirements, the first thing that comes to mind is to throw some courses at the staff and follow up with a test phishing campaign. But it isn't that simple. Let's work through it.

We want to change how employees behave, and to do that we first have to understand what behavior we're actually after. In other words, we need to define goals — and they should be as concrete as possible rather than vague, written out along SMART lines. Usually what interests us are short-term goals with a positive outcome, judged on results rather than on formal metrics. Most important of all, goals are built up from a risk assessment, which works across three dimensions: severity, frequency, and criticality.

The best-known model of behavior change — more than forty years old now — is the transtheoretical model. It runs through six stages describing an employee's evolution from unaware, unwanted behavior to the behavior you want:

- **Precontemplation** — doesn't want to change, and is unaware of the consequences of the current behavior.
- **Contemplation** — wants to change within the next six months, and knows both the upsides and the difficulties of the process.
- **Preparation** — is getting ready to change within the next month and has taken preparatory steps: joined support groups, found a coach, bought the books, subscribed to the channels.
- **Action** — has made noticeable changes over the past six months.
- **Maintenance** — sustains the new habits.
- **Termination** — has no desire to return to the old ones.

There are other models too — the information–motivation–behavioral skills model, say, or the behavior change wheel and the COM-B model.

But all of them share a set of key principles, and without grasping those you won't change anyone's behavior. People, for one, are usually all too willing to trade away security for a reward, or simply for convenience. Take a requirement to create a strong password of at least *n* characters. Simple and clear enough, on the face of it. But a given person may have trouble with memory or eyesight, and after ten attempts to invent a strong password they may simply lose their temper — and hand the job off to their child. In my experience, password managers and easily memorable passwords lead to better overall protection. Encouraging passphrases instead of complex passwords is what strikes the balance between security and ease of use.

There is no universal model. Every company is unique and calls for its own approach. Laws, regulations, and social and cultural values all bear heavily on security practice, and paying close attention to how those factors get interpreted will teach you a great deal about why certain security behaviors come to dominate an organization.

My own model of behavior change looks like this:

1. **Pin down the problem.** What are we trying to solve, and what behavior are we aiming for? You start by observing the current behavior, analyzing it, and building a vision of the new behavior on top of what's already there.
2. **Context and environment.** Look more closely at the current behavior; study the likely effect of your intervention; account for cultural context and the fine points of usability. And always account for employees' limits in skill, motivation, and knowledge.
3. **Build the solution.** It might be nothing more than talking to employees and identifying the security champions among them.
4. **Apply the solution** — with metrics.
5. **Determine the impact and effect** of the intervention.

The work usually throws off a whole set of artifacts, which I sort into these groups:

1. Conceptual (ideas, hypotheses)
2. Procedural (instructions, methods)
3. Regulations
4. Empirical data (observations from research)
5. Directive (how to reach a specific result)
6. Facts
7. Incentives

Any security-management model is made of people, processes, and policies. The diagram below illustrates a typical security-management decision flow. On its left sit the three principal inputs to a security-management decision: (i) regulatory data, covering laws, rules, and values; (ii) business risks, covering the cybersecurity risks to the company's strategic goals, brand, and reputation; and (iii) operational risks, covering the cybersecurity risks that arise from using, managing, and deploying technology. These inputs are assessed and prioritized by a committee of the organization's stakeholders, and the resulting prioritization and risk assessment produce the security-management processes and technologies shown on the right.

![Security-management decision flow](https://your-scorpion.ru/wp-content/uploads/2024/07/Frame-1000006119.png)

My favorite part of the diagram is the operational risks. Assess those, and it becomes easy to articulate the problems tied to everyday human–computer interaction. When individuals and groups don't use security controls as intended, that can be a sign of a mismatch in security priorities.

## Heuristics

A heuristic is a mental shortcut — you could call it a checklist of ready-made rules for judging an interface, an idea, a piece of code. A great deal has already been worked out for us. Before you rush off to run yet another study, generate hypotheses, and hire an agency for the entire annual budget, for instance, you could simply read the usability criteria in ISO 9241-11.

The first block of information-security interface heuristics is about making everything clear to the user, because clarity is what builds trust.

1. The user can always reach security-relevant information.
2. The system notifies the user of relevant changes to security measures.
3. The system directly informs the user of any mandatory security actions they must take.
4. The system offers several levels of security detail, where it's used by beginners and experienced users alike.
5. The system shows only the security information needed for the current activity.
6. Security-relevant information is concise, easy to understand, and descriptive.
7. It is stated clearly what the user's personal information is used for.
8. The system informs the user when it is in an insecure state.
9. Security terminology is expressive and consistent.
10. Security terminology conforms to standards.
11. The system reports progress when a security-related function introduces delays.

**Authentication.**

1. Authentication is a precondition for reaching protected or confidential sections.
2. The user can choose among different authentication options.
3. The authentication options are designed so as not to place an extreme cognitive load on users.
4. The system enforces a strong password policy that is nonetheless simple to understand.
5. The user can change their password.
6. The system states the password policy at the moment the user sets a password.
7. By default, the system never displays the password.
8. The system tells the user whether authentication succeeded.
9. The system locks out repeated failed authentication attempts after *n* tries.

**User control and freedom.**

1. The user must accept the terms of use and the privacy policy.
2. The user must confirm any transfer of data to third parties.
3. The user can choose between an icon and a text display of security-status information.
4. The system provides shortcuts for frequent tasks.
5. The user can configure their security preferences.
6. The user can undo their security-related operations.
7. The user can reverse security-related operations they themselves initiated.
8. The user must confirm actions with drastic, negative, or irreversible consequences.
9. The user can update or delete personal information.
10. The system implements "security by default" and "privacy by default."
11. All privacy settings apply across the entire system.

**Error recognition, diagnosis, and recovery.**

1. Error messages always point to the cause of the problem.
2. Error messages tell the user how serious the problem is.
3. Error messages include information about the action the user should take.
4. Error messages are written in human language.

**User support and documentation.**

1. Reaching the documentation doesn't require suspending the task at hand.
2. Help and documentation follow the sequence of the user's actions.
3. Users are informed of updates to help and documentation.
4. Help and documentation are reachable from every screen.

**Accessibility.**

1. The system allows graphical passwords for users who have difficulty reading text.
2. The system allows text passwords for visually impaired users.
3. The system is designed for people with a wide range of needs.

We can't skip past Nielsen's heuristics. Yes, they're dated and can feel obvious, but they're the base layer everything else sits on. **Visibility of system status** wants clear indicators when a threat or breach is in play. **Match between the system and the real world** means the interface speaks a familiar language, with warnings and prompts a non-specialist can follow. **User control and freedom** turns on how much say people have over their own security settings and configurations. **Consistency and standards** asks that security protocols hold steady across platforms and devices. **Error prevention** is about the ways a system keeps users from making the mistakes that would compromise them in the first place. **Recognition rather than recall** is the discipline of minimizing cognitive load without giving up safety. **Flexibility and efficiency of use** means meeting users at whatever level of knowledge they bring. **Aesthetic and minimalist design** is a reminder that how the interface looks shapes engagement and perception. **Helping users recognize, diagnose, and recover from errors** calls for clear instructions and real support when something goes wrong. And **help and documentation** means usable docs and support channels a non-technical person can actually reach.

## Metrics

We need metrics. Above all, we only ever count them relative to a specific environment. A good metric has a quantitative expression — not "high" or "low." The number of blocked connections or attacks, say; the number of devices with out-of-date antivirus definitions; the count of security-related queries. The percentage reduction in security risk, on the other hand, is a bad metric — even if some SIEM happily reports it. A good one is something more granular, like the resilience of a system to XSS before and after taint checking. There's a list of them at [securitymetrics.org](https://www.securitymetrics.org/), and I usually try to write metrics like these into the SLA requirements.

You can't rely on observation alone; for cybersecurity it's a very limited approach. Ideally, metrics should be free of expert opinion. And sending staff into every office to gather the data can prove expensive for an international corporation — with the added risk that people will warn each other the observers are doing another round, urging colleagues to lock their screens, hide their flash drives, briefly come back into the office from remote work, and peel the password sticky-notes off their monitors before the observer reaches their desk. Outsiders assessing change can also make employees feel awkward and watched as they go about their ordinary work. Still, pairing observation with self-reports can smooth over some of the shortcomings of both methods, for the cybersecurity behavior that technical tooling can't measure directly.

Since users are almost certainly interacting with some system, you can automate the collection of certain data — the percentage who click phishing emails, and, if they open one, whether they then entered sensitive data or launched the attached file. How much time passed between opening the email and running the attachment? Another metric is how many times the system locked itself because the user was inactive. Let's take one metric apart in a little more detail — an event in a SIEM, say.

**Structure of the source data**

- What the dump is based on
- How much of it is synthetic
- How it was synthesized
- Median (1Q) message size
- What stream was generated, and how it was fed into the SIEM (pushed onto the bus, or collected by agents)

**Structure of the platform**

- How many servers
- Their technical specifications
- How they're connected, and by what (the network)

**Content**

- How many normalization rules there are
- How many normalization rules actually fired during the test
- The average number of fields normalized across all rules (otherwise you can write a rule that normalizes a single field and show off insane performance)
- How many enrichment rules there are
- How many enrichment rules use table lists
- The average volume of data in the tables (in bytes and in rows)
- How many correlation rules there are
- The average depth of the rules (the depth of the event chain)
- How many correlation rules use tables
- The average volume of data in those tables (in bytes and in rows)
- How many correlation rules fired
- How many correlation rules generated incidents

**Storage**

- Whether raw events were stored, or only normalized ones
- Whether storage was configured to a database, or simply to /dev/null
- Whether the database was empty at the start

If metrics this intricate don't yet match an organization's level of maturity, there's always the universal trio: effectiveness, efficiency, and satisfaction.

**Effectiveness** is whether people can successfully reach their goals — whether an employee can quickly and easily report a phishing email to the cybersecurity team, for instance. The psychological dimension matters here too: I've noticed that when a user knows their computer is being monitored, it affects how effectively they get their tasks done. Remember that, for an employee, thinking about security is only ever a secondary task.

**Efficiency** is the resources — time, cognitive effort — spent reaching those goals: how long it takes an employee to authenticate into an application, say. As a rule, people find it easy to make an equivalent one-way choice between two or more options — install the update or don't. But when the security guidance is contradictory, no one is going to follow it — rules on sharing your password with colleagues, for example.

**Satisfaction** is the intersection of a user's physical, cognitive, and emotional responses to a system, product, or service, along with how well their needs and expectations are met. In a cybersecurity context, satisfaction can be dented by the irritation of running into the same security warning over and over. As for the good examples of usable security: two-factor authentication. If the code arrives quickly and the token gives you no trouble, security has improved. Allowing password managers — good. And two-factor authentication (2FA) with Face ID helps lower the cognitive effort.

That way we cover all three aspects: product, process, and context.

## What to watch for

These are the principles I hold to when I'm dealing with the grizzled security specialists who always want to cut and forbid everything under the sun. Left to their own devices, they'd lock down every rule through Polkit or Active Directory. The gist is that security shouldn't add time to a task or make it less convenient to complete. And you don't need too much security — it's a service function.

- **First, any product has to install easily.** Ease of use and accessibility make security measures easier to adopt and to follow. When security tools are convenient and reachable, people are more likely to start using them.
- **Second, usability.** If measures like two-factor authentication (2FA) are designed so the user doesn't have to keep interrupting their work, they're more likely not to push back — passphrases instead of complex passwords being one example.
- **Third, don't overdo the access-policy configuration.** Usability takes priority when we're trying to balance security against simplicity of use.
- **Accessible materials.** All regulations and documents should be written in human language and be easy for every employee to reach; only then do we have the right to demand that employees understand the security protocols and follow them.
- **Account for existing behavior.** Any change has to take the organization's existing behavior into account.
- **Copywriting.** Presenting security information in the organization's own language, rather than in technical security terminology, makes it more usable and more accessible to employees.
- **Remove the barriers.** Before rolling a change out, think about removing the "capability barriers" — the factors working against changes meant to improve security behavior.
- **A variety of access methods.** Offering different access methods ensures everyone can interact effectively with security systems, including people with particular needs — the elderly among them.
- **Reduce the effort.** Simplify security behaviors by lowering the complexity and the number of steps required.

As a UX specialist, I never blame users. But I do aim to teach them — and here the users are the company's employees. Communicating with them regularly is a non-negotiable part of an information-security professional's job. Your security people, likewise, shouldn't suffer from the "curse of knowledge"; they should talk without the jargon and the intricate technical detail. Don't ask employees to memorize NIST — it's far too technical. A good example of the right register is the English-language [plainlanguage.gov](https://plainlanguage.gov/). Keep in mind, too, that poor usability creates threats: bring in a rule that locks the screen after a minute of inactivity, and users will start generating artificial mouse movements. And even if your security is perfectly usable, employees will still make mistakes. Just less often.

I understand perfectly well that criminals attack people before they attack technology — and when the targeting is deliberate, it keys off behavior far more than anything technical. The patterns that draw the attention of an APT are mostly behavioral: being a high-profile person; visiting a particular religious institution more than two hundred times a year; using a VPN; sending or receiving religious texts over SMS or social media; trying to reach social networks after they've been blocked at the national level; turning up at protests. None of that is about the hardware — it's about what the person does. The security crowd, for their part, hate ceding control by giving employees freedom, so the whole thing becomes a search for balance. Simply throwing a security course at your staff and expecting flawless adherence to the rules is nonsense; the mere fact of holding some knowledge after a course changes behavior not at all. If you want people to report phishing emails, then at the very least put a "report phishing" button in their mail client.

And if you want to see what checking a message actually looks like, rather than leaving it an abstraction, the mechanics are unglamorous. Save the email to disk — I'll demonstrate on Kali Linux, where I generated one — and read its headers with `grep -E 'Received:|From:|Return-Path:' name.eml`, since that's where the true origin tends to give itself away.

![Reading the email headers with grep](https://your-scorpion.ru/wp-content/uploads/2025/05/аф.png)

If you've lost track of the file, `ls -l test_email.eml` locates it and `readlink -f test_email.eml` gives its full path; failing that, `pwd` shows the current folder and `find . -name test_email.eml` or `find /home -type f -name '*.eml'` will turn it up. Then pull every link out of the message with `grep -Eo '(http|https)://[^"]+' <path to file>`, and check whether a tracking pixel has been planted with `grep -E '<img.*src="http' <path to file>`. For something more interesting, take a look at RSPAMD; in a great many setups the rspamd/SpamAssassin combination is the backbone of anti-spam checking. On Kali that's `sudo apt install rspamd` followed by `sudo systemctl enable --now rspamd`, and then you analyze the message itself with `rspamc '/home/kali/test_email.eml'`.

![Scoring the message with rspamc](https://your-scorpion.ru/wp-content/uploads/2025/05/dasdasads.png)

Once you have the domains a message points to, WHOIS turns a single lead into many: it surfaces the nameservers a domain lives on — for Yandex, `ns1.yandex.ru` and `ns2.yandex.ru` — and running WHOIS against those nameservers in turn helps you find the related domains. And when a site won't open at all, `traceroute` shows you the hop where things fall over; the missing replies come back marked with an asterisk, which can mean a firewall, packet loss, or a router failure.

Now, a little about behavior itself. Stripped down, it's all about pressing the right buttons and not pressing the wrong ones. For the right press to happen, the user needs motivation (the desire) and ability (the necessary permissions); then, and only then, does the coveted button get clicked. There's one more component — the trigger. The term was originally applied to people with PTSD who couldn't control their emotions or behavior, and for whom the trigger might be any stimulus at all: a sound, a smell, a set of words. A trigger, in that sense, is an external stimulus. The commonly accepted "triggers" for working with interfaces are best treated as hypotheses — and tested as such.

A trigger has to be timely; it shouldn't hang on every screen as a notification. It can be any signal, any instruction, some inner realization on the user's part — and a person's individual characteristics shape all of it. Berger wrote that our perception is influenced by age, sex, sexual orientation, race, faith, linguistic traditions, civil status, and ideology. And, again, there's a methodology for this. It's called SCENE, and its central aim is to bring stakeholders into the creation and evaluation of behavioral incentives. To work out which nudging mechanisms to reach for, SCENE draws on the MINDSPACE framework.
