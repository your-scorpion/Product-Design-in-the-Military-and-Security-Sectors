# An Introduction to Color and Light Theory
*Maksim Tcvetkov · Product Design and Security Lead*



Today I will cover the fundamentals of color science. But I have no intention, of course, of limiting myself to advising you to paint a color wheel in watercolor and do a still life in three colors. Those are basic exercises you can manage on your own. This article leans more toward *memory colors*, *neutral colors*, and other popular terms from the color grading of photo, video, and art.

## Why Color Matters

The importance of color is obvious enough; color is often the foundation of a company's brand. Red = Coca-Cola; yellow + red = DHL; green + violet = MegaFon. If Beeline is yellow + black, then no other telecom will dare use that combination. The Union Jack is hugely popular and respected in Britain, which means using red, white, and blue for products is an excellent idea — the audience will associate them with something positive and familiar. The same works for the United States, France, and Russia; and for Germany or Belgium, black, red, and yellow will do.

## How Light Works

Let us look briefly at the theory of human color perception, and at the principles of building lighting along the way. The first thing to understand is that vision happens in the brain, not in the eyes. We have all clicked "render" in some program, watched a result appear, and called it magic. But here is the interesting part: the foundation of that "magic" was laid by Isaac Newton. He proposed the corpuscular theory of light and used it to describe such key phenomena as reflection, refraction, conservation of energy, and subsurface scattering. Intriguing, isn't it? But how does light actually "work"?

Light consists of a countless number of tiny particles called photons. These particles have different wavelengths depending on their color: photons of blue light have a shorter wavelength than photons of red light. Light — that is, a swarm of photons — collides on its way through the dense layers of our atmosphere with various gases, molecules, and much else, which changes the photons' direction and scatters them. As I said above, blue photons are significantly shorter than red ones, so they scatter in the dense layers of the atmosphere while the red ones can survive and reach the ground. That is why our sky is blue: all the blue photons scatter while still in the atmosphere and create the appropriate tint. And the tints of a single color should be as varied as possible — a technique called color variation.

[![Photon wavelength diagram](https://your-scorpion.ru/wp-content/uploads/2012/09/3221-300x141.png)](https://your-scorpion.ru/wp-content/uploads/2012/09/3221.png)

*Photon length. Length is the distance between the crests of a wave. Wavelength is denoted by lambda (λ) and measured in meters or fractions of one — nanometers (nm, 10⁻⁹ m), micrometers (µm, 10⁻⁶ m), or centimeters (cm, 10⁻² m). Frequency shows the number of cycles per unit of time and is measured in hertz (Hz). It is not hard to guess that the shorter the wavelength, the higher the frequency.*

Some of the photons reach us and begin colliding with the surface of every object in their path, including motes of dust. The color of the light reflected from a surface depends on the color of the reflecting surface. A white surface will reflect the entire color spectrum; black absorbs almost all the photons that strike it. This is why it is reasonably held that black clothing is warmer. If white light falls on a green table, the green is absorbed while the red and blue are reflected (I am putting this very crudely, of course). And the full spectrum is not, of course, limited to green, red, and blue — the eye perceives around two million shades in all.

The sun's white light is a set of full-spectrum photons "split" into the colors of the rainbow — red, orange, yellow, green, cyan, blue, violet — and the sum of those colors yields white. Black, correspondingly, is colorless, and able to absorb all the colors of the spectrum. For the sake of color constancy, our brain tries to refer everything back to daylight; for this we have a built-in approximation of white balance.

## Light in Numbers

This can also be read through numbers. The maximum luminance the eye can tolerate is about 7,500 cd/m²; the sky sits around 3,000–5,000. Pure white paper under 100 lux of lighting will be around 250 cd. There are norms: for a dining room, 200–300 lux; a bedroom, 30–300 lux; a kitchen, 200–400 lux. But these are minimums — and for beautiful work with light you also need contrast, not flat, linear light.

This is not idle theory. The choice of colors in a traffic light is dictated by wavelength: long waves scatter less than short ones and are visible far farther off. Likewise, in combat operations, blue bulbs are used for lighting, since their light is far less noticeable from a distance and scatters faster than yellow light.

Or take color temperature, where 2700 K is a soft white light with a reddish cast and 6500 K is a cold daylight. The rule is that cool tints are used for office spaces and warm ones for living spaces. Moreover, public transport needs circadian light in the mornings and a muted, warm light in the evenings. 5000–6000 K is a universal value, or a warmer, more intimate one from the 3000–4000 K range. To go deeper than this you would need spectroscopy.

There is the CRI (Color Rendering Index), and the higher the value, the better — +90 is the best.

### A practical aside: are LED lamps good enough for work?

LED lamps can be used, but there are drawbacks. When buying, look at the color rendering index (a low index is bad) and check for flicker with your phone camera: point the camera at the lit LED and you will see traveling bands. Those bands mean there is invisible flicker, which tires the eyes quickly.

Incandescent and halogen lamps have a very good rendering characteristic, above 90, at rendering grade 1A (100 is considered the reference light source). LED lamps have a rendering coefficient of 80–89 and rendering grade 1B. There is a problem with a peak in the blue region of the light, but if you take a 2500–3000 K lamp (a warm yellow color), or a white one, the blue peak will be less intense; a 6500 K lamp is still not suitable for work. Different manufacturers use different yellow-phosphor compositions; naturally, the spectral makeup of expensive phosphors is more even and the resulting sense of "white" more realistic than with cheap "white" LEDs. Expensive LED lamps also have an internal stabilizer, so they shine evenly across very large swings in input voltage.

## Light and Shadow

The interplay of light and shadow matters. In interiors, a downlight directly over a person's head can lead to harsh shadows on the face in mirror reflections; better to use linear lighting on the ceiling. Light makes it easy to zone a space — lighting only near the sofa, say, or in the kitchen.

### A practical aside: lighting a home broadcast studio

The simplest setup is to sit facing a window. If that is not possible, you should have three light sources: a key light in front of you and two additional lights — or two sources to the left and right, angled slightly to the front. A ring LED lamp is rather gimmicky, but it works; direct lighting from an overhead chandelier guarantees hard shadows.

## How We Perceive Color

"That's all well and good, but what's the use?" you ask. Now we know the rules by which color exists. Let us work out how we perceive it.

Here the leading role belongs to our brain. Color is a wave phenomenon based on photons. Photons "slip" into the pupil (a black pupil absorbs all colors; in blind people the pupil is white), the information is converted into neural signals and travels to the brain, and the brain forms a color picture by the following criterion: chromatic colors are defined by lightness and chromaticity. Chromaticity is "added" through saturation and hue. Hue is the color itself: red, blue, magenta, and the other two million. Saturation is how "pure" a color is — how far it departs from colorless/gray/achromatic. Processing this information, the brain builds the picture we "see."

A good illustration is the common misconception that a darker tone is more saturated. The fact is that as illumination drops, the sensitivity of the eye's receptors decreases, and the red–green region of the picture the brain receives shifts toward night vision (the rods). For saturated colors, the greatest contrast is between blue and yellow, weaker between red and cyan, and weakest between green and magenta. This is often used in construction: the LEED standard for green building is demanding about the presence of natural light, which is always contrasty. Other standards require particular color-rendering characteristics for glazing, the presence of daylight, a certain quantity of light, and protection from glare.

Incidentally, the eye can distinguish on the order of 300 achromatic shades. A rule: the less time the brain needs to process the information, the more it likes the picture, and the better that picture is.

## The Emotional Life of Colors

We react to color in astonishingly different ways; our emotional response is incredibly strong and underrated.

**Red** is an active color — the color of energy and confidence, the leader in its category, but without grace. In interiors it shrinks space. It is the color of sexuality (which is why many people unlucky in relationships dislike bright red). In clothing it looks good if there is more than 50% red. But red in large quantities also provokes aggression — not for nothing have many militaries tried to work a bit of red into their combat dress. It is the color of pioneers and of cruelty: exciting, warming, active, energetic, penetrating, thermal, activating all the body's functions. The strongest color. For a short time it increases muscular tension, raises blood pressure, and quickens the breathing rhythm if the surroundings are painted in shades of red. Red liquid soap is usually perceived as more powerful but slightly dangerous; green liquid soap, as safe and eco-friendly — red + green = nature. (But we are talking only about perception of a product's color, not its composition.) Red is the color of England, Japan, the USSR. It appears in 78% of the world's flags as a symbol of struggle, courage, bravery, wars, and uprisings. On Iceland's flag the red is lava.

**Yellow** is light, radiant, exciting; the rays reflected from it warm you (the color of frozen sunlight) but also scorch and irritate — hence the yellow press, yellow discounts, radiation. In China, yellow is the color of holiness; yellow + green = Latin America, Africa. Seemingly a fine color. People read yellow fastest of all; the default color for highlighting text is yellow, as is the sticky note for important reminders. Yellow is toning, invigorating, warming, and increases muscular activity. It looks good in contrast with black. It speaks of inspiration, creativity — the color of the sun. Yet in contrast with black or red it warns of something, but does so kindly, with a sense of joy and fun. (Though bees, radiation symbols, and crime-scene tape are always black-and-yellow.) It is good for "aggressive" brand identities (Beeline). The term "yellow press" did not arise for nothing: yellow is also the color of gossip and absent-mindedness. Yellow price tags mean cheap goods. In many academic paintings Judas is dressed in yellow because he betrayed Jesus, so yellow can also speak of cowardice; in Brazil it is the color of despair, in Syria of death. Breaking up with your girlfriend? Give her yellow flowers — in Russia yellow is the color of parting. Or give a gold ornament, since gold is very close to yellow. To get a good yellow on screen you need the P3 gamut's green, such is the logic of trichromacy. Blue tones in the background plus yellow/orange in the foreground optically enlarge a space — a technique widely used in creating public spaces.

**Green** calms; a passive color. It symbolizes youth, fun, freshness, mint, and at the same time stability and ecology. Ideal for a bathroom or bedroom, for a secluded nook, and for banks. It lowers blood pressure, soothes, and relieves tension. Green is associated with beer because after the war a great deal of green glass was left over — glass that lets little light through — so beer began to be bottled in what was on hand. The color of immortality and nature, popular among Muslims. One must understand that miracles do not happen: if a factory adopts green for its brand yet keeps turning up in the news for polluting the environment, the color will not save the brand. Ireland is a green country. The detergent maker Ty-D-Bol changed its products' color from blue to green and nearly doubled its sales. Without green there is no yellow, especially on screens.

**Violet (Purple)** is the color corresponding to monochromatic radiation of the shortest wavelength. A very ambiguous color — most likely because it sits on the border of red and blue, of the unruly and the calm. Combined with orange it carries associations of suicide. It belongs to something mystical and secret, luxurious and romantic — the color of philosophers, mysticism, luxury. Its luxury is inherited from the costliness of the pigment, which in ancient times was extracted from mollusks, so only the very wealthy wore violet clothing.

**Blue** is calm; with it you can beautifully convey depth in a picture — a very kind and unobtrusive color. In sound, blue is a very low tone. The most neutral color, beloved of finance. A shade of blue — light blue — is recommended for the design of meeting rooms, since it aids the absorption of information and the building of friendly relations. An excess of blue can increase a person's fatigue. It slows cardiac activity and acts soothingly, in some cases depressively. Blue rays are used in treating inflammatory eye diseases, and I am sure that is no accident. As it happens, a blue light is blinking on my modem right now, and it does not distract me at all, though it does draw the attention. Stability, truth, water. Your mage's mana, in the end. Blue is also the color of IT people — Intel, Facebook, VK are all in shades of blue.

**Brown** is the color of confident, "homey" people.

**Orange** is a very energetic, youthful color — Holland. Less strong in perception than red, it symbolizes pleasure and creative activity, and consequently a certain irresponsibility. It warms, but... you should not use it often, anywhere — in design, in painting, in scribbles on the pavement. In interiors, peach tones are more desirable. The color of vitamins, of oranges.

## Seasonal Color Types

I would also like to single out the color types.

**Summer type** is calm, unshowy, aristocratic. The colors are walnut; the skin uses cool pale-pink tints; gray eyes with only a faint cast. These are the colors of queens — a queen always dresses in the summer type. The negative color here is black, so gray tints are appropriate; using some charcoal shade is very much the right move.

**Autumn type** — the red-haired folk. Freckles, bright red hair; shades of bronze or copper suit them, peachy skin, bright eyes. A vivid, sunny type that black definitely does not suit; here all the earth tones work, the violet-blues, deep bright warm shades, or a blue-violet range. Pink is of course excluded :).

**Spring type** — the bright-golden blond. Blue eyes, golden-toned hair, peach-colored skin. This type suits the colors of little flowers, or of a green forest. You might add, say, light-blue stones in place of eyebrows, light-blue tints.

**Winter type** — dark-brown, walnut colors; ash-brown clothing or close to it; bluish skin, perhaps slightly olive, or white. The eyes might be violet or blue; the hair, as an option, gray.

[![Colors by weather](https://your-scorpion.ru/wp-content/uploads/2012/09/jpg-300x297.jpg)](https://your-scorpion.ru/wp-content/uploads/2012/09/jpg.jpg)

But these are just examples. Any seasonal palette can contain ALL colors — only each in its own unique shade (orange may be an exception, as it has no cool variant). Although black scarcely appears in the types above, it is very common nowadays. It is the color of negation and distrust, so characters painted black can be not only Japanese ninjas but also creative folk, since rejecting the standard is the basis of creativity. But let us not forget that there is no absolute black in nature: black should have a clear hue. In support of this, it is worth noting that when teaching children to draw, the black and brown paints are deliberately removed from the paint box so that the child begins to perceive the world through color.

## The 60/30/10 Rule

A dress code is very telling. Clothing can have three colors, drawn from a person's eye, skin, and hair color. 60% of the look is taken by one color — the main one, the darkest. The next color takes 30% — a middle shade, not too bright, perhaps pastel. The last color is the accent 10%. It is always a small thing: a tie, horns, fins, the skull on an orc's chest — but the accent color cannot be pastel; it must be bright, bold, making the accent. This 60/30/10 ratio is very close to the theory of the golden ratio.

## Color Balance and Complementary Contrast

By applying colors, we change the "color balance." Anyone with an art education, or who has painted in oils or acrylics, has surely noticed that as paint goes onto a white canvas everything looks rather attractive in the early stages — but the moment the whole plane of the canvas is covered with color, the picture loses its appeal, because the contrast with the white is lost. That contrast can be restored with the contrast of complementary colors (red/green, violet/yellow), which strengthens the work's coloring, its chromatic expression, and a special vividness of color. Prolonged contemplation of one color lowers sensitivity to that color and raises sensitivity to its complement. Stare at a bright red for a while, then shift your eyes to gray, and the gray will appear green-blue.

---

![Types of harmony](https://your-scorpion.ru/wp-content/uploads/2012/09/23_0-300x280.jpg)

We have only just dealt with chromaticity. **Now let us talk about saturation.**

## Saturation, Base Colors, and Harmony

The base colors are held to be those that cannot be obtained by mixing other shades. Red, blue, and yellow are base (this is the additive color model). That is what we are taught at school — but many have tried to paint a still life with these three paints and nothing good came of it. I would prefer to call magenta, cyan, and yellow the base colors (the subtractive model) — or did you think plotters print in CMYK rather than RGB for no reason? The only "honest" RGB device is a three-stimulus visual colorimeter — in particular, a monitor.

Harmony is a well-chosen combination of colors. It comes in the following kinds:

- **Monochrome harmony.** Simple enough: you have only one color at your disposal. Recognizability of forms is achieved through the tones of that single color.
- **Analogous harmony.** Here there is not one shade but all the shades adjacent on the color wheel, close to one another; the effect somewhat resembles monochrome harmony. The phrase "dominance of shades" is easier to remember and describes analogous harmony very accurately.
- **Complementary harmony (two-color).** Opposite colors on the wheel are taken — the simplest method. Not exactly primitive, but nothing to be proud of either.
- **Triadic harmony.** Three shades placed at equal distances from one another.
- **Tetradic harmony.** Two sets of complementary colors — a little square rotating around the wheel. Used in landscapes: two colors for the foreground, two for the background.

![Good color pairs](https://your-scorpion.ru/wp-content/uploads/2012/09/nice-color.png)

These ways of "orienting" yourself around Itten's color wheel will help you pick beautiful, harmonious combinations of shades. But this holds only for mixing paints. Thus the complement of red is a turquoise-cyan, not green. Complementary colors on Itten's and Goethe's wheels are not opposites. Wilhelm Ostwald's wheel, by contrast, is very close to RGB and works quite well for choosing complementary colors. The test: a pair of mutually complementary colors, built into a gradient, will produce a gray shade at the center.

### A practical aside: killing the gray in complementary gradients

If you do not want those gray transitions in complementary gradients, they can be removed by adding intermediate shades. There is a good helper for this — an [LCH/Lab color-gradient picker](http://davidjohnstone.net/pages/lch-lab-colour-gradient-picker).

![Gradient example](http://your-scorpion.ru/wp-content/uploads/2017/05/Untitled-1.png)

Gray tones should stay gray — that is Evans's principle. Black-and-white film has its own meaning: the image of an era. Costumes, sets, and dialogue are about style; but immersion in the era comes through color.

## Contrast and Image Quality

And finally, a useful and important note about the contrast and quality of a computer image: in a dark room we see more detail than in a bright one. In all bright areas we see less detail than in a darkened region — that is how the human eye is built. Everyone distinguishes warm colors better than cool ones. All cool-toned objects are always a general mass, as in nature: fruit and people are bright and warm, while sky, foliage, and sea are of a cool tint. There is even a rule: if you darken a base color, you must make it brighter and cooler in hue. Programmers use this to optimize images: with logarithmic compression they strip information from the bright areas of video material, optimizing the final file with no visible loss of quality. Interior designers make a kitchen cool when they need to reduce people's appetite, and the theater curtain is red so that the stage stays in the foreground.

Now to put this into practice: the general accessibility requirement is a minimum contrast ratio of 4.5 for small text and 3.0 for large. But not every art director will approve colors so dark that they cannot always be told apart. They can be lightened, at the cost of the contrast ratio. The point is that the WCAG standards focus on the contrast between background and text/object.

![WCAG contrast illustration](https://your-scorpion.ru/wp-content/uploads/2019/12/Frame-5.png)

### A practical aside: color contrast for readable text

Is color contrast also good for choosing a readable text/background color? Not quite. Color contrast plays a part, but choosing colors for text and background is a more complex discipline. Without going into detail: there is a service, [snook.ca](https://snook.ca/technical/colour_contrast/colour.html), where you can check any two colors for contrast against the W3C recommendations. And here is my favorite [site](http://www.geotests.net/couleurs/frequences_en.html) for extracting a color palette.

## Color on the Web

On the web, color is formed from three "bulbs" — red, green, and blue — which mix to form a color. But people find it easier to navigate in terms of Hue, Chroma, and Lightness — that is, HSL. Another good system is CIELAB (Lab). The L in Lab differs from the L in HSL; it is more uniform, which lets you pick brighter and more legible shades (so beloved of brunettes).

![CIELAB illustration](https://your-scorpion.ru/wp-content/uploads/2012/09/23-159x300.jpg)

### A practical aside: naming colors in a project

The most common approach for small projects is the obvious names, such as `"blue"` and `"primary"`. Or the Material Design approach, where all the shades of `"blue"` are grouped in one place and their lightness is marked with three digits — 100, 200, 300. That system may break down if you have both a dark and a light theme; in that case a semantic color system is built on top.

Or there is the old and not-so-great approach, used when a project grows complex and the grays split into cool darks for text and lights for backgrounds, giving names based on the role or association the color plays in the UI (you can apply radiant thinking):

| Hex | Name |
| --- | --- |
| `#ffffff` | snow |
| `#f8f8f8` | cloud |
| `#ebebeb` | dove |
| `#dbdbdb` | shark |
| `#aaaaaa` | robot |
| `#727272` | asphalt |
| `#333333` | night |

Color names can be generated [here](http://chir.ag/projects/name-that-color/). If the project is huge, you can add prefixes — `Ink` for text, `brand` for styling, `ui` for interface elements.

In more technical terms, there are two modern approaches: semantic and atomic. Semantic puts the color's role in the name: `bg-danger`, `text-brand`, `bg-hover`. Atomic: `red-500`, `blue-300`, `gray-500`. Such names are easy to formulate by a set scheme:

```text
Type → Color role → Prominence → Interaction
```

That is: type (border, icon, text, bg) → color role (brand, info, danger, warning) → prominence (tertiary, secondary, strong) → interaction (hover, pressed). Everything except the type is optional. You can complicate this further by adding levels: theme (dark/light); color group (accent/warning); importance (20/60/100); component name (button/input); size (xs/s/m); state (focus/hover); visual property (textColor/fontSize).

## White Balance and Color Management

The ability of human vision to adapt to changes in lighting while keeping the original colors of the surrounding world unchanged is called *chromatic adaptation*. To perceive colors in their familiar balance, we lean on the temperature of a reference source in the picture we see. If that temperature changes sharply, a person needs time to adapt to the new conditions. This balance between cool and warm colors is built into our brain — but it is not white balance; it is the eye's analog of chromatic adaptation, since it involves working with warm and cool colors.

On a monitor: looking at a scene whose color temperature (the reference white light illuminating the scene) and illumination level (lightness, cd/m²) change gradually, our vision uses two kinds of adaptation at once — chromatic adaptation and lightness adaptation — and our sensation of color shifts with it. A monitor has a constant contrast range, which compensates for lightness adaptation. Color is trickier: the image on a monitor will always relate to our subjective notion of the original. The reference white point is D50 in CIE XYZ (the PCS). For this you must understand that Photoshop uses ICC Lab D50 with Bradford chromatic adaptation; if a profile does not use Bradford, there will be calculation errors — the farther from D50, the cruder the error. And there are many different CIE Lab variants, not all of them D50; moreover, there is a difference between averaged natural daylight at D50 and a lamp fixture with D50 bulbs. Meanwhile sRGB has a D65 white point (xy 0.3128, 0.3292), and offset CMYK profiles are fixed to the paper's white point at D50.

White balance is easier to grasp by working with the histogram. We move the extreme light points with curves, changing the channel coefficients; the midpoints are then used for chromatic adaptation to the scene's color. Put simply, working with white balance, we change the color of objects so that white in the photo or illustration and white in the viewer's mind line up without any sense of discomfort.

Below is a list of white-point coordinates:

| Illuminant | X | Y | Z |
| --- | --- | --- | --- |
| A | 1.09850 | 1.00000 | 0.35585 |
| B | 0.99072 | 1.00000 | 0.85223 |
| C | 0.98074 | 1.00000 | 1.18232 |
| D50 | 0.96422 | 1.00000 | 0.82521 |
| D55 | 0.95682 | 1.00000 | 0.92149 |
| D65 | 0.95047 | 1.00000 | 1.08883 |
| D75 | 0.94972 | 1.00000 | 1.22638 |
| E | 1.00000 | 1.00000 | 1.00000 |
| F2 | 0.99186 | 1.00000 | 0.67393 |
| F7 | 0.95041 | 1.00000 | 1.08747 |
| F11 | 1.00962 | 1.00000 | 0.64350 |

A word on wide-gamut monitors, since they are a common source of confusion: the most unpleasant problem is when the black point has a noticeable blue cast. It is compensated with curves in the lower-left corner of the graph (the black point) during calibration; on good monitors the black point need not be calibrated, and a white D65 may differ by a hair. Tonal breakup of a gradient into bands is, in most cases, fine — but chromatic breakup is a problem. On a good monitor even 6-bit can suffice for preparing graphics, though I still try to work on 10-bit monitors (1024 gradations), since I sometimes change the white point's chromaticity with a LUT. A wide gamut is needed, above all, to display colors that do not exist in print and advertising photography. The nonexistent color is cyan, which scarcely occurs in nature — you might see something like it only in the tint of Mediterranean water. That is what fits into a wide gamut and can be printed, but cannot be seen in sRGB.

### A practical aside: white balance in Photoshop

A quick, reliable method for good white balance in Photoshop:

1. Open the photo, duplicate it twice, and create a fill layer of `#303030` on top.
2. Change the top layer's blend mode to **Luminosity**.
3. On top of that, create a Curves adjustment layer and invert the position of the points.
4. Then merge the three top layers (the curves, the fill, and one of the photo copies), set the resulting layer's blend mode to **Color**, and adjust its opacity for a result that is pleasant to the eye.

![Photoshop white-balance setup](https://your-scorpion.ru/wp-content/uploads/2024/12/photo_2024-11-28_18-42-06.jpg)

## Vision

I have already written that the cause of the gigantic number of spectral colors lies in the *wavelength of the dominant light wave* (in fact, ultraviolet and infrared radiation also act on the eye, but produce no visual sensation). Through the eyes we receive 78% of our information about the surrounding world. 700 nm is red, 400 nm is violet, and in between lie all the other visible colors. We also know that only three parameters affect how a color looks — lightness, hue, and saturation — and that the human eye reacts to the brightness of the radiation, not to the quantity of light energy.

Perceiving the brightness of radiation requires an eye, a brain, light, and an object. The eye is built on the principle of an ordinary camera lens, with some very important nuances described below: yes, the human eye too has its own analog of an aperture and of depth of field. From school biology you know that the eye is made of rods and cones. Cones are for perceiving color; rods see everything in black and white and are ultimately responsible for form. At the center of the eye is the central fovea (Latin *fovea centralis*), where cones are very densely packed; the farther from the center, the fewer the cones. The optical angle of sharp vision is only 1.5 degrees — everything else we see blurred. When we look at an object, the eye itself turns its cluster of cones toward it and moves continuously — imperceptibly to us, but it moves. It is precisely thanks to these rapid movements that different radiation falls on different parts of the retina.

The inner lining of the eye is the retina, coated with rhodopsin, where the light-sensitive cells also reside; from them the information passes to the optic nerve and then straight to the brain. The middle of the eye is a jelly-like, transparent body called the vitreous. The cones sit on the retina, which contains light-sensitive cells for different regions of the light spectrum (violet-blue, green-yellow, and yellow-red). The eye has the most cones for green shades, which lets us spot danger amid abundant greenery; second most frequent are the blue shades — sky and sea. The retina works as the analog of a camera's sensor. The lens focuses light onto the retina, playing the role of a lens, forming the image and projecting the picture. Focal length depends on the radius of curvature, so our lens is not perfectly flat and can even change its curvature by means of muscles — this is called accommodation. Object close — shorten the focal length. In old age people develop presbyopia, which is precisely the loss of the lens's elasticity. Cones can sense light thanks to the pigment iodopsin, which is itself divided into separate pigments. There is also the aqueous humor, located in the anterior chamber. When we step quickly from a bright sunlit place into a dark room, we need time to adapt to the changed lighting — all because some pigments are sensitive to the yellow-green spectrum and differ in reaction speed from the rods. The rods react slowly, hence the need to get used to a sharply changed light. Over the years our ability to see violet/blue colors declines, as the lens gradually yellows, but this is compensated by visual experience.

On receiving information, our eyes and brain group the data by similarity and by the indivisibility of a structure into simpler elements. Elements grouped by similar characteristics give us the ability to perceive an image as a whole. So, when drawing an image, you should strive to create unifying links between elements, organizing the space conveniently for perception in tandem with the viewers' prior visual experience.

![Weak radiation is color to us; strong radiation is light](https://your-scorpion.ru/wp-content/uploads/2012/09/dsc_2993-164x300.jpg)

## Light Versus Color, and Albedo

If you need to give objects a clear boundary, you must play with the brightness of the objects. Radiation is light, and it belongs to the environment; color is the object itself. But what we see is the radiation, which paints the object in its local color — that is, color too is radiation, only not as bright. As Titian said, it is important to establish an object's local color as modified by reflected light. A sheet of paper caught in a strong stream of light energy will seem to glow, owing to the reflection of light waves.

It is important to understand that each color, at maximum saturation, has its own brightness. By changing the brightness we get something visually lighter or darker — but a color of a different hue.

The interaction between an object's illumination and the force with which the object reflects light energy is called *albedo*. As you recall, an ideally white surface reflects all the light energy that strikes it, so the albedo of ideal white equals one. But paper is not an ideally white object and absorbs the shades of its surroundings. After all, many of us have seen a very nice little white scarf in a shop that looks simply dreadful at home — the fault of the ambient lighting and of complementary colors.

## The Law of Complementary Colors

We must not forget this law: a color placed on, or beside, a colored background takes on a tint of the color complementary to the background. Take a red background, for example, and dab on a few gray spots — those gray spots will seem greenish. If the dots are dark gray, the green tint will lean toward yellow; if light gray, toward cyan. The contrast of a complementary color against the main color strengthens both (example: violet and yellow — a cool and very contrasty palette).

A good trick from the world of branding when analyzing competitors: cross out, on such a wheel, all the colors already taken by competitors, and consider only the colors that remain for the company's identity. True, the strongest colors are often already taken.

[![Color wheel](https://your-scorpion.ru/wp-content/uploads/2012/09/circ.png)](https://your-scorpion.ru/wp-content/uploads/2012/09/circ.png)

Complementary colors mix optically, which lets you step slightly beyond the technical limits of whatever color model you are working in. Finding out which colors are complementary is easy: the two, when "mixed," mutually annihilate to produce white, yet placed side by side they strengthen each other. It is precisely from the theory of complementary colors that we can explain why, after staring long at some local color and then quickly shifting the gaze to a white background, for a few moments we see — instead of white — the shade opposite the local color. This afterimage reminds us once again how strong complementary colors are. The brain "imagines" nonexistent shades on its own, and this strongly influences how a person perceives a picture. It also explains the "eye gone stale" problem, when you look at a frankly awful picture and it seems quite beautiful to you. The eye has a way of adapting, which is exactly why it is important to take breaks and look at something else. And it is better to use a gray environment when you work.

A good illustration of complementary colors at work: life vests are almost always orange, because that color is opposite the blue of water on the color wheel.

## Symbolism

If we go by color symbolism, white turns out to be an entirely positive color (although I once read that an abundance of white is a tendency toward schizophrenia) — but you cannot solve most tasks with white alone. A few more "top" colors help attract attention: red; a roughly equal bright blue (which, alas, cannot be had in ordinary CMYK); and a step below sit yellow and green. Calming: light blue, green, blue. Exciting: orange, yellow, red.

## A practical aside: how to learn to draw

A reader once asked how I would advise someone to learn to draw. My answer:

1. **Build the habit of progressing** and work out a strict schedule keyed to your habits (get up at 7 a.m., drink a glass of water, and so on). Attach cues to your habits (started drinking coffee → immediately picked up paper and pencil and began training the "living line"). Talent is nothing without practical skills, so it is important to build them.
2. **Structure your learning by the week.** Seven days is a short span, but enough to acquire a skill. Say you have a week of drawing the head of Apollo: the drawing on Monday and the drawing on Sunday will differ in quality. Don't forget to change the angle — it matters for building a sense of proportion.
3. **Develop gradually.** Composition and design develop through ornaments, especially corner and square ones, from simple to complex, from symmetrical to asymmetrical. Next, master drawing from a photo or from life (the contour) with layout, imagining objects as solid fills — the fewer the fills, the better. Always remember there must be a single, unified movement of the line. Then come the simplest things: proportions and textures. Here it is simple — you do studies and build the skill. Sometimes you will need to draw not only from photos but from life.
4. **Mastering color begins with tone.** The academic school teaches you to do tonal gradients; I would advise not to neglect this exercise. Try to guess the tone while looking at professional painters' work. By this stage you should already have learned to draw nice cubes, spheres, triangles, and other primitive forms, and you can move on to two-, three-, and four-tone copies of the masters' works.
5. **Copy lighting** from different weather conditions, seasons, and day/night. Understand that contrast differs at different times of day. And in general, small color copies of the masters' works are a good exercise.

## In Closing

During my studies I had one very good teacher — a fine specialist, not worn down by the academic school, and a thoroughly good man. He criticized no one and never said a person's work had blunders and flaws; there were always only wishes for the work. And here is one of his most important wishes: a work should contain no superfluous shade. To this day I more than agree.
