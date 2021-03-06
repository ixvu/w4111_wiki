# Midterm 2 Study Guide  
  
##__Important Definitions with Examples__  
<details> 
  <summary>__*Superkey*__</summary><br /><p>
Given the following EMPLOYEE relation: 

|_**SSn**_|Ename|Bdate|Address|Dnumber|
|---|---|---|---|---|  
  
where SSn is the __key__ for the relation and {Ssn}, {Ssn, Ename}, {Ssn,Ename,Bdate}, and any set of attributes that includes Ssn are all __superkeys__. If a relation schema has more than one such key, each is called a candidate key. One of the candidate keys is arbitrarily designated to be the primary key, and the others are called secondary keys. In a practical relational database, each relation schema must have a primary key. If no candidate key is known for a relation, the entire relation can be treated as a default superkey. In the previous schema, {Ssn} is the only candidate key for EMPLOYEE, so it is also the primary key. 
</p></details>

<details> 
  <summary>__*Prime attribute verses non-prime attribute*__</summary><br /><p>
An attribute of relation schema R is called a __prime attribute__ of R if it is a member of some candidate key of R. An attribute 
is called __nonprime__ if it is not a prime attribute—that is, if it is not a member of any candidate key.  
  
Given the following schema, where the primary key for the relation is {Ssn,Pnumber}, both Ssn and Pnumber are __prime attributes__ and the __Hours__ attribute is __nonprime__:  

|_**Ssn**_|_**Pnumber**_|Hours|  
|---|---|---|  
</p></details>
  
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

1. AB → C  
2. BC → AD  
3. D → E  
4. CF → B  

<details> 
  <summary>Find (AB)<sup>+</sup></summary><br /><p>
&nbsp;&nbsp;= AB&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Reflexivity  
&nbsp;&nbsp;= ABC&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Given FD 1  
&nbsp;&nbsp;= ABCD&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Given FD 2  
&nbsp;&nbsp;= ABCDE&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Given FD 3  
  
So (AB)<sup>+</sup> is __ABCDE__ 
 
</p></details>
###__Irreducible Set or Canonical Form of Functional Dependencies (known as Minimum Cover in this course)__  

Used to determine if an attribute of a functional dependency is extraneous.  An attribute is said to be extraneous if we can remove it without changing the closure of the set of functional dependencies.  
  
Given the following relation: R(WXYZ)  
  
And the following functional dependencies:    
1. X → W  
2. WZ → XY  
3. Y → WXZ  

<details> 
  <summary>Step 1: Turn FDs into standard form: split FDs so there is one attribute on the right side.</summary><br /><p>
This means that you perform the following decomposition:  
  
&alpha; → &beta;&gamma;  
&nbsp;&nbsp;↳ &alpha; → &beta;   
&nbsp;&nbsp;↳ &alpha; → &gamma;  
  
So we end up with the following:  
  
1. X → W  
2. WZ → X  
3. WZ → Y  
4. Y → W  
5. Y → X  
6. Y → Z

</p></details>
<details>
  <summary>Step 2: Minimize left side of each FD: for each FD, check if can delete a left attribute using another FD.</summary><br /><p> 
WZ is the potential candidate for reduction here.
Now, check if there is any FD that left-side including W that determines Z or left-side including Z that determines W.
In this case, neither of these FDs exsits, so no deleting can be make here.
But if, for example, Z → W exists, we could reduce WZ → X to Z → X and WZ → Y to Z → Y.

So in this step, we end up with the same as above:  
  
1. X → W  
2. WZ → X  
3. WZ → Y  
4. Y → W  
5. Y → X  
6. Y → Z

</p></details>
<details> 
  <summary>Step 3: Delete redundant FDs: check each remaining FD and see if it can be deleted.</summary><br /><p>   
Y → W implied by Y → X and X → W, 

WZ → X implied by WZ → Y and Y → X

So we end up with the following:

1. X → W   
3. WZ → Y  
5. Y → X  
6. Y → Z
  

</p></details>

IMPORTANT: Step 2 must happen before 3!!!

###__Finding Candidate Keys__  
<details> 
  <summary>Summary</summary><br /><p> 
We begin by finding the candidate key(s) of the following relation.  It is important to remember that only minimal superkey's become candidate keys:  
  
R(ABCD)  
  
and the following functional dependencies:  
  
1. A → BCD  
2. AB → CD  
3. ABC → D  
4. BD → AD  
5. C → AD  
  
|FD|Superkey|candidate key|  
|-------|---|---|  
|A → BCD|Yes|Yes|  
|AB → CD|Yes|No |  
|ABC → D|Yes|No |  
|BD → AD|Yes|Yes|  
|C → AD |No |No |  
  
When can you say that a superkey is minimal and consequently is a candidate key?  
  - __If a superkey has a subset of its attributes that are another superkey, then it is not a candidate key.__  
  - __Alternatively, if a superkey has no subset of attributes that are themselves superkeys, then it is a candidate key.__  
  
For example, ABC and AB are both superkeys.  Since AB is also a superkey, then this means ABC is not minimal and not a candidate key.  
The same logic can be considered for keys AB and A.  We have already decided that both AB and A are both superkeys.  Since A is also a superkey, this means that AB is not minimal.  Since superkey A does not have any subsets, it alone is the only candidate key for the relation.  
  
Now consider the last superkey __BD__.  Is it a candidate key?  We begin by asking if there is any proper subset of __BD__ that is also a superkey?  The only subsets of __BD__ are __B__ and __D__ and neither of these individual attributes are superkeys.  So the answer is __yes__, __BD__ is also a candidate key of the relation.  
</p></details>

###__Finding Number of Candidate Keys Additional Examples__  
<details> 
  <summary>__Example 1__</summary><br /><p>  
Finding the candidate key(s) of the following relation.  
  
R(ABCDEFGH)  
  
and the following functional dependencies:  
  
1. AB → C  
2. A → DE  
3. B → F  
4. F → GH  
  
__Step 1__: We start by looking for attributes that do not appear on the right hand side of any of the functional dependencies.  This implies that it cannot be found through any of the functional dependencies and it is consequently part or potentially singly comprises the candidate key of the relation.  
  
By looking at the four functional dependencies, we see that neither A or B appear on the right hand side of any of the FD.  This means that AB is either part of or the entire candidate key of the relation.   
  
__Step 2__:Now we find the closure of AB<sup>+</sup>  
  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;AB<sup>+</sup>=ABCDEFGH&nbsp;&nbsp;&nbsp;  
  
So AB is both the candidate key and superkey of relation R.  
</p></details>
<details> 
  <summary>__Example 2__</summary><br /><p>
Finding the candidate key(s) of the following relation.  
  
R(ABCDEFGH)  
  
and the following functional dependencies:  
  
1. AB → C  
2. BD → EF  
3. AD → G  
4. A → H  
  
__Step 1__: We start by looking for attributes that do not appear on the right hand side of any of the functional dependencies.  This implies that it cannot be found through any of the functional dependencies and it is consequently part or potentially singly comprises the candidate key of the relation.  
  
By looking at the four functional dependencies, we see that neither A,B or D appear on the right hand side of any of the FD.  This means that ABD is either part of or the entire candidate key of the relation.   
  
__Step 2__:Now we find the closure of ABD<sup>+</sup>  
  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ABD<sup>+</sup>=ABCDEFGH&nbsp;&nbsp;&nbsp;  
  
So ABD is both the candidate key and superkey of relation R.  
</p></details>
<details> 
  <summary>__Example 3__</summary><br /><p>
Finding the candidate key(s) of the following relation.  
  
R(ABCDE)  
  
and the following functional dependencies:  
  
1. BC → ADE  
2. D → B  
  
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
</p></details>
<details> 
  <summary>__Example 4__</summary><br /><p> 
Finding the candidate key(s) of the following relation.    
R(WXYZ)  
  
and the following functional dependencies:  
  
1. Z → W  
2. Y → XZ  
3. WX → Y  
  
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
</p></details>

__*ACID*__ - Related to the properties of Transactions   
__A__ stands for Atomicity  
__C__ stands for Consistency  
__I__ stands for Isolation  
__D__ stands for Durability  
  
__Atomicity__ - this means states that either all operations of a transaction occur on none of the operations occur.  This property is maintained by the Transaction Management Component of the database.  
  
__Consistency__ - consistency refers to __*correctness*__.  Any transaction should lead from one consistent state to another consistent state.  
  
__Isolation__ - ensures that concurrent execution results in a system state that would be obtained if a transaction had been executed serially.  This property is managed by the Concurrency Control Manager.  
  
__Durability__ - this property states that changes made by a transaction must be permanent.  The changes must __NOT__ be due to a database failure.  This property is the responsibility of the Recovery Manager.  

# [[Join Problem Walkthroughs|join-problem-walkthroughs]]


# Transactions
* A transaction is a sequence of actions. Actions are :
    1. READ
    2. WRITE
    3. BEGIN
    4. COMMIT (successful end of transaction)
    5. ABORT (unsuccessful transaction due to error)

* Serial Schedule  
   * T1: `begin R(A) R(B) W(A) W(B) COMMIT`  
   * T2: &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&ensp;`begin R(A) W(A) W(B) R(B) COMMIT`  

* Concurrent Schedule
A concurrent schedule is "correct" if the results are the same as those of a serial schedule. A correct concurrent schedule is a "serializable" schedule.  
  * Example of correct concurrent schedule:
    * Consider the following logical exacts (where initially A = B = 0): 
        * A = A + 1; B = A + 1
        * A = A + 10; B = B + 1
    * A corresponding serial schedule would be:
        * T1: `begin` `r(A) w(A)` `r(A) w(B)` `commit`
        * T2: &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&nbsp; `begin` `r(A) w(A)` `r(B) w(B)` `commit`
        * After this schedule: A = 11, B = 3
    * This concurrent schedule is correct
        * T1: `begin` `r(A) w(A)` &emsp;&emsp;&ensp; `r(A) w(B)` `commit`
        * T2: `begin` &emsp;&emsp;&emsp;&emsp;&ensp; `r(A)` &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&ensp; `w(A) r(B) w(B)` `commit`  


* Anomalies:  
    * Dirty Reads: Reading in between uncommited data. Violates the atomicity.  
    * Unrepeatable Reads: A transaction reads from same page but get different values. Violates consistency.  
    * Lost Writes: A transaction's write is overwritten by another transaction before the first transaction gets committed.  

* Conflict Serializability  
   * Definition: For any conflicting operations O1 of T1 and O2 of T2, O1 always happens before O2 in the schedule or O2 always happens before O1 in the schedule.
   * Reads on reads are OK but any other combination are not. The following diagrams show a simple way to check the conflict serializability of concurrent schedules.
![](https://github.com/harrybari/w4111ScribeNotes/blob/master/newgraphnocycle.PNG)
![](https://github.com/harrybari/w4111ScribeNotes/blob/master/newgraphcycle.PNG)  

* Issues with Conflict Serializability  
   * Not recoverable - when a transaction commits a change before another transaction has a chance to abort.  
       * T1:`R(A) W(A)`&emsp;&emsp;&emsp;&emsp;`R(B)ABORT`  
       * T2:&emsp;&emsp;&emsp;&emsp;`R(A)COMMIT`  
   * Cascading rollback - when a transaction aborts, modifications by other uncommitted transactions are lost  
       * T1:`R(A) W(B) W(A)`&emsp;&emsp;&emsp;&emsp;`ABORT`  
       * T2:&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;`R(A)W(A)`  

* Lock-Based Concurrency Control  
   * To deal with issues in conflict serializability, lock-based concurrency control is used.
   * Strict two-phase locking (Strict 2PL): 
       * Growing phase: acquire locks   
       * Shrinking phase: release locks. But HOLD ON locks until commit/abort.  

* Overall relationships diagram
  * Below is a diagram showing the relations of transactions that we learned so far.
![](https://cloud.githubusercontent.com/assets/17611263/21064322/2c2c6334-be28-11e6-8c62-79006e1d8ba7.png)


# Recovery  
* Logging
    * Write Ahead Logging (WAL)  
    * Steps:  
         * Flush log records to disk before data pages persisted  
         * Persist all log records before commit  
         * Log is ordered, if record flushed, all previous records must be flushed  