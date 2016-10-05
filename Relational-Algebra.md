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

###join
- find same attributions 
- concatenate 
- select(c) (A X B)

###Eui-Join

###Natural join
- same name cause different result 

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
* -The more input => the more output grows. 
* -If A ⊇ B => Q(A,T) ⊇ Q(B,T). 
* -Can **compute incrementally**. 
* -This is a desirable property.

However **set difference** is **not monotonic**: 
* -If A ⊇ B => T-A ⊆ T-B  
* -Cannot compute incrementally. 
* -Computers with **blocking**. 
* -This is undesirable.

The difference between **incremental** and **blocking**: 

Given Op1(Op2(table)), where Op1 takes 10 seconds, and Op2 takes 10 seconds: 
* -**Incremental**: overlap execution of Op1 + Op2 (10-11 seconds total) 
* -**Blocking**: Op1 finishes, then Op2 can start (20 seconds total) 

Note: when aggregating data, there is usually an elegant incremental solution. 

##Recap: Joins
-The definition of joins is you do the cross-product, and then filter.
-Join only takes two tables as input. Need to decide which two tables join first.
-Joins are very popular and are natively supported.

Note: We can’t express left outer join and right outer join in relational algebra. The reason is we haven’t introduced NULL in this model.

##Relational Operator: Set Division 
**General Form:**
* -Relations: A(x,y) and B(y). 
* -Division: A/B = { <x> | ∀y ∈ B<x,y> ∈ A }
**Example:** 
* -Relations: A(name,bid) and B(bid), where name is a student’s name and bid is a book ID. 
* -Division: A/B = { <name> | ∀bid ∈ B<name,bid> ∈ A }
* -Meaning: Find ALL students that have reserved all books. 
**Meaning:** 
* -Used to express “ALL.”
* -Example: Which students are registered in ALL courses given by Prof. Wu? 
Such that if Prof. Wu gives a course, then the student is registered in it. 

**Note:**
Division is not supported in most systems: 
* -Huge run-time. 
* -Very expensive. 
* -Not used often. 
* -Blocking operation. Not monotonic.
* -There are alternative methods to accomplish “ALL” statements. 

##Examples of Set Division 

A/R1


A/R2


A/R3


##How to Express Set Division?
* -Find all xs not “disqualified” by some y in B.



**Meaning:** 
* -Disqualified: Students that do not reserve the particular books.
* -Final answer: All students minus disqualified students.

##More Examples
Tables: Book(rid, type) Reserve(sid,rid) Students(sid)
* -Name of students that reserved DB books

Note: If there are many attributes and records, the cardinality is high. The latter one is a more efficient query.
Note: Query optimizer can find the more efficient query!

Students that reserved DB or HCI book (Hint: or operation)


Students that reserved DB and HCI book (Hint: find two sets, and perform intersection)


Students that reserved all books

If primary key of Reserve is (sid, rid) and we forget to use sid, rid, we will still end up with the same result.

Students that reserved all horror books


##Why learn relational algebra? 

Relational algebra is a language benchmark: if a language can express something as well as relational algebra, it is considered good. 

**Other pros:** 
* -Query by example. 
* -Novel relationship interfaces. 
* -Equi-Joins are a lifestyle.  
**Cons:**
* -Limits on nulls,aggregation, recursion, and duplicates, which cannot be expressed. 
* -All of the above can be carried out in other ways. 