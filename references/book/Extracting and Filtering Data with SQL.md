# Extracting and Filtering Data — SQL
### A working tour of the query language, from your first SELECT to designing the database

We won't dwell on how useful SQL is; let's just open [SQLite Online](https://sqliteonline.com/) and start learning the query language for talking to data. If you run into encoding trouble, check that the encoding is utf8mb4 — it holds practically everything you might need, Cyrillic and emoji included. SQL was originally a language for accountants, which is where its simplicity comes from. It has no proper loops or procedures, only queries. There is, admittedly, a Microsoft dialect with loops, but you're unlikely to need to work with it. There are subsets of the SQL language — DML, DCL, DDL — but we'll be looking at the simplest SQL.

Why do you need SQL at all, rather than staying in Google Analytics or Tableau? For typical, simple tasks those are excellent tools. But the main reason to move to SQL is the volume of data. On top of that, no one will let you upload customers' banking data into GA. And, above all, speed — writing a query is faster than writing a spec for someone to write the query. If you're already living in BI systems, data warehouses, and databases, you'll need SQL syntax everywhere: relational databases, warehouses like Hadoop, BI systems like Apache Zeppelin. And it isn't the last stop in your professional development, either — when SQL's capabilities run out, complex analytics calls for R or Python, since loops and row-by-row query iteration are very slow in SQL. There are five main types of database:

- **Relational** — where you need transactionality, high normalization, and a large share of insert operations.
- **Key–value stores**, where a large hash table holds keys and values. Examples: Riak, Amazon DynamoDB.
- **Document stores**, which hold documents made of tagged elements. Example: CouchDB.
- **Columnar stores**, where each block holds data from a single column only. Examples: HBase, Cassandra.
- **Graph-based stores** — a network database that uses nodes and edges to represent and store data. Example: Neo4J.

This raises a question that comes up constantly: should you build your own analytics system, or reach for a ready-made one — especially with the industry trending toward locking down access to user data? For typical, simple tasks GA and Tableau are excellent, and at the early stages of a business, before anyone thinks to ask "can we actually trust external vendors?", they're enough. But the external systems are opaque and built on events; GA can be off by tens of percent when the audience has a poor connection, and customization is more or less out of the question. Amplitude, meanwhile, costs *a great deal* — hundreds of thousands of dollars for a large company. On my own projects I'm watching external tracking degrade, with problems pulling data from AppsFlyer's web SDK, so I'm gradually moving everything to server-side analytics. The task itself is simple enough: collect the data, process it, visualize it, and train the managers to work with the results, whether through a plain `select *` or a dashboard.

If you do build your own, the system mustn't be allowed to age and has to be well documented — and in real life most home-grown systems fall well short of that. Yandex built ClickHouse to solve the data-warehouse problem (almost entirely without documentation, granted); a player at Yandex's level can afford that. For most, I'd settle on a cloud server at around 30k a month with your own database, pulling the data with Python or R and visualizing it in Exploratory. If your skills allow, my favorite visualization stack is ClickHouse + R (data.table) + Shiny + JS (d3).

And if you go further and stand up your own DWH with ETL processes on a small company's budget, the main advice is Airflow, into which you can write your own Python modules. Decide early how you'll transform the data — before loading or after (ETL vs ELT). For small companies I lean toward ELT, since it's now cheaper to compute and store data straight at the DWH level: no server, no problem. One criterion matters more than it seems — idempotency, where a query's result is identical to the same query run at another point in time; [Athena](https://docs.aws.amazon.com/athena/latest/ug/ddl-sql-reference.html) helps me here, taking SQL in and giving the parsed files out. And when GA4's more complex alerts refuse to fire reliably, the fix is the same instinct: pull the data yourself through the API or into BigQuery, and send the alerts through something you control — a Telegram bot, say. In any case, you must ask users for consent to process their personal data; trackers and browsers are clamping down on casual audience tracking, and that pressure isn't going away.

To begin learning you need to get hold of some data. If you don't have a suitable dataset to hand, take any database that interests you from [Kaggle](https://www.kaggle.com/datasets?fileType=sqlite) and start experimenting. As for tools, you can download the desktop MySQL — go to the [site](https://www.mysql.com/downloads/) and choose the community download; on macOS you'll want the WorkBench section. But SQLite Online is quite enough for picking up the basics. MySQL is a relational database, an excellent fit for small and medium projects.

So, the typical situation: the data is collected, and we want to get insights out of it. By "data" I mean not only tables — a database can also hold triggers, views, and procedures. We never, under any circumstances, change the data while doing analytical work. We can, however, add data. The simplest task: we want to count the number of buyers from Kirzhach in 2021. Or find the top 10 urologists on a doctor-aggregator in 2021 in a particular region. Or find every user who came from an email campaign to a landing page and made a purchase of more than 1,000₽. Or find out the hardest levels in a computer game. In short, any task involving sorting and filtering.

First steps. If we want only the unique values across two columns, a simple `SELECT` (what we want — the column or columns) `FROM` (where we want it from — the table) is enough. After `SELECT` you can list the columns in the order you want to see them: `SELECT date, session, user, status`.

```sql
select DISTINCT tm, player
FROM NBA_season1718_salary
```

There are other interesting keywords in our structured query language, structured in this writing order: `select -> from -> where -> group by -> having -> order by`. The `select` and `from` keywords are mandatory, and keeping the order is mandatory too. Execution runs along a chain: `from -> where -> select + group by -> having -> order by`. The execution order of a SQL query is `FROM -> WHERE -> GROUP BY -> HAVING -> SELECT -> DISTINCT -> ORDER BY -> LIMIT`. That's not the writing order but the execution order. SQL looks at the whole query at once and works out which table it needs to work with; it finds the `WHERE`, understands the target table, and then goes through the conditions.

Before you make any expensive mistakes, let's get `LIMIT` down straight away — a database usually has millions of rows, and printing each one can cost money, even if a single row is a single cent. `Limit 4` means printing only 4 rows from the table. You can also write `limit 5 offset 10`, which lets you skip the first 10 rows. `Limit` always goes at the very end of the query. The `*` symbol for getting all the data we use only when we have few columns (no more than 30). Some companies forbid the `*` symbol — a kind of protection against structure changes in the database, since if one of the columns changes, the query will error. You can write `SELECT * FROM INFORMATION_SCHEMA.COLUMNS` to see all the tables in all the databases.

```sql
select *
FROM NBA_season1718_salary
LIMIT 12
```

Or find out the database version with `SELECT @@VERSION`. And if you tinker a bit, the command `BENCHMARK(10000, SELECT @@VERSION)` amounts to a stability and security stress test.

Data types come in numeric and textual (phrasal) varieties. And money, of type `decimal` — exact numbers that are slow to compute. In the documentation you'll find many different data types for saving space on the hard drive, but for now people simply allow for the maximum size. Text and dates are written in quotes: `name = 'Irene', date = '2021-03-04'`. Here are some examples: `SELECT 12, true, false, 'text', '2020-03-04', 8.53, NULL`. In this example `True` is always 1 and `False` is 0. And in the example `SELECT '123.4ad3a' + '342.3da3'` the result will be 465.7, since the whole block of letters is cut off. `NULL` is the absence of a value: the row exists, but it isn't filled in.

If there's no calculator to hand, `SELECT 2+5` will work, and you can do concatenation: `concat('Today is ', name_pipe)`.

```sql
SELECT DISTINCT basin,
GROUP_CONCAT(basin)
from production
GROUP BY 1
LIMIT 20
```

![GROUP_CONCAT result](https://your-scorpion.ru/wp-content/uploads/2021/09/data.png)

If we're planning to concatenate a lot of values, we need to set `SET` before the first `SELECT` and explicitly increase the string length. For example: `SET group_concat_max_len = 18446744073709551613;`

A little to build intuition for data types: type in the string `SELECT 10 - NULL, TRUE = FALSE, 10=10` and the result is:

| 10 — NULL | TRUE = FALSE | 10=10 |
| --- | --- | --- |
| NULL | 0 | 1 |

> Any direct comparison with NULL returns NULL.

Many queries need `where` to find data — the clause `where name = '2021-04-07'`, for instance. That example is about equality; inequality can be written three different ways: `!=`, `<>`, `is not`. And for a partial match — since a city can be written all sorts of ways — the answer is `where location LIKE '%Dublin%'`.

```sql
select *
FROM NBA_season1718_salary
where season17_18 < 3345566 AND player LIKE '%Paul%' AND season17_18 != 1096080
LIMIT 12

-- Example with any two characters
SELECT year, well, field
FROM production
WHERE year LIKE "yand_"
LIMIT 20
```

`%` lets you match any number of characters, or none. So any date in 2021 is `"2020-%"`. Case doesn't matter, except in strings. Priority always goes to the `AND` operator. The principle is like multiplication in those popular order-of-operations puzzle-jokes — 2 + 2 * 2 — and SQL has the same order in 2 or 2 and 2.

```sql
select *
FROM NBA_season1718_salary
where season17_18 < 3345566 and x1 NOT IN (254, 232)
LIMIT 12

SELECT year, well, field
FROM production
WHERE year NOT IN ("2019", "2017")
LIMIT 20
```

`IN` is presence in a list — that is, filtering — and the inverse construction is `NOT IN`. A second useful condition is `between`: `where Seasons_Stats BETWEEN '2020-03-01' and '2020-01-03'` reads far more pleasantly than `>= '2020-03-01' and date <= "2020-01-03"`. Another handy condition is `OR`.

```sql
select *
FROM NBA_season1718_salary
where season17_18 IS not NULL and (player not IN ('Blake Griffin') OR season17_18 <= 2578645)
LIMIT 12
```

To pull text out of the database, use `select 'value'`. The example below prints a new column. A reminder that we don't change the values in the database itself — we simply transform the data in the result, which is a separate table for reading and analysis.

```sql
select *
, 'test_result'
FROM NBA_season1718_salary
where x1 >= 43
```

And a typical query for composing the text of an email campaign might look like this:

```sql
SELECT concat('Your order #', order_id, ', total $', total_revenue, ', has shipped. Shipping cost $', total_revenue * 0.1, '. Thank you for your order!') as message
FROM sales
where order_date = '2021-04-04'
```

An `alias` is a nickname for renaming tables and columns. The name should be meaningful, in English, use an underscore instead of a space, be lowercase, and avoid duplicating reserved words and column names. Companies will have their own guidelines for naming tables. Say we want to rename a table:

```sql
select player as new_name
FROM NBA_season1718_salary
```

The `UPPER()` and `LOWER()` transformations let you shift values to upper or lower case.

```sql
select player, UPPER (player) AS new_name
FROM NBA_season1718_salary
```

You can perform arithmetic operations — this is exactly how metrics are computed. Don't forget the `%` symbol for showing a remainder.

```sql
select x1, x1 * 2.75 as x1
FROM NBA_season1718_salary
```

A clear, simple way to get the current time and date: `SELECT TIME('now') as CURRENT_TIME`. But if we want to change the time, we'll need [modifiers](https://www.sqlite.org/lang_datefunc.html) — a very common task. You can also add a date to make clear the exact moment the data was pulled from the database:

```sql
SELECT *
,datetime('now', 'start of month', '-59 minutes') as new_date
from
NBA_season1718_salary
```

Sorting with `order by`: `asc` is ascending; `DESC` is descending. A simple example:

```sql
SELECT *
FROM NBA_season1718_salary
ORDER BY player ASC
```

When sorting by several columns at once, the sort on the first parameter fires first, and the second parameter sorts within the data already sorted by the first. An example, in the table:

| Name | |
| --- | --- |
| A.J. Hammons | 2 |
| Aaron Brooks | 3 |
| Abdel Nader | 4 |
| Amir Johnson | 5 |
| Bruno Caboclo | 1 |
| Cody Zeller | 2 |
| DeAndre Liggins | 3 |

Extracting the `TOP n` records:

```sql
SELECT * FROM NBA_season1718_salary
order by season17_18 DESC
LIMIT 20

-- or

SELECT *
from production
ORDER by state DESC
LIMIT 15
```

## Conditions

The `CASE` operator is an iterator for checking conditions and returning a result.

```sql
select x1,
	CASE
    when x1 < 3 THEN x1 % 3
	when x1 = 5.5 THEN 'condition two'
    when x1 > 3 THEN x1 % 5
    else 'piece of crap'
    end AS result_calc
FROM NBA_season1718_salary
```

Aggregation functions: to pick the smallest value from a table, we write `SELECT MIN(season17_18) FROM NBA_season1718_salary`, or swap `MIN` for `MAX` to get the opposite. More interesting is grouping by the arithmetic mean, `AVG` — the average across the whole column. To count an average, `SELECT avg`. And we can total up all the orders: `SELECT sum(sales) from production`.

The aggregation function `COUNT()` can count the number of values in a column — `SELECT COUNT (*) FROM NBA_season1718_salary`, or another example, `SELECT COUNT(*) as total_nmb from production`. But it won't count `NULL` as a value. In this simple way you can count the number of applications handled by a single manager. And `COUNT (DISTINCT x1)` counts the unique values of one column. `DISTINCT` is about unique values — it removes all the repeated rows. Here's a useful example in which we compute the average total cost twice:

```sql
SELECT COUNT(*) as total_nmb,
SUM (total_price) as all_data,
SUM (total_price) / COUNT(*),
AVG(total_price)
from production
```

But… `Group by` — grouping data. We pull some column and collapse the identical values into one. You have to name the column or its ordinal number. `GROUP BY` is far more interesting than `DISTINCT`: it has aggregation functions. `Group by` glues the data together; `DISTINCT` only prints the target column.

```sql
SELECT player, MIN(season17_18) AS min_season, SUM(season17_18) AS sum_season, COUNT(season17_18) AS count_season
FROM NBA_season1718_salary
GROUP BY tm
HAVING season17_18 and min_season <= 83312219 and count_season != 14

-- or

SELECT month, environment, year, month
from production
GROUP BY 1
LIMIT 20
```

In the example above, `Group by 1` means the number of the column to group by. Developers dislike this approach; analysts rather love it. Also, the first example above has an ambiguity in how `MIN/MAX` is read. Look at this code:

```sql
SELECT basin, field, installation, MAX(year), MIN (year)
from production
LIMIT 20
```

We'll get a single row — but whether it carries the max or the min value is anyone's guess. Unclear, which is why subqueries are the more correct tool.

```sql
SELECT *
from production
WHERE year = (SELECT MAX(year) FROM production)
LIMIT 20
```

The second subtlety: if you write the query

```sql
SELECT basin, avg(year)
from production
LIMIT 20
```

the result will print the first value of the `basin` column, and to get the list of all the values that took part in the calculation you have to specify `GROUP_CONCAT` — which we did a little earlier. So, writing a query of the form `SELECT environment, COUNT(*) from production GROUP BY environment`, we get a count by `environment`.

![Count by environment](https://your-scorpion.ru/wp-content/uploads/2021/09/sea_or.png)

`HAVING` is the keyword for filtering grouped values. It helps filter the results of the groupings above: `WHERE` filters rows, while `HAVING` filters grouped values. The difference between `WHERE` and `HAVING`: `WHERE` selects the rows for grouping, `HAVING` selects among the results of the grouping. Recalling the execution order of queries, `WHERE` comes first, and `HAVING` filters afterward.

And a small and dangerous example of changing data:

```sql
UPDATE sales SET
country = NULL
WHERE order_id = 43342424;
```

## Grouping data

In the old days there were punch cards (paper flash drives with little holes), and the computer read the query language off them. Then came magnetic disks, and with them the notion of a file. The first data model was hierarchical, the second was the network model, in which it became possible to shorten the path to a file. But in 1970 the relational data model appeared, and that's when the world gained the ability to link different tables together. So, a database is the place where tables are stored — a folder that holds tables.

To work with the tables you use a DBMS: a program that lets you make queries against the database — software for managing the database. With a query we ask for some piece of information. To simplify, you can think of it as an analog of pivot tables. We want to total up all the orders — and it's for tasks like that that you need to group data from different tables. Ideally the database should follow the principle of normalization: as little duplication as possible, and every table should have its own entity (managers, customers, products). Databases are designed for fast operation and for saving space. But our task is to build a beautiful, useful report for decision-makers, which means we have to use many tables with unique values.

The kinds of relationship between tables: one-to-one, where values correspond to each other directly — capital -> Moscow. Another kind is one-to-many: team -> developers. And many-to-many: books and authors.

For extending a table vertically, row-wise combination with `Union` is the fit. In effect we simply insert the data from one table into another. When combining, the data types in matching columns must be compatible. But if the data types happen to differ, the combination will still work — you'll get a column with mixed data types, which is inconvenient to work with.

```sql
SELECT *,
'new' as  year
FROM production
UNION ALL
SELECT *,
'new' as  year
FROM production2
ORDER by basin
```

There's a subtlety in the query above. In real work you'll most likely need to spell out the columns by hand: select col1, col2. `union` defaults to `distinct`, so if you want duplicate rows in the table, you explicitly write the `all` flag.

![UNION result](https://your-scorpion.ru/wp-content/uploads/2021/10/Group-156.png)

Next comes my beloved `JOIN` — the horizontal extension of tables, with regard to their relationship (or without). Let's start with `CROSS JOIN`, which matches every row of one table with every row of another. This is a very slow and dangerous kind of `join`. Sometimes, before running such a query, you need `Explain` to work out which table is too heavy and in need of optimization:

```sql
SELECT * FROM production, production2
cross JOIN
```

`ON` and `Using`: in the following example they'll be interchangeable, since we're joining on two identical columns. Rare as this case is, in practice columns are named differently. `USING` bails us out when there's a column with the same name, `manager_id`, across all the tables — that is, when the columns in the two joined tables are named the same. In practice `ON` is used most often.

```sql
SELECT *
FROM tags
JOIN torrents ON artist = artist and tag = tag
-- or
JOIN torrents USING (artist, tag)
```

`INNER ()` and `OUTER JOIN (left, right, full)`. `INNER` returns only the rows that intersected; rows that didn't match won't be shown in the table. `left` takes all the rows from `from` and adds `null` where there's no data; `right` works in the opposite direction. `full OUTER` yields all possible data.

When we write `SELECT * FROM torrents JOIN tags USING (id)`, that's considered `INNER JOIN` by default. For readability, the conditions are added on a new line via the `where` section:

```sql
SELECT *
FROM torrents
INNER JOIN tags USING (id)
WHERE groupyear >= 1987
```

![INNER JOIN result](https://your-scorpion.ru/wp-content/uploads/2021/10/Group-159.png)

`LEFT JOIN` — we attach the right table to the left one: `left join a.key = b.key`. It's important to keep the tables' sizes in mind, so that joining a large table with a small one doesn't lose data. There's a set of ACID requirements for data integrity; any transactional system must meet them, and relational databases usually do.

`FULL OUTER JOIN` — the two tables are combined with all their values, without loss. `CROSS JOIN` combines on the principle of a Cartesian product — every cell gets a pair. `UNION` can glue tables together vertically. If your data on user behavior is stored by month and you need to see the statistics for a year, this is where `UNION` saves the day. By default it keeps only the unique records, but you can avoid that by adding `UNION ALL`.

The `UNION` command also comes in handy when you have two queries identical in structure. Like this: `SELECT color, shape FROM mydb WHERE color='gray' UNION SELECT color, shape FROM mydb WHERE color='magenta'`.

So, the kinds of join:

- INNER JOIN (inner)
- LEFT JOIN (left)
- RIGHT JOIN (right)
- FULL OUTER JOIN (full)
- CROSS JOIN (cross)
- UNION (union)

In practice, `LEFT JOIN` with filtering is used constantly — `date IS NOT NULL`, say — and the performance hit is almost always negligible.

Now let's talk about the keys you may have noticed in the code above. A key can be primary or foreign. The primary key is the identifier of a record in a table and must not be null; by this key a record in the table can be uniquely identified (order_id). And the foreign key is needed for linking information between tables.

So, we work with a database (scheme), which is simply a folder with tables. There are also views, foreign keys for setting up database automation (delete a manager, delete all their data), and triggers as functions that fire at the moment data is manipulated.

## Subqueries

```sql
SELECT basepay FROM Salaries
where otherpay NOT IN (SELECT benefits FROM Salaries WHERE year = 2011)
```

In the example above you can't use `LIMIT` in MySQL; to get around that error, use `where otherpay NOT IN (select * from (SELECT benefits FROM Salaries WHERE year = 2011 LIMIT 20) t)`. Instead of `IN` you can use `EXISTS`, which behaves quite similarly. Let's take apart another example:

```sql
SELECT *
FROM Salaries
WHERE (SELECT status FROM managers where id = sales.manager_id) !='Finished'
```

And now look at the example below, in which we get a new column `new_date` that can be used. A query like this runs for every row, which puts a heavy load on the database — each time, we recheck that every row has status `= finished`. The more correct way is to run the subquery once and filter out the rows you don't need into a new table.

```sql
SELECT *,
(SELECT status FROM Salaries WHERE benefits = 22) as new_date
FROM Salaries
```

And virtual tables: tables that let you view data across only one or several tables. Here's an example of creating one:

```sql
CREATE VIEW 'new_one' as
SELECT basepay FROM Salaries
where otherpay IN (select * from (SELECT benefits FROM Salaries WHERE year = 2011) t)
```

Once created, such a database can be accessed by other database users, and the table is dynamic — it automatically pulls new data from the database. It's exactly these that get handed to analysts: tables with aggregated data from many databases.

Subqueries can be moved into a variable — this is called common table expressions. We use the `with` operator, which allows recursion.

If we need `if-then-else`, we have to understand that the conditional-logic operators are rather strange. Even so, you can create new columns based on some logic. The first operator is `CASE`. With this code we count the number of salaries under 2000 and under 4000.

```sql
SELECT *,
CASE
when otherpay <= 2000 THEN 1
when otherpay <= 4000 THEN 2
else 3
end as new_arr,
COUNT (*)
from Salaries
GROUP by new_arr
```

For direct comparison the code is (`=`):

```sql
SELECT DISTINCT jobtitle,
CASE jobtitle
WHEN 'IS PROJECT DIRECTOR' THEN 'promotion'
ELSE 'keep'
END
FROM Salaries
```

To add new functions to the database, we can use window functions. This is a kind of grouping, but it divides the rows into windows; the combined rows can share actions, like computing a sum. The popular window functions are `SUM` / `AVG` / `COUNT` / `MIN` / `MAX`. Let's look at `row_number()` for numbering rows. For example:

```sql
SELECT
year, well, field,
ROW_NUMBER() over(partition by field) as new_one
FROM production
ORDER by year
```

And we put it in a subquery:

```sql
SELECT new_one, MAX(well)

from (SELECT
year, well, field,
ROW_NUMBER() over(partition by field) as new_one
FROM production
ORDER by year) t

group by new_one
```

## Converting data types

In truth, types are rarely changed. The simplest option is `SELECT '12.3'- 3`, but you should start with the `CAST` function, which can tell you whether a type conversion is possible. Example: `SELECT *, CAST('12-12-2020 15:42:42' as date) FROM demo`. Instead of a value you can name a column: `SELECT *, CAST(col_name as date) FROM demo`.

If you need to add data, here's an example:

```sql
UPDATE demo SET Name = CAST ('2' as DATE);
SELECT * FROM demo
```

String functions: we already know `GROUP_CONCAT`, but there's also the plain `CONCAT`, which glues strings together inside the function — `SELECT CONCAT ('We',' are', ' Venom')`. A simple operation on the face of it, but it lets you glue together columns with abstract values, which often helps. Note that such code converts everything to a string, numbers included. Functions are best looked up in the documentation — ones like `INSERT` and `SUBSTRING_INDEX`.

One small subtlety I'd like to mention: to understand the difference between two dates, you can use `SELECT datediff ('2020-03-03', NOW()), NOW()`. But the more correct way is `timestamp` — `SELECT timestampdiff (day, '2020-03-03', NOW()), NOW()` — a function that will help you compute a user's age.

## Designing a database

Building a database yourself comes up even less often than converting types. Creating a database is literally the very first step in starting a project. What matters is deciding which data you need for analysis and in what form. When creating it, make sure the encoding is `utf8` or `utf8mb4`. `mb4` can do smileys and many symbols; `utf8` doesn't support 4-byte characters. Sometimes on older projects you'll find the fairly ancient `cp1251` for Windows, and occasionally you'll run into `Latin-1`.

One of a database's parameters is Collation. It may carry a `ci` suffix, which governs the case-sensitivity of characters — again, relevant to old databases. A `ci` at the end of the encoding means case-insensitive; `cs` at the end means case-sensitive. We use `general` or `unicode`, which are fine for Slavic languages.

The table engine: InnoDB and MyISAM are the most popular, though the latter is already leaving the market. InnoDB is developed under Oracle's wing, and by default we choose it. MyISAM can be used when there are many reads and few writes — when the database is updated rarely but read often. If you need to change data in a single cell at the same time — during simultaneous editing of a document, say — we use Operational Transformation or a Conflict-free Replicated Data Type.

The query for creating a database is fairly simple — `CREATE DATABASE name1;`. And to activate the database, next we write `USE name1;`. For deletion, `DROP demo;` — it's all straightforward. From this comes the description of the `CRUD` operations (create, read, update, delete), which apply to any queries, including on the back end and in HTTP requests.

But here you have to be careful. Take the simple query `SELECT color, shape FROM mydb WHERE color='magenta'` — if some hooligan adds `; DROP TABLE mydb --` to the end, the query turns into the following malicious code:
`SELECT color, shape FROM mydb WHERE color='magenta'; DROP TABLE mydb --'`
and the whole mydb table is deleted.

The possible column properties:

- uniqueness of values
- values being mandatory
- primary key + auto-increment
- binary text values (case-sensitive search)
- integer data not allowed to be negative
- size in characters
- the ability to derive a value from two other columns

As for search, though: for searching a database it's customary to use Elasticsearch, which can index the whole project's data. Within the database itself, search is rarely performed.

Every entity should have its own table (in most cases) — that is, all the columns about products go in the "Products" table. This avoids duplication, since only identifiers should be duplicated. Relational databases also take up less space on the server, and read and write speeds improve. There are fewer anomalies and errors, too.

The first rule of such tables: every value must sit in its own cell, with no overlaps. `water: Mai Dubai, Aqua Blue Water, Al Kafaah` — that's wrong. Right: `water: Mai Dubai`, `water: Aqua Blue Water`, `water: Al Kafaah`.

The second rule: all the tables must be linked to one another, and every table must have a primary key. The remaining rules didn't take hold in real practice, though they were in the original mathematical model.

Here's how user creation works on any website: we send the username to the server, where a new record should be created in home/members. In the text field for the name, someone can enter the command `rm -rf $HOME/../* && exit` — and that will delete every file, which is a SQL injection attack.

Another example, with a search field. This command goes off to the database:

```sql
SELECT itemName, itemDescription, itemPrice
FROM Products
WHERE itemName LIKE '%RED%' AND itemName LIKE '%borsa%'
```

But since an attacker is interested in something else, they might write the command `SELECT customerName, creditCardNumber FROM orders` and obtain data on credit cards and names. Or another lovely command, `WAITFOR DELAY '00:00:15'; --`, which can postpone the execution of any commands by 15 seconds.

For database security, don't forget to pay attention to single-quote (`'`) and double-quote (`"`) characters — the delimiters of text values in queries. And `;` separates queries, as in any programming language.
