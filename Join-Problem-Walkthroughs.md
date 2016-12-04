# Join Problem Walkthroughs
<details> 
  <summary>Example 1</summary><br />  
|Fill Factor|Dir. Entry Size|Tuple Size|Page Size|    
|:---------:|:-------------:|:--------:|:-------:|  
|     0.5   |    5 bytes    | 100 bytes|400 bytes|

### Relations
| Attribute\Relation |T<sub>1</sub>|T<sub>2</sub>|T<sub>3</sub>|T<sub>4</sub>|   
|-------------------:|:--------:|:--------:|:--------:|:--------:|
|       Tuples       |  2000    |  20000   |    20    |  200000  |  
| Primary Hash Index |          |     ✓    |          |          |  
|Secondary Hash Index|     ✓    |          |          |          |  
| Primary Tree Index |          |          |    ✓     |     ✓    |  
|Secondary Tree Index|          |     ✓    |          |     ✓    |  
</details>
### Preparation Steps
<details> 
  <summary>1. Compute the Directory Entries Per Page</summary><br />
   1.  floor(<sup>Page Size * Fill Factor</sup>&frasl;<sub>Dir Entry Size</sub>)  
   2.  floor(<sup>400 * 0.5</sup>&frasl;<sub>5</sub>) 
   3.  40 <sup>DEs</sup>&frasl;<sub>Page</sub>
   
|Fill Factor|Dir. Entry Size|Tuple Size|Page Size|DE/Page|    
|:---------:|:-------------:|:--------:|:-------:|:-----:|     
|     0.5   |    5 bytes    | 100 bytes|400 bytes|  40   |  
</details>

<details> 
  <summary>2. Compute the Tuples Per Page</summary><br />
   1.  floor(<sup>Page Size * Fill Factor</sup>&frasl;<sub>Tuple Size</sub>) 
   2.  floor(<sup>400 * 0.5</sup>&frasl;<sub>100</sub>)  
   3.  2 <sup>Tups</sup>&frasl;<sub>Page</sub>

|Fill Factor|Dir. Entry Size|Tuple Size|Page Size|DE/Page|Tups/Page|    
|:---------:|:-------------:|:--------:|:-------:|:-----:|:-------:|   
|     0.5   |    5 bytes    | 100 bytes|400 bytes|  40   |    2    |  
</details>


<details> 
  <summary>3. Compute the Page Sizes for each of the relations T1 thru T4</summary><br />
Note: we need to end up in units of pages!
   1.  T<sub>1</sub> = <sup>2000 Tuples</sup>&frasl;<sub>2 Tuples/Page</sub> = 1000 Pages  
   1.  T<sub>2</sub> = <sup>20000 Tuples</sup>&frasl;<sub>2 Tuples/Page</sub> = 10000 Pages  
   1.  T<sub>3</sub> = <sup>20 Tuples</sup>&frasl;<sub>2 Tuples/Page</sub> = 10 Pages  
   1.  T<sub>4</sub> = <sup>200000 Tuples</sup>&frasl;<sub>2 Tuples/Page</sub> = 100000 Pages  

| Attribute\Relation |T<sub>1</sub>|T<sub>2</sub>|T<sub>3</sub>|T<sub>4</sub>|        
|-------------------:|:--------:|:--------:|:--------:|:--------:|
|       Tuples       |  2000    |  20000   |    20    |  200000  | 
|       Pages        |  1000    |  10000   |    10    |  100000  |  
</details>


<details> 
  <summary>4. Compute the Lookup Costs for each of the indexes relations T1 thru T4</summary><br />
Note: Costs is to lookup 1 tuple in page units!
1.  Equations
    1. Hash Index Cost = 1
    2. Primary Tree Index Cost = ceiling(log<sub>DE&frasl;Page</sub>(# of Relation Pages)) + 1
    3. Secondary Tree Index Cost = ceiling(log<sub>DE&frasl;Page</sub>(<sup># of Relation Tuples</sup>&frasl;<sub>DE/Page</sub>)) + 2
    4. Nested Loop = M + T*M\*N or M+T\*M\*C
    5. Hash Join Cost = M + N + (T\*M)(1)
2.  T1: Primary Tree Index = 3
    1.  ceiling(Log<sub>40</sub>(1000)) + 1 = ceiling(1.872) + 1 = 2 + 1 = 3 
3.  T2: Primary Hash Index = 1  
3.  T2: Secondary Tree Index = 4
    1.  ceiling(Log<sub>40</sub>(<sub>20000</sub>&frasl;<sub>40</sub>)) + 2 = 
    2.  ceiling(Log<sub>40</sub>(500)) + 1 = 
    3.  ceiling(1.68) + 2 = 2 + 2 = 4 
4.  T3: Primary Tree Index = 2
    1.  ceiling(Log<sub>40</sub>(10)) + 1 = ceiling(.624) + 1 = 1 + 1 = 2  
4.  T4: Primary Tree Index = 5
    1.  ceiling(Log<sub>40</sub>(100000)) + 1 = ceiling(3.12) + 1 = 4 + 1 = 5   
4.  T4: Secondary Tree Index = 5
    1.  ceiling(Log<sub>40</sub>(<sub>20000</sub>&frasl;<sub>40</sub>)) + 2 = 
    2.  ceiling(Log<sub>40</sub>(5000)) + 2 = 
    3.  ceiling(2.3) + 2 = 3 + 2 = 5  

| Attribute\Relation |T<sub>1</sub>|T<sub>2</sub>|T<sub>3</sub>|T<sub>4</sub>|      
|-------------------:|:--------:|:--------:|:--------:|:--------:|
|       Tuples       |  2000    |  20000   |    20    |  200000  | 
|       Pages        |  1000    |  10000   |    10    |  100000  |  
</details>
      
### Solution Steps

<details> 
  <summary>1. Find the Relation with the least number of tuples</summary><br />
  T<sub>3</sub> &lt; T<sub>1</sub> &lt; T<sub>2</sub> &lt; T<sub>4</sub>
</details>


<details> 
  <summary>2. Compare the cost of of joining against the other</summary><br />

| T<sub>3</sub> ⋈<sub>attr</sub> T<sub>X</sub> Cost   |    T<sub>1</sub>    |    T<sub>2</sub>    |    T<sub>4</sub>    |   
|-------------------------------:|:--------:|:--------:|:--------:| 
| Hash Inner Nested Loop         |     ✖    |10 + 20(1)|    ✖     |  
|Primary BTree Inner Nested Loop |10 + 20(3)|    ✖     |    ✖     |  
|Secondary BTree Inner Nested Loop|10 + 20(5)|    ✖     |10 + 20(5)|  
|Nested Loops                    |10 + 2(10)(2000)|10 + 2(10)(20000)|10 + 2(10)(200000)|   
|Hash Join                       |10+1000+20(1)|10+10000+20(1)|10+100000+20(1)|    

| T<sub>3</sub> ⋈<sub>attr</sub> T<sub>X</sub> Cost    |    T<sub>1</sub>    |    T<sub>2</sub>    |    T<sub>4</sub>    | 
|-------------------------------:|:--------:|:--------:|:--------:| 
| Hash Inner Nested Loop         |     ✖    |   30     |    ✖     |  
|Primary BTree Inner Nested Loop |    70    |    ✖     |   110    |  
|Secondary BTree Inner Nested Loop|    ✖    |    ✖     |   110    |  
|Nested Loops                    |40010     |400010    |4000010   |   
|Hash Join                       |1030      |10030     |100030    |   

We observe that the Index Nested Loop using the T2 Hash Index is the least costly operation with a 30 page cost.  
R<sub>1</sub> = T<sub>3</sub> Hash-INL T<sub>2</sub>
</details>

<details> 
  <summary>3. Compare the cost of joining R1 against the other relations</summary><br />

| (T<sub>3</sub> ⋈<sub>attr</sub>T<sub>2</sub>) ⋈<sub>attr</sub> T<sub>X</sub> Cost   |    T<sub>1</sub>    |    T<sub>4</sub>    |   
|-------------------------------:|:--------:|:--------:|  
| Hash Inner Nested Loop         |     ✖    |    ✖     |  
|Primary BTree Inner Nested Loop |30 + 20(3)|30 + 20(3)|  
|Secondary BTree Inner Nested Loop|     ✖    |30 + 20(5)|  
|Nested Loops                    |30 + 2(10)(2000)|30 + 2(10)(200000)|   
|Hash Join                       |30+1000+20(1)|30+100000+20(1)|    

| (T<sub>3</sub> ⋈<sub>attr</sub>T<sub>2</sub>) ⋈<sub>attr</sub> T<sub>X</sub> Cost|  T<sub>1</sub>   |  T<sub>4</sub>    |   
|-------------------------------------------------:|:-----:|:------:|  
| Hash INL                                         |   ✖   |    ✖   |  
|Primary BTree INL                                 |90     |130     |  
|Secondary BTree INL                                |   ✖    |130     |  
|Nested Loops                                      |40030  |400030  |   
|Hash Join                                         |1050   |100050  | 
 We observe that the Index Nested Loop using the T1 Primary B-Tree Index is the least costly operation with a cumulative 90 page cost.  
R<sub>2</sub> = R<sub>1</sub> Primary-B-Tree-INL T<sub>1</sub>
</details>

<details> 
  <summary>4. Calculate the cost of joining R1 against the last relation</summary><br />

| ((T<sub>3</sub> ⋈<sub>attr</sub>T<sub>2</sub>) ⋈<sub>attr</sub> T1) ⋈<sub>attr</sub> T<sub>4</sub> Cost|  T<sub>4</sub>   | 
|----------------------------------------------------------------------:|:-----:| 
| Hash INL                                                              |   ✖   |  
|Primary BTree INL                                                      |90 + 20(5)|  
|Secondary BTree INL                                                     |90 + 20(5)|  
|Nested Loops                                                           |90 + 2(10)(2000)|30 + 2(10)(200000)|   
|Hash Join                                                              |90+100000+20(1)|   

| ((T<sub>3</sub> ⋈<sub>attr</sub>T<sub>2</sub>) ⋈<sub>attr</sub> T1) ⋈<sub>attr</sub> T<sub>4</sub> Cost|  T<sub>4</sub>   | 
|----------------------------------------------------------------------:|:-----:| 
| Hash INL                                                              |   ✖   |  
|Primary BTree INL                                                      |190    |  
|Secondary BTree INL                                                    |190    |  
|Nested Loops                                                           |400090 |   
|Hash Join                                                              |100090 | 

We identify that an Index Nested Loop join using either the Primary B-Tree or Secondary B-Tree equivalently incur the least cost.  
We select the Primary B-Tree and formulate the final relation.  
R<sub>3</sub> = R<sub>2</sub> Primary-B-Tree-INL T<sub>4</sub> with estimated cost 190.
</details>
</details>