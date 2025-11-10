1. `FROM`
	1. Which table(s)?
	2. If multiple: how are they connected (keys)?
2. `JOIN/ON`
	1. Add join conditions to avoid Cartesian product 
	2. Chose join type: `inner, left, right, full`
3. `SELECT`
	1. Which columns to show?
	2. Do I need all `(*)` or specific?
	3. Any calculations or aliases?
	4. Use `distinct`if needed
4. `WHERE`
	1. Filter rows.
	2. Use comparisons (`=, >, <`) and logical ops (`and, or`).
	3. Remember: `is null, like, in`
5. 