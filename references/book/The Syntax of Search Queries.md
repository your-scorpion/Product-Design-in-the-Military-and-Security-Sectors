# The Syntax of Search Queries

*Maksim Tcvetkov · 4 April 2015*

Searching the internet effectively means using a range of command operators — search queries that set precise conditions on what you're looking for. Only then do you get a genuinely short list of documents that actually match your query, instead of a million pages of results. Since research is one of the core ways we learn, work, and make decisions, it's hard to overstate how valuable it is to search as effectively as possible.

You can't build especially elaborate queries, since Google caps input at 32 words. Plenty of operators work in both Google and Yandex — though not all of them.

**OR** — logical "OR," letting you search for several word or phrase variants at once. Example: design OR your-scorpion

**before:** — add `before:2014` and get results from before that year.

**+** — forces a word to be included exactly as written, marked with a plus sign in front of it: working +president

**—** — the minus sign excludes a word from your results: construction -renovation

**[design graphics mime:doc]** — surfaces documents in `.doc` format containing both "design" and "graphics"

**""** — quotation marks like these find only the exact set of words placed inside them: "what a beautiful sunset." If you want to check whether someone's plagiarized your article, drop a chunk of your own text into quotes and search for it directly.

**~** — searches not just the word itself, but its synonyms too

**anchorint:** — I use this to find every internal page on a site linking to a specific page through a specific keyword phrase. `anchorint:"unity3d" site:http://www.render.ru`

**\*** — substitutes one word for another: "rainbow\*elephant." Roughly speaking, this controls how many different words can sit between your two search terms.

**..** — searches for a numeric range, "from–to": founding year 1950..1960; buy a book $100..$150; population 1913..1935

**[!deadline]** — finds documents containing the word "deadline" exactly as capitalized. It's worth understanding here that [usability basics] counts as a less precise query than [!usability !basics].

**filetype:** — lets you specify the file type to search within for a word or phrase: file type filetype:pdf

**site:** — everyone knows this one, searching within a specific site. Worth noting there shouldn't be a space between the colon and the address. Correct usage looks like this: `site:boards.greenhouse.io united states intext:"apply" (intext:"Product Designer" OR intext:"UX Designer")`

**movie:** — searches for information about a film: "movie:little red riding hood"

**source** — searches for news from specified sources.

**allinurl:** — shows you a list of pages that have your search terms somewhere in the URL itself: allinurl:joke pravda

**allintext:** — searches only within the document's text, ignoring titles, addresses, and everything else.

**define:** — looks up a word's definition, like a dictionary: define:archivist

**object** — searches by the contents of an `object` tag's attribute: object:align center

**applet** — searches by the value of the `code` attribute: applet:button.class

**related:** — my favorite function. Finds pages with a similar topic to a given URL: `related:www.rg.ru`. You can get clever here too — a URL can be represented in punycode, or with Cyrillic characters standing in for Latin ones, which is a potential homograph attack, and most browsers will warn you when that happens. It's possible to disguise a domain this way for hosts a user already trusts.

**cache:** — my second-favorite function. Shows the most recent cached version of a page from Google.

**link:** — shows pages linking to a given address: link:www.google.com

A browser query like `google.com/#q=filetype:pdf+site:2gis.ru` finds every PDF file on the 2gis site. Or a query like `google.com/search?#q=inurl:"ViewerFrame?Mode="` finds exposed web cameras — this particular dork has been public knowledge for well over a decade, and it's a genuinely good illustration of how much power `inurl:` carries. One related trick, useful either for avoiding this kind of thing or for expanding your own search toolkit, is the special character for right-to-left text. In other words, a file extension like `.ps1` can be written in reversed form: `filename1sp.pdf`. To a computer, that's still a PowerShell script underneath. This particular bit of magic runs on a dedicated Unicode character, U+202E, which forces text to render right-to-left: `filename‮pdf`.

Understanding how Unicode actually works makes your search queries considerably more effective. Unicode covers every language in the world, emoji included. A byte pattern like `10111011` gets stored in memory and rendered in a query as `bb`. ASCII, by contrast, is a subtype that can only store a character in 7 bits at most. UTF-8 is also a subtype, not a full implementation of Unicode in itself. And as data travels across a network, bytes can shift and change their semantic meaning along the way. Send a laptop emoji (U+1F4BB), for instance, and in machine terms that's `f0 9f 92 bb` — which doesn't fit inside 7-bit ASCII at all. All of these quirks get compensated for through the NFD, NFC, NFKD, and NFKC normalization forms.

Yandex carries a few more oddities of its own, though it's exposed the most popular queries directly through its [interface](http://yandex.ru/search/advanced?&lr=213). Now, suppose we're SEO professionals and want to put all this knowledge to financial use (no point pretending otherwise — people get into SEO purely for the money). Head to [wordstat.yandex.ru](https://wordstat.yandex.ru) and put every one of the refinement tricks above to work. Enter your key phrase in quotation marks, for instance, and the service returns the exact number of searches for that specific phrase. Or type in "!design !mobile !apps," and you get statistics on exactly how many people searched for mobile app design specifically. The result is a genuinely high-quality semantic core of search queries.

A related, natural next step once you have that core: building an anchor-text list for link building. I've never worked from the principle of "let's just go buy a bunch of links" — that never worked back when I started out, and it won't work now either. Building a proper anchor list means first building out your link profile. Head back into Yandex's ["keyword selection" tool](https://wordstat.yandex.ru), enter your keywords, and pull the resulting search queries. Use those same queries in Yandex and Google to find genuinely strong, relevant commercial sites. From there, tools like Ahrefs, LinkPad, and Rankinity let you examine those sites' own backlinks, which is how you build your list of potential donor sites. Then it's simply a matter of reaching out to webmasters or SEO specialists and negotiating link placement.

It isn't just Google and Yandex that built a dedicated search-query language for their own services, either — plenty of large portals have their own version of the same basic refinement operators. Good recruiters, for instance, have real mastery of HH.ru's own fairly [limited](http://hh.ru/article/1175#slovosoh-search) search-query language.

Using search operators too aggressively can trigger a 503 error (Service Temporarily Unavailable) — that's the search engine protecting itself against what looks like a DDoS. And don't forget the more technical tools out there too, like Sublist3r.

A related, practical extension worth folding in here, since it's really just automation layered on top of the `filetype:` and `site:` operators already covered above: pulling structured data — page titles and meta descriptions — out of a large batch of URLs at once is easy to automate in Google Sheets. The formula for a title looks like this:

```
=IMPORTXML(D6,"//title")
```

And for a meta description:

```
=IMPORTXML(D8,"//meta[@name='description']/@content")
```

![Using IMPORTXML to audit page titles and descriptions](https://your-scorpion.ru/wp-content/uploads/2018/02/heFTY.png)

I use this to check which pages are missing important tags. To apply it to a genuinely large site, list every URL down the left-hand column (Netpeak Spider is a good source for that list), write the formula in the column next to it, and drag it down. For a batch of 1,000 URLs, though, expect to wait a while, and don't be surprised if it doesn't fully complete.

The same `IMPORTXML` approach can pull images into spreadsheet cells too, using a formula like:

```
=ARRAYFORMULA(IMAGE(IMPORTXML(HYPERLINK("https://yandex.ru/images/search?text=normal%20distribution"),"//img/@src"),1))
```

![Pulling image results directly into spreadsheet cells](https://your-scorpion.ru/wp-content/uploads/2019/04/Screen-Shot-2019-04-03-at-10.40.png)

And on the `filetype:` operator specifically — it's worth knowing what exists beyond it for searching across every file type at once, rather than the content on a page. [Metagoofil](https://github.com/opsdisk/metagoofil) is a solid tool for exactly this: `metagoofil -d example.com -t pdf,doc,ppt -l 50 -n 10 -o /tmp/metadata -f report.html` searches by file type, downloads 10 of the results into a metadata folder, and generates a report with the extracted metadata. As an alternative, `theHarvester -d site.example -b otx` or `theHarvester -d example.com -b urlscan,crtsh,rapiddns -v`, depending on what you're after — you specify which search engines and data sources to pull from, since this is really just automation on top of interacting with those same search systems (a source like [urlscan.io](http://urlscan.io) can just as easily be used on its own through its own web interface). As a starting point for research, though, a command like `theHarvester -d example.com -b rapiddns,crtsh,urlscan,otx -l 1000 -v` gives you a genuinely useful sense of what's out there.

Or, staying within Google itself, a query like: `site:example.com (filetype:pdf OR filetype:doc*)`
