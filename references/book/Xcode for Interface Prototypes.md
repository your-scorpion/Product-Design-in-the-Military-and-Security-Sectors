# Xcode for Interface Prototypes

*Maksim Tcvetkov · 19 March 2017*

Why does a designer need to know how to lay out iOS apps at all? First and foremost, it helps open a real dialogue between developer and designer. Knowing the nuances and capabilities of development saves time and lets you use 100% of what the platform offers. For instance, understanding that Launch Screen only works from iOS 8 onward, and that iOS 7 and earlier need a launch image added to the app's asset catalog instead, lets you produce every asset in one pass, without peppering developers with irritating questions.

Plenty of people have heard developers throw around the word AutoLayout. It's the layout system for building apps on Apple devices. By default, if you drop a colored object onto a phone screen (a View inside a View Controller), that object anchors to the top-left corner, and on different iPhones it will **not** resize or reposition itself. That behavior does a poor job of answering the demands designers actually put in front of developers.

![Default anchoring behavior without Auto Layout](https://your-scorpion.ru/wp-content/uploads/2016/12/7586664н67474.png)

To make every spacing value and size match the design, Auto Layout is used, which positions elements relative to one another. Auto Layout is a technology built around constraints describing the interface. It dynamically computes the position and size of every object in the UIView hierarchy, based on the constraints defined for those objects. With Auto Layout, you can constrain an element so it sits centered with its top edge always 8pt below the View's top edge, say. And, most importantly, if screen sizes differ, Auto Layout automatically places the graphical object exactly where it needs to be. This matters, because iPhone screen sizes vary enormously — just look:

For the iPhone 5 / 5S, the portrait screen is 320 points (or 640 pixels) wide and 568 points (or 1136 pixels) tall.
For the iPhone 6 / 6S, the screen is 375 points (or 750 pixels) wide and 667 points (or 1334 pixels) tall.
For the iPhone 6 / 6S Plus, the screen is 414 points (or 1242 pixels) wide and 736 points (or 2208 pixels) tall.
For the iPhone 4S, the screen is 320 points (or 640 pixels) wide and 480 points (or 960 pixels) tall.

![iPhone screen size comparison](https://your-scorpion.ru/wp-content/uploads/2019/12/Artboard-4.png)

Let's start with the simplest possible layout task: we want an object centered on screen, staying put no matter what. For that, disable every guide. Similarly, if a View is stretched to fill the full width and height of the screen with every guide disabled, you get a fully filled background.

![Disabling guides for a full-bleed background](https://your-scorpion.ru/wp-content/uploads/2019/12/Untitled-1.jpg)

In most cases, designers draw layouts with fixed margins and stretching content. To pull that off, use the Add New Constraints icon in Xcode's bottom-right corner. It lets you add spacing (constraints) to neighboring objects or to the screen's edges. Constraints show up as blue lines. You can also build constraints with a hotkey: select an object, hold Ctrl, and click the object you want the guides built relative to.

![Building constraints with Ctrl-click](https://your-scorpion.ru/wp-content/uploads/2019/12/Untitled-1-1.jpg)

If your constraints turn yellow, check the reason in the yellow circle next to the View Controller Scene in the right-hand panel. Yellow errors aren't critical, but red ones will stop your project from compiling. Say you manually resized a View and picked up yellow errors — you'd choose Fix Misplacement from the yellow circle's menu, which stretches the object to match the size you set visually in the editor. Resolve Auto Layout Issue gets you the same result. Designers love nudging elements a hair's-width left or right in the graphical editor, which is exactly what breaks constraints — all you need to do is add or subtract, from the constraint's settings, whatever value you nudged the element by. It's more convenient, though, to just click Update Constraint Constant for the selected element.

Next to the Add New Constraints button sit a few other useful buttons:

![Xcode's Stack, Align, Pin, and Resolve buttons](https://your-scorpion.ru/wp-content/uploads/2019/12/Screenshot_3.png)

1. **Stack** — drops the selected interface pieces into a StackView. Interface Builder decides on its own what kind of StackView to build based on how the elements are arranged. Besides the Stack button, you can also build a StackView by dragging one in from the object library, like any other element.
2. **Align** — lets you snap elements precisely to a grid, whether that's side-by-side, vertically centered, or bottom-aligned. You've likely used this same logic constantly in text editors, centering text or aligning it strictly to the left edge of the page.
3. **Pin** — my main menu, letting you set hard boundaries relative to an element's own size or its nearest neighbor. In Pin, you can explicitly specify which constraint you want in any given direction and set its parameters. This same menu also lets you give a group of buttons a fixed size, regardless of screen scale.
4. **Resolve Auto Layout Issue** — the layout fix-up tool.

## Theory

Spacing in your layout shouldn't follow a progressive scale like 4, 8, 12, 16, 20, 24, 28, 32, 36, 40, 44 — that's simply too many spacing variants. The better approach is a geometric progression based on the golden ratio, giving you a spacing scale of 2, 4, 8, 16, 32, 64. You can, of course, insert 24 between 16 and 32, but only as an exception.

![Geometric spacing progression](https://your-scorpion.ru/wp-content/uploads/2019/12/впв.gif)

By default, Interface Builder previews the interface on an iPhone 6S — and Apple's own [layouts](https://developer.apple.com/ios/human-interface-guidelines/resources/) are built for that same size.

> For anyone actively working with graphics, there's a handy hotkey — **Cmd** + **=** — that resets height and width back to their reference values.

In the simulator, you can scale the interface via Window > Scale. It's important to set maximum dimensions in Interface Builder.

## The Xcode interface

Interface elements follow three basic models: active, static (inactive), and passive. A button is an active control; an image is a static element. A text field is a passive element. Depending on the model, you'll have a different set of options available in the Utility Area.

Every constraint has its own properties, found under the Size Inspector tab (with the element selected). Head into View > Utilities > Attributes Inspector, and you'll find these properties:

![Constraint properties panel](https://your-scorpion.ru/wp-content/uploads/2019/12/Screen-Shot-2016-12-20-at-12.08.03.png)

- `Relation` — how the constraint relates to its Constant value,
- `Constant` — lets you change the object's size.
- `Priority` — runs from 1 to 1000. A value of 1000 gives the constraint maximum priority.

A Multiplier of 1 means the element's size stays 1:1, or 100%. Change that value to 2, and you get 2:1, or a 200% increase. Using a multiplier, the constraint looks at all the space available to it. At 3:1, all available space splits into three, with one part assigned to the element. Set 4:3, and all available space splits into four, with the element assigned a scale of three parts. Ratios like 16:9 and 4:3 help adapt content across iPad and iPhone. To make sense of it, just remember 6:1 = 6 — swap the `:` for a `/` and it all clicks into place.

![Multiplier constraint example](https://your-scorpion.ru/wp-content/uploads/2019/12/sfldkajldamlaljlkwf.gif)

Elements can also work out how much space they need on their own, based on their content. Reproduce the constraints in the image below to see how flexible, "rubber-band" layout gets built.

![Content-based sizing example](https://your-scorpion.ru/wp-content/uploads/2019/12/Untitled-1-2.jpg)

Constraints let you scale objects too. Adding a Height constraint to a Segmented Control, for instance, lets you increase its height.

![Scaling a Segmented Control's height](https://your-scorpion.ru/wp-content/uploads/2019/12/идеи.png)

One of the standard design requests is scaling buttons and spacing proportionally to the screen. **Equal Widths** and **Equal Heights** handle exactly that — Auto Layout can constrain width or height this way. Hold Ctrl and drag a line from one text block to another; in the popup that appears, choose Equal Widths.

We've done some practicing — now let's understand what's actually happening. Every control has a position and a size. Pay attention to the "spacing to nearest neighbor" hint that appears when building constraints, and you'll notice spacing can be set relative to the main view or to the nearest object. Adding a UIView, you can adjust its size not just through height and width constraints, but through position constraints too. It's important to understand that every constraint interacts with the object sitting above it in the layer hierarchy.

![Constraint hierarchy example](https://your-scorpion.ru/wp-content/uploads/2017/08/sfldkajldamlaljlkwf-3.gif)

- **Vertical Spacing to Layout Guide** (pins the top of the image to the top of the window)
- **Leading Space to Container Margin** (the image's left edge stays "nailed" to the window's left side)
- **Trailing Space to Container Margin** (the image's right edge stays "nailed" to the window's right side)
- **Vertical Spacing** (the vertical distance between the image and its label)

Knowing these nuances lets you build genuinely complex layouts. Say you need to increase the spacing between vertical elements — in Auto Layout, add a vertical spacing constraint and give it a greater-than-or-equal-to (or less-than-or-equal-to) attribute.

Everything described so far has been about panels and standard controls. But text can create considerably more trouble. Let's build two Labels inside a UIView. Select the top label and apply these settings:

![Label constraint settings](https://your-scorpion.ru/wp-content/uploads/2019/12/44.png)

Apply the same settings to the bottom Label, but use Bottom Space instead of Top Space. Switching between devices, you'll notice the text overflows the space allotted to it. Shrinking the font size isn't really an option, since a size that suits an iPad Pro 9.7 could easily be too large for an iPhone 4. I've repeatedly heard designers argue that mobile apps are better off not varying font size based on content — you can't anticipate every possible problem, it's unclear how you'd even design for it, and nobody wants to draw out every possible variant by hand.

That's where Adaptive Layout comes to the rescue. A single universal Storyboard covering the whole interface is a great solution — one layout for every screen is far easier to maintain than a pile of separate layout variants for the same screen. Which is why a ViewController in Xcode exists inside a Class Size environment with a defined width and height. Class Size comes in two flavors, **Compact** and **Regular** — you can read more [here](https://developer.apple.com/documentation/uikit/uitraitcollection). Within AutoLayout, you can toggle constraints on and off per the current Class Size — and that's exactly what we'll use to solve the text problems described above.

1. In the Attributes Inspector, click the small + to the left of the Font field. Choose Compact for Width and Any for Height. A second Font selector appears with a `wC` attribute, letting you shrink the font size. Do this for every text block.

2. Now click the Vary for Traits button and check the layout in Compact mode. You'll notice every iPad, portrait or landscape, draws its width and height as Regular. `wR` and `hR` denote Regular Width and Regular Height; `wC` and `hR` denote Compact Width and Regular Height.

3. Changing the iPad's height under Regular will affect a phone's portrait orientation too. To touch only the iPad, work with width alone. The iPhone Plus is its own particular headache, carrying `wR` and `hC`.

4. In the inspector, also find the AutoShrink field and choose Minimum Font Scale, adding a value of 0.5 just below it. This lets the text block shrink automatically. And yes, you can test text scaling with Autoshrink > Maximum Font Scale (0.4) to compress text size based on how many characters a block holds.

![AutoShrink demonstration](https://your-scorpion.ru/wp-content/uploads/2019/12/sfldkajldamlaljlkwf-1.gif)

You can now lay out simple screens, but without transitions between them, they're not worth much. You can create as many new screens (View Controllers) as you like, and build transitions between them using a Segue — the mechanism for switching screens. Xcode draws arrows between screens, making it easy to see which screen follows which. Push, for instance, is the classic right-to-left screen transition. With what you've learned so far, you can already build a prototype like this — enough to test a hypothesis.

![Segue transition example](https://your-scorpion.ru/wp-content/uploads/2017/03/ezgif-3-4596fbb25b.gif)

## Looking ahead

You've probably already wondered: what happens once you have too many artboards and get lost in your own prototype? Hold *Cmd* and click the edges of the screens you want — a blue outline appears. Then go to Editor > Refactor to Storyboard. You'll be prompted to build a new storyboard from the selected screens; once it's created, you'll land in that new storyboard, complete with segues and the screens you selected. The screens moved out of the main storyboard become references back to it. Congratulations — you've just refactored your project.

---

## Choosing a design resolution, and Retina

What size, or set of sizes, should you actually design for on iOS? If you draw from Sketch's built-in screen-size presets, smaller iPhones end up needing a scroll — and whether that's a designer's problem or a developer's problem is worth settling clearly. It helps to think more abstractly here: as a designer, you're not really drawing an interface — you're describing an interface through the logic of its dimensions. Separate layouts for separate screens only really make sense for splitting iPhone from iPad. Draw a button, and what you're actually doing is describing every distance from that button's edges outward.

Scrolling is a perfectly normal outcome — width stretches the interface, height scrolls it. If a design genuinely needs to avoid scrolling, a developer can walk through the constraints and multiply them by `UIScreen.main.bounds.height / 568.0`, which adds real overhead on older devices — but scaling an interface proportionally to screen height is possible if the requirement calls for it.

If you draw a separate design for every resolution, a developer ends up needing to add `UIScreen.main.bounds.size.height == <value>` checks and lay out every screen individually — a distinct form factor for every resolution and orientation takes a very long time. If you have to settle on one reference size, I design against the iPhone 5's dimensions, since scaling upward is easier than designing for a larger screen and later trying to compress it down for smaller ones. For checking a design across every resolution, constraints paired with the Anima plugin work well. If you do decide to design at the larger size graphic editors default to, use the iPhone 8 at 1x scale (375×667) with constraints.

Pixel Perfect died on iOS the moment 5.5″ displays arrived — even on an iPhone 7+, you can't get a genuinely perfect 1px line, even setting it explicitly. About the closest you can get is something like a 0.3333-point value.

On base measurements: at the iPhone X's 375×812px screen size, the tab bar runs 83px (accounting for the 34px Home Indicator zone at the bottom), the Navigation Bar runs 97px, and the status bar runs 44px.

**Retina** covers anything above 220 ppi. You can enable HiDPI Mode on macOS, which is effectively a 2x compression that some people find preferable — just run this in the terminal:

```bash
sudo defaults write /Library/Preferences/com.apple.windowserver.plist DisplayResolutionEnabled -bool true
```

There's a familiar story with designers who, working on Retina displays themselves, end up drawing microscopically small fonts without noticing — easily solved by keeping a cheap, lower-quality second monitor around specifically for checking layouts. And if you're designing in Photoshop on a Retina display, you'll either need to draw everything at 2x, or live with blurry-looking mockups on your own screen.

## Getting the implementation to match the design 100%

A design can be perfectly built and still drift from its implementation — worth addressing directly, since it comes up constantly. There are three core building blocks of modern design worth understanding here: typography, color, and shadows.

**Typography.** You'll most likely be working with San Francisco, the system font. The recurring headache on iOS is tracking, and the correct values need to come from Apple's own [guidelines](https://developer.apple.com/ios/human-interface-guidelines/visual-design/typography/). Sketch users have a solid [plugin](https://github.com/kylehickinson/Sketch-SF-UI-Font-Fixer) for this. It's also worth toggling Sketch's text alignment between Fixed and Auto to reset a text block's width and height back to its defaults.

**Shadows.** Shadows on iOS always render larger than they look in Sketch, since there's no Spread parameter — this gets corrected manually on the development side, by shrinking the shadow's radius and increasing its opacity.

**Colors.** I've never really run into problems with solid colors, but gradients render darker on iOS than a designer expects — this one gets corrected by eye.

On the deeper question of why iOS development so often ends up looking like a pile of workarounds — something plenty of designers notice even in Apple's own apps: historically, this traces back to insets for transparent bars and similar tricks. It's exactly why developers don't love writing "custom" bars — Apple's own automatic behavior stops working the moment you do, and you end up having to build your own layout entirely from scratch, often without access to certain capabilities that simply aren't exposed in the public API. My usual advice to designers is to make peace with a single Navigation Bar behavior across every scene, at least through iOS 13 — you can't have a black nav bar on one screen and a transparent one with elaborate animation on the next.

On the large-title navigation bar introduced in iOS 11 specifically: it's genuinely difficult to customize, so it's not worth building functionality into it beyond the default behavior. That bar bundles a large, attractive title, a search field, and a pull-to-refresh control. Apple's own designers stretch the search field across the full Readable Width, depending on whether the interface follows a Master-Detail or Fullscreen pattern. As for the large gray divider that separates content in iOS — that's used to divide sections within a grouped table.

## Beyond Auto Layout: platform capabilities worth knowing

A few reader questions push past pure layout work into the broader iOS platform — genuinely useful context for a technical designer to have, since these capabilities shape what's actually feasible in a design brief.

**Peer-to-peer transfers with no internet on one side.** Say two customers of the same bank are standing next to each other, and one of them has no internet connection — how do you move money between their two banking apps at a systems-requirements level? If we're talking iOS specifically, Bluetooth is the right fit. On iOS and macOS, the possibilities here are nearly unlimited; watchOS and tvOS carry real restrictions, and the system can even tie connection behavior to the accelerometer, lengthening or shortening how often it attempts to reconnect. This is entirely workable for transmitting payment details and a small photo — and notably, one side of that exchange doesn't need internet at all, since all it does is hand over its own payment data. NFC is the alternative technology, though it's heavily restricted on iOS while working without much trouble on Android.

A related question: does a photo actually show up on the receiving side without internet? It will, if the avatar's been cached beforehand. Content is usually cached following a Lazy or Synchronized cache pattern for things like text, news, and e-commerce listings — but you can cache pretty much any content (avatars, GIFs in comments), as long as the cache doesn't balloon past roughly 100MB. Video can be cached using a fast-start pattern. Write-through caching and LRU caching are both worth looking into here.

**Battery life, from a design standpoint.** The screen eats more battery than almost anything else, and it's the one thing design actually has leverage over. Plainly: if the screen resolution is Quad HD (2960×1440), it's worth offering a switch down to FHD+. An LCD display burns battery simply through its own backlight brightness, while on AMOLED, your actual design choices matter — blue and white cost a lot of energy, black costs noticeably less.

In 3D games, the GPU eats battery aggressively, and the camera isn't far behind. The OS itself draws on RAM, CPU, and GPU too. Streaming video is efficient when it's decoded by the device's hardware codec, but a software codec burns through a lot of energy. Music, by contrast, uses very little power, since it runs through its own dedicated co-processor. GPS paired with Google's Fused Location Provider API helps by scanning the signal continuously only while the device is stationary on a surface. A GSM connection is fairly costly too, though WiFi and SMS cost even more — which is exactly why a phone switches over to 4G at night while idle.

**Notifications, App Clips, and deep links, for a retail app specifically.** Notification UI is configurable, and the trigger for one can be a push message or an in-app event. There's the older Today widget and Force Touch on the icon — a widget can hold almost anything, limited only by its size. But iOS 14's SDK changed a lot of this: your app's icon may not even live on the home screen anymore, since it can get swept into the App Library.

On the more interesting side, App Clips let a user tap an NFC tag, a banner, a QR code, a link, or even ask Siri, and that launches the app. Access here comes with real constraints — a 10MB size cap, a trimmed-down set of frameworks, and data can only pass through to your own main app, all fairly strict. Since this is tied to a real physical location, it's worth building in a location-confirmation step before payment.

You can also extend your app's capabilities into Siri through Siri Intents, `NSUserActivity` plus Wind-down, Siri recommendations, and Shortcuts integration.

A Deep Link is simply a link into the app, used to route a user to a specific screen — open an email from a desktop, and you redirect to a landing page; open it from a phone, and you redirect into the app instead. Deep links come in three types:

- **Basic** — the simplest kind, opening either a page in the app, the App Store, or a promo page.
- **Deferred** — when the app isn't installed yet, we collect device data and build a fingerprint; once the app is installed, we already know which page to show the user.
- **Contextual** — we gather everything about the user and show a personalized onboarding flow through the app, based on the link.

iOS supports URL schemes like `tg://` or `superapp://` — these fail if the app isn't installed, and an unfamiliar-looking URL tends to unsettle users. The more modern Universal Links, iOS 9 and up, look like ordinary links and open either an app screen or any regular web page. Android works similarly, though it can use plain HTTP instead of HTTPS.

On the marketing side of deep links: in an AppMetrica campaign, you can link out to an external site through one of three options — App, Deeplink, or URL. The URL option is exactly what launches a browser — specifically `SFSafariViewController`, a browser embedded directly inside the app.

**Content failing to load for certain carriers in certain regions.** If it's a plain HTTP GET request through something like Alamofire or RxMoya, start by checking whether an Android build can swap in a different IP for the domain. Where the error resembles an SSL/DNS failure, running that configuration through Firebase and sending requests via Curl mode can help isolate it. If the error is SSL-specific on iOS 12, try capping the maximum TLS version at 1.2 rather than 1.3.

It's fine to log anonymized network metrics — I don't fully trust logs myself until I've confirmed they're placed correctly — but this isn't something to ship in a release configuration. Log to your analytics service, never store on the client, or you're opening the door to a MITM attack.

## A few workflow odds and ends

**Cleaning up stray `.DS_Store` files after moving from Mac to Windows.** Open `cmd.exe` and run:

```
del /s /q /f /a .DS_STORE
```

**Getting code to run on a laptop with too little memory.** There's an old-school trick using a USB flash drive: plug it in, open its Properties, go to the ReadyBoost tab, choose "Dedicate this device to ReadyBoost," and slide the setting to its maximum value.

![Windows ReadyBoost configuration](https://your-scorpion.ru/wp-content/uploads/2021/04/s.png)

From there, it's worth dedicating memory to the page file too — the more RAM you actually have available, the smaller that page file needs to be:

- 512 MB RAM — 5012–5012
- 1 GB RAM — 4012–4012
- 2 GB RAM — 3548–3548
- 4 GB RAM — 3024–3024
- 8 GB RAM — 2016–2016

Paging (or swapping) works on an old, simple principle: a computer might be able to address up to 16MB of memory space, while only actually having 4MB of RAM installed. Certain segments get offloaded to disk and reloaded into RAM only when needed, which lets a program use more memory than the installed RAM could otherwise provide on its own.

## A note on iOS security

Is iOS actually secure out of the box, and are there ways around its security model? iOS is genuinely secure, provided you stick to some straightforward practices: never hand physical access of the device to anyone, set a passcode, disable JavaScript in the browser, don't keep anything genuinely important on the phone itself, avoid installing suspicious apps, keep everything updated, disable iCloud, turn on "Ask to Join Networks," disable Siri, disable voice dialing, and don't open unfamiliar links. Skip these, and there's a real chance you become a victim.

On bypassing that security model: there's a user-application layer and a hardware layer. iOS is, quite literally, a layer sitting between the hardware and user-facing applications — apps never talk to the hardware directly, only through iOS's own layers:

1. **Cocoa** — the application layer: an app's front end, push notifications, icon badges.
2. **Media** — OpenGL and the like, 2D/3D rendering.
3. **Core Services** — the security layer, peer-to-peer functionality, used for games and content transfer.
4. **Core OS** — system configuration, OpenCL.
5. **The kernel** — BSD, the filesystem, networking, drivers, DNS access.

Jailbreaking is the tool used to get around these restrictions — a kind of root access for iOS, though not quite the same thing as root on other systems (root on iOS and root on Android are genuinely different concepts). Jailbreaking simply lets you work more flexibly with the rules built into the OS, removing restrictions — done by installing a custom kernel with root access. Yes, once jailbroken, you can install apps Apple would never have approved, which means there's a real risk that an app could reach protected data it shouldn't have access to. Jailbreaking is carried out through third-party utilities like Cydia (a package store for tweaks on jailbroken devices), Hexxa Plus, ApricotiOS, Yuxigon, Sileo, and Trimgo.

There are different categories of jailbreak: with some, the underlying vulnerability persists indefinitely; with others, a simple reboot undoes it. There's also a variant that requires a connected computer for the device to boot successfully after jailbreaking.

Broadly, regular reboots, Lockdown Mode, and BlastDoor all help defend against this. None of them help against attacks that come through the system's own built-in libraries, though — Apple, for instance, provides a CoreML Engine for photo analysis, and a larger Apple Foundation Models framework. Any developer can use CoreML Engine — you feed it data and drag an `.mlmodel` file into Xcode — and some people have found ways to put that capability to improper use.

**A related, practical question: what if macOS itself won't let a file run at all?** That's a bit risky territory, but the [PermissionsReset](https://ohanaware.com/permissionsreset/) utility is worth trying.
