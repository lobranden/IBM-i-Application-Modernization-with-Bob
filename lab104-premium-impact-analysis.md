# Lab 104: IBM i Impact Analysis - Analyze Object Dependencies

## Overview
Learn how to use Bob's IBM i Impact Analysis mode to understand object dependencies and assess the impact of changes.

**Duration**: 15 minutes  
**Difficulty**: Intermediate  
**What You'll Build**: Complete dependency analysis for ARTICLE file

---

## Prerequisites
- Access to IBM i system via Code for IBM i extension
- Bob Premium Package for i installed
- SAMCO1 library with objects (from Lab 106)

---

## Use Case: Safe Change Management

We'll use Bob's Impact Analysis mode to:
1. Find all objects dependent on ARTICLE file
2. Analyze program references
3. Identify database relationships
4. Assess change impact

---

## Step 1: Switch to IBM i Impact Analysis Mode (1 minute)

**Action:**
1. Open Bob chat interface
2. Switch to **IBM i Impact Analysis** mode (🔗)

**What This Mode Does:**
- Finds object dependencies
- Analyzes program references
- Identifies database relationships
- Assesses change impact

---

## Step 2: Find Direct Dependencies (3 minutes)

**Prompt for Bob:**
```
Find all objects that depend on the ARTICLE file in SAMCO1.
Show me programs, views, and other files that reference it.
```

**What Happens:**
Bob uses `get_related_objects` to query:
- Programs using the file
- Logical files based on it
- Views referencing it
- Triggers on the file

**Expected Output:**
```
Direct Dependencies on SAMCO1/ARTICLE:

Programs (5):
- ART200 (SQLRPGLE) - Work with Articles
- ART201 (RPGLE) - Article Maintenance
- ART202 (RPGLE) - Article Functions
- ORD200 (SQLRPGLE) - Order Entry
- ORD201 (SQLRPGLE) - Order Processing

Logical Files (2):
- ARTICLE1 - Keyed by ARID
- ARTICLE2 - Keyed by ARTIFA (Family)

Views (1):
- ARTLSTDAT - Article with last order date

Service Programs (1):
- FARTICLE - Article functions
```

---

## Step 3: Analyze Program Usage (4 minutes)

**Prompt for Bob:**
```
Analyze how program ART200 uses the ARTICLE file.
Show me which fields are read, updated, and in what operations.
```

**What Happens:**
Bob uses `run_rpgle_parser` and `read_member` to identify:
- File operations (READ, WRITE, UPDATE, DELETE)
- Fields accessed
- SQL statements
- Embedded SQL vs native I/O

**Expected Output:**
```
Program: ART200
File Usage: ARTICLE

Operations:
- SELECT (SQL) - Reads all fields for subfile display
- UPDATE (SQL) - Updates ARDESC, ARSALEPR, ARWHSPR, ARSTOCK
- INSERT (SQL) - Creates new articles
- DELETE (SQL) - Soft delete (sets ARDEL='X')

Fields Accessed:
Read: ARID, ARDESC, ARSALEPR, ARWHSPR, ARTIFA, ARSTOCK
Write: ARDESC, ARSALEPR, ARWHSPR, ARSTOCK, ARMOD, ARMODID
Key: ARID (primary key)

SQL Statements:
1. SELECT * FROM ARTICLE WHERE ARDEL='' ORDER BY ARID
2. UPDATE ARTICLE SET ARDESC=:desc, ARSALEPR=:price WHERE ARID=:id
3. INSERT INTO ARTICLE VALUES(...)
```

---

## Step 4: Check Database Relationships (3 minutes)

**Prompt for Bob:**
```
Query the system catalog to find all foreign key relationships
involving the ARTICLE table.
```

**What Happens:**
Bob executes SQL queries:
```sql
-- Find foreign keys referencing ARTICLE
SELECT * FROM QSYS2.SYSCST 
WHERE CONSTRAINT_SCHEMA='SAMCO1' 
  AND (TABLE_NAME='ARTICLE' OR REFERENCED_TABLE_NAME='ARTICLE')
  AND CONSTRAINT_TYPE='FOREIGN KEY';

-- Find referential constraints
SELECT * FROM QSYS2.SYSREFCST
WHERE CONSTRAINT_SCHEMA='SAMCO1'
  AND (TABLE_NAME='ARTICLE' OR REFERENCED_TABLE_NAME='ARTICLE');
```

**Expected Output:**
```
Foreign Key Relationships:

Referenced By:
- DETORD.ODARID → ARTICLE.ARID (Order details reference articles)
- ARTIPROV.APARID → ARTICLE.ARID (Article-provider relationships)

References:
- ARTICLE.ARTIFA → FAMILLY.FAID (Article references family)
```

---

## Step 5: Search for Code References (2 minutes)

**Prompt for Bob:**
```
Search all RPG programs in SAMCO1 for references to field ARSALEPR
(article sale price). Show me where it's used.
```

**What Happens:**
Bob uses `search_qsys` with pattern matching:
```
Library: SAMCO1
File Pattern: QRP*
Member Pattern: *
Search Term: ARSALEPR
```

**Expected Output:**
```
References to ARSALEPR found in:

ART200.SQLRPGLE (3 occurrences):
- Line 45: ARSALEPR = :newPrice
- Line 67: IF ARSALEPR < ARWHSPR
- Line 89: EXFMT SCREEN01 (displays ARSALEPR)

ART202.RPGLE (2 occurrences):
- Line 123: price = ARSALEPR
- Line 145: ARSALEPR = price * 1.20

ORD201.SQLRPGLE (1 occurrence):
- Line 234: SELECT ARID, ARDESC, ARSALEPR FROM ARTICLE
```

---

## Step 6: Assess Change Impact (2 minutes)

**Prompt for Bob:**
```
If I change the ARTICLE file structure by adding a new field ARDISCOUNT,
what programs will need to be recompiled or modified?
```

**What Happens:**
Bob analyzes dependencies and provides impact assessment:

**Expected Response:**
```
Impact Assessment: Adding ARDISCOUNT field to ARTICLE

Must Recompile (5 programs):
- ART200 - Uses SELECT * (will include new field)
- ART201 - Native I/O, needs recompile
- ART202 - Native I/O, needs recompile
- ORD200 - Uses SELECT * in cursor
- ORD201 - Uses SELECT * in subfile

May Need Modification (2 programs):
- ART200 - If discount should display on screen
- ORD201 - If discount affects order pricing

No Impact (3 programs):
- CUS200 - Doesn't use ARTICLE
- PAR200 - Doesn't use ARTICLE
- PRO200 - Doesn't use ARTICLE

Logical Files (2):
- ARTICLE1 - Recompile required
- ARTICLE2 - Recompile required

Views (1):
- ARTLSTDAT - May need ALTER VIEW if discount needed
```

---

## ✅ Success Criteria

You've successfully completed this lab when:
- [ ] All dependent objects identified
- [ ] Program usage analyzed
- [ ] Database relationships mapped
- [ ] Code references found
- [ ] Change impact assessed

---

## Key Takeaways

1. **Know Dependencies**: Always check before making changes
2. **Analyze Impact**: Understand what will break
3. **Plan Changes**: Identify all affected programs
4. **Test Thoroughly**: Recompile and test all dependents
5. **Document Relationships**: Keep dependency maps updated

---

## Tools Used in This Lab

- `get_related_objects` - Find dependencies
- `run_rpgle_parser` - Analyze program structure
- `read_member` - Read source code
- `search_qsys` - Search for references
- `run_sql_statement` - Query system catalogs
- `get_database_objects` - List database objects

---

## System Catalog Queries

**Useful queries for impact analysis:**

```sql
-- Find all programs using a file
SELECT * FROM QSYS2.SYSPROGRAMSTAT
WHERE PROGRAM_SCHEMA='SAMCO1'
  AND PROGRAM_NAME IN (
    SELECT BNAME FROM QSYS2.SYSDEP 
    WHERE DNAME='ARTICLE' AND DTYPE='*FILE'
  );

-- Find all dependencies
SELECT * FROM QSYS2.SYSDEP
WHERE DNAME='ARTICLE' AND DSCHEMA='SAMCO1';

-- Find foreign keys
SELECT * FROM QSYS2.SYSCST
WHERE TABLE_SCHEMA='SAMCO1' 
  AND CONSTRAINT_TYPE='FOREIGN KEY';
```

---

## Best Practices

1. **Before Any Change**: Run impact analysis
2. **Document Dependencies**: Keep a dependency map
3. **Test in Development**: Never change production first
4. **Coordinate Changes**: Notify all affected teams
5. **Version Control**: Track all related changes together

---

## Next Steps

- Analyze other critical files (CUSTOMER, ORDER)
- Create dependency documentation
- Plan a database modernization project
- Set up automated impact analysis
- Return to Lab 106 for more DevOps practices