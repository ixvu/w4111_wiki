# I. Terminology
* **Transaction:** A single logical operation on a database that provides "ACID" guarantees  (essentially a simpler and more fundamental method of reading/writing)
* **ACID:** A set {Atomicity, Consistency, Isolation, Durability} of transaction properties that ensure consistency and correctness of the database.
    * **Atomicity:** 
        * "All or nothing": All changes are applied or none are
        * Users never see in-between transaction states
    * **Consistency:**
        * Database always satisfies integrity constraints
        * Transactions move from one valid DB to another
    * **Isolation:**
        * Transaction thinks it is the only one running (ie. can't see anything else)
    * **Durability:**
        * If a transaction commits (ie. succeeds), then its effects must persist
* **Commit:** When a transaction has been fully completed
* **Schedule:** An ordering of transaction operations
    * **Serial Schedule:** Execute transactions one at a time (ie. no concurrency, no interleaving)
    * **Equivalent Schedule:** Database state is the same at the end of both schedules, both schedules yield the same value
    * **Serializable Schedule:** A schedule that is *equivalent* to a serial schedule
* **Correctness:** A schedule is correct if it is serializable
* **Conflict:** A conflict occurs when it is possible to get two different results from running two operations in a different order
    * **Write/Read Conflict (Dirty Reads):** Reading in between uncommitted data
    * **Read/Write Conflict (Unrepeatable Reads):** Reading same data gets different values
    * **Write/Write Conflict (Lost Writes):** Overwriting someone else's writes

# II. The Problem
* How do we maintain consistency/correctness when dealing with computer crashes, failing hardware, and queries running in parallel?
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

Why do we need concurrency? Serial schedules may preserve correctness and ACID guarantees, but it's slow. We need to be able to update our database quickly as well as correctly. We thus must serve transactions concurrently.

# III. Translating Application Semantics to Transaction Semantics
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
        * Transaction 1: `begin` `r(A) w(A)` `r(B) w(B)` `commit`
        * Transaction 2: `begin` `r(A) w(A)` `r(B) w(B)` `commit`
    * Consider the following logical exacts: 
        * T1: `begin` `r(A) w(A) r(B) w(B)` `commit` (e.g. A = A + 100; B = B - 100)
        * T2: `begin` `r(A) w(A) r(B) w(B)` `commit` (e.g. A = A * 1.5; B = B * 1.5)
* Example: `UPDATE accounts SET bal = bal + 1000 WHERE bal > 1M`
    * Read the balances for every tuple and update those with balances > 1M.
    * Does the access method matter? Yes!
        * If we scan, we have to read every tuple
        * If we index, we only have to read the tuples with balance > 1M

# IV. Serial Schedules
* Example 1: T1 first, T2 second, no concurrency (serial 1)
    * T1: `r(A) w(A)` `r(B) w(B)`
    * T2: &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp; `r(A) w(A)` `r(B) w(B)`
* Example 2: T2 first, T1 second, no concurrency (serial 2)
    * T1: &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp; `r(A) w(A)` `r(B) w(B)`
    * T2: `r(A) w(A)` `r(B) w(B)`
* Are these two examples the same?
    * No!  Different serial schedules can have different effects! Transaction order matters!

* **NOTE:** A serial schedule does not necessarily guarantee an error free application; it only ensures that there are no anomalies due to ACID violations
* This ties into the ideas of concurrency control (techniques to ensure correct results when running transactions concurrently) and recovery (on a crash or abort, how do we get back to the correct state?), which are intertwined.  

# V. Correctness 
* What does correctness mean?
    * What are "correct" results when running transactions concurrently?
    * On crash or abort, how do we ensure that we can recover to a "correct" state?
    * **Definition:** An interleaving is "correct" if its results are the same as a serial ordering (so basically a serializable schedule)
* More example schedules:
* Consider the following logical exacts: 
    * T1: `r(A) w(A) **r(A)** w(B) (e.g. A=A+1; B=A+1)`
    * T2: `r(A) w(A) r(B) w(B) (e.g. A=A+10; B=B+1)`
* Concurrency (bad, this doesn't work)
    * T1: `r(A) w(A)`&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp; `r(A) w(B)`
    * T2: &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;`r(A) w(A)`&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;`r(B) w(B)`
* Concurrency (same as serial (T1, T2)!)
    * T1: `r(A) w(A)`&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;`r(A) w(B)`
    * T2: &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;`r(A)`&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;`w(A) r(B) w(B)`

# VI. Serializable Schedules: the "gold standard" for correctness
* Why?  Because they prevent concurrency anomalies.  For example:
    * **Write/Read Conflicts - reading in between uncommitted data (Dirty Reads):**
        * Consider the following schedule S:
            * T1: `r(A) w(A)` &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp; `r(B) w(B) abort`
            * T2: &emsp;&emsp;&emsp;&emsp;&emsp; `r(A) w(A)` `commit`
        * In this schedule, transaction 2 reads and commits data - it's write was based on an uncommitted read, and may lead to an inconsistent database state
            * The data written by T1 should not have been read/used by T2, as it was not committed
        * Schedule S is *NOT* serializable because it is not equivalent to any serial schedule
            * Transactions in a serial schedules always have access to the most "up-to-date" data, so this situation could never happen (ie. they're atomic)

    * **Read/Write Conflicts (Unrepeatable Reads):**
        * Consider the following schedule S:
            * T1: `r(A)` &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp; `r(A) w(A)` `commit` 
            * T2: &emsp;&emsp;&ensp; `r(A) w(A)` `commit`
        * Here, transaction 1 reads from A twice and gets different results from each read
        * This schedule is also *NOT* serializable because there does not exist an equivalent serial schedule
            * Similar reasoning to previous example

    * **Write/Write Conflicts (Lost Writes):**
        * Consider the following schedule S: 
            * T1: `w(A)` &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp; `w(B)` `commit`
            * T2: &emsp;&emsp;&ensp; `w(A) w(B)` `commit`
        * In this case, T1's write to A is overwritten by T2
        * Again, this schedule is *NOT* serializable (ie. there is no serializable schedule that exists that would yield the same result as this)
        * NOTE: there is no W/W conflict between T2's and T1's write to B because T2 committed before T1 wrote to B

* Notice that all anomalies occur when one transaction writes and another is reading/writing
    * That is to say: if transactions are just reading, then there are no anomalies
    * This also suggests that tracking writes may help us prevent anomalies

# VII. Conflict Serializability
* A schedule is conflict serializable if it is conflict equivalent to a serial schedule
    * A schedule S1 is conflict equivalent to another schedule S2 if:
        1. S1 and S2 have the same set of actions
        2. Each pair of actions in S1 and S2 have the same order
    * To put it another way, if you can obtain a serial schedule by swapping non-conflicting operations, then that schedule is conflict serializable 
* Some serializable schedules are not conflict serializable, but all conflict serializable schedules are serializable
* **Example of a Conflict Serializable Schedule:**
    * Consider the following schedule S: 
        * T1: &emsp;&emsp;&emsp;&emsp;&emsp; `r(A) w(A)`
        * T2: `r(A) w(A)` &emsp;&emsp;&emsp;&emsp;&emsp; `r(B) w(B)`
    * We can swap `r(A) w(A)` in T1 and `r(B) w(B)` in T2 to obtain the serial schedule:
        * T1: &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp; `r(A) w(A)`
        * T2: `r(A) w(A)` `r(B) w(B)`
    * So S is conflict serializable
* **Example of a Non-Conflict-Serializable (Regularly) Serializable Schedule:** 
    * Consider the schedule S:
        * T1: `w(A)` &emsp;&emsp;&emsp;&emsp;&emsp; `w(B)`
        * T2: &emsp;&emsp;&nbsp; `w(A) w(B)`
        * T3: &emsp;&emsp;&ensp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&ensp; `w(B)`
    * This schedule is basically one big write/write conflict and you can't do any swapping because every operation is a conflict
        * So because this schedule isn't already serial, then it is also not conflict serializable by default
    * S *IS* serializable because it is equivalent to the following schedule:
        * T1: `w(A) w(B)`
        * T2: &emsp;&emsp;&emsp;&emsp;&ensp; `w(A) w(B)`
        * T3: &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&ensp; `w(B)`
        * We can do this because T1's and T2's write to B is overwritten to T3, and because T1's write to A is lost to T2, so logically this serial schedule is equivalent to S



