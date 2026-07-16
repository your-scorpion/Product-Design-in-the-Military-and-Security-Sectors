# Variable Data in Adobe Illustrator
### Turning repetitive layout work into a batch job, from template to export

*Maksim Tcvetkov · 14 May 2015*

A lot of what designers do is repetitive: adapting a flyer built by a star designer to fit a company's branch contact details, dropping a pile of photos into a business-card template, exporting print-ready graphics for a day's worth of reporting. All of it eats time and energy without offering much satisfaction in return — most designers would rather create than sit around cutting and pasting content. Fortunately, Illustrator lets you dynamically swap objects in a layout and save yourself a great deal of time, the same way [InDesign](https://your-scorpion.ru/principle-data-merge-indesign/) does. Let's walk through this not-especially-popular feature.

First, you need to build a design template made up of linked images, text, graphs, or other objects — and every one of these objects can also be shown or hidden dynamically once you're working with variable data in Illustrator.

![Dynamic data in Illustrator](https://your-scorpion.ru/wp-content/uploads/2015/05/iz1.jpg)

*Dynamic data in Illustrator*

Now let's assign variables. Select the object you want to make dynamic and open the Variables panel. Click Make Object Dynamic to create a Linked File variable, or Make Visibility Dynamic in the Variables palette. As you'd expect, if you're using a Text String variable, it's the text inside that text object that ends up changing.

Variables define the traits of dynamic objects and come in a few types:

- **Graph Data** — lets you build charts.
- **Linked File** — for building dynamic images tied to image files or other linked documents, always brought in via File > Place.
- **Text String** — for dynamic text.
- **Visibility** — the visibility of an object or a group of objects; in the XML file, this toggles via true/false.
- **Unspecified type** — you'll assign the type later; the object will end up with a type regardless.

Once you've created your variables, it's worth renaming them — "Variable 1" is a terrible name, while `name_person` is far clearer. You can assign two variables to a single object, as long as one of them is Visibility.

Click Capture Data Set in the Variables palette to lock in the values of the variables currently displayed on the canvas, then choose Save Variable Library from the Variables palette menu. What you get is an XML template for the client to fill in.

![Building dynamic text in Illustrator](https://your-scorpion.ru/wp-content/uploads/2016/05/664646.png)

*Building dynamic text in Illustrator*

Now imagine we're on the client's side, and filling in all of this ourselves is our job. You could take it to a developer, show them the XML you've built, and walk them through it — they'll bind the variables and data sets to whatever database exists and export everything in a suitable format. Back in the Variables panel, you'd choose Load Variable Library and load the edited variable library. Unfortunately, that route rarely actually happens. In most cases, you'll be filling in the data yourself, by hand, in whatever format's available.

I'd bet you'll be handed a plain Excel or CSV file instead, with the excuse that the managers are far too busy to solve a designer's problems for them. Plenty of sharp minds will immediately think to fall back on the old trick of XML exported from FileMaker Pro — that approach works in InDesign, but not in Illustrator. You'll have to do the manual work in Excel yourself. In Excel, it's worth noting that the first row's headers are exactly the variable names Illustrator expects. Save the file as ODS, CSV, or XLS, and from there you need to convert it into XML, since Illustrator can't work with anything else. Head to an [online converter](https://joaofaraco.com.br/converter/) (there are [plenty](http://shancarter.github.io/mr-data-converter/) of these), and copy-paste your way to an XML file.

You can also get data into Illustrator using this handy [script](https://github.com/Silly-V/Adobe-Illustrator/blob/master/Variable%20Importer/VariableImporter.jsx). To me, the script is the cleanest way to move data over — it converts CSV/TXT straight into XML, and handles every data type, true/false object visibility included.

Whichever way you generate the XML, the last step is the same: load that XML back into the program. Do it right, and the arrows for switching between loaded data sets in the Variables panel become active.

![Switching between data sets in Illustrator](https://your-scorpion.ru/wp-content/uploads/2016/05/variable_illustrator.gif)

## Batch processing

Now it's time to reach for batch processing. First, object names need to follow XML naming conventions, so head into the preferences and check this useful box:

![Illustrator XML naming setting](https://your-scorpion.ru/wp-content/uploads/2016/05/setting_illustrator.png)

Click Next Data Set in the Variables palette to confirm every piece of data landed neatly and correctly in the template. We'll use batch processing to save each individual business card, but first we need to build an action that saves the file in the right format with the correct print settings.

> Once the work is done, save the template as SVG using File > Save As. This lets you carry the work into other Adobe products — Adobe® Graphics Server, for instance — so a developer can connect server data directly to your layout. To do this, check "Include Adobe Graphics Server Data" in the save settings.

Building the action works the same way it does in Photoshop. Click New Action, name your action, and click Record. Save the template in the format you want, then stop recording. Now for the batch itself: choose Batch from the Actions palette's dialog menu, and select the action you just built.

![Batch processing in Illustrator](https://your-scorpion.ru/wp-content/uploads/2016/05/lsn.png)

*Batch processing in Illustrator*

For Source, choose the Data Set you built. For Destination, choose None, and set the file name in File Name. Click OK to run the batch. Done — you've saved yourself a couple of hours and learned Illustrator's variables feature. Congratulations.

## A related complication worth flagging up front: getting other programs to talk to Illustrator at all

None of the above matters much if the data itself, or the files feeding your template, can't move cleanly between programs in the first place — and that's a problem worth solving before you ever open the Variables panel. In Illustrator's own preferences, enable AICB and Preserve Path under File Handling — these govern clipboard behavior, and without that box checked, files paste into other editors as flat bitmaps instead of editable paths. If you need a visible preview after importing a file elsewhere, save with Create PDF Compatible File checked; that single checkbox has saved plenty of projects — if a linked .ai file ever goes missing, as long as the file that imported it kept a PDF preview, you can still pull that preview out and use it as an image.

In InDesign specifically, if you want to paste in editable text, go to Preferences > Clipboard Handling and choose Text Only (no formatting) — or All Information in that same menu if you need to preserve appearance, patterns, transparency, and Word styles. Pick whichever setting suits you and save separate presets for DOC and RTF. On import, checking Show Import Options and using Object > Object Layer Options lets you choose which layers to display (this works with PSD, PDF, AI, and INDD).

Photoshop, for its part, can't import vector graphics at all — the only way to bring curves in is through the clipboard, and you'll run into fewer problems if you import complex data as a Smart Object and layer Smart Filters on top afterward, since that avoids pixel degradation entirely. Going the other direction, from Photoshop into Illustrator, to preserve editable text, open the PSD directly in Illustrator, or use File > Place without the Link checkbox, and check Convert to Layers. Adjustment layers can't be imported into Illustrator at all, so those layers need to be flattened first — there's a more detailed guide on this in the section on [moving between Photoshop and Illustrator](http://your-scorpion.ru/blog/basic_technical_drawing/).

If you ever need to reset Illustrator to its factory settings, hold Cmd-Opt-Ctrl-Shift on startup on macOS, or Alt-Ctrl-Shift on Windows.

For plain text editors, the standard trick is saving a .doc as .docx, then back to .doc; from there you can also save as .rtf and paste it into your target editor with Ctrl+D. If none of that helps, copy the entire text into docs.google.com and re-save it from there as .docx or .rtf. If problems persist, the [DoTextOk](https://github.com/Mikhail-Ivanyushin/DoTextOK) script is worth trying — forced line breaks (ASCII character 31) don't survive in Text Only mode under Clipboard Handling preferences, whereas pasting text directly carries line breaks across as discretionary breaks instead.

## Handling large batches of individual objects

Everything above assumes a handful of objects feeding a single template, but the same batch logic scales up to a genuinely large number of separate objects too — say, more than a hundred sitting in one file that each need their own individually exported output.

If those objects are scattered with no particular structure, select all of them and, in the Align panel, choose Horizontal Distribute Space — this lines everything up in a row with even spacing. From there, you need one artboard per object. Artboards can be built out of square shapes: create as many squares as you have objects to export (the Blend Tool is well suited to this), then apply Object > Artboards > Convert to Artboards to those squares, and you'll have your artboards.

![Building one artboard per object](http://your-scorpion.ru/wp-content/uploads/2016/12/Screenshot_1.png)

Or use a script instead:

```javascript
function make_artboards(){
    // written by Qwertyfly
    // this may not work as expected if art contains Clipping paths
    if(app.documents.length==0){return;}
    var doc = app.activeDocument;
    var grps = doc.pageItems;
    var abs = doc.artboards;
    var c = 0;
    for(var i=0; i<grps.length; i++){
        if(grps[i].parent.typename == "Layer"){
            c++
        }
    }
    if(c<=100){
        for(var i=1; i<abs.length; i++){
            abs[i].remove();
        }
        for(var i=0; i<grps.length; i++){
            if(grps[i].parent.typename == "Layer"){
                //this one needs an artboard
                var box = grps[i].visibleBounds;
                var AB = doc.artboards.add(box);
                if(!i){abs[0].remove();}
            }
        }
    }else{
        alert(c + " is too many items, max of 100 artboards per document");
    }
}
make_artboards();
```

Once you've got artboards with objects inside them, a further [script](https://gist.github.com/larrybotha/5baf6a9aea8da574cbbe) handles exporting the layers and artboards, or you can use File > Export > Export for Screens directly.

A common gotcha with batch export specifically, worth flagging since it trips people up constantly: if Illustrator keeps overwriting the same output file over and over instead of producing a separate file per data set, check the box for "Override Action 'Save' Commands" in the Batch dialog — and it's also worth explicitly setting a destination folder for the saved files in that same dialog, rather than leaving it on the action's own save location.

You can build considerably more advanced scripts than any of this, too. At my current job, for instance, I built a script for translating layouts into another language: it pulls all the text out of an Illustrator file into Excel with a single click, and once the spreadsheet's been translated, loading it back into Illustrator replaces every piece of text with the translated version.

If you run into script trouble, the command `defaults write com.adobe.CSXS.9 PlayerDebugMode 1` often helps. Any `.zxp` file just needs renaming to `.zip`, unzipping, and moving into `~/Library/Application Support/Adobe/CEP/extensions`.

---

## Addenda: a couple of adjacent, closely related tasks

A couple of reader questions sit just outside variable data specifically, but stay close enough to the same territory — repetitive, data-driven layout work — that they're worth folding in here.

**Slicing an image into many separate pieces.** Draw white shapes over the image using the Multiply blend mode, select everything, and apply Flatten Transparency with Vector set to 100% and Preserve Alpha Transparency checked.

![Slicing an image using Multiply and Flatten Transparency](http://your-scorpion.ru/wp-content/uploads/2019/05/Untitled-1.jpg)

**Building a large batch of charts automatically for a brochure layout**, when the source data lives in Excel and the target layout lives in InDesign, is really the same problem this whole article addresses, just one step removed — Excel doesn't just hold text to merge, it can hold chart data too. If the charts already exist as finished images in Excel, the straightforward path is exporting them as images and placing them with Data Merge (the same [Data Merge](https://your-scorpion.ru/principle-data-merge-indesign/) workflow this article opened by comparing itself to). If instead you only have the raw numbers and need InDesign to build the charts itself, a dedicated [font](https://www.fontshop.com/families/ff-chartwell?SiteId=1299) exists specifically for building charts out of styled text characters, alongside [plugins](https://www.indiscripts.com/post/2010/06/claquos2-pie-chart-builder-for-indesign) built for the same purpose.

Exporting an Excel sheet as a web page is a reliable way to get clean chart images out of it — doing so drops every chart as a separate image file into an accompanying `filename_files` folder. From there, the usual Data Merge error, "the data source refers to one or more missing images," almost always comes down to one of a small set of causes: an incorrect path, an unsupported format, a missing file, or Cyrillic characters somewhere in the file path. The cleanest fix is generating the CSV programmatically rather than by hand. Put every chart image in a single folder, then run a script like the one below from InDesign, point it at that folder, and it builds a correctly pathed CSV to feed straight into Data Merge:

```javascript
Array.prototype.inArray = function(obj){
    var arrMax = this.length-1;
    for(var i=arrMax; i>=0; i--){
        if(this[i]===obj){
            return true;
        }
    }
    return false;
}

var csvParser = (function(){
    var csvFile;
    return{
        create:function(fo){
            csvFile=File(fo+"/"+fo.name+".csv");
        },
        write:function(csvContent){
            csvFile.open('w');
            csvFile.encoding="UTF-8";
            csvFile.write(csvContent);
            csvFile.close();
        },
        execute:function(){
            csvFile.execute();
        },
        getCSV:function(){
            return csvFile;
        }
    }
})();

function imagesToCSVthenChoose(){
    var doc,
        fo,
        fis,
        fiMax,
        fi,
        fiName,
        fiPath,
        imgFormats=["eps","jpg","tif","psd","pdf","png","ai","bmp","jpeg"],
        imgFormatMax = imgFormats.length-1,
        imgOk = [],
        csvContent = [],
        ext,
        csvLine=[],
        csvSep=",";

    if(app.documents.length==0){
        alert("No documents open !");
        return
    }

    doc=app.activeDocument;
    fo = Folder.selectDialog("Please choose a folder with images");
    if(!fo) return

    fis = fo.getFiles();
    fiMax=fis.length;

    for(var i=0; i<fiMax; i++){
        fi=fis[i];
        ext = fi.name.match(/.([a-z]+)$/i);
        if(ext==null) continue;
        ext = ext[1].toLowerCase();
        if(!imgFormats.inArray(ext)) continue;
        fiName = decodeURI(fi.name);
        fiPath=decodeURI(fi.fsName);
        csvContent.push(fiName+csvSep+fiPath);
    }

    csvContent = "Name"+csvSep+"@imagesr"+csvContent.join("r");
    csvParser.create(fo);
    csvParser.write(csvContent);
}

imagesToCSVthenChoose();
```

Save that script as a `.jsx` file into InDesign's scripts folder to run it, or download it ready-made [here](http://indesignsecrets.com/downloads/ImagesToCSV.jsx.zip). As for charts that vary in size once placed, InDesign's Content Placement Options setting handles that reasonably well on its own. And where charts still need to be built entirely from scratch — genuinely simple pie charts and line/bar charts rather than pulling in finished images — that's exactly the territory the Chartwell font and the pie-chart plugin linked above are built for, since neither Illustrator nor InDesign will generate a chart natively from raw percentages sitting in a spreadsheet.
