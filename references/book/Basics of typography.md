# The Basics of Typography. For the Web, Too.

*Maksim Tcvetkov · Product Design and Security Lead*


**Introduction.** Type is the single most important part of the corporate identity of any self-respecting company, and one of the few branches of design in Russia that still hasn't been popularized as it deserves to be. Companies that order their design "from the outside," especially through tenders, frequently end up with no coherent visual identity at all, thanks to the way design studios tend to operate. The result is the dreadful type you see at many large domestic companies — particularly when they've worked up the nerve to commission a Cyrillic typeface from a Western studio. Typography is the craft of working with type, and type has to give *emotion* to text. Not merely to be quirky.

## A short history of writing

Historically it all began with pictographic writing — simple cave drawings. Over time writing grew more complex and hieroglyphs appeared, each symbol carrying its own commonly understood meanings. The next logical stage was syllabic writing, where a symbol stands for a syllable. And the evolutionary endpoint, for us, is modern phonetic writing and the alphabet, in which one symbol equals one sound.

At every stage of this development there were different instruments for forming the symbols — fingers and sticks for cave drawings, or a reed brush in Ancient Egypt. In time, sets of reed pens with various nibs appeared. It's from these that flat-nib pens descend, and they set the aesthetic of serif type that has survived to this day.

Majuscule writing became enormously popular, since at first only capital letters existed. But already by the medieval period — the 8th century — the Carolingian minuscule appeared, bringing speed of writing through simple forms and the contrast produced by a flat nib held at a fixed angle. Several centuries later all of this led to Gothic writing and a new instrument: the broad, trimmed goose quill. Gothic script can be summed up by the word *Blackletter*, since it leaves very little empty space and so saved precious parchment. Its legibility was poor, and the next turn of the evolutionary wheel wasn't long in coming — the humanist minuscule. And there the history of writing ends, for us, because in the 15th century Johannes Gutenberg created the first printing press. The letters (the *types*) were made of metal, and that's where the term "type case" originates.

The first typeset face was Gothic — even though it was considered barbaric in the cultural centre of the time, Italy. As we already know, Gothic letters read badly, and to the rescue came Nicolas Jenson with his typeface "Jenson," a genuinely legible face. Another milestone was Aldus Manutius, who made books affordable for the middle class and popularized them: books became compact, subject matter spread beyond religion, and italics appeared to save space, along with punctuation marks. Italics can vary. In Renaissance faces the letters were strongly "cursive," but in the modernist faces of the 20th century the letters came closer to a simple oblique. In the Latin alphabet it's the *plasticity* of the signs that changes, not their construction. Cyrillic, by contrast, is characterized by changes in the construction of its letters as well.

![Evolution of letterforms](https://your-scorpion.ru/wp-content/uploads/2020/12/ffss.png)

The next iteration arrived in 1725 and is tied to the name of William Caslon, author of the Adobe Caslon old-style face, who set the typographic tone for the English-speaking world. The drawing of the letters began to change, the pointed nib appeared, and stroke thickness now came from the pressure of the hand. Then, in the second half of the 19th century, the modernists dared to remove serifs and advertising was born — pure rationality and function, the birthday of Futura. The concept didn't quite take, but it gave rise to Gill Sans and Helvetica.

Type production became less laborious thanks to the Linotype, which could cast an entire line at once and melt it back down into metal.

The evolution of typesetting ran along a chain: metal type on the stamp principle → the pantograph → "Monotype" composing machines → phototypesetting → modern vector-graphics technology. This whole journey left a deep mark on the final result and on the aesthetics of letters; the faces born along the way define whole categories and styles. Stencil faces, the typewriter face, artistic constructivism. Technical limitations of the past have become recognizable styles in our own time.

I started out working with type on real materials, not on a computer. My first encounter with computer fonts on Windows 95 horrified me: the rounded letters were exactly the same height as the rest, the kerning was uniform, and there were all the other tell-tale signs of work done by people who don't understand the foundations of the craft. To me — someone who has known how to work with lettering stencils since the age of 14 — it looked appalling. But I can't pass judgement on the authors of those technologies, since out of curiosity I worked in DOS myself. There, the video adapter supported several screen modes; text mode gave you only 25 rows of 80 columns and 16 colours. There could be no talk of typography. In that mode you could only output crude symbols. The character table held 256 symbols in 8×14 or 8×16 matrices. So in this article I'll try to lay out the basics of legibility and the fundamental technical points.

## Readability is the starting point

The point of departure for creating or choosing a typeface is its legibility — type is the primary and most important means of setting a publication. But the text you have to set matters a great deal too. Its logical structure, its volume, its rhythm — all of it affects the final composition and, consequently, how the reader perceives it. The ultimate goal is legible text. Designers who aim to minimize the number of type sizes often shift to a horizontal layout of content, which lets them get by with one or two sizes. The philosophy of symbols to keep in mind when working with type: "Any disproportion looks attractive; any anomaly signals danger."

There is the concept of the *typeface family* (гарнитура) — a set of fonts with stylistic unity across all the characters, containing several weights of differing density.

There are two main groups of type: serif (antiqua) and sans (grotesque). Serif means a typeface *with* serifs — *serif* literally means the little terminal stroke — and grotesque means *without* serifs. Serifs come in many kinds and matter enormously; even in sans-serif faces the qualifier *sans serif* ("no serifs") gets added. Grotesques can be script, monospaced, symbolic, and display. Script faces are easy to spot by the "script" ending in their names.

### Small caps, while we're on faces

A natural question once you start thinking about faces: how do these rules apply to small caps, and when are they normally used? Small caps are a separate face within a typeface family. They differ in that the lowercase characters resemble reduced capitals while matching the height of the lowercase. That said, you can't get true small caps simply by shrinking the capitals — in quality fonts the small-cap characters are separately drawn. They're used for setting names, abbreviations, and titles, for highlighting a passage within text, and for certain kinds of headings. (As a reminder of the timeline: serif faces were used everywhere until the 19th century, and the first sans-serif — the grotesque — appeared at its start.)

Grotesques generally have no contrast, which is why a medium-contrast serif with an open aperture is still the better choice for body text — even though the grotesque has become the most popular family of all. For long passages, a serif is preferable: Garamond, for instance, the favourite of both Steve Jobs and Wikipedia, or the more interesting Brill, Octava, Cloister, Birka, Goudy, and Village 1903. For short texts, grotesques are recommended, such as Verdana, Tahoma, and Segoe UI on Windows, and Helvetica on macOS.

Worth singling out is the humanist grotesque, the favourite of most interface designers — again Tahoma, Verdana, Corbel, Meta, Segoe, Gill Sans, Frutiger, Optima, Lucida. Grotesques were originally used as display faces. The humanist grotesque carries a hint of calligraphy thanks to its pen-derived elements, which is what allows it to stay readable across large bodies of text.

![Humanist grotesque examples](https://your-scorpion.ru/wp-content/uploads/2020/12/Group-84.png)

The old grotesques are the very first ones, such as Franklin Gothic, PF Das Grotesk Pro, and Akzidenz-Grotesk — contrast in the letters is still visible. Inter is hard to call a modern take on the old grotesque, but its simplicity and popularity oblige me to mention it in this section.

The new grotesques are the best-known and most popular on the modern web — Arial, Helvetica, Roboto, Univers, CNN Sans, IBM Plex, MS Sans Serif, San Francisco Pro for macOS and iOS, and Compact for the Apple Watch. They're more geometric, with no hint of serif heritage, neutral forms, and a closed aperture. The starting point was Helvetica, created at the Haas Type Foundry in 1956 (also known as Neue Haas Grotesk), which was slightly improved as Helvetica Neue in 1982. The trouble with Helvetica is that the letters are all too similar to one another — unacceptable at small sizes. The domestic Golos is rather better in that respect.

![New grotesque animation](https://your-scorpion.ru/wp-content/uploads/2021/01/fs.gif)

Geometric grotesques are built on geometric forms plus optical compensation: Futura, Gilroy. On many projects I've successfully replaced Futura with Tilda Sans.

![Geometric grotesque examples](https://your-scorpion.ru/wp-content/uploads/2020/12/Group-85.png)

Old-style serif is an umbrella term for several regional serifs; it inherits all the traits of broad-nib handwriting, hence the low contrast in the letters. It's marked by a slanted axis on the ovals, open apertures, and asymmetric serifs. The best-known example is Cheltenham.

![Cheltenham](https://your-scorpion.ru/wp-content/uploads/2020/12/Cheltenham.png)

Transitional serif marks the move from the broad nib to the pointed nib. We all know Times New Roman, Baskerville, Caslon, Georgia, and Bookman. They all combine elements of old-style handwriting with elements of the new style: the axis of the ovals has straightened, contrast has increased, the serifs are symmetric. Baskerville stands out for a certain roundness and jewel-like quality of its letters; a modern alternative is Vollkorn.

![Transitional serif examples](https://your-scorpion.ru/wp-content/uploads/2020/12/Group-86.png)

New-style, or Didone, serif uses the pointed nib. The letters are very contrasty, which isn't great for readability: Didot, Dido, Bodoni. The face is *drawn*, with no trace of handwriting. Luxury goods love the new-style serif — because in the 19th century you could only print such thin lines on good paper with expensive ink. Note the "б" in Bodoni: an excellent example of a letter for the new-style serif.

![Didone serif examples](https://your-scorpion.ru/wp-content/uploads/2020/12/Group-85-2.png)

Slab serif has straight, massive serifs, as in Clarendon and Officina (the latter closer to a grotesque). Not for bodies of text — these are display faces, really.

Display fonts are sometimes singled out as a separate category, mostly to file away everything that's neither serif nor grotesque. Modern fonts can contain parameters from several groups, and classification keeps getting harder.

If we broaden the categories a little:

- Grotesque — Syntax.
- Venetian serif — that most ancient serif; a bright representative is Centaur.
- Garalde (Italo-French serif) — Sabon.
- Transitional serif — Janson.
- Glyphic / ribbon serif — Optima.
- New serif — Bodoni, on Italian motifs; Playfair belongs in the same category.
- Slab serif — Memphis, with slab serifs as a must.
- Display / titling — faces for highlighting something important, e.g. Fleurs du mal; odd proportions in the capitals are permitted.
- Calligraphy — handwriting-style faces, e.g. Poetica Chancery.
- Others (Old Slavonic, Asian, symbol).

When designing a typeface it's important to consider the individuality of each letter's form. Every letter must have its own characteristic feature that guarantees it will be read correctly and can't be mistaken for some other symbol. No superfluous elements — you want rigour and completeness of form, and, most important of all, rhythm in the alternation of letters as they combine into a word. Happily, the high-ranking Byzantine statesmen and clerics Cyril and Methodius did good work in their day: unlike the Europeans, who keep re-cutting the Latin alphabet, we have no superscript marks, no multi-letter symbols for single sounds, no diacritics. Which is why Bulgaria is the cradle of Slavic written culture, the land that gave us the Glagolitic script.

![Glagolitic script](https://your-scorpion.ru/wp-content/uploads/2022/03/unknown.png)

*Glagolitic.*

Fonts also come as Uniwidth and Monospaced. In a uniwidth font the width of the letters doesn't change as the weight changes. Monospaced fonts, by contrast, are obliged to give every letter the same width and height. Among the monospaced (typewriter) faces my own favourite is [OCR-A](https://en.wikipedia.org/wiki/OCR-A), which I'd recommend — it even holds up for text on microchips; you might also look at Courier and Iosevka Slab. It's entirely respectable to use Apple's San Francisco, which has a monospaced version for tables, provided the Cyrillic Ё and Ъ aren't critical for you. For dessert, [Source Code Pro](https://github.com/adobe-fonts/source-code-pro) and [FiraCode](https://github.com/tonsky/FiraCode) with ligatures, or pick one [to your own taste](https://app.programmingfonts.org/#font3270); the classic is Fira Code with ligatures switched on, and Letter Gothic is worth a glance too. For pure code work, the most popular today are Fira Code and JetBrains Mono. And don't forget the uniwidth faces — unlike monospaced ones, their characters keep familiar proportions, which makes them useful in very dense interfaces; an example is ParaType's Root, alongside plenty of [Latin options](https://www.fontshop.com/people/david-sudweeks/fontlists/uniwidth-typefaces).

## Definitions and fundamentals

Let's set out the core terms first.

**Stem** (the main stroke) is the vertical line. A horizontal stroke is called a **crossbar**.

**Baseline** is an imaginary line running along the lower edge of a letter, ignoring its extenders. This matters when you hand layouts off to development: will distances be measured from the baseline, or from the BBox?

**x-height** is the height of a lowercase character. The name is no accident — you simply draw two lines along the top and bottom of the `x`, ignoring all the optical compensation of rounded letters and any extenders. Maximum height is read off the characters Åy, and width is easily gauged from Ws. There are settled traditions for letter widths and proportions — for example, "щ" is wider than "н," and "о" is wider than "г." On the subject of height, it's important to align your grid to the x-height rather than to the height of the capitals: the main visual weight of letters sits in the x-height region. The exception is that abomination, setting in all caps.

![x-height illustration](https://your-scorpion.ru/wp-content/uploads/2020/06/Group-63.png)

**Serif** is a small stroke or extension on a letter's main strokes. Serifs guide the eye along the lines, which is why they're so popular in print and on websites whose authors approach design a little too academically.

**Counter** is the empty space inside a letter; it can be closed or open (`e` and `g` respectively). Any art director will tell you, over and over, that a letter is not only its drawn form but also the empty space within it, which characterizes the letter's openness or closedness. And they'll be right.

**Очко** (the printing face), in its original sense, meant the upper end face of a type's head or of the printing elements of a composed forme — in effect, a mirror image of the symbol. Nowadays the term denotes the drawn image of a symbol together with its spacing elements.

**Letter-spacing** differs from kerning in that it increases all the side-bearings in the text uniformly; historically it was a simple rectangular block used to mark spaces. In modern typesetting there are two main kinds of spacing elements.

**Leading** (интерлиньяж) is the space between lines — a very important parameter for legibility. Different letterforms call for different leading, so there are no universal values here, as is the case with nearly all of typography. But one-fifth of the type size is usually fine: at a size of 10, the leading should be 12. So there *are* rules — for instance, if the leading is larger than the size, you can increase the kerning a touch to improve readability. Leading is always equal to the first-line indent. A curious fact: the correct word for the calculation of line spacing is "leading" — which is exactly интерлиньяж. People sometimes confuse it and use "leading" to mean the height of Vincent's virtual area, even though Vincent himself used the English term "leading" in his articles.

There is also the spacing built from kerning and tracking. **Approche** (апрош) is the distance between adjacent glyphs. A **half-approche** (полуапрош) is the distance from the inner side wall of a container's boundary to the nearest point on the letter.

**Kerning** is the distance between letters as a function of their shapes. It's complicated by the fact that the kerning between one pair of letters and another can differ, and automation can't solve that problem — compare "кл" and "дл." Kerning also serves to even out the approches. If you're working with a finished font, it's best not to touch this parameter without a 146% understanding of how kerning works — and it's a fairly dull, slow job besides. With numerals and Cyrillic characters you have to pay particular attention to kerning; the rule holds for any character with vertical strokes — roughly, "fence" letters need more space than rounded ones. Tracking, meanwhile, is the spacing between letters that frequently appear together (for example the prefixes "роз/раз," "пре/при," and so on). In InDesign, 5% / −2% / 5% are reference values for tracking, and as a benchmark tracking shouldn't exceed ±25. It solves the task of making text looser or tighter. Type designers usually build sensible approches and side-bearings into their work, so on the whole things shouldn't be too bad. A handy phrase for checking kerning: "вот где клад для нашей жабы!"

There's a related question that comes up constantly — how kerning and tracking actually work in Photoshop, because the letters keep sticking together. Kerning is the space between *pairs* of characters; tracking is the space across an entire block of text, and ideally you use tracking and manual kerning together. Both are proportional to the current type size: for an 8-point font, 1 em equals 8 points; for a 16-point font, 1 em equals 16 points. The conversion to em is X / 1000 = Y, where X is the spacing value shown in Photoshop and Y is that same value expressed in em. The conversion to pixels is X · S / 1000 = P, where S is the font size in pixels on a 72-dpi screen. So −50 in Photoshop equals −0.05 em; by the same logic, 10 is 0.001 em, 50 is 0.05 em, 500 is 0.5 em. Photoshop offers two kerning modes: *metric*, which uses the kerning data built into the font and is the default, and *optical*, which derives kerning from the sizes of the letters. To cure the "sticking" of letters, switch off fractional character widths — this constrains the inter-character spacing to whole pixel values; the setting lives in the Character panel menu under "Fractional Width." But again, keep an eye on the platforms you're designing for. On Android Lollipop, say, tracking is hard to change and is set not in pixels but as a percentage of the width of the letter *m*; from the documentation: "The value is in 'EM' units. Typical values for slight expansion will be around 0.05. Negative values tighten text."

**Ligature** places two or three signs on a single body to avoid situations where kerning is powerless. You may even edit the drawing of the letters and join them. Ligatures are used for difficult letter pairs — a common phenomenon in English — and they're also a way to make a font more recognizable.

**Point size** (кегль) is the height of a letter including its lower and upper extenders, measured in typographic points (the fundamental relative unit being the em). A little historical detour is needed here: in the old days a line of set text was assembled from individual types — metal blocks bearing mirror-image text characters — and it was the *height of the type* that was called the point size. In modern terms, the "type" is the system that forms the composed font; so the point size is the entire height of the letter. The standard point sizes — 6, 8, 10, 12, 16, 20, 24, 28, 36, and 46 — are likewise inherited from the old casting technique. In the 18th century the Frenchman Firmin Didot brought the foundations of the typometric system to maturity, and in the 19th it was adopted, in slightly modified form, in England and America.

But what's done is done; in our age of total computerization all sizes are set relative to an abstract em. Suppose the bounding box of the letter "ч" works out to 0.45 × 0.41 em. When that letter is sent to an output device, all its dimensions scale in proportion to the point size. Set "ч" at 18 points and its dimensions become 18 × 0.45 and 18 × 0.41 = 8.1 and 7.38. There is, of course, a degree of standardization: in most computer fonts, as in metal, the height of lowercase letters varies from about 0.40 to 0.55 of the size, and the height of capitals from 0.65 to 0.72. Two systems of typographic measurement are in use today, differing in the size of the point: the Didot system (1 point = 0.375 mm) and the Anglo-American (1 point = 0.352 mm). Russia uses Didot; so, fortunately for Russian designers, does Europe. Unfortunately, computer typesetting is dominated by the Anglo-American system. For simplicity, many layout programs define the point as 1/72 inch (0.3528 mm, or 352.777773956019 microns). And yes — thanks to Adobe for adopting 1/72 inch as the standard in their software, or we'd still be suffering with the differing point values of Fournier, Didot, and a few others.

**Paragraph indent** is the space at the start of a paragraph's first line, meant to carry the reader's eye to the next group of text in a book. Everything here is very dogmatic: the indent is one em (so at a size of 12, it's 12 points). Only one situation calls for no indent — beneath a centred heading, where the first paragraph always begins flush. But under a left-aligned heading, the indent is required.

It's also important to remember optical illusions: letters with pointed and oval apexes — "а," "о," "з," and the rest — look smaller than rectangular ones like "ы," "ш," "п." To compensate, the round and pointed letters must sit slightly higher than the others.

Fonts have a fundamental relative unit of measurement: the em. It descends from the spaces (blocks of varying thickness used for spacing in hand-setting); a space one size wide was called an em, and one half that, an en. It follows that, depending on the point size, the width of an em in points can vary. When in doubt about what to do, you can take one-third of an em as your starting point.

Then there's the Panose font-classification system (best not said aloud in front of the uninitiated). It's a TrueType thing, describing fonts by 10 parameters: family, serif form, weight, proportion, contrast, stroke, arm (the form of stroke terminals), letterform, midline, and x-height (the match of lowercase to capital height). Verdana, for example, is described as 1:2 11 6 4 3 5 4 4 2 4, and Calibri as 1:2 15 5 2 2 2 4 3 2 4.

## The structure of letters in Cyrillic

![Letter A](https://your-scorpion.ru/wp-content/uploads/2020/03/А-а-а-А-А-а-а.png)

**А** — called "Az" in the Church Slavonic alphabet; an angel. It originates in the ancient Greek Alpha (Α α), and in a more ancient reading, an upside-down Aleph, which in Phoenician represents the head of an ox. The structure of А in Cyrillic is no different from the Latin, except that the Latin may carry a horizontal stroke whereas the Cyrillic and Greek may not — otherwise it becomes the letter Л. In serif faces the left stroke should be thin and the right one heavy. All ascending lines should be thin and all descending ones thick: the descending stroke matches the main stroke in weight, the thin ascending one the connecting stroke. The lowercase "а" is very varied and rich in ornament (the top arc, the bowl, the vertical stroke, the tail), and such elements very often express the style of the whole font or piece of lettering. The balance between arc and bowl can suffer from clutter; a good type designer avoids that. The italic form of "*а*" is its own story; sans faces are conventionally made oblique (not cursive), finishing the stroke with a handsome terminal.

![Letter B](https://your-scorpion.ru/wp-content/uploads/2020/03/Б-б-б-Б-Б-б-б.png)

**Б.** The gloomiest letter of Cyrillic — "Buka" (though "Bukva," "letter," is closer) — going back to the Greek Beta (Β, β) and originally from the Phoenician word "bet," house. Slavs can't do without it: it's the first letter of the word God ("Бог"). The years have scarcely changed it; only a serif-style half-oval appeared in the lower part, and the upper horizontal stroke can't be longer than that half-oval. The top of the letter should be slightly bowed (serif). A serif on the upper horizontal stroke that slants outward is a serif trait; a vertical serif marks a slab face. Slab serifs are distinguished by rectangular serifs, as seen in the examples below. The upper and lower joins of the half-oval to the straight stroke should be at right angles (grotesque). The capital "Б" is virtually the same everywhere; the half-oval joins the main stroke above its optical midpoint. The form and height of the top tail of the capital "Б" are in no way fixed; the double curve has an arched look and turns inward, which can make it resemble the digit 6. The tail is always a continuation of the spine — you take a flattened "о" as your basis and add a little tail, and the widths of "о" and "б" match. In their italic forms "*б*" and "*д*" look identical.

![Letter V](https://your-scorpion.ru/wp-content/uploads/2020/03/В-в-в-В-В-в-в.png)

**В** — Greek Β, β (vita); the Cyrillic "В" is analogous to the Latin "B." It has the most stroke-intersection points of any letter, all of four. So in bold weights you have to watch the internal space, which is often too tight: this is the main thing to mind, the small space inside the ovals (the same problem afflicts Я). It's therefore acceptable to make the horizontal strokes thinner than in other letters. The lower half-oval projects further right than the upper one. The middle node always butts in (the ovals enter the vertical stroke at right angles) and sits above the letter's centre; the lower node may be rounded. In the lowercase letter the horizontal stroke fits poorly into the x-height matrix, so the upper and lower nodes often jam into the vertical stroke at right angles. The italic form is characterized by an unclosed stroke, though not always. In general, across all letters, the italic form can be low (running to the height of the line — the main variant) or high (like a schoolbook copybook hand, with a loop). Oblique strokes belong to the grotesque — a simple slant at some angle — whereas cursive strokes belong to the serif, where the form of the signs differs significantly.

![Letter G](https://your-scorpion.ru/wp-content/uploads/2020/03/Г-г-г-Г-Г-г-г.png)

**Г.** The simplest letter. "Glagol," to speak. Derived from the ancient Greek Gamma (Γ, γ), which as usual has Phoenician roots. If you want a Latin analogue, it's an "F" without the middle stroke and narrower. The top serif is like the one on "Б," but narrower. In serif faces the horizontal stroke of the lowercase letter ends in a large serif, which gives good visual distinction.

![Letter D](https://your-scorpion.ru/wp-content/uploads/2020/03/Д-д-д-Д-Д-д-д-д.png)

**Д.** The form is inherited from the Greek Delta (Δ, δ). "Dver," a door. It looks more like a Chinese character than a European letter. It comes mainly in two forms, triangular and trapezoidal. The trapezoidal is more frequent, and the rule matches that for "Л." The triangular is more antique and geometric, and harder to work with, since there's a lot of space to its left and right that's awkward to kern. The left vertical stroke is always slightly angled and enters the crossbar quite hard, with no smooth join. The "feet" shouldn't be too small, but no larger than the main lower extenders. The capital and small-cap versions of "Д-д" are very alike. The italic, though, is fascinating: *д*. This is the tail-up example — the most frequent, and the most Cyrillic — and there's also a tail-down variation; tail-down is a greeting from calligraphy.

![Letters E, Yo](https://your-scorpion.ru/wp-content/uploads/2020/03/Е-е-Е-е-Е-е-Ё-е-ё.png)

![Letter sample](https://your-scorpion.ru/wp-content/uploads/2013/12/sd.jpg)

**Е, Ё** — the letter Е from the Greek Epsilon (E, ε); "Е" is the verb *to be*. It came to us from the Phoenician and was flipped horizontally (the direction of writing changed). The letter is narrow, narrower than "Н." "Е" lends horizontal stability to rounded Latin letters. If the serifs on the horizontal strokes are concave on the outside, it's a serif face; if straight, a slab; or there are none at all. The letter "е" is one of the most frequent in European languages. "Ё" is very young compared with the rest; the dots on top are smaller than punctuation marks and slightly flattened, sitting at the same level as the diacritic on "й."

![Letter Zh](https://your-scorpion.ru/wp-content/uploads/2020/03/Ж-ж-Ж-ж-Ж-ж-ж.png)

**Ж.** It has no Greek analogue and strongly defines the aesthetic and recognizability of a Cyrillic face — especially when "branchy and curly." But there's a current trend toward resemblance with a Latin look. There are many theories of its origin; technically it's two narrowed "к"s, with the left stroke heavier and the right one lighter. The vertical stroke is narrowed, and a small horizontal stroke sometimes appears in the middle of the letter. In the lowercase you can do without teardrops, and the waist sits closer to the middle than in the capital.

![Letter Z](https://your-scorpion.ru/wp-content/uploads/2020/03/З-з-З-з-З-з-З-з.png)

**З.** "Zvuk," sound. Derived from the Greek Zeta (Ζ, ζ). A weapon, an arrow. An unstable letter, since it stands on a single point, with the lower element thrust to the left. Numbers used to be written with letters, and there was no conflict between the digit 3 and the letter З. We, however, have to separate the two somehow: you can flatten the top of the digit, for instance, while the letter must carry a serif on top (in a serif face) and a teardrop below. The digit is also usually narrower than the capital letter, and more closed. The join between the upper and lower half-ovals is conventionally made soft, unless it's a geometric face. The italic has only the lower teardrop, with the right part turning into something resembling a "з."

![Letters I, I-kratkoye](https://your-scorpion.ru/wp-content/uploads/2020/03/И-и-Й-й-И-и-И-и-и.png)

**И and Й.** Descended from the Phoenician letter Het, whose drawing resembled 日. It was originally drawn as "Н," while the letter Н was drawn as the Latin N. Then everything swapped. But that doesn't mean you can make "И" by simply mirroring "N." Remember the rule: everything going up should be thin, everything going down thick. So the diagonal stroke is always thin. The diagonal is often pulled away from the serifs, or the serifs aren't drawn at all. Verticals heavier, diagonal lighter.

![Letter K](https://your-scorpion.ru/wp-content/uploads/2020/03/К-к-к-К-К-к-К-к.png)

**К.** Greek Kappa (Κ, κ). In the Latin version all three main lines are straight; in the Russian the right part is more shapely. Even so, making the Latin and Cyrillic versions of the letter different is a mistake. A teardrop is added on top and the lower branch is curved (by analogy with Ж). The lower branch can also be curved — and the curve may go either way. The lowercase form has no tall mast.

![Letter L](https://your-scorpion.ru/wp-content/uploads/2020/03/Л-л-Л-л-Л-л-л-Л.png)

**Л.** The Greek Lambda (Λ, λ). You can still find a lambda-like look for the letter. It resembles "П" visually, but it shouldn't be that rectangular — though you can't overdo the slant either, or it gets hard to kern. It's one of the defining signs of Cyrillic: the left stroke is thin, and at the bottom there should be a curl or teardrop. In italic the left stroke is thinner still ("*л*"), and here a teardrop is absolutely indispensable.

![Letter M](https://your-scorpion.ru/wp-content/uploads/2020/03/М-м-М-м-м-м-М-М.png)

**М** takes its origin from the Greek Mu (Μ, μ). It mustn't be an upside-down "W." Slanted side strokes are permitted. The upper triangle is wider than the side angles and equal in width to the Latin "V," and it need not reach the baseline. The rhythm of the lines is clear: thin-thick-thin-thick. At the joins up top you can shift the lines a little, which visually lightens the angles. The side strokes can be slanted, even inward. The italic "*м*" has wave-like strokes on the left and right, a legacy of handwriting, and a hard sharp angle in the middle.

![Letter N](https://your-scorpion.ru/wp-content/uploads/2020/03/Н-н-н-Н-н-н-Н-Н.png)

**Н.** Nu (Ν, ν) — a fish. The proportions of the whole face are built on the letter "Н." There's even a test: —нннооонононо—, a sequence in which you must achieve harmony in the setting, which then gives the right relationship between straight and rounded signs. The inner serifs are usually shorter than the outer ones, and serifs of equal thickness are rare in general.

![Letter O](https://your-scorpion.ru/wp-content/uploads/2020/03/О-о-о-О-о-О-О-О-о.png)

**О** — the most frequent letter of Russian. It comes from the Greek Omicron (ὦ) and hasn't changed at all since Phoenician times. All round letters are considered derivatives of "О," in both Cyrillic and Latin. The lowercase "о" is wider than the capital "О," and closer to a true circle.

![Letter P](https://your-scorpion.ru/wp-content/uploads/2020/03/П-п-п-П-п-П-П-П-п.png)

**П.** From the Greek Pi (Π, π). Tables used to be arranged in a "П" shape, associated with "pokoy," peace. "П" is narrower than "Н." The italic "*п*" is a copy of the Latin "*n*."

![Letter R](https://your-scorpion.ru/wp-content/uploads/2020/03/Р-р-р-Р-р-Р-р-Р-р.png)

**Р.** The Greek Rho (Ρ, ρ). "Golova/govori" — head, speak. A Latin "P" with a long serif on the right. In the Cyrillic "Р" the half-oval should sit below the centre, and the arc should be close to the arcs of "р," "б," "ъ." The lower part of the arc may not touch the vertical stroke — that's a more ancient form of the letter. The right serif is longer than the left, and a lower extender is a rarity in general, yet "р" has one.

![Letter S](https://your-scorpion.ru/wp-content/uploads/2020/03/С-с-с-С-с-С-с-С-с.png)

**С.** Identical to the Latin; it defines the closedness/openness of all the letters. You can slant the inner oval. There may be serifs, in which case the lower "jaw" is thrust slightly forward. A serif pointing outward evokes the old-style serif; pointing inward, calligraphy or handwritten faces.

![Letter T](https://your-scorpion.ru/wp-content/uploads/2020/03/Т-т-т-Т-т-Т-т-Т-т.png)

**Т.** Derived from the Greek Tau (Τ, τ). No different from the Latin; make the width not too narrow, matching it to "Н." Serifs may slant inward if you want a more ancient look. The capital can have three feet, the way it's taught in school copybooks — possible only in Cyrillic; in Latin "T" always stands on one foot. The italic "*т*" is a copy of the Latin "*m*."

![Letter U](https://your-scorpion.ru/wp-content/uploads/2020/03/У-у-у-У-у-У-у-У-у.png)

**У.** Its roots are like the Latin Y. Graphically it's close to "ч"; the strokes should intersect below the centre, but not too low. It's a very unstable letter with little equilibrium — a heavy left stroke fastened to a thin ascending right one. You can round the tail, if working with leading doesn't scare you. A diagonal stroke always looks heavier than an equivalent vertical. The lowercase "у" is as close as possible to the Latin "y," its extender ending in a teardrop. In ancient Cyrillic this was a digraph of izhitsa (Ѵ), a copy of the Greek "ου," made of "о" and "у."

![Letter F](https://your-scorpion.ru/wp-content/uploads/2020/03/Ф-ф-ф-Ф-ф-Ф-ф-Ф-ф.png)

**Ф.** A fairly massive, standoffish letter, and far from the most frequent. "Hands on hips" — its form fits poorly among the forms of other Cyrillic and Latin characters. Draw the left and right "hands" as two ovals and you get a "Cheburashka." Or use a single oval, which in the capital sits in the middle of the main stroke — perhaps a touch above centre, purely for optical alignment. The junction of the ovals with the main stroke needs lightening. Draw one oval and rotate it 180 degrees rather than applying a flip command. An upper extender on the main stroke is essential if there's one at the bottom.

![Letter Kh](https://your-scorpion.ru/wp-content/uploads/2020/03/Х-х-х-Х-х-Х-х-Х-х.png)

**Х.** Graphically the analogue of the Latin "X," as wide as "А." It can be of the serif kind (strokes of differing thickness, thin going up, thin going down). The narrow stroke must be shifted to the right — another law of optical alignment. There's also the grotesque kind (equal thicknesses) and a third, rectangular kind used in narrow faces. You can draw this letter simply by taking two "С"s and flipping one (*х*).

![Letter Ts](https://your-scorpion.ru/wp-content/uploads/2020/03/Ц-ц-ц-Ц-ц-Ц-ц-Ц-ц.png)

**Ц.** Our little eastern letter. It resembles an upside-down "П," but narrower. There's a shoulder (the base on which the verticals stand), and from the shoulder runs a line equal in thickness to the vertical stroke. The thickness can vary, though; for references, look at the analogous element in "Д" and "Щ." The italic can have a very light descending tail, "*ц*," or a full, bold one.

![Letter Ch](https://your-scorpion.ru/wp-content/uploads/2020/03/Ч-ч-ч-Ч-ч-Ч-ч-Ч-ч.png)

**Ч.** Another little eastern letter; foreigners learning Russian confuse it with the digit "4." In ancient times it had the shape of a wine glass, but over time its symmetry was lost. The letter itself is narrower than the two-legged "И," "П," "Ц," but wider than "Р." It consists of two strokes — a supporting right one and a half-length left one — plus a connecting arc between them, and the join is usually drawn quite hard. The lowercase is identical to the capital, "Ч — ч," though you can see the arc is a little more curved in the capital. If you recall the copybooks, "*ч*" can be very calligraphic, but it's rare and reads poorly. In drawing it's close to the Latin "*r*."

![Letters Sh, Shch](https://your-scorpion.ru/wp-content/uploads/2020/03/Ш-Щ-ш-Щ-ш-Щ-ш-Ш-ш-ш.png)

**Ш, Щ.** The widest letter of the alphabet. Because it has three strokes, the gaps between the plain strokes must be narrower than in "П" or "Ц." If you make the tail not a serif but a flourish, you can extend it under the letter. The lowercase forms are very similar, "Ш — ш." Everyone knows how to frighten a foreigner: write the word *шиншилла* (chinchilla) by hand and you get an indistinguishable handwritten set of sticks. "Ш" coincides completely with its Cyrillic form in Glagolitic.

![Letters Hard sign, Soft sign](https://your-scorpion.ru/wp-content/uploads/2020/03/Ъ-ь-Ъ-ь-Ь-Ъ-ь-Ъ-ь-Ь-Ъ.png)

The diacritic signs **Ъ and Ь** have no sounds of their own — though once they did. "Ь" is a modification of the letters "О" and "И." "Ъ" is the rarest sign in Russian. The peak on the left of "Ъ" is the analogue of the left half of the letter "Т," but slightly narrower than the one on "Г." In italic, the "*ъ*" stroke may well be slanted. The lowercase forms are like the capitals, only the half-oval joins are harder, without rounding. The italic "*Ъ — Ы — Ь*" is marked by identical lower round elements; the lower part of the letters is quite rounded, only the upper joins are hard.

![Letter Y](https://your-scorpion.ru/wp-content/uploads/2020/03/Ы-ы-Ы-ы-Ы-ы-Ы-ы-Ы-ы.png)

**Ы.** Also "yer," "er," "kor." Their basis is the soft sign — and it is *not* an upside-down Р but a Б without the upper stroke. Ы is Ь + I. They aren't usually joined; joining is generally a lettering move.

![Letter E-oborotnoye](https://your-scorpion.ru/wp-content/uploads/2020/03/Э-э-Э-э-Э-э-Э-Э-э-э-Э.png)

**Э.** In its time it was much disliked by Lomonosov, who proposed writing the letter Е everywhere in its place. Part of the issue was that there was a round "Е," so "Э" was regarded as its reverse. "Э" isn't similar in drawing to "С"; it's closer to a "З" with less ornament (and "З" and "3" are alike). Comparing "Э" and "С," they have different dynamics, with the centre of gravity around the middle stroke. The horizontal "tongue" stroke can be straight without serifs, straight with a serif, or soft, wave-like, curved. In the lowercase form a serif and a teardrop often appear together, or two serifs at once. The italic "*э*" resembles an "*х*" with a wave-like tongue.

![Letter Yu](https://your-scorpion.ru/wp-content/uploads/2020/03/Ю-ю-Ю-ю-Ю-ю-Ю-Ю-ю-ю-Ю.png)

**Ю** = I + O. In sound it's "Yo." The horizontal stroke is a touch thinner than on "Н" and is drawn through the middle of the letter. It's usually drawn by combining Н + О.

![Letter Ya](https://your-scorpion.ru/wp-content/uploads/2020/03/Group-279.png)

**Я.** The half-oval is drawn not from a true semicircle but made a little squarish. The node joining the half-oval to the main stroke should sit a little below the letter's optical centre. The leg can be either curved or straight. In italic you can play with the tail: "*я*." The tail can be large and dynamic — and don't make it fully vertical.

**Numerals** should also have extenders and an uneven contour. The most popular figures are the majuscule, or lining, figures: all one height with the capitals. There are also minuscule, or oldstyle, figures — the lowercase kind, slightly taller than lowercase letters and with extenders. (These are the "jumping letters" you sometimes notice in a face: a set of figures, often with ascenders and descenders, meant to be used alongside lowercase in running text. In plain terms, they're the lowercase of the numeral world — and historically they came *first*, with the uppercase lining figures appearing later.) Oldstyle figures are the more ancient kind; their use faded over the course of history but became relevant again in the computer era. The rule: if the capitals carry a serif, the lowercase figures should carry a teardrop, or nothing. Figures are often made a little narrower than letters, because you need to separate 3 from З.

## Electronic display

Mac users are accustomed to FreeType-style rendering, but Windows uses a different system, ClearType. Microsoft's philosophy is legibility at the expense of aesthetics, whereas Apple is willing to accept blurred letter edges in order to match the designer's vision. When rendering, ClearType creates a subpixel of varying colour depending on the slant of the line, and it switches off above a certain letter size. If you're writing your own software, you can work with text properly thanks to the HarfBuzz, Cairo, and Pango libraries, and rasterize with [Skia](https://skia.org/) — the user's eye will be glad of soft LCD anti-aliasing.

Beyond that, three factors affect a font's display and smoothing on the web: the browser, the monitor, and the font itself. The browser and the monitor are beyond a site's developers, but choosing the font's parameters is 100% the site authors' responsibility. Don't tie yourself to some pixel value for type; there's no benchmark. Personally I use anywhere from 12 to 16 em depending on the situation, but I always keep in mind that vk.com and Facebook used 11px Tahoma and nobody complained. Body text is usually set at 12–14 points, and the line spacing should equal 1 to 1.5 of the size. Paragraphs exist to structure text, and a paragraph indent of 3–5 ems is recommended. More important is understanding what monitor dpi your site's audience will have — dpi being dots per linear inch, ppi being pixels per linear inch. In the 1980s monitor dpi was 70–74. Back then the most correct approach was Apple's, who set the value to the typographic 1/72 inch; later they decided to come over to Microsoft's value of 1 inch = 96px. But both 72 and 96 are too few dots to get rid of the jaggedness in displayed type, which is why subpixel smoothing is applied. On a website it's not advisable to set type size in pixels — a fixed size for the modern web is an unjustified evil. The developer or designer should choose an em value such that, at the standard setting, the font looks the way it was meant to in the layout. And the font absolutely must do TrueType hinting. Don't forget: on the Mac it's anti-aliasing, on Windows it's hinting. Hinting is an algorithm that "nudges" the points of a vector font to hit the pixel grid. So when you set a notional 14px in the browser, that's a relative, approximate display, because hinting may render the letters at 13px or 15px.

On Windows specifically, the familiar size for Segoe UI is still 12px (9pt). Go any smaller and anti-aliasing distorts the letterforms dramatically — especially if it isn't ClearType anti-aliasing but the grayscale kind used in many apps on the WPF engine.

Units of measurement on the web are absolute or relative. The absolute ones are inches (in), centimetres (cm), millimetres (mm), points (pt), picas (pc), and a dozen more that are rarely used. Remember the figures: 1pt = 1/72in, and 1pc = 12pt. These suit print better; for monitors you want relative units — % (percent), em, x-height (ex), and px (pixels). And yes, since you're already a little confused, let me dispel the doubt: 12 points and 12 pixels in Adobe programs are the same thing. Adobe products use a virtual resolution of 72 dots/pixels per inch — and there are exactly 72 rounded Adobe points in an inch. There's a reference number of characters per line for comfortable reading — 66, including spaces and punctuation — though you can play within a range of 45–75; for the eye, around 66 is optimal. Set your width in the `ch` unit and it'll all work out.

You don't have to reinvent the wheel when choosing a face; just look at the web-safe fonts: Arial, Arial Black, Comic Sans MS, Courier New, Georgia (very good), Impact, Times New Roman, Trebuchet MS, Verdana. They're all very good and present on the overwhelming majority of Windows, Mac, and Linux machines. Bear in mind that Verdana and Georgia were created for small sizes, and setting huge headlines in them is wrong.

A related question is whether to use system fonts or web fonts for a web project — the arguments being "web fonts look nicer" against "system fonts load faster." System fonts aren't necessarily ugly. Georgia is rather attractive (Windows, Mac, iOS) and looks very good on low-resolution screens — no wonder, the face dates all the way back to 1993; it does carry a whiff of imitation print, but on HiDPI screens it's perfectly fine. Charter ships with Windows, Mac, iOS, and Ubuntu, was likewise made for printing on less-than-ideal equipment, and looks good on budget screens. Palatino (Windows, Mac, iOS) has the same virtues, as does Hoefler Text (Mac and, with limitations, iOS).

There's also the cross-platform question for desktop apps — is there a universal font guaranteed to be present on every OS? Even with the same font, text on Windows takes up more space than the equivalent on Mac. The main advice is to use Arial: it's present and works well even under the domestic Astra Linux and on mobile OSes (Android excepted), though it lacks the semibold and light weights designers are used to. Tahoma, Times New Roman, and Verdana are alternatives.

On output, the designer should remember, at the very least, maximum and minimum line length, word-breaking, soft hyphens, invisible spaces, and the [wbr](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/wbr) tag.

A few notes on the web-safe faces themselves:

**Arial** is the most popular font in the Russian segment of the web — and the culprit behind the Cyrillic Л looking like П in so many faces. It's not that bad; there are a couple of flaws in a few Russian letters that 99.9% of the planet wouldn't notice at gunpoint. It has by now been replaced by Segoe UI.

**Verdana** was designed back in 1996 by the artist Matthew Carter. It was created from the start for output on small monitors, so it has the best legibility at small sizes. Recommended size/leading pairs are 11/14, 12/16, 13/18; the optimal size is 12px.

**Times New Roman** is a good font, created all the way back in 1931. Here the designer (especially the print designer) has to be careful: at very small or very large sizes it becomes hard to read, while at medium sizes it looks quite good. So using it well usually only works out for art directors. In 2004 the United States adopted it as the standard for its documents. If you want to step away from the formal style while keeping an English flavour, try [Kelyon](https://www.dafont.com/kelyon.font).

**Georgia** is a successful replacement for Times New Roman. It works well for evoking the mood of old business publications, is comfortable to read reversed-out (a rarity on the web), and is also a good example of a serif. A good alternative is [Garamond](https://www.fonts.com/font/adobe/adobe-garamond). macOS users will want other serifs, such as Iowan Old Style, Charter, or PT Serif.

**Comic Sans MS** is a decorative font — a fun comic-book face that's perfectly suitable for headings on a site aimed at schoolchildren or other specific audiences.

**Impact** is another decorative font, this time a grotesque. It's quite bold, so it looks poor at small sizes, and it isn't present on every macOS.

**Trebuchet MS** is a fairly good font, again a grotesque. It reads very well, especially with increased leading.

### Choosing a face for a specific job

For all the theory, the question that comes up most often is simply "what font should I use for X?" — so here are the workhorses by task.

For **narrow, condensed** type with Cyrillic, look at Impact, the legendary [DIN](https://www.paratype.ru/pstore/fonts/FF-DIN-Pro.htm) that art directors almost always reach for, or Roboto Condensed (not my favourite, but popular). Also Gotham, Reforma, my own favourite [Bebas](http://www.dafont.com/bebas.font), and Rufo; among the free ones, Open Sans Condensed; and as a last resort, Montserrat.

For **trendy wide, squished** faces, Druk Wide and RF Dewi; for inspiration in that style, dig into the work of Rodchenko and Stepanova.

For **free Cyrillic** fonts: first and foremost Roboto and Open Sans; then PT Sans, PT Mono, and PT Serif. If you want something more exotic, many paid fonts have a [free weight](http://www.myfonts.com/search/free%2Bcyrillic/), though some free weights carry commercial-use restrictions; beyond that, look at [Pobeda](http://mishapanfilov.ru/fonts/pobeda/font_pobeda.html), [Alice](https://fonts.google.com/specimen/Alice), and [Marmelad](https://fonts.google.com/specimen/Marmelad).

For a **complex web-app interface**, look at [Inter UI](https://rsms.me/inter/) — universal, scaling well offline, and matching Helvetica across all dimensions. For Cyrillic there are honestly few good web faces; the main ones are Roboto and Roboto Condensed, Roboto Slab, Open Sans and Open Sans Condensed, PT Sans, Proxima Nova, [Noto Serif](https://www.google.com/get/noto/) (which handles literally every language), Intro, Bebas Neue, MuseoSansCyrl, Ubuntu, GothamPro, Geometria Heavy, and Sero Pro. On a current project I'm using [Circe](https://www.paratype.ru/pstore/fonts/Circe.htm), which I like a lot, though it too is far from perfect.

For **infographics** — footnotes rather than headings — there are faces specially adapted for comfortable on-screen reading even at small sizes; search for them under the keyword [screensmart](https://www.typography.com/collections/screensmart/). For very small body text, take wider faces and reduce the size rather than reaching for condensed or narrow weights. It's also worth checking whether the font includes tabular figures, which sit more pleasantly in columns of numbers regardless of weight; search the keyword [charts tables](https://www.typography.com/collections/charts-tables/).

For **posters**, if you want a fresher alternative to Georgia, Garamond or Minion Pro will probably do; look at examples on [Fonts In Use](https://fontsinuse.com/) and run the standard [WhatTheFont](https://www.myfonts.com/WhatTheFont/) → MyFonts combination. Something a little more modern is [Cormorant](https://fonts.google.com/specimen/Cormorant) — not bold, with an easy-on-the-eye letterspaced small-cap set and oldstyle figures. And for **interior design** signage, Navigo, Permian, or Inter (if you can make peace with its Ц).

### Pairing, and a little code

On font pairing — it isn't necessarily two fonts. It can be two sizes at different weights. Or there are the classic pairs, like the geometric grotesque Futura plus the serif Garamond, which create contrast through form. A script plus a serif always looks good.

Fonts live a life of their own, so let's discuss a couple of code matters. In CSS, `font-variant-numeric` lets you add a little variety to your web typography, and you mustn't forget `text-overflow: clip | ellipsis`. Use `text-align: left` only. For hanging elements, `hanging-punctuation` will eventually become available. In many fonts you'll find Display and Text versions; the difference is that Display is used for headings above 20 pixels and Text for ordinary smaller text (easy to tell apart by the increased letter-spacing). I very much hope you know the difference between TrueType, OpenType, and PostScript fonts. If you don't, the main thing to know is that with TrueType fonts the correct reproduction on printing devices isn't guaranteed, and the print shop bears no responsibility for it. In the early days of the technology, fonts didn't display properly in WYSIWYG mode, and PostScript fonts were made specifically for the printer.

When the moment comes to pull *your own* font onto a website, the simplest route is to drop it into [Font Squirrel](https://www.fontsquirrel.com/), which hands you an archive of HTML pages with everything already hooked up. Alternatively, link the OTF file directly — that gets you all the subsets, oldstyle figures, and other characters, if they're built into the font; [browser support](http://caniuse.com/#search=otf) is fine too. To use superscripts and subscripts, for example, the CSS is:

```html
<sup>9</sup>  — superscript
<sub>9</sub>  — subscript
```

And keep an eye on memory footprint: a Latin character in UTF-8 weighs 1 byte, if it's below code point 127. If each weight lives in a separate file, you connect a different font per `font-weight` simply by sharing one family name across several `@font-face` rules:

```css
@font-face {
  font-family: 'Roboto';        /* one name */
  src: url('roboto-regular.woff2'); /* different files */
  font-weight: 400;             /* different density */
  font-style: normal;
}

@font-face {
  font-family: 'Roboto';        /* one name */
  src: url('roboto-semibold.woff2'); /* different files */
  font-weight: 600;             /* different density */
  font-style: normal;
}
```

On formats: list WOFF2 first (for the future), then WOFF as the most cross-browser, and TTF last (optional). It also makes sense to use `local` to check for a locally installed version of the font. There's a handy [reference table](https://discourse.wicg.io/t/more-font-weight-names/892) for `font-weight` names.

### Graphemes and glyphs

Here it's worth clearing up a common confusion: in an OTF font, are graphemes separate characters? A grapheme is what we usually call a letter, as a unit of text. A glyph is a unit of graphics, and it can represent the grapheme itself or only a part of it (for example various diacritics: accents, umlauts, the superscript double-dot on "ё," and so on). Graphemes are the most interesting thing in extended OpenType typography. The glyph and the numeric code of a character are separated: the character is ordered according to Unicode and is usually a letter, while the glyph is a graphical image, and a single character can have several glyphs — for instance a lowercase "щ" and a small-cap "щ." At the same time, one glyph can correspond to a combination of characters: the single grapheme "aff" corresponds to the sequence a, f, f, so the word "Affinity" is eight characters but the graphics processor hands back six glyphs. And don't forget ligatures: working for the domestic market, many designers attach little importance to them, but when you work with Eastern languages, where signs join and change form depending on their position in the word, the importance of ligatures is hard to overstate. (As an amusing edge case — if you ever see "type" that looks like letters drowning in stacked accents and tails, that's not a typewriter face but a Zalgo text generator, built precisely by piling combining diacritics onto a base character.)

### A word on tooling and special cases

To add your own character to a finished typeface — a ruble sign, say — use a font editor like Glyphs: open the font, add a new symbol, drop the ruble sign into it (Unicode 20BD), and either drag the vector form in from any vector editor or draw it in Glyphs directly. If you instead need to *swap* one font for another across a huge project — the credits of an After Effects project, for example — and the software's standard substitution doesn't suit you, there's a radical route: install FontForge, duplicate font 1, copy all the properties of font 2 from the project onto it, delete font 2, and save the duplicate of font 1 under the existing name of font 2.

There's also the perennial question of the ideal font for setting **mathematical formulae**. Formulae are a very international affair — sigma, for instance, is written in Western European culture as Σ, σ, ς, while in the CIS only two forms, Σ and σ, are accepted. For setting formulae, LaTeX gives the highest typographic quality. Out of TeX (LaTeX being a macro package) there are two PDF-export engines, DVIPS and PDFTeX: the latter generates PDF directly, while the former can tap PostScript's capabilities. MathType is also good for setting formulae, and pairs well with InDesign.

### When the text is subtitles

Subtitles deserve their own rules. From the obvious: mind the contrast of text against background — in games subtitles are often set on a semi-transparent black plate (Portal 2). Place them centred and low on the screen, with a margin from the bottom edge, or contextually next to the object for VR; ideally, 12.5% margins left and right and 10% from the bottom. Don't put too much text up at once: for HD screens, no more than 40 characters per line and no more than two lines. That also helps you choose the right size — Amazon's guidelines for TV UI elements prescribe 28px/14sp for 1080p, which is the minimum, with 46px recommended for HD and 26px for SD. There's a platform dependency: on small game screens reserve 4% of the screen height, on large game screens 6%, and on big televisions 8% — these being the rules for people with 20/20 vision. Reading one line of subtitles (per the above) takes an adult about 2.5 seconds and a child 3–4.5 seconds per line, with a 0.16-second gap between subtitles (4 frames at 25fps). On Xbox One, Android, and iOS you can switch subtitles on app-wide; it's important to keep an eye on UI elements and Quick Time Events so they don't cover the subtitles, which are added bottom-up. They can be coloured for different characters — favour light colours and go easy on red, which reads as dark brown to people with the most common forms of colour-vision deficiency; better to stick to white, yellow, cyan, and green. Ideally subtitles should be configurable, since some people only glance at them occasionally while others read every word, so give users control over size, colour, shadow/outline, the background plate and its opacity, and the level of detail (dialogue only, versus showing the rustle of trees and the bark of a dog). Minimum text size 46px. You can also use an arrow to indicate the direction a sound is coming from: "← John: Thanks!". On the choice of face, I'd take a sans-serif: subtitles should reflect the artistic flavour of the work, but not at the expense of legibility — and if the artistic side matters a great deal, push it into the settings (artistic / sans-serif / dyslexia-friendly). If the font has an italic, it's used for telephone conversations and TV broadcasts. As for which fonts: there's a dedicated face for [dyslexia](https://opendyslexic.org/); for a typewriter look, [Roboto Slab](https://fonts.google.com/specimen/Roboto+Slab) and [Warenhaus-Typenhebel](https://fontlibrary.org/en/font/warenhaus-typenhebel); for serif, [Seshat Regular](https://fontlibrary.org/en/font/seshat-regular) and [Noto Serif](https://fonts.google.com/specimen/Noto+Serif); for monospaced, [ChicagoFLF](https://fontlibrary.org/en/font/chicagoflf) and [Roboto Mono](https://fonts.google.com/specimen/Roboto+Mono); for sans-serif, [Aileron](https://fontlibrary.org/en/font/aileron) and [Roboto](https://fonts.google.com/specimen/Roboto); for handwritten, [Marius1](https://fontlibrary.org/en/font/marius1) and [Kalam](https://fonts.google.com/specimen/Kalam); for italic, [Promocyja](http://www.glukfonts.pl/font.php?font=Promocyja) and [Satisfy](https://fonts.google.com/specimen/Satisfy); for small caps, [Ferrum](https://fontlibrary.org/en/font/ferrum) and [Alegreya Sans SC](https://fonts.google.com/specimen/Alegreya+Sans+SC). And read the platform requirements: [BBC](http://bbc.github.io/subtitle-guidelines/), [Channel 4](http://www.channel4.com/media/documents/corporate/foi-docs/SG_FLP.pdf), [Netflix](https://partnerhelp.netflixstudios.com/hc/en-us/categories/202282037-SPECIFICATIONS-GUIDES), and [the FCC rules](https://www.ecfr.gov/cgi-bin/retrieveECFR?gp=&SID=9d2817fd6a79ea586556fba66f187ff2&mc=true&r=SECTION&n=se47.4.79_1102).

### Drawing your own type

If you've decided to draw a face, the formula 3/5 to 5/6 of the letter's height will help you find its width; the optimal main-stroke thickness for lowercase at a size of 10 is 0.30–0.35 mm, and the optimal counter width (taking the letter "н") is 4/5 of its height. As you can see, not everything in type is so relative. Contrast is necessary, at least if you want legibility, and short serifs also raise it. The hardest text to read is text set in capitals: identical height, mass, and width of letters impede reading. Serifs and grotesques are harder to read. The well-known idea that "the simpler the letterforms, the easier the text is to read" is wrong, because we read words, not letters — and the more the letters differ from one another, the easier the reading. An example of a very legible face is [Thesis](https://www.lucasfonts.com/fonts/the-sans/normal#hair).

Good web practice is to vary the ratio of type sizes by screen diagonal. Universal values are 1.2 for small screens and 1.4 for large ones. If you want to do it all by the golden ratio, the recommended value would be 1.17398 (one-third of the golden-ratio step) or 1.27202 (one-half). The golden ratio in its pure form would create an imbalance in the proportions.

You don't always need to play actively with weight, since bold letters stand out too much and grey ones sink. A contrasty face is the ratio of main-stroke thickness to connecting-stroke thickness. A font's weight is the ratio of stroke thickness to the height of an upright character; the main-stroke thickness can vary across a wide range, and in a normal weight it's roughly 1/7 of the lowercase letter. It's exactly this parameter that's responsible for classifying fonts as Extra Light/Thin, Light, Book/Regular, Demi Bold/Semi Bold, Bold, Extra Bold/Heavy/Black/Ultra. But all these degrees of weight were created not out of some perverse whim of the designer or client (as they are now, T_T), but to solve a specific task — always keep that in mind. What makes a font beautiful and legible is a competent drawing of the signs, harmonious proportions, and uniform weight.

The **en-dash** (–), the width of an "N," is appropriate between numbers (12–24). The **em-dash** (—) is used in direct speech and is not set off by spaces. The **hyphen** (-) goes in compound words like "красно-синий" (red-blue) or "юго-западный" (south-western). Often you have to use the hyphen and the minus as a single character.

## A word on licensing

When you hand the source of a logo, fonts and all, over to a client, you have to honour the font's licence terms. A font is essentially a program, which means the crux is the *distribution of the file*. Font stores usually state where the font may be used, and some fonts have a separate licence for logos and branding. You, as the designer, either buy the font for your own permanent use or use a trial version — but you never pass it on to anyone. When you deliver the design to the client, you convert the font to outlines; you don't hand over the font file. If you're making materials for games, the licence is only needed by the designer who sets text on the assets and exports it as a raster image.

The general rule: the font must be bought by both you and the client, each with their own licence. If the client gives you a font and you use it, the demand will fall on *you*, not just on the client. For solid bodies of text it's better to use free fonts (but not the Windows ones, which are far from free by licence), such as [PT Sans](https://fonts.google.com/specimen/PT+Sans) — because a large document with fonts fully converted to vector weighs far too much even by modern standards; alternatively, set the "preview & print" attribute instead of editable embedding. Problems can arise: OpenType is often compressed into Adobe's Compact Font Format, which discards unused characters and squeezes character addressing down to ANSI, moving Cyrillic characters to positions that aren't theirs. Free fonts are also good in that the [pairings](http://fontpair.co/) have already been worked out for them. I won't reprint everything that's on Google; if you're interested, read up [here](https://www.paratype.ru/help/license/) and [here](http://paratype.livejournal.com/27213.html?thread=317005#t317005), and I recommend services like [Rentafont](https://rentafont.ru/tariffs).

When a designer works *for a company*, they're most likely developing the font as a work-for-hire, meaning the rights to it will belong to the company; on paper, the exclusive rights to the software pass to the company. For that, the company properly executes its internal acts — a trade-secret policy, say — has employees read and sign them, and after the font is drawn the designer files a completion report, receives their compensation, and forgets about the fruits of their intellectual labour. The same applies when rights are acquired through an author's-commission or a work contract. Even if a font is free, you can still run into a viral licence (your software must also become free and open) — so check the licences on [TL;DR Legal](https://tldrlegal.com/). If we were talking about software development, the question would be framed as: do we give the end user raster fonts (an image) or the vector forms of the letters (.ttf, .otf)? I know for a fact that in the United States the *shape* of a letter, as an artistic component, is not protected by law as strongly as the .ttf *file* is as a piece of software; the set of curves that forms a unique image isn't licensable. That's why game engines can rasterize a font, and you only need to buy the licence for the development team.

Every studio has its own rules and licences for using fonts. Sometimes you can write to the font's authors directly and buy the right to use it from them — they earn more, you pay less, and you can ask them to add special characters or support for new languages. Corresponding with type companies and authors is useful in general, since the usage terms often don't make it clear that servers, commercial printers, and the like are involved. Or take a font under the OFL (Open Font License): you'll need to credit the authors, and you can then calmly use and modify it — an example is [Golos](https://www.paratype.ru/fonts/pt/golos). Some people use an SDF approach, which renders a raster font without including the OTF/TTF in the product. The main aggregator stores are [MyFonts](https://www.myfonts.com/), [Fontspring](https://www.fontspring.com/), and [Creative Market](https://creativemarket.com/fonts).

## The grid

"Design without a grid is unstirred semolina." The unity of the text is a very important parameter, no less important than the choice of a quality font and support for the Tatar and Udmurt languages. The compositor's task is to do everything possible to create a layout conducive to calm, focused reading. The reader shouldn't notice the design at all. Modular grids have existed for this purpose for a very long time. A modular grid is the foundation on which a standard layout scheme is built, one that matches the nature of the material placed on a given page. It's an enormous subject, impossible to cover within a single article or even a single textbook. The important thing to understand is that a grid is not just the alignment of elements to its left edge.

There's a concept called a "river": the vertical alignment of the spaces between words. It's a serious blunder, and it appears especially often in narrow columns, particularly because of word-breaks — so when working with narrow columns you have to watch for rivers with special care.

A line should hold no more than 65 characters, and ideally fit within 70–120 mm. A page should hold no more than 40 lines. In multi-line headings, every line should make sense on its own. There should be no blank lines between a heading and the paragraph that follows it. Between paragraphs there should be one blank line. It's desirable to achieve alignment of the baseline across all pages of the work (cross-alignment of the setting). The spacing between sentences should be equal.

The most common fonts for multi-page setting in Russia are [Charter](https://www.paratype.ru/pstore/fonts/ITC-Charter.htm), [Swift](https://www.paratype.ru/pstore/fonts/Swift-2-0.htm), [Franklin Gothic](https://www.paratype.ru/pstore/default.asp?fcode=PT_FRG&letter=F), [Minion](https://ru.fontzzz.com/font/13659_minion_pro.htm), and [Helios](https://www.paratype.ru/pstore/fonts/Helios.htm). They're handsome enough to sit well in the format of listings and explanatory text.

## Cyrillic versus Latin

There's a widespread opinion that the Latin alphabet is prettier and more modern than Cyrillic. It's true — the Latin is more harmonious. It evolved naturally out of handwriting; the Latin alphabet is older and more global, and is therefore better balanced. Cyrillic arose artificially 300 years ago, when Peter the Great commissioned an alphabet from the Dutch, in 1708. The main advantage of the Latin is the aesthetics of its letterforms, which is what he wanted to borrow. But compare the plasticity of G | Ж and D | Д — the plasticity didn't quite come off. The core problem is the absence of the many arcs that let adjacent characters sit pleasantly together. Cyrillic is itself wider than Latin, its words are longer, and its system of spacing differs.

On top of this lies the "bouba–kiki" effect. The human mind establishes associations between the sound shell of a word and the geometric form of an object, and the Latin script has a very well-developed synaesthesia of sound and form. The upshot is that Latin faces and the Latin alphabet are calibrated so that whatever you do with them turns out well, whereas with Cyrillic, whatever you do turns out badly. And yet Cyrillic must be worked with, and worked with well, with love. Anything at all can be done well — a typeface is, after all, a set of vertical sticks, diagonal sticks, and round elements, or rather, instruments of narration. For good examples, turn to the work of František Štorm, who makes excellent Cyrillic faces.

On these foundations one can wrap up this article and begin writing the next. The volume of information about typography is, of course, vast — but approach it in a structured way, and some of you will manage just fine.
