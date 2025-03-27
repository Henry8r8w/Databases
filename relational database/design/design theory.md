## Relationships Examples
- **one-one**: SSN - driver license
- **one-many**: SSN - phone number
- **many-many**: store - product
- **is-a**: computer - PC and computer - Mac
- **has-a**: country - city
Knowing the relationships well can help you solve problem caused by table redundancy
## Table Redundancy
Think about this: there is a table that
- Hold information about name, SSN, phone, and city
- Associate people with the city they live in
- Associate people with any phone numbers they have

| Name | SSN         | Phone        | City          |
| ---- | ----------- | ------------ | ------------- |
| Fred | 123-45-6789 | 206-555-9999 | Seattle       |
| Fred | 123-45-6789 | 206-555-8888 | Seattle       |
| Joe  | 987-65-4321 | 415-555-7777 | San Francisco |

Anomalies:
- We see the redudancy of the table. This imply that the update would be slow.
- We see that the only difference in Freds' entry is the phone. How would one delete just one of the phone

To avoid it, we can break down the table

| Name | SSN         | City          |
| ---- | ----------- | ------------- |
| Fred | 123-45-6789 | Seattle       |
| Joe  | 987-65-4321 | San Francisco |

| SSN         | Phone        |
| ----------- | ------------ |
| 123-45-6789 | 206-555-9999 |
| 123-45-6789 | 206-555-8888 |
| 987-65-4321 | 415-555-7777 |

## Functional Dependencies (FDs)
- Definition: 
$A_1,..., A_m \rightarrow B_1, ..., B_n$ holds in the relation if
$$\forall t, t' \in R, (t.A_1 = t'.A_1 ^ ... ^ t.A_m = t'.A_m \rightarrow t.B_1 = t'.B_1 ^ ... ^ t.B_n = t'.B_n)$$
- In another words: some attributes (A, the antecedent) determine other attributes (B, consequent)

**Example**

| EmpID | Name | Phone | Position |
| ----- | ---- | ----- | -------- |
| E001  | John | 111   | Security |
| E002  | Adam | 222   | Salesman |
| E003  | Mary | 333   | Clerk    |
| E004  | Jane | 444   | Janitor  |
- $EmpID \rightarrow Name, Phone, Position$ (valid FD)
- $Position \rightarrow Phone$ (valid FD)
-  $Phone \rightarrow Position$ (not FD)

## Checking FD with SQL Queries

| EmpID | Name | Phone | Position |
| ----- | ---- | ----- | -------- |
| E001  | John | 111   | Security |
| E002  | Adam | 222   | Salesman |
| E003  | Mary | 333   | Clerk    |
| E004  | Jane | 444   | Janitor  |
```sql
SELECT *
FROM T as t_1, T as t_2
WHERE (t_1.position = t_2.position) AND
		(t_1.phone != t_2.phone) /* query for entry of multiple phones given a position, which there should be none outputed if FD (position -> phone) is true*/


SELECT *
FROM T as t_1, T as t_2
WHERE (t_1.phone = t_2.phone) AND
		(t_1.position != t_2.position) /* query for entry of multiple position given a phone, which there should be none outputed if FD (phone -> position) is true*/
```

Note:
- The above examples in real-life query can still output certain entries, for one to consider a position with only one phone could be not fully possible, vice versa
- The above query contain self-join
- If we can be sure that every instance of T will be one in which a given FD is true, then we say that T satisfies the FD
- If we say that T satisfies an FD, we are stating a constraint on T
	- It is worthy to mention that the dependency can inherit one and another (transitive)
	- $name \rightarrow color$
	- $category \rightarrow department$
	- $color, category \rightarrow price$
		- based on the 3 FDs above: $name, category \rightarrow price$
## Fundamentals of FDs
Armstrong's Axioms
- Axiom of Reflexivity (Trivial FD)
	- If $B \subseteq A$, then $A \rightarrow B$
	- ex. {name} $\subseteq$ {name, job} so {name, job} $\rightarrow$ {name}
- Axiom of Augmentation
	- If $A \rightarrow B$ , then $\forall C, AC \rightarrow BC$
	- ex. {ID} $\rightarrow$ {name so {ID, job} $\rightarrow$ {name, job}
- Axiom of Transitivity
	- If $A \rightarrow B$ and $B\rightarrow C$ , then $A \rightarrow C$
	- ex. {ID} $\rightarrow$ {name} and {name} $\rightarrow$ {initials}, so {ID} $\rightarrow$ {initials}
Some Interesting Secondary Rules
- Pseudo Transitivity
	- If $A \rightarrow BC$ and $C \rightarrow D$, then $A \rightarrow BD$
- Extensivity
	- Id $A \rightarrow B$, then $A \rightarrow AB$
Note:
- it is typically okay to introduce attribute on the left-hand side as the pre-existing antecedent already constraining the consequent, but the another way around would not work. If you would like to introduce attribute on the right-hand side, you would need a left-hand side attribute constraint
- If we are talking about left-hand side and right-hand side shared attributes, we are also talking about the existence of two tables
## Closure
Definition: The closure of the set $\{A_1,..., A_m\}$, written as $\{A_1,..., A_m\}^+$, as the set of attribute B such that $A_1,..., A_m \rightarrow B$

Example
- SSN $\rightarrow$ Name
- Name $\rightarrow$ Initials

- Name^+ = {Name, Initials}
- SSN^+ = {SSN, Name, Initials}
- Initials^+ = {Initials}
- {SSN, Initials}^+ = {SSN, Name, Initials}

```
Closure Algorithm
X = {A_1, ..., A_m}
Repeat until X does not change
	if B_1, ..., B_N -> C is FD and B_1, ..., B_N in X
	then X <- X U C

In practice:  
Repeated use of transitivity  
1. Find some attribute(s) C to add to right side  
2. Add them  
3. Look back at the FDs to find more C
```

Note:
- You left-hand side is your X closure, initialized without true closure achieved, whereas right-hand side should be update updated X based on the Bs in the given functional dependencies list
- Closure help elucidate the interrelationship of data and help us to find the right key
1. name $\rightarrow$ color
2. category $\rightarrow$ department
3. color, category $\rightarrow$ price
```
{name, category}+  = {name, category} [reflexivity]  
                  = {name, category, color} [transitivity, name -> color]
                   = {name, category, color, department}  [transitivity, category -> department]
                  = {name, category, color, department, price}[transitivity, color, category ->price]
```
## Superkeys in DB Design

Superkey: a set of attributes $A_1,..., A_n$ s.t. for any single attribute B:

$$A_1, ..., A_n \rightarrow B$$
In other words, for the set of all attributes C in the relation R, the set {A_1, ..., A_n} is a superkey iff {A_1, ..., A_n}^+ = C

**Example**
- Restaurants(rid, name, rating, popularity)  
- rid $\rightarrow$ name  
- rid $\rightarrow$ rating  
- rating $\rightarrow$ popularity

|               | Closure                         | Superkey? | key?              |
| ------------- | ------------------------------- | --------- | ----------------- |
| {rid, rating} | {rid, name, rating, popularity} | Yes       | No                |
| rid           | {rid, name, rating, popularity} | Yes       | Yes ({A})         |
| rating        | {rating, popularity}            | No        | No ({C}, not {B}) |
| popularity    | {popularity}                    | No        | No ({C}, not {B}) |

## Usefulness of Keys in Design

**Intuition**
- FDs that are not superkeys hint at redundancy  
	- If a FD antecedent is not a superkey, we can remove redundant information, i.e. the FD consequent
- In another word:
	- {A} $\rightarrow$ {B} is fine if {A} is a superkey
	- Otherwise, we should extract {B} into a separate table

Before: 1 table with redundancy, no superkey

| Name | SSN         | Phone        | City          |
| ---- | ----------- | ------------ | ------------- |
| Fred | 123-45-6789 | 206-555-9999 | Seattle       |
| Fred | 123-45-6789 | 206-555-8888 | Seattle       |
| Joe  | 987-65-4321 | 415-555-7777 | San Francisco |

After: 2 tables with separated redundancy (incomplete FD: SSN $\rightarrow$ Phone), and supkey {SSN}+ = {SSN, Name, City}

| Name | SSN         | City          |
| ---- | ----------- | ------------- |
| Fred | 123-45-6789 | Seattle       |
| Joe  | 987-65-4321 | San Francisco |

| SSN         | Phone        |
| ----------- | ------------ |
| 123-45-6789 | 206-555-9999 |
| 123-45-6789 | 206-555-8888 |
| 987-65-4321 | 415-555-7777 |
## Boyce-Codd Normal Form (BCNF) - no transitive FDs, but can lose FDs

**First Normal Form**: A relation 𝑅 is in First Normal Form if all attribute values are atomic. Attribute values cannot be multivalued. Nested relations are not allowed

**BCNF**: 
- for every non-trivial dependency, $X \rightarrow A$, X is superkey
- a relation R is in BCNF if $\forall X$ either $X^+ = X$ or $X^+ = C$ where C is the set of all attributes in R

| Name | SSN         | Phone        | City          |
| ---- | ----------- | ------------ | ------------- |
| Fred | 123-45-6789 | 206-555-9999 | Seattle       |
| Fred | 123-45-6789 | 206-555-8888 | Seattle       |
| Joe  | 987-65-4321 | 415-555-7777 | San Francisco |
- Looking at this table, we would say that this is not in BCNF form, because of the bad FD (SSN $\rightarrow$ Phone is invalid)
	- {SSN}+ $\rightarrow$ SSN, Name, 
- From bad FD, sometime we can decompose / extracting attributes by splitting the schema into smaller parts

$$R(A_1, ..., A_n, B_1,..., B_m, C_1,...,C_k) \rightarrow R_1(A_1,..., A_n, B_1,..., B_m),  \rightarrow R_2(A_1,..., A_n, C_1,..., C_k)$$

```
BCNF Decomposition Algorithm

Normalize(R)
	C <- the set of all attributes in R
	defin X+
	find X s.t. X+ neq X and X+ neq C
	if X is not found
	then "R is in BCNF"
	else
		decompose R into R_1(X+) and R_2((C- X^+) U X)
		Normalize(R_1)
		Normalize(R_2)
```
Note:
- Recall X+ is the closure of $\{A_1,..., A_m\} = X$. the set of attributes we determine FD exist 
- Normalization in our context...

Example:
- Given the following functional dependencies
	- Restaurants(rid, name, rating, popularity, recommended)
	- rid $\rightarrow$ name, rating
	- rating $\rightarrow$ popularity
	- popularity $\rightarrow$ recommended
1. C <= rid, name, rating, popularity, recommended
2. X^+ <= rating -> rating, popularity, recommended
3. X as (rid and name)  are equal to X^+ nor X^+ equal to the C (aka. all the attributes)
4. R1 = rating, popularity, recommended; R2 = rid, name, rating
	- we can still see that popularity -> recommended is bad FD, so we need to decompose again
	- R2 = rid, name, rating; R3= rating, popularity; R4= popularity, recommended
## Losslessness
Definition: a reversible decomposition where a rejoin of all the decomposed relation will result with the original data
- ex. PNG, JPEG, Huffman encoding

In the example of Restaurant BCNF, it is a lossless decomposition
- R2 = rid, name, rating
- R3 = rating, popularity
- R4 = popularity, recommended

