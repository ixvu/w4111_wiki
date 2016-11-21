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
    A relational algebra expression can be evaluated in many ways. An annotated expression specifying detailed evaluation strategy is called the execution plan (includes, e.g., whether index is used, join algorithms, . . . ) Among all semantically equivalent expressions, the one with the least costly evaluation plan is chosen.

## Query Evaluation
-    Push: Operators are input-driven; As operator gets data, push it to parent operator; May run out of number and overload(???).
-    Pull: The root operator is likely the cursor; Operators are demand-driven; Do not do anything until parent operator asks for the next data.

### Condition 1: Data doesn't have to be sorted.
B: number of pages; M: number of pages matched in WHERE clause.
+    Execution Types:
     +    Naive execution: Following query steps and only pass data to next level if it is completely processed in one level. Cost can be up to B+B+M given a where clause.
     +    Pipelining. evaluate several operations simultaneously, and pass the result on to the next operation. Cost: B.
+    Comparison:
     +    Pipelining is usually cheaper than naive execution, because temporary relations are not generated and stored on disk.
     +    Pipelining is not always possible, e.g. sort because each page needs to be compared to the rest unsorted pages.
+    Example query: SELECT a FROM R WHERE a>10
+    Pipeline Execution: Read a page from offers and pass to selection. As selection looks for tuples in the page that satisfy the predicate, can read the next page from offers. Similarly, pass valid pages to projection, and as projection returns value of projected attributes, selection can look for tuples in the page that satisfy the predicate.

### Condition 2: Data is sorted and indexed.(???)
+    Execution Types:
     +    Hash index: It is only applicable for equalities.
     +    B+ tree index: It uses index to find all record, identify first index page and number of pages that matches and pass to projector operator. Cost is reduced to logarithm.
+    Example query: SELECT a FROM R WHERE a>10. Assume we have 10K pages, each page has 10 tuples, each page has 100 directory entries. Assume uniform distribution with ‘a’ ranging from 0 to 100. 
+    Comparison:
     +    Hash table(a): Need to go over all pages since hash table only supports equality. Cost: 10K pages.
     +    Primary B+ Tree: Root node 1, having 100 DEs. Second level 100 pages, each having 100 DEs. Third level 10K pages. Number of pages need to read: 90%x10K=9K. Number of steps needed to reach the starting page: log100(10K)=2. Total cost: 9K+2.
     +    Secondary B+ Tree: Number of tuples: 10x10K=100K. Number of pages needed to store pointer to each tuple: 100K/100=1K (each directory pointing to one tuple). Number of directory needed to read all satisfied data: 1Kx90%x100=90K. Number of steps to reach the starting directory: root+leave+middle=3. Total cost: 90K+3.

## Access path
Access Path refers to the path chosen by the system to retrieve data after a structured query language (SQL) request is executed. A query may request at least one variable to be filled up with one value or more.

### Index + matching condition
+    Sequential Scan: doesnt accept condition.
+    Hash Index Search: accept equality conditions on all search keys.
+    Tree Index Search: accept conditions on prefix of search keys.

### How to pick access path
+    Depend on number of data pages: secondary indicies, less than 2%, not worthy to use.
+    Selectivity only for selection operation.