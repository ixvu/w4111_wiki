## Midterm 2 Study Guide  
  
###__Important Definitions with Examples__  

__*Superkey*__  
  
Given the following EMPLOYEE relation: 

|_**SSn**_|Ename|Bdate|Address|Dnumber|
|---|---|---|---|---|  
  
where SSn is the __key__ for the relation and {Ssn}, {Ssn, Ename}, {Ssn,Ename,Bdate}, and any set of attributes that includes Ssn are all __superkeys__. If a relation schema has more than one key, each is called a candidate key. One of the candidate keys is arbitrarily designated to be the primary key, and the others are called secondary keys. In a practical relational database, each relation schema must have a primary key. If no candidate key is known for a relation, the entire relation can be treated as a default superkey. In the previous schema, {Ssn} is the only candidate key for EMPLOYEE, so it is also the primary key. 

__*Prime attribute verses non-prime attribute*__  

An attribute of relation schema R is called a __prime attribute__ of R if it is a member of some candidate key of R. An attribute 
is called __nonprime__ if it is not a prime attribute—that is, if it is not a member of any candidate key.  
  
Given the following schema, where the primary key for the relation is {Ssn,Pnumber}, both Ssn and Pnumber are __prime attributes__ and the __Hours__ attribute is __nonprime__:  

|_**Ssn**_|_**Pnumber**_|Hours|  
|---|---|---|  

  
__*Full Functional Dependency verses Partial Functional Dependency*__  
  
These topics deal primarily with deciding whether a relation schema is in 2NF.  Althought not covered specifically in class, they are important topics to understand for the upcoming examples.  

A relation schema R is in 2NF if every nonprime attribute A in R is fully functionally dependent on the primary key of R. 

Second normal form (2NF) is based on the concept of full functional dependency. A functional dependency X → Y is a __full functional dependency__ if removal of any attribute A from X means that the dependency does not hold any more. A functional dependency X→Y is a __partial dependency__ if some attribute A ε X can be removed from X and the dependency still holds.  In the EMP_PROJ relation below,

_EMP_PROJ_ relation  

|_**Ssn**_|_**Pnumber**_|Hours|Ename|Pname|Plocation|
|---|---|---|---|---|---|

and the _EMP_PROJ_ relation functional dependencies given below:  

- __FD1__ {Ssn,Pnumber} → Hours
- __FD2__ Ssn → Ename 
- __FD3__ Pnumber → {Pname,Plocation} 
  
We can see the following:  

- {Ssn, Pnumber} → Hours is a fully functionally dependent because neither of the following hold:   
    - Ssn → Hours  
    - Pnumber → Hours  
- However the dependency   
    - {Ssn, Pnumber} → Ename is partially functionally dependent because FD2 = Ssn → Ename and Ssn is only part of the primary key of the relation.  
  
If a relation schema is not in 2NF, it can be second normalized into a number of 2NF relations in which nonprime attributes are associated only with the part of the primary key on which they are fully functionally dependent. Therefore,the functional dependencies FD1, FD2, and FD3 previously shown, lead to the decomposition of EMP_PROJ into the three relation schemas EP1, EP2, and EP3 as shown below, each of which is in 2NF.  

_EP1 relation_

|_**Ssn**_|_**Pnumber**_|Hours|
|---|---|---|

_EP2 relation_

|_**Ssn**_|Ename|
|---|---|

_EP3 relation_

|_**Pnumber**_|Pname|Plocation
|---|---|---|  
  
###__Finding the Closure Set of Attributes__  
  
Being able to find the the closure of a set of attributes is an important step in finding the candidate keys of a relation.  We begin by finding the closure of (AB)<sup>+</sup> in the following relation:  

R(ABCDEF)  
  
Given the following functional dependencies:  

1) AB-->C  
2) BC-->AD  
3) D-->E  
4) CF-->B  
  
Find (AB)<sup>+</sup>  
&nbsp;&nbsp;= AB&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Reflexivity  
&nbsp;&nbsp;= ABC&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Given FD 1  
&nbsp;&nbsp;= ABCD&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Given FD 2  
&nbsp;&nbsp;= ABCDE&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Given FD 3  
  
So (AB)<sup>+</sup> is __ABCDE__  
  
###__Irreducable Set or Canonical Form of Functional Dependencies__  
  
Used to determine if an attribute of a functional dependency is extraneous.  An attribute is said to be extranious if we can remove it without changing the closure of the set of functional dependencies.  
  
Given the following relation:  
  
R(WXYZ)  
  
And the following functional dependencies:  
  
1) X-->W  
2) WZ-->XY  
3) Y-->WXZ  

__Step 1__: Apply Decomposition Rule:  
  
This means that you perform the following decomposition:  
  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&alpha;-->&beta;&gamma;  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&alpha;-->&beta;   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&alpha;-->&gamma;  
  
So we end up with the following:  
  
1) X-->W  
2) WZ-->X  
3) WZ-->Y  
4) Y-->W  
5) Y-->X  
6) Y-->Z  
  
__Step 2__: Determine if any of the FD are redundant. We do this by determining the closure of each attribute along with testing each of the FD one at a time to see if they are redundant as shown:  
  
Using FD number 1 compute (X)<sup>+</sup>  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;X<sup>+</sup>=XW  

Now compute (X)<sup>+</sup> again except this time do not consider FD number 1.  If you can still find (X)<sup>+</sup> without FD number 1 then this FD is redundant.  

(X)<sup>+</sup> without FD number 1:  
  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;X<sup>+</sup>=X  
  
This proves that FD number 1 is necessary and is not redundant.  
  
Using FD number 2 compute (WZ)<sup>+</sup>  
  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;WZ<sup>+</sup>=WZXY  
  
So it shows that with (WZ)<sup>+</sup> all attributes can be found.  
  
Now compute (WZ)<sup>+</sup> again except this time do not consider FD number 2.  If you can still find (WZ)<sup>+</sup> without FD number 2 then this FD is redundant.  
  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;WZ<sup>+</sup>=WZYX  
  
We see that we can find WZ<sup>+</sup> without FD number 2.  FD number 2 is therefore redundant.  
  
Continuing with this process allows to determing that there are only four of the original six FD that are required to find all attributes.  They are:  
  
1) X-->W   
2) WZ-->Y  
3) Y-->X  
4) Y-->Z  
  
__Step 3__: The final step is to determine if any of the FD with more than one attribute on the left hand side contain any attributes that are unnecessary in determining the closure of the particular FD.  In our example, we have only one FD to consider - FD number 2:  
  
2) WZ-->Y  

We compare the closure of WZ<sup>+</sup> with the closure of W<sup>+</sup> and Z<sup>+</sup> to see if either of the left hand side attributes alone can find the full closure WZ<sup>+</sup>.  If either of them can, then the other attribute can be disgarded.  
  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;WZ<sup>+</sup>=WZYX&nbsp;&nbsp;&nbsp;found by FD 2 and FD 3  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;W<sup>+</sup>=W  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Z<sup>+</sup>=Z  
  
So in this example, if W<sup>+</sup> had found the same closure as WZ<sup>+</sup>, then attribute Z would have been redundant.  If  Z<sup>+</sup> had found the same closure as WZ<sup>+</sup>, the attribute W would have been redundant.  But as we see, neither attribute alone was able to find the full closure of WZ<sup>+</sup>, so both left hand attributes of FD 2 are required.  
  
###__Finding Candidate Keys__  
  
We begin by finding the candidate key(s) of the following relation.  It is important to remember that only minimal superkey's become candidate keys:  
  
R(ABCD)  
  
and the following functional dependencies:  
  
1) A-->BCD  
2) AB-->CD  
3) ABC-->D  
4) BD-->AD  
5) C-->AD  
  
|FD|Superkey|candidate key|  
|---|---|---|  
|A-->BCD|Yes|Yes|  
|AB-->CD|Yes|No|  
|ABC-->D|Yes|No|  
|BD-->AD|Yes|Yes|  
|C-->AD|No|No|  
  
When can you say that a superkey is minimal and consequently is a candidate key?  
  - __If a superkey has a subset of its attributes that are another superkey, then it is not a candidate key.__  
  - __Alternatively, if a superkey has no subset of attributes that are themselves superkeys, then it is a candidate key.__  
  
For example, ABC and AB are both superkeys.  Since AB is also a superkey, then this means ABC is not minimal and not a candidate key.  
The same logic can be considered for keys AB and A.  We have already decided that both AB and A are both superkeys.  Since A is also a superkey, this means that AB is not minimal.  Since superkey A does not have any subsets, it alone is the only candidate key for the relation.  
  
Now consider the last superkey __BD__.  Is it a candidate key?  We begin by asking if there is any proper subset of __BD__ that is also a superkey?  The only subsets of __BD__ are __B__ and __D__ and neither of these individual attributes are superkeys.  So the answer is __yes__, __BD__ is also a candidate key of the relation.  
   
###__Finding Number of Candidate Keys Additional Examples__  
  
__Example 1__  
  
Finding the candidate key(s) of the following relation.  
  
R(ABCDEFGH)  
  
and the following functional dependencies:  
  
1) AB-->C  
2) A-->DE  
3) B-->F  
4) F-->GH  
  
__Step 1__: We start by looking for attributes that do not appear on the right hand side of any of the functional dependencies.  This implies that it cannot be found through any of the functional dependencies and it is consequently part or potentially singly comprises the candidate key of the relation.  
  
By looking at the four functional dependencies, we see that neither A or B appear on the right hand side of any of the FD.  This means that AB is either part of or the entire candidate key of the relation.   
  
__Step 2__:Now we find the closure of AB<sup>+</sup>  
  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;AB<sup>+</sup>=ABCDEFGH&nbsp;&nbsp;&nbsp;  
  
So AB is both the candidate key and superkey of relation R.  
  
__Example 2__  
  
Finding the candidate key(s) of the following relation.  
  
R(ABCDEFGH)  
  
and the following functional dependencies:  
  
1) AB-->C  
2) BD-->EF  
3) AD-->G  
4) A-->H  
  
__Step 1__: We start by looking for attributes that do not appear on the right hand side of any of the functional dependencies.  This implies that it cannot be found through any of the functional dependencies and it is consequently part or potentially singly comprises the candidate key of the relation.  
  
By looking at the four functional dependencies, we see that neither A,B or D appear on the right hand side of any of the FD.  This means that ABD is either part of or the entire candidate key of the relation.   
  
__Step 2__:Now we find the closure of ABD<sup>+</sup>  
  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ABD<sup>+</sup>=ABCDEFGH&nbsp;&nbsp;&nbsp;  
  
So ABD is both the candidate key and superkey of relation R.  
  
__Example 3__  
  
Finding the candidate key(s) of the following relation.  
  
R(ABCDE)  
  
and the following functional dependencies:  
  
1) BC-->ADE  
2) D-->B  
  
__Step 1__: We start by looking for attributes that do not appear on the right hand side of any of the functional dependencies.  This implies that it cannot be found through any of the functional dependencies and it is consequently part or potentially singly comprises the candidate key of the relation.  
  
By looking at the four functional dependencies, we see that the only attribute that does not appear on the right hand side of any of the FD's is attribute C.  This means that C is either part of or the entire candidate key of the relation.   
  
__Step 2__:Now we find the closure of C<sup>+</sup>  
  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;C<sup>+</sup>=C&nbsp;&nbsp;&nbsp;  
  
So in this case, we see that attribute __C__ is a part of the candidate key for the relation.  In this instance, we need to find the closure of some composite keys using attribute __C__ to determine the actual candidate key for the relation.  
  
We will try the following - AC, BC, CD and CE:  
  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;AC<sup>+</sup>=AC&nbsp;&nbsp;&nbsp;  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;BC<sup>+</sup>=ABCDE&nbsp;&nbsp;&nbsp;  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;CD<sup>+</sup>=ABCDE&nbsp;&nbsp;&nbsp;  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;CE<sup>+</sup>=CE&nbsp;&nbsp;&nbsp;  
  
So we have found two candidate keys __BC__ and __CD__ for the relation.  
  
__Example 4__  
  
Finding the candidate key(s) of the following relation.  
  
R(WXYZ)  
  
and the following functional dependencies:  
  
1) Z-->W  
2) Y-->XZ  
3) WX-->Y  
  
__Step 1__: We start by looking for attributes that do not appear on the right hand side of any of the functional dependencies.  This implies that it cannot be found through any of the functional dependencies and it is consequently part or potentially singly comprises the candidate key of the relation.  
  
By looking at the four functional dependencies, we see that there are no attributes that do not appear on the right hand side of any of the FD's.  This means that we need to check combinations of all attributes to determine the candidate key(s) for the relation.
  
__Step 2__: We will try the single attributes individually first:  
  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;W<sup>+</sup>=W&nbsp;&nbsp;&nbsp;  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;X<sup>+</sup>=X&nbsp;&nbsp;&nbsp;  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Y<sup>+</sup>=XYWZ&nbsp;&nbsp;&nbsp;  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Z<sup>+</sup>=ZW&nbsp;&nbsp;&nbsp;  
  
We now need to check combinations of the single attribute keys to determine if we have additional candidate keys for the relation.
  
__Step 3__: We will now try combinations of the single attributes that failed when checked individually:  
  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;WX<sup>+</sup>=WXYZ&nbsp;&nbsp;&nbsp;  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;XZ<sup>+</sup>=WXYZ&nbsp;&nbsp;&nbsp;  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;WZ<sup>+</sup>=WZ&nbsp;&nbsp;&nbsp;  
  
So we have found three keys for the relation: __Y__, __WX__ and __XZ__.  
  
__*ACID*__ - Related to the properties of Transactions   
__A__ stands for Atomicity  
__C__ stands for Consistency  
__I__ stands for Isolation  
__D__ stands for Durability  
  
__Atomicity__ - this means states that either all operations of a transaction occur on none of the operations occur.  This property is maintained by the Transaction Management Component of the database.  
  
__Consistency__ - consistency refers to __*correctness*__.  Any transaction should lead from one consistent state to another consistent state.  
  
__Isolation__ - ensures that concurrent execution results in a system state that would be obtained if a transaction had been executed serially.  This property is managed by the Concurrency Control Manager.  
  
__Durability__ - this property states that changes made by a transaction must be permanent.  The changes must __NOT__ be due to a database failure.  This property is the responsibility of the Recovery Manager.  

# Join Problem Walkthroughs
<details> 
  <summary>Example 1</summary><br />
### Stats
|Fill Factor|Dir. Entry Size|Tuple Size|Page Size|    
|:---------:|:-------------:|:--------:|:-------:|  
|     0.5   |    5 bytes    | 100 bytes|400 bytes|

### Relations
| Attribute\Relation |T<sub>1</sub>|T<sub>2</sub>|T<sub>3</sub>|T<sub>4</sub>|   
|-------------------:|:--------:|:--------:|:--------:|:--------:|
|       Tuples       |  2000    |  20000   |    20    |  200000  |  
| Primary Hash Index |          |     ✓    |          |          |  
|Secondary Hash Index|     ✓    |          |          |          |  
| Primary Tree Index |          |          |    ✓     |     ✓    |  
|Secondary Tree Index|          |     ✓    |          |     ✓    |  

### Preparation Steps
<details> 
  <summary>1. Compute the Directory Entries Per Page</summary><br />
   1.  floor(<sup>Page Size * Fill Factor</sup>&frasl;<sub>Dir Entry Size</sub>)  
   2.  floor(<sup>400 * 0.5</sup>&frasl;<sub>5</sub>) 
   3.  40 <sup>DEs</sup>&frasl;<sub>Page</sub>
   
|Fill Factor|Dir. Entry Size|Tuple Size|Page Size|DE/Page|    
|:---------:|:-------------:|:--------:|:-------:|:-----:|     
|     0.5   |    5 bytes    | 100 bytes|400 bytes|  40   |  
</details>

<details> 
  <summary>2. Compute the Tuples Per Page</summary><br />
   1.  floor(<sup>Page Size * Fill Factor</sup>&frasl;<sub>Tuple Size</sub>) 
   2.  floor(<sup>400 * 0.5</sup>&frasl;<sub>100</sub>)  
   3.  2 <sup>Tups</sup>&frasl;<sub>Page</sub>

|Fill Factor|Dir. Entry Size|Tuple Size|Page Size|DE/Page|Tups/Page|    
|:---------:|:-------------:|:--------:|:-------:|:-----:|:-------:|   
|     0.5   |    5 bytes    | 100 bytes|400 bytes|  40   |    2    |  
</details>


<details> 
  <summary>3. Compute the Page Sizes for each of the relations T1 thru T4</summary><br />
Note: we need to end up in units of pages!
   1.  T<sub>1</sub> = <sup>2000 Tuples</sup>&frasl;<sub>2 Tuples/Page</sub> = 1000 Pages  
   1.  T<sub>2</sub> = <sup>20000 Tuples</sup>&frasl;<sub>2 Tuples/Page</sub> = 10000 Pages  
   1.  T<sub>3</sub> = <sup>20 Tuples</sup>&frasl;<sub>2 Tuples/Page</sub> = 10 Pages  
   1.  T<sub>4</sub> = <sup>200000 Tuples</sup>&frasl;<sub>2 Tuples/Page</sub> = 100000 Pages  

| Attribute\Relation |T<sub>1</sub>|T<sub>2</sub>|T<sub>3</sub>|T<sub>4</sub>|        
|-------------------:|:--------:|:--------:|:--------:|:--------:|
|       Tuples       |  2000    |  20000   |    20    |  200000  | 
|       Pages        |  1000    |  10000   |    10    |  100000  |  
</details>


<details> 
  <summary>4. Compute the Lookup Costs for each of the indexes relations T1 thru T4</summary><br />
Note: Costs is to lookup 1 tuple in page units!
1.  Equations
    1. Hash Index Cost = 1
    2. Primary Tree Index Cost = ceiling(log<sub>DE&frasl;Page</sub>(# of Relation Pages)) + 1
    3. Secondary Tree Index Cost = ceiling(log<sub>DE&frasl;Page</sub>(<sup># of Relation Tuples</sup>&frasl;<sub>DE/Page</sub>)) + 2
    4. Nested Loop = M + T*M\*N or M+T\*M\*C
    5. Hash Join Cost = M + N + (T\*M)(1)
2.  T1: Primary Tree Index = 3
    1.  ceiling(Log<sub>40</sub>(1000)) + 1 = ceiling(1.872) + 1 = 2 + 1 = 3 
3.  T2: Primary Hash Index = 1  
3.  T2: Secondary Tree Index = 4
    1.  ceiling(Log<sub>40</sub>(<sub>20000</sub>&frasl;<sub>40</sub>)) + 2 = 
    2.  ceiling(Log<sub>40</sub>(500)) + 1 = 
    3.  ceiling(1.68) + 2 = 2 + 2 = 4 
4.  T3: Primary Tree Index = 2
    1.  ceiling(Log<sub>40</sub>(10)) + 1 = ceiling(.624) + 1 = 1 + 1 = 2  
4.  T4: Primary Tree Index = 5
    1.  ceiling(Log<sub>40</sub>(100000)) + 1 = ceiling(3.12) + 1 = 4 + 1 = 5   
4.  T4: Secondary Tree Index = 5
    1.  ceiling(Log<sub>40</sub>(<sub>20000</sub>&frasl;<sub>40</sub>)) + 2 = 
    2.  ceiling(Log<sub>40</sub>(5000)) + 2 = 
    3.  ceiling(2.3) + 2 = 3 + 2 = 5  

| Attribute\Relation |T<sub>1</sub>|T<sub>2</sub>|T<sub>3</sub>|T<sub>4</sub>|      
|-------------------:|:--------:|:--------:|:--------:|:--------:|
|       Tuples       |  2000    |  20000   |    20    |  200000  | 
|       Pages        |  1000    |  10000   |    10    |  100000  |  
</details>
      
### Solution Steps

<details> 
  <summary>1. Find the Relation with the least number of tuples</summary><br />
  T<sub>3</sub> &lt; T<sub>1</sub> &lt; T<sub>2</sub> &lt; T<sub>4</sub>
</details>


<details> 
  <summary>2. Compare the cost of of joining against the other</summary><br />

| T<sub>3</sub> ⋈<sub>attr</sub> T<sub>X</sub> Cost   |    T<sub>1</sub>    |    T<sub>2</sub>    |    T<sub>4</sub>    |   
|-------------------------------:|:--------:|:--------:|:--------:| 
| Hash Inner Nested Loop         |     ✖    |10 + 20(1)|    ✖     |  
|Primary BTree Inner Nested Loop |10 + 20(3)|    ✖     |    ✖     |  
|Secondary BTree Inner Nested Loop|10 + 20(5)|    ✖     |10 + 20(5)|  
|Nested Loops                    |10 + 2(10)(2000)|10 + 2(10)(20000)|10 + 2(10)(200000)|   
|Hash Join                       |10+1000+20(1)|10+10000+20(1)|10+100000+20(1)|    

| T<sub>3</sub> ⋈<sub>attr</sub> T<sub>X</sub> Cost    |    T<sub>1</sub>    |    T<sub>2</sub>    |    T<sub>4</sub>    | 
|-------------------------------:|:--------:|:--------:|:--------:| 
| Hash Inner Nested Loop         |     ✖    |   30     |    ✖     |  
|Primary BTree Inner Nested Loop |    70    |    ✖     |   110    |  
|Secondary BTree Inner Nested Loop|    ✖    |    ✖     |   110    |  
|Nested Loops                    |40010     |400010    |4000010   |   
|Hash Join                       |1030      |10030     |100030    |   

We observe that the Index Nested Loop using the T2 Hash Index is the least costly operation with a 30 page cost.  
R<sub>1</sub> = T<sub>3</sub> Hash-INL T<sub>2</sub>
</details>

<details> 
  <summary>3. Compare the cost of joining R1 against the other relations</summary><br />

| (T<sub>3</sub> ⋈<sub>attr</sub>T<sub>2</sub>) ⋈<sub>attr</sub> T<sub>X</sub> Cost   |    T<sub>1</sub>    |    T<sub>4</sub>    |   
|-------------------------------:|:--------:|:--------:|  
| Hash Inner Nested Loop         |     ✖    |    ✖     |  
|Primary BTree Inner Nested Loop |30 + 20(3)|30 + 20(3)|  
|Secondary BTree Inner Nested Loop|     ✖    |30 + 20(5)|  
|Nested Loops                    |30 + 2(10)(2000)|30 + 2(10)(200000)|   
|Hash Join                       |30+1000+20(1)|30+100000+20(1)|    

| (T<sub>3</sub> ⋈<sub>attr</sub>T<sub>2</sub>) ⋈<sub>attr</sub> T<sub>X</sub> Cost|  T<sub>1</sub>   |  T<sub>4</sub>    |   
|-------------------------------------------------:|:-----:|:------:|  
| Hash INL                                         |   ✖   |    ✖   |  
|Primary BTree INL                                 |90     |130     |  
|Secondary BTree INL                                |   ✖    |130     |  
|Nested Loops                                      |40030  |400030  |   
|Hash Join                                         |1050   |100050  | 
 We observe that the Index Nested Loop using the T1 Primary B-Tree Index is the least costly operation with a cumulative 90 page cost.  
R<sub>2</sub> = R<sub>1</sub> Primary-B-Tree-INL T<sub>1</sub>
</details>

<details> 
  <summary>4. Calculate the cost of joining R1 against the last relation</summary><br />

| ((T<sub>3</sub> ⋈<sub>attr</sub>T<sub>2</sub>) ⋈<sub>attr</sub> T1) ⋈<sub>attr</sub> T<sub>4</sub> Cost|  T<sub>4</sub>   | 
|----------------------------------------------------------------------:|:-----:| 
| Hash INL                                                              |   ✖   |  
|Primary BTree INL                                                      |90 + 20(5)|  
|Secondary BTree INL                                                     |90 + 20(5)|  
|Nested Loops                                                           |90 + 2(10)(2000)|30 + 2(10)(200000)|   
|Hash Join                                                              |90+100000+20(1)|   

| ((T<sub>3</sub> ⋈<sub>attr</sub>T<sub>2</sub>) ⋈<sub>attr</sub> T1) ⋈<sub>attr</sub> T<sub>4</sub> Cost|  T<sub>4</sub>   | 
|----------------------------------------------------------------------:|:-----:| 
| Hash INL                                                              |   ✖   |  
|Primary BTree INL                                                      |190    |  
|Secondary BTree INL                                                    |190    |  
|Nested Loops                                                           |400090 |   
|Hash Join                                                              |100090 | 

We identify that an Index Nested Loop join using either the Primary B-Tree or Secondary B-Tree equivalently incur the least cost.  
We select the Primary B-Tree and formulate the final relation.  
R<sub>3</sub> = R<sub>2</sub> Primary-B-Tree-INL T<sub>4</sub> with estimated cost 190.
</details>
</details>