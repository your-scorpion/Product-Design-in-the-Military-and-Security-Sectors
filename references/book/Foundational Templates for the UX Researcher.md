# Foundational Templates for the UX Researcher

*Maksim Tcvetkov · Product Design and Security Lead*

Every research effort, however ambitious, resolves into four movements: define the goals and objectives, prepare and recruit, run the study, then analyze and synthesize. And like any honest article on the subject, this one has to open with the tedious part — understanding the problem. It is tedious precisely because it is where most projects quietly go wrong. Get the **goal** of the research wrong and you will reach for the wrong method, and the findings will end up in a drawer no one opens. Worse, the blind spot between goals and objectives tends to propagate downstream until it resurfaces as a mispositioned product.

The goal is the outcome of the work — the thing you are actually trying to learn — and it holds even for syndicated research. *Why doesn't our audience use the portal, or upgrade to the paid tier? Why doesn't the top slice of TikTok's audience ever show up on Netflix? Why does nobody buy from the Chilled Food Counter?* Around each goal you set objectives: the concrete steps by which you'll get there.

- Understand why people use Netflix, Apple TV+, Disney+, HBO Max, Peacock, Amazon Prime Video.
- Work out what draws this audience to those services — and what pushes them away.
- Surface the specific friction our audience hits on Netflix.

There is a short conversation to have with yourself first, because you need to know where in the product's life you're standing and what good you can actually do from there. Put these to yourself and to your colleagues:

- What is the product's business goal?
- Who is our target audience?
- What are users trying to accomplish in the product?
- Is this their first attempt at the task?
- Where does the product help the user, and where does it get in the way?
- Why would this audience choose our product specifically?
- Where will they use it, and in what context?

Some of these answers live with your stakeholders, and stakeholders have a habit of handing back something strange and abstract when you ask. That's fine — translating the abstract into crisp goals and objectives is the job. It helps to know who your stakeholders actually are: anyone with a material interest in the project, up to and including its beneficiary. Split them into external and internal. For the internal set, build a RACI matrix — it doubles as a way to read how loaded each person is. **R**esponsible does the work, under direction. **A**ccountable owns the outcome, usually the project manager, and carries the SLA. **C**onsulted are brought in for their expertise before the work happens. **I**nformed are simply told when it's done. From there, an Eisenhower matrix helps you sequence the work; the sweet spot is the quadrant of *important but not urgent*.

![Eisenhower matrix](http://your-scorpion.ru/wp-content/uploads/2021/06/Frame-41.png)

A third lens, the stakeholder onion diagram, lets you arrange people by how much they influence the product — the closer to the center, the more sway they hold.

## Choosing a method

Once the problem is framed, the method almost always narrows to an interview or a usability test. If you want to learn what stands between people and a visit to the Botanical Garden, that's a problem interview. If a product already exists and the question is whether a feature is pleasant to use, that's usability testing.

With the method chosen, the real substance of the work is asking a great many questions and getting them answered. Is there demand for the feature? How do we know? Does our product roadmap actually serve the user and the business? Put the following to yourself and your stakeholders to find out.

- What problem does your idea solve?
- How do people cope with that problem today?
- Which other products address it?
- How did the previous solution fail?
- Do users understand how the product works?
- What do users say — and think — about the service?
- Who are the competitors?
- What is the app or site *for*; what job does it close out?
- Is there demand for our solution among the target audience?
- On which devices is the product used?
- Which scenarios do we cover with it?

## Planning your metrics

Decide up front which metrics you'll track. Strong (behavioral) metrics capture what users *do*; weak (perceived) metrics capture what users *say*. The weak family includes NPS (likelihood to recommend), SUS (usability), CES (ease of use), and CSAT (satisfaction).

CES, the Customer Effort Score, is a single question about how hard it was to get a task done, with five answer options; push it further and you're into True Intent Studies. CSAT, the Satisfaction Score, is again one question — "how satisfied were you with your experience" — plus a scale, and it usually speaks to the interface. It's the classic mall-kiosk question, and worth a word of warning: a CSAT of 9 in England is not comparable to a CSAT of 6 in Russia. Mentality colors the number. For work like *compare our branches across countries*, you need per-segment threshold values, or you watch the trend over time rather than the absolute figure. If you want that instant, mall-terminal style of feedback — the little box with the smiley faces you see in shopping centers and government service halls — you can buy a JTK controller and map any actions you like onto its keys.

Resist the urge to grow a metric purely to produce a prettier number in the report. I prefer to track the broad dimensions of user satisfaction — improvement, efficiency, intuitiveness (usability), engagement, trust — and to decide, case by case, which specific measures will move those dimensions.

And a caution before you fall for your dashboards: strong metrics can look immaculate while the real experience quietly fails. This is the trap of *bounded rationality* — the idea that people make economic decisions through a narrow lens, rarely weighing every possible factor. Your users decide behind the wheel, walking the dog, half-listening in a meeting; their attention is fragmented. They also resent things a model would never flag — a charge for water in a café, a fee for moving money between their own accounts. So a flow can pass cleanly, every task completed, and the product can still feel wrong. A bank may offer a dozen well-designed cards, and all a rational person has to do is read the terms and pick the optimal one — but nobody wants that. People want one card they can tune as they go.

## Preparing for a usability test

Usability tests vary by how you talk to the respondent, by project type, and by goal. Games, for instance, are tested by observation: you watch the play session, then interview afterward. The skeleton is always the same — an opening interview, the tasks, and a closing. Sorted by goal, four kinds recur:

1. **Formative** — you're hunting for what's awkward in the product. Qualitative.
2. **Summative** — you're measuring *how* (in)convenient it is. Quantitative, with a questionnaire, so you can infer from the sample how widespread the problem is across the whole population.
3. **Hybrid** — the formative hunt for problems combined with capturing metrics of success, productivity, and satisfaction.
4. **Comparative** — weighing the pros and cons of several competing products.

The mode of contact can be in-person, remote moderated, or remote unmoderated.

Sorted by project type:

1. **Classic** — you test a finished product before release. The respondent gets tasks, you observe and ask questions. The build is well made and past QA, with no obvious bugs. If the interface is complex, play the recording of the user's actions back to them afterward and ask them to narrate it — retrospective think-aloud.
2. **Playtests** — in games there are no tasks; you simply observe, ask questions, and hand over questionnaires.
3. **First Click Test (FCT)** — you give the respondent a task to find a particular piece of functionality, having first set the context. It suits the case where you have a single mockup.
4. **Rapid Iterative Testing and Evaluation (RITE)** — you test the prototype in iterations. In one day you run 2–3 respondents, make changes off the back of what you learn; the next day, 2–3 fresh respondents and fresh changes — carrying on until you converge on something close to ideal, usually in five or six passes.

If you need screen recording, call in on a service that lets you share a screen — Zoom, Whereby, Google Meet, Discord. Match the tool to the audience: with gamers, Discord is the natural home. You can try Fabuza, which has remote-moderated functionality built in, but it's simpler to use the mainstream services everyone already knows. To capture the stream, set up OBS:

![OBS setup](https://your-scorpion.ru/wp-content/uploads/2021/02/ff.png)

Then, in [YouTube Studio](https://studio.youtube.com/), generate a unique stream key and paste it into OBS. From there, one button starts the broadcast and you're live.

![YouTube Studio stream key](https://your-scorpion.ru/wp-content/uploads/2021/02/sf.png)

For remote unmoderated testing, reach for Fabuza, Usability Factory, or Lookback. A fuller roster, in case it's useful: UserTesting.com, TryMyUI, Userlytics, Amazon Mechanical Turk, CrowdFlower, Fabuza, Loop 11, UserZoom (formerly Validately), Userfeel.com, UX Army, Lookback, UXCrowd.

There's also a lightweight route worth naming when budget is thin: guerrilla research. It's no substitute for a proper usability test, but it gets you a fast pulse of feedback. Just know your terrain — in Japan, for example, there's no culture of walking up to strangers and quizzing them about anything personal, least of all where business people gather in public. If you must, aim for Tokyo, and specifically Shibuya; a colleague there found people noticeably more willing to engage.

## Interrogating the client

Preparation isn't finished. When an external manager turns up wanting to commission a usability study, your job is to bury them in questions. Ideally the product designer sits on both sides of the table — client and executor at once — but if you're operating as an external UX researcher, you probe hard:

1. What is expected from the research?
2. Have you worked with a product designer / UX researcher / UX analyst before? Is it clear what the output will be?
3. What information is missing? How do we get it?
4. What problems already exist, and why are they problems?
5. Is there properly configured analytics? Can I have access?
6. Why are you coming to this so late?
7. Where is our users' attention — where will they discover our product and form the habit of using it?
8. Is the client the decision-maker?
9. What prompted them to plan research now?
10. Do people grasp the value of the product? How does that show up?
11. Are there any hypotheses already on the table?

**About the project.** What are the expectations for launch? Which product metrics will be tracked? How will it monetize? What user behavior is expected — and useful for hitting the business goals? What development stage is the project at? If it's a redesign, what's changing, and are there new features? On which devices will we test the prototype, and where do those devices come from?

**About the audience and respondents.** Who is the target audience — do we even know? What groups can we split it into? Are there results from earlier studies? Who are the competitors? Are we testing on users who already have experience with comparable services?

**Constraints.** What limits are we working within — app version, platform, browsers? Which scenarios in the prototype don't work? Can users in different roles reach the functionality (for live B2B products)?

**Logistics.** Who pays to source respondents? Is there an understanding of how agency recruiting works? Can our own employees be respondents? When are you ready to start? Who else has a stake in this?

When a business is only just approaching you — before a single pixel of design work — a compact opening questionnaire earns its keep: What problem are we solving? For whom? How does the user solve it today? What do they want to get done? What do *we* want from the user? Which metric captures their engagement? How do we motivate them to tell friends about the service? On hitting which goals, and in what timeframe, is the product a success? How does it differ from competitors? How does it relate to the company's other products? And how will it affect the company itself?

If a fixed formula is easier to remember, use the five W's — Who, What, When, Where, Why.

**Who** — questions that map the audience. *Who influences how our product is found in the market? Who comes to mind when we mention product X? Who did you talk to while building product X?*

**What** — internal motivation. *What most irritates or wears on you during the day when working with product X? What can't you do in the product that you wish you could? What do you find yourself doing in the product that you'd rather not? What's the single best thing about product X? What's the most important aspect of working with it day to day?*

**When** — external motivation. *When did you first become aware of the problem? When did you first understand what solution you needed? When, using the product, did you feel stuck and unsure what to do next? When did it frustrate you? When did you start using it? When did you realize it was the right fit for the task?*

**Where** — context. *Where do you look for products that meet needs like this? Where do you get updates for product X? Where are the venues whose reviews you actually trust?*

**Why** — the implicit drivers. *Why do you think product X is better than product Y? Why? Why? Why? Why? Why? — and, just as usefully, Why not?*

Two notes from the trenches of contracting before you get too comfortable. First, if you need to understand a person more deeply — their priorities, what makes them tick — three questions do a lot of work: *If I asked your closest friend or a family member to describe you, what would they say? What's the most useful feedback you've ever had from a colleague? If the engineering team were a football side, what role should you be playing?* Second, if a large client keeps stringing you along for a year and floating impossible terms, don't chase them. The moment the project becomes both urgent and important, they'll come running and suddenly find the budget. Bank each completed block of work behind a letter of guarantee, and keep a good IT lawyer on call — one who knows that the civil code outranks the contract.

The other law you can't argue with is the project triangle. "Fast, cheap, and brilliant" is the folk version of it: time, money, and scope, with quality sitting in the middle. When a project hits trouble, the discipline is to name which face it's pressing on — time, money, or scope — because a push on one face reshapes the others. So at the outset you decide which face is fixed. The textbook move: to hit a deadline you either add people (money) or cut features (scope), or you move the launch date. Quality is the fourth element, at the center, and what "quality" means is defined per company — landing on budget, release velocity, a set of metrics.

That same discipline extends to steering the work over time. Set goals by horizon: a week is a feature shipped or an analysis done, a month is a moved metric, a quarter is revenue up, a year is what you actually walked away with. Your own goals — experience, career growth, earnings, networking, influence — sit underneath those. Leading the work well leans on situational leadership theory, with its familiar quadrant matching management style to the performer's level; set tasks with SMART, spell out *why* each one matters, and praise the wins.

![Situational leadership quadrant](http://your-scorpion.ru/wp-content/uploads/2021/12/Group-184.jpg)

And with that settled, you launch recruiting.

## Questions for the prototype

In time you'll have a prototype or product to test. For internal users evaluating alpha and beta builds, a general set covers most of it:

- Is it clear to the user what to do on the current screen?
- Do users understand how the flow works, and which actions each step needs?
- Do they know what they're meant to do next?
- Where are we running this — lab, office, field, with or without a live stream?
- Is it clear which actions lead to the goal?
- Can the user tell whether their actions were correct and successful?

## Writing the script

Open with a welcome, a word about yourself, and the aims of the interview or test. Establishing trust matters; give the respondent a general picture of how the session will go. In order: thank them and say hello, introduce yourself, describe the goal in broad terms, warn them about the length and about audio/video recording, handle the NDA and consent to process personal data, and walk them through the process.

Then warm-up questions — what the respondent does, their hobbies, how they spend their downtime — folded together with screening questions that confirm the experience you're after and filter out the tire-kickers. *"What do you do for work? Tell me more about your hobbies. How often do you use a home computer? How often do you buy things online?"*

A tidy way to hold a user's need in a single sentence is the user story: *As a [role], I want to [do something], so that [outcome].* It forces the three answers that matter — who, what, why — in one breath. *As a pensioner with poor eyesight, I want to check every paper and read all the text on documents at my local post-office branch, so I use a special magnifier built into my glasses.*

Now the questions on the topic itself. You are not selling the product, and you keep your own behavior in check, never hinting to the user which answers or actions are "right." Move from the general to the specific: *How have your shopping habits changed over the past year? → Why did you start using grocery delivery? → How often do you use it? → How do you track the courier? → What problems came up on your last delivery?* A few that lean fintech:

- What does your company do?
- Which services do you currently use?
- How long has your company been on the market?
- What's your role there?
- What does your company have in place for…?
- Which tasks interest you, and why?
- How do you usually interact with your bank?
- Which banking products interest you?
- Who deals with the bank on your company's behalf?
- When do you turn to the bank?
- How did the choice of service happen?
- Where did you hear about it?
- Whose opinion did you weigh when choosing…?
- How long did choosing the product take?
- How often do you revisit the terms or makeup of the service?
- How long have you been using it?
- How do you prefer to receive news of changes?
- What were your expectations?

Once every task is done, draw out the respondent's impressions — then close. Summarize the session, paraphrase the ideas back, ask whether there were questions you should have asked and who else you ought to talk to, and hand over the incentive.

## Checking the prototype

Start by learning everything about the product. If the goal is merely "let's see what's up with the UX," that isn't a goal:

- What is the product?
- What's expected from the research?
- Which problems are already on the surface?
- Which metrics are affected?
- Is there any data?
- Can we get away with cheap methods — session replay, cold-calling?

For automating the calling there's no shortage of tooling — Dialplan, UniMRCP, AMI, Asterisk paired with Tinkoff VoiceKit; you can even synthesize the speech.

Usability testing, at bottom, is a check on how comfortable the interface is to work with. If you like your definitions official, ISO 9241-210:2010 should please you: a testing method aimed at establishing the degree to which a product under development is usable, learnable, comprehensible, and appealing to its users within a given context of use. Along the way you gauge the product's ergonomics against competitors and surface usage patterns, impressions, and problems.

Everyone always wants to hear about the product's problems, and to surface them you answer three questions: **1.** What did the user want — what were they trying to do? **2.** Why did it turn out the way it did? **3.** What happens if we leave it exactly as is? A handful of further questions you should be able to answer after a prototype test:

- Can you get your everyday tasks done in the prototype?
- Does the design match the product's purpose?
- What's the first thing you want to do in it?
- What irritates you?
- What pulls you away from the goal?
- Which features were ignored entirely?
- Does the information architecture match users' mental model?
- Does the audience feel the product was built for them?
- What might get you using it regularly?
- In their own words, how do users describe the experience of working in the prototype?

### Wireframes

If you're showing wireframes rather than a working MVP, I'd try to answer a slightly different set:

- Before touching the prototype, what functionality do they expect from the product?
- What look do they expect?
- After a first glance, is it clear what the service is about and what you can do in it?
- Does that first impression hold up on closer use?
- What functionality is missing?
- Which elements sit in illogical places?
- How does using the prototype *feel*?
- If the user had a magic wand, what would they change?

And for extra feedback:

- What are the three hardest moments in product X's interface?
- What did you want to do but couldn't?
- What functionality is missing?
- Which product do you wish X felt more like?
- What's the single most irritating thing about X?
- If you met an expert on X, what three questions would you ask?
- What eats the most time in X?
- Which products do you use alongside X?

That's a great many questions, which means a great many answers — but not all of them need to be spoken aloud. You can work with a respondent in four ways: **observation** (give a task, ask nothing, just watch); **think-aloud** (give a task and ask them to narrate every action as they go); **active intervention** (give a task and ask questions as they work); and **retrospective think-aloud** (give a task, record the session, and afterward have them comment on their actions from the video).

## The questionnaires

### SUS

After the test, hand the respondent a questionnaire such as SUS to gauge how usable the system is. You give them ten statements and ask, on a scale of 1 to 5, how much they agree:

1. I think I'd like to use this system frequently.
2. I find the system straightforward.
3. I think the system is easy to use.
4. I think I could use it without help from a technical specialist.
5. I found the system's various functions well integrated.
6. I think the system is very logical and coherent.
7. I imagine most people could learn to use it quickly and easily.
8. I find the system intuitive.
9. I feel confident using the system.
10. I could use it without needing to learn anything new first.

The scoring is not obvious. For odd-numbered items, subtract one from the response — a 4 becomes 4 − 1 = 3. For even-numbered items, subtract the response from five, so a 3 becomes 5 − 3 = 2. Each item now sits between 0 and 4. Sum all ten, multiply by 2.5, and you land on a number from 0 to 100 — the average usability score. Counterintuitively, a SUS of 50 is *not* a decent middling result; 50 is very nearly bad. The gradation runs:

- **92** — exceeds expectations
- **85 — excellent** — somewhere around here people start recommending you to friends
- **72** — good — the commercial-service standard
- **68 — the average**, the equivalent of 50%
- **52** — passable
- **38 — poor** — somewhere around here it's time to sound the alarm
- **25** — exceeds your worst expectations

### Kano

Kano gauges users' *emotional* reaction to features, which makes it a questionnaire for prioritizing the backlog. It's about what people feel — how they relate to a feature being present or absent. I use it when deciding which features to carry from an old version of a product into a new one. Kano maps the range between user delight and disappointment, between a good and a bad implementation. A caveat: I only use it with a motivated, English-speaking audience, because the translation chain — Japanese → English → Russian — doesn't inspire confidence, and the wording is genuinely tricky.

![Kano model](https://your-scorpion.ru/wp-content/uploads/2020/09/Group-7.png)

The model comes from the Japanese professor Noriaki Kano. For each feature you ask two questions — one about the user's attitude to the feature being *present*, one to it being *absent* — because the whole model rests on how the user feels in each case. Phrasing them well is critical:

1. How do you feel about **[feature]** being in the service?
2. How do you feel about **[feature]** *not* being in the service?

For the functional (present) question, the answers run: *I like that it's there · I expect it to be there · I'm neutral · I can tolerate it · I dislike that it's there.* For the dysfunctional (absent) question: *I like that it's not there · I expect it not to be there · I'm neutral · I can tolerate its absence · I dislike that it's not there.* You can safely rephrase to fit the situation — for example: *If our company introduced such a service, how would you rate that?* → *I'd definitely like that you have it · I think a company like yours must have it · I'm neutral · I don't like the service, but I could live with it if you added it · I don't need it, and I'd dislike it if you added it.*

That yields five kinds of feature:

- **Attractive / WOW features** — the ones that belong in advertising and produce a genuine frisson. Unexpected delights. Smart-animate in Figma: they shipped it and the community was thrilled.
- **One-dimensional (performance)** — your product's core characteristics. The faster the internet, the happier the user, though there's an acceptable floor (hygiene) beneath which it simply must not fall.
- **Must-be (hygiene)** — a messenger has to send messages; a bank has to pay a mobile bill. Their absence is a catastrophe, and they have to be executed well. These are the baseline expectations: no internet bank without a balance view, no phone without calls, no car without brakes.
- **Indifferent** — features nobody particularly needs.
- **Reverse** — the ones you're better off removing entirely.

![Feature evolution over time](https://your-scorpion.ru/wp-content/uploads/2021/02/Group-304.png)

And note the drift: if smart-animate was a coveted differentiator at launch, it's now a desired feature, and in a few years it becomes hygiene — the kind of thing without which there's no point shipping the product at all. To place each feature in its category, use the evaluation table.

![Kano evaluation table](https://your-scorpion.ru/wp-content/uploads/2021/02/sf.gif)

The failure modes are real. It's hard to hold the line between "present" and "absent" in the phrasing; get the semantics wrong — two near-identical questions differing by a single word — and the results are safe to ignore. If every feature lands in the performance bucket, your study design has a problem. It pays to pair Kano with a classic battery of ratings. Kano is a fast study, though; for anything more serious, look toward MaxDiff. Say the task is to pick which 3 of 10 branded phone cases go into production for nationwide sale. You run MaxDiff, where respondents choose the most and least appealing cases, then add TURF to size the audience that would be interested in the limited lineup.

### NPS

A satisfaction questionnaire that measures willingness to recommend — the story of customer loyalty. Two questions: *How likely are you to recommend X to friends or acquaintances?* on a scale where 0 is "absolutely never" and 10 is "definitely will," followed by *Name the main reason for your score.* Respondents fall into three groups: **promoters** (9–10), **passives** (7–8), and **detractors** (0–6).

The simple calculation is NPS = %promoters − %detractors; I usually drop the results into an [NPS calculator](http://www.npscalculator.com/en). You might get an index of, say, −34%. Ship a feature, watch how it moves: if NPS climbs to −12%, there are fewer critics, but promoters are still in the minority. Often enough, a negative NPS is simply the normal NPS for a given market. NPS is also a handy way to filter out the detractors (everyone who scored 0–6) and invite them to a problem interview — that group is usually the one leaving the negative reviews. Don't draw grand conclusions from it; it's a very simple instrument, useful mainly for reading the overall situation over time. And it certainly does not map directly onto the Wallet Allocation Rule. The business cares far more about profit than about affection, and your highest-paying customers can hand you the lowest NPS of all.

### PSSUQ

The Post-Study System Usability Questionnaire is an alternative to SUS, likewise built from statements rated 1 to 7 (with an n/a option):

1. Overall, I'm satisfied with how easy this application is to use.
2. It was simple to use.
3. I could complete tasks quickly.
4. I felt comfortable using it.
5. It was easy to learn.
6. I believe I could be productive working with it.
7. Error messages were clear, and I understood what to do.
8. I could fix any error quickly and easily.
9. All the reference information, documentation included, was clear.
10. It was easy to find the information I wanted.
11. The supporting information helped me finish the task.
12. The way information was laid out on screen was clear and as expected.
13. The interface was pleasant.
14. I enjoyed using the interface.
15. It had all the functions I expected.
16. Overall, I consider the application good and successful.

You come away with four scores: an overall figure across all items, System Quality (items 1–6), Information Quality (7–12), and Interface Quality (13–15).

### ASQ

The After-Scenario Questionnaire is a short alternative to PSSUQ, rating the ease of completing tasks, expectations about time spent (productivity), and the quality of the supporting information. It's free to use, provided you cite the source. Three statements:

1. Overall, I'm satisfied with how easy it was to complete the tasks in this scenario.
2. Overall, I'm satisfied with the amount of time it took to complete them.
3. Overall, I'm satisfied with the support information (online help, messages, documentation) while completing the tasks.

## Protocol and analysis

After testing you're left with a pile of artifacts, and they need structure — usually a table. Name the theme (say, *motivation to renew a subscription*), put respondents across the columns and the gist of each answer down the rows.

| Dmitry Ivanov | Vasily Petrov | Elena Ivanova | Maria Markovna |
| --- | --- | --- | --- |
| Ran out of content on the platform; no reason to renew | Poor internet quality makes it impossible to use the subscription's perks | Feels the price doesn't match the quality; willing to pay $2–3 | Not enough content, so buys the subscription twice a year for a couple of months |

*Motivation to renew a subscription*

The interview audio is either transcribed or written up into a protocol, typically in Excel or Google Sheets — themes across the columns, respondent names down the rows.

| **№** | **Respondent** | **Theme 1** | **Theme 2** |
| --- | --- | --- | --- |
| 1 | Maria Markovna | *quote* | *quote* |
| 2 | Elena Ivanova | *quote* | *quote* |
| 3 | Vasily Petrov | *quote* | *quote* |
| 4 | Dmitry Ivanov | *quote* | *quote* |

With those built, you run a thematic analysis: work through every protocol and extract the themes — *6 of 8 respondents flag the couriers' speed*, for instance — and from that you assemble the report. When the volume of interviews justifies proper tooling, the academic options are NVivo, Atlas.ti 6.0 (my recommendation), HyperRESEARCH 2.8, and MaxQDA; if there are only a handful of interviews and everything is kept lightweight, Excel will do.

The first and most important question to ask the client about reporting is: *what will you do with the results?* Present to colleagues, prioritize the backlog with the product manager, enrich the CJM? The answer picks the format. Briefly: Google Sheets is good for quantitative studies; online whiteboards suit reports for a savvier audience; PowerPoint is the classic hundred-slide deck with minimal information per slide, made for pitching to a room; and a video report — a short film about the results and the process — is excellent for *selling* research into a company that has no culture of it yet.

A workable report structure:

1. Goals and objectives of the study
2. Methodology
3. General conclusions and headline findings
4. Answers to the questions and hypotheses
5. Quantitative analysis (metrics, questionnaires)
6. Detailed results
7. Wishlist
8. Recommendations

Recommendations aren't always wanted — clarify that with the client in advance. Sometimes all they need is content analysis: describe the ideas, words, and phrases common across respondents, and let the team decide for itself which terms belong in the interface. Sometimes it's thematic analysis instead — describing, say, how using an animal-care social network changes a person's behavior.

A problem slide follows a fixed structure: what's wrong in the interface, why that's a problem, how it showed up during testing, and — if the problem is critical — respondent quotes. The convention is at least three slides per problem. The first is effectiveness: *most respondents (7 of 10) didn't notice the new button*, plus the severity of the issue. The second is productivity: the average time to complete the task. The third is satisfaction: a value on the five-point scale, the SUS result. To score all three together, use Jeff Sauro's SUM — and for the count to be valid, every respondent needs identical starting tasks and no interference from the researcher.

If you decide to build your own questionnaire, don't skip the pretest and the pilot. A pilot is a run on a small sample to check that your questions, and their order, actually hold together; a pretest is about testing specific questions from the questionnaire rather than the whole thing.

## Analyzing quantitatively

Quantitative work turns on two terms. The *population* is every user you have; the *sample* is the slice the study covers. The sample has to be representative — it must mirror the population. Suppose we know our audience is 50% men and 50% women, and that a third order food online, a third shop at the corner store, and a third eat only in restaurants. Then the sample drawn from that population has to carry the same 50/50 split, with each third of users falling into one of those three eating habits.

Quantitative methods demand large numbers of respondents. There are plenty of [sample-size calculators](https://socioline.ru/rv.php) where you enter the population, the precision you want, and the acceptable margin of error as a percentage — the smaller the percentage, the more representative the result, but the larger the sample it requires. For scale: VTsIOM runs its polls on 1,600 people, and major marketing studies take in around 500. To find respondents you can use [fastuna](https://fastuna.ru/), or read [this article](https://your-scorpion.ru/product_design_approaches/).

If the goal is to capture *public opinion* — say, before a well-known figure's appearance — the fast route is probability-based panels rather than the cheaper non-probability online panels; [the LISS Panel](https://www.lissdata.nl/) and the [GESIS Panel](https://www.gesis.org/gesis-panel/gesis-panel-home) are good examples. And when you've collected demographics for each respondent alongside their product preferences and want to see how demographics relate to class membership, start with a latent class analysis to build the classes, then run them through crosstabs — or a weighted regression.

Sometimes the data has to come off the internet — from contractors, from open data sources, or through an API. An HTTP request has a few parts: the address you're calling (what sits in the browser bar), technical information (cookies), and the request method. The response carries a status — 200 for success, 404 when the address isn't found, and so on; the [full list of HTTP status codes](https://ru.wikipedia.org/wiki/Список_кодов_состояния_HTTP) is worth a bookmark — plus the body in the requested format (HTML, XML, JSON…) or media files, and further technical detail. Requests use one of two methods. GET simply fetches text or a media file from an address: you ask the server for a file, the server hands it over. POST is used to send forms and carries extra data beyond the address — form fields, images, and the like.

In Python, the `requests` library sends a request to a server with almost no ceremony. (You can generate JSON from Figma source files with my own tool at [your-scorpion.github.io](http://your-scorpion.github.io/).)

```python
import requests
response = requests.get('https://your-scorpion.ru/wp-content/uploads/2019/04/3.4.2020-403.json')
```

Check the response with `print(response.status_code)` and you'll see 200 — the server answered and returned what we expected. Right now the text lives in a plain string variable; you can turn that string into a dictionary with Python's JSON parser, or with the `.json()` method already built into the response object.

More interesting is parsing a page:

```python
import requests

url = 'https://your-scorpion.github.io'
response = requests.get(url)
print(response.status_code)
print(response.text)
```

That gives you the page's raw HTML. To go further you'll want [BeautifulSoup](https://pypi.org/project/beautifulsoup4/), which pulls text out of a forest of tags — `print(page.find('h1').text)` returns the text inside the `h1`.

```python
from bs4 import BeautifulSoup
import requests

url = 'https://your-scorpion.github.io'
response = requests.get(url)
page = BeautifulSoup(response.text, 'html.parser')
print(page.title)
page.title.text
```

With the results scraped — useful when a survey doesn't hand back the data directly — you organize them. For experimenting, load the data into Python lists. Create an empty list with `my_list = []` (the syntactic sugar) or `my_list = list()`, and confirm it with `type(my_list)`. A populated one looks like `count = [1, 2, 3, 4, 5]`.

Lists come with handy methods. `split` is one: `skills = 'UX,UI,JTBD,CJM,Persons,Dev'.split(',')` returns `['UX', 'UI', 'JTBD', 'CJM', 'Persons', 'Dev']`. Its mirror image is `.join`, and `.append` adds items. But when you need key–value pairs, dictionaries beat lists — and you'll often meet a list nested inside a list with a dictionary inside *that*. Dictionary lookups are famously fast: searching a dict of 500,000 entries takes about as long as one of 500.

```python
animals = {'cat': 'Murzik', 'dog': 'Bars', 'fish': 'boost', 'lemur': 'Mastik', 'parrot': 'Gesha'}
```

## Working with CSV

Say you have a table of results that needs prep before analysis. First, pull the dataset into Python. If the encoding gives you trouble, try `df = pd.read_csv('Book_3.csv', encoding='utf-8')`.

```python
import pandas as pd
from google.colab import files
uploaded = files.upload()
data = pd.read_csv("PSSUQ_res.csv")
data.head(10)
```

![Loading the CSV](https://your-scorpion.ru/wp-content/uploads/2021/02/PSSUQ.gif)

Look at the dataframe's info. Rarely is every field filled — surveys tend to carry missing values, especially when you're polling detractors. Drop the columns you don't need:

```python
data = data.drop(['Comments'], axis=1)
data.info
data.head(10)
```

Now you can see the `Tool` column has two missing values. You could treat that as a signal in its own right, but here it's better to fill the gaps with the most frequent value — the mode:

```python
data['5'].value_counts()
fix_tool_column = data['5'].mode()[0]
data.info
```

![Inspecting missing values](https://your-scorpion.ru/wp-content/uploads/2021/02/PSSUQ_res.gif)

Column 16 has far more gaps, so fill those with the column mean. A note for ML work with training and validation sets: compute the mean from the *training* data only, then use it to fill the gaps in both training and validation. That's what keeps you from overfitting. And if your data contains literal `N/A` values that you don't want pandas reading as `NaN`, reach for the `na_values` parameter.

```python
mean_speed = data['16'].mean()
data['16'] = data['16'].fillna(mean_speed)
data
```

You'll also hit categorical variables. Something like gender resolves with a single command, but for columns with more than two values you'll need dummy variables. The quick route, `data = pd.get_dummies(data)`, converts *every* text column — and here we only want to touch `Remotely`:

```python
data = pd.get_dummies(data, columns=['Remotely'])
data
```

Save the result to a fresh CSV with `data.to_csv('filename.csv')` — for example `df[[0, 1]].to_csv` — or push it straight into SQL with `df.to_sql(…)`, and analyze from there.

Cleaning the data itself is its own small craft. In Excel, `TRIM` strips the stray spaces and `CLEAN` removes non-printing characters; in Python, lemmatization or stemming does the heavier lifting well.
