#Lecture 3:

Lecture 3 continued with the discussion on Entity Relationships and introduced several additional key topics encountered in database design.  The key points discussed were:
* ISA (is a) Hierarchies
* Aggregation
* Using the Entity-Relationship Model

##ISA (is a) Hierarchies

Those familiar with an object oriented language such as C++ or Java will see that ISA Hierarchies are related to what is known in those languages as "inheritance."

For example, consider the concept of a user.  You wouldn't necessarily want to only represent a user in a database or application, you would probably want to have different types of users that each have their independent set of attributes and relationships that you would want to capture.  For example, as the figure shows, it might not make sense to represent all of  the instructors and student attributes in the generic users table or "Users" entity because there are potentially some things that are specific to Instructors that you don't want students to have access to.  Additionally, there are probably some things specific to students that do not apply to instructors.  For example, the "Rating" attribute shown for Instructors and the "Grade" attribute shown for Students are examples.  The correct way to refer to the following figure would be to say that "A is a B" or "A is a type of B" meaning that Instructors are a type of Users or a Student are a type of Users.

##__INSERT ISA DIAGRAM HERE__

ISA Hierarchies are useful in a variety of situations.  For example:
*  If you want to segment a particular attribute to a specific table.  For example, the "Grade" attribute is not an applicable attribute to Instructors , so the ISA Hierarchies enables us to segment the Grade attribute out of the Users table to the Student table.
*  Additionally, if during your database design you start creating multiple entities and then realize that they overlap by the set of attributes by which they are defined.  In this situation, you might decide to create a parent class and use the ISA Hierarchy to show this inheritance relationship.

On top of ISA Hierarchies, there are two types of constraints that are potentially useful.

* Overlap Constraint - for a given entity - or in case of the ISA diagram, for a given "User", can they be both Instructors and Students, or must they be only one or the other?  This type of constraint can either be allowed or disallowed.

* Covering Constraint - in the case of the ISA diagram, one might ask, do all Users have to be either an Instructor or a Student?  If this is required, this is referred to as forcing the Covering Constraint.  If you don't care, and Users do not have to be an Instructor or a Student, but could for example be part of the Administrative group of employees, you would say that the Covering Constraint does not hold.

Depicting Overlap and Covering Constraints are not easily captured or represented in the Entity Relationship Diagram.  Instead, they must be documented in text or supporting documentation for your design.

##Aggregation

Aggregation is used in instances when you want to represent Relationships between Relationships.  Where are these useful?  For example, as shown in the following diagram:

##__INSERT DONATES TUPLE DIAGRAM HERE__

This diagram shows two Entities; "Companies" and "Courses," that are connected by the "Donates" Relationship.  This relationship shows that Companies, can Donate a specific Amount, to particular Courses.  For example, IBM could donate to COMS 4111.

What kinds of Donations, or Relations, would we not be able to do?  As the diagram shows there is some information, about the donation, that we might want to capture - namely, in this case, the "Amount" of the Donation.  In some cases, where there are attributes associated with a Relationship - you might find a need to represent Relationships as Entities.  In this case, you would want to treat the entire Relationship as an Entity - this is called __Aggregation__.

##__INSERT DONATES AGGREGATION DIAGRAM HERE__

Notice that the previously shown "Donates Tuple Relationship" is now surrounded by a dashed line - depicting an Aggregation.  This abstraction allows us to now treat a Relationship Set like an Entity Set.
The correct way to interpret this diagram is to say that Instructors can "Manage" specific types of "Donations."  It is important to note that both the Companies and Courses Entity in the Aggregation are still treated as Entities in a Relationship Set.  Also, it is important to understand, that the Relationship between the Instructors that are Managing the Donations, is on the Donation itself, it is not on the Company or on the Course.
For example,  you might consider having an alternative way to depict the previous Relationships.

##__INSERT AGGREGATION VERSES TERNARY RELATIONSHIP DIAGRAM HERE__

In this diagram, Instructors, Companies and Courses have the Ternary relationship shown.  What are some potential issues with this design?  
What if there was a requirement, for example to impose the constraint that a Companies Donation could be managed by at most one Instructor?  How would we depict this in such a Relationship?
If we for example, placed the "at most one" arrow from Instructors to the Manages/Donates Relationship, would this correctly implement our restriction?  This could be misinterpreted as, "Instructors have at most one Company that can donate", or alternatively, "Instructors can have at most one Course they can Manage" because the "at most one" arrow is now imposed on all Entities on the Relationship.  This is a common consequence of placing constraints on one Entity in a Ternary Relationship - the constraint can cause an unintended side affect on the other Entities in the Relationship Set.
Another example that shows imposing a "Exactly One" constraint on a Ternary Relationship is shown below:

##__INSERT AGGREGATION VERSES TERNARY RELATIONSHIP WITH COURSES CONSTRAINT DIAGRAM HERE__

This diagram depicts that "A Course can have exactly one Instructor."  In this situation, you have potentially placed unintended constraints on the "Companies" Entity.  

As noted, when using constraints in a Ternary or N-ary Relationship, the constraint can have an unintentional side effect on other Entities in a Relationship Set.  In cases where there are potentially unintentional side effects, Aggregation is often used.
For example, if you added a constraint on the Courses or Companies Entity in the below diagram, it wouldn't inadvertently carry over into the Instructors Relationship.  In addition, if you wanted to say that each donation "Donates Relationship" can be managed "Manages Relationship" by at most one Instructor, you could modify the line connecting the "Manages" and "Donates" Relationships with an arrow and it doesn't inadvertently affect the Entity Relationships in the Aggregation portion of the diagram.

##__INSERT DONATES AGGREGATION DIAGRAM HERE - potentially with modified line?__

##Entity-Relationship Model

###ER Definition Review

* Entity: An "object" that is distinguishable from other objects
* Attribute: Information that describes the entity
* Attribute domain: Range of permissible values
* Entity Set: Collection of entities with the same attributes
* Relationship: Association between Entities
* Relationship Set: Collection of similar relationships

###Keys

Keys are the minimal set of attributes that uniquely identify an Entity.  Most Entities have a key.  The exception is a "Weak Entity." In an Entity, there might be multiple candidate keys.  For example, both a "uid" and "email" might be unique to a particular Entity.  In additon, a key might involve multiple attributes.  For example, a class might be identified by both a number and a section.  Primary keys are considered the most important type of "key" and are chosen from the set of candidate keys and underlined in Entity diagrams.