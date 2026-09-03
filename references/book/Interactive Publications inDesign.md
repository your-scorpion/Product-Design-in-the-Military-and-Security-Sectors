# Creating Interactive Publications in InDesign

*Maksim Tcvetkov · Product Design and Security Lead*


A new era has arrived, and once again the programmers have automated away a great heap of boilerplate work. A whole class of button-pushing designers now finds itself hunting for new ways to earn a living — so let us learn to build interactive, multimedia documents and applications with Adobe's Digital Publishing Suite. Most of the work happens inside InDesign, where you assemble a file in the *folio* format and then distribute it through Adobe's services. And yes: distributing the fruits of your labour means handing Adobe a little money.

As always, let us begin with the fundamentals — the things that keep you from tripping over your own feet later.

## Choosing a format

The principal formats for periodical publishing today are PDF, FB2, ePUB, and folio. Folio has the best support for liquid layout — the mechanism responsible for design across both portrait and landscape orientation — which is an immediate point in its favour and, frankly, your ticket to leaving work an hour early. FB2 suits prose fiction; anything more elaborate calls for ePUB. As for DPS, many designers produce their publications for mobile and target Retina resolution from the outset, then scale down either on the server or by assembling a fresh folio. DPS also offers the best interactivity available right now. That said, the beloved, classic PDF should not be forgotten, and clients remain perfectly comfortable with ePUB. In any case, the moment you need to present different layouts of the same page, put PDF out of your mind — for mobile, DPS is far better suited.

## Protecting your work

Adobe would have us believe that safeguarding your creation is now simple, fast and convenient — no more piracy, no more designers murdered in back alleys for their source files, none of the other charming hallmarks of the 2000s. In practice, though, WinHex can still pry a great deal out of a folio file, and even plain 7-Zip is quite capable of lifting the images straight out of a project.

## A short history of fonts, and why OpenType matters

Every font I use these days is OpenType, and it helps to understand why. Identifying a font's type is straightforward. If the font is a pair of files with the `.pfb` and `.pfm` extensions, it is a PostScript font — Type 1, also seen as Type 3. A single `.ttf` file is TrueType. A `.otf` file is OpenType. Keep in mind that TrueType uses quadratic Béziers while Type 1 uses cubic ones. Quadratic curves require more points to describe the same line, which means converting TrueType to Type 1 changes almost nothing, whereas converting Type 1 to TrueType introduces new points.

It is worth knowing how we arrived here. The first fonts were bitmaps, drawn for 72 dots per inch, and printers shipped with their own protected typefaces — you simply could not specify a different font at print time. Then Steve Jobs sat in on a calligraphy course, came away inspired, and commissioned Adobe to develop the PostScript language. That language naturally needed fonts, so Adobe produced fully vector typefaces in the PostScript format and named them Type 1, with every glyph — every character of the font — described in PostScript itself. Apple and Microsoft, unwilling to let Adobe walk off with the entire print-market pie, scratched their heads, picked up a pen, and created TrueType in response.

There was a second limitation baked in early. When the Americans were building computers, they decided a single byte — values from 0 to 255 — was plenty for one letter, which capped a font at 256 characters. That is plainly far too few. The answer was OpenType together with the Unicode encoding, conceived to unify PostScript and TrueType under one roof. It uses an extended coding scheme that includes characters for non-Latin alphabets; Unicode fonts are two-byte and can hold up to roughly 65,000 glyphs.

Reach for older fonts, however, and you should be ready for trouble: OpenType with PostScript outlines (OT/PS) and OpenType with TrueType outlines (OT/TT) behave differently across operating systems and applications. There are three baseline levels of OpenType support. Basic support uses Western encoding and behaves like an ordinary font. Multilingual support uses Unicode. Full support uses Unicode and unlocks the complete range of glyph positioning and substitution features — and this is the level I recommend for building publications.

A word on tooling while we are on typography: lately I have grown fond of [underware.nl](https://underware.nl). To my eye, their advanced-typography offering is the strongest thing going at the moment.

One small but firm rule before we move on: keep Cyrillic out of the project's own naming — out of your entire electronic life, really. Names stay in Latin script.

## Design and usability

One loose translation of *usability* is *accessibility*. If the reader finds your publication uncomfortable to read, you will bleed money, customer loyalty, and ultimately business. It is therefore essential to make publications not merely interactive but also accessible and intelligible. A quick refresher on the nuances will help you avoid robbing the reader of comfort.

Start with the background colour. Must the page be white? Usually yes — but since this is an electronic publication, a faintly yellow background is sometimes the better choice. Blue, perhaps, or grey. The text colour should not default to pure black either. High contrast is well known to grate, because the eye stops reading the text as text and starts perceiving it as an image, which pulls attention away from the meaning. Use the contrast between background and text with judgement.

The reader also expects the controls for an object to sit as close as possible to the object itself — Lebedev's law of proximity. And those controls must be obvious and self-explanatory. Never pin your hopes on a viewing setting, however prominently you have surfaced it in the interface: readers very rarely change the defaults.

People feel far more confident when they receive clear, continuous feedback, so do not be lazy about drawing the pressed states of your controls. Mental images form in the associative areas of the cortex, assembled by matching one feature against another, and geometry is a necessary feature for classifying form. Objects should be well-proportioned — the correct ratio of length, width and height in a human body reads as comfortable, while disproportion provokes a negative response, and the same principle holds for any image at all. In practical terms: buttons should look like buttons, not like coloured squares. Spare us the gratuitous creativity. Clear, convenient, simple — that is the golden rule.

The same discipline governs the smaller decisions, beginning with links. People register colour first and form second, so colour is where to start. Everyone knows links should be blue, and there is sound reasoning behind the convention: even people with colour blindness can distinguish blue, which makes it a dependable way to identify a link. Use blue, then — or another colour if you prefer, so long as it remains obvious that a link is a link.

Whether to underline links is a more delicate matter. Facebook underlines nothing, Google underlines nothing, and on that basis a good many inexperienced designers conclude that underlining is unnecessary. My own inclination is to strive to remove underlines — but it must be apparent at a glance that the text is a link. An underline set at 25 or 50 per cent alpha threads the needle nicely, signalling the link without adding visual noise. There is a catch worth knowing, though. I have access to session-recording data for sites with audiences in the hundreds of thousands, and the statistics make one thing plain: people try to click underlined text even when it is not a link at all and the cursor never changes on hover. Those examples are large portals where nearly everything is clickable, so the lesson does not transfer blindly — but it does suggest that within a solid block of body text, links may well need underlining after all. The point is to manage the reader's attention deliberately rather than defer to a pile of design articles.

The cursor deserves the same care. Apple's guidelines change the pointer to a finger on hover, and by specification that finger is meant to tell the reader the cursor is over a link — a link specifically, since for plain buttons the cursor does not change.

Selected and unselected states call for the right control in the right place. An iPhone-style switch is the tool when you need to show two states; just keep text and icons out of the switch itself and place them to its left or right. Checkboxes are chiefly for multiple selection, though they also serve single affirmations such as "I consent to the processing and storage of my personal data," and by default none should be ticked. Radio buttons are for choosing exactly one item from a list, with one always selected by default — for gender, use three options (unspecified / male / female), or keep two (male / female) if the radio buttons are rendered as a toggle rather than as circles. And to show on a single button whether it is pressed or released, change more than the icon: give it a visual shift as well, pressing the button inward.

## Into production

First, install Folio Producer — search for the DPS Desktop Tools for InDesign.

A caveat that catches many licence holders: after installing the full, licensed Digital Publishing Suite, Windows can seize up entirely — Explorer hangs, and the Adobe applications refuse to launch. The culprit is Creative Cloud, and the fix, while something of a crutch, is simple. Kill every Adobe process, navigate to `…\Users\[username]\AppData\Local\Adobe\OOBE`, and delete or rename `opm.db`. The TMG Client also resolves a good many Creative Cloud problems on its own.

With that out of the way, create your source documents in InDesign. You should hit no trouble here — since roughly 2008, schoolchildren have known InDesign about as well as top designers know Photoshop. The essentials: InDesign must understand that you are working on an interactive document, and there should be no bleed margins. Pick from the presets the platform you are targeting (iPhone, iPad, Kindle Fire, Nook, 10″ Android tablet) and start building something handsome. When you are finished, open the Folio Overlays panel.

## Folio Overlays

The Folio Overlays panel presents a set of buttons. One especially useful function is the Scrollable Frame, which lets you build blocks of scrolling or page-turning content and so save space. You can show or hide the scroll indicators and restrict scrolling to the vertical or horizontal axis alone. Audio, video and panoramas should give you no grief — the functionality there is primitive and intuitive. Pan & Zoom handles the scaling of elements, and it carries three golden rules: no transparency on raster, images no larger than 2000 px, and an image that zooms only up to its 100 per cent size — there is no scaling to 200 per cent.

## The Web Viewer

As established earlier, a plain interactive document differs from DPS chiefly in one respect: DPS is built for mobile devices. Since modern mobile devices are bound so tightly to the internet, the ability to reach the web had to be there too, and that is the Web Viewer. It pulls in online content — news from a social feed in a column of your publication, say, with no fuss at all. A nice touch: you can style what it displays with your own CSS.

## Hyperlinks and Open in Folio

Hyperlink, as the name suggests, lets the reader follow links. You might think InDesign's ordinary functionality already covers this, but the Open in Folio option adds a little variety: it opens the link directly inside your publication's own interface. Which to choose? Lean on the idea that complex sites are better opened in a browser, since jQuery can misbehave inside the publication environment.

## Image Sequence

Image Sequence is among the less intuitive functions. A sequence, as you know, is a run of images — so what use is it inside a publication? Faking video, of course. You have surely seen Apple's beautiful animated promo pages, where the animation is tied to the scroll of the page; you can achieve the same effect with Image Sequence.

## Linking within the folio

To link to a page inside the folio, give the button the *go to URL* action and set the address to:

```
navto://[articlename]#n
```

where `articlename` is the name as it appears in Folio Builder and `#n` is the page number. You can also point a link at an email address (`mailto:`), a text message (`SMS`), or a phone call (`tel:`), and you can offer video playback in iTunes. When you create a link to an external application, choose the **Open in Device Browser** option in the Overlays palette.

## Importing HTML

By now you have likely counted two ways to use HTML: linking to external resources, and displaying web content directly in the publication. But the most rewarding option is importing HTML straight in. Take your HTML page and archive it into a `.zip` together with everything it needs. Name the folder holding that archive `HTMLResources` — there is no need to archive the folder itself. You can prepare two versions for the two screen orientations simply by appending `_v` and `_h` to the file name, as in `article_h.html`.

Once it is zipped, open Folio Builder, select your folio, click **Import HTML Resources**, and choose `HTMLResources.zip`.

While we are zipping things, a note on handing large projects between departments: my preference used to be PeaZIP back in my print-production days. It packs to 7-Zip, computes checksums, and drops the reports alongside the resulting archive as `.txt` or `.svg` files — convenient when someone downstream needs to verify what they received.

## Getting your hands on the folio file

To recover a folio file, look in the user profile directory for `*.folio` files. On my machine they live at:

```
[Logical disk]:\Users\[UserName]\AppData\Roaming\StageManager.BD0…4.1\Local Store\dmp
```

If the folio is in the cloud, it takes a little more effort: in Folio Builder, click the folio you want, then choose **View → on the Desktop**. The folio downloads from the cloud, and you return to the first step — head to the `dmp` folder and look for the `*.folio` files.

One important caveat: a folio file will not open on Windows unless InDesign is installed. On an iPad it opens happily, and on Android too — but on Windows without InDesign, you are out of luck.

## Alternatives, and who this is really for

There are alternatives to DPS — from Woodwing and Quark, plus Calibre and the Telerik Platform. The thing to keep firmly in mind is that electronic magazines are made not for computers but for tablets and smartphones. Readers consume them on those devices through a dedicated engine application, unlike the glossy coffee-table magazines, which are image pieces carrying no fresh news.

## Bringing existing material into InDesign

A finished PDF can be brought in too. Through the **Place** command you can specify exactly which pages to import. Video, sound and other interactivity will not come across, but vector stays vector and raster images lose no quality. Colours carry over from the original, even Pantones: InDesign renders placed PDF pages using their embedded ICC colour profiles, and when you print to PDF/X you can swap the embedded profile for the output device's own.

Sometimes a placed PDF brings a stubborn spot colour with it — one that simply refuses to delete, especially across a file full of linked documents. First ask what happens when you replace it with another colour. If that leads nowhere, create a file in Illustrator containing the spot colour and an empty object, rename the spot colour so it matches the InDesign one exactly, and link that file into your InDesign source. Then, back in Illustrator, delete the spot colour, save, and update the link in InDesign; that usually does it. Re-saving as IDML is the old, reliable fallback. And if nothing else works, assign the colour as *process* rather than *spot* so it no longer affects your separations.

Adding bleed to a multipage PDF in a hurry has a couple of tidy solutions. In the Scripts panel, under JavaScript or VBScript, run `PlaceMultipagePDF.jsx` (or the `.vbs` variant) and point it at your PDF. Alternatively, Acrobat has a built-in route at **Document → Crop → Change Page Size**.

Threaded text is the easy part of multipage work — type flows from frame to frame on its own. Images laid out on a grid are the harder case. Build the whole thing as a chain of linked frames with anchored objects for the photos, or lay the grid out as a table; if you take the table route, work through a Book file so InDesign does not buckle under the load.

Splitting one text frame into many separate frames while preserving the frame sizes you originally drew is a one-line job: open **Window → Utilities → Scripts**, then run **Application → Samples → SplitStory.jsx**.

Composing for right-to-left and other complex scripts deserves its own note. Get the text prepared first, then lay it out as usual, left to right. Run the ScribeDoor script to flip the layout, or flip it with InDesign's native tools. Make sure the World-Ready Composer is switched on in the paragraph settings. Text flows top to bottom as it normally would, and the print shop will handle imposition.

## Acrobat Pro and JavaScript

Acrobat Reader Pro can build interactive documents in its own right, and good results come from JavaScript — which lets you perform calculations, validate data, drive interactive behaviour, improve the prototyping of various interface ideas, trigger printing the instant a file opens (handy on the web), and, less benignly, load malicious code. Treat that last capability as a reminder to be careful about what you open.

Getting started takes a moment of setup. In Acrobat's preferences, under the JavaScript tab, enable **Enable Acrobat JavaScript** and restart. In Acrobat 9 the document scripts live under **Advanced → Document Processing → Document JavaScripts**; in Acrobat 10, under **Tools → JavaScript → Document JavaScript**.

Let us try a small global function. Open the page properties, switch to the Actions tab, and add the following on page open:

```javascript
if (app.viewerVersion < 7) {
    // Acrobat/Reader version 6 and earlier are unacceptable
    app.alert("We recommend viewing this file in Acrobat Reader 7 or newer.");
}
```

That is all. Opened in a version earlier than 7 — that is, 6 or older — the document now shows a warning. Add the line `this.closeDoc(true);` and the file will close itself automatically as well.

We can also attach a preview thumbnail to the PDF. Go to **Tools → Content** and add a **Button** to the page. With the new button selected, choose **All Properties**, switch to the **Actions** tab, and add:

```javascript
event.target.buttonImportIcon();
```

Then move to the **Icon** tab and configure how the imported image appears in the document. It works simply enough.

## An aside on prepress automation

In one small print shop I once used this same sleight of hand to automate the prepress for business cards, brochures and other templated pieces, and it paid off handsomely. Consider the arithmetic: a prepress specialist in Moscow earns about 50,000 roubles, plus roughly 27,000 in taxes and around 12,000 in non-refundable VAT, with the software stack — Adobe Acrobat Pro, Enfocus PitStop Pro, Illustrator, Photoshop, InDesign, QuarkXPress, CorelDRAW, Kodak Preps Pro — running close to ten thousand dollars. The total is far from trivial. Automated, the whole job comes down to pressing two to five preconfigured buttons and pestering the author of the automation once or twice a month. Very convenient indeed — though, as with any such scheme, not without a certain fragility.

## When Windows and Adobe misbehave

A few gremlins are worth keeping in your back pocket, because they tend to surface at the worst moments.

InDesign sometimes crashes while its tray icon stubbornly remains, occasionally even duplicating itself — a Windows quirk more than anything. The behaviour can in fact be reproduced on purpose with any application: open Task Manager, find the process (Skype, say) and note its PID — 1234, for example — then open a console and run `taskkill /pid 1234 /f`. The application ends, but the tray icon lingers until you move the cursor over it, at which point it vanishes.

If you type a command into the console and the window flashes up for an instant and disappears before you can read or copy anything, write your commands in Notepad instead and save the file with a `.cmd` extension. You can then run it from the command line just by entering the file name, and adding `PAUSE` at the end keeps the window from closing the moment it finishes.

And if audio or video keeps freezing while you work — for instance during a call running alongside InDesign, with the freeze accompanied by a perceptible stutter in the application — start with the network. Check your ping to Google's servers with `ping 8.8.8.8 /t`; if the loss is heavy, take it up with your ISP. Then update your drivers: get the exact name of your motherboard with

```
wmic baseboard get product,Manufacturer,version,serialnumber
```

and download everything you need from the manufacturer's official site. If that still does not settle it, a specialised forum or chat is your next stop.
