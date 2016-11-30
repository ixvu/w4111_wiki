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
        * `begin` `r(A) w(A) r(B) w(B)` `commit` (e.g. A = A + 100; B = B - 100)
        * `begin` `r(A) w(A) r(B) w(B)` `commit` (e.g. A = A * 1.5; B = B * 1.5)
* Example: `UPDATE accounts SET bal = bal + 1000 WHERE bal > 1M`
    * Read the balances for every tuple and update those with balances > 1M.
    * Does the access method matter? Yes!
        * If we scan, we have to read every tuple
        * If we index, we only have to read the tuples with balance > 1M

# IV. Serial Schedules
* Example 1: T1 first, T2 second, no concurrency (serial 1)
    * T1: `begin` `r(A) w(A)` `r(B) w(B)` `commit`
    * T2: &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&nbsp; `begin` `r(A) w(A)` `r(B) w(B)` `commit`
* Example 2: T2 first, T1 second, no concurrency (serial 2)
    * T1: &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&ensp; `begin` `r(A) w(A)` `r(B) w(B)` `commit`
    * T2: `begin` `r(A) w(A)` `r(B) w(B)` `commit`
* Are these two examples the same?
    * No!  Different serial schedules can have different effects! Transaction order matters!

* **NOTE:** A serial schedule does not necessarily guarantee an error free (ie. all constraints satisfied) application; it only ensures that there are no anomalies due to ACID violations
* This ties into the ideas of concurrency control (techniques to ensure correct results when running transactions concurrently) and recovery (on a crash or abort, how do we get back to the correct state?), which are intertwined.  

# V. Correctness 
* What does correctness mean?
    * What are "correct" results when running transactions concurrently?
    * On crash or abort, how do we ensure that we can recover to a "correct" state?
    * **Definition:** An interleaving is "correct" if its results are the same as a serial ordering (so basically a serializable schedule)
* Example:
    * Consider the following logical exacts (where initially A = B = 0): 
        * A = A + 1; B = A + 1
        * A = A + 10; B = B + 1
    * A corresponding serial schedule would be:
        * T1: `begin` `r(A) w(A)` `r(A) w(B)` `commit`
        * T2: &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&nbsp; `begin` `r(A) w(A)` `r(B) w(B)` `commit`
        * After this schedule: A = 11, B = 3
    * This concurrent schedule is **NOT** correct (ie. doesn't work, not equivalent to serial schedule)
        * T1: `begin` `r(A) w(A)` &emsp;&emsp;&emsp;&emsp;&emsp; `r(A) w(B)` `commit`
        * T2: `begin` &emsp;&emsp;&emsp;&emsp;&ensp; `r(A) w(A)` &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&ensp; `r(B) w(B)` `commit`
        * After this schedule: A = 11, B = 13 &emsp;**&#10007;**
    * This concurrent schedule is correct
        * T1: `begin` `r(A) w(A)` &emsp;&emsp;&ensp; `r(A) w(B)` `commit`
        * T2: `begin` &emsp;&emsp;&emsp;&emsp;&ensp; `r(A)` &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&ensp; `w(A) r(B) w(B)` `commit`
        * After this schedule: A = 11, B = 3 &emsp;**&#10003;**
        * We call this a *serializable* schedule because it is equivalent to the serial schedule of T1 & T2

# VI. Serializable Schedules: the "gold standard" for correctness
* Why?  Because they prevent concurrency anomalies.  For example:
    * **Write/Read Conflicts - reading in between uncommitted data (Dirty Reads):**
        * Consider the following schedule S:
            * T1: `begin` `r(A) w(A)` &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&ensp; `r(B) w(B) abort`
            * T2: `begin` &emsp;&emsp;&emsp;&emsp;&ensp; `r(A) w(A)` `commit`
        * In this schedule, transaction 2 reads and commits data - it's write was based on an uncommitted read, and may lead to an inconsistent database state
            * The data written by T1 should not have been read/used by T2, as it was not committed
        * Schedule S is *NOT* serializable because it is not equivalent to any serial schedule
            * Transactions in a serial schedules always have access to the most "up-to-date" data, so this situation could never happen (ie. they're atomic)

    * **Read/Write Conflicts (Unrepeatable Reads):**
        * Consider the following schedule S:
            * T1: `begin` `r(A)` &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp; `r(A) w(A)` `commit` 
            * T2: `begin` &emsp;&emsp;&ensp; `r(A) w(A)` `commit`
        * Here, transaction 1 reads from A twice and gets different results from each read
        * This schedule is also *NOT* serializable because there does not exist an equivalent serial schedule
            * Similar reasoning to previous example

    * **Write/Write Conflicts (Lost Writes):**
        * Consider the following schedule S: 
            * T1: `begin` `w(A)` &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp; `w(B)` `commit`
            * T2: `begin` &emsp;&emsp;&ensp; `w(A) w(B)` `commit`
        * In this case, T1's write to A is overwritten by T2
        * Again, this schedule is *NOT* serializable (ie. there is no serializable schedule that exists that would yield the same result as this)
        * NOTE: there is no W/W conflict between T2's and T1's write to B because T2 committed before T1 wrote to B

* Notice that all anomalies occur when one transaction writes and another is reading/writing
    * That is to say: if transactions are just reading, then there are no anomalies
    * This also suggests that tracking writes may help us prevent anomalies

#VII. Conflict Serializability
* Definition: For any conflicting operations O1 of T1, O2 of T2, O1 always before O2 in the schedule or O2 always before O1 in the schedule. For example:
    * **There are two Ts**
         * T1: `R(A)W(A)` `R(B)W(B)`
         * T2: `R(A)W(A)` `R(B)W(B)`
    * **We find the conflicts as the following:**
         * T1.R(A) with T2.W(A)
         * T1.W(A) with T2.R(A)
         * T1.W(A) with T2.W(A)
         * T1.R(B) with T2.W(B)
         * T1.W(B) with T2.R(B)
         * T1.W(B) with T2.W(B)
    * **An easy way to think about it is that you will have conflicting operations whenever one transaction has a "write" operation and the other transaction attempts to operate on the same object.**
    * **One possible serializable:**
         * T1:`R(A) W(A)`&emsp;&emsp;&emsp;&emsp;`R(B)W(B)` 
         * T2: &emsp;&emsp;&emsp;&emsp;`R(A)W(A)`&emsp;&emsp;&emsp;&emsp;`R(B)W(B)`
         * In this case, all T1's operations on a given object are before the conflicted T2's operation and vice versa, therefore it is conflict serializable.
         * Another algorithm we can use to determine conflict serializability is the following: try to swap a set of adjacent operations from each transaction in time and if we do not have conflicts we are okay. Continue this process until we find a swap that would feature a conflict or until all of one transaction happens exclusively before the other. We see this below.
         * Swapped transaction statements:
         * T1:`R(A) W(A)``R(B)W(B)`&emsp;&emsp;&emsp;&emsp; 
         * T2: &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;`R(A)W(A)``R(B)W(B)`
         * We see that T1 completely executes before T2 so this schedule is conflict serializable.
   * **One Not Serializable**
         * T1: `R(A)`&emsp;&emsp;`W(A)`&emsp;&emsp;`R(B) W(B)`
         * T2: &emsp;&emsp;`R(A)`&emsp;&emsp;`W(A)`&emsp;&emsp;&emsp;&emsp;`R(B) W(B)`
         * In this case, T1.R(A) is conflicted with T2.W(A) and T1.R(A) is executed first; however, T2.R(A) is conflicted with T1.W(A), but T2.R(A) is executed first. Therefore, it is not serializable.
   * **Look is from a big picture:**
         If we suppose there is one edge from T1.operator -> T2.operator if the two operators confict AND there is edge between both conatenate operation in `T1` or `T2` => If the graph does not contain cycles, then it means conflict is serializable.

#VIII.  Conflict Serializabilizable issues
* Why it is a question? Because there are the following problems:
   * **Not recoverable** 
          * T1:`R(A) W(A)`&emsp;&emsp;&emsp;&emsp;`R(B)ABORT`
          * T2:&emsp;&emsp;&emsp;&emsp;`R(A)COMMIT`
          * In this case, T1 first do some modification for A but finally T1 decides to abort this modification(In reality, it might happen when T1 read another value B and find something wrong and need to abort the former modification). However, due to T2 have commit this modification, the abort is unsucessful.
   * **Cascading Rollback**    
          * T1:`R(A) W(B) W(A)`&emsp;&emsp;&emsp;&emsp;`ABORT`
          * T2:&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;`R(A)W(A)`
          * In this case, T2 read the uncommitted data updated by T1, but T1 abort is shortly. Although T1 only want to abort itsefl modification for A, the T2's modification is aborted as well because A has not been committed. 

* What should we do?
   * **Lock-based concurrency control**
       * The idea is to have a shared(read) and exclusive(write) lock before op.
               * The original idea is not so useful in the following case:
               * T1  `R(A) W(A)`&emsp;&emsp;&emsp;&emsp;`R(B) ABORT`
               * T2 &emsp;&emsp;&emsp;&emsp;`R(A) COMMIT`
               * The case will cause some problems, but it is allowed for this lock mechanism.
       * Two-phase locking(2PL) Growing phase: acquire locks   Shrinking phase: release locks
               * The idea is not so useful in the following case:
               * T1  `R(A) W(B) W(A)`&emsp;&emsp;&emsp;&emsp;`ABORT`
               * T2  &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;`R(A) W(A)`
               * The case will cause some problems(it is unrecoverable), but it is allowed for this lock mechanism.
       * Strict two-phase locking (Strict 2PL): Growing phase: acquire locks   Shrinking phase: release locks. But HOLD ON locks until commit/abort.
               * This idea is useful and can avoid cascading rollbacks and gurantees serializable schedules.
               * To be specific the following case is forbidden since T1 will hold the lock for W(A) before ABORT op
               * T1  `R(A) W(B) W(A)`&emsp;&emsp;&emsp;&emsp;`ABORT`
               * T2  &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;`R(A) W(A)`

       