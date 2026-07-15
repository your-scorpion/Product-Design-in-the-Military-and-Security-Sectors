# Analyzing Malicious Files
### A working tour of malware analysis — static, dynamic, and everything the analyst reaches for

*Maksim Tcvetkov · 11 February 2024*

Malware is the umbrella name for viruses, worms, trojans, and the other pests of the cyber world. Their main goal, in most cases, is to do damage and to gain access to protected resources. Some malware lands on your computer and pulls malicious code down from the network; others carry the dangerous code inside from the start. A keylogger, for instance, records everything you type on the keyboard and sends it back to its owner. A C&C (C2) is a bot that talks to a server, and the server sends tasks from the attacker down to your personal computer — one of which might be taking part in a DDoS, driving traffic at some machine on the network. Another example is the insider threat, which may well be a person — an employee of yours — who has led to the network's infection, deliberately or by accident.

Malware comes in different kinds, and it isn't necessarily complex software. Ordinary malware can be a very simple ten-line program. It might begin with a `#!/bin/bash`, so the system knows to run it in Bash. But not always — this code, for example, carries nothing bad in it; it's simply sending an email from a bash script:

```bash
#!/bin/bash

recipient="recipient@example.com"
subject="Email subject"
body="Email text"

echo "$body" | mail -s "$subject" "$recipient"
```

Add an infinite loop, though, and it's already spam and DDoS. Malware needn't hide in the system at all — it can simply break a legitimate program. The more advanced kinds can be cunning enough to graft themselves into a program's code without even changing the size of the infected file.

How does malware launch, and how does it survive on a system that has antivirus? Sometimes it writes itself into the Windows registry. The registry is like the central nervous system, and malware plants its keys there so it can activate on every fresh Windows boot. There's also the startup folder, where a piece of malware keeps a shortcut to itself and fires on every user login. Scheduled tasks, too, are a fine instrument for running something on a regular basis. In PowerShell, type `Get-ScheduledTask` or run **taskschd.msc** to find out what's scheduled on your Windows — you'll discover plenty of interesting things. And make sure WinRM isn't enabled, with `Get-Service WinRM`.

Something more advanced: in the registry you can lodge yourself into autostart via Winlogon. To check, there's Autoruns from Sysinternals, which shows every auto-launched program on the machine, scheduler and registry included. And it never hurts to scan the PC with MalwareBytes, since nobody has repealed injection into legitimate software. A plain KVRT won't hurt either, even if you're skeptical of Kaspersky's products. Add RootkitRevealer from MS and Hfind for finding hidden files on disk.

To spread, a virus needs a carrier — an infection vector — usually the physical channels: email, instant messaging and chat, social networks, URL links, file shares, software vulnerabilities.

Some malware will hide if it knows the system is protected. It often leans on `ptrace`. Malware typically uses the `ptrace()` system call to detect debugging, checking whether `ptrace()` returns an error when it tries to trace itself. If `ptrace()` returns an error on that attempt, it indicates the process is already being traced — possibly by a debugger. Such an anti-detection checklist is bypassed with nested debugging, where several debuggers cover for one another.

Overriding the `ptrace()` function can be especially useful when a security analyst is dealing with malware that uses `ptrace()` for anti-debugging. Many kinds of malware check whether they're being debugged by calling `ptrace()` with the `PTRACE_TRACEME` flag. If that call fails, the malware understands it's being debugged and may change its behavior, terminate, or take other evasive action. By overriding `ptrace()` so that it always returns 0, the analyst can effectively neutralize that anti-debugging check. As a result, when the malware makes the `ptrace()` call, it gets a response indicating it isn't being debugged, though it is — letting the analyst keep observing the malware's behavior without tripping its defenses, and get a clearer picture of its functionality and potential threats.

Or the reverse Turing test, where we try to convince the malware that the computer is interacting with a human — emulated clicks, say, or mouse movements. Malware may also check the system for processes tied to a virtual environment, such as vmtoolsd.exe. Or even the hardware drivers. It's very fond of `FindWindow()`, `CreateToolhelp32Snapshot()`, and `FindProcess()`. Some malware watches with `rdtsc` + `Sleep()` to make sure time hasn't been sped up on the system.

Another dangerous species is the rootkit. A rootkit is a serious threat to computer security thanks to its ability to gain deep access to a system — often with administrator rights, and without detection. Rootkits can conceal very serious attacks. Once installed, one can manipulate core system functions, hide other malware, create backdoors, and bypass standard detection methods, which makes it extremely hard to detect and remove. Rootkits can lead to a full takeover of the system, data theft, and a long, unnoticed presence of attackers in a company's network. They can burrow even into the OS kernel, and they love to masquerade as drivers, which makes them very hard to detect. They can also use internet connections while staying invisible to most security tools.

One effective way to defend against rootkits is behavior-based detection tools. These watch the system's behavior for anomalies that might indicate a rootkit — unexpected changes to system files, say, or unauthorized access to sensitive areas of the operating system. Regular updates to the OS and to your protective software help too, since updates often include patches for the known vulnerabilities that rootkits exploit.

Macro viruses live in Excel and Word macros; the malicious script runs the moment the document is opened. Very often they ask permission to run the macro on opening, so read those messages carefully.

There are even polymorphic viruses, which change their code on the fly and are harder to detect. Inside the virus's code sit independent blocks that can mutate or shift their position within the code.

And hypervisor viruses, which compromise the entire virtual environment between the metal and the OS. They're effectively part of the system — impossible to detect.

You can also check for Kaspersky's antivirus with a PowerShell/wmic query against the SecurityCenter2 namespace: `wmic /namespace:\\root\SecurityCenter2 path AntiVirusProduct get /format:list`. Malware can go a step further and use the same namespace to request the product's removal — I'm leaving those particular commands out of this write-up, since their sole purpose is to tear down security software.

## Office files

As an opener, let's look at the PDF format and how an attacker can bend it to their ends. A PDF file is made of four blocks: header, body, cross-reference table, trailer.

In the header you'll find a line like `%PDF-1.1`, which is the version. Anything below version 1.4 is unsafe. Every object in a PDF carries an *object number* and a *generation number*. In the trailer you'll find the number of objects in the document, along with metadata — the author's name, the creation date, and so on. And you can embed almost anything inside a PDF. `base64`, for instance, in which the word *design* is represented as ZGVzaWdu; the letter `a` is the number `97`, which in turn is the 8-bit binary `01100001`. A PDF can be encrypted with RC4 and AES. I've laid all this out to show that you can stuff wildly different content into a PDF, and that content can be represented in different ways. You can even use `eval()` from JS.

The process of reading a PDF. To read a PDF and turn it from a flat series of bytes into a graph of objects in memory, the usual steps are:

- Read the PDF header from the start of the file, confirm it really is a PDF document, and get its version number.
- Now you can find the end-of-file marker by searching backward from the end of the file. Then read the trailer dictionary and get the byte offset of the start of the cross-reference table.
- Now you can read the cross-reference table. And now we know where every object in the file is.
- At this stage you can read and parse all the objects, or you can leave that until each object is needed, reading it on demand.
- Now we can use the data — extract pages, parse the graphic content, pull metadata, and so on.

We'll look at malware disguised inside a PDF document. Start by examining the PDF's header, using a few very useful scripts preinstalled in REMnux: pdfid, pdf-parser, and peepdf. In REMnux, type `pdfid.py collab.pdf`, where collab.pdf is the name of the malicious (or any other) file.

![pdfid output for collab.pdf](https://your-scorpion.ru/wp-content/uploads/2024/02/dadadad.png)

In the screenshot above, three instances of `/JavaScript` were found in collab.pdf. The presence of JavaScript in a PDF is a significant sign that the document may be malicious. On top of that, the appearance of `/OpenAction` indicates the document tells the PDF application to perform a specific action when the file is opened — functionality that could potentially be used to kick off the execution of malicious code.

Onward: with `pdf-parser.py collab.pdf --search JavaScript | more` we scan collab.pdf for objects containing the term 'JavaScript'. Among them is object 1 0. This object appears to be a call to a JavaScript function named `WRYXKTNGCHZUIHQNDKDRYSREUUBHDTLWVGNINGPL`. That function is probably defined elsewhere in the PDF. Worth noting: JavaScript in a PDF can be spread across several objects.

![pdf-parser JavaScript search](https://your-scorpion.ru/wp-content/uploads/2024/02/Desktop-1.png)

For a deeper look, we type `pdf-parser.py collab.pdf --object 10` and get… nothing interesting.

![pdf-parser object 10](https://your-scorpion.ru/wp-content/uploads/2024/02/Frame-1261158463-1.png)

![Catalogue view](https://your-scorpion.ru/wp-content/uploads/2024/02/Catalogue.png)

But after checking object 13 with `pdf-parser.py collab.pdf --object 13`, we see the file "Contains Stream," meaning its contents are encoded in a stream and must be decoded. Let's decode the stream with pdf-parser.py and save the decoded data to a file, streamdecode.txt, with:

`pdf-parser.py collab.pdf --object 13 -f -w -d streamdecode.txt`

![Decoding the stream from object 13](https://your-scorpion.ru/wp-content/uploads/2024/02/Desktop-1-1.png)

With `js -f /usr/share/remnux/objects.js -f streamdecode.txt` we get a response pointing us to a particular function — say it's `HjuHgfd`.

Next we move to `scite`. SciTE is a text editor you can use to open the PDF and inspect the structure. In SciTE we can see what `HjuHgfd` actually is — it may well be a pseudo-function for `eval()`.

Then we need to move this file to a Windows virtual machine to check it with Windows tools. For that we enable the SSH daemon on REMnux and note the IP address, with two commands: *sshd start*, immediately followed by *myip*. We get the IP, which we enter on the Windows machine in WinSCP as the host name.

![WinSCP host configuration](https://your-scorpion.ru/wp-content/uploads/2024/02/78654t.png)

So we've set up the link between Windows and REMnux, and now we can drag files back and forth between the two operating systems to analyze the file on the Windows machine with scdbg and find the shellcode. In short, that's the manual-analysis process.

## Where vulnerabilities come from

When we talk about software security, we aren't only talking about IT — the digital, internet-facing side. We mean OT (Operational Technology), which touches the physical world directly: water, electricity, manufacturing, oil, gas. Attacks can involve fairly banal ransomware or full geopolitical forces. In OT, updates happen more slowly — everything runs on the principle of "don't touch it and it won't break." Hence the high risks that track each industry's specifics: in healthcare they try to steal patients' personal data; in retail, payment fraud and the erosion of customer trust are popular; in industry it's the interruption of production; attacks on the state are attacks on critical infrastructure, often using CaaS (crime as a service).

By analogy, there's a difference between cyber-dependent and cyber-enabled crimes. The first depends on ICT; the second uses ICT to increase its scale and impact. Cyber-dependent crimes require the use of ICT either as the means and instrument, or as the object, of the criminal act.

How to protect yourself? Organizations can lower the level of insider threat by putting in strict access controls, running regular security training for staff, watching network activity for unusual behavior, and building a culture of security awareness. Regular audits and applying the principle of least privilege — where employees have only the access they need to do their jobs — also help reduce these risks.

If your company develops its own software, integrating SDLC and SecDevOps practices for security is recommended. SDLC helps surface and fix security vulnerabilities early in the development process. That proactive approach is more effective and more cost-efficient than solving security problems after the software ships. It ensures security is a foundational aspect of the software rather than an "afterword," which leads to more robust and secure applications. And S-SDLC is better still: it's not only about preventing vulnerabilities but about increasing the trust of users and partners. At the requirements stage the security requirements are described > then comes planning > development and testing with those requirements in mind > we deploy to production > we remove everything obsolete. The requirements account for the input of security specialists — what data must be protected, what potential threats to consider, how to shift the architecture toward the most resilient form. Regulators' requirements too, privacy especially. Writing the code is also about resilience to attacks, correct session handling, and using static and dynamic code analysis. And review by colleagues. During deployment, the final security checks are run: whether services are configured correctly, whether all the data is protected.

A regular analysis of the historical layer of problems in your technology stack is also strongly recommended. You definitely need a check, for instance, that all paths are wrapped in quotes — simply always, without exception. No quotes = a chance of privilege escalation, thanks to the quirks of Windows' path-search algorithms. If you reference some DLL file, Windows may look for it in various places on the system. There's also checking the access lists on the production registry configuration. Attackers have their favorite registry addresses — `HKEY_LOCAL_MACHINE \ SOFTWARE \ Microsoft \ Windows \ CurrentVersion \ Device In`, for example.

Another possible problem is last-byte sync — the ability to cram two requests into a single TCP packet on a good internet connection.

And one more frequent problem is the race condition: reusing a captcha multiple times, say, or applying discount coupons more than once. In other words, a race condition is about performing an action more times than the developers intended. Two requests at the same moment — a login and gaining access to the administrator panel, for instance — can be the cause of a data leak. To fight it, there's Delay Jitter / Network Jitter.

Predictable identifiers belong on this same list. Say you're building your own URL shortener and want the short links to be unique. If the maximum link is 256 characters, it has to be shrunk — I'd look toward 64 bits, as with a snowflake ID. The external keys are randomized after encryption while sitting in order in the database, and to encrypt the index you can use a cipher with a 48-bit block, such as KATAN48, which gives you an 8-character limit for the shortened link. It's essential to add pseudo-randomness, or an attacker will generate two indices, subtract one from the other to recover the increment, and brute-force their way to every link in the database.

There's a statistic that 64% of vulnerabilities come from programmer errors. These errors lead to the creation of CVEs — a classification of the flaws in code that lead to vulnerabilities. Static Application Security Testing helps find vulnerabilities at the programming stage; PVS-Studio, from the folks in Tula, for one. For web applications specifically, dynamic scanners surface issues fast: WPScan is the number-one tool for WordPress, with ZAP (zaproxy) a heavier, more serious second, and Skipfish good for quick checks. In a scan result you might see something like `CSP: script-src unsafe-inline`, which opens the door to an XSS attack.

![Scan result showing an unsafe-inline CSP](https://your-scorpion.ru/wp-content/uploads/2025/05/Untitled-1.png)

When a development team finds a vulnerability in its own software, the manager's first question is: "does the vulnerability affect the product?" A vulnerability in itself is just the presence of behavior that leads to bad things. To use that behavior for bad ends, you need an exploit. There are exploit databases: shodan, sploitus, and Google.

## Binaries

When you compile code, you can "add" all the libraries you need directly into your program's file, or reference them. The latter is called dynamic linking, and it has its advantages:

- **Memory savings.** When several programs use the same library, dynamic linking lets them share one copy of it in memory, which reduces the total memory footprint.
- **Disk savings.** Since the library code isn't included in every executable, it saves disk space on the host. You'll agree a user doesn't want to download a calculator that weighs 200 MB.
- **Ease of updates.** Libraries can be updated independently of the executable, so security fixes and improvements can be rolled out quickly without recompiling the dependent programs.
- **Modularity.** It allows a more modular approach to development, where different parts of a program can be built and updated independently.

The potential downsides of dynamic linking:

- **Runtime overhead.** Dynamically linked programs can take a performance hit at launch, since linking happens at runtime.
- **Dependency management.** Tracking the correct versions of dynamic libraries can be complex, especially when different programs need different versions.
- **Breakage potential.** If a shared library is updated incompatibly, it can break every program that depends on it — what's known as "dependency hell."
- All in all, dynamic linking offers advantages in efficiency and maintainability, but demands careful management of library versions and can add extra complexity to deployment.

Either way, compilation produces a binary that converts the C++ (or other) code into machine code for the processor — into ones and zeros. Such binaries can run and produce a result for users, games included. The classic compilation process has four stages: preprocessing, compilation, assembly, linking.

At stage 1, *preprocessing*, you can decide whether you'll have many small files or one big one. It's the preparation stage, where the code is readied for compilation — comments are removed, macros are expanded, content is added — and it yields a single `.i` file.

At phase 2, *compilation*, we get code in assembly language for a specific architecture (not machine code), which is to say code a human can still read. Assembly suits both the computer and the person. This stage includes various optimizations, syntax and semantic checks, and outputs a file in `.asm` format — prepared code that includes the header files and macros.

At stage 3, *assembly*, you now get machine code, which might be LSB (numbers ordered in memory least-significant-byte-first) and 64-bit ELF. The linking phase is the final stage of compilation, where the linker combines several object files (.o or .obj) and libraries into a single executable. Its main jobs include:

- The linker matches function calls and variable references to their definitions, locating functions and variables in the provided object files and libraries.
- **Address binding.** It assigns final memory addresses to functions and variables, adjusting the code and data sections in the object files to reflect the correct addresses.
- **Library linking.** It includes code from the library files the program uses — either static linking, where the library code is copied into the final executable, or dynamic linking, where references to the library code are set up to be resolved while the program runs.

The problems a linker can hit include:

- **Undefined references.** If the linker can't find a symbol's definition, it produces an "undefined reference" error.
- **Multiple definitions.** If the same symbol is defined in more than one object file or library, it can produce a 'multiple definition' error.
- **Library compatibility.** Version or compatibility problems can arise when a program needs a different version than the one provided.

4) *Linking* — a set of files is taken and combined into one. If a static library is used, it's added straight into the compiled file. Dynamic libraries aren't added, which keeps the final program's weight down; naturally, external dependencies can change from version to version, and each time the libraries have to be loaded at runtime. To find a file's location for analysis, libbfd will do (if you're willing to suffer with C), or, far better, [llvm](https://github.com/llvm/llvm-project). PE Explorer should be within reach too.

Everything above hints that binaries aren't built for easy reverse engineering. Such a file is more compact and faster, since there's no on-the-fly translation into machine language — it's machine language already. For disassembly, look toward IDA Pro, Radare2, Ghidra. If you use IDA Pro, bear in mind that analyzing x64 needs the advanced version of the software; the free version isn't fit for serious work. On the plus side, when you start analyzing a PE file, it occupies the same memory cells it would when potentially launched in the OS. Where that isn't possible, rebasing is performed. Add-ons like the [Hex-Rays Decompiler](https://hex-rays.com/decompiler/) or [zynamics BinDiff](https://www.zynamics.com/bindiff.html) will make the work easier.

Let's not forget the super-ancient SoftICE, which let you debug any process with ring-0 access privileges on Windows. The alternative is WinDbg, currently the only popular tool that supports kernel debugging, and not bad for user-mode debugging either. OllyDbg is the most popular debugger among malware analysts, but it doesn't support kernel debugging. If you need a built-in debugger, OllyDbg will be more convenient and more advanced than IDA Pro. OllyDbg and OllyICE are user-mode debuggers; WinDbg is for the kernel; and Ether, BOCHS, and HyperDBG work with virtualization. MALT is for working with hardware — the deepest level of immersion.

Alongside OllyDbg, PEiD comes in handy. And if all you need is to gauge the threat level, Mandiant Red Curtain can do that. Unpacking comes in three types: automated static unpacking, automated dynamic unpacking, and manual dynamic unpacking. If Automated Unpacking worked for you, count yourself lucky — that's the best turn of events. Most often, unpacking malware creates a new binary that isn't identical to the original but does all the same things. If you can't fully unpack the program, you work with what you managed to unpack.

Keep some plugins on hand too (NSPack, UPack, and UPX); they're usually built into PE Explorer. UPX is the most popular; another is PECompact; and slightly more advanced is ASPack, after which you usually have to resort to manual unpacking. The hardest is Themida, for foreign Windows machines. For the gaming industry — VmProtect.

To understand what a potentially malicious program was packed with, use Exeinfo PE. Executable files on Windows must conform to PE/COFF. Files of type .exe, .dll, .sys, .ocx, .drv count as Portable Executable (PE). These files include a PE header describing the structure — the `.rdata` block, for example, holds read-only data, and `.rsrc` holds icons and menus. Section names exist only for humans, and after obfuscation the names can be changed. The file's compilation date is also present, and it too can be altered; if you see a date in the future, that's a warning sign with high probability. Packing isn't archiving — a packed file must run on a machine with no preinstalled software.

In DOS there's an order of execution based on the file extension. By file name: COM, then EXE, then BAT. If you have three files named HELLO.BAT, HELLO.EXE, and HELLO.COM in the same directory, typing HELLO at the command line runs HELLO.COM. When running programs at the DOS command line, it's always advisable to type the full file name, extension included.

## Dynamic analysis

Dynamic analysis is used when you need to understand how malware behaves during execution — how it acts in real time. A dedicated piece of software, the debugger, lets you understand how a program runs, revealing the decisions and paths the code takes in real time, showing exactly how particular processes execute. You can track the changing values in memory cells, for example. When you're writing a program, a Source-Level Debugger, which looks at the code you wrote, most likely flags your errors; all that setting of breakpoints on specific lines is precisely the work of a source-level debugger. The other type is the Assembly / Low-Level Debugger, which operates at the assembly level. Disassembly — converting binary code into human-readable assembly — is what the analyst works with.

Dynamic analysis is a great fit for obfuscated or encrypted malware, thanks to its ability to analyze behavior during execution. When malware is obfuscated or encrypted, its code can look meaningless. But at runtime the malware has to reveal its true functionality to carry out its intended actions, and dynamic analysis lets analysts observe those actions in real time — going around the obfuscation or encryption that might hide the malware's true nature from static-analysis tools.

There's a catch, though. Dynamic analysis underestimates a program's behavior, since it analyzes the program only while it runs, focusing on the path or paths actually taken during that run. Any code that doesn't execute during the observed run stays unanalyzed. Sophisticated malware can exploit this limitation by including conditional or trigger-based behaviors that stay dormant during typical analysis scenarios. That's the main drawback of dynamic analysis: you won't see instructions if they don't execute. If a program is built to run malicious code five years after release, you'll never know it without setting the right date in the OS. Programs like fuzzers try to generate content to feed into a program, to test as many code-execution scenarios as possible; examples are AFL, Microsoft's Project Springfield, and Google's OSS-Fuzz. Another way to avoid detection is the "logic bomb," which triggers only when certain conditions are met; since those conditions may not occur during dynamic analysis, the malicious behavior stays hidden and the malware evades detection. Symbolic execution is a very useful technique here too.

So: detection is avoided with time bombs and logic bombs — trigger-based behaviors that can bypass dynamic analysis by staying dormant until a specific condition is met.

On tools. Winitor's PeStudio is a solid choice for both static and dynamic analysis. You can generate a file's hash to check on [VirusTotal](https://www.virustotal.com/gui/home/upload), and the indicators section is especially good, where you'll find things like links pulled from the binary and the entropy level — the higher the entropy, the more suspicious the indicator is considered.

Process Hacker is a core dynamic-analysis tool, showing every process in the system. Regshot is a kind of snapshot of the registry, usually used to compare how the registry looked before and after a malicious file ran. The x64 Debugger lets you dig into a file and run it with breakpoints.

![Process view](https://your-scorpion.ru/wp-content/uploads/2024/02/jhj.png)

Process Monitor lets you export a CSV and load it straight into ProcDOT to get a visualization of the process. In the screenshot below you can see the system is infected with the process brbbot.exe, running on the system.

![ProcDOT visualization of brbbot.exe](https://your-scorpion.ru/wp-content/uploads/2024/02/Group-2-4.png)

Let's practice. Like the pe-tree and peframe tools we used earlier, PeStudio provides very useful information about a file, including the hash values that, as we've seen, help identify malware. One of PeStudio's most useful features is the Indicators area, which automatically highlights the potentially malicious aspects of the Windows executable under examination. You can see the characteristics PeStudio considers suspicious for brbbot.exe by clicking the tool's indicators area.

![PeStudio indicators for brbbot.exe](https://your-scorpion.ru/wp-content/uploads/2024/02/Group-47441-1.png)

Let's open the malicious file in x64dbg. To set a breakpoint, type `SetBPX ReadFile` in the command line and switch to the breakpoints tab to make sure everything worked.

![Setting a breakpoint in x64dbg](https://your-scorpion.ru/wp-content/uploads/2024/02/jhj-1.png)

Click Debug > Run, and after a while we land on the CPU tab with the breakpoint tripped right at the start of the ReadFile function.

![Breakpoint hit at ReadFile](https://your-scorpion.ru/wp-content/uploads/2024/02/Frame-15.png)

Time to turn to the [MS documentation](https://learn.microsoft.com/en-us/windows/win32/api/fileapi/nf-fileapi-readfile), where we see that the value on the first line relates to hFile.

![Microsoft documentation for ReadFile](https://your-scorpion.ru/wp-content/uploads/2024/02/Group-7001.png)

![Inspecting the hFile value](https://your-scorpion.ru/wp-content/uploads/2024/02/dadadad-1.png)

We find the encrypted data. There are several methods of encrypting and decoding data, some more complex than others. With malware, the encoding and decoding have to be simple, so the file takes up as little space as possible when working in memory — which is why malware authors usually prefer simple methods. One of the most popular for this purpose is a bitwise XOR, where each bit in the text you want to decode is XORed with a known value (the key). For that, REMnux needs only `xxd -r -p encrypted.hex > encrypted.raw` and `translate.py encrypted.raw decrypted.txt 'byte ^ 0x5b'`.

During any analysis, decompilation happens — converting machine code back into C++ or C, almost always with the loss of variable names and the rest. Obfuscation, meant to complicate reverse engineering, doesn't play in our favor (attackers love packers and cryptors). Between high-level code and machine code there's also an intermediate representation (IR), which is good for cross-platform analysis. There are two key algorithms — disassembly with linear sweep and disassembly with recursive traversal — two different methods for analyzing binary code, each with its own set of advantages and limitations.

Disassembly with linear sweep uses a sequential, systematic approach and doesn't disassemble instructions in random order. If a program's execution is non-linear, you'll miss many details; if it has loops with dynamic conditions or any other complex logic, it won't cope. Linear sweep starts at a special point in memory and moves linearly, processing one instruction at a time. It's simple to implement, good for beginners. Such an algorithm doesn't handle heavily optimized and obfuscated code well, can produce incorrect results, and sometimes confuses data with code.

Disassembly with linear sweep is a good choice for analyzing binary code in scenarios that call for a quick initial assessment, or when working with relatively simple code structures. One such scenario is early-stage malware analysis, where the main goal is a basic understanding of the code's functionality and behavior. Linear sweep processes code sequentially and is more likely to misinterpret obfuscated code. The reasons for choosing it in this scenario:

- **Simplicity of implementation.** Linear-sweep disassembly is relatively simple to implement, which makes it a fast, accessible option for initial analysis.
- **Sequential analysis.** It gives a clear, step-by-step reading of the instructions, helping you understand the sequence of the code's execution.
- It lets you trace the main code structures and identify the key execution paths.
- **Minimal overhead.** Linear-sweep disassembly usually has low computational cost, allowing for faster analysis.
- **Resource efficiency.** Where computing resources are limited, linear sweep can be the economical — or even the only — available choice.

Although linear disassembly has its limits, such as difficulty handling complex control flow and obfuscated code, its simplicity and speed make it valuable for preliminary analysis and a quick understanding of binary code.

Recursive traversal works by following the code's logical flow, jumps and calls included, which allows for more accurate disassembly. It has four stages. 1) The first is choosing a starting point, a kind of front door, usually the `main` function. 2) The second is following the code's flow — finding branches and other logic. 3) Recursive analysis, which can be presented visually, which is convenient. It suits complex structures thanks to its contextual analysis and graph visualization. Code that isn't referenced from known entry points may go untraversed and therefore be skipped, as noted above.

During recursive traversal, a list of addresses is kept, letting the disassembler track where it's been and avoid re-disassembling code — which is very efficient.

A hybrid approach, combining the linear and recursive methods, draws on the strengths of both to overcome their individual weaknesses. So, the key differences:

Disassembly with linear sweep:

- A sequential, iterative approach
- Simple to implement
- Limited handling of complex control flow
- May misinterpret data as code

Disassembly with recursive traversal:

- Uses recursion and often includes a control-flow graph (CFG). The edges in a CFG represent the possible paths execution can take from one block of instructions to another.
- Handles complex control-flow structures and obfuscated code
- More resource-intensive and complex to implement
- Provides a full understanding of the program's structure

Advantages:

| Linear sweep | Recursive traversal |
| --- | --- |
| Simple and easy to implement; fast initial assessment; low computational overhead | Accurate control-flow handling; effective for complex code and obfuscation; comprehensive understanding of the program's structure |

Limitations:

| Linear sweep | Recursive traversal |
| --- | --- |
| Limited accuracy with obfuscated code; difficulty handling complex control flow; may misinterpret data | Complex to implement; resource-intensive; potential for infinite loops |

Linear sweep is preferable for initial analysis, quick assessment, and cases where simplicity is decisive — analyzing simple executables, say, or identifying a code's basic functionality. Recursive traversal is preferable for deep analysis of complex code, critical applications, malware analysis, and scenarios where understanding intricate control flows and obfuscation matters. The choice between them depends on the goals of the analysis, the code's complexity, the resources available, and the need for accuracy and depth of understanding.

## Static analysis

Static analysis means analyzing code without executing it — simply reading it. By analogy, developers do code reviews of colleagues' work and find poor decisions in the code. But in malware analysis we don't have access to the source code. Binary reverse engineering + IDA FLIRT comes to the rescue. It's clear enough that manually analyzing a huge program without source can eat up years hunting for a vulnerability.

But antivirus checks software somehow without running it? Antivirus has signatures — patterns associated with viruses. And it isn't about an exact match; similarity, or a change in the size of standard files, is enough. A signature can be a string of bytes — [EICAR](https://en.wikipedia.org/wiki/EICAR_test_file), for example — or a cryptographic hash function. For classifying malicious files, fuzzy hashing works too, or a graph-based hash for executables. Among the tools, Binwalk, Strings, and Objdump are popular for static analysis.

In the most primitive version, viruses are identified not by file name but by hash. A hash also lets you tell whether a virus installed other viruses on the system (so analyzing a single file is enough) or replicated itself. I mention this is the simplest version deliberately, since even a minor change to the virus's code will completely change the hash.

If you want to create a hash yourself, Linux has md5sum, sha256sum, and sha1sum. Python has `hashlib`. Windows has [hash my file](https://www.nirsoft.net/utils/hash_my_files.html). There are many such services — [sha256algorithm](https://sha256algorithm.com/), for instance, which simply uses `echo -n "hello world" | sha256sum`.

Static analysis doesn't handle obfuscated code especially well. The simplest way to obfuscate data is base64, where binary data is converted into ASCII format — a method used even in ordinary HTTP. Take the word "one." First, each character is turned into a bit value: `"o" > 0x4f > 01001111`, `"n" > 0x6e > 01101110`, `"e" > 0x65 > 01100101`. Second, the bits: `010011110110111001100101`. Then the result is split into groups of 6 bits each: `010011 -> 19 -> base64 table lookup -> T`. And we get `One = T25L`.

With static analysis the code is studied without execution, which means obfuscation or encryption stays intact, making it harder to surface the malware's true purpose or functionality. Dynamic analysis, by contrast, lets you "see" what the malware actually does — which systems it talks to, what data it accesses or transmits, how it behaves under certain conditions — giving a clearer picture of its intentions and capabilities. But for analyzing shellcode, static analysis is almost always used. JS is fond of encoding like `%u33aa%ubbff%uddee`, which becomes `33 ff bb ee dd` — visible even in static analysis.

Attackers have various ways of protecting their software "creations." XOR, for one, is very popular in making malware because it's very simple; multi-byte XOR especially is relatively resistant to brute force. Junk insertion is also used — adding unnecessary bytes — as are branch functions that simulate code behavior, and overlapping instructions.

Overall, the typical, simplified process of working through a security-breach alert might look like this: we received an alert from CrowdStrike Falcon with high severity, then we decode a base64 string via PowerShell, check the destination IP on VirusTotal, check the file's hash, check which .exe files the user opened, and if there's an infection — we deal with the problem.

## Assembler

Many people learned BASIC at school, but there are other versions of assembly, such as MASM, Intel's standard for writing x86 code. The i386 processor, for example, contains eight 32-bit registers — that's x86. Although the same system can run both 32- and 64-bit applications, when the processor runs 32-bit code it works in 32-bit mode and can't run 64-bit code. So if malware needs to run inside the process space of a 64-bit process, it has to be 64-bit.

When an analyst examines a finished program for malicious code, they'll most likely be working with assembly. This is safe analysis, where "taking apart" the malware lets analysts study its code without executing it, avoiding potential harm to the analyst's system or network. If the analyst can read the code, they can understand the software's functionality without running it. There's no need to read the binary form of a suspicious file when there's assembly code. It also becomes possible to understand the malware's methods of spreading, payload delivery, and evasion techniques.

Since the analyst works with disassembled code, it has to be structured somehow. There are techniques for compartmentalizing and for identifying the control flow (CFG). Control-flow graphs are a visual map of the code's execution logic, which makes it easier to understand.

A control-flow graph (CFG) is an invaluable tool in reverse engineering for visualizing and understanding a complex program's logic. It gives a graphical representation of all the possible execution paths. Each node (basic block) in a CFG represents a sequence of instructions from a single entry to a single exit, and the edges indicate the transition from one block to another. This visualization lets the reverse engineer see conditional statements, loops, and branches in a clear, organized form. By studying the CFG, you can surface critical paths, dead code, and potential vulnerabilities. On top of that, a CFG helps you understand the program's high-level structure, making it easier to form hypotheses about the structure and logic of the program's source. That bird's-eye view of the execution process is crucial when working with large, complex software, where reading linear disassembled code is impractical and error-prone.

## Antivirus signatures

So the analyst has taken the virus apart and understood its innards. Spent months reverse-engineering. Managed to understand the malware's internal design, its weak points, perhaps discover its origin or author. What next? The analyst's knowledge has to become a signature for the antivirus. Taking things apart surfaces the unique templates or signatures of the malware, which helps develop antivirus signatures and heuristic analysis. Antivirus signatures are hashes, often using algorithms like CRC or MD5. Byte-streams are also popular, primitive almost to the point of the impossible: they simply search for a string, such as `X5O!P%@AP[4\PZX54(P^)7CC)7}$EICAR-STANDARD-ANTIVIRUS-TEST-FILE!$H+H*`.

Signatures also include a checksum verification via CRC — just 4 bytes, or 32 for CRC32. For the string above, the checksum would be `0x6851CF3C`. Both algorithms generate many false positives.

Every antivirus has its own checksum algorithm, and all the changes and improvements to those algorithms aim to reduce false positives. A cryptographic hash, on the other hand, barely suffers from false positives. But because a cryptographic hash is more expensive to compute, it isn't always used — an MD5 or SHA1 hash costs more than CRC32. And, again, changing even a single byte in the virus's code leads to a completely new hash: add one byte at the end of the file and it can fool the OS/antivirus.

That sounds dangerous, so the reader is surely expecting algorithms for more successful virus detection. They exist — one of them fuzzy hashing, with each antivirus having its own implementation. False positives will still happen, but not to the same degree, since it looks for a pattern rather than a sequence of bytes — which means fooling such an algorithm takes more changes to the malware's code.

Suppose you download an Astra Linux distribution: how do you know it wasn't modified by an attacker? Almost always it's a hash check. Hash functions process a file's contents and generate a unique, fixed-length output. Such hashing is irreversible — you can't reconstruct the original file from the hash. The hash stays unchanged if the file's contents don't change; in particular, changing the file's name doesn't affect the hash. These hashes play an important role in identifying unique malware, searching databases, and serving as indicators of compromise (IOCs), and they're also used in storing passwords for user authentication. During analysis, names like Emotet, QakBot, or LockBit may surface, which lets you classify things — spot LockBit, and you assume the attacker is aiming to disable security tools and encrypt files. Some of the commonly used hashing algorithms are MD5, SHA-1, and SHA-256. Let's try it by hand:

`sudo apt install hashalot`, then `sha256sum filename.exe`. This command creates a hash of the file that you can paste into [VirusTotal](https://www.virustotal.com/gui/home/search) to check whether it's shown up in the world of malware. Example: <https://www.virustotal.com/gui/file/ac73e3c9e7ee62be2d2138fa5f8ef28679c0a191882b7a30e35ce7b89786935f>

A slightly more advanced level is working with PE Tree. Let's examine the WannaCry.exe file we already have. PE Tree gives information about the file, with its sections. First, it provides file information including various hash values, with one additional hash known as imphash. Imphash, or import hash, is a special type of hash used in malware analysis. It's created by hashing the list of imported functions of a Windows Portable Executable (PE) — a .exe or .dll, say. This hash helps identify and correlate different malware samples that may share the same codebase or functionality, since samples with similar behavior often import similar sets of functions. Imphash is a valuable tool for quickly grouping and identifying related malware samples. You can check whether an imphash is "malicious" at [bazaar.abuse.ch](https://bazaar.abuse.ch/verify-ua/).

![PE Tree file information](https://your-scorpion.ru/wp-content/uploads/2024/01/photo_2023-03-22_08-34-46.jpg)

PE Tree also shows the level of entropy (randomness) in the file.

Another useful tool is [peframe](https://github.com/guelfoweb/peframe); install it and run `peframe WannaCry.exe`. The result gives you information about the file, a list of suspicious actions and sections, a list of the packers used if it's packed, a list of functions with anti-debugging techniques, and much more.

The command `vol.py -f wcry.raw imageinfo` shows the KDBG (KDDEBUGGER_DATA64) structure, which will be used by the `pslist` and `modules` plugins. From the output you can guess that the infected system runs Windows XP.

![Volatility imageinfo output](https://your-scorpion.ru/wp-content/uploads/2033/07/dadadad.png)

We move to `pslist` for a list of the system's processes. We see the process ID, its name, the number of threads, the start and end times, and whether the process is Wow64 (when 32-bit space is used on a 64-bit kernel). We won't see hidden processes, but psscan will show those. In this particular case, PID 1940 initiated PID 740, and both processes look suspicious. Next, with `psscan` you can see a list of all processes, terminated ones included, which helps determine the process hierarchy and the chronology of their creation.

![Volatility pslist output](https://your-scorpion.ru/wp-content/uploads/2033/07/fda.png)

Let's run `psscan` and see the terminated processes taskdl.exe and taskse.exe together with the parent process PID 1940. With the `sort` key you can sort the results by date, which makes the chronology clearer.

![Volatility psscan output](https://your-scorpion.ru/wp-content/uploads/2024/03/Customer.png)

But that's not all — we continue with `dlllist`, which lets us see the loaded DLL processes. This is how we can understand which file initiated the infection, and it can give a clear picture of the malicious processes. In our case, tasksche.exe looks suspicious. Usually at this stage you need to study the DLLs to understand a process's characteristics — encryption, registry modification, socket creation, and so on.

![Volatility dlllist output](https://your-scorpion.ru/wp-content/uploads/2024/03/dadadad.png)

The @WanaDecryptor@ process with PID 740 also uses the same path as tasksche.exe. Judging by the DLLs loaded by @WanaDecryptor@, it may perform socket creation (Ws2_32.dll), high-level network interactions (WININET.DLL), registry queries (ADVAPI32.DLL), encryption (SECURE32.DLL), and interaction with browsers (URLMON.DLL) such as Internet Explorer, and so on.

Other useful commands are `handles`, `printkey`, `connscan`, `ethscan`, `filescan`, `memdump`.

## Stack

In x86 assembly the stack plays an important role during a function call. When a function is called with the `CALL` instruction, the address of the instruction following the `CALL` (the return address) is pushed onto the stack. Execution then jumps to the function's starting point. Inside the function, parameters may be pushed onto the stack, and local variables are often stored there too. The stack pointer (ESP) is adjusted accordingly throughout the function's execution. When the function is ready to return, the `RET` instruction is used; `RET` pops the return address off the stack and jumps to that address, resuming execution after the original `CALL`. The state of the stack on the function's return should match its state at the call, except for the return address, which `RET` removes.

The extended stack pointer (ESP) points to the current position at the top of the stack. Before it comes the base pointer (EBP). The command `mov eax, [ebx]`, for instance, simply moves 4 bytes from `EBX` into `EAX` — it's a move instruction. `mov eax 0` puts `eax` at position zero. And the instruction `foo: .string "name"` is for creating a string with the name *name*. `$0x6` is a constant. `PUSH EBP; MOV EBP, ESP` is the standard sequence for setting up a new stack frame at the start of a function. `CALL` is a function call; `PUSH` is used to put data on the stack, not to call subroutines, as is sometimes written in the Russian-language internet.

The `EBP` register, the base pointer, matters because it provides a stable reference point for accessing a function's parameters and local variables, regardless of where the stack pointer (ESP) might be at any given moment of the function's execution. The old base pointer is accessed via the current base-pointer register (EBP), referencing the memory location directly above where EBP points on the stack.

Any process in x86 architecture can live in a size from 0 to 4 GB — virtual memory the process treats as entirely its own. That same range can be expressed from 0x0000000 to 0xFFFFFFFF. Kernel is the base; then the Stack; the Heap for dynamic, real-time work with memory; then data and text, which don't change size at runtime.

## Buffer overflow

A buffer is a region of memory holding data. If there's too much data, you get a buffer overflow. An overflow on the stack can overwrite adjacent memory that may hold critical control information, such as return addresses, which can lead to unstable program behavior or to exploitation. A user can enter too much data into a text field themselves, and a poorly written program will create a buffer overflow. Even network packets can cause one.

How overflow works. Suppose we have a memory cell `a` with two bytes of data, and right after it a memory cell `b` with two bytes of data. These two cells sit next to each other on the stack. If more than two bytes are placed into cell `a`, the data effectively overflows and gets written into cell `b`, which the programmer didn't expect. Buffer-overflow exploits use this process for their own ends.

To fight this vulnerability there are terminator canaries, random canaries, and random XOR canaries. These techniques help surface a buffer overflow in advance and avoid running potentially dangerous code.

Stack canaries are security mechanisms that protect against buffer-overflow attacks by detecting changes to a known value placed before the sensitive control information on the stack. When creating shellcode for an exploit, an attacker usually aims to overwrite part of the stack, the return address included. Stack canaries prevent this by detecting the overflow and potentially terminating the process or raising a warning, thereby cutting off the exploit attempt.

Random XOR canaries are like random canaries in that a random value is used as a security measure. But in random XOR canaries the canary value is combined with some control information, such as the return address, to make it even harder for an attacker to predict the value and successfully carry out an overflow without detection.

Another technique, Address Space Layout Randomization (ASLR), first appeared in Linux, then in Vista; Apple adopted it only in 2007. Brute-forcing even a 64-bit system is still possible.

So-called shellcode can provoke stack and buffer overflows. kernel32.dll holds fairly important functions and lives in memory; shellcode finds kernel32.dll in memory and parses it in search of the function it needs. kernel32.dll has an address reached along the TEB -> PEB chain. Shellcode is used in exploits to execute arbitrary code on a target system — usually to open a shell that can accept commands, which lets the attacker control the system. Other uses aren't the primary goals of shellcode.

Shellcode shouldn't contain null bytes, because functions like `strcpy` stop copying at a null byte. Many C standard-library functions, `strcpy` among them, end their processing when they hit a null byte; if shellcode contains null bytes, it can be cut short prematurely during injection, causing it to fail.

Importantly, shellcode has to be in a form the processor can execute directly, without extra compilation or interpretation — it has to be machine code, the set of native instructions the processor can run.

## Analyzing a malicious file

Picture a scenario in which malware is suspected of stealing sensitive user information. To understand how and when the theft happens, an analyst can use breakpoints in a debugger. This lets you stop at the functions presumably involved in the data leak, so the analyst can pause the malware's execution right before those functions run.

If, for example, the malware's code has a function that appears to send data to an external server, you can set a breakpoint (0xCC) on the call to that function. The analyst can then view the program's state, including the contents of variables and memory. This can reveal the exact data being targeted for theft — credit-card numbers, passwords, or personal identifiers.

Breakpoints can also help you understand under what conditions the data theft occurs — whether it triggers on certain user actions or after a certain interval. This information is very important, both for understanding the malware's behavior and for developing strategies to reduce or prevent unauthorized access to data.

Reverse engineering can be used legally and ethically for interoperability. A software company may need to make its new product compatible with older systems, for instance; here, reverse engineering can be used to understand the mechanisms and interfaces of the older systems. That knowledge helps build software that can interact smoothly with those systems without violating intellectual-property rights. Another ethical use is security research, where reverse engineering surfaces vulnerabilities in software to raise the level of cybersecurity.

In the example below I'll work with REMnux — like Kali, but for reverse engineering. Flare-VM will also be used: a script that installs all the tools for malware analysis on Windows.

Let's take WannaCry apart: launch Ghidra in the terminal and create a new Non-Shared Project.

![Ghidra new project](https://your-scorpion.ru/wp-content/uploads/2024/01/iPhone-14-Pro-Max-2-1.png)

Drag the malicious file into the program window and get this popup:

![Ghidra import popup](https://your-scorpion.ru/wp-content/uploads/2024/01/jhj.png)

Then, in "Analysis Options," don't forget to check the boxes "WindowsPE x86 Propagate External Parameters" and "Variadic Function Signature Override (Prototype)," and only then click Analyze. After analysis, the left side of the interface has a Tree section with a nested Export list, which presents the exported functions, while the Import section holds the API functions — the dependencies. In this file we observe a number of APIs tied to registry access, such as `RegCreateKeyW` and `RegSetValueExA`. Malware often uses the registry to store persistence and configuration data. For details you can always turn to the [Microsoft documentation](https://learn.microsoft.com/en-us/docs/).

![Ghidra analysis view](https://your-scorpion.ru/wp-content/uploads/2024/01/6865858.png)

Now let's look at Symbol Reference. Open the window and immediately filter by `reg`. In the filtered list, `RegCreateKeyW` appears. Select it, and you'll see two references (Call and Data) in the "Symbol References" window. Double-clicking the address `00401d00` jumps you to CodeBrowser, where a CALL to RegCreateKeyW is highlighted — essentially a function call.

![CodeBrowser CALL to RegCreateKeyW](https://your-scorpion.ru/wp-content/uploads/2024/01/iPhone-14-Pro-Max-1.png)

If you right-click 80000001h and choose Set Equate from the list, a window appears. Since we know RegCreateKeyW works with the registry, we'll choose HKEY_CURRENT_USER, which lets us create a reference in place of the constant — turning the line into something more readable.

![Set Equate window](https://your-scorpion.ru/wp-content/uploads/2024/01/dadadad.png)

And so on… what will the researcher be looking for in such code? In WannaCry's case specifically, an attempt was spotted to connect to a rather long domain. As it turned out, if the program manages to establish a connection with the domain, it halts. The program thought it was in a sandbox and stopped spreading and encrypting files. This helped slow the spread, but other versions appeared with other kill mechanisms.

Another useful tool is strings. The command `strings WannaCry.exe` extracts the text from the file and prints it to the screen — the strings it managed to find in the file. The result is far from the best way to study a binary, but it gives a sense of some of its capabilities. A more interesting command is `strings WannaCry.exe | grep Crypt`.

## Fuzzing

This is one way of testing — at bottom, the plain generation of an endless number of input variants for a program, at enormous speed. It can be strange, invalid data meant to surface bugs and vulnerabilities. If we've simply downloaded a program from the internet and are trying to test it, that's the black-box format — we know nothing about the program's internals, we have only the interface. White-box fuzzing is when we've been given full access to the original code. And gray-box fuzzing is when we have some knowledge of the internals, but not complete.

The data can be different. If we take existing data and change it algorithmically, that's mutation; the alternative is generating data from scratch, based on certain rules. On top of that you need automation in the form of watching behavior, tracking application crashes and unexpected behavior.

Naturally, software exists for this: AFL (American Fuzzy Lop) is known for its algorithm and helps find memory leaks and vulnerabilities; LibFuzzer plugs into the development process; PeachFuzzer and BOOFUZZ are good for network protocols.
