
# Lec 18 Physical Design
## Query Performance I: Disk Storage and Indexing
* Why do we care about hardware?
* What properties that really matter for design the rest of the system?

##Work from bottom up
### Why Disk is important? 
1. Disk is the cheapest per Gigabyte Storage mechanism in the market
2. The process of analyzing and optimizing disk is the same process you might go through for optimizing any others

![](https://github.com/pyw2102/w4111ScribedNotes/blob/master/Physical-Design/disk.png?raw=true)

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

![](https://github.com/pyw2102/w4111ScribedNotes/blob/master/Physical-Design/layers.png?raw=true)

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

![](https://github.com/pyw2102/w4111ScribedNotes/blob/master/Physical-Design/jimgray.png?raw=true)

##  What is a disk? How a disk work?
- Think of the disk as a very fast DVD or record player. Stack a bunch of the round disks on top of each other. The tip of the head will know how to read and write whatever is underneath it. This is how DVD and record players work. Each of the rings correspond to a track and it stores data. Each track is split into segments called Sector. 

![](https://github.com/pyw2102/w4111ScribedNotes/blob/master/Physical-Design/diskcomponents.png?raw=true)

- This a representation of hard drive. The tip of Head know how to read and write what ever underneath it. Each of the ring is a track, which stores data. Each track is separated into several sector.

### Interesting properties:
1. The size of the sector is determined by the rotational angle rather than a fixed length. So the sector far away from center will be larger than the sector near the center. You can read more data outside than inside for a given amount of time.
2. How much data you can read depends on the spin speed. You want to maximize the RPM.
3. If you want to move the arm in/out to read some data, there will be a seek cost (moving the arm to access data). It dominates by far the cost of accessing data.

![](https://github.com/pyw2102/w4111ScribedNotes/blob/master/Physical-Design/timetoaccess.png?raw=true)

- Problem is the first 2 delays if you care about speed (the latency in which to get the first bit of data to read). We want to optimize seek and the rotational delays. Seeks are expensive, but reading things that is right underneath the head is very fast. This is called sequential access.
- Sequential access: access to a computer data file that requires the user to read through the file from the beginning in the order in which it is stored.
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

![](https://github.com/pyw2102/w4111ScribedNotes/blob/master/Physical-Design/valuereadpersec.png?raw=true)

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

![](https://github.com/pyw2102/w4111ScribedNotes/blob/master/Physical-Design/table.png?raw=true)

## What is a page?
- Unit of transfer between storage and database
- Typically fixed size
- Small enough for one I/O to be fast
- Big enough to not be wasteful
- Usually a multiple of 4 kB 
- Intel virtual memory hardware page size
- Modern disk sector size (minimum I/O size)

### Default page sizes in DBs
Note: Typically multiple of 4 kBs
* SQLite: 1kB
* IBM DB2: 4kB
* Postgres: 8kB
* SQL Server: 8kB
* MySQL: 16kB
* MongoDB: 32kB

### Disk Space Interface
Below is the API. There are 4 ways of access things.

DiskInterface: API, four ways of accessing things
* `readPage(page_id): data` give it a page id, database will translate that into a position on the disk drive
* `writePage(page_id, data)` do the same thing as readPage, but also provide a data
* `newPage():page_id` allocate space on disk, give me the `page_id` of that 
* `freePage(page_id)` I am done with this page, clears up space, so it can be reused.


### Record, Page and File Abstractions
* File is a collection of pages for which if you collect all the pages together, you get all the data. 
* Everything is going to be stored as files. 
* A page is  “collection” of records because we can’t keep track of the order. Keeping track of the order is not needed as a guarantee. However, it is useful to have sorted pages to be able to perform binary search.
* Record: "application" storage unit
  * e.g. a row in a table
* Page: Collection of records
* File: Collection of pages
  * insert/delete/modify record
  * get(record_id) a record
  * scan all records
* May be in multiple OS files spanning multiple disks

### Units that we’ll care about
- B # data pages on disk for relation
- R # records per data age (we talk about it in terms of records, not the physical size of page) 
- D avg time to read/write data page to/from disk. 

- I.e. if i have 100 records per page, and 100 pages in a table, then we’ll have roughly 10,000 records in that particular table. 
- We ignore sequential access for now. It will be added back in later on.

## Different ways to store a table
- Criteria: accessing, deleting, inserting data
- Unordered Heap Files
- Unordered collection of records
- Pages allocated as collection grows
- Need to track:
 - Pages in file
 - Free space on pages
 - Records on pages (which pages are free, which pages are full, etc)
 - Example: let's say you have a table with attributes (a int, b int).  Then you might store (say) 1000 (a, b) value pairs in one page.  One question is how you store them within a page --- sorted?  unsorted?  A second question is how to organize the pages in the table.  You may not be guaranteed there's contiguous sectors on the disk drive for all of the pages so some pages won't be next to each other.  So they will need to be able to "point" to their neighboring pages.  One way is to organize the pages as a linked list.

### A data page is just a bunch of records + two pointers 
- Each data page as a pointer to the next and previous data page
- If we want to read and write data, we will start at the first page, and read on until the record I care about. 
- Since this is unordered, we have to read all the pages. This is the dumbest way of representing it.
- Smarter way: keep track of which pages have full data and which pages have free space. (as shown in the image below).

![](https://github.com/pyw2102/w4111ScribedNotes/blob/master/Physical-Design/heapFile.png?raw=true)

### What might be a smarter way of doing this? 
- We can have pointers to all pages. Instead of single header page, we use a directory. 
- You keep track of two numbers: the data page it points to, the amount of free space. 
- Small constant cost to reading the directory to find which pages are free if you want to insert data
- If you want to read data, this way still requires you to read everything. 
- Even smarter: we can ensure the pages are sorted. In order to do that, the directory also has to keep track of the minimum and maximum value of all the data in each page. This will allow us to do binary search faster. Slightly faster if we start storing more information in the header page. 

![](https://github.com/pyw2102/w4111ScribedNotes/blob/master/Physical-Design/directory.png?raw=true)

## Indexes
If you spend a lot of time building an index so you can access your data way faster, that will be much faster than naively executing a query. 
###To enhance the visiting and search speed, the traditional idea that we set a head page and set pointers between each page is not a good choice. As a alternative we use the idea of directory. The idea is like we use some extra space to store the page location information, although we might need some extra space and time to store and maintain it, the directory can extremely enhance the search page. Considering this case, we use indexes to implement this idea.
###Index is defined for a search key and is different than a candidate key (For candidate key, it can be used to label an unique turple; however, index is used for searching issue) In SQL, you can the syntax:"CREATE INDEX [idx1] ON users USING btree (sid)" to assign an index for the table directly. Commonly, if you do not assign an index, the default is CREATE INDEX ON TABLE USING btree (primary key) in PostgreSQL
###High level index structure: commonly, it includes index entries and data entries. In the primary index structure, the data entries is the data record and we can directly access the turple using it; however, in secondary index structure, the data entries has the following format <search key value, rid> and we have another layer called data records. The actual record is stored in that layer.
###B+ tree Index: Each node of it represent for a page. It is self-balanced, therefore, it can keep a good search performance. Besides, leaf nodes are connected as the following picture showed. This data structure help to actualize the disk optimition.
![1](https://github.com/JisongLiu/Gym-/blob/master/1.png)
![2](https://github.com/JisongLiu/Gym-/blob/master/2.png)
The number of B+ tree should that we can store the top levels in memory. Although we only store a little data in memory, we can find the other data entries easily by using only the data in height 2 and height 3 data.
###Hash Index for example. In this type of index, hash function is used to determine which hash bucket stores the page we want to search. Similarly, when we want to insert some pages, hash function is used to determine which hash bucket we should use to store this page. However, when the data size enlarge, there will be more possibility to meet the collision when insert a page and will induce the reduce of performance.
##Costs for each type
###The operatations we care about is scan everything, equality, range, insert record and delete record.
###The following image shows the time complexity for each operator by using different data structure.
![3](https://github.com/JisongLiu/Gym-/blob/master/3.png)