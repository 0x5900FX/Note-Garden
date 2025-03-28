`Web security vulnerability that allows an attacker to interfere with the queries that an application makes to its database.`

SQL injection common occurrences location:
- `WHERE` clause of a `SELECT` query
- `UPDATE` statements, within the updated values or the `WHERE` clause
- `INSERT` statements, within the inserted values
- `SELECT` statements, within the table or column name
- `SELECT` statements, within the `ORDER BY` clause

`SELECT * FROM products WHERE category = 'Gifts' AND released = 1`
 Can add OR '1'='1' --
 `SELECT * FROM products WHERE category = 'Gifts' OR'1'='1' -- AND released = 1`

`Union` attacks allows us to execute one or more `SELECT` queries and append result to original query.

`SELECT a, b FROM table1 UNION SELECT c, d FROM table2`



```
Payloads List

OR '1'='1' -- 
OR '1'='1' #

UNION 

```