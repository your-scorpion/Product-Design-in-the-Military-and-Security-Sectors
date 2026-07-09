# Xcode for Interface Prototypes

> Translated and adapted from the Russian original by Maxim Tsvetkov.

## Contents

- [Why a designer should learn to build iOS layouts](#why-a-designer-should-learn-to-build-ios-layouts)
- [Auto Layout](#auto-layout)
- [First constraints](#first-constraints)
- [The toolbar beside Add New Constraints](#the-toolbar-beside-add-new-constraints)
- [Theory: spacing by the golden ratio](#theory-spacing-by-the-golden-ratio)
- [The Xcode interface](#the-xcode-interface)
- [Multipliers and proportional sizing](#multipliers-and-proportional-sizing)
- [Position constraints and the view hierarchy](#position-constraints-and-the-view-hierarchy)
- [The trouble with text, and Adaptive Layout](#the-trouble-with-text-and-adaptive-layout)
- [Screens and transitions](#screens-and-transitions)
- [For later: refactoring to a storyboard](#for-later-refactoring-to-a-storyboard)
- [What size to design for](#what-size-to-design-for)
- [Matching the mockup: type, shadows, colour](#matching-the-mockup-type-shadows-colour)
- [Working on Retina](#working-on-retina)
- [Base iOS sizes and the iOS 11 bar](#base-ios-sizes-and-the-ios-11-bar)
- [Caching, battery, and connectivity](#caching-battery-and-connectivity)
- [Deep links, App Clips, and notifications](#deep-links-app-clips-and-notifications)
- [iOS security and jailbreaking](#ios-security-and-jailbreaking)
- [Odds and ends](#odds-and-ends)

## Why a designer should learn to build iOS layouts

Why should a designer be able to build iOS app layouts? First and foremost, it improves the dialogue between developer and designer. Knowing the nuances and capabilities of development saves time and lets you use 100 % of the platform — for example, understanding that the launch screen works only from iOS 8 onward, and that for iOS 7 and below you must add a launch image to the app's asset catalogue, lets you do all the slicing in one pass without asking the developers irritating questions.

## Auto Layout

Many have heard the word **Auto Layout** from developers. It is the way iOS layouts are built. By default, if you add a coloured object to a phone screen (a View inside a View Controller), it pins to the top-left corner and does **not** change its size or insets across different iPhones — behaviour that answers the designers' tasks poorly.

![A view pinned to the top-left, unchanged across screen sizes](https://your-scorpion.ru/wp-content/uploads/2016/12/7586664н67474.png)

So that every inset and size matches the design, Auto Layout positions elements relative to one another. It is a technology built on the principle of constraints describing the interface, dynamically computing the position and size of every object in the UIView hierarchy from the constraints set for those objects. With it you can constrain an element to sit centred with its top edge always 8 pt below the View's top, say — and most importantly, if the screen size differs, Auto Layout places the object in the right spot automatically. That matters, because iPhone sizes differ greatly: the iPhone 5/5S portrait screen is 320 points (640 px) wide by 568 points (1136 px) tall; the iPhone 6/6S is 375 points (750 px) by 667 points (1334 px); the iPhone 6/6S Plus is 414 points (1242 px) by 736 points (2208 px); and the iPhone 4S is 320 points (640 px) by 480 points (960 px).

![iPhone screen sizes compared](https://your-scorpion.ru/wp-content/uploads/2019/12/Artboard-4.png)

## First constraints

Take the simplest possible task: we want an object centred on the screen that doesn't move. For that, turn off all the guides. Likewise, if a View is stretched to the full width and height of the screen with the guides off, you get a fully filled background.

![A centred object and a full-screen filled view](https://your-scorpion.ru/wp-content/uploads/2019/12/Untitled-1.jpg)

In most cases designers draw mockups with fixed insets and stretching content. To build that, use the Add New Constraints icon in the bottom-right corner of Xcode, which adds insets (constraints) to neighbouring objects or to the screen edges; constraints are shown as blue lines. You can also make them with a shortcut: select the object, hold Ctrl, and click the object the guides should be built against.

![Adding constraints with Add New Constraints](https://your-scorpion.ru/wp-content/uploads/2019/12/Untitled-1-1.jpg)

If the constraints turn yellow, look at the reasons in the yellow circle opposite the View Controller Scene in the right-hand menu. Yellow errors are not critical, but red ones will not let you compile the project. Say you manually changed a View's size and got yellow errors: choose "Fix Misplacement" from the yellow circle's menu and the object stretches to the sizes picked in the visual editor; the same result comes from "Resolve Auto Layout Issues." Designers love nudging elements a hair left or right in the GUI, and that breaks the constraints — it is enough to add or subtract the amount you moved the element from the constraint's setting, but it is more convenient to press "Update Constraint Constants" for the selected element.

## The toolbar beside Add New Constraints

Beside Add New Constraints sit other useful buttons. **Stack** places the selected interface details into a StackView, with Interface Builder deciding the StackView's axis from the elements' arrangement (you can also create a StackView by dragging it from the object library, like any other element). **Align** sets elements neatly to the grid — by the side, vertically centred, or at the bottom — the way you align text in a text editor. **Pin** is my main menu, letting you set firm bounds relative to an element's own size or its nearest neighbour, where you explicitly state which constraint you want in each direction and set its parameters, and can also give a group of buttons a constant size regardless of screen scale. And **Resolve Auto Layout Issues** handles the layout.

![The constraints toolbar in Xcode](https://your-scorpion.ru/wp-content/uploads/2019/12/Screenshot_3.png)

## Theory: spacing by the golden ratio

Your spacing should not follow a progressive system — 4, 8, 12, 16, 20, 24, 28, 32, 36, 40, 44 — because that yields too many inset options. It is better to use the golden ratio to build a geometric progression, giving a series of 2, 4, 8, 16, 32, 64. You can, of course, add 24 between 16 and 32, but only as an exception.

![Spacing built as a geometric progression](https://your-scorpion.ru/wp-content/uploads/2019/12/впв.gif)

By default, Interface Builder previews the UI on the iPhone 6S, and Apple's own templates are made for that size. For people who work heavily with graphics there is a useful shortcut, **Cmd + =**, which returns the height and width to their reference values. In the simulator you can scale the interface through Window → Scale; the important thing is to set the maximum sizes in Interface Builder.

## The Xcode interface

Interface elements use three basic models — active, static (inactive), and passive. A button is an active control, an image is a static element, and a text field is a passive one; depending on the model, a different set of capabilities is available in the Utility Area. Every constraint has properties, found on the Size Inspector tab (the element must be selected), and going to View → Utilities → Attributes Inspector you meet these:

![Constraint properties in the Attributes Inspector](https://your-scorpion.ru/wp-content/uploads/2019/12/Screen-Shot-2016-12-20-at-12.08.03.png)

`Relation` (relative to the Constant property), `Constant` (a fixed value that lets you change the object's size), and `Priority`, which ranges from 1 to 1000, where 1000 gives the constraint maximum priority.

## Multipliers and proportional sizing

A Multiplier of 1 means the element's size is 1:1, or 100 %; change it to 2 and you get 2:1, a 200 % increase. Using the multiplier, the constraint looks at all the space available to it: at 3:1 the whole space is divided into three and one part is assigned to the element; at 4:3 the space is divided into four and the element is given a scale of 3. The 16:9 and 4:3 ratios help adapt content for iPad and iPhone — and to make sense of it, 6:1 = 6, so replace the `:` with `/` and it all falls into place.

![A multiplier dividing the available space](https://your-scorpion.ru/wp-content/uploads/2019/12/sfldkajldamlaljlkwf.gif)

Elements can decide for themselves how much space they need based on their content. Repeat the constraints from the image to see how fluid layout is built.

![Content-driven sizing constraints](https://your-scorpion.ru/wp-content/uploads/2019/12/Untitled-1-2.jpg)

Constraints let you scale objects — adding a Height constraint to a Segmented Control, for instance, lets you increase its height.

![Increasing the height of a Segmented Control](https://your-scorpion.ru/wp-content/uploads/2019/12/идеи.png)

One standard design wish is to scale buttons and insets proportionally to the screen, which **Equal Widths** and **Equal Heights** make possible: Auto Layout limits the width or height — hold Ctrl, drag a line from one text block to another, and choose Equal Widths in the dialog that appears.

## Position constraints and the view hierarchy

Having practised, let's try to understand what is happening. Every control has a position and a size, and if you notice the "spacing to nearest neighbor" hint when creating constraints, you'll see that insets can be from the main view or from the nearest objects. If you add a UIView, you can regulate its dimensions not only through Height and Width constraints but through position constraints too — and it is important to understand that each constraint interacts with the object higher in the layer hierarchy.

![Position constraints relative to the container](https://your-scorpion.ru/wp-content/uploads/2017/08/sfldkajldamlaljlkwf-3.gif)

So you have Vertical Spacing to Layout Guide (pinning the image's top to the top of the window), Leading Space to Container Margin (the image's left side stays nailed to the window's left), Trailing Space to Container Margin (the right side stays nailed to the right), and Vertical Spacing (the vertical distance between the image and a label). Knowing these nuances, you can do complex layouts — to increase the distance between vertical elements, for example, you add a vertical-spacing constraint in Auto Layout and give it a greater-than-or-equal or less-than-or-equal attribute.

## The trouble with text, and Adaptive Layout

Everything above was about plates and standard controls, but text can cause far more trouble. Create two Labels in a UIView, select the top one, and apply these settings:

![Constraint settings for the top label](https://your-scorpion.ru/wp-content/uploads/2019/12/44.png)

For the lower Label apply the same, but set Bottom Space instead of Top Space. Switching between devices, you'll notice the text has spilled out of its allotted area. Shrinking the font isn't the answer, since a font size for the iPad Pro 9.7 may well be too big for the iPhone 4 — and I've often met the view among designers that in mobile apps it's better not to change the font size with content, because you can't foresee every problem, it's unclear how to design for it, and no one wants to draw every possible variant.

Adaptive Layout comes to the rescue. One universal Storyboard for the whole interface is an excellent solution: all screens in one layout are far easier to maintain than many variants of one screen, so a ViewController in Xcode exists in a Class Size environment with a set width and height. Class Size comes in two kinds, **Compact** and **Regular**, and in Auto Layout you can enable and disable constraints in the current Class Size — which is exactly what we'll use to solve the text problems. First, in the Attributes Inspector press the small + to the left of the Font field, choose Compact for Width and Any for Height, and a second font selector appears with the `wC` attribute, in which we can reduce the size — do this for every text block. Then press "Vary for Traits" and check the layout in Compact: you'll notice every iPad in portrait or landscape draws its width and height as Regular, where `wR` and `hR` mean Regular Width and Regular Height, and `wC hR` means Compact Width and Regular Height. Changing the Regular height of the iPad would affect the phone's portrait orientation, so to change only the iPad you work only with width — and a particular pain is the iPhone Plus, which is `wR hC`. Finally, in the inspector find the AutoShrink field, choose Minimum Font Scale, and add a value of 0.5 just below, so the text block shrinks automatically; you can test this scaling with AutoShrink → Maximum Font Scale (0.4) to compress the text by the number of characters in the block.

![Text scaling with Adaptive Layout and AutoShrink](https://your-scorpion.ru/wp-content/uploads/2019/12/sfldkajldamlaljlkwf-1.gif)

## Screens and transitions

Now you can build simple screens, but without transitions they have no value. You can create as many new screens (View Controllers) as you like and make transitions between them with a Segue, the way screens are changed; Xcode draws arrows between the screens, so you can easily see which follows which — Push, for example, is the classic right-to-left change. With this knowledge you can already build a prototype, which is enough to test a hypothesis.

![Segue transitions between screens](https://your-scorpion.ru/wp-content/uploads/2017/03/ezgif-3-4596fbb25b.gif)

## For later: refactoring to a storyboard

You've surely wondered what to do when the artboards grow too many and you get lost in your own prototype. Hold **Cmd** and click the screens' borders, and a blue outline appears; then go to Editor → Refactor to Storyboard. You'll be offered to create a new storyboard from the selected screens, and after creation you're thrown into the new storyboard, where you see the segues and the new artboards, while the moved screens in the main storyboard become a reference. Congratulations — you've refactored.

## What size to design for

Try to think more abstractly: as a designer you don't *draw* the interface, you *describe* it with the logic of sizes. Separate layouts for separate screens are warranted only to split iPhone from iPad — if you've drawn a button, you must describe all its edge distances. Scrolling is normal: the interface stretches in width and scrolls in height. If you need it without scrolling, the developer must walk the constraints and multiply them by `UIScreen.main.bounds.height / 568.0`, which adds load on old devices, but scaling the interface proportionally to screen height is possible. If you draw a design for every resolution, the developer has to add `UIScreen.main.bounds.size.height == <value>` and lay out each screen separately — a separate form factor for each resolution and orientation takes a very long time. If you settle on one screen size, I draw for the iPhone 5, because scaling up is easier than designing into a large screen and then trying to compress it for smaller ones; Anima's constraint plugin works well for checking the design at all resolutions. If you do draw at the large size editors default to, it's better to use the iPhone 8 at 1× (375×667) with constraints. Pixel-perfection on iOS died with the 5.5″ screen — on the iPhone 7+ you can't make a perfect 1 px line even if you set it explicitly, short of something like 0.3333 of a point.

## Matching the mockup: type, shadows, colour

To make the implementation match the mockup 100 % despite identical shadow or font parameters, consider the three core entities of modern design — typography, colour, and shadows. With typography you'll most likely work with the standard San Francisco font, where iOS mostly has trouble with tracking, whose values must be taken from Apple's guides (Sketch users have an excellent plugin for this, and in Sketch it's important to toggle text alignment between Fixed and Auto to reset a block's width and height to default). Shadows in iOS always come out larger than in Sketch because there's no Spread parameter, corrected manually on the development side by reducing the shadow radius and raising the opacity. Solid colours give no trouble, but gradients render darker than the designer expects, corrected by eye.

## Working on Retina

Retina is anything above 220 ppi. You can enable HiDPI Mode in macOS, effectively a 2× compression you may like, with this terminal command:

```
sudo defaults write /Library/Preferences/com.apple.windowserver.plist DisplayResolutionEnabled -bool true
```

The classic story of designers on Retina drawing microscopic fonts is easily solved with an extra low-quality monitor for checking mockups. If you draw mockups in Photoshop on Retina, you'll have to draw everything at 2× or look at blurry mockups.

## Base iOS sizes and the iOS 11 bar

On an iPhone X at 375×812 px, the tab bar is 83 px (including 34 px at the bottom for the Home Indicator), the navigation bar is 97 px, and the status bar is 44 px. The new iOS 11 bar is hard to customise, so don't add functionality to it that differs from the default — it includes a handsome large title, a search field, and a refresher. Apple's designers stretch the search field to the full Readable Width, depending on whether the interface is Master-Detail or Fullscreen; the large grey strip between content exists to separate the sections of a grouped table.

![Search-field behaviour on iPad, example one](http://your-scorpion.ru/wp-content/uploads/2017/12/2562626.jpg)

![Search-field behaviour on iPad, example two](http://your-scorpion.ru/wp-content/uploads/2017/12/6664.jpg)

![Search-field behaviour on iPad, example three](http://your-scorpion.ru/wp-content/uploads/2017/12/56.png)

These problems historically come from the insets for transparent bars and similar features, which is why developers dislike writing custom bars — Apple's automation doesn't work with them, so you have to make your own layout entirely, given that some capabilities aren't in the public API. I usually advise designers to accept that there is one Navigation Bar for all scenes before iOS 13: you can't make the navbar black on one screen and transparent on the next, with pretty animations on top.

## Caching, battery, and connectivity

Transferring money between two clients of the same bank who are physically near each other when one has no internet can, on iOS, be done with the new Bluetooth; for iOS and macOS the possibilities are almost limitless, while watchOS and tvOS have limits, and the system can hook into the accelerometer to lengthen or shorten the interval of connection requests — fine for transferring payment details and a photo, where one party doesn't even need the internet, simply handing over the payment data. The alternative is NFC, heavily limited on iOS but fine on Android. The avatar will appear for the data sender offline if it's cached, and content is usually cached on the Lazy and Synchronized-cache principle for texts, news, and e-commerce — though you can cache anything (avatars, GIFs in comments) provided the cache doesn't balloon past 100 MB, with video cached on the faststart principle; look toward write-through cache and LRU cache.

Optimising battery life from the design side comes down mostly to the screen, the biggest consumer. Trivially, if the screen is Quad HD (2960×1440) you can offer to switch to FHD+; an LCD (backlit) display drinks battery simply through its brightness, while on AMOLED the design style affects power draw — blue and white cost a lot of energy, black noticeably less. In 3D games the GPU eats battery hard, the camera comparably; the OS too consumes RAM, CPU, and GPU, and streamed video encoded by the device's hardware codec is far cheaper than a software codec. Music, by contrast, consumes very little, having its own coprocessor; GPS with Google's Fused Location Provider API helps by scanning the signal constantly when the device lies on a table; and a GSM connection is costly, though Wi-Fi and SMS cost more still, which is why a phone at rest overnight switches to 4G.

When content fails to load on certain carriers in certain regions, and it's a simple HTTP GET via Alamofire or RxMoya: on Android you can substitute the IP address for the domain, and on an SSL/DNS-looking error run the config through Firebase and send requests in Curl mode; on an SSL error with iOS 12, set the maximum TLS version to 1.2 rather than 1.3. You can log anonymised network metrics — though I don't always trust logs until I'm sure they sit in the right places, and never in the release configuration — logging to analytics services rather than storing on the client, or you risk an MITM attack.

## Deep links, App Clips, and notifications

Notification UI can be configured, with the source being a push or an in-app event. There's the old "Today" widget and force-touch on the icon, where the widget can hold anything, limited only by size — but the iOS 14 SDK changed everything, and your app's icon may no longer be on the home screen, flying off to the App Library. The good news is App Clips: the user simply uses an NFC tag, a banner, a QR code, a link, or Siri to launch the app, with many limits on such access (10 MB weight, trimmed frameworks, data passed only to your main app, all strict) — and since it's tied to a real place, it's better to require confirmation of being in the store before payment. You can also add app capabilities to Siri through Siri intents, NSUserActivity plus Wind-down, Siri recommendations, and Shortcuts integration.

A Deep Link is a link into the app, used to drop the user onto a target screen — open a letter on the desktop and you redirect to a landing page, on the phone into the app. Deep links come in three types: basic ones, the simplest, opening either a page in the app, the App Store, or a promo page; deferred ones, which collect data from the device when the app isn't installed and form a fingerprint, so once it's installed we already know which page to show; and contextual ones, which collect all the user's data and show a personalised onboarding by the link. iOS supports URL Schemes like `tg://` and `superapp://`, which fail if the app isn't installed and whose odd URLs unsettle users, while the more modern Universal Links (iOS 9+) look like ordinary links and open an app screen or any page — done similarly on Android, where http may be used instead of https. To put an external-site link in an AppMetrica campaign, the campaign setup has three options — App, Deeplink, and URL — and the last launches the browser, opening an `SFSafariViewController` (a browser embedded in the app).

## iOS security and jailbreaking

iOS is secure if you follow simple advice: never give anyone physical access to the device, set a passcode, turn off JavaScript in the browser, store nothing important on the phone, install no suspicious apps, update everything, turn off iCloud, enable "Ask to Join Networks," turn off Siri and voice dictation, and don't open links — otherwise there's a chance you become a victim.

On bypassing the restrictions, there's the level of user apps and the hardware, with iOS literally a layer between them; apps don't work with the hardware directly, only through the iOS layers, which are: Cocoa, the application layer (the app front end, pushes, badge notifications); Media (OpenGL, 2D and 3D rendering); Core Services (security, peer-to-peer, for games and content transfer); Core OS (system configuration, OpenCL); and the kernel (BSD, the file system, networking, drivers, DNS access). To bypass restrictions there's jailbreaking, a kind of root for iOS but not quite — root in iOS and root in Android differ — which simply lets you work more flexibly with the OS's built-in rules and removes limits, done by installing a customised kernel with root access; afterwards you can install apps Apple wouldn't allow, which is the risk that an app gains access to protected data, and it's realised with apps like Cydia (a tweak store for jailbroken devices), Hexxa Plus, ApricotiOS, Yuxigon, Sileo, and Trimgo. There are different jailbreak types — one leaves the vulnerability permanently, another is cleared by a reboot, and a third needs a computer to boot the device successfully after the jailbreak. In general, regular reboots, Lockdown Mode, and BlastDoor help, though not against attacks through built-in libraries: Apple provides the Core ML engine for photos, and the larger Apple Foundation Models, and any developer can use Core ML by feeding it data and dragging an `.mlmodel` file into Xcode — which some use for illegitimate ends.

## Odds and ends

Transparent `.DS_Store` files left after moving files from a Mac to Windows are removed from `cmd.exe` with `del /s /q /f /a .DS_STORE`. If a Mac won't let you launch a file, you can try PermissionsReset (risky). And if code won't run for lack of memory on a laptop, there's the ancient ReadyBoost route — insert a flash drive, open its Properties, and on the ReadyBoost tab choose "Dedicate this device to ReadyBoost," setting the slider to maximum — then hand memory to the page file (more RAM means a smaller page file: roughly 5012 MB at 512 MB of RAM, 4012 at 1 GB, 3548 at 2 GB, 3024 at 4 GB, 2016 at 8 GB). Paging works on the old principle: a computer might address up to 16 MB while having only 4 MB of RAM, so some segments were swapped to disk and reloaded when needed, letting a program use more memory than the RAM could provide.

![The Windows ReadyBoost tab](http://your-scorpion.ru/wp-content/uploads/2021/04/s.png)
