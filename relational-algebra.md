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


Relational Algebra vs. Relational calculus 
result in 3 
RA: 
1+2
2+1
3+0
...
RC: {(a,b) | a,b are integers and a+b = 3}


query is a function over relation instances 

relation
instance is a set of tuples
schema defines field names and types 

how are relations different than generic sets?
can assume item structure ue to schema
some algebra operations (x) need to be modified



Core 5 operations
PROJECT
given attributes to reflect results
duplicate results? No. return a set 
real systems typically don't remove duplicates 
because you want to know the distribution 
deduplication is costly 
SELECT
select subset of rows that satisfy condition (age < 30)
UNION
A op B = Result 
A and B must be union-compatible (types better be compatible )
union remove duplicate 
SET DIFFERENCE
CROSPRODUCT
vertically concatenate tables 
rename duplicate names 
all pairwise combination 
loose foreign key references 

additional operations
interset
A and B must be union compatible 
join
find same attributions 
concatenate 
select(c) (A X B)
Eui-Join
Natural join
same name cause different result 
divide 

Commutatively allowed. 
project different attribute, commutatively is not allowed 

if S1 is very wide, use project narrow down.
think about efficiency 











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
