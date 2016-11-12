
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

### Interesting numbers:
- 1: The cost of compression 1k bytes is 3000 ns. This means if you can compress your data sufficiently, that is more worthwhile to do than storing the uncompressed version in disk. It is faster to decompress the memory than to fetch the uncompressed version from disk.
- 2: If you run a data center, the cost of access data in memory from another machine is .5 ms, which is significantly cheaper than disk.
- The above two examples of some of the things that influence design decisions. What does this mean? If you can afford multiple machines with lots of RAM, it makes sense to use Disk for other things you never access, and put everything on other machines. 

- Jim Gray found the idea of transactions, recovery, and other core databases ideas. Below is a storage hierarchy analogy. Going to memory/RAM is like driving to Philly and then coming back. Going to Disk is like going to Pluto and back. (“You might as well give up on Tape.”- Prof Wu.)

-(graph)


