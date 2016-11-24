## Background
    Task: Find an efficient physical query plan (execution plan) for an sql query.
    Goal: minimize the evaluation time for the query, compute query result as fast as possible.
    Cost Factors: Disk accesses, read/write operations, [I/O, page transfer] (CPU time is typically ignored).

## Translate Sql to query plan
+    Query plan: A query plan (or query execution plan) is an ordered set of steps used to access data in a SQL relational database management system. This is a specific case of the relational model concept of access plans.
+    Parsing and Translating: Translate the query into its internal form/ parse tree (Given query may have many kinds of trees). This is then translated into an expression of the relational algebra. – Parser checks syntax, validates relations, attributes and access permissions.
+    Evaluation: The query execution engine takes a physical query plan (aka execution plan), executes the plan, and returns the result.
+    Optimization: Find the “cheapest” execution plan for a query.

### Notice:
    A relational algebra expression can be evaluated in many ways. 
    An annotated expression specifying detailed evaluation strategy is called the execution plan 
    (includes, e.g., whether index is used, join algorithms, . . . ) 
    among all semantically equivalent expressions, the one with the least costly evaluation plan is chosen.

## Query Evaluation
-    Push: Operators are input-driven; As operator gets data, push it to parent operator.
-    Pull: The root operator is likely the cursor; Operators are demand-driven; Do not do anything until parent operator asks for the next data.

### Condition 1: Data is not indexed.
B: number of pages; M: number of pages matched in WHERE clause.
+    Execution Types:
     +    Naive execution: Following query steps and only pass data to next level if it is completely processed in one level. Cost can be up to B+B+M given a where clause.
     +    Pipelining. evaluate several operations simultaneously, and pass the result on to the next operation. Cost: B.
+    Comparison:
     +    Pipelining is usually cheaper than naive execution, because temporary relations are not generated and stored on disk.
     +    Pipelining is not always possible, e.g. sort because each page needs to be compared to the rest unsorted pages.
+    Example query: SELECT a FROM R WHERE a>10
+    Pipeline Execution: Read a page from offers and pass to selection. As selection looks for tuples in the page that satisfy the predicate, can read the next page from offers. Similarly, pass valid pages to projection, and as projection returns value of projected attributes, selection can look for tuples in the page that satisfy the predicate.

### Condition 2: Data is indexed as hash table.
It is only applicable for equalities.
+    Example query: 
    +    SELECT a FROM R WHERE a>10. 
    +    Assume we have 10K pages, 
    +    each page has 10 tuples, 
    +    each page has 100 directory entries. 
    +    Assume uniform distribution with ‘a’ ranging from 0 to 100. 
+    Need to go over all pages since hash table only supports equality. Cost: 10K pages.

### Condition 3: Data is indexed as B+ Tree.
It uses index to find all record, identify first index page and number of pages that matches and pass to projector operator. Cost is reduced to logarithm.
+    Example query: Same query as previous.
+    Comparison:
     +    Primary B+ Tree: 
          +    Number of leaf pages: 10K. 
          +    Number of pages need to read: 90%x10K=9K. 
          +    Given 100 DEs of each page, number of pages one level up: 10K/100=100. 
          +    One level up is 1 page, root page. 
          +    Total height: log100(10K)=2. 
          +    Total cost: 9K+2.
     +    Secondary B+ Tree: 
          +    leaf pages store pointers to tuples.
          +    Number of leaf pages = Total Number of tuples/number of DEs in each page = 10x10K/100=1K
          +    Given 100 DEs of each page, number of pages one level up: 1K/100=10. 
          +    One level up is 1 page, root page. 
          +    Total height: 3. 
          +    Total cost: 90K+3.

## Access path
Access Path refers to the path chosen by the system to retrieve data after a structured query language (SQL) request is executed. A query may request at least one variable to be filled up with one value or more.

### Index + matching condition
+    Sequential Scan: doesnt accept condition.
+    Hash Index Search: accept equality conditions on all search keys.
+    Tree Index Search: accept conditions on prefix of search keys.

### How to pick access path
+    'SELECT' is required to choose the appropriate index to use.
+    Depend on number of data pages: secondary indicies, less than 2%, not worthy to use.
+    Selectivity only for selection operation.

### How to determin/compute selectivity
+    Scan the whole data and multiple, assuming the distribution over all values is uniform.
+    How many distinct values in a hash table.
+    Default estimate -- if know nothing else, assume 5%.

### Notice:
        If attributes is primary key, you know exactly one matches it.
        Number of value equals to cardinality.
        Assumption: attribute selectivity is independent 

## Complementary Concepts
### Primary and Secondary Index
    Primary: data is store in the leaf nodes. 
    Secondary: leaf nodes only contain pointers to actual data files.
    The query optimizer attempts to determine the most efficient way to execute a given query by considering the possible query plans.

## Primary Index and Secondary Index Example
<img src="https://github.com/xz2581/project1/blob/master/1.png" width="450">
###Preliminaries
+ Underlying data has the schema R(a int, b int, c int, d int)
+ We built primary and secondary B+ trees with key = a.
+ Page 1 is an example leaf page for primary index. It stores the actual tuples.
+ Page 2 is an example leaf page for secondary index. It stores a values with corresponding pointers to the actual tuples.

###Assumptions
+ Each page can fit in 8000 bytes of data.
+ An integer costs 8 bytes.
+ For simplicity, each pointer object has the type int.
+ There are 10,000 tuples total.

### Calculations for the Height of the two B+ Trees
<img src = "https://github.com/xz2581/project1/blob/master/2.png">

### Additional Notes
+ For primary index, the bottom layer of the tree is sorted while this is not necessarily the case for secondary index.
+ Typically, the secondary tree is smaller than the primary tree. For example, in this case, there are 40 pages at the bottom for primary and only 20 pages at the bottom for secondary.
+ When would we want to use secondary index (or when would using secondary index probably faster)?
  + When you only want equality predicates.
  + When you don't want to access the actual tuples associated with this "a" value
    + ex1. count queries 
    + ex2. when you only want to access the values of a: Select a from R where a > 0 

####What Optimization Options Do We Have? 
-Access Path ✔ 
-Predicate push-down 
-Join implementation 
-Join ordering

##Predicate Push down: 
if I see (b), (b) can be pushed down to (a), then do so.
In general, depends on operator implementations. 
<img src = "https://github.com/xz2581/project1/blob/master/8.png">

Which option is faster if B+ tree index on a?
a)Log F(B) + M pages: using Btree, find the start point and scan to the right
b)B pages : not using B tree, scans entire relation

##Projection with DISTINCT clause
need to deduplicate e.g., π  rating Sailors
						
Two basic approaches
1. Sort: sort on rating, remove dups on scan of sorted data
fundamental database operation
O(2n + n): 2n to sort and n to scan


2. Hash:partition into N buckets remove duplicates on insert						
3.Index on projected fields: scan the index pages, avoid reading data 


## The Join: 
- Core database operation
 join of 10s of tables common in enterprise apps
						
- Join algorithms is a large area of research
e.g., distributed, temporal, geographic, multi-dim, range, sensors, graphs, etc
Discuss three basic joins: nested loops, indexed nested loops, hash join
						
- Best join implementation depends on the query, the data, the indices, hardware, etc 


###1.Nested Loops Join
		 	 	 		
# outer ⨝1 inner
# outer JOIN inner ON outer.1 = inner.1 for row in outer:
```						
for irow in inner:
       if row.attr == irow.attr:      # could be any check
                yield (row, irow)
```
####property: 						
-Very flexible,can implement theta join
-Equality check can be replaced with any condition
-Incremental algorithm
As you execute it, it will generate record, while some other join executor requires waiting until you create the hash table or you sorted the data before you can start to get result.
- Cost: M + MN, pretty expensive
For each row of outer, go through each row in the inner and check. If it is matched, then yield that.						
-Is this the same as a cross product? 
No, for cross product, just remove the predicate check.


####What this means in terms of disk IO?						
A join B
A is outer. M pages
B is inner. N pages 
T tuples per page
						
Cost: M +T × M × N
for each tuple t in tableA, (M pages,TM tuples)
scan through each page pi in the inner (N pages) 
compare all the tuples in pi with t 


#### Order matters
M + T x M x N --- A join B
N + T x N x M  --- B join A
Scan “Outer” once, the first constant
Scan Inner multiple times
If inner is small IO, can fit in memory, then cost is M + N.
You have to incur a quadratic cost for implement this join, ideally you want something linear, so we can try indexed nested loops join


###2.Indexed Nested Loops Join					
```						
for row in outer:
    for irow in index.get(row[0], []):
	yield (row, irow)
```
For every row in outer, do index look up, only iterate through everything that match.		Slightly less flexible
Only supports conditions that the index supports 


####What this means in terms of disk IO
						
A join B on sid
M pages in A 
N pages in B
T tuples/page
Primary B+index on B(sid)
Cost of looking up in index is CI 
predicate on outer has 5% selectivity (if there is filter over A)
						
M + T × M × C1


M: read all the outer table in A
T × M × C1:for each tuple t in the outer, (M pages,TM tuples),incur the cost of looking up index
 if predicate(t): (5% of tuples satisfy predicate)
lookup_in_index(t.sid) (CI disk IO) -->M + T × M × C1 x 0.05


###3. Hash Join
Type of index Nested Loops Join;When no index on inner table A join B on sid
How: Create a hash table on inner loop and then run indexed nested loops
						
M pages in A
N pages in B
T tuples/page
Cost of looking up in index is CI predicate on outer has 5% selectivity
						
N + M + T × M × 0.05 × C1
index = build_hash_table(B)             (N pages)
-for every sid in B, create a key, and then match all the tuple with that particular sid. By doing so, speed up C1
for each tuple t in the A:	 	(M pages,TM tuples)					
if predicate(t): 		(5% of tuples satisfy pred)
lookup_in_index(t.sid)		 (CI disk IO) 
<img src = "https://github.com/xz2581/project1/blob/master/9.png">


## Selinger Optimizer Example A⋈<sub>x</sub>B⋈<sub>x</sub>C⋈<sub>x</sub>D
### Preliminaries
+ We have primary B trees B(x) and C(x) both with height 2.
  + C = cost of indexing
      = height + #pages that matched the predicate (1 here since we have equality predicate) = 2 + 1 = 3
+ x values are distinct in each of the table A,B,C,D.
+ Cardinalities (in pages)
  + |A| = 100 
  + |B| = 1000
  + |C| = 10,000
  + |D| = 100,000
  + T = #Tuples/page

### Step One: Find the two-table joins with the least cost (Dynamical Programming example)
+ The existence of B tree indices B(x) and C(x) suggest that B or C should be the "inner" table for the two table join.
+ Assume we use indexed nested loops, the possible combinations of joins and their corresponding costs are: 

<img src = "https://github.com/xz2581/project1/blob/master/3.png">

+ Here we have a tie between AC and AB, let's say we decided to go with AC.

### Step Two: Find the three-table join with the least cost
+ Two options (AC)B and (AC)D
  + For (AC)B we can use indexed nested loop.
  + For (AC)D since there is no index built for D, we can only use nested loop.
  + Intuitively, (AC)B should have less cost.
+ To estimate the cost for the three-table join, we need to find the estimated output size (in tuples) of A⋈C
  + Denote the output size by |A⋈C|
  + |A⋈C| = selectivity * |A X C|
           = 1/max(1K, 100K) * (1K*100K) = 1K
+ (AC)B and (AC)D with their corresponding costs:
<img src = "https://github.com/xz2581/project1/blob/master/6.png">
+ (AC)B is indeed the one with the less cost.

### Step Three: find the four-table join with the least cost
+ Since there is no index built on D, we can use either nested loop join or hash join
+ The details for calculating the cost of nested loop join and hash join are left to the readers.


### The full process for determining the join orders is shown below
<img src = "https://github.com/xz2581/project1/blob/master/7.png">






