## Argmax / Argmin Simplified 
Question: using table1 and table2, we want to return the person with the highest salary for each job type

Key phrases: 
- highest salary - aggregate result
- person - single field equality with aggregate 

Table1 (UserID (unique)\, Name, Job, Salary), Table2(UserID (foregin key to Table1), Car, Year)

Using sub-query to compute the maxima first then join
```sql
WITH MaxPay AS
		(SELECT t1.Job AS Job, MAX(t1.Salary) AS Salary
		FROM Table1 AS t1
		GROUP BY t1.Job)
SELECT t1.Name, t1.Salary
FROM Table1 AS t1, MaxPay AS MP
WHERE t1.Job = MP.Job AND t1.Salary = MP.Salary  /* the join of maxima and our original table*/
```
## The Punchline About Subqueries
- Subqueries can be interpreted as single values or as whole relations
	- A single value (a 1x1 relation) can be returned as part of a tuple
	• A relation can be:
		• Used as input for another query
		• Checked for containment of a value
We always start query execution with a tuple from the outer query first
## Set Operations
- Renaming: bag = multiset
- UNION (ALL) $\rightarrow$ set union (bag union)
- INTERSECT (ALL) $\rightarrow$ set intersection (bag intersection)
- EXCEPT (ALL) $\rightarrow$  set difference (bag difference
```sql
(SELECT * FROM T1)
UNION
(SELECT * FROM T2)

(SELECT * FROM T1)
INTERSECT
(SELECT * FROM T2)

(SELECT * FROM T1)
EXCEPT
(SELECT * FROM T2)
```

## Subqueries in SELECT
- must return a single value
- Uses: 
	- to compute an associated value
```sql
SELECT t1_1.Name, (SELECT AVG(t1_2.Salary)
				FROM Table1 AS t1_2
				WHERE t1_1.Job = t1_2.Job)
FROM Table1 AS t1_1
```
