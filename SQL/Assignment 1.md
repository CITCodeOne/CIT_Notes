#### -- 1. Find the names of all the instructors from Biology department 
1. We want names `FROM`the instructors (find instructor table).
2. We only want instructors from the Biology department, create filter `WHERE dept_name = 'Biology'
3. `SELECT` we only want the name

```
SELECT name
FROM instructor
WHERE dept_name = 'Biology';
```

#### -- 2. Find the names of courses in Computer Science department which have 3 credits
1. Find the course info, in course table `FROM`
2. Filter rows `WHERE` 'Computer Science'
3. New filter `WHERE` credits = 3 
4. `SELECT` title

```
SELECT title 
FROM course
WHERE dept_name = 'Computer science’ AND credits = 3;
```

#### -- 3. For the student with ID 30397, show all course_id and title of all courses registered for by the student.
1. The takes table links the students with the sections they take
2. The course table has the titles 
3. Use `JOIN` to join takes with course on the course_id.

```
SELECT DISTINCT t.course_id, c.title
FROM takes t
JOIN course c ON t.course_id = c.course_id
WHERE t.id = '30397';
```

1. t is a shortcut (alias) for `takes`
2. c is a shortcut for `course`
3. `JOIN`is because we can 'borrow' the course title from the course table. 
4. `DISTINCT` to make sure that courses are only shown once, even if the students are registered for it twice.

#### -- 4. As above, but show the total number of credits for such courses (taken by that student).
1. Same concept as before, but add the credits column from course
2. Use `SUM(credits)` 
3. Group by each course. (Because we don't want the total sum)

```
SELECT t.course_id, c.title, SUM(c.credits) AS sum
FROM takes t
JOIN course c ON t.course_id = c.course_id
WHERE t.id = '30397'
GROUP BY t.course_id, c.title;
```

#### -- 5. Display the total credits over all courses for each of the students having more than 85 in total credits, along with the id of the student
1. We need the tables `takes` and `course`
2. `takes`lists each student's registrations; `course`gives credit values.
3. `GROUP BY` t.id gives one row per student; `SUM` adds their credits. 
4. `HAVING`filters after aggregation (vs `WHERE`, which is before). 

```
SELECT t.id, SUM(c.credits) AS sum
FROM takes t
JOIN course c ON c.course_id = t.course_id
GROUP BY t.id
HAVING SUM(c.credits) > 85
ORDER BY t.id;
```

#### -- 6. Names of students who took any Language course with grade 'A+' (no duplicates).
1. We need tables, `student`, `takes` and `course`
2. Find registrations in `takes` with grade = 'A+'
3. Make sure department is only languages. Use `course.dept_name`
4. Use `JOIN`for `takes`and `course`. 
5. `DISTINCT` is used to avoid duplicates. 

```
SELECT DISTINCT s.name
FROM student s
JOIN takes   t ON t.id = s.id
JOIN course  c ON c.course_id = t.course_id
WHERE c.dept_name = 'Languages'
  AND t.grade = 'A+'
ORDER BY s.name;
```

#### -- 7. IDs of Marketing instructors who never taught.
1. We need tables `instructor`and `teachers`
2. Where is our department, in this case `i.dept_name`, is marketing.
3. We need to exclude those that appears in `teachers` (who has taught). 
4. Use `NOT EXISTS` 
	1. Anti-join pattern

```
SELECT i.id
FROM instructor i
WHERE i.dept_name = 'Marketing'
  AND NOT EXISTS (
    SELECT 1
    FROM teaches t
    WHERE t.id = i.id
  )
ORDER BY i.id;
```

#### -- 8. Same as question 7, but now show name
1. Same concept, just add a new condition. 

```
SELECT i.id, i.name
FROM instructor i
WHERE i.dept_name = 'Marketing'
  AND NOT EXISTS (
    SELECT 1
    FROM teaches t
    WHERE t.id = i.id
  )
ORDER BY i.id;
```

#### -- 9. Student count per section in 2009
1. We need table `takes` 
2. We need to define that it is only from 2009.

```
SELECT
  t.course_id,
  t.sec_id,
  t.semester,
  t.year,
  COUNT(*) AS num
FROM takes t
WHERE t.year = 2009
GROUP BY t.course_id, t.sec_id, t.semester, t.year
ORDER BY t.course_id, t.sec_id, t.semester, t.year;
```

#### -- 10. Max/Min enrollment across all sections
1. Same set up from question 9, but without year filter. 
2. Add MAX/MIN

10a with a subquery in FROM
```
SELECT MAX(en.num) AS max, MIN(en.num) AS min
FROM (
  SELECT t.course_id, t.sec_id, t.semester, t.year, COUNT(*) AS num
  FROM takes t
  GROUP BY t.course_id, t.sec_id, t.semester, t.year
) AS en;
```

10b with a WITH (CTE)
```
WITH en AS (
  SELECT t.course_id, t.sec_id, t.semester, t.year, COUNT(*) AS num
  FROM takes t
  GROUP BY t.course_id, t.sec_id, t.semester, t.year
)
SELECT MAX(num) AS max, MIN(num) AS min
FROM en;
```

Notes
Building the `per-section counts` (derived table/CTE) automatically excludes empty sections because an empty section has no rows in takes. 

Aggregate with MAX/MIN over those counts.

#### -- 11. Sections that had the maximum enrollment.
1. A section is uniquely defined by (`course_id`, `sec_id`, `semester` and `year`).
2. Same as question 9, but now we want sections with the largest count. 

11a — Subquery in `FROM`
```
SELECT en.course_id, en.sec_id, en.semester, en.year, en.num
FROM (
  SELECT t.course_id, t.sec_id, t.semester, t.year, COUNT(*) AS num
  FROM takes t
  GROUP BY t.course_id, t.sec_id, t.semester, t.year
) AS en
WHERE en.num = (
  SELECT MAX(num)
  FROM (
    SELECT COUNT(*) AS num
    FROM takes
    GROUP BY course_id, sec_id, semester, year
  ) x
);
```

11b: With a CTE (`WITH`)
```
WITH en AS (
  SELECT t.course_id, t.sec_id, t.semester, t.year, COUNT(*) AS num
  FROM takes t
  GROUP BY t.course_id, t.sec_id, t.semester, t.year
),
mx AS (
  SELECT MAX(num) AS maxnum FROM en
)
SELECT e.course_id, e.sec_id, e.semester, e.year, e.num
FROM en e
JOIN mx ON e.num = mx.maxnum;
```

1. Students are counted per section by using `GROUP BY`
2. Find the largest count `MAX`
3. Match only section rows that equal that max

#### -- 12. Max/Min enrollment including sections with 0 students
1. Treat empty sections as 0; use outer join + aggregation

```
SELECT MAX(en.num) AS max, MIN(en.num) AS min
FROM (
  SELECT s.course_id, s.sec_id, s.semester, s.year,
         COUNT(t.id) AS num
  FROM section s
  LEFT JOIN takes t
    ON t.course_id = s.course_id
   AND t.sec_id    = s.sec_id
   AND t.semester  = s.semester
   AND t.year      = s.year
  GROUP BY s.course_id, s.sec_id, s.semester, s.year
) AS en;
```

1. `LEFT JOIN`ensures we keep sections with no matching `takes`rows; `COUNT(t.id)` counts only non-nulls, giving 0 for empties. 

#### -- 13. All courses taught by instructor id '19368'
1. Have to filter by id.
2. Put rows in order
```
SELECT t.id, t.course_id, t.sec_id, t.semester, t.year
FROM teaches t
WHERE t.id = '19368'
ORDER BY t.course_id, t.sec_id, t.semester, t.year;
```

#### -- 14. Instructors who have taught all the courses that '19368'
1. We are using a **relational division**
2. Let A be the *set of sections* taught by instructor 19368.
3. A section is identified by (`course_id`, `sec_id`, `semester`, `year`) IMPORTANT, must compare all four. 

```
SELECT i.id
FROM instructor i
WHERE NOT EXISTS (
  -- courses taught by 19368
  (SELECT t.course_id, t.sec_id, t.semester, t.year
   FROM teaches t
   WHERE t.id = '19368')
  EXCEPT
  -- courses taught by candidate i
  (SELECT t2.course_id, t2.sec_id, t2.semester, t2.year
   FROM teaches t2
   WHERE t2.id = i.id)
)
ORDER BY i.id;
```

#### -- 15. Insert each instructor as a student (same dept)
1. `tot_cred = 0` 
2. We copy `student`from `instructor`
3. The `NOT EXISTS`guard avoids clobbering any pre-existing student rows that happen to share an id. 

```
INSERT INTO student (id, name, dept_name, tot_cred)
SELECT i.id, i.name, i.dept_name, 0
FROM instructor i
WHERE NOT EXISTS (
  SELECT 1 FROM student s WHERE s.id = i.id
);
```

#### -- 16. Delete the newly added "students" (but not any ezisting legitimate students with `tot_cred = 0`)
1. Use `DELETE FROM` 

```
DELETE FROM student s
WHERE EXISTS (SELECT 1 FROM instructor i WHERE i.id = s.id);
```

*Info* — mine deleted 50

#### -- 17. Compare stored `tot_cred` vs. recomputed credits (show only matches)
1. Build passed totals
2. Compare to `student.tot_cred` 
3. Filter to equal values

```
WITH passed AS (
  SELECT t.id, COALESCE(SUM(c.credits), 0) AS sum
  FROM takes t
  JOIN course c ON c.course_id = t.course_id
  WHERE t.grade IS NOT NULL
    AND t.grade <> 'F'
  GROUP BY t.id
)
SELECT s.id, s.tot_cred, p.sum
FROM student s
JOIN passed p ON p.id = s.id
WHERE s.tot_cred = p.sum
ORDER BY s.id;
```

#### -- 18. Fix all students' `tot_cred`to the correct computed total 
1. Resue the same definition of 'passed'
2. If a student has no passed courses, COALESCE(...,0) sets them to 0.

```
WITH passed AS (
  SELECT t.id, COALESCE(SUM(c.credits), 0) AS sum
  FROM takes t
  JOIN course c ON c.course_id = t.course_id
  WHERE t.grade IS NOT NULL
    AND t.grade <> 'F'
  GROUP BY t.id
)
UPDATE student s
SET tot_cred = COALESCE(p.sum, 0)
FROM passed p
WHERE p.id = s.id;
```

*Info* my program says UPDATE 1997 and not 2000

#### -- 19. Re-run Q17, but now show only differences

1. Same as Q17, but now we filter where they dont match. 
```
WITH passed AS (
  SELECT t.id, COALESCE(SUM(c.credits), 0) AS sum
  FROM takes t
  JOIN course c ON c.course_id = t.course_id
  WHERE t.grade IS NOT NULL
    AND t.grade <> 'F'
  GROUP BY t.id
)
SELECT s.id, s.tot_cred, COALESCE(p.sum, 0) AS sum
FROM student s
LEFT JOIN passed p ON p.id = s.id
WHERE s.tot_cred <> COALESCE(p.sum, 0)
ORDER BY s.id;
```

#### -- 20. Update each instructor's salary to 29001 + 10000 * (#sections taught)

```
UPDATE instructor i
SET salary = 29001 + 10000 * COALESCE(tc.cnt, 0)
FROM (
  SELECT id, COUNT(*)::int AS cnt
  FROM teaches
  GROUP BY id
) AS tc
WHERE i.id = tc.id;
-- then set the zero-teachers:
UPDATE instructor
SET salary = 29001
WHERE id NOT IN (SELECT id FROM teaches);
```

#### -- 21. List name and salary for the first 10 instructors in alphabetical order
1. We read from `instructor`
2. `ORDER BY`name sorts alphabetically
3. `LIMIT 10` takes the first ten after sorting. 

```
SELECT name, salary
FROM instructor
ORDER BY name ASC
LIMIT 10;
```
