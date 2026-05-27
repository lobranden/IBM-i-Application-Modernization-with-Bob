# Lab 103: IBM i Modernization - Convert DDS to SQL

## Overview
Learn how to use Bob's IBM i Modernization mode to convert legacy DDS physical files to modern SQL tables.

**Duration**: 15 minutes  
**Difficulty**: Intermediate  
**What You'll Build**: Convert ARTICLE physical file from DDS to SQL DDL

---

## Prerequisites
- Access to IBM i system via Code for IBM i extension
- Bob Premium Package for i installed
- SAMCO1 library with objects (from Lab 106)

---

## Use Case: Database Modernization

We'll use Bob's Modernization mode to:
1. Analyze existing DDS file structure
2. Generate SQL DDL from DDS
3. Create SQL table in new schema
4. Verify data migration

---

## Step 1: Switch to IBM i Modernization Mode (1 minute)

**Action:**
1. Open Bob chat interface
2. Switch to **IBM i Modernization** mode (💡)

**What This Mode Does:**
- Converts DDS to SQL DDL
- Refactors Fixed to Free format RPG
- Modernizes legacy code patterns
- Validates conversions

---

## Step 2: Analyze DDS Structure (3 minutes)

**Prompt for Bob:**
```
Parse the DDS file structure of ARTICLE in SAMCO1.
Show me the field definitions, keys, and constraints.
```

**What Happens:**
Bob uses `run_dds_parser` to analyze:
- Field names, types, lengths
- Key fields
- Text descriptions
- Field-level keywords

**Expected Output:**
```
Physical File: ARTICLE
Fields:
- ARID (CHAR 6) - Article ID *KEY
- ARDESC (CHAR 50) - Description
- ARSALEPR (PACKED 7,2) - Sale Price
- ARWHSPR (PACKED 7,2) - Wholesale Price
- ARTIFA (CHAR 3) - Family Code
- ARSTOCK (PACKED 5,0) - Stock Quantity
...
```

---

## Step 3: Generate SQL DDL (4 minutes)

**Prompt for Bob:**
```
Generate SQL DDL to recreate the ARTICLE file as a SQL table.
Include primary key, field descriptions, and constraints.
```

**What Happens:**
Bob uses `generate_sql` to create:
- CREATE TABLE statement
- Column definitions with SQL types
- Primary key constraint
- Column comments

**Expected Output:**
```sql
CREATE TABLE SAMCO1.ARTICLE (
  ARID CHAR(6) NOT NULL,
  ARDESC VARCHAR(50) NOT NULL DEFAULT '',
  ARSALEPR DECIMAL(7,2) NOT NULL DEFAULT 0,
  ARWHSPR DECIMAL(7,2) NOT NULL DEFAULT 0,
  ARTIFA CHAR(3) NOT NULL DEFAULT '',
  ARSTOCK DECIMAL(5,0) NOT NULL DEFAULT 0,
  ARMINQTY DECIMAL(5,0) NOT NULL DEFAULT 0,
  ARCUSQTY DECIMAL(5,0) NOT NULL DEFAULT 0,
  ARPURQTY DECIMAL(5,0) NOT NULL DEFAULT 0,
  ARVATCD CHAR(1) NOT NULL DEFAULT '',
  ARCREA DATE NOT NULL,
  ARMOD TIMESTAMP NOT NULL,
  ARMODID CHAR(10) NOT NULL DEFAULT '',
  ARDEL CHAR(1) NOT NULL DEFAULT '',
  
  CONSTRAINT ARTICLE_PK PRIMARY KEY (ARID)
);

LABEL ON TABLE SAMCO1.ARTICLE IS 'Article Master File';
LABEL ON COLUMN SAMCO1.ARTICLE.ARID IS 'Article ID';
LABEL ON COLUMN SAMCO1.ARTICLE.ARDESC IS 'Article Description';
```

---

## Step 4: Create SQL Table (3 minutes)

**Prompt for Bob:**
```
Create a new schema SAMCO_SQL and create the ARTICLE table
using the generated DDL.
```

**What Happens:**
1. Bob validates SQL syntax with `run_sql_syntax_checker`
2. Creates schema: `CREATE SCHEMA SAMCO_SQL`
3. Executes CREATE TABLE statement
4. Verifies table creation

**Expected Output:**
```
✓ Schema SAMCO_SQL created
✓ Table ARTICLE created successfully
✓ Primary key constraint added
✓ Column labels applied
```

---

## Step 5: Compare Structures (2 minutes)

**Prompt for Bob:**
```
Compare the DDS file SAMCO1/ARTICLE with the SQL table SAMCO_SQL.ARTICLE.
Show me the differences in structure.
```

**What Happens:**
Bob queries system catalogs to compare:
- Field names and types
- Constraints
- Indexes
- Descriptions

**Expected Comparison:**
```
Similarities:
✓ Same field names
✓ Same data types (converted)
✓ Same primary key

Differences:
- SQL uses VARCHAR instead of CHAR (where appropriate)
- SQL has explicit PRIMARY KEY constraint
- SQL has column-level labels
- SQL supports CHECK constraints (can be added)
```

---

## Step 6: Verify Conversion (2 minutes)

**Prompt for Bob:**
```
Query both the DDS file and SQL table to verify they have
the same structure and can hold the same data.
```

**What Happens:**
Bob executes queries:
```sql
-- Check DDS file
SELECT * FROM QSYS2.SYSTABLES 
WHERE TABLE_SCHEMA='SAMCO1' AND TABLE_NAME='ARTICLE';

-- Check SQL table
SELECT * FROM QSYS2.SYSTABLES 
WHERE TABLE_SCHEMA='SAMCO_SQL' AND TABLE_NAME='ARTICLE';

-- Compare columns
SELECT COLUMN_NAME, DATA_TYPE, LENGTH 
FROM QSYS2.SYSCOLUMNS 
WHERE TABLE_SCHEMA IN ('SAMCO1','SAMCO_SQL') 
  AND TABLE_NAME='ARTICLE'
ORDER BY ORDINAL_POSITION;
```

---

## ✅ Success Criteria

You've successfully completed this lab when:
- [ ] DDS structure analyzed
- [ ] SQL DDL generated
- [ ] SQL table created in new schema
- [ ] Structures compared and verified
- [ ] You understand DDS to SQL conversion

---

## Key Takeaways

1. **Analyze First**: Understand DDS structure before converting
2. **Generate DDL**: Use tools to create accurate SQL
3. **Validate Syntax**: Check SQL before execution
4. **Compare Results**: Verify conversion accuracy
5. **Modern Benefits**: SQL provides more features than DDS

---

## Tools Used in This Lab

- `run_dds_parser` - Analyze DDS structure
- `generate_sql` - Convert DDS to SQL DDL
- `run_sql_syntax_checker` - Validate SQL
- `run_sql_statement` - Execute DDL
- `get_database_objects` - Verify objects

---

## Modernization Benefits

**DDS Limitations:**
- Fixed field lengths
- Limited data types
- No referential integrity
- No check constraints

**SQL Advantages:**
- VARCHAR for variable length
- Rich data types (DATE, TIMESTAMP, etc.)
- Foreign key constraints
- Check constraints
- Triggers and stored procedures

---

## Next Steps

- Convert other DDS files (CUSTOMER, ORDER)
- Add foreign key constraints
- Create indexes for performance
- Migrate data from DDS to SQL
- Move to Lab 109 for impact analysis