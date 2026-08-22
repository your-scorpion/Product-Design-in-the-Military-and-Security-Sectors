*Maksim Tcvetkov · Product Design and Security Lead*


Tables are one of the main ways of working with structured data. R has a great many libraries for working with them. `data.table` is one of the fastest R libraries for large arrays of tabular data, and it works on the basis of chains: the result from one link is passed to the next link as a finished result. In effect, it's an improved, inherited version of `data.frame`, which is the standard data structure for storage in base R.

To get started you need to install and declare the package: `install.packages("data.table")` and `library("data.table")`. Then create some data:

```r
userb2b = data.table(
  ID = c("max","dim","max","sveta","dim","sveta"),
  a = 1:11,
  b = 7:5,
  c = 11:23
)
```

The result is the simplest possible table, with columns ID, a, b, c and the random ranges of values from the code above for each column. I'm aware that choosing digits or a single letter instead of proper names for columns is bad practice, but thanks to this example I get to mention that bad practice so that you won't do it in future. Also, if you decide to import data from a CSV, use the `readr` library or the `fwrite` and `fread` functions from data.table; the base functions are slow.

On that note — loading files without spelling out the full path every time. Put the file in the folder that RStudio reports after you run `getwd()`, which checks your working directory. You can set your own directory and then reference a file inside that folder like this:

```r
setwd("C:/folder_R_yandex")
yandexSurvey <- read.table(file = "data1.txt", header = TRUE)
```

I'd also recommend saving your list of installed packages — the file with that list lands right in the working directory from the commands above.

**Viewing.** From the structure of the resulting table you can see that the `:` symbol separates the row number from the value of the first column. You can verify that the data exists with the `tables()` command, and it's useful to remember `head(userb2b)` for showing the first six elements of a table. If that isn't enough, `print(userb2b, nrows = Inf)` will show far more. You can inspect the table's structure with `str(userb2b)`.

![str() and head() output](https://your-scorpion.ru/wp-content/uploads/2018/12/Frame-31.png)

There's much more on offer: you can view the second and third rows of a table with `userb2b[2:3]`, and to view a single column use `userb2b[, b]`. Note that when you use data.table, all operations are best performed inside the square brackets — otherwise you can get unpleasant surprises in the form of excessive copying.

![Viewing rows and columns](https://your-scorpion.ru/wp-content/uploads/2018/12/asa.png)

**Sorting.** To sort the table, use `userb2b[, sort(unique(c))]`.

You can sort a table first in ascending order for one column and then in descending order for another, using the well-optimized `order()` function — for example, `newtav <- userb2b[order(c, -b)]`. The `-` symbol means descending. I stressed how optimized this function is for a reason: `order()` uses the internal fast-ordering method `forder()`, which is so much better than R's standard `base::order` that data.table's sorting algorithm was adopted as the default in R 3.3.0 back in 2016.

You can extract data as a vector with `userb2b[["ID"]]`, and to show only certain columns use `userb2b[, c("a","b","c")]`.

All of this has practical application. Suppose that in our table the "ID" column stands for the username and column "c" for the daily ARPU. The command `userb2b[, .(ID), by = "c"]` will easily sort the usernames by the ARPU metric.

**Editing.** If you need to edit the table — to replace the maximum value 23 with 11, say — that's easily done as follows: `userb2b[c == 23L, c := 11L][]`.

And if we want to delete a whole column, there are two common ways: null out the column you want, or delete it by column number.

```r
userb2b[, c := NULL]
userb2b[, 2 := NULL]
```

![Deleting a column](https://your-scorpion.ru/wp-content/uploads/2018/12/asa-1.png)

You'll have noticed that the `:=` operator is used everywhere; it's needed for adding, removing, and updating columns.

Now let's create columns. In the second example the new column is created on the basis of mathematical operations on existing columns (recall that we no longer have a column **a** left):

```r
userb2b[, num_night := match(c, sort(unique(c))), by = "ID"]
userb2b[, num := (speed = c / (c - b))]
```

![Creating columns](https://your-scorpion.ru/wp-content/uploads/2018/12/asa-3.png)

**Joining tables.** Working with a single table is nothing complicated, and the reader's first thought is surely "I'd rather do this in Excel." Since we'll primarily be working with several tables, let's create two:

```r
ID <- c(54,45," ",46,78,53,32,51,95)
Formidable <- c(0.44,0.34,"",0.31,0.86,0.87,0.27,0.412,0.5)
Sober <- c("True","True","True","","True","False","True","False","False")
afterCorporateParty <- data.frame(Subject=ID, pt=Formidable, Event=Sober)
DT_data <- as.data.table(afterCorporateParty)

Errands <- c("Can","Can't","","","Can","Can","Can","Can","Can")
ID <- c(34,68," ",46,"",53,"",51,55)
resultResearch <- data.frame(Subject=ID, ready=Errands)
DT_data_2 <- as.data.table(resultResearch)
```

![Two tables](https://your-scorpion.ru/wp-content/uploads/2018/12/Isometric-Graphic-Blockchain-Data.png)

We have two tables with a common "Subject" column, and that's what we'll use to join them. The principles of joining data are the same in Excel, in SQL, and in R. In data.table there's a `merge` function for this, by analogy with SQL: `merge(behavb2b, userb2b, by = "ID")`. This example shows the basis of the syntax — `DT[i, j, by]` — but unlike SQL, in R you can use any functions from any package to work with tables, without being limited to SQL's capabilities. A data.table can be passed to any package that knows how to work with a data.frame. There are four classic kinds of JOIN.

**INNER JOIN** retrieves all records common to both tables on the basis of a foreign key. The result is a table with the records common to the left and right tables. Its main use is obtaining a slice of data across two columns — for example, pulling data from the first table about all the clients who paid and looking in the second table at how much they paid. Inner Join was made for exactly this: it returns rows from both tables that satisfy the given conditions.

```r
newtav <- data.table(merge(x = DT_data, y = DT_data_2, by.x = 'Subject', by.y = 'Subject'))
newtav <- data.table(merge(x = DT_data, y = DT_data_2, by = 'Subject'))
```

![INNER JOIN result](https://your-scorpion.ru/wp-content/uploads/2020/08/965.png)

**LEFT JOIN** retrieves all records from the table specified on the LEFT side. Records from the right table aren't taken. Its main use: viewing all records by a particular column. If no matching record is found in the right table for a row from the left table, the value will be N/A.

```r
newtav <- merge(x = DT_data, y = DT_data_2, by = "Subject", all.x = TRUE)
```

![LEFT JOIN result](https://your-scorpion.ru/wp-content/uploads/2020/08/878554.png)

**RIGHT JOIN** works the same as the example above, but retrieves all records in the table specified on the RIGHT. It's not hard to guess that a Left Join easily turns into a Right Join when you swap the order of the tables being joined.

![RIGHT JOIN result](https://your-scorpion.ru/wp-content/uploads/2018/12/66965555454545464.png)

**FULL JOIN** retrieves all records from both tables and places NULL in the columns where matching records don't exist in the opposite table. Many people may be tempted to immediately merge all the tables they have and then work with a single one. That's not always a good idea; let's consider an example. A client has a website-design business with ten designers on staff, each assigned to a particular set of projects. Over ten years the whole team has built 10,000 sites, and every site is rebuilt once every two years. That gives 10 years × 10 designers × 10,000 sites × 2 = at least 2,000,000 records in a single table — and if you break the ten years down into months/days/iterations, the number of records will tend toward the hundreds of millions. Now try to estimate, by analogy, the volume of data for a mid-sized e-commerce business, with records detailed down to the package size of every order. Such a table would be inconvenient to work with, which is why it's customary to keep records in separate tables.

![Joining at scale](https://your-scorpion.ru/wp-content/uploads/2018/12/Isometric-Graphic-Blockchain-Data-1.png)

You can set the key in advance with `setkey(DT_data, Subject)`, and to check whether a table has a key, use `key(DT_data)`.

The resulting table can be formatted nicely, exported, and used to form hypotheses on the basis of the data obtained:

```r
newtav <- merge(x = DT_data, y = DT_data_2, by = "Subject", all.y = TRUE)
```

```r
newtav <- merge(DT_data, DT_data_2, by = "Subject", all = TRUE)
```

![FULL JOIN result](https://your-scorpion.ru/wp-content/uploads/2020/08/Screenshot_3.png)

Forming those hypotheses usually means looking for relationships in the data — for instance, tying purchases to what a user did on the site. That's a job for a correlation coefficient, Pearson's or Spearman's. A linear relationship is detected by Pearson's: with a positive coefficient, an increase in some values accompanies an increase in others. If you have no outliers, or you're comfortable with the linearity bias, use Pearson (X·t(X), result from 0 to 1); otherwise use Spearman (−1 to 1). Spearman doesn't need to know the distribution of the features in the population, since Spearman's correlation is just Pearson's computed on the ranks of the data — that is, nonparametric — and the accuracy is acceptable.

```r
x = 1:25; y = x^2
cor(x, y, meth = "p")
cor(x, y, meth = "s")
```

But this is a simple example, without multicollinearity (mutual correlation of features). A result of 0.9 < r ≤ 1 means strong correlation. In practice it looks roughly like this: the correlation between entering a promo code in the cart and placing an order will be high, while between logging in and changing the interface language it'll be low. The alternatives are Kendall's tau, Cohen's kappa, ANOVA, or VIF plus permutation importance from the `boruta` package.

But what do you do when there's simply too much data, the number of tables runs into the dozens, and joining them one by one becomes too slow, expensive, and complicated? When the volume of collected user data crosses a terabyte, one of the solutions is multidimensional databases.

A terabyte of data also raises the unglamorous question of how reliably it's stored, so a brief detour is worth it. Formatting comes in two kinds — low-level and high-level. Low-level formatting creates the layout of sectors and tracks on the disk; on modern disks that option is locked. High-level formatting changes only the disk's table of contents, so lost data can be recovered, albeit with loss of structure, hierarchy, or part of the data. On Windows with NTFS I'd reach for GetDataBack or DMDE; if the disk's controller has failed, you need a donor device. If, on the other hand, your goal is deletion with no possibility of recovery, then simply deleting a file and formatting the disk isn't enough — even overwriting the sectors twice doesn't guarantee complete erasure, since the disk can be imaged with WinHex and recovered elsewhere; dedicated multi-pass overwriting software is what actually helps. And if you want to store data reliably for the long haul, tape is still one of the most dependable methods: the recording density is low and there's no read mechanism inside the cassette, and the data-retention guarantee is on the order of 75 years versus 3–5 years for modern disks. Aircraft black boxes now store data on SSDs, where they once used cassettes, wires, and foil. (The original discussion here also included a few destructive "for laughs" batch one-liners that wipe a system drive; I've left those out.) For testing, there's the Sysinternals **NotMyfault** tool, which deliberately generates blue screens for a variety of reasons.

There's a related kind of data loss — the machine itself falling over and taking data with it. The green screen of death is the analog of the blue screen of death for Insider Preview builds; it appears when Windows fails to handle an exception in kernel mode — a driver, say, decided there was some inconsistency in its processes and that the best course was to forcibly halt the whole system. So a blue or green screen is the OS's protective mechanism against irreversible data loss, and the fact that you lost data anyway means the system simply failed at its job: most likely some low-quality code reached the kernel, the system grew doubtful about the correctness of its processes, and threw the green screen, because all it can do at that stage is force a reboot. To understand the cause, write the memory state to a dump file and analyze it. That file's location lives under About your PC → Advanced system settings → Startup and Recovery → Settings…, where you can see where the dump is written — by default `%SystemRoot%\MEMORY.DMP`.

![Startup and Recovery dump settings](https://your-scorpion.ru/wp-content/uploads/2023/05/b73f3c0ea8f698fc79b3597e89053745.png)

You can open the file with WinDBG and run `!analyze -v`; and if you're handing the file to someone, don't forget the `/pdbstripped` option. The dropdown offers several dump types: **none** writes no dump at all; **small** is up to 256 KB and holds only system information — handy for emailing but useless for hard cases; **kernel** is quite good (the default on Windows 7 and earlier) and includes everything that was in the kernel; **automatic** is the default since Windows 8, writing the dump safely at the right size, and is currently the best option; **complete** writes all of memory, which can balloon the file to tens of gigabytes — fine in the past when memory was small, but unsuited to modern systems; and if you need exhaustive data, **active** also writes all memory, but does it intelligently. To trigger a blue screen on demand for debugging, you can set the registry value `CrashOnCtrlScroll = 1` under `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\kbdhid\Parameters`; after that, holding Ctrl and pressing Scroll Lock twice produces a controlled crash and a fresh dump.

With storage and stability out of the way, the querying problem at this scale is what multidimensional databases solve. They're very convenient to work with: such databases have several dimensions, more than the two of a classic table. Classic databases have columns and rows, as in Excel or the table we created above; multidimensional databases consist of at least three dimensions, forming a cube.

Let's look at an example. There are three dimensions: sales, years, and channels. From such a cube we can learn, say, that 566 sales were made through the direct-sales channel in 2011. That figure comes to us from the intersection of three dimensions in a single cell.

But a single cell is too simple and useless on its own; OLAP lets you obtain the whole flat square with all the data for one channel. And if you work as an outsourcer, there's no getting by in modern CRMs without OLAP.

![OLAP cube](https://your-scorpion.ru/wp-content/uploads/2020/08/Frame-2.png)

OLAP lets you avoid needing a programmer's help, and the speed of data access is very high. It also brings unpredictable load on the servers when the chain "date — date — date — fact — fact — fact" grows too long. But the main thing is that you can analyze an endless number of variations of how users behave, spinning the cube and linking it to MS Office. Remotely, this work resembles pivot tables in Excel. Let's create new data and build an OLAP cube on it, holding the monthly ARPU over a 24-month span for three phone brands.

```r
library(formattable)
formattable(userb2b, align = c("l","r","r","r"), preproc = NULL, postproc = NULL)
```

OLAP is precisely what feeds a business dashboard, and the metrics worth tracking depend on the inputs you have. From a typical e-commerce dataset — client and order IDs, order date without time, the chosen shipping method, and the amount — the financial metrics to start with are AOV (average order value), its median, and the maximum order value (MedOV and MaxOV). Here's [the kind of table](https://drive.google.com/open?id=1VgQJ4t_sc3uX7T5rDacnE2ogqcgordK-) such a calculation works on, lightly trimmed from a recent project:

```r
library(readr)
library(dplyr)
library(knitr)

data <- read_delim("/amazon/orders.csv", delim = ",")
head(data)

data %>%
  summarize(sales_total = sum(sales),
            orders_count = n(),
            AOV = mean(sales),
            MedOV = median(sales),
            MaxOV = max(sales))
```

We get sales_total = 181337, orders_count = 2299, AOV = 78.9, MedOV = 50, MaxOV = 760. What can we say from these numbers? If AOV is in dollars, that's not bad. The median is below the average order value — in other words, half of all orders are priced below the mean, which is already a problem for an online store. Now let's visualize it:

```r
library(ggplot2)

data %>%
  ggplot(aes(sales)) +
  geom_density() +
  geom_histogram() +
  stat_bin(aes(label = ..count..), geom = "text", position = "identity")
```

![Order-value distribution](http://your-scorpion.ru/wp-content/uploads/2019/06/Bitmap.png)

You can see that the number of orders above 200 is noticeably lower than you'd want, so it makes sense to work on high-margin products. You'd also do well to compute cost per sale or per customer (CPS), cost per conversion (CPC), and conversion volume (CV) — and then you can build an excellent business dashboard.

A typical business dashboard, for its part, pulls together a fairly standard set of panels:

- A financial forecast for the week (updated weekly).
- Financial results for the past week (weekly).
- Receivables: the total of invoices issued but not yet paid (real-time).
- P&L (parsed from the accounting system and the banks).
- Employee utilization (against the financial plan).
- The balance sheet (real-time).
- An HR report (weekly).
- Company expenses (weekly).
- An accounting audit.

Separately, there are reports for the operations director (revenue) and for the person responsible for expenses. You can mix metrics from internal and external systems, where the external ones cover the availability of outside services. A contact center's internal metrics are far more varied:

- From telephony: SL, AR (LCR), the current queue, ASA (AWT), and contact volume, to show the queues.
- The same — SL, AR (LCR), current queue, ASA (AWT), contact volume — plus headcount on each skill, to gauge the load on skill groups.
- The outbound line can be characterized by outbound call volume, percentage of the base worked through, percentage of agreements, percentage of sales, and so on, with data drawn from the CRM.
- Performance: time spent in the current status, AHT, productivity, total contacts handled, Absenteeism, Occupancy, and the like.
- Channel load and the number of type-A blockages. If a channel is overloaded, customers can't get through, and it's hard to track — which phone numbers carry what percentage of load, and internet quality by region.
- Quality of work, judged by emotions, interruptions, forbidden phrases, script compliance, and so on.
- Adherence across the various statuses, as a measure of schedule fulfillment.
- From WFM and telephony: Scheduling Volume Forecast Accuracy, Scheduling AHT, and Forecast Accuracy, as forecasting indicators.
- Automation statistics, such as the percentage of IVR-automated contact handling, the percentage handled by the chatbot, the percentage of voice recognition, and so on.
- And at the top sit the call center's business metrics: Sales, the quality of internal business processes, Resolution Rate, and the like.

The result of the cube exists in three dimensions, and now a simple `myResult[, , ]` will give you all the data as a set of tables (since the table is multidimensional), while `myResult[5, -456, ]` makes a slice across two dimensions and yields a simple two-dimensional table. If you look at the data structure with `str(myResult)`, you'll see that `dimnames` is a list of three lists — and the lists are quite a motley bunch, even though they're stored in one place:

```r
coolColor_one = "#27CF9F"
coolCOlor_weak = "#A5E2E3"
levelColor = "#E1BC50"

formattable(newtav, align = c("l","r","r","r"), list(
  "Subject" = formatter("span", style = ~ style(color = ifelse(Subject == "53", "red", "gray"), font.weight = "bold")),
  "pt" = color_bar(levelColor),
  "Event" = color_tile(coolColor_one, coolCOlor_weak),
  "ready" = color_tile(coolColor_one, coolCOlor_weak)
))
```

![Styled table with formattable](https://your-scorpion.ru/wp-content/uploads/2019/01/Group-70.png)
