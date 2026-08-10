# Client-Server Architecture of Casual Networked Games

*Maksim Tcvetkov · Product Design and Security Lead*

---

Good afternoon. In this article I'll try to give a general overview of the role of the server and of ping in animation for casual games. Everything here is presented with a bias toward casual games running on TCP protocols; large commercial projects often use UDP + TCP, and they have far more tricks up their sleeve.

To begin, let's agree that architecture is the set of important, accepted, and agreed-upon decisions — not merely C4 modelling. Architecture is a consequence of requirements, and those requirements obey the Vitruvian triad: durability, functionality, beauty. Next comes the business domain — the basic building blocks of the field. Then the functional requirements (FRs). And the more technical, non-functional requirements: security, legal compliance, stakeholder risks, client interests, strategy, infrastructure, lifecycle, and so on. Monolithic or distributed architecture? Elastic or predictable scalability? A relational database or a combination of technologies? Synchronous or asynchronous processing for timings? Failure prevention, or invisible (graceful) failures? Large updates or small ones?

Forming such an architecture is a process in its own right. You begin by identifying who is responsible, then agree on the processes and documentation — Event Storming works well here. With Event Sourcing you put up sticky notes and isolate the main entities of each subdomain, gather the functional and non-functional requirements, work through the use cases, and only then start implementing. What you end up with is a set of artifacts: the domain and its subdomains, a shared business glossary, the data flows, an infrastructure diagram, a service diagram, and the SLAs. There's no shortage of methodologies to lean on either — Clean Architecture, Hexagonal (ports and adapters), Onion, DDD, CQS, CQRS, n-tier. To make it concrete, take a learning-management system: the functional requirements would be the schedule, webinars, the student list, homework assignment and grading, payment processing, and quizzes; the non-functional ones might be an account with a proprietary CPM, 200k monthly active users, everything squeezed into a two-hour window across eleven time zones, a franchise model, and customer data that must stay in-country.

Obviously, in online games there is — besides the player — also a server. The server is a fiendish little machine that takes money and qualified specialists to set up; it is precisely the server we'll entrust with handling the gameplay. The player and the server need some way to exchange data, so let's talk about that.

Consider the P2P protocol. **Peer-to-Peer (P2P)** architecture means that each computer connects to a shared network and each computer has control over the game's data. By analogy, each computer is also a server, since it sends data to other client devices on request — that is, all computers are equal peers and form a peer-to-peer network among themselves. In other words, P2P is a complete absence of security. Both data tampering and inconsistency between two peers are possible here. Of course, P2P isn't witchcraft, and the technology has its place in some cases — for example, a serverless game of tic-tac-toe — but in practice it is only suitable for games with short session lengths and a small number of players playing simultaneously in one session. Let me note right away that across Russia and the post-Soviet space a great many gamers have all sorts of MonsterDebugger and other cheating programs installed for extracting and substituting data, so the developer has to set up additional protection methods. But protecting something that lives on the user's computer is very hard. So what do we do?

Simple! We offload all the work from the player's computer onto the server; the server is what will process the players' actions and simply broadcast the results of its work. This is what's called **client-server architecture.**

Since in the past — and sometimes in the present — I'm a Flash developer, let's look at ways of exchanging data with the server using Flash-game development as an example (yes, Flash can't use anything but TCP protocols :)). To start, let's pose the most down-to-earth question of the modern schoolkid: how can you hack a Flash game? Recompile it. Or use alternative Flash clients with modified functionality, or even hex editing. But all the information the game needs is processed on the server! In that case, attacking our game's client is effectively useless — it has little value, being merely a program for displaying the gameplay. The client sends messages to the server and has little influence over the game logic. So, to ensure security, it's best to do all computation on the server side. That much is clear.

The client, then, is mostly a window onto the game — and even getting that window to open can be fiddly. Running an SWF on a Mac is a classic example: when no browser will play it, the standalone Flash Player is the reliable fallback. The heavier burden the client carries is the art, and art has a way of ballooning, with sprite sheets swelling to a hundred megabytes if nobody keeps an eye on them. It helps to understand why the same image can report a different size in your editor than in the file explorer: the explorer shows it compressed, while some editors show it raw. The raw figure is easy to compute — one byte per channel, four bytes per pixel, so 32 bits per pixel, which makes a 2048×512 sheet 2048 × 512 × 32 / 8 / 1024 / 1024 = 4 MB. The simplest fix is [TinyPNG](https://www.npmjs.com/package/tinypng-cli), though as a compressor it will thin out the color palette; running it from the terminal works well, with the caveat of a 500-image monthly limit. Better still, wire TinyPNG into [Gulp or Webpack](https://www.npmjs.com/package/gulp-tinypng) as a build-time plugin so it isn't left to the designer at all. JPEGs can go through [mozjpeg](https://github.com/mozilla/mozjpeg), and if you don't need soft transparency you can keep the alpha separately at two bits. And if you're serious about the web, reach for NVIDIA Nsight when debugging — you may find the real culprit is shaders and batching rather than the sprites themselves.

But once everything lives on the server, ping appears. Ping is the time a message spends travelling from the client to the server and back. And there's no honest way to fix the situation. As a result, the delay adds jerkiness and other lags to the game. In fast games, if a player runs 5 metres in 900 ms, then with a 900 ms lag the player teleports 5 metres. The same goes for effects. If a player stepped on a mine and managed to run back, but because of ping lag this information doesn't update in time, the player will be thrown by the blast wave in entirely the wrong direction from what they expected. I think the problem is clear. That said, 100 ms of latency is perfectly fine — only professional players, not casual ones, will notice it.

You can't solve the problem by simply handing the task to a programmer; ping cannot be eliminated. But you *can* create the illusion that such delays don't exist. If a player has good ping — under 20 ms — you can quite reasonably deceive them. Namely, you reconstruct what ought to be on the second player's screen. You simply guess the player's actions and use interpolation to smooth the animation. There are many interpolation methods, from ordinary linear interpolation (smoothly moving the player from where they are to where they should be) all the way to cubic-spline interpolation, but these are all half-measures, because the time spent moving the player to the correct position can mislead other players. And all delay-related problems are caused by players interacting with one another. The server holds the most up-to-date information about players' actions, and until the server tells the game clients about those actions, the actions don't happen. We'll leave prediction for the flight of an explosive projectile — prediction is only good for things with linear motion.

We'll orient ourselves exclusively around the server, receiving information about a player's actions only from the server; until the server says so, nothing changes. But the player will certainly be unhappy with the game character's untimely reaction to key presses. The player simply loses control over their hero. What do we do? We lean hard on the designers and animators, of course }: ). Let the animation contain delays — a character decelerating after a run, crouching before a jump, the weapon turning before a shot — and this lets us hide the imperfections of the client-server architecture. And, obviously, it makes sense to make the game a touch slower than the art director dreams of; this too helps provide a buffer of time for exchanging data with the server.

The control scheme shapes how forgiving those buffers feel, so it's worth pausing on it. WASD has been the de facto standard ever since World of Warcraft, though it really took hold back at a 1995 Quake championship, and it doubles nicely for camera control. People periodically go hunting for something better — WADX with S for crouching, or ESDF, where reaching Shift or Ctrl is harder, and the occasional oddity like ASXC — while the newer Dvorak keyboards add their own confusion, since their keys don't follow the old typewriter. For a casual title, though, fighting the standard rarely pays off.

There's something else from optimization: multiplayer games scale as O(n²), where n = 128 — these are the players. One player makes a move, so all players need to be told about it, and for each player a connection has to be created. The amount of transmitted information, as you can see, is very high, and the load on the network grows. But one player can't see all 128 players at once under any circumstances. So the number of connections can be optimized. And if half of those 128 players are on our team, the amount of information about their actions can be optimized too — after all, it's not certain they can deal us damage or even collide with us, depending on the game's design.

You already know that interpolation with linear smoothing is a rather ambiguous solution that can't really be called good, whereas the cubic-spline interpolation mentioned earlier looks more interesting. From school maths everyone remembers that a linear function can be represented by an equation with a first-degree variable — for example, x = 5·y. In a cubic function, at least one term is raised to a power: x = 5·y². More on that here.

There are other tricks too. For example, everyone knows that in MMORPGs moving a character by clicking on the location is common. And it's so simple! We pass the information about where to send the character and start the animation — no problems. The character runs, the game draws a beautiful, living picture, and the server has time to do its work. In the worst case, you can reduce the player's speed by a few percent, which buys a buffer of a few dozen milliseconds and will be unnoticeable to the player.

Let's go a little deeper into the problem of writing the application. Configuring IP addressing on the client side is inconvenient. Imagine a typical casual player setting up IP addressing themselves. Not a pretty picture. The desire to spare the user such configuration is quite understandable. For this, DHCP is used — a host configurator. It sets up not only the IP but also the hostname, DNS, and so on. A DHCP server manages data about the computers on the local network, as well as information about the default gateway, the domain name, the name server, and the time server.

How does it work? A simple broadcast is used via DHCP, which works over the UDP protocol. The DHCP client on the computer knows in what format and what to send. At layer 3 of the OSI model there's no IP yet, so the destination is 255.255.255.255 and the source is 0.0.0.0. At layer 2 we don't know the MAC address; the destination address is ffff.ffff.ffff.

We want to send a bit of information out to the internet — say, a login and password. We've gone online, and at home we already have our DHCP server. It might be an access point or a satellite router that hands out IP addresses to all devices connected to the network. Our device contacts the DHCP server and says it has no IP. And the server hands out an IP, a subnet mask, DNS, and other information. The server provides the information instead of the player configuring everything by hand.

1. The first stage, **DHCPDISCOVER**, where we simply fire a message into the network saying we want to obtain any IP address at all.
2. The second — the computer receives a number of **DHCP Offers**.
3. The third step — once we've received what we need, we send a **DHCP Request**, thereby confirming we're ready to take that IP address.
4. The fourth step — the server confirms the handover of the IP address. The IP address is leased; the downside here is the interaction type — broadcast. The DHCP server has to sit in the same broadcast domain as our computer, whereas we'd prefer the DHCP server to be located centrally.

This is solved with a DHCP relay, which passes the message to the DHCP server, sending a specific UDP packet with the same source port. A broadcast arrives at the router, and the router converts the message from broadcast to unicast. After that the message flies off to the DHCP server as unicast; it looks at the source IP, figures out the subnet, and replies back as unicast. This is how IP addresses are handed out to all devices centrally. All of this sounds rather complicated. The simplest thing you can try in order to understand it is this chain of commands:

```
ipconfig
ipconfig /release
ipconfig /renew
ipconfig /all
```

While we're in the Windows command line, it's worth remembering it's the same place you end up when something far more fundamental breaks — a machine that refuses to boot, say. Dropping into Safe Mode by holding Shift during startup (or force-restarting with a five-second press of the power button) gets you to the Command Prompt and MSConfig, where `sfc /scannow` repairs system files, `chkdsk c: /f` checks the disk, and the `bootrec` family — `/scanos`, `/fixmbr`, `/fixboot`, `/rebuildbcd` — together with `bcdboot c:\windows /s c:` rebuilds a broken boot record. Back to addressing.

We have a DHCP server and a couple of VLANs. We want addresses for two subnets to be issued from the DHCP server. But there's a transit subnet between them, and broadcast traffic from the VLANs won't reach the DHCP server. This is exactly the problem the DHCP relay solves — forwarding the replies to the client. A few more useful commands:

- `show ip dhcp binding`
- `show ip dhcp conflict`
- `show ip dhcp pool`

The transport layer of the network comes to the rescue: we have a server, and several services run on it. At the network and data-link layers there are no guarantees that packets will be delivered. There are all sorts of services with physical addresses, logical addresses, and host groupings, but successful delivery isn't guaranteed. The second problem: how is the server to tell the traffic apart? So the transport layer is needed to isolate applications from one another and to provide the right type of interaction — for cases when guaranteed packet delivery is, or isn't, needed.

There are two protocols here: TCP and UDP. For example, a phone conversation is a huge number of tiny packets. The voice is processed by a codec, digitized, and transmitted in small portions over the network. Let's suppose it's transmitted letter by letter. The word "hello" is sent as "h-e-l-l-o." If the packet carrying one of the "l"s is lost, a second later we no longer need it. Here's a different example, though: transferring money from one bank account to another. It's important to transfer exactly the amount the user specified. We can't lose a single digit. In that case we have to wait for the lost information.

TCP guarantees packet delivery or else ends the session. UDP is considered faster than TCP, but it's a question of how much information is transmitted. For a small amount of information UDP is faster, but for 1 GB TCP will be faster. If we're working through DNS to obtain the address bound to a domain name, UDP is perfectly applicable for that — as it is for LDAP / TFTP. A simple message and a simple reply. The rest is TCP (RDP, SSH, Telnet).

![TCP vs UDP](https://your-scorpion.ru/wp-content/uploads/2021/01/Frame-24.png)

Guaranteeing delivery of all the information isn't simple. The client and the server know nothing about each other. Packets are numbered, and to begin exchanging data TCP establishes a connection. UDP doesn't establish a connection, which is why it's faster with a small amount of data. Sending a DNS query to a local DNS server is precisely a job for UDP. The computer sends a special TCP message with the TCP SYN flag, and on the server there's some program you can connect to on port 80. And the sockets sit there, waiting for new connections.

The packet arrives at the server, and the server replies with the acknowledgement + syn flags (SYN-ACK). It says its reference point = y. The client computer replies to the server with packet y + 1, and after that the data exchange begins. This is also how the SYN flood attack works: a TCP connection is imitated and the server is buried in packets, simulating the first stage of establishing a TCP connection.

To set up a session, TCP uses a minimum of three packets — this is called the 3-way handshake. Even if your internet speed is 400 Gbit/s over SONET, that won't speed up the connection-establishment process in any way. There's a first handshake via SYN from the device to the server, a SYN/ACK reply, and a final ACK. If the server is far away, there will be a large delay measured in milliseconds. The propagation of light between St. Petersburg and Moscow takes 5–7 ms, and on top of that the light has to be converted into electricity — a process that can't be sped up. And between Moscow and New York the delay is 200 ms. Once the three little packets have done their job, you can establish HTTP or HTTPS and fetch the website from the server. This is a great example of when we stop working at layer 4 of the OSI model and move to layer 7. Incidentally, TCP/IP also has layers: the network-access (link) layer for packets over cables → the internet layer for IP addresses → the transport layer for protocols (TCP, UDP) → the application layer (HTTP, TLS, DNS, file and mail transfer). The application layer in TCP/IP combines the OSI session, presentation, and application layers.

Now it's UDP's turn. There's a computer and a server; the computer simply sends a packet toward the server. And there's no function to verify packet delivery. If, when writing the application, we specified the wrong IP address, we'll never find out.

Connectionless protocols are used when small portions of data need to be delivered and when the loss or ordering of the transmitted messages isn't critical — when transmission speed matters more than guaranteed delivery of every message. UDP is good for online broadcasts and VoIP (where the choice is between voice distortion / packet loss on one hand and delay / waiting for all the packets on the other). And UDP is popular in the implementation of online games.

The problem is that there are few IP addresses, and some are unavailable to computers — for example, the range 224.0.0.0 – 239.255.255.255 cannot be assigned to our network adapter. So NAT is used, which assigns a group of non-internet addresses a single public IP address that faces the internet. NAT is a terrible, retrograde technology, but it exists.

Ports. We type some URL into the application (the browser), and since the browser is by default geared toward working with the web, the port is either 80 or 443. So we don't have to type the port by hand. We always know it; it's set by the developer in the application. HTTP = 80, HTTPS = 443, DNS = 53. You can write it out explicitly — `http://ya.ru:45/` — and then we'll try to knock on port 45. If Yandex has no page on that port, nothing will open.

Well-known ports are 0 – 1023. Above them we can see a few reserved ports. Registered ports run from 1024 – 49,151 and are used for client applications.

That's about the destination port; the source port is chosen at random from 65,000 options. This allows different sessions to be created between the same sender and recipient. Sockets, meanwhile, use a 5-tuple: protocol, source IP, source port, destination IP, destination port. These five values make it possible to create a unique session — and it's often the source port that does it. The only case where ports can run out is load balancers.

A small experiment: IP address + port = a socket, which looks like `8.8.8.8:452`. This is the entry point for exchanging data between devices. Let's send information between two sockets. We'll create two Python files and run them one after the other in two separate terminal windows:

```python
import socket

sock = socket.socket()
sock.bind(('', 9090))


while True:
   print('Listening...')
   sock.listen(1)
   conn, addr = sock.accept()
   print('Client:', addr)
   data = conn.recv(1024)
   print(f"Found the data: {data}")
   conn.send(data)
   conn.close()
```

```python
import socket

while True:
   message = input("You: ")
   sock = socket.socket()
   sock.connect(('localhost', 9090))

   sock.send(f'{message}'.encode())

   data = sock.recv(1024)
   sock.close()

   print(data)
```

![Socket exchange demo](https://your-scorpion.ru/wp-content/uploads/2024/03/ezgif-7-9a72487ab3.gif)

Note that the same ports are specified. Another experiment is the [dmitry](https://www.kali.org/tools/dmitry/) tool, which lets you see ports and much more. The command `dmitry example.com` will give you an excellent summary of a site. And adding the `-f` flag switches it into advanced mode.

![dmitry tool demo](https://your-scorpion.ru/wp-content/uploads/2025/05/%D1%84%D0%B2.gif)

Browser tooling deserves the same hands-on treatment. When you're debugging a web game and want to see how it behaves with pieces missing, you can selectively cut off resources from the front end using Network request blocking, tucked away in DevTools under the top-right menu → More → Network request blocking.

![Network request blocking](http://your-scorpion.ru/wp-content/uploads/2022/07/Network-request-blocking.gif)

And if you'd rather DevTools open automatically with every new tab, launch the browser with the `--auto-open-devtools-for-tabs` flag.

**Switching.** To learn how to work with networks you need not only to read but also to get hands-on. There are two options: either emulation with Cisco Packet Tracer, or virtualization software — that is, the real software of real devices. The second option is preferable, and it can be done with both free and paid software. Cisco Modeling Labs and EVE-NG are paid hypervisors; for free work, create an account on [cisco.com](https://developer.cisco.com/site/sandbox/) and use the lab exercises as your playground. Alternatively, buy yourself a MikroTik router, which has a lot of interesting settings. And definitely don't get involved with ZyNOS — very vulnerable.

**Native VLAN.** What is a switch? We need to switch little packets. A device has ports; packets arrive at some port, show their headers, and fly out of another port. The switch makes its decision based on layer 2 of the OSI model (Data Link), where MAC addresses are involved. All devices connected to the switch can talk to one another through the switching domain. But if our company has admin, designer, and programmer departments, their devices shouldn't be able to talk to each other. We need to split them into subnets. For the split, layer 3 of the OSI model is used. A VLAN is a way to separate some devices from others on a switch.

![VLAN separation](https://your-scorpion.ru/wp-content/uploads/2021/11/Group-182.png)

So, we have a switch and a server. A typical task: we need to push an operating system onto thousands of servers — this is done with DHCP + PXE Boot. But by default our server is just a piece of hardware: useless and knowing nothing about itself. And a Trunk is required on both sides. We resort to a Native VLAN. This is the access role for a Trunk interface. The interface's operating mode is Trunk, and its native VLAN = 1 (a port in Trunk mode passes the tag). If clean Ethernet arrives at the corresponding interface on the switch, it will be encapsulated with VLAN ID 1.

![Native VLAN](https://your-scorpion.ru/wp-content/uploads/2021/03/Group-32974.png)

Logical and physical topology very rarely coincide in real life. Thanks to VLANs we create whatever we want. Can you reach from one VLAN into another? An ARP request will return nothing.

And now let's introduce the concept of protocols. A switch doesn't know how the network topology is arranged. We connected the network somehow, as in the picture above, but the network device itself doesn't hold such a picture inside it. Routers are aware of the topology; switches aren't. At L2 we need to tell the switches about the topology. If redundancy appears in the network, packets can loop and the network will stop working. Yet the network must be redundant for fault tolerance — we need additional paths in case individual interfaces fail.

The first protocol is STP (Spanning Tree Protocol), which logically finds redundancy in the network and switches that redundancy off. It teaches our switches to hold a dialogue with one another. BPDU messages are used. First, the main device is chosen as the top of the network tree, using the Bridge ID (MAC address + Priority). The lowest MAC address = the oldest device, so we set the Priority by hand as a reference point — at least some control over the network.

The Bridge ID size = 8 bytes. Of that, 2 bytes are allotted to the switch's Priority relative to other switches — the switch's priority compared with the rest. The value can range from 0 to 65535 in steps of 4096 (0, 4096, 8192, and so on). Also, each interface is assigned a cumulative Cost metric, and the network is built according to this metric. The lower the metric value, the better.

So, switches and the whole of layer 2 of the OSI model are a so-so area for flexible network operation. SIP (5060 / 5061) helps switches communicate about topology, but the only switch allowed to send messages is the Root Bridge. The problems: downtime appears, and if one interface breaks there's a 20-second wait to transition from state to state. The RSTP protocol comes to the rescue — it's about speed. We forget about timers and instead react to changes. The logic of how switches communicate changes. Switches with RSTP can send messages themselves. So, instead of the disabled, blocking, and listening states, we're left with a single discarding state. Learning and forwarding remain as they were.

Now everything's fine, but each RSTP is one instance per VLAN. That problem is solved with MSTP.

Let's return to our favourite problem: the fight against loops. The general advice is that everything should be built in little triangles. Yes, providers may turn a micro-district into a ring because it's cheaper for them, but data centres are mostly little triangles.

Now, routing. A layer 2 port is a switch port without an IP address; a layer 3 port = a router port with an IP and a MAC. At layer 2 of the OSI model a packet simply runs around the switched local network and flies into all the ports. This approach works up to about 1,000 devices, but the internet is far bigger — even a modern apartment building has a large number of devices. Hence the need to limit the broadcast domain, and we introduce the concept of a new device: the router. On a router you populate the routing table in the following ways:

- We write a **static route.** Looking at the routing table on Windows is easy; in PowerShell we type `netstat -rn`. The Gateway will be the *Next Hop*.
- **Dynamic** population of the routing table. There are two kinds of protocols for populating the routing table: distance-vector and link-state.

![Hub-spoke topology](https://your-scorpion.ru/wp-content/uploads/2021/03/Group-32982.png)

*Hub-spoke*

Population methods: there's a set of routers, and they tell one another about the networks they're aware of. The result is a RIB.

The illustration shows how, every 30 seconds, a message is sent out to all interfaces where RIP v2 is enabled. The routers start talking; R3 tells R2 that it has the network 10.0.0.0/24 and indicates a distance value = 0. R2 records this in its routing table and increases the value to 1. R2 sends a RIP update to R1, and a record is made that 10.0.0.0/24 is reachable via R2 with a metric of 2.

![RIP update](https://your-scorpion.ru/wp-content/uploads/2021/03/Group-32985.png)

If R3 suddenly dies, R2 will remove the record from the table. And hello to the loop between R1 and R2, since there's no knowledge of the topology. This is fixed with split horizon. But RIP works on timers — and that timers are bad, we've already established.

Example routing table:

| Room          | Destination   | Subnet Mask     | Gateway       | Interface | Metric |
| ------------- | ------------- | --------------- | ------------- | --------- | ------ |
| A             | 162.132.9.0   | 255.255.255.156 | 162.132.9.130 | 1         | 10     |
| B             | 162.132.9.128 | 255.255.255.44  | 162.132.9.210 | 2         | 1      |
|               | 162.132.9.160 | 255.255.255.153 | 162.132.9.221 | 4         | 10     |
| Default route | 0.0.0.0       | 0.0.0.0         | 162.132.9.221 | 4         | 10     |

The metric in the table reflects the route's efficiency. The default route is always last, where the subnet mask 0.0.0.0 permits any traffic. When a packet arrives, the table is checked from top to bottom. In this example, the first match is the check against the range 162.132.9.0/25 (162.132.9.0 – 162.132.9.127).

## Routing. BGP

ISPs love to provide a fault-tolerant service in the form of two separate cables, but the cables run together and, in the event of physical damage, they fail together. So we buy one network each from two different providers. And we realize that IP addresses are tied to the provider. Conclusion: we need our own IP addresses. We go to IANA (0–223) and look at the registrars by continent (in Europe it's RIPE).

- **African Network Information Centre (AfriNIC)** — manages the address space in Africa.
- **Asia-Pacific Network Information Centre (APNIC)** — manages the address space in the Asia-Pacific region.
- **American Registry for Internet Numbers (ARIN)** — manages the address space in the USA, Canada, and some Caribbean countries.
- **Latin American and Caribbean IP Address Regional Registry (LACNIC)** — manages the address space in Latin America and the remaining Caribbean countries.
- **Réseaux IP Européens Network Coordination Centre (RIPE NCC)** — manages the address space in Europe, the Middle East, and Central Asia.

If we need our own addresses and we're big players, we can request personal IP addresses. For fault-tolerant services, blocks of IP addresses are used. With such addresses you yourself become part of the internet, with responsibility for routing. You need an IGP (internal), especially with link-state. And an EGP (external) to provide routing between companies.

RIPE knows that company A was issued the address 1.0.0.0/24 and company B was issued 2.0.0.0/24, and that is the only address each company has the right to announce to its provider. But the world isn't free of negligence, so it's possible to announce another company's prefix and break their network.

Here's a task for you. You need to compare two identical prefixes in different routing protocols. They have the same mask. And you can't compare the metric: in RIP the metric maxes out at 15, based on the number of routers, whereas in OSPF the metric is tied to interface speeds and maxes out at 65,000.

So, you can't compare metrics across different routing protocols. We won't be able to look at the metric and say which route is better.

Let's try to understand how a router chooses the best route. First — the prefix length. No matter which routing protocol the prefix came from, the most specific one is always chosen. Of these two addresses:

- 10.0.0.0/31
- 10.0.0.0/32 — this one wins; it's more specific.

The /31 mask is used when routers are connected directly to each other; such a mask splits the subnet into two IP addresses. The standard option is four addresses: the network address, two usable ones, and the fourth a broadcast. A decent money saving for a company when each IP costs $30.

Imagine there's an interface Gb01 with IP 10.0.0.1/24. A second interface, Gb00, on which we receive an OSPF announcement for the network 10.0.1.0/25. And if traffic arrives at the router from 10.0.1.2, the traffic will fly off to 10.0.1.0/25 — because traffic always goes to the more specific route, if one exists.

![Route specificity](https://your-scorpion.ru/wp-content/uploads/2021/06/Group-57.png)

The second parameter is AD (Administrative Distance) — the most favourable routing protocol. For OSPF this distance is 110, for RIP it's 120, and the lowest AD value makes a route the highest priority. The nice part is that we can influence the AD value.

TCP is practically the basic protocol of the internet — that is, a set of rules for exchanging data within a network. Routing seems clear, but there are a few nuances with TCP. TCP "guarantees" delivery, but it has a certain number of send attempts. To send data between hosts there must be a connection. Besides the standard x + 1 and y + 1, there's also the concept of window size: 2¹⁶ = 65,535 bytes that we can send without receiving acknowledgement of receipt. Networks today run at 100 Gbit/s, and 65,535 bytes is very little. TCP is BDP — that is, it depends on the time it takes to exchange information between sender and receiver.

BGP — a metric alone isn't enough for it; it also needs Network Layer Reachability Information (NLRI). BGP works on top of the TCP protocol and uses TCP port 179. Since TCP guarantees delivery, no delivery acknowledgements are implemented inside the protocol itself. An attack on BGP consists of announcing an incorrect IP address. The more local your network, the lower the chance of attack. BGP is the main protocol for the internet; EIGRP was intended for corporations, as was OSPF. Play around with the commands `pathping` and `tracert`. So, `tracert -d 8.8.8.8` will let you receive a message each time a router is encountered. For macOS the command is different: `traceroute docs.google.com`.

![traceroute](https://your-scorpion.ru/wp-content/uploads/2021/03/Group-33009.png)

One practical wrinkle worth flagging before we leave routing: firewalls can quietly get in the way. Russian-made firewalls in particular tend not to play well with routing — they're built for the government sector and for plugging legal gaps around the personal-data encryption law, so the box is often stuffed with cheap commodity parts, and the routing behavior reflects it.

And much of the beauty you ultimately layer on top has nothing to do with packets at all. Audio is a good example: I tend to mix at around −6 dB, shipping OGG for Chrome and Unity and MP3 for Flash, and it helps to keep the arithmetic in mind — CD-quality stereo at 44.1 kHz and 16 bits per sample is 2 × 44.1 × 1000 × 16 ≈ 1.41 Mbit/s, whereas a telephone line gets by on 8 kHz at 8 bits, a mere 64 kbps. For tooling, UE4 has perfectly good internal audio tools, but the industry standards are Wwise and Fmod, and Wwise will integrate into UE4 after a fashion. Subtitles belong in the same conversation: even with multilingual voiceover they're worth adding, because plenty of situations keep a player from hearing the audio — loud effects drowning out dialogue, the sound switched off in favour of background music, speakers kept quiet out of respect for the household, no headphones to hand. They're a gift to non-native speakers and to players with hearing impairments, and it's worth exposing a subtitle-size setting and a high-contrast mode (black background, white text) for readers with dyslexia; the [BBC subtitle guidelines](https://bbc.github.io/subtitle-guidelines/#Typography) are the reference here. It isn't a fringe feature, either — by 2018 more than 60% of players were already leaving subtitles switched on.

To sum up, the synchronization problem in games is a deception in which both designers and programmers take part. First you need to build the project technically correctly, and from that result you then layer on the beauty — and that is the job of the designer and the animation artist working as part of a team on an IT project.
