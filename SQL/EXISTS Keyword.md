
# EXISTS Keyword

In SQL Server, `IF EXISTS` is a control-of-flow statement that checks whether a subquery returns at least one row.
It’s most often used in `IF…ELSE` logic to run different statements depending on whether data exists.

#### 1️⃣ Basic Syntax
```sql
IF EXISTS (subquery)
    -- statement to run if the subquery returns at least one row
ELSE
    -- statement to run if the subquery returns no rows
```
- The subquery is usually a SELECT statement.
- SQL Server stops scanning as soon as it finds the first matching row—fast and efficient.

#### 2️⃣ Simple Example
```sql
IF EXISTS (SELECT 1 FROM Customers WHERE City = 'Hyderabad')
    PRINT 'There are customers in Hyderabad.';
ELSE
    PRINT 'No customers in Hyderabad.';
```

#### 3️ IF NOT EXISTS
Reverse logic to run code when no rows exist:
```sql
IF NOT EXISTS (SELECT 1 FROM Users WHERE Email = 'abc@example.com')
    INSERT INTO Users (Name, Email) VALUES ('Alice','abc@example.com');
```
Useful for “insert-if-missing” patterns.

#### 4️⃣ Tips & Best Practices

Use SELECT 1 or SELECT * – both work, but SELECT 1 is a clear signal you only care about existence.

EXISTS ignores the actual column list and values—it just checks for any row.

EXISTS usually performs better than COUNT(*) when you only need to know if rows exist, because it stops at the first match.
