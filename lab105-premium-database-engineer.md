# Lab 105: IBM i Database Engineer - SQL Development

## Overview
Learn how to use Bob's IBM i Database Engineer mode to create SQL views, develop stored procedures, and query data effectively.

**Duration**: 15 minutes  
**Difficulty**: Beginner  
**What You'll Build**: SQL view and stored procedure for article reporting

---

## Prerequisites
- Access to IBM i system via Code for IBM i extension
- Bob Premium Package for i installed
- SAMCO1 library with data (from Lab 106)

---

## Use Case: Database Development

We'll use Bob's Database Engineer mode to:
1. Create a reporting SQL view
2. Develop a stored procedure
3. Query and analyze data

---

## Step 1: Switch to IBM i Database Engineer Mode (1 minute)

**Action:**
1. Open Bob chat interface
2. Switch to **IBM i Database Engineer** mode (🛢️)

**What This Mode Does:**
- Creates SQL objects (views, procedures, functions)
- Executes and validates SQL statements
- Provides SQL examples and documentation
- Queries database objects

---

## Step 2: Create a Reporting View (5 minutes)

**Prompt for Bob:**
```
Create a SQL view called ARTICLE_SUMMARY in SAMCO1 that shows:
- Article ID and description
- Family name (joined from FAMILLY table)
- Sale price and stock quantity
- Total inventory value (stock * sale price)
```

**What Happens:**
Bob validates SQL and creates:

```sql
CREATE OR REPLACE VIEW SAMCO1.ARTICLE_SUMMARY AS
SELECT 
  a.ARID AS ARTICLE_ID,
  a.ARDESC AS DESCRIPTION,
  f.FADESC AS FAMILY_NAME,
  a.ARSALEPR AS SALE_PRICE,
  a.ARSTOCK AS STOCK_QTY,
  (a.ARSTOCK * a.ARSALEPR) AS TOTAL_VALUE
FROM SAMCO1.ARTICLE a
LEFT JOIN SAMCO1.FAMILLY f ON a.ARTIFA = f.FAID
WHERE a.ARDEL = '';

LABEL ON TABLE SAMCO1.ARTICLE_SUMMARY 
  IS 'Article Summary with Family';
```

**Test the View:**
```sql
SELECT * FROM SAMCO1.ARTICLE_SUMMARY
ORDER BY TOTAL_VALUE DESC
FETCH FIRST 10 ROWS ONLY;
```

---

## Step 3: Develop a Stored Procedure (5 minutes)

**Prompt for Bob:**
```
Create a stored procedure called GET_LOW_STOCK in SAMCO1 that:
- Takes a minimum quantity parameter
- Returns articles where stock is below that quantity
- Shows article ID, description, current stock, and reorder quantity needed
```

**What Happens:**
Bob creates the procedure:

```sql
CREATE OR REPLACE PROCEDURE SAMCO1.GET_LOW_STOCK(
  IN p_min_qty DECIMAL(5,0)
)
DYNAMIC RESULT SETS 1
LANGUAGE SQL
BEGIN
  DECLARE c1 CURSOR WITH RETURN FOR
    SELECT 
      ARID,
      ARDESC,
      ARSTOCK AS CURRENT_STOCK,
      ARMINQTY AS MIN_STOCK,
      (ARMINQTY - ARSTOCK) AS REORDER_QTY
    FROM SAMCO1.ARTICLE
    WHERE ARSTOCK < p_min_qty
      AND ARDEL = ''
    ORDER BY ARSTOCK;
  
  OPEN c1;
END;
```

**Test the Procedure:**
```sql
CALL SAMCO1.GET_LOW_STOCK(20);
```

---

## Step 4: Query Database Objects (2 minutes)

**Prompt for Bob:**
```
List all tables and views in SAMCO1 schema with their row counts.
```

**What Happens:**
Bob queries system catalogs:

```sql
SELECT TABLE_NAME, TABLE_TYPE, NUMBER_ROWS
FROM QSYS2.SYSTABLES
WHERE TABLE_SCHEMA = 'SAMCO1'
ORDER BY TABLE_TYPE, TABLE_NAME;
```

**Expected Output:**
```
Tables:
- ARTICLE (33 rows)
- CUSTOMER (10 rows)
- ORDER (10 rows)
...

Views:
- ARTICLE_SUMMARY (33 rows)
- ARTLSTDAT (33 rows)
```

---

## Step 5: Use SQL Examples (2 minutes)

**Prompt for Bob:**
```
Show me SQL examples for querying table statistics
using IBM i Services.
```

**What Happens:**
Bob provides examples from `get_sql_examples`:

```sql
-- Get table sizes
SELECT TABLE_NAME, NUMBER_ROWS, DATA_SIZE
FROM QSYS2.SYSTABLESTAT
WHERE TABLE_SCHEMA = 'SAMCO1'
ORDER BY DATA_SIZE DESC;

-- Get index information
SELECT INDEX_NAME, TABLE_NAME, NUMBER_KEYS
FROM QSYS2.SYSINDEXES
WHERE TABLE_SCHEMA = 'SAMCO1';
```

---

## ✅ Success Criteria

You've successfully completed this lab when:
- [ ] SQL view created and tested
- [ ] Stored procedure developed and executed
- [ ] Database objects queried
- [ ] SQL examples explored

---

## Key Takeaways

1. **Views Simplify Queries**: Create reusable views for complex joins
2. **Procedures Encapsulate Logic**: Store business logic in database
3. **Use System Catalogs**: Query QSYS2 for metadata
4. **Validate SQL**: Always check syntax before execution
5. **Learn from Examples**: Use SQL examples library

---

## Tools Used in This Lab

- `run_sql_statement` - Execute SQL DDL/DML
- `run_sql_syntax_checker` - Validate SQL
- `get_sql_examples` - Access SQL reference library
- `get_database_objects` - List database objects

---

## Next Steps

- Create more views for different reports
- Develop additional stored procedures
- Explore IBM i Services examples
- Try Lab 111 for unit testing