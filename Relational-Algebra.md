# Lecture 7 Relational Algebra

#####[Questions regrading to ISA relationships is at the end](#ISA_QA).

Relational algebra [Wikipedia](https://en.wikipedia.org/wiki/Relational_algebra)

##Overview

- relational algebra:   operational 
  - clean query semantics : core fundamental operation 
- relational calculus: logical
  - Grounded in mathematical notations , borrow from math analysis. 

##What's a query language?
- Allows manipulate and `retrieve data` from database. 
- It is a domain specific language. 


##Relational Algebra vs. Relational calculus 
- Q: How many ways you can add two numbers to 3 ?  
- Answer of Relational Algebra: 
  - 1+2
  - 3+0
  - ...
- Answer of Relational Calculus: 
  - {(a,b) | a,b are integers and a+b = 3}


##Prelims
- Query is a function over `relation instances` 

- Relation
  - Instance is a set of tuples
  - Schema defines field names and types 

- how are relations different than generic sets?
  - One can assume item structure due to schema
  - Some algebra operations (x) need to be modified



##5 Core operations
- [PROJECT](#project)
- [SELECT](#select)
- [UNION](#union)
- [SET DIFFERENCE](#setdifference)
- [CROSSPRODUCT](#crossproduct)


![](https://github.com/WillCGitHub/scribenotes/blob/master/GraphForOperations.JPG)

###<a id="project">PROJECT</a>
- Given attributes to reflect results
- No duplicate results because PROJECT returns a set 
- real systems typically don't remove duplicates because you want to know the distribution 
and deleting duplication is expensive.


![](https://github.com/WillCGitHub/scribenotes/blob/master/PROJECT.JPG)
![](https://github.com/WillCGitHub/scribenotes/blob/master/ProjectExp.JPG)

 
###<a id="select">SELECT</a>

![](https://github.com/WillCGitHub/scribenotes/blob/master/SELECT.JPG)
- select subset of rows that satisfy condition 
- For example, select age under 30 SELECT(age < 30)


![](https://github.com/WillCGitHub/scribenotes/blob/master/SelectExp.JPG)



![](https://github.com/WillCGitHub/scribenotes/blob/master/Commutatively1.JPG)
![](https://github.com/WillCGitHub/scribenotes/blob/master/Commutatively2.JPG)
![](https://github.com/WillCGitHub/scribenotes/blob/master/Commutatively3.JPG)
![](https://github.com/WillCGitHub/scribenotes/blob/master/Commutatively4.JPG)
![](https://github.com/WillCGitHub/scribenotes/blob/master/Commutatively5.JPG)



###<a id="union setdifference">UNION,SET DIFFERENCE</a>
- A op B = Result 
- A and B must be union-compatible (types should be compatible )
- union remove duplicate 

![](https://github.com/WillCGitHub/scribenotes/blob/master/Union2.JPG)

![](https://github.com/WillCGitHub/scribenotes/blob/master/Union.JPG)

![](https://github.com/WillCGitHub/scribenotes/blob/master/SETDIFFERENCE.JPG)


###<a id="crossproduct">CROSSPRODUCT</a>
- Vertically concatenate tables 
- Rename duplicate names 
- All pairwise combination 
- Loose foreign key references 

![](https://github.com/WillCGitHub/scribenotes/blob/master/Crossproduct.JPG)

![](https://github.com/WillCGitHub/scribenotes/blob/master/Crossproduct2.JPG)


##additional operations
###interset
- A and B must be union compatible 

![](https://github.com/WillCGitHub/scribenotes/blob/master/IntersectExp.JPG)

##Joins (3 types)
General definition: cross-product followed by selections and projections (Ramakrishnan 107)

###Condition Join (Theta Join)
- Most general form of join
- All fields of cross product retained
- select(<sub>c</sub>)(A X B)

###Equi-join
- A special case of condition join where the join condition consists solely of equalities.
- To avoid redundancy, left side of the equality is retained, right side dropped.
- E.g., if the join condition is (R.name1 = S.name2), S.name2 is dropped as the final step of the join (after doing the cross product and selection)

###Natural join
- Further special case of equi-join in which equalities are specified on all fields having the same name.

###Example: join where one of the conditions is an equality, the other not
Consider the following join: T1⋈<sub>((T1.A>T2.C)∧(T1.B=T2.D))</sub>T2.

Will the output contain fields (T1.A, T1.B, T2.C) or (T1.A, T1.B, T2.C, T2.D)?

Answer: The join condition does NOT consist solely of equalities, so it is considered a theta join, not an equi join. Therefore, all fields of the cross product are retained. Output relation contains fields (T1.A, T1.B, T2.C, T2.D). (Table 1's fields come first)


![](https://github.com/WillCGitHub/scribenotes/blob/master/EquiJoin.JPG)
###divide 

#####Commutatively is allowed between PROJECT and SELECT. 
#####Commutatively is not allowed if project different attributes.  

- If S1 is very wide, use project narrow down.
  - think about efficiency 


###<a id="ISA_QA">ISA Relationships Questions</a>

- Users(uid, name)
- Students(grades) isa Users
- Staff(ratings) isa User

Question
- what if Employs table wants to reference Users? 
- what if Employs table wants to reference Students and Staff?

Constraints
- Covering: must be an instructor or student 
- Overlaps: can be an instructor and student

##Set Difference and Performance

Most operations are **monotonic**: 
* The more input => the more output grows. 
* If A ⊇ B => Q(A,T) ⊇ Q(B,T). 
* Can **compute incrementally**. 
* This is a desirable property.

However **set difference** is **not monotonic**: 
* If A ⊇ B => T-A ⊆ T-B  
* Cannot compute incrementally. 
* Computers with **blocking**. 
* This is undesirable.

The difference between **incremental** and **blocking**: 

Given Op1(Op2(table)), where Op1 takes 10 seconds, and Op2 takes 10 seconds: 
* **Incremental**: overlap execution of Op1 + Op2 (10-11 seconds total) 
* **Blocking**: Op1 finishes, then Op2 can start (20 seconds total) 

Note: when aggregating data, there is usually an elegant incremental solution. 

##Recap: Joins
* The definition of joins is you do the cross-product, and then filter.
* Join only takes two tables as input. Need to decide which two tables join first.
* Joins are very popular and are natively supported.

Note: We can’t express left outer join and right outer join in relational algebra. The reason is we haven’t introduced NULL in this model.

##Relational Operator: Set Division 
**General Form:**
* Relations: A(x,y) and B(y). 
* Division: A/B = { <x> | ∀y ∈ B<x,y> ∈ A }
**Example:** 
* Relations: A(name,bid) and B(bid), where name is a student’s name and bid is a book ID. 
* Division: A/B = { <name> | ∀bid ∈ B<name,bid> ∈ A }
* Meaning: Find ALL students that have reserved all books. 
**Meaning:** 
* Used to express “ALL.”
* Example: Which students are registered in ALL courses given by Prof. Wu? 
Such that if Prof. Wu gives a course, then the student is registered in it. 

**Note:**
Division is not supported in most systems: 
* Huge run-time. 
* Very expensive. 
* Not used often. 
* Blocking operation. Not monotonic.
* There are alternative methods to accomplish “ALL” statements. 

##Examples of Set Division 

A/R1
![](https://github.com/sacrespo/ScribeNotes/blob/master/1.png)

A/R2
![](https://github.com/sacrespo/ScribeNotes/blob/master/2.png)

A/R3
![](https://github.com/sacrespo/ScribeNotes/blob/master/3.png)

##How to Express Set Division?
* Find all xs not “disqualified” by some y in B.
![](https://github.com/sacrespo/ScribeNotes/blob/master/4.png)


**Meaning:** 
* Disqualified: Students that do not reserve the particular books.
* Final answer: All students minus disqualified students.

## Additional Notes on Set Division
###Division can be thought of in relation to the Cross Product
If A / B = C, B X C is a subset of A 
####Explanation and Example
From the above example, 
* C(name), or A/B can be thought of as "all students who have reserved all books"
* B(bid) is the set of all books 
* A (name,bid) is the set of all reservations

Thus, 
* B X C yields the set of reservations that have been made by "all students who have reserved all books", a subset of A
Following, if every student in A had reserved all books in B, then A / B = C and B x C = A. 

### Division as an SQL Query follows the principle of double negation 
In SQL queries, division A ( name, bid) / B(bid) is implemented using double negation. 
![]()

### Division is affected by the number of attributes used in both the divisor and dividend
The number of attributes in the tables used by division matter in obtaining the correct result of division. 
For example, 
+ Let there be tables A(x, a1, a2) and B(x). 
+ Let A1 $\Pi$ <sub>x,a1</sub>(A)
+ $\Pi$ <sub>a1</sub>(A / B) will not yield the same results as A1 / B. 
#### Explanation
Given tables A(x, a1,a2,...an) and B(x), then A / B finds all instances of (a1,a2..an) that contain every x in B. 
For table A1, this means A1/B will yield every a1 that has every x in B. 
However, for table A, this means A/B will yield every pair (a1,a2) that has every x in B. 

Following, even though a value of a1 may contain every x in B and be in A1/B, a pair of (a1,a2) may not contain every x in B, and may result in that a1 value being excluded from the result of $\pi${\substack{a1}}A / B. 

##More Examples
Tables: Book(rid, type) Reserve(sid,rid) Students(sid)
* Name of students that reserved DB books
![](https://github.com/sacrespo/ScribeNotes/blob/master/5.png)

Note: If there are many attributes and records, the cardinality is high. The latter one is a more efficient query.
Note: Query optimizer can find the more efficient query!

* Students that reserved DB or HCI book (Hint: or operation)
![](https://github.com/sacrespo/ScribeNotes/blob/master/6.png)

* Students that reserved DB and HCI book (Hint: find two sets, and perform intersection)
![](https://github.com/sacrespo/ScribeNotes/blob/master/7.png)

* Students that reserved all books
![](https://github.com/sacrespo/ScribeNotes/blob/master/8.png)

If primary key of Reserve is (sid, rid) and we forget to use sid, rid, we will still end up with the same result.

* Students that reserved all horror books
![](https://github.com/sacrespo/ScribeNotes/blob/master/9-1.png)
![](https://github.com/sacrespo/ScribeNotes/blob/master/9-2.png)


##Why learn relational algebra? 

Relational algebra is a language benchmark: if a language can express something as well as relational algebra, it is considered good. 

**Other pros:** 
* Query by example. 
* Novel relationship interfaces. 
* Equi-Joins are a lifestyle.  

**Cons:**
* Limits on nulls,aggregation, recursion, and duplicates, which cannot be expressed. 
* All of the above can be carried out in other ways. 