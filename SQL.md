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

## Aggregation
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