# Creating a Database
1. On Instabase homepage, select a repository
2. Click the _New_ dropdown, and select "Create New Database"
3. Select _Postgres_ as the **Database Type** and provide a **Database Name**

# Playing With Constraints
1. Open lec5.ipynb
1. Connect to your database: `ib.connect_db('ib://ewu/w4111-public/databases/w4111')` (replacing the url with that of the database you previously created.

2. Try selecting from a non-existant table
    ```sql
    %%sql
    select * from A
    ```
> `ERROR: ERROR: relation "a" does not exist`  

### Domain Constraints Section  
3. In the first cell, create Table A and provide three attributes
    ```sql
    %%sql
    CREATE TABLE A ( a int, b text, c float );
    ```
> `{u'status': u'OK'}` 

4. Create a new cell and select all values from Table A:
    ```sql
    %%sql
    SELECT * FROM A;
    ```
>|   | a | b | c |
|---|---|---|---|

5. Replace the cell's contents and insert new values into Table A:
    ```sql
    %%sql
    INSERT INTO A(a,b,c) VALUES (1, '2', 3);
    ```
> `{u'status': u'OK'}` 
This worked even though `3` wasn't `3.0`, because Postgres casted `3` into a float.

5. Replace the cell's contents to insert values with alternate types into Table A:
    ```sql
    %%sql
    INSERT INTO A(a,b,c) VALUES (1, 2, 3);
    ```
> `{u'status': u'OK'}` 

6. Insert a new cell and select all values from Table A to see your previous inserts:
    ```sql
    %%sql
    select * from A;
    ```
>|   | a | b | c |
|---|---|---|---|
| 0 | 1 | 2 | 3 |
| 1 | 1 | 2 | 3 |

Postgres will cast values if it knows how to. So even though we provided an integer, Postgres knows how to cast it into a string and does so for us.

7. Replace the second cell's contents to again insert values with alternate types into Table A:
    ```sql
    %%sql
    INSERT INTO A(a,b,c) VALUES (1.5, 4, 5);
    ```
> `{u'status': u'OK'}` 

8. Re-run the last cell to see Table A:
    ```sql
    %%sql
    select * from A;
    ```
>|   | a | b | c |
|---|---|---|---|
| 0 | 1 | 2 | 3 |
| 1 | 1 | 2 | 3 |
| 2 | 2 | 4 | 5 |

7. Replace the second cell's contents to again insert values with missing attributes into Table A:
    ```sql
    %%sql
    INSERT INTO A(a,b) VALUES (3, '4');
    ```
> `{u'status': u'OK'}` 
