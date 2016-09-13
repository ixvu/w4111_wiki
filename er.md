1. L2: Entity-Relationship Modeling(micklinISgood)

# Homework review

![](https://github.com/micklinISgood/scribenotes/blob/master/L2/img1.png)

Note:
target = "single malt scotch"

target in el.lower() will match if there is a substring in el.lower() matches target.

modifying to **target == el.lower()** will get the correct result.



# Lecture 2 Entity-Relationship Model
## Inconsistencies/ConstraintViolations

* duplicate attribute: 
 
  1. Search a author name on DBLP, the site for computer science publications. You may find different authors with the same name, which is not unique. Therefore, we call this as Inconsistencies.
  2. Error may happen if two people own the same bank account.
  3. Error may happen if two people own the same facebook id. Sending messages to wrong friends, etc.
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
   * flight type + flight date makes a flight unique, ie can be specifically identified
 * surrogate key : a manually constructed key to identify a row
   * flight No., ie flight num
* non-unique
 * foreign key

## Entity graph representation
![](https://github.com/micklinISgood/scribenotes/blob/master/L2/img7.37.50%20PM.png)

Keys are underlined

Age is not good because age may vary with time. Use birthday instead.

## Relationships
* Symbol definition
![](https://github.com/micklinISgood/scribenotes/blob/master/L2/img10.37.51%20PM.png)
* Relationship
 * 1-to-1
 * A course has at most one instructor AND users can instruct at most one course
![](https://github.com/micklinISgood/scribenotes/blob/master/L2/img10.38.28%20PM.png)![](https://github.com/micklinISgood/scribenotes/blob/master/L2/11.png)
 * 1-to Many

![](https://github.com/micklinISgood/scribenotes/blob/master/L2/1m.png)
 * Many-to-Many

![](https://github.com/micklinISgood/scribenotes/blob/master/L2/mm.png)
* Relationships sets




 





