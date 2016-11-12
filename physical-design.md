
# Lec 18 Physical Design
##- Query Performance I: Disk Storage and Indexing
* Why do we care about hardware?
* What properties that really matter for design the rest of the system?

##Work from bottom up
### Why Disk is important? 
1. Disk is the cheapest per Gigabyte Storage mechanism in the market
2. The process of analyzing and optimizing disk is the same process you might go through for optimizing any others

-(graph)

- Disk space manager : The lowest layer of the DBMS software manage space on disk, where the data is stored. Disk space manager supports the concept of a page as a unit data, and provide commands to allocate or deallocate a page and read or write a page. Higher layers allocate, deallocate, read, and write pages through (routines provided by) this layer.(p231)
- Buffer Manager: the software layer that is responsible for bringing pages from disk to main memory as needed. (p232)

## $ Matters 
 Data is literally driven by money.

### Why not store all in RAM?
1. Cost too much:
High-end Databases today store Petabyte (1000TB) of data
60% of the cost of operating a database is in supplying, managing and maintaining disks.
2.  Main Memory not persistent; It is important if DB stops or crashes. 

- Example: If you spend $ 1000 on hardware, you can get 64 - 96 GB for RAM, 400 - 1000 GB for SSD, 24000 for Disk. 
You can spend some money on RAM for active data, Disk for main database, secondary storage, and Tapes for archive.
- What does this mean? 
 You can prioritize where your money goes to- ram, disk, etc.

### What ends up with is following architecture:

-(graph)

- You will have most of your storage capacity on disk, because it’s cheap.
- As you go up the memory hierarchy, things become more expensive but faster. 
- Register is a piece of memory that is accessible in CPU. (Ridiculously expensive)
- You can purchase RAM with reasonable prices.
- The faster you go up the less storage space you have. You need to optimize the rest of the system. 

### Interesting numbers:
1. The cost of compression 1k bytes is 3000 ns. This means if you can compress your data sufficiently, that is more worthwhile to do than storing the uncompressed version in disk. It is faster to decompress the memory than to fetch the uncompressed version from disk.
2. If you run a data center, the cost of access data in memory from another machine is .5 ms, which is significantly cheaper than disk.

- The above two examples of some of the things that influence design decisions. What does this mean? If you can afford multiple machines with lots of RAM, it makes sense to use Disk for other things you never access, and put everything on other machines. 
- Jim Gray found the idea of transactions, recovery, and other core databases ideas. Below is a storage hierarchy analogy. Going to memory/RAM is like driving to Philly and then coming back. Going to Disk is like going to Pluto and back. (“You might as well give up on Tape.”- Prof Wu.)It is important to minimize the time you need to access data.

-(graph)

##  What is a disk? How a disk work?
- Think of the disk as a very fast DVD or record player. Stack a bunch of the round disks on top of each other. The tip of the head will know how to read and write whatever is underneath it. This is how DVD and record players work. Each of the rings correspond to a track and it stores data. Each track is split into segments called Sector. 

-(graph)

- This a representation of hard drive. The tip of Head know how to read and write what ever underneath it. Each of the ring is a track, which stores data. Each track is separated into several sector.

### Interesting properties:
1. The size of the sector is determined by the rotational angle rather than a fixed length. So the sector far away from center will be larger than the sector near the center. You can read more data outside than inside for a given amount of time.
2. How much data you can read depends on the spin speed. You want to maximize the RPM.
3. If you want to move the arm in/out to read some data, there will be a seek cost (moving the arm to access data). It dominates by far the cost of accessing data.

-(graph)

- Problem is the first 2 delays if you care about speed (the latency in which to get the first bit of data to read). We want to optimize seek and the rotational delays. Seeks are expensive, but reading things that is right underneath the head is very fast. This is called sequential access.
- Seek cost: the cost for moving arm.
- Seek time : the average time to move arm
- Rotational delay: the data you want is not directly underneath the reader, so you need to wait until the disk rotates until you can actually read that thing.
- The key thing here is to reduce seek and rotational delays: HW & SW approaches. 

## Pre-fetching
We want to optimize the amount of data we can sequentially access. Disk drive can do pre-fetching. If you are sequentially reading some data in a file that store sequentially on disk, then disk driver can just start reading ahead. E.g. doing data processing and pause it, next time you read the next data, it will already be there because it has already fetched it for you. 

## Graph of read cost for random vs. sequential access
If you are doing random access (randomly placed in storage device), how many can you read per sec? 316 values/sec
If you look at memory, you will see that there is higher throughput. 
Random access between memory and disk is pretty much on par. 

-(graph)

## Pragmatics of Databases
- Most databases are pretty small
- All global daily weather since 1929:20GB
- 2000 US Census: 200GB
- 2009 english wikipedia: 14GB (you can fit this in your laptop!)
- You don’t have to have Google infrastructure to do interesting things. 

## What is the API between data base system and disk?
- API is centered around a page, a fixed size block of data. This is the unit we pass around. We want to amortize the cost of having to move that arm. 

- If page you read from disk too small: dominated by moving arm around
- If page is too big (ie. 1 GB in size): read a huge amount of data but only access a small amount inside of it. Typically Operating system will pick a page size between 4kb and 64kb in size to balance out moving the arm and reading more data than you ask for. 

- The data we store is in terms of pages. 
- On disk, we store a file that represents Customers Table. We split and store Customers Table as pages. 

-(graph)

## 

