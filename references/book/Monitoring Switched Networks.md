# Monitoring Switched Networks

*By Maxim Tsvetkov*

Interfaces for enterprise tech companies demand more of a designer than design chops alone; they demand a working grasp of the neighbouring disciplines. Build tooling for network operations and you'll need to understand how switches are put together at the hardware level — TCAM, FIB versus RIB, CEF, and a great deal besides. This article walks through the fundamentals of network design and the rules for visualising what network devices tell you.

Before any of that, though, a word on architecture, because the tooling you design is only as good as your picture of the estate it sits on. When you shape an architecture, you account for the whole inventory. On the hardware side: telephony, workstations, your own servers, rented servers, network gear, cloud capacity, and even the point-of-sale tills wired to fiscal-data operators. On the software side: CRM, CMS and ERP systems, database servers, web servers, the servers carrying business logic, orchestrators, queue servers, analytics stacks (DWH, data lake) — and much more. Miss a layer of that map and the monitoring you build will have a blind spot exactly where the incident lands.

## Switching

Switching itself is simple. It's easy to configure and hard to fix, and the number of ways it can go wrong scales directly with the price of the box. Yet even a generous budget buys no lasting peace: the RB3011 is slow, the RB4011 and RB1100 ship with bugs, the CCR1009 is simply too expensive. Let's work out why some devices cost more than others.

Start with the physical box full of ports — the network switch. Plugged into the network, it moves data from one device to another, using a table of MAC addresses to steer each packet toward the right destination. A packet arrives at an interface, the box reads its headers and checks the checksum. Switches are everywhere in SOHO settings (home and small offices), working at Layer 2 of the OSI model — the Data Link layer. Home routers and company routers are, at bottom, just devices that join machines into a network. Modern hardware splits loosely into two camps by how it handles frames.

The first is **Store-and-Forward**. The box takes in the whole frame, reads the headers, and sends it back out of an interface. If a device is more than ten years old, it almost certainly works this way. The drawback is time — you have to receive the entire frame first. For some businesses response time is everything: in high-frequency trading, milliseconds matter, and sometimes the conversation is about nanoseconds.

![Store-and-Forward mode: red marks the cables where the errors are](https://your-scorpion.ru/wp-content/uploads/2021/06/Artboard.jpg)

*Store-and-Forward mode. Red = errors, showing which cable has the problem.*

The second, dominant in the modern world, is **Cut-Through**. It's heavier to implement — if one side is 10 Gigabit Ethernet and the other is 1 Gigabit Ethernet, you can't do cut-through switching; with matching interface speeds it's no trouble. The two modes swap automatically according to speed, so if the inbound port is 10 GbE and the outbound is 1 GbE, cut-through is off the table. Check the spec sheet for the specific model to see how switching behaves. Most data-centre and large-office switches run cut-through, and you never flip it by hand.

The defining difference is *when* the decision is made. Cut-through decides on the strength of the first 64 bytes, while the whole packet runs to about 1,500. The first 14 bytes are Ethernet, the next 20 are the IP header, the next 20 the TCP header (assuming IPv4). The switch begins analysing the frame the moment those first 64 bytes land; by the time the rest of the packet arrives, the system has already decided where to send it and is forwarding it out of another interface. In PPP, for instance, the packet looks like this:

| 8 (bits) | 8       | 8       | 16       |         | 16          | 8    |
| -------- | ------- | ------- | -------- | ------- | ----------- | ---- |
| Flag     | Address | Control | Protocol | Payload | Checksum/CRC| Flag |

*PPP*

| 8               | 16     |      | 16  | 8            |
| --------------- | ------ | ---- | --- | ------------ |
| Start of frame  | Header | Body | CRC | End of frame |

*HDLC*

![Wireshark showing a reassembled PDU during a TCP session](https://your-scorpion.ru/wp-content/uploads/2023/01/b73f3c0ea8f698fc79b3597e89053745.png)

The Wireshark capture above shows a PDU being reassembled during a TCP session — proof that the large chunk of data had been broken into smaller packets.

This approach has costs. There's no checksum verification, so a bad packet isn't dropped and you can't catch a CRC drop (Cyclic Redundancy Check). Nor can you run an MTU check — the MTU being the largest IP packet that need not be fragmented to cross the network. The textbooks say the MTU check is done in hardware, but since we don't know the frame's size, the decision is taken before the whole frame arrives. What you get in return is faster switching and routing, and a happy high-frequency-trading desk.

In the textbooks it's all tidy: computer → switch → switch → computer. You may split devices into managed and unmanaged switches, the former giving you far more control over the ports. If CRC errors appear, the story goes, the cable between the switches is damaged — clean and obvious. Real life is less obliging: CRC errors often surface somewhere other than where the fault actually lives, and a green network engineer starts swapping cables in all the wrong places. The rule is simple: when you see inbound CRC errors, go to the switch, read the packet statistics per interface to find where they came in, and reason from there. Our job as designers is to light up the interface so the engineer looks in the right place.

![A real error (red) next to a fake one (orange)](https://your-scorpion.ru/wp-content/uploads/2021/06/Artboard-1.jpg)

*A real error (red) and a fake one (orange).*

The second intriguing case. Say a switch has a set of interfaces, all running at 10 GbE. That's the *arrival* rate — packets can't move faster or slower; slowness comes only from timing intervals and physical quirks. Anyone who's held real hardware has seen a counter on the interface called *Input Drop* or *FIFO Drop*, telling you how many packets were discarded. But how can that be? The device's whole job is to shuttle packets to another interface, and if it's 10 GbE in and 10 GbE out, where's the loss?

Here's how. A packet arrives on interface 0 and leaves on interface 3 — and meanwhile the stream also carries packets bound for interface 2. Put crudely: three ports each deliver 100% of their capacity, 300% in total, all trying to exit one port with 100% of capacity.

Interface 2 is saturated. At some point a packet lands in a queue and it backs up behind the packet ahead of it. It has to wait for a free slot before it can be pushed out, and while it waits the others sit in the buffer. The buffer isn't infinite; it overflows, and packets are dropped.

![Head-of-line blocking](https://your-scorpion.ru/wp-content/uploads/2021/06/Artboard-1.png)

Less intuitively still, the switch might be loaded to just 7% and still be losing packets for exactly this reason. It's called **head-of-line blocking (HOL)**, and there is a fix: parallel queues. A switch that can do this costs more — the DES-1026G, say. The more complex the hardware, the pricier the box. The cheapest network adapter, the e1000, manages a single queue; a costlier adapter or switch carries eight, and you never meet the blocking problem. The RB1100AHx4 has a full 25 simple queues. All of this happens in hardware and doesn't cost you speed. Different models exist for service providers, for data centres, and so on — each tuned at the hardware level for a different set of conditions.

On an industrial production floor, networks split into three tiers: the upper tier is the servers; the middle tier runs on PLCs that can stand in for a DCS, giving transparent data transfer across the whole system in real time; and the lower tier is the sensors.

## Addressing

Private networks use these ranges:

- **10.0.0.0/8** — 10.0.0.0 to 10.255.255.255
- **172.16.0.0/12** — 172.16.0.0 to 172.31.255.255
- **192.168.0.0/16** — 192.168.0.0 to 192.168.255.255

The addresses above are four decimal numbers, each 0–255. The first three are the network portion, the last the host portion — the first three a kind of unique location, the last the neighbourhood. In truth the host portion can sit at the front too. Take the Class A address 10.0.10.10: the leading 10 is the network portion and the remaining three values are the host portion. In the Class B address 172.16.10.10, the first two blocks are network and the last two are host — 16 bits each side. In the Class C address 192.168.0.13, the first three blocks are network and only the 13 is host.

CIDR lets you attach subnet masks to addresses and so carve out subnets. Plainly: the ordinary address 198.51.100.0 becomes 198.51.100.0/24 in CIDR form, covering everything from 198.51.100.0 to 198.51.100.255. That /24 you've seen everywhere is the subnet mask — shorthand for 255.255.255.0. So 192.128.23.10/24 is shorthand for the address 192.128.23.10 with mask 255.255.255.0, and 10.0.0.0/8 is 10.0.0.0 with mask 255.0.0.0, i.e. the range 10.0.0.0 to 10.255.255.255. Originally there were no subnet masks at all, only classes: A ran 0.0.0.0–127.255.255.255, B ran 128.0.0.0–191.255.255.255, C ran 192.0.0.0–223.255.255.255. Those three classes are the ones still live on the public internet for unicast, spanning 0.0.0.0–223.255.255.255 (with some exceptions). The 224.0.0.0–255.255.255.255 range is for multicast and isn't carried on the public internet, only inside companies. You can play with CIDR at [ipaddressguide.com/cidr](https://www.ipaddressguide.com/cidr).

It's worth pausing here on a few things that trip people up, because addressing is where careers stall in interviews. A **range of IP addresses is not the same as a LAN**: the smaller the subnet mask, the more subnets you can carve. A 192.168.0.0/24 (255.255.255.0) can hold 254 hosts; drop the mask to /16 (255.255.0.0) and you get 65,534. A **host** is a single machine; a **domain** is a logical grouping — different things entirely.

For the OSI layers people forget: **Layer 6 is Presentation** — imagine a message in ASCII, where A = 41 and y = 79, but the device you bought is IBM and speaks EBCDIC; the Presentation layer holds the protocols that translate ASCII ↔ EBCDIC. **Layer 5 is Session** — the Citrix ICA protocol lives here. Both mattered in the 1970s and are all but irrelevant today. For **email**, receiving runs over POP3 (110/995) and IMAP (143/993), while SMTP (25/465) takes a message onto the server and relays it onward; in each pair the second port is the encrypted variant.

On subnetting proper, any address divides into three parts: network, subnet, host. Take 10.25.156.193 with mask 255.255.248.0. The network ID (10) is fixed by class — here Class A. When the mask is variable-length rather than classful, the 255.248 portion produces this: the first two octets, 10.25, never move (their mask is 255), while the subnets step as 10.25.0.0, 10.25.8.0, 10.25.16.0 and so on, all masked 255.255.248.0 — the 25.8 being counted as the subnet. The quickest way to place a host in its subnet is the **magic-number** method. In 172.26.88.44/29, the /29 corresponds to 248 in the fourth octet (255.255.255.248); the magic number — the step between subnets — is 8, so only the fourth octet moves in steps of eight. The subnet IDs come out as 172.26.88.40, .48, .56, .64 and so on, with 172.26.88.44 sitting inside the .40 subnet whose usable hosts run .41–.46. There's also the **wildcard mask**, written inverted — 255.0.0.0 becomes 0.255.255.255 — which lets you alternate sequences of zeros and ones and so express intricate subnet-selection rules. If you'd rather not do the binary by hand, tools like [jodies.de/ipcalc](http://jodies.de/ipcalc) and [subnetting.net](https://subnetting.net/) do it for you.

![Subnetting worked through as a table](http://your-scorpion.ru/wp-content/uploads/2022/01/Screen-Shot-2022-01-14-at-9.36.35-AM.png)

On, then, to how the devices work. A device has a **Receive** side and a **Transmit** side. Receive first behaves as a physical interface, taking in the electrical current, and then forms an inbound queue. The device holds a set of tables — L2 forwarding (CAM) to steer packets one way, an Access List (ACL), and others. A mid-range switch handles 5–6 terabits per second, which is a lot: billions of packets, each to be checked against the tables. Enter **TCAM**, where a match can be made on three states — the classic 1, 0, and x, x meaning *don't care*. After that the packets join the egress queue and the traffic leaves.

We lean on those on-device tables heavily, but they aren't infinite — there's a hard limit on entries. So an Access List has its ceilings, as does the number of routes, and both are bound directly to the chipset. This is a physical limit: no firmware update will make more data fit on the silicon. Hence devices at different price points — one learns 8,000 MAC addresses, another 200,000. Tables are never made arbitrarily large.

An **ACL (Access Control List)** is a list of subnets or IP addresses, used to restrict which traffic may cross an interface, for NAT, QoS, VPN tunnel selection, and for filtering routes in the routing table or the routes you advertise and accept. At the end of every ACL sits an implicit deny: anything not explicitly permitted becomes forbidden. **QoS** prioritises particular traffic — VoIP gets high priority and runs at 800 kbps while FTP is held to 130 kbps — leaning on metrics like network reliability, end-to-end transit time and its stability, and the resources available for transfer. It pairs well with SD-WAN.

Configuring an extended list with an explicit protocol:

```
ip access-list extended <100-199> | <2000-2699> | <>
permit tcp 192.168.0.0 0.0.255.255 8.8.8.8 0.0.0.0 eq 53
permit ip host 172.23.21.3 10.0.0.0 0.255.255.255
deny ip 172.16.0.0 0.0.255.255 10.0.0.0 0.255.255.255
permit ip any any
implicit deny any any
```

Better than ACLs are **prefix-lists**, which help you filter routes — to catch everything from /17 to /24 you need only write `17 le 24`. Useful commands:

```
show ip prefix-list
show run | in ip prefix
show route-map
```

So: the L2 forwarding table is CAM, the L3 forwarding table is FIB. Beyond simply moving packets between interfaces there's logic that lives outside those two tables, in TCAM, describing the extra functions.

The CAM table is mandatory on any device, holding three values: MAC, egress port, VLAN. On some switches TCAM is optional — no queues, no classifier — and its absence usually marks a cheap device. Suppose you want to forbid traffic between two hosts: you write an ACL on the switch saying host 1.1.1.1 may not send to host 1.1.1.2, and you have thousands of such rules. Checking every inbound and outbound frame against all of them serially would crawl. TCAM lets you check for a rule match in a single pass, so you can run your ACLs in peace.

If the destination MAC is in the table, the match is found in hardware and the system says which port and VLAN to send the packet on. A **VLAN** is nothing more than isolation at the link layer — segments of the network holding machines. On a machine, `ip a` surfaces plenty of network detail for troubleshooting; on a plain home router you're probably on DHCP with a dynamic IP, and `sudo dhclient` renews it.

![Output of `ip a`](https://your-scorpion.ru/wp-content/uploads/2021/11/Group-2.png)

A router works much the same, but it has jobs beyond forwarding. As on a switch there's an RX side for arriving packets, everything drops into a queue — though the queues can be organised differently — and from the queue we reach the TCAM tables, now joined by an L3 forwarding table (FIB) alongside the L2 one (CAM). On top of that a router rewrites the L3 header (the hash has to be recomputed) where a switch rewrites nothing: the router enters an L3 rewrite, changes the header, and hands the packet to the egress queue and then TX. That extra work raises the device's complexity — brutally hard to implement in the early days of the technology, which is what once multiplied a router's price several times over.

In practice, imagine a set of routes:

- 10.0.0.0/8 via 192.168.0.7
- 10.0.0.0/16 via 10.0.9.7
- 10.0.0.0/24 via 192.168.1.95
- 10.0.0.0/27 via 192.168.9.3
- 10.0.0.0/32 via 172.16.0.7

A packet for 10.0.0.4 matches the first four entries. The device picks 10.0.0.0/27 via 192.168.9.3 because it's the most specific — the fewest addresses inside it. That's not the simplest logic: routing goes by IP *and* by the most specific prefix.

A sample FIB table:

| IP Address  | Next-Hop IP  | MAC of Next-Hop   | Egress Port          |
| ----------- | ------------ | ----------------- | -------------------- |
| 10.0.0.0/27 | 192.168.0.7  | 00:1B:44:11:3A:B7 | Gigabit Ethernet 0/0 |

A sample CAM table:

| MAC-address    | Egress Port | VLAN |
| -------------- | ----------- | ---- |
| 0000.0000.000A | 1           | 34   |

Making that choice in hardware is genuinely hard, and procurement will push you toward the cheaper box, which adds its own dance with tambourines. The technology that came to the rescue is **route-cache**. The router has a central processor, and TCP or UDP flows run across the network (TCP is harder to filter than UDP, so it especially wants DDoS protection). When the first traffic of a flow hits the interface, that first packet goes up to the CPU, which decides which interface to send it out of; the packet leaves, the CPU builds a cache, and every later packet in that session takes the same port — no FIB lookup, just *I've seen this flow, send it the familiar way*. The cache is flushed periodically, roughly every ten minutes. It sounds wonderful, and it's essentially retired — you'll meet it only on very old gear. Many sessions mean much work for the CPU, and the CPU should not be busy. If it is, suspect a MAC-table overflow, a `debug` left on, or a flood of SNMP or routing-protocol chatter. It's also worth glancing at ARP, whose whole idea is to help data reach its destination by resolving the MAC of the next router or device on the path. Run your very first ping right after configuring routing and it'll vanish, because the ARP table isn't populated yet. Gratuitous ARP refreshes every table — and hands you a man-in-the-middle attack, so lock it down immediately. If a lot of resource is going to the net background process, expect a lot of port errors; you may also hit memory and SDM-template trouble, which these commands help you catch:

```
show sdm prefer
show sdm prefer access | routing
sdm prefer access
show platform tcam utilization
```

The modern approach is topology-based, or **CEF**. It rests on a cache built in advance — and Cisco worked out how to build it ahead of time, since the routes already sit in the routing table. Only when CEF genuinely can't process a packet does it go up to the CPU; the rest of the time the processor's resources go entirely to forwarding. The Forwarding Information Base (FIB) is assembled from two kinds of table — the routing table plus an adjacency table (loosely, the ARP table) — with no CPU involvement, which is precisely what we want. When the CPU *is* dragged in because traffic can't be handled in hardware, that's a **CEF punt**, and there's a counter showing how many entries get sent up.

By example: the route to 10.1.0.0/16 sits in the FIB alongside 10.1.1.0/24 and 10.1.1.128/25, the masks growing steadily longer. Addresses in the FIB are sorted by the most specific mask — **Longest-Prefix Match (LPM)**. A packet arrives, the device checks the destination and quickly finds the route with the longest match.

So far we've discussed devices that are pleasant to run: glance at the buffer, find the outbound interfaces, forward the traffic. More complex boxes exist. Pizza-box units, 1RU high — and the port count always tracks the silicon, how well it's cooled, how far apart the chips sit, and power draw, which is why one box can hold only so many ports. Need more? Meet **modular devices** like the Nexus 9500, dense with ports.

You buy such a box to get port density inside a single chassis. It's expensive: 600 ports is 600 servers, an enormous estate, and few companies in the region even run a fleet over 600. If that one box dies, the whole estate goes with it — which can mean the death of the business. So redundancy is baked into the management plane: the device's management module is the **supervisor** (control plane). We spoke earlier of a CPU managing the silicon; here there are two supervisors — one active, one waiting for the first to fail.

Large companies tend to use AAA with RADIUS, or TACACS on Cisco. With many admins at different levels of privilege, everyone needs account-based access, and there's a dedicated server in the network holding those accounts on a domain controller with Active Directory, with a RADIUS server taking on the job of validating credentials. Under Microsoft Network Policy Server, the correct settings look like this:

![Microsoft Network Policy Server settings](https://your-scorpion.ru/wp-content/uploads/2022/02/реаер.png)

A **line card** is a module (in the server world, a *blade*) — technically a switch on a chip. You can mix different line cards, but there's a single supervisor, which raises a problem: traffic can arrive on one physical device and leave from another, and since those are different chips, you have to teach them to exchange table information.

The typical life of a packet in such a modular box: there's an inbound line card and an outbound one, and for a signal to cross from A to B the cards must be physically joined. In comes a layer called the **switch fabric**, responsible for connecting every line card. Depending on the box, line cards are built differently — for instance, a separate network processor (NPU) with a group of ports it serves, and different port groups served by different NPUs. A module called the **PHY** handles the physical signal: we take the signal in, the PHY hands off to the network processor, and the work happens — packet classification, queues. The queues feed the fabric interconnect and buffering, after which the switch fabric can glue packets into super-packets, cutting the number of interrupts and letting it process more. Inside the fabric an arbiter watches whether there's enough bandwidth for the packets.

![Switch fabric in a modular device](https://your-scorpion.ru/wp-content/uploads/2021/06/Artboard.png)

All of this is complicated, and packets can be dropped at any stage above. The moment you're asked to fix slow TCP sessions, the pain begins — you'll spend an age working out *where* the drops happen. So answer yourself honestly: do you actually need a modular box, and will it earn its keep? Sooner or later it'll need fixing — packets will start dropping, that's inevitable reality — and there are no simulators for these boxes, because it all runs in hardware.

For contrast, look at the silicon inside a simple switch: ports, ASICs (application-specific integrated circuits) and a CPU. The layout is fairly standard — call it a Catalyst 9000. Blue marks the physical ports where data arrives.

![The ASIC layout of a simple switch](https://your-scorpion.ru/wp-content/uploads/2021/06/Group-85.jpg)

Green is the ingress forwarding controller; an arriving packet passes through a FIFO queue. A scheduler with a packet buffer ties ingress to egress, and the packets fly out of the right port. Simple?

Two directions, two chips. **Ingress** handles L2 and L3 lookup, a policer to cap traffic (no more than 100 Mbit/s even if 10 GbE is technically possible), tunnelling, a classifier to split traffic, ACLs and much else — all in parallel. **Egress** does much the same but without the lookup, adding an out-policer and traffic-copy (SPAN). The simplest framing: traffic from the internet into the LAN is ingress, LAN-to-internet is egress — though the terms are interchangeable enough that practitioners rarely bother with them. At the in-FIFO the packet is duplicated: the original goes to the buffer, a copy to the ingress controller, and the headers are read from the copy. The tables (CAM, TCAM, FIB), which may live in different places, are consulted, a decision is made, and it's written to a packet descriptor; that goes to the buffer holding the original, then scheduler → forwarding controller → headers rewritten → packet gone. Incomparably simpler than any modular system like the ASR 9922.

### Native VLAN

Few people know how to use it, and knowing how is a competitive edge. We already know the two interface modes: Ethernet access, and 802.1Q (dot1q) encapsulation on a trunk. But what if you need to send *untagged* Ethernet frames across a trunk — a common enough need when diagnosing problems or running a data centre?

```
interface GigabitEthernet0/1/6
switchport access vlan 5
```

A patch cord joins two switches. A tagged VLAN-10 packet crosses from one to the other and all is well — until one day a packet arrives with no tag, and it lands in the **Native VLAN**.

![Native VLAN handling](https://your-scorpion.ru/wp-content/uploads/2022/10/Group-48095828.png)

| Port | MAC |
| ---- | --- |
| 1    | 6E  |
| 2    | B3  |
| 3    | 3F  |
| 4    | DF  |
| 5    | C2  |
| 6    | A7  |

*MAC address lookup*

Picture a switch whose every port has a server plugged in, because servers are mostly used for virtualisation. You get a pool of virtual machines, saving resources; each VM lives in its own VLAN, and the server runs a virtual switch — in effect, the server *is* a switch, hosting VMs across different VLANs, with virtual machines joined to the virtual switch by virtual cables.

Now, you buy the device, rack it, and it has no operating system — installing that is your job. At scale nobody images servers from a laptop. You could use KVM, but when 4,000 new servers arrive in a month a better path is this: the server's BIOS sends a DHCP message to a DHCP server and gets back an operating system with its configuration. In more detail, boot involves the motherboard and its many chips: the crystal oscillator sends clock pulses, the first to clear the processor's internal registers; then an address in binary, burned into the board at the factory, points to a ROM cell (non-volatile memory) holding the processor's first instruction; from there the BIOS runs, sends test bytes to every cell to confirm all is well, and the bootloader starts the OS. (If you're doing this the small way and just need a bootable ISO or USB stick, Microsoft ships a dedicated tool for it — the Media Creation Tool — and you're spared the ceremony.)

There's a catch, though: a server out of the box knows nothing about VLANs. The server side presents an access interface while the switch side is a trunk. Reworking ports from access to trunk is slow and inelegant. Using the Native VLAN parameter, by contrast, is elegant: a trunk port can carry Ethernet frames, so the server boots, all its messages land in a VLAN, the OS installs, the VMs start. Native VLAN is used only on trunks, and `switchport trunk allowed vlan [xxx]` opens the tags you want — or, to hand a client just VLAN 34, `switchport trunk allowed vlan 34`.

The usual VLAN problems:

- An SVI is given an address from the wrong subnet.
- The VLAN was never created on the switch.
- The wrong VLAN is assigned to a port.
- No physical port on the switch is in the UP state.

Start diagnosing with:

```
show vlan brief          # VLANs created and their access ports
show interfaces trunk    # every trunk
show running-configuration
show interface switchport # all Layer 2 parameters
```

— and, failing all else, bounce the VLAN.

## Redundancy and Spanning Tree

Now, redundancy. The first protocol we meet for taming it runs on top of the network and is called **Spanning Tree**, though you'll rarely see it in its original form now. Don't disable it — but do understand it, because it's foundational to switched networks. Not everyone loves it, since it blocks interfaces, yet there's no good reason to turn it off. The one exception: if you're a lead engineer plugging into a network you don't control, it's defensible to disable it on that single interface.

Networks aren't built without redundancy. Take three switches: if one cable fails, connectivity is lost.

![Redundancy across three switches](https://your-scorpion.ru/wp-content/uploads/2021/07/Group-86.png)

The staple troubleshooting commands: `show interfaces counters` for basic per-port traffic, and `show interfaces counters errors` for the error statistics.

But Ethernet has a problem — packets can loop forever, and every switch dies in the loop. The conclusion: the network must be *physically* redundant but *logically* not. You teach the switches to understand the topology and jointly decide which port is surplus. **Spanning Tree Protocol (STP)** switches off everything surplus at the logical level.

Pair a $100 switch with clumsy engineers and you can disable STP and wait for consequences. One day an installer trims a couple of cables and splices them together, or the cleaner plugs a lead into the port of a decommissioned router just to get it out of the way — and you get a broadcast storm, MAC flapping, a PortFast loop, the wrong root bridge. Helper commands:

```
show spanning-tree vlan <>
show spanning-tree bridge
```

![Spanning tree in operation](https://your-scorpion.ru/wp-content/uploads/2021/11/Group-169.png)

Loopback Detection (LBD) is meant to help but doesn't always fire, and it's often left off, especially on cheap kit. There are many ways for things to go wrong — broadcast control begets a broadcast storm and the whole network folds — so build it robustly from the start. Here's how.

The control plane can switch interfaces off logically by exchanging packets called **BPDUs**; the switches confer and decide which port to disable. But switches aren't clever devices and need the help of a chief — and one switch is always the chief. Every switch has a unique MAC address, and the lower the MAC, the more senior the switch. (If you know the term BSSID: a MAC serves as the ID for a BSS.)

![Every router has a factory MAC address](https://your-scorpion.ru/wp-content/uploads/2021/08/microtic.jpg)

*Every router carries its own factory MAC address.*

The snag: MACs are assigned to switches in sequence, so the oldest device ends up the most senior. You'd rather nominate a newer box, and you can, via the **priority** parameter — the lower the priority, the more senior the device. The chosen switch becomes the **Root Bridge**, and the L2 topology is computed from it.

![Root bridge topology](https://your-scorpion.ru/wp-content/uploads/2021/07/Group-88.png)

Time to block the unwanted interfaces. Ports run at different speeds, and blocking the fast ones loses you throughput, so you have to block the redundant *and slow* port, not merely the redundant one. Speed is governed by the **cost** parameter, derived from how long a packet takes to reach the root switch. If cost ties in every direction, you fall back to the last resort: the sender bridge ID. The switch with the lowest bridge ID originates the BPDUs; they arrive at the other switches and move on, each switch adding root and sender bridge and the cost value — and whichever has the higher sender bridge ID is the one that blocks its ports. And if someone patches one port into another on the *same* device? Then **port priority** decides — each port has its own, and the lower value wins.

By now we can name the basic operator tasks: set priority correctly, identify the redundant links, and filter BPDUs on interfaces that shouldn't take part in building the tree.

STP is a very old protocol and runs on timers. The principle is clear enough — a switch receives a message, checks its tables, passes it on — and the CPU can create and send BPDUs too, though once a root switch exists, only it originates them. An inquisitive young hacker might picture plugging their own switch into the network in place of a computer and setting its priority to 0, making it the root so all traffic flows through their box — and it can work. The switch might even be software: in Windows it's *Network Connections → Bridge Connections*. As network engineers we may want to pin a specific switch as root so an access switch can never crown itself at the top of the topology — only a core or distribution switch should. STP alone won't give us that.

![Bridging connections in Windows](https://your-scorpion.ru/wp-content/uploads/2021/07/windows.gif)

But there are answers. The first is **BPDU Guard** on access ports: the port may send BPDUs, but if an external BPDU arrives, the interface goes dark and Layer 1 stops. An engineer later finds an *err-disabled* incident, investigates, and brings the port back by hand. For end devices, BPDU Guard is excellent. The second is **Root Guard**, which fights BPDUs arriving with high-priority IDs so the port can't become a root port — use it when you're certain who the root should be. The third is **PortFast** (EdgePort), for an end device joining STP: in the ordinary listening → learning → forwarding sequence, packets don't pass for 30 seconds at startup and the internet is unreachable — unthinkable for a modern user on an end device, which needs connectivity instantly. PortFast skips the fuss of listening and learning and jumps straight to forwarding, safe because an end device can't form a loop. And the **BPDU filter** — handle with great care — disables STP on the port entirely: no BPDUs leave, all inbound are ignored, which is dangerous for loops; but if you're plugging your switch into an ISP's network where they run a switch of their own, the BPDU filter is your tool. It's also worth disabling **VTP**: plug a switch in without wiping its config and VTP can delete every VLAN on every switch — so set it to `transparent`.

We covered the cleaner plugging a cable in and breaking everything. Consider the reverse: the cable is *pulled* and everything breaks. The devices must swiftly move one root port to another, and that takes nearly a minute of lost connectivity — picture a modern person offline for a whole minute. STP is good at finding redundancy but runs on timers, and anything that runs on timers is, by design, rather dim.

So we use the more modern **Rapid Spanning Tree Protocol (RSTP)**, which isn't timer-bound. Better still: in classic STP every device merely receives a BPDU and passes it on, enriching but never originating it; in RSTP any device can generate a BPDU, letting devices talk in a proposal/agreement handshake. On receiving a proposal a switch confers with its neighbours — blocking ports and agreeing, or not, that the proposal holds the best BPDU value — then forwards the best one onward. Every change in the network prompts a change in topology. RSTP and STP are compatible, so if the cleaner plugs in an old device everything falls back to STP correctly. A fun detail: if a port sends an RSTP message and gets no reply, it tries a plain STP message before changing state — which is slow, which is why you always configure PortFast (and PortFast also assures you that L3 devices won't provoke needless network reactions).

Now let's make life harder. **Multiple STP Instances**: you create instances, each holding VLANs, and a tree is built per instance, becoming an MSTP multi-region arrangement with one zone as the Common and Internal Spanning Tree (CIST) — a set of nesting dolls, regions within which sit instances with their permitted VLANs. Your professional goal is to *not* have an MSTP multi-region spanning tree: you'll never hunt L2 faults quickly through that matryoshka. So revision, instance and name must always match.

![Multiple Spanning Tree instances](https://your-scorpion.ru/wp-content/uploads/2021/07/Artboard-Copy-2.png)

Same with **multicast**: lose packets in multicast and it's a nightmare to fix, the usual culprits being the RPF check, getting the rendezvous point right, and multicast drops. Multicast is meant to move traffic more efficiently, so it does its work only on the switch closest to the end devices — for IPTV streaming, say, that's a perfectly workable story.

Multicast lives at both L2 and L3. A packet carries IP and Ethernet headers, and a BPDU is an example of an L2 message; you may have many switches that can't route and work only at L2. At Layer 3 (IP), multicast spans 224.0.0.0–239.255.255.255. Multicast is always a *destination*, never a sender: a server sends to 225.0.0.1, we have no multicast MAC as such but a set of recipients in the form of computers — and the packets start spraying in every direction like a plain broadcast.

To the rescue comes **IGMPv2**, which announces that a new computer wants to receive multicast: the client sends an IGMP message asking for traffic on 225.7.9.100, and it travels through the switch fabric to the router, which now knows there are multicast receivers in the switched network. To deliver packets to exactly the client that asked, we use **IGMP snooping** — and so multicast at last behaves at least a little differently from broadcast on a switched network. There's also **IGMPv3**, which lets you choose the source, so you no longer need rendezvous points — this is **SSM (Source-Specific Multicast)**: with a routed network and a source server, the **RPF check** prevents loops by inspecting the sender's IP against the routing table, accepting multicast only from interfaces that hold a route back to the sender.

The server streams to a multicast group; the computer knows nothing of the server and simply sends its wishes to the switch — an IGMP message asking for multicast on 225.0.1.2. But how is the router to know who the sender is? Sender and receiver meet at the so-called **rendezvous point**, which the devices know about, and they build the multicast tree from sender to receiver. At Layer 2 the devices send IGMP messages expressing their wish to take part.

![Multicast delivery](https://your-scorpion.ru/wp-content/uploads/2021/07/Artboard-Copy.png)

**PIM Sparse Mode** is the most popular way to deliver multicast, needing a specially prepared router to act as the rendezvous point; every device knows where to send the multicast. Less popular are Bidirectional PIM and PIM Dense Mode. And as ever we want fault tolerance, so we insure ourselves by duplicating the rendezvous-point role — solved with MSDP or a Phantom RP.

### Fault tolerance

In the diagrams above, routers connect straight to other routers. Usually, though, a router plugs into a switch — often a router reaches another router *through* a switch — and where there's a switch there's STP. The situation: the provider sends us a BPDU with priority 0 and, for our network, the provider's switch becomes root. We already know the fix: enable the BPDU filter on the interface facing the provider. But if two interfaces run to the provider, the BPDU filter turns dangerous. What then?

Ideology first. Always try to follow the accepted reference architectures. That's how you get maximum fault tolerance, high scalability, easy onboarding of new modules and services, easy maintenance, and comfortable troubleshooting.

![Recommended reference architecture](https://your-scorpion.ru/wp-content/uploads/2022/09/Group-2.png)

The finest and most reliable is an **L3 design**, possibly with micro-segmentation (many firewalls). But an updated **L2 design** is popular too. L2 is classic stacking, where a special cable fuses several switches into one logical device — except that cable is expensive and short, so nobody uses cables any more; everyone has moved to stacking over **VSS**. You join two switches into a VSS pair with links and get a logical device, a virtual stack: each keeps its own data plane, the management plane is shared, and you can usually combine up to eight.

Routers with software switching use NICs — the packet hops from NIC 1 to NIC 2, every bit copied straight across the I/O via DMA while the CPU checks the header in memory. The bottleneck here is the memory limit. Suppose a device handles 40 million packets per second and the average packet is 64 bytes: 40 × 10⁶ × 64 × 8 = 2.048 × 10¹⁰ ≈ 20 Gbit. Not bad — until a 16-port switch splits that evenly and each user gets a shade over 1 Gbit at best. STP, RIP, OSPF and the like are part of the control plane; the above is the data plane, and the strategy for separating the two is **SDN**.

An L3 access layer means, obviously, L3 switches, with the user's gateway being the local switch and routing done from the access switch. L3 designs are dear but common, and the upside is real: webcams are notorious broadcasters, choking an L2 network, whereas an L3 architecture bounds the broadcast domain so no storm reaches you — even BUM traffic can be tamed.

Fault tolerance and redundancy are tightly bound. A switch was plugged into a server; we add a second switch and run a spare cable, gaining redundancy, and STP blocks the surplus. In a large network, any change costs STP re-convergence time. And don't forget the switch is a $10,000 lump and the transceivers $1,500 each — you buy all this, install it, leave some ports idle, and wait for something to break so the kit earns its keep. Happily, SNR transceivers cost a fraction of that and do the job: run them from access to distribution over 10-gigabit links and you're fine. Need more? A QSFP+ module gives 40 gigabits, QSFP28 gives 100 — priced about the same, except not all kit supports QSFP28. Transceivers are consumables and fail within three to five years; one way to spot it on a dual-fibre transceiver is to loop an optical patch cord across both ports — the port comes up, because you've made a loop.

Single-fibre bidirectional transceivers carry both receive and transmit on one strand, which suits buying a whole route where you pay per fibre. The transparency windows might be 1270 and 1330 nm, so differing wavelengths avoid interference. Order a 40-kilometre run and it might be twenty one-kilometre segments, each splice adding attenuation, and you gauge that attenuation from the OTDR trace — which also records the wavelength it was taken at, perhaps 1550 nm. Fit a transceiver and it can't light the far end because everything attenuates — because yours is 1330, not 1550.

Assume the standard Cisco three-tier hierarchical model: access, aggregation, core, each tier with its own kit and rules. There's always the temptation to buy a pile of switches and wire them together, fast and cheap — because doing it properly is expensive. But with a poor architecture, when something breaks it's always agony. Stick to a two-tier (no core) or three-tier model if you expect the business to grow. A core layer earns its place when you have a large building with many aggregation pairs and every floor collapsing onto access switches; and if you're designing the core, pick the most capable routing hardware you can. A collapsed-core network is awkward to extend.

**Port Channel**: physical interfaces become one logical interface — and one interface can't be blocked by STP. There are subtleties: the switch must forward traffic, but traffic leaves a *specific* physical interface, so you have to choose which one — a packet can't split into atoms and be reassembled at the client. (It can, actually, but spread packets across interfaces and the client may get them out of order, 3-1-4-2 instead of 1-2-3-4.) So a Port Channel always caps a single session at one interface's speed. Because different interfaces may deliver different volumes, you need **load-balancing algorithms**. Cheap switches balance on source and destination MAC; costlier ones on source + destination IP; a 5-tuple marks a modern device. It works like this: four interfaces belong to one Port Channel, a hash is computed over source MAC + destination link and taken modulo the count — hash 73 / 4 leaves 1, so the traffic exits port 1. With a 5-tuple the traffic spreads evenly; old models spread it unevenly and there's nothing to be done.

There's a danger in bundling interfaces into a Port Channel differently on the two switches — it leads to an STP loop or packet loss. The fix is simple: Port Channels come static and dynamic. A static Port Channel can break STP; a dynamic one is control-plane, and LACP PDUs automatically strip out mis-configured ports. That's fine between switches, but running LACP to a *server* isn't so carefree, because the server gets its OS by DHCP, and LACP assumes both sides (switch and server) support it — a server out of the box has no LACP, and the ports block. Set `static` instead of LACP and you can catch a storm, because static has no auto-negotiation at all; LACP must be `active` on at least one side. `port-channel load-balance <>` is your friend.

Another fault-tolerance family is **FHRP**, comprising GLBP, HSRP and VRRP. On Cisco K9 you'll use HSRP, spotted by the `07.ac` in the MAC; GLBP is rare and used for balancing. These give first-hop redundancy: you assign two routers the same IP and, by priority, they answer client requests, exchanging keepalives so that if one dies the other steps in with its own unique MAC. Earlier we touched only on switched networks running on switches — but duplicating switches isn't always wise; sometimes you need to duplicate L3 routers. Assign two routers the same IP naively and it all goes wrong, yet duplicating the router's function is exactly what we want.

```
# HSRP
standby 1 ip 192.168.1.1
standby 1 priority <>
standby 1 preempt
standby 1 timers <> <>
standby 1 track <> decrement <decrement-value>
standby 1 version 2
track <object-number> interface <> {line-protocol | ip routing}

# VRRP
vrrp 1 ip 192.168.1.1
vrrp 1 priority <>
vrrp 1 preempt
vrrp 1 timers <> <>
vrrp 1 track <> decrement <decrement-value>
track <object-number> interface <> {line-protocol | ip routing}
```

You could give the second router a different IP, but the client machine has only one gateway field while there are two IPs — you can't enter two gateways through the GUI (you can via the console), yet traffic is still lost when one router fails, since end devices normally hold a single gateway. The solution: assign different IPs anyway and create an HSRP group on the router interfaces, assign a **virtual IP (VIP)**, after which the routers exchange L2-multicast HSRP packets; one becomes master, the other slave, and the instant the master dies the slave seizes the VIP. An L3 switch and a router are similar as devices, so just compare specs: the router routes better (more protocols), the L3 switch has more ports.

An end device isn't always a computer — it may be a server, and plug a server into a switch and its failure takes everything with it; fifty VMs, gone. To hedge, dual-home the server with two interfaces, each an L3 IP, an IP each side — but the moment you create a bridge, STP kicks in and blocks one interface, so a bridge on the server is exactly what you don't want. What's left? A Port Channel. Add a second switch: you plug the server into two switches and assemble a cluster/stack, joining the two switches with short stack cables so a single OS manages both — or three, up to eight.

![Stacking switches into a cluster](https://your-scorpion.ru/wp-content/uploads/2021/07/Artboard-Copy-3.png)

Beyond servers, an end device can be a mobile phone. Phones have MAC addresses too, and you can identify a device by one — but modern devices aren't naïve: since iOS 16, MAC randomisation is on by default. A meaningful share of Androids can still be tracked for now, though the data quality will only decay. To answer any product designer's next question in advance: from mobile MACs you can *statistically* infer what sorts of people passed by and their interests — but you can't measure a specific user's contacts with offline advertising this way. A router has an air-scan frequency, and each vendor sets it differently, standards and processing methods varying too, so you can drop a sniffer on a billboard or in a cinema and get an acceptable read — for Androids.

Which is a natural moment for the physics of wireless, since it governs all of the above. Radio is a wave, and a wave loses amplitude passing through the clutter of any urban space — sometimes to the point of losing the information — which is precisely why city antennas sit on towers: a centimetre-band wave can be pressed from 1 down to 0 and can't bend around obstacles. If you're wiring a home rather than a billboard and can't decide where to put the router, a powerline (CPL) adapter is a perfectly good answer when there are many walls, plenty of sockets, and no fixed spot for devices — the internet simply runs over the mains. Wall material decides everything: 80 cm of monolithic membrane is brutal, and you'll drag the signal through only with slow repeaters that aren't about speed — twisted pair or fibre otherwise. Rely on Wi-Fi alone and, since it's a wave, a router jammed into a corner leaves dead zones and re-reflection losses, and the higher the frequency, the worse the penetration.

The same physics shapes how you'd build a short-range wireless device — say, Bluetooth meant to punch through walls at around 25 metres, driven from an app. The lever is the **modulation**: the popular choices are frequency and phase modulation. With frequency modulation you might have 100 MHz and add another 20 via a summer to reach further, and against obstacles you vary and combine the 100 and +20 — 100, then 120, in rhythm — so that the difference in frequencies, plus the summer, lets you recover a clean stream of ones and zeros; the receiver then needs an antenna and a circuit that strips the other frequencies out and reads back the bit sequence. Two devices on the same frequency interfere, and since even free space attenuates electromagnetic waves, there's always absorption and the receiver always sees a smaller amplitude — the farther the device, the greater the loss. Your levers are to raise transmitter power, raise receiver sensitivity, or narrow the bandwidth, and otherwise to escape other devices' noise (which is to say, go out into a field). Bluetooth version 1 you'll only meet if you dig an old Nokia out of a warehouse; it runs at 2.4 GHz. For IoT devices the usual pick is **BLE**, and you decide whether your Bluetooth lives on the host and talks to a controller, or is a single host-plus-controller unit, or something else again — for a build like that I'd look at an nRF24L01 paired with RemoteXY.

Back to the wired world. What can go wrong now? The weak point becomes the switches' operating system — one OS across two devices — so stacks solve some problems and create others; calling it a fault-tolerance technology doesn't sit right. Better to look at **M-LAG** or **vPC**: two switches with a keepalive running between them, so you have not a cluster but two separate devices. L3 gets fiddly, but case by case, vendor by vendor. See what a good solution looks like? One sentence and everything's clear. Another option is to ignore L2 altogether: no L2 on the server, routing on the host — a choice for large companies, where the rule holds that the bigger the scale, the simpler the solutions have to be.

## Monitoring

The starting point for monitoring devices is **SNMP v2 and v3** (v3 being the more secure). A network engineer constantly needs to pull statistics to spot problems — and a problem should be caught before it becomes visible and before it dents any company metric. SNMP polls the devices; for visualisation you'd use Cacti with Weathermap, SolarWinds, Zabbix, or PRTG — but each needs configuring.

Don't forget security: the switch isn't the only box in the network. Cisco IOS IPS, for instance, is not a router but a full intrusion-prevention system, guarding every entry point (firewalls, VPNs) with its own signature database. And the monitoring you build *is* part of the security posture. The rogue-root trick we described earlier — an attacker's switch claiming priority 0 to sit in the traffic path — is exactly why BPDU Guard exists and why the dashboard should surface an unexpected topology change loudly. Two more defensive habits worth designing for: attackers who get a foothold go after credentials, so treat the domain controller's Active Directory database and the memory of the process that holds live credentials as crown jewels, and monitor for anomalous access to them; and never lean on SMS as a serious second factor, since messaging over legacy radio has well-documented interception weaknesses. Corporate Wi-Fi, likewise, is routinely under-secured and deserves regular, authorised penetration testing rather than blind trust. None of this is the monitoring tool's headline job, but a dashboard that ignores security is only telling half the truth.

The standard dashboard tiles are interface counters and CPU/memory load, shown as factoids (the honest kind, not fake ones). If the processor is pegged, the switch reboots. It's also useful to know how much power is being drawn. All of this is configured and monitored via SNMP — but SNMP gives you data not in real time, rather every few minutes and aggregated, so changes aren't shown live and an operator can't tell whether a spike happened in a single second or the change accrued linearly across the whole interval.

![QSW-IM1200-8C](https://your-scorpion.ru/wp-content/uploads/2022/10/QSW-IM1200-8C.jpg)

*QSW-IM1200-8C*

![GS-6322-24P4X](https://your-scorpion.ru/wp-content/uploads/2022/10/313.jpg)

*GS-6322-24P4X*

The second problem: a switch has a great many counters, and collecting everything from the control plane hits performance dramatically — a constraint you have to design around. The reverse flow is also possible: the switch tells SNMP about a situation the instant it happens. That's an **SNMP trap**, which fires immediate notifications on critical events — an important interface goes down, paint the dashboard red *now*. SNMP is a monitoring protocol, so password resets, reboots and config delivery belong to it too. When trouble strikes, **ICMP** is the troubleshooting protocol — a plain `ping` rides on it, protocol number 00000001 — and, worth knowing defensively, a covert tunnel can be layered over ICMP to smuggle TCP data, which is precisely the kind of thing your monitoring should be able to notice.

Logs have to live somewhere. **Syslog** takes events off a device and ships them elsewhere; writing to a physical medium and keeping logs locally is fine, but you usually forward to a Syslog server and keep three to four months, which is normally enough. Syslog messages carry a severity, 0 being most important and 7 least; by default you see level 7 only with debug on. Configure with `logging host <>` and `logging trap 7`, and `show logging` shows the history so you can reconstruct what happened. You can stand up a modest KIWI Syslog Server; if none is installed yet, Tftpd32 will do; ideally, run Graylog. Syslog can also route messages through the **Embedded Event Manager (EEM)** — meet a defined pattern in the logs and EEM runs a custom script. The severities matter:

| Severity | Keyword        | Meaning                             |
| -------- | -------------- | ----------------------------------- |
| 0        | emergencies    | System is dead                      |
| 1        | alerts         | Immediate action required           |
| 2        | critical       | Critical condition                  |
| 3        | errors         | Error                               |
| 4        | warnings       | Warning                             |
| 5        | notifications  | Normal but noteworthy               |
| 6        | informational  | Informational message              |
| 7        | debugging      | Debug                               |

One more ingredient of trustworthy dashboard data is an **NTP server** — the source of exact time. You want a single source of truth: exact time from the internet, or a satellite clock feeding time to every device, which matters especially on an air-gapped segment. Every device has its own clock, and it's almost always wrong even if you know `clock set 10:21:00 DEC 12 2021` — and a few seconds' drift is critical to an engineer. Add time zones and you use UTC, the prime meridian defining midnight as 00:00 at Greenwich. Configure it thus:

```
ip name-server 8.8.8.8
ntp server pool.ntp.org
clock timezone UTC+3 3
show clock
```

NTP works master/slave over UDP ports. Sometimes you must distribute time across L2 devices, and to keep the company's enemies from forging it, you authenticate NTP:

```
config t
ntp authentication-key 1 md5 NTPpassword
ntp trusted-key 1
ntp authenticate
```

(A geographic aside: Iceland is treated *de facto* as being on permanent summer time, holding UTC+0 all year since 1968 despite sitting more than 15° west of Greenwich.) Exact time comes from Stratum 0-1-2-3 servers, the lower the number the more precise — GLONASS is Stratum 1, and Stratum 0 is an atomic clock.

Simple solutions tend to win, and ease of setup depends on how available the information is. Right now **Zabbix** leads for monitoring network equipment: it reaches devices over SNMP and records the metrics to a database, and it integrates well with Grafana for solid visualisation. There's a simpler option, **LibreNMS** — where Zabbix costs me a day to set up, LibreNMS takes half an hour — though it's network-only and its built-in syslog collector is far from the best. Open-source options: Zabbix, BGPlay, Pmacct, SNAS, Topolograph, NAV, NOC Project. Proprietary: Cisco WAE, Ciena Blue Planet, Juniper WANDL, IP Fabric, Kentik.

For traffic *flows* you use **NetFlow**, which ships the headers of packets arriving on your kit to a NetFlow collector — it runs only to L3 devices — and on into an ELK NetFlow stack: Logstash (ingest) → Elasticsearch (processing) → Kibana (visualisation). For metadata collection, FOCA will do. NetFlow shows only headers, so the statistics are hard to reason from. The alternative is a dedicated NDS server, but again the statistics limp and it's usually a paid solution; ideally, a MikroTik paired with a DPI/IPS/IDS and a SQUID proxy.

## Visualisation

There are accepted patterns for laying out statistical information. The search bar always sits top-centre. The top-left corner is for information summaries — a viewer's attention lands there first, so it should hold the most important information (or the centre, if that's the stronger visual accent). Dashboards usually break into five large blocks.

The essence: a dashboard is like a set of pockets — the user should know at a glance which pocket to reach into. It's an instrument panel that tells you the state of the system in a single look, and the idea is borrowed from machinery, where speed, revolutions, fuel remaining and roll angle are all shown at once. The user's goal isn't to stare at unchanging information for hours but to catch only what's *new*.

Because the widgets sit one after another, they should form a story — this is **consistency**. The eye's saccades let it read similar information quickly, so if two things aren't related, place them well apart — perhaps on separate tabs of one page, or on different floors of the scroll. A designer trying to cram the whole product onto one dashboard is rarely making the right call; yet the dashboard's purpose is to save the user time and effort by showing everything needed at once, without scrolling or extra interaction and without clutter.

The most critical statistic should be bright and top-left. Here the **Von Restorff effect** is at work — the one object that stands out is the one remembered, and Hedwig von Restorff typically found red the most memorable. Be careful, though: a poorly judged design can instead trigger banner blindness (Jakob Nielsen). And it's always a good idea to let people customise their dashboards — we remember information better when we had a hand in creating it.

Which components to use?

### Pie charts

For showing proportions — the share of incidents by type, how an analyst's time splits, budget across directions, feature-usage ratios, the most-attacked sites. The chief problem with a pie chart is holding in mind which colour maps to which value, which fights against short-term memory; by the Brown–Peterson measure, short-term memory runs to about nine seconds. So constant back-and-forth between legend and chart is *acceptable* — but not always.

### Bar graphs

Bars can sit in any order. Most bar charts run in sequence — lowest to highest (ascending) or highest to lowest (descending, the Pareto chart). But when values fluctuate over time, as they do on many dashboards, you may want them alphabetical or ordered by some other factor.

There are many further visualisations — choropleth, line chart, scatter plot, box plots, stacked bar. And to test a dashboard, UX specialists reach for a handful of research methods. **Usability-lab studies**: the respondent comes to your office, to a specially equipped room, and works through tasks on a prototype or live rig alone with the researcher. **Ethnography — field research**: the researcher goes out to the respondents' workplace, where they use the product, or solve their problem *without* it, perhaps with a competitor's product. **Focus groups**: 3–12 participants who discuss a set topic and carry out particular tasks. Between them, they tell you whether the pockets are in the right places — and whether, at a single glance, the panel tells the truth about the system.
