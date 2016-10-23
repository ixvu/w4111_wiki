# Structured Query Language
The two subsets of SQL language:
- Data Definition Language (**DDL**)  
 - All the statements in order to define and modify schema (physical, logical, view).
 - `CREATE TABLE`, Integrity Constraints
- Data Manipulation Language (**DML**): Get and modify data
 - simple `SELECT`, `INSERT`, `DELETE`
 - _human-readable_ language

DBMS attempts efficient execution
- Key: precise (well-defined) query semantics
- Reorder/modify queries while answers stay the same
- Estimates costs for different evaluation plans
- SQL -> Plan Generator -> Plans 1...n -> Select Cheapest Plan

SQL is an extension of relational algebra with: 
- Multi-sets
 - Results of SQL statement can contain duplicates
 - Order doesn't matter
- NULLs
- Aggregates

**SQL is worthwhile to study as it is not just the most widely used relational query language, but also most widely used query language!**

# Database Example
![](https://github.com/CHJoanna/W4111_sribenote/blob/master/img1.png)

<details> 
  <summary>Q1: Is the Reserves table correct?</summary><br />
   No! The **Reserves** primary key is **sid** and **bid**, meaning a sailor can only reserve a boat once. Therefore day should be part of key (and would be underlined accordingly)
</details>
     
***

**<30 year old sailors**

| sid | name   | rating | age |
| --- | ---    | ---    | --- |
| 1   | Eugene | 7      | 22  |
| 3   | Ken    | 8      | 27  |

> `SELECT * FROM Sailors WHERE age < 30`  
> **σ<sub>age<30</sub>(Sailors)**

<details> 
  <summary>Q2: Which relational operator is this equivalent to?</summary><br />
   Project takes columns, Select is the operator that takes a predicate and reduces a set of rows. In contrast, in SQL this select takes out columns. **SQL Select Clause != Select Relational Operator**. They're opposites
</details>
     
***

```sql
SELECT name,age FROM Sailors WHERE age < 30
```  
**π<sub>name,age</sub>(σ<sub>age<30</sub>(Sailors))**
  
***

```sql
SELECT S.name  
FROM   Sailors AS S, Reserves AS R  
WHERE  S.sid = R.sid AND R.bid = 102  
```
**π<sub>name</sub>(σ<sub>bid=102</sub>(Sailors⋈<sub>sid</sub>Reserves))**

<details> 
  <summary>Q3: What kind of join is this?</summary><br />
   An equi-join!  
   ![](https://github.com/CHJoanna/W4111_sribenote/blob/master/img2.png)
</details>

### Who reserved boat 102?

**Sailors**  

| sid | name   | rating | age |
| --- | ---    | ---    | --- |
| 1   | Eugene | 7      | 22  |
| 2   | Luis   | 2      | 39  |
| 3   | Ken    | 8      | 27  |

**Reserves**  

| sid | bid | day  |
| --- | --- | ---  |
| 1   | 102 | 9/12 |
| 2   | 102 | 9/13 |
| 2   | 103 | 9/14 |
| 1   | 102 | 9/15 |


<details> 
  <summary>Q4: Who reserved boat 102?</summary>
   <table>
   <tr><th>name</th></tr>
   <tr><td>Eugene</td></tr>
   <tr><td>Luis</td></tr>
   </table>  
   Note the duplicate "Eugene" row.
</details>

Note:  
The addition of relations to a **FROM** clause correspond with the addition of a **cross product**.  
The **WHERE** statement is the **filter** condition for the output of the cross product.

***

# Semantics
#### FROM: compute cross product of relations
#### WHERE: remove tuples that fail qualifications. Specify the selection conditions on the relations in FROM. 
#### SELECT: specify columns to be retained in the result
#### DISTINCT: remove duplicate rows


# Structure of a SQL Query
SELECT [DISTINCT] target-list   
FROM relation-list   
WHERE qualification   

### target-list
- list of expressions over attributes of tables in **relation-list**
- e.g. `SELECT s.name`

### relation-list  
- list of relations  
- Can define aliases `AS X`  
- e.g. `FROM Sailors AS s, Reserves as R`  

### qualification  
- Boolean expression
 - Combined w/ `AND`, `OR`, `NOT`
 - attr op const
 - attr<sub>1</sub> op attr<sub>2</sub>
 - op is =, <, >, <>, etc.

### DISTINCT
- Optional: Remove duplicates (set)
- Default: duplicates permitted (multiset)

##TODO: Conceptual Query Slide!

# SQL Query Examples

## Building Queries!

### Sailors that reserved 1+ boats

<details> 
  <summary>1. In order to find out what sailors reserved boats, I'll need the sailors relation and boats relation</summary>
  `FROM Sailors AS S, Reserves as R`  
  <details> 
    <summary>2. Well what data do I want to keep?</summary>
      `FROM  Sailors AS S, Reserves as R`  
      `WHERE S.sid = R.sid`  
      <details> 
        <summary>3. Happy with that and only want the sailor id</summary>
          `SELECT S.sid`  
          `FROM   Sailors AS S, Reserves as R`  
          `WHERE  S.sid = R.sid` 
          <details> 
             <summary>Q5: Would DISTINCT change anything is this query?</summary>
             Yes, a **sid** can join to multiple reservations, so adding DISTINCT will change the result. Even if the            attribute is a primary key, it can appear multiple times in the relation.
             <details> 
               <summary>So what query do I really want?</summary>
               `SELECT DISTINCT S.sid`  
               `FROM   Sailors AS S, Reserves as R`  
               `WHERE  S.sid = R.sid`  
               ![](https://github.com/CHJoanna/W4111_sribenote/blob/master/img3.png)
             </details>
          </details>
      </details>
  </details>
</details>

#### Professor Pro-tips:
> "When I give you a question I'm going to say 'give me the *unique set* of sailor names'; In which case that tell's you what I'm looking for. If I just said 'I want the sailor names', it's ambiguous and you can interpret both ways. We want to be precise about what we're saying in terms of writing SQL queries"

****

### Table Alias (AS, Range Variables)
- Disambiguate relations (useful when the same table is used multiple times)

<details> 
  <summary>1. We want all sailors who are older than another sailor so we use a use a self join</summary>
  So we try:  
  `Select sid`  
  `FROM   Sailors, Sailors`  
  `WHERE  age > age`  
  <details> 
    <summary>Q6: But what's wrong with this query?</summary>
      Ambiguity! (Big hint that it's incorrect)  
      `SELECT S1.sid`  
      `FROM   Sailors AS S1, Sailors AS S2`  
      `WHERE  S1.age > S2.age`   
      ![](https://github.com/CHJoanna/W4111_sribenote/blob/master/img4.png)
      <details>
         <summary>And if we want additional fields we must label them explicitly</summary>
         `SELECT S1.name, S1.age, S2.name, S2.age`  
         `FROM   Sailors AS S1, Sailors AS S2`  
         `WHERE  S1.age > S2.age`  
      </details>
  </details>
</details>

### TODO: Link to Livecode

<br\>

![](https://github.com/CHJoanna/W4111_sribenote/blob/master/img5.png)
#### AS: give name to the output attribute (e.g. age2)

<br\>

![](https://github.com/CHJoanna/W4111_sribenote/blob/master/img6.png)
- LIKE: match all strings that are similar to the given string.
- e.g. 'e_%': match the name of all sailors who's name starts with 'e' and has at least one more character in the name.  

<br\>

![](https://github.com/CHJoanna/W4111_sribenote/blob/master/img7.png)

<br\>

![](https://github.com/CHJoanna/W4111_sribenote/blob/master/img8.png)
- INCORRECT! (B.color = ‘red’ AND B.color = ‘blue’) == FALSE, so it will return an EMPTY table.

<br/>

![](https://github.com/CHJoanna/W4111_sribenote/blob/master/img9.png)
- (B1.bid = R1.bid) gives all the distinct sailors who reserve red boat.


# Expressions
<table>
<tr><th>Type</th><th>Example</th></tr>
<tr><td>Constant</td><td>1, 'hello', 7.85</td></tr>
<tr><td>Column Reference</td><td>Sailors.name</td></tr>
<tr><td>Arithmetic</td><td>Sailors.sid * 10</td></tr>
<tr><td>Unary Operators</td><td>NOT</td></tr>
<tr><td>Binary Operators</td><td>AND, OR, <, =, <>, >=</td></tr>
<tr><td>Functions</td><td>abs(), sqrt()</td></tr>
<tr><td>Casting</td><td>1.7::int, ‘10-12-2015’::date</td></tr>
</table>
Note: Use a single quote for string and `<>` means "non equal"

# UNION, INTERSECT, EXCEPT
SELECT [query1] UNION SELECT [query2]
- produce DISTINCT result
- query1 and query2 need to be UNION compatible
<br/>

SELECT [query1] UNION ALL SELECT [query2]
- keep all the duplicate

[More Examples about SQL statement](https://www.instabase.com/ewu/w4111-public/fs/Instabase%20Drive/Examples/sql.ipynb)

# SET Comparison Operators
We can create operators to compare scalar values to sets.
-x IN r. True if value x appears in r.
-EXISTS r. True if relation r is not empty.
<br/>
x (op) ANY r: True if x (operator) is true for any row in r.
- Example: x = ANY r. True if x is equal to ANY value in r. This is also equivalent to x IN r.
<br/>
x (op) ALL r: True if x (operator) is true for all rows in r.
- Example: x <> ALL r. True if x is not equal to ALL values in r. This is also equivalent to x NOT IN r.  

![](https://github.com/cchao595/scribenotes/blob/master/1.png)
<br/>
- Note that the max cardinality of the query is cardinality of Sailors.
- Joins can increase cardinality of output beyond the input tables (cross products)  

Example: Sailors whose rating is greater than any sailor named "Bobby"  
![](https://github.com/cchao595/scribenotes/blob/master/11.png)  
<br/>
"> ANY": Output sailor from S1 that has rating greater than people named Bobby. 
<br/>
"> ALL": Output sailor from S1 that has rating greater than **all** sailors named Bobby.
Equivalences- can find min or max of ratings of people named Bobby and compare to ratings in S1 to get > ANY and > ALL equivalences respectively. Good to try equivalences on Instabase. (Multinested queries may be on midterm)
<br/>
![](https://github.com/cchao595/scribenotes/blob/master/3.png)
<br/>
Find sailors where their ratings are greater then two and have made a reservation.  
What if want names instead of sids?
- sid is distinct, not unique.
- Syntactically does not work- reserves do not store the names.  <br/>
What if we replace **intersect** with **intersect all**?
- Intersect all includes duplicates, but for this example this will not change anything, We know sid are primary keys, so we know there won't be duplicates. <br/>
Similar trick for EXCEPT--> NOT IN  
Example: Name of sailors that reserved all boats  
Use double negation. "Reserved all boats" == "no boats without reservations"  
Break query into smaller parts first. First, we take a look at Sailor 1.
<br/>
![](https://github.com/cchao595/scribenotes/blob/master/5a.png)
<br/>
Hint says that we can look at the negation of boats reserved by Sailor 1.
<br/>
![](https://github.com/cchao595/scribenotes/blob/master/9.png) 
![](https://github.com/cchao595/scribenotes/blob/master/6.png) 
<br/>
For each boat, we want only boats that have not been reserved by Sailor 1. Equivalent statement: Select the boats which have not been reserved by Sailor 1.
<br/>
![](https://github.com/cchao595/scribenotes/blob/master/7.png)
<br/>
Want to be able to express "All sailors without unreserved boats." Double negation is present in "without unreserved". To go from one sailor to all sailors, we take s.sid and s.name from Sailors. We use NOT EXISTS check if the query is true. 
<br/>
Alternatively, we can approach the problem the following way:  
![](https://github.com/cchao595/scribenotes/blob/master/8.png)
<br/>
The general structure of nested query of a statement "noun verb noun":  
NOUN--> NOUN--> VERB  
ENTITY1--> ENTITY2--> RELATIONSHIP  
Check [Instabase](https://www.instabase.com/ewu/w4111-public/fs/Instabase%20Drive/Examples/sql.ipynb) to see the swap between Reserves and Boats and why the outputs are different. 

# Aggregation
Aggregates: Functions over sets.  
Syntax: Function(expression). Expression can contain math (age*2+5) and "DISTINCT"- distinct expression vals.
Examples of aggregates:  
- counting: COUNT(*)
- average: AVG(s.age)
- SUM( [DISTINCT] A)
- MAX/MIN(A)
- STDDEV(A)
- CORR(A,B)
<br/>
Note: SELECT COUNT(\*) = SELECT COUNT(name), we are not using name in any meaningful way. However SELECT COUNT(*) =/= SELECT COUNT(DISTINCT name) could be smaller than count(\*).  
Sum(\*) doesn't work. We cannot sum over attributes.  
All SELECT values must also be aggregates. Ex: We cannot have SELECT S.name, MAX(S.age).  However, multiple aggregates do work. Ex: SELECT AVG(S.rating), MAX(S.age).  
To get the name of age of oldest sailors:  
![](https://github.com/cchao595/scribenotes/blob/master/10.png)
<br/>
Which is more efficient?  
#GROUP BY 
<br/>
Syntax:  
SELECT target-list
FROM relation-list  
WHERE qualification  
GROUP BY grouping-list  
- grouping list: expressions that define groups. Set of tuples w/ same value for all attributes in grouping-list.
- target-list contains attribute names that are a subset of the grouping list, or aggregate expressions.  
What if we want reservations per boat? If we didn't know all our boats, we can use group by.  
SELECT bid, count(*)  
FROM Reserves R  
GROUP BY  bid <-- now at a per boat basis.  
What if we want to show an sid that reserved boat?  
SELECT bid, count(*), sid
FROM Reserves R  
GROUP BY  bid  
This is ambiguous. Expressions must have one value per group.  

In Class example:  
![](https://github.com/cchao595/scribenotes/blob/master/fig0.png)
<br/>
select a, sum(b)  
...  
group by a  
We can use a hashtable. Consider:  
![](https://github.com/cchao595/scribenotes/blob/master/fig1.png)  
First we take all values of a and put it in the bucket. This groups all tuples together. Finally, we take the sum of all b's in each group and return (a, SUM(b)).  
Alternatively, we can just keep track of b and sum up. There are different ways to do this, for example f(list of vals), f(memo, newval)  
We can also sort by a:  
[1,1,6 <br/> 1,2,7 <br/> 1,4,9] <br/> [2,3,8] <br/> [3,5,10]  First we sort by a and then group them by the same values. Once we hit the end of the group, we find (a, SUM(b)). This process is repeated until you reach the end of the list. This will also get you the same answer.  
Suppose we had select a, sum(b), c. If we look at 1: the last step would yield: (1,7, ?). There are 3 values in this case: 3, 7, and 9. We wouldn't know which the last number would be set to.
<br/>
# HAVING 
<br/>
- Once there are groups, want to filter again. 
- group-qualification used to remove groups (like WHERE)
- expressions must have one value per group. 
- Shouldn't have access to other attributes after the group by. Instead, filter before you group using WHERE.  
A continuation of the process of evaluating queries:  
![](https://github.com/cchao595/scribenotes/blob/master/14.png)

# ORDER BY
- Expressions to determine precedence in output table
- ASC: Ascending (Lowest to Highest)
- DESC: Descending (Highest to Lowest)
![](https://github.com/zzhanzzhao/scribenotes/blob/master/2.png)

# LIMIT
- Limit to only first n results in output table 
- OFFSET: skip n rows in beginning before beginning to return rows.

## Only the first two results
**Sailors**

|sid|name  |rating|age|
|---|------|------|---|
| 1 |Eugene| 7    |22 |
| 2 |Luis  | 2    |39 |
| 3 |Ken   | 8    |27 |

```sql
SELECT    S.name, (S.rating/2)::int, S.age
FROM      Sailors S
ORDER BY  (S.rating/2)::int ASC,
          S.age DESC
LIMIT     2 OFFSET 1
```

**Results**

|name  |int4  |age|
|------|------|---|
|Ken   | 4    |27 |
|Eugene| 3    |22 |

#NULL
- Means "unknown" or "maybe" 
- Example: NULL AND True? Could be true if NULL were TRUE: = NULL
- Example: 1/NULL? Could be error if NUll were 0, but not necessarily: = NULL
- IS NULL & IS NOT NULL: tests if a value is NULL or NOT NULL
![](https://github.com/zzhanzzhao/scribenotes/blob/master/1.png)

# JOIN
<br/>
![](https://github.com/zzhanzzhao/scribenotes/blob/master/4.png)
- INNER JOIN: returns all matched rows; is default JOIN type
![](https://github.com/zzhanzzhao/scribenotes/blob/master/5.png)
- NATURAL JOIN: equi-join for each pair of attributes with the same name 
- LEFT OUTER JOIN: returns all matched rows and **unmatched rows from table on left of join clause**
![](https://github.com/zzhanzzhao/scribenotes/blob/master/6.png)
- RIGHT OUTER JOIN: returns all matched rows and **unmatched rows from table on right of join clause**
- FULL OUTER JOIN: returns all matched rows and unmatched rows from **both sides of join**
<br/>
![](https://github.com/zzhanzzhao/scribenotes/blob/master/7.png)

# Table Constraints
- Inserts/Deletes/Updates that violate Integrity Constraints rejected
- CHECK: ensures columns meet certain criteria
- CONSTRAINT: sets rules for the data 
![](https://github.com/zzhanzzhao/scribenotes/blob/master/8.png)

# User Defined Functions (UDFs)
<br/>
![](https://github.com/zzhanzzhao/scribenotes/blob/master/9.png)
- Custom functions that can be called in database
- Many languages: SQL, python, C, perl, etc
- Input arguments could be tables or columns with a certain data type like "int"
- Input arguments can be called using "$1", "$2", etc or their given names 
![](https://github.com/zzhanzzhao/scribenotes/blob/master/10.png)