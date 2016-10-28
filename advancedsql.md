# Overview

what does this document go over?

www.instabase.com
go to sqladvanced.ipynb

# Triggers
**Triggers** are used for:

1. Constraining (e.g. at least one)

2. Copying/filling data based on other tables (e.g. purchasing an item, and copying the current price into the record of the purchase)

3. Recording history of updates

### Example 1: Executing an Outside Procedure through a Trigger
```sql
CREATE FUNCTION copyit() RETURNS trigger
AS $$
BEGIN
    INSERT INTO b VALUES(NEW.a);
    RETURN null;
END;
$$ language plpgsql;

CREATE TRIGGER t_copyit
BEFORE INSERT ON a
FOR EACH ROW
  WHEN (NEW.a > 10)
  EXECUTE PROCEDURE copyit();
```
- `NEW`: new row being inserted ( `OLD`: data that was there in the table )
- The trigger `t_copyit` imposed on table `a` requires that before insertion of new value `NEW.a` into `a`, it must be confirmed as being > 10. 
- If this trigger check is passed, then the trigger executes `copyit()` which copies `NEW.a` into `b`
- `RETURN NULL` aborts the `copyit()` execution for that record. The trigger then moves on to next record in the `FOR EACH ROW` loop

### Example 2: Interchanging `BEFORE INSERT` and `AFTER INSERT` Triggers

- The below two trigger definitions are equivalent. They both check records for meeting some constraint, and prevent the table from being updated with these records.

- **Before Insert**: Before insert, the record's insertion is blocked if it meets the condition.
```sql
CREATE TRIGGER nocromulons 
BEFORE INSERT ON users
FOR EACH ROW
    BEGIN
        IF NEW. species = 'Cromulon'
            RETURN NULL; 
        END IF;
        RETURN NEW; 
    END ;
```

- **After Insert**: After insert, the record gets deleted if it meets the condition.
```sql
CREATE TRIGGER nocromulons
AFTER INSERT ON users
FOR EACH ROW [or STATEMENT]
    BEGIN
        DELETE FROM users WHERE species = `Cromulon`;
        RETURN NULL;
    END ;
```


### Triggers vs Constraints
Constraints

1. Provides a statement about the state of the DB

2. It doesn't modify the DB state

3. It is "understood" by the DB

Triggers

1. It is operational: X runs when Y happens

2. It is very flexible

3. Must be executed on every matching statement: If more than one trigger matches an action, the system doesn't know which one to run first


# With
- The **`WITH`** statement provides a means to define temporary tables
- It is a good approach for nested queries
- A nested query can simply be substituted with the name of a table defined by `WITH`

### Example
- You must define the schema for the temp table e.g. `RedBoats(bid, count)`
- First Define the temp table (and its schema) via `WITH`...`AS`
```sql
WITH RedBoats(bid, count) AS
    (SELECT B.bid, count(*)
    FROM Boats B, Reserves R
    WHERE R.bid = B.bid AND B.color = 'red'
    GROUP BY B.bid)
```

- Then reference this temp table like you would any other table
``` sql
SELECT name, count
FROM Boats AS B, RedBoats AS RB
WHERE B.bid = RB.bid AND count < 2
```

# View
- **Views** define tables that can be saved, as well as accessed by other people 
- Used for constructing tables out of actual table results, unlike `With` which just generates a table confined to the query
- When a view table is updated, its base data is also updated.

- **BUT**, when a view table with a `GROUP BY` is updated, the base data can’t be updated 
    - This is because if you have an aggregated value in your view, it would be ambiguous as to which tuple involved in the aggregated value to apply the update to.
    - e.g.If you're multiplying a `count()` field by 2, which tuple in the base data do you have to duplicate to yield that transformation in the view?
    - Using `CREATE TABLE` is immune to this issue. An aggregated table defined by `CREATE TABLE` can be updated, with successful updating of the base data.


# UDFs

previous notes here

## Procedural Language/SQL(lang = plsql) 

Define a function that runs a sql query and can take as parameters individual scalar values or tuples.  Mult2(sailors)

There is boilerplate code to achieve this, and you can write multiple lines of statements with if else statements:


```
Declare 
//you define the variables you want to use in the body of this function, you don’t need to declare anything
Begin 
//where you write the code and logic
End;
```

You can write multiple statements, expressions, arbitrary SQLs queries, return values.  For example, the following does XXX

```
example
```



