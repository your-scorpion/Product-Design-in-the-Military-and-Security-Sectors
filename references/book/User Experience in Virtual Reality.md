# User Experience in Virtual Reality

*Maksim Tcvetkov · 27 March 2015*

Augmented reality (AR) and virtual reality (VR) have been around for a while now — reasonably reliable, and genuinely promising. Established standards already exist for these technologies, browsers like [junaio](https://play.google.com/store/apps/details?id=com.metaio.junaio) and [layar](https://play.google.com/store/apps/details?id=com.layar&hl=ru) among them, and plenty of people have seen large companies put these [technologies](https://play.google.com/store/apps/details?id=com.ikea.catalogue.android) to use already.

AR is the real world with extra information layered on top. VR is full immersion into a new three-dimensional space that doesn't necessarily connect to the real world at all. In augmented reality, you look at the world through your phone's screen or through glasses like HoloLens; in virtual reality, you look at the world through a phone mounted in a headset, or through a dedicated headset. Why do these technologies matter, and to whom? To plenty of people — film, gaming, education, medicine, even advertising. You can keep churning out flyers and buying landing-page traffic if you want. But whoever successfully puts these new technologies to work first, within their own market, wins the edge as the more innovative and interesting partner in the client's eyes.

# VR usability

Usability is about comfortable user actions, and those actions are shaped by the input device. VR devices are optical instruments that convert a stereo pair into a single image. The core idea is that as the user moves, the device changes the image shown to the left and right eye. Devices differ mainly in lens quality and field of view — the better the lenses, the longer you can comfortably stay in virtual reality. Lenses focus the user's attention 1.5–2 meters ahead, which creates a sense of depth — but to actually see an object sharply, focus needs to sit at 2 meters. Mobile VR needs a phone with a gyroscope (missing from plenty of phones, and only genuinely good in top-tier ones). Whatever the phone is missing can, and should, live in the headset itself. The other important sensors are the accelerometer and compass (both prone to drift, where sensor readings gradually shift off true, the compass especially) — these handle rotation left-right-up-down.

Field of view isn't just a technical limitation — it's also a matter of perception. A few reference values:

- Left/right: comfortable up to 30°, maximum 55°.
- Up: comfortable up to 20°, maximum 60°.
- Down: comfortable up to 12°, maximum 40°.

There's no trackpad or mouse anymore — we're rotating a full 360 degrees around ourselves. So the controllers here are magnetic clickers or gamepads. If you want to actually reach out and touch a virtual object, you'll need something like Leap Motion, which has an Android SDK and eats battery relentlessly. There are optical gamepads too, but they need a whole battery of cameras placed around the room to work correctly. Those cameras can track hand position and render it directly in the game. If you're not showing the player's hands, you need to give a reason why they're not visible. Right now, the future seems to belong to motion controllers carrying the same sensors as headsets and phones (gyroscope, accelerometer, compass). A controller like this can turn into practically anything in virtual reality, and users love that — it can become a dragon's reins, a fishing rod, a magic wand, a scalpel, or a wrench. Always rethink your game mechanics for VR specifically — don't just try to adapt a game that already succeeded on mobile. It's important to understand that a player in virtual reality is effectively blind in the ordinary sense — they can't type on a keyboard. But they can use dedicated gestures and voice instead.

You can't ignore the fundamentals of joystick design either — accounting for a player's cognitive and physical limits, giving them room to build mastery, communicating intent correctly, and delivering a genuine sense of control. Some basics: the thumb and index finger are capable of fast, precise action, which is why every running-and-shooting game maps its controls around those two fingers. The middle finger can be put to work too, for sustained actions like accelerating a run. The pinky and ring finger are weaker, and it's best to reserve them for secondary actions only. Average finger width runs about 11mm, so a physical or touch button needs to be larger than that. A joystick's responsiveness plays a defining role in how an app is perceived — a player's input needs to register within 100ms, or it reads as lag.

You can't overlook a player's reaction time to events either: 240ms is the average reaction time to a change within the field of view. That splits into stages — perceiving what happened (100ms), deciding how to respond (70ms), with the rest of the time going to the muscular response itself.

In VR, elements of the digital world are no longer confined to rectangular screens — they live in the space around us. The user isn't looking at a screen anymore; they're stepping into a world someone else built. **The answer to every graphic designer's question:** how do you even use square 2D layouts here? Design at qHD, 960×540, and be careful with transparency, since text needs to stay legible. VR is stereoscopic, so a classic two-dimensional Heads-Up Display (HUD) needs enough distance from the eyes (1–3 meters) and needs to sit on a three-dimensional plane. Selecting menu items on these panels can be done through gaze-based control (with a required dwell delay) — it's far easier on the brain that way. Fonts still need to scale based on the player's distance from the HUD. But it's worth understanding that in VR, an intuitive gesture generally beats a button.

In untracked VR (Gear VR, Google Cardboard), reticles are used to show the user's line of sight. This helps with spatial orientation and guides the user along a predetermined path. A reticle might change shape depending on whether an object can be interacted with, or fade in and out depending on whether the user is looking in the right direction. A reticle doesn't have to be a plain dot — it can take the form of a key, a handle, a card, or any other visual hint for interacting with the surrounding world.

Usability in VR is a genuinely new discipline, since the human body tracks head movement through the vestibular system, and the device is trying to do the same thing — showing an image the brain will accept as reality. Different parts of your brain see different pictures. The deep regions responsible for emotion and instinct believe entirely in what's happening; consciousness, meanwhile, understands it's a game and a simulation. Which is why HUD elements shouldn't be fixed to a specific spot relative to the camera. It's uncomfortable — HUD elements sitting at the edges of your vision are hard to read, and the center of the camera should hold gameplay, not buttons. Locking 2D elements on top of 3D objects is also bad practice. But making it clear to the user that an object is interactive is essential — those indicators just need to stay at the same distance as the content the user is actually interacting with.

There's a concept called motion sickness — nausea brought on by turning your head and having the image lag behind that rotation. The acceptable delay is no more than 20 milliseconds; even a 50ms deviation in how the image tracks movement is enough to trigger nausea. Sampling rate plays a huge role too: 100Hz at an absolute minimum, ideally north of 1000Hz — this value governs how many times per second the system reads the user's position in space. Holding your hands up high in front of the headset isn't viable, since it tires people out fast. Objects shouldn't come closer than a meter. Past 8 meters, an object starts reading as flat; past 100 meters, it collapses into a single pixel. Distorted image → nausea. Low refresh rate → nausea.

![VR distance perception chart](https://your-scorpion.ru/wp-content/uploads/2015/03/474388.png)

One of virtual reality's biggest problems: it makes people motion sick. Moving in a straight line will start to make someone sick, since they're also moving up and down as they walk — and there's a blurred zone near the nose worth accounting for too. This happens because the brain perceives the body's motion without the body actually experiencing real movement — the person is sitting, their legs aren't moving, yet their body seems to be traveling somewhere. Add hand tracking on top of that, and the brain goes into overdrive, and the body starts feeling genuinely nauseated. But experienced VR users learn to manage their own movement and avoid triggering strong nausea.

Display refresh rate matters enormously. The eye's own microtremor runs at 83Hz — that's the frequency at which the eye naturally vibrates, though that figure shifts somewhat under stress, maxing out around 103Hz. Which is why 90Hz is the refresh rate that matters for displays and frame rate — Sony runs 120Hz per eye.

Since there's no way to give physical feedback from a hit, you either need to show the player their strike is doing real damage, or avoid letting them land hits at all (shooting a bow reads better than swinging an axe). If you do decide to build hits into your game, modern controllers can vibrate to provide feedback, and the headset can play an appropriate sound on impact along with plenty of particle effects.

### How does AR actually work?

The user downloads a mobile app, launches it, and holds their phone up to a marker — in a magazine, on a display stand, on a model's skin, on a train car's window. That sequence of actions delivers content onto the phone's screen. Which means the content itself needs to be genuinely interesting, since the sequence of steps required to reach it isn't trivial. If your marketing team thinks a corporate YouTube video or a contact form counts as good content here, they're wrong. If a user downloads a 30MB app, scans a marker, and gets nothing but an ad, they'll be unhappy. Simple interactivity like an animated spinning mug isn't especially useful either — a discount, real interaction, or free content is what actually works.

Genuinely serious applications of these technologies already exist. Just a few years ago, "augmented reality" meant putting a marker on a table and watching a dancing pen appear when you pointed your phone's camera at it. Now augmented reality helps assemble complex industrial equipment — point your phone at a plane's engine, and the app shows you exactly what goes where. VR is used to train soldiers, doctors, rescue workers, and firefighters. Nobody wants to train a doctor by having them operate on a living patient for the first time — they get their foundational skills in virtual reality instead.

But back to marketing. Where should you actually deploy AR to attract customers? Not everywhere, and not everyone has enough idle time to spend fiddling with AR markers. On the subway? Definitely not — too much foot traffic, and unreliable internet. On a plane? No. Somewhere with free WiFi, where people are waiting around near shops for someone else? Yes.

### Advertising isn't the only niche

You don't have to sell anything with AR and VR at all. AR can be used for navigation, say — a user simply rotates a directional marker across every axis relative to its own center, provided they're willing to bother using AR in the first place. Another niche: presenting building exteriors and interior design. AR mode naturally suits examining an object, or a group of objects, at a small scale, within the bounds of an AR marker — the object under examination becomes the focal point, and the viewer can study it from every angle, but only from the outside. If that object is an architectural structure, it's entirely reasonable to want to examine it from the inside too, from a first-person viewpoint standing within the structure — which is where switching into VR mode comes in. To switch viewpoints, you can use silhouettes of people: point at a silhouette, and you get the coordinates of a new viewpoint in VR space, smoothly moving into position from that new vantage point. The user gets two distinct ways to study a building's exterior and interior.

By now it's clear that AR and VR apps tied to physical entry points are a completely different beast from an app that exists on its own, self-sufficient with no external hook. A user needs a reason to actually use AR or VR, and that reason is the marker. What can serve as a marker? The answer you'll like: pretty much anything. A cat, groceries, even an unfinished shopping mall. There are technical limits, of course — a plain white cube is hard to recognize — but the room for art direction here is genuinely substantial.

We already have something like veteran markers by now. QR codes see heavy use, and 90% of young people have an app that reads them. A QR code on a bag of chips lets you quickly find and download the right app and launch it immediately. But the bag itself is also a cubic AR marker, launching an entry point into a follow-up VR app. A different bag might continue the story of a cartoon or game launched from a previous package's marker. Eight bags together might become an entirely new marker, unlocking a new storyline, new content, or a new mechanic in the app. As you can see, there's real room here for marketing — and for saving money on real physical prizes.

The marker itself needs to look genuinely great — that's the art director's job. A marker needs to prompt action. It's worth spending real time designing a marker so it acts as a call to action, not just a piece of visual design.

### Putting it into practice

I built AR markers for historical landmarks for the ["Discover Moscow"](https://itunes.apple.com/ru/app/uznaj-moskvu/id643324519?mt=8) app. It was enough to point the device's camera at a landmark to immediately get relevant information, a living 3D model of the historical figure acting as guide, and plenty of other spinning bits of information. The same trick works with a magazine page, a catalog page, product packaging, an ad banner, a person, or a museum painting. Using AR mode makes sense as an entry point, connecting information to a real-world object. You can take it further, too — using formulas from a textbook as the starting point of a virtual guided tour, which helps the material land far better. Finland already uses this approach.

You could enable a direct purchase from a magazine or an ad, or let people share a piece of news on social media for a discount. News publications could animate photos coming to life, Harry Potter–style. Traveling renovation consultants already use a tablet to show clients how new wallpaper, a window, or a door will actually look in their existing space.

The next stage in interface evolution will be holograms, and the evolutionary chain of interfaces will follow the familiar path of CLI > GUI > HUI > NUI. VR and AR will become an inseparable part of interfaces, since games have already proven how much people want interactive emotional experiences. At this point, the two technologies have already merged into the concept of **Mixed Reality** (MR). Microsoft, Google, Facebook, and Intel are pouring enormous money into developing these technologies, headset [hardware](https://en.wikipedia.org/wiki/Head-mounted_display) included. That hardware is exactly where the first big money in VR will land.

### Nuances

It's worth understanding the basic characteristics of any VR headset.

● **FOV** — field of view. The larger this figure in degrees, the better. Human vision runs about 114°, but peripheral vision extends past 200° and has a major effect on immersion — which is exactly why HoloLens positions its lenses so unusually.

● **Refresh Rate** — the higher, the better. This is the display's refresh-rate equivalent, and it directly governs how long you can comfortably stay in VR.

● **Display Resolution** — pixels per inch, ppi. Genuinely critical, since at high pixel density a lens magnifies each pixel, and the resulting image can end up looking quite poor.

Let's talk about mobile VR and how it differs from dedicated headsets. Google Cardboard supports a FOV of 90–120°, noticeably less than a headset's. That's compensated for through lens correction. Google built a unified way to read a Cardboard's parameters via QR code — just scan the code printed on the Cardboard itself, and it renders correctly. Cardboard isn't the best mobile VR option out there. Samsung Gear VR lets you clip a phone in directly, which improves how accurately it reads accelerometer, gyroscope, and other sensor data. And crucially, its FOV approaches headset-level quality, and its screen resolution actually exceeds what dedicated headsets offer. It only loses out on Refresh Rate (60Hz at most) and overall render quality, due to rendering-pipeline limitations.

A new unit of measurement joins the standard ones: Pixels Per Degree (PPD). Everything is also measured in meters — the main prototyping tools mostly work in that unit, no feet involved anywhere.

Paper and pencil haven't gone anywhere — idea generation still happens in two-dimensional space. But even with excellent intuition for good decisions, you need to test every idea on an actual VR device as quickly as possible. Tools worth knowing: [aframe.io](https://aframe.io), [Unity](https://unity3d.com/ru/), [Unreal Engine](https://www.unrealengine.com/what-is-unreal-engine-4). Facebook's own [template](http://facebook.design/vr-template) is a solid starting point. Build your first project, publish it to Google Play or WebVR, and once you've got the hang of it, move on to the more demanding stores — the App Store and the Oculus Store. The latter runs through many premoderation stages, checking performance requirements, content guidelines around originality and safety, screenshots, and UX. You'll almost certainly find yourself coming back to this article more than once across all of those stages.

---

## Extending the article: interface terminology

The article's own evolutionary chain — CLI > GUI > HUI > NUI — deserves unpacking, since the acronym soup around interface paradigms gets genuinely confusing.

DMUI is direct manipulation — rotating and scaling objects by hand, essentially. HMI (Human-Machine Interface) is the popular term from automotive interface design — Tesla's touch panels are HMI. GUI is the interface layer needed for working with abstract data (video, photos). TUI is direct interaction with a physical material. HUD is a head-up display — the holographic, multi-touch interfaces you'd recognize from Iron Man's onscreen interface, say. FUI is the interface style seen in films and games — usually futuristic concepts, holomaps; ideally, not just visual noise, but genuinely grounded in real-world instrument references. CLI is the command line.

HID (Human Interface Device) is data transmission from an input device over a defined protocol — keyboards, mice, computer speakers, webcams, headsets. HUI (Human User Interface) covers keyboards, mice, and other native platform hardware, usually over USB — not to be confused with Holographic User Interfaces, which shares the same acronym. NUI is a natural interface, in the spirit of Google Home, Apple AirPods, or Amazon Dash.

There are equivalent sub-terms on the UX side too — DEX, for instance, is Digital Employee Experience: an employee's experience of their digital workplace.

## Reducing motion sickness in practice

Beyond the theory above, it's worth spelling out what actually helps in practice, especially for indie developers testing their own builds constantly in cheap Cardboards or low-end headsets.

First, it genuinely helps if your game doesn't lag — low FPS essentially never happens in real life, and 90fps is what fools the brain into acceptance. It's worth cleaning every display and lens beforehand, and making sure the Cardboard sits level on your head. Avoid sharp turns and fast movement, since that's exactly what triggers dizziness and nausea.

Second, play seated, with a fan nearby — a sense of physical stability helps the brain cope with VR. Grid mode and vignette mode during onboarding help too. Build in a pause every 10 minutes, and find a way to work that pause into the gameplay itself.

Third, don't play with an ear infection (the inner ear governs your vestibular system), and don't play exhausted.

Fourth, art direction plays a real role: softer tones, smaller pixels, and make sure to show the character's feet — avoid long descents down spiral staircases or cliffs. Unrealistic racing physics will trigger motion sickness specifically in players with real-world driving experience. I'd recommend disabling all camera shake, and tuning camera and control sensitivity along with FOV carefully.

There are also folk and medical remedies for motion sickness that can genuinely help, and it's worth drinking less coffee or energy drinks before a VR session — something with mint or ginger is a better choice. Oddly, some people start a course of medication and suddenly find themselves motion sick playing shooters that never bothered them before.

## Mobile AR mechanics in practice

The article's "how does AR work" section stays at a conceptual level — worth getting concrete about the actual technical pieces involved in building a minimum viable AR feature.

Mobile augmented reality comes down to geometry detection (ARCore) plus rendering (OpenGL). Motion tracking works the same way everywhere: points get placed on high-contrast edges, and their positions get tracked over time. Right now, mobile AR can only reliably track horizontal surfaces, with a 4-meter placement distance for something like furniture. Without corners or texture in frame, there's no surface to track at all. It's best to lock screen orientation, target a 30fps minimum, and give interface feedback without vibration.

Objects break down into a 3D model and a texture. Models need to be in `.obj`, `.collada`, or `.fbx` format (the last one supports animation); textures should be `.png` or `.webP` — PNG eats a lot of space, WebP eats far less. You'll occasionally run into more exotic 3D formats too: `.ply`, `.stl`, `.usd`, `.abc`, `.bsp`, `.vox`, `.md3`. It's common to end up converting from `.dae` to `.scn`. Lighting for models is typically derived from the average intensity of the camera's own image feed.

GLTF 2.0 is the popular format right now — a simple JSON structure built for WebGL. A GLTF scene is made up of a `.gltf` (JSON) file holding cameras, materials, and the like; a `.bin` file for geometry and animation; and a set of raster textures (`.png`, `.jpg`).

## VR training simulators for industrial staff

The article mentions VR training soldiers, doctors, and firefighters — worth extending into a genuinely different context: industrial-plant training simulators.

The first thing to internalize is that VR training doesn't replace classic training. The environment and its scale need to be copied with real precision — if it's already modeled in AutoCAD, you can export it straight into Unreal Engine with tessellation, and handle texturing inside the engine itself, mostly wrestling with UVs for lightmaps. Some lighting can be baked, but interactive objects still need to exist, or you end up with a strange, disconnected training experience. The old game-dev trick of letting buggy lighting sit in one corner of a room and just hiding it behind a cabinet won't fly here — everything needs to look genuinely realistic, weather conditions and time of day included.

VR isn't well suited to training people on an on-screen interface, since resolution is too low for reading text comfortably — gamification helps bridge that gap. For training, it helps enormously to hand the trainee a controller that closely matches the weight and physical feel of the real tool — a simple weighted grip, with the rest rendered in VR, works fine as long as the physical sensation is close enough. Where the real equipment is genuinely rare, a 3D printer makes it easy to build a training replica and dial in the right weight, since there's no shortage of printing materials to work with.

Where the training covers a genuinely dangerous process — say, teaching someone not to touch a pipe that runs at 100°C — it's worth measuring muscle tension, pulse, and brain activity directly, to actually gauge how well the person understands the danger involved.

A related, practical question: is there a minimum age for VR use? Almost every VR headset carries a 12+ restriction — a headset simply won't stay on a child's head reliably before that age, and bright screens held directly against developing eyes isn't a great idea besides. That age floor sometimes drops as low as 8+.

## Building the interface in Unity and Unreal

A cluster of practical engine questions, worth grouping together since they cover the actual mechanics of putting a usable interface on screen.

**Adapting Unity UI across screen densities.** By default, Unity interface elements anchor to the center of their parent rectangle (the screen), preserving their offset relative to that center — which means even a simple orientation change can push buttons outside the screen's bounds. The simplest fix is anchoring UI elements to the screen's corners instead, which keeps their position relative to those corners consistent across resolutions. But the density problem remains — any product manager will insist button scale stay tied to screen size, and that's exactly what Unity's [CanvasScaler](http://docs.unity3d.com/ru/current/Manual/script-CanvasScaler.html) is for. Its settings are easy to pick up by trial and compilation — pay attention to the Match property, which runs from 0 (width) to 1 (height), or anywhere in between. At its default of 0, the current screen width is compared against the reference resolution's width, and everything scales by that ratio; at a value other than 0.5, it'll compare both width and height against their respective reference values and settle on a scale factor that balances the two.

For jagged edges on rendered images, adjusting anti-aliasing settings helps — Edit > Project Settings > Player, pick your target quality tier (Fastest through Fantastic), and enable Anti-Aliasing at 2x, 4x, or 8x, keeping performance in mind.

**Reversing a UI animation** — a button flying onto screen, say, that needs to fly back off just as cleanly. First, disable looping on the animation itself in the inspector (select the animation, not the animated object). In the Animator panel, duplicate the animation node, set the duplicate's Speed to -1, and mark it as the default state. Add a bool parameter to track the button's state — call it `IsHidden`. Add transitions between the two nodes: for the transition using `IsHidden`, select the arrow itself (not the node) and set the Conditions attribute to `false` for the forward transition, and `true` for the reverse one.

**Building a standard Unity menu.** Create a Panel, set its size, add a Vertical Layout Group, and adjust its spacing. Add a Button inside the Panel and style it to taste, then duplicate it as many times as needed. For navigating between screens from those buttons: build several Canvases, each with its own functionality, disable the checkbox on the ones that shouldn't show at launch, and on your starting Canvas's button, configure its On Click() event to activate the target Canvas — clicking the button then switches to that screen.

**Extending the Unity editor itself** is possible through the `EditorWindow` and `Editor` classes, both of which can be subclassed.

**A scrolling content area** is best handled with the Scroll Rect component: create a new UI object, add Mask and Scroll Rect to it, and the scrollable region gets defined by that object's own bounds — point Scroll Rect at whatever content should scroll. By default, this supports both the mouse wheel and click-and-drag scrolling.

**UI animation tooling that avoids a lot of pain**: [DoTween](http://dotween.demigiant.com/) is genuinely popular, and considerably nicer to work with than Unreal's built-in Animator Controller.

**Building interface elements in Unreal.** Interfaces typically use the UMG Rich Text Block blueprint. A second route is C++, since Blueprints don't expose everything, and you often end up writing your own wrapper. The deepest route is Slate, which can build essentially anything — Slate is where Unreal's own base UI structures live, and while working in it is genuinely a lot of fun (not really), anything missing from UMG can be built in Slate via C++. Looking at a widget's code, UMG widgets are tied to Unreal through `UCLASS`, while Slate widgets (`SLATECORE_API`) aren't tied to Unreal at all and can't be reached from Blueprints — which is exactly why they expose so few configurable settings in the editor.

A basic blueprint might trigger a widget named "Test" on pressing the H key, using [Set Input Mode UI Only](https://docs.unrealengine.com/4.26/en-US/BlueprintAPI/Input/SetInputModeUIOnly/) (or its siblings, Set Input Mode Game And UI and Set Input Mode Game Only) to shift cursor focus from the game to the interface widget — without that call, the default behavior requires Shift+F1 to toggle focus manually, which isn't especially convenient. A widget can also be created at [Event BeginPlay](https://docs.unrealengine.com/4.26/en-US/BlueprintAPI/AddEvent/EventBeginPlay/) rather than on a keypress, and then simply shown or hidden by that same key.

Looking at Slate directly — `button.h` in Unreal's own source holds `SButton.h`, "S" for Slate. Slate widgets live in a `TSharedPtr` and don't inherit from `UObject` at all, so a Slate widget has to be destroyed manually and its `TSharedPtr` reset by hand — UMG, by contrast, cleans up hidden components automatically through reflection.

**A glowing lava or fire shader effect** has dozens of viable approaches — one of the simplest chains a SmoothStep node (fairly expensive performance-wise; Step works as a cheaper substitute) into a Multiply feeding the Emissive Color output.

## Lighting, rendering, and texturing for VR/AR assets

A separate cluster of questions on the art-production side, worth grouping since they cover the render pipeline that actually produces the assets this article's UX principles get applied to.

**Getting good highlights from an HDR map in 3ds Max + V-Ray.** In VRayLight's settings, under Texture, set a Composite map and drop it into the material editor — the bottom layer holds your HDRI map, and the top layer can hold whatever looks good and reads with strong contrast. Alternatively, export your scene as DAE or OBJ and finish the work in HDR Light Studio, which has a refreshingly simple interface. This particular workflow is really only relevant to V-Ray, where a somewhat noisy, mottled irradiance map is exactly what gives you a sense of realism — Corona renders everything far more cleanly by comparison out of the box.

On improving exterior and interior lighting more generally: take your HDRI and pull down its brightness or gamma until the HDRI itself isn't blown out in the scene, while making the sun source itself brighter. Shadows should lean bluish, overall lighting should lean warm. Get rid of every blown highlight — there shouldn't be pure white pixels sitting on a gray material.

Measuring light in watts is the wrong approach — light is measured in lumens, or more precisely in lux (the amount of luminous flux landing on a square meter of surface). Head to a manufacturer's own site and download their IES or LTD files instead (LTD models a light source with real physical flux characteristics). Every competent interior designer runs a lighting calculation in Dialux beforehand — it's free, and most lighting manufacturers' fixtures are already loaded into it.

For generating noise textures and similar alpha maps, [Filter Forge](https://ru.wikipedia.org/wiki/Filter_Forge) or [Substance Designer](https://www.allegorithmic.com/products/substance-designer) both work well.

It's also worth adding a bokeh effect — not simply blurring a point into a circle, but varying the highlight shape based on distance (a genuine lens effect), with differently colored rims around the edge. Film lenses, cylindrical ones especially, produce genuinely interesting bokeh this way — though it's easy to overdo it and end up with a "toy" miniature look instead.

**Troubleshooting a light rendering the wrong color** — a blue light source rendering as turquoise, highlights included. Check the Fog Color setting on your materials first. Fog Color drives the colored-haze effect in transparent and semi-transparent materials, and its density scales with the size of the scene. Color also depends on a light's intensity — at zero intensity, you get exactly the color set in the light's own settings. In product visualization specifically, it's common to use two separate lights: one for the actual illumination, one purely for reflections.

**Working with Quixel's dDo/nDo workflow**, and where AO fits in V-Ray. dDo (not nDo) is for procedural generation of scratches, dust, damage, streaks, and wear; nDo is for hand-building a normal map. dDo is a texture-projection tool. The workflow: build your model in 3D, apply a UVW Map and then Unwrap UVW (or get a clean UV layout some other way), export a render of the UV map, and export the model as OBJ. In Photoshop, use the nDo plugin to start a new project and get a canvas ready for painting a normal map — draw a selection, and the plugin offers to convert it into a normal map directly, letting you paint a normal map of any complexity by hand. From there, load your model, textures, painted normal map, and color map into dDo itself, assign links so you can set material IDs in the ID map (yellow for metal, red for wood, and so on), and choose the target game engine under Calibration Profile — clicking Create Base then generates a model preview and a set of PSD files covering the AO map and related passes. Smart Material is dDo's material library — apply materials to your object via those same links, and remember to save your project regularly.

For baking, Substance Painter is genuinely the nicer tool to work in — the resulting normal, AO, and curvature maps can still be brought back into dDo afterward if needed. V-Ray has no dedicated AO channel, so AO usually gets used either as a mask inside the diffuse channel, or multiplied onto diffuse directly in Photoshop. For cleaning up noise on a Mac, [Noiseless](http://macphun.com/noiseless) does the job.

**Noisy normal maps from a Scanline render in 3ds Max for VR.** Scanline can't actually produce noise by definition, since it isn't a path tracer — the problem is almost certainly in the export format. As a sanity check, try exporting to 32-bit EXR, then converting down to 16-bit and then 8-bit; there shouldn't be any noise at that point. For textures generally, `.tga` is the better choice, and Substance Painter bakes normal maps noticeably better than the alternatives.

## 360 video and camera tracking for VR production

**Editing and exporting 360 video comfortably in After Effects.** The standard approach uses the [Mettle](https://www.mettle.com/product-category/360vr-plugins-for-after-effects/) plugin suite. For reviewing 360 footage outside a headset or YouTube, [DomePlayer](http://www.orihalcon.co.jp/amateras/domeplayer/en/) is a genuinely nice standalone viewer. On export specifically: MP4 tops out at 4096×2048, while a proper panorama needs 8192×4096 — so even H.264's own 7680×4320 ceiling won't cut it. You'll need a proprietary codec instead; HAP-Q and DXV3 are both solid choices, or you can push it out through ffmpeg to H.264, though that route can run into playback issues.

**Camera tracking studios in Moscow for UE4 work** (outside of Vive-based setups): Q-take is worth looking at, and for tracking directly into an Unreal 3D world, Aximmetry.
