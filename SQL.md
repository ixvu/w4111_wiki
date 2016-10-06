# Lecture 9 SQL

## Structured Query Language
The two subsets of SQL language:
- Data Definition Language (DDL): All the statements in order to create or define schema.
- Data Manipulation Language (DML): Get data into the database, modify and fetch data.


SQL is an extension of relational algebra, it extends in many ways: 
- Multi-sets: results of SQL statement can contain duplicates
- NULLs
- Aggregates


## Database Example
![](https://github.com/CHJoanna/W4111_sribenote/blob/master/img1.png)
- The Primary key for Reserves is sid and bid, meaning a sailor can only reserves a boat once.
- Day should be part of key.   
   
    
    
    
![](https://github.com/CHJoanna/W4111_sribenote/blob/master/img2.png)
- Add a relation to the FROM clause is similar to perform a cross-product, and the WHERE statement is the filter condition for the output of cross-product.


## Semantics
- FROM: compute cross product of relations
- WHERE: remove tuples that fail qualifications. Specify the selection conditions on the relations in FROM. 
- SELECT: specify columns to be retained in the result
- DISTINCT: remove duplicate rows

[More Examples about SQL statement](https://www.instabase.com/ewu/w4111-public/fs/Instabase%20Drive/Examples/sql.ipynb)


