## Grouping
- GROUP BY
	- purpose: to group rows based on matching attribute values
- HAVING
	- condition groups based on the top level aggregate 
	
Example: from the purchase table, we want the product column and the sum of quantity preserved  where the sum of quantity should be greater than 20
- Purchase (Product, Price, Quantity, Month)
```sql
SELECT Product, SUM(quantity) 
FROM Purcahse
GROUP BY Product
HAVING SUM(quantity) > 20 /* notice how SUM(quantity) is an aggregate of which we are here using HAVING to condition */
```
General semantics: FROM  -> WHERE -> GROUP BY  -> HAVING  -> ORDER BY  -> SELECT (FWGHOS)

## General Form of GROUP BY
```sql
SELECT S
FROM R1, ..., Rn
WHERE C1
GROUP BY a1, ..., ak
HAVING C2
```
- S = an attributes a_1, ..., a_k and/or any aggregates, but no other attributes
- C1 = any condition on the attributes in R_1, ..., R_n
- C2 = any condition on the aggregate expressions and attributes a_1, ..., a_k

## Aggregate + Joins
Question: how many cars made before 2017 does each
person drive?
- Table1 (UserID (unique)\, Name, Job, Salary), Table2(UserID (foregin key to Table1), Car, Year)

Key phrases: 
- how many....each - aggregate result (COUNT, GROUP BY)
- before 2017... each person - join tables (WHERE t1.attribute = t2.attribute)
```sql
SELECT t1.Name, COUNT(*)
FROM Table1 t1, Tabel2 t2
WHERE t1.UserID = t2.UserID AND t2.Year < 2017
GROUP BY t2.UserID, t2.Name  /* ensures that we have userID at front to preven duplciate names  */
```
notice how in the query, we ask for people with "new car" and  "people with car" dictated by "year" of the car

 **Including Empty Group**
 
Question (updated): How many cars does each person drive?
- In this question, we expect some individual from table1.UserID do not have corresponding t2.Car, making a join happening to have null values
```sql
SELECT t1.Name, COUNT(t2.UserID) as count
FROM Table1 t1 LEFT OUTER JOIN Tabel2 t2 /* t1 is the dominant table where t2 need to reference on */
ON t1.UserID = t2.UserID 
GROUP BY t2.UserID, t2.Name  
```

## Argmax/ Argmin
Question: using table1 and table2, we want to return the person with the highest salary for each job type

Key phrases: 
- highest salary - aggregate result
- person - single field equality with aggregate 

Note: SELECT, HAVING, ORDER BY must use aggregate functions or attributes in GROUP BY 

```sql
SELECT t1_1.Name, MAX(t1_2.Salary)
FROM table1 AS t1_1, table1 AS t1_2
WHERE t1_1.Job = t1_2.Job
GROUP BY t1_2.Job, t1_1.Salary, t1_1.Name /* group job, then group salary from t1 (prevent cross join effect) and then group name*/
HAVING t1_1.Salary = MAX(t1_2.Salary) /* elimnatethe lowest salary one from the name group*/
```
