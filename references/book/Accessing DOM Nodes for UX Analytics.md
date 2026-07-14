

# Accessing DOM Nodes for UX Analytics

*Maksim Tcvetkov · Product Design and Security Lead*


UX designers run into the same situation again and again: there's no manager, no graphic designer, no backend dev, no frontend dev available to spin up a test version of the site — and the hypothesis still needs to be checked. In that case, you have to edit the live web service yourself. JavaScript is how you get access to a page and control its content. With JS, you can pull whatever information you need out of the HTML and modify it for an A/B test or a prototype. Although this article is nominally about working with HTML nodes, it's really about working with the DOM in general. JS lets you do more than just change the behaviour of controls — you can also extract a surprising amount of useful data from input fields, which is exactly what makes them the hardest things to test, because users can type almost anything into them.

The DOM is an API for HTML and XML pages, written in C++, that provides the structure and methods you need to work with elements. Every element on the page is a node, and the contents of a node are themselves child nodes. There are *element nodes* — tags like `body`, `head`, `html` — and *text nodes* containing the content inside those tags, which can't have children of their own. Code comments and the entry point into the DOM live separately in the tree.

![DOM tree](https://your-scorpion.ru/wp-content/uploads/2026/03/Layer-1-copy-2.png)
*The DOM is conventionally drawn as a tree, with child nodes branching off from a parent.*

JS lets you play with the DOM as a whole. To walk up to an element's parents:

```js
var allContent = document.querySelector('div');
allContent.parentElement.parentElement;
```

Execution moves up the hierarchy starting from the element held in the variable. For children, use `allContent.children`.

`allContent.childNodes` is worth experimenting with — it makes it visible that text, spaces, even line breaks count as nodes in the DOM. Text nodes and element nodes are distinct types, as shown in the diagram above. You can also reach a specific child with `allContent.children[0]`, and count them with `allContent.childElementCount`.

## DOM, Virtual DOM, BOM, Shadow DOM

A browser is made up of the DOM (or Virtual DOM) and the BOM. The DOM is the document — all the `body`, `div`, `span`, and so on. A tree of objects. All of its properties are documented at [w3.org](https://www.w3.org/DOM/DOMTR). The BOM is a set of objects for working with things that are independent of page content. JS drives both.

The Virtual DOM is built in memory by libraries like React — also a tree of objects. React constructs a virtual DOM in memory, and the browser renders the actual DOM from it. Any change to the virtual DOM kicks off a diff between the old and new versions; if there are differences, only those specific spots get patched in the browser DOM. The result is fewer hitches when working with animations and dynamic components. The Shadow DOM standard lets you combine several trees into one, which makes the real DOM easier to manage. The more correct pattern, though, is the Virtual DOM, where you edit lightweight copies of the real DOM tree — you don't update the whole tree, you only reconcile the parts that need it. This isn't just a web-development trick; it's a general programming concept that ReactDOM happens to implement for React.

The BOM is easier to demonstrate. If we break down the URL `https://your-scorpion.ru/portfolio#about`:

- `location.href` returns the full URL
- `location.hostname` returns just `your-scorpion.ru`
- `location.pathname` returns `/portfolio/`
- `location.hash` returns `#about`

## Events

When you're testing, you need to react to user actions. That's what events are for — signals from the browser that the user did something. There are three ways to attach a handler:

1. The HTML attribute: `onclick="..."`
2. The DOM property: `elem.onclick = function`
3. The method: `elem.addEventListener(event, handler[, phase])`

The events you'll reach for most:

- `click` — left mouse-button click on the element
- `contextmenu` — right mouse-button click
- `mouseover` — pointer enters the element
- `mousedown` / `mouseup` — mouse button pressed / released
- `mousemove` — any pointer movement
- `submit` — form submitted (on `<form>`)
- `focus` — user focuses an element, e.g. tabs into an `<input>`
- `keydown` / `keyup` — key pressed / released
- `DOMContentLoaded` — the HTML has been loaded and parsed, the DOM tree is fully built and available
- `transitionend` — a CSS animation has finished

Not an exhaustive list, but these are the ones UX designers and product designers actually need when working through control states.

### Handlers

A handler can be put right in the markup, in an `on<event>` attribute. Because the attribute itself sits inside double quotes, you use single quotes inside `onclick`. Writing handlers in the markup is not best practice — usually you only do it for quick, throwaway tests. The better pattern is to write a named function and call it from the handler.

```html
<script>
  function countRabbits() {
    for (var i = 1; i <= 3; i++) {
      alert('Rabbit number ' + i);
    }
  }
</script>

<input type="button" value="Count" onclick="countRabbits()">
```

An anonymous function would fit nicely here, as long as you don't mind passing the function's name later when you need to remove the handler.

You can also attach the handler to a DOM element. It looks like `on<event>`:

```html
<input id="elem" type="button" value="Click me" />

<script>
  elem.onclick = function () {
    alert('Thanks');
  };
</script>
```

The downside of this approach: **the `onclick` DOM property is a single slot — you can't attach more than one handler.** Coming from a Flash background, I much prefer `addEventListener` and `removeEventListener`, which are the right way to attach and detach handlers and let you stack as many of them as you like:

```js
element.addEventListener(event, handler[, phase]);
```

`event` is the event name. `handler` is the function reference. `phase` is optional — it controls *where* in the event flow the handler fires (capture vs. bubble).

User actions aren't the only things that can trigger a handler. For instance:

```js
document.addEventListener(
  'DOMContentLoaded',
  console.log("Fired"),
  { once: true }
);

// or simpler
document.addEventListener("DOMContentLoaded", ready);
```

`"Fired"` will be printed to the console once the browser has finished loading the HTML and built the DOM tree. Note that the `load` event on the window fires *later* than `DOMContentLoaded` — it waits for all content and scripts to finish loading. In the second variant, we just call the `ready` function by name.

So you've got two options:

```js
document.getElementById('id0').onclick = function () {
  alert('Only one handler ever');
};

document.getElementById('id1').addEventListener('click', function (event) {
  alert('As many handlers as you want');
});
```

A pattern that comes up constantly in UX work is tracking *which* item in a list the user clicked, and acting only on that item. The clean way is to bind a single handler to the parent, then use the index of the clicked child to do whatever you need — log it, toggle a class, mark it as the selection. Building a to-do-style list from a text field is a small but representative example:

```js
var btnAdd = document.getElementById('btnStyleSend');
var ul     = document.getElementById('list');

btnAdd.addEventListener('click', function () {
  if (document.getElementById('youtTask').value === '') {
    alert('Type a task into the text field');
  } else {
    addNewLi();
  }
});

function addNewLi() {
  var value = document.getElementById('youtTask').value;
  var li = document.createElement('li');
  li.appendChild(document.createTextNode(value));
  ul.appendChild(li);
  li.className = 'myClass_style2';
  document.getElementById('youtTask').value = '';
  ul.addEventListener('click', clickerFn);
}

function clickerFn() {
  for (var i = 0, len = ul.children.length; i < len; i++) {
    (function (index) {
      ul.children[i].onclick = function () {
        console.log('clicked item index: ' + index);
        var item = document.getElementsByTagName('li')[index];
        if (item.classList.contains('myClass_style2')) {
          item.setAttribute('class', 'myClass_style');
        } else {
          item.setAttribute('class', 'myClass_style2');
        }
      };
    })(i);
  }
}
```

Another handy console pattern for finding DOM elements is XPath: `//div[text()="1"]` finds every div whose text is exactly "1". This is what automated testers lean on. Variations: `//div[contains(text(), "1")]` finds any div containing "1"; `//div[@class="wo"]//a/@href` returns the href of every link inside divs of class `wo`.

## Selecting by name, id, class, tag

Start with the `document` object — it has properties and methods for reaching elements. The simplest and most common is **`getElementById`**, which returns the element with a given id. Say you want the HTML inside the element with id `masthead`:

```js
var dataCollection = document.getElementById("masthead").innerHTML;
```

This works perfectly as long as every id in the document is unique. With duplicates, behaviour is undefined. `getElementById` also lets you change inline `style`.

Naturally, any script that touches DOM elements must run *after* those elements have been created. You can ensure this by putting the `<script>` after `<body>`, using `onload`, or using event listeners — but with `onload` the script only runs once the entire page (including every asset) has finished loading. `DOMContentLoaded` runs as soon as the HTML is loaded. The cleanest approach is one of two script-tag attributes: `async` or `defer`. `async` tells the parser not to pause when it hits the script — the page keeps rendering. With many scripts, though, their load order can shuffle depending on file size. `defer` is `DOMContentLoaded`'s cousin: the browser delays execution until the page is fully parsed.

A couple of footnotes worth keeping in mind. CSS properties written with hyphens (`border-right-padding`) are camelCased in JS and the DOM (`borderRightPadding`). And: if a variable `myMug` holds a reference to a DOM object and you remove the object, it hangs around in the DOM, just invisible on the page. To actually free it, you need to null the reference — the garbage collector won't touch it while a variable is still pointing at it.

---

**`getElementsByName`** returns a collection of elements with a given `name`. Only works for elements that explicitly support `name` — `form`, `input`, `a`, `select`, `textarea`, and so on. Won't work on `div` or `p`. You can use `.length` and `.item()` like with any collection. Rarely needed, but useful when you want to fill in fields and submit:

```js
var elems = document.getElementsByName('description');
document.getElementsByName('login')[0].value    = 'login';
document.getElementsByName('password')[0].value = 'password';
document.getElementsByName('goOff')[0].form.submit();
```

---

**`getElementsByClassName`** does the same thing as `getElementById` but matches on the class attribute. Returns an `HTMLCollection`.

```html
<div class="article">Article</div>
<div class="long article">Long article</div>

<script>
  var articles = document.getElementsByClassName('article');
  alert(articles.length); // 2 — both matched
</script>
```

Another example:

```js
function com(flight, color = 'red') {
  const row = document.createElement('tr');
  const flightCol = document.createElement('td');
  flightCol.innerText = 'ada';
  flightCol.style.color = color;
  row.appendChild(flightCol);

  const parent = document.getElementsByClassName('w3-example');
  parent[0].appendChild(row);
}

const displayAll = function (...flights) {
  flights.forEach(f => com(f.flight, f.color));
};

displayAll(
  { flight: 'a', color: 'orange' },
  { flight: 'a', color: 'orange' },
  { flight: 'a', color: 'orange' }
);
```

You can read all of an element's classes as a string with `elem.className`, but there's a problem: if you only want to drop part of the class list, you have to redefine the whole thing, which means knowing what was there beforehand — or doing string `replace`.

Use `classList` instead:

- `elem.classList.add('true')` — adds a class
- `elem.classList.remove('true')` — removes one
- `elem.classList.contains('true')` — returns true/false
- `elem.classList.toggle('true')` — adds it if missing, removes it if present

That last one saves you from writing tedious `if/else` branches. Useful.

---

**`querySelectorAll`** is more interesting and bails you out in the messier cases. It gives you DOM nodes via CSS selectors — the fastest path, compared to XPath. It returns a `NodeList`, which is more of a collection than an array. If you want to surprise your colleagues, write `querySelectorAll.forEach` — it works in [some](https://developer.mozilla.org/en-US/docs/Web/API/NodeList/forEach) browsers. The example below grabs all the inputs and converts them to a real array. Works in Safari ≥ 5.1. In the console I use the shorthand `$$('li.group')`, but it isn't available everywhere.

```js
var listDomeArray = document.querySelectorAll('input');
listDomeArray.forEach(function () { /* … */ });
Array.prototype.forEach.call(domList, function () { /* … */ });
```

In CSS you can stack selectors; in JS there's only one selector argument. `document.querySelector('body')` gives you the whole `body`. Assign it to a variable and you get all the JS properties and methods that come with it:

```js
var dropPage = document.querySelector('body');
dropPage.baseURI;
```

Adding a tooltip is similarly trivial — read the attribute with `getAttribute`, set one with `setAttribute`:

```js
var elem = document.querySelector('div');
elem.getAttribute('class');
elem.setAttribute('title', 'tooltip text');
```

And here's the example that matters most for A/B testing: changing CSS properties of an on-page object.

```js
var elem = document.querySelector('h1');
elem.style.color = 'red';
```

That recolours the `h1`. Position changes use `top`, `right`, `position`, and friends. If you need an element's rendered size:

```js
var allContent = document.querySelector('body');
allContent.offsetWidth;
```

That's content width, and the result may differ from `allContent.clientWidth`, which excludes the scrollbar. Properties starting with `offset` return the size of visible content including padding, borders, and scrollbar. `client` returns it without borders and scrollbar. `scroll` returns the full content size minus scrollbar. The same applies to height: `offsetHeight`, `clientHeight`. Numbers will differ across browsers and operating systems. For the full page height including off-screen content, use `allContent.scrollHeight`. Now you know how to measure your landing page properly.

If you want to grab every DOM element on the page whose text content matches a string:

```js
Array.from(document.querySelectorAll('*'))
  .filter(item => item.textContent === '设计师主导');
```

---

**`document.getElementsByTagName`** finds every element with the given tag inside whatever element you scope it to. Returns a node list, behaves a lot like an array, and you index into it the same way. Pass `*` and you get the whole document.

```js
var myTime = document.getElementsByTagName('*'); // every element on the page
```

The line above pulls everything and wraps it in a node list. To reach each node you need a loop. In practice `querySelectorAll` and `getElementsByTagName` are interchangeable. Grabbing every element on a dynamically-updating page isn't as scary as it sounds — what you get back isn't an array, so it stays live.

```js
var dataMy = document.getElementsByTagName('div');
for (var i = 0; i < dataMy.length; i++) {
  // do something
}
```

The array-like things you get back — `HTMLCollection` and `NodeList` — differ from real arrays in one way: they inherit from `Object.prototype`, not `Array.prototype`. So `forEach`, `push`, `map`, `filter`, `slice` aren't available. Convert with:

```js
var almostArray = {
  0: 'myItem1',
  1: 'myItem2',
  2: 'myItem3',
  length: 3
};

var fullyArray = Array.prototype.slice.call(almostArray);
fullyArray = [].slice.call(almostArray);
fullyArray.indexOf('myItem1');
```

To check whether the conversion worked — or just to ask whether something is an array — use `Array.isArray(dataS)`. The `instanceof` variant below works too, but breaks for arrays created inside a frame, because they inherit a different prototype:

```js
var whatItIs = [];
var itWillFalse = '';
alert(whatItIs instanceof Array);
alert(itWillFalse instanceof Array);
```

If you want to attach handlers to every element with a given class:

```js
var elements = document.querySelectorAll('.user');
var handler = function (e) { console.log('your code'); };

for (let btn of elements) {
  btn.addEventListener('click', handler);
}
```

## Reading and writing attributes

Reading elements isn't enough most of the time — you also need to read and change their attributes. **`getAttribute`** takes one argument: the attribute name. If the attribute isn't there, you get back `null` or an empty string.

```js
var homeCont = document.getElementById('home');
alert(homeCont.getAttribute('src'));
```

To change an attribute, **`setAttribute`** — fast and simple:

```js
var item = document.getElementById('afisha');
item.setAttribute('class', 'democlass');
```

You can swap the `href` on a `<link>` to load a different `.css` file. `getAttribute` and `setAttribute` are also nice because they work in older browsers.

**`innerHTML`** lets you read and write the markup and text inside an element. You can wipe the entire `<body>`, or pull the live source of a page that's been mutated client-side. Great for testing. For the trivial case of inserting plain text I prefer `node.textContent` — fewer security gotchas.

```js
var list = document.getElementById('header');
for (var i = 1; i < 5000; i++) {
  list.innerHTML += 'item'; // update 5000 times
}
```

`innerHTML` comes up a lot in this article. It isn't standardised, but it works almost everywhere, and it's usually faster than equivalent DOM operations. It can also pull out everything inside an element and clear it:

```js
swillovPage = document.getElementsByTagName('body')[0];
swillovPage.innerHTML = swillovPage.innerHTML;
swillovPage.innerHTML = '';
```

If you stuff malformed markup into `innerHTML` — say `elem.innerHTML += '</div>'` — the browser can't parse it and the result is unpredictable. For simple things like changing the document title, just use `document.title = 'New title'` instead of bigger scripts.

## Creating elements

**`createElement`** creates a new element node. One argument: the element to create. It doesn't appear on the page yet, but JS knows about it.

You might wonder whether this is just `innerHTML` in disguise. It's not — it's both better suited to adding elements and significantly faster. `innerHTML` removes every child, parses the string you handed it, and appends the result. `createElement` skips all of that.

```js
var newDiv = document.createElement('div');
```

Give the empty element some content:

```js
littleOne.innerHTML = 'cats';
```

Add a class with `littleOne.classList.add('ac-gn-item')`. Drop it on the page:

```js
var content = document.querySelector('body');
content.appendChild(littleOne);
```

That appends to the end of the page, which is rarely what you want. To insert at a specific spot, use `insertBefore` — two parameters, the element to insert and the reference element it should go before:

```js
content.insertBefore(littleOne, content.children[1]);
```

Replacing is easy:

```js
var newContent = document.createElement('span');
newContent.innerHTML = 'Something went wrong';
newContent.style.borderBottom = '2px dotted white';
content.replaceChild(littleOne, newContent);
```

Removing: `content.removeChild(littleOne)`. More on that below.

**`registerElement`** lets you define a custom element. The first argument is the tag name (e.g. `mug-shops` — the hyphen is mandatory). The second is an options object whose new element inherits from `HTMLElement`, otherwise the standard methods and properties won't be there. The call registers a new custom element and returns its constructor.

```js
var constructor = document.registerElement(tagName, options);
```

**`createTextNode`** creates a new text node:

```js
var itsText = document.createTextNode('the string you need');
```

The qualitative difference from `innerHTML` is that with `innerHTML` you go through the whole DOM-construction pipeline. Between `createElement` and `createTextNode`, performance is roughly the same.

We've created an element and a string, but where do they live? To insert into the document, use **`appendChild`**. One argument: the node to add. The element you call it on becomes the parent — the new node is appended as the last child.

```js
var myNewText = document.createElement('div');
myNewText.textContent = 'New element';
document.body.appendChild(myNewText);
```

Or:

```js
document.domain = 'google.ru';
var output = document.createElement('p');
document.body.appendChild(output);
```

**`insertBefore`** is what you want when you need to slot something in among the children of a parent. Two arguments: the new child node, and the existing sibling it should sit before. Combine `appendChild`, `insertBefore`, and `replaceChild` and you can put a node anywhere on the page.

## Cloning and removing

Clone with `elem.cloneNode(true)`. Remove with `removeChild`:

```js
var post = document.getElementById('_Q5');
post.parentNode.removeChild(post);
```

That's enough to get you flexibility when preparing tests and validating hypotheses. A little JS goes a long way, and the validation loop suddenly involves fewer people on the team. And yes — prepare for fun with older versions of IE. There's no avoiding it.

---

These have all been toy examples. In real hypothesis-testing work I've written over a thousand lines of code in service of a single experiment. Once it's written, you drop it into Google Tag Manager, run the A/B test, and collect data in parallel.

## Designing tracking before the build starts

If you're building a service from scratch, there's a way to make your future self's life much easier: design your tracking model *before* handing mockups to engineering. Think about structure and the naming of elements in the markup. Categorise your events.

Say you have an e-signature flow. Put every action inside it into a single event category — `order_esignature` — so you don't end up with one scenario's events bleeding into another. The minimal field set:

- URL of the page
- **Trigger** — what initiates data collection
- **Category** — what kind of component this is: Inputs, Buttons, etc.
- **PageType** — what page or step we're on
- **Action** — what the element actually does (from/to, date of birth, etc.)
- **Label** — the value of the field or control

For instance: `Category: order_esignature`, `Action: esign_CTA`, `Label: buy_esign`. The point is that later — when you're searching for events and trying to make sense of them — the structure does the explaining. Naturally, a spec like this also includes the full set of Google Analytics bindings.

### When the markup doesn't cooperate

Tracking specs are easy when the markup gives you stable ids and classes to bind to. Often it doesn't. When you can't get the team to expose ids, classes, or URL changes, bind to CSS selectors instead — that often lets you skip editing site code entirely. In GTM, pick Click Element or Form Element as the trigger, then point at a selector. A unique compound selector like `#gform_btn [type="submit"]` gives you a reliable binding to a single button. In GTM, create a new variable of type CSS Selector and feed it the selector — say `#form_element .title`, which reaches into the element with id `form_element` and finds the descendant with class `.title`. You can pass that variable as an event attribute when the click fires.

![CSS Selector variable in GTM](http://your-scorpion.ru/wp-content/uploads/2017/09/shot_170925_132508.png)

For very simple goals — like firing a conversion on a "Register" button — you don't even need GTM. In Yandex.Metrica, putting `onclick="yaCounterYOUR_COUNTER_NUMBER.reachGoal(TARGET); return true;"` (or `onsubmit` for a form) directly on the button is enough.

### Making URLs and labels readable

Long, opaque URLs make analytics reports unreadable. The cleanest fix in GTM is a Lookup Table — give it a list of values, and when a variable matches one, the table swaps in the value you've chosen. The value can be any GTM variable, but the fields are case-sensitive and require an exact match.

![GTM Lookup Table](http://your-scorpion.ru/wp-content/uploads/2017/10/shot_171031_14255833.png)

You usually want fuzzier matching, though, so reach for a RegEx Table. With "Full Matches Only" enabled, `dev\.infotecs\.ru` returns exactly `dev.infotecs.ru`. Turn it off and it'll also return things like `anydev.infotecs.ru.com`. If neither table does what you want, write your own JavaScript Variable.

![GTM RegEx Table](http://your-scorpion.ru/wp-content/uploads/2017/10/shot_171031_145854-1.png)

### Pushing into the dataLayer

There are two ways to send data to GA: directly, and through `dataLayer`. The direct path uses `send`:

```js
ga('send', {
  hitType: 'event',
  eventCategory: 'video',
  eventAction: 'hover',
  eventLabel: 'Campaign_September'
});
```

You see those in GA under Behavior → Events → Top Events.

The more common — and more flexible — path is to push data into `dataLayer` and extract it with GTM variables. Stash data in a JS array, then push it into `dataLayer` with an `event` key (itself an array). GTM reacts to the push and pulls the data in. The [GTM debugger extension](https://www.thyngster.com/release-google-tag-manager-debugging-extension/) is great for inspecting all of this.

A bare push looks like `dataLayer.push({ event: 'trigger_for_tag' })`, and the container runs whatever tag is bound to that custom event. The value can be anything. `dataLayer` is just a variable — you can have several of them under different names. One constraint: the dataLayer code has to be placed *after* the GTM snippet on the page.

The hands-on flow in GTM: create a new Custom HTML tag.

![GTM custom HTML tag](http://your-scorpion.ru/wp-content/uploads/2019/02/Screen-Shot-2019-02-21-at-9.00.10-AM-1.png)

Write code that does something useful on the site. For example:

```html
<script>
  document.getElementsByClassName('block_ui_cust')[0].onmouseover = function () {
    dataLayer.push({
      event: 'autoEvent',
      eventLabel: $('h1').text()
    });
  };
</script>
```

Pick or create a trigger and save. Then tell GTM what to forward to GA, by turning the dataLayer keys into variables of type Data Layer Variable.

![Data Layer Variable](http://your-scorpion.ru/wp-content/uploads/2019/02/Screen-Shot-2019-02-21-at-9.22.16-AM.png)

Then build the forwarding tag, with a trigger whose event name matches the `event` value from the script.

![Forwarding tag](http://your-scorpion.ru/wp-content/uploads/2019/02/Screen-Shot-2019-02-21-at-9.31.14-AM.png)

If everything's wired up, you'll see it firing end-to-end:

![Working dataLayer example](http://your-scorpion.ru/wp-content/uploads/2019/02/Artboard.png)

From here you can forward into VK, Facebook, MyTarget pixels, spreadsheets, GA, Metrica. One warning: this is also exactly how client data gets siphoned by unscrupulous contractors off their customers' sites — so lock tag publishing down to one or two trusted people. The same mechanism is occasionally weaponised to feed fake data to competitors and poison their analytics; I don't recommend that. You *can* combine direct sends and `dataLayer` sends in one project, but I'd strongly suggest against it. In the spec you hand engineering, paste one of these snippets plus a screenshot of the page, and specify exactly when the code should fire — on successful login, on a particular event, and so on.

### Verifying events actually fire

Before trusting any of this, verify the events are landing where they should. Google Tag Assistant works at the code level and gives you an instant pass/fail on the page.

![Google Tag Assistant](http://your-scorpion.ru/wp-content/uploads/2021/02/google-tag-assistant.gif)

Or watch the requests directly in the browser's network tab.

![Watching analytics requests in DevTools](http://your-scorpion.ru/wp-content/uploads/2021/02/faceb.gif)

When you're checking flows that span multiple pages, the DevTools console wipes itself on every navigation — which is exactly the wrong behaviour. The fix is Chrome's "Preserve log" option, which keeps server requests around so you can step through them and replay if needed. The trade-off is huge log files. For dataLayer specifically, the [dataSlayer](https://chrome.google.com/webstore/detail/dataslayer/ikbablmmjldhamhcldjjigniffkkjgpo) and [WASP.inspector](https://chrome.google.com/webstore/detail/waspinspector-analytics-s/niaoghengfohplclhbjnjheodgkejpih) extensions give you a much better view.

![Preserve log in DevTools](http://your-scorpion.ru/wp-content/uploads/2018/08/3424.png)

### Funnels

Once events are flowing, the natural next thing to build is a funnel. In Google Analytics: Admin → Goals → Custom → Destination. On the "Goal details" step, supply the URL of the funnel's final step. The earlier steps go under "Funnel" — either as URLs or as virtual pages when there's no clean URL to point at.

![Funnel goal in GA](http://your-scorpion.ru/wp-content/uploads/2018/09/example.png)

After that, GA starts collecting and structuring the data. The user does something, the GA code runs, drops or reads cookies, sends a GIF request to Google's servers; the server processes the data and exposes it back to GA via API. From there you can build a global funnel (mostly useless) or a segmented one (useful). It isn't the cleanest approach — GA's reporting model hasn't been seriously rethought in a long time, and reports are session-based. A single user spawns many sessions, and your data starts drifting from reality. For a saner number, export absolute conversion values (goal completions) and divide by total users.

For a more flexible funnel, build it from sequences instead: My Reports → Create Report. In the new report, "Add Segment" → "+ Segment" → "Sequences". You get up to four segments.

In Excel: empty cell, type `=`, Ctrl-click the starting data point, repeat in the adjacent cell, drag both down to fill across every step. Pick one column and apply a histogram with solid fill. Do the same for the second, but flip the direction in "Manage Rules".

![Excel funnel](http://your-scorpion.ru/wp-content/uploads/2018/11/Untitled-5.png)

What you'll get is a linear funnel, but real funnels look more like Markov chains — chaotic distributions of user actions. Account for the contribution of individual scenarios and track sequences page-by-page using each user's cookie. Funnels come in many shapes. A common one: great leads come in, the offline sales team drops the ball, the deal dies. Adding offline data to the funnel needs a CRM integration (assuming the sales team's actions are even logged). And you'll often want end-to-end analytics across the whole journey to compare channel effectiveness — every acquisition channel is its own funnel, and you'll be reaching for far more than GA.

Edge cases get hairy. Three people call from three different numbers, a fourth shows up with two different phones, signs a power of attorney to buy a car for a fifth, all of them are family members interested in the same car — you need to be able to merge five contacts into one. The CRM has to be willing to hold many phone numbers on a single record.

Then you go looking for bottlenecks. That part comes down to domain knowledge. A "cart → payment" step depends heavily on basket size: 80% compression at the top is fine for high-ticket purchases; 50% is normal for low-ticket.

### How sessions actually get counted

The session model surprises people, so it's worth being explicit. One search engine, less than 30 minutes between hits = one visit. Different search phrases get attributed to the first one. Closing a tab or the browser doesn't end the visit. If the user is gone for 30 minutes and comes back, that's a new session sourced from your site. So comparing visits is more reliable than comparing visitors — one user can spawn many visits across sessions.

When the source changes the session is supposed to break, in theory. In Metrica it doesn't always — even WebVisor sessions don't always split into 30-minute chunks. It's common to see 300 visits in Metrica and 600 in GA. Metrica counts sessions by cookie, so if the cookie's deleted you get a new session. In Metrica, conversions count only goal visits — visits where the goal was reached at least once. A single visit can have multiple goal completions.

A few common edge cases. A click inside a mobile app opens a link in the app's in-app browser, the visitor does nothing, leaves, then comes back later through a different link in the phone's regular browser. New user, source `direct / none`. The fix is usually to pull the `c_user` cookie ID from Facebook Pixel and ship it into the GA ID.

External authentication providers — Gosuslugi, OAuth flows, anything that bounces the user off your domain — are another classic source of broken sessions. In GA you can add the provider to the referral-exclusion list, but that's a filter, not real automation. The cleaner move is `utm_nooverride=1` on the outbound URL, and on the way back `utm_referrer=` with an empty value. With an engineering bent, drop a cookie into `localStorage`, read it on a specific event, and stitch sessions yourself. In Metrica it's harder — `client_id` likes to go missing and you end up with a fresh user record.

Ad-network referrals are separate visits. Every new click on an ad within the same 30 minutes is its own visit; each ad hit creates a new visit and terminates the old one.

WebVisor itself can quietly stop recording — usually because someone disabled it in the counter code for page-speed reasons. If you have more than one counter on the site, only enable the visor on one of them. And try disabling ad blockers if visitors aren't appearing.

### GA structure when you have many sites

The typical GA structure is account → properties → views inside properties. With multiple domains, or country-specific sites, give each site its own GA account. You *can* keep separate properties (counters) inside a single account, but you'll hit GA's limits.

![GA account structure](http://your-scorpion.ru/wp-content/uploads/2021/01/Group-32940.png)

A useful four-view layout: one view holds RAW data with no settings, the second holds your configured settings, the third is for test settings, the fourth uses User ID (not Client ID). For authenticated users you generate identifiers tied to email; the User ID view has its own unique reports. In GA4, there's no view layer — just a single data stream into the account.

The Account level itself has very few settings — basics, access, filters, change history — so grant access there when someone needs everything. An SEO contributor only needs organic-traffic data, which lives at the view level.

Three things worth ticking off on every property: set Data Retention to "Do not automatically expire"; if you have payments that redirect to a bank's site, disable source overwriting on Last Non-Direct Click and add every bank domain to the Referral Exclusion List; under "Bot Filtering", tick "Exclude all hits from known bots and spiders" and "Remove query parameters from URL".

## React

Everything above is about using JS to reach an element and pull data out of it. But that's working with the real DOM, and many modern sites use React, where you don't have direct access — only the virtual DOM. Say you have a parent file `App.js`:

```js
import React from 'react';
import ReactDOM from 'react-dom';
import Users from './app/components/Users';

const USERS = ['Moscow', 'Saratov', 'Dubai', 'Minsk'];
ReactDOM.render(<Users items={USERS} />, document.querySelector('#root'));
```

And a `Users.js` component:

```jsx
import React, { Component } from 'react';

export default class Users extends Component {
  constructor(props) {
    super(props);
    this.state = {
      users: this.props.items,
    };
  }

  render() {
    const users = this.state.users.map((user, index) => (
      <li key={index}>{user}</li>
    ));

    return (
      <div>
        <ul>{users}</ul>
        <hr />
        <label>
          Enter your city: <input type="text" placeholder="City name" />
        </label>
        <button>Add</button>
      </div>
    );
  }
}
```

You want to read the value out of that text field. In React, the way to do this is via a ref you've defined in advance, and you reach into that ref to pull the value. Add `this.myRef = React.createRef()` to the `Users` class, and `ref={this.myRef}` to the `<input>`.

The task: extract the field's value when the button is clicked. Logically, you'd call a function on click — `<button onClick={() => {}}>`. But since we're working in a class and we own the source, defining a method is more elegant. Don't add the ref directly; instead, in `Users`, add `this.addCity = this.addCity.bind(this)` so that calling it returns a function with the same behaviour but the correct `this`. Now the cherry on top:

```js
addCity() {
  console.log(this.myRef.current.value);
}
```

If you'd rather not just log the value, store it in an array. You can't mutate the current array, but you can build a new one. Pass an object with a `users` property to `setState` and feed it a new array. The helper: `const users = [...this.state.users, user]`. I deliberately named the array and the state key the same, so instead of `users: users` I can just write `({ users })`.

```js
addCity() {
  const user = this.myRef.current.value;
  const users = [...this.state.users, user];
  this.setState({ users });
  this.myRef.current.value = '';
}
```

And you can clear or seed the input via `this.myRef.current.value = ''`.

### Tracking React apps

GTM's standard `pageview` trigger doesn't fire on client-side route changes in a React SPA, so you need a different approach. The easiest is to call `dataLayer.push` from wherever you actually need an event. You can also build an extra layer on top of GTM with `tiny-emitter`. If you don't want to touch the site at all, use `History Change` (History Listeners) in GTM instead of `pageview`.

![History Change trigger in GTM](http://your-scorpion.ru/wp-content/uploads/2021/03/bf.png)

The spec for engineering is just to push data on view and on route change:

```js
window.dataLayer = window.dataLayer || [];
window.dataLayer.push({
  event: 'Pageview',
  pagePath: '/virtual-page/',
  pageTitle: 'title'
});
```

And the GTM side:

![GTM trigger setup](http://your-scorpion.ru/wp-content/uploads/2021/03/gd.png)

## Performance

The metric everyone reaches for is FPS. A small script that prints frames-per-second every second:

```js
const ONE_SECOND_DURATION = 1000;
let framesCount = 0;
let startTime = performance.now();

function fpsCounter(now) {
  if ((now - startTime) > ONE_SECOND_DURATION) {
    console.log(framesCount);
    framesCount = 0;
    startTime = now;
  } else {
    framesCount += 1;
  }
  window.requestAnimationFrame(fpsCounter);
}

fpsCounter(startTime);
```

In theory the browser calls `requestAnimationFrame` at the monitor's refresh rate — a 100Hz monitor should manage 100fps. If float precision is good enough, there are still a few different things you could be counting:

1. Frames the GPU computed (`setTimeout`, `readPixel`).
2. Frames the monitor or OS actually displayed.
3. Frames the browser was instructed to draw (`requestAnimationFrame`).

The cleaner approach is `(window.performance || Date).now()`, averaging frame counts per second. A peak at any single moment will lift the running fps value. Linear interpolation helps too: `const lerp = (a, b, t) => (1 - t) * a + t * b` — pass start, end, and a coefficient between 0 and 1; get back a value between them. Quick and dirty: just `performance.now()` before and after a change, on the fly.

For high-load DOM rendering, [Inferno.js](https://infernojs.org/) holds up well, and so does [incremental-dom](http://google.github.io/incremental-dom/). But there are no miracles — you still have to optimise the markup. None of these libraries change the asymptotic complexity if users can generate page content. There'll be a temptation to "fix" the DOM with accordions and pagination, but that's bad for UX, especially for users with low vision. Better paths: time slicing (React), prerender, lazy virtual rendering.

The simplest wins are newer CSS properties:

```css
content-visibility: auto;
content-intrinsic-size: 1200px;
```

Plus `will-change`. Another approach is the Intersection Observer, which tells you when an element enters or leaves the viewport — the alternative being to walk the page with `requestAnimationFrame` and ask every element where it is.

## Automating clicks across a site

For automating hundreds of clicks across a site, native JS isn't enough. Selenium is the standard. The short version on macOS:

[Install chromedriver](https://chromedriver.chromium.org/downloads) and [Beautiful Soup](https://pypi.org/project/beautifulsoup4/). [Jupyter](https://jupyter.org/install) is the standard IDE choice.

```bash
python3 -m pip install virtualenv
```

Spin up a virtualenv and activate it:

```bash
virtualenv -p /usr/local/bin/python3.11 ps_scape
source ps_scape/bin/activate
```

If anything breaks, `pip install virtualenv` almost always sorts it. Install the rest:

```bash
pip install selenium
pip install beautifulsoup4
pip install jupyter
```

Tell the shell where chromedriver lives:

```bash
export PATH=$PATH:/Macintosh\ HD/Users/name/DownloadsFolder/chromedriver_mac64/chromedriver
```

Reactivate the env (`source ps_scape/bin/activate`), launch the IDE in the browser with `jupyter notebook`, pick:

![Jupyter new notebook](https://your-scorpion.ru/wp-content/uploads/2023/08/Rectangle-1.png)

A small example that opens a page and scrolls it:

```python
from selenium import webdriver

driver = webdriver.Chrome()
driver.get("https://q.midpass.ru/ru/Account/PrivatePersonLogOn?ReturnUrl=%2fru%2fHome%2fIndex")
driver.execute_script('window.scrollBy(0, 500)')
driver.execute_script('window.scrollTo(0, document.body.scrollHeight)')
driver.execute_script('window.scrollTo(0, 0)')
# driver.quit()
```

For clicks and other interactions, the [docs](https://www.selenium.dev/documentation/webdriver/actions_api/) are the right reference. Typing into a field:

```python
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys

driver = webdriver.Chrome()
driver.get("https://q.midpass.ru/ru/Booking?serviceId=98a6eb")

input_element = driver.find_element(By.ID, "Password")
input_element.clear()
input_element.send_keys("Hgd5w")
input_element.send_keys(Keys.RETURN)
```

You can even react to changes — here, getting notified when a dropdown's contents shift:

```python
import time
from selenium import webdriver

driver = webdriver.Chrome()
driver.get("https://example.com")

select_element = driver.find_element("class name", "SelInp")

initial_value = select_element.get_attribute("value")
initial_options = [o.text for o in select_element.find_elements("tag name", "option")]

while True:
    current_value = select_element.get_attribute("value")
    current_options = [o.text for o in select_element.find_elements("tag name", "option")]

    if current_value != initial_value or current_options != initial_options:
        print("Visual change detected")
        print(f"Initial value: {initial_value}, current: {current_value}")
        print(f"Initial options: {initial_options}")
        print(f"Current options: {current_options}")

        initial_value = current_value
        initial_options = current_options

    time.sleep(5)
```

Or skip code entirely and record interactions with [Selenium IDE](https://www.selenium.dev/selenium-ide/) — it'll push you to install a browser extension; the interface is straightforward.

For other parts of the job, smaller specialist tools usually beat one big one. Xenu parses broken links well; so do web services like deadlinkchecker.com.

## A Webflow checklist for SEO

If the site lives in Webflow rather than custom code, the tracking and ranking story comes down to a short checklist: name classes following a convention; strip unused data attributes; set explicit dimensions on raster images and give them alt text; add `nofollow` to links that need it; build a clean heading hierarchy from H1 to H5; remove unused styles; structure the page as `Body → sections → containers`; convert raster images to AVIF.

## A note on what A/B tests do and don't prove

One last thing, because this comes up constantly. A/B tests are sometimes treated as a verdict on the designer — "my variant beat theirs, the designer must be incompetent." That's the wrong frame. A/B tests come in two flavours: client-side and server-side. Client-side tests the frontend; server-side tests the merchandising matrix. Either way, you don't plan a test around design details — you plan it around PIE Score. Small design changes (button colour, headline size) move product metrics very little, and they're easy to validate with MVT. So an A/B test isn't a designer evaluation; it's a hypothesis test.

And before drawing any verdict from a result, ask the boring questions. Was the result statistically significant? Was the data trustworthy? Were outliers controlled for? How was payment information validated? What models were the results built on? Did one variant consistently lead, or did they trade places? Was the traffic split parametrically?

If the actual goal is measuring a designer's KPIs, start by measuring the whole team's ROI; if it's bad, bring that back to the team. They'll sort out internally who's underperforming. That kind of criterion does drive hiring and firing in industry. On the individual level, designer metrics include CSAT, conversion rate, NPS, drop-off rate, increase in order value, increase in page views, decrease in support calls, user-error reduction — but the designer also needs the authority to actually push their changes into the product, even when the PM and the analyst disagree. There's also Fit For Purpose: was the task done at all?

---

You now have more flexibility to read, change, and add data when preparing tests and validating hypotheses. A little JavaScript, and validation gets dramatically faster while touching fewer people on the team. And — get ready for headaches with older IEs. No way around it.
