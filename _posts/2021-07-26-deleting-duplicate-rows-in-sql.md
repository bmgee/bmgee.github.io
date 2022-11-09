---
id: 240
title: 'Deleting Duplicate Rows In SQL'
date: '2021-07-26T10:10:47-07:00'
author: 'Code Apex'
layout: post
guid: 'https://www.codeapex.com/?p=240'
permalink: /deleting-duplicate-rows-in-sql/
categories:
    - Uncategorized
---

## Situation:

There is a table in our SQL database which contains rows which we consider duplicate based upon the values in some or all of the columns.

We wish to delete these duplicate rows.

## Objective:

To demonstrate and explain a short memorable method of deleting duplicate rows in a SQL database table using a common table expression and a window function.

Specifically if columns `[col1],[col2],[col5]` define uniqueness for some table, say `dbo.Table`, the following query will delete duplicates:

`;WITH CTE AS(<br></br>SELECT ROW_NUMBER()OVER(PARTITION BY col1, col2, col5 ORDER BY col1, col2, col5) AS RN<br></br>FROM dbo.Table<br></br>)<br></br>DELETE FROM CTE WHERE RN > 1`

## Plan:

First we define which columns being the same between rows implies the rows are duplicate or in other words what columns taken together we want unique across the table.

Next we partition and enumerate each set of unique rows using a common table expression (CTE) and the window function `ROW_NUMBER()OVER()`.

What this means is we are partitioning the rows into sets of the same row and for each partition assigning a 1 to the first row in the partition, 2 to the next row in the partition, 3 in the next row in the partition, and so on; call this number RN.

Then we delete all such rows where the row number is greater than 1 (RN &gt; 1).

## Execution:

**Example Table:** `dbo.Table`

|  | col1 | col2 | col3 | col4 | col5 |
|---|---|---|---|---|---|
| 1 | Alpha | 1 | 1 | CO | 42 |
| 2 | Alpha | 1 | 1 | CO | 42 |
| 3 | Beta | 2 | 2 | MN | 29 |
| 4 | Beta | 2 | 2 | MN | 29 |
| 5 | Beta | 2 | 2 | MN | 37 |

**Define uniqueness / duplicate:**

Let’s say a certain set of columns from the table taken together should be unique across the table; say columns `[col1],[col2],[col5]`.

**Enumeration / CTE part of query:**

We create a CTE for our table that partitions the rows into sets of the same row and enumerates each partition.

The result of the CTE should look like this:

|  | col1 | col2 | col3 | col4 | col5 | RN |
|---|---|---|---|---|---|---|
| 1 | Alpha | 1 | 1 | CO | 42 | 1 |
| 2 | Alpha | 1 | 1 | CO | 42 | 2 |
| 3 | Beta | 2 | 2 | MN | 29 | 1 |
| 4 | Beta | 2 | 2 | MN | 29 | 2 |
| 5 | Beta | 2 | 2 | MN | 37 | 1 |

A CTE that accomplishes this is:

`;WITH CTE AS(<br></br>SELECT col1, col2, col5,<br></br>ROW_NUMBER()OVER(PARTITION BY col1, col2, col5 ORDER BY col1, col2, col5) AS RN<br></br>FROM dbo.Table<br></br>)`

The key part is we PARTITION BY the columns that define uniqueness.

**Deletion part of query:**

Using our CTE we delete the rows from the table where RN &gt; 1 as such rows are duplicate.

`DELETE FROM CTE WHERE RN > 1`

**Result:**

After deletion our example table would look like this:

|  | col1 | col2 | col3 | col4 | col5 |
|---|---|---|---|---|---|
| 1 | Alpha | 1 | 1 | CO | 42 |
| 2 | Beta | 2 | 2 | MN | 29 |
| 3 | Beta | 2 | 2 | MN | 37 |

## Conclusion:

For the CTE some of the columns in the SELECT portion are optional and were included for illustrative purposes only.

Specifically only the RN column is needed since the DELETE statement only works with RN.

Consequently the others can be dropped from the query without altering the end result giving us our final query:

**Final query:**

`;WITH CTE AS(<br></br>SELECT ROW_NUMBER()OVER(PARTITION BY col1, col2, col5 ORDER BY col1, col2, col5) AS RN<br></br>FROM dbo.Table<br></br>)<br></br>DELETE FROM CTE WHERE RN > 1`