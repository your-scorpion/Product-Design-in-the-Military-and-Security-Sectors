# Choosing the Right UX Research Method for Cybersecurity Products

*Maksim Tcvetkov · Product Design and Security Lead*

Before you choose a research method, choose a goal. Every study needs one, and that goal is a hypothesis. Skip the hypothesis and you'll pick the wrong method, write the wrong test tasks, and miss the one finding that actually mattered.

One hypothesis is never enough. Running a full study to test a single guess is slow and expensive, so you need several — and each one has to be sharp, not vague. "Users are more comfortable looking at pictures of kittens" is not a hypothesis, it's a feeling. More comfortable than *what*, exactly? A real hypothesis resolves to a clean yes/no in a qualitative study and to a number in a quantitative one. And above all, it has to matter to the business. If you're building a tool for designers, there's no point testing whether they'd like cooking-discount notifications.

It's also worth remembering that you're not only testing an interface — you're sometimes testing the respondent's own skill level. Their familiarity with other interfaces, or their grasp of the profession itself. A low-skill specialist can still know one vendor's console inside out. And what does it actually tell you if someone understands the standards stack — ISO 27001, ITIL, COBIT? CEH, OSCP, and COBIT are notoriously vague about what they actually certify, while ISO 27001 is much more precise. Does a PCI DSS or SOC 2 audit trail say anything real about an individual's competence? Worth deciding before you write a single screener question — because how you screen respondents is itself a research design choice.

## How do you know a hypothesis is any good before you've spent a dime testing it?

The simplest gut check: your hypothesis shouldn't be a moonshot. If it's unattainable, no amount of research will validate it — you'll just burn a cycle proving what you already suspected. For an early-stage effort, seven solid, validated hypotheses are usually enough to build a business case on. The reasoning behind each one tends to follow a chain: assume a need exists → confirm the problem is real and see what's wrong with current solutions → hypothesize who the ideal customer is → define why your solution beats the alternatives (this is where a value proposition canvas earns its keep, mapping the customer's jobs and pains against what your product actually delivers) → hypothesize what the solution needs to include → settle on a monetization model → and finally, will anyone actually pay, and how much?

A useful filter on top of that chain is SMART, adapted for research hypotheses rather than corporate goals:

- **Specific** — where exactly are we going, and what are we trying to achieve? "Test a new acquisition channel" is vague; "test whether channel X converts at a target rate" is specific.
- **Measurable** — what result counts as good? If you're running new outreach campaigns, what's the number — leads, sales, activation — that tells you it worked?
- **Attainable** — is this realistic given your actual capacity? "Launch campaigns across every customer segment tomorrow" isn't a hypothesis, it's a wish list.
- **Relevant** — does it serve the business goal? Testing a channel that brings ten visitors a month, to a tiny, low-paying segment, isn't worth the calendar space.
- **Time-bound** — every hypothesis needs a deadline. Break big ones into smaller ones; a one-to-two-week validation cycle is a healthy default. Do the arithmetic: at roughly one hypothesis validated per week, across 248 working days a year, that's about 35 shots on goal — and if three or four of them land, that's a normal, healthy year, not a failure.

Because a full validation cycle is expensive, it helps to know which layer of the product a given hypothesis actually belongs to — Value, Feature, Design, or Feasibility — and to prioritize across a simple impact/effort matrix rather than testing everything with equal urgency.

## Finding respondents

You can always find respondents for free or for very little money, but quality will swing wildly. If you already have a base of users to survey, Google Surveys (not to be confused with Google Forms, a different product entirely) will draw you clean, presentable charts once responses are in. There's also cint.com, a mid-quality panel aggregator — convenient, but you pay a premium for the convenience.

Free respondents are, almost by definition, not the unemployed or people with nothing better to do — but in cybersecurity specifically, you won't run into that problem at all; at most you'll be talking to students. For working security professionals, the conversation itself can be the incentive: a chance to compare notes, hear what the market is doing, and pick up something new. It's not beneath you to buy a stack of Starbucks cards out of pocket and hand them out. You can also offer to credit the respondent publicly as one of the experts who consulted on the product. Bolder incentives work too — introductions to other security people, a podcast slot, certificates, course access, a seat in a private respondent community.

Every respondent-sourcing channel has a catch. The big one with free channels: if you don't already have a respondent base, you're recruiting cold — posting survey links in public groups and forums, pinging friends, seeding chats. Gizmo, which I recommend to students without reservation, has a built-in quality checker for exactly this problem: it flags respondents who answer in a suspicious "staircase" pattern, or far too fast to have actually read the question. If you're surveying an international audience — Southeast Asia, South Asia, LatAm, MENA — you're down to two options, neither free. You simply won't find enough local chats, groups, and forums to blanket twenty countries with a free questionnaire, so it's either targeted Facebook ads or a paid panel like Qualtrics, where the going rate is roughly $3–5 per respondent.

Beyond ad-hoc panels, there are agencies and there are paid online panels — pre-built pools of people who've already opted in to paid research for a small fee. Respondent.io serves mostly the US market: you register, define your screening criteria, attach a card, and get a shortlist of matching respondents to review, message, and schedule directly. The interview happens through the platform and the charge goes through automatically — typically $50–100 per person, plus roughly 50% on top as the platform's cut. Toluna is another option, and for the CIS market, Tiburon/Fastuna is a reasonable place to start.

Contrary to what you'd expect, finding respondents with genuinely strong English is hard even in Europe. That means there's a real risk of losing nuance, or misreading it outright, if you're running interviews in English as a non-native speaker on either side — language isn't just grammar and vocabulary, it's culture. I'd strongly recommend bringing in a native speaker to help. Agencies can source local freelancers, or a simultaneous interpreter if you need one live. For translating my own question scripts into English, I use OneHourTranslation, and then have a native speaker check the result before it goes anywhere near a respondent.

## The interview: your default method

If you only have budget for one method, make it this one. A one-on-one deep interview — anywhere from twenty minutes to six hours — answers more questions than almost anything else and forms the foundation everything else builds on. What you're after isn't facts; it's how someone *interprets* facts, and what they feel about the subject.

A deep interview, sometimes called exploratory research, happens before a project starts, and there's no reason the respondent has to be local — you can run this over video with people anywhere in the world. It also pairs unusually well with usability testing in the same session. Focus groups across different user segments are worth running too, especially when you need to get inside a product's problem space quickly. And if you're trying to capture someone's in-the-moment reaction to a new feature rather than their retrospective account of it, a diary study is the right tool.

**Before the interview even starts, things can already go sideways.** You greet the respondent and thank them for coming, and that's usually where the first surprises show up. In 2020 I had a five-year-old scheduled for an interview; before we'd even begun, he grabbed the consent form and started filling it in — upside down. Another time, a respondent walked through a metal detector, emptied his pockets of keys and a phone, and it turned out afterward he'd forgotten to also take out his gun.

Once the session starts, my first job is making sure the respondent is actually comfortable. Introduce yourself, explain how the session will run, and — gently, without turning it into an interrogation — ask about their role and experience; letting people talk about their own expertise early tends to relax them into feeling like the expert in the room, which they are. Flag that you're recording, and ask if they have questions before you begin. NDA terms get a separate mention of their own, with a signed confidentiality agreement.

### Writing questions for a semi-structured qualitative interview

Rule one, and the most important one: ask *why* more than feels natural.

> Why do you order food delivery?
> Because I don't want to carry heavy grocery bags home.
> And what's the real reason behind that?
> Because my wife and I agreed on it.
> Why?
> We don't like cooking at home, we don't like grocery shopping, and we can't carry heavy bags.
> Why?
> Because we get home late, and by then the good stuff at the store is already picked over. In summer, the fish salad we like is starting to turn on the shelf, so we'd have to buy raw ingredients and cook from scratch, and there's no time for that when you're getting home late.

That's how you dig down to the actual fact underneath the stated reason. The technique comes from Toyota, built specifically to trace root causes. If you ever go blank mid-interview and don't know what to ask next, cycle through **What–How–Why** in your head; once you have answers to all three, you can move on to the next beat in your script.

A second Toyota example makes the chain even clearer: *Dead battery? → Why? → Alternator isn't charging it? → Why? → Problem with the alternator belt? → Why? → It hadn't been replaced in a long time, even though it started out fine. → Why hadn't it been replaced? → The car hadn't had a service in a long time.* Five "whys," and you've landed on the real root cause — it was never the belt, it's that someone needs a service reminder. Five is roughly the ceiling; push past it and people get irritated. And "why" itself can read as confrontational, so it's often smoother to approach the same ground through "what" and "how."

As a rule, "why" surfaces abstractions, while "how" gets you specifics. "Why" is about defining the problem — finding the point of pain. "How" is about finding a solution once you know what you're designing for.

**What** is about the concrete details of the moment: what is the person doing, in what context? Good answers here are full of adjectives — "which," "what kind," "whose" — so push for as much specificity as you can get.

**How** describes the manner of doing it: is the person putting in a lot of effort? Frowning or smiling while they work? Reaching for extra tools to make the task easier? Try to capture the emotional texture of the task, not just the mechanics.

**Why**, finally, is where you interpret. Based on what you observed in the "what" and "how," try to understand the emotional drivers underneath. English-language literature on this often tells you to *guess* at this point — but that's not quite right, and I'd push back on it.

If you're asking about something routine, simple direct questions are fine: what sport do you play, which car brands do you trust? But for something more specific and not top-of-mind, you need cued recall — questions built around concrete evidentiary detail rather than an open prompt. For example: when people look at a dashboard, they scan different chart types — critical incidents, threat graphs, active tasks, recent events, vulnerable assets. *Which of these widgets do you actually reach for in your own work?* is a cued question in that spirit.

Watch out for future-tense questions, direct yes/no-flavored questions ("do you like this idea?"), and closed questions generally — none of these belong in your original interview plan. One caveat: closed questions are fine later in the same script, specifically to narrow down a line of reasoning the respondent has already opened up — and if your hypotheses genuinely call for a closed question, that's a defensible reason to include one.

On future tense specifically: ask someone if they'd like a self-refilling water glass, and they'll say yes. Ask if they'd actually buy one, and the answer flips to no. People want to look better than they are, so they answer in whatever way makes them look good — and this effect gets sharper, notably, with technical guys being interviewed by women researchers.

### Why interviews are especially good for security research

**One reliable way to keep answers honest on sensitive ground** is to reach for projective questions — ones aimed at other people's behavior rather than the respondent's own, so nobody has to defend themselves directly. *Why do you think people leave jobs that aren't even bad? What does it take to stay relevant in this field today? What motivates an adult to keep learning? Why do people chase career advancement at all? What's a fireable offense on day one?* — questions like these let people project their own experience onto a hypothetical third party, which is exactly the point. Don't fire them one after another, though; weave them into the script gracefully, spaced among more neutral ground.

Interviews are a great fit for security research specifically because the field runs on accountability and inheritance. Inheritance is about large, interconnected systems and integrations; accountability is about event logging — knowing whether information was actually compromised. That need is what gave rise to security testing and penetration testing as disciplines in the first place. Five basic parameters run through the profession: threat, asset, vulnerability, control, and risk. A threat can be active (an attack in progress) or passive (a piece of ransomware exists somewhere in the world and could, in principle, reach you). With security properly architected, passive threats get deflected before assets are ever exposed. An asset is anything measurable worth protecting — financial records, user data, network paths. Attacks themselves split into those with feedback and those without: feedback means gaining database access and executing a command; no feedback means, for instance, a logging gap that never surfaces at all.

### Testing expertise, not just opinions

This is also where you can settle the skill-level question raised earlier: how do you actually probe whether a respondent knows their stuff, rather than just how confidently they talk? The right question set depends on context, but a few hold up well across most: What is the Cyber Kill Chain, and can they explain the framework, not just name it? What does "scanning" actually cover during reconnaissance — HTTP headers, open ports, WAF fingerprinting, Shodan, infrastructure mapping, WHOIS, DNS servers, reverse-IP lookups, Geo-IP, robots.txt, credential leaks, ICMP behavior, DNS records, HTTPS quirks, exposed files, user enumeration, software fingerprinting, or just public breach data? What's the first phase of recon, and can they name the tooling — Recon-ng, for instance? What does Shodan already know about your organization before you've done anything, including leaked employee data? If a leak already exists and can't be scrubbed from the internet, what's actually left to do — misdirection, mostly. Can they tell a honeypot apart from deception technology more broadly? A good closing exercise: hand them a scrambled matching task — Devices/MDM, Data/SSO, Networks/CASB, Workload/Compliance, People/Log — and see if they pair category to control correctly. For breadth, you can even probe how far their curiosity extends technically — directional microphones, active vibro-acoustic protection, that sort of thing.

Security specialists, like most experts, love talking about themselves and their own experience — lean into that. You'll learn how someone gets by *without* your product; maybe inventory at a plant still gets tracked by hand, on paper. Don't mention your own product, or even the idea behind it, until the interview is over. At the very end, ask: "Is there anything I should have asked but didn't?" — this works once you've actually built rapport. A second closer, "Who else should I be talking to about this?" often surfaces new audiences you hadn't considered — managers, sysadmins — just make sure you leave with their contact details. If you want to go deeper on this whole approach, *The Mom Test* is the book to read.

The most common question patterns, in order of how you'll actually use them:

- **Opening:** "Can you walk me through the last time you…?"
- **Probing:** "Can you tell me more about…?"
- **Clarifying:** "What exactly were you feeling in that moment?"
- **Direct:** "Have you worked with Metasploit before?"
- **Interpretive:** "Am I right that what you're feeling is…?"
- **Structured/closed:** "Let's go back to X," "Let's move on to Y."

How would you get someone to articulate, unprompted, that a vulnerability is really just the opportunity to execute a specific attack scenario? Or that organizational defense is a real, distinct concept? Sometimes the honest answer is: you just ask, directly, mid-conversation.

**A short exercise worth trying on yourself.** Take the sentence "An attacker gained access to the director's new phone." Buried in there are several separate facts: the director has a new phone, the attacker has access, the director has a phone at all, and the attacker did something bad. They read as equally solid, but they aren't.

Run a negation test: *"An attacker did **not** gain access to the director's new phone."* Now it's genuinely unclear whether the attacker did anything bad at all, or even tried. That's called **implicature** — the inferences a sentence invites beyond its literal content.

But notice what doesn't budge even under negation: the director still has a new phone. That's a **presupposition** — a given, not a claim up for debate. Reword again: *"There's no way the attacker could have gained access to the director's new phone,"* and you can pull out four presuppositions: the director has a new phone; the attacker has no access to it; the attacker couldn't do anything bad; the director has a phone, period. None of that is actually in question anymore — and that's exactly the mechanism behind a manipulative question. "Why does everyone on the market choose Avast?" leaves no room to doubt that Avast is impressive, even though it opens with "why." Same with "what are the three reasons you like this interface?" — you can feel the question leading you by the hand.

Compare these two:

1. "What do you see as the main value of this product?"
2. "Does this product have any value to you at all?"

The second one is closed, and it's still the better question. In a real script, you'd want to swap their order — ask the closed, harder-edged one first.

Interviews work best when you're hunting for the *reason* behind a behavior, starting from the respondent's actual problem. Talk like a friend, not like someone running through a checklist of "hey, I want to get a couple of facts from you about why you did that." Build empathy first. Ask for real examples from their life, and stay neutral while they talk — this is what's usually called a *problem interview*.

Sometimes it's worth bringing two or even three respondents into the same session — these are called dyads and triads. Ideally the respondents talk to each other as much as to you, and you harvest whatever useful thinking comes out of that exchange.

**Once a prototype exists,** you come back to interviewing — but now it's a solution interview (CustDev), aimed at early adopters around an actual MVP. The question shifts from "is this a real problem" to "does this solution fit, and how do we improve the MVP." This phase leans commercial. A problem interview gives you a set of insights; you build against them, then return to the *same* respondents to validate the fix. Those people can easily become your first customers — if the product genuinely solves their problem, the interview itself can double as your first sale. The whole approach sits inside human-centered design, which every flavor of design thinking is ultimately built around.

So, to recap the three interview modes: a **deep interview** surfaces insights around a single theme by watching how the respondent actually operates and what they need, with heavy emphasis on rapport and trust. A **problem interview** tests a specific hypothesis about a difficulty you suspect exists. A **contextual interview** is about watching how a product actually gets used — hard to describe in words once a workflow has become automatic for someone, so you have them walk you through it in the moment instead, staying firmly in the here-and-now, no future or past tense.

Strung together, the process looks like this: hypothesize value → interview customers to confirm it → segment and sharpen the value proposition → model the economics → build an MVP → run solution interviews and land the first sales.

In JTBD terms, an insight is structured as situation → motivation → expected outcome — also known as a job story. I usually simplify it to two things: the task/need, and the problem/difficulty. From there, I log each insight in Airtable with a structure like this:

- **Observation:** Wants visibility into the product roadmap
- **Team:** Front/Back
- **Level:** Hygiene
- **CJM stage:** Step 3 — sprint planning
- **Type:** Problem
- **Segment:** Armenia office staff
- **Votes:** 4

### On tools and technique

Record every interview. An interview you didn't record is an interview you didn't run. Bring a recorder, take notes by hand too — and go back and re-listen; you'll catch your own mistakes and adjust before the next session. If you don't have time to transcribe for clustering later, outsource it — plenty of services like zapisano exist for exactly this.

Storage is its own decision. I'd point you toward Dovetail — it exports cleanly to CSV, though its Miro integration is weak, which is a real gap. For transcription specifically I use Trint, mostly for data-handling reasons around confidentiality, though it comes with its own constraints. With fewer restrictions, fireflies.ai, otter.ai (it struggles noticeably with some African accents), toloka.ai, and Descript are all worth a look. I've recently been using otter.ai for its automatic transcription straight out of Zoom cloud recordings — genuinely useful. Glean is worth a look too, and a well-maintained internal wiki does more heavy lifting than people expect.

## Jobs to Be Done: why context beats demographics

There's a familiar failure mode in research: someone goes off to run a study, comes back, and announces that "there was a pandemic and a wave of unrest, so people will probably spend more online and governments will invest more in social programs." Interesting, maybe — but what is the product team actually supposed to *do* with that? Jobs to Be Done exists to fix exactly this gap between a macro observation and a decision someone can act on.

Clayton Christensen, the Harvard Business School professor who authored the framework, put it this way: most companies segment their audience by user or product characteristics, but the customer doesn't see the market that way at all. They simply have a job to be done, and they go looking for the best available product to hire for that job. Christensen introduced the idea in *The Innovator's Dilemma* and developed it further in *The Innovator's Solution* — chapter three of the latter is the primary source, and job stories are the piece of it we care about most here.

My own working definition: JTBD is the process of pursuing a goal under a specific set of circumstances — and understanding that process is what lets you predict why people would hire *your* product specifically to do the job. It's a structured way of describing reality that supersedes the older user-story format, which centered the user; a job story centers the task instead — the situation someone is in, what motivates them to act, and what outcome they expect. Formally, a job story is:

- The **situation** the problem showed up in;
- What the person believes needs to happen for the problem to go away (**motivation**);
- What they get once it's actually solved (**outcome**).

Example: *When I land on an unfamiliar online store and something about it feels off, I want to know whether I can trust it — otherwise I won't hand over my card details.*

JTBD is specifically about context. I didn't buy an iPhone with more storage because I'm a particular age, from a particular background, with a particular outlook. I bought it because I shoot a lot of video and needed it to actually fit on the phone.

Another angle on the same idea: satisfying hunger at a shopping mall is a job, and McDonald's is who you hire for it. Discovering the finer notes of French cuisine is a completely different job, handled by a Michelin-starred restaurant. Impressing a date is one restaurant; feeding your coworkers is a different one entirely. That's the whole logic of JTBD in a sentence: how well does your product actually do the job the user hired it for?

Kaspersky builds what is, by most measures, the best antivirus engine in the world, and customers hire Kaspersky to close a need for security. As designers, it's worth entertaining the uncomfortable version of that thought: what if security just... became a given? No hackers, no viruses, no industrial espionage. At that point you're no longer competing with Kaspersky on team size or technology — you're competing with them indirectly, on the job itself disappearing. That sounds abstract, and it is — JTBD by design operates at a very high level, without much granular detail. It earns its keep when a company is entering a market at a meaningfully higher price point with something genuinely new, one that erases an old, painful task rather than incrementally improving it. AirPods didn't out-feature wired earbuds; they eliminated the tangled-cable problem entirely, which meant the Core Job — "listen to music from my iPhone" — simply got done better.

That reframing changes how you see the competitive landscape. A French restaurant and a pizzeria in the same neighborhood: are they direct competitors? Research might reveal that people duck into the pizzeria for something fast on their way to work, in which case your real competitor isn't the restaurant across the street, it's a Snickers bar. JTBD lets you think about competition at the level of the job, not the product category.

Why not just use personas, then? Because age, gender, and habits don't explain why someone ate a candy bar. Having thirty spare seconds and needing to not be hungry for the next half hour does explain it. This is the idea of a **Core Job** — the job a business gets hired for at the most fundamental level. For a supermarket, that's "stock up for the week" or "save money via discounts." If you already have a working business, the highest-leverage research move is talking to people who've already bought your product and asking what job they hired it for — then building an atlas of those jobs and prioritizing against it.

**A short note on JTBD versus personas.** JTBD answers who-what-how through a single formula:

> When _____, I want to _____, so I can _____.

Context, motivation, outcome. *When I'm bored, I want to be entertained, so I open Facebook and look for cat gifs.* "When ___" is the situation, "I want ___" is the motivation, "so I can ___" is the outcome.

A persona, by contrast, reads like: *"Vladimir from Vorkuta, 27, married with three kids. Extraverted, teaches English, warm and open, always smiling. Weekends he's running in the park, taking the kids to the playground, swimming. Once a quarter he grabs a beer with friends."*

The JTBD version: *"Vladimir was bored, so he bought a Netflix subscription."* None of the persona detail actually drove that purchase. He just wanted to be entertained — the person's identity barely enters into it. What matters is the situation someone's in, which is really a nod to a different school of psychology entirely. It would be tempting to conclude personas are now obsolete, but that's not the relationship between the two — they complement each other. My own readers, for instance, are wildly different: some employed, some students, some looking to switch careers or pick up freelance work from a beach in Bali. As personas, you'd all look pretty different from each other. Russian Post, meanwhile, covers nearly the entire population of Russia — hugely varied personas, but one job. Once a product already exists and is doing a job, it's worth layering personas back on top — two axes of segmentation, really: role (driver, passenger, dispatcher) and job.

Or: a mom in the US posts a photo of herself grilling barbecue, and a teenager in Thailand posts a photo of himself fishing. Same underlying job, wildly different personas. The takeaway: personas *bound* your audience; JTBD is about the motivation, personas are about the roles people occupy while pursuing it.

JTBD isn't flawless, though. A persona is a realistic sketch of one representative member of your audience, carrying every trait that shapes how they relate to the product. People want fun, so they buy a game — but introverts buy a solo game and extraverts buy a party game. Persona work is really empathy work, a necessary ingredient for messaging, positioning, and brand voice. JTBD's own weak spot: once the job is done, the product's role is done too, so you have to be deliberate about the workflow your product creates and how you move a user along a CJM. A CJM lets you mark exactly where your product's journey starts and ends, and what came before and after — which keeps a product usefully narrow, competing well in a defined space instead of trying to cover everything and drifting into territory it doesn't need to own, while still knowing where users come from and where they go next.

A more everyday example: I spot what looks like a gorgeous BMW X6 on the car-share map near my apartment and sprint over — only to find it's some cheap hatchback instead. That's a broken expectation, but in JTBD terms: what job was I actually hiring "BMW X6" to do? Personas build a realistic picture of your audience; JTBD is about fit between that audience's problems and what you deliver. What people want ends up mattering more than who they are.

That's JTBD, in short. Where it earns its place for product designers specifically is in writing interview questions — phrasing them so you reliably land an answer that fits: *When _____, I want to _____, so I can _____.*

A caution on the output of that interview, though: it's not enough to learn that a user wants to find the cheapest flights *fast*. "Fast" and "cheap" are already constraints smuggled into the finding — and fast-and-cheap can't, by definition, also be good. Same trap with weather apps that lean hard on precipitation volume, barometric pressure, and a dozen forecast variables nobody asked for — the actual question users are trying to answer is something as plain as "will it rain?" or "how warm will it be?" Customers, as a rule, rarely buy what the company thinks it's selling.

A more advanced cousin of JTBD is Goal-Directed Design, which leans harder into motive and environment — what actually creates the need for a product — where JTBD stays centered on the product and the job it performs.

There are four forces that either push someone toward a purchase or hold them back:

- **Current situation (Push):** "My mattress is uncomfortable. I wake up several times a night with back pain."
- **Pull toward a new solution:** "If I got a new mattress, I'd sleep better — better mood at home and at work."
- **Anxiety and doubt:** "What if the new mattress turns out just as bad? I can only try it for a few minutes in the store."
- **Attachment to the status quo:** "I've had this mattress since college."

Those are offline examples; here's the same structure online:

- **Push:** "We want to process user tickets faster than we currently can. Our current tool is limited and expensive."
- **Pull:** "The new tool promises to be a lot better — faster ticket handling, and by extension, more revenue."
- **Anxiety:** "What if the new tool doesn't integrate the way we need? We've already tried three other tools for this and none of them were good enough."
- **Attachment:** "We already have our workflows and integrations set up, and reconfiguring all of it would be painful."

Here's the real question underneath all four: when do people actually enter active shopping mode, and what makes them willing to abandon a familiar product for a new one? People are deeply inertial and will keep using a product they're used to even when it's clearly not the best option, so your messaging needs to isolate the pain of the current product and lean on it hard:

1. Show exactly how bad the current product really is.
2. Show how yours solves the same problem better.
3. Shrink the doubts around switching — make the switch itself as painless as possible.
4. Loosen the attachment to what they're already using.

Apple's "I'm a Mac" campaign is the textbook case: it isolated Windows's pain points (1), showed off how good the Mac experience was (2), demonstrated how easy switching would be (3), and cast holdouts as figures of fun — deliberately loosening their attachment (4) to old habits.

The standard artifact for capturing all of this is the JTBD Statement. Read down one axis, it maps jobs and the specific situations that activate them; read across the other, it maps the artifacts describing motivation within one situation, the decisions made, and the requirements attached to them — which then flow into Job Stories or value propositions tying everything back to the product and its messaging.

Good questions for filling in that kind of table are always about switching between products, or about how a product is actually used — grounded in real use cases, not abstractions:

- What problems and needs does the audience actually have?
- Who are our real competitors?
- How do users compare us against them, and why do they land on the choices they make?
- Which needs are we meeting — and which are we not?
- How should the product evolve to deliver more value and beat the competition?
- How do we get our value, and specific features, across to the audience?
- How do we improve the experience of actually using the product?

One more thing worth knowing: people systematically overvalue what they already have by roughly a factor of three. Which means your new product doesn't just need to be a little better — it needs to be three times better, or positioned as though it were. A 10% improvement won't move anyone.

### The one place "the user's task" isn't quite the whole story

It's tempting, once you've absorbed all of this, to conclude that the task a user is executing is the only thing that matters, and everything flows from there. That's close, but not quite right. JTBD, at a deeper level, sits inside William Powers's hierarchy of goals, where a goal splits into "being" and "doing." A useful worked example: the top-level **Be goal** might be "make people like me"; the **Do goal** underneath it is "tell good jokes"; and the concrete **motor-control goal** underneath *that* is "memorize jokes." The point of the hierarchy is that the specific tasks and subtasks matter far less than the goal they're in service of — you can absolutely make people like you without being especially funny, and you can tell a joke well without having memorized a library of them. It's even worth asking whether "make people like me" is the right goal to chase in the first place, or just a proxy for something more honest. All of it still maps onto the standard JTBD formula: *When <situation>, I want <action>, so that <outcome>.*

A second, related tool worth having on hand is **Impact Mapping**, which reduces to four questions: **Why** — what's the actual problem we're solving? **Who** — whose behavior are we trying to change? **How** — what behavior change, specifically, would move us toward the goal? And **What** — what are we actually going to build or do about it?

## Surveys: the fastest quantitative method

The simplest and quickest method in the whole toolkit, and purely quantitative. You build a questionnaire and push it out — no shortage of tools to do that with. The usual suspects: SurveyMonkey, SurveyGizmo, Google Forms, Typeform. There are free alternatives too, if budget is tight — LimeSurvey and the French Framaforms are both genuinely free, and plenty of paid tools offer a free trial worth exploiting, Phonic.ai among them. Decipher (now under FocusVision) and Qualtrics sit at the more serious, paid end. There are also opt-in panels like Yandex.Vzglyad, which show your questions to people before they watch a video on Yandex.Video. In my own work I reach for Qualtrics and Fabuza fairly often, and LimeSurvey for smaller projects, though it does need to be self-hosted.

**Sample size** follows ordinary statistics around confidence intervals: 250 respondents gets you a ±5% margin at 90% confidence with a 50% baseline split. Always assume only 10–15% of the people you email will actually complete the survey (it varies with audience loyalty, sometimes lower, sometimes higher), so back into your send size with a simple formula: **send size = target number of responses × 10**. If your audience isn't especially loyal, send to the largest list you can.

Yandex.Vzglyad and Yandex.Toloka can source respondents on your behalf — for a fee — with some control over audience quality and targeting. Toloka lets you build a full survey; the international equivalent, UserCrowd, lets you tack open-ended questions onto the end of a closed-question flow. Show someone an image, ask how "friendly" the interface feels, and you immediately run into a wording problem: "friendly" means different things to different people, so you need to pin down what the respondent actually means by it. The same goes for any generalization — "always," "never," "everyone" — and any vague noun — "love," "fear," "help," "solution" — and any vague adjective — "quality," "worthwhile," "nice," "good." All of them need to be pinned down or they'll just reflect back whatever the respondent already believes.

Don't forget age either — if your interface is about investing in US equities and your respondent pool is school-age, your results are going to be shaky no matter how well-designed the questionnaire is.

A few hard rules for questionnaire design:

- Never let the respondent see the next question in advance.
- Open with closed questions.
- Closed-question answers belong on buttons, not text fields.
- Never write a leading question — "we'll solve your problem, right?" gets binned on sight.
- Long surveys don't get finished. Nobody's making it to question forty.

That last point deserves its own structure. I default to:

0. A headline that actually grabs attention.
1. A greeting that states how long the survey takes, why it exists, promises confidentiality, and dangles whatever incentive is on offer.
2. Questions about the product itself.
3. Demographics, plus the incentive payoff.

Demographics go last because most people simply won't answer age and gender questions, and if those questions come first, people bail before they've told you anything useful. Partial-response capture helps here — some paid platforms save whatever a respondent entered before they dropped off, though Google Forms and Typeform don't, so an incomplete session there is just gone.

Avoid double-barreled questions too — "what's your age and which city do you live in?" is genuinely hard for automated analysis to parse cleanly, and respondents tend to only answer half of it anyway.

**Checking your results afterward:** did they actually fill in the open-ended fields, how long did each question take, did they select the same option — "A," always "A" — straight down the page. Some platforms build this checking in for you: the longer someone spends on a single question, the more that particular answer tends to skew dishonest.

Surveys are also good for surfacing baseline expectations of a product. Take CVSS scoring — a vector reads something like *Medium 4.5, CVSS:3.1/AV:P/AC:H/PR:L/UI:R/S:U/C:L/I:N/A:H*, where every letter encodes a specific dimension of threat. Does that notation actually work for practitioners, or does it need a more visual treatment? You won't know until you ask.

There's a formal vulnerability-description standard too — OVAL, a standardized language security scanners use to describe what they find. Scanners themselves vary wildly: free versus paid, CVE-aware or not, OS-only versus broader coverage, GNU GPL-licensed components, Metasploit or NVT integration. Does the scanner auto-remediate (a workaround), suggest mitigations to blunt impact, pull vendor patches automatically, or simply flag that nothing's going to get fixed? A survey is a good way to map the full universe of functionality your users actually expect — but it helps to have already done your homework on the popular scanners first: XSpider, Nessus, Scanner-VS, OpenVAS (via Kali), ScanOVAL, Vulners, RedCheck.

The US market has its own dedicated survey infrastructure. Amazon Mechanical Turk is the best-known, and doubles as a respondent-recruitment channel. Prolific, Condens, Dovetail, EnjoyHQ, and MaxQDA round out the space, and MyGiftCard covers incentive payouts with proper documentation. For the Russian market, Anyfield is worth a look.

Rating scales come up constantly, usually a 1–5 scale or a full Likert scale. For instance: *rate how much you liked the incident graph, on a nominal scale* — very much, somewhat, neutral, not really, not at all. VTsIOM's phrasing conventions are worth stealing from, even though they get it wrong sometimes too. More examples:

> How recently have you analyzed your infrastructure against the latest MITRE ATT&CK version? — Today, for the first time / Less than a week ago / Less than a month ago / 1–3 months / 3–6 months / 6–9 months / 9 months to a year / 1–3 years / More than 3 years / Not sure.

A dichotomous nominal scale works too: "which email client does your organization use — 1 = Outlook, 2 = BlueMail." Average the responses and a result of 0.52 reads directly as 52% of respondents using Outlook.

Once the survey closes, pull everything into Excel and clean it up: strip out anyone who gave nonsense answers, and drop, wholesale, anyone who failed a trap question.

### Measuring loyalty, not just satisfaction

A survey can also carry more weight than a single project — it can track loyalty over time, and that's a fair question to ask up front: which metric actually moves customer loyalty and satisfaction? The right approach differs for B2B and B2C, but a solid, general-purpose option comes from Sean Ellis: a **PMF (product-market fit) survey**. Sean has an official site where you can send the survey directly, though I'd rather build it in whatever survey tool the rest of this section already covers — Typeform, for instance. The structure runs:

1. How did you hear about [product name]?
2. How would your life change if you could no longer use [product name]?
3. What alternative would you use instead of [product name]?
4. Have you recommended [product name] to anyone?
5. What's the main benefit you get from [product name]?
6. Who else might get value out of [product name]?
7. How could we improve [product name] further?
8. Would you mind if we came back to you for follow-up on your answers?

Always capture geography and buying habits alongside it. Export results to CSV, load them into pandas, and go to work. Ideally you're running this automatically every quarter, tracking sentiment drift over time, backed up with a handful of ad-hoc customer calls to sanity-check what the numbers say.

A second, complementary metric is **RFM analysis** — Recency, Frequency, Monetary — which segments customers by actual purchasing behavior:

- **Loyal customers:** R = 4–5, F = 3–5, M = 3–5
- **Loyal, but still shopping around:** R = 4–5, F = 1–3, M = 1–2
- **Buy anywhere:** R = 1, F = 1, M = 5
- **At risk, needs attention:** R = 2–3, F = 1–5, M = 1–5
- **Already gone to a competitor:** R = 1, F = 1–5, M = 3–5
- **Gone, and actively negative about you:** R = 1, F = 1–5, M = 1–2

In R, the pipeline looks roughly like this — load `data.table`, `dplyr`, `ggplot2`, `tidyr`, `rfm`, and friends, set an analysis date, run `rfm_table_order()` against your order data, define the segment boundaries above, then call `rfm_segment()` and plot the medians for recency, frequency, and monetary value across segments. Worth pairing with **NPS** (Promoters at 9–10, Passives at 7–8, Detractors at 0–6, giving you a score from –100 to 100) and **K-means clustering** for adjacent behavioral groupings. Layer RFM, NPS, and a Bayesian or neural forecasting model together and you've got a genuinely predictive churn signal: negative recency trend, likely churner; positive trend, likely loyal.

### Readability is not one metric — it's two

Question wording matters enough that it's worth being precise about what "clear" even means. There are two separate metrics hiding under that word: **Readability**, which is about word choice, and **Legibility**, which is about visual presentation — type size, letterforms, and so on. They need to be measured independently. For readability specifically, the Flesch–Kincaid readability tests (FRES) and the Gunning Fog index are both standard, well-validated tools worth running your survey copy through before you send it anywhere.

## Mapping the experience: User Experience Maps and CJM

A **User Experience Map** — often called *core experience* in the English-language literature — comes out of in-person interviews with people who recently solved a specific problem. The job is to find out how they solved it *without* your product, and visualize that. Where a User Experience Map is product-agnostic, a CJM is the same idea inside the context of a specific product, and it can take a few forms: *dream* (the ideal state), *to-be* (a realistic target state), and *as-is* (where things stand today).

A **Customer Journey Map** reduces a product's context to a simple sequence of steps. Building the map itself isn't the hard part — gathering the underlying data is. In a problem interview, you're digging for the barriers at every step of the journey and logging them straight into the CJM, which lets you see exactly where people hit friction. A CJM is really a record of user emotion as they move through a scenario; a good scenario-based interview digs into one specific step and surfaces its sub-steps.

The point of the exercise: track the quality of every interaction between the business and the user, surface every touchpoint across channels, and capture — then optimize — how people actually get things done with your product or service.

A CJM is a living artifact. Because it's trying to cover the entire experience, it's rare that a single study gives you the whole picture — that would be prohibitively slow and expensive. Which is exactly why I like treating the CJM as a working artifact inside the product team, something that gets refined continuously rather than delivered once and filed away. It's a shared artifact, too — researchers, PMs, and designers, everyone actively shaping the product vision, should have a hand in building it. Done well, it keeps the team anchored to real context, real tasks, and real pain — a coherent story, not a pile of disconnected features.

After your first round of interviews you'll have a rough CJM to work from going forward. From there it takes a lot of ethnography — observing, interviewing, documenting how the context of use actually shapes the experience. Slow, effortful work, and nearly impossible for a freelance researcher working solo; plain observation is the fallback.

If the data you have is thin, an imperfect CJM still beats no CJM — as long as you're honest with yourself that it rests on assumptions that need validating with real users, and you actually go validate them. The usual build order: construct a persona, map their journey, mark the drop-offs, note emotions, pain points, quotes, and channels. A CJM is fundamentally qualitative; ideally, you validate it quantitatively afterward. Quantitative CJMs do exist — UXPressia supports them — and a CJM, at its best, functions like an information radiator: a steady, passive, always-on source of shared context for the team.

### The wider CJM family — and a caution about generic frameworks

CJM has cousins worth knowing by name. **CDJ** (Consumer Decision Journey) tracks the communication touching a customer at every step of a decision. **User Experience Map / core experience**, as above, is how a user solves a problem *today*, without your product. **CEM** (Customer Experience Management) is the broader discipline — researching customers, designing their journeys, designing the communications along the way — usually backed by software that stores customer feedback about the company as a whole. Where CEM differs from **CRM**: CRM holds information about users generally, CEM is specifically about the feedback layer. One step past CJM sits **USM** (User Story Mapping).

Worth sitting with, though: all of the above are generic, universal strategic tools — and Hume's guillotine applies here as sharply as anywhere. A universal tool, by definition, doesn't get you to a specific answer. If you fill in any of these artifacts purely in "as-is" mode, you will never arrive at "as it should be." The fix is almost embarrassingly simple: add a "key conflict" section to whichever map you're using, with explicit quality criteria attached to it — that's usually the difference between a CJM that sits in a drawer and one that actually changes a roadmap.

## Focus groups

The advanced end of the qualitative toolkit. A focus group runs four to eight people; the sample should be homogeneous — one social layer — but without a single shared opinion on the topic at hand. Go much bigger than eight and the room gets unmanageable. Focus groups have a reputation problem, and the most common complaint is fair: participants influence each other, and an opinion leader emerges almost instantly. That's a moderation problem, not a fundamental flaw in the method. The moderator needs to hold something like a parent's role in the room and never, under any circumstances, react to what a respondent says — the moment you do, you've contaminated every answer that follows.

As one example: a focus group I ran surfaced an audience segment I hadn't accounted for on a social platform — people running multiple accounts specifically to track a partner's activity, or to keep music tastes in different genres separate from each other.

There are situations where this method genuinely doesn't work — an audience that spins up an opinion leader almost instantly, for example. In security research specifically, that's manageable enough through moderation that focus groups remain a perfectly viable tool.

## Usability audits — and the trust layer underneath them

An expert review, or heuristic evaluation. An audit's job is to surface everything getting in the way of a site being usable, clear, and actually convertible into a purchase — and it usually needs access to the analytics stack (Google Analytics, typically) to say anything meaningful. Nielsen's heuristics alone don't cut it anymore.

In a security context, part of usability is trust — specifically, how a user can trust a site enough to not become a Man-in-the-Middle victim. That trust is anchored by certificates, and not all certificates are equal. **Domain Validated (DV SSL)** only confirms domain registration — genuinely weak assurance. **Organization Validated (OV SSL)** is a step up. **Extended Validation (EV SSL)** is the strongest tier, reserved for financial transactions — which is exactly why large retailers redirect checkout flows to dedicated payment pages rather than handling it in-line. The attacks that exploit weaker assurance go by names like SSL Strip and SSL Split: network-layer attacks dressed up as ordinary browsing — hijack the certificate, splice into the session, and the attacker is off to the races. The same MITM pattern is possible over WPA2, too.

## Desk research

Also called secondary research, and this is usually where people who don't do research for a living get stuck, simply because they don't know where to start digging. In practice, a plain web search covers most of it. Beyond dedicated site- and trend-analysis tools, subscribing to the major news and analyst outlets in your space goes a long way, and — for an international market — government statistics are worth checking directly: population, category spend, that kind of thing. The catches: the data ages fast, it's slow to gather, and sometimes the specific number you need just doesn't exist online.

Some things, though, are genuinely well suited to desk research — vulnerability scoring among them. **CVSS 3.0** gives you a quantitative severity rating alongside CVE identifiers; qualitative scoring is closer to an expert-assigned label — critical, high, medium, low — or the simple red/yellow/green treatment that most vulnerability scanners already use to flag severity at a glance. Quantitative risk assessment gets considerably harder once you're modeling something like business continuity.

Desk research doubles as a fast way to pick up the field's vocabulary — exploit (a tool for exploiting a vulnerability), payload, zero-day, advanced persistent threat, watering hole. That last one is worth defining since it's rare enough to be unfamiliar even to some practitioners: a watering-hole attack is when an attacker has already breached a perimeter and uses that foothold to seed additional vulnerabilities elsewhere across the IT landscape — building something extra into an already-compromised environment.

### What to actually ask when you're researching the market

Specific questions should always tie back to the type of business you're in, but as a general starting kit: Who is our ideal customer? What do they actually want, underneath what they say they want? What sets us apart from competitors? What advantages do our customers actually perceive, versus the ones we think we offer? Who's buying from us right now, and why isn't everyone else? Who might buy from us in the future? What value or need are existing customers actually satisfying by buying from us? What would make us the obvious choice of brand? What's the best way to reach the people we're trying to reach? What could we improve about the product or service to broaden its appeal? What's the right price point? What's the long-term vision for the brand, and how do we actually get there? Where does the brand need to evolve to avoid stagnating or turning into a copy of someone else? What adjacent products or services could we bundle alongside what we already offer?

For secondary research at the US market level specifically, the Pew Research Center's surveys, the Bureau of Labor Statistics, and US Census data are all worth going to directly rather than through a summary.

## Feature tables

A comparative table — one sub-method inside the much larger competitive-analysis family. Feature names run down the rows, products run across the columns. Simple to describe, genuinely useful to actually build.

## Usability testing in the lab

Here you've got an actual usability lab: interviewing a user while simultaneously testing the product live. You're not limited to your own product either — running the same script against a competitor's product surfaces its weak points just as well. Build the script around your actual goals: uncovering motivations, the criteria people use to choose a product. Every task in the script should ladder up to the macro-goal of understanding how the product is actually perceived.

Preparation matters more than people expect: define your core questions and scenarios ahead of time and structure them properly. Stay locked onto each participant's own experience — don't let them speculate about how *other* people might behave, or how they themselves might behave in some hypothetical situation. Above all, go in knowing the subject cold; it's genuinely difficult to improvise good follow-up questions mid-session if you don't already understand the domain.

Always run a pilot on internal experts first. Does the overall task logic hold up? Do any tasks need rewording? Is the wording actually landing the way you intended? Are respondents giving you real, developed answers, or one-word non-answers?

Questions split into three types:

1. Closed, yes/no.
2. Leading, with the answer half-embedded in the question.
3. Open and unambiguous, where the person tells you a story.

A typical session runs about ninety minutes — long enough that I'm hesitant to call it a "deep interview," even though usability testing plus interviewing together often gets lumped under that label anyway.

Keep the development team well away from the respondent. One of my more memorable moments running an international session: the dev team, watching from behind the glass, broke into applause when a respondent successfully completed a task. Don't let that happen.

## Field research: watching security work happen in the wild

**Contextual inquiry** is field research aimed specifically at generating ideas. You go to someone's actual workplace and watch how they interact with something. It's a genuinely good way to understand where vulnerabilities in software actually come from, just by observing specialists and asking questions as you go — usually you're looking at coding errors like buffer overflows, weak or default configuration, hardware-level architecture quirks (Meltdown is the canonical example), or **XSS**, where an attacker injects a malicious script into a page. Counterintuitively, XSS is a client-side problem, not a server-side one — browsers ship with essentially no default XSS protection: Firefox needs a NoScript-style plugin bolted on, and Chrome actually removed what little built-in protection it had. XSS isn't always about stealing data either; plenty of the time it's ad injection, or simply a foothold into a corporate network.

Attacks split, classically, into active and passive. Active is the dangerous half — code lands on the server, cookies or form data get stolen, and every visitor to the site becomes a victim on contact. Cookie theft alone covers the entire top five of the OWASP Top Ten. Passive attacks are the quieter cousin — a malicious link, spam distribution, that category of thing.

Say you already have penetration-testing skills and can genuinely break software. As a researcher, you might get embedded with a Red Team and watch how they hunt for threats and write up hardening recommendations for a client's infrastructure. Watching isn't enough on its own — keep asking questions throughout, because people consistently do things other than what they'd tell you they do. Watch, ask, document. A few days embedded in a compressor plant, or watching someone attempt to compromise the automated control system on a gas-pumping unit, is the kind of unforgettable experience that gets you fluent in the domain fast. Gas drying and purification systems, uninterruptible power supply chains, sensor-based gas-quality monitoring, even something as mundane as fire safety and perimeter security — all of it is exactly the kind of hands-on exposure that industrial control system interface design actually requires. Any thermal or hydro power plant, any SCADA server, is generating millions of measurements a second, and every one of them eventually needs to render as something a human can actually read.

I work across international teams and travel for fieldwork fairly often, and as unglamorous as this sounds: it's usually smarter to hire a local agency in whatever country you're doing field research in, and take a back seat as the primary researcher yourself. Cultural fluency takes real time to build and doesn't come from search engines. The one exception I've found: English-speaking Germans are genuinely researchable solo, without a local agency in the loop.

### A day in the life

Take a corporate phone as the research object: how does someone actually live with a smartphone day to day? You shadow them for the whole day, like a satellite, and just watch. You're unlikely to stumble into a disaster-recovery scenario this way, but the ordinary, everyday tasks are exactly what you'll catch.

A more advanced version of the same idea: shadow a penetration tester and watch how an attack actually gets built, step by step — how they hunt for weaknesses at the application layer. A network-level compromise opens the door to attacking the architecture or the application itself, brute-forcing weak IoT credentials, fingerprinting insecure protocols, and so on — even hunting for hardware-level weaknesses. It's a far more interesting lens than the textbook version of "an attack": breach the corporate network → establish persistence → cause damage.

Obviously, what you're observing here is ethical hacking specifically — the kind of work certain teams do under an R&D mandate. The broader taxonomy includes black-hat, gray-hat, and white-hat practitioners, and there's a whole discipline of corporate forensics that grew directly out of cybercrime investigation. Mapped onto OWASP's framing, this is penetration testing as a way of surfacing network- and system-level vulnerabilities specifically.

**What about malware that behaves like a virus but technically isn't one?** They exist, and Denuvo is the go-to example — an anti-tamper, anti-piracy layer that operates at Ring 0 (kernel mode), which means it has access to essentially everything on the machine it's installed on, exactly like the malware it's often mistaken for.

Classically, attacks split again into passive and active. Active attacks directly interfere with a system's operation. Web-application attacks follow the same split — passive attacks hunt for vulnerabilities, active attacks go a step further and try to actually cause harm. Either can compromise the integrity or confidentiality of an information asset, and the fallout usually lands in one of three buckets: denial of service, information disclosure, or integrity violation. Every version of it, ultimately, comes down to gaining access to information that shouldn't be accessible.

### Empathy research: going hands-on yourself

Sometimes the researcher steps directly into the security specialist's seat and earns the experience firsthand — which obviously assumes you already have the domain knowledge and qualifications to do it credibly. Where that's not realistic, interviews, embedded observation, or really any way of getting out of the office and into the field serve the same empathy-building purpose.

For hands-on security work specifically, Parrot Security OS is a solid starting point — it ships with a full set of pre-installed utilities, nothing to chase down or update separately. Any penetration test, without exception, happens only with signed client consent and on client-controlled infrastructure. Kali or Parrot plays the attacker's machine, alongside XAMPP, all running in a VM — 2GB of RAM and two CPU threads is a workable minimum.

You'll want two target machines as well: Metasploitable and bwapp. The latter is a purpose-built lab for working through the OWASP Top Ten, alongside alternatives like RingZer0 and testphp.acunetix.wain. Set it all up, and you get a real, hands-on feel for what the job of a security specialist actually involves.

The researcher obviously needs to understand the domain, or none of this will make sense in the moment — plenty of exploits today are trivial enough to run against real services with barely any skill required. Still, real comfort with Kali Linux matters; it was designed from the ground up as a complete security-audit distribution. It bundles categories for reconnaissance, vulnerability analysis, web-application analysis, database and password attacks, sniffers and spoofers, exploit frameworks, and even social-engineering tooling. A minimal pentest lab is really just two machines — attacker and target — and a setup of around a dozen VMs can simulate almost any modern office network. Install Kali and Metasploit as your baseline.

**Metasploit** is the pentester's workhorse — a C2 framework in the same tier as Mythic and Sliver — handling the bulk of scanning, exploitation, and payload work. Functionally it's about automation, built from REX, MSF Core, and MSF Base. Two flavors exist: an open-source edition and a commercial one with a considerably more polished interface. Metasploit ships inside Kali already; `msfconsole` from the terminal is all it takes to launch it. From there you've got the full tool belt — `ifconfig`, `ls /usr/share/metasploit-framework/modules/` to browse available modules, and `ls /usr/share/metasploit-framework/modules/exploits` to jump straight into the exploit catalog.

### Observation and shadowing, applied to recon

Plain observation, applied to security work, still counts as observation — recorded web sessions are a legitimate observational artifact too, with a few security-specific wrinkles worth knowing.

The typical process: any reconnaissance starts with scanning. When an attacker probes a service, the flags that come back reveal how correctly it's configured — and from that, the attacker infers your actual security posture.

Before you shadow a "day in the life" of a security specialist, it's worth brushing up yourself. At minimum, run `nslookup` against a target domain; for something lower-effort, sitereport.netcraft and Robtex both work, with Robtex giving you the deeper dataset of the two. Check SPF records specifically — without one, a domain has no protection against unauthorized senders spoofing mail from it; an SPF record is what establishes that a given domain is actually authorized to send mail at all.

Top-ten-style attacks rarely target a company's primary domain directly — subdomains are the usual target instead. Sublist3r is the standard tool for enumerating them, and large organizations tend to have an enormous, under-monitored subdomain footprint, any one of which can be the weak point. From there, attackers typically run a "fitting" attack like phishing — sites like emkei.cz let an attacker send mail that appears to come from anyone, and disposable-inbox services like Guerrilla Mail make setting up a temporary receiving address trivial. The email body itself is where the actual injection usually lives.

## Comparative and quantitative testing

**Monadic testing** — you show one version of a design and the respondent evaluates it in isolation. That's actually closer to real life than it sounds: if you show someone a hero illustration on a landing screen, that's the only version they'll ever see, so ask how it lands on its own merits.

**Dyadic testing** — show two versions side by side and ask which one they'd pick. The catch: you might be asking someone to choose between two options that are both bad.

**A/B testing** — a genuinely quantitative comparison of two variants, with real statistical machinery behind it if you want to go deep on the math.

**Analytics platforms** let you spot negative trends early and track them with real rigor, unit economics included.

Analytics, over time, also tells you things you wouldn't get any other way — in my own research, C++ and iOS consistently come out as the languages most resistant to OWASP-style attacks, with PHP sitting stubbornly near the bottom of the safety ranking. OWASP itself is the consortium that catalogs security issues and publishes the guidelines around them, including a standing set of penetration-testing measures — their Top Ten list specifically is worth knowing cold.

## Co-creation: building the fix alongside the experts

**Co-creation** is a sub-method inside the broader *ideation* family from design thinking. You bring the people who actually have the problem into a working session and build the solution together. It doesn't have to mean building a full MVP — card sorting or tree testing is often plenty.

You can also go considerably deeper: take a specific, real case and work through it with domain experts, surfacing the difficulties and designing around them together. **Fuzzing** is a good example of a case worth co-designing around. File fuzzing is the simplest form — a program is handed a malicious file and has to open it. Fuzzing itself splits into generation (feeding in random byte sequences) and mutation (perturbing an otherwise valid file). Network protocols mostly rely on mutation alone — either corrupting traffic outright or making surgical changes designed to overflow a buffer. Metasploit's fuzzing module set is fairly thin; you can browse what's available with `use auxiliary/fuzzers/`, then target something specific, e.g. `use auxiliary/fuzzers/ftp/client_ftp`, point it at a target with `set RHOSTS 192.168.0.xx`, and `run`. This is the foundation most DLC-style testing is built on.

Launching an actual attack runs through a **shell** — client half, server half. Shells split into subtypes, including reverse shells that ride out over the outbound-traffic port. **Meterpreter** acts as the shell payload of choice, and thanks to DLL injection, leaves essentially no trace. **PassiveX** is another technique, built to slip past outbound-traffic firewall filtering, alongside NoneX, ORD, and a long list of others.

**Injections** are still the most common vulnerability class — database errors, blind injection, boolean-based injection, and time-based injection. The interesting design question is: how do you actually prevent SQL injection, and what would make that easier for the specialists doing the work? An expert's instinct, drawn from real experience, is to reach for parameterized queries only where genuinely necessary, and fall back to strict input validation everywhere else — alongside real fluency with sqlmap. There's also the "magic quotes" approach: `magic_quotes_gpc` in PHP, which escapes quote characters and blocks a subset of injection techniques outright. Trimming unused functionality helps too, if only because it shrinks the attack surface.

None of that is bulletproof, though — attackers route around it with fairly simple transformations: a broken-up tag like `<scriPt>`, a trailing null-byte injection (`%00`), inline comment tricks (`/*!SELECT*/`), or splitting a keyword apart (`sel<ECT`). A step up in sophistication: fragmented queries, buffer-overflow attempts, or IP-reputation-based evasion.

Injection itself comes in a string flavor —

```sql
SELECT * FROM table WHERE example = 'Example'
```

— and a numeric flavor —

```sql
SELECT * FROM table WHERE id = 334
```

Weak authentication and session management is the next layer down. Authentication, at bottom, is identity verification, and it's usually attacked through social engineering or brute-forcing rather than anything exotic. The standard defenses are straightforward: strong password policies, lockouts after failed attempts, IP-based rate limiting, hashed session IDs. CAPTCHA feels like the obvious next step — but a security expert will immediately point out that CAPTCHA itself needs protection against automated brute-forcing, and if you're generating CAPTCHAs on your own server, that server now needs DDoS protection too. So: do you actually need a CAPTCHA? And if you're specifically the target of a well-resourced, persistent adversary, very little of this stack will save you regardless. For testing and reconnaissance on the certificate/config side, SSLLabs is a solid, purpose-built tool.

**Concept validation**, or a **Proof of Concept (PoC)**, is really MVP-building applied to a technical hypothesis — a good way to confirm, concretely, that a microservices architecture has introduced new security exposure. Establishing trust between microservices, plus the architectural assumptions required to implement that trust, adds up to real security gaps if you're not deliberate about it.

**Corridor testing** — build a rough prototype, walk into the nearest hallway, and ask two questions of whoever you run into: *what do you see, and what do you understand?*

### Pulling it together: pairing methods and running the process

To recap: understand the need → choose a research method, or more often a pair of them, which is genuinely rare in practice but worth the extra effort. If two independent studies both confirm the same hypothesis, defending the finding afterward gets dramatically easier. Some pairings that consistently work well:

- Desk research + stakeholder interviews
- Surveys + focus groups
- Co-creation + a standard interview
- Field research + ethnography
- Usability testing + think-aloud protocol
- Expert review + analytics
- A/B testing + expert review

The overall process, end to end: design the study → decide how you'll capture notes → run it → gather results → tag the results → walk the findings past stakeholders and align on them → prioritize what you found → write it up. Along the way, frameworks like the Design Thinking Wayfinder or the Double Diamond are both worth leaning on.

## Diary studies

Diary studies sit at the advanced end of the method spectrum. They're slow by design: participants live with your product in their own natural environment over an extended period, checking in periodically with reports and questionnaire responses. The payoff is that you can track how perception of a product shifts over real time, without your own presence as a researcher distorting the context of use. Studies run anywhere from two weeks to two years, with reporting cadence anywhere from daily on up. Reports can be text, voice, or short emotional video clips, and they're excellent raw material for building a CJM afterward. Most people simply don't recall negative or positive moments accurately after the fact, even from the recent past — that's the core weakness of relying on interviews alone — so a diary study's real value is catching feedback at the actual moment it happens. Layer in product analytics alongside the diary reports and cross-reference the two, and you can build a genuinely strong CJM out of it.

You're not pinging participants daily to remind them they're being watched — instead, you're tracking their activity and prompting for feedback at each meaningful milestone. How often a report gets filed depends entirely on what you're studying: a sales agent handling dozens of customer interactions a day needs frequent same-day check-ins, while someone in the middle of a months-long apartment search needs far less frequent prompting. Interviews bookend the study at the start and the end, but nothing stops you from running one in the middle too, especially if the reports themselves start looking thin.

The underlying logic: a cashier or a taxi driver runs into a constant stream of different situations on the job. If you're studying how people file a complaint with a government agency, you talk to people who've filed one recently. In security specifically, this method lets you track which tools got used in which situation, and why — what the fallout of an incident actually was, or exactly where a product failed to meet expectations in the moment.

Aim for at least ten participants, assuming they all report on time, which doesn't always happen. It's worth mixing people who already have experience with your product alongside people who've only used competitors' products. For diary studies specifically, I use Fabuza — participants install a browser extension, and recording kicks in automatically the moment they land on the target site, which I can then review as the researcher.

## How much time to actually budget

Hypotheses can absolutely come straight out of the backlog — a task to improve some existing piece of functionality. Inside a two-week sprint, you can reasonably carve out one to five days for research — half the sprint — which is plenty for straightforward usability testing of a prototype against real users.

A genuinely new feature needs five to ten days, or a full sprint on its own. That's room for competitive analysis specific to the feature, interviews, enriching the CJM, and usability testing — ideally run inside a Dual-Track Agile setup.

A service redesign is two sprints, roughly twenty days. That calls for real market research, competitive analysis, user flows, a wave of prototypes, a new UI kit, and testing across multiple user cohorts.

A new product is four sprints, roughly forty days. Early on, that means in-depth interviews with the business itself, competitive analysis, persona construction, building a CJM from scratch, prototyping, and testing. To actually build empathy at this stage, interviews with domain experts, observation in context, competitive analysis, and surveys are all close to mandatory, not optional.

## Closing: what "it's working" actually looks like in security

Every method above eventually points back to the same question: how do you know any of this is actually working? For a security product specifically, that comes down to picking the right operational metrics and being disciplined about how you calculate them. A reasonable starting set:

- Mean time to detect an incident
- Mean time to remediate
- Patch-management coverage
- Vulnerability-scanning coverage
- Risk tolerance
- Risk-assessment coverage
- Risk-treatment-plan coverage
- Security-testing coverage

None of these are self-explanatory the moment you try to actually calculate them, though — it's worth thinking through the math before you commit to reporting on one. Take "unwanted network traffic" as a worked example: at a high level, it measures illegitimate traffic — the ratio of malicious, spam, or unauthorized traffic to total network traffic. A high value there points to a heavier-than-normal presence of malicious actors or compromised systems on the network. Spam specifically is simple to compute: spam volume divided by total mail volume, tracked weekly, monthly, quarterly, and annually.

That's really the throughline of everything above: pick the right method for the question you're actually asking, be precise about what a hypothesis needs to say before you go test it, and once you ship something, hold yourself to metrics specific enough that you can't quietly redefine "working" after the fact.
