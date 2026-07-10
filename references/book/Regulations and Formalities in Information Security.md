# Regulations and Formalities in Information Security

*Adapted and translated from the original article by Maxim Tsvetkov (12 October 2021).*

## Standards and regulations

There is a vast constellation of associations and standards bodies — IEEE, IEC, ISO, ITU-T, ANSI, IETF, the Broadband Forum, the Metro Ethernet Forum — and all of them write standards for the information-security industry. Those standards don't always complement one another; the IETF, for instance, doesn't hold itself to the requirements of the ISO OSI standard.

One of the most popular standards is ISO 27001. A complete guide to implementing it well takes a long time to read — worthwhile as that is — but you need to know the seven basic steps for a first pass at introducing information security into an organization:

- Define your risk-assessment methodology.
- Compile an inventory of your information assets.
- Identify threats and vulnerabilities.
- Assess the risks.
- Mitigate or delegate the risks.
- Produce risk reports.
- Review, monitor, and audit.

ISO 27001 requires that adequate resources be provided to create, implement, maintain, and continually improve an information security management system. What is the essential role of ISO/IEC 27002 — the standard formerly known as ISO/IEC 17799? It supplies a catalogue of best-practice controls, each of which ISO/IEC 27001 asks you to weigh when designing an ISMS. ISO/IEC 27001 also stipulates a commitment from senior leadership to securing the company. The general idea is straightforward: departments work in concert, an assessment system is in place, communication with counterparties and partners is running smoothly, and employees genuinely pay attention to security. What you need are processes, support from the top, and — most important of all — a clear understanding of *why* the company is doing any of this.

Let's try to bring some structure to it. There are policies, standards, procedures, and guidelines.

- A **policy** is a high-level statement of an organization's values, goals, and objectives in a particular area, together with its overall approach to reaching them. Although a policy should be reviewed regularly, it is meant to hold for some time, because its job isn't to give detailed or specific guidance on *how* those goals are met. A policy might state, for example, that every user is responsible for creating and maintaining their own system passwords — without saying a word about how, precisely, to do so. Policies are mandatory.
- A **standard** is a more prescriptive document than a policy. It sets out the quantitative parameters of what must be done and brings consistency to controls that can then be measured. Passwords, for instance, must be at least eight characters long, combine digits, letters, and special characters, and be changed in the event of a compromise or for other comparable reasons. Compliance with standards is likewise mandatory. Standards should support the policy and define both what "must" be done and how it is to be achieved. They may be general (handling confidential information, say) or technical (data encryption), but they should always address a specific topic.
- A **procedure** is a set of detailed working instructions describing what must be done, when, how, and by whom. Again, procedures are mandatory, and they should support the organization's policies and standards.
- **Guidelines** are not mandatory, but they can serve as advice, direction, and best practice in the many cases where it is genuinely hard to prescribe how something should be done — the etiquette of working outside the office, for example.

There is also the Statement of Applicability (SoA), which under ISO 27001:2013 sets out recommendations for assessing and treating risk, and is therefore useful when preparing an ISMS (Information Security Management System). An ISMS doesn't require you to install any particular piece of software, but it does require management to back the initiatives that put information-security policies into practice.

But what *is* a risk? There are many ways to answer, but let's say risk = likelihood × impact. A risk can never simply be removed, yet it can be avoided, its probability reduced, or its ownership transferred to and shared with counterparties. Every specialist knows, for example, that X.509 certificates carry vulnerabilities — not fundamental ones, but problems tied chiefly to how they are used. Under ISO/IEC 27000, that is a potential *threat*: something that could conceivably happen. A *vulnerability*, by contrast, is a weak point in your defenses that can be exploited once or many times. The combination of the two is what constitutes risk — a weak point in the defenses that something is poised to exploit.

And if a risk materializes, does that event become an incident? What counts as an incident? It might be an attempt to gain unauthorized access to a system or its data, the unauthorized use of systems to process or store data, changes to a system's firmware, software, or hardware without the owner's consent, or the malicious disruption of service and denial of service. For us the upshot is the same in every case: we need to gather evidence that will stand up in court. That is the job of the Incident Response Team (IRT), which must have leadership's backing and every resource it needs to work an incident quickly. If rules for detecting TTPs (tactics, techniques, and procedures) across your various technologies have been drawn up in advance, so much the better.

Standards prescribe rules. Such rules can exist in an organization *de facto* — already operating, but not formally sanctioned — or *de jure*, once a legal requirement to conform to a given standard lands on your desk. That is the moment the certification process begins, and it runs in three steps: a preliminary stage, in which you submit basic information; the conformity assessment against ISO/IEC 27001 itself; and, finally, certification. ISO/IEC 27001 describes the steps you must take to bring an ISMS into compliance. The description is fairly superficial; for the details, we always turn to ISO/IEC 27000. At the top level the steps run: context of the organization → leadership → planning → support → operation → performance evaluation → improvement. Contrary to what you might expect, ISO/IEC 27002 and ISO/IEC 27001 serve rather different aims — the former is purely a set of good recommendations. They are decent instructions, and the IEC goes so far as to hold that you can stand against a DDoS attack by certifying your business processes to ISO/IEC 27001 and 27002. Broadly, if you follow the approaches of the leading tech companies, there's a good chance you already comply with the standards *de facto*.

Beyond the ones already described, there are other standards worth knowing:

- The **IETF**, whose approaches differ sharply from those of the IEC and ISO. The latter two don't publish drafts — hardly surprising, since access to IEC and ISO material is paid. That paywall protects the revenue of the IEC's national member bodies and of ISO itself.
- **3GPP** produces the standards for mobile phones, from 2G through 5G.
- **GPRS** is a standard too, and one that began life as a European specification (**GPS**, for its part, is a widely adopted standard as well).
- **NIST SP 800-53**, uncertified, is aimed at U.S. federal systems and offers a great deal of good practice for industrial settings.
- **PCI DSS** was originally built for the United States alone, but everyone understands that U.S. legislation ripples out across the world; it governs the conditions for storing bank-card data and the security of payments.
- **HIPAA** and **HITRUST** cover healthcare.
- **ISO 31000** offers guidelines for risk management, including rules for risk assessment — identifying the risk, working out what to protect and from what, how likely the risk is to occur, and whether it's worth protecting against at all.
- The **ISO/IEC JTC 1/SC 27** standard and its subsection **ISO/IEC 27000** can be sorted into categories, ISO/IEC 27001 chief among them.
- **ITU-T X.509**, published as **ISO/IEC 9594-8**, is useful for certifying public keys.

To develop standards, working groups are formed — designated WG and each focused on a specific domain:

- WG1 — network services
- WG2 — cryptography
- WG3 — security evaluation and testing
- WG4 — network security
- WG5 — privacy

## Mapping standards onto the network

Three basic principles are the starting point for all of information security:

- **Confidentiality** — encompassing anonymity and a high degree of assurance that no outsider will gain access to the data.
- **Integrity** — only legitimate changes are permitted.
- **Availability** — the data is available only to the users for whom it was intended; it is authentic, and it can be verified against its original state.

At its foundation, the internet works by forwarding packets, with applications running on the end hosts. In the modern internet, though, applications can decide for themselves how and where to forward packets. The name for this hybrid is the *overlay network* — a kind of tunneling, familiar from the VPN. Physically the host has a single wire, but virtually the device is connected to a host of others through that tunneling. Which is, in itself, difficult to pin down with standards.

There is also the Rasmussen hierarchy, which lets you look at security from several angles at once — both abstractly and from a practical standpoint. If you lean toward the practical layer, that's component-driven risk management, and it can take in hardware (computers, servers, and so on), software, data sets, personal information, business-critical information, and personnel. Over the top of all that you need to lay a layer of knowledge about ZTN, ZTNA, and ZTA — the concepts for realizing zero trust. At the application layer the standards are decidedly ambiguous, for understandable reasons. Take the Presentation layer, where data is turned from a human-readable form into something that can be sent across the network: you'll naturally run into differing standards, or none at all.

In terms of technical implementation, it all comes down to the LAN (Local Area Network). A network in its most minimal form is your two computers joined by a wire. That's a fairly reliable connection — information is easier to protect inside a wire. The simplest attack on such a network is to cut the wire or steal the router, or to interfere with the traffic by plugging in your own device to intercept it and to forge and modify messages. A wire will connect two computers in your home, but over long distances SONET was for a long time the standard for carrying data. It all seems simple enough — until protocols enter the picture, and even plain HTTP turns out to be a stack:

- **HTTP** provides global object identifiers (URIs) and a simple GET/PUT interface.
- **TLS** provides end-to-end communication security.
- **TCP** provides connection management, reliable delivery, and congestion control.
- **IP** provides global host addresses and a network abstraction layer.

Let's start with TCP, the most popular data-transfer protocol and a thoroughly reliable one. At the heart of the protocol lives the sliding window, in which the Acknowledgment, SequenceNum, and AdvertisedWindow all play a part. The recommended MSL (maximum segment lifetime) is about two minutes. Packets may get shuffled around a little in transit, but in the end they fall into the right order. You can experiment on your own machine with `netstat -nap` to view the protocol statistics, or use Nginx to stand up a general-purpose TCP/UDP proxy server.

Is any of this laid down in the standards? Because the OSI protocols were never popular — there was never even a complete stack — the TCP/IP world won out in the end. ISO created the reference OSI model, while the TCP/IP protocol stack was created by the IETF. TCP and UDP are IETF protocols and have nothing to do with ISO/OSI. If you want to criticize OSI: in the real world there is no data-link layer as such, only MAC and LLC. A network layer does exist, but it differs from OSI's description. HTTP is an application-layer protocol under OSI. And according to Cisco's documentation, Base64 is a presentation mechanism — so when we send a PNG file over HTTP, it is encoded in Base64 *above* HTTP, whereas under the OSI model it ought to sit below. This is why TCP and UDP are the defaults. TCP's predecessor was SSL.

| OSI model            | Model      | Protocol          |
|----------------------|------------|-------------------|
| Application          | Application| HTTP, FTP         |
| Presentation         | Application| HTTP, FTP         |
| Session              | Application| HTTP, FTP         |
| Transport            | Transport  | TCP, UDP, SCTP    |
| Network              | Internet   | IPv4, IPv6        |
| Data link            | Link       | Ethernet, Wi-Fi   |
| Physical             | Link       | Ethernet, Wi-Fi   |

UDP delivers a message from one host to another. It sends the data and simply hopes it all arrives at the far end — no handshake, it just hands the data over. That is exactly what makes it useful for DNS, SNMP, and HTTP/3.

## Physical constraints

Beyond the protocols, there are physical limits too. Employees need a decent internet speed. Let's calculate the delay in transmitting data across a 1 Gb/s network with a single store-and-forward switch and packets of 5,000 bits, and let's say each hop introduces 10 μs of propagation delay. For each link, the transmission delay is 1 Gb ÷ 5 kb = 5 μs, and the last bit needs a further 10 μs to propagate. With one switch there are two links, so the total delay comes to 30 μs. Now make it harder: with three switches we have four links, which means four transmission delays and four propagation delays — 60 μs in all.

Here's another networking problem. Suppose there are 50 km between the devices, and we want the bandwidth-delay for 100-byte packets given a signal speed of 2 × 10⁸ m/s. The propagation delay is 50 × 10³ m ÷ (2 × 10⁸ m/s) = 250 μs. Then 800 bits ÷ 250 μs = 3.2 Mb/s. A reminder that a lowercase *b* means bits and an uppercase *B* means bytes; mega is 2²⁰ or 10⁶, kilo is 2¹⁰ or 10³, and giga is 2³⁰ or 10⁹. Cryptography is applied *over* the network stack, which lets it leave network quality all but untouched.

As for the limits on file storage: one kilobyte is 1,024 bytes — that is, 1,024 memory cells. Both ASCII and UTF-8 render the basic characters with 8 bits, so `00101110` is a period; UTF-8 can render rarer characters with 16 bits. In RAM, a single cell holds 8 bits, or one byte.

When packets are forwarded, headers are formed. There's a concept called the maximum transmission unit (MTU), which governs the maximum size of the headers. VLANs, for instance, are virtualization at layer 2, and their more advanced cousin is VXLAN. Here is a VXLAN header:

| Outer Ethernet Header | Outer IP Header | Outer UDP Header | VXLAN Header | Inner Ethernet Header | Body |
|-----------------------|-----------------|------------------|--------------|-----------------------|------|

And here is a typical IPv4 header:

| Field 1 | Field 2 | Field 3 | Field 4 |
|---------|---------|---------|---------|
| Version | HLen | TOS | Length |
| Ident | Flag | Offset | |
| TTL | Protocol | Checksum | |
| SourceAddr | | | |
| DestinationAddr | | | |
| Options | Pad | | |
| Data | | | |

All of this weighs something, and it's being forwarded across the network constantly. The maximum size of an IP datagram is 65,535 bytes, set by the Length field. The TTL (time to live) is more of a legacy artifact and isn't much used now. The Protocol field, on the other hand, must be UDP or TCP — though other protocols are possible — and the Checksum is computed over the whole header. These details rarely make it into the standards documents; the specifics of implementation are always left to the specialist's judgment as the work demands.

A UDP datagram, for its part, is deliberately minimal: a source port, a destination port, a length field, and a checksum, followed by the payload.

## Passwords

A password is easy to regulate: it should be long and complex. Here's why. With a one-character password drawn from the 26-letter English alphabet, we have 26¹ possibilities — brute-forced in a couple of hours. A six-character password gives 26⁶ = 308,915,776 combinations. (26² is the English alphabet across a two-character password, 26 × 26 = 676.) If only 10 symbols are available and the password is 16 long, we're talking about 10¹⁶ — ten quadrillion potential passwords. Long passwords beat short, complex ones.

You can generate a password with a pseudo-random number generator (PRNG). That gets us past manual guessing, but modern machines can generate and check 100,000 password hashes every second, so relying on password strength alone is a vulnerability and a risk in itself.

An eight-character, digits-only password gives 10⁸ = 100,000,000 variants. Restrict it instead to upper- and lowercase letters and you reach roughly 200 billion — which sounds like a lot, until you remember the machine grinds through 100,000,000 a second and cracks it in about 30 minutes. Layer digits and special characters on top and you get around 7.2 quadrillion, which would take some two years to break. Ten characters yields 477,404,928,000 passwords; a five-character password of two digits and three lowercase letters is 36⁵ = 60,466,176, and that one falls in a fraction of a second.

Two metrics are worth tracking:

- The **False Acceptance Rate (FAR)** — how often the system compares a credential against the database and matches it to the wrong person.
- The **False Rejection Rate (FRR)** — how often the system compares a credential against the database and fails to match it to the very user who supplied it.

Passwords, though, are only half the story once devices start leaving the building — and the first place they leak is the browser. Chrome saves everything marked `type=password` by default. The obvious workaround on internal services is to use `type=input` instead of `type=password`, but the browser doesn't guard a plain text field nearly as well as a password field, and extensions can lift data straight out of an `input`. It's not out of the question that some very large companies have a hand in keeping password-saving alive, given how readily browsers ignore `autocomplete=off`.

On mobile I'd issue a corporate phone and layer MDM on top. If you're committed to BYOD and a user is saving every one of their passwords on the device, then handing each employee a hardware token to slot into the phone's USB port starts to look like a reasonable option. But BYOD is a privilege, not a right, and the user has to accept a measure of inconvenience — DPI, SSO, MFA — in return. At the very least: two-factor authentication, regular password rotation, and no lingering session cookies. The baseline BYOD rules run roughly as follows:

- No confidential data is stored on the mobile device, or it is stored encrypted.
- IT staff can reach the phone remotely, wipe all of its data, and then disable the device if it is lost or stolen.
- Only approved applications may be installed.
- Strict rules govern synchronization, backups, and the use of cloud storage.
- Possibly, the camera and GPS are disabled.
- The screen auto-locks after *n* minutes of inactivity.
- A PIN protects both the device and its mail.
- Form autofill is off.
- Remote data wipe is enabled.
- SSL protection is switched on wherever it can be.
- All software and the OS itself are kept constantly updated.
- Antivirus is present.

## MAC and IP

Layer-2 devices find their way around the network by MAC address, which is unique to each device. Devices under the AMD brand carry the 24-bit prefix `080020` (or `8:0:20`). A MAC address is 48 bits long — take `8:0:2b:e4:b1:2`, which in the machine's own language reads `00001000 00000000 00101011 11100100 10110001 00000010`. As a reminder, a whole frame runs to 1,500 bytes, though in the modern world it can reach 9,000.

- 48-bit addresses that identify the computer's chipset — and therefore the computer system; the format is set by the IEEE, and every computer system has exactly one such address.
- 48-bit broadcast addresses, for delivering a frame to every recipient on the local network.
- Control information, including a checksum. The most basic Ethernet format is a length field and a checksum, but Wi-Fi carries a far more elaborate set of control data for managing transmission over the air, which we come to later.
- Media Access Control (MAC) protocols, which use the control data and the electrical signals — on the wire or in the air — to decide what to do and when to send and receive frames.
- A data field carrying the payload, of variable length up to some defined maximum.

## Wireless networks

Both ordinary internet and Wi-Fi run to IEEE standards. Wireless networks are identified by their SSID; the SSID is precisely what you query to reach a network that isn't visible in the Wi-Fi list. There used to be WEP, but it is highly vulnerable and unwieldy. Modern Wi-Fi protection rests on WPA, WPA2, and WPA3. WPA3 is used first and foremost on public networks, where stealing the secret key is harder. WPA2 uses AES with CCM, which provides both encryption and integrity; CCM in turn uses a counter assembled from a 6-byte packet number, a priority, and a MAC address, which guarantees that every computation is unique.

| Threat vector                    | WPA2 | IPSec Gateway | IPSec laptop | SSL | PGP / S/MIME |
|----------------------------------|------|---------------|--------------|-----|--------------|
| Network traffic from outside     | Yes  | No            | Yes          | Yes | Yes          |
| Other users on our own network   | No   | No            | Yes          | Yes | Yes          |
| Other internet users             | No   | Yes           | Yes          | Yes | Yes          |

Many modern devices support all five 802.11 wireless standards (a, b, g, n, ac), which allows them to work at bitrates of 6, 9, 12, 18, 24, 36, 48, and 54 Mb/s.

In both IPsec and 802.11, the session between two hosts is called an SA. A host need not be a client or a server — it can be a router. For 802.11 there should be four steps:

1. The two devices find each other by way of a probe message.
2. Authentication.
3. Association — establishing the link between the base station and the mobile client.
4. Keys are exchanged over SSL, both sides generate an identical block of key material, and each derives its own keys for traffic. On insecure networks the last step is missing.

DHCP is what lets you flip on Wi-Fi sharing from a smartphone so easily. But if you're streaming video while moving from one access point to another, DHCP won't be enough: each network switch pulls a new IP address, and if that's so easy to do, an attacker can exploit it too. Plainly, it's far harder to stop a neighbor from seeing your Wi-Fi — just as it's hard to know where data went and how many people received it. Bluetooth reaches about 10 metres in the 2.45 GHz band, Wi-Fi about 100 metres, and 4G tens of kilometres. Want to shrink the coverage? Turn down the transmitter's power — though that won't save you from most threats. Spreading the spectrum is possible with direct-sequence techniques, where each bit is represented as several bits.

Wi-Fi runs on routers, typically in the 20–150 metre range. It broadcasts an SSID, the user connects by SSID, and the device remembers it. If we don't want the Wi-Fi to be visible, we hide the SSID. IEEE 802 spans several layers, the lowest being the physical layer — bit handling and signal decoding, antenna characteristics. Wireless networks demand heightened attention to security, which is why RSN (Robust Security Network) exists, describing authentication, access control, and privacy. Even so, 802.11 traffic takes real effort to intercept — at a minimum, you need equipment for listening on 2.4 GHz.

Hiding the SSID, in truth, buys far less than people hope. From a Windows box you can pull every saved network with `netsh wlan show profiles`, then read back any one of them in the clear with `netsh wlan show profile name=wifiname key=clear`. And a hidden network still answers to anyone who asks for it by name — which is exactly what the tooling on Kali does. Find your interface with `ip link`, put it into monitor mode with `sudo wash -i <interface>`, and the networks around you start to surface. A slightly longer route: run `ifconfig` to see the available networks with their addresses, then `sudo airmon-ng` to confirm the Wi-Fi adapter is present, and — for good measure — `sudo airmon-ng check kill` to stop the network managers that would otherwise interfere. Bring monitoring up with `sudo airmon-ng start <interface>`; re-running `ifconfig` will likely show the interface has been renamed. Finish with `sudo airmon-ng <interface>` and the networks around you light up.

TCP always assumes the IP address stays constant for the whole session — so when you move with a mobile device, that device must keep a permanent IP address whose network prefix matches its home network. In IPv6 a mobile device works with two addresses at once: a primary, home address, and a secondary care-of address that changes as the device moves and is valid only while the device is on a foreign network.

At a lower level, delivery is confirmed with an ACK — a small control frame telling the sender the message reached the recipient. If that confirmation never arrives, the ACK mechanism re-sends the original message. This is ARQ, and it exists in several implementations, such as stop-and-wait. The logic is simple: we don't send a new frame until we're sure the previous one was delivered — we don't send the next frame of video until the last one arrived. It sounds dependable, but the ACK can itself be lost or arrive late, and with this approach we can never use the full bandwidth of the network — which we very much need. Suppose we've fired up Zoom and are streaming uncompressed video while on the move: 1920 × 1080, 24 bits, 30 frames per second works out to 1920 × 1080 × 24 × 30 ≈ 1.5 Gb. Stream GSM phone audio instead — 260 bits at 50 Hz — and the channel is 260 × 50 = 13 kb/s (the bitrate). This is why we use the sliding window discussed above: it can put several frames on the wire at once, each with its own SeqNum, so the video frames fall back into the right order automatically.

An ATM cell can only ever be 53 bytes. That size was easier to work with at the hardware level back in the 1980s, and it was well suited to carrying audio, which wants small packets — a principle that remains inviolable for the mobile carriers. ATM nonetheless folded neatly into IP.

To speed things up, a range of protocols come into play. The protocol for real-time data transfer is RTP, which mostly runs over UDP — a transport protocol atop a transport protocol. For acceleration there's also the CDN: a set of servers spread across different countries, all able to serve the same content. WebRTC handles audio and video and doesn't use RTP, but SRTP.

CSMA/CD is a protocol too. Picture a network link over which several devices can send and receive packets simultaneously, so that we need multiplexing. It resembles 802.11 or Wi-Fi, but CSMA/CD can't operate over Wi-Fi.

Token Ring is an obsolete protocol for moving data across a ring topology.

DVMRP is a protocol for multicast routing. Multicast is the distribution of updates, radio, news; given a set of domains, the algorithm simply hands you the shortest path from sources to destination. A one-to-one link is unicast; multicast is many-to-many. IP reserves a special range for multicast under class D.

RPC isn't really a protocol so much as a pattern for structuring distributed systems: the server accepts requests and returns responses. SunRPC and gRPC are the accepted standards.

BGP prevents traffic from looping and runs over TCP. It divides the network into regions, each with its own border routers, or gateways.

Finding a printer on the network is a job for a broadcast protocol. Torrents, meanwhile, are peer-to-peer, where a client can become a server and a server a client.

PPTP is a protocol that can connect Windows to the internet and provides cryptographic protection. It shares vulnerabilities with SSL, but where SSL's problems were compensated for, PPTP's were not. When it comes up in a dropdown, always choose IPSec over PPTP — IPSec is, in effect, a VPN.

TLS is a supremely important protocol in the modern internet. Note that TLS is an application-layer protocol implemented in the browser. It uses the TCP connection established between client and server to deliver content reliably and in order, and it's in TLS that end-to-end encryption of data happens at the application layer. TLS 1 lives atop SSL 3.0, but TLS 1.3 exists for HTTPS. Apple uses end-to-end encryption for all video calls, Meta for its messengers. End-to-end encryption differs from the other kinds: it usually takes place between two people (or two processes), whereas network encryption happens between the software or hardware components of the network — and when you encrypt the network, the link and network headers remain in the clear.

Not all of these protocols fit tidily into the concept of network layers. DNS, for one: we give a site the name `google.com` instead of the address `8.8.8.8` because people are bad at remembering numbers. For safety there's the DNSSEC extension. OpenID, from 2007, is insecure.

But the more interesting and modern protocol — the one we've praised several times already — is IPSec. IPsec is a protocol of components that can't be described within OSI at all. It exists to protect IP packets and works at a lower level than TLS. Configure IKEv1 for IPSec and you'll be astonished at how slow and heavy everything becomes, thanks to Diffie–Hellman, which is what makes the secure key exchange possible; IKEv2, mercifully, is fast. In the modern world you sometimes need asymmetric encryption *and* the ability to move enormous volumes of traffic at once. You need encryption keys to verify integrity — and certainly not MD5, but SHA-512 — and authentication. SHA-3 is a very flexible algorithm with no fewer than four specifications: 224, 256, 384, and 512. We keep in mind, all the while, that a quantum computer could break hash functions and pieces of asymmetric cryptography. Git still uses SHA-1. For Russia the customary choice is 3DES, because it resembles the GOST encryption algorithm; it isn't the most secure, so you might look to AES instead. When we use a secret key, we bear in mind that it is installed into the system ahead of time. SSL spares the end user the bother of installing keys, and it runs over TCP; the SSL handshake relies on RSA to establish the key and on AES as the encryption algorithm.

IPsec was originally designed for IPv6. It lets you authenticate and encrypt all traffic at the IP level, which is exactly what a VPN needs. Because what matters most in a VPN? Ensuring the integrity and confidentiality of the data through ESP. The upshot is that you can view blocked sites through tunneling protocols like a VPN, and in all likelihood no one will be the wiser. Configure IPsec on a router or firewall and you protect all the traffic crossing the perimeter; the end users and their software never notice IPsec is there, yet they get authentication, confidentiality, and key management all the same.

If you need to grant remote access to corporate resources, a remote-access VPN works on a host-to-network basis, letting you connect from a computer into the local network. Another option is the intranet site-to-site VPN, which joins two internal networks inside a single closed network — a company's offices in Malaysia and Dubai, say. And the last is the extranet site-to-site VPN, where two different companies connect to each other's networks. There are other types, but these are simply the most common.

IPSec works with ESP, which is what gives it authentication and encryption, and ESP handles both tunnel and transport modes.

| | Transport-mode SA | Tunnel-mode SA |
|---|---|---|
| **ESP with authentication** | Encrypts the IP payload and any IPv6 extension headers following the ESP header. Authenticates the IP payload but not the IP header. | Encrypts the entire inner IP packet. Authenticates the inner IP packet. |
| **ESP** | Encrypts the IP payload and any IPv6 extension headers following the ESP header. | Encrypts the entire inner IP packet. |
| **AH** | Authenticates the IP payload and selected parts of the IP header and IPv6 extension headers. | Authenticates the entire inner IP packet (the inner header plus the IP payload plus selected parts of the outer IP header and outer IPv6 extension headers). |

As the table shows, ESP can give us confidentiality and communication integrity, and it works with a wide range of authentication and encryption algorithms. For IPv6, ESP operates as an end-to-end payload format. In tunnel mode the entire IP packet is encrypted. Transport mode applies encryption *above* the IP layer, since it leaves the IP header in the clear; tunnel mode applies encryption *below* the IP layer, since the IP header itself is encrypted — and that is the whole of the difference.

## Getting our hands dirty: tunneling on VyOS

Time to work with our hands. Let's set up a tunnel between two VyOS devices. We have two devices; we log in and land at the VyOS command line. Ping the second device with `ping 192.168.110.110`. The host will, of course, be unreachable — the tunnel isn't configured yet. Drop into Kali and set port `br0` to monitor.

Now for the IP-in-IP encapsulation. This protocol adds one more IP layer on top of the existing network-layer IP. The commands for the first router:

```
configure
show
set interfaces tunnel tun0 encapsulation ipip
set interfaces tunnel tun0 source-address 192.168.1.10
set interfaces tunnel tun0 remote 192.168.1.11
set interfaces tunnel tun0 address 192.168.200.10/24
show
commit
```

With those commands we name the virtual network protocol `tun0`, which identifies the tunnel, and tell the system this is to be an IP-in-IP tunnel. We also tell it which of the local IP addresses to use and what the remote device's address is, and in the final step we set the IP subnet and IP address to use within the tunnel. In this example we use the subnet `192.168.200.0/24` and assign `192.168.200.10` to the router. The commands for the second router are identical, save that its address is `192.168.200.11`. Once all of that is done, run `ping 192.168.200.11` and confirm everything is working. Wireshark bears it out — the tunnel is carrying traffic.

But since the exercise was a simple one, the conclusion isn't hard to guess: IP-in-IP encapsulation provides neither confidentiality nor integrity. An attacker along the path can easily eavesdrop on the exchange and alter it; more than that, even an attacker from outside can inject messages into a tunnel like this. To clear the earlier changes, these commands will do:

```
delete interfaces tunnel tun0
delete protocols static route 192.168.110.0/24
commit
```

While we're hardening things by hand, it's worth knowing how to keep the brute-forcers off SSH. On Ubuntu Server 22.04, before you configure fail2ban you have to set up UFW, the firewall built into Ubuntu — fail2ban leans on the firewall to block IP addresses. First, `sudo ufw allow ssh` configures the firewall to permit SSH; then `sudo ufw enable` turns it on. If you now run `sudo less /var/log/auth.log` on the server and try to enter a password, access is denied and the event is written down as an intrusion attempt. On VyOS the configuration works differently:

```
configure
set firewall state-policy established action accept
set firewall state-policy related action accept
```

## VPN architecture and zero trust

A zero-trust architecture is worth sketching out. In it, every connection passes through a stage of authentication and authorization, high encryption standards are required, and it's especially important to have two-factor authorization for every device in the chain.

The scheme is sound but for the caveats. The Point-to-Point Tunneling Protocol (PPTP) is very popular for VPNs and simple to set up on Windows Server — but its 128-bit encryption isn't secure. You're better off looking toward L2TP and IPSec.

## Privacy, anonymity, and the hard problem of electronic voting

Suppose we wanted to build a voting system — which way should we go, what should we read, and how would we guarantee privacy? Start by separating two terms that are too often conflated: privacy and anonymity are not the same thing. Take the example of a state — any state. In that context, anonymity is the guarantee that the state isn't watching you.

And it would *seem* that anonymity guarantees privacy. Privacy is a human right, but not an absolute one: there can be exceptions, in cases involving real or even potential criminals. It amounts to a form of coercion — being compelled to reveal certain data, to hand over a correspondence or photographs. The *absence* of coercion is a precondition for privacy, because there must be no information indicating whom to coerce.

In practice, consider an anonymous messenger on your phone. The messenger needs a SIM card — that's privacy, but not anonymity. For anonymity there can be no phone number acting as an identifier pointing back to you. Encryption in the messenger is anonymity, not privacy: sending a message on WhatsApp, I have no idea what happens on the server, so privacy is in doubt. A VPN, meanwhile, buys privacy only with respect to your internet provider — and it isn't anonymous, since the provider can dump the traffic and knows you by the service contract you signed. Privacy is when I did something and no one found out. Anonymity is when I did something, everyone knows about it, but no one knows it was me. Which is why, when a company demands under threat of an NDA that employees not disclose where they work, yet sticks a label with the owner's name on the corporate hardware, it is — a little strange.

And here the question of the excessive use of leverage arises, of coercion applied on the belief that a crime *might* be committed. Electronic voting is therefore a contentious business, with steep requirements for privacy, non-verifiability, ballot confidentiality, and — most important of all — freedom of expression.

Electronic voting itself unfolds in several stages: first the setup, then the voting, then the counting of votes. In a little more detail, there are four steps of differential privacy. First, compute the privacy budget and the sensitivity of the function. Second, choose a mechanism to hide the result — Laplace. Third, apply that mechanism to the function, adding noise. Fourth, release the data. (Laplace is an algorithm that produces an independent variable when applied to the input data — in plain terms, it adds noise, drawn from the Laplace distribution.)

If you're collecting data, it begins with the gathering of raw data; then noise is added and post-processing is applied. At every stage the user must voluntarily consent to provide their data and must be able to delete it. We also keep documented what counts as Personally Identifiable Information (PII) and for what purposes it is used. Age, for instance, matters in voting, so as not to admit minors — and here the notion of the privacy budget appears, the maximum amount of information we can afford to reveal. Contact tracing is a matter of gathering personal and sensitive information.

A great deal of good material is written into the ISO/IEC standards — there are many of them, and they exist as experts' agreement on the best universal way to solve something, offering a working guideline for how to meet the requirements of the law. ISO/IEC 20889 concerns data anonymization. ISO/IEC 27701 covers information-security management; it is the most important standard here and provides GDPR compliance, adapted to a specific industry, and it introduces the DPIA as a process for understanding and reducing a project's privacy risks. Understanding risk is good risk management: it should improve decision-making and adapt to new risks, and under a DPIA every new system in the company is reviewed and consulted on with the DPO. ISO/IEC 27001 handles risk assessment, establishing the appropriate processes for accepting risk, assigning a risk owner (the legal controller), and defining how risk is to be assessed; a PIMS is mandatory, concerning the protection of the organization's data. It brings its own vocabulary, too — the data controller is the organization that decides how and why data will be processed, while the data processor is the one who actually carries out the work on the data. ISO/IEC 29100 and 29101 provide a framework for structuring privacy around PII; PII includes a name, address, date of birth, ID, phone number, email, IP address — anything by which a person can be identified.

That last framework also touches on anonymizing a dataset. There's the term *quasi-identifier* — the parameters unique to a person that can be used to identify them — and *re-identification*, moving data from an anonymized status back to a de-anonymized one. K-anonymity is a measure of how much generalization has been applied and which attributes carry more weight; it's very popular in medical datasets. So how does anonymization actually go? Email is always unique, so it can simply be deleted. Age is grouped into bands (20–30). Do we even need gender? Probably not. A postal code can be partially masked; a name can be removed altogether. But strip *all* the data out of a dataset and it may become useless for recommender systems, so you have to strike a balance against the goals of the business — Apple wants to know which emoji is the most popular, not which one is *your* favorite. And then there's l-diversity, where for any k-anonymity there exist l variants — not merely iPhone and Android, say, but other operating systems too, with a certain distribution across them.

To pull all this off you need homomorphic encryption and zero-knowledge proof. Homomorphic encryption is when data is stored in an encrypted cloud, encrypted by the user and decryptable only on the user's own machine; the user never reveals their data, because it begins its journey already encrypted and the server never receives the plaintext. Technically, the promising avenue is multiparty computation (MPC) — a secure computation in which the data stays private while the legitimacy of the results can still be confirmed. The criteria for MPC are privacy, correctness, independence of the original inputs, and a guarantee that the result is delivered into the right hands. But MPC algorithms are very heavy to compute and, in terms of data transfer, can run to gigabytes; this is decidedly not a solution for phones, and at present there simply aren't good off-the-shelf implementations. In theory, though, MPC is ideal for privacy — the sort of world in which no one learns what queries I sent to ChatGPT or Google.

A step further, there's MPC combined with FHE (fully homomorphic encryption): FHE to encrypt the inputs, and MPC to keep the public output private. Uniting the two would require fully reactive, on-the-fly computation, but for working with the data of groups of people it would be an excellent solution. It's what makes zero-knowledge proofs (ZKPs) attainable — proving a fact is true without demonstrating all the details, such as a key. Validation comes from the ZKPs, as does protection against collusion; privacy comes from the MPC. FHE-CPA is fully homomorphic encryption; RSA, by contrast, can be considered only *partially* homomorphic, whereas FHE matches the IND-CPA security level. It is, technically, impossible at present.

There are ready-made election systems, such as Helios, with methods for reducing the chance of coercion.

## A networking cheat sheet (for the initiated)

- I'd tell you a joke about UDP, but you might not get it.
- I'd tell you a joke about TCP, but if you don't get it, I'll just keep repeating it until you do.
- Does anyone have a good joke about ARP? Please reply directly with yours.
- Have you heard the one about ICMP? Just checking you're still there.
- Has someone already told you the STP joke? I don't want to create a loop.
- I'll wait for Anton before I tell the QoS joke — his takes priority.
- I had a brilliant XML joke, but I had to wrap every word in tags and now nobody can read it.
- No more than five people are allowed to tell FSMO-role jokes.
- Hold on, everyone — here comes the bus-topology joke, and you'll all hear it whether you like it or not.
- I'd love to tell a Token Ring joke, but it isn't my turn to hold the token.
- Wait, wait — first listen to the joke about interr—
- I remember when a modem joke went pshhhkkkkkkrrrkakingkakingkaking…
- Just now, specially for the whole group at once, a multicast joke arrived.
- A shame the Fault Tolerance joke can only be one word long — so it still lands even if half of it fails.
- It's about time I told the NTP joke.
- I'll tell a great VPN joke, but only the person at the other end of the tunnel will get it.
- Everyone has to establish readiness first before I can tell the SCTP joke.
- Because one of you yawned, I now have to retell the frame-relay joke to everyone on this point-to-multipoint link.
- HDLC jokes are never understood by the people who know *other* HDLC jokes.
- DWDM jokes are told in several colours at once.
- An E3 joke is just 30 identical E1 jokes, plus two more that only insiders get.
- Everyone loves a MitM joke. Well — everyone except Alice and Bob.
- can arrive best The a in thing about pieces order. joke is BitTorrent that the any
- I'd tell you a CSRF joke, but you just told it yourself without realizing it.
- Here's an IGMP joke — please forward it to everyone in the group.
- No… there's nothing… there's nothing funny… there's nothing funny about jokes… there's nothing funny about jokes concerning MTU discovery.
- PPP jokes are only ever told strictly between two people.
- RAID jokes are almost always redundant.
- Fragmented jokes…
  - …are always told…
  - …in pieces.
- Have you heard the Jumbo-frame joke? It's veeeeeery long.
- The best thing about rsync jokes is you're only told them if you haven't heard them before.
- The trouble with IPv6 jokes is they're impossible to remember.
- DHCP jokes are only funny when exactly one person tells them — otherwise there's a conflict.
- Shame nobody remembers IPX jokes anymore.
- Anyone got a cable? I've got a funny RS-232 joke and a half-funny RS-485 one.
- I'm about to tell everyone a broadcast joke, all at once.
- I've got roughly 450,000 BGP jokes.
- If you've got the passwords, come on over for the RADIUS jokes.
- You can just tell yourself the 127.0.0.1 joke.
- Wait — have we already run out of IPv4 jokes?
- RFC1918 jokes can only be told among your own.
- SSH-1 jokes and SSH-2 jokes are mutually incompatible.
- Only one person in this forest gets to tell the Schema Master joke.
- MAC-address jokes might not reach your namesakes.
- The DNS server didn't get the DDoS joke, so everyone started retelling it a hundred thousand times a second.
- With IPSec jokes you have to say exactly who you're telling them to.
- Both GOST and ISO agree there are seven layers of joke-telling.
- The U.S. Department of Defense recognizes only four layers of jokes.
- Jokes about jokes about jokes are most often heard inside tunnels.
- You're unlikely to hear a 10/100/1000BASE-T joke from more than 100 away.
