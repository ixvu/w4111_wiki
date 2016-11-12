
# Lec 18 Physical Design
##- Query Performance I: Disk Storage and Indexing
* Why do we care about hardware?
* What properties that really matter for design the rest of the system?

##Work from bottom up
### Why Disk is important? 
- 1: Disk is the cheapest per Gigabyte Storage mechanism in the market
- 2: The process of analyzing and optimizing disk is the same process you might go through for optimizing any others

-(graph)

- Disk space manager : The lowest layer of the DBMS software manage space on disk, where the data is stored. Disk space manager supports the concept of a page as a unit data, and provide commands to allocate or deallocate a page and read or write a page. Higher layers allocate, deallocate, read, and write pages through (routines provided by) this layer.(p231)
- Buffer Manager: the software layer that is responsible for bringing pages from disk to main memory as needed. (p232)

## $ Matters 
- Data is literally driven by money.
### Why not store all in RAM?
- 1: Cost too much:
- High-end Databases today store Petabyte (1000TB) of data
- 60% of the cost of operating a database is in supplying, managing and maintaining disks.
- 2: Main Memory not persistent; It is important if DB stops or crashes. 

- Example: If you spend $ 1000 on hardware, you can get 64 - 96 GB for RAM, 400 - 1000 GB for SSD, 24000 for Disk. 
You can spend some money on RAM for active data, Disk for main database, secondary storage, and Tapes for archive.
- What does this mean? 
- You can prioritize where your money goes to- ram, disk, etc.

### What ends up with is following architecture:

-(graph)

- You will have most of your storage capacity on disk, because it’s cheap.
- As you go up the memory hierarchy, things become more expensive but faster. 
- Register is a piece of memory that is accessible in CPU. (Ridiculously expensive)
- You can purchase RAM with reasonable prices.
- The faster you go up the less storage space you have. You need to optimize the rest of the system. 



