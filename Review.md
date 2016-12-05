# Review Topic 1: Serializable
We will show how to check serializable and conflict serializable for the examples. And we will give a not conflict serializable but serializable example.
## Example:
* T1: `R(A)` `W(A)`&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;`W(A)`
* T2: &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;`W(A)``R(A)`&emsp;


## Serializable? NO

Recall definition of **Serializable**: 

- If execute the schedule the end state of the database is equivalent as some serial schedule.

For this example the end result should be equivalent to either
 
(T1,T2) 
* T1:`R(A)``W(A)``W(A)`
* T2: &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;`W(A)``R(A)`

(T2,T1) 
* T2: `W(A)``R(A)`
* T1: &emsp;&emsp;&emsp;&emsp;&emsp;`R(A)``W(A)``W(A)`

## Check It with Numbers:

Define `T1.W(A): A+1` `T2.W(A): A=10`

### Original:

* T1: `R(A=0)``W(A=1+0)`&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;`W(A=1+0)`
* T2: &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;`W(A=10)``R(A=10)`

Outcome A=1

*Note that T2.W(A) is called blind write since it is writing without reading.

### T1,T2:
* T1:`R(A=10)``W(A=1+10)``W(A=1+10)`
* T2: &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;`W(A=10)``R(A=10)`

Outcome A=10


### T2,T1:
* T1: &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;`R(A=10)``W(A=1+10)``W(A=1+10)`
* T2: `W(A=10)``R(A=10)`

Outcome A=11

### Original is different from T1,T2 and T2,T1, therefore it is not serializable

## Conflict Serializable? NO

Since there is a dependency cycle between T1 and T2, then it is not conflict serializable.  The cycle is from operation T1.W(A)<sub>1</sub> and T2.W(A) being in conflict, and operations T2.R(A) and T2.W(A)<sub>2</sub>  being in conflict.

* T1: `R(A)``W(A)`<sub>1</sub> &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;`W(A)`<sub>2</sub>
* T2: &emsp;&emsp;&emsp;&emsp;&emsp;`⇘``W(A)``R(A)``⇗`

## A Not Conflict Serializable But Serializable Example
* T1: `W(A)`<sub>1</sub>&emsp;&emsp;&emsp;&emsp;&emsp;`W(A)`<sub>2</sub>
* T2: &emsp;&emsp;&emsp;`⇘``W(A)``⇗`

Since there is a dependency cycle between T1 and T2, then it is not conflict serializable. The cycle is from operation T1.W(A)<sub>1</sub> and T2.R(A) being in conflict, and operations T2.R(A) and T2.W(A)<sub>2</sub> being in conflict.

However it is serializable. Since it is equivalent to T2,T1.
 
## A Summary of Serial sets
* Serial ⊆ Conflict Serializable ⊆ Serializable




# Review Topic 2: Access Costs
In this example, we will cover access costs computing for a table in primary/secondary, candidate key/not candidate key, π<sub>id</sub>, and overflow page cases.
## Setting:
* Fill factor: 50%
* DE size: 10 bytes
* Tuple size: 50 bytes
* Page Size: 500 bytes
* T1: 20,000 tuples
* Primary B+ INDEX T1(id)
* 𝜎<sub>id=10</sub>(T1) id is key

## How many pages we need to store T1? 4,000
We first need to compute how many tuples one page can contain. Note that page size is 500 bytes with 50% fill factor, and tuple size is 50 bytes. Therefore the amount of tuples one page can contain is
* 500/2/50=5 tuple/page

We have 20,000 tuples in total, so we need
* 20k/5=4,000 pages

If we do not use index, then on average we need half of it which is 2,000.

We can say T1 = 20,000 tuples ≅ 4,000 pages

## Index Case: 4
We first need to find fan out to help us get the height of the tree. The size of index is DE size: 10 bytes and the page size is 500 bytes with 50% fill factor. Therefore
* Fan out: 500/2/10=25 index/page

Then we can use fan out to get the Height of tree
* Height of tree=⌈log<sub>25</sub>4,000⌉=3

The access cost will be the height plus one more leaf page:
* Cost= h+1(leaf page)=4
 
## Id Is Not Key Case: 203
We need to know selectivity, the default selectivity is 5%.

Note that the selectivity is defined as the number of output records / number of input records. The reason why we can use the selectivity also for page calculations is because the leaf pages are sorted (which is assumed by Primary B+ INDEX). If they were not, then it could be one page per tuple.

We first compute how much leaf pages we need to read, note at above we computed that we need 4,000 pages in total. Thus 5% of it is:
*  Data pages=4,000*0.05=200 pages

The access cost will be the height plus the amount of leaf pages we need to read:
* cost=h+200=203


## Secondary Index Case: 5
At above we computed fan out is 25 and we have 20,000 tuples in total.
* Leaf pages: 20,000/25=800

Therefore the new height of tree is:
* new height of tree=⌈log<sub>25</sub>800⌉=3 (this is different from the previous one even they are both 3) 

The access cost will be the height plus one more leaf page and since we are in secondary case we need one more page on disk:
* Cost = 3(new height)+1(leaf page)+1(disk)=5


## π<sub>id</sub>𝜎<sub>id=10</sub>(T1) Case: 4
The difference between this problem and the previous one is that since we are only looking for the id which is the index we do not need the information from disk. Thus
* Cost = 3(height)+1(leaf page)=4

## Secondary B+-tree 𝜎<sub>id=10</sub>(T1) and 10 matched tuple: 14
Since DE/page = 25, it's reasonable to assume that the directory of these 10 matched page are stored in a single leaf page.
* Cost = 3(height)+1(leaf page)+10(disk)=14

## Secondary B+-tree π<sub>id</sub>𝜎<sub>id=10</sub>(T1) and 10 matched tuple: 4
Since we don't need to get the real data tuple
* Cost = 3(height)+1(leaf page) = 4

## Primary Hash Index Case
𝜎<sub>id=10</sub>(T1)

1. If id is not the key and there is no overflow page
    Cost = 1

2. If id is not the key and on average there are 10 overflow pages
    Cost = 10

## Secondary Hash Index Case
𝜎<sub>id=10</sub>(T1)

1. If 5 matched tuple and there is no overflow page
    Cost = 1 + 5 = 6

2. If 5 matched tuple and on average there are 10 overflow pages
    Cost = 10 + 5 = 15