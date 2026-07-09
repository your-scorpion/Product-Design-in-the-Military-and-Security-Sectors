# Getting Data from External Sources

*Maxim Tsvetkov · February 20, 2019 · [original (RU)](https://your-scorpion.ru/rstudio-data-from-internet/)*

R's functions can read data not just from your local machine but from the network as well. Suppose a task lands on you: gather every available contact for the sales managers handling a particular product in your region, and drop all of it, neatly formatted, into Excel. You can either spend hours crawling sites and copy-pasting by hand, or run a script across all of them and scrape the data. Before you do, though, read up on your country's laws about scraping websites. And since sites are physically hosted all over the world, it's better to also get written consent to scrape from the site's owners. Only then do you have the right to do any reverse engineering.

The first thing you do before hunting for data on a site is look at its robots.txt file: `baidu_robotstxt <- "http://www.baidu.com/robots.txt"`. Inside, the site's sections are listed, and some will carry an Allow directive that permits crawling by search robots. If you see something like `Disallow: /data/` together with `Allow: /data/texts/`, the data directory is closed to scraping but you may look at texts. You can also find the line `content="noindex, nofollow"` in the page's own header — that, too, is an explicit ban on copying the information. There's a [script](http://www.r-datacollection.com/materials/ch-9-scraping/robots-parser.r) that reads robots.txt and prints the list of a site's scraping-forbidden directories straight into the RStudio console.

For this article we'll work with open-data sources — machine-learning data repositories, for instance. The simplest task is to download a ready-made .csv file from the internet (HTTP) or from your company's FTP. The protocol might be FTP, sFTP, or TFTP; the second is the more secure, and TFTP is for small files, with no login or password. The ports: FTP uses 20 and 21, sFTP uses 22, TFTP uses 69, and SMB (Microsoft) uses 445. FTPS is FTP plus TLS.

A data source is usually handed to you as a .csv (comma-separated values), but the data may in fact be separated by semicolons, so it's better to use the more general `read.table` rather than `read.csv`. For the first example, let's take a dataset from [archive.ics.uci.edu](http://archive.ics.uci.edu/ml/index.php).

```r
dafileMaster <- "http://archive.ics.uci.edu/ml/machine-learning-databases/wine-quality/winequality-white.csv"
df <- read.table(file = dafileMaster, sep = ";", header = TRUE)
View(head(df))
```

It worked — the table showed up in RStudio. Note that `header` lets you say whether the values in the first row are column titles. The .csv format is popular to work with, but in a corporate setting you'll easily run into an Excel file too. And you may well need to write data straight into an .xlsx file, refreshing all the formula calculations, and read the updated results back into other tables. With [XLConnect](https://cran.r-project.org/web/packages/XLConnect/) you can work with Excel files both in the ancient, slow Excel '97 format (*.xls — and hello to accountants' interfaces) and in modern OOXML (Excel 2007+, *.xlsx), and write values to a file. An example of reading data:

```r
install.packages('XLConnect')
library(XLConnect)
wb = loadWorkbook("C:/Users/Downloads/testfile.xlsx", create = T)
df = readWorksheet(wb, sheet = "data")
newExport = readWorksheet(wb, "Data", 1, 4, 1000, header = TRUE)
summary(newExport)
```

![](https://your-scorpion.ru/wp-content/uploads/2019/03/max_tsvetkov_artist_WIP.gif)

Now let's try writing data to a file with a simple `writeWorksheetToFile("C:/Users/Downloads/testfile.xlsx", data = iris, sheet = "iris2")` — and that's basically it, the test iris2 data has appeared in the file.

![](https://your-scorpion.ru/wp-content/uploads/2019/03/max_tsvetkov_artist_WIP-2.gif)

If you hit Java dependency problems, you can use [openxlsx](https://cran.r-project.org/web/packages/openxlsx/index.html) instead of XLConnect.

```r
install.packages("openxlsx", dependencies = TRUE)
readWorkbook('C:/Users/Downloads/testfile.xlsx')
```

But sometimes you have to roll up your sleeves and work with noisier data — pulling down an entire web page, say. To download a site from the internet, it's enough to declare the site's address in a variable, point to a download folder, and issue the download command. The same approach works for PDFs and archives. Once you're done, the downloaded site or file can be deleted with `file.remove(paste(myfolder, "filename.html", sep = ""))`. Below is a basic example, but there's a [downloader](https://cran.r-project.org/web/packages/downloader/downloader.pdf) library that lets you work more flexibly with the https protocol. Also, the protocol is sometimes called the scheme, and a full link looks like this: `<scheme>:[//[<login>[:<password>]@]<host>[:<port>]][/<path>][?<parameters>][#<anchor>]`. That's how the browser knows what information to carry to the server, and over which channel. A port rarely shows up visibly in an address, but every site has one. Port 80 is http, and different services can live on different ports of the same domain. Login and password are needed for FTP.

In the second example the file will be downloaded into the default folder — for me that's `C:\Users\user\Documents`.

```r
url <- "https://your-scorpion.ru/"
myfolder <- "E:/download/"
download.file(url, paste(myfolder, "filename.html", sep = ""))
```

```r
download_url <- "http://insight.dev.schoolwires.com/HelpAssets/C2Assets/C2Files/C2ImportSchoolSample.csv"
download.file(download_url, "./C2ImportSchoolSample.csv")
```

This is interesting, but what if we only need a small piece of information from a site? Let's return to the original task — yanking the key information off a page. For that we'll need the [XML](https://cran.r-project.org/web/packages/XML/) and [rvest](https://cran.r-project.org/web/packages/rvest/) libraries.

```r
install.packages("rvest")
install.packages("XML")
install.packages("openxlsx")
library(rvest)
library(openxlsx)
url <- read_html("https://your-scorpion.ru")
names <- url %>%
    html_nodes("h1") %>%
    html_text()
names1 <- url %>%
    html_nodes("article") %>%
    html_text()
newOne <- html_children(img)
lineOne <- html_attr(newOne, "href")
linetwo <- html_attr(newOne, "div")
myTableForBoss <- data.frame(title = names1, datam = names)
write.xlsx(myTableForBoss, file = 'C:/Users/checkInOnMonday.xlsx')
```

What we get out is a table of scraped article titles and descriptions. This is very often exactly how product data gets scraped from online stores. In the example we scraped only a single page; to scrape every page of a site you'd first have to scrape all the links you need — or generate them, since URLs are formed by certain algorithms in the engine.

![](https://your-scorpion.ru/wp-content/uploads/2019/04/Screenshot_2.png)

Pulling those links is a small job of its own. Point rvest at the page, grab every `<a>` node, pull its `href`, and filter down to the ones you care about:

```r
install.packages('rvest')
library(rvest)
library(selectr)
library(xml2)
library(jsonlite)
library(tidyverse)
page <- read_html("https://hh.ru/")
links <- page %>% html_nodes("a") %>% html_attr("href")
print(links)
links <- links[grepl('Tools', links)]
page2 <- paste0('https://hh.ru/', links[100])
print(page2)
```

From there it's just string assembly to build the next page's address. And when you don't yet know a site's shape at all, it helps to map it first. Heavier tooling like Burp Suite Pro (with a license), Zed Attack Proxy, or Fiddler will do it: in Burp Suite's site map you can explore a site's components, intercept traffic, and read through the HTTP history. More homespun, you can size up what a site is built from with the browser extensions Wappalyzer, BuiltWith, and WhatRuns. To keep your results free of contamination you'll want to rotate proxies often — any proxy manager will do; I use the foxyProxy browser extension. In foxyProxy you add a new proxy with IP 127.0.0.1 and port 8080 and activate it; after that, trying to open any site will tell you there's no internet, which is normal. You can add black and white patterns — white for allowed traffic, black for traffic that's forbidden but still routed through the proxy. It's also about the most basic way there is to restrict access to sites: blacklist ctrip.com, and any traffic from ctrip.com won't pass through the proxy. A working set of proxy parameters looks like this — Title: anything; Proxy Type: SOCKS5; IP address: any proxy IP (say, xx.xx.xx.xx); Port: 3785; Username and Password: the proxy's, if any. And this is the right place to say it plainly: be very clear about the difference between making frequent requests to a site and scraping it. The latter is bad, and forbidden.

As a site's structure gets more complicated, the code can get hairier, and you won't manage to avoid NA (Not Available — a missing value). Let's get a film's rating from a third-party site:

```r
library(rvest)
movie2 <- read_html("https://www.kinopoisk.ru/film/840372/")
rating <- movie2 %>%
   html_nodes(xpath = '//*[@id="block_rating"]/div[1]/div[1]/a/span[1]') %>%
   html_text() %>%
   as.numeric()
print(rating)
revenue <- c("rating_ball")
companiesData <- data.frame(revenue, rating)
write.csv(companiesData, "E:/Downloads/data.csv", row.names = FALSE, na = "")
# or, keeping the NAs in the data
rating <- movie2 %>%
   html_nodes("a span") %>%
   html_text() %>%
   as.numeric()
print(rating)
library(data.table)
result <- data.table(rating)[, lapply(.SD, function(x) x[order(is.na(x))])]
result <- result[!result[, Reduce(`&`, lapply(.SD, is.na))]]
revenue <- c("rating_ball", "ratingCount", "Expectations")
companiesData <- data.frame(revenue, result)
```

![](https://your-scorpion.ru/wp-content/uploads/2019/04/Screenshot_2-1.png)

As a result we got the film's rating and even stripped out all the NAs. In the real world it's not so rosy: sites defend themselves against scraping with a captcha at the OAuth level if you don't supply a certificate. To get around it people may point you to anti-captcha services — but they won't warn you that you'll be banned before you ever get a result. Especially if you fire off parallel requests to pull heavy data like images or video. And that's only right. For working with OAuth there are packages like [httr](https://cran.r-project.org/web/packages/httr/vignettes/quickstart.html) and [RCurl](https://cran.r-project.org/web/packages/RCurl/index.html).

OAuth comprises the client credentials, temporary authorization data, and a token. Every OAuth problem is cured by supplying the correct certificate. After registering, we usually receive the credentials. The temporary credentials prove that the application's request for access tokens is being made by an authorized client — our own Twitter account, for instance. Once you have the temporary credentials, you can exchange them for token credentials.

Because sites run on a client-server architecture, every isolated request and response would be hard to analyze, so requests from a single user are grouped into a session. Below is an example that opens a session and submits a comment through a form.

```r
library(rvest)
box_office <- read_html("https://your-scorpion.ru/")
box_office %>% html_node("form") %>% html_form()
session <- html_session("https://your-scorpion.ru/printing-for-dummies/")
form <- html_form(session)[[1]]
form <- set_values(form, author = "Ivan Ivanov", comment = "My comment is that this idea is quite sound.")
submit_form(session, form)
```

Now let's scrape an API — the output is plain JSON. If you can get the data you need through an API, that's the preferred route compared with scraping pages; otherwise you're in for long, stubborn work with regular expressions. The methods can be mixed. The simplest, most obvious thing with JSON:

```r
install.packages("rjson")
library("rjson")
json_file <- "C:/Users/25.4.2019-Untitled.json"
jsonclass <- fromJSON(file = json_file)
```

![](https://your-scorpion.ru/wp-content/uploads/2019/04/smoke_PNG55243.png)

Let's make the example look a bit more like a real task and use regular expressions. Strictly speaking, any string is a regular expression, even a single character — but the real value of regular expressions is in their complexity.

```r
install.packages("tidyverse")
library(tidyverse)
datalist <- c("Facebook.com", "Twitter.de", "Wikipedia.org", "Google.at", "Vc.ru",
"Dribbble.cn", "Instagram.fi", " Vk.cc", "Yandex.ru", "Your-scorpion.ru",
"Linkedin.com", "Wordpress.org", "Pinterest.kz")
pattern <- "(G|F)"
pattern2 <- "[a-zA-Z]*$"
str_subset(datalist, pattern)
sum(str_detect(datalist, pattern2))
```

There's a whole range of interesting things you can do with strings through regular expressions. For example, `str_extract(datalist, pattern2)` will show all the characters with certain occurrences, and the regular expression `"[a-zA-Z]*$"` lets you pull every domain out of the strings. One important nuance: you need to use `/.csv`, because a bare `.csv` matches any value. For instance, `str_match(c('abcsv', 'a.csv'), '.csv')` returns the match `bcsv`, which isn't what we want, whereas `str_match(c('abcsv', 'a.csv'), '/.csv')` works as expected.

Pulling the data is only half the job. Sooner or later the questions stop being about how to fetch a number off a page and start being about what to do with everything you've collected — where it should live, and how to make sense of it.

Take a common one: how do you tell that the same person is using two different mobile apps of yours — that the Vasya in app one is the Vasya in app two? By the device identifier. In any analytics system the device identifier will be the same, so all you need is a simple join on it.

Then there's the question of where to keep the data at all. Technically you want to accumulate the logs of your web services and microservices — clickstream above all — and hold it all in your own database. Early on you can live with the likes of Amazon Kinesis, Apache Kafka, Presto, Impala, or Hadoop plus Amazon S3. Later, once you need deep behavioral analytics and there's money for DevOps, you stand up something like ClickHouse and write the data transport for it. Survey results can go straight into the database. What you should end up with looks roughly like Kafka/RabbitMQ → Spark Streaming → Cassandra. And the division of labor is just as worth getting right: the developer and the analyst design how data is collected, the sysadmin and the analyst decide how it's stored, and the manager and the analyst make decisions on the back of it.

If the goal specifically is to manage the customer experience better, there are dedicated CDP platforms — Customer Data Platforms — that pull data in from every source you have (WhatsApp, the website, the app, the ERP) and work out which customer should be reached, and through which channel: SMS, push, email, messengers. That's data on purchases, on product views, on bonuses about to expire. Personalization here means addressing people by name, showing their bonus balance, tying to their geography, reading their likely intent, understanding where they are in their lifecycle. And one thing worth watching on the side is employee fraud: if a cashier is logging a customer's bonuses onto their own card, fire them on the spot.

A different but related need is telling whether a customer has paid back what you spent to acquire them over some stretch of time. If you're buying users through targeted ads and every lead is expensive — and it has to pay off over a year or two, because the customer keeps coming back for servicing — that's a textbook cohort analysis, and it's comfortable to do in Python. You take a dataframe of the data:

```python
import matplotlib
matplotlib.use('TkAgg')
import pandas as pd

df = pd.DataFrame({"id": ["user1", "user2", "user2", "user3", "user2", "user1", "user3"],
                   "order_date": [5/25/14, 5/25/14, 4/14/14, 3/12/14, 5/25/14, 5/25/14, 5/25/14],
                   "customer_id": [454332, 564333, 454333, 754211, 454332, 433135, 564333],
                   "sales": [43.0, 23.0, 53.0, 10.5, 7.5, 33.3, 3],
                   "first_orders": [5/25/14, 5/25/14, 5/25/14, 15/25/14, 5/25/14, 5/25/14, 5/25/14]})

df['order_date'] = pd.to_datetime(df['order_date'])
```

compute the total revenue and the order count:

```python
print(df['sales'].sum())
print(df['sales'].count())
```

and then the same figures per user:

```python
print(df.groupby('customer_id')['sales'].agg(['sum', 'count']))
```

When the data you need is from a game, the toolset shifts. For browser games, anything that speaks POST requests will do — you just send events to the server as JSON; GameAnalytics is worth a look too, you could practically embed it in a fridge. For mobile games Amplitude fits well, and if its pricing doesn't suit you, AppMetrica and DeltaDNA are fine as well, or Firebase for raw data. Really it comes down to the analytics system's pricing plan and the kind of game you have: if you'll have a flood of users with short sessions, take a system that charges by data volume; charging by DAU suits you better if you'll be shipping megabyte JSON that logs every single user action. Mixpanel, Localytics, and Appsee are worth a look as well — I love the last one for its heatmaps and screen recordings of users. If the emphasis is on ASO — testing icons and screenshots — look at SplitMetrics. And there's no escaping the trio of AppsFlyer, Firebase, and Facebook Analytics. If the game's on Unity, don't turn your nose up at Unity Analytics either: it's free, simple, tracks app crashes, and hands you raw data. One catch you'll meet with off-the-shelf tools like Amplitude is data getting trimmed at the browser level — that isn't really an Amplitude problem, many boxed solutions suffer from it. The usual fix is to set up a separate proxy so the data flows through your own servers and nothing is lost; or, again, your own servers plus an open-source analytics system.

Inside GA itself there are two kinds of funnel: e-commerce and goal-based. To build end-to-end analytics you need data sources — at least two, say a CRM and Google Analytics — and the ability to store and visualize it all; put together, that's your end-to-end analytics. To check the cost of a lead: UTM tags arrive from Yandex.Direct, a sale is attributed under the last-click model to the last non-direct traffic, you export the data into BigQuery, your own SQL Server, or ClickHouse, and you visualize it. On top of that you pass Google's Client ID into the CRM and use connectors to load the UTM-tagged data from your ad sources into a separate database. Client ID is an alias for a device or a browser instance — really just a cookie; the CID is usually unified. Nuances will crop up here: if you re-send hits, they all get written to the last non-direct interaction (whatever random channel ga:clientID came back through). And a clientID + sessionID pairing isn't quite a per-user funnel — it's more a per-session one. The alternative is to take the raw data on user actions and assemble it into sessions yourself.

Sometimes access to GA data through R simply stops working — it used to run, and now it throws "Google temporarily disabled for this app." The way out: first, update all your packages (Tools → Check for Package Updates), and play with the checkboxes under Options → packages. Then load `library(googleAnalyticsR)` and `library(gargle)`. Next, run `options(gargle_oauth_email = "name@gmail.com")`, and `ga_auth()` will throw you into the browser. Test it by pulling every linked account into a list with `account_list <- ga_account_list()`:

![](http://your-scorpion.ru/wp-content/uploads/2020/05/Screenshot_26.png)

Then set the account identifier you need with `ga_id <- 00111000` and make your request. There's another way too, through Search Console. Authorize:

```r
library(searchConsoleR)
scr_auth()
sc_websites <- list_websites()
sc_websites
```

You'll be shown a list of sites and need to pick one; `list_websites()` builds the list and `sc_websites` shows it. Then run the query:

```r
gbr_desktop_queries <- search_analytics("https://your-scorpion.ru/",
  "2019-07-01", "2019-08-01",
  c("query", "page"),
  dimensionFilterExp = c('device==DESKTOP'),
  searchType = "web", aggregationType = "byPage", rowLimit = 100)
```

and `View(gbr_desktop_queries)` to look the results over.

![](http://your-scorpion.ru/wp-content/uploads/2020/06/gj.png)

Once you're in, it's worth knowing GA's additional parameters, and what to google for: hit time, session ID, CID, and UID. CID is the browser cookie that identifies a unique user; session ID is the user's session; hit time comes into play when you build complex analytics systems where you control the very notion of a "session," via custom JS — GA passes the event time itself, but a custom one buys you millisecond precision; UID identifies a client — someone who has already bought, i.e. registered — and can be tied to a phone number, an email, and so on, which makes it convenient to shard by. UID takes a developer's involvement: they have to run code that pushes the client identifier out of the CRM.

```js
dataLayer.push({
  "UID": "{{userID value}}"
})
```

Events themselves are built on a Category → Action → Label structure. In code:

```js
dataLayer = [{
  'eventCategory': 'article_32',
  'eventAction': 'link_click',
  'eventLabel': '{{productName}}'
}];
```

A small thing that trips people up is getting the right timestamp out of GA. Timestamps are always in UTC. The cleanest way to get the first-visit date is to stream the data from GA into BigQuery; failing that, you can pull it out of the GoogleClientId, where the first part of ga:clientID — everything before the dot — is itself the first-visit timestamp. In a Client ID of 202344278.1460084621, for example, 1460084621 is the timestamp, though that isn't always so and is worth re-checking every time. By hand it's convenient to convert a timestamp into something human-readable in R with `as.POSIXlt(1460084621, origin = "1970-01-01")`, which gives `"2016-04-08 06:03:41 MSK"`. Or, in BigQuery, `TIMESTAMP_MICROS(1460084621)`.

A narrower snag: on Tilda, form submissions don't reach GA out of the box — or rather, they do if you handle them right. If the form is marked up as a form, the standard path is Triggers → Form Submission. You can also bind to the page URL, since after a submission you usually get a popup or a redirect to a dedicated page:

![](http://your-scorpion.ru/wp-content/uploads/2021/03/S.gif)

Otherwise, look in GTM at which events fire when the form is sent and bind to those — something like trigger → custom event → submit_form.

People also ask whether data from 1C can be pushed into GA, and whether the two integrate at all — there's very little written about it online. You can wire up things like updating GA when an order's status or payment changes in 1C using a Webhook and the measurement protocol. Updating a transaction on the GA side is done with a refund plus a repeat transaction four hours back; set the refresh to a day back and your revenue reports go down the drain. You can send negative transactions, but that breaks the reports too, and such transactions may end up attributed to other acquisition channels. As you can see it's an expensive road — it's simpler to collect the data in your own Data Warehouse and visualize it in BI. Say you've got GA, Yandex.Metrica, and Facebook Ads: matching clickstream across them will come out under 50% out of the box. Or take conversions — GA records them on last-click, and if you drop the time-granularity of a conversion down to the hour (spend ÷ conversions per day = cost per conversion), you get 24 conversion costs in a day. Layer on splits by device, sex, country, day of week, OS, search phrase, and landing page, and you're hunting for the good variants in a multidimensional space — manual analysis becomes impossible, which plays right into the vendors' hands, since it grows their revenue. The right path is ETL + Data Warehouse + a visualizer (Power BI, Data Studio).

Underneath all of this sits the cookie, so it's worth being clear on the types. A third-party cookie is one that doesn't come from your domain; a first-party cookie is your own domain's native cookie. And is it unsafe to keep data on the client, in the browser? It can be: a browser plugin can reach the data if the cookies don't have the httpOnly flag set. Google Chrome stores logins and cookies in encrypted SQLite databases — on your own machine you'll most likely find them at `%localappdata%\Google\Chrome\User Data\Default\Cookies` and `%localappdata%\Google\Chrome\User Data\Default\Login Data` — encrypted with the Windows Data Protection API's `CryptProtectData()` (DPAPI). If you work out `CryptUnprotectData()` you can decrypt them, once you've found the key under `C:\Users\...\AppData\Roaming\Microsoft\Protect\`. You can also launch the browser with its protections off:

```bat
@echo off
cd C:\Program Files (x86)\Google\Chrome\Application
call chrome.exe --disable-web-security --user-data-dir="D:\.tmp"
exit
```

And don't forget the finer points of cookies. Third-party ones are used to track browsing history for advertising. Persistent cookies stick around a long time but have nothing to do with cross-website tracking. There's also the so-called same-origin policy, a security measure that limits how a document or script loaded from one origin can interact with resources from another. Browsers do have vulnerabilities: in Safari's IndexedDB API this policy was applied incorrectly, and the flaw let a script from one site reach the names of databases created on another, breaking same-origin. That led to the disclosure of confidential data that was supposed to stay isolated per origin — one of the real risks of the Safari IndexedDB API bug, namely the unauthorized disclosure of sensitive information. If a malicious site got hold of the database names created on other sites a user had visited, it could also glean their personal interests, activities, or even credentials, and that information went toward targeted phishing, identity theft, and further exploitation. Even so, `credentials: "same-origin"` generally does its job.

A couple of things come up at the edges of all this. When the data you're after is how users actually behave — recording the screen while testing a mobile prototype, say — Android 10 has screen recording built in (Settings → About, scroll down, long-press the Android build number to switch on developer mode, then Settings → General → Developer options), and iOS records the screen easily too. For remote capture you can use Zoom, TeamViewer, or lookback.io; from the desktop, [Reflector 3](https://www.airsquirrels.com/reflector/features/mirroring) is a good choice, though the cables will distort how your respondents really behave. To capture the actual taps you add an external camera plus telemetry, or switch on Show touches on Android (in developer mode), while on iOS you have to add code to the build to highlight taps — libraries like [Touchpose](https://github.com/toddreed/Touchpose) or [Fingertips](https://github.com/mapbox/Fingertips) help. And to record several respondents' mobile sessions at once, [Total Control](http://www.sigma-rt.com/en/) can do the job. At the far other end of "external sources," the data can be the planet itself: to gauge something like the scale of deforestation across enormous areas, you reach for hyperspectral imaging, and in places the method of interferometry.

Combining all the methods laid out here — pulling files, scraping pages, hitting APIs, then storing and making sense of what you've gathered — you can solve most of the everyday tasks you'll meet. And any automation of routine work saves your time and your employer's money.
