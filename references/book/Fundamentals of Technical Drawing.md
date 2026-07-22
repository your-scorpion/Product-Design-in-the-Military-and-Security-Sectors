# Fundamentals of Technical Drawing
### A working reference for painting, retouching, and the Photoshop/Illustrator craft behind it

*Maksim Tcvetkov · Product Design and Security Lead*


## Sequence

Start drawing an object from the perspective grid. Then draw the volume, and only after that the details and textures. Light sources matter enormously — they even shape the vignette on the final image — so at every stage of the work, keep an eye on your [lighting](https://www-personal.umich.edu/~mrwizard/wkshps/jewel/jewel.html) sources. The choice of light source should always serve the goal of revealing the object's form through light and shadow as clearly as possible. If elements of the image look flat and don't behave quite correctly relative to light and shadow, the picture will never read as realistic. At the final stage, don't forget defocus and depth planes — the closer the foreground, the more detail it should carry, though there shouldn't be too many sharp, "ringing" areas competing for attention. Framing should follow the laws of [composition](https://your-scorpion.ru/introduction-composition/).

![Sequence of building an image](https://your-scorpion.ru/wp-content/uploads/2019/07/cpp.png)

## Materiality and naturalness

Materiality can be conveyed through texture as much as through the shader itself. Don't overshoot contrast — it gives skin and other organic surfaces an unnatural cast and loses detail in the shadows. It's important to keep detail readable in both shadows and highlights whenever the scene's dynamic range is wider than the target output device can handle. If you're aiming for a glossy look, keep this in mind: a glossy surface produces a sharp, well-defined highlight, and that highlight describes the surface's curvature — a curved highlight follows the object's form (a straight highlight only ever occurs on a flat object). Highlights pointing in inconsistent directions on the same surface shouldn't happen in technical rendering. Lettering on objects should stay legible; air conditioners get removed from buildings, blemishes get removed from skin — everything extraneous gets cleaned up. A highlight's color matches the color of the light source: a green glossy surface under a red light will show a red highlight. The highlight is the lightest, brightest point on an object.

![Highlight and gloss example](https://your-scorpion.ru/wp-content/uploads/2015/10/shot_170804_134725.png)

Albedo is a material's own inherent color; diffuse is the blend of lighting and that inherent color. For gold, copper, and other colored metals, the highlight's color matches the albedo — gold shows a gold-colored highlight, and a stock white highlight is an obvious tell that the gold is faked. For every other material, the specular carries the tint of the light source, which is exactly why specular should be colored rather than white.

![Albedo vs. specular example](https://your-scorpion.ru/wp-content/uploads/2019/07/imagin.jpg)

Depending on how high heat has affected a metal, it can oxidize and develop a colored film, producing beautiful, iridescent colors on the surface. Below is a table of the permissible tarnish colors for heated metal.

![Metal temperature-color chart](https://your-scorpion.ru/wp-content/uploads/2015/10/Temperature-color-chart-large.jpg)

## Channels

![Complex selection via channels](https://your-scorpion.ru/wp-content/uploads/2019/07/selection.gif)

Every complex selection gets built through channels. Technical designers and photo retouchers need real fluency working with channels in Photoshop, since anything with complex shapes — hair, especially — can only be handled through channels. To build a simple contour selection, pick the channel with the most contrast in the selection area, duplicate it, and go over the boundary between the background and the object with a brush set to Overlay (30–50% flow), alternating its color between black and white — or increase contrast at the edges by whatever method you prefer. Then fill the interior with white. Naturally, this approach produces halos, but blurring the mask itself by 1 pixel (never the image!) usually fixes it. The result is a white shape on a black background. Cmd/Ctrl-clicking the edited channel copy's thumbnail creates a selection that accounts for transparency.

Invert a selection with Cmd+Shift+I. To send your work to an art director, you'll need to save the selection: use any selection tool, right-click the created selection, and choose Save Selection — it'll be saved into a separate channel. If you decide to rasterize a one-pixel-stroke contour, scale it up several times in Photoshop, then scale back down with bilinear interpolation, and finish it off with Levels. An interesting note: white multiplied by 0.5 turns gray, and multiplied by zero turns black — this often comes in handy when scripting automated mask generation.

Scratches and other small details are better kept in the green channel, since it's least prone to compression artifacts. Metalness can be tucked into the blue channel.

A related, useful practice, once you're already this deep into masks and channels: there are ways to speed up mask work considerably. To build a selection along the boundary of a transparent layer, Ctrl-click the layer's thumbnail in the layers panel. With an active selection, clicking the "add layer mask" button builds the mask directly from that selection. And if you Alt-click a mask's thumbnail next to its layer, you'll see only the mask's contents temporarily — pasting and copying (Ctrl+V, Ctrl+C) work directly into the mask in that view, which is often convenient; Alt-clicking the mask again exits that view.

## People

A person's height: draw a vertical line and halve it, then halve each half again, giving four equal segments. Split the top segment in half to get the height of the head (1/8 of total height). Every part of the body is a sphere, and a sphere always has a highlight — the darkest area on a sphere is its own core shadow.

Skin color varies across different parts of the body. Women's skin is usually lighter than men's, and a child's skin differs from an adult's in local color too. Hands, feet, elbows, and knees read darker and redder than the center of the chest (conventionally taken as the base color). Flushed skin carries a lot of violet undertones; fleshy areas run warmer than bony ones. The face usually splits into three zones: chin to nose runs cool, nose to brow runs red, and brow to hairline runs golden. For skin-color correction, it's worth converting the source image into Lab or LCh. When building a reference skin color for a white person, the a and b channel values are always positive/warm — cool undertones in skin only occur on darker skin tones, since skin overall leans yellow. In an ideal studio setup with no stray color casts, a white person's skin in shadow reads slightly more red relative to the yellow local skin color.

Below is the range of human skin color. As you'd expect, the extreme values apply only in very specific situations, and the truth sits somewhere in the middle:

**RGB**: 156 ≤ R < 256, 91 ≤ G < 256, 69 ≤ B < 256
**CIE LaB**: 5 ≤ L < 100, −35 ≤ A < 55, 3 ≤ B < 35

Also, be sure to look at [examples](https://humanae.tumblr.com/) of skin tones.

## Light and shadow

![Light and shadow reference](https://your-scorpion.ru/wp-content/uploads/2019/07/te.png)

Color temperature has to be kept consistent. If a warm tint is planned for the image, stick to roughly 3500K at the photo-shoot or 3D-rendering stage. The most serious mistake is missing reflections from cast shadow — never skip these, since they're what separates an object from the background (reflections are simply glints bounced off neighboring shapes). Light reflections are always tinted the color of the background (or made semi-transparent). Black surfaces can only exist in a truly black space, so don't paint the darkest areas of an image pure black. Always stick to the sequence: highlight, light, penumbra, shadow, reflection. A penumbra comes from a surface positioned close by lighting the object. A good way to disguise most mistakes is using backlighting.

A few terms: Lightness (the L in Lab) governs the degree of illumination. Brightness is, say, the brightness of light reflected off an object combined with the resulting color on that object. Luminance is also a kind of brightness, but of the light source itself. A less technical term is tone, which describes the shift from saturated color toward monochrome — examples of working with tone can be found in Munsell's books. You'll also occasionally see Chroma, which governs colorfulness.

Try to steer clear of adjustment layers — light and shadow intensity are better regulated separately. Recoloring into a new hue is done via a mask (saved into a channel). After creating a light or shadow selection, exclude everything extraneous by clicking the newly created channel's thumbnail while holding Cmd+Shift+Alt.

Core shadow is built with the Multiply blend mode (for hard core shadows), the same way patterns on animal fur or fabric are. Shadow density and sharpness depend on the light source's brightness — the brighter the source, the darker the shadow and the lighter the illuminated surfaces.

Once the drawing's done, convert it all to a smart object. The Liquify filter helps enormously when working with organic forms and fabric folds. It can be applied directly to a smart object — just run the script from Photoshop/Scripting/Sample Scripts/JavaScript/EnableAllPluginsForSmartFilters.jsx. That lets you warp the smart object with Liquify while keeping the original image untouched, and it's possible to save the Liquify distortion mesh too.

A reflection should never be lighter than the surface it's reflecting. There's no such thing as a soft shadow on a glowing screen. Vector shapes used for masks should be saved in the channels panel, the same as clipping masks. Don't limit shadows to plain gray — building shadow is done by generalizing the object's overall form.

![Shadow construction principle](https://your-scorpion.ru/wp-content/uploads/2019/07/box_2.png)

## The Pen tool

First, set things up properly: Photoshop > Preferences > Performance > Advanced Settings > disable Anti-Alias Guides And Paths, then restart Photoshop. I don't recommend the **Freeform Pen Tool** — if you want to work with vector shapes quickly, stick to the plain Pen tool. Certain key combinations temporarily switch the Pen tool to other vector tools while held:

- Pen Tool + `Cmd` = Direct Selection Tool
- Pen Tool + `Alt` = Convert Point Tool
- Pen Tool, hovering over a segment of the active path = Add Anchor Point Tool
- Pen Tool, hovering over a non-endpoint anchor of the active path = Delete Anchor Point Tool
- Pen Tool + `Ctrl` = opens the Path panel's dropdown menu
- Pen Tool + `Cmd`+`Alt` + a single click on the path = Path Selection Tool, selecting the whole path
- Pen Tool + `Cmd`+`Alt` + clicking the path and dragging without releasing = Direct Selection Tool with duplication — selects the path and copies it to a new location

I strongly recommend enabling "Rubber Band" in the Pen tool's options — it lets you see the outline before you even place the next point.

![Rubber Band preview](https://your-scorpion.ru/wp-content/uploads/2015/10/Screenshot_3.png)

Rounded corners on nested plates shouldn't share identical radii — pick values that read comfortably to the eye, and don't eyeball it: use the formula `outerRadius = innerRadius + (outerSize − innerSize) / 2`.

![Rounded corner formula](https://your-scorpion.ru/wp-content/uploads/2022/03/rounders.jpg)

When aligning text to a shape, use the height of a lowercase letter, not a capital.

![Text alignment by lowercase height](https://your-scorpion.ru/wp-content/uploads/2022/03/align.jpg)

## From Photoshop to Illustrator and back

Despite Photoshop's impressive capabilities, it's worth using a dedicated editor for vector work — Illustrator, in our case. If you've built a path in Photoshop, use File > Export > Paths to Illustrator (choose which path to export in the settings), which gives you an .AI file to work with in Illustrator.

In View > Outline mode you'll see your vector path along with the crop-mark typography. To turn those crop marks into a working artboard sized to the original image, apply Object > Crop Area > Release. Once you've edited it, copy it and paste it back into Photoshop (for a correct paste, the whole image needs to be visible).

![Pixel-perfect alignment in Illustrator](https://your-scorpion.ru/wp-content/uploads/2019/07/aling_pixel.png)

If you're building pixel-perfect graphics in Illustrator, the Align to Pixel Grid checkbox in the Transform panel is useful — every point gets automatically snapped to the pixel grid. This same checkbox shows up when creating a new document too, so pay attention there. It can be disabled globally from the Transform panel's dropdown menu, under Align New Objects to Pixel Grid. In Inkscape, achieving "pixel perfect" means setting a one-pixel grid; in Preferences > Guides and Grid you can enable displaying the image against the pixel grid at 600% zoom and above.

Often you'll need to know a curve's exact dimensions — open Window > Document Info and switch the display to Objects; the first line will show the selected object's length. For deeper, more precise dimensional work, I recommend CADtracker from [CADtools](https://www.hotdoor.com/cadtools).

![Document info panel](https://your-scorpion.ru/wp-content/uploads/2015/10/shot_170714_101847.png)

## Photoshop tools

**Spot Healing Brush**: removes blemishes, skin irritation, scars, and scratches. The tool accounts for the background color, lighting, shading, and the transparency of the surrounding pixels as it works, and it's ideal for detailed areas like the face and hair. It's worth actively working with the settings in the Clone Source panel.

**Patch Tool**: replaces an area of the image using another area of the image as its source. Photoshop CC added Content-Aware functionality for extra precision. It's worth understanding that it pulls replacement information not just from the specifically indicated source area (and with the Spot Healing Brush, no source is specified at all), but from a certain radius around the area being edited.

**Dodge and Burn**: strengthening shadows and highlights. Cap exposure at 50%, though ideally no more than 15%, and use matching values for Dodge and Burn. Fill a layer with 50% gray, set its blend mode to Overlay, and paint on that gray layer — this keeps the main image itself untouched, with the original always recoverable. Switching the blend mode to Soft Light makes the shadows and highlights read "softer." Be careful, though: over-lightening will make the work look metallic or plastic, and over-burning risks an irreversible slide into pure black.

**Render passes**: passes handed over by a 3D artist help build complex masks, strengthen highlights, swap out reflections, and run the whole "deep compositing" cycle on an image. You can also use the z-depth channel to build quality depth of field.

**Adjustment layers**: for working with color and tone — from a simple Hue/Saturation correction to a Lookup adjustment, which can imitate a particular kind of lighting or a film stock. Read my [tutorial](https://your-scorpion.ru/script-photoshop/) on automating tonal work.

**Clone Stamp Tool**: clones from one part of the image by sampling and overlaying similar pixel regions. Works well for retouching large, textured areas — cheeks, for instance. There's an "aligned" checkbox in the tool's options; disable it, and the sample will always be pulled from a single fixed point.

**Filters**: sharpening, blurring, adding noise. Filters can build fairly complex image-processing algorithms — High Pass, for instance, is a staple of frequency separation and general photo enhancement. If you want to boost image detail quickly, Unsharp Mask is the fast route, but for more careful work with variation and an emphasis on quality, High Pass is the better fit (it's ideal for finishing small images and fine detail on large files).

**Blend modes**: adjusting light, shadow, and highlights.

**Layer masks**: work on parts of an image with the ability to fully revert to the original state.

**Selections**: choose an area of the image to make edits and corrections to. There are many ways to build a selection in Photoshop; my favorite is Color Range.

![Blend mode compatibility chart](https://your-scorpion.ru/wp-content/uploads/2015/10/565.png)

The image above shows the conditions under which the various blend modes actually work. Every blend mode works in RGB at 8- and 16-bit color depth. In 32-bit RGB, only the modes marked with a blue "32" in the image work correctly. In the Lab color model (8 or 16 bit), only the modes marked "Lab" work.

It's worth getting comfortable with Auto-Select. If you need to select a whole group of elements at once, you don't have to hold Shift and click through each one — holding Ctrl+Alt (or enabling Auto-Select and holding just Alt) lets you drag a marquee over the elements you want directly.

And the useless "Browse in Bridge…" button is easily removed via Edit > Menus:

![Removing Browse in Bridge from the menu](https://your-scorpion.ru/wp-content/uploads/2021/06/Screen-Shot-2021-06-08-at-1.48.45-PM.png)

If you want to work efficiently across several layers at once — so a stroke with the Clone Stamp, or a dodge/burn on one area, propagates the same change across other layers, the way it would when retouching diffuse, bump, displacement, and normal maps together — Fusion or Foundry Nuke's node-tree approach is the better tool for that, letting you build a node graph once and apply it across every channel. If it has to happen in Photoshop specifically, the Quixel plugins are worth looking at.

## Illustrator nuances

![Illustrator pixel-preview reference](https://your-scorpion.ru/wp-content/uploads/2015/10/Screen-Shot-2016-07-21-at-08.56.54.png)

Only enable pixelization preview (View > Pixel Preview) when working with icons and complex shapes, and only enable Snap to Pixel (View > Snap to Pixel) when Pixel Preview is already on.

For resizing vector shapes, it's better to set the Reference Point to the top-left, and it's worth enabling View > Smart Guides to show dimensions while resizing.

![Reference point placement](https://your-scorpion.ru/wp-content/uploads/2015/10/4224.png)

When working with one-pixel lines, you can use 0.5px values for X and Y to keep things pixel-perfect. If a line is 2px thick, as in the image on the left, check Use Preview Bounds in the program's settings to get a bounding box that accounts for the stroke, not just the shape itself.

For text, it's worth using antialiasing that emphasizes the letters' original shape well. Adding fractional 0.1px values is fine, for the crispest possible vertical strokes — and remember that every web browser renders type differently, according to its own engine's quirks. When exporting an image with text to PNG via Save for Web, use PNG-24 only if you need transparency, and always check the Type Optimized box — this keeps every letter razor-sharp. In the Save for Web dialog you can also adjust the Percent parameter, and the layout will resize while keeping all of vector graphics' advantages.

When working with artboards, always use `[shift] + [O]` — it drastically speeds up moving artboards around the canvas. If you built your base layout at 100% screen size (@1x or mdpi), it's useful to go into Effect > Document Raster Effects Settings and adjust the Resolution parameter when generating layouts at other sizes.

![Rotating a bounding box](https://your-scorpion.ru/wp-content/uploads/2021/07/rotate_tool_illustrator.png)

*To rotate a bounding box, just select one point on a vector shape and rotate. Once you have the resulting bounding box, you can work with it through the Free Transform Tool (E).*

The eyedropper in Illustrator doesn't work all that well — it samples color on click rather than on hover, and to pull a color from a gradient you have to click while holding Shift; the color shows up in the Info and Color panels. It also samples colors straight from the screen's image, which means RGB — noticeable when working in CMYK. In Photoshop this is usually handled by working in LAB/16 instead.

Along similar lines, recoloring a finished interface quickly in Illustrator CC has its own set of shortcuts. There's the built-in Edit > Edit Colors > Recolor Artwork function; if you have several similar shades of a color that you want to unify, the Magic Wand tool helps, where the Tolerance setting controls how close in value the selected objects need to be — a tolerance of 1 means total identity across every selected object. You can also work magic through the Swatches panel: add your brand colors to a group by selecting the relevant swatches or design objects, then, with the objects you want recolored selected, click Edit Color Group in the Swatches panel to reopen the same color-editing window.

## In conclusion

What you've learned here will help you build attractive, correct images. If you're not drawing from scratch but processing a photograph, the general checklist looks like this: remove unwanted reflections and add in the needed ones, refine the light-and-shadow rendering, correct color, sharpen and boost local contrast, cut out the subject, then slice and optimize for the target platform. Don't forget to optimize your own workflow, too, since that's what keeps management's spending on your time reasonable — nobody's going to let you cut out every single link of a chain by hand, for instance, since it's slow and not worth the employer's money. Cut out a couple of links, join them into a chain of the length you need, and use warp tools to create the bends you need. It's also useful to start with the more complex elements first — the simpler ones go much faster afterward.

![Working with a complex object like a chair](https://your-scorpion.ru/wp-content/uploads/2019/07/chair.jpg)

Cutting objects out from a background can follow a universal method: trace the object's clear edges with a path, convert the path to a selection (Feather = 0), apply Select > Modify > Contract with a value of 1–2 pixels, then Select > Modify > Feather with 1–2 pixels. Turn the resulting selection into a layer mask and fix any rough spots with a brush. And before handing the image to the art director, don't forget Layer > Layer Mask > Apply.

![Local contrast enhancement example](https://your-scorpion.ru/wp-content/uploads/2019/07/local-contrast.png)

The algorithm for boosting local contrast: apply Curves to the layer, nudging the black point slightly up (turning black into gray) and the white point slightly left (turning white into gray). Local contrast is then reinforced with the USM (Unsharp Mask) filter across several passes, gradually shrinking the radius while increasing the effect's strength — the first pass for large details, radius 20–30 and strength 20; the second pass for fine details, radius 5–10 and strength 20–30; and so on, until the result looks good enough. The final pass is a plain sharpen. If you're preparing material for print, a touch of over-sharpening actually helps. Dropping resolution from 300 ppi down to 72 ppi can leave an image looking a little blurred and soft.

When saving web graphics with slightly blurred detail, I use Unsharp Mask with these settings: Amount 200–400%, Radius 0.3, Threshold 0.

![Correct seamless texture example](https://your-scorpion.ru/wp-content/uploads/2019/07/texture_line.png)

*Example of a correctly built seamless texture*

---

## Practical addenda

A few reader questions over the years have stayed close enough to this article's core craft that they're worth folding in here as extensions of the techniques above.

**Upscaling an image without losing quality or gaining noise.** It depends heavily on how you're upscaling and the source photo's starting quality. If you have a clean, corrected TIFF at 40×50cm and 100ppi, and you want 300ppi, simply raise the ppi value with Resample checked. If instead you're dealing with a 500×500px JPEG, convert the image to Lab, blur the color channel with Gaussian Blur, apply a light Reduce Noise to the luminance channel, then scale up using Bicubic (ideally Bicubic Smoother). Check the result and apply Smart Sharpen locally wherever needed; if you're not happy with it, Smart Blur is the fallback. It's worth understanding that bilinear interpolation is less accurate than bicubic but faster, since bilinear only accounts for two samples; Nearest Neighbor works by "growing by nearest neighbor," which can create unwanted thickening when scaling text but works well for textures.

Sometimes the noise is concentrated in one particular RGB channel specifically, since each individual channel's resolving power on a camera sensor differs, and the noise levels in each channel differ too (usually it's the blue channel that carries the most). Blurring the blue channel directly will smear the image badly, so to preserve detail, convert to Lab again and use a median filter plus blur (median gives a harsh effect, blur softens it). Sharpness lost to blurring should then be restored with High Pass and a light Gaussian blur (0.4–0.8).

It's also worth restoring the original image at the edges, to keep them crisp. A few good plugins for this: Neat Image, Alien Skin Blow Up, Genuine Fractals Print Pro, [ON1 Resize](https://www.on1.com/products/resize10/), and [waifu2x](http://waifu2x.udp.jp/). Broadly, downsampling and upsampling are handled well with Lanczos resampling, or libraries offering more flexible options, such as a nonlinear kernel with D8.

As for judging whether an image is noisy in the first place: select a flat, uniform area, look at the histogram data for the red channel, and check the Std Dev (standard deviation) value — anywhere within 6 suggests the image isn't noisy. Since most noise tends to concentrate in the blue channel (a consequence of how the sensor forms that channel's data), it isn't a reliable channel to judge by; the green channel is often barely engaged in common photo elements like sky or skin, which makes it a steadier reference point.

**Speeding up brush and layer opacity changes.** It comes down to timing: pressing the 0 key normally sets opacity to 100%, but a quick double-tap of 0 sets it to 0%.

**Working efficiently with linked smart objects.** Create an external file and reference it: File > Place Linked, and drop the file into your document — or drag the file into an open document while holding Opt/Alt. This keeps the overall file size down, since a linked file isn't embedded into the final PSD, and updates to linked files happen automatically as you work.

**A quick, reversible way to undo layer visibility toggles.** Enable the relevant checkbox in the History panel's options, and toggling a layer's visibility becomes just another undoable step alongside everything else.

**Getting an image's average color.** In Photoshop: Filters > Blur > Average (the values it gives aren't perfectly precise, but close enough). In After Effects: the Eyedropper tool.

**A cheap per-pixel shader that multiplies the screen buffer's color by the light source's color**, done via a lookup table. In principle, an LUT could be a 256×256×256 image holding 16,777,216 unique RGB colors, which would need about 48MB to store — too much for most uses, so a 64×64×64 3D array is more common, with each square broken into 8×8 blocks, or even a 16×16×16 LUT unwrapped onto a 256×16 texture. This is functionally an ordinary normal map, where R maps to the X axis, G to Y, and B to Z. A simple way to experiment: stack a few adjustment layers in Photoshop and go to File > Export > Color Lookup Tables, where the Grid Points setting lets you choose the grid size and export the LUT in 3DL, CUBE, CSP, or ICC Profile format. From there, take any neutral LUT you find online and apply your own exported file to it through Color Lookup.

**Rounding CMYK values after flattening transparency**, if you run into it, is handled well by a small dedicated script, linked from the original comment thread.

**Choosing a tablet.** If budget is a real constraint, a Bamboo is a reasonable start; otherwise, an Intuos is the better choice. A few conveniences worth knowing with an Intuos: you can save all the tablet's settings to a file and carry them with you, using the bundled Tablet Preference File Utility. In the Wacom Tablet Properties options, disabling "Adjusts the pen pressure for compatibility" lets you use more than 1024 pressure levels, and the Application tab lets you configure the pen differently for each program. Under Mapping, disabling "Use Windows Ink" removes the circle that appears around the pen tip on press; if the tablet's own buttons aren't proving useful, the pop-up window for them can be turned off via Show Express View — though it's worth learning them, since they let you fire off complex combinations like Alt+Shift+Ctrl with a single press. That said, Wacom isn't the only option, especially if your work doesn't demand highly complex, detailed graphics — Chinese alternatives like Gaomon, Huion, and XP-Pen have caught up considerably in quality, and an iPad plus Pencil plus Procreate is a perfectly solid setup for sketching.
