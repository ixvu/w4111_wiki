# Creating a Database
1. On Instabase homepage, select a repository
2. Click the _New_ dropdown, and select "Create New Database"
3. Select _Postgres_ as the **Database Type** and provide a **Database Name**

# Playing With Constraints
## Setup
1. Open lec5.ipynb
1. Connect to your database: `ib.connect_db('ib://ewu/w4111-public/databases/w4111')` (replacing the url with that of the database you previously created.

2. Try selecting from a non-existent table
    ```sql
    %%sql
    select * from A
    ```
> `ERROR: ERROR: relation "a" does not exist`  

## _Domain Constraints_ Section  
3. In the first cell, create **Table A** and provide three attributes
    ```sql
    %%sql
    CREATE TABLE A ( a int, b text, c float );
    ```
> `{u'status': u'OK'}` 

4. Create a new cell and **select all** values from **Table A**:
    ```sql
    %%sql
    SELECT * FROM A;
    ```
>|   | a | b | c |
|---|---|---|---|

5. Replace the cell's contents and insert new values into **Table A**:
    ```sql
    %%sql
    INSERT INTO A(a,b,c) VALUES (1, '2', 3);
    ```
> `{u'status': u'OK'}` 
This worked even though `3` wasn't `3.0`, because Postgres casted `3` into a float.

5. Replace the cell's contents to **insert** values with alternate types into **Table A**:
    ```sql
    %%sql
    INSERT INTO A(a,b,c) VALUES (1, 2, 3);
    ```
> `{u'status': u'OK'}` 

6. Insert a new cell and **select** all values from **Table A** to see your previous inserts:
    ```sql
    %%sql
    select * from A;
    ```
>|   | a | b | c |
|---|---|---|---|
| 0 | 1 | 2 | 3 |
| 1 | 1 | 2 | 3 |

Postgres will cast values if it knows how to. So even though we provided an integer, Postgres knows how to cast it into a string and does so for us.

7. Replace the second cell's contents to again **insert** values with alternate types into **Table A**:
    ```sql
    %%sql
    INSERT INTO A(a,b,c) VALUES (1.5, 4, 5);
    ```
> `{u'status': u'OK'}` 

8. Re-run the last cell to see **Table A**:
    ```sql
    %%sql
    select * from A;
    ```
>|   | a | b | c |
|---|---|---|---|
| 0 | 1 | 2 | 3 |
| 1 | 1 | 2 | 3 |
| 2 | 2 | 4 | 5 |

7. Replace the second cell's contents to again **insert** values with missing attributes into **Table A**:
    ```sql
    %%sql
    INSERT INTO A(a,b) VALUES (3, '4');
    ```
> `{u'status': u'OK'}` 

9. Re-run the last cell to see **Table A**:
    ```sql
    %%sql
    select * from A;
    ```
>|   | a | b | c |
|---|---|---|---|
| 0 | 1 | 2 | 3 |
| 1 | 1 | 2 | 3 |
| 2 | 2 | 4 | 5 |
| 3 | 3 | 4 | None |

Instabase translates the `NULL` value to `None` as they are equivalent between database.

9. Re-run the last cell to **select** only the values of **column a** from **Table A**:
    ```sql
    %%sql
    select a from A;
    ```
>|   | a |
|---|---|
| 0 | 1 |
| 1 | 1 |
| 2 | 2 |
| 3 | 3 |

9. Re-run the last cell to **select** only the values of column a, incremented by 1, from **Table A**:
    ```sql
    %%sql
    select a+1 from A;
    ```
>|   | ?column? |
|---|---|
| 0 | 2 |
| 1 | 2 |
| 2 | 3 |
| 3 | 4 |

10. Re-run the last cell to **select** only the values of **column b**, incremented by 1, from **Table A**:
    ```sql
    %%sql
    select b+1 from A;
    ```
> `Error: ERROR: operator does not exist: text + integer

Can we force it to convert text to numbers?

10. Re-run the last cell to **select** only the values of **column b**, casted to integers and incremented by 1, from **Table A**:
    ```sql
    %%sql
    select b::int+1 from A;
    ```
>|   | ?column? |
|---|---|
| 0 | 3 |
| 1 | 3 |
| 2 | 5 |
| 3 | 5 |

11. Replace the second cell's contents to insert values, with a non-integer for column b, into **Table A**:
    ```sql
    %%sql
    INSERT INTO A(a,b) VALUES (10, 'foo');
    ```
> `{u'status': u'OK'}` 

12. Re-run the last cell to select only the values of column b, casted to integers and incremented by 1, from **Table A**:
    ```sql
    %%sql
    select b::int+1 from A;
    ```
> `Error: ERROR: invalid input syntax for integer: "foo"`

13. Clean up your database by dropping **Table A**.
    ```sql
    %sql drop table a;
    ```
> {u'status': u'OK'}

## _Primary Keys_ Section 
1. In the first cell, create **Table A** and provide three attributes
    ```sql
    %%sql
    CREATE TABLE A ( a int, b text, primary key (a) );
    ```
> `{u'status': u'OK'}` 

2. Create a new cell, and insert values into **Table A**:
    ```sql
    %sql insert into A values(1, 'a');
    ```
> `{u'status': u'OK'}` 

3. Create a new cell, and select all values from **Table A**:
    ```sql
    %sql select * from a
    ```
>|   | a | b |
|---|---|---|
| 0 | 1 | a |

4. In the second cell, try inserting a value with the same `a` attribute:
    ```sql
    %sql insert into A values(1, 'b');
    ```
> `Error: ERROR: duplicate key value violates unique constraint "a_pkey" Detail: Key (a)=(1+ already exists.` 

5. In the second cell, try to insert a value with a `null` attribute:
    ```sql
    %sql insert into A values(null, 'b');
    ```
> `Error: ERROR: null value in column "a" violates not-null constraint Detail: Failing row contains (null, b).` 


6. Re-run your last cell to **select** all values from **Table A**, to ensure it has not changed:
    ```sql
    %sql select * from a
    ```
>|   | a | b |
|---|---|---|
| 0 | 1 | a |

7. Reset your database by dropping **Table A** in the first cell
    ```sql
    % sql drop table a;
    ```
> `{u'status': u'OK'}` 


8. Create a new **Table A** with two primary keys
    ```sql
    %%sql
   CREATE TABLE A ( a int, b text, primary key (a), primary key (b) );
    ```
> `Error: ERROR: multiple primary keys for table "a" are not allowed` 

9. Create a new **Table A** with a primary key and a candidate key
    ```sql
    %%sql
   CREATE TABLE A ( a int, b text not null, ****primary key (a), primary key (b), unique (b) );
    ```
> `{u'status': u'OK'}` 

As **attribute b** is constrained to unique and not null it is an acceptable candidate key

# _References_ Section 


# Question & Answer
1. Are table names case-sensitive?
> No, table names are not case-sensitive.