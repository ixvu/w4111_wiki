# Lecture 16: Normalization

## General Idea: What and Why
Quick review on steps for a new application

* Requirements Analysis: What are you going to build? 
* Conceptual Database Design: Pen-and-pencil description, ER diagram
* Logical Database Design: Conversion into a relational database schema
* Schema Refinement: Problem fixing, **Normalization** (to reduce redundancy by decompositions to normal form)
* Physical Database Design: Optimize for speed/storage


## Redundancy and Anomalies
#### Example 1: 
* people have names and addresses 
* hobbies have costs
* many-to-many relationship between people and hobbies

<img src="https://github.com/pw2393/testrepo/blob/master/example1.PNG" width="450">

Some information are stored repeated, e.g. (Eugene, amsterdam), (Bob, 40th), (cheese, $).
These redundancies lead to anomalies:

* Update Anomaly
  * In Example: If we update Eugene's address, we need to change every record related to Eugene. The same for Bob.
  * Definition: If one copy of such repeated data is updated, an inconsistency is created unless all copies are similarly updated.
* Insertion Anomaly
  * In Example: Suppose the primary key is (sid, hobby), it's not allowed to add a person without hobby.
  * Definition: It may not be possible to store certain information, unless some other information is stored as well.
* Deletion Anomaly: 
  * In Example: If we delete a hobby e.g. swimming, Shaq's record will be deleted unless it's allowed to update all 'swimming' to 'NULL'.
  * Definition: It may not be possible to delete certain information without losing some other information.

## Decomposition
1. What is decomposition?
2. What criteria of decomposition do we care?

### 1. What is decomposition?
Fortunately, many problems arising from redundancy can be addressed by replacing a relation **R** with a collection of 'smaller' relations,
s.t.
* each contain subset of attributes in **R**,
* and together include all attributes in **R**.
For example, a relation with attributes (ABCD) can be replaced with (AB,BCD) or (AB, BC, CD).

### 2. What criteria of decomposition do we care?
#### Example 2_1: A bad attempt

<img src="https://github.com/pw2393/testrepo/blob/master/example2_1.PNG" width="450">

This is a bad attempt. It costs no less memory, and more importantly it's a lossy decomposition.

Explanation: If we read 'person' relation only, we cannot understand the meaning of attribute 'cost' without join, since it's the hobby's cost. However, if we equi-join two relations on sid, it actually cannot recover the original relation. 
Because we have two tuples with 'sid = 1', equi-join is ambiguous that for instance cost '$$' can be associated with 'cheese' or 'trucks'. The decomposition breaks the original association between hobby and cost, and thus loses information.

#### Example 2_2: A familiar attempt

<img src="https://github.com/pw2393/testrepo/blob/master/example2_2.PNG" width="450">

It works out, thanks to heuristic ER diagram.

Explanation: In ER model, attributes only describe the entity they belongs to, and every entity table has a primary key that determines all values of a tuple. In this sense, both person and hobby are independent entities with meaningful attributes. The relationship table describes the association between person and hobby, and if we join it with person and hobby, we will recover the original table without any loss of information.

#### Desirable criteria of decomposition
* Lossless-join: enable us to recover original relation from the smaller relations. **π<sub>X</sub>(R)⋈π<sub>Y</sub>(R) = R**
* Dependency-preservation: enable us to enforce any constraint on the original relation by simply enforcing some constraints on each of the smaller relations, i.e. to enforce constraints even without joins.

#### Decompose or not? A trade-off
* Advantages
  * Less anomalies: We make changes in one place that apply everywhere, and it eliminates possibility of data getting “out of sync”.
  * Direct and fast query: By accessing a sub-section of the data for some queries
* Disadvantages
  * If always need all data together, joins may be slower
  * Less flexible because there is no redundancy 

#### How can we systematically decompose relations according to the desirable criteria?
See below: functional dependencies and normal forms


## Functional Dependency
1. What is FD?
2. How do we use FD?
3. Where to find FD?

### 1. What is FD?
**Definition** Let X, Y be sets of attributes. If for any tuples t1 and t2 that t1[X] = t2[X] implies t1[Y] = t2[Y], we then call X->Y a functional dependency.

Intuitively, FD X->Y means that if two tuples agree on the values in attributes X, they mush also agree on the values in attributes Y. 

#### Example 3: FDs

<img src="https://github.com/pw2393/testrepo/blob/master/example3.png" width="450">

* sid -> name, address 
* hobby -> cost
* hobby -> name, address cost

For `sid -> name, address`, sid sufficient to identify name and address, but not hobby.
In other words, if 2 records have the same sid, their name and address are the same.

#### Note that
* A functional dependency is a integrity constraint. It is a statement about _all_ instances of relations.
* Redundancy depends on FDs (SUPER IMPORTAANT): 
  * FD X->Y definition does not require that X be minimal. X can be either super key or candidate key.
  * If K is candidate key of R, then K -> R. 
  * When K is not a table key, then it can be copied around, so it can introduce redundancy.
  * In other words, if R has no redundancy, FDs are key constraints.

### 2. How do we use FDs (at a high level)
* Start with some relational schema
* Find out its FDs
* Use these to design a better schema, with less redundancy and anomalies.

### 3. Where to find FDs?
We can never deduce that an FD does hold by looking at one or more instances of the relation, because an FD, like other ICs, is a statement about all possible legal instances of the relation.


## Normal forms
Normalization functions to reduce data redundancy and improve data integrity.
Decomposition is a good way of normalization.
Normal forms can help us determine if/how we want to decompose a relation.

The point of these normal forms is that we will show algorithms to achieve them. 
In order to make these algorithms, we need good definitions of what they are.
We have two options of normal forms: 

* BCNF is no redundancy, but loses dependencies. 
* 3NF preserves dependencies but has some redundancy.

### Boyce Codd Normal Form (BCNF)
BCNF ensures that no redundancy can be detected using FD information alone.
BCNF has no redundancy, but the decomposition into it may lose dependencies (that R constraints must be enforced with joins).

#### Definition:
* R: relation
* F: set of FDs given to hold over R
* X: subset of the attributes of R
* A: one attribute of R
* R is in BCNF if:
```
for every FD(X -> A) in F:
  A is in X 
  OR
  X is a superkey of R
```
Note that we assume here all FDs are of the form (set of attributes X -> single attribute A).
Here if we say X is a superkey, it is greater or equal to the size of a candidate key.

For example, 'hobby' relation in example2_2 is in BCNF, because (i) for ({name}->name), name is in {name} and (ii) for ({name}->cost), name is a key of R.

If any FDs in R does not satisfy the 'OR' condition above, R is not in BCNF.
This 'for loop' can be used to **check** if a relation is in BCNF.

#### More Examples:
Given S->NA, H->C, and BOLD primary key attributes, we have

| Relations | BCNF? | Reason
| ------|------| ------|
|**SH**NAC | NO | for H->C, C does not belong to SH, and H is not a superkey
|**S**NA, **SH**C | NO | for H->C, C does not belong to SH, and H is not a superkey
|**S**NA, **H**C, **S**H | YES | Fits the definition

## Decompose table to BCNF by FDs
### Algorithm
```java
while BCNF is violated: 
   R with FDs FR
   if X->Y violates BCNF
      turn R into R-Y & XY
```
### Step by step example
Table: Branch,Customer,banker Name,Office  BCNO

FDs:
   1. Name -> Branch, Office  N -> BO 
   2. Customer,Branch->Name  CB->N

Steps:
   1. R: BCNO, violates BCNF
   2. X->Y = N -> BO
      1. R-Y = BCNO - BO = CN
      2. XY = NBO
      3. R = (NBO,CN)
   3. R: (NBO,CN) is a BCNF, end while
   4. miss CB->N

#3rd Normal Form (3NF)
We relax BCNF (BCNF is a stricter version of 3NF):
    * F: a set of functional dependencies over relation R
        * for (X->Y) in R:
            * Y is in X OR X is a superkey of R OR **Y is some attribute in a key in R**
This new condition is NOT trivial, as this key is minimal, not a superkey. This guarantees lossless join AND dependency preserving decomposition
Tradeoff is that a relation in 3NF form does retain some redundancies. 

Let's look at the pizza example:
![](https://github.com/agango/Scribesnotes-image/blob/master/image.png)
  
The key for this table is (Pizza, Type)-unique pairing of attributes, each pizza has one of every type
We have the functional dependencies Topping->Type, and Pizza, Type->Topping. This relation is not in BCNF form, but is in 3NF form because the FD Topping->Type is no longer violated, as Type is a part of the key. However, this table does have redundancies.

I thought we were trying to get rid of redundancies?

Yes, but we can improve our data design abilities by understanding redundancy 

###Closure of FD's

Let's start with an example: 
If we know that Name->BDay, and BDay->Age, then we know that Name->Age

A functional dependency f' is implied by a set F if f' is true when F is true. 

All the functional dependencies that can be implied from F is called the closure (F<sup>+</sup>)

We can construct a closure of a set F using Armstrong's axioms

### Armstrong's Axioms:
* Reflexivity: if attribute Y is a subset of key X, then X->Y
    The "trivial" rule-a column always determines itself, and a set of column always determines a subset of that set
    * A->A
    * (X, Y)->Y
* Augmentation: if X->Y, then XZ->YZ for any Z
    * Example: A->B, C->C by reflexivity. So, (A,C)->(B,C)
* Transitivity: if X->Y and Y->Z, then X->Z
    * Apply them in sequence. If we know (x, y), we know that from X=x we can get Y=y. Similarly, if we know (y, z), we know that from Y=y we can get Z=z. Therefore, from X=x we can get Y=y, and since we know y, we can get Z=z. 

Example: Consider the functional dependency F=(A->B, B->C, CB->E)
Is A->E in the closure?
Solution: 
* A->B (given) 
* A->AB (augmenting A on both sides, AA can be reduced to A-remember that a functional dependency is a constraint between two sets of attributes in a relation, and a set requires distinct elements)
* A->BB (apply A->B)
* A->BC (apply B->C)
* BC->E (given)
* A->E (transitivity)

### Minimum Cover of Functional Dependencies
