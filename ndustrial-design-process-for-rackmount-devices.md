---
title: "The Industrial Design Process for Rackmount Devices"
author: "Max Tsvetkov"
date: "2018-08-01"
source: "https://your-scorpion.ru/rackmount-industrial-design/"
---

# The Industrial Design Process for Rackmount Devices

Industrial design isn't always about couches, spoons, chairs, and consumer electronics. Rackmount devices need it too, and there's almost nothing written publicly about that side of the discipline. Large studios publish a lot of industrial-design process work, but very few describe the problems specialists actually run into when building even a small metal box with two buttons and ten connectors. This article is about the routine tasks and problems an industrial designer runs into.

These days I'm mostly involved in designs for either desktop devices (the kind that sit on a desk) or rackmount devices (the kind that bolt into a 19″ rack). I've also worked on a button-based mobile phone, an STB and its remote, and several full hardware-software systems. Five years ago, the question was just whether the device worked — form followed function. Now the market is saturated, and form-factor matters. Aesthetics moves alongside functionality, not behind it.

Worth saying upfront: industrial design is stricter than web design. In web, a 1px deviation from the mockup is nothing; in industrial design, a 1mm deviation from the drawing is a major defect. You can ship an MVP web service in a week; an industrial sample from idea to server rack is six months. A rackmount device has an average lifecycle of about six years — comparable to a server. Not the 30–40 years a car can run, but a lot longer than the one-or-two-year life of a website's design.

---

## Analysis

The typical task is designing a device that has competitors. So the work begins by analysing comparable enclosure designs and the standards they have to meet. You also pick the materials for the enclosure parts. The brief will just say "metal"; in practice the choice usually comes down to aluminium or steel. Plain iron rusts easily, is soft, and is heavy — but you can improve it with aluminium, silicon, manganese, and phosphorus, plus galvanising and electroplating. If conditions allow, D16T (an aluminium alloy) is a well-established choice for the right balance of strength and weight. Don't trust it under threaded loads, but for enclosure parts it's fine.

So the opening questions to the client are always:

- What is the enclosure for?
- What's the first-year production run?
- How important is the visual design?
- What conditions will the device operate in?
- Who are the competitors?

Competitor analysis here isn't a market exercise — it's technical. The job is to figure out whether you can build a prototype on a dev kit or an evaluation kit, and to assess the others' work from an engineering perspective. With budget, you can buy comparable devices and tear them down. Without budget, there are plenty of resources online where people have already done the teardowns and laid out the internals.

If we're talking purely physical mechanics with no extreme thermal requirements, plastic can replace metal — Teflon, PTFE, caprolon, glass-filled polyamide, PVC-U, PP-H, PPN. At the design stage you produce a stack of rough sketches and explore ideas for how the cover opens, which is just standard design thinking. Drawing dynamic, Karim Rashid-style forms is pointless — this is equipment for server cabinets. What you're delivering is 1U or 2U. A 1U-height enclosure that fits a telecommunications 19″ rack.

### When materials carry the brief

Material choice is often the single most consequential decision at this stage, so it's worth knowing the landscape. For aesthetics on metal, you can move from D16T to a custom aluminium alloy like AK12och or GALSI13. You'll occasionally encounter magnesium-alloy enclosures (AZ91) — magnesium is exceptionally light, which is why it's used for printing clichés. Polycarbonate, fibreglass, and ABS plastic don't corrode at all, which sometimes makes them the obvious choice. I usually reach for plain old polycarbonate — heat-resistant, fire-retardant, light, and strong. Good for mosaic mimic panels. I've even used solid polycarbonate as a replacement for protective glass (with replacement after scratches and clouding sets in). There's also laminated triplex glass when budget allows.

On corrosion: if the enclosure is painted steel, stainless, or aluminium, you can't avoid it — you can only slow it down with anti-corrosion treatment. Whether you need that depends on environment. Heat and humidity together accelerate corrosion fast. The desert is comparatively slow because there's no moisture, despite the heat. Corrosion doesn't just hurt looks — it also degrades the declared IP rating.

The cross-industry view helps. Watches with sapphire glass, double-sided anti-reflective coating, a 316L steel case and bracelet, 100M/10 ATM water resistance, and chronograph functions run around $400 retail. Replace the dial face with dinosaur bone, and the price climbs. Small devices like Swiss watches have an enormous material range — from carbon composites and low-grade stainless to 316L or 904L steel (Rolex), titanium, sapphire, and glass with anti-reflective coating, armoured film, plexiglass, or crystal. Apple Watch is aluminium 7000, an aerospace-grade material. The iPhone 6 was aluminium 6000. Galaxy S9 is 7003. The iPhone X has stainless steel edges that can be polished back to original quality. The first digit of an alloy number is the series — aluminium foil is 1050 or 1100, beverage cans are 3004 (aluminium plus manganese), 6000 is aluminium with silicon and magnesium, 7000 is the zinc blend. Submarine crankshafts are 50G steel. 316L stainless and AlSi10Mg aluminium alloy can even be 3D-printed, but 904L is machined by hand. Large industrial parts with 40Kh steel, 28-32 HRC heat treatment, and nitriding aren't 3D-printable at all. Same for the duralumin used on lunar landers. All of this affects price — handwork is always more expensive. The opposite extreme also exists: extremely simple no-name steel, often made from scrap, with inconsistent thickness and random springiness. Cost of steel is often allocated as a risk line in the estimate because it keeps rising.

If the device will live in an environment that wants armour rather than aesthetics, you handle that by hiding the protection layer. Armour usually sits under the skin as a flat steel sheet. Steel is strong — it's what ship hulls, icebreakers, submarine hulls, and stamping moulds are made of. You can design an interesting curved outer shell and put a roughly geometric box of thick armour plates underneath, with proper angles and load distribution. That's passive armour, not active. Steel is the right choice for large blocks like a vehicle roof. Composites of steel and aluminium, or Kevlar, give you stronger material; carbon fibre if you have the budget. Fibreglass holds up well against blasts — good for vehicle floor reinforcement. If weight matters, Kevlar plus ceramic gives you something equivalent to steel at much less mass. Here, for reference, are the tempering colours that steel takes on at different heat-treatment temperatures — the leftmost square is untempered:

![Steel tempering colours](http://your-scorpion.ru/wp-content/uploads/2019/08/Untitled-1.png)

If you temper using a specialist polymer rather than ordinary oil, you can push the strength further.

If the device is more complex, engineers usually work out the mechanical assemblies, mechanics, and functional implementation first, and only then the designer adds aesthetic shape and UX. All of that is the R&D stage, four to eight weeks in total. The general process for any part: brief → design → engineering + physical design (synthesis and topology) → documentation → prototype → test batch → series production. There are sub-steps too: RTL design, MBIST, FPGA, PCB.

### Certification and the off-the-shelf chassis question

Worked-up concepts get shown to the client. One direction emerges for further development, or several — both are normal. Engineers come in at this point, because designing your own enclosure requires certification under the Customs Union Technical Regulations (EAEU) for electromagnetic compatibility and 220V safety. When you order a ready-made chassis, the manufacturer handles certification for the chassis itself, but not always — EMC and electrical-safety certification applies to the whole product, not just the case. Industrial-class EMC under IEC 61000-6-5:2001. The enclosure itself isn't an interference source, since external interference doesn't affect its functional qualities. Same for electrical safety. There are RF labs available to investigate electromagnetic interference levels.

Off-the-shelf chassis come with limitations. Always over-provision power supplies and fans — they fail constantly. And size the chassis for seven years out. The more ports, the more devices you can connect. And in the modern world, Power over Ethernet (PoE) is essentially mandatory for cameras, IP phones, Wi-Fi access points.

For network equipment, an off-the-shelf chassis is genuinely good — especially if the network isn't very distributed, a few racks. For equivalent port density, the chassis is cheaper, and it'll last up to ten years. You can fit different line cards for port configuration; usually 2-4-8-10-12 expansion slots.

The electronics need separate certification, before production starts. Basic electronics need an electrical-safety certificate. Add Wi-Fi and you also need RF safety (FCC). You can delegate this task to manufacturing in China, but it's a paid service.

It's perfectly normal to settle on an off-the-shelf case with minor reworking of the front, rear, and internals. Many companies design their boards to fit standard production cases. The market offers plenty of standard electrical enclosures rated IP65 or higher, with intrinsic safety, explosion safety, and surface-charge accumulation already accounted for. In that case, the designer's job is reduced to UX ergonomics and how the device behaves in its environment, plus colour-accent placement. That's still hard work, tied to function. Silver always shows every flaw on a case. Pale colours, "white night" shades — there are many beautiful options to work with. Although guidelines often mandate black, white, grey, green, or blue, which cover 90% of preferences. Or you go fully custom with an aluminium-alloy case.

For all of this simplicity, we owe a thank-you to Alan Turing — he's the one who laid down the idea that the same chip can sit in a computer or a washing machine.

## Rough 3D model and approval of the proposed layout

You'd be surprised how much you can do with a cardboard box, hot glue, and masking tape. We build physical mock-ups at real scale and start working through the construction and details. In a healthy process you establish the design language at this point — what elements will give the device its character. Plus sketches. A lot of sketches.

The internal layout has to accommodate boards, power supplies, and so on, along with construction requirements: how the case opens (slide-and-lift), where everything sits.

Active cooling design begins here, and it's one of the most important pieces of work. Fans, free-flow cooling, heat sinks, hot-swap fans and power supplies. Or — abruptly — requirements may land for fanless design instead. Rackmount conventions call for two power supplies; if one drops consistently below the required level, the system switches to the other, with an indicator showing which supplies are alive. The ambient operating range can be −40 °C to +85 °C, with snow load and other industrial-equipment delights, and the device has to cope with all of them. The connector for a backup supply must be a dedicated port — and definitely not USB (you'll get hit for that in industrial). Every junior eventually hits the case where bad cables or bad batteries hung the system and the team spent a week chasing the bug in software. In the simplest case, power runs through an Ethernet cable (PoE) — the 2009 802.3at standard provides 25.5W DC.

The 3D model should immediately make the cooling type obvious — front-to-back, for instance, with cold air drawn in the front and hot air ejected at the back. The brief often specifies this; it tends to depend on the layout of the customer's server room. Alternatively, the fan pulls in from the port side and blows out the back; this affects where the device lives in the rack. Cooling capacity in a brief is almost always less than what's needed in practice, because lasers like things cool. They get an additional air duct. I also like adding overlap baffles made of springy material with vertical slits at the bottom — a kind of fringe — that lets cables route however you want while still blocking airflow. Occasionally someone tries to slap a piece of cardboard in front of the cold-air stream, by analogy with a car radiator. Don't do that.

For magnets — say, to hold a handle to a board — I always keep neodymium on hand. Ferrite gives less holding force and demagnetises faster.

On the other hand, if the device is going to sit in an executive office, the power supply needs to be quiet, so we use passive cooling. That opens the door to injection-moulded plastic or 3D-printed enclosures, and life gets simpler. Injection moulding is very cheap in production but the mould itself can cost hundreds of thousands of roubles. Silicone moulds are an option for low-budget plastic or rubber.

![3D-printed prototype](https://your-scorpion.ru/wp-content/uploads/2021/12/3d.jpg)

Any product can meet two out of three criteria: performant, cheap, reliable. If we want a metal case at minimal budget, the material's going to be sheet metal.

At this stage, an important question: does the front panel get an LCD? Just a status display for the current switch state, or a full touchscreen replacing the physical buttons? The second drastically complicates the brief, but lets you replace mechanical buttons with software ones. That's cheaper and easier to implement. The choice drives mechanical button and switch placement, default-state planning, and approximate mass. And maybe you need a microphone? You picked ribbon — and it's sagging.

The work happens in Alias, Rhino, Siemens NX, or SolidWorks. Not 3ds Max, Maya, or Cinema. As a last resort, for form work, AutodeskSpeedForm and Autodesk Surface can carry you — especially when your predecessor didn't read this article and handed you the work in 3ds Max. Alias and Rhino lean more conceptual and design-oriented; SolidWorks is a full CAD system. You might also run into Altium Designer and Creo. After each iteration you produce a draft model and re-present visualisations in their real environment. Renders show material gloss and colour. The reality of the software burden may surprise the novice expert: aerodynamic calculations, mechanical and thermal load analysis, magnetic fields, electrical automation. So the stack ends up looking like SolidWorks + Flow Simulation + Simulation + Motion. Or ANSYS with the SigmaFlow CFD packages. And if money is no object — CATIA with modules, Alias and Icem Surf.

![CAD workflow](https://your-scorpion.ru/wp-content/uploads/2045/03/answer.png)

The design phase breaks down as: sketches → detailed sketches → 3D → visualisation → 3D printing.

Some details have outsized impact on how the device feels. When a phone vibrates, a small motor spins an off-centre weight. You can only really test that effect by sticking the motor in and feeling it for yourself.

## Detailed work

Build a mood board. Work the colour, pick material shades (silicone, plastic). At this point you should be ready to deliver finished CAD models. Detail the position not just of the Power button but also of Factory Reset, which is usually board-mounted.

You don't have to build the product yourself, but you have to understand how it'll be built. The same way a web designer has to know HTML/CSS/JS, an industrial designer needs hands-on experience with:

- A three-segment sheet-metal brake
- CNC bending
- Milling
- Laser cutting (case prototyping; ideally diamond turning, 5–10 micron tolerance)
- A press (with tooling)
- Hand welding clamps
- A multi-tool of some kind
- A drill press

The survival kit:

1. Socket set with ratchet and bits
2. Tap and die set
3. Bench vice and a full set of wrenches
4. Rotary fixtures
5. Air compressor
6. Torque screwdriver
7. Digital callipers
8. Label tape for marking the device
9. Loupe, flux gel, tweezers, side cutters, glass-epoxy laminate

You'll need all of that for prototyping, but the prototype gets built by a whole team — not just you. A welder, two metalworkers, an electrical assembler, a PLC programmer. With luck you'll get a lot done on a 3D printer. Without luck, welcome to the world of expanded polystyrene. A design engineer will help you translate the idea into engineering terms.

![Prototype animation](https://your-scorpion.ru/wp-content/uploads/2045/03/Tsvetkov_animation_-1.gif)

Why so much tooling? If a requirement says the removable part of the case has to require sliding force after fasteners are removed, before it can lift off, you can't prototype that in clay or LEGO — only heavy materials work. For exterior connections of case parts (excluding the cover), you can use permanent or anti-tamper fasteners and try to break them. Micro-switch throw shouldn't allow any gap to form before the tamper-detection signal triggers — always fun to test. When prototyping sliding case sections, the removable sliding part has to slide under retaining lips so you can't slip a flat object under the cover.

For mass production of a cast iron part, for example, the foundry process looks like:

1. Drawings for the casting tooling.
2. Building the form (casting tooling), on something like a Sinto FBO-V.
3. Charge stage — unloading material, GOST compliance checks.
4. Melt.
5. Mould production on a moulding machine.
6. Pouring metal into the mould.
7. Cleaning and verification against drawings.

Slow and complex. So for verifying the design and assembly experience, 3D printing or milling is far more practical. For 3D printing, the cheapest plastic-filament FDM is enough. If the budget allows, SLA or SLS prints can even go to clients.

For 3D printing in general — PLA is fine for kids' toys. Kitchen appliance parts want PETG or, if you're brave, nylon. You can save on adhesive by mixing your own at home: pharmacy-grade enterodes or PVP K-90 (povidone), dissolved in isopropanol or vodka, roughly 200 mL liquid to 5g powder. For nylon, use BF-2 adhesive.

If the prototype will see actual use, you're looking at milling and polyurethane casting. Milled plastic or metal is almost a final prototype with final-product quality. Improvisation works too — need to join two parts? Take any piece of iron and weld it. Five-minute job. Final dimensions should be locked in by this stage. They can differ from the original idea — speakers, for instance, have an acoustic volume that prevents the device from being smaller than a certain envelope. Flexible parts can be cast in silicone.

### Prototyping cost calibration

It's worth grounding the cost numbers, because clients ask. For an ABS plastic case part at roughly 200×120×90 mm, a milled prototype runs around $350. Producing 25 units, including tooling, comes in around $50 per unit. Larger and more complex shapes — say a motor cover at 350×410×120 mm in single-piece quantities — go higher. The part is calling out to be cast, so milling a few individually is wasteful. As a rule of thumb, around 13,000–15,000 roubles per piece. If the geometry is complex you can FDM print and finish with hand tools, or upload to a service like [weerg](http://www.weerg.com) for a real quote. But finishing operations stack up — milling, tumbling, anodising, boring out seats for seals and bearings — and the price can triple. Top-quality results are also achievable on resin-based 3D printers. Material choice swings the cost too; my baselines come from 7075 (V95) and 6082 (D16T).

### Going larger

Prototyping at car scale is a different planet. Most problems get solved at the CAD stage. If you discover a defect in CAD and the prototype needs a watertight enclosure added to a part, that means a new mould, a new part (with slides, hundreds of thousands of dollars for complex parts), and a test cycle that surfaces bugs (condensation collecting on the rubber-metal mount, accelerating wear), tolerance issues at ±0.01 mm / +0.02 mm, mould remakes, more bugs, more remakes. Then you discover that on the assembly line the robot can't reach the new part, so you redesign neighbouring parts because you can't edit theirs — their hardness is 58 HRC. Then testing reveals that the noise and vibration insulation has shifted out of spec, and you either add more material (driving up unit cost — a $10 increase on a million-unit production run is a $10 million hit) or you go back to step one and redo the mould. On top of that there's a much heavier compute load: predicting crack growth at the crystal lattice level, modelling creep and low-cycle fatigue accumulation. Size combined with operating conditions: run a car in mineral and chalky water with a budget primer enamel (Prodecor 1202, the cheap option), and after seven years it'll rot anyway.

For comparison with the consumer end of the spectrum, the workflow for Apple's official cases is reportedly: a very rigid 0.6 mm polycarbonate base (not TPU) gets cast, gets coated with an adhesive chemical, then coated with silicone under 1 mm thick. The area around the buttons is cleaned and re-poured. Many cycles of cleaning and manual work follow, then many layers of paint (each layer adds cost), then microfibre is glued to the inside on proprietary machinery. Cherry on top: the logo gets laser-etched.

## Thermal modelling

Your two main problems are heat and uninterrupted power. Every other decision dances around those. The pain: a case with vent holes gives better thermal dissipation from components at normal ambient temperatures, which matters when you're operating near the limit of the spec. A reasonable solution is covering the holes with a fine mesh — that prevents access (via wires, etc.) to motherboard components, their leads, and any debug headers. Test the processor under maximum load to validate the chassis's heat dissipation. Especially if it's an FPGA. If the processor is something like an Allwinner R18, Amlogic S905, or Allwinner A64, you got lucky. It could also be a cheap RISC-V — 64-bit and architecturally more interesting than ARM, but requires careful testing because of its newness. Chinese vendors are already shipping RISC-V even in laptops.

For the heat calculation itself, you start with a rough estimate, mental arithmetic if the case is milled — not too hard. Then proper analysis in a CAD system like ANSYS, done by people who specialise in it.

Decide whether to make blanks for unused expansion slots, which walls get perforated. If there's any chance of water ingress, you need protection from water and foreign objects, IP30 compliance per GOST 14254-96.

![Sealing and airflow](https://your-scorpion.ru/wp-content/uploads/2045/03/ezgif-4-42e86c1a79.gif)

## 3D-model revisions

What kind of graphical interface might be needed; work on the display graphics. Minimise the number of removable case parts that have externally accessible fasteners. Ideally there are just two parts: the base and the cover. Dieter Rams: "Good design is as little design as possible." Each removable case section needs to be held by at least two micro-switches connected in series in a ring. Triggering any of them sends a tamper signal. Fewer case parts = fewer micro-switches.

This also lets you eliminate openings that would allow direct-probe access to internal components. Once the case is settled and the team is happy with the prototype, you place an order at the factory for test units (5–10 pieces). Marked EVT — meaning identical to the final product in both appearance and functionality.

On screens: if there's a user interface, dark themes for industrial systems are basically not done. Greys are standard, not blacks. Back in the CRT days, white burned the phosphor and dark interfaces were required; CRTs are long gone, but the habit survives — and grey is the compromise, easy on the eyes at high monitor brightness. Siemens and Emerson publish colour guidelines. The FSK EES standard 56947007-25.040.70.101-2011, section 3.3.2.2, mandates the background colours for mnemonic schemes: black for control room software, grey for substation SCADA. These aren't preferences, they're enterprise-standard requirements that vary by industry and vendor. Worth reading: ISO 9241 ("Ergonomics of human-system interaction"), ISO 14915 ("Software ergonomics for multimedia user interfaces"), the GOST general ergonomic requirements, GOST R 52872-2012, W3C WCAG 2.0, Section 508, and optionally ADA.

### Finishing

At this point you can play with paint. Since the case is metal, we apply enamel. Enamel is a fully vapour-impermeable coating containing alkali-metal oxides, quartz, colour pigments, and anti-corrosion pigments for extra metal protection. These aren't interior decoration enamels. Polyurethane enamel, for example, is used on iron tools so people in the north don't get their hands stuck to them. Reference points: car paint colour palettes.

![Enamel finish](https://your-scorpion.ru/wp-content/uploads/2018/08/lksfskl.jpg)

A genuinely beautiful technique for metal cases is electroplating. Works on objects of any scale, from the very large to the very small. The plating can be copper, zinc, nickel, brass, gold, or silver. Plenty of room to play here; each type adds different functional properties.

Manufacturer execution of the case design tends to limp on both legs. If drawings specify a 1mm corner radius, production might deliver radii over 2 mm, and the edges will stand out. The most common defect is uneven joins on covers and crooked connector mounting; if the drawing allows 0.2-0.4 mm gap tolerance, production may deliver 3 mm. Cavities and dents are also routine. Your job is to receive the EVT sample (Engineering Verification Test) and inspect it carefully.

![Test pattern](https://your-scorpion.ru/wp-content/uploads/2045/03/filmakr-lut-template.png)

The main task with factory test units is tracking cosmetic defects that don't affect functionality. Beyond the case itself, the boards are worth a look too. The substrate should give the PCB good rigidity. Components can sit crooked, even ones that go into holes or sockets. Boards can have flux residue. There's an IPC-A-610-E standard for flux cleaning that says visible residue from removed flux, or residue of any active flux, is a defect for all three classes (1, 2, 3).

For board reliability, if you want better connections between boards, use collet (split-pin) connectors — they give reliable long-term contact in a separable connection. Boards are also typically coated with lacquer and special dielectric grease, because under extreme conditions you'll get humidity, oxidation, and even crystalline growths.

![Board inspection](https://your-scorpion.ru/wp-content/uploads/2045/03/IMG_7047.jpg)

One important thing to evaluate: how easy is the device to repair? Sloppy work with thread-locking fluid leads to it ending up on components and surfaces near the screws. That makes disassembly harder (degrading repairability) and may also damage components, depending on the locker's chemistry. It's also worth understanding how the epoxy reacts with flux and water — typically badly. Epoxy plus water equals an explosion. Sometimes hot glue is the better choice — Chinese manufacturers will use it alongside Sugru routinely.

For service-friendly cases that don't need full disassembly to swap components, look at the Thermaltake P3/P5 series and the View 28. They're a genuinely good solution for testing environments where you're constantly changing internals.

For gluing plastic parts, *plastic* is a category, not a material. Adhesive packaging usually states which plastic it's formulated for; what you really want is a substrate-glue compatibility chart. Joints that will flex want contact adhesive; static joints want a reactive adhesive or a liquid solvent-based one (not water-based). Glue plastic in a well-ventilated space at the temperature stated on the label. Gloves, goggles, respirator — these adhesives aren't safe. For large surface areas, use structural adhesives chosen against the load type. ACRID FS22 works well for large-area bonding of acrylic, steel, aluminium, even plastic-to-glass. For PVC, either home-brew dichloroethane with shavings of the material itself, or Quilosa SINTEX H44 or Cosmofen Plus (which is purpose-built for window frames). For PVC pipes, Bailey L-6023, and "PVC patch glue" for inflatables. For ABS plastic, cyanoacrylate or a universal reactive adhesive — CUDO bonds in 10 seconds, Cosmoplast 500 gives a little more working time. Griffon B-25 actually dissolves the surface and is dangerous to the eyes. Loctite 406 and SikaFast-555 L05 work well in automotive contexts. ABS is brittle, so its joints are brittle too; reinforce them with glass fibre or fuse in wire. Polyurethane uses Uran (I sometimes repair shoes with it). For polyethylene, YTAN Classic Fix, Temonten (leaves a permanent tacky film), or 3M 90 Scotch-Weld aerosol.

Repairability and serviceability are strongly tied to cable routing inside the case. The 3D layout gets an augmented model of the connecting cables. You work out positions for the tamper-detection micro-switches and connector blanking plugs.

### Components and edge-case requirements

Even simple decisions like battery choice have hidden traps. Because operating conditions are extreme, consumer-grade batteries capped at **+60 °C** are out. Get familiar with industrial-grade: **+70 °C**, **+80 °C**, **+85 °C**, **+125 °C**. Battery replacement should be possible without opening the case, which means designing a battery hatch. After all that, you get a DVT sample — practically identical to the production unit. Don't forget the software side. Any industrial PLC ships with a fault-state map for emergencies — any failure should trigger safe-level signals at the outputs. For extreme-condition computation, finite-element analysis in OpenFOAM.

It's hard to invent conditions more extreme than the Arctic and space. At minimum, electronics have to deal with the radiation belts at the magnetic equator. Some charged particles dump out at the poles, where the magnetic field lines converge — that's what causes the aurora borealis. Beautiful, but it's cosmic radiation. Charged particles attack the poles, so aircraft don't fly through those latitudes much. On the ground the atmosphere protects us, but at 10 km altitude you start accumulating significant radiation dose — something pilots track. Higher means more. For electronics, that means more shielding.

Another common issue is salt-spray fog. The case needs chemical conversion treatment and testing per the military standards MIL-STD-810 and ASTM B117 / D610.

### Antennas and connectivity

If the brief includes an antenna, this is when you figure out whether it reaches the destination — and where the destination is. An antenna is a resonant circuit. In the most familiar case, the destination is in space. Signal transmission to space requires a giant hemispherical antenna pointed up; it's directional and reaches far, but you can't transmit more than the signal's energy. To extend range, you concentrate the energy — the narrower the emitted signal, the higher the antenna's gain. The weakest antennas radiate 360°; parabolic is much stronger; the strongest is Yagi. You see Yagi antennas on government buildings; they can warm your wet clothes with their signal. They can be horizontally or vertically polarised. And for the grown-ups: over-the-horizon radar. The more obstacles on the signal's path, the lower the chance it arrives — solvable by raising frequency. The signal travels from transmitter (an MQTT server, or something older on OPC) to receiver through the Fresnel zone, meaning the wave bends point-to-point instead of going in a straight line.

![Antenna installation](https://your-scorpion.ru/wp-content/uploads/2023/09/f5e58b41ae0344ab37f13973861f9359.jpg)

For IoT-class industrial devices the connectivity question gets more interesting. Commercial IoT devices typically have narrow bandwidth and low-repetition data collection (cameras being the exception). Most run on LPWAN, which sends small amounts of data over long distances. General rule: the lower the bit rate, the further you can transmit. That kind of modulation lets you get data from a basement to a base station through kilometres and many walls. The lower the frequency, the further: 5.8 GHz is Wi-Fi, 868 MHz and 433 MHz are simple radio. You can also use cellular M2M — GSM, 3G+, H+, 4G — fast and high-coverage but not cheap; fine for personal devices, often not for commercial. Inside a single building, a local network of any flavour works (Wi-Fi, ZigBee, Bluetooth), but again you have to feed it power, which costs. Or LPWAN with the LoRa protocol, and live with the low data rate. Or budget NB-IoT with high latency, or LTE-M, which is brisk enough for audio and video.

On the platform side, the giants are IBM Watson IoT, AWS, Google Core IoT, Microsoft Azure IoT Hub, and ThingsBoard. There's little real difference between them — what varies is the ecosystem and the billing model. Some charge per device, some for traffic, some for data processing. The questions worth answering before picking a platform: vendor track record, flexibility in adapting to new market and technology realities, deployed projects and outcomes, alignment of vendor roadmap with yours, vendor survival prospects, partner-channel ecosystem, openness and interoperability, supported cloud platforms and languages, time to onboard new devices, ease of integration with third-party device-management systems, fit for your use cases, scalability, and security model.

## Workstation choices on the shop floor

A small tangent worth including here, because it comes up often. For shop-floor compute — not the rackmount device, but the workstation that runs it — the answer depends on environment. There are dedicated industrial PCs for workshops, and they're expensive. If the workshop has stable ambient temperature (around +15 °C to +26 °C year-round), you can get away with stick PCs — compact enough to hide in a dust-protected enclosure with a cooling fan that handles particulate. The monitor gets plexiglass over it because shop dust is unforgiving. The monitor itself wants something in the IDS-3210 class. You can also build your own miniITX PC. For cooling, EKWB is my reach — minimum EK-Furious Vardar EVO FF5-120 fans, or another low-profile 60 mm cooler. If it fits, the JONSBO SHADOW TW4-360 Color is the move.

## Packaging design

Final approval of markings and nameplates with product name, designation, serial number, and manufacturing date. Packaging marking follows GOST 30668-2000. The packaging itself is usually single-use, individual, and large — miniaturisation belongs more to IoT/IIoT territory. There's a GOST for that: 23088-80. The packaging includes a label sealed in a polyethylene-film bag.

The main thing to consider when designing packaging is transport and storage. For industrial network equipment, that's "C" conditions per GOST 23216-78. Transport happens in heated, sealed compartments — doesn't matter whether it's a truck with pneumatic suspension, a train, or a plane. Storage happens in warehouse spaces in original packaging at ambient −40 to +60 °C and humidity up to 95% (no condensation, no frost).

Marking isn't a heavy workload — generally you just verify that every connector and indicator on the device has the correct labelling. It's worth complying with GOST 18620-86 (Electrical products: marking) and remembering the orientation-and-handling symbols from GOST 14192-96.

There will also be a lot of mistakes from the printers — far more than at standard print shops. Errors at the level of switching the logo from colour to black-and-white. Hitting the right colour is not even up for discussion. Orders will almost certainly get split across multiple printers, so colour proofs and reference samples sent to each printer are highly advisable.

After all this, you calculate the final MSRP.

## Preparing the data package for production

This stage is PVT — pilot batch. For oversight, you typically have to show up at the factory in person, even though the factory would rather work without intermediaries. Doesn't matter if the factory is in Russia, China, or Europe — they all mess up. The factory wants a .step or .iges file, the production run size, the requirements; after negotiation you wait for a master model and the production tooling.

When test units from the factory are verified, it's time to place the full production order with the contractor. There will be plenty of engineering tasks — flashing BIOS, loading software — but by then you'll be off writing the user manual, which isn't really industrial design territory.

A note on documentation discipline. When working in CAD systems, you follow ESKD (the unified system for design documentation). Drawings then go to production, to a technologist in the better case, along with the STP model. I used to use Kompas and T-Flex for this. But if you're working with garage-scale fabrication on simple parts, strict ESKD compliance can do more harm than good.

---

Could an engineer do all of this instead of a designer? Yes. But the result will be what you get when a backend developer builds a website on Bootstrap without a designer. It works, but the UX is bad. Similarly, an engineer can run through IPC-A-610E and ship a product that creates many problems for everyone downstream of them in the business chain. An industrial designer on a rackmount device finds the balance between all the requirements and the comfort of working with the device. And occasionally introduces something genuinely new.
