# Roadmap
* Data models history
* Basic concepts
* Data definition language (DDLs)
* Integrity Constraints (ICs)
* Data Manipulation Language Selection Queries (DML) *(Not mentioned in Lec 4)*
* ER --translate--to--> Relational model *(Not mentioned in Lec 4)*

# Data Model History
## Information Management System (IMS), 1968
<img src="https://github.com/leighton613/scribenotes/blob/master/pics/ims.png" width="460">

* widely used, runs the world's most workloads
* major U.S. insurance companies use, while may not be adapted by new applications
* tree structure:
 * with data layouts as tree structure
* Cons:
 * Limited ability to write arbitrary queries, needs for loops to navigate tree structure;
 * Changing hierarchy structure can breaks the whole application;
 * Lacks of physical data independence;
* Example: [IBM IMS](https://www-01.ibm.com/software/data/ims/)

## Network Models, CODASYL, 1969
<img src="https://github.com/leighton613/scribenotes/blob/master/pics/network_models.png" width="400">

* connected by named sets
* graph structure
 * uses arrows: 1-many relationship;
 * uses at least one root for accessing the graph;
* Cons:
 * Complex without tool to guide in graph, and have to keep track of where you are in the graph
 * Must load all data while memory is expensive at that time
 * No physical nor logical data independence
* Example: [Oracle CODASYL DBMS](http://www.oracle.com/technetwork/products/rdb/index-086844.html)

## Relational Model, 1970
* [A relational model of data for large shared data banks](https://www.seas.upenn.edu/~zives/03f/cis550/codd.pdf) 
* Key properties:
   * simple representation: table
   * set oriented model
   * no physical data model needed
* Pros and Cons
   * Pros: simple
   * Cons: slow

# Basic Concepts
* **Database** a set of relations
* **Relation** a table with rows and columns
 * **Schema** name of relation + name & type of each column
   * column names are distinct, but column content can be any value fitting the type
 * **Instance** specific set of record, 
   * rows are distinct
 
* Example: *Student* Relation

 | **sid** |  name  |     login   | age | gpa |
 | -------:| ------:| ----------- | --- | --- |
 | 1       | eugene |   ewu@cs    | 20  | 2.5 |
 | 2       |  enha  |   neha@cs   | 20  | 3.5 |
 | 3       |   lin  |   lin@math  | 33  | 3.9 |
 | 4       |leighton| leighton@ee | 18  | 3.0 |

 * Schema: `Student(sid: int, name: string, login: string, age: int, gpa: float)`

* Terminology

 | Formal Name | Synonyms | Example |
 | -----------:| -------- | ------- |
 | Relation    | Table    | *Student* Relation |
 | Tuple       | Row, Record | `(1, eugene, ewu@cs, 20 2.5)`, etc |
 | Attribute   | Column, Field | sid, name, login, age, gpa |
 | Domain      | Type | `int` for sid, etc |
 | Cardinality | number of tuple | 4 |
 | Degree      | number of attributes | 5 |

# Data Definition Language (DDL) 
 **DDL**: a syntax defining data structures, especially database schemas.[Ref: wiki](https://en.wikipedia.org/wiki/Data_definition_language)
* `CREATE`: creates database, table or index.

  ```sql 
CREATE TABLE Name(   -- syntax to create a table
    columnName columnType,
...
)
  ```

* `DROP`: destroys an existing database, table, index, or view
* `ALTER`: modifies existing database object
* `RENAME`: modifies an existing database object

# Integrity Constraints (ICs)
**IC**: a condition that is true for *any* instance of database
* **Domain Constraints** -- Attribution type
        
  ```sql
CREATE TABLE Students (
    sid int,    -- syntax: colname type
    name text,  -- specific type of sid is int
    login text,
    age int,
    gpa real
)
  ```
   * Programmers can make type assumptions on data, and no need to check every time
   * Tells Database hints how to store data
* **Null Constraints**

  ```sql
CREATE TABLE Students(
    sid int NOT NULL, -- Default: can take NULL value
    name text,        -- NULL means empty/no value/ill defined/not exist/know nothing about
    login text,
    age int,
    gpa real
    )
  ```

# Keys
## Candidate key
* Conditions:
  1. Two distinct valid tuples cannot have same values (distinct);
  2. This is not true for any subset of the key (minimal)
* **superkey**: if (b) is false

## Primary key

```sql
CREATE TABLE Enrolled (  -- each student can enroll in a course only once
    sid int,
    cid int,
    grade char(2),
    PRIMARY KEY (sid, cid)
               -- combination (sid, cid) 1.is unique and not null in the table,
               --                    and 2. is used to identify the record
)
```

* Used to identify tuples elsewhere in the database
* If more than one candidate keys are in relation, then admin assigns one as primary key
  * if DNA and sid both are candidate keys, we'd better choose sid, because we need to copy the whole sequence to identify, shorter one will be better
  * indicates:
    * unique
    * not `NULL`
    * primary way to refer to record
* Example: What does the DDL say?

```sql
CREATE TABLE Enrolled (
    sid int, cid int, grade char(2),
    PRIMARY KEY (sid)
    UNIQUE (cid, grade)
```
* `sid` is the primary key means each student can only appear once, i.e. enroll in one course
* `(cid, grade)` is unique means each course can have each grade appear for once. E.g. only one student can get A/B/C/F in 4111. This also limits the number of students for a course.

#### Question: Can a primary key be null/blank?  
**No!**  
```sql
PRIMARY KEY(a,b,c) --means
    a NOT NULL
    b NOT NULL
    c NOT NULL
``` 

***

## Foreign key
**Referential Integrity**: if all foreign key constraints are enforced
  * Enforced: well-maintained relational database
  * Not enforced: 
    * HTML links (404 not found)
    * Restaurant menus (out of order)
    * Business card (number changed)
  * Legitimate reasons to violate: unnecessary or too costly
  * Imagine we have two relations *Students* and *Enrolled*, and *Enrolled*'s column referred to *Students* via *Students*'s primary key

 * <img src="https://github.com/leighton613/scribenotes/blob/master/pics/foreign_key_1.png" width="500">
 * set of fields in Relation used to refer to tuple in other relations by its primary keys

```sql
CREATE TABLE Enrolled (
    sid int, cid int, grade char(2),
    PRIMARY KEY (sid, cid),
    FOREIGN KEY (sid) REFERENCES Students -- field sid represents as primary key in relation Student
)
```
* If primary key of *Students* is `(sid, foo)` then we need the combination to refer to *Students* relation.
  * <img src="https://github.com/leighton613/scribenotes/blob/master/pics/foreign_key_2.jpg" width="600">

```sql
CREATE TABLE Enrolled (
    sid int, cid int, grade char(2),
    PRIMARY KEY (sid, foo, cid),
    FOREIGN KEY (sid, foo) REFERENCES Students -- field (sid, foo) represents as primary key in relation Student
)
```
* Q: Why not use a table to keep those arrows?
* A: 
  1. Save *space*. New table takes up more disk space;
  2. No need to worry about other tables when *modifying*;
  3. Avoid dangling references when deleting (otherwise, application developer will be responsible for maintaining tables);
  4. Have access to relationships from table design (for application development reasons).

#### Question: Can a foreign key be null/blank?  
**Yes!**  
```sql
CREATE TABLE tweet (
  tid int, author_uid REFERENCES users
``` 
Tweet of (0, null) is okay

***

## Single column keys
```sql
name type PRIMARY KEY
name type UNIQUE
name type REFERENCES table
```

### Shortcut notation
```sql
CREATE TABLE A (
    id int,
    ref int,
    PRIMARY KEY (id),
    FOREIGN KEY (ref) REFERENCES other
);
```
collapses to:
```sql
CREATE TABLE A (
    id int PRIMARY KEY,
    ref int REFERENCES other
);
```

## Enforcing Integrity Constraints
Run checks any time the database changes.
Enact "Gatekeepers" on any modification statement (**INSERT**, **UPDATE**, **DELETE**) can prevent database from being "corrupted" (violating integrity constraints)

* On **INSERT**

  What options are available when a new _Enrolled_ tuple refers to non-existent _Student_?
  1. Reject Insertion

* On **DELETE**

  What options are available if a _Student_ tuple is deleted?
  1.   DELETE dependent _Enrolled_ tuples
  2.   Reject deletion
  3.   set _Enrolled_.sid to default value or `null` (null in SQL: unknown/inapplicable)

## General Constraints
**Boolean expressions** are a powerful tool to define arbitrary constraints.
Need to be careful, as these are expensive to check, as the database has no guarantee on how l
```sql
CREATE TABLE Enrolled (
    sid int,
    cid int,
    grade char(2),
    CHECK (grade = 'A' or grade = 'B' or grade = 'C' or grade = 'D' or grade = 'F')
)
```
Cautions:
* Expensive to check as database has no guarantee on how long the statement is.
* Very difficult to optimize

## Where do Integrity Constraints originate?
* Based on application semantics and use cases  
* IC is statement about the world (all possible instances)  
  - Can't infer ICs by staring at an instance  
  e.g. Is "login" a unique candidate key? Maybe?  
* Unique and foreign key ICs are very common (others less so)  

Research on correlated attributes to recommend foreign key relationships

## Referential Integrity
A database has referential integrity if all foreign key constraints are _enforced_ (no dangling references)

Examples where referential integrity is not enforced:
* Weblinks
* Restaurant Menus
* Some relational databases!

Why not? Too expensive or don't care about it.

# Translating ER diagrams into Relational Schemas with constraints

## Entity Set to Relation
<img src="https://github.com/davidkuhta/scribenotes/blob/master/images/entity_set.png" width="600">  
1. Include all entity set attributes  
2. Entity set key becomes relation primary key  
```sql
CREATE TABLE Course(
  cid int,
  name text,
  loc text,
  PRIMARY KEY (cid)
)
```

## Relationship Set without constraint to Relation
<img src="https://github.com/davidkuhta/scribenotes/blob/master/images/relationship_set.png" width="600">  
1. Add keys for each entity set as foreign keys: _superkey_  
  Note: if there are other constraints, you may not need all columns  
2. Include all attributes of the relationship set  
```sql
CREATE TABLE Takes(
  uid int,
  cid int,
  since date,
  PRIMARY KEY (uid, cid),
  FOREIGN KEY (uid) REFERENCES Users,
  FOREIGN KEY (cid) REFERENCES Courses
)
```
***
### Why do we need the primary key?  
Application dependent difference between `PRIMARY KEY (uid, cid)` and `PRIMARY KEY (uid, cid, since)`
***

Strict translation of relationship set implies only one instance of each relation "pair" and corresponds to a primary key composed of the foreign keys.

## "At Most One" to Relation
<img src="https://github.com/davidkuhta/scribenotes/blob/master/images/at_most_one_set.png" width="600">  
1. Add keys for each entity set as foreign keys: _superkey_  
  Note: if there are other constraints, you may not need all columns  
2. Include all attributes of the relationship set  
```sql
CREATE TABLE Instructs(
  uid int,
  cid int,
  PRIMARY KEY (cid),
  FOREIGN KEY (uid) REFERENCES Users,
  FOREIGN KEY (cid) REFERENCES Courses
)
```
***
### What should the primary key be set to?  
Set primary key to `cid` (course). This establishes that for a given course there can be at most one relation. Another way to check is to assess the cardinality.  One can see that if there where five _Courses_ and five-hundred _Users_, this relationship arrangement would mean that the cardinality of the _Instructs_ table would be equivalent to that of the _Courses_ table, and hence five rows in the _Instructs_ table. This make senses.
***

### "At Most One" combined

Noting that the _Course_ table and _Instructs_ table have the same primary key of `cid`, it may be possible to combine them in one table.  
<img src="https://github.com/davidkuhta/scribenotes/blob/master/images/at_most_one_set.png" width="600">  
```sql
CREATE TABLE Course_Instructs(
  uid int,
  cid int,
  name text,
  loc text,
  PRIMARY KEY (cid),
  FOREIGN KEY (uid) REFERENCES Users
)
```
***
### Why combine the Course and Instructs tables?  
Combining the table sets us up for discussing "total participation". 

### How to represent courses without an instructor?  
Allow `uid` to be `NULL` (in conjunction with other _Instruct_ attributes)

### How to ensure _total participation_ (i.e. _Course_ definitely has an _Instructor_)?  
Combine relationship into Courses and require `uid` to not be `NULL`
```
uid int NOT NULL
...
FOREIGN KEY (uid) REFERENCES Users ON DELETE NO ACTION
```
***

## At Least One Constraint
<img src="https://github.com/davidkuhta/scribenotes/blob/master/images/at_least_one_relationship.png" width="600">  
1. Can't be easily expressed! (Needs to be enacted at the application level, because the database cannot create the relationship automatically)  
```sql
CREATE TABLE Instructs(
  uid int,
  cid int,
  PRIMARY KEY (cid, uid),
  FOREIGN KEY (cid) REFERENCES Course
  FOREIGN KEY (uid) REFERENCES User
)
```

## Weak Entity to Relation
<img src="https://github.com/davidkuhta/scribenotes/blob/master/images/weak_entity_set.png" width="600">  
1. Translate weak entity set and identifying relationship set into a single table.  
2. Ensure deletes are cascaded such that when the owner entity is deleted, all owned weak entities are also be deleted.
E.g. Users can post WallPosts and WallPosts cannot exist without a corresponding user. Hence the WallPost primary key is dependent on the user id. Accordingly combine WallPosts entity and Posted relationship.  
```sql
CREATE TABLE Wall_Posted(
  uid int,
  post_text text,
  posted_time DATE,
  PRIMARY KEY (uid, posted_time),
  FOREIGN KEY (uid) REFERENCES Users ON DELETE CASCADE
)
```

<detail>
    <summary>Can a weak entity be involved in other relationships?</detail>
    Yes! No limits to relationship with weak entities
</detail>

<detail>
    <summaryWhat are key features of a weak entity?</detail>
    * Cannot be uniquely identified by its own attributes
    * Binary "exactly one" relationship with "owner"
    * Delete owner: delete weak entities references
</detail>

##ISA Hierarchies
<img src="https://github.com/davidkuhta/scribenotes/blob/master/images/isa_set.png" width="600">  

* Numerous way to represent, but none ideal.  
* Inheritance is difficult to represent in the relational model.  
* There are tradeoffs  

### Options:  
### 1. Keep base relation  

  1. Shared child A & B attributes recorded in parent (Instructors and Students recorded in Users)
  2. Unique attributes recorded in child A or child B relation (Information in Instructors or Students Relation)
  3. Primary key is the key of the parent table and is the foreign key for the child tables

### 2. Only keep child relations

  1. Children copy attributes from parents (Instructors and Students contain all information from Users)

```sql
CREATE TABLE Users(       uid int, name text,     PRIMARY KEY(uid) )
CREATE TABLE Instructors( uid int, rating int,    PRIMARY KEY(uid), FOREIGN KEY (uid) REFERENCES Users )
CREATE TABLE Students(    uid int, grade char(2), PRIMARY KEY(uid), FOREIGN KEY (uid) REFERENCES Users )
```
Only if covering constraint = yes (all users are instructors or students)

## Aggregation
1. Convert the aggregated relationship into an entity (similar to other entities)
E.g. Donates: PRIMARY KEY (company_id, course_id)
     Manages: References this key
```sql
CREATE TABLE Donates( 
    company_id int,
    course_id int,
    amount int,
    PRIMARY KEY(company_id, course_id),
    FOREIGN KEY(company_id) REFERENCES companies,
    FOREIGN KEY(course_id) REFERENCES courses
);
CREATE TABLE Manages( 
    uid int,
    company_id int,
    course_id int,
    since DATE,
    PRIMARY KEY(uid, company_id, course_id),
    FOREIGN KEY(uid) REFERENCES Instructors,
    FOREIGN KEY(company_id, course_id) REFERENCES Donates
);
```
####TODO: Aggregation Image ####

## Entities vs Relationships
### "<entity> has <relationship> with <entity>"

* user has instructor relationship with courses
* user has friendship with user
* user can view profile

**Question to ask oneself: Is the relationship something you actually want to store?**

## Data vs Logic
* ER model stores minimum data to support application. Does not store logic!  
#### TODO: Insert Data vs Logic Image

## Multiple Relationships  
#### TODO: Insert Multiple Relationships

Note: Lists, Strings
- Thinking about how to store the data, not the data itself, violates physical data independence.

<detail>Refer to non-prima
## Review
* **Relation**: Set of tuples with typed values (table)
* **Schema**: Names and types for values in relation
* **Database**: Set of relations
* **SQL**: Structured Query Language
* **Integrity Constraint**: Restrictions on valid data
* **Candidate Key**: Minimal set of fields to identify a tuple in a relation.
* **Primary Key**: Designated identifier for a tuple
* **Foreign Key**: Reference to another tuple (Logical pointer)

## See [Playing With Constraints](https://github.com/w4111/scribenotes/wiki/Livecode---Playing-With-Constraints) Livecode for a hands-on review.

## Question & Answer
<details>
    <summary>Issue with auto-incrementing or auto-generated primary key identifier, if you have some mistake in the values of the tuples you may not catch it. Example: Very large databases, easy to miswrite</summary><br />
Could use a hash of a tuple or hash of the memory address of the tuple.
</details>

<details>
    <summary>Talking to ER models not specific to any</summary><br />
    ER model is one way to represent relationships in data. Natural mapping between ER model and relational model (whereas more difficult in the hierarchical)
</details>

<details>
    <summary>[Can foreign key be `NULL`?](#at-most-one-to-relation)</summary><br />
    No enforcement that we have to have a value anywhere, unless we say so.
</details>

<details>
    <summary>[Can the `uid` be `NULL`; wouldn't it be impossible to have a `NULL` foreign key?] (#at-most-one-combined)</summary><br />
    All this says is there is a `uid` value we know what it refers to. The foreign key statement means "when you interpret the value for `uid`, know it's a primary key for the `User` table". 
</details>

<details>
    <summary>[How does the Donates table have a primary key? (context of aggregation)](#isa-hierarchies)</summary><br />
    Primary Key of _Donates_ is a combination of course and company (composite).
</details>

<details>
    <summary>Refer to non-PrimaryKey attributes using foreign keys?</summary>
    ER diagrams: never happens
    SQL: Must refer to primary key/unique (candidate key) `FOREIGN KEY (a,b) REFERENCES other(x,y)`
</details>

<details>
    <summary>SQL for 2 relationships, 1 entity</summary><br />
    Two relationships = two tables  
    Relationship with itself = two attributes
</details>

<details>
    <summary>How to formulate ISA hierarchy so children can't overlap?</summary><br />
    Users, Students, Instructors  
    - 3 tables: A user could be in all three  
    - 2 tables: Can't be a plain user  
    **No way to express this constraint**
</details>

```sql
CREATE TABLE follows (
    source int,
    destination int,
    PRIMARY KEY (source, destination),
    FOREIGN KEY (source) REFERENCES users,
    FOREIGN KEY (destination) REFERENCES users
);
```
