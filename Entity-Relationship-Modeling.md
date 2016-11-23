# Homework review

![](https://github.com/micklinISgood/scribenotes/blob/master/L2/img1.png)

target = "single malt scotch"

target in `el.lower()` will match if there is a substring in `el.lower()` matches target.

modifying to **target == el.lower()** will get the correct result.



# Lecture 2 Entity-Relationship Model
## Inconsistencies/ConstraintViolations

* duplicate attribute: 

  * Search a author name on [dblp](http://dblp.uni-trier.de/), the site for computer science publications. You may find different authors with the same name, which is not unique. Therefore, we call this as Inconsistencies.
  * Error may happen if two people own the same bank account.
  * Error may happen if two people own the same facebook id. Sending messages to wrong friends, etc.

* unique attribute: email

## It is Hard to Design Applications

Task: Design an application to find a product in a supermarket. What's your data model?

* Product
  * inStock
  * Price
  * Categories
  * Expiration
  * Barcode(primary key)
  * Name
  * Alternates
* Loc 
  * Aisle
  * Shelf
  * Rack
* (Prod, loc)
* User
 * Name/pw
 * List
 * Qty
 * Product
 * loc

## Could we draw a line to represent the relationship between entities?
(If you miss something, you may need to redesign the app.)
* Entity: a row, which has a set of attributes.
* Entity Set: a table. Differentiate each entity by a primary key, which is unique.    
Note: set is non-duplicate.

## Keys
* unique
 * candidate key : a unique key 
 * primary key : a selected candidate key. Of course unique.
 * composite key : two attributes to make a row unique
   * airplane number + flight date makes a flight unique, ie can be specifically identified  

      | Airplane Number | Flight Date |
      | -------------   |:-----------:|
      | UA 36           | 03 Sep 2016 |
      | EVA 66          | 04 Sep 2016 |
      | UA 36           | 04 Sep 2016 |
      | DEL 7           | 03 Sep 2016 |
 * surrogate key : a manually constructed key to identify a row  
   * flight number  

      |Flight Number| Airplane Number | Flight Date |
      |-------------| -------------   |:-----------:|
      |1            | UA 36           | 03 Sep 2016 |
      |2            | EVA 66          | 04 Sep 2016 |
      |3            | UA 36           | 04 Sep 2016 |
      |4            | DEL 7           | 03 Sep 2016 |

* non-unique
 * foreign key

## Entity graph representation
![](https://github.com/micklinISgood/scribenotes/blob/master/L2/img7.37.50%20PM.png)

<u>Keys</u> are underlined

Age is not good because age may vary with time. Use birthday instead.

## Relationships
* Symbol definition
![](https://github.com/micklinISgood/scribenotes/blob/master/L2/img10.37.51%20PM.png)
* Relationship
 * Key Constraints
 * 1-to-1
   * A course has at most one instructor AND users can instruct at most one course
![](https://github.com/micklinISgood/scribenotes/blob/master/L2/img10.38.28%20PM.png)
![](https://github.com/micklinISgood/scribenotes/blob/master/L2/11.png)
 * 1-to Many
   * A course has at most one instructor AND users can instruct many courses
![](https://github.com/micklinISgood/scribenotes/blob/master/L2/img10.38.21%20PM.png)
![](https://github.com/micklinISgood/scribenotes/blob/master/L2/1m.png)
 * Many-to-Many
   * A course has many instructors AND users can instruct many courses
![](https://github.com/micklinISgood/scribenotes/blob/master/L2/img10.38.22%20PM.png)
![](https://github.com/micklinISgood/scribenotes/blob/master/L2/mm.png)

* Relationships sets
 * self-loop
   * Users takes different roles in same relationships set 
    * an user can teach many users AND an user can be taught by many users
![](https://github.com/micklinISgood/scribenotes/blob/master/L2/img10.40.48%20PM.png)
 * Relationships sets can have descriptive attributes
   * an user takes a course since when
![](https://github.com/micklinISgood/scribenotes/blob/master/L2/img10.39.31%20PM.png)
 * Participation constraints & Ternary Relationships - Connects three entities
   * A course has at least one user AND at least one assignment
    * An user has at least one course AND at least one assignment
    * An assignment has at least one course AND at least one user
    * Grade not necessarily exists
![](https://github.com/micklinISgood/scribenotes/blob/master/L2/img10.38.45%20PM.png)
 * Participation constraints
   * A course has at least one user
    * A course has exact one instructor
![](https://github.com/micklinISgood/scribenotes/blob/master/L2/img10.38.01%20PM.png)
 * Weak Entities
   * A weak entity can only be uniquely identified by using the primary key of its owner entity
    * Owner and weak entity sets must be in one to many relationship set
    * Weak entity set must have total participation in this identifying relationships set
    * An user has many wall posts AND a wall post belongs to exact one user. 
    * If an user is deleted, his/her posts are deleted. 
![](https://github.com/micklinISgood/scribenotes/blob/master/L2/img10.38.09%20PM.png)

## Quiz
### Diagram an airplane reservation with the following entities: 
### Passenger, Seat

* Answer
 * A passenger can book many seats AND a seat has at most one passenger
![](https://github.com/micklinISgood/scribenotes/blob/master/L2/img10.37.38%20PM.png)
 * A passenger can book at most one seat AND a seat has at most one passenger
![](https://github.com/micklinISgood/scribenotes/blob/master/L2/img10.37.39%20PM.png)


#Entity Relationship Modeling (detailed version)
Entity-Relationship (ER) Modeling is very important in the database. This section first introduces the entity-relationship model as part of database design process. Then addresses the reasons that we should use ER model (such as the complexity of databases or reducing possible inconsistencies and data errors). Finally, this section introduces the ER basic concepts such as Entity, Relationships, Constraints, and so forth.

 * What is Entity-Relationship Model?
 * Why do we need ER Model?
 * Basic concepts and diagrams of ER Model
   * Entity and Entity Set
   * Relationships and Relationships Set
   * Constraints 
   * ISA Hierarchies, Aggregation

##What is Entity-Relationship Model?
For building a new Application, we typically have a few steps, including Requirements, Conceptual Database Design, Logical Design, Schema Refinement and Physical Database Design.
 * Requirements:
   When we build an application, we have a few steps. Typically, you will first consider what are you going to build (functionality, features). 
 * Conceptual Database Design:
Once you have features, you do Conceptual Database Design (What data you are going to store, and what are the relationships between the data). That is where ER model come from.
 * Logical Design:
Formal database schema. Formally implement into the database.
 * Schema Refinement:
Fix potential problems, normalization. To remove possible inconsistencies and errors.
 * Physical Database Design:
The database could work, but maybe slow. This step is to make it fast.

##Why do we need Entity-Relationship Model?
* 1. Database in real world is incredibly complex. 

For example, a typical fortune 100 company has around 10k different information (data) systems, and 90% of them are relational databases (DBMSes). A typical database has >100 tables and one typical table has 50 – 200 attributes. If you don’t design this in a structural or systematic way, you may end up with many tables but don’t know how to deal with it.
For example, wikipedia seems very simple but turns out to have many databases.

* 2. Hope to help reduce the possible inconsistencies and data errors in your database.

For example, in DBLP website, there may be different people with the same name duplicates. If the database does not take it into account, this error may be corrected manually, which is expensive. What if your bank account? Or health insurance.
Google deals with it by telling you the username you chose whether or not has been used by others. 
Databases need to provide guarantees, so that you can build applications on them. 

* 3. It is hard to Design Applications.

For example, it you add a new function to your application such as check_unique(username), you have to make sure all the teams (Web, Mobile, and so on) to add this function. That would be hundreds of thousands of lines of code.

<img src="https://github.com/wyd856570831/ScribeNotes/blob/master/2.1.png" width="350">
<img src="https://github.com/wyd856570831/ScribeNotes/blob/master/2.2.png" width="380">

Ideally, the unique username data guarantee could be enforced in DBMS, because that’s where you store and manage data.
“DRY” principle you should follow in Computer Science. Don’t copy a lot. It may result in many bugs. That's because when you copy code or other stuff from one place to another, if you changed one of them, the other one will not be changed. 

##Basic Concepts for ER Modeling
Basic concepts for ER Modeling includes:
   * Entity and Entity Set
   * Relationships and Relationships Set
   * Constraints 
   * ISA Hierarchies, Aggregation

###Basics for ER Modeling - Entity and Entity Set
   * Entity:
An object that is distinguishable from other objects of the same “type”. For example, when it comes to Users, there is difference between Bob and Alice. Each one would be one entity.
An entity is described as set of attributes and their values.
Domain of an attribute: set of possible values (e.g. integers for data time).
You can think an entity as a record in the database.

   * Entity Set:
A set of all possible entities, each entity is distinguishable with each other. For example, in an Entity Set “courses”, there could not be two same courses, because it violates the definition of “set”. That’s very important.
All entities have same attributes.
You can think entity set as a table that contains all the records.

   * Keys:
How do you distinguish one entity in an entity set. Use a unique key.
A key is the minimal set of attributes that uniquely identify an entity.
      * You may have multiple candidate keys. For example, for entity set “User”, both “uid” and “email” are unique and could be the key.
      * The key may involve multiple attributes. E.g. A class could be identified by both number and section.
      * Primary key: designated unique identifier. Namely, just pick one key from candidate keys, and take it as a primary key.
      * Most entities have a key.

   * An Example:

    <img src="https://github.com/wyd856570831/ScribeNotes/blob/master/3.1.png" width="600">

In this example, keys are underlined.
Attributes should be invariant to time so that your database will not goes wrong in the future. So “age” is a subtle bug in this example.


###Basics for ER Modeling - Relationships and Relationships Set
   * Relationships:
     Association between 2 or more entities. E.g. Alice is taking Intro to Databases.
   * Relationship Set: 
     A collection of all relationships of a particular type. For example,

     <img src="https://github.com/wyd856570831/ScribeNotes/blob/master/3.2.png" width="200">
     
     Rectangle represents entities, and diamond represents relationships. The reason that we don’t just draw a line between two entities is that there could be multiple relationships between these two entities.
In this case, the relationship could be interpreted as users can take courses. How to interpret depends on your application.

     * Users can take different roles in same relationships set. 
    The interpretation in this case, is that users can be teachers or students, so users can teach other users.
You might want to add constrains to this relationship, such as a user cannot teach themselves.

    <img src="https://github.com/wyd856570831/ScribeNotes/blob/master/3.3UsersTeaches.png" width="350">

     * Relationships sets can have descriptive attributes.
    Users could take courses multiple times, so you can add an attribute “since” to track at which time this user took the course.

    <img src="https://github.com/wyd856570831/ScribeNotes/blob/master/3.4SinceRed.png" width="300">

   * Ternary Relationships

    <img src="https://github.com/wyd856570831/ScribeNotes/blob/master/3.5GradedRed.png" width="400">

    Connected three entities. N-array relationships are possible too.
For example, this ternary relationship can be interpreted as “for a course, and a particular assignment, a user gets graded”. Remember, this relationships set just indicates that it is possible that a user could get graded for specific assignments. It does not mean all users must get graded or all assignments must get graded. That is the difference between the set and an entry of the set.


###Basics for ER Modeling - Constraints
Help avoid corruption and inconsistencies. Including:
  * Key constraints
  * Participation constraints
  * Weak entities
  * Overlap and covering constraints

In Specific:
* Key constraints： Key constraints define cardinality requirements on relationships
   * Many to many: a user can take many courses, a course can have many users that take the course.
   * One to Many: e.g., a course has at most one instructor.
   * One to One: e.g., a course has at most one instructor and one instructor has at most one course.
  
    <img src="https://github.com/wyd856570831/ScribeNotes/blob/master/3.6Many.png" width="500">

    We could use arrows to represent these constraints. An arrow means “at most one”, with the direction from the end to the arrow. 1-to-many and 1- to-1 cases are shown as follows.

<img src="https://github.com/wyd856570831/ScribeNotes/blob/master/3.7Arrows.png" width="500">
* Participation Constraints：
    e.g. a course needs at least one instructor. In this case, the participation of “Courses” in “Instructs” is total, otherwise (not thick line), it is a partial participation constraint.
    This constraint can be represented by thick line. 

    ![](https://github.com/wyd856570831/ScribeNotes/blob/master/3.8thick.png)
    <img src="https://github.com/wyd856570831/ScribeNotes/blob/master/3.8thick.png" width="600">

    In this case, the thick line with arrow means each course has exactly one instructor, and the thick line without arrow means each course needs to be taken by at least one student.
Arrows only go inwards to relationship diamond.

* Weak Entities：
    A weak entity can only be uniquely identified by using the primary key of its owner entity.

    
    <img src="https://github.com/wyd856570831/ScribeNotes/blob/master/3.9WeakEntity.png" width="250">


    In this case, if a user was deleted, the WallPosts will also disappear. That is what “weak entity” means. It cannot exist without the owner “Users”.
Owner and weak entity sets must be in 1-to-many relationship set. Weak entity set must have total participation in this identifying relationships set.

* Notation summary:


<img src="https://github.com/wyd856570831/ScribeNotes/blob/master/3.10Notations.png" width="300">


###Basics for ER Modeling - ISA Hierarchies， Aggregation
* ISA (is a) Hierarchies

    Inheritance rules similar to programing languages.
    A ISA B: every A also considered as a B. When querying for Bs, must consider As.
    Why use ISA?
    Add descriptive attributes specific to a subclass. 
    Identify entities that participate in a relationship.
    E.g. If you don’t want some attributes of instructors (a subset of Users) belong to Students (another subset of Users), you need a Hierarchy.

    <img src="https://github.com/wyd856570831/ScribeNotes/blob/master/4.1ISA.png" width="500">

    In this case, an Instructor does not have a grade. A is a B, Instructors is a Users, Students is a Users.

    * Reasons for creating ISA.
      * Grade is specific to Students, and not to Instructors. So you may want to segment them out from Users table.
      * When start to create multiple entities, you might find some of them overlap in their attributes, so you decide to create a parent class.
So in this case, you can see the ISA as a specialization of Users, or a generalization of Instructors and Students.

    * Two constraints of ISA Hierarchies.
      * Overlap Constraint:
For a given entity (User), can it be both an instructor and a student? You can allow or disallow it.
      * Covering Constraint:
For a given entity (User), must it be an instructor or student?  Yes or No. In the case that Users could be other people in the organization, so a User does not have to be Instructor or Student. Thus Covering Constraint does not hold.

    These two constraints can not be expressed in diagram using Syntax for this course. You can come up with your own methods such as shaded or thick.


* Aggregation

    Relationships between entities and relationships. This is the case when you want to have relationship with another relationship.
    For example,

    ![](https://github.com/wyd856570831/ScribeNotes/blob/master/4.2Aggregation.png)
    <img src="https://github.com/wyd856570831/ScribeNotes/blob/master/4.2Aggregation.png" width="500">

    A company can donates a particular Amount to a particular Course.
    If you want to treat this donation as an entity, you can use aggregation.

    ![](https://github.com/wyd856570831/ScribeNotes/blob/master/4.3Aggre.png)
    <img src="https://github.com/wyd856570831/ScribeNotes/blob/master/4.3Aggre.png" width="500">

    Dashed box represents “entity” that represents the entire relationship. When considering Instructors manage donations, the Instructor manage donation (the whole relationship) itself, not Course or Companies. But in terms of implementation, the data of Courses or Companies are stored in themselves.

  * Why use aggregation?
    Aggregation v.s. Ternary Relationships
    ![](https://github.com/wyd856570831/ScribeNotes/blob/master/4.4WhyAggre.png)
    <img src="https://github.com/wyd856570831/ScribeNotes/blob/master/4.4WhyAggre.png" width="500">

    When you want to put constraints on those entities, they apply to all entity sets. For example, If you want courses has exactly one Instructor. But in this case, you put constraints on Companies.
Manages and Donates are separate ideas, you may want to separate them out.


## Using the ER Model

**Entity or Attribute?**
* Is address an attribute of User or an entity connected to Users by a relationship?
* when should you have address as entity set?
  * if address itself has attributes: neighborhoods
  * want to be able to access address as zip code
  * variant over time: users can move at certain time- maybe want to keep track of multiple addresses.
* when should you have address as an attribute?
  * when you want to keep it as a name and not do anything with it
* If you have more than 1 instance of a relationship attribute, then you probably want it as an entity
  * first diagram: ie. if ibm donate to databases, they cannot donate it again because this is a set.
  * second diagram shows that you can now keep track of multiple Donations.

  ![](https://github.com/pyw2102/w4111ScribedNotes/blob/master/ER-Modeling/1donations.png?raw=true)


**Entity or Relationship?**
* Say company say they want to give Columbia money and Columbia gets to choose which courses to allocate the money to
  * Redundancy of amount, need to remember to update everyone
  * Misleading implies amount tied to each donation individually
  ![](https://github.com/pyw2102/w4111ScribedNotes/blob/master/ER-Modeling/2redundancy.png?raw=true)

* Add “Donating Company”, move amount to attribute. What is the interpretation of this approach?
  * Company can donate this amount EVER
  * Issue if company wants to donate multiple time
  ![](https://github.com/pyw2102/w4111ScribedNotes/blob/master/ER-Modeling/3donating_companies.png)


* If company donates once to school for data related courses
  * refactor amount into entity
  * what happens now?
    * donation now is it’s own key with time and amount.
  * Does this seem reasonable?
  * We need to make sure that there is a way for us to tell that the sum of the donations of the courses end up to be the right amount
  ![](https://github.com/pyw2102/w4111ScribedNotes/blob/master/ER-Modeling/4donate_for_data_course.png?raw=true)

**Binary or Ternary Relationships?**
* Example: We have graders, students and assignments.
* Keep in mind: assignment is part of a syllabus, not a specific submission
* the table on the bottom left represents the diamond
![](https://github.com/pyw2102/w4111ScribedNotes/blob/master/ER-Modeling/5graders_students_assignment.png?raw=true)

* Better approach: split it up by  having graders to assignments and students to assignments.
  * Before: we had 2 separate relationships that we accidentally merged into a single one
  ![](https://github.com/pyw2102/w4111ScribedNotes/blob/master/ER-Modeling/6two_relationships.png?raw=true)

* Question: If we want students can only submit assignment once, is that representable?
  * If you put an arrow from assignment to submitted, then it means that student can only submit one from ANY assignments
  * This is not representable by the current diagram.
  ![](https://github.com/pyw2102/w4111ScribedNotes/blob/master/ER-Modeling/7submit_one_from_any.png?raw=true)
* Question: How can we represent students submit multiple times for multiple assignments
  * thick line between students and submitted
 

# Software Tools for ER Modeling
* [ERDPlus](https://erdplus.com)
* [LucidChart](https://lucidchart.com) (Note the free upgrade for educational use)
* [Draw.io](https://www.draw.io)



