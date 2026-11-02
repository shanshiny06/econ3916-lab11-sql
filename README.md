# econ3916-lab11-sql
# SQL for Economists — Joins, Windows and the Cardinality Trap

## Objective

I learned to move between pandas and SQL (via DuckDB), and I checked where the two give the same answer and where a join or a NULL quietly changes the result.

## Methodology

- I translated five pandas operations (select, filter, sort, groupby, merge) into SQL queries run in DuckDB, and I confirmed that each pair returned the same rows.
- I ran a one-to-many LEFT JOIN of customers onto a table with several rows per customer, then ran the same Welch t-test on revenue before and after the join to see how duplicated rows affect inference.
- I used window functions (AVG OVER PARTITION BY and RANK) to attach group averages and within-group ranks to each row without collapsing the table the way GROUP BY does.
- I applied the filter `WHERE income != 50000` to a 5-row survey table in SQL and the equivalent filter in pandas, and I compared how many rows each kept.
- I queried a public Parquet file of about 3 million NYC taxi trips directly over HTTP, without downloading it, so DuckDB read only the columns my query used.
- I asked an AI to translate three of my pandas steps into SQL, revised my prompt once, and compared the row counts of its queries against my pandas results.

## Key Findings

**The cardinality trap.** The one-to-many LEFT JOIN turned [YOUR VALUE] customer rows into [YOUR VALUE] rows. The same Welch t-test on the same revenue figures went from t = [YOUR VALUE] (p = [YOUR VALUE]) to t = [YOUR VALUE] (p = [YOUR VALUE]). The repeated rows made the sample look larger than it was, which shrank the standard error, and they weighted some customers more than others, which moved the difference in means. Nothing in the data changed except the row count, yet the test result did. I now check row counts before and after every join.

**Window functions.** AVG OVER PARTITION BY and RANK let me keep every row while adding group-level information. This is useful when I want to compare each observation to its group, which GROUP BY alone cannot do.

**Three-valued logic.** In SQL, `WHERE income != 50000` kept [YOUR VALUE] of 5 survey rows, because [YOUR one-line reason]. The same filter in pandas kept [YOUR VALUE]. The same-looking filter can give different samples in the two tools when missing values are present.

**Querying remote data.** I queried about 3 million taxi trips over HTTP without downloading the file. Because Parquet stores data by column, DuckDB only fetched the columns the query needed.

**AI-assisted translation.** I had an AI translate three pandas steps into SQL and revised my prompt once. Comparing its row counts to my pandas results was a quick way to check whether its queries were correct, and I treated a mismatch as a reason to read the SQL closely rather than trust it.
