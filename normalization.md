# I. General Idea: What and Why
Quick review on steps for a new application

* Requirements Analysis: What are you going to build? 
* Conceptual Database Design: Pen-and-pencil description, ER diagram
* Logical Database Design: Conversion into a relational database schema
* Schema Refinement: Problem fixing, **Normalization** (to reduce redundancy by decompositions to normal form)
* Physical Database Design: Optimize for speed/storage


# II. Redundancy and Anomalies
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

# III. Decomposition
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


# IV. Functional Dependency
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


# V. Normal Forms
We will focus on two normal forms: BCNF and 3NF
* BCNF has no redundancy, but may lose some dependencies
* 3NF preserves all dependencies but has some redundancy

## BCNF: Boyce-Codd Normal Form
* BCNF ensures that no redundancy occurs from Functional Dependencies (other forms redundancy can occur)
* In ensuring no redundancy, BCNF may not preserve all dependencies (ie. constraints may need to be enforced with joins)

### Definition:
Given a relation *R* and a set of functional dependencies *F* of the form (*X* -> *A*), we say that *R* is in BCNF if:

```
for every X -> A in F:
  if (A is in X OR 
      X is a superkey of R)
```

In words:
* If **every** functional dependency is either trivially satisfied (ie. A in X, right-side of arrow is also part of the left-side) or a superkey of all corresponding relations, then the decomposition is in BCNF
 * That is, loop through FDs and if any do not satisfy the "OR" condition above, then decomposition is not in BCNF
  * This is how we "check" if a relation is in BCNF
* Conversely, if **any** functional dependency is neither trivially satisfied nor a superkey, then the entire decomposition is **NOT** in BCNF
* **NOTE:** Superkey: a combination of attributes that can uniquely identify an entity (row) in a relation (table).
 * So a trivial superkey would just be a tuple of every attribute and candidate keys are a subset of superkeys, since they are *minimal* superkeys
 * More importantly, we say an attribute is a superkey of a relation w.r.t. FDs iff every attribute in the relation is defined in the functional dependency (eg. given `ABC` and `A -> BC`, `A` is a superkey of `ABC` because `A` uniquely identifies `B` and `C` as stated in the FD `A -> BC`)

### A Few Examples:
**Given the relation `ABCD` and the FDs `A -> B`, `A -> C`**

| Relations | BCNF? | Reason |
| ------ | ------ | ------ |
| ABCD | NO | `A -> B` is not trivially satisfied (ie. `B` in not in `A`) and `A` is not a superkey of `ABCD` |
| AB, AC, AD | YES | `A` is superkey of `AB` and `AC` (again don't really care about `AD`) |
| AB, AC, D | YES* | **BUT** this is **NOT** desirable because we've lost data about how `D` relates to the rest `A`, `B`, and `C` |
| ABC, D | YES* | `A -> B` and `A -> C` implies `A -> BC`, which is satisfied by this relation; but like the relation above, this is **NOT** desirable because we've lost data about `D` |

So why is `AB, AC, AD` considered a "good" decomposition while `AB, AC, D` is "bad"?
* Because we know `A` is a key of `AB` and `AC`, we can accurately recover the original schema `ABCD` by performing a join on `A`
* Conversely, we cannot do this on `AB, AC, D` because after joining `AB` and `AC` on `A` we have no lossless way of combining `ABC` and `D` (ie. we can only perform a cross-product, which will most likely produce too many tuples)


**Given the relation `SHNAC` and the function dependencies `S -> NA` and `H -> C`**

| Relations | BCNF? | Reason |
| ------ | ------ | ------ |
| SHNAC | NO | for `S -> NA`, `NA` is not in `S` and `S` is not a superkey of `SHNAC` => NOT BCNF |
| SNA, SHC | NO | for `S -> NA`, `S` is a superkey of `SNA`... ignore `SHC` because does not contain `NA`... for `H -> C`, ignore `SNA`... `H` not superkey of `SHC` => NOT BCNF (ie. `H -> C` FD is not satisfied) |
| SNA, HC, SH | YES | for `S -> NA`, `S` is a superkey of `SNA`... ignore `HC` and `SH`... for `H -> C`, ignore `SNA`... `H` is a superkey of `HC`... ignore `SH` => YES BCNF (ie. all FDs satisfied!) |


**Given the relation `IJKLM` and the functional dependency `IJ -> K`**

| Relations | BCNF? | Reason |
| ------ | ------ | ------ |
| IJKLM | NO | Should be pretty obvious by now: `IJ` is not a superkey of `IJKLM` (and `K` is not in `IJ`) |
| IJK, KLM | YES* | `IJ` is a superkey of `IJK`, but this is **NOT** desirable because we cannot recover the original table |
| IJK, IJLM | YES | `IJ` is a superkey of `IJK` and we can perform a lossless join on `IJ` |

**Ok what if we add the functional dependency `L -> M`?**

| Relations | BCNF? | Reason |
| ------ | ------ | ------ |
| IJK, IJLM | NO | Now, because of our new FD `L` must also be a superkey which isn't true for `IJLM` |
| IJK, LM | YES* | Again, technically this is in BCNF, but it is **NOT** desirable because we lose data about how `IJK` relates to `LM` and therefore can't accurately recover the original table |
| IJK, LM, IJL | YES | `IJ` is superkey of `IJK`, `L` is superkey of `LM`, and we can perform a lossless join using `IJL` |

Things to note about this last example:
* We can kind of see a pattern begin to emerge for BCNF:
 * A simple decomposition could just be a relation for each FD and then an additional relation of all the keys if there is no common key between the two tables
 * So for this last example: `IJK` and `LM` are relations for each FD and `IJL` is just a table of the keys
* We can also begin to see a rough outline of the algorithm for decomposing a relation to BCNF
 * Basically, if the relation isn't in BCNF, just keep breaking the relation up along the lines mentioned above until the relation is in BCNF

### A ~More~ Formal Algorithm for BCNF Decomposition
#### Algorithm
```
while BCNF is violated: 
   R with FDs FR
   if X->Y violates BCNF
      turn R into R-Y & XY
```
Intuitively, we can understand this as, "while BCNF is violated, find a relation that is violating a FD and decompose it further by splitting it into two tables: one with all the attributes defined in the FD and another with everything else and the key of the FD (ie. left side of FD = `X`).

#### Step By Step Example
**Given the relation `BCNO` and the FDs `BC -> N` and `N -> BO`, decompose R using the following steps:**
 1. `BCNO` violates BCNF
 2. Break `BCNO` (in this example we use `N -> BO` as the "base" FD)
  * `NBO` is one relation (obtained by just combining attributes of "base" FD)
  * The other relation is simply `R - Y` = `BCNO` - `BO` = `CN`
 3. Now we have `NBO`, `CN` which is in BCNF

**NOTE:** that we have "lost" `BC -> N` since there is no longer a relation that contains all three attributes, but this is ok because we are in BCNF and apparently no one cares :/

## 3rd Normal Form (3NF)
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

# VI. Formalization of Normal Forms
## Closure of FDs
Let's start with an example: 
If we know that Name->BDay, and BDay->Age, then we know that Name->Age

A functional dependency f' is implied by a set F if f' is true when F is true. 

All the functional dependencies that can be implied from F is called the closure (F<sup>+</sup>)

We can construct a closure of a set F using Armstrong's axioms

## Armstrong's Axioms:
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

## Minimum Cover of Functional Dependencies

Closures can allow us to compare sets of FD's meaningfully. 

For example, if F1={A->B, A->C, A->BC}, and F2={A->B, A->C), are they equivalent? Yes! We can get from F2 to F1 using Armstrong's axioms, so F1 is not minimal.

If there's a closure (a maximally expanded set of functional dependencies), then there must be a minimal set as well. 

### Steps:
 1. Turn the FD's into _standard form_
    * Split the FD's so there is only one attribute on the right side
 2. Minimize left side of each FD
    * For each FD, check if we can delete a left attribute using another FD. 
        * Given (A, B, C)->D, and B->C, we can reduce this to (A, B)->D, and B->C
        * Why? B->C, so we can replace C on the left with B, and since we're looking at a set of attributes, this collapses to (A, B)->D
 3. Delete redundant functional dependencies
    * Check each remaining functional dependency and check to see if it can be deleted (if it's in the closure of the other FD's)

**NOTE: You must do the steps in this order**

Examples:
Consider the set A->B, ABC->E, EF->G, ACF->EG

1. First convert to standard form (only one attribute on the right side):
    * We get A->B, ABC->E, EF->G, **ACF->E**, **ACF->G**
2. Minimize left side
    * A->B, **AC->E**, EF->G, ACF->E, ACF->G
        * Why? AC->E and AC->B implies ABC->E
3. Delete redundant FD's
    * A->B, AC->E, EF->G
        * ACF->E implied by AC->E (a smaller set of attributes that determines E), ACF->G implied by AC->E EF->G (we can substitute AC for E to get ACF->G to reconstruct this dependency)

## Principled Decomposition

We eventually want to be able to decompose relation R into sub-relations R1, R2,..., Rn such that we can do a join on R1, R2, ..., Rn and get back the original relation R, while preserving the functional dependencies F of R. 

However, we've seen issues with decomposition:
   
    * Lost joins (can't recover R from R1, R2, ... , Rn)
    
    * Lost dependencies (we cannot enforces all the dependencies when we decompose R into the sub-relations)

###How can we tell if a decomposition will yield lossless joins?

Consider the decomposition of R into R1 and R2

A decomposition is lossless w.r.t F if the closure of F contains one of the following:

(R1 intersect R2) -> R1 OR (R1 intersect R2) -> R2. 

This means that if the intersection of R1 and R2 is a key for either R1 or R2, and is contained in the closure of F, then the decomposition of R into R1 and R2 is lossless. 

**Note: here R1 intersect R2 is simply the intersection of the attributes in the relation. If R1=(A, B) and R2=(B, C), then R1 intersect R2 = B**

Example: 

![](https://github.com/agango/Scribesnotes-image/blob/master/example2.png)

Why is this an example of lossy decomposition? In this case, (R1 intersect R2) = AB intersect BC = B. In table R1, B does not determine anything (we have 2 values of A for the same value of B), and the same for table R2. As we can see, when we join the two relations, we get new rows that were not there before. We have not been able to completely recover the original relation by joining R1 and R2. 

What's correct?

![](https://github.com/agango/Scribesnotes-image/blob/master/example3.png)

Why is this correct?

A does not uniquely determine anything in R1, but it does in R2, and R1 intersect R2 = A, so we get the FD A -> AC (which can be decomposed to A->A, which is trivial, and A->C, which is a FD of R). This functional dependency is in the closure of F. 
 
###What about dependency preservation?

F<sub>R</sub>



