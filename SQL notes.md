### Introduction

SQL is known as Structured Queray Language 
- Declarative language   → You say *what* you want, not *how* to compute it. 
- Developed in the 1970s (IBM).
- Stadardized (SQL-92 → SQL:2023). Different DBMS add features. 

*Think of SQL as ordering food: you say pizza (what), not how to cook it*

--- 

### SQL Components
- DDL (Data Definition Language): structure → CREATE, ALTER, DROP
- DML (Data Manipulation Language): data → SELECT, INSERT, UPDATE, DELETE.
- Integrity constraints: rules for consistency. 
- Views: virtual tables from queries.
- Transactions: Begin - commit - rollback
- Authorization: who can do what.

---

### Data definition (DDL)

Create Table
```
 create table instructor (
  ID char(5),
  name varchar(20) not null,
  dept_name varchar(20),
  salary numeric(8,2),
  primary key(ID),
  foreign key(dept_name) references department
);
```

Data types
> char(n), varchar(n), numeric(p,d), int, float, date, time, timestamp, interval.

Constraints
> primary key = unique & not null. 
> foreign key = must exist in another table. 
> not null = value required. 
> unique = values must differ (but can be null).
> check = enforce condition.

---

### Basic Queries (DML)

**Structure**
```
select A1, A2
from R1, R2
where condition;
```

> **SELECT** = what to show (columns).
> **FROM** = which tables.
> **WHERE** = filter rows.

--- 
### Select Clause

> * = all columns.
> distinct = remove duplicates
> arithmetic = salary/12 as monthly_

---
### Where Clause

> Comparisons <, >, <=, >=, =, <>.
> Logical ops: and, or, not. 
> Special: is null, is not null, like, ilike.

---
### Joins

> Inner join = only matching rows
> Natural join = auto-match by same column names (risk)
> Outer joins
- Left = all from the left, fill missing with null
- Right = all from right. 
- Full = keep all from both. 

*Cartesian product = “everyone shakes hands with everyone. Only useful when filtered*

---
### Rename (Aliases)

```
select i.name
from instructor as i;
```

Useful for self-joins.

---

### Ordering

```
select name
from instructor
order by dept_name, name desc;
```

---

### Set Operations
> union = combine, no duplicates
> union all = keep duplicates
> intersect = common rows
> except = difference

--- 
### Null Values 
> null = unknown/missing
> Logic: true, false, unknown
> Must test with is null
> Aggregates ignore nulls (except count( * )).

--- 
### Aggregates
`avg, sum, min, max, count`

With group by:
```
select dept_name, avg(salary)
from instructor
group by dept_name;
```

With having:
```
... having avg(salary) > 70000;
```

--- 
### Subqueries

Is a query inside another query 
Use in `select, from, or where`

Example
```
where salary > (select avg(salary) from instructor);
```

> Varients: `in, not in, exists, some, all`

---
### Views
Views is a virtual table = a saved query.

Example:
```
create view faculty as
select ID, name, dept_name
from instructor;
```

**Materilized views** = stored physically (need refresh).

---
### Transactions
> Group of queries/updates = all or nothing
> Commands
- `begin`(or start `transaction)
- `commit` (save changes)
- `rollback (undo changes)

*Bank transfer = classic example; money leaves one account AND enters another, or nothing happens*

--- 
### Authorization
> Privileges: `select, insert, update, delete`

**Grant/revoke**
```
grant select on department to user1;
revoke select on student from user2;
```

**Roles**: group of persmissions: 
```
create role instructor;
grant instructor to Amit;
```
