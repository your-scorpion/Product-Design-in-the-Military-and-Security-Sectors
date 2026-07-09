# Automating Asset Slicing for Mobile Devices

> Translated and adapted from the Russian original by Maxim Tsvetkov.


The designer is often handed the technical work of slicing graphics for iPhone and Android — pixel-perfect, naturally, since the graphics are the main means of expression. Slicing eats an enormous amount of time, time better spent working with your head than with your hands. Happily, the process is automated in a great many programs and plugins. You may have no wish to master new tools and can afford to hire an outsourcer; he will slice the images well enough, but the work has to be checked, the tickets with corrections written, the graphic optimisation watched, and the man paid, so the time saved evaporates. Let us look instead at the tools the market offers for this routine.

## Getting the colour right first

Before anything else, set the correct colour settings in Photoshop. The saga of getting image colour to display correctly drags on for a long time, and it is funny and sad to watch famous web designers, even print specialists with twenty years' experience, step on the same rake — the one called the colour-management system (CMS) — again and again.

Browsers do not work very well with colour management. The standard rule is that the CMS engages only when it meets a profile embedded in the image; everything else is shown to the user as is, with the browser assigning the monitor's profile, which distorts the colour. And a monitor profile may legitimately be assigned to an image only when a monitor created the image — a screenshot. So a profile-less image displays differently across browsers: some assign it the sRGB working space of the internet, others assign the user's monitor profile. Only Photoshop shows colour correctly — that is an axiom; everything else can lie even with an embedded profile. The best solution is to follow the rule that *an embedded profile guarantees the picture displays the same everywhere.* Set the following so you see the layouts in approximately correct colour:

![Photoshop colour settings, part one](https://your-scorpion.ru/wp-content/uploads/2014/12/La-Maison-de-L-Espadrille.png)

![Photoshop colour settings, part two](https://your-scorpion.ru/wp-content/uploads/2017/12/photoshop.gif)

And keep in mind that the monitor profile in the operating system should be built by an instrument after hardware calibration; if there was no hardware calibration, use the monitor manufacturer's profile.

A related trap appears the moment you place an image into a smart object and it scales, losing pixel-perfection. The fix is in Photoshop's preferences: under Edit → Preferences → General, uncheck "Resize image during place," and make sure the DPI is the same everywhere.

## The manual way, and the tools that replace it

With colour settled, back to the routine. To slice icons, "specialists" usually copy the vector shapes into separate files, press Cmd+Shift+R, save each through Save for Web, resize, chase pixel-perfection, and rename the files by hand. A perfectionist designer might instead lay down a heap of slices, arrange every icon neatly on the canvas, and name the layers cleanly. I have nothing against slices, by the way, but they are justified only in production where the layout's palette might change at the very last moment, in which case ready slices let you re-slice quickly. Our goal today, though, is to save time in less uncertain production. Consider the following programs.

**Sketch** is a large, paid vector editor that exists only on macOS, with auto-save, versioning, and artboards. It exports graphics in every size you need and adds prefixes to the file names. Beyond the standard iOS export, one wonderful plugin lets you slice all the Android graphics in a single click, from mdpi to xxxhdpi. Looking at the market as a whole, Sketch paired with the excellent Zeplin for specs and Craft for filling layouts with content can be considered the core toolkit for a UI/UX designer — and Zeplin can in fact slice your layouts itself, without your involvement. (The full set of Sketch hotkeys lives at sketchshortcuts.com.)

![Sketch's export panel](https://your-scorpion.ru/wp-content/uploads/2014/12/cketch.png)

**Assistor PS** does almost everything. **Resonator** places all the icons from the source files into columns in an intermediate file, Res-Project.psd, resizing for every screen density along the way; you do the pixel-perfect finishing in that file and only then save the icons, the bonus being that the whole "slicing" lives in one file rather than scattered across folders when an icon needs reworking. **Cut&Slice me** automatically converts sizes from the largest down to the smallest, so the layout must be at the maximum size (XXXHdpi or @3x); if you draw at a smaller size, the 2XScaler plugin helps. Cut&Slice me creates a folder named `<filename>_Android_assets` and drops the ready sets into the matching size folders; for it to work, put the graphics to be sliced into a folder and end the name with `@` — `FolderName@`. It is handy for icons, and free.

![Cut&Slice me output folders](https://your-scorpion.ru/wp-content/uploads/2014/12/sss.png)

**LayerCraft** is the most ordinary slicing plugin: it can resize the document to 200 % or 50 %, trim transparent pixels, and slice the iOS sizes (1x, @2x, @3x) and Android sizes (Mdpi, Hdpi, Xhdpi, XXHdpi, XXXHdpi) from the selected layers. Here it helps to know a small Apple nuance: take the original non-Retina iPhone as scale factor 1, the Retina iPhone 5 as scale factor 2, and the iPhone 6 Plus, with its Retina HD display, as scale factor 3 — where the image is downscaled from the logical 2208 × 1242 to 1920 × 1080. Pixel-perfect graphics then stop being pixel-perfect, because 1920⁄2208 = 1080⁄1242 = 20⁄23; in other words, the phone's screen is scaled to about 87 % of its original size.

## Hitting the pixel grid

Back in Photoshop, set the right checkboxes.

![The correct Photoshop export checkboxes](https://your-scorpion.ru/wp-content/uploads/2017/12/575-1.png)

Next, make sure you have an iPhone 6 Plus, xScope, or other software to check how the graphics render on the device. Paste the vector icon as a Shape Layer, shrink it to 20 %, and use the Direct Selection Tool to snap the contour to the pixel grid; then select your icon and scale it to 500 %, and you have pixel-perfect graphics at non-Retina size. Now the funny part: you must always scale an icon from the top-left corner, watching that the X and Y values stay even. This habit keeps your graphics sharp under automatic resizing by any plugin, and observing the multiplicity of all distances solves a great many problems. On output the grid should be 12 px for XXXHdpi and 6 px for the iPhone 6 Plus.

![Pixel-perfect icon scaling](https://your-scorpion.ru/wp-content/uploads/2017/12/6471.png)

Alternatively, you can draw the layout for the iPhone 6 Plus and follow the resize principle 4 px → 2 px → 1 px.

## Drawing icons that look right

Rotating icons such as Reply or Update need their *optical* centre found, because on a great many icons the optical centre is offset from the geometric one.

![The optical centre of an update icon](http://your-scorpion.ru/wp-content/uploads/2015/06/correct_crop_icon.png)

And don't forget the correct alignment of icons relative to one another.

![Correct alignment of icons relative to one another](http://your-scorpion.ru/wp-content/uploads/2015/07/best_align3.png)

The same principle works in the figure eight: make both of its loops the same size and the upper one will look bigger. Then there are the utterly basic matters of pixel-perfection.

![Proper pixel-perfect alignment](http://your-scorpion.ru/wp-content/uploads/2015/11/pixel_perfect.png)

It is also worth remembering that circles come in two kinds — geometrically correct and optically correct — and they are two different circles. Find out which kind your editor draws; professional tools like FontLab 6 can draw both and have a special Curve tension setting for regulating the optical distortion, where a Curve tension of 55 % gives a geometrically correct circle. Triangles are better made with 60-degree angles.

![A triangle with 60-degree angles](http://your-scorpion.ru/wp-content/uploads/2022/01/Asset-1.png)

## Measuring and spec tools

**PNG Express** is considered the best of the available plugins for dimensioning a Photoshop layout, and at a very reasonable price (in dollars). Say you have made a row of buttons with several states: by creating a group containing the layers for each button and adding the prefix "PNG" to the group's name, you tell PNG Express which layers to export; by adding "STATE:_on" or "STATE:_off" to the relevant layer names, you make it export and name the resulting files by state. So in a group named "PNG:map" the layers "STATE:_on" and "STATE:_off" become `map_on.png` and `map_off.png`. Adding the suffix "–m" to a layer or group name merges it with all visible layers, which is very handy when your project has layers with soft transitions and interactions, and the plugin can also export at exact dimensions you specify rather than only the sizes it would choose itself. **Specking** displays the width and height of blocks plus margins and padding — fine if your programmers demand a marked-up layout, though it is more convenient to teach them to set the layout as a background and make the text a contrasting colour (usually yellow or green) so the difference is visible at a glance, which takes a couple of minutes and needs no mark-up at all. And if you are a hipster, there is the full analogue Specctr, used by people at Apple, Google, and Autodesk.

## Naming and structuring source files

Now a very important point. There is such a thing as a programming style guide, and among much else it advises on the proper naming of sliced files. The gist: do not dump all your sliced iOS graphics into one folder, naming the icons `close.png`, `close_popup.png`, and so on. Every tool and plugin above works its magic only if the designer keeps the source in perfectly structured order and does not merely *simulate* order by colouring groups and scattering little locks. Train yourself to keep sources tidy and to append prefixes and suffixes to icons — it is your job to do everything technically clean and correct. In the more fashionable editors, the omega symbol (Ω) holds an element at the very bottom of the component list and alpha (ɑ) at the very top. Some can ignore all this — the star art director, say, who created a cool concept and handed it to the lead designers; they will bring every offset to a multiple value, rename the layers, structure the nesting, and write all the names without spaces and in lowercase. Here is an example of reasonably sensible file naming from one of my iOS apps.

![Well-structured asset file names from one of my iOS apps](https://your-scorpion.ru/wp-content/uploads/2017/12/name.gif)

The rules, in short: no Cyrillic, no spaces, lowercase only, and it makes sense to create an `in` folder for all incoming requirements, an `out` folder for the final design, assets, store graphics, and animations, and a separate folder for the designers, where the sources live.

If you work in Sketch, the same discipline runs through the symbols. Guidelines form automatically into symbols, and to structure them you name the symbols in the form `Base/element/top_line`, which builds sub-folders of symbols.

![Symbol sub-folders in Sketch](http://your-scorpion.ru/wp-content/uploads/2016/07/Screen-Shot-2016-07-11-at-13.21.03.png)

Two practices keep a large file sane. First, templates plus a symbolic link: make a master file (File → Save as Template) with your app's guidelines, then create a symbolic link between your templates folder and a folder in Dropbox, and move the guideline file there; whenever you need buttons or colours from it, open File → New from Template and get a copy of the original, so you cannot spoil the guidelines by accident (the catch being that if you update the template in Dropbox, the symbols in local projects do not update). Second, name symbols by the same logic, `Buttons → Round → Active → Accent color`, and pull colours out into a separate style so you can recolour every symbol at once. Just as important is exporting the slices straight into the right folders: put the folder path in the suffix, so `/folder/file_name` creates a folder named after the layer with `folder` inside it, while `/../folder/file_name` skips the layer-named folder and a construction like `icon/` creates a folder.

![Exporting Sketch slices into the right folders](http://your-scorpion.ru/wp-content/uploads/2017/01/sketch_correct.png)

Text styles should be structured the same way — `Desktop/Header`, `Mobile/Header`. For managing whole icon sets there are services like geticonjar and svgsus; lately I use nucleoapp and am happy with it, but I would also point to lingoapp, which integrates beautifully with Sketch. You can also build an icon library on Sketch components — make solid colours as separate symbols and add them as masks to the icons, changing colour through overrides — though that brings SVG-export trouble, fixed by running through SVGO first and then editing the code or automating it. For exporting from Photoshop, expresso-exporter is the current choice.

## Photoshop Generator

Why the paragraph on file naming? Because Adobe gave us the wonderful option of not slicing graphics at all, but simply naming the layers correctly. From CC onward, Photoshop can save the graphics itself in real time: you rename a layer and the image is saved. Enable File → Generate → Image Assets and put a postfix on the layer name in the form of a dot and a file extension (`.jpg`, `.png`, `.gif`). Need three iOS sizes? Easy — add a plus: `100% icon.png32 +200% icon@2x.png32 +300% icon@3x.png32`. And the Android template is `100% icon_xxhdpi.png32 +67% icon_xhdpi.png32 +50% icon_hdpi.png32 +33% icon_mdpi.png32`.

As you can see, graphics can be scaled simply by giving a percentage, and the permitted units are pixels, centimetres, millimetres, and inches; with no unit, Photoshop assumes pixels. Generator is an elementary tool with one more lovely function — proportional resizing. Want an image 350 px wide with the height fitted to that width while preserving the ratio? Put a question mark in place of the height or width: `350x? image.png`.

If you would rather not let go of the mouse for the keyboard, even this was automated, by the **oven** plugin, which has the iOS templates (@1x, @2x, @3x) and Android (ldpi, mdpi, hdpi, xhdpi) baked in; from Photoshop v15.2 the plugin is no longer relevant, and from v14.2.1 some bugs in how adjustment layers were counted were fixed, allowing export by the mask's dimensions. **Slicy** for Mac is still more convenient for naming layers, though that is more a matter of habit. One limitation worth knowing: oven does not work with artboards.

And when you need to generate an image whose canvas size differs from the image size, there are two routes. In Photoshop CC 2015 there is Export As.

![Generating images at a size different from the canvas, Photoshop 2015](http://your-scorpion.ru/wp-content/uploads/2015/08/Screenshot_6.png)

The other route is artboards — possible in Sketch through its artboards and, since the new Photoshop copied almost everything from Sketch except the performance, in Photoshop 2015 too.

![Artboards in Sketch](http://your-scorpion.ru/wp-content/uploads/2015/08/icon.png)

You create an artboard of the needed size, drop the icon into it, and give the artboard a name that follows the Photoshop Generator syntax, then go to File → Generate → Image Assets, and you are done.

![An artboard set up for Generator in Photoshop](http://your-scorpion.ru/wp-content/uploads/2015/08/Screenshot_7.png)

## Slicing icons with Generate

In practice, Generate is excellent for automating icon slicing. App icons usually consist of a background and a symbol, and three rules keep them clean: put each icon in its own group, named after the final file; make the shared background a Smart Object while drawing the symbol separately for each icon size, to keep it pixel-perfect; and remember that for Apple devices the slices must be square even though the icons appear round or square-with-rounded-corners on the device — apply a mask of the needed shape to the icon's layer group, and the slices will generate square while the icon still displays in the right shape in Photoshop thanks to the mask.

![Slicing app icons with Generate](https://your-scorpion.ru/wp-content/uploads/2014/12/gif_lesson_crop_photoshop.gif)

One more thing even the mastodons of the trade overlook: an image must not become a *different* image because the screen's quality changed. If your icon is 12 × 12 px at mdpi, you must fit the icon's form into that size regardless; the icon should be the same at xxxhdpi and at ldpi.

Icons for a particular platform also carry their own specs. For the macOS status bar, for instance, the icons must be single-colour, black, with opacity; the colour inverts automatically when the bar switches from black to white, and the size is 42 px tall for Retina. (The bar, incidentally, is correctly named and googled as NSStatusBar.)

## Extract Assets

Adobe gave us another built-in tool, Extract Assets. Select the layers or layer groups you need, right-click, and choose Extract Assets from the menu; you can add scaling factors and automate the layer naming, with export to PNG-8, PNG-24, PNG-32, GIF, JPG, or SVG, and if you gave several layers the same name they are renamed automatically.

![Extract Assets in Photoshop](https://your-scorpion.ru/wp-content/uploads/2014/12/7577.png)

You can also look at the plugins less common on the local market: Breezy (free), Slicy, PNG Hat, Slash, Lasagna, and Export Pro.

## Densities and target sizes

How many sizes you actually slice depends on the platform. On iOS, @1x is everything older than the iPhone 4 (non-Retina screens), @2x covers the iPhone 4 through 6, and @3x is the iPhone 6 Plus and 7/7+; since @1x is now rare, mostly the iPad 1 and 2, you can begin slicing for the iPhone at Retina — @2x at 100 % and @3x at 150 % — with a recommended navigation-bar icon of 44 × 44 and a tab-bar icon of 50 × 50, both at high resolution, plus the app icons. In fact you can get away with slicing a single vector PDF. For Windows Phone the scale factors are WVGA 1×, WXGA 1.6×, 720p 1.5×, and 1080p 2.25×.

Minimum touch-target size is a recurring question, and it helps to start from the body: the average finger pad is around 10–14 mm and a fingertip 8–10 mm. The research settles on a range between 9.2 × 9.2 mm and 15 × 15 mm, while the big vendors give their own minimums — Apple, 44 × 44 pt; Google, a 24 dp icon inside a 48 × 48 dp target plus at least 8 dp of spacing, so 56 × 56 dp (56 dp being the floating-action-button size); Microsoft, 9 × 9 mm plus 2 mm of spacing each side, so 13 × 13 mm. To find a reference size you convert millimetres → pixels → points, where pixels are PPI × (0.03937 in/mm × mm), since 1 mm = 0.03937 in. On a 326 PPI screen (iPhone 7 Retina) that gives about 13.2⁄@2x ≈ 6.6 mm. Compared with the vendors, Apple's 44 pt is roughly 6.9 mm (nearly the same), while Microsoft recommends 82 pt, about 12.8 mm — and measuring the buttons in recent built-in iPhone 7 apps lands closer to Microsoft's figure, so 12.8 mm can be taken as the most suitable.

## Optimisers and compression

Don't forget the optimisers — pngcrush, optipng, jpegtran, PngThing, PngCrusher, **ImageOptim**, SmushIt, imgo, borschik. The graphics that leave a designer should be ideal, if the deadlines allow. Many JPEG optimisers finish with the Huffman algorithm, which belongs to the stone age of computing — one bit per symbol, which is wasteful, when you can and should pack several symbols into a bit (you could fit a gigabyte into one bit if it were all zeros). Prediction algorithms based on neural networks are well suited to that work, so pay attention to the compression algorithm when choosing optimisation software. But knowing the optimal algorithm is not a cure-all. Noise-suppression preprocessors, for example, give different results simply because they come from different vendors: feed in the same stream and bitrate, and one codec suppresses the noise first and then compresses the picture (an excellent result), while another starts compressing the noise itself, which is very expensive — and genuine, non-algorithmic noise almost no algorithm can predict.

For PNG specifically, every optimiser picks a set of filters to shrink the colour information, since a PNG is encoded line by line and the filters predict neighbouring pixels. Any optimiser will do, but I would recommend TruePNG, and it is worth digging into pngwolf, whose heuristic applies filters especially effectively. Among the lesser-known tricks: you can strip the colour information from fully transparent pixels, which shortens the IDAT chunk and helps a better filter get chosen, and you can drop the metadata chunks — only IHDR, IDAT, and IEND are essential, while the rest are in theory unneeded (though without gAMA the slice will not render correctly in Safari, and removing the iCCP colour profile is on your conscience), editable with exiftool. There are six PNG colour palettes — grey, indexed (256 colours), and full-colour, any of which may use transparency — so if you have under 256 colours, choose PNG-8 and watch the transparency, since not every editor saves semi-transparency for PNG-8; Optimizilla is convenient here and can regulate the colour count. You can squeeze further with zlib, 7-Zip, Kzip, or **zopfli** — I single out zopfli because it usually gives the biggest reduction — as a final pass through optipng, TruePNG, PNGwolf, AdfDef, or PNGout. Dithering, true colour, and posterisation can also fool the eye into accepting less information: cutting the colour count by two or three times is often invisible yet shaves 20–40 % off the file, and the "interlacing" checkbox (the PNG analogue of progressive JPEG) saves another 5–7 %.

The whole pipeline, in practice, looks like this for me: export from Sketch with the sketch-image-compressor plugin, run the slices through ImageOptim (and Pngyu for batch work), use ImageAlpha to kill some colours when fighting for bytes, and optionally bring in ImageMagick for script-driven resizing. For JPEG on the web, save from Photoshop at quality 75–90 and then optimise with Guetzli plus MozJPEG (which adds a micro sRGB profile for wide-gamut screens); for Retina you can drop quality to 51. Progressive JPEG is the usual advice, but its decoding is two to three times slower than baseline because of the multiple passes, which can matter on mobiles or in web games, and there is no point in it for very small images; squoosh.app is a fine service, sometimes layered on top of MozJPEG. For WebP I keep the recommended `-m 4 -q 75`, or `-m 6 -q 100` when time allows, remembering that WebP shines only on smaller images and can even lose to JPEG; by default it compresses with the VP8 codec, while the best codec now is AV1, newer than Apple's HEIC and Google's WebP, from which you can even pull a single video frame with ffmpeg and use it as a picture. There are many image formats, but not all belong in production — the working set is PNG, GIF, JPEG, WebP, HEIC, and AVIF for raster and SVG for vector — and heavy images are not the designer's responsibility alone but the developer's too, who must use lazy loading, HTTP/2, and a CDN. As for editors, almost any one makes good PNGs except Photoshop's Save As and Affinity Designer at default settings; the lightest PNGs that need no ImageOptim pass come from Photoshop's Quick Export as PNG and Export as PNG-24, and Sketch produces fairly light PNGs too (inspectPNG helps you study them).

## File formats

With the graphics sliced, optimised, and scattered across seven to ten folders, you are happy — but it can be done better. Look at a couple of less common formats that own the future and that you can already use on your projects.

**SVG** has long been in production and everyone knows it; it is heavily used for slicing Android graphics. Its XML-like structure carries a host of fine filters, the text inside it is indexed and copied by Google as well as ordinary text, and — a very important plus — you can put media queries inside it, so the SVG changes its appearance, even swapping vector and base64, depending on its own size, which partly solves the subpixel-rendering problem, since hitting the pixel grid is the true designer's chief desire. An SVG can be made into a (safely) binary file, and with SLIM you will be able to make cool animations in future; for now, good old JavaScript can animate DOM elements, and an SVG's elements are DOM elements, so you can animate them — which is also the catch, because animating complex graphics brings lag, every SVG element being a DOM element. The format will not die; it will only develop. SVG has two profiles your editor will offer on save — SVG Tiny, the shortened version for phone browsers, and SVG Basic, for more complex devices — but for production it is better to save version 1.1 (from 2003) without Tiny or Basic, since the 1.2 spec is still being developed and will never stop being merely a candidate. There is also SVGZ, an SVG compressed with gzip.

Preparing SVG for the web is simple. Optimise the number of points with tools like Simplify in Illustrator, the Smart Remove Point function of the VectorScribe plugin, or node reduction in CorelDRAW (VectorScribe and VectorFirstAid from Astute are best of all); run the result through an optimiser such as svgo; on export from Illustrator set Decimal Places to 3 if your SVG is under 32 px, to 2 if it is up to 256 px, and to 1 for anything larger (set 1 for an icon under 32 px and the lines of the source and the final SVG will differ badly, especially if the icon was not pixel-perfect), and uncheck "Responsive"; uncheck "Preserve Illustrator Editing Capabilities," which greatly inflates the file; and clean out the junk afterwards, in a text editor or through svgo, or save with Inkscape. SVG is contraindicated for very small icons (leave those in base64) and icons with complex gradients, or when you cannot make SVG sprites; for animation, snap-svg suits it.

How you save the SVG matters. Export it from Illustrator via File → Export rather than Save As, because Export offers a choice of method — internal SVG (with CSS styles, acceptable for the web), inline style (also CSS, but inline, which you should not use), and presentation attributes (the standard SVG attributes like fill and stroke). Disable "Responsive" unless you know why you need it, prefer SVG 1.0 and UTF-8, and first do Outline Stroke and merge the shapes with the Pathfinder; you can also inspect the code as you save to confirm there are no raster shadows or other images. And run the finished file through something like the svgomg tool regardless — enabling all of its checkboxes except "Round/rewrite paths," "Merge paths," "Prefer viewBox to width/height," "Remove unknown & defaults," and "Remove XML instructions," and if problems arise, also turning off "Compare gzipped," "Prettify code," and "Multipass."

Checking the quality of an SVG a designer hands you comes in two parts. Open it in a vector editor and confirm the extra points have been removed (points that do not affect the shape often linger), that there are no strokes or compound shapes (fixable in Illustrator), and that the text has been converted to curves. Then open the code and check that Deflate or GZIP is enabled for `.svg` files:

```
AddType image/svg+xml .svg
AddOutputFilterByType DEFLATE image/svg+xml
```

In the code, `preserveAspectRatio` governs the proportions; its default `xMidYMid meet` preserves the ratio, shows the content without clipping, and centres it, where the X axis runs `xMin`/`xMid`/`xMax`, the Y axis `YMin`/`YMid`/`YMax`, `meet` keeps the image fully visible, `slice` scales it like `background-size: cover`, and `none` lets the proportions change to fill the viewBox. `preserveAspectRatio` and `viewBox` are the two most important parameters. For sprites, confirm the `viewBox="x y width height"` is present and pay attention to the bounding boxes, the rectangle containing your shape with all its shadows and strokes, since resizing the bounding box changes the padding from shape to image edge and eases aligning the SVG against other elements — and designers, frankly, should not be making sprites; developers do. Repeating elements should use the `<use>` tag; a `base64` string in the code means a raster image is embedded (better to put a CSS mask over the raster, with a tool like Clippy); and if shapes are filled strangely, check the `fill-rule`. Evenodd is the more modern, flexible fill algorithm, which Sketch uses by default, but Android's VectorDrawable works only with nonzero, so set `nonzero` instead of the default `evenodd` (or tick the right box on export), and if you keep hitting this on Android, the SvgToVectorDrawableConverter tool and, for After Effects, the ae_SVG plugin help.

![Setting the fill-rule to nonzero on export](https://your-scorpion.ru/wp-content/uploads/2018/07/Cup.png)

A subtle SVG bug worth knowing: a shape can come out blurry even with whole-number values at 100 % zoom. If you set a stroke of 1 px it is split in two, giving you a semi-transparent half-pixel on each side; adding fractional values to the code yields a result without hinting. Illustrator, for its part, cannot make more than 100 artboards in one file, but it is fine for drawing icons — just do Object → Path → Outline Stroke, union the contour into a single shape, and have no masks; a complex icon of many objects will almost certainly copy into Sketch wrong and must be fixed by hand, deleting masks and extra elements before selecting everything and pressing Union. Test icons in svg2android before handing them to development.

**Icon fonts** are best forgotten. Not every device supports Unicode's private-use areas, icon fonts cannot be animated, they are always monochrome, they render differently across operating systems and even browsers, and they cause trouble in a version of the site for the visually impaired; if you do use them, be ready to control font size, line height, alignment, and block height and width. If you must organise icons as a font on a big project, icomoon is the simplest first approximation, with fontrapid for Sketch lovers and Orion for grouping icons and choosing their colours, or the slightly harder console route with gulp iconfont.

**WebP** is like JPEG but better, with semi-transparency and animation. Google designed it to make small, higher-quality images that speed up page loads; a WebP file consists of VP8 image data and a RIFF-based container. It uses lossless compression for photos, can compress animation lossily without backward compatibility, and supports 8-bit YUV 4:2:0, which can cause colour loss on images with one-pixel contrast transitions, such as pixel art; it also does lossless, and copes poorly with ringing, the legacy of frequency transforms.

Raster sprites I will pass over in silence — on the web they were killed by DataURL and SPDY. There is also **JPEG 2000**: worth slicing in only if 100 % of your audience can view it (a very specific audience, essentially Safari and iOS), because ordinary JPEG uses the discrete cosine transform while JPEG 2000 uses wavelet transforms, giving a smoother, sharper picture without the 8×8 grid of compression artefacts and a file 15–20 % smaller, with the bonus of 8- and 16-bit encoding rather than only 24 — but it is poorly supported by browsers. It was developed for both video and photo and combines the JPEG and MPEG work; it can compress three-dimensional data arrays, which is just what tomography, medical equipment, X-rays, MRI, and CT need (they sit on JP2 wholesale), and it suits scientists working with 80,000-px files who need only a small slice at a time, and Mars photos are usually JP2. Cinemas use the DCP format, inside which sits JPEG 2000 in the CIE XYZ colour space at 12-bit depth, most likely with a custom paid codec — and creating a Digital Cinema Package is genuinely hard, since codecs struggle to decode films at 25/30/60 fps in 12-bit at 2K or higher, while Netflix demands 4K and up.

To sum up, the best way to slice graphics for the web is an SVG map. And do not treat slicing carelessly — crooked anti-aliasing ruins the impression of an app. If a user gives you one star out of five purely for badly prepared graphics, you will need to collect twenty five-star ratings to bring the average to 4.8; a few ones in a row and you will struggle to get the total above 4. Good motivation, is it not, to do the work properly the first time?

## Fonts and text on mobile

Pulling the font from the system is the safe default. The size should come from the OS settings and be no smaller than 11 pt, so it can be read at normal distance without zooming, since many users with weak eyesight set a larger system font precisely to use their device comfortably. Whether to fix your own typeface or take the system one is a holy war I will not settle outright. Manually installing a font like Roboto used to matter when you needed special weights — Condensed, Light, Thin — but since Android 4.1 that is done through the `fontFamily` of the elements. Embedding your own font brings problems: rendering and sizing bugs, a font that may be outdated (Roboto updates, and you might ship an old version), a fatter APK, extra drawing cost, and the fact that non-standard fonts are usually smaller than the system one, forcing you to enlarge the text. Against that are the arguments for a fixed typeface: the design looks as intended; `fontFamily` is modern and good, but in many apps I worked on (more than 60 unique Android apps) the minimum API was 14, Android 4.0, where the new `fontFamily` values are ignored, so `sans-serif-black` is bold on 5.0+ and default-regular below; some Android shells let users pick their own system-wide fonts, so an inexperienced user choosing `sans-serif-black` makes your whole app look unpresentable; and rendering lag can be removed with `TypefaceEditText.java`. If you cannot find the optimal solution for your app, simply delete all the manual font settings — it will almost certainly be better than before. And remember: if you want the size to follow the system settings, dimension the layouts in `sp`; if you want fixed sizes, in `dp`.

The trouble is that the layout cannot grow automatically, so a user who sets the font to 150 % may find that the text does not fit. You can give some regions a dynamic height as a hedge against size changes, but to keep the gist clear: `dp` is a fixed layout size across devices of differing pixel density, `px` is a different size depending on density, and `sp` is `dp` multiplied by the font-scaling factor. Fonts can be specified in `dp` if you do not want them to break the layout, but `sp` is kinder to the elderly and the bespectacled.

Grey text, finally, should not exist on a mobile device — it becomes unreadable on dark backgrounds. The text should be black with transparency, and I limit myself to 50 %.

![Grey versus black-with-transparency text on mobile](http://your-scorpion.ru/wp-content/uploads/2016/11/gray_exaple.png)

As for smoothing the text inside Photoshop, I used the Sharptype plugin, but that was long ago, and you will never reach Safari's level of font smoothing anyway — which is why Sketch uses native rasterisation through OpenGL — while the other Adobe programs smooth fonts better. On a Mac you can play with the smoothing through `defaults write -g CGFontRenderingFontSmoothingDisabled -bool NO`.

## Adaptive and vector icons

Adaptive icons, introduced in Android O, let you create icons of different shapes, so an icon can be square on a Google Pixel XL and round on a Samsung Galaxy and look good either way. The overall size is 108 × 108 dp with a 66 dp safe zone in the centre; a round mask is 52 dp in diameter, a square one 44 × 44 dp with a 4 dp corner radius, and rectangles 52 × 36 dp or 36 × 52 dp, also at a 4 dp radius — not forgetting mdpi through xxxhdpi.

![Adaptive icon sizes and safe zone](http://your-scorpion.ru/wp-content/uploads/2017/08/shot_170822_143510-1.png)

The developer needs a foreground and a background layer, both 108 × 108 dp; the background must not be fully transparent, while the foreground may contain transparent parts.

![The foreground and background layers of an adaptive icon](http://your-scorpion.ru/wp-content/uploads/2017/08/IMG_4661-4.jpg)

The resulting motion works through the familiar parallax or pulse effect.

![The parallax animation of an adaptive icon](http://your-scorpion.ru/wp-content/uploads/2017/08/test-of.gif)

You can also draw a white mask on the foreground with a transparent icon shape (a lock, a star, a triangle) and place a texture on the background, producing a non-standard icon shape.

![A masked adaptive icon over a background texture](http://your-scorpion.ru/wp-content/uploads/2017/08/koala_head.gif)

When a designer hands you a layout where all the icons are raster, the usual route to usable vector files is Inkscape: drop the icons in, choose Trace Bitmap (Shift+Alt+B), and save.

## Material Design, shadows, and navigation

Migrating apps built to the Holo guidelines onto Material Design is best done without drawing mockups — the designers think through how to use the new components, which are shadow, animation, and colour. Shadow appears through Elevation (for static) plus translation (for dynamic), and must stay natural, intersecting other shadows to convey which element is higher. For colour the designer sets a palette: `colorPrimary` and `colorPrimaryDark` recolour the action and status bars; `colorAccent` recolours radio buttons, switches, and other controls (usable on old Android too, via a tiny drawable in our case); `colorEdgeEffect` recolours the overscroll glow; and `colorControlNormal`, `colorControlActivated`, and `colorControlHighlight` recolour the controls' states. The designer simply assigns colours to those properties and draws a couple of example mockups, and you get a perfectly systematised palette — the exception being a button unique to the app's style, say on a promo screen, which must be built directly in the view. For animation, `property_animation` can change any property of any object so long as you follow Google's motion guidelines, and the developer can also set `animateLayoutChanges` and let the platform create the animations itself.

Shadows are fine on iOS too — Apple sets no weighty restrictions and uses shadows itself. They are not sliced as raster; they are coded at the Core Graphics level, and you pass the developer the colour, opacity, offset, and radius:

```objc
imgView.layer.shadowColor = [UIColor blackColor].CGColor; // colour
imgView.layer.shadowOpacity = 0.7f;                       // opacity
imgView.layer.shadowOffset = CGSizeMake(10.0f, 10.0f);    // the shadow's "bulge"
imgView.layer.shadowRadius = 3.0f;                        // the shadow's size
```

Navigation patterns are a perennial argument. A tab bar at the bottom on Android is allowed — everything is, within reason — but if a Toolbar stays at the top (you have to put the standard buttons and "Report a bug" somewhere), a bottom menu is not quite right, and the standard Android pattern of tabs hiding on inner screens will feel unfamiliar, breeding confusion between the "Up" and "Back" actions; the Navigation Drawer sins the same way, though Google+ once managed a Drawer, a Tab Bar, and Bottom Navigation on one screen, so it all comes down to the information architecture. The side menu is the standard Android pattern, but is it a *good* one? The usual argument for the hamburger is saving screen space, which to me is untenable, since the top or bottom bars can hide on scroll the way Safari does; the obvious downsides are that the content is hidden by default, so you must open the menu (covering the screen with your hand) before reaching a section, and that a hidden drawer allows only one notification badge whereas a tab bar can put a context-bound badge on each tab. On iOS a side menu is outright nonsense, conflicting with the standard navigation pattern, and tests repeatedly show a bottom menu lets people move through an iOS app faster. The hamburger is acceptable when the app has no complex hierarchical navigation, or when the structure is very complex but you make the menu openable from any screen rather than only the leftmost (see VKontakte). And do not forget that the hamburger on websites is a fashion: interface trends have very short life cycles, so on a large app, lean toward neutral solutions that do not age so fast — or gamble on guessing the trend and, if you are lucky, tell a conference about it afterwards. For where to place the hamburger in a web layout, A/B testing answers better than I can; the general rule is the primary action on the left, the secondary on the right.

![Correct menu placement: primary action left, secondary right](http://your-scorpion.ru/wp-content/uploads/2016/11/JBIxt.png)

## Saving, files, logs, and versioning

A few engineering questions round things out. To save information in text fields on mobile there are four common approaches: make the save explicit, so the user cannot leave the screen until it finishes (a popup, a diskette icon in the toolbar); for cloud storage, spin an icon in the toolbar during each save so Android does not kill the app; for Gmail-type apps, build an offline database and a queue of operations for the server (technically hard); or save on blur, with the catch that if the user leaves the app while the save is running, Android may kill the process — so the rule there is to show a spinner if the operation has not finished a second and a half after the action. Transferring specific file types to an iPhone without Dropbox or other public services — when the data is personal — comes down to iTunes file sharing (open Programs, select the app, Add File), AirDrop, or any messaging app.

Pulling logs from Android takes a little setup: on the phone, allow installation from unknown sources, enable USB debugging (turn on Developer Options by tapping the build number a few times, then enable USB debugging and install via USB), and get root; on the PC, download ADB and confirm the device drivers are installed (the device should show "Composite ADB Interface" in its name). Then open `ADB/sdk/platform-tools`, run `adb start-server` and `adb devices` to confirm the device is listed, and collect the logs with `adb logcat > logcat.txt` and `adb bugreport > bugreport.txt`. On iOS the crash files live in `~/Library/Logs/CrashReporter/MobileDevice/` on macOS (after syncing with iTunes) and in `C:\Users\<user>\AppData\Roaming\Apple Computer\Logs\CrashReporter\MobileDevice` on Windows.

On uploading illustrations to Twitter without it mangling them, keep the proportions at 16:9, or 8:9 (which crops the preview cleanly in half vertically, showing the top), with typical 16:9 resolutions of 1024×576 up to 3840×2160 and 8:9 of 512×576 up to 1920×2160; for maximum quality use 896×504 (8:9: 448×504) and export to JPEG or PNG-8. And on the difference between layout schools: fluid (rubber) layout stretches the blocks and content proportionally in percentages; Adaptive Web Design builds interfaces that adapt to the user's capabilities, progressively enhanced and laid out from the largest resolution down to the smallest with `@media` breakpoints; and combining the two gives Responsive Web Design, where the molecules are fluid and the organisms themselves rebuild — which is how most modern sites are made, at roughly twice a developer's time compared with classic adaptive.

For versioning the design sources, the development-side answer is Git or Perforce, with Seafile or SVN if you run your own server; on Linux it is a plain `yum install git`. On Windows you go to git-scm.com/download, run the installer (the defaults are fine, just pick the code editor you prefer).

![Installing Git on Windows](https://your-scorpion.ru/wp-content/uploads/2024/07/fd.gif)

After installation the relevant items appear in the Windows context menu.

![Git in the Windows context menu](https://your-scorpion.ru/wp-content/uploads/2024/07/jjd.gif)

Then point `cd` at the folder, run `git init` (or `git init sample_repo` to name it) to make it the parent, drop your files in, add them with `git add`, and check with `git status` that everything went in.
