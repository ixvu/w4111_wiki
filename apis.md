## SQL != Programming Language
- You cant actually create an application with SQL
- How do you actually access a database without SQL injections?
- How does the application talk to the database and do things?


## Many Database API Options
Couple of different ways to solve this problem:

1. _Embedded SQL_
    - Design a programming language that understands SQL natively
    - **Embedded**: just as you can write a `x = y` statement in an application language, you can write `x = [a SQL query statement]` 

2. _Low-level library with core database calls (DBAPI)_
    - Use a library that's specific to your desired programming language to access the database from your application.

3. _Object-relational mapping (ORM)_
    - Each entity relates to an object via an automated, natural mapping. 
    - Don't have to interface directly with the database at all
    - Objects can save themselves to the database

## Embedded SQL
- Was previously a really popular approach; not any more.
- e.g. Take the C (or C++, Fortran, Pascal, etc.) program that contains SQL, then compile it into parts that are just C code and parts that know how to talk with the DB.
See below Oracle Pro*C code:
```cpp
    int a;
    /* ... */
    EXEC SQL SELECT salary INTO :a
    FROM Employee
    WHERE SSN=876543210; 
    /* ... */
    printf("The salary is %d\n", a);
    /* ... */
```
- The `EXEC SQL` statement sets the salary into a variable `a`.
- **_BUT_**, what if the `SELECT` query returns multiple rows for `salary` which we declared as an integer? 
    - `a` would have to be a list or a set. And anything that references `a` would instead have to loop through `a`.

### Embedded SQL Flow Example
<img src="https://github.com/Wangler/scribenotes/blob/master/embeddedsql.png" width="460">

1. **Preprocessor**: Run the code (with embedded SQL) through a query preprocessor which identifies all the `EXEC SQL` statements and translates them into DBMS library calls. 
2. **Java Compiler**: Take the code and the DB library, and put into an executable which communicates with the database. 

**_BUT_**, if we're using the DB library anyways, why not just use only this core component?

## Aside: Embedded SQL History
Embedded SQL hasn't taken off in popularity. Some of the issues it has faced are:
- There could be runtime problems
- SQL standard has changed over time. There are dependencies between SQL and other programming languages. e.g. If there's a fancy new SQL feature, the compilers of all these other languages need to be augmented to support this new feature
- SQL with enough extensions becomes Turing complete
- **_Making a library alone just sounds good enough_**


## Libraries
### What does a library need to do?
- **_At minimum_**: write a function that takes in a query string and execute it
- It still requires making a connection to the DB
- Given that there are so many different DB engines (i.e. Postgres, MySQL, Microsoft), ideally you'd be able to connect to them all with the same library
- The library serves as an intermediary between your program and all the databases you want to talk to
- e.g.
<img src="https://github.com/Wangler/scribenotes/blob/master/libraryflow.png" width="460">
- In a real life company application, you need to connect to multiple difference databases (e.g. Ads, Users, Billing).

### Engines
- Ideally you want a common interface to all databases.
- e.g. `SQLAlchemy` is a popular library. See below example for establishing a database connection
    - You insert the path string to the db into the `create_engine()` function. 
    - The path looks like: `protocol_name` `://` `location` `:` `port` `/` `database_name`
        - **protocol_name**: the type of db, e.g. postgresql, mysql, sqlite
        - **host**: the location/ip address of the db
        - **port**: must specify the port that the db is on, as it can exist on many

``` python
    from sqlalchemy import create_engine 
    db1 = create_engine(“postgresql://localhost:5432/testdb” )
    db2 = create_engine(“sqlite:///testdb.db”)
    // note: sqllite has no host name (sqlite:///)
```

### Connections
- You establish an HTTP or TCP connection to a particular machine at that particular host and port. The engine then allocates the resources in the db to start running queries.
``` python
    conn1 = db1.connect()
    conn2 = db2.connect()
```
- You can open multiple connections to the same db for running queries in parallel.
- Should run `.close()` on your connections. otherwise resources leak.


### Query Execution
``` python
    conn1.execute(“update table test set a = 1”) 
    conn1.execute(“update table test set s = ‘wu’”)

    foo = conn1.execute(“select * from big_table”)
```
- Run `.execute()` with a string which is blindly sent to the database.
- Some issues:
    - Needs to do error handling
    - Want to be able to allocate the data to a variable.
- What should be the object type of what's returned in `.execute()` (`foo` in this e.g.)? 
    - You don't want `foo` to consist of 5TB of data in memory.
    - Lists? (not immutable)
    - Dictionaries? (take a lot of space with schema information)
    - Sets? (expensive to represent because of duplicate removal)
    - Answer is `cursor` (more on that later)

## Detour: SQL INJECTIONS
- **SQL Injection**: "an injection attack wherein an attacker can execute malicious SQL statements (also commonly referred to as a malicious payload) that control a web application’s database server" ([source](http://www.acunetix.com/websitesecurity/sql-injection/))
- A SQL injection can arise from inserting a string into your db that contains multiple sql statements separated by `;`

**If we insert the variable `name` defined as below:**
``` python
    name = " '); DELETE FROM bad_table; -- " 

    conn1.execute (“““
    INSERT INTO bad_table(name) 
    VALUES('{name}')”””.format(name=name))
```

**Query is:**
``` sql
    INSERT INTO bad_table(name) VALUES(''); DELETE FROM bad_table; -- ');
```

- The bad string adds a semicolon which finishes the insert statement without inserting anything. It then adds `--` which comments out the SQL code that comes after it.
- Must have some function which _sanitizes_ the input string to prevent certain types of sql text from existing
- Most libraries have a parameter in its `execute()` function that allows you to pass in a tuple of query arguments so that it can properly escape input values, thereby sanitizing everything **before** it hits the db.
``` python
    args = (‘Dr Seuss’, ‘40’) 
    conn1.execute(
        “INSERT INTO users(name, age) VALUES(%s, %s)”, args)
``` 
- **NEVER CONSTRUCT RAW SQL STRINGS**. You must always pass in a template instead.

### Placeholders
Use for these query templates. Not standardized. Vary between languages and databases.
- Postgres in Python: Use `%s`
- Postgres in Go: Use `?`
- SQLite in Python: Use `?`

## Relational Impedance Mismatches
The way the db thinks about the world is not the same as how your programming language expresses and describes it
e.g. object(programming) != Row (db)


### By Type
Default Mappings: most programming languages have same set of fundamental data types
e.g. sql:
<img src=https://github.com/Wangler/scribenotes/blob/master/objecttypes.png width=300>

- this mapping is straight forward bc its simple
- complex objects? e.g. python dictionary


### By Object
- Want to write an object with prog lang
- in your prog lang you want to be able to change an object's attribute so that it can be stored in a database
- in the 80s there was a push for object-oriented-database system
- instead of doing embedded sql stuff, if objects can just correspond naturally to ER entities and relations in a relational model, why not just do this translation automatically. use C executables that knows how to change DB automatically when you change them.
- didn't work. for same reason embedded sql didnt work. real companies dont use a single prog lang for all of their DB stuff.
- so creating a c++ variant to store in db didn't make sense
- ORMs are designed to solve this by turning object <-> db data translation into a library (see next section)


### By Results
- if you do select * from a big table, u want a pointer
- a cursor is not a list - no inherent order
- how to do this? 
- you call cursor.next, and you just move to next entry
- the DBMS won't actually compute sailor 9 and beyond. when you .next() from sailor 8 to 9, then db will do more work to get sailor 9
- on program side: no need to run 5tb of data
- on db side: does not need to do work if not asking it to do work
- only cursor.next makes it do work
- cursors are like iterators in normal prog languages
- rowcount - gives you number of records to be returned in query
- keys() = schema of result set
- previous, next, get(idx) = call next x times until you get given result


### By Functions
- solved impedance mismatch for results: cursor
- solved it in objects and records with class objects
- functions? how to insert a defined function into the SQL string for execution?
- convert into a UDF. DB needs to be able to support this particular runtime. 


### By Constraints
-DB-style constraints are assertion statements represented as exceptions
- if u violate constraint, throw error in program
- constraints are naturally expressed in db bc theres only place where data is stored and represented, which is nice
- but in program, it's scattered everywhere, and uhave to read prog to know what's happening
- if u want to check whether an age is in some bound: 
- the JS or app code will make that check
- in addition, DB will check - why have this duplication? 
- JS is error message construction - not validation. the purpose is not to correct. it won't guarantee that the constraint is upheld. only doing it via DB will ensure that. also people managing db are diff from ppl writing the JS code. so they won't trust everyone.
- ORM will have one place to define constraints. 
- it will translate the code into a check constraint in DBMS
- sitting betw prog and db library
- heavyweights in terms of libraries: 
-ODBC - Standard for Microsoft
-JDBC - Standard for Java
- 2 diff library implementations: java and javax
- the semantics and functions exposed are diff

## Object-Relational Mappers
- use objects in your program, read/write relations
- inefficient
- when you want to change your app, it's too hard
- able to express simple things but with very complex queries


e.g. creating an ORM
create a Base database object called User
in python, attribute id is part of user
it corresponds to an int column in db, and its pk is true
mixture of defining schema and object attributes at same time
this is sufficient info for the ORM library to translate the code into a db
- can now create new user, instantiating this object `ed_user`.
- underneath covers, knows how to do more stuff (sessions)
- can do ed_user.password = 'new password'
the database has not changed at this point. in the program, it only made that assignment
- the object has knowledge of how it's repped in the db, so you can run `.save()` so that it can take that info and store back into a db
- these ORMs also let you query these objects
- e.g. want all users of a particular type in a list (see slide) this code is complicated!
- the return value of the query is a list of objects, which is nice
- but its not easy to construct sql strings!
- if you have to create sql strings dynamically in your program, you're just doing string manipulation a lot in your program
- it'll protect you from sql injections bc you're not executing the sql strings directly
- it'll be really tricky knowing how to translate your sql query into this language


### ORM Relationship Challenges (skip in lecture 15)

### Modern Database APIs
- modern languages try to blur the lines betw db query code and app code e.g. linq, scalding, sparksql
- wants to open text file (.log) in spark. each line corresponds to a single record in the form of a very large string
- you have lines which are pointers to a table with a schema with string attribute - there are as many rows in the table as there are lines in the log file
- can do `.filter` (=`where`) so that every record must start with `Error`. this is written in Scala. db is embedded in the app code, so all these objects know that theyre relations. so you can run `.filter` and just pass in a function, which it knows as a SQL query
- distributed db system: you have a relation, apply relational algebra, if you run execute it has a built in engine to run relational statement. 
- the now defined errors variable can map and split by tab
- .count() computes count over all of it
- all within this prog which ahs implemented a db engine, can construct sql statement and execute it
- done to bridge impedance mismatch


### What to Understand
- Impedance mismatch = the way you represent data in the db vs in the app are different
- learn - what is it, what are examples and solutions
- sql injection - what it is how to protect it
- diff uses of a DBAPI
- why embedded sql not good
- why cursors exist






