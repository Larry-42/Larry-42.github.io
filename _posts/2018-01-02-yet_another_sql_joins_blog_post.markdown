---
layout: post
title:      "Yet Another SQL JOINs blog post"
date:       2018-01-02 22:57:27 +0000
permalink:  yet_another_sql_joins_blog_post
---


OK, I'll admit it, it took me a little while to understand SQL JOINs.  And even though there are [a](http://blog.seldomatt.com/blog/2012/10/17/about-sql-joins-the-3-ring-binder-model/)  [number of](https://blog.codinghorror.com/a-visual-explanation-of-sql-joins/) [good](https://www.geeksforgeeks.org/sql-join-set-1-inner-left-right-and-full-joins/) blog posts out there on JOINs, I had to play around with SQL a bit to really build a mental model of how JOINs behave. So I figure it's worth sharing yet another SQL JOIN blog post with you all. 

<br>
Let's start off with three SQL tables for a "store"--a customer table that stores the customers' names, a catalog_items table that store the names and prices of the items for sale, and an orders table that stores the customer ID and item ID for each order.  These tables are populated as seen below; feel to scroll down to the Appendix at the end of the article for the SQL to setup the tables if you want to play along:

<br>
customers:

|customers.id|customers.name|
|--|-------------------------|
|1|Mike|
|2|Sarah|
|3|John|
|4|Jennifer|

<br>
catalog_items:
	
|catalog_items.id|catalog_items.name|catalog_items.price|
|--|-------------------------|-------------------------------|
|1|widget|1.29|
|2|doohickey|2.54|
|3|thingamabob|3.48|
|4|whatchacallit|2.99|

<br>
orders:

|orders.id|orders.customer_id|orders.catalog_id|
|--|-------------------------|-------------------------------|
|1|1|1|
|2|1|3|
|3|1|2|
|4|2|2|
|5|2|3|
|6|3|1|

<br>
Anyway, on to JOINs.  A JOIN is essentially the intersection between two tables.  Normally, you JOIN ON some variable that is common to both two tables (for instance, the catalog id in the orders table and the id in the catalog_items table, as we'll see shortly).  If you don't have an ON statement, *i.e.* if you run `SELECT * FROM catalog_items JOIN orders` you get the following monstrosity back (which I'll refer to again a bit later):

<br>
|catalog_items.id|catalog_items.name|catalog_items.price|orders.id|orders.customer_id|orders.catalog_id|
|--|-------------------------|-------------------------------||--|-------------------------|-------------------------------|
|1|widget|1.29|1|1|1|
|1|widget|1.29|2|1|3|
|1|widget|1.29|3|1|2|
|1|widget|1.29|4|2|2|
|1|widget|1.29|5|2|3|
|1|widget|1.29|6|3|1|
|2|doohickey|2.54|1|1|1|
|2|doohickey|2.54|2|1|3|
|2|doohickey|2.54|3|1|2|
|2|doohickey|2.54|4|2|2|
|2|doohickey|2.54|5|2|3|
|2|doohickey|2.54|6|3|1|
|3|thingamabob|3.48|1|1|1|
|3|thingamabob|3.48|2|1|3|
|3|thingamabob|3.48|3|1|2|
|3|thingamabob|3.48|4|2|2|
|3|thingamabob|3.48|5|2|3|
|3|thingamabob|3.48|6|3|1|
|4|whatchacallit|2.99|1|1|1|
|4|whatchacallit|2.99|2|1|3|
|4|whatchacallit|2.99|3|1|2|
|4|whatchacallit|2.99|4|2|2|
|4|whatchacallit|2.99|5|2|3|
|4|whatchacallit|2.99|6|3|1|

<br>
You see every line of orders next to every line of catalog_items.  Yikes!  To actually get meaningful information, you need to JOIN ON a statement to narrow things downa bit.  For instance, `SELECT * FROM (catalog_items JOIN orders ON catalog_items.id = orders.catalog_id)` gives the following table (the bold and italic highlights are there for a reason, bear with me!):  

<br>

|catalog_items.id|catalog_items.name|catalog_items.price|orders.id|orders.customer_id|orders.catalog_id|
|--|-------------------------|-------------------------------||--|-------------------------|-------------------------------|
|***1***|*widget*|*1.29*|1|1|**1**|
|**3**|thingamabob|3.48|2|1|**3**|
|**2**|doohickey|2.54|3|1|**2**|
|**2**|doohickey|2.54|4|2|**2**|
|**3**|thingamabob|3.48|5|2|**3**|
|***1***|*widget*|*1.29*|6|3|**1**|

<br>
There are a few things that immediately jump out at you here.  First of all, notice how I had `catalog_items JOIN orders ON catalog_items.id = orders.catalog_id` in parentheses in that last SQL statement?  The parentheses aren't necessary, but they help illustrate that `some_table JOIN some_other_table ON some_table.some_row = some_other_table.some_other_row` behaves just like a table, at least where SELECTing is concerned.  Essentially, you can think of an entire JOIN statement as a table when you write your SELECT statement, to make things a bit easier on yourself.

<br>
Next, note that line catalog_items.id is the same as orders.catalog_id on every line (both columns are highlighted in bold to bring this out).  This is what `ON catalog_items.id = orders.catalog_id` does; in plain English, we are creating rows of every case where each catalog item is referenced using catalog_id in the orders table.

<br>
Finally, notice how you see the same rows from catalog_items multiple times?  For instance, I italicized both places where the first row of catalog_items (the row for the widget) appears.  There are two ways that I like to think about this.  The first way is that SQL fills in duplicates of a given row wherever they are needed to complete the JOIN.  The second way is that `ON` essentially filters from the monstrosity of a table we saw earlier for `SELECT * FROM catalog_items JOIN orders`, and filters that result to only those rows where the statement `catalog_items.id = orders.catalog_id` is true. 

<br>
Incidentally, this last bit was something I only really understood once I started playing around with SQL myself.  And it's pretty useful--you can do things like use COUNT statements to figure out how many orders were placed for widgets, or how many orders Sarah placed.

<br>
Once you understand JOINs, as described above, the rest falls into place.  You can have complex JOINs--a JOIN behaves just like a table in that you can JOIN a table to a JOIN.  Or, you can JOIN a JOIN to another JOIN if you have to.  For instance, you can JOIN the customers table to the orders table JOINed to the catalog_items table, using `SELECT * FROM catalog_items JOIN orders ON catalog_items.id = orders.catalog_id JOIN customers ON orders.customer_id = customers.id` to get the following result (the customer row, catalog row, and order row for each order):

<br>

|catalog_items.id|catalog_items.name|catalog_items.price|orders.id|orders.customer_id|orders.catalog_id|customers.id|customers.name|
|--|-------------------------|-------------------------------||--|-------------------------|-------------------------------|
|1|widget|1.29|1|1|1|1|Mike|
|3|thingamabob|3.48|2|1|3|1|Mike|
|2|doohickey|2.54|3|1|2|1|Mike|
|2|doohickey|2.54|4|2|2|2|Sarah|
|3|thingamabob|3.48|5|2|3|2|Sarah|
|1|widget|1.29|6|3|1|3|John|

<br>
One thing to keep in mind is that JOINs by default are INNER JOINs--you don't see anything that isn't in the intersection of an ON statement.  For instance, in our little example here there are no orders with a catalog_id of 4 (no one wanted a whatchacallit)  So, when we run `SELECT * FROM (catalog_items JOIN orders ON catalog_items.id = orders.catalog_id)` as described above we don't see the row with the watchacallit anywhere. 

<br>
Now, you can also have LEFT, RIGHT, and FULL OUTER JOINs in addition to INNER JOINs, as discussed in the [other](http://blog.seldomatt.com/blog/2012/10/17/about-sql-joins-the-3-ring-binder-model/) [blog](https://blog.codinghorror.com/a-visual-explanation-of-sql-joins/) [articles](https://www.geeksforgeeks.org/sql-join-set-1-inner-left-right-and-full-joins/) I linked.  These let us see rows in a given table that do not match in the `ON` statement in a JOIN. For instance, if we did want to see the row with the whatchacallit we would do a LEFT OUTER JOIN, `SELECT * FROM (catalog_items LEFT OUTER JOIN orders ON catalog_items.id = orders.catalog_id)`, giving the table below:

<br>

|catalog_items.id|catalog_items.name|catalog_items.price|orders.id|orders.customer_id|orders.catalog_id|
|--|-------------------------|-------------------------------||--|-------------------------|-------------------------------||--|-------------------------|
|1|widget|1.29|1|1|1|
|1|widget|1.29|6|3|1|
|2|doohickey|2.54|3|1|2|
|2|doohickey|2.54|4|2|2|
|3|thingamabob|3.48|2|1|3|
|3|thingamabob|3.48|5|2|3|
|4|whatchacallit|2.99|NULL|NULL|NULL|

<br>
Notice that we return the last row in the catalog_items table (the row for the widget), along with NULLs for the corresponding orders row (since the catalog_id of 4 is not present in the orders table).  For a LEFT OUTER JOIN, we return nulls for missing values and return everything in the table in the left in a JOIN statement; similarly, a RIGHT OUTER JOIN does the same thing for the table on the right, and a FULL OUTER JOIN does this for both tables in a JOIN statement.

<br>
Well, that's about it for JOINs.  Once you understand it you'll see that there's not much to it.  And I found that creating a dataset and playing around with it was what enabled me to figure out what was going on.  In fact this is an approach I've found very useful whenever I've had to learn a new concept in programming--creating and running examples just to see what happens.

<br>
**Appendix--SQL code to populate the tables referenced in this blog post:**

<br>
```
CREATE TABLE catalog_items (
    id INTEGER PRIMARY KEY,
    name TEXT,
    price REAL
);

CREATE TABLE orders (
    id INTEGER PRIMARY KEY,
    customer_id INTEGER,
    catalog_id INTEGER
);

CREATE TABLE customers (
    id INTEGER PRIMARY KEY,
    name TEXT
);

INSERT INTO catalog_items (name, price) VALUES ("widget", 1.29);
INSERT INTO catalog_items (name, price) VALUES ("doohickey", 2.54);
INSERT INTO catalog_items (name, price) VALUES ("thingamabob", 3.48);
INSERT INTO catalog_items (name, price) VALUES ("whatchacallit", 2.99);

INSERT INTO customers (name) VALUES ("Mike");
INSERT INTO customers (name) VALUES ("Sarah");
INSERT INTO customers (name) VALUES ("John");
INSERT INTO customers (name) VALUES ("Jennifer");

INSERT INTO orders (customer_id, catalog_id) VALUES (1,1);
INSERT INTO orders (customer_id, catalog_id) VALUES (1,3);
INSERT INTO orders (customer_id, catalog_id) VALUES (1,2);
INSERT INTO orders (customer_id, catalog_id) VALUES (2,2);
INSERT INTO orders (customer_id, catalog_id) VALUES (2,3);
INSERT INTO orders (customer_id, catalog_id) VALUES (3,1);
```

