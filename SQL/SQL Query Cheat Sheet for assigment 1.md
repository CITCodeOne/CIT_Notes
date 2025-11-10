### General SQL Building Blocks
- SELECT = What columns to show
- FROM = Which table(s) to read from
- JOIN = Link tables together to combine information
- WHERE = Filter rows before grouping/aggregating
- GROUP BY = Collect rows into groups (usually per student, course, or section)
- HAVING = Filter groups after aggregation
- DISTINCT = Remove duplicates from the result
- ORDER BY = Sort rows
- LIMIT = Show only first N rows
- NOT EXISTS / EXCEPT = Logic for “doesn’t exist” or “all of these must be included”
- WITH (CTE) = Create a temporary table to make queries easier to read

### The why
##### Filtering by department
```
WHERE dept_name = 'Biology' 
```
👉 Because only rows matching that department should appear.

##### Joining tables
```
FROM takes t JOIN course c ON t.course_id = c.course_id
```
👉 takes has student enrollments, course has course titles & credits. To answer questions like “what courses does student X take?”, we must connect them.

##### Removing duplicates
```
SELECT DISTINCT …  
```
👉 Because the same student can appear multiple times (different sections/semesters), but we only want unique course listings.

##### Summing credits per student
```
SUM(c.credits) GROUP BY t.id
```
👉 We use SUM to add up credits, and GROUP BY so each student’s credits are calculated separately.

##### Finding students above a threshold
```
HAVING SUM(c.credits) > 85
```
👉 HAVING applies to aggregated results (after SUM), unlike WHERE.

##### Counting enrollments per section
```
COUNT(*) AS num GROUP BY course_id, sec_id, semester, year
```
👉 Each row in takes = one student enrollment. Counting them tells us how many students are in that section.

##### Finding maximum/minimum values
```
SELECT MAX(num), MIN(num)
```
👉 Once we have counts per section, we use MAX/MIN to find extremes.

##### Getting rows with the maximum
```
WHERE num = (SELECT MAX(num) …)  
```
👉 First find the largest enrollment, then return only rows equal to that number.

##### Including empty sections
```
LEFT JOIN takes …
```
👉 A normal JOIN ignores unmatched rows. A LEFT JOIN keeps all sections, even with 0 students.

##### Checking “never taught”
```
NOT EXISTS (SELECT 1 FROM teaches WHERE t.id = i.id)
```
👉 If no row exists in teaches, that instructor has never taught.

##### “Taught all courses” (relational division)
```
NOT EXISTS ( A EXCEPT B )  
```
👉 A = courses 19368 taught. B = courses candidate taught. If A minus B is empty, candidate taught them all.

##### Fixing stored data with calculated values
```
UPDATE student SET tot_cred = …
```
👉 Sometimes the stored total doesn’t match the real sum. We recalc from takes and fix it.

##### Updating based on counts
```
salary = 29001 + 10000 * COUNT(*)  
```
👉 We use counts (like sections taught) to compute new salary formulas.

##### Sorting alphabetically
```
ORDER BY name ASC
```
👉 Alphabetic sorting is always explicit in SQL; ASC = A → Z.


#### Quick Memory Tricks

- PK vs FK: “Primary = Passport, Foreign = Visa.” Passport is unique to you, visa shows up in another country’s system.
- JOIN = “Bring info together across tables.”
- DISTINCT = “Stop showing me clones.”
- GROUP BY = “One row per group.”
- HAVING = “WHERE for groups.”
- NOT EXISTS = “Nobody home = condition true.”
- LEFT JOIN = “Keep the left side, even if right side is empty.”
--- 
- “Why are you grouping?” → Because I need totals per student/section.
- “Why DISTINCT?” → To avoid duplicates from multiple rows.
- “Why JOIN?” → Because the info I need is split across two tables.
- “Why NOT EXISTS?” → Because I want to check the absence of something, not its presence.