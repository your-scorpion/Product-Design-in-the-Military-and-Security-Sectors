# Managing Paper Security
### Standards, risk, and the governance layer that makes cybersecurity actually stick

*Maksim Tcvetkov · 18 January 2023*

I suspect this will be my most boring article yet. What's more boring than following standards, even cybersecurity management standards? But a good engineer always follows instructions, and documentation is the foundation of management, information security included. Since working with documents is unavoidable, and it's a necessary condition for an organization's — and a specialist's — "growing up," let's dig into the subject properly.

Why should we work with standards someone else wrote? First, standards are written by large groups of experts from around the world and represent accumulated wisdom drawn from extensive practical experience. Second, in some cases you can obtain certification against a standard (ISO/IEC 27001, say), which gives key stakeholders, internal and external alike, extra confidence in the organization's security posture. Third, following a well-established process saves a great deal of time and effort, and lowers the odds that key steps get skipped. Fourth, some clients will simply insist on compliance with one or more standards as a condition of doing business with you at all.

Standards are also simply the norm for any mature industry. Around 1980 in the West, awareness emerged that cyber threats existed at all, as government documents started leaking over networks. By 1990, critical infrastructure and internet commerce had taken shape as concepts, which meaningfully increased the number of internet incidents. The 2000s can fairly be described as a cyber-arms race, even though cyber weapons still aren't regulated by the Geneva Convention in any real sense. The industry keeps growing, so standards will keep appearing, and the pressure to follow them will keep tightening.

## ISO 27001 and NIST

The two key standard families for information security are ISO 27001 and NIST. There are others easily counted as key too — PCI DSS, GDPR, SWIFT, Russia's Federal Law 152-FZ, GOST R 57580, Bank of Russia regulations, OUD4 — but let's focus on the most internationally recognized ones for corporate InfoSec. ISO 27001 provides terminology and an understanding of how everything connects, and it's always paired with ISO 27002 as a catalog of applied recommendations. NIST tends to get used where self-certification is enough — hitting internal OKRs, say. ISO 27001 is the right fit where international certification is actually required. Why might that matter? Say you produce meat for McDonald's burgers, and McDonald's wants confidence that its suppliers can deliver patties every single day without interruption. Your certification as a supplier gives McDonald's more confidence that the supply chain won't break down because of an information-security incident. That said, standards don't dictate exactly which InfoSec products you need to use. ISO/IEC 27001 lets an organization choose any risk-management approach it likes, as long as that approach produces consistent results and covers the other general characteristics ISO/IEC 27001 specifies.

So, we need a certificate. Any organization seeking ISO/IEC 27001 certification has to go through an audit at an accredited certification body. Very roughly summarized, ISO/IEC 27001 requires an organization to implement and maintain an audit program. UKAS publishes a [list](https://www.ukas.com/find-an-organisation/) of UK organizations that have achieved 27001 accreditation.

The steps: the first is a documentation review — really, an analysis of the company's own rules, standards, and guidelines. The second is the certification audit itself: confirming that everything described in step one exists in practice, not just on paper. The third is analysis — checking that every document actually meets ISO 27001's requirements. The fourth is the audit report from the accredited body, delivered to top management. And that's not quite the end — once certified, a schedule of follow-up audits gets established.

A little more detail on those core steps:

1. The first step is a documentation review — reviewing the organization's policies, procedures, standards, and governing documentation to confirm they meet objectives, satisfy ISO/IEC 27001's requirements, and are properly reviewed and maintained.
2. The certification audit — a form of evidentiary audit (or field check) — involves actively examining evidence to verify compliance with policies and established procedures and standards.
3. At the analysis stage, the auditor evaluates the results of the documentation review and certification audit to confirm ISO/IEC 27001's requirements are actually being met.
4. The analysis results feed into an audit report, delivered to management.

NIST is free, and you can find it [here](https://www.nist.gov/cyberframework). It's less prescriptive than ISO 27001, and quite possibly more popular precisely because free standards tend to become more influential even when they're less thoroughly developed. NIST is a mandatory federal-level requirement in some US states. NIST has five core functions: Identify, Protect, Detect, Respond, and Recover the system. It also defines Tiers (maturity levels) from 1 to 4, running from chaos up to a genuinely strong security culture — a gradient from purely reactive problem-handling to a properly configured, risk-based security posture.

**Tier 1: Partial** — vulnerability management isn't formalized; work happens ad hoc and reactively. The organization doesn't understand the value of InfoSec.

**Tier 2: Risk Informed** — risk-handling practices exist but aren't implemented organization-wide. The company understands the importance of managing InfoSec risk, but the work itself is disorganized and unsystematic.

**Tier 3: Repeatable** — InfoSec regulations are agreed upon and built into daily operations. Vulnerabilities are tracked and handled through a defined process.

**Tier 4: Adaptive** — best market practices are in use, and every change happens quickly and effectively.

Both ISO 27001 and NIST CSF help build an ISMS. ISO 27001 goes further, laying out specific steps that have to happen before an ISMS program can even start. Neither standard specifies particular technologies — both give general recommendations instead. ISO 27001 suits commercial companies better, because it splits documentation into mandatory and optional categories, and does a genuinely good job laying out how responsibility and accountability get distributed. NIST, though, is more structured and easier to grasp — it's very easy to present the concept of InfoSec as, at its core, five items: Identify, Protect, Detect, Respond, and Recover.

## Other standards

There's an enormous number of standards across different industries. Not knowing some of the key ones can even land you in prison, or cost a company enormous fines. The most well-known, easily understood example is GDPR, but it isn't the scariest one out there. Look at the US Sarbanes-Oxley Act of 2002, which requires public companies to apply effective internal controls over financial reporting, so that any violations can be disclosed promptly if they occur — meant to prevent deceiving the public or covering up financial errors. This requirement is enforced by the US Securities and Exchange Commission, and applies to businesses based in the US as well as any business actively operating there. The penalty for violating this law runs up to 20 years in prison. And remember, the Streisand effect always applies.

Since we're already on the subject of legal consequences: it's worth being aware of the broader landscape of cybercrime law too, UK law specifically being a good illustration of just how many distinct statutes can apply to what feels like a single category of "computer crime." Simply possessing files sympathetic to a terrorist organization can fall under Section 58 of the Terrorism Act 2000. The Computer Misuse Act carries its own tiers — Section 1 carries up to two years, Section 2 up to five. A DDoS attack falls under CMA Section 3, covering "unauthorized acts with intent to impair, or being reckless as to impairing, the operation of a computer." Passing along phishing-email templates can fall under Section 7 of the Fraud Act 2006, which criminalizes making, supplying, or offering to supply articles for use in fraud — a serious offense carrying up to ten years. Where something illegal is stored on a phone, police can invoke Section 49 of the Regulation of Investigatory Powers Act 2000 (RIPA), which compels the device's owner to provide access. Blackmail following the theft of something valuable from a victim's computer carries up to 14 years under Section 21 of the Theft Act 1968 — and notably, that Act doesn't even define what counts as a "computer." Even online harassment or stalking can bring charges under the Protection from Harassment Act 1997 and Section 127 of the Communications Act 2003. And possession of indecent images of children is punishable under Section 1 of the Protection of Children Act 1978, carrying up to ten years.

Setting the scarier examples aside, it's simply useful to know what standards exist in your own industry. Not sure whether you need certification at all? ISO 27006 gives guidance on who needs certification and when. ISO 31000 covers risk management. 27007 and 27008 cover ISMS auditing specifically.

ISO 27031 is an important standard on business continuity — formally titled "Guidelines for information and communication technology readiness for business continuity." Worth noting, though, that ISO/IEC 22301 is the broader business-continuity-management standard for security and resilience generally. ISO/IEC 27031 lays out six steps:

1. Identify the system failure
2. Determine the cause of the failure
3. Assess what steps can prevent a repeat failure
4. Apply the changes to the systems
5. Record the outcome of the actions taken
6. Review

Another standard, ISO/IEC 27035, gives us a standardized definition of an "information security incident": one or more related and identified information-security events that could damage an organization's assets or threaten its operations. The most interesting part of that definition is that information-security incidents can relate to one or more events that could potentially cause various kinds of harm — now, or in the future. In other words, this definition assumes harm has already occurred, rather than merely being potential.

The second useful thing ISO/IEC 27035 tells us: there are three types of Incident Response Team — standalone, hierarchical, and distributed. Standalone IRTs are usually responsible for a single organization; hierarchical IRTs usually connect several IRTs each focused on different security aspects within a large organization; and distributed IRTs typically run on a staffing-agency or outsourced basis.

The 27002:2022 recommendations mentioned above contain an excellent organizational-controls section, laying out instructions for policy. It also describes preventive and reactive tools (further split into detective and corrective). And going a level deeper, corrective tools split into response and recovery.

And if you need to build a genuine defensive posture, the COSO model offers three lines of defense:

1. **First line of defense** — the people working directly with clients, capable of reducing and assessing risk themselves.
2. **Second line of defense** — independent risk assessment, and oversight of the first line.
3. **Third line** — internal audit, reporting directly to the board of directors, overseeing both the first and second lines of defense.

## Risk

Working in InfoSec and assessing/prioritizing risk are basically synonyms. An employee handing their credentials to a third party as the result of a phishing attack, for instance, is an example of a threat — which is itself a risk.

When we talk about risk assessment, the obvious question is: assessed in what unit? Rabbits, deadlines, rubles? There are two main ways to measure impact and likelihood: quantitative and qualitative. Quantitative measurement assigns a numeric value — estimating the average financial cost of a risk materializing, say, or the likely number of times it'll occur within a year. Quantitative assessment needs some numeric scale behind it — monetary cost, frequency, or probability of occurrence.

Qualitative methods, by contrast, analyze risk based on impact and likelihood using a scale of qualifying labels — assigning one of a small, predefined set of values, like high, medium, or low risk.

A hybrid method also exists: semi-quantitative approaches, using qualitative scales with assigned numeric values. In a hybrid approach, it's entirely reasonable to assign a risk at least two properties: likelihood and severity. Risk can also be assessed either by event or by asset.

Take a data center that could be destroyed in a terrorist attack or a natural disaster — the odds are low, and a risk like that is hard to simply accept, but the risk exists nonetheless. If the data center sits in a conflict zone or a seismically active region, the risk can be mitigated. Duplicating the data center is one option, though an expensive one. Alternatively, you could share the risk through an insurance company, or hire a separate company to provide capacity for a backup data center.

Looking at a more practical example — data theft — the usual goal is to steal data, alter it, or deny the company access to it. Every risk like this needs to be documented in a risk register and prioritized.

From there, you need tools to eliminate the risk entirely, or at least mitigate it. If we're worried a database might leak onto the internet, encrypt the database. Or take the risk of a disgruntled employee deleting an important file — here, you need to build in personal accountability for actions, up to and including criminal liability. You can also formally accept a risk and do nothing about it — officially confirming the risk is small and unlikely to occur. Or eliminate the risk entirely: if SFTP is inconvenient and can be removed from the system without real cost, and the risks tied to SFTP are high, you simply cut SFTP out of the product. (SFTP runs over SSH via TCP port 22.)

We've discussed how a company can protect itself once a risk has already materialized. But it's far safer to prevent a risk from occurring at all than to deal with the consequences afterward. That's exactly what preventive control serves: a control designed to stop an information-security event from happening in the first place, since such an event could lead to one or more consequences. The second layer is detective control: a control designed to detect that an information-security event has occurred. And after that comes corrective control, designed to limit the consequences of information-security events.

- Corrective control should reduce risk if detective control fails to catch something.
- Preventive control should reduce the odds that corrective control ever has to be used at all.
- Detective control should reduce risk if preventive control has already failed.

And reducing risk through the normal performance of everyday duties is often just as simple as configuring a firewall properly:

- Change default administrative passwords to strong, unique ones (see password-based authentication) — or disable remote administrative access entirely.
- Prevent access to the administrative interface (used for managing firewall configuration) from the internet, unless there's a clear, documented business need, and the interface is protected by one of the following controls:
  - multi-factor authentication
  - an allowlist of IP addresses, restricting access to a small range of trusted addresses, combined with a properly managed password-authentication approach
- Block unauthenticated inbound connections by default.
- Ensure firewall rules for inbound connections are approved and documented by an authorized person, with the business need included in that documentation.
- Promptly remove or disable firewall rules that are no longer needed.

Everyone knows about SaaS, but there are other "as a service" delivery models too: IaaS, PaaS, SaaS. A firewall or antivirus, for instance, should be installed both on the service provider's side and on your own client machines, where IaaS (Infrastructure as a Service) is involved. That's risk reduction too.

All of this sounds like it demands installing a pile of software solutions from different vendors, which is obviously expensive, and nobody wants to overpay. Avoiding duplicate implementations of the same solution across a company, and keeping financial costs under control, is exactly what an ISMS helps with. An ISMS is the single source of truth for all information within an organization. A dedicated document, the SoA (Statement of Applicability), records the reasoning behind including or excluding each control from the ISMS. The SoA lets an auditor understand that the ISMS is functioning effectively. If anyone needs to find a regulation, an instruction, or information about an employee — it all belongs in the ISMS. The ISMS might (and should) define a Recovery Time Objective (RTO), for instance — the period of time within which a minimum acceptable level of customer service must be restored after an adverse event. The ISMS should also hold information on every one of the organization's assets, and who's responsible for each.

By assets, I mean the full inventory: information, an employee directory, devices assigned to employees, physical and virtual servers, other network devices, and the equipment infrastructure itself depends on (ICT, HVAC pumps and diffusers, generators, UPS units), plus a CMDB.

Who's actually responsible for incident management and response in an organization? Most likely, different elements of incident management and response fall to different departments depending on the type of response needed. It's likely an InfoSec team plays a key role, of course, but far from every organization actually has one.

Here's one possible table layout for describing cyber risk:

| Function | Category | Subcategory | Reference |
| --- | --- | --- | --- |
| Identify | Identity Management and Access Control | External information systems are catalogued | Informative references |
| Protect | Categories | Subcategory | Informative references |
| Detect | Categories | Subcategory | Informative references |
| Respond | Categories | Subcategory | Informative references |
| Recover | Categories | Subcategory | Informative references |

### How far does "administrator" actually reach?

A related, practical question worth folding in here, since it sits right at the intersection of risk assessment and access control: what limits does an "administrator" access level actually carry, and can an employee grant themselves rights to modify something at the memory level on their own?

Administrator-level access covers an administrator's everyday tasks — installing software, changing system settings, and the like. The "administrator" role isn't about system-process or memory-level access at all; that's System Level, which handles privileged tasks and has memory access. This is easy to confirm directly: run these commands in PowerShell —

```powershell
cd C:\Windows\System32\config\
dir
```

— and you'll be denied access, even as an administrator. Administrators themselves typically reach System Level access through tools like PsExec, Mimikatz, PowerShell, or AnyDesk.

Employees might attempt to use, and sometimes do use, privilege-escalation-auditing frameworks like PowerSploit, PowerUp, Empire, PoshSec, Lazagne, or Linpeas — but every off-the-shelf tool in this category tends to get flagged easily by antivirus software. [PowerUp](https://github.com/PowerShellMafia/PowerSploit/blob/master/Privesc/PowerUp.ps1), for instance: attempting to download that file will usually get blocked outright by a browser or antivirus flagging it as malicious. Its main function, for context, is `Invoke-AllChecks` — an automated audit that flags common local privilege-escalation misconfigurations, which is exactly why security teams use tools like this defensively, to find and fix those same weaknesses on their own systems before anyone else does.

A related aside from a reader testing this in an authorized capture-the-flag exercise: fingerprinting a remote machine's OS is a good example of the kind of reconnaissance CTF exercises are built around. Metasploit's `smb_version` module handles this cleanly — `msfconsole`, then `use auxiliary/scanner/smb/smb_version`, set the target with `set RHOSTS 192.168.125.11`, and `run`. It's a simple banner-grab against the target's SMB service, nothing more.

## Vital infrastructure

National security is an enormously complex subject. But that water needs to reach people's homes, and hospitals need reliable electricity, are simply undeniable facts. And the complexity of the systems behind tasks like that is genuinely a system of systems — with the fittingly grand acronym SoS.

We've talked about companies, but there are also nation-states, and it's often states that hand down requirements to us through standards and certifications. Every service that delivers a government function has its own standard somewhere, often authored by ISO or CENELEC. IEC covers electricity; ETSI writes European telecommunications standards. Identity cards fall under B10, cybersecurity under CS1, biometrics under M1, and radio-frequency identification technology under T6.

For the financial sector, there's PCI DSS, covering payment cards — it lets organizations store card information securely. First and foremost, the standard sets requirements for a secured network, data protection, and having a vulnerability- and access-management program in place. There are 12 requirements in total:

1. Install and maintain a firewall configuration to protect cardholder data.
2. Don't use vendor-supplied defaults for system passwords and other security parameters.
3. Protect stored cardholder data.
4. Encrypt transmission of cardholder data across open, public networks.
5. Use and regularly update antivirus software or programs.
6. Develop and maintain secure systems and applications.
7. Restrict access to cardholder data based on business need to know.
8. Assign a unique ID to each person with computer access.
9. Restrict physical access to cardholder data.
10. Track and monitor all access to network resources and cardholder data.
11. Regularly test security systems and processes.
12. Maintain a policy that addresses information security for all personnel.

What if a standard genuinely doesn't exist yet? That's where things get tricky for industrial operations. In the InfoSec world, specialists can define an MBCO — the minimum level of service acceptable for an organization to keep functioning. But is that enough for a factory? You can restore a server from backup easily enough — but how do you restore nature after a chemical spill? That's a far harder, far costlier problem. This is worth pausing on, since it gets at the real difference between cyber resilience and cybersecurity. Cybersecurity focuses on information technology, while cyber resilience describes an organization's ability to anticipate, prevent, and warn against cyber risks. Cyber resilience takes a broader view than cybersecurity, considering how cyber risks might threaten the survival of the whole organization and how they affect business processes. And notably, there's no need to wait for a government requirement here — reliability at hazardous industrial sites starts de facto, and only later, over time, becomes de jure at the national or even international level.

## People, not documents

InfoSec today is far more focused on people than on technical solutions. It doesn't matter how good your protective tools are if employees confidently click any link that lands in their inbox. And the first instinct of any manager is to introduce strict rules, so employees are afraid to take any action beyond the bare minimum. But the problem is that this always reduces productivity. Low-performing employees rarely get promoted or given a raise, so they'll start breaking the rules just to boost their own output. Which brings us to another standard worth mentioning, ISO 9241-11:2018, on usability. It centers on three key words: effectiveness, efficiency, satisfaction — criteria that also line up with the design principles behind Kerckhoffs's principle and Saltzer & Schroeder's design principles. In other words, InfoSec shouldn't degrade employees' quality of life or break a goal-driven culture.

But how does this translate into practice? Comfortable user behavior is, after all, predictable behavior — and predictable is exactly what an attacker wants. We know, for instance, that people tend to pick bad, easy-to-remember passwords, or use their daughter's birthday as one. What about picking images? People choose brighter, higher-contrast, more memorable ones. Choosing from photos of people, they'll pick people from their own ethnic group, and more conventionally attractive ones. Drawing a graphical unlock pattern, people tend to favor the edges and draw left to right, if that's how they write. All of this is real, and worth regulating deliberately.

But that's not really what we're getting at here. Employees have an expected amount of time to complete a given task, and that expectation shouldn't balloon just because of security policy.

In other words: kill the vakhtyor mindset in yourself — the security guard who enforces rules for their own sake. There's a concept called negative security, and the term isn't meant pejoratively — it refers to security controls that provide "freedom from" threats. Typically, this means technical controls like authentication or network event monitoring. This approach to managing cybersecurity is the most common one in organizations today.

By contrast, there's positive security, which describes a sense of wellbeing built on trusting relationships between people. You read that right. Positive security often refers to how people can be supported by building trusting relationships that contribute to their wellbeing — financial security, or a sense of trust and support from your local community or company, for instance. We should think of positive security as providing the "freedom" to do things, by making sure the necessary conditions for doing them actually exist — so employees don't feel afraid, but actually want to protect the company. In this framing, security starts with HR. I understand that trust and InfoSec look, at first glance, like mutually exclusive concepts. But experienced InfoSec professionals know that strong, positive social relationships between people, built on shared history, are the real key to security. The ability of people to build strong, positive social relationships with one another and within an organization is genuinely hard to overstate. This is often reinforced by a sense of loyalty and support between people and organizations, confidence that controls will be applied fairly and consistently across every part of the organization, and, fundamentally, a sense that the organization actually cares about its people. And when some control does get introduced, it should read as a necessary measure taken for people's benefit, not against them.

Here's another analogy: InfoSec has the concept of information integrity. But integrity applies just as much to people's professional honesty at work as it does to information itself. This matters, because it instills confidence that the organization will maintain consistent information, that people will be treated fairly, and that this leads to more predictable outcomes across a range of technical and non-technical questions alike.

Since we're talking about people, it's worth mentioning EDI assessments — equality, diversity, and inclusion. Don't close the article yet — the mix of ethnicities and genders in a company isn't up to us (I'd hope), it's simply a given fact for us to work with. But people from different cultural backgrounds genuinely need different approaches to security. Security management can use EDI assessments to help design and implement security controls suited to different people — which might mean paying attention to different aspects of software use, what's acceptable in terms of storing personal data, and making sure security controls don't privilege one particular way of working or living over another.

So, before rolling out new, stricter rules as a reaction to a security incident, try answering a few questions about the people involved:

- What tool are they using (and what potential user-behavior problems do you need to account for)?
- What are they doing (and what could they potentially do)?
- Where are they doing it (how does context affect their ability to complete the task)?
- When are they doing it?
- Why are they doing it (is it optional or required)?
- How are they doing it (which devices are they using)? A thread running through all of this is that security considerations still need to be respected, so it's worth adding one more:
- What are the actual security requirements for the user's action?

Answer these honestly, and employees are far less likely to bristle at new security measures.

---

## A couple of practical Windows access-control footnotes

Two reader questions extend the same underlying idea as the article's technical controls — just applied at the scale of a single machine rather than an enterprise ISMS.

**Restricting what a user can do on a shared Windows computer, natively — parental controls for a home PC, say.** Security Templates live inside the MMC snap-in system: open the management console with `mmc`, then File > Add or Remove Snap-ins, and add both Security Configuration Analysis and Security Templates. You can build your own template starting from an existing one, configure it, and save it. To check a system against a template, go to Security Configuration Analysis, click Open DB to create a database, then Analyze Computer Now — the resulting report shows exactly where the system diverges from your defined settings. In PowerShell (as administrator), `secedit /validate .\test.inf` gives you a compliance report the same way.

All of this ultimately lives in Group Policy, which goes considerably further — you can restrict a user's capabilities down to the bare minimum. Open `gpedit.msc`, and you'll find two branches: Computer Configuration, which affects the system as a whole, and User Configuration, which targets an individual account. Under "Start Menu and Taskbar," for instance, you can flexibly restrict the UI itself. It's also worth keeping an eye on Event Viewer > Windows Logs > Application for anything relevant.

**Running an application as a different Windows user.** For a different machine entirely, `compmgmt.msc` has a right-click option, "Connect to Another Computer," which may be what you're after. On the same machine, `runas /user:user2 /savecred notepad` or `runas /user:user2 /noprofile notepad` runs a program under a different local account you already have credentials for. If you're trying to run something as a Guest-type account specifically, keep in mind that account's profile resets on every relogin, so nothing saved to it will persist.
