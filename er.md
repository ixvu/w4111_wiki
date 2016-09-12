1. L2: Entity-Relationship Modeling(micklinISgood)

# Homework review

![](http://imgur.com/y32u2kN.jpg)

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






 





