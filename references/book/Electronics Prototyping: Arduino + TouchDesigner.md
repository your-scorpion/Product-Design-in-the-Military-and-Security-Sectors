

# Electronics Prototyping: Arduino + TouchDesigner

One of the most interesting skills an industrial designer can develop is the ability to translate their ideas into the physical world. Alongside the obvious things — designing production-ready plastic parts, solid-body surface modelling in SolidWorks — they need to be comfortable with multiple prototyping techniques. Any technique will do: shape something out of clay, model it for the 3D printer, sketch it and find someone with the right machinery to cut, cast, or carve the shape you need. But an equally important branch of the work is prototyping electronics.

The most popular way to prototype electronics is to use Arduino as the controller. It can drive analog, digital, I2C, and DMX devices via C++ code. [Arduino](http://arduino.cc/) is the name of the company that makes the microcontroller, which now ships in many variants — including the Russian Iskra Neo from Amperka, and JavaScript-flavoured versions. If you don't enjoy coding, there's the Firmata library, which lets you read sensor values and control them visually in TouchDesigner.

What to buy to start learning:

- Arduino Nano and Uno (small and large)
- USB cable for connecting to the computer
- An 830-point breadboard
- A potentiometer (10 kΩ)
- A resistor kit (220 Ω, 10 kΩ)
- A capacitor
- A VT90N2 photoresistor or equivalent
- A push-button switch
- Add-ons as you go: a servo with a power supply, a sound-level sensor, an infrared distance sensor

![Arduino Nano and Arduino Uno](https://your-scorpion.ru/wp-content/uploads/2020/06/Untitled-1.jpg)
*Arduino Nano and Arduino Uno.*

And a power supply. If you buy a 12V 6A supply, choose one rated for roughly twice the current you actually need — especially if you're sourcing from Chinese suppliers. The components above split into two groups by their voltage–current behaviour: passive/linear and active/nonlinear. Resistors, microphones, and inductors are passive — they obey Ohm's law. Diodes, transistors, and triacs are active; voltage and current don't scale linearly. Current, in short, is the directed motion of charged particles — electrons carrying negative charge. When you slot a battery in the right way around, plus to plus, minus to minus, that orientation is what sends the electrons moving in a particular direction. A human body conducts current too, which is why we work with low-current circuits. Electricians have a harder, more dangerous job.

## Where the electricity comes from

We need to get electricity out of a primary source — a galvanic or fuel cell, say — and convert it in a secondary source. A primary source might be a hydroelectric plant: water mechanically spins a generator, and the mechanical energy becomes electricity. Then we convert one form of electrical energy into another (DC to AC). Having generated high-voltage, low-current power, we have to push it over enormous distances — over wires, most likely, into some closed administrative territory deep in Siberia.

Near your house you'll find a transformer. Visual cue: if there are more windings on the input side than the output, it's a step-down transformer; the opposite, step-up. A transformer can output a higher voltage than it received. The energy then runs through a rectifier with a diode bridge and a smoothing filter. Low-frequency transformers are physically big.

In a closed administrative territory you'll have a five-storey building with a transformer cabinet feeding cables into the apartments. Inside the apartment, almost certainly, sits a stabilised transformer power supply — because that's the most common kind. Mains 220 → step-down transformer → bridge to rectify the sinusoid. Somewhere in the house there's a phone that needs charging. When you charge it, the source voltage is converted to a higher-frequency voltage and then rectified. On simpler devices, the charging cable may carry ferrite filters to suppress noise, in cases where the electronics are sensitive.

Take the transformer out of this chain and you're in dangerous territory. You almost certainly need galvanic isolation, which is about exactly that — isolation. Galvanic isolation is simply a connection without direct electrical interaction; it can even be done with reagents. I wouldn't advise running a linear power supply without a transformer with isolation at the end. And more generally: if a device is mains-powered (220V), galvanic isolation is non-negotiable, even when it makes the device more expensive. A smartphone can get by without it — on the motherboard, near the processor, there are always converters anyway. The secondary source has to deliver the specified power, convert the form and magnitude of the voltage, and stabilise it.

When you're designing the protection layer for the board itself, galvanic isolation is the universal answer for the strongest defence — it sits in every decent power supply. It assumes no electrical link between blocks. The isolation can be transformer-based, opto-isolated, or capacitive.

For protection against reverse polarity, even the simplest diode-based circuit can do the job. Picture a device powered from mains, converted by a switching supply, with secondary supplies or a battery as the primary power element. Mains-powered devices have phase protection. Smart devices use batteries. If the user happens to swap polarity by mistake, both the control and execution sides of the device can die without at least a diode in the path. Even with protection, reverse polarity is generally bad news unless the device runs on AC. Connectors usually have built-in protection — the physical shape of a barrel connector with an inner positive and outer negative simply doesn't let you wire it backwards.

So you stick a diode in: it lets current through one way and blocks the other. If the user inserts the supply incorrectly, current doesn't flow — you'll see breakdown voltage. Suppose the load draws 2A and the diode drops 1.5W. That's a lot — you'll need a higher-power diode and a heatsink. Diodes are a fine solution for low-power loads but wasteful for big ones. If a mid-range diode is dropping 0.5W that's already too much, and you need another approach. A Zener diode is also a kind of diode, with a low breakdown voltage — handy for cutting power. The alternative is TVS diodes, which suppress sharp spikes faster than Zeners. For high static-discharge protection, the universal answer is a TVS diode, a varistor, or a gas-discharge tube. That's a level of overkill protection, though — for consumer devices a resistor and a Zener are enough.

You can also drop a fuse in series with the supply and a status LED in parallel. Fuse blows, circuit opens, current stops. Putting a fuse on the input is always a good idea; the diode less so. There are self-resetting fuses too — carbon chains break under load, then restore once the voltage stops being applied. A standard fuse you have to swap, but it handles bigger loads. A good pattern is a regular fuse at the input and a self-resetting one on the converter or regulator — they combine well.

Another route is power FETs. At high voltages, in the tens of volts, the transistor breaks down. Personally I like relay-based systems despite the switching they require: a control system, independent of the power source, drives the relay. And another solid option is a linear regulator: 12V in, 5V out. Seven volts get lost as heat in the regulator, and that has limits. Remember that a regulator always *reduces* — it can't step voltage up.

The coils of wire you see in electronic devices are inductors — they suppress interference, store energy, and smooth ripple. They have colour markings too, with the thick ring marking the starting position. There are high-frequency and low-frequency versions. The high-frequency ones, made from ferrite, amplify signal coupling; low-frequency ones, made from electrical steel, are big. If an inductor is being used to limit current, it's called a choke. The power switch on a desktop PSU is a special case of switchgear with a dual choke. Audio gear uses chokes to filter and split frequency bands across different drivers. Old radios with tuning dials used variometers, which have since been replaced by varicaps. Solenoids are used in Gauss guns, but they're also the door-lock actuators in cars, the gear shifters in automatic transmissions, and the inductor in induction heating.

## Transistors, capacitors, diodes

Transistors change current and voltage parameters in a circuit. Modern devices can contain billions of them; they're how computation happens. The Apple M1 Ultra packs in 114,000,000,000+ transistors. In the simplest schoolbook example, one transistor can open or close current flow and remember its state, performing one of three SIMD operations. Almost any device that switches on involves a big change in current — a lamp, an electric stove — and that's a transistor's job. There are even biotransistors designed to work inside living organisms. Capacitors, when discharged, let current flow unrestricted, racing off toward infinity. Once charged, current doesn't pass through any more. Voltage with no current, current with no voltage. Semiconductor diodes in modern circuitry can permit or forbid current in a particular direction. A diode rated for more than 1A is called a rectifier. And a ceramic capacitor loses capacitance when heated.

## Arduino as a board

I have an Arduino UNO R3 (CH340G) on the bench. I use the UNO for prototyping and the Nano for commercial projects. Nothing's stopping you from making your own Arduino on a prototyping board. For schematic design and exporting Gerber files for fab, I use EasyEDA.

An Arduino is a regular electronic board. That is, a collection of separate components connected to each other. It could equally have been an integrated circuit packing millions of transistors onto a single die. All of the components are linked by traces on the PCB or by wires on a breadboard. Schematics in general split into analog, digital, and hybrid. Digital are discrete: a particular voltage level corresponds to a state of 0 or 1, the logic gates. Digital design is easier than analog, though both can carry distortion and noise. Hybrid circuits combine both. The simplest radio receiver almost certainly has an analog frequency converter alongside digital control.

![Arduino board](https://your-scorpion.ru/wp-content/uploads/2022/06/Artboard.png)

The Arduino board has a USB input for connecting to a computer, a reset button, an LED on digital pin 13, fourteen digital I/O pins (signal on/off — 0/1 — 0V/5V — for buttons, LEDs, sensors), and six analog inputs that read a wider gradient of incoming signals (light intensity, sound volume, distance, moisture, and so on). Pins 0 and 1 shouldn't be occupied by sensors.

![Arduino pinout](https://your-scorpion.ru/wp-content/uploads/2020/07/Group-136-1.png)

The Arduino Nano has linear regulators and a USB–UART converter. If you're doing anything with a cellular module, the CPU will almost certainly talk to it over USB 2.0 HS or UART. The Nano can power external modules drawing up to 50 mA. Unregulated external voltage goes only into the VIN pin, which feeds the regulator and accepts 7–12V. Linear regulators have low efficiency — the excess voltage is dumped as heat, so they need extra cooling. They also require a positive voltage difference between input and output, because there's always some voltage drop across them. With only half a volt of difference, most regulators just won't work. That said, you can still power an STM-based board with linear regulators. And they're very easy to use.

### Going from prototype to production

A lot of Chinese consumer electronics is built on STM32 (in contrast to Arduino's Atmega), and any piece of digital equipment needs a microcontroller or microprocessor. Electric toothbrushes, warehouse robots, intercoms, Yandex devices — all STM32. From there it's mass production, but that requires real market demand. Bringing in pre-assembled electronic modules and screwing them together is one process; doing the component placement on the board yourself, designing the schematic, working out the PCB topology is another process entirely. ESP is the other common choice and turns up frequently in consumer goods, especially in smart speakers. Don't chase 14nm — 28nm production is much better established. Plenty of Russian electronics ship in millions, too — electricity meters, light fixtures, point-of-sale terminals. And the Elbrus/Baikal lines. All inside the Eurasian Economic Union.

A microcontroller is just a chip for controlling other electronic devices. It contains a processor core and its own RAM and ROM — a one-chip computer for simple operations. STM32 is the foundation for consumer electronics, with deep documentation, an integrated development environment (Eclipse), debuggers and analyzers, and a wide library ecosystem. Programming for one STM32 is very similar to programming for another in the same family — the memory addresses change, but the high-level functions for sending data are the same. That sounds simple, but the real difficulty of working with electronics is having to juggle many aspects at once: the electronics themselves, the PCB, the logic of internal peripherals, programming, debugging tools, and the heaviest one of all — powering the microcontroller. There are different STM32 lines: L0 is power-sensitive, WL is for wireless.

On the power side, the supply voltages have to sit in the permitted range (thanks, Captain Obvious). Typical STM32 voltage is 3.3V; you can't go higher than that. Only pins marked FT will accept up to 5V. Analog peripherals need their own ground plane separate from the digital ground, so noise from the digital side doesn't bleed across. For an STM32F030, the typical power scheme has VDD as digital power and analog power that has to be ripple-free. If you're building a measurement reader, for instance, the analog and digital sections need separate supplies — noise corrupts the readings. But if you don't need high analog precision, you can power everything off a single supply, with filtering networks and decoupling capacitors.

For a small device that stores, plays, and overwrites a handful of audio files, the architecture looks a lot like an office printer with a small embedded web server. I'd reach for the FatFs library. Add an SD card for file storage, and let something like an STM32F407 do the rest. FatFs is old and its popularity is fading, but it's still in active use on small devices and it's straightforward: a root catalogue with filenames and pointers to the sectors that hold the file data. Microcontrollers have a library called FatFs that's a platform-independent driver for this filesystem — runs on any microcontroller as long as there's enough RAM. In theory it leaves no traces, but since writes go sector by sector, at least one sector (~1 KB) lives in memory at any time. With it you can read and write files by name.

## Processors and the size of a machine word

Arduino supports a range of processor architectures — Intel (x86 and ARC-Argonaut RISC Core), Arm® (Advanced RISC Machine). If you look up the Arduino Due you'll be surprised to find a proper 32-bit Arm Cortex®-M3 inside, powerful enough to run music. *32-bit* means every data type is represented as a 32-bit number. 2^32 = 4,294,967,296 bytes = roughly 4 GB. That's the memory limit on 32-bit machines, the one that doesn't exist on 64-bit ones. The 32 here is the width of the machine word: how wide the registers are and what width of operation the ALU can do in one step. 8 bits = 1 byte (IBM), but the computer cares more about the machine word, which is 32 bits. 64-bit systems are more interesting: 2^64 = 18,446,744,073,709,551,616 = 16,777,216 TB. That's the current ceiling on CPU bitness, though 128-bit CPUs are theoretically possible, and GPUs already use 512-bit registers. In reality, you can still bump into 16-bit processors that work with… 64 KB, since 2^16 gives 65,536 values. You'll find the ancient Intel 8080 driving cheap displays, or the immortal homegrown Z80. Even simpler processors get 256 values from 2^8 — an 8-bit processor that can address the ASCII character set.

If you go looking for processors used in simpler equipment than computers or phones, Intel (x86) and AMD dominate the desktop space, ARM dominates mobile. Apple's M1 ARM is a category of its own — it only runs in Apple hardware and qualifies as a work of art. Outside those, you can look at Zhaoxin KX-U6780A or KX-U6880A — performance doesn't impress and doesn't quite reach Intel Core i5, but they work. The Hygon Dhyana family exists too, with less rosy results. Then there's Elbrus, used in government servers and military equipment. Personally I'd be watching RISC-V.

Every processor has adders, which add single-digit numbers. A modern CPU adds multi-digit binary numbers, and that requires a multi-digit adder — one that takes carries from earlier additions into account. To convert decimal numbers to binary you use an encoder; the most familiar example is a telephone keypad. To convert the other way, a decoder. Working with quantum devices, you operate on qubits and their superpositions.

A processor has registers — its own ultra-fast memory in the form of named cells. Since all the work happens inside the CPU, this is the fastest memory, and conventionally the most expensive and the most scarce.

## Operating systems

Choosing the OS is its own question. Every OS distinguishes between user space and kernel space. Kernel space is the kernel's space. As a user, you always work with software in user space. A program can ask the kernel to send a file to the printer or to allocate memory — that is, the program interacts with the kernel. The kernel manages resources. DOS didn't have this distinction. DOS was an optimal solution for the processors of its era, but it was extremely vulnerable. MS-DOS was just five files:

- **io.sys** — started first and read config.sys
- **command.com** — the interface
- **config.sys** — rules for how the system worked
- **msdos.sys**
- **autoexec.bat** — commands to run after boot

Plus drivers — `mouse.sys`, `cdrom.sys`, `keyrus.exe`. Since MS-DOS had no user/kernel split, any software stayed in the system as a driver, which was very welcoming territory for viruses. Hence the constant freezes. Fonts were bitmap (8×8 matrix), and `KeyRus.exe`/`kb.com` could swap Latin characters for Cyrillic and track keyboard-layout changes.

When picking or designing an OS for a device, remember that drivers belong in kernel space, because they work with hardware in a monolithic kernel. Microkernels exist in various flavours, with drivers running outside both kernel and user space. The Linux kernel is monolithic — if a driver dies, you get a kernel panic and the system reboots. For embedded OSes, the kind that run avionics or in-car computers, the system has to respond to interrupts and not fall over, so a microkernel is the better fit, with drivers separated from the kernel. The kernel is the basis of the OS, responsible for the bridge between software and hardware, file and network access, inter-process communication, task management, and parallelism. Multitasking also matters. There's preemptive multitasking, where the system kills a hung program, and cooperative multitasking, where if one program hangs you can still work with others. MS-DOS was single-tasking — one program at a time. Fairness compels me to add that MS-DOS did have a form of cooperative multitasking through a driver that switched tasks via interrupts. Cooperative multitasking is easy to implement.

Many OSes exist: server OSes like Linux, Windows Server 200x, FreeBSD, Solaris. Go deeper and you find embedded OSes running inside microwaves, cars, phones, MP3 players — QubesOS, QNX, VxWorks. Sensors run TinyOS; smart cards have their own very primitive OSes. An OS doesn't have to be huge — RIOT weighs 10 KB and runs on 8-bit microcontrollers.

I'm spending extra time on this because it directly affects UX. A hybrid kernel means that a crash of the NVIDIA driver no longer takes the whole OS down with it. The famous Windows Blue Screen of Death often came from a driver crash. There are plenty of other reasons it appears, of course.

For decent audio you'll also need to think about driver direction, the shape of the acoustic enclosure, the presence of a bass-reflex port, and a hundred other things. And about putting the volume knob in a good place.

## Connecting expansion boards

The board accepts all kinds of nice sensors — "shields" or expansion boards. They come in two ribbon-cable flavours: 3-pin and 4-pin. The 4-pin form is GVSS — ground / voltage / signal / signal. The 3-pin is signal / power / ground. Easy rule: the black wire always goes to the pin marked `GND`. I prefer 3-pin shields — the sensor only needs power, signal, and ground. You can run the board off a battery: for the Nano, take a battery, connect plus to 5V via VIN, and minus to GND.

A few things to bear in mind on the power side. Power can travel down a wire — copper, say. You need to feed a device with 10A at a constant 12V; the distance between device and supply is 10 metres (or 20 metres of wire — 10 going, 10 returning). The wire is 1.5 mm² in cross-section, so the supply outputs 12.5V but the device only sees, say, 10.9V. That's why the supply belongs as close to the load as possible. Sometimes you want to bring power up gradually — dimming a lamp, say — and that's a job for a thyristor or triac.

Every enclosure for an electronic device needs grounding. Without it you'll feel shocks, you'll interfere with microphones, speakers, electrostatic emitters, and pickups, and the electronics inside are at risk. The metrology folks won't be happy either. If your enclosure is shocking people, the cause is usually dust in the PSU, the PSU dying, or chips touching the case. The cheapest grounding is a varistor or reed switch. For industrial sites, lead or zinc grounding electrodes. Old wiring is ungrounded TN-C, even when the cable has three conductors with one notionally being the ground. The reverse also happens: the outlet has a ground, but a cheap extension cable has a decorative grounding pin that's not connected to anything.

![Component photo](https://your-scorpion.ru/wp-content/uploads/2020/06/IMG_6481.jpg)

## Picking a battery

A battery is a cell with an anode, cathode, separator, and electrolyte. Cells go into modules, modules go into a pack with a battery-management system (which also handles thermal regulation). You can buy cells and assemble your own packs.

Li-ion and Li-Pol are roughly equivalent: Li-ion has fewer cycles but delivers energy better. Most gadgets use Li-Pol. Fast charging shortens battery life. Cars often use cobalt-based chemistries. You can find Li-ion at 4.35V or 4.4V (Li-HV cells). For lithium-manganese and lithium-nickel-manganese cells, don't charge to 100% or discharge below 30% — the battery lasts longer that way. For lithium-cobalt cells, don't charge above 4.15V; otherwise hydrogen is released and, with nowhere to go, the enclosure swells.

## Assessing battery quality and measuring voltage

Your electronics person should always have two high-voltage tools on hand. The first is a multimeter, which measures up to roughly 50V — but it only shows average voltage. If you need to see the actual signal shape, levels, a sine-wave plot, confirm the line isn't smoothing, a multimeter won't help. That's what the second tool is for: an oscilloscope, which isn't cheap. Plus current clamps that handle DC.

If you're building an autonomous system, you have to measure the supply current. On mains-powered systems, you care about avoiding current spikes and making sure nothing heats up more than it should.

Say you have a battery in a phone and you need to monitor its capacity. One option is monitoring by voltage — but voltage drops nonlinearly and changes over time as the electrolyte degrades. The other approach: you know the rated capacity in advance, and you install a component to track how much current has gone into the system. That number is subtracted from the theoretical total. I can't recommend that approach. You've probably had the experience of a phone reporting 20% and then dying a moment later. That's exactly this unreliable calculation at work. You should keep the objective capacity tracked, and the only reliable way to measure real capacity is current monitoring — a dedicated chip for it. If you don't know the initial capacity, drop in a resistor and time how long the battery takes to discharge through it. Full charge to full discharge is the only reliable measurement.

Make sure the measurement instruments don't influence the system. A voltmeter has nominally infinite impedance; if that impedance isn't significantly greater than the load voltage, the meter affects the whole system. Many times greater, and it doesn't materially affect anything. Voltmeters have good impedance for measuring high-power circuits. Analog and digital solutions both exist for measurement; the digital ones pass the signal through some analog-to-digital processing. A really precise microammeter is more likely to be analog and used for debugging — useful, for example, when a device is in sleep mode drawing some unknown current and you need to know exactly how much. Measuring nanoamps will be analog, usually on either ancient or highly specialised equipment. Standalone voltmeters are scarce on the market today; the multimeter dominates. The measurement methods are the same, but the analysis differs. A modern instrument is expected to show current, temperature, capacitance, and resistance — which sometimes hurts the accuracy of one or two of those. A multimeter is non-negotiable for a programmer working with hardware.

For solutions: measuring the voltage drop across a shunt is one of the most reliable methods for systems with modest current draw, where the current monitor is built into the system. That's for small electronics. Another method, fairly unpopular in my experience, is a Hall-effect sensor for current estimation — works well for circuits with high current draw. Current through a conductor creates a magnetic field around it, and the two ends of the conductor end up at different potentials. That's the corkscrew rule — any conductor creates a magnetic field. The third method is small transformers, rated for low current draw and used for current detection. Not used in DC networks.

## Writing code to the board

Assuming you have all the materials, the software side comes next: download the [IDE](https://www.arduino.cc/en/main/software). If your Arduino isn't official, you'll need the CH340/CH341 driver. The board will appear under *File → Port*. Pick the board type under *File → Board*. For Chinese clones, set the board to "Duemilanove" — the one that came before the UNO. After that, go to Windows Device Manager, find the COM port the Arduino took, and pick it under *File → Port*. If you hit problems, almost every question has been answered [here](https://www.arduino.cc/en/Guide/HomePage) and [here](https://www.arduino.cc/reference/en/).

The first step: any board has an LED on digital pin 13. Most Arduino boards ship with a debug LED so you can run your first program — a blinking light — without wiring anything else up. Just current, no voltage. Plug in over USB, go to *File → Examples → Basics → Blink*. The LED should start blinking. Don't forget the reset button on the board if anything misbehaves. Don't get stuck here if it doesn't work the first time — we're going to look at setup in more detail.

![Blink example](https://your-scorpion.ru/wp-content/uploads/2020/07/575757575242.png)

Now let's blink an external LED. Every LED has a + (anode) and a − (cathode). You can tell them apart visually: looking at the underside of the LED, there's a flat side along the rim — that's the cathode. The other clue is leg length: the anode is longer. This matters, because LEDs only let current flow one way — anode to cathode — and they don't tolerate overload. Since current flows from positive to negative, the anode goes to the current source (here, the 5V digital signal) and the cathode goes to ground, with a resistor in series to limit the current.

A resistor is a current limiter. Resistors have a nominal resistance in Ohms, a maximum rated power (exceed it and the resistor dies), and a tolerance. They aren't polarised, so orientation doesn't matter. For more detail, look up GOST 2.728-74. For audio volume control you use a variable resistor. If the resistor is built into the board, you can't measure it directly; the colour rings on through-hole resistors give you the nominal value. The standard banding is 4 rings, sometimes 5. The first two rings are the significant digits, the third is the multiplier, the fourth is the tolerance. I use the EE Helper mobile app as a reference for the markings.

Picking a resistor for a white LED:

- 5V − 3.2V = 1.8V. Subtract the LED's operating voltage from the supply.
- Required resistance: R = U/I = 1.8/0.03 = 60 Ω. We've taken 30 mA and divided it in. Pick the nearest standard value. Or reduce the current and accept a slightly dimmer LED.
- Power dissipation: P = 1.8V × 0.03 = 0.054 W.

If you're wiring several LEDs at once, do it in series, not in parallel. Parallel wiring assumes identical LEDs, but they never are — not even within the same batch. The risk is that one LED fails, the current through the next one increases, and you get a chain failure. The fix: a separate resistor per LED, or a parallel-series arrangement. That's how LED strips are made.

![Voltage and LED wiring](https://your-scorpion.ru/wp-content/uploads/2022/06/напряжение.png)

Resistor values are easy to read with an ohmmeter or a multimeter (tester, AVOmeter). Four green bands = 5.5 Ω at 0.5%; red = 110 Ω at 1%. The larger the resistance, the more current is limited and the dimmer the LED. An oscilloscope is also worth having for inspecting electrical signals, alongside a signal generator, a bench power supply, and a logic analyzer. With a multimeter, attach the black lead to ground (any piece of metal) and touch the red one to the pin. If the multimeter reads zero, you've found ground. In high-volume production, the multimeter gets replaced by a dedicated jig — purpose-built pins handle power-up, reading, and data transfer.

I have classic low-power resistors with colour codes, 220 Ω at 5%. Put a heating element next to a resistor and its parameters drift. So a "100 Ω ±5" rating reflects the permissible spread of the real value. The Home button on the iPhone 7 sits on a strain-gauge resistor — resistance changes with deformation.

### Power transmission and grounding details

Sometimes you want to deliver power gradually — dimming a lamp via a dimmer — and that's a thyristor or triac job. As for grounding, any enclosure that's giving you a shock or interfering with microphones, speakers, electrostatic emitters, or pickups needs proper grounding. Otherwise the electronics inside are at risk and the metrologists will be unhappy.

Ohm's law from school: V = IR, where V (voltage) is the potential difference between two currents, I is the current — always flowing from higher voltage to lower — and R is resistance, low values meaning the current passes through fast. More by the book: V is the voltage difference in volts, I is current in amperes, R is resistance. The equation tells you which resistor you need. The weaker the resistor, the brighter the LED, within its rated capacity — mine, like most, top out at around 20 mA. You can't use Ohm's law to compute the V-I characteristics of an incandescent bulb, because the filament's resistance changes as it heats up — hotter means more resistance. That's why first power-ups of a quadcopter often go through a light bulb.

Plug the Arduino UNO into your computer with the USB cable. You'll see the "ON" LED light up on the board and the "L" LED start blinking. That means the board is powered and the microcontroller is executing the "Blink" program you (or the factory) flashed onto it. From there:

- Windows tries to install drivers on its own and fails.
- You probably have a Chinese clone, and most online instructions won't help.
- Download the right driver. In my case, CH341. Install CH341Serial_driver — the UART one.
- Install the Arduino IDE if you haven't.

Then set everything up as in the picture and run the code:

![LED wiring](https://your-scorpion.ru/wp-content/uploads/2020/06/list_of-goods_magnes-1.png)

```c
int led = 8;

void setup() {
  pinMode(led, OUTPUT);
}

void loop() {
  digitalWrite(led, HIGH);
  delay(50);
  digitalWrite(led, LOW);
  delay(50);
}
```

![Blink result](https://your-scorpion.ru/wp-content/uploads/2020/06/Untitled-1-1.jpg)

![Blink animation](https://your-scorpion.ru/wp-content/uploads/2020/06/video_2020-06-17_22-57-17.gif)

A more interesting LED is the hw-479, which can shine independently in R, G, and B. You can use it in a COB lamp without embarrassment.

![RGB LED](https://your-scorpion.ru/wp-content/uploads/2022/08/IMG_3962.jpg)

```c
const int redPin = 8;
const int greenPin = 10;
const int bluePin = 12;

void setup() {}

void loop() {
  analogWrite(redPin, random(0, 255));
  analogWrite(greenPin, random(0, 255));
  analogWrite(bluePin, random(0, 255));
  delay(800);
}
```

[RGB LED video](https://your-scorpion.ru/wp-content/uploads/2022/08/IMG_3963-2.mp4)

## A practical example: temperature and humidity

Let's do something applied. I have a temperature and humidity sensor — the DHT11, with three pins (VCC, GND, DATA). It reads temperatures from 0–50 °C ±2 °C and humidity from 20–80% ±5%. It's pictured below: very cheap, so the actual error is much higher than the spec, especially on humidity. If you can buy a DHT22 or BC18-D20 directly, do that. For a prototype, though, the older model is fine.

A small warning about temperature sensors: they heat themselves up constantly, so for high-temperature work prefer tungsten, titanium, zirconium, or contactless variants over hard-glass ones. And heating components in general don't enjoy frequent on/off cycles. Tungsten is extremely hard to melt but absurdly dense (read: heavy) and brittle. For large structures, no; for electronics, ideal. Aircraft use nickel-based heat-resistant granulated alloys that only get stronger when heated.

![DHT11 sensor](https://your-scorpion.ru/wp-content/uploads/2020/07/Untitled-2.jpg)

Wire it as shown. Plug in over USB, run the code, and Serial Monitor (`Ctrl+Shift+M`) will show temperature and humidity tracking. Play with it — heat the air, raise the humidity; in my example the sensor reacts visibly to the changes. Or check the plotter window (`Ctrl+Shift+L`).

![DHT11 wiring and output](https://your-scorpion.ru/wp-content/uploads/2020/07/get.jpg)

```c
#include "DHT.h"
#define Type DHT11

int sensePin = 2;
DHT HT(sensePin, Type);

float humidity;
float tempC;
float tempF;
int setTime = 500;
int dt = 1000;

void setup() {
  Serial.begin(9600);
  HT.begin();
  delay(setTime);
}

void loop() {
  humidity = HT.readHumidity();
  tempC = HT.readTemperature();
  tempF = HT.readTemperature(true);

  Serial.print("Humidity: ");
  Serial.print(humidity);
  Serial.print("% Temperature ");
  Serial.print(tempC);
  Serial.print(" C ");
  Serial.print(tempF);
  Serial.println(" F ");
  delay(dt);
}
```

```
17:02:35.143 -> Humidity: 64.00% Temperature 28.00 C 82.40 F
17:02:36.161 -> Humidity: 64.00% Temperature 28.00 C 82.40 F
17:02:37.419 -> Humidity: 68.00% Temperature 28.00 C 82.40 F
17:02:38.438 -> Humidity: 68.00% Temperature 28.00 C 82.40 F
17:02:39.692 -> Humidity: 95.00% Temperature 28.00 C 82.40 F
17:02:40.711 -> Humidity: 95.00% Temperature 28.00 C 82.40 F
17:02:41.963 -> Humidity: 95.00% Temperature 29.00 C 84.20 F
17:02:42.980 -> Humidity: 95.00% Temperature 29.00 C 84.20 F
```

`Serial.begin(9600)` sets the serial communication speed to 9600 bps, which is the most common rate for TTL-connected devices. The second most common is **115200**.

A breadboard is the easy way to prototype electronics without soldering. The careful reader will notice the blue and red lines running along the length of the board — those are power and ground. Red is 5V, plus. Blue is for digital inputs and outputs, minus. The top and bottom rails aren't connected, but you can bridge them with a wire. The vertical rows are connected in two blocks. So the top blue line is connected and is minus, the red is connected and is plus, and the lower block of holes is only connected vertically. Breadboard contacts aren't very reliable, though, and almost certainly aren't suitable for production.

### Sensors in production: a small detour

Sensors aren't only what you stick on a breadboard. To see what a mature sensor stack looks like, consider fuel injection in a car. To start, the engine needs petrol, oxygen, and an ignition source. Oxygen gets sucked in by vacuum. Delivering the right amount of fuel used to be a carburetor's job: fuel entered through Pitot/Venturi tubes into the carburetor and was distributed across the cylinders. Pressure difference pushed fuel into the engine — more push, more fuel, faster car. You may remember old cars where you had to pump the pedal a few times to get the fuel going. That's a leftover from how combustion really works.

Modern cars use electronic fuel injection. Same idea as above, but electronically and far more precisely — you can dose each cylinder individually. Like most electronics, an injector has two wires: power and ground, and the computer controls power delivery. Around that is a sensor stack. Fuel has to be delivered at the right moment in the intake cycle, so the crankshaft has a camshaft position sensor that tells you when the rotation cycle begins. The optimal oxygen-to-fuel ratio is 14.7:1 — every gram of fuel needs 14.7g of air to burn completely. To monitor that, you use a mass air-flow sensor or a manifold absolute-pressure sensor. The first measures temperature difference to figure out the air properties; the second, pressure difference.

## A larger display: the 1602a

Let's grab another expansion module — the 1602a, a large text display that can't show Cyrillic (the English alphabet is hardcoded into the controller). The characters live in ROM and don't need power to stay there. *1602* means 16 characters across two rows. There are many variants of the 1602, mostly built on the older HD44780U controller, with varying screen quality and brightness. Alongside the display itself you need a 1602 LCD to IIC/I2C converter. Make sure the LiquidCrystal library is installed. The first attempts almost always look like this — solid squares. The usual culprits: bad contact or wrong wiring (wrong pins or swapped order).

![1602a issues](https://your-scorpion.ru/wp-content/uploads/2020/07/646464.jpg)

To adjust screen brightness, turn the blue screw on the module — that varies the voltage and thus the contrast. The jumper on the left controls the backlight. GND to minus, VCC to 5V, SDA to A4, SCL to A5.

```c
#include <Wire.h>
#include <LiquidCrystal_I2C.h>

LiquidCrystal_I2C lcd(0x20, 16, 2); // address and dimensions

void setup() {
  lcd.init();
  lcd.backlight();
  lcd.print("Hello, world!");
}

void loop() {}
```

Simple as that — "Hello, world!" on the screen.

## A sound sensor

Next, a sound sensor. I'm using the KY-037 with a Bochen 3296 guosheng potentiometer, which measures sound volume. It's the awkward 4-pin variant: pin 4 digital out, pin 3 +5V, pin 2 ground, pin 1 analog out — and you need a resistor. Wire it as in the photo and run:

```c
void setup() {
  Serial.begin(9600);
}

void loop() {
  Serial.println(analogRead(A0));
  delay(100);
}
```

![Sound sensor setup](https://your-scorpion.ru/wp-content/uploads/2020/07/tutu.jpg)

```
19:29:44.651 -> 323
19:29:44.753 -> 67
19:29:44.854 -> 838
19:29:44.955 -> 254
19:29:45.057 -> 125
19:29:45.159 -> 68
19:29:45.261 -> 72
19:29:45.363 -> 51
19:29:45.465 -> 892
```

The sensor responds to volume. Sensitivity is adjustable with the small screw on the potentiometer.

It might all look simple. Then look at the schematic of a SIM card — also very simple.

![SIM card schematic](https://your-scorpion.ru/wp-content/uploads/2022/07/Asset-1@2x.png)

## TouchDesigner

For anything more involved you'll need Firmata or some C++. I prefer Firmata + TouchDesigner. Firmata lets you work visually with a lot of sensors at once, and TouchDesigner generates interactive installations. We'll do a Blink-style example but with *Examples → Firmata → StandardFirmata* flashed onto the board. That's what's going to let us talk to TouchDesigner and VVVV.

![TouchDesigner](https://your-scorpion.ru/wp-content/uploads/2020/07/75757.png)

TouchDesigner organises nodes into categories: TOP for graphics, CHOP for math and audio, SOP for geometry generation, MAT for materials, DAT for tables. Hook up a joystick, or any other input device, and wire the nodes together. Pick the right port, turn on all the switches, and click all the pulse buttons. You'll see jumping numbers — analog ports always carry noise. The fix is a `lag` node where you can tune the smoothing values. You can also add an op-amp to amplify the analog signal.

![TouchDesigner with input](https://your-scorpion.ru/wp-content/uploads/2020/07/ezgif-2-a4282762f6bf.gif)

The example above shows two nodes — one for analog and one for digital pins. From here you can map any other parameter to the joystick — sound volume, a drum kit — and use it to drive playback of a video, its speed, its volume. Very handy for installations.

Comparing analog and digital ports: a digital port is a one-shot "jump" between 0 and 5V, a True/False switch. An analog port also runs between 0 and 5V, but uses the full range. For a light sensor, complete darkness reads 0, brightest light reads 5. Analog-to-digital converters (ADCs) translate analog voltages into a format the rest of the system can work with. The Arduino Uno uses a 10-bit ADC delivering up to 2^10 = 1024 different values. So 5V returns 1023, 0V returns 0, 2.5V returns 512.

This is the territory of computational complexity theory: per Turing, you solve these problems by enumerating all variants. Boolean algebra, where every value takes one of two states, true or false — 1 && 0 = 0, 1 || 0 = 1. The first machines used vacuum tubes, everything manual: a diode either passes current or it doesn't, and out come zeroes and ones. Then came transistors, where impurity atoms are placed in a crystal lattice and conduct current. Analog and digital circuits both run on transistors, and the first punched-card programs were already running through transistors.

## A note on high-voltage in everyday objects

A tangent worth a sentence. If a household gas stove's igniter button breaks, you don't strictly need to fix it — a piezoelectric lighter delivers 20kV and is surprisingly useful around the house. Same idea as the ignition systems in the cars above: high-voltage spark in a controlled package. An ebonite rod or just a box of matches works too.

## Going further

If you have a curious mind, look at the `maxuino` library inside Max/MSP/Jitter — another tool for building visually striking installations. Or Processing, which is about writing code in its own [editor](https://processing.org/).

---

A small piece of Arduino is enough to get you from idea to working object. Plan for headaches with cheap clones and the matching drivers — that's the price of starting at the bottom of the stack. Once it works, the same techniques scale all the way up to STM32, to ESP, and to the boards quietly running everything from toothbrushes to warehouse robots.
