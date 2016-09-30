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
