#Transaction Processing

###Terminology
* **Transaction:** A single logical operation on a database that provides "ACID" guarantees  (essentially a simpler and more fundamental method of reading/writing)
* **ACID:** A set {Atomicity, Consistency, Isolation, Durability} of transaction properties that ensure consistency and correctness of the database.
    * **Atomicity:** 
        * "All or nothing": Lll changes are applied or none are
        * Users never see in-between transaction states
    * **Consistency:**
        * Database always satisfies integrity constraints
        * Transactions move from one valid DB to another
    * **Isolation:**
        * Transaction thinks it is the only one running (ie. can't see anything else)
    * **Durability:**
        * If a transaction commits (ie. succeeds), then its effects must persist
* **Schedule:** An ordering of transaction operations
    * **Serial Schedule:** Excute transactions one at a time (ie. no concurrency, no interleaving)
    * **Equivalent Schedule:** Database state is the same at the end of each schedule
    * **Serializable Schedule:** A schedule that is *equivalent* to a serial schedule

###Problem
* How do we maintain consistency/correctness when dealing with computer crashes, failing hardware, and parallelly running queries.
* Simple Example: A wants to transfer $1000 to B.  How should we do this?
    1. Check if A has at least $1000
    2. Subtract $1000 from A's account
    3. Add $1000 to B's account

    * But what if the computer crashes between states (2) and (3)?
        * A's money has disappeared!
        * Ideally A should get their money back

How can we solve these problems?
* We can use the concept of "*atomicity*" in transactions
    * A transaction is "atomic" if it is indivisible (ie. it cannot be interrupted), so if the transaction starts then we guarantee that it will complete
    * So, if we group steps (1-3) together as 1 "atomic" transaction, then this solves the crash problem because it is impossible for step (1) and (2) to run without step (3) running as well
* And also "*isolation*"
    * This provides the illusion that each transaction runs sequentially without concurrency
    * Essentially, transactions run in a vacuum: they are unaware and unaffected by other concurrently running transactions

###Translating Application Semantics to Transaction Semantics
* A transaction is just a sequence of actions, where an action is one of the following:
    1. Read an object
    2. Write an object
    3. Commit: sucessfully end the transaction
    4. Abort: end the transaction prematurely or in error

    * These actions provide an API between the application and the DBMS

* SQL to R/W
    * SELECT: Read
    * INSERT: Write
    * UPDATE: Read then Write
    * DELETE: Write an empty value

* Example:
    * User's (Application) View:
        * Transaction 1: `A = A + 100` `B = B - 100`
        * Transaction 2: `A = A - 50` `B = B + 50`
    * DBMS's Logical View:
        * Transaction 1: `r(A) w(A)` `r(B) w(B)` `commit`
        * Transaction 2: `r(A) w(A)` `r(B) w(B)` `commit`

###Serial Schedules
* Example 1: T1 first, T2 second
    * T1: `r(A) w(A)` `r(B) w(B)`
    * T2: &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp; `r(A) w(A)` `r(B) w(B)`
* Example 2: T2 first, T1 second
    * T1: &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp; `r(A) w(A)` `r(B) w(B)`
    * T2: `r(A) w(A)` `r(B) w(B)`
* Are these two examples the same?
    * No!  Different serial schedules can have different effects!
    * For example: getting $100 then giving $100 vs. giving $100 then getting $100
        * In the second case, you might not have enough money to give!
* **NOTE:** A serial schedule does not necessarily guarantee an error free application; it only ensures that there are no anomalies due to ACID violations

###Correctness
* What does correctness mean?
    * What are "correct" results when running transactions concurrently?
    * On crash or abort, how do we ensure that we can recover to a "correct" state?
    * **Definition:** An interleaving is "correct" if its results are the same as a serial ordering (so basically a serializable schedule)

##Serializable Schedules: the "gold standard" for correctness
* Why?  Because they prevent concurrency anomalies.  For example:
    * Write/Read Conflicts (Dirty Reads): Reading in between uncommitted data
        * Consider the following schedule S:
            * T1: `r(A) w(A)` &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp; `abort`
            * T2: &emsp;&emsp;&emsp;&emsp;&emsp; `r(A) w(A)` `commit`
        * In this schedule, transaction 2 reads and commits tainted data that is aborted by T1
            * The data written by T1 should not have been read/used by T2
        * Schedule S is *NOT* serializable because it is not equivalent to any serial schedule
            * Transactions in a serial schedules always have access to the most "up-to-date" data, so this situation could never happen (ie. they're atomic)
    * Read/Write Conflicts (Unrepeatable Reads): Reading same data gets different values
        * Consider the following schedule S:
            * T1: `r(A)` &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp; `r(A) w(A)` `commit` 
            * T2: &emsp;&emsp;&ensp; `r(A) w(A)` `commit`
        * Here, transaction 1 reads from A twice and gets different results from each read
        * This schedule is also *NOT* serializable because there does not exist an equivalent serial schedule
            * Similar reasoning to previous example
    * Write/Write Conflicts (Lost Writes): Overwriting someone else's writes
        * Consider the following schedule S: 
            * T1: `w(A)` &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp; `w(B)` `commit`
            * T2: &emsp;&emsp;&ensp; `w(A) w(B)` `commit`
        * In this case, T1's write to A is overwritten by T2
        * Again, this schedule is *NOT* serializable (ie. there is no serializable schedule that exists that would yield the same result as this)
        * NOTE: there is no W/W conflict between T2's and T1's write to B because T2 committed before T1 wrote to B
        


*currently being edited*










