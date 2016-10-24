##This is the start of a study guide for the midterm.  Please add anything you think would be useful.

## Important Terms

###Chapter 2

<details> 
  <summary>All Terms</summary><br />
***Attribute*** - a property or description of an entity. A toy department employee entity could have attributes describing the employee’s name, salary, and years of service.<br /><br />
***Domain*** - a set of possible values for an attribute.<br /><br />
***Entity*** - an object in the real world that is distinguishable from other objects such
as the green dragon toy.<br /><br />
***Relationship*** - an association among two or more entities.<br /><br />
***Entity set*** - a collection of similar entities such as all of the toys in the toy department.<br /><br />
***Relationship set*** - a collection of similar relationships<br /><br />
***One-to-many relationship*** - a key constraint that indicates that one entity can be associated with many of another entity. An example of a one-to-many relationship is when an employee can work for only one department, and a department can have many employees.<br /><br />
***Many-to-many relationship*** - a key constraint that indicates that many of one entity can be associated with many of another entity. An example of a many-to-many relationship is employees and their hobbies: a person can have many different hobbies, and many people can have the same hobby.<br /><br />
***Participation constraint*** - a participation constraint determines whether relationships must involve certain entities. An example is if every department entity has a manager entity. Participation constraints can either be total or partial. A total participation constraint says that every department has a manager. A partial participation constraint says that every employee does not have to be a manager.<br /><br />
***Overlap constraint*** - within an ISA hierarchy, an overlap constraint determines whether or not two subclasses can contain the same entity.<br /><br />
***Covering constraint*** - within an ISA hierarchy, a covering constraint determines where the entities in the subclasses collectively include all entities in the superclass.<br /><br />
***Weak entity set*** - an entity that cannot be identified uniquely without considering some primary key attributes of another identifying owner entity. An example is including Dependent information for employees for insurance purposes.<br /><br />
***Aggregation*** - a feature of the entity relationship model that allows a relationship set to participate in another relationship set. This is indicated on an ER diagram by drawing a dashed box around the aggregation.
</details>

<details> 
  <summary>Attribute</summary><br />
  A property or description of an entity. A toy department employee entity could have attributes describing the employee’s name, salary, and years of service.
</details>

<details> 
  <summary>Domain</summary><br />
  a set of possible values for an attribute.
</details>

<details> 
  <summary>Entity</summary><br />
  an object in the real world that is distinguishable from other objects such
</details>

<details> 
  <summary>Relationship</summary><br />
  an association among two or more entities.
</details>

<details> 
  <summary>One-to-Many Relationship</summary><br />
  a key constraint that indicates that one entity can be associated with many of another.
</details>

<details> 
  <summary>Many-to-Many Relationship</summary><br />
  a key constraint that indicates that many of one entity can be associated with many of another entity. An example of a    many-to-many relationship is employees and their hobbies: a person can have many different hobbies, and many people can have the same hobby.
</details>

<details> 
  <summary>Participation Constraint</summary><br />
  a participation constraint determines whether relationships must involve certain entities. An example is if every department entity has a manager entity. Participation constraints can either be total or partial. A total participation constraint says that every department has a manager. A partial participation constraint says that every employee does not have to be a manager.
</details>

<details> 
  <summary>Overlap Constraint</summary><br />
  within an **ISA** hierarchy, an overlap constraint determines whether or not two subclasses can contain the same entity.
</details>

<details> 
  <summary>Covering Constraint</summary><br />
  within an **ISA** hierarchy, a covering constraint determines where the entities in the subclasses collectively include all entities in the superclass.
</details>

<details> 
  <summary>Weak Entity Set</summary><br />
  an entity that cannot be identified uniquely without considering some primary key attributes of another identifying owner entity. An example is including Dependent information for employees for insurance purposes.
</details>

<details> 
  <summary>Aggregation</summary><br />
  a feature of the entity relationship model that allows a relationship set to participate in another relationship set. This is indicated on an ER diagram by drawing a dashed box around the aggregation.
</details>

###Chapter 3

***relation schema***

A relation schema can be thought of as the basic information describing
a table or relation. (Note that relation is a set, so rows have to be distinct.) This includes a set of column names, the data types associated
with each column, and the name associated with the entire table. For example, a
relation schema for the relation called 

Students could be expressed using the following
representation:

Students(sid: **string**, name: **string**, login: **string**,
age: **integer**, gpa: **real**)

***relational database schema***

A relational database schema is a collection of relation schemas, describing one or more
relations.

***domain***

Domain is synonymous with data type. Attributes can be thought of as columns in a
table. Therefore, an attribute domain refers to the data type associated with a column.

***relation instance***

A relation instance is a set of tuples (also known as rows or records) that each conform
to the schema of the relation.

***relation cardinality***

The relation cardinality is the number of tuples in the relation.

***relation degree***

The relation degree is the number of fields (or columns) in the relation.

***integrity constraint***

A condition specified on a database schema and restricts the data that can be stored in an instance of the database. If a database instance satisfies all the integrity constraints specified on the database schema, it is a legal instance. A DBMS enforces integrity constraints, in that it permits only legal instances to be stored in the database.

***domain constraint***

Domain constraints in the schema specify an important condition that we want each instance of the relation to satisfy: The values that appear in a column must be drawn from the domain associated with that column. Thus, the domain of a field is essentially the type of that field, in programming language terms, and restricts the values that can appear in the field.

***Key constraint***

A key constraint is a statement that a certain minimal subset of the fields of a relation is a unique identifier for a tuple.
A set of fields that uniquely identifies a tuple according to a key constraint is called a candidate key for the relation.


***definition of a (candidate) key***

1. Two distinct tuples in a legal instance (a legal instance is an instance that satisfies all Integrity Constraints,
including the key constraint) cannot have identical values in all the fields of a key.  This means that a candidate key 
is the minimum set of attributes used to uniquely identify a tupel or record in a table.

2. No subset of the set of fields in a key is a unique identifier for a tuple.  This means that if for example, {eid} was
a candidate key, {eid,name} could not be a candidate key because {eid} is a subset of {eid,name}.

Example:

|eid|eage|ename|
|---:|---|---|
|1|	20|	lisa|
|2	|20	|lisa|
|3	|30	|andy|
|2	|30	|andy|


Here, eid does not uniquely identify a tupel however, the combination of {eid,eage} does uniquely define each tuple so {eid,eage} is a candidate key.

Example:

|eid|eage|ename|
|---:|---|---|
|1|	20|	tony|
|2	|20	|lisa|
|3	|30	|andy|
|2	|30	|bill|

Here, the combination of {eid,eage} and {eid,ename} are both candidate keys.

***superkey***

A ***superkey*** is acandidate key plus zero or more attributes. Every candidate key is a superkey however the reverse is not true - every superkey cannot be a candidate key.  The minimal superkey is the candidate key.

Example:

|employeeID|salary|employeename|
|---:      |------|---|
|1         |	22,000	|ernie|
|2	   |	40,000	|alice|
|3	   |	22,000	|bart|
|4	   |	45,000	|ernie|

In this example, empoyeeID is the only candidate key

What are all the superkey's?

- employeeID
- employeeID + salary
- employeeID + employeename
- employeeID + salary + employeename

As stated, the minimal superkey is the candidate key.  Therefore, the candidate key in this case is empoyeeID.

Note that every relation is guaranteed to have a key. Since a relation is a set of tuples, the set of all fields is always a superkey.

***primary key***

A ***primary key*** is a candidate keys which has no NULL values

|employeeID	|salary|	employeename|
|---:|---|---|
|1	|	22,000	|ernie|
|2	|	40,000	|alice|
|3	|	22,000	|bart|
|4	|	45,000	|billy|

So in this relation, employeeID is a candidate key.  Also, no values in the employeeID are null, so employeeID is also the primary key.  Also employeename is a candidate key and since it does not contain any null values, it could be selected as a primary key.

Out of all the available candidate keys, a database designer can identify a primary key.  

##SQL and Relational Algebra  

Consider the following schema - the primary keys are __*bold italic*__:  

Suppliers(__*sid*__: integer, sname: string, address: string)   
Parts(__*pid*__: integer, pname: string, color: string)   
Catalog(__*sid: integer, pid: integer*__, cost: real)  

__*Find the names of suppliers who supply a red part.*__  

RA&nbsp;&nbsp;&nbsp;**π<sub>sname</sub>(π<sub>sid</sub>((π<sub>pid</sub>σ<sub>color='red'</sub>Parts)⋈Catalog)⋈Suppliers)**

```sql
SELECT S.sname          
FROM Suppliers S, Parts P, Catalog C   
WHERE P.color=’red’ AND C.pid=P.pid AND C.sid=S.sid  
```

__*Find the sids of suppliers who supply some red or green part.*__

RA&nbsp;&nbsp;&nbsp;&nbsp;**π<sub>sid</sub>(π<sub>pid</sub>(σ<sub>color='red' V color='green'</sub>Parts)⋈Catalog)** 

```sql
SELECT C.sid   
FROM Catalog C, Parts P   
WHERE (P.color = ‘red’ OR P.color = ‘green’)   
AND P.pid = C.pid
```
__*Find the sids of suppliers who supply some red part or are at 221 Packer Street.*__  

RA&nbsp;&nbsp;&nbsp;&nbsp;**ρ(R1,π<sub>sid</sub>((π<sub>pid</sub>σ<sub>color='red'</sub>Parts)⋈Catalog))**   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**ρ(R2,π<sub>sid</sub>σ<sub>address='221PackerStreet'</sub>Suppliers)**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**R1∪R2**  

```sql
SELECT S.sid  
FROM Suppliers S  
WHERE S.address = ‘221 Packer street’  
OR S.sid IN ( SELECT C.sid  
              FROM Parts P, Catalog C  
              WHERE P.color=’red’ AND P.pid = C.pid )  
```

__*Find the sids of suppliers who supply some red parts and some green parts.*__  

RA&nbsp;&nbsp;&nbsp;&nbsp;**ρ(R1,π<sub>sid</sub>((π<sub>pid</sub>σ<sub>color='red'</sub>Parts)⋈Catalog))**   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**ρ(R2,π<sub>sid</sub>((π<sub>pid</sub>σ<sub>color='green'</sub>Parts)⋈Catalog))**     
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**R1∩R2**  

```sql
SELECT C.sid  
FROM Parts P, Catalog C  
WHERE P.color = ‘red’ AND P.pid = C.pid  
AND EXISTS ( SELECT P2.pid  
             FROM Parts P2, Catalog C2  
             WHERE P2.color = ‘green’ AND C2.sid = C.sid  
             AND P2.pid = C2.pid )
```

__*Find the sids of suppliers who supply every part.*__  


RA&nbsp;&nbsp;&nbsp;&nbsp;**(π<sub>sid,pid</sub>Catalog)/(π<sub>pid</sub>Parts)**  

```sql
SELECT C.sid  
FROM Catalog C  
WHERE NOT EXISTS (SELECT P.pid  
                  FROM Parts P  
                  WHERE NOT EXISTS (SELECT C1.sid  
                                    FROM Catalog C1  
                                    WHERE C1.sid = C.sid  
                                    AND C1.pid = P.pid))  
```

__*Find the sids of suppliers who supply every red part.*__  

RA&nbsp;&nbsp;&nbsp;&nbsp;**(π<sub>sid,pid</sub>Catalog)/(π<sub>pid</sub>σ<sub>color='red'</sub>Parts)**  

```sql
SELECT C.sid  
FROM Catalog C  
WHERE NOT EXISTS (SELECT P.pid  
                  FROM Parts P    
                  WHERE P.color = 'red'  
                  AND (NOT EXISTS (SELECT C1.sid  
                                   FROM Catalog C1  
                                   WHERE C1.sid = C.sid AND  
                                   C1.pid = P.pid)))
```

__*Find the sids of suppliers who supply every red or green part.*__

RA&nbsp;&nbsp;&nbsp;&nbsp;**(π<sub>sid,pid</sub>Catalog)/(π<sub>pid</sub>σ<sub>color='red' V color='green' </sub>Parts)**  

```sql
SELECT C.sid  
FROM Catalog C  
WHERE NOT EXISTS (SELECT P.pid  
                  FROM Parts P    
                  WHERE (P.color = 'red' OR P.color = 'green')  
                  AND (NOT EXISTS (SELECT C1.sid  
                                   FROM Catalog C1  
                                   WHERE C1.sid = C.sid AND  
                                   C1.pid = P.pid)))  
```

 __*Find the sids of suppliers who supply every red part or supply every green part.*__  
 
RA&nbsp;&nbsp;&nbsp;&nbsp;**ρ(R1,((π<sub>sid,pid</sub>Catalog)/(π<sub>pid</sub>σ<sub>color='red'</sub>Parts)**   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**ρ(R2,((π<sub>sid,pid</sub>Catalog)/(π<sub>pid</sub>σ<sub>color='green'</sub>Parts)**    
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**R1∪R2**  

```sql
SQL SELECT C.sid  
    FROM Catalog C  
    WHERE (NOT EXISTS (SELECT P.pid  
    FROM Parts P    
    WHERE P.color = 'red' AND    
       (NOT EXISTS (SELECT C1.sid  
       FROM Catalog C1  
       WHERE C1.sid = C.sid AND  
       C1.pid = P.pid)))  

       OR (NOT EXISTS (SELECT P1.pid  
       FROM Parts P1    
       WHERE P1.color = 'green' AND  
       (NOT EXISTS (SELECT C2.sid  
       FROM Catalog C2  
       WHERE C2.sid = C.sid AND  
       C2.pid = P1.pid)))  
```

__*Find pairs of sids such that the supplier with the ﬁrst sid charges more for some part than the supplier with the second sid.*__  

RA&nbsp;&nbsp;&nbsp;&nbsp;**ρ(R1,Catalog)**    
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**ρ(R2,Catalog)**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;π<sub>R1.sid,R2.sid</sub>(σ<sub>R1.pid=R2.pid∧R1.sid ≠ R2.sid∧R1.cost>R2.cost</sub>(R1×R2))  

```sql
SQL SELECT C1.sid C2.sid  
    FROM Catalog C1, Catalog C2  
    WHERE C1.pid = C2.pid AND  C1.sid <> C2.sid  
    AND C1.cost > C2.cost
```

__*Find the pids of parts supplied by at least two diﬀerent suppliers.*__  

RA&nbsp;&nbsp;&nbsp;&nbsp;**ρ(R1,Catalog)**    
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**ρ(R2,Catalog)**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;π<sub>R1.pid</sub>σ<sub>R1.pid=R2.pid∧R1.sid ≠ R2.sid</sub>(R1×R2)    

```sql
SQL SELECT C.pid
    FROM Catalog C
    WHERE EXISTS (SELECT C1.sid
    FROM Catalog C1
    WHERE C1.pid = C.pid AND C1.sid <> C.sid)
``` 

__*Find the pids of the most expensive parts supplied by suppliers named Yosemite Sham.*__

RA&nbsp;&nbsp;&nbsp;&nbsp;**ρ(R1,π<sub>sid</sub>σ<sub>sname='YosemiteSham'</sub>Suppliers)**    
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**ρ(R2,R1 ⋈ Catalog)**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**ρ(R3,R2)**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**ρ(R4(1->sid, 2->pid, 3->cost),σ<sub>R3.cost < R2.cost </sub>(R3xR2))**    
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**π<sub>pid</sub>(R2-π<sub>sid,pid,cost</sub>R4)**  

```sql
SQL SELECT C.pid
    FROM Catalog C, Suppliers S
    WHERE S.sname = 'Yosemite Sham' AND C.sid = S.sid
    AND C.cost ≥ ALL (Select C2.cost
    FROM Catalog C2, Suppliers S2
    WHERE S2.sname = 'Yosemite Sham' AND C2.sid = S2.sid)
```


## Additional SQL queries with Relational Algebra Equivalents  

Instance 83  of Sailors    

|sid| sname |rating|age |
|---|-------|------|----|
|22 |Dustin |   7  |45.0|
|29 |Brutus |   1  |33.0|
|31 |Lubber |   8  |55.5|
|32 |  Andy |   8  |25.5|
|58 | Rusty |  10  |35.0|
|64 |Horatio|   7  |35.0|
|71 | Zorba |  10  |16.0|
|74 |Horatio|   9  |35.0|
|85 |  Art  |   3  |25.5|
|95 |  Bob  |   3  |63.5|

Instance R2 of Reserves  

|sid|bid|   day  |
|---|---|--------:|
|22 |101|10/10/98|
|22 |102|10/10/98|
|22 |103|10/8/98 |
|22 |104|10/7/98 |
|31 |102|11/10/98|
|31 |103|11/6/98 |
|31 |104|11/12/98|
|64 |101|9/5/98  |
|64 |102|9/8/98  |
|74 |103|9/8/98  |

Instance HI of Boats  

|bid|  bname  |color|
|---|---------|-----|
|101|Interlake|blue |
|102|Interlake| red |
|103| Clipper |green|
|104|  Marine | red |

### Some queries related to the above table  

__*Find the names of sailors who have reserved boat 103.*__

```sql
SELECT S.sname  
FROM Sailors S  
WHERE S.sid IN (SELECT R.sid  
                FROM Reserves R  
                WHERE R.bid = 103)
```

The set contains the names of the sailors associated with sid 22,31, and 74.  This query could be modified to find all sailors who have not reserved boat 103 by replacing __IN__ by __NOT IN__.  

RA&nbsp;&nbsp;&nbsp;**π<sub>sname</sub>(σ<sub>bid='103'</sub>Reserves)⋈Sailors)**  

__*Alternatively*__

```sql
SELECT S.sname  
FROM Sailors S, Reserves R  
WHERE S.sid = R.sid AND R.bid = 103  
```

__*Find the names of sailors who have reserved boat number 103. - using EXISTS*__  

```sql
SELECT S.sname  
FROM Sailors S  
WHERE EXISTS (SELECT *    
              FROM Reserves R  
              WHERE R.bid = 103 AND S.sid = R.sid)
```

__*Find the names of sailors who have reserved a red boat.*__  

```sql
SELECT S.sname  
FROM Sailors S  
WHERE S.sid IN (SELECT R.sid  
                FROM Reserves R  
                WHERE R.bid =IN (SELECT B.bid  
                                 FROM Boats B  
                                 WHERE B.color = 'red'))
```

The result of this query would be to return the names Dustin, Lubber, and Horatio.  To find the names of sailors who have not reserved a red boat we replace the outermost __IN__ by __NOT IN__.  

RA&nbsp;&nbsp;&nbsp;**π<sub>sname</sub>(π<sub>sid</sub>((π<sub>bid</sub>σ<sub>color='red'</sub>Boats)⋈Reserves)⋈Sailors)**  

__* Find sailors whose rating is better than some sailor called Horatio.*__  

```sql
SELECT S.sid  
FROM Sailors S  
WHERE S.rating > ANY (SELECT S2.rating  
                      FROM Sailors S2   
                      WHERE S2.name = 'Horatio')  
```

On instance 83, this computes the sids 31, 32, 58, 71, and 74.  

__*Find sailors whose rating is better than every sailor called Horatio*__  


```sql
SELECT S.sid  
FROM Sailors S  
WHERE S.rating > ALL (SELECT S2.rating  
                      FROM Sailors S2   
                      WHERE S2.name = 'Horatio')  
```

As can be seen, the __ANY__ in the previous query was replaced with an __ALL__ in this one.  This query would return sids 58 and 71.  

__*Find the sailor's with the highest rating.*__

```sql
SELECT S.sid  
FROM Sailors S  
WHERE S.rating > ALL (SELECT S2.rating  
FROM Sailors S2)  
```
This query would return the sids of the sailors with a rating of 10 belonging to sid 58 and 71.  