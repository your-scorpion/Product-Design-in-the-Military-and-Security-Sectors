# The Path of a Pixel: How Digital Color Management Works


This note will help designers understand the principle by which color is formed on a monitor and in Photoshop. All information about color traces back to the CIE experiment, which produced the spectral color-sensitivity curves corresponding to particular color sensations. In 1931 the experiment established that humans perceive colors well in the red, green, and blue regions of the spectrum; this gave rise to the CIE 1931 color model, and later to CIE XYZ. It is precisely CIE XYZ that sits in the middle of any RGB-to-RGB conversion. And every color model in use today is built on the result of that experiment. From its findings came the color solid and the color locus, which define the boundaries of visible color, letting us draw chromaticity diagrams and work with different color spaces.

A pixel's path to the monitor screen runs along the following chain — video card, display, cable, and ambient lighting:

1. The pixel's values are matched against the color profile set in the Color Settings dialog for the given color model. If a profile is embedded in the image, the values are taken from the embedded profile. The values are then converted to Lab.
2. The monitor profile is taken from the system settings, or — in its absence — sRGB (which, from a color-reproduction standpoint, is considered a huge blunder). The Lab values are converted to RGB according to the monitor profile and passed to the monitor driver. The monitor profile must be assigned to the monitor at the OS level; every modern monitor-calibration program does this without the user's involvement.
3. In the monitor driver the values are compensated according to a compensation curve. That compensation curve is created during monitor calibration and lives in the monitor profile; the resulting values are sent to the LUT of the video-card driver when the OS loads. The values are then sent to the monitor.

In sum: sRGB → Lab → RGB of the monitor. The sRGB → Lab step is handled by the sRGB profile, and the monitor profile is responsible for the Lab → RGB-of-the-monitor step. Lab contains a lightness map, and you can get interesting effects by manipulating that channel.

It won't hurt to understand how the conversion from RGB to Lab and back actually happens, and what we lose along the way. The core idea is that a color space can contain a certain range of color. The image the specialist works with has a narrower range than the wide-gamut color models. All existing variations of RGB (which I describe below) are matrix profiles, which means they can't carry CLUT tags for different rendering types when converting to other color spaces. Lab is also a matrix profile (with the exception of Bruce Lindbloom's UPLab), and Lab is moreover a representation of the XYZ color model. XYZ is very wide and precise, linear, and takes no account of the peculiarities of human vision — just mathematics in the form of coordinates on mutually perpendicular axes. So it's used only as the foundation for the models we find more interesting, such as Lab, with a further superstructure on top in the form of [CIEDE2000](https://en.wikipedia.org/wiki/Color_difference#CIEDE2000). CIE xy and CIE xyY, on the other hand, are already nonlinear, as are CIE Lab and LCH.

You're [familiar](https://your-scorpion.ru/printing-for-dummies/) with the four basic conversion types: Absolute Colorimetric, Relative Colorimetric, Saturation, and Perceptual. Without tags, the final conversion happens by the relative or absolute rendering method, and all colors wider in gamut than the target profile get clipped. So during a Lab → RGB conversion, loss of information when numbers are rounded is unavoidable. That's important to know, but it's even more important to know which color space suits developing photos from RAW into RGB while minimizing the losses incurred when working in Lab — namely the AdobeRGB, L-Star, and ECI RGB described below. RAW can hold either 12 or 16 bits per channel.

From here we move smoothly to the question of converting the RGB values of one space into the RGB values of another. On operating systems earlier than Windows 7 there was no proper color management, and the process of setting up display and interpretation of color was built around software platforms such as Adobe or Corel. So, if in Adobe Photoshop you create a document in the Adobe RGB (1998) color space and fill it with the solid color RGB (31, 121, 238), then after converting to sRGB IEC61966-2.1 (Edit → Convert to Profile), the RGB values turn into 0, 121, 242.

To keep track of fun of this sort, Photoshop introduced a separate **Working Color Space.** This is an intermediate space, configured in Color Settings. If you set it to Adobe RGB (1998), then all new documents will be created in Adobe RGB (1998), and that same profile will be assigned to images that have no embedded profile.

![Color Settings — Working Color Space](https://your-scorpion.ru/wp-content/uploads/2020/08/46464gdg.jpg)

The reason for such puzzling behavior lies in the algorithm for computing the conversion matrix:

- The gamma for Adobe RGB is computed by raising each channel to the power of 2.2.
- Conversion from an 8-bit value is done by dividing each channel by 255 (2 bits would be monochrome).
- Chromatic adaptation to D50, if the source and target RGB values have different color temperatures. D65 is actually considered more universal, but I recommend D50, since the reference and verification point for print work is the monitor.
- The final AdobeRGB → sRGB matrix is obtained by multiplying the XYZ → sRGB and AdobeRGB → XYZ matrices.
- And data normalization: all negative values are replaced with 0, and all values greater than 1 are reduced to one. This is exactly the data loss I wrote about above. The algorithm described corresponds to Relative Colorimetric.

![Conversion matrix](https://your-scorpion.ru/wp-content/uploads/2013/09/Untitled-1.png)

In Adobe Photoshop everything is more complicated than in the operating system. Adobe has its own engine; in Color Settings you can choose either the Adobe engine or the system engine for conversion. Other programs use the LinoColorCMM engine on Windows and ColorSync on Mac. The mechanism for converting one color space into another is called Windows ICM.

A monitor profile consists of two parts:

- **Compensation curves** — what the monitor's settings are brought into a straight-line form by (correcting the gray balance). This is applied to everything shown on the monitor, always.
- **A mapping of the numbers arriving at the monitor to a specific color in the coordinates of a device-independent color-representation model (XYZ),** which can only be measured by a hardware calibrator — a spectrophotometer or colorimeter. It's important that the instrument not be old, or the gelatin filters may have lost their correct transmission coefficient. And for correct display you need the inverse mapping — which numbers to send to the monitor to obtain a specific color defined in XYZ coordinates — and it's this table or matrix that the profile contains. It is obtained ONLY through hardware calibration; software won't help here, and without this part of the profile a faithful display is impossible.

You can't opt out of using a monitor profile in Photoshop. If a monitor profile is absent, sRGB is used. As you'll appreciate, display without a monitor profile will be wrong by definition. The "correct solution for the lazy" is to use the monitor's factory profile, or to ask a kindly print-shop monster for a hardware-calibrated profile. But that isn't the recommended way.

Calibration consists of two stages: the calibration/linearization itself, and characterization/profiling. The calibration process touches the following values: the screen's black and white points, the color temperature, and the gamma pre-distortion. The profiling stage fixes those values and records them into a dedicated monitor profile. The white-point and color-temperature parameters depend directly on the lighting conditions, which is why factory profiles — even for "monolithic" computers like the iMac — will never be the ideal choice for you.

Banding, by the way, shouldn't appear after profiling. If it does, either something went wrong in the process or the monitor itself is poor — which is unlikely, since modern monitors are very good, unlike the old CRT displays. It's worth checking what you profiled with: the venerable [DisplayCAL](https://displaycal.net/) is free, works with any calibration device, and remains the modern go-to.

Let me give the characteristics of the RGB working spaces.

**sRGB** — a joint development by Hewlett-Packard and Microsoft to represent an averaged monitor. Most of us use monitors whose color range is larger than sRGB, and for printing sRGB is rather mediocre (no good at all); it's better suited to preparing images for the internet, but you should work in wider color ranges. sRGB has characteristics that are too smoothed-over for professional work and falls short, in gamut, of any modern monitor. It was originally created to align the working color space with CRT monitors calibrated to a gamma of 2.2. Put simply, sRGB is a finished image for viewing, not for working. The point of sRGB is the guarantee that on all monitors and printers the picture will be shown roughly the same — but it's no good for color correction.

The Windows operating system itself has no effect on how color is displayed in other software. The system holds device-profile associations, one of those devices being your monitor. In Photoshop you can set color display based on the Windows CMS, as in any other professional software such as RIPs or RAW converters, which carry their own profiles and device drivers entirely. The conclusion is that all color-display settings in software should live in the software itself.

So what should you work in? Let's look at the other color standards.

**Apple RGB** — the standard space of Photoshop 2.0. It's oriented toward Apple's 13-inch RGB monitors, and its gamut is only marginally wider than sRGB, which is sad. The gamma of 1.8 used in Apple RGB provides no visual uniformity and leads to posterization of shadows even faster than sRGB. So although this color space differs somewhat from sRGB, it's essentially no better.

**CIE RGB** — a development of the Commission Internationale de l'Éclairage. It has a very wide color space, covering all of human visual perception, which makes it very inconvenient for working with 8-bit images, plus posterization problems. It also has problems with darkening of the blue.

**DonRGB4** — a fairly wide gamma of 2.2, with the industry-standard white point D50. It almost completely covers Ektachrome and is used by many world-class professionals. It has a twin brother, BestRGB, which differs by a slightly extended range of available colors, but doesn't exceed CIE Yxy.

**MaxRGB** — exceeds even CIE Yxy. Lets you work with very unnatural colors.

**ColorMatch RGB** — truncates shades of cyan, though on the whole it's a very good option for print. A touch wider than sRGB. Among its downsides is the gamma of 1.8, which reduces detail in the shadows.

**NTSC (1953)** — an old video-broadcast standard from North America. Its gamut is fairly wide but insufficient for working with 8-bit color, and it pushes whites toward yellow. In general, it's fine for working with video.

**PAL/SECAM** — a gamut close to Apple RGB; the video-broadcast standard for Europe and certain Asian countries.

**Adobe RGB (1998)** — its gamut almost completely covers all of CMYK, and the gamma of 2.2 gives color uniformity. It works especially well for conveying desaturated and dark shades. At gamma 2.2 the dark areas aren't always correct, so I sometimes use AdobeRGB (1998) GammaL, or set gamma 1.8 for offset. Across the brightness range 0–255 the monitor will show every shade of gray from 0 to 255. But we'll clip the brightness shades above 1.8, and everything above that is a white sheet with no ink. Among Adobe RGB's downsides are the problems with green described in one of my previous articles. Its very wide gamut of green shades yields excess information that can't be printed — that's a fact. For landscape photographers, though, that fact is interesting, because it gives an enormous color range in juicy reds and bright greens. Vivid landscapes are done in AdobeRGB, but here, frankly, Lab has the advantage, since it lets you make colors saturated and bright at the same time. Fogra39 has more cyan than Adobe RGB. All print specialists sit on Adobe RGB, and they fight the lack of full coverage of all the pure CMYK colorants like this: they don't give a damn, and occasionally use Proof Colors. If, in Color Settings under Working Space → RGB, you put "Monitor RGB" instead of "sRGB IEC61966-2.1," then normal colors for the tasks described above will be displayed without needing to turn on "Proof Colors." AdobeRGB provides visual coverage on monitors with extended gamut, but the final output materials may be wider in gamut than AdobeRGB. And Adobe RGB (1998) is the recommended space for your photo cameras.

It's worth pausing here on the "Monitor RGB" trick, because it's controversial — plenty of articles and respected forums recommend turning on Proof Colors with a soft proof for Monitor RGB, and I caution against it. Here's why. Proof Colors is merely a simulation of how colors will be displayed; the feature exists to check how an image will look in print or on a device. But every window of every Windows application uses the system CMS, with sRGB as the input profile and the monitor profile as the output profile. Proof Setup together with Monitor RGB switches off Color Management, using the monitor profile plus Preserve RGB Numbers — so Monitor RGB can only show the color that's used after color management has been forcibly disabled. That means several conditions have to coincide: a similar monitor with an equivalent color profile, and a viewer program with no color transforms. There's a related misconception that if you want to see a picture in the colors available to your monitor, you should go to View → Proof Setup → Custom, specify your monitor's profile in "Device to Simulate," and clear the "Preserve RGB Numbers" checkbox — but the Photoshop CMS recalculates colors from the working space into the active monitor's profile, and the monitor shows nothing new. The right move with that approach is to enable View → Gamut Warning and see the regions of the image the monitor can't display correctly. In short, many specialists, having downloaded Margulis's book and bought a feeble monitor with weak sRGB, work by the numbers in AdobeRGB; such specialists need to specify their monitor's profile in "Device to Simulate" with "Preserve RGB Numbers" off, and after enabling Gamut Warning, watch for the areas the monitor renders incorrectly. My own main use case for Proof Colors: a monitor with almost-AdobeRGB gamut, and when saving graphics for the internet I set an sRGB profile in "Device to Simulate" without Preserve RGB Numbers and enable View → Proof Colors to check the color.

![Proof Colors workflow](http://your-scorpion.ru/wp-content/uploads/2017/09/main_screen-1.png)

JPEG files work with only 8 bits. That means you get 256 reds, 256 greens, and 256 blues, regardless of whether you use Adobe '98 or sRGB. In total that's 16.78 million colors. Very simple arithmetic: 8 bits each for red, blue, and green, so 2⁸ = 256 distinct color representations per channel, for 256 × 256 × 256 = 16,777,216 colors total. Adobe '98 is wider in color range, and to fit within the range available to JPEG you have to make "coarse" transitions between color shades. Of course, there are tasks you can't solve in sRGB — the HP logo, say, or a pure cyan — but for the web sRGB is usually preferable. Adobe '98 is better for magazines, or if you have an inkjet printer that can reproduce colors beyond sRGB. But in that case it's better to work not with Adobe '98 but with ProPhoto. Also, the new 4K televisions with 10-bit or higher resolution can reproduce 1024 shades of each color, allowing for over a billion colors.

Since RGB and HEX come up constantly here, it's worth being precise about how they relate. `#000000` specifies a color in the sRGB space. To convert HEX into the associated RGB coordinate, the values are divided by 0xFF (255). For example, `#566698` is `rgb(86, 102, 152)`, or roughly (34%, 40%, 60%), so the coordinate becomes (0.34, 0.40, 0.60). After that comes gamma correction — the very same 1.8 for ProPhoto RGB, 1.93 for Rec.709, 2.2 for sRGB/Adobe RGB, and so on — while the L curve from Lab imitates the gamma correction of the human eye, using a gamma of ≈ 2.45. In WebGL, on which all the complex products on the web run, gamma works in linear space, and a color after rendering ought to be 255 × 0.5 ≈ 128, but a different gamma is applied and instead of 128 you get a lighter ≈ 187. Unscientifically put, each RGB coordinate is the effect of a luminous flux on the retina, whereas Lab is responsible for the retina's response to that effect, since color is a human sensation. You can also append transparency in HEX: for a 40% alpha on pure black `#000000` you append `66`, giving `#66000000` — that is, the Android mask `#AARRGGBB`, or the browser's `#RRGGBBAA`.

<details>
<summary>HEX alpha-transparency lookup table (percent → hex)</summary>

```js
const hexTransparency = {
  100: 'FF', 99: 'FC', 98: 'FA', 97: 'F7', 96: 'F5', 95: 'F2', 94: 'F0',
  93: 'ED', 92: 'EB', 91: 'E8', 90: 'E6', 89: 'E3', 88: 'E0', 87: 'DE',
  86: 'DB', 85: 'D9', 84: 'D6', 83: 'D4', 82: 'D1', 81: 'CF', 80: 'CC',
  79: 'C9', 78: 'C7', 77: 'C4', 76: 'C2', 75: 'BF', 74: 'BD', 73: 'BA',
  72: 'B8', 71: 'B5', 70: 'B3', 69: 'B0', 68: 'AD', 67: 'AB', 66: 'A8',
  65: 'A6', 64: 'A3', 63: 'A1', 62: '9E', 61: '9C', 60: '99', 59: '96',
  58: '94', 57: '91', 56: '8F', 55: '8C', 54: '8A', 53: '87', 52: '85',
  51: '82', 50: '80', 49: '7D', 48: '7A', 47: '78', 46: '75', 45: '73',
  44: '70', 43: '6E', 42: '6B', 41: '69', 40: '66', 39: '63', 38: '61',
  37: '5E', 36: '5C', 35: '59', 34: '57', 33: '54', 32: '52', 31: '4F',
  30: '4D', 29: '4A', 28: '47', 27: '45', 26: '42', 25: '40', 24: '3D',
  23: '3B', 22: '38', 21: '36', 20: '33', 19: '30', 18: '2E', 17: '2B',
  16: '29', 15: '26', 14: '24', 13: '21', 12: '1F', 11: '1C', 10: '1A',
  9: '17', 8: '14', 7: '12', 6: '0F', 5: '0D', 4: '0A', 3: '08',
  2: '05', 1: '03', 0: '00'
}
```

</details>

One important nuance: before converting a picture from one color model to another, it's advisable to get rid of layers that contain transparency. If you take an AdobeRGB picture with transparent gradients and convert it to sRGB, many of the gradient stretches will be ruined.

![AdobeRGB vs sRGB](https://your-scorpion.ru/wp-content/uploads/2013/09/argb-vs-srgb-600x600.jpg)

The widest — and therefore excessive — color space is **ProPhoto RGB**, and the most popular profile among photographers is **ProPhoto RGB (D50)**. If you convert ProPhoto RGB to sRGB, an enormous part of the gamut will be lost. The alternative is Adobe RGB, with 52.1% color coverage. This isn't bare theory — for print work such nuances are deadly important. For example, six-color Epson inkjets are able to put the needed saturation onto paper such that cyan is covered only by AdobeRGB while yellow is covered only by ProPhoto RGB.

Try to keep photos destined for a glorious future of luxurious printers in ProPhoto RGB, since a camera like the 5D Mark III can capture more colors than fit in AdobeRGB 1998. Or Melissa RGB, identical to ProPhoto in gamut, differing in gamma. ProPhoto RGB Gamma 1 also runs in the background of Lightroom.

For video it's better to use the Rec.709 (35.9% coverage) or Rec.2020 (75.8% coverage) color spaces. Rec.709 is used for modern HD televisions. While working, you can use Rec.709 D60 sim for the viewer so the color matches for all project participants (after first calibrating the monitor). With an uncalibrated monitor the correct move is to switch to sRGB (D60 sim.). The new Rec.2020 profile was developed for 4K televisions and offers a far wider palette. Among the less common ones, you can look toward Melissa RGB and MaxRGB, which are comparable in gamut; MaxRGB dominates in the blue-green and yellow regions.

Color and codecs are also where a lot of video grief comes from in practice. A common scenario: a clip looks fine locally but muted once uploaded to YouTube, even though downloading it again restores the color, and the preview thumbnail (an automatic screenshot taken before compression) shows correct colors while playback degrades them. Ideally you'd work uncompressed, and when h.264 is uploaded to YouTube or Vimeo the colors should be reconstructed; if free space is tight, an image sequence comes out smaller than uncompressed. I then compress to h.264 — Vimeo's native codec and seemingly the only codec in the Adobe package able to squeeze 4K — though it hits the blacks hard; sometimes I use QuickTime JPEG, which has deep blacks and vivid colors but weighs a lot and gets re-compressed to h.264 by the hosts anyway. It's also worth setting the right value in your video-card driver's color settings:

![Correct monitor color settings](http://your-scorpion.ru/wp-content/uploads/2016/10/Screenshot_3.png)

In general the video hosts are very stingy with bitrate and with color-space handling. Exporting **ProRes** specifically, I usually take one of two routes. The first is the free [ffmpeg](http://ffmpeg.org/ffmpeg.html) converter, which exists only in bare command-line form, though a dedicated [script](https://www.dubon.fr/conversion-prores422/) for After Effects usually does the job — its downside being that ffmpeg has no ProRes decoder of its own. The second is to drop [Appleproresdecoder.qtx](https://support.apple.com/downloads/Apple_ProRes_QuickTime_Decoder_1_0_for_Windows) into `C:\Program Files (x86)\QuickTime\QTSystem` (or the `MProResCodec.qtx` file into the same folder), then render with the QuickTime codec and choose Mirazon ProRes in the settings — not an ideal option either, with occasional alpha and color problems on Windows, and you'll need QuickTime installed. There's also the paid [AfterCodecs](https://aescripts.com/aftercodecs/), but by the reviews it works slowly and badly.

These days it's customary to calibrate monitors to the sRGB gamma. This yields an averaged color, suitable even for consumer photo printing. Sacrificed are all the acid-bright shades of lime and green, the blue shades in the shadows and any bright cyan, and predictability with red. Modern monitors may offer an sRGB-gamma setting, so if you work in the media industry you need gamma L (2.4). You can use a VCGT curve to correct the sRGB gamma and you'll see all the details and shades in the deep shadows. Or use 2.2, confident that the end user will see your work exactly that way. In ancient times the gamma power function was V = v^(1/g), a very simple formula. The modern sRGB gamma function is far more complex and has more felicitous compensations, especially for working with shadows and midtones.

**CIE v2 RGB** — its coverage of the cyan and red regions is somewhat better than Adobe RGB. Recommended for film production.

**SMPTE-C** — the current standard for video in the USA. A very narrow gamut; no sense bothering unless you're doing work for the US market.

**Wide Gamut RGB** — possesses an enormous gamut, with the result that the slightest changes render a 24-bit image unusable. As does Lab, with its posterization. Compared with Adobe Wide Gamut RGB, these profiles have different white points, so part of the color range doesn't overlap.

**Simplified Monitor RGB** — this is effectively the ICC profile of your monitor. Unlike the other spaces, it's tied directly to your monitor and is not device-independent. On another computer the image will look different.

![Monitor profile](https://your-scorpion.ru/wp-content/uploads/2013/09/IMG_4661.jpg)

If you ever want to build a custom space, Photoshop allows it: Edit → Color Settings → RGB → Custom RGB. In the window that appears you can flexibly control the RGB parameters and create your own profile. Here, for example, are the settings for RussellRGB — wider in gamut than Adobe RGB but smaller than ProPhoto RGB, with a white-point basis of 5500 K:

![Custom RGB — RussellRGB](http://your-scorpion.ru/wp-content/uploads/2017/01/Screenshot_3.png)

For CMYK there are also abstract color-reproducing devices, such as Euroscale Coated, SWOP Coated, SWOP Uncoated, and others, but a review of them is beyond the scope of this note.

It should be noted that if you work with a full CG render, the full color range is available to you. If, instead, you work with shot footage, you depend on the camera's capabilities. From a render you get an image in linear space, or a logarithmic (log) one when shooting on a good camera. If you're a CG artist, you should settle on two color models convenient for the work — HSL and Lab — and remember about LUTs. From the toolkit you'll need: white- and black-balance correction, selective correction of individual regions, masked correction, and a color script (a sketch of how the future film should look in color and what lighting will be used in different episodes). In software like Nuke and DaVinci everything is fine with the tools described above; for After Effects you need to add plugins: Test Gear, Colorista II, DV Rebel Tools, Magic Bullet Looks.

While we're in After Effects: those channel curves have an annoying habit of grabbing the wrong channel — you drag what you think is the master curve and the red one moves, so you undo, re-aim, and try again. As best I can tell, this is a bug: the RGB curve is *shown* as active by default, while the genuinely active curve is whichever one was used last. The workaround is to dip into the curve-selection menu each time, pick something other than RGB, and switch back; there's no magic button, though zooming the curves in makes aiming easier. Better still, use SA Color Finesse 3 and forget the trouble, or at least the Lumetri from a recent AE version.

HSL deserves its own warning here, since its principle differs sharply from the familiar RGB. The channels remain as layers with a range from 255 to 0, but the red channel is substituted by Hue, the green channel becomes Saturation, and the blue takes charge of the picture's Lightness. Hue is wavelength — color, simply put — and saturation is the purity of the color. (One 8-bit pixel occupies one byte of disk space; one kilobyte equals 1024 bytes, one megabyte equals 1024 kilobytes.) It's better to use LUT converters for RGB → HSL and HSL → RGB. The problem is plain if you open any RGB picture and apply Filter → Other → HSB/HSV to turn it into HSL, then go into Channels and look at the red channel (now formally the Hue): you'll see black parasitic pixelization with no antialiasing — the result of the wrap from 0 to 360 degrees.

![HSL Hue channel pixelization](https://your-scorpion.ru/wp-content/uploads/2016/12/Untitled-1-1.jpg)

So it's better to look toward plugins or third-party tools for HSL work. There's a good [plugin](http://nuclearlight.net/manual.html) that works well, the [C3C Color Wizard](http://www.uni-vologda.ac.ru/~c3c/plug-ins/colorwizardbasic.htm) which can even work in an orthogonal color model, and a handy color-model [calculator](http://davidjohnstone.net/pages/lch-lab-colour-gradient-picker) worth keeping bookmarked.

And if you need to build LUTs from a reference, the simplest path is to take a Hald CLUT look-up table with the changes you want and convert it to a LUT — easily done in Photoshop via File → Export → Color Lookup Tables. Otherwise, buy the little program 3DLUT Creator.

The essence of how color is represented on output devices should now be clear. I hope you'll now have fewer problems with posterization.

---

And, of course, **the correct color-management process in Photoshop for the web.**
Edit → Color Settings: Settings: Custom, RGB: — if there was no hardware calibration, then sRGB (the advice holds for the internet and minilab). When your working space and your picture both live in sRGB, everything will display normally even without an ICC profile. If your working space is Adobe RGB '98 and you didn't convert your document to sRGB on save, the picture will come out too saturated.

**When creating a document**
File → New → Color Profile → Don't Color Manage This Document
**When saving**
File → Save As… → leave the checkbox next to the ICC profile in the settings ticked. No ICC profile embedded in the picture = a lottery. If the lottery doesn't suit us and we want the picture to look good in everyone's browser, then under File → Save for Web… you need to leave the "Convert to sRGB" checkbox ticked.

Photoshop CC can work with both Windows ICM and Apple ColorSync.

Lab in Photoshop, on the other hand, isn't entirely honest. The white point isn't absolute but depends on the color space. The CMYK values in an ICC profile don't match the analogous ones in Photoshop's Lab. For printing on coated paper the maximum black is Lab = 16, 0, 0. But without converting from CMYK to Lab by the absolute method you'll see all sorts of other values.

Viewing CMYK images with their embedded profiles in mind can be entrusted to FSViewer (except for .PSD). And for re-converting into other CMYK profiles people use [drycreekphoto](https://www.drycreekphoto.com/).

It's worth distinguishing assigning a profile from converting one, since people mix them up. A profile carries information about the color-reproduction characteristics of a particular device, and from it a lookup table of color values is built; a profile may also be not for a device but for an editing working space — idealized profiles with no real device behind them, needed for exchanging images and editing graphics. Any profile carries a kind of "dictionary" for converting one set of numbers into another, where the numbers describe color. If you *assign* a profile to a document, the numbers in the file don't change — you've swapped the "dictionary," and by the new dictionary the same numeric values correspond to different color values, so the image on the monitor changes color. If you *convert* the document with the new profile in mind, the final numbers describing each pixel change.

### Technical matters

If you want to get the most out of Photoshop in terms of color work, you can enable 30-bit color. Most modern monitors can show 5–6 bits per channel with interpolation, and the sRGB profile is tuned for such monitors. Expensive monitors can put out 8 bits per channel, which corresponds to 256 shades of gray. Those that can put out 10 bits per channel can delight you with as many as 1024 shades of gray. Accordingly, you need a monitor that supports 30-bit color — usually such monitors carry a "Deep Color" sticker. The video card must also be able to work with 30-bit color. Deep Color increases the depth of detail of each color, while xvYCC widens the gamut of reproducible colors (IEC 61966-2-4).

You'll need HDMI ver. 1.3 or Dual DVI; the specification defines bit depths of 30 bits (1.073 billion colors), 36 bits (68.71 billion colors), and 48 bits (281.5 trillion colors).

If you have all the necessary hardware, you need to enable 30-bit color support in the video-card drivers. Then, in Photoshop, go to Preferences → Performance → Advanced Settings and tick the "30 Bit Display" checkbox. Done! Now you can view pictures in far greater detail than medical workers with their specialized 16-bit monitors.

This question of how good a display is for serious work comes up constantly, especially around HDR and Apple hardware, so a few practical notes. There's an [Ultra HD Alliance](http://www.uhdalliance.org/) that developed contrast and color specs for HDR displays: for LCDs, brightness from 1000 cd/m² with a black level no higher than 0.05 cd/m² (a contrast ratio above 20,000:1); for OLED, peak brightness above 540 cd/m² and a black level below 0.0005 cd/m² (a ratio of 1,080,000:1; a good figure back in 2012 was 10,000,000:1). Resolution should be 4096 × 2160 (4K), with the minimum for "Ultra HD Premium" being 3840 × 2160 plus a 10-bit color signal, and the gamut should exceed sRGB/Rec.709 — DCI-P3, the cinema standard, for instance. If you do graphic design professionally, suitable professional models start at 32 inches, though there are sensible 27-inch ones, and for 10-bit work you'll need a capable video card; to economize you can sacrifice gamut (but AdobeRGB 99% minimum), frame rate, and resolution. When choosing between panel types, IPS is better and pricier than VA for viewing angles — but even on an expensive IPS you likely won't see several shades in the shadows (gamma 2.2, brightness 80–90 cd/m²), and Photoshop won't help; you'll have to fiddle with VCGT at the monitor level to fit the monitor's characteristics to the TRC curves, since it's the TRC that Photoshop leans on. Higher refresh rate is better, though high refresh is usually a VA trait; high pixel density is good if you're willing to scale the interface; and the video card must support the monitor's maximum resolution.

As for the perennial belief that Apple machines are inherently better for designers: a Mac is a fairly high-quality solution, but not the best. An Apple MacBook Pro has on the order of 91–93% sRGB coverage, and Mac OS X is conventionally held to be not very tightly fitted to sRGB — the colorimetry of its primaries doesn't coincide with that of the sRGB primaries, so a compositor or photographer must compensate for a strong dip in the shadows using i1 Profiler. But it all depends on the specific model, since the iMac 4K/5K from 2015 onward and the MacBook Pro from 2016 onward are tuned for the P3 gamut. A meaningful plus: Safari can show pictures in Adobe RGB (1998) under certain conditions, and in Mac OS X the monitor profile is used by all programs by default. The gamma story is related: 1.8 is rarely used even on a Mac these days, since it suits print tasks poorly. Gamma 1.8 once made the Mac better for professional printing, but printing has become far simpler and the ubiquity of 2.2 has won out, so today gamma is set to 2.2 even on Apple devices. For more down-to-earth tasks — viewing photos from an iPhone 5 (gamma 2.4), say — you can use gamma 1.8 in the sRGB profile: iPhone photos in sRGB 2.2 differ from what you see on the phone's screen, while at gamma 1.8 it's just right. (The iPhone X, for its part, ships with a gamma of 2.22.)

Calibrating a phone screen, incidentally, [can and even should be done](http://www.xrite.com/colortrue) on the devices from which you show a client the working version of a mobile app.

---

If you were attentive enough while working in Photoshop, you've surely noticed extra symbols in the title of an open file.

![Title-bar profile symbols](https://your-scorpion.ru/wp-content/uploads/2013/09/4747363.jpg)

**RGB #** means the document has no embedded profile; the image is shown on screen according to Photoshop's settings. **RGB \*** is a value that should please you, since it indicates that a color profile different from Photoshop's settings is embedded in your document. **RGB** with nothing indicated means the document has an embedded profile identical to the one in Photoshop's settings.

Another display matter is tied to View → Proof Setup. In this menu you can set up the document's display so that it's as close as possible to the printed version. For that you need the color profile of the monitor on which the soft proof will be done, and the color profile of the printer on which the printing will be done. Of course, not everything is so rosy and ideal. Old monitors, even ones able to use the sRGB gamut, can't display the whole available spectrum of colors, especially for glossy paper; for a correct Proof Setup display for glossy paper you need a monitor up to 107% of Adobe RGB '98. You can also enable View → Proof Setup → Internet Standard (sRGB) to check how your picture will look in sRGB.

But there's a catch with Photoshop's soft-proofing algorithm. RGB profiles are usually matrix profiles, and direct conversion into CMYK is equivalent to clipping all the information above the final profile's gamut — that's how the colorimetric rendering algorithm works. Photoshop's soft-proof method is a simple linear transformation, with no account taken of the difference in compression and rarefaction during compression. So an on-screen soft proof is not a precise method.

Proof Setup and Proof Color are also closely tied to Relative Colorimetric and Perceptual rendering. In most cases you should have Relative Colorimetric selected. Relative Colorimetric more often gives the best results for preserving a photo's natural color. Perceptual rendering is good when an image contains many color shades that fall outside your printing device's gamut. You can set Saturation if you plan to print drawn graphics (greeting cards). Briefly: when we fit a larger gamut into a smaller one, it makes sense not to clip the image's saturation but to shift it a little in hue, preserving the distances between hues. That's what Perceptual Colorimetric does. If you use Relative Colorimetric, you simply preserve lightness while expanding the color region for compression.

There's a practical way to *see* one gamut against another, by the way, and it's worth knowing. Since this article is about color on the monitor, take RGB — CMYK is for print and Lab for color correction — and compare sRGB with AdobeRGB. First create a new Lab document in Photoshop and draw a gradient across the a and b channels:

![Lab a/b gradient](http://your-scorpion.ru/wp-content/uploads/2017/08/shot_170802_130721.png)

Then choose View → Gamut Warning, and from the same menu Proof Setup → Custom, selecting your monitor's profile (the one named in the monitor settings) in the window that appears:

![Gamut Warning custom setup](http://your-scorpion.ru/wp-content/uploads/2017/08/shot_170802_124655.png)

You'll get a cheerless gray picture with a white blob inside, but if you run the right-hand slider in the Levels settings for Lightness, the region of displayed colors will change:

![Levels lightness slider](http://your-scorpion.ru/wp-content/uploads/2017/08/record_170802_132431.gif)

Now duplicate the window and apply different soft-proof settings to the new one. You'll be able to see, plainly, the difference in range between sRGB and AdobeRGB:

![Comparing sRGB and AdobeRGB ranges](http://your-scorpion.ru/wp-content/uploads/2017/08/shot_170802_124655-1.png)

Pulling a picture cleanly into monochrome runs into the same gamut-and-gamma logic, and different tools give different results for a reason. Desaturating with Hue/Saturation is not the same as Vibrance, because in Photoshop "Brightness" is the intensity of the RGB pixels, not the intensity of light, so the Lab Lightness behaves differently depending on which tool you desaturate with:

![Monochrome — Hue/Saturation vs Vibrance](http://your-scorpion.ru/wp-content/uploads/2021/04/colors-e1619073440575.jpg)

Going through Menu → Image → Mode → Grayscale gives yet another result — yellow comes out too light, blues too dark. A good practice is to use Ctrl+Y (Proof Colors) after first setting a profile under Menu → View → Proof Setup → Device to Simulate → Working Gray → OK; you can pick other gray profiles too, such as "Gray Gamma 2.2" for Windows and "Gray Gamma 1.8" for very old Macs:

![Working Gray proof setup](http://your-scorpion.ru/wp-content/uploads/2021/04/Screen-Shot-2021-04-22-at-10.36.06-AM.png)

Adjustments → Black & White is an excellent tool. And never use Desaturate, which is algorithmically equivalent to Hue/Saturation — especially on pictures with a lot of yellow.

There's a CMYK-side counterpart to all this that bites in InDesign: when exporting to PDF and assigning a color profile to the images, black text laid over a picture can also get converted to CMYK — picking up the background colors — while text on white stays a clean 100% black, as it should. The causes are many. Make sure Simulate Overprint is off; check that you haven't set "Include Destination Profile" instead of "Don't Include Profiles"; don't convert images through InDesign at all — do it in Photoshop; and bear in mind that the profile itself isn't to blame, though US Web Coated (SWOP) v2 can push grays toward greenish and pinkish casts, much depending on the print house. From the very start, assign colors in CMYK for vector elements in InDesign. And to review the finished print file, use Acrobat's Output Preview: if an ICC profile is embedded in the PDF, the file is shown with that profile; if not, the default profile specified in Acrobat Reader is used. You can take any profile, drop it into `C:\WINDOWS\system32\spool\drivers\color`, set it as the default in Acrobat Reader (Edit → Preferences → Color Management), and the file will be viewed with the profile you want.

While shapes are on the subject: white pixels sometimes show through where filled shapes intersect, and they can be dealt with. Extend the vector shapes under one another to avoid the white pixels. But that won't solve every blending problem between pixel colors on different layers. Press Shift+Ctrl+K to open the color settings and change "Blend RGB Colors Using Gamma" to 1.5 — a value that yields a transition neither darker nor lighter than expected.

![White pixels at shape intersections](http://your-scorpion.ru/wp-content/uploads/2016/12/Screenshot_3-2.png)

Other values lead to less than aesthetic results:

![Blend RGB gamma — other values](http://your-scorpion.ru/wp-content/uploads/2016/12/Screenshot_3-3.png)

The setting works only for RGB. It plays a role in rendering semi-transparent regions by correcting gamma, and gamma is almost always different from 1, the most common being 2.2 and 1.8. There's an analogous setting for text rendering, "Blend Text Colors Using Gamma." It's important to understand that using a gamma of 1 for light text on a dark background makes the text visually heavier than the equivalent dark text on a light background. Screens are usually calibrated to 1.8, 2.2, 2.0, or 2.4, and televisions to 2.4, 2.5, 2.6. For print the standard is considered to be gamma 2.2 or L, for cinema 2.4, 2.6, Rec.1886, and for medicine DICOM.

A note on render color temperature, since neutral gray is a frequent question: in V-Ray, Corona, and RedShift 6500 K is considered neutral, but even so a render will show a slight blue cast, because that's physically correct. There's a handy table showing that 5600–5800 K is the temperature closest to white:

![Color-temperature table](http://your-scorpion.ru/wp-content/uploads/2017/09/photo_2017-09-06_16-15-57.jpg)

**Mythology.** The internet is full of popular myths — that Apple devices are a priori better at color reproduction than IBM-compatibles, say, or that a display is calibrated for a specific printing device.

Working with raw footage brings its own color setup, and the right place to start is the RAW converter. If you've been handed a DNG raw sequence whose colors look very pale on ordinary viewing, start from there. For images coming off a camera it's better to use matrix profiles such as RGB; such profiles always convert to one another as Relative Colorimetric Intent, including for output to the monitor, and in Photoshop you'll end up with sRGB (standard) or Linear sRGB (linear images) anyway. Along the rest of the chain — be it Maya, Unreal Engine, or DaVinci Resolve — it'll be OpenColorIO. If you plan to work with textures by the ACES standard, OCIO can convert sRGB to ACES; recent Photoshop versions can take the ACEScg color profile and apply it to 32-bit floating-point images, and we exclude any color management at the Photoshop level by clicking "Don't Color Manage this Document." A list of ready OpenColorIO configs lives [here](http://opencolorio.org/CompatibleSoftware.html). You install the plugin into AE, download the needed configuration file, and export DPX to sRGB.

![OpenColorIO in After Effects](http://your-scorpion.ru/wp-content/uploads/2019/01/Untitled-1.png)

If compositing is needed, here it is step by step, using ACES as an example. Download OpenColorIO for After Effects and the OCIO configurations, then go to View → Use Display Color Management and set the color space to acescg. When importing EXR/HDR into the project, enable Preserve RGB; for 8-bit files turn Preserve RGB off. Create an adjustment layer and place it above the rest, applying Utility → OpenColorIO to it with **OCIO Configuration:** aces_1.0.3, **Input Space:** Output – Rec.709, **Output Space:** ACES2065-1. Create one more adjustment layer above the OpenColorIO layer, set it to GUIDE MODE, and again apply Utility → OpenColorIO with **OCIO Configuration:** aces_1.0.3, **Input Space:** ACES2065-1, **Output Space:** Output – Rec.709. The result is a sandwich: an OCIO adjustment layer doing ACES2065-1 → Rec.709 as a guide layer on top, your effects in the middle, and an OCIO adjustment layer doing Rec.709 → ACES2065-1 at the bottom. To export all this, choose OpenEXR Sequence (Floating Point+), Format Options → Zip16, and make sure Color Management → Preserve RGB is on.

---

An important nuance of color work is your workplace. Everything around you can affect how you perceive the image on the monitor. The thing is that, as a result of the visual system's chromatic adaptation to changes in the spectral composition (color) of light over the course of the day, changes occur in how you perceive the monitor's image. This is exactly the problem when, after calibrating the monitor, all images take on some pronounced color cast — the lighting conditions at the workplace simply don't let you get used to the monitor's configured color temperature. Beyond that, our vision is in a constant state of changing illumination, from day to night, because of which the illuminance of objects changes by a factor of thousands: from roughly 100,000 down to 0.1 lux. This phenomenon is called brightness adaptation.

That was theory; now practice. What matters when setting up a workplace? Make sure there are no objects in your field of view brighter than the monitor's white point, and no objects darker than the monitor's black point. Light falling on the screen also affects the monitor's color temperature; a hood is recommended, and ideally you should isolate yourself from daylight entirely. The walls are painted with a paint that has a 60% reflectance coefficient. Thanks to that, the eye needs to adapt only to the monitor's brightness (recommended brightness 80–120 cd/m²), which lets you perceive its whole tonal range without a visual loss of display contrast. The lighting should be close to the spectral composition of D50 (≈5000 K), with the workplace illuminance around 32–64 lux, possibly lower — for print work, 32 lux and below only. If your workplace lighting shifts its cast to something "warmer," the screen takes on a bluish cast; if the surrounding light's cast is "cold," the picture on the screen will go yellow.

For viewing printed materials you need good lighting close to D50 (Ra > 90%, the color-rendering index), with an illuminance of 1500–2500 lux. This is fairly bright lighting, and it lets you convey the contrast from the maximum white point on the paper to the maximum black point.

There's an international standard, ISO 3664:2009, for setting up a workplace. On the monitor the white point should be 6500 (D65), which makes it easier to achieve the maximum width of the profile's gamut. There's plenty of criticism online of this approach and of the demand to do D50 for print work, but D50 is far harder to set up, which means it'll break at the most inopportune moment. To improve the quality of color work, calibrate to 80–100 cd/m²; for web designers 100–120 cd/m², and you can sometimes stretch to 160. The monitor's brightness is always stable unless you change it yourself, so the perception of images will be constant. A matte monitor is better than a glossy one. Contrast around 350–370 by the proof, as in Fogra39. Gamma 2.2 or gamma L. For video work the parameters will be different, taking Rec.709 into account.

A few words on screens and sleep, since the night color-shift mode in phones raises questions about gamma and perception. The problem is an old one — an entire generation watched television before bed, falling asleep in the blue glow of the screen, and that happened several meters from the eyes, whereas we bring a phone screen right up to the face. Worse, on phone, tablet, and computer screens we mostly work with text on a bright white background, and that's not the passive viewing of a television but concentration. There have been studies (I can't speak to their statistical significance) concluding that viewing content on a phone screen before sleep in a dark room can worsen sleep quality and the ease of falling asleep. The key problem is a certain part of the blue spectrum (460–500 nm), which is why reducing that range's contribution gives the screen an orange cast. That's the principle behind Night Shift on the iPhone, which cuts the 460–500 range by 60% at peak while raising the brightness of the red range. The "poor man's" option is red or orange tinted glasses, since red light doesn't affect the eye's adaptation to darkness. The general advice: the farther the screen from your face the better; LED lamps emit more blue than the classic kind; reduce display brightness from 400 nits to 100 nits; and read white text on a black background.

A couple of browser-side gotchas round this out. If colors look more vivid in Chrome than in your mockup — even on images — go to `chrome://flags/#force-color-profile` and specify the right profile, and all will be well. Firefox seemingly can't be fixed: its incorrect defaults and poor gradients use the monitor's colors instead of sRGB. Safari, too, makes a mess of gradients.

And one larger point worth making, since fine color work on the web used to feel optional and no longer is: many new monitors have appeared, and modern CSS — "CSS Color 4" — has introduced the LCH color space (lightness, chroma, hue). It gives access to a range about 33% above sRGB, and those percentages are the brightest colors — closer to P3, which the new monitors can reproduce beyond the sRGB gamut. But its main advantage is uniform lightness. RGB and HSL don't support uniform perception of lightness, saturation, and color; only adjusting the lightness parameter gives a predictable result, letting you influence brightness and saturation while working with a single color. LCH has no such problem, nor does CIE L\*a\*b\*.

![LCH uniform lightness](http://your-scorpion.ru/wp-content/uploads/2020/06/Group-61.png)

With RGB the situation is better — sRGB and P3 have very similar blue, though green and red shades can vary strongly. Even so, for now all designers are advised to use HSB as the color model when working on shades of a base color: Hue doesn't change, and the other parameters can be varied.

![HSB for base-color shades](http://your-scorpion.ru/wp-content/uploads/2020/06/Group-62-1.png)

And here's some play with LCH:

![Playing with LCH](http://your-scorpion.ru/wp-content/uploads/2020/06/Frame-12.png)

As you can see, lightness, chroma, and hue are perceived as orthogonal — you can change one parameter without touching the other two. OkLab and JzAzBz work just as well. Technically, I was using P3 in Safari back in 2016 — `color(display-p3 1 0 0.647);` — and there's a fairly simple [P3-support test](https://codepen.io/aolko/full/VwLMoQg). On top of that, ACEScg and BT.2020 have appeared and become popular in some industries; for ACEScg there's a ready [LUT](https://github.com/alexfry/PureNukeACES/tree/master/Luts) — you just drop on a Color Lookup adjustment layer and choose the file (it works, if not perfectly), or the more venerable [OpenColorIO for Photoshop](https://fnordware.blogspot.com/2017/02/opencolorio-for-photoshop.html) if you don't need 32-bit work. ACEScg has been added to V-Ray as well, so these new approaches to color can't be ignored.

As for whether to bother with P3 in a tool like Sketch: in macOS the monitor profile is changed in System Preferences → Display, and that setting makes the computer think you have a different display, so leave the default — "Color LCD" or "iMac," whatever sits at the top of the profile list — and turn off Automatically Adjust Brightness, Night Shift, and f.lux, so you know exactly how people on other computers will see your work. Recent Sketch versions gained color management, with a choice between sRGB, Unmanaged, and Display P3; the default is Unmanaged, which is bad — better to set sRGB, since Display P3 isn't useful for every job and sRGB is more universal. To apply a profile to a document, choose File → Change Color Profile. One rule: if Save For Web is off, a chunk of the ICC profile is added to the exported PNG; recall that in most cases a picture is assumed to be sRGB if there's no ICC profile, so when saving an sRGB PNG you should turn Save For Web off (this shaves the file size a touch, since the sRGB-profile chunk is smaller than the gamma chunk), and when saving a P3 PNG you should also turn Save For Web off, so the gamma chunk isn't included while the ICC chunk — which matters more, since we don't want sRGB — is.
