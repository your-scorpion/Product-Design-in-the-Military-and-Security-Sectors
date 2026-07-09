# Regular Expressions in a Designer's Practice: InDesign, JavaScript

*Max Tsvetkov · 28 December 2013*

Most of a designer's work involves text one way or another. Fussing over typography, building multi-column layouts, getting lost in ParaType's typefaces, sneaking Lobster into a banner when the colleagues aren't looking. But text has another, very important side to it — one that isn't aesthetic at all: parsing and globally editing large volumes of text.

Why would you need that? Say a designer is handed text data to drop into 40,000 business cards. It's a rare client who hands over clean, optimized text. And since the job still has to get done, you'll end up processing that data yourself. This is exactly where regular expressions come to the rescue. The core — and most primitive — job of a regular expression is smart text search. InDesign has supported regex since CS3, and InDesign scripts have supported it since CS2 through the Microsoft VBScript Regular Expressions library (`vbscript.dll`). This article covers PCRE syntax.

Regular expressions are built from two kinds of characters: metacharacters (the functional pieces you assemble your code from) and literals (ordinary characters). Strip away all the syntactic sugar and regex really does just three things: repeat a character, require that one character follow another, and branch on conditions.

## Syntax: the metacharacters

**`\`** turns special characters into ordinary ones and vice versa. For example, `/d/` simply looks for the letter d, while `/a\*/` looks for the literal `a*`. So `\\` is just a backslash, and `\\\\` is two backslashes — every pair collapses into one when it's converted to a literal character.

**`/`** marks the start and end of a regex literal in JS.

**`.`** any character except a line break (it also matches a literal period). `[A-Z]..` selects text starting with a capital letter plus two more arbitrary characters.

**`*`** the preceding character repeated zero or more times. `a*b*c*` matches `aabbbccc`; `/kia*/` finds `kiaaaaaaaaaaaaaaaa`.

**`+`** the preceding character repeated one or more times.

**`?`** an optional character — the preceding character repeated zero or one time. In `mo?ther` it won't matter whether the word contains the letter o; either way it's matched.

**`\d`** any digit. Equivalent to `[0-9]`.

**`\w`** any word character (letters, digits, and `_`). Equivalent to `[A-Za-z0-9_]`.

**`[]`** any one of the characters listed inside. `[d]` finds every d. `/[A-Z]/` matches all uppercase English letters. `/x[eaoy]n/` selects Xen, xan, xon, xyn. `[XYZ]+` is one or more of the listed characters. `[^xyz]` is any character *except* those in the set. `[Mm]a[ks]|[x]|[xs]im` will match any of the names maxsim, maksim, masim, Maxim, Maksim, maxim.

**`$`** end of input. `/a$/` matches the word *anna* but not *ann*. `/^[0-9]+\.00$/` checks whether the value ends in `.00`.

**`^`** start of input. `^man` is true if the string begins with the unbroken sequence m-a-n. Inside a character class, `[^a-z]` means a non-lowercase letter. `/[^b]log/` selects plog, dlog, flog — but not blog.

**`\s`** matches every whitespace character.

**`()`** grouping parentheses (subexpressions). `(\d+).(\d+)` captures data like `1024, 768`. `(file.*)\.pdf$` is a file name, then any number of characters, a period, and the extension. `(?:<img.*?\/?>)` finds an image tag in text.

**`|`** or. `I love (cats|dogs)` — one or the other.

**`{m,n}`** from m to n repetitions of the preceding character. An indispensable part of validating logins and passwords, as in `/^[\w_]{8,22}$/`.

**`\<` and `\>`** the position at the start and end of a word, respectively.

## A few base patterns I lean on

Here's the short reference I keep coming back to when writing expressions:

- `/abc/` — the characters abc in a row
- `/[abc]/` — one of the characters a, b, or c
- `/[^abc]/` — none of those characters: not a, not b, not c
- `/[a-z]/` — a range of characters consecutive in the Unicode table
- `/\b/` — a word boundary
- `/\B/` — not a word boundary
- `/\d/` — a digit
- `/\D/` — not a digit
- `/\w/` — a Latin letter, a digit, or `_`
- `/\W/` — not a Latin letter, digit, or `_`
- `/\s/` — a whitespace character
- `/\S/` — a non-whitespace character
- `/a{3}/` — exactly 3 a's in a row
- `/a{2,4}/` — 2 to 4 a's in a row
- `/a+/` — 1 or more a's in a row
- `/a*/` — 0 or more a's in a row
- `/a?/` — 0 or 1 a
- `/./` — any single character except a line break

## Flags

There are some extra options — or, more precisely, flags. I personally know them from the MEL language, where they're used heavily. **`g`** is a global search and forces the expression to run across all the content; **`i`** lets you forget about case; **`m`** is a multi-line search. If text you type into a search matches none of the metacharacters, it's treated as ordinary text. You'll rarely need `m`, while `i` and `g` come up quite often.

Regex even works in PowerShell on Windows. Here, for instance, is a search of the C drive for every file that mentions mp4:

```powershell
Get-ChildItem C:\ -Recurse | Select-String -Pattern '\.mp4$'
```

## Quantifiers and grouping

Quantifiers let you specify how many times the character to their left may repeat:

- `*` — zero or more times
- `+` — one or more times
- `?` — zero or one time
- `{x}` — exactly x times
- `{x,y}` — from x to y times

Then there's grouping: whatever sits inside parentheses is a subexpression, and subexpressions can be referenced by index. Thanks to the `|` delimiter you can build logical constructs, choosing one expression or another.

Here, for example, is a pattern for a phone number:

```
(\+7|8)[-\s(]*?(\d{3})[-\s)]*?(\d{3})[\s-]?(\d{2})[\s-]?(\d{2})
```

And here's a regex for a vehicle license plate:

```
(\p{L}\p{N}{3}\p{L}{2}|\p{L}{2}\p{N}{3}\p{L})(\p{N}{2}|\p{N}{3})$
```

![The phone-number regular expression rendered as a finite-state machine](https://your-scorpion.ru/wp-content/uploads/2019/02/Screen-Shot-2019-02-20-at-3.29.04-PM.png)

*The phone-number regular expression, converted into a "state machine."*

## "That's all lovely, but I need a ready-made solution!"

And you'd be right to say so — regular expressions are hard to read, hard to write, and hard to maintain. But they're often the only way to pull off complex manipulations on text. In InDesign you can use them through *Edit → Find/Change* on the **GREP** tab. It's simple: type your regular expression in the top field, put `$0` in the bottom field (that's a reference to everything matched), set the options you need in the change settings, and — profit.

![The GREP tab in InDesign's Find/Change dialog](https://your-scorpion.ru/wp-content/uploads/2018/12/1.png)

Here's an example with a more elaborate structure:

![A more complex GREP find/change pattern](https://your-scorpion.ru/wp-content/uploads/2018/12/pass.png)

Need a URL? Just drop in the matching pattern:

```
/^(https?:\/\/)?([\da-z.-]+).([a-z.]{2,6})([\/\w .-])\/?$/
```

An email address? Here you go:

```
/^([a-z0-9_.-]+)@([a-z0-9_.-]+).([a-z.]{2,6})$/
```

## Controlling text with the right kind of space

Finding text is only half of a designer's text work in InDesign. The other half is controlling how the text *behaves* — and the finest-grained tool for that isn't a search at all. It's the space between words.

The classic problem: keeping a short word — a preposition, a conjunction, a pronoun — from being stranded at the end of a line, dangling on its own while the word it belongs with sits on the next line. The fix is a **non-breaking space**, which glues the two words together so they wrap as a unit. It's easy to type: `option + shift + space`, or `alt + 0160`.

But the ordinary space and the non-breaking space are only the beginning. There are many spaces, because a single keyboard space isn't enough to control how text looks and behaves. Take a standard mistake plenty of art directors make: they assume an em dash doesn't need spaces around it. In fact it's set off with very narrow — or zero-width — spaces, and that's what lets words break onto the next line cleanly. Even the familiar note to "tighten this by a hair" isn't some abstract sense of beauty; it refers to a very specific glyph, the Hair space.

For fixing stranded lines you reach for non-breaking spaces specifically, and there are three of them: `Narrow no-break space`, `No-break space`, and `Figure space`. You can pull any of these straight from Unicode, or just copy them from the list below:

| Space | Code point | Width / use |
| --- | --- | --- |
| Hair space | `\u200A` | a hairline |
| Six-per-em space | `\u2006` | 1/6 em |
| Thin space | `\u2009` | about 1/6 em |
| Normal space | `\u0020` | the keyboard space |
| Four-per-em space | `\u2005` | 1/4 em |
| Mathematical space | `\u205F` | 4/18 em, for formulas |
| Punctuation space | `\u2008` | the width of a period |
| Three-per-em space | `\u2004` | 1/3 em, a normal space |
| En space | `\u2002` | half an em |
| Ideographic space | `\u3000` | for East Asian languages |
| Em space | `\u2003` | one em wide |
| Narrow no-break space | `\u202F` | about 1/5 em |
| No-break space | `\u00A0` | the width of a normal space |
| Figure space | `\u2007` | the width of a digit |
| Zero-width space | `\u200B` | no width at all |

That last one is especially interesting precisely because it has no width: drop a zero-width space in and you can force words separated by a slash to break onto a new line.

And here's where this loops back to everything above — because each of these has a Unicode code point, you can hunt them down or insert them with the very same GREP and JavaScript regex we've been working with (searching for `\u00A0`, replacing a plain space with `\u202F`, and so on). [Unicode](https://unicode-table.com/en/) has a great deal more worth exploring. A real [lot](https://en.wikipedia.org/wiki/Unicode_block) more.

## Regex in JavaScript

Regex isn't confined to InDesign, of course. Different programming languages speak different regex dialects: Perl and PHP allow recursive expressions, and flags don't behave identically everywhere either — so always check the documentation. Let's look at regex in JavaScript. The most primitive example is `console.log(/java/.test(str));` — just copy it into your browser console. The answer comes back as `false` or `true`, depending on what the variable `str` contains.

Declaring the expression:

```javascript
const myrg = new RegExp('car')
const myrg = /car/
```

The methods worth knowing:

- **`test`** — checks whether there are any matches in the given string;
- **`exec`** — searches for matches in a string and returns an array of data;
- **`search`** — tests a string for a match;
- **`match`** — finds matches and returns an array of data;
- **`replace`** — finds matches and replaces them with something;
- **`split`** — breaks a string into an array of substrings.

Let's try it on something alive. We'll put together a little script that strips out the word *cat*:

```javascript
var reg = /\b(?!cat\b)\w+/g;
var strin = "my cat, you cat, world cat!";
myArray = strin.match(reg);
document.write(myArray);
```

JS and RegExp syntax overlap in their use of these characters: `\ / [ ] ( ) { } ? + * | . ^ $`. If you want to match them literally in a pattern, you have to escape them with a backslash.

The `search` method, illustrated:

```javascript
let str = "some text is always nice to have";
let regExp = new RegExp('nice');
console.log(str.search(regExp));
```

This outputs the number 20 — the index where the match occurs in the string. If we add flags here — `let regExp = new RegExp('nice', 'igm');` — it makes no difference to the result. Even with `g`, we don't get an array of indices, because `search` always returns only the index of the first match. The next example:

```javascript
let str = "some text is always nice to have";
console.log(str.match(/to/));
```

If there are no matches, it returns `null`. If there are, we get an array — and with `match` you can count the number of occurrences. For replacement, use this construct: `console.log("+971-532-122-111".replaceAll('-','_'));`.

If you need to swap words around, here's one way to do it:

```javascript
let name = "Katia, Aliana";
console.log(name.replace(/([a-z]+), ([a-z]+)/i, '$2 $1'));
```

Another important part of regex is quantifiers. In their shorthand forms:

- `+` — an unlimited number of occurrences, starting from 1
- `*` — from zero to infinity
- `?` — either nothing, or exactly 1

So if you want to know whether a particular word sits at the end of a string, there's a tidy quantifier-based solution: `console.log('cat is a cat'.match(/cat$/i));`

## And the best part

Regular expressions also work in Google Analytics, Google Tag Manager, and Yandex Metrica — when configuring a goal, when setting up an advanced filter in a report, when building custom segments, when creating filters in a view, and even when using filters in queries against the Google Analytics API.

![Regex used in an analytics filter configuration](https://your-scorpion.ru/wp-content/uploads/2013/12/dda.png)

All told, what we've got is a pretty decent working-class programming language living inside a programming language — one that will free up a heap of your time so you can spend it polishing your skills. More automation = more free time to polish your automation skills!
