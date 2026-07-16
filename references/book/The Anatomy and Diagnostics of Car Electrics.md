# The Anatomy and Diagnostics of Car Electrics
### From Ohm's law to fuses, wiring, and the side channels an ECU can leak through

*Maksim Tcvetkov · 8 June 2024*

## What electricity actually is

Electricity is electrons flying through a conductor — usually a cable, though electrons can travel through something as simple as salt water too. The easiest way to picture the flow of electricity is by analogy with water in a hose: imagine a hose with water running through it under pressure. That pressure is voltage (V). Amps are the volume of water, and resistance is a narrow section partway down the hose, keeping the flow from exceeding what's expected. These three properties are what Ohm's law captures. To find voltage (V), multiply resistance by amps — less resistance means more amps. Ohm's law doesn't account for efficiency at all; it's a genuinely simple formula: `E = I × R`, where E is volts, I is current in amps, and R is resistance. So 120V = 20 amps × 6 — if the current is 20 amps and the resistor is 6 ohms, you get 120 volts. This is why, in an ordinary AA battery, the bottom of the cylinder heats up the most: that's where the high-resistance foil sits.

Joules tell us about heating. A fuse heats up, the same as a light bulb, a kettle, or an iron. A toaster has wires that carry current through themselves and heat up as a result — electrons collide with the wire's atoms, slow down, and that friction between the atoms produces heat. Run current through water, and H₂O splits into hydrogen and oxygen. Say you have a 2-amp current running through a wire with 100 ohms of resistance for one minute: I = 2A, R = 100Ω, t = 60 seconds. That gives (2)² × 100 × 60 = 24,000 joules. If you wanted to run current through a thick nichrome wire to heat a car seat to 40°C, a 5-meter, 0.5mm-diameter nichrome wire would need 150 joules to produce that heat over 30 seconds — and it wouldn't melt, since nichrome melts at 1300°C.

## Power sources

Most people have seen a car battery, or at least know that any AA or prismatic battery has a positive and a negative terminal. On a car battery, the red terminal is positive — pin 30 — and the black terminal is negative, pin 31. Whenever you're connecting anything to the battery, always connect the negative first, then the positive, so the electricity has somewhere to go, from red to black. On power devices you'll often see the abbreviation DC, telling you the electricity in the battery comes from a chemical reaction — a typical battery, in other words. Keep in mind that when connecting or disconnecting jumper cables to start an engine from another car's battery, you connect the positive terminal first, and when disconnecting, you disconnect the negative terminal first.

Multimeters explicitly indicate DC support for measuring voltage in cars — black lead to ground, red lead from the battery to the meter, and the battery starts supplying current. A car battery's voltage is almost always 12V — that's a constant. It might be 24V or 48V in some vehicles, but we'll stick with 12V here. Resistance varies with temperature and other factors, corrosion on the wire included. A light bulb's tungsten filament is what glows, thanks to its high resistance.

Amps need a ground, or the electricity has no way to travel from point A to point B. Voltage, though, will always look for somewhere to go — if the ground has high resistance, the odds of getting shocked go up.

## Tools

Computer diagnostics are the foundation of the trade. On the software side, a good auto-electrician will have Autodata, MotorData, Mitchell OnDemand (US-focused), and Elsa. On the hardware side, scanners for passenger cars include the LAUNCH X431, VAS 5054 (Vasya), BMW ICOM, SD Connect 4, and Lexia; for trucks, FCAR, Jaltest, and Texa.

There are also consumer diagnostic scanners like the FIXD Bluetooth OBD2 — you drive, and the app quietly accumulates data. Very convenient, and ideally something like it should just live in the glovebox permanently. A thickness gauge is worth having too. None of this requires reaching for an industrial-grade signal calibrator standing in for a multimeter.

Every reasonably capable person also has exactly one working multimeter, and a box of burned-out ones. If a battery has drained and you need to check for a leak, connecting a multimeter and pulling resistors out one at a time is usually enough to find it — a set of current clamps helps too.

## Circuits

Series and parallel connections. In a parallel circuit, electricity heads toward ground, from positive to negative. Put a light bulb in the middle of that circuit, and the electricity runs down the wire, reaches the bulb, and powers it — the bulb absorbs all the electricity, and everyone's happy. Swap that one bulb for two, and you now have a series circuit: two bulbs at 6 volts each. And if there's no bulb at all in the path, the current will burn something, since there's no resistance to stop it. Volts measure quantity — one volt is a specific amount of energy. 12V means 12 volts' worth of energy arriving in the system through the conductor. If your wires are catching fire, that's the amps at work, not the volts — volts burn out equipment, while a circuit breaker protects the wiring itself.

If voltage drops, that means less energy is present after a resistor than before it — the resistor dissipated heat, spending that energy. LEDs typically run from around 3 volts, but without current limiting, even those 3 volts will burn them out.

![Basic circuit diagram](https://your-scorpion.ru/wp-content/uploads/2024/12/Frame-162718.png)

Almost every car runs on a 12-volt battery, and the electricity travels in a loop — positive is always positive, negative is always negative. Ground gets attached to the frame or the engine block, but it's always the negative side, which carries no voltage of its own. Ground has to be attached to an unpainted section of metal. You could technically run ground straight back to the battery itself, but the wire length usually won't allow for that in practice. Positive goes into the fuse box, and from there connects out to the headlights and everything else that needs power. In other words, to add a new device to a car, all you really need to do is connect that device to the fuse box. Worth clarifying here: a resistor is not the same thing as a fuse. A resistor is shown in the photo below:

![A resistor](https://your-scorpion.ru/wp-content/uploads/2024/12/adadaded-1-600x311.jpg)

A car's battery charges while driving, from the alternator. The alternator generates far more electricity than the battery needs for charging, but only while the engine is running — that's how AC gets converted into DC. A voltage regulator also protects the car's electronic components, holding voltage in the 13.5–14.5-volt range.

![Voltage regulation diagram](https://your-scorpion.ru/wp-content/uploads/2024/12/Group-8.png)

Above is the wiring for a car horn. What matters here is never sending more amps than the relay can handle. In the example below, the relay switches voltage from Pin 30 to Pin 87, and the yellow wire feeds two horns. Two separate fuses here would be better replaced with a proper fuse block, though the circuit as shown is perfectly workable.

![Horn wiring diagram](https://your-scorpion.ru/wp-content/uploads/2024/12/Group-4.png)

Another example, with the starter. The starter is a fairly standardized component across car models, which simplifies things. In the diagram, notice the fuses sit as close to the battery as possible — not just in terms of components between the battery and the fuse, but in the length of wire between them too. And the red wire running to the starter is the thickest one in the car.

## Fuses

A fuse is a simple device: push too much current through it, and it breaks under the voltage, stopping electricity from traveling any further. Inside a fuse there's usually a thin metal strip that snaps, and you can see this happen visually, the same way you can see a bulb's filament give out. Plenty of people have seen the glass-tube fuses in old Soviet-era equipment — once the wire inside snapped, the fuse went straight in the trash.

![Fuse construction](https://your-scorpion.ru/wp-content/uploads/2024/12/Group-11.png)

If a fuse is rated for 5A and 10A gets pushed through it, the fuse will blow — saving whatever's next in the circuit, which would otherwise fail above 5 amps and potentially start a fire. If a fuse has died, there's a real chance a short circuit was involved somewhere. You could put in a 100A fuse, and even that would blow easily from something as simple as a botched jump-start (crossed cables), or an alternator that wasn't disconnected during a repair.

An example: the car won't start, and you've confirmed the battery's in great shape and fully charged. The driver presses Start and nothing happens — not even a dashboard light blinks. The first thing to check is whether the ground connection is intact. Second, whether there's contact with the fuse box. Say the cigarette lighter doesn't work: pop the hood, open the fuse box, find the corresponding fuse on the diagram, pull it out, and check whether it's still good.

![Checking a fuse](https://your-scorpion.ru/wp-content/uploads/2024/12/ddd.jpg)
![Fuse box diagram](https://your-scorpion.ru/wp-content/uploads/2024/12/dada.jpg)

Another common example is installing new equipment — a dash cam, say. You can install one just by plugging it into the cigarette lighter or a USB port, but then you're left with wires dangling around the cabin, and the dash cam won't run with the engine off. Some car models keep that outlet live constantly, or for just a couple of minutes after the engine's shut off. Another option is the OBD port, used for vehicle diagnostics — it's always live, but for security's sake it's better tucked deep inside the car, since there are known methods for stealing cars through the OBD port. A third option is an external battery pack that charges while the car's running, which only adds to the temptation for car thieves. Ideally, then, you connect a new device straight to the fuse box: one wire to a live slot, the other to ground. You can always probe fuse slots with a tester to find out which ones stay live even with the car off, then find a slot that isn't tied to anything safety-critical and wire the new device in there.

Fuses come in different shapes and colors, but inside one you'll almost always see a metal strip or wire. The fuse in the photo is a green 30A automotive fuse; red ones are usually 10A, blue ones 15A. Even an opaque fuse will have a small window somewhere letting you check the strip's condition. To avoid burning through fuses while experimenting with electronics, just wire up a high-power lamp or a kettle instead.

![Fuse color coding](https://your-scorpion.ru/wp-content/uploads/2024/12/Untitled-2.png)

Self-resetting fuses exist too. If a fuse looks fine visually but nothing's working anyway, further checks are needed. The first method is a test light: clip it to the battery's negative terminal, and touch the pointed end to the battery's positive side to confirm the test light itself works. Then touch the pointed end to the pins on the fuse — if even one side fails to light the lamp, the fuse has blown. If neither pin lights it, there's simply no current present at all.

![Testing a fuse with a test light](https://your-scorpion.ru/wp-content/uploads/2024/12/Frame-5925.png)

## Wires

Electricity mostly travels through wires, so their quality affects a great many aspects of the electronics involved. In the most bare-bones setup, a battery connects directly to the end component with no wire at all, as in the video below, and it'll technically work. But the minimum safe chain looks like this: battery > a good-quality, appropriately thick wire > a fuse box for protection > a switch to control current flow > a relay > the device itself > a ground wire running back to the battery or to the car's chassis.

[Video demonstration](https://your-scorpion.ru/wp-content/uploads/2024/12/фвфв.mp4)

A wire has its own resistance too — in other words, its length matters. A fuse box, when it blows, stops the flow of electricity, since electricity with no ground will heat the wire and risk starting a fire. That's exactly why the closer the fuse box sits to the power source, the safer it is for every other device in the circuit — shown earlier in the starter diagram.

A cable must never be damaged — a break in a cable's protective sheathing leads straight to fire risk. If a wire has snapped, it needs to be re-soldered. When crimping wires, you can't afford to lose even a single strand, since that risks overheating. Always use heat-shrink tubing to prevent corrosion — not electrical tape, just heat-shrink tubing slid over the soldered joint, run over with a heat gun, and done. Ideally, you'd also label the wire with a small adhesive tag noting what it's responsible for.

On the subject of protecting metal from corrosion more broadly — since a car's electrical wiring is far from the only place rust can start — it's worth knowing what actually causes it and how to head it off. Steel rusts on contact with water and oxygen; aluminum and plastic aren't prone to corrosion the same way. If you live somewhere without snow, with good roads and infrequent rain, rust isn't a serious concern, and factory protection is generally enough — most cars carry roughly 15 years of built-in rain protection. Road salt combined with water is what really accelerates rusting: a car drives over a salted road, the salt wears down the protective coating, exposes bare steel, and from there water and oxygen do the rest.

For trucks, Por-15 works very well, though it has to cover the vehicle completely — you can only coat what you can actually see, and any tiny gap between fasteners left uncoated is exactly where corrosion will quietly start, invisible to the eye. More convenient for everyday use are Fluid Film (the best option) and Blaster; both use lanolin to repel water, and lanolin conveniently stays visibly damp on the surface after application, making it easy to confirm coverage. Coat the vehicle's components once every one to two years, keep a visual eye out for corrosion, and you can otherwise stop worrying about rust. If those specific products aren't available where you are, look for Krown, Woolwax, Boeshield T-9, Rust Check, or Waxoyl (common in Europe).

On hybrids and electric vehicles specifically, be careful — Fluid Film can cause wiring insulation to swell, breaking its seal and inviting corrosion, so avoid spraying it onto any rubber components. Don't spray it on the transmission or the engine either, since those parts heat up and the smell won't be pleasant, and the engine block is most likely aluminum anyway. Skip the exhaust turbo too — there's no point, since it's going to rust regardless, and it'll eventually need replacing as a simple fact of car ownership. The key is applying the coating on a warm, dry summer day, and definitely not right after washing the car, since you don't want to seal moisture in between the coating and the steel.

One more lifestyle-level point: rust doesn't come from snow itself — snow is just frozen water, and rain falls almost everywhere except the Middle East. The real culprit is salt; drop a piece of steel into a glass of salted water and you'll see the result within a month. Corrosion is essentially unavoidable if you drive through winter. The fix: as soon as warmer days arrive (or at least once water stops freezing), get the car washed underneath right away — but skip that in winter itself, since water can get into seals and cause its own problems. And if you're driving a modern unibody (frameless) car, life is considerably easier, since corrosion isn't nearly as much of a threat.

Back to the electrical side: think through what happens when you turn the key in a car. You turn the key or press the button for barely a second — in that second, the starter fires the engine, and you let go, since the starter's only job is getting the engine started. A car battery is rated at 500 amps or more, and turning the key demands somewhere between 125 and 400 amps all at once, more still in cold climates. That calls for a wire of the right thickness and quality, with low resistance — copper has very low resistance, which is exactly why it's used.

Somewhere under the hood, a whole set of ECUs (Electronic Control Units) are also firing away, passing messages from one to another. MPUs (Memory Protection Units) exist to guard this, and split into two kinds. The first, Core MPU (CMPU), governs code execution from memory — a genuine security feature, and a difficult one to break. The second, Source MPU (SMPU), identifies data sources, isn't a security feature in itself, and is more vulnerable.

Power-consumption side-channel attacks are also possible here. A side channel is any incidental leakage — timing, electromagnetic emissions from a chip, sound, and so on. Static power consumption is always present in a device, while dynamic power consumption arises specifically when the device performs operations. An attacker's reasoning might go like this: power is the product of voltage and current, but a device's voltage is usually fixed (5 volts, say), so any change in power consumption has to come from a change in current. From there, an attacker takes a shunt resistor, measures the current, works out the Hamming weight, gathers power-consumption traces, and analyzes the resulting pattern. For learning and legitimate testing purposes, ChipWhisperer is the tool typically used — it runs bitwise operations and plots a trace that can be interpreted to extract secret data bit by bit.

Defending against this comes down to a few principles: where a branch in the code depends on secret information, it's better to avoid branching altogether, since linear algorithms are more resistant to this kind of analysis. Adding random delays and noise, masking operations through XOR (overlaying a random value onto the mask), and using non-standard logic-gate constructions all help too, along with settings like BODLEVEL and BODEN for brown-out detection.

---

Since the article opened on diagnostics as the foundation of the trade, it's worth closing on the other end of that same thread — not diagnosing a fault in a car you already own, but diagnosing the condition of one you're about to buy. The tools are largely the same instincts already covered above: a scan tool, an eye for wiring and connections, and knowing what a healthy system should look and sound like.

Start with a mechanic's help where you can get it, but a few checks are worth doing yourself first. Once the car arrives for inspection, let it cool down, get in, start it, and take it for a drive — get it up to at least 40 km/h so the engine has a chance to actually work. Turning the wheel shouldn't produce any strange noises, and the car shouldn't pull left or right while the wheel sits straight. If you like the car despite a slightly unsettling sign or two, try turning the wheel gently left and right within your lane and ask the shop whether the sound changes as you do.

Don't forget to drive over a speed bump or two — again, no strange noises should appear. Braking should be free of pulsation: pulsation felt through the steering wheel points to the front brakes, while pulsation felt through the seat or the car's body points to the rear brakes needing a shop's attention. Repeat all of this a few times, so everything in the car gets a chance to warm up properly.

Next, pop the hood and look at its edges — they shouldn't be uneven, since uneven panel gaps usually mean the car's been repainted. Test the battery, check the oil level, and compare the layout and shape of every component under the hood against reference photos online, to rule out unexpected parts or modifications. Shine a flashlight into every opening on the engine — there shouldn't be any trace of coolant or other leaks. Oxidation on aluminum parts should look consistent throughout; if it doesn't, there's a chance a part's been replaced, though depending on mileage and the specific part, that can be entirely expected too. Headlights should be free of chips, cracks, and condensation.

Any large, squeezable wiring you can reach — squeeze it. It should feel firm and springy. If the AC seems weak and the corresponding line compresses very easily under your fingers, that's a likely sign of low refrigerant. Check the brake fluid with a test strip (the paper kind, not electronic — those don't work well), and do the same for the coolant, though coolant test strips are noticeably less reliable.

Scan for fault codes — this also gives you a read on the actual mileage — and make sure the codes weren't cleared the day before your inspection.

If you can get the car up on a lift, rock each wheel back and forth; it shouldn't have play, and there shouldn't be rust or leaks. Check the tires' manufacture date too — a code like 2424 means the 24th week of 2024. If a tire is more than five years old, or the tread is essentially gone, it's due for replacement.
