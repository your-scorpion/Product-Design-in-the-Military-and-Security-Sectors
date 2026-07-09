# Extracting and Filtering Data with SQL

*Adapted and translated from the original article by Maxim Tsvetkov (18 August 2021).*

We won't spend long defending SQL's usefulness. Open SQLite Online and start learning the language we use to talk to data. If you run into encoding trouble, check that the character set is `utf8mb4` — it covers almost everything you'll ever need, Cyrillic and emoji included. SQL began life as a language for accountants, which is where its simplicity comes from: there are no real loops or stored procedures here, only queries. Microsoft does ship a dialect with loops, but you're unlikely to ever need it.

So why reach for SQL at all? Why not stay comfortable in Google Analytics, or Tableau? For routine, straightforward tasks, those are excellent tools. The real reason to move to SQL is the sheer volume of data — and the fact that no one will ever let you upload clients' banking records into GA. Above all, it comes down to speed: writing the query yourself is faster than writing a spec asking someone else to write it. The language has formal subsets — DML, DCL, DDL — but we'll stay with the simplest, most practical SQL throughout.

If you already live in BI platforms, data warehouses, or databases, you'll meet SQL syntax everywhere you turn. Relational databases, warehouse systems like Hadoop, BI tools like Apache Zeppelin — all of them speak it. And SQL is not the last stop on your professional journey. When its capabilities run out, serious analytics calls for R or Python, because loops and row-by-row iteration are painfully slow in SQL. Broadly, databases fall into five families:

- **Relational** — where you need transactional guarantees, heavy normalization, and a large share of insert operations.
- **Key–value stores**, essentially one enormous hash table of keys and values. Examples: Riak, Amazon DynamoDB.
- **Document-oriented stores**, which hold documents built from tagged elements. Example: CouchDB.
- **Columnar stores**, where each block holds data from a single column only. Examples: HBase, Cassandra.
- **Graph-based stores** — network databases that use nodes and edges to represent and hold data. Example: Neo4J.

Before you can learn anything, you need data. If you don't have a suitable dataset within reach, grab whatever database interests you from Kaggle and start experimenting. On the tooling side, you can download desktop MySQL from the official site under the community download; on macOS, head to the WorkBench section. But SQLite Online is more than enough for the basics. MySQL itself is a relational database, and a fine fit for small and mid-sized projects.

## Getting insights out of data

Here's the typical situation: the data is collected, and we want to pull insights from it. By "data" I don't mean only tables — databases can also hold triggers, views, and procedures. When we do analytical work, we never change the underlying data. We may add to it, but we don't alter what's already there. The simplest task imaginable: count how many buyers came from Kirzhach in 2021. Or surface the top ten urologists on a doctor-aggregator site for a given region in 2021. Or find every user who arrived from an email campaign, landed on the page, and spent more than ₽1,000. Or work out the hardest levels in a video game. In short, anything that comes down to sorting and filtering.

**First steps.** If all we want is the unique values across two columns, a simple SELECT will do — SELECT (what we want: the column or columns) FROM (where we want it from: the table). After SELECT, list the columns in the order you'd like to see them: `SELECT date, session, user, status`.

```sql
SELECT DISTINCT tm, player
FROM NBA_season1718_salary
```

Our structured query language has other keywords worth knowing, and they follow a strict *written* order: select → from → where → group by → having → order by. Only `select` and `from` are mandatory, and the order must be preserved. Execution, however, follows a different chain. Spelled out fully, the engine runs the query as FROM → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → LIMIT. That is the order of *execution*, not the order in which you *write* it. SQL reads the whole query at once, works out which table it needs, finds the WHERE, understands the target table, and then walks through the conditions.

Before you make any expensive mistakes, let's get comfortable with LIMIT. Databases routinely hold millions of rows, and returning each one can cost money — even if a single row is only a cent. `LIMIT 4` returns just four rows from the table. You can also write `LIMIT 5 OFFSET 10` to skip the first ten. LIMIT always comes last in the query. As for the `*` wildcard that returns everything: reach for it only when you have few columns — no more than thirty or so. Some companies forbid `*` outright, as a safeguard against schema drift; if a single column changes, the query breaks. When you do need the full picture, `SELECT * FROM INFORMATION_SCHEMA.COLUMNS` will show you every table across every database.

```sql
SELECT *
FROM NBA_season1718_salary
LIMIT 12
```

You can also read the database version with `SELECT @@VERSION`. And if you want to poke a little harder, `BENCHMARK(10000, SELECT @@VERSION)` runs the expression ten thousand times over — a way to probe how the server holds up under repeated load.

## Data types, NULL, and the small surprises

Data types come in numeric and textual flavors. Money gets its own treatment as `decimal` — precise numbers that are, by nature, slow to compute. The documentation lists a great many types aimed at squeezing disk space, but for now it's fine to simply allocate the maximum. Text and dates go in quotes: `name = 'Irene'`, `date = '2021-03-04'`. A few illustrative values: `SELECT 12, true, false, 'text', '2020-03-04', 8.53, NULL`. Here `true` always equals 1 and `false` equals 0. And in `SELECT '123.4ad3a' + '342.3da3'` the result is 465.7 — everything from the first letter onward is simply lopped off. NULL, meanwhile, is the *absence* of a value: the row exists, but the field was never filled.

If you're ever without a calculator, `SELECT 2+5` has you covered, and you can concatenate too: `concat('Today is ', name_pipe)`.

```sql
SELECT DISTINCT basin,
GROUP_CONCAT(basin)
FROM production
GROUP BY 1
LIMIT 20
```

When you plan to concatenate a great many values, declare a `SET` before the first SELECT and raise the string length explicitly — for example, `SET group_concat_max_len = 18446744073709551613;`.

To build intuition for how types behave, run `SELECT 10 - NULL, TRUE = FALSE, 10 = 10`:

| `10 - NULL` | `TRUE = FALSE` | `10 = 10` |
|-------------|----------------|-----------|
| NULL        | 0              | 1         |

Any direct comparison against NULL returns NULL — a rule worth burning into memory early.

## Filtering with WHERE

Most queries lean on WHERE to find the data you're after — for instance, `where name = '2021-04-07'`. That's a test for equality; what about inequality? You can express it three ways: `!=`, `<>`, or `is not`. And when you need only a partial match — because a city name can be written half a dozen ways — the answer is `where location LIKE '%Dublin%'`.

```sql
SELECT *
FROM NBA_season1718_salary
WHERE season17_18 < 3345566 AND player LIKE '%Paul%' AND season17_18 != 1096080
LIMIT 12
```

```sql
-- matching any single character
SELECT year, well, field
FROM production
WHERE year LIKE 'yand_'
LIMIT 20
```

The `%` wildcard matches any number of characters, or none at all — so any date in 2021 is captured by `'2021-%'`. Case doesn't matter, except inside string literals. AND always takes priority over OR; the logic mirrors the arithmetic in those "2 + 2 × 2" trick puzzles, where multiplication binds first. In SQL, `2 or 2 and 2` resolves the same way.

```sql
SELECT *
FROM NBA_season1718_salary
WHERE season17_18 < 3345566 AND x1 NOT IN (254, 232)
LIMIT 12
```

```sql
SELECT year, well, field
FROM production
WHERE year NOT IN ('2019', '2017')
LIMIT 20
```

`IN` tests for membership in a list — filtering, in other words — and `NOT IN` is its mirror image. A second handy condition is `BETWEEN`: `where Seasons_Stats BETWEEN '2020-03-01' AND '2020-01-03'` reads far more pleasantly than stringing together `>=` and `<=`. And then there's the ever-useful OR.

```sql
SELECT *
FROM NBA_season1718_salary
WHERE season17_18 IS NOT NULL AND (player NOT IN ('Blake Griffin') OR season17_18 <= 2578645)
LIMIT 12
```

When you need to pull literal text out of a query, use `SELECT 'value'`. The example below adds a brand-new column. Remember, we're not touching the values in the database itself — we're simply transforming them in the *result*, which is a separate table meant for reading and analysis.

```sql
SELECT *,
'test_result'
FROM NBA_season1718_salary
WHERE x1 >= 43
```

A typical query for assembling the body of an email campaign might look like this:

```sql
SELECT concat('Your order #', order_id, ' for $', total_revenue,
              ' has shipped. Shipping cost $', total_revenue * 0.1,
              '. Thank you for your order!') AS message
FROM sales
WHERE order_date = '2021-04-04'
```

## Aliases, transformations, and time

An **alias** is a nickname for renaming tables and columns. A good one is meaningful, written in English, uses underscores instead of spaces, stays lowercase, and never collides with reserved words or existing column names. Companies will have their own house style for naming. To rename a column, for example:

```sql
SELECT player AS new_name
FROM NBA_season1718_salary
```

`UPPER()` and `LOWER()` recase values as needed.

```sql
SELECT player, UPPER(player) AS new_name
FROM NBA_season1718_salary
```

You can run arithmetic directly, too — this is precisely how metrics are calculated. And don't forget `%` for the remainder.

```sql
SELECT x1, x1 * 2.75 AS x1
FROM NBA_season1718_salary
```

Reading the current time and date is refreshingly simple: `SELECT TIME('now') AS CURRENT_TIME`. If you want to shift that time, you'll need modifiers — and this comes up constantly. Stamping a row with the moment its data was pulled from the database is a common and useful habit:

```sql
SELECT *,
datetime('now', 'start of month', '-59 minutes') AS new_date
FROM NBA_season1718_salary
```

## Sorting

Sorting is handled by ORDER BY: `ASC` for ascending, `DESC` for descending.

```sql
SELECT *
FROM NBA_season1718_salary
ORDER BY player ASC
```

When you sort by several columns at once, the first parameter is applied first, and the second then sorts *within* the ordering the first has already established:

| Name            | sort |
|-----------------|------|
| A.J. Hammons    | 2    |
| Aaron Brooks    | 3    |
| Abdel Nader     | 4    |
| Amir Johnson    | 5    |
| Bruno Caboclo   | 1    |
| Cody Zeller     | 2    |
| DeAndre Liggins | 3    |

Pulling the top *n* records:

```sql
SELECT * FROM NBA_season1718_salary
ORDER BY season17_18 DESC
LIMIT 20
```

```sql
-- or
SELECT *
FROM production
ORDER BY state DESC
LIMIT 15
```

## Conditional logic

The CASE operator steps through conditions in turn and returns a result for the first that matches.

```sql
SELECT x1,
  CASE
    WHEN x1 < 3 THEN x1 % 3
    WHEN x1 = 5.5 THEN 'condition two'
    WHEN x1 > 3 THEN x1 % 5
    ELSE 'none of the above'
  END AS result_calc
FROM NBA_season1718_salary
```

## Aggregation and grouping

Aggregate functions come next. To pull the smallest value from a table, write `SELECT MIN(season17_18) FROM NBA_season1718_salary`, or swap MIN for MAX to get the opposite. More interesting is grouping by an arithmetic mean with AVG — the average across an entire column. Summing works the same way, so we can total up every order with `SELECT sum(sales) FROM production`.

Then there's COUNT(), which counts the values in a column: `SELECT COUNT(*) FROM NBA_season1718_salary`, or `SELECT COUNT(*) AS total_nmb FROM production`. Note that NULL is never counted as a value. With nothing more than this, you could tally how many requests a single manager has handled. `COUNT(DISTINCT x1)` counts the unique values in a column — DISTINCT being all about uniqueness, stripping out every repeated row. Here's a useful example that computes the average total cost two different ways:

```sql
SELECT COUNT(*) AS total_nmb,
SUM(total_price) AS all_data,
SUM(total_price) / COUNT(*),
AVG(total_price)
FROM production
```

And now, GROUP BY — the real workhorse of grouping. We take a column and collapse its identical values into one, naming either the column or its ordinal position. GROUP BY is far more interesting than DISTINCT precisely because it brings aggregate functions along: where DISTINCT merely returns the target column, GROUP BY fuses the data behind it.

```sql
SELECT player, MIN(season17_18) AS min_season, SUM(season17_18) AS sum_season, COUNT(season17_18) AS count_season
FROM NBA_season1718_salary
GROUP BY tm
HAVING season17_18 AND min_season <= 83312219 AND count_season != 14
```

```sql
-- or
SELECT month, environment, year, month
FROM production
GROUP BY 1
LIMIT 20
```

In that last example, `GROUP BY 1` refers to the column's position number. Developers tend to dislike this shorthand; analysts tend to love it. The first example, though, hides an ambiguity in how MIN and MAX are read. Consider:

```sql
SELECT basin, field, installation, MAX(year), MIN(year)
FROM production
LIMIT 20
```

We get a single row back — but is it the row with the maximum value, or the minimum? It's genuinely unclear, and the cleaner answer is to use a subquery:

```sql
SELECT *
FROM production
WHERE year = (SELECT MAX(year) FROM production)
LIMIT 20
```

A second subtlety: write a query like this,

```sql
SELECT basin, avg(year)
FROM production
LIMIT 20
```

and the result simply shows the first value of the `basin` column. To see the full list of values that went into the calculation, you'd reach for GROUP_CONCAT, exactly as we did earlier. So when you write `SELECT environment, COUNT(*) FROM production GROUP BY environment`, what you get back is a count broken down by environment.

**HAVING** is the keyword for filtering grouped values, and it cleans up the results of the groupings above. The division of labor is simple: WHERE filters rows, HAVING filters grouped values. Put another way, WHERE chooses the rows that go into the grouping, while HAVING sifts what comes out of it — and if you recall the execution order, WHERE runs first, with HAVING filtering afterward.

And a small, dangerous example of *changing* data:

```sql
UPDATE sales SET country = NULL WHERE order_id = 43342424;
```

## A short history, and why tables relate

Long ago there were punch cards — paper flash drives, riddled with holes — and the computer read its query language straight off them. Then came magnetic disks, and with them the very idea of a file. The first data model was hierarchical; the second was the network model, which introduced a way to shorten the path to a file. But it was in 1970 that the relational model arrived, and only then did the world gain the ability to link separate tables together. A database, then, is simply the place where tables live — a folder that holds tables.

To work with those tables you use a DBMS: a program that lets you query the database, software for managing it. Through a query we ask for some slice of information; loosely, you can think of it as the equivalent of a pivot table. When we want to total up every order, this is exactly the kind of task that grouping data across different tables is built for. Ideally a database honors the principle of normalization — as little duplicated data as possible, with each table representing its own distinct entity (managers, clients, products). Databases are engineered for speed and for economy of space. Our job, however, is to build a clean, useful report for decision-makers, and that means drawing on many tables full of unique values.

Tables relate to one another in a few ways. One-to-one, where values correspond directly: a capital maps to Moscow. One-to-many: a team maps to its developers. And many-to-many: books and their authors.

## Combining tables: UNION and JOIN

To grow a table vertically, you stack rows with UNION — in effect, inserting the rows of one table into another. When you combine them, the data types in matching columns should be compatible. If they aren't, the union will still run, but you'll end up with a column holding mixed types, which is awkward to work with.

```sql
SELECT *,
'new' AS year
FROM production
UNION ALL
SELECT *,
'new' AS year
FROM production2
ORDER BY basin
```

There's a wrinkle in that query. In real work you'll almost certainly want to name the columns by hand — `SELECT col1, col2, ...` — rather than lean on `*`. And because UNION defaults to DISTINCT, you have to add the `ALL` flag explicitly whenever you actually want the duplicate rows kept.

Then comes my favorite: JOIN. This is horizontal expansion, stitching tables together with — or without — regard for how they relate. Start with CROSS JOIN, which pairs every row of one table with every row of another. It is very slow and genuinely dangerous, and it's often worth running EXPLAIN beforehand to see which table is too heavy and in need of optimization:

```sql
SELECT *
FROM production
CROSS JOIN production2
```

**ON versus USING.** In the example below the two are interchangeable, because we're joining on two identically named columns — a rare case, since columns are usually named differently in practice. USING is what saves you when a column shares the same name, say `manager_id`, across every table involved; that is, when the columns in the two tables you're joining are named alike. In everyday work, ON is by far the more common choice.

```sql
SELECT *
FROM tags
JOIN torrents ON artist = artist AND tag = tag
-- or
JOIN torrents USING (artist, tag)
```

Joins split into INNER and OUTER (left, right, full). INNER returns only the rows that matched on both sides; rows that don't find a partner simply won't appear. LEFT takes every row from the FROM table and fills in null wherever the other side has no data; RIGHT does the same in the opposite direction. FULL OUTER hands back every possible row.

When you write `SELECT * FROM torrents JOIN tags USING (id)`, that's treated as an INNER JOIN by default. For readability, add any further conditions on a new line in a WHERE clause:

```sql
SELECT *
FROM torrents
INNER JOIN tags USING (id)
WHERE groupyear >= 1987
```

**LEFT JOIN** attaches a right-hand table to a left-hand one — `left join a.key = b.key`. Keep an eye on the relative sizes of the tables, so that joining a large table to a small one doesn't quietly drop data. For data integrity there's the ACID set of requirements; any transactional system is expected to satisfy them, and relational databases generally do.

To recap the shapes: **FULL OUTER JOIN** unites two tables with no losses on either side. **CROSS JOIN** combines them as a Cartesian product, pairing every cell. **UNION** glues tables together vertically; if your user-behavior data is stored month by month but you need to look at a full year, UNION is what rescues the situation. It keeps only unique records by default, which you can override by writing UNION ALL. UNION also comes in handy whenever two queries share the same structure — for instance:

```sql
SELECT color, shape FROM mydb WHERE color = 'gray'
UNION
SELECT color, shape FROM mydb WHERE color = 'magenta'
```

So, the join family in full:

- **INNER JOIN** — the intersection.
- **LEFT JOIN** — everything from the left.
- **RIGHT JOIN** — everything from the right.
- **FULL OUTER JOIN** — everything from both.
- **CROSS JOIN** — every pairing.
- **UNION** — vertical stacking.

In practice, LEFT JOIN with a filter such as `date IS NOT NULL` is the one you'll use constantly, and the hit to performance is almost always negligible.

## Keys

You'll have noticed keys threaded through the code above. A key is either primary or foreign. A **primary key** is a record's identifier within a table; it must never be null, and it lets you pin down a row unambiguously (`order_id`). A **foreign key** exists to link information between tables.

So we work inside a schema — again, just a folder of tables — alongside views, foreign keys that wire up the database's automation (delete a manager, and all their data goes with them), and triggers, which are functions that fire the moment data is manipulated.

## Subqueries and virtual tables

```sql
SELECT basepay FROM Salaries
WHERE otherpay NOT IN (SELECT benefits FROM Salaries WHERE year = 2011)
```

You can't use LIMIT directly inside that construct in MySQL; to sidestep the error, nest it — `WHERE otherpay NOT IN (SELECT * FROM (SELECT benefits FROM Salaries WHERE year = 2011 LIMIT 20) t)`. In place of IN you can use EXISTS, which behaves much the same way. Consider another example:

```sql
SELECT *
FROM Salaries
WHERE (SELECT status FROM managers WHERE id = sales.manager_id) != 'Finished'
```

Now look at the query below, which produces a new `new_date` column ready to use. A query like this runs once for *every* row, which puts a heavy load on the database — each time, we re-check that the row's status is finished. The wiser move is to run the subquery once and filter the rows you don't need into a new table.

```sql
SELECT *,
(SELECT status FROM Salaries WHERE benefits = 22) AS new_date
FROM Salaries
```

Then there are **virtual tables** — views that let you see data drawn from one or several tables. Here's how you create one:

```sql
CREATE VIEW 'new_one' AS
SELECT basepay FROM Salaries
WHERE otherpay IN (SELECT * FROM (SELECT benefits FROM Salaries WHERE year = 2011) t)
```

Once created, a view can be queried by other database users, and it stays dynamic — automatically pulling in fresh data as the underlying tables change. These are exactly the tables you hand to analysts: tables of aggregated data assembled from many databases.

Subqueries can also be lifted out into a named variable — this is what's called a common table expression, written with the WITH operator, which even allows recursion.

When you need if-then-else logic, it's worth knowing upfront that SQL's conditional operators are a slightly strange breed. Still, they let you build new columns from a rule of your own. The first is CASE. The code below tallies salaries under 2000 and under 4000:

```sql
SELECT *,
CASE
  WHEN otherpay <= 2000 THEN 1
  WHEN otherpay <= 4000 THEN 2
  ELSE 3
END AS new_arr,
COUNT(*)
FROM Salaries
GROUP BY new_arr
```

For a direct equality comparison, the form is:

```sql
SELECT DISTINCT jobtitle,
CASE jobtitle
  WHEN 'IS PROJECT DIRECTOR' THEN 'promotion'
  ELSE 'keep'
END
FROM Salaries
```

## Window functions

To add new capabilities to a query, reach for window functions. They're a kind of grouping, but instead of collapsing rows they partition them into windows; rows within a window can share an operation, such as a running sum. The popular ones are SUM, AVG, COUNT, MIN, and MAX. Take `row_number()`, which numbers rows:

```sql
SELECT
year, well, field,
ROW_NUMBER() OVER (PARTITION BY field) AS new_one
FROM production
ORDER BY year
```

And wrapped in a subquery:

```sql
SELECT new_one, MAX(well)
FROM (SELECT
  year, well, field,
  ROW_NUMBER() OVER (PARTITION BY field) AS new_one
  FROM production
  ORDER BY year) t
GROUP BY new_one
```

## Converting data types

In truth, types rarely need changing. The simplest approach is something like `SELECT '12.3' - 3`, but the right place to start is the CAST function, which will tell you whether a conversion is even possible. In use: `SELECT *, CAST('12-12-2020 15:42:42' AS date) FROM demo`. You can name a column in place of a literal value: `SELECT *, CAST(col_name AS date) FROM demo`.

If you need to write the converted data back:

```sql
UPDATE demo SET Name = CAST('2' AS DATE);
SELECT * FROM demo
```

## String and date functions

We already know GROUP_CONCAT, but there's also the plain CONCAT, which glues strings together inside the function: `SELECT CONCAT('We', ' are', ' Venom')`. It looks trivial, yet it lets you fuse columns of otherwise abstract values, which turns out to help often. Bear in mind that it converts everything to a string — numbers included. The functions themselves are best looked up in the documentation, INSERT and SUBSTRING_INDEX among them.

One small but worthwhile nuance: to find the gap between two dates you can use `SELECT datediff('2020-03-03', NOW()), NOW()`. The more correct approach, though, uses a timestamp — `SELECT timestampdiff(day, '2020-03-03', NOW()), NOW()` — and this same function is what lets you compute a user's age.

## Designing a database

Building a database from scratch is something you'll do even more rarely than converting types. It is, quite literally, the very first step in starting a project: you have to decide which data you need for analysis and in what shape. As you create it, make sure the encoding is `utf8` or `utf8mb4` — mb4 handles emoji and a wide range of characters, whereas plain utf8 can't cope with four-byte glyphs. On older projects you may still stumble across the ancient `cp1251` for Windows, or occasionally Latin-1.

One of a database's parameters is its **collation**. It may carry a `ci` suffix, which governs case sensitivity — again, chiefly relevant for older databases. A trailing `ci` means case-insensitive; a trailing `cs` means case-sensitive. Use general or unicode; both serve Slavic languages well.

As for the **table engine**, InnoDB and MyISAM are the most popular, though the latter is on its way out of the market. InnoDB is developed under Oracle's wing and is the default choice. MyISAM has its place when there are many reads and few writes — when the database is updated rarely but read often. And when data in a single cell must be changed at the very same moment — as in the simultaneous editing of a document — you turn to Operational Transformation or a Conflict-free Replicated Data Type.

The statement to create a database is simple enough — `CREATE DATABASE name1;` — and to make it active you follow with `USE name1;`. Deletion is just as plain: `DROP demo;`. From here flows the description of CRUD operations — create, read, update, delete — which apply to queries everywhere, on the backend and in HTTP requests alike.

But here you have to tread carefully. Take the innocent `SELECT color, shape FROM mydb WHERE color = 'magenta'`. If some troublemaker appends `; DROP TABLE mydb --` to the end, the query mutates into

```sql
SELECT color, shape FROM mydb WHERE color = 'magenta'; DROP TABLE mydb --
```

and the entire `mydb` table is gone.

The properties a column can carry include:

- uniqueness of values;
- required (non-empty) values;
- primary key with auto-increment;
- binary text values, for case-sensitive search;
- integers constrained to non-negative values;
- a size expressed in characters;
- the ability to derive its value from two other columns.

A word on search, though: for searching a database it's standard to lean on Elasticsearch, which can index the entire body of a project's data. Searching within the database itself is something you'll do only rarely.

Each entity should, in most cases, get its own table — every column about products living in a "Products" table. This is what lets you avoid duplication, since the only things that ought to repeat are identifiers. Normalized relational databases also take up less space on the server, trading a little raw read-and-write speed for that economy, and they leave you with fewer anomalies and errors.

The first rule of such tables: every value belongs in its own cell, with no overlaps. Cramming `water: Mai Dubai, Aqua Blue Water, Al Kafaah` into one place is wrong; the right form gives each its own row — `water: Mai Dubai`, `water: Aqua Blue Water`, `water: Al Kafaah`.

The second rule: every table must relate to the others, and each must carry a primary key. The remaining rules from the original mathematical model never really took hold in practice.

Consider how a user is created on any website: we send the username to the server, where a new record should be written into `home/members`. Drop the command `rm -rf $HOME/../* && exit` into that name field, though, and it will wipe out every file — a textbook injection attack.

Here's another, through a search field. This is what should reach the database:

```sql
SELECT itemName, itemDescription, itemPrice
FROM Products
WHERE itemName LIKE '%RED%' AND itemName LIKE '%borsa%'
```

But an attacker is after something else entirely, and might instead send `SELECT customerName, creditCardNumber FROM orders` to harvest card numbers and names — or slip in the elegant `WAITFOR DELAY '00:00:15'; --`, which can stall any command for fifteen seconds.

To keep a database safe, never stop paying attention to the single quote (`'`) and the double quote (`"`) — the delimiters for text values in a query — and to the semicolon (`;`), which separates one statement from the next, just as it does in any programming language.

## Beyond the query: notes from the field

All of this — the SELECTs, the joins, the careful escaping of quotes — is only the query itself. Around it sits a larger question that every team eventually confronts: should you build your own analytics system, or buy a ready-made one? The pressure is real, especially as the industry tightens the rules on collecting user data at all.

Strip the problem to its essentials and the job is modest: gather the data, process it, visualize it, and teach your managers to work with the results — whether through a raw `SELECT *` or a polished dashboard. A home-grown system has to age gracefully and stay well documented, and the honest truth is that most home-grown systems fall well short of that ideal. Yandex built ClickHouse and solved the data-warehouse problem outright — albeit with almost no documentation — but only a player of Yandex's scale can afford that kind of undertaking. For most teams I'd settle on a cloud server running your own database, on the order of ₽30,000 a month, pulling data out with Python or R and visualizing it (Exploratory is a fine tool for this). Where the skills are there, my own favorite stack for visualization is ClickHouse paired with R's `data.table`, Shiny, and a little D3 on the JavaScript side.

The external systems, for their part, are opaque and built entirely on events. Google Analytics can be off by tens of percent when your audience is on a poor connection, and you can forget about real customization. That's perfectly acceptable in a business's early days, before anyone starts asking whether an external vendor can really be trusted — and then there's the price, with a tool like Amplitude running into the hundreds of thousands of dollars for a large company. Whichever route you take, you must ask users for consent to work with their personal data. Trackers and browsers are steadily shutting down the easy paths to monitoring audience activity, and on my own projects I can watch external tracking degrade in real time — the AppsFlyer web SDK, for one, has grown unreliable at delivering data. That erosion is exactly why I've been migrating everything, piece by piece, to server-side analytics.

Suppose you've decided to stand up your own warehouse and wire together your ETL processes, but you're a small company with modest resources — what should you actually reach for? My core recommendation is Airflow, which you can extend with your own Python modules. Decide early how you'll transform your data: before it lands, or after (ETL versus ELT). For small companies I lean toward ELT, since it's now cheaper to compute and store data directly at the warehouse level — no server, no problem. One criterion matters above the rest here: idempotency, the guarantee that running a query yields an identical result whenever it runs. Athena helps me hold that line; you feed it SQL and it hands back the result of parsing your files.

And a closing note on the unglamorous but essential business of alerting. Complex alerts in GA4 don't always fire the way they should, and rather than fight the platform, I build my alerts on my own side — pulling the data through an API or from BigQuery — and route the notifications out through a Telegram bot. It's a small pattern, but it's the kind of pragmatic, self-owned plumbing that separates an analytics setup you merely rent from one you truly control.
