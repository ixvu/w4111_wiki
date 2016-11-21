## Background
+    Task: Find an efficient physical query plan (execution plan) for an sql query.
+    Goal: minimize the evaluation time for the query, compute query result as fast as possible.
+    Cost Factors: Disk accesses, read/write operations, [I/O, page transfer] (CPU time is typically ignored).

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