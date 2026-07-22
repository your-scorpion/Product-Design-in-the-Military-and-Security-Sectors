# Units of Measurement in iOS and Android

*Maksim Tcvetkov · Product Design and Security Lead*

Designing and optimizing how a site displays on mobile devices is a mandatory part of building a site nowadays. The world has changed; the new generation no longer imagines the internet without mobile devices. And once again it is very important to account for content load speed. Yes, the broadband 100 Mbit went down the drain, and ideally sites once again weigh 150 KB (mobile ones only, of course! }: ). A project starts with UX (design), sometimes with a business analyst involved, and they must decide how the product will look across all devices.

Since everything has to scale across a multitude of devices, it is important to understand how content scaling works. Let us begin with the units of measurement.

## Units of Measurement

**Pixel** — the most artless option, and by now rather impractical: on a 320×240 screen an 8×8 px button becomes unforgivably small, barely noticeable, and not at all button-like.

**In/pt** — these units will not let a button scale, and it may simply not fit on the screen. Technically this unit is closely tied to pixel density on mobile devices. A pt is a point, not a pixel. At "1x" resolution (@1x), 1 pt = 1 px. At "2x" (@2x), 1 pt = 4 px, because the resolution doubles along the X and Y axes, making it 2 px wide and 2 px high. At "3x" (@3x), 1 pt = 9 px (3 px × 3 px), and so on.

**Dip** — now this is our man: abstract pixels independent of screen density. The number of pixels in a physical screen area is usually called DPI (dots per inch). Dp is for the dimensions of an on-screen element; sp is for font size. On a screen with a pixel density of 160 dpi (mdpi), 1 px = 1 dp. This ratio changes as pixel density changes across devices, but the proportional relationship stays the same.

DPI can be described differently too: it is a coefficient showing how many pixels fit into one centimeter (or inch) of a print or a screen. Resolution is measured as the number of pixels per inch — ppi (pixels per inch).

Let us give a more sensible definition of the density-independent point (DP). These units help make your layouts independent of screen pixel density. One DP equals one pixel at 160 dpi (the medium screen density). When an app is running, the system handles changes in screen scale according to the density of the screen in use, and adjusts parameters by the formula `1 px = dp × (dpi / 160)`. On a 240 dpi screen, 1 dp = 1.5 physical pixels.

## Adaptive (Responsive) Layout

The goal of adaptive web design is to let you view one and the same web page across the whole variety of mobile devices without serious display glitches. I will simply note the main advantage: an adaptive site recognizes the browser's width and adjusts to it, whereas mobile sites have to recognize the device — and devices keep multiplying, so the device-detection script has to be updated constantly. For adaptive layout you simply need to think through three page types for screens of width 1024 (computer), 768 (iPad in portrait), and 320 (iPhone in portrait). Many apps have two orientations, landscape and portrait. The iPhone 4 has the new "retina" display, which doubles the number of pixels within a single screen, up to 2048×1536. You should design for Retina first and then shrink to the ordinary screen (−50%). This means you also need to create two sets of icons for your layouts: initially the icons will be at 163 ppi, but you need to include icons at 326 ppi for the iPhone 4. To tune for the device's various screen densities, we should follow the scaling ratio 3:4:6:8 for the four density sizes (for the iPhone it is easy: a 2:1 ratio between the 4 and the iPhone 3GS). Icons are traditionally marked with @2x at the end of their file names, e.g. `home@2x.png`.

## Design Sizes

A designer should draw sites to the following widths:

| Width | Target |
| --- | --- |
| 320 px | Mobile devices in portrait |
| 568 px | iPhone 5 |
| 640 px | Small tablets |
| 768 px | Tablets in portrait |
| 1024 px | Tablets in landscape, monitors in government offices |
| 1280 px and up | Desktop computers |

These breakpoints describe the layout grid; the actual device resolutions, meanwhile, kept fragmenting in the years after. The two most relevant resolution sets to design against are, for Android, 480×800, 480×854, 540×960, 720×1280, and 1080×1920; and for the iPhone, which has also fragmented over the years, 640×960, 640×1136, 750×1334, and 1242×2208.

## Navigation and Touch Targets

Let us talk about buttons. Data is no longer entered from a keyboard, and you cannot click with a mouse, so big buttons beat small ones — a finger lands on them more easily. You want movements that are natural for a person, with a reaction and a change always visible. And yes, it is good when links are buttons rather than a heap of blue underlined lines, and you should not skimp on font size. But how big should buttons be? 44×44 px for iPhone, 48 dp for Android, 36×36 for Windows Phone; Nokia, for its part, recommends a button of no less than 1 cm. That is straight from the official guidelines, in which even the units differ. So I will share a conclusion from my own experience: a button presses well if it is 45–75 dp, depending on the task. The minimum touch area is 7×7 mm, and the average finger is 25 mm wide. But do not ignore the guideline recommendations — if you realize you cannot rely on your own experience, follow the guidelines (and you still remember that the real size depends on the pixel density of the specific device :).

As you understand, bigger buttons in an interface mean fewer buttons fit on the screen — and that is good for mobile apps. After an epic tap on a convenient button, the user moves to some page. This is an important moment, since a person must clearly grasp where they are, where they came from, why, what for, for whom, what, and how to go back. iPhones and iPads use a very correct, cinematic device — a right-to-left transition and back — which preserves the visual link in the structure of the interface's windows.

## Numbers for Mac People (iOS)

**iPad:** 768 × 1024 px, 1024 × 768 px; screen resolution 72 ppi; file format PNG-24. The iPad can run any app built for the iPhone, but in that case they are simply scaled up to the larger screen. Icon size: 72×72 px, corner radius 12 px.

**Other iPhone and iPod touch devices:** 320 × 480 px; screen resolution 72 ppi; icon size 57 × 57 px, corner radius 10 px; file format PNG-24.

**Graphics for the App Store:** icons 512 × 512 px (.tif, .jpg, or .png, 72 dpi, RGB — it matters to convey the core idea); iPhone screenshots 320 × 480 px or 640 × 960 px (.tif, .jpg, or .png, 72 dpi, RGB); iPad screenshots 1024 × 768 px (.tif, .jpg, or .png, 72 dpi, RGB).

**Search icons:** size 29×29 px, corner radius 5 px.

**For iPhone 6:** 750 × 1334 (@2x) portrait, 1334 × 750 (@2x) landscape.

**For iPhone 6 Plus:** 1242 × 2208 (@3x) portrait, 2208 × 1242 (@3x) landscape.

Launch images: Default@2x 640–960; Default-568h@2x 640–1136; Default-667h@2x 750–1334; Default-736h@3x 1242–2208. In sum, the backgrounds should be 640×960, 640×1136, 750×1334, and 1080×1920.

Icons are made in these sizes: 512×512 (iTunesArtwork), 57×57 (Icon.png), 114×114 (Icon@2x.png), 72×72 (Icon-72.png), 29×29 (Icon-Small.png), 50×50 (Icon-Small-50.png), 58×58 (Icon-Small@2x.png), and 144×144.

For the macOS menu bar you need to draw a set of white/black and retina/non-retina files in the ICNS format.

If you also need the **file-type icon** sizes for iOS, they are: on iPad, 64 × 64 px and 320 × 320 px; on iPhone and iPod touch, 22 × 29 px and 44 × 58 px.

While I am at it, let me answer a regularly recurring question about modular grids: 44, 88, and 176 are good values for designing modular grids. Lay your buttons and content out on that grid and everything will be fine.

## Designing for Other Devices

But the mobile internet does not live by iPhones alone. More and more mobile devices are reaching the market, and the number of touchscreens keeps growing. Although we have been talking about designing iOS apps, designers should account for other devices too. To avoid redrawing the site for each device, keep everything in the source files as shape layers or vector smart objects; then you will have no serious trouble scaling.

## Android

Now let us give a little attention to Android. How do iOS and Android differ? One has tabs on top, the other on the bottom. And the units of measurement are quite tangled. Android has four main screen densities: LDPI (low), MDPI (medium), HDPI (high), and XHDPI (extra high, since Android 2.2); they indicate how many pixels fit into one inch. At a minimum, a designer should hand you MDPI and HDPI graphics sets for any smartphone app (I usually limit myself to MDPI, HDPI, and XHDPI).

A common question here is what to do with complex shapes in Android — cut a heap of sizes, or make the developers implement the shape. It depends on the developers' skills: any shape can be implemented, since in the end it is no more than a contour described by formulas. The catch is that device performance may sag, so it is sometimes more sensible to spare the phones and stick to standard shapes. *Sometimes.* Android easily draws a solid circle; a solid (outline) ring; a solid filled rectangle; a solid (outline) rectangle; a solid filled rounded-corner rectangle; and a solid (outline) rounded-corner rectangle. Anything else you will have to make either as a composition of those figures, or by drawing something entirely your own.

## Android Screens, Aspect Ratios, and Font Sizes

I will try not to get bogged down in detail and just give the figures you need. Android has the following screen sizes:

- **xlarge** screens are at least **960 dp × 720 dp**
- **large** screens are at least **640 dp × 480 dp**
- **normal** screens are at least **470 dp × 320 dp**
- **small** screens are at least **426 dp × 320 dp**

Experience shows two most popular aspect ratios: (a) 1.77 (480×854, 960×540, 1280×720) and (b) 1.6 (480×800, 1280×800).

Font sizes, meanwhile, often turn out rather unattractive:

| Size | ldpi | mdpi | hdpi | xhdpi |
| --- | --- | --- | --- | --- |
| **17sp** | 12.75 px | 17 px | 25.5 px | 34 px |
| **23sp** | 17.25 px | 23 px | 34.5 px | 46 px |

SP means scaled points, tied to physical units. Type should be the same size on your phone, on an old phone with a small screen, and on a fine, expensive tablet. Font size is not dictated by screen size — that is the classic mistake.

## Recommended Sizes, Resizing, and Naming

Below are Google's recommendations. These values will help you avoid fractional numbers on resize:

- mdpi — 24
- hdpi — 36
- xhdpi — 48
- xxhdpi — 72
- xxxhdpi — 96

For slicing, only density matters: dp = 1 px at mdpi. With fonts the situation is the same, but the unit is sp. The rhythm is 48 dp. And these are the sizes I use — it is up to you which to use for your own mobile project:

- mdpi — 36
- hdpi — 54
- xhdpi — 72
- xxhdpi — 108
- xxxhdpi — 144

How to resize? Here is an example:

- 24 × 24 for low-density screens (i.e. × 0.75) — ldpi;
- 32 × 32 for medium-density screens (our base 1 × 1) — mdpi;
- 48 × 48 for high-density screens (× 1.5) — hdpi;
- 64 × 64 for very high-density screens (× 2.0) — xhdpi.

And for the rest of the picture: *low-density* (ldpi) screen (~120 dpi); *medium-density* (mdpi) screen (~160 dpi) — the base; *high-density* (hdpi) screen (~240 dpi); *extra-high-density* (xhdpi) screen (~320 dpi).

When naming the sliced graphics, follow the rules: lowercase, no spaces, no hyphens, nine-patch files with a `.9` before the extension. Underscores are allowed.

As of now it is relevant to also produce an xxhdpi slice; since 2014 I do not hand over projects without that screen density.

## Density Formulas

Accordingly, the formulas for getting the sizes (160 is mdpi):

**ldpi** — Vertical = 426 × 120 / 160 = 319.5 px; Horizontal = 320 × 120 / 160 = 240 px.

**mdpi** — Vertical = 470 × 160 / 160 = 470 px; Horizontal = 320 × 160 / 160 = 320 px.

**hdpi** — Vertical = 640 × 240 / 160 = 960 px; Horizontal = 480 × 240 / 160 = 720 px.

**xhdpi** — Vertical = 960 × 320 / 160 = 1920 px; Horizontal = 720 × 320 / 160 = 1440 px.

As you have gathered, the formula is `px = dp × dpi / 160`.

## Performance and Load Optimization

The introduction raised load speed, and that opens onto two practical questions readers keep asking: how to make a heavy page load fast, and how to specify an app's runtime performance.

### Page-load optimization

Modern sites weigh a megabyte or more — to say nothing of large portals with a long history — so does a heavy site have to "die"? Not at all; it can all be optimized. First, you must develop an adaptive version of the site — this is mortally important for any business. Second, the web standards gained the abbreviation AMP (thanks to Google), short for Accelerated Mobile Pages; this technology helps overcome the shortfall of mobile-internet speed by optimizing the page. In effect it gives a new, alternative presentation of the desktop site's content: the AMP page exists in parallel with the heavy desktop version, the two linked by a special tag in the header. It works on this principle — a heavy site with a pile of tracking code, GIFs with frame-by-frame interframe, video, banners, and other heavy things is transformed into a light, fast-loading version; content comes first, then video, and only then advertising, so advertisers' revenue may start to fall. If your site has no excess code and you relied strictly on best practice in development, this technology will not speed loading up much. Third, a site almost certainly weighs a lot because of images: the HTML `Picture` element has width descriptors (`400w`, `800w`, `1200w`) and much else useful, which will strongly optimize the volume of images loaded on mobile devices.

Are there ways to optimize loading without AMP and without reducing graphics weight — since those are too obvious and carry losses? First, what counts as "load optimization" for you, and when, from your point of view, is the site loaded? If one character has appeared, the site is already loading, but it is still not useful. That was the lyrical digression; now to business. Chrome's developer console has a Performance section where you can see how resources load and how your site looks at every stage of loading, and simulate CPU and network throttling. Watch how your site loads and optimize. Then you can apply lazy loading. Beyond images, you can also optimize the loading of web fonts: there are now the `link rel="preload"` and `as="font"` properties, which let you load a font at the moment it is needed (usable for video too, with `as="video"`). If things are bad and for the first seconds of loading the site is white, with one image and no text, that is solved by the new `font-display: swap;` property, which shows the text in whatever font it finds on the user's device. And so the whole text does not jump while the font loads, use `font-display: fallback;`, which tells the browser to load the font if the load takes no more than 100 ms, and otherwise to use the device's font. Developers should also look toward HTTP/2 Server Push, JS parsing, reducing bundle size, and server-side rendering — measuring everything with Google Lighthouse and the Navigation Timing API.

### Runtime performance and how to spec it

To frame an app's performance requirements in a technical specification, the metric for working speed is FPS. You can state a vertical line = 16 ms (for 60 FPS) and a minimum value of 30 FPS at least, for a sense of smooth operation. Computing your values is not hard: 1 second = 1000 ms, so 1000 / 30 = ~33.3 ms. This can be read as: for 30 FPS you must compute, animate, and render a frame in less than 33.3 ms, and repeat it 30 times; for 60 FPS, 1000 / 60 = ~16.6 ms. Find out whether you can use the GPU instead of the CPU — the GPU is faster at mathematical operations and can do many floating-point operations in parallel, but demands more memory, while the CPU works more reliably but slower (and the GPU can render an image with bugs, though that depends heavily on the API version — 16 at minimum). You can also work from the opposite end and count the delay from the last rendered frame, or start a timer and measure whether it elapsed within a set interval. For a more systematic approach there is a set of metrics called RUM — Real User Monitoring — measured on real users in production.

## Other Platforms and Links

I hope this information answers all your questions — if you are not a beginner, of course. There are also the likes of Java ME (weak, small, ungainly), Windows Phone (cool API, C#, little used in Russia), and Symbian (Java, Qt, unsupported), but the main giants, Android and iOS, demand the most attention.

And a few links on the topic:

- [Microsoft guidelines](http://msdn.microsoft.com/en-us/library/windows/desktop/aa511440.aspx)
- [Apple guidelines](http://developer.apple.com/library/ios/#documentation/userexperience/conceptual/mobilehig/Introduction/Introduction.html)
- [Android guidelines](http://developer.android.com/design/index.html)
- [Em/Px calculator](http://type-scale.com)

---

<sub>Translated from the Russian original by Maksim Tsvetkov. The author's reader Q&A — on handling complex shapes in Android, current Android and iPhone screen resolutions and iOS file-type icon sizes, optimizing heavy pages (AMP, the Picture element) and load without AMP (DevTools, lazy loading, font-display, preload, HTTP/2 Server Push, Lighthouse), and specifying app performance (FPS, GPU vs CPU, RUM) — has been folded into the sections above. Device-specific figures reflect the author's original, 2012-era advice.</sub>
