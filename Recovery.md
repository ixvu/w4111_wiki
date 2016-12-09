### In this section, we want to learn the challenge about recover database from crash. By building a proper log system, the difficulties we are facing should be solved.

# Purpose
A crash could happen at any point in time, we can’t control it.

Reminder: This is the architecture of a DBMS
![](https://github.com/jh3768/imgs/blob/master/imgs/dbms_architecture.png?raw=true)

We want to figure out how do we actually decide what information to store so that whenever the crash happens, we are able to undo all the things that have not been committed and recover anything that has committed. The picture above gives you an idea of how the DBMS work and where is the lock manager, log manger, and transaction manager.

**Atomicity**:  Either all of a transaction's actions are carried out or none are

**Durability**: if I commit, it’s there until the end of time

That is to say we want to remove the effects of partial transactions from the database. Database may ensures transaction atomicity by undoing the actions of incomplete transactions. And if the system crashes before the changes made by a completed transaction are written to disk, the system should be able to restore these changes when it restarts.

Before we start, we need to make some assumptions about our system. Intuitively, we can ask the database be able to recover the failure from OS, but it can do nothing about disk failure.

**Assumption**: 
* Disk is safe.
* Memory is not.
* Running strict-2PL

By looking at two basic crash case, we may find a solution for recovery.

**First case: 'no steal'**
How to prevent uncommitted writes appear after crash

Naive solution: Don’t flush modified pages before commit

**Second case: 'force'**
If a crash happens after a commit, how to make the transaction durable

Naive solution: flush all modified pages

**Problems**: 
- What if a transcation writes 10 pages but crash after flushing page 1?
 - Second case fail. Disk contains partial results.

- What if a transcation writes 10 pages then aborts?
 - We have no idea how to re-load all modified pages from disk unless we store the data before modification.

- What if T1 writes A; T2 writes B; T1 commits
 - When T1 committed, B will be flushed unless T1 wait for T2 to complete (or lock page)

- What if UPDATE all tuples?
 - We can't re-load all modified pages from disk. That's too big for memory

So Let's consider changing our mental model

# Log
One way to view database is just the log of everything you done to the database and by actually run it, you get the result. We can think the modern database just a fast version of it.

Log records are typically much smaller than data page, just contain the information that you can undo an operation.

From this consideration, at least the log need to contain:
* old & new value
* commit/abort actions
* transcation id & transcation's previous log record

The order of flushing log to disk and flushing data page to disk matters. (we can see this in next section)

## Protocol:

Now we know what log should contain, but how to record and recover properly? 

Attempt of Protocol
* Flush log records to disk before data pages persisted
* Undo uncommitted records

<img src="https://github.com/cp2923/W4111_sribenote/blob/master/OK.jpg" width="400">

Protocol is OK under this scenario. 

If the crash is after we wrote A to disk, then we have the log record to undo it

If the crash happened before flushing A to disk, then we don’t need to undo it

<img src="https://github.com/cp2923/W4111_sribenote/blob/master/Bad.jpg" width="450">

Protocol is Bad under this scenario. 

If the crash is after we wrote A to disk, this is alright since we have enough information to re-run T1 to reproduce the correct state at the point of the crash.

If the crash happens right when T1 commits, no log records have been written.

**Write ahead logging (WAL)**
* Flush log records to disk before data pages persisted
* Persist all log records before commit
* Log is ordered, if record flushed, all previous records must be flushed

First requirement guarantees UNDO info and second one guarantees REDO info.

Also works in other system. This is a way to recover from failure.

**Aries Recovery Algorithm** Use WAL during normal database operation

After crash, we should fix the database by 3 phases:
* **Analysis:** Scan the log forward (from most recent checkpoint) to identify all Xacts that were active, and also all dirty pages in the buffer pool, as of the time of the crash: analyze the log to find status of all transactions
  * Whether a transaction has committed or not?
* **Redo:** Redo all updates to dirty pages in the buffer pool, as needed, to ensure that all logged updates are in fact carried out and written to disk. (Establishes state to recover from.): Redo transactions that were committed but not in database
  * Now at the same state at the point of the crash
* **Undo:** Undo writes of all Xacts that were active at the crash (by restoring the before value of the update, which is found in the log record for the update), working backwards in the log. (A bit of care must be taken to accomodate the case of a crash occurring during the recovery process!): Undo partial (in flight) transactions that not been committed

##Example
<img src="https://github.com/cp2923/W4111_sribenote/blob/master/crash1.jpg" width="450">

First phase: If crash at this time, T1 is committed but not flushed while T2 is not committed but flushed. 

Second phase: redo op5 

Third phase: undo op6.


What If crash happens while recovering like shown in the picture?

<img src="https://github.com/cp2923/W4111_sribenote/blob/master/crash2.jpg" width="450">

We need to redo undo op6.
**So we also need to record redo and undo**

## Summary
WAL protocol: flush to log before disk

ARIES Algorithm:
*	Redo for durability and atomicity
*	Undo for atomicity
*	Redo all committed writes,  Undo uncommitted tranactions
*	Enables transactions bigger than memory!

Buffer pool (database) can write RAM pages to disk any time

Tables are just views of log.

## Four kinds of Durability
We need to know what kind of guarantee that we need to make things work. 
* Transaction:  All or nothing, write randomly
* Log:  Always have a complete prefix
* Disk: After flush, written pages will survive
* Physics: No idea how this works

## Examples

1. Example 1:

**Given the Redo log and current disk state. Reconstruct the state of database after recovery:**

Redo Log:
```
transaction          operation
          A          w=50
          B          y=60
          C          x=70
          D          z=80
          B          COMMIT
          C          y=90
          A          w=100
          C          COMMIT
```
On disk state:
```
item_id        value
      w        1
      x        2
      y        3
      z        4
```
In the log file, we can see that only transaction B and C committed, therefore, we need to redo operations did by transaction B and C. Therefore, y firstly was assigned a value 60 and then modified by C to 90. So y should have value 90. Transaction C also assign x a value 70. Therefore, the disk state after reconstruction should be:

```
item_id        value
      w        1
      x        70
      y        90
      z        4
```

2. Example 2:

**Given the following log writes, decide whether those writes guarantee correct recovery after a crash at any time:**

1). 

```
1. BEGIN;
2. Write(A)
3. Write(B)
4. flush log record for "Write(A)" to disk
5. flush A to disk
6. flush log record for "Write(B)" to disk
7. flush B to disk
9. flush log record for COMMIT to disk
10 COMMIT;
```

This sequence of operations guarantee correct recovery after a crash at any time. Every time it write data to disk, the log record for that record is already in the disk. eg. Before flushing A to disk, we already have flushed a log for write(A) into disk, which makes sure that it can be recovered. This sequence of operations actually strictly follows the write ahead protocol mentioned during the lecture, which ensures the recovery of data after a crash at any time.

2). 

```
1. write A
2. flush A
3. flush log record for "Write A"
4. flush log record for "COMMIT"
5. COMMIT
```

This sequence of operations cannot guarantee correct recovery after a crash at any time. Since if we have a crash after step 2, the transaction does not actually committed and any operations should be redone, however, we cannot find the log of Write A, since we have not flush the log into the disk yet. So if there is a crush after 2, the system could go wrong.
