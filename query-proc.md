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
          +    Number of leave pages: 10K. 
          +    Number of pages need to read: 90%x10K=9K. 
          +    Given 100 DEs of each page, number of pages one level up: 10K/100=100. 
          +    One level up is 1 page, root page. 
          +    Total height: log100(10K)=2. 
          +    Total cost: 9K+2.
     +    Secondary B+ Tree: 
          +    Leave pages store pointers to tuples.
          +    Number of leave pages = Total Number of tuples/number of DEs in each page = 10x10K/100=1K
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
    Primary: data is store in the leave nodes. 
    Secondary: leave nodes only contain pointers to actual data files.
    The query optimizer attempts to determine the most efficient way to execute a given query by considering the possible query plans.