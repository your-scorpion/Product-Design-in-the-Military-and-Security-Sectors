# Preparing a Security Report
### A hands-on tour from infrastructure and testing to the document that makes it matter

*Maksim Tcvetkov · 5 March 2025*

## Servers and virtual machines

Companies are under constant attack. DDoS attacks grow heavier year on year, and the attack vectors keep getting more inventive. Your company might be hit through a contractor, for instance — especially over the holidays. You could say the contractor bears the legal responsibility in that case. But it's worth asking what a cyber incident even *is* de jure, and whether the concept is defined anywhere in the contract at all. And even where it is, picture the contractor sitting under a DDoS with their little site knocked offline — as the client, that probably doesn't concern us in the slightest. In information security everything is always tightly interconnected, so let's talk through the basic methods for defending and checking a company's infrastructure, the ones that keep you clear of both breaches and legal fallout.

So: the smart folks have sized the hardware, bought the servers, and now your organization owns a set of machines. On them sits a host operating system — the OS that runs directly on the metal. You install Windows or Astra on the box, and the hardware runs under that system. Against it stands the guest operating system, running as a virtual machine. There's also the VMM.

Virtualization: your box runs Ubuntu; it has a keyboard, a mouse, a hard drive with the OS on it, and a great many other little chips. The OS talks to the hardware through a driver — one written for Ubuntu — that translates commands from the language of the OS into the language of the hardware, from functions into ones and zeros. Mice from different manufacturers can speak different syntaxes, which is why every device has its own driver. And when different operating systems run through virtualization, they too need some way to reach the hardware. That's what the hypervisor is for. Plenty of companies run virtualization, using a VM as their primary working system, and from a security standpoint that's good practice. QEMU and Bochs are popular examples. One thing worth knowing: many attackers build their software with checks for whether the malware is running inside a VM or on a real system. Another arms race.

The OS makes a great many requests of the hardware, and some of them need special privileges to execute. The x86 architecture uses privilege levels 0 through 3, where 0 is the most privileged. In security we often simplify this to two: 0 as the kernel level and 3 as the user level.

Paravirtualization is a technique that lets the guest OS understand it's living inside a virtual machine, with a dedicated interface for asking the host OS for system functions. The guest knows it lacks ring-0 access and behaves accordingly. XEN and UML are examples. But paravirtualization means rewriting the OS, and not every vendor is willing — Windows 8 supported it, though Microsoft has the resources for that kind of work.

Hardware virtualization is the move from software-level kludges to hardware-level ones. Intel-VT and AMD-V are the examples: the processor's architecture is altered to give guest operating systems direct access.

A very popular way of working through hypervisors is the standalone hypervisor, which runs on bare metal, needs no intermediate layers, and is itself the OS. If you've worked remotely, you've probably run into VDI — and this is exactly that. High performance, and no intermediate layer to compromise, because there simply isn't one. The downside is a stripped-down OS. It's implemented through Hyper-V on Windows, or VMware ESXi. The alternative is the hosted hypervisor, which acts as an intermediary; KVM, for one, is a separate process in the system. As you'll have gathered, any large company will have a hypervisor with some number of machines assigned to you, and inside the hypervisor live the virtual machines.

VMs ≠ containers. Containerization and virtualization solve different problems. VMs once played the role of containers, but containers handle the more resource-heavy work. A container knows it's a container and can't reach other containers (though you can introduce them to one another). Docker is the most popular containerization software; it's built around one container = one application, with the small footprint that implies. A container is a trimmed-down OS. And remember that Docker is a Linux thing — you can set Windows up on it, but it hurts. Another difference: a virtual machine can run for hundreds of days, whereas containers live until the next code update. They're easy to kill and restore, which is one more security advantage. A virtual machine usually comes as an `.iso`, `.vmdk`, or `.vdi`; containers, by contrast, are ready-made images or are built from instructions in a configuration. You can find ready Docker images [here](https://hub.docker.com/). Once you're running a thousand containers, it's time for orchestration — Kubernetes, Docker Swarm, Nomad.

For solutions aimed at small organizations and sole proprietors, you'll still be working with a server — a VPS, a virtual machine dedicated to you. When you rent a VPS, the host handles security and you get SSH access (a key and/or a password). The provider takes partial care of a rented server, and an agent such as Zabbix will be installed on your VPS. Check with the provider about port access in advance, since you may need a port for mail, or port 80 to obtain certificates. You can also rent an unmanaged VPS, where you solve every problem with memory, performance, and the rest yourself.

So you've rented a VPS and want to use it as a trap for an attacker. To do that, we make our service deliberately vulnerable. The attacker lands on the fake server, rejoices, runs their commands, downloads "secret documents.txt" — and we're running the audit. That's how we can learn the source IP. Suppose the attacker planted their own software on our server and communication began with a botnet server (*robot* + *network*). That botnet server's address is what becomes an Indicator of Compromise (IoC) for us.

You'll most likely have a SIEM, where syslog ships logs in for normalization and correlation. The data gets collected over syslog, SNMP, or packet capture. If there's no budget for an expensive SIEM and we're still a sole proprietor, [fail2ban](https://github.com/fail2ban/fail2ban) will do us just fine. It lets you configure correlation rules — ban an address for *n* time after ten failed login attempts from it, say. And spinning up a simple trap with [PentBox](https://github.com/technicaldada/pentbox) looks like this:

```bash
git clone https://github.com/technicaldada/pentbox
cd pentbox
tar -zxvf pentbox.tar.gz
cd pentbox-1.8
./pentbox.rb
2
3
```

## Firewall

A firewall limits network connections. In its simplest form you have: router > firewall > your network. Cloudflare, too, plays the role of a firewall and protects against DDoS; or a firewall can take the form of a hardware-software appliance. But it has to be configured properly — nothing works out of the box, whatever the vendors tell you. Linux ships with `netfilter` (iptables). `iptables` is a utility for managing IP packets; there's also the newer `nftables`. Inside, traffic filtering happens in several stages:

- `Prerouting` — for every packet arriving at the network interface.
- `Input` — rules applied to packets bound for the host itself or a local process. Input governs incoming traffic to the router, and to block some traffic through your router this is exactly where you write the unwanted addresses.
- `Forward` — rules that fire when your host is acting as a router. Roughly, it governs traffic passing through the router.
- `Output` — the host has generated packets itself and is sending them outward. This is the traffic the router generates and sends out.
- `Postrouting` — rules applied to any packets that must leave the network interface. This is the base chain.

There are different tables; the NAT table redirects packets and rewrites the addresses in the destination and sender fields: `iptables -t nat -L`.

iptables lets you close or mask unused ports (port knocking), segment access by IP, and prevent leaks of traffic or your IP address (a killswitch).

iptables needs root, so we elevate with `sudo -s`. There's `iptables -L`; something more practical is `iptables -A INPUT -p icmp --icmp-type echo-request -j DROP`, and we'll add a second, `iptables -A INPUT -p tcp -j DROP`. That gives us these rules:

```
target     prot opt source               destination
DROP       icmp --  anywhere             anywhere             icmp echo-request
DROP       tcp  --  anywhere             anywhere
```

It's very common to open a particular port. Say it's port 22 — then we let that traffic through with `sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT`.

The rules are written; now, when a packet comes down the input chain and it's ICMP or TCP traffic, the packets get dropped without notifying the sender. To block some port, the command is `iptables -A INPUT -p tcp --dport 80 -j DROP`. You can list the active listening ports with `netstat -tulpn | grep LISTEN`.

![Listing the active listening ports](https://your-scorpion.ru/wp-content/uploads/2044/03/adaddadada.gif)

And to block access by domain: `iptables -A OUTPUT -p tcp -d pfr.ru -j REJECT`.

Active Directory mapping is the process of pulling information out of an Active Directory environment — enumerating users, permissions and privileges, groups, computers, and so on — as a preliminary step toward finding vulnerabilities and attack paths.

## Reconnaissance

We'll use BloodHound to escalate privileges. BloodHound is an open-source tool — built by harmj0y, CptJesus, and _wald0 — that uses graph theory to surface the hidden, often unintended relationships in an Active Directory or Azure environment. Every pentest involves BloodHound, because Active Directory is more than 25 years old and gaining privileges is the foundation of any attack. Attackers can use BloodHound to easily surface extremely complex attack paths that would otherwise be impossible to map quickly. We, as defenders, can use it to find and close those paths. Both blue and red teams use BloodHound to understand the privilege relationships in an AD or Azure environment more deeply.

BloodHound gathers data from the AD infrastructure using a C# executable or a PowerShell script. The user needs no special privileges beyond those of a domain user. The collected data goes into a Neo4j database — a graph-based database management system used to render complex relationships as graphs.

From there, purpose-built queries let you identify the likely attack paths in a reasonably readable interface. The graph helps you surface a whole range of relationships, such as:

- Users in particular business groups holding privileges beyond what a privileged object (a user or computer) requires — an HR group with RDP privileges on the domain controller, for example.
- The shortest path to a domain administrator with the same privileges.
- Users with elevated privileges beyond those in the domain administrators group.

As a bonus, Maltego graphs always look striking in a report.

## Investigating your own network

We've obtained every official authorization to test the infrastructure. We'll practice in a Kali VM. Here are the install instructions for [BloodHound](https://bloodhound.readthedocs.io/en/latest/installation/linux.html), for finding vulnerabilities. First, update the apt sources:

```bash
echo "deb http://httpredir.debian.org/debian stretch-backports main" | sudo tee -a /etc/apt/sources.list.d/stretch-backports.list
```

Run `sudo apt-get update`. Neo4j will now pull automatically from this repository when it needs to install Java as part of its own setup. Then install Neo4j:

```bash
wget -O - https://debian.neo4j.com/neotechnology.gpg.key | sudo apt-key add -
sudo -i
echo 'deb https://debian.neo4j.com stable 4.0' > /etc/apt/sources.list.d/neo4j.list
sudo apt-get update
sudo apt-get install neo4j -y
```

Now launch and install BloodHound:

```bash
cd /usr/bin
sudo ./neo4j console
sudo apt install bloodhound -y
```

In the browser, go to `https://localhost:7474/`.

## Access

Once the "weak link" in the AD chain has been chosen, it's time for a more targeted penetration test. At this stage you need to get hold of a set of wordlists. There are plenty of tools for generating them — mentalist or bopscrk, for instance. To build a personal wordlist, try `bopscrk -i`. It's worth researching the person beforehand — their interests and personal details — since the personalized list is built from exactly that. But keep the law in mind, and don't use anyone's personal data.

If we're on the network, the console can run [nethogs](https://github.com/raboof/nethogs), which reads traffic through libpcap and maps it onto `/proc`. If the task is to see how much traffic is being eaten right now, nethogs is the light, fast option. It looks like this:

```bash
sudo apt install nethogs
sudo nethogs eth0
```

![nethogs showing per-process traffic](https://your-scorpion.ru/wp-content/uploads/2044/03/h.gif)

The second useful tool is iftop, which shows you which application is reaching out where — and lets you work out which of them are illegitimate.

```bash
sudo apt install iftop
sudo iftop -i eth0
```

All these console applications are convenient to run under `apt install screen`. With `screen -S` you switch between terminals while the processes keep running in the background. And remember you can always inspect a tool's repository with `apt policy iftop`. If logging the network activity for the security report becomes important, you can do it with Netstat, Burp, or Wireshark; with Firejail and the `--trace`, `--tracelog`, `--debug` flags. From Wireshark it's convenient to save a `.pcap` and drop it into Zeek. And remember: all your work is pointless if you haven't conveyed the real situation — and the danger of failing to make the important decisions — to the business, through reports and communication.

For real-time observation you can use [Prometheus](https://prometheus.io/). To install it, throw the command at Docker with `sudo docker run -d -p 9090:9090 prom/prometheus` and go to `http://localhost:9090`. Prometheus runs on `:9090`. Create any job you like — monitoring Prometheus itself, for example. The metrics will be served at `http://localhost:9090/metrics`.

![Prometheus running](https://your-scorpion.ru/wp-content/uploads/2044/03/Untitled-2.gif)

![Prometheus metrics endpoint](https://your-scorpion.ru/wp-content/uploads/2044/03/adada.gif)

Spotted suspicious activity on a corporate server? The data had better be in a backup. Above all, keep backups of everything — `brbackup` is your best friend. And the important files are better deleted before the attacker carries them off from a compromised server. Linux can delete a file securely: many people use `rm filename.txt`, but `shred -u -n 20` is more reliable. Or, for a quick job across the whole filesystem, `srm -v`. Or `shred -v -z -n 3 /dev/sda` to overwrite the disk completely — this kills the OS outright. Among the more extreme options is [ShredOS](https://github.com/PartialVolume/shredos.x86_64); if you need to dispose of decommissioned corporate equipment safely, ShredOS is a good choice.

Before building backups, a few questions set the shape of the whole thing: what exactly you're copying, how often, how long the copies are kept, which tools do the work, and — the one that bites — whether you'll actually be able to restore from them. (I've had an unpleasant experience with PostgreSQL on precisely that last point.) Then you choose the tool. Linux has `tar`, but it only backs up without compression; if you need compression, there's `rsync`. And you can tinker with the ancient `dd` and `cpio`.

A sole-proprietor case: a public website lands on your desk to analyze. You can start with DNS. Finding it is easy — `dig +short mx example.site` — and the answer might look like `50 fb.mail.test.net`. The 50 at the front is the priority; a server at `10.fb.mail.test.net` would take precedence. Then you "probe" the mail with `swaks --to target@example.com --from test@domain.com --server fb.mail.test.net --port 25`. The response will be something like `Service unavailable; Client host [x.x.x.x] blocked using pbl.spamhaus.org; Listed by PBL` — meaning the IP is on a blacklist. Find your own IP (on a Mac, `ipconfig getifaddr en0`) and run it through the services below. That's how you learn whether your IP is blacklisted — and if it is, that's a problem for the organization.

- virustotal.com
- ipvoid.com
- talosintelligence.com
- otx.alienvault.com
- projecthoneypot.org
- spamhaus.org
- ipqualityscore.com
- shodan.io
- greynoise.io
- fraudguard.io
- threatminer.org

You can build chains across social networks with **SpiderFoot**, launched with `spiderfoot -l 127.0.0.1:5001`, which then opens a web interface for scanning — a real Swiss Army knife. Paired with [CyberChef](https://gchq.github.io/CyberChef/), it makes for a fairly powerful analysis combination.

An alternative is ZAProxy, a web-application scanner. Run a scan, then Report > Generate report, and you can start fixing things.

![ZAProxy report generation](https://your-scorpion.ru/wp-content/uploads/2044/03/adad.gif)

Or SkipFish, for a fast scan. Whatweb is good too — `whatweb --user-agent "Mozilla/5.0" --max-threads 1 https://www.camn.ids/` — and the result gives you something like:

- `HTTPServer[cloudflare]` — the site is behind Cloudflare and hiding behind IP [104.16.80.32].
- The site is built on Bootstrap, with JQuery [1.11.1] (out of date).
- Cookies [VSID].

While we're at it, let's check whether Cloudflare protects it well. The command `curl -s "https://www.cambodiaimmigration.org/search?q=<script>alert(1)</script>" | grep -i "alert"` gives the answer: the protection holds, the XSS is blocked.

## Further steps

The first commands after gaining access to a server are for checking your role and the disks — `whoami`, or `lsblk` for information on disks, partitions, sizes, and mount points. Add `df -h` to see used disk space and `fdisk -l` for detailed information on each disk and partition. `lsof -u kali` shows what software is running as the user kali; for more detail, a custom view — `ps -ao tty,comm,pid`. And `ps -ef` for a static look at the processes, `top` for a live one. These are all Linux commands, and there's no getting anywhere without them. There's always Linux on the network. There you are on your Windows machine browsing the web, and the router — which is also your gateway — runs Linux. Linux is everywhere: nearly all web servers, routers, and CI/CD pipelines run on it, and virtualization too. Because Linux is free.

The basic commands for machine information are `dmesg | less` (or `more`) and `dmesg | head -n2` — that's how you learn the Linux kernel version and the bootloader details. For memory, `dmesg | grep 'Memory'`. You can swap Memory for eth, DIGSIG, ttyS5, CPU, digsig, amdgpu, and plenty more to suit your needs.

![dmesg output](https://your-scorpion.ru/wp-content/uploads/2025/03/Untitled-1.gif)

In the Linux filesystem (FHS), the entry point is `/`. The command `ls -lah /` lists a directory's files in a convenient, informative format. Drop into Linux process monitoring regularly — the equivalent of Task Manager, opened with `top`. Pay particular attention to systemd, which defines the relationships between the system's components and hands processes all sorts of capabilities; attackers love it dearly. And libcap.

A very primitive tool for ARP (Address Resolution Protocol) on the network is [netdiscover](https://www.kali.org/tools/netdiscover/), which finds the active hosts on a local network. When it starts, it may say the scan is finished when it's really still going. It may turn up web servers on port 80 (HTTP) or 443 (HTTPS), or simply hand you the targets' IP addresses. You can scan those for open ports with nmap. Found open ports — close them. Or set traps there, per the instructions above. For scanning large subnets that would otherwise take months, use Masscan and RustScan. Look for port 2000, which is usually open; `masscan -p 2000 —banners` helps nicely there.

![Masscan / nmap results](https://your-scorpion.ru/wp-content/uploads/2044/03/adada.png)

Now let's send something out into the world. [hping3](https://www.kali.org/tools/hping3/) lets you generate a lot of packets — you can use it to test a server's DDoS protection. The basic command is `sudo hping3 -1 google.com`; the already-dangerous one is `sudo hping3 --flood -S -p 80 sitefor.test`, for DDoS testing. Want to know whether there's a firewall? `sudo hping3 -A -p 80 8.8.8.8` gives you this stream of digital consciousness:

```
len=46 ip=8.8.8.8 ttl=255 id=8333 sport=80 flags=R seq=0 win=0 rtt=7.6 ms
len=46 ip=8.8.8.8 ttl=255 id=8334 sport=80 flags=R seq=1 win=0 rtt=7.1 ms
len=46 ip=8.8.8.8 ttl=255 id=8335 sport=80 flags=R seq=2 win=0 rtt=7.0 ms
```

The thing to watch here is **`flags=R`**, which tells us the port is filtered. TTL (Time to Live) is the metric governing the number of hops a packet may cross before it expires.

To test forms, unscrupulous minds use `sqlmap -u "http://example.com/wp-login.php" --forms --batch`, which checks forms for SQL injection. Either it finds nothing, or it offers up a few injection points.

Now, proxies. [Mitmproxy](https://mitmproxy.org/) has a mode that works over WireGuard: you bring up a VPN tunnel that all traffic from every application flows into, and mitmproxy listens to it. A proxy serves precisely as the layer between you and the resource you're requesting, hiding your IP. And don't imagine that a VPN fools your provider. Every VPN has a definable protocol. Traffic moves in packets whose composition can be analyzed with Deep Packet Inspection (DPI): it checks what's inside a packet, and if the composition resembles an OpenVPN or WireGuard packet, onward transmission can be blocked. A VPN simply isn't a tool for visiting forbidden resources — it's for building tunnels for remote offices and corporate networks. Take the GRE protocol, for point-to-point tunnels: if you have two offices, 192.168.1.0 and 192.168.10.0, and need to join them into one network, GRE is the popular answer. For money, you could realize the same idea as an L3 VPN from a provider; for free, GRE — which simply adds a new header to the packet. GRE also solves the problem of multicast traffic having no encryption.

You can gather resource-usage statistics with `mpstat -P ALL 1`; and if you need to carry off a copy of an internal attacker's disk for analysis, [guymager](https://www.kali.org/tools/guymager/) handles it beautifully.

![guymager disk imaging](https://your-scorpion.ru/wp-content/uploads/2044/03/ddee.gif)

With all the information gathered, we turn to writing YARA rules. First check the version with `yara --version`, create a new rule with `nano my_first_rule.yar`, and paste in any of the rules the vendors publish so generously — [here](https://github.com/SEKOIA-IO/Community/tree/main/IOCs/fakebat), for one, or [here](https://github.com/Yara-Rules/rules/pull/448). Not every rule is perfect; rules that are too general throw a lot of false positives. Once the rule is copied from GitHub, you can set it loose on a file with `yara my_first_rule.yar '/home/kali/fakebat_test.ps1'`. If it fires, you get this:

![YARA rule match](https://your-scorpion.ru/wp-content/uploads/2044/03/yara.gif)

Rules can also be found in reports — [this one](https://www.elastic.co/security-labs/fragile-web-ref7707), say, or [this](https://www.akamai.com/blog/security-research/new-aquabot-mirai-variant-exploiting-mitel-phones). You can write your own, too; there's [documentation](https://yara.readthedocs.io/en/stable/). Beyond YARA, there are Sigma and Suricata rules. And then you automate the process across thousands of such rules with [velociraptor](https://docs.velociraptor.app/). That's how we organize the defense.

Binwalk pulls a great deal of information out of a file — the architecture, the controller a firmware image was written for, or the entropy via `binwalk -E`. In its simple form the command looks like `binwalk '/home/kali/Punktracker-ModRep-2.5.6-XM.bin'`, but a proper analysis calls for reading the [documentation](https://github.com/ReFirmLabs/binwalk/wiki).

On mobile. OWASP's MASVS is about *what* to test; MASTG is one of the core references on analyzing mobile apps and describes *how*. And, plainly, the SMS-code check shouldn't happen on the client — keep your developers in line. You can always enrich a report with output from `npm audit`, though it throws a lot of false positives; if you're doing web development, the `node_modules` folder is forever full of vulnerabilities. `npm audit fix --force` helps, but it sometimes breaks the developers' kludges, which is why they don't want to fix the vulnerabilities. Better still is [dependabot](https://docs.github.com/en/code-security/getting-started/dependabot-quickstart-guide). You can also glance at vulnerability details on [ossindex.sonatype](https://ossindex.sonatype.org/). More modern is [auditjs](https://www.npmjs.com/package/auditjs), with Nexus on top as a layer between NPM and the project.

## Setting up tunneling

There are many ways to set up a corporate VPN and give employees convenient usability on their mobile devices; WireGuard is one of them, letting you configure encrypted tunnels. Suppose you've already rented or set up your own server with, say, Kaspersky. Open the console, where it's enough to type `ssh root@xxx.10.11.xxx` — you'll need either a password or a private key to authenticate. See the notes on the VPS above. Then on to configuration.

Confirm WireGuard is installed with `wg --version`. Its default port is UDP 51820, because we don't want to break any laws and we do everything within the framework of Russian legislation.

You'll need public and private keys for the server and for the devices, since this uses symmetric encryption, and you'll need to create a configuration file called wg0.conf. Step by step:

1. Move into the right folder: `cd /etc/wireguard`.
2. `umask 077`, to tighten security.
3. `wg genkey | tee /etc/wireguard/privatekey | wg pubkey | tee /etc/wireguard/publickey`, to generate the keys; afterward you'll have `privatekey` and `publickey` files.
4. By hand, save the value from `cat /etc/wireguard/privatekey` to paste into wg0.conf.

Then create and fill in the file with `nano /etc/wireguard/wg0.conf`, and paste in this template:

```ini
[Interface]
PrivateKey = <contents_of_privatekey>
Address = 10.8.0.1/24  # VPN subnet
ListenPort = 51820
PostUp = iptables -A FORWARD -i wg0 -j ACCEPT; iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
PostDown = iptables -D FORWARD -i wg0 -j ACCEPT; iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE
```

- `wg-quick up wg0` to bring the interface up.
- `systemctl enable wg-quick@wg0` to start the service.
- `systemctl status wg-quick@wg0` to check it.
- You'll need to restart it regularly, so these two — `wg-quick down wg0` and `systemctl start wg-quick@wg0` — will be getting typed into the terminal on a regular basis.

Settings for the end devices:

1. Generate fresh keys for a given Android device: `wg genkey | tee /etc/wireguard/android_privatekey | wg pubkey | tee /etc/wireguard/android_publickey`.
2. Return to the wg0.conf configuration file with `nano /etc/wireguard/wg0.conf` and add:

```ini
[Peer]
PublicKey = <ANDROID-CLIENT-PUBLIC-KEY>
AllowedIPs = 10.0.0.3/32
```

Create a second file with `nano /etc/wireguard/ios.conf` and fill it in:

```ini
[Interface]
PrivateKey = <ANDROID_PRIVATE_KEY>
Address = 10.0.0.3/32
DNS = 8.8.8.8

[Peer]
PublicKey = <SERVER-PUBLICKEY>
Endpoint = <SERVER-IP>:51820
AllowedIPs = 0.0.0.0/0
PersistentKeepalive = 20
```

Then generate a QR code: `qrencode -t ansiutf8 < /etc/wireguard/ios.conf`. Scan it with the phone and you're done — a bare-bones corporate network for your company is up, and you can build the same on any Russian server by analogy.

## Setting up email

Everyone always wants their own mail server. It's not as hard to set up as it seems. You just rent an Ubuntu VPS, generate a key locally on your own machine with `ssh-keygen`, and push the public key to the rented server. For protection, set the permissions with `chmod 400 .ssh/folder`, so only we can read the key — and we don't much need to change it. Then take [poste.io](http://poste.io/), or an alternative like [mailu.io](https://mailu.io/2024.06/). As engineers, we either follow instructions or write them. We need Docker for poste, so we find the [instructions](https://docs.docker.com/engine/install/ubuntu/) and copy the commands over to our server, then do the same for poste from its [official guide](https://poste.io/doc/getting-started).

Now we need to find an open port, done with `netstat -tulpn | grep LISTEN` on our VPS. Take your VPS's IP address and follow the poste.io setup instructions. The next step is to buy a domain name that isn't on any blacklists. If a spam email reaches you from gmail, the sender's name was spoofed — and to fight that, don't forget to configure SPF, DKIM, and DMARC.

Sending mail runs on SMTP, which needs ports 25 (incoming), 587 (encrypted exchange), and 465 (deprecated). It's a text protocol: connect to the sending server > transmit the text > close the connection. Sending isn't receiving — for receiving mail, the protocols are POP3 and IMAP.

## The report

If there's time to use SysReptor, good — they even have [example reports](https://demo.sysre.pt/projects/7cd2ec39-e8d8-4976-b136-c65e8b3d64f9/reporting/). Otherwise, Word will do.

Drawing a simplified network diagram is very illustrative for a CTO. You can wire your CMDB up to Visio, NetBox, or another tool programmatically to produce it, and the asset count should match the number of assets coming from your scanners, SIEM, and NTA.

And how do you know the work was done well? If it meets the requirements, it was done well. That sounds trivial, but it's exactly what standards like ISO codify. Customer experience can serve as a quality indicator too: if an operator can answer a customer's question on the first try, that's quality. The American ASQ publishes a great deal on this as well. QC (Quality Control) is the testing of the software; QA is about the processes around it. You draw a cause-and-effect diagram. And the Cost of Quality (COQ) can quite reasonably eat up to 20% of revenue when we're talking about manufacturing physical goods.
