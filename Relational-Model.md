# Lecture 4 Relational Model
## Roadmap
* Data models history
* Basic concepts
* Data definition language (DDLs)
* Integrity Constraints (ICs)
* Data Manipulation Language Selection Queries (DML) *(Not mentioned in Lec 4)*
* ER --translate--to--> Relational model *(Not mentioned in Lec 4)*

## Data Model History
### Information Management System (IMS), 1968
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

### Network Models, CODASYL, 1969
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

### Relational Model, 1970
* [A relational model of data for large shared data banks](https://www.seas.upenn.edu/~zives/03f/cis550/codd.pdf) 
* Key properties:
   * simple representation: table
   * set oriented model
   * no physical data model needed
* Pros and Cons
   * Pros: simple
   * Cons: slow
 

## Basic Concepts
* **Database** a set of relations
* **Relation** a table with rows and columns
 * **Schema** name of relation + name & type of each column
   * column names are distinct, but column content can be any value fitting the type
 * **Instance** specific set of record, 
   * rows are distinct
 
* Example: *Student* Relation

 | **sid** | name | login | age | gpa |
 | ------- | ---- | ----- | --- | --- |
 | 1       | eugene | ewu@cs | 20 | 2.5 |
 | 2       | enha | neha@cs | 20 | 3.5 |
 | 3       | lin | lin@math | 33 | 3.9 |
 | 4       | leighton | leighton@ee | 18 | 3.0 |
 * Schema: `Student(sid: int, name: string, login: string, age: int, gpa: float)`

* Terminology

 | Formal Name | Synonyms | Example |
 | ----------- | -------- | ------- |
 | Relation    | Table    | *Student* Relation |
 | Tuple       | Row, Record | `(1, eugene, ewu@cs, 20 2.5)`, etc |
 | Attribute   | Column, Field | sid, name, login, age, gpa |
 | Domain      | Type | `int` for sid, etc |
 | Cardinality | number of tuple | 4 |
 | Degree      | number of attributes | 5 |

## Data Definition Language (DDL) 
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

## Integrity Constraints (ICs)
**IC**: a condition that is true for *any* instance of database
* **Domain Constraints** -- Attribution type
        
  ```sql
CREATE TABLE Students(
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

* **Keys**
  * **Candidate key** 
    * Conditions:
       1. Two distinct valid tuples cannot have same values (distinct);
       2. This is not true for any subset of the key (minimal)
    * **superkey**: if (b) is false
  * **Primary key**

    ```sql
CREATE TABLE Enrolled(  -- each student can enroll in a course only once
    sid int,
    cid int,
    grade char(2),
    PRIMARY KEY (sid, cid)  -- combination (sid, cid) 1.is unique and not null in the table,
                                --                    and 2. is used to identify the record
)
    ```

     * Used to identify tuples elsewhere in the database
     * If more than one candidate keys are in relation, then admin assigns one as primary key
        * if DNA and sid both are candidate keys, we'd better choose sid, because we need to copy the whole sequence to identify, shorter one will be better
     * indicates:
        * unique
        * not NULL
        * primary way to refer to record
     * Example: What does the DDL say?

        ```sql
CREATE TABLE Enrolled(
sid int, cid int, grade char(2),
PRIMARY KEY (sid)
UNIQUE (cid, grade)
        ```
          * `sid` is the primary key means each student can only appear once, i.e. enroll in one course
          * `(cid, grade)` is unique means each course can have each grade appear for once. E.g. only one student can get A/B/C/F in 4111. This also limits the number of students for a course.
 * **Foreign key**
    * **Referential Integrity**: if all foreign key constraints are enforced
       * Enforced: well-maintained relational database
       * Not enforced: HTML links (404 not found), Restaurant menus (out of order), Business card (number changed)
    * Imagine we have two relations *Students* and *Enrolled*, and *Enrolled*'s column referred to *Students* via *Students*'s primary key

    * <img src="https://github.com/leighton613/scribenotes/blob/master/pics/foreign_key_1.png" width="500">
    * set of fields in Relation used to refer to tuple in other relations by its primary keys

      ```sql
CREATE TABLE Enrolled(
    sid int, cid int, grade char(2),
    PRIMARY KEY (sid, cid),
    FOREIGN KEY (sid) REFERENCES Students -- field sid represents as primary key in relation Student
)
      ```
      * If primary key of *Students* is `(sid, foo)` then we need the combination to refer to *Students* relation.
         * <img src="https://github.com/leighton613/scribenotes/blob/master/pics/foreign_key_2.jpg" width="600">

         ```sql
CREATE TABLE Enrolled(
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
