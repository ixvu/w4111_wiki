
# Lec 18 Physical Design
##- Query Performance I: Disk Storage and Indexing
* Why do we care about hardware?
* What properties that really matter for design the rest of the system?

##Work from bottom up
### Why Disk is important? 
- 1. Disk is the cheapest per Gigabyte Storage mechanism in the market
- 2. The process of analyzing and optimizing disk is the same process you might go through for optimizing any others



- Disk space manager : The lowest layer of the DBMS software manage space on disk, where the data is stored. Disk space manager supports the concept of a page as a unit data, and provide commands to allocate or deallocate a page and read or write a page. Higher layers allocate, deallocate, read, and write pages through (routines provided by) this layer.(p231)
-Buffer Manager: the software layer that is responsible for bringing pages from disk to main memory as needed. (p232)


