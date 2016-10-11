# Lecture 9 SQL

## Structured Query Language
The two subsets of SQL language:
- Data Definition Language (DDL): All the statements in order to create or define schema.
- Data Manipulation Language (DML): Get data into the database, modify and fetch data.


SQL is an extension of relational algebra, it extends in many ways: 
- Multi-sets: results of SQL statement can contain duplicates
- NULLs
- Aggregates


## Database Example
![](https://github.com/CHJoanna/W4111_sribenote/blob/master/img1.png)
- The Primary key for Reserves is sid and bid, meaning a sailor can only reserves a boat once.
- Day should be part of key.

<br/>
     
![](https://github.com/CHJoanna/W4111_sribenote/blob/master/img2.png)
- Add a relation to the FROM clause is similar to perform a cross-product, and the WHERE statement is the filter condition for the output of cross-product.


## Semantics
- FROM: compute cross product of relations
- WHERE: remove tuples that fail qualifications. Specify the selection conditions on the relations in FROM. 
- SELECT: specify columns to be retained in the result
- DISTINCT: remove duplicate rows


## Structure of a SQL Query
SELECT [DISTINCT] target-list   
FROM relation-list   
WHERE qualification   
- target-list: list of attributes that we want to pull out of the relations
- relation-list: list of relations
- qualification: should be Boolean expression.


## SQL Query Examples
![](https://github.com/CHJoanna/W4111_sribenote/blob/master/img3.png)
- A sid can join to multiple reservations, so adding DISTINCT will change the result 

<br\>

![](https://github.com/CHJoanna/W4111_sribenote/blob/master/img4.png)

<br\>

![](https://github.com/CHJoanna/W4111_sribenote/blob/master/img5.png)
- AS: give name to the output attribute (e.g. age2)

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


## Expressions
- Constant 1, ‘hello’, 7.85 (use single quote for string)
- Col reference Sailors.name
- Arithmetic Sailors.sid * 10
- Unary operators NOT
- Binary operators AND, OR, <, =, <>, >= (<> means "non equal")
- Function abs(), sqrt(), ...
- Casting 1.7::int, ‘10-12-2015’::date


## UNION, INTERSECT, EXCEPT
SELECT [query1] UNION SELECT [query2]
- produce DISTINCT result
- query1 and query2 need to be UNION compatible
<br/>

SELECT [query1] UNION ALL SELECT [query2]
- keep all the duplicate

[More Examples about SQL statement](https://www.instabase.com/ewu/w4111-public/fs/Instabase%20Drive/Examples/sql.ipynb)

## SET Comparison Operators
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

-Note that the max cardinality of the query is cardinality of Sailors.
-Joins can increase cardinality of output beyond the input tables (cross products)
