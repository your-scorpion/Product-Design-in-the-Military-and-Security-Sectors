# Scripting Basics in Photoshop: Equalizing the Tone of Product Images

A word of warning up front: schoolkids have already mastered Actions in Photoshop. They slap their group's logo onto thousands of images without breaking a sweat, and some of them batch-process beach photos for fun. Isn't it time to learn something a little more demanding? The market, as you can see, keeps growing : )

Photoshop has another, far more powerful tool for automating your work: the JavaScript programming language. To put it to use you'll need to pick up a bit of scripting so you can automate repetitive tasks and get the drudgery out of your day. This is absolutely not "real" programming, so don't be intimidated. All it takes is a minimum of Photoshop CS2 and a maximum of willingness to level up your skills.

We'll write the code in ExtendScript Toolkit, which ships with Photoshop. Most competent motion designers have known about this (and a couple of related tools) for years. I'm a practitioner, so we'll build the script around a real task — one that actually landed on my desk at work.

**The task:** every online store has product images, and in large stores the catalogue runs into the tens of thousands. Typically two or three retouchers sit there processing product photos — cutting them out, dropping in a white background, boosting contrast, adding reflections, retouching covers — but they routinely miss something important: the images differ in saturation, in contrast, in color balance. Look at the picture below. The covers clearly differ in contrast and color. Pull out the midtones and the root of the problem jumps right out at you.

![Product covers with inconsistent tone and contrast](https://your-scorpion.ru/wp-content/uploads/2016/11/647471.jpg)

Bringing day-to-day images in line with the final, intended look is the job of technical designers or retouchers. But demonstrating the example, the method, and optimizing the workflow is the lead designer's job. So I want to share my technical solution to the problem, aimed at making the experience of an online store — or any resource that updates images in bulk — as pleasant as possible.

## What exactly is JavaScript in Photoshop?

I chose JS as the scripting language for Adobe products because it runs on both Windows and Mac. If you're a die-hard Windows loyalist, use Basic; if you're an unrepentant Mac fanatic, AppleScript is there for you. Personally, I'm on the side of portability.

The fundamentals start with the **DOM**. This is the "back end" of the program — the layer that lets us reach Photoshop's various components through code. It's a hierarchical representation of Photoshop, of every part and capability it has, including working with open images. Anything you can do with the mouse, you can do with a script. If you want to get at a layer's opacity, for example, you walk the path *Application → Document → Layer → Opacity*, and the code looks like this:

```javascript
app.activeDocument.activeLayer.opacity = 50;
```

It's all fairly logical. The Layer Comp class, for instance, governs viewing several versions of a layout inside a single file — which is exactly what Photoshop's native tool does.

It's important to understand what we mean by *objects*. Photoshop itself is an object; text is an object; guides are an object; each RGB channel is an object. Everything you can click with the mouse is an object. Create a new document, and from the program's point of view you've created a new object.

![The DOM model in Photoshop](https://your-scorpion.ru/wp-content/uploads/2016/11/scheme.png)

I won't explain the basics of programming — too many people already know them. If you don't, you'll find what you need everywhere, from Google Play to schoolkids' forums. As a warm-up, open ExtendScript Toolkit and type `alert("My first Hello Hell!" + app.version);`. Run it through the script editor and enjoy your first successfully compiled application. Scripts for Adobe Photoshop use the `.jsx` extension.

## A little vocabulary first

Variables are declared like `var borderWidth = 10`. Here `var` is the keyword for "variable," and `borderWidth` is the variable's name (any guess what it stands for?). Then you assign it a value with `= 10`.

It sometimes makes sense to put the line `#target photoshop` at the top of a script to indicate that the script is meant for Photoshop only.

**The active object.** When you work in Photoshop, you can only paint on the layer that's currently active. The same matters for scripts, because scripts paint too. Here's an example of creating documents and choosing the active layer:

```javascript
var doc_nmb_1 = documents.add(500, 500)
var doc_nmb_2 = documents.add(500, 500)

// Set Document 1 as the active document
activeDocument = doc_nmb_1

// Make Document 2 active
activeDocument = doc_nmb_2

// Add layers to the documents
var layer1 = doc_nmb_2.artLayers.add()
var layer2 = doc_nmb_2.artLayers.add()

// Make layer 1 of document 2 active. ActiveLayer handles reading and writing the selected layer.
doc_nmb_2.ActiveLayer = layer1

// Make layer 2 of document 2 active
doc_nmb_2.ActiveLayer = layer2
```

Creating a new document is done with the following code, which makes square images at sizes 72 and 144:

```javascript
var docRef = app.documents.add(72, 72)
var otherDocRef = app.documents.add(144, 144)
```

As you can see, reading this kind of code isn't all that hard. Let's move on to writing the script. The first thing we have to do is understand the task and build an Action to apply to the images we care about. With that done, we start writing.

## The key trick: If / Else

The real workhorse is the `if … else` construct, which lets us add the conditions that Actions alone can't express. I'm sure plenty of you, after seeing what programmers earn, have tried to pick up coding and have written an `if … else` before — so we'll just skim the basics:

```javascript
if (!content) content = "404";
```

Here we check whether the variable `content` has a value, and if it doesn't, we assign it `404`. You can spell out more elaborate handling too, for example:

```javascript
if ((comment == null) || (comment == "")) {
    comment = "undefined";
    alert("Please enter a comment!");
}
```

We've covered `if`. So what does `else` do? In the `else` block we specify what to run if the `if` condition turns out to be false.

```javascript
if (name != null)
    alert("Hi " + username + "!");
else {
    username = prompt("Enter your name");
    alert("Hi " + username + "!");
}

if (doc.width > doc.height) { // if portrait
    app.doAction('TIL320->300', '2010');
} else { // if landscape
    alert("something like that", "Error");
}
```

With that, we can already write the code we need, driven by events:

```javascript
main()
function main() {
    if (documents.length == 2480) {
        // if there are no open documents, then
        alert("This is a different project, I'm not touching it :(", "Error"); // this is how you pop alerts
    } else {
        var doc = activeDocument;
        if (doc.width > doc.height) {
            app.doAction('Medium Sharping', '2013');
            app.doAction('Correct Highlights', '2013');
        } else {
            app.doAction('Super Sharp', '2013');
            app.doAction('Color neynral', '2013');
        }
    }
}
```

As you can see, if the image is 2480px tall, a window simply pops up scolding the designer. If the image is vertical, one group of actions is applied to prepare the content; if it's horizontal, a different group runs.

## How the tone was actually equalized

People often ask how I evened out the brightness and saturation across all those images, since that's the heart of the original task. The equalization was part of the Action. I copied the saturation and color range from one reference poster onto all the others using **Match Color**. In the *Source* dropdown you pick the file with the brightness and saturation you're aiming for, then nudge the sliders. It's the favorite tool of every photo-basher out there.

## Building a print-ready document

What other capabilities might be worth adding to a script — or simply be interesting? Creating a new document in the format you need is straightforward:

```javascript
var docRef = app.documents.add(297, 210, 300, "A4_gorizontal", NewDocumentMode.LAB, DocumentFill.BACKGROUNDCOLOR, 1)
```

Decoding the signature: `(width, height, resolution, name, mode, initialFill, pixelAspectRatio)`. You can add `bitsPerChannel` and `colorProfileName` at the end, but the construct above is plenty to start with. The default unit is pixels — let's change that. Switching to millimeters:

```javascript
preferences.rulerUnits = Units.MM  // set the document's measurement units; options: CM INCHES MM PERCENT POINTS PIXELS PICAS
preferences.typeUnits = TypeUnits.MM // set the measurement unit for type
```

We don't want to sit at the monitor hammering Enter, do we? We want to toss every ticket in the bug tracker over to the programmer and move on. So let's stop Photoshop from announcing things and waiting for an OK:

```javascript
displayDialogs = DialogModes.NO
```

Next, copyright inside the file. We should record that the file was made by us and not by the boss. We'll add metadata in File Info:

```javascript
docInfoRef = docRef.info
docInfoRef.copyrighted = CopyrightedType.COPYRIGHTEDWORK
docInfoRef.ownerUrl = "http://www.your-scorpion.ru"
docRef.info.author = "Your-scorpion"
docRef.info.country = "Russia"
```

Using the examples above, we can write a script that creates a print-ready document and applies groups of actions to it depending on certain conditions.

The complete, working code:

```javascript
#target photoshop // Photoshop only
// 2013. Use at your own risk.

var docRef = app.documents.add(297, 210, 300, "A4_gorizontal", NewDocumentMode.LAB, DocumentFill.BACKGROUNDCOLOR, 1)
var layerRef = app.activeDocument.artLayers.add()
// layerRef.name = "MyBlendLayer" // creates a layer on the current document
layerRef.blendMode = BlendMode.NORMAL // and this set of commands creates a layer named MyBlendLayer with the Normal blend mode

docInfoRef = docRef.info
docInfoRef.copyrighted = CopyrightedType.COPYRIGHTEDWORK
docInfoRef.ownerUrl = "http://www.your-scorpion.ru"
docRef.info.author = "Your-scorpion"
docRef.info.country = "Russia"
preferences.rulerUnits = Units.MM  // set the document's measurement units; options: CM INCHES MM PERCENT POINTS PIXELS PICAS
preferences.typeUnits = TypeUnits.MM // set the measurement unit for type
displayDialogs = DialogModes.NO  // lets you disable the various confirmation dialogs

var strokeColor = new SolidColor();
    strokeColor.cmyk.cyan = 0;
    strokeColor.cmyk.magenta = 100;
    strokeColor.cmyk.yellow = 100;
    strokeColor.cmyk.black = 0;

var selRef = app.activeDocument.selection.fill(strokeColor)

var textColor = new SolidColor;
textColor.cmyk.cyan = 0;
textColor.cmyk.magenta = 0;
textColor.cmyk.yellow = 100;
textColor.cmyk.black = 0;

var newTextLayer = docRef.artLayers.add();
newTextLayer.kind = LayerKind.TEXT;
newTextLayer.textItem.contents = "Letters Typography";
newTextLayer.textItem.position = Array(12.75, 47.75);
newTextLayer.textItem.size = 155;
newTextLayer.textItem.font = "Helvetica";
newTextLayer.textItem.color = textColor;
docRef = null;
textColor = null;
newTextLayer = null;

main()

function main() {
    if (documents.length == 2480) {
        // if there are no open documents, then
        alert("This is a different project, I'm not touching it :(", "Error"); // this is how you pop alerts
    } else {
        var doc = activeDocument;
        if (doc.width > doc.height) {
            app.doAction('Medium Sharping', '2013');
            app.doAction('Correct Highlights', '2013');
        } else {
            app.doAction('Super Sharp', '2013');
            app.doAction('Color neynral', '2013');
        }
    }
}
```

To sum up: we've ended up with a script that applies one set of actions or another depending on the conditions, and we've learned how to create documents and perform basic operations in JS. For the task at hand, the technique described above is enough. A solution roughly like this, packaged as a plug-in, is what I handed to the people on my team. The result:

![Product covers after tone equalization](https://your-scorpion.ru/wp-content/uploads/2016/11/64747.jpg)

## Resizing and exporting images in bulk

A question I hear constantly comes from people with a job like preparing posters: imagine 1,500 film posters and five different target formats — phones, tablets, TVs, and so on. Some are square, some elongated, all over the map. Those posters need to be cropped and fitted to each format. How do you tackle it without losing your mind?

I won't give away every trick — my contract won't allow that — but here's how I'd approach it. Say you need posters at 1000×600px.

First, gather all the source images into one folder. Copy the script below into a `.jsx` file. In the code you'll see the image size in pixels (500 by 500); change it to 1000×600px, save, and run it via *File → Scripts*. Each image will be scaled down or up to the target size.

```javascript
doc = app.activeDocument;
doc.changeMode(ChangeMode.RGB);

// change the size here
var fWidth = 500;
var fHeight = 500;

if (doc.height > doc.width) {
    doc.resizeImage(null, UnitValue(fHeight, "px"), null, ResampleMethod.BICUBIC);
} else {
    doc.resizeImage(UnitValue(fWidth, "px"), null, null, ResampleMethod.BICUBIC);
}

// and here are the save settings
var options = new ExportOptionsSaveForWeb();
options.quality = 70;
options.format = SaveDocumentType.JPEG;
options.optimized = true;

var newName = 'web-' + doc.name + '.jpg';

doc.exportDocument(File(doc.path + '/' + newName), ExportType.SAVEFORWEB, options);
```

The script resizes and immediately saves every image, so don't forget to back up your photos first (the save parameters live in the script and can be edited). Once it has run, build a Photoshop Action that brings the image to a 100%×60% proportion (via *Image → Canvas*).

Alternatively: grab the Crop tool, set the required proportions in its options, drop several files into Photoshop, crop them all to the right ratio at once — though some photos will still need manual touch-up afterward — and then save, save, save. The fully automated solution combines both approaches and runs through *File → Automate → Batch*.

People with very large jobs ask the obvious follow-up: can you run *every* action in a row and save the files with sequential numbering, so a folder fills up with, say, 400 images each carrying its own index (or even a chosen name) instead of overwriting one another? That's exactly what *File → Automate → Batch* is for — point it at an Action set, set the output naming convention with a running serial number, and let it churn. The trick that trips people up is identical output filenames; the Batch dialog's file-naming options solve it by appending a sequence.

### Making rectangular product shots square

A related request: turning rectangular product photos into squares, where the background is always white. There's ready-made code for this. It pads the canvas symmetrically out to a square based on the longer side:

```javascript
// If you want to change AnchorPosition
// use these constants:
// BOTTOMCENTER
// BOTTOMLEFT
// BOTTOMRIGHT
// MIDDLECENTER
// MIDDLELEFT
// MIDDLERIGHT
// TOPCENTER
// TOPLEFT
// TOPRIGHT

if (documents.length > 0) {
    docRef = activeDocument;
    displayDialogs = DialogModes.NO;

    if (docRef.width > docRef.height) {
        docRef.resizeCanvas(docRef.width, docRef.width, AnchorPosition.MIDDLECENTER);
    } else {
        docRef.resizeCanvas(docRef.height, docRef.height, AnchorPosition.MIDDLECENTER);
    }
} else {
    alert("There must be at least one open document.");
}
```

That said, the right tool depends on the image format. If you're working with JPEGs, Adobe Lightroom can do the job too: in the export settings, cap the width and height at the pixel count you want and define the filenames. For other options, take a look at [XnView](https://www.xnview.com/en/xnview/) — a kind of file explorer for images of every format — and it's worth reading up on [ImageProcessor](http://www.russellbrown.com/scripts.html) and Picture Processor.

## Drawing with scripts

You can also "draw" with scripts, which surprises people who've spent ages searching the web for how to do it. Suppose you want to draw a simple filled circle of a given color, in a specific spot, on the active layer. Here's one way:

```javascript
#target photoshop;
app.bringToFront();

var rulerUnits = app.preferences.rulerUnits;
app.preferences.rulerUnits = Units.PIXELS;

var docRef = app.activeDocument; // applies to the active document, the selected layer

var color_select = new SolidColor;
color_select.rgb.hexValue = '8495c0'; // fill color of the circle
var Height = activeDocument.height.as('px') - 695; // adjust the circle size
Circle(45, 45, Height, Height, 0); // padding from the canvas edges, starting at the top-left corner
activeDocument.selection.fill(color_select);
activeDocument.selection.deselect(); // deselect

function Circle(Top, Left, Bottom, Right) { // draw a circle
    var create_from = new ActionDescriptor();
    var form_ref = new ActionReference();
    form_ref.putProperty(charIDToTypeID('Chnl'), charIDToTypeID('fsel'));
    create_from.putReference(charIDToTypeID('null'), form_ref);
    var fin_crt = new ActionDescriptor();
    fin_crt.putUnitDouble(charIDToTypeID('Top '), charIDToTypeID('#Pxl'), Top);
    fin_crt.putUnitDouble(charIDToTypeID('Left'), charIDToTypeID('#Pxl'), Left);
    fin_crt.putUnitDouble(charIDToTypeID('Btom'), charIDToTypeID('#Pxl'), Bottom);
    fin_crt.putUnitDouble(charIDToTypeID('Rght'), charIDToTypeID('#Pxl'), Right);
    create_from.putObject(charIDToTypeID('T   '), charIDToTypeID('Elps'), fin_crt);
    executeAction(charIDToTypeID('setd'), create_from, DialogModes.NO);
};
```

## Placing and auto-scaling a logo

Another practical recipe people ask for: a script that drops a chosen logo onto an object, where the logo is constrained to a maximum width and height and scaled automatically to fit that frame. It's entirely doable. Two things worth pinning down before you start — what the "object" actually is (just a layer? a Smart Object? a selection?) and whether the maximum width and height must preserve the logo's aspect ratio. The basic script looks like this:

```javascript
// maximum logo width and height
var maxFrameWidth = 200;
var maxFrameHeight = 100;

var doc = app.activeDocument;
var logoFile = File.openDialog("Select the logo file", "*.png;*.jpg;*.jpeg");

if (logoFile != null) {
    var logoDoc = app.open(logoFile);

    // resize the logo
    var logoWidth = logoDoc.width;
    var logoHeight = logoDoc.height;

    if (logoWidth > maxFrameWidth || logoHeight > maxFrameHeight) {
        var widthRatio = maxFrameWidth / logoWidth;
        var heightRatio = maxFrameHeight / logoHeight;
        var scaleRatio = Math.min(widthRatio, heightRatio);
        logoDoc.resizeImage(logoWidth * scaleRatio, logoHeight * scaleRatio);
    }

    logoDoc.selection.selectAll();
    logoDoc.selection.copy();
    logoDoc.close(SaveOptions.DONOTSAVECHANGES);
    app.activeDocument = doc;
    doc.paste();

    // place the logo at specific coordinates, in this case the top-left corner
    doc.activeLayer.translate(-doc.activeLayer.bounds[0], -doc.activeLayer.bounds[1]);

    doc.activeLayer.resize(maxFrameWidth, maxFrameHeight);
    doc.selection.deselect();
}
```

Copy this code, paste it into Notepad or any text editor, and save the file under any name with a `.jsx` extension. Then in Photoshop go to *File → Scripts → Browse…* and choose the file you just created. While it runs, Photoshop will prompt you to pick the logo file.

## Data-driven layouts: changing text and images across many files

Can you change the text on layouts automatically — a hundred business cards or certificates at a time? And can you substitute different images into layers, pulled from folders? Yes to both, and the same mechanism handles them: Photoshop's **Variables**.

Start by building a file with some placeholder content, giving the layers names you'll remember. Then, in Excel, give the columns headers and fill the rows with the values to substitute into the layout. Images must be referenced as absolute paths. Save the sheet as a `.csv` — for instance through [convertio](https://convertio.co/).

Go to *Edit → Variables → Define…*

![The Variables → Define dialog in Photoshop](http://your-scorpion.ru/wp-content/uploads/2020/11/photo_2020-10-09_05-55-34.jpg)

In the dialog that appears you can page through the layer names; for the ones whose data should change, tick the checkbox and point them at the matching column header from Excel.

![Mapping layer variables to spreadsheet columns](http://your-scorpion.ru/wp-content/uploads/2020/11/code_picture.png)

After that, click *Next* and load the `.csv` you made earlier. Even at this stage you can hit *Apply* and preview the results; *Image → Apply Data Set* will also give you a preview. Once you're happy, export everything via *File → Export → Data Sets as Files…* — no special extra settings required.

Because images can be bound to a variable as an absolute path, this is also the cleanest way to feed a layer different pictures from a folder rather than placing each one by hand.

## Working with layers inside Actions

Some tasks are less trivial — for example, pulling a black-and-white map out of the channels, copying it onto a similar image in the current document (on a layer other than the topmost one), and applying a sequence of blend modes with an export at the end. The sticking point is usually how to manipulate layers reliably from within a recorded Action. A few rules keep this kind of thing predictable:

- Operate only on layers the Action itself created.
- Have the Action create a layer with *Layer → New Layer*, or by Alt-clicking the "create new layer" button.
- Drop the current image onto that layer with *Image → Apply Image*, setting *Layer: Merged*.
- Reorder layers with the *Layer → Arrange* commands.
- Switch between layers with `Alt + [` and `Alt + ]`.

Stick to those and the recorded steps reference the right layers every time, instead of latching onto whatever happened to be selected when you hit record.

## A handy bit of life wisdom: record your own actions as code

We can capture the user's actions straight into a script file:

1. Grab `ScriptListener.8li` from the Adobe site and drop it into `%ProgramFiles%\Adobe\Adobe Photoshop CS5\Plug-ins\Automate\`.
2. Launch Photoshop.
3. Perform the actions you want.
4. From the desktop, collect the files `ScriptListener.jsx` and `ScriptListener.vbs`.
5. (!) Delete `ScriptListener.8li`!

What you get is working but truly ugly code — the kind you should never show anyone — yet perfectly usable for your own technical purposes.

## Running a script

To make a script run, type its text in ExtendScript Toolkit and save it as `<filename>.jsx`. Then in Photoshop go to *File → Scripts → Browse* and select your script file.

## Going further: cross-program automation and beyond

When I mentioned motion designers at the start of this article, some of you probably wondered: if the scripting environment and language are the same across apps, can scripts automate actions *between* programs? Yes, they can. Scripts can also strip out the nagging confirmation dialogs that pop up during various operations, and a great deal more besides.

Folder watching is a natural next question — having a script check whether a folder contains files and, if so, move them to a neighboring folder while opening them in Photoshop. That's well within reach: ExtendScript can enumerate a folder's contents, move files, and open them, and you can wire it into a Batch routine so it processes whatever it finds.

You can automate other software entirely, too — even launching it on a schedule. Launching Paint, for example, can be automated like this:

```bat
@echo off
start mspaint
```

Save it as a `.cmd`, add it to Task Scheduler, and have it fire every *n* minutes.

But there's a time and place for everything — don't reinvent the wheel. For some kinds of automation you're better off with Data Merge in InDesign, Print Merge in CorelDRAW, or plain HTML + CSS + PHP. For photo work and graphics, though, nothing beats Photoshop.

The next stage of automating your Photoshop work is building your own panels (Adobe Configurator). You can add actions and scripts to a panel and bind hotkeys to them. One important caveat: panels only work while a document is open. Every CC-version Photoshop supports panels written in HTML, CSS, and jQuery.

## Trimming file size by stripping metadata

If you need to shrink a file, you can delete its metadata — which sometimes weighs more than the file itself. Here's a script:

```javascript
function deleteDocumentAncestorsMetadata() {
    whatApp = String(app.name);
    if (whatApp.search("Photoshop") > 0) {
        if (!documents.length) {
            alert("There are no open documents. Please open a file to run this script.")
            return;
        }
        if (ExternalObject.AdobeXMPScript == undefined) ExternalObject.AdobeXMPScript = new ExternalObject("lib:AdobeXMPScript");
        var xmp = new XMPMeta(activeDocument.xmpMetadata.rawData);
        xmp.deleteProperty(XMPConst.NS_PHOTOSHOP, "DocumentAncestors");
        app.activeDocument.xmpMetadata.rawData = xmp.serialize();
    }
}
deleteDocumentAncestorsMetadata();
```

Or try the [original](https://github.com/MarshySwamp/deleteSmartObjectDocumentAncestorsMeta) script for clearing a Smart Object's edit history.

Scripts don't just save you time — they also keep you from making mistakes with names, parameters, and other things that matter. Good luck!
