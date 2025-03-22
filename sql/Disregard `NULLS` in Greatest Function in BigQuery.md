#bigquery #sql

## Problem Statement
Greatest function in BigQuery returns the greatest value among `X1,...,XN`. If any argument is `NULL`, returns `NULL`
I needed to determine the greatest value disregarding the `NULL`. 
## Solution

```sql 
create function greatest_nonnulls(arr any type) as (
	(
		select max(x) max_value
		from unnest(arr) x
		where x is not null
	)
);

-- Examples:
-- SELECT greatest_nonnulls([1, 5, NULL, 3]); -- Returns 5
-- SELECT greatest_nonnulls([NULL, NULL]); -- Returns NULL
```