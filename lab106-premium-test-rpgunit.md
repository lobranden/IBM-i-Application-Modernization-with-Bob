# Lab 106: IBM i Test - Generate RPGUnit Test Cases

## Overview
Learn how to use Bob's IBM i Test mode to generate unit test stubs for RPG procedures using RPGUnit framework.

**Duration**: 15 minutes  
**Difficulty**: Intermediate  
**What You'll Build**: RPGUnit test suite for article management procedures

---

## Prerequisites
- Access to IBM i system via Code for IBM i extension
- Bob Premium Package for i installed
- RPGUnit framework installed on IBM i
- SAMCO library with compiled objects (see Step 0 below)

---

## Use Case: Automated Test Generation

We'll use Bob's Test mode to:
1. Set up SAMCO1 test library (one-time setup)
2. Identify procedures to test
3. Generate RPGUnit test stubs
4. Create test data setup
5. Compile and create test program
6. Run unit tests
7. Analyze test coverage

---

## Step 0: Set Up SAMCO1 Test Library (One-Time Setup - 5 minutes)

Before generating tests, you need to create a test library (SAMCO1) that mirrors the production SAMCO library structure but is isolated for testing purposes.

### Why a Separate Test Library?

- **Isolation**: Tests won't affect production data
- **Safety**: Can delete and recreate test data freely
- **Consistency**: Each test run starts with known data state
- **Parallel Development**: Multiple developers can have their own test libraries

### Step 0a: Create SAMCO1 Library

**Prompt for Bob:**
```
Create a new library called SAMCO1 for testing purposes
```

**What Happens:**
Bob executes:
```cl
CRTLIB LIB(SAMCO1) TEXT('Test Library for SAMCO Application')
```

### Step 0b: Copy Database Objects from SAMCO to SAMCO1

**Option 1: Copy All Objects (Recommended for Initial Setup)**

**Prompt for Bob:**
```
Copy all database files from SAMCO library to SAMCO1 library
```

**What Happens:**
Bob executes commands like:
```cl
CRTDUPOBJ OBJ(*ALL) FROMLIB(SAMCO) OBJTYPE(*FILE) TOLIB(SAMCO1)
```

**Option 2: Create Tables from DDS Source (If SAMCO doesn't exist yet)**

If you're starting from scratch with just the source code:

**Prompt for Bob:**
```
Compile all physical files from SAMCO/QDDSSRC into SAMCO1 library
```

**What Happens:**
Bob compiles DDS files:
```cl
CRTPF FILE(SAMCO1/ARTICLE) SRCFILE(SAMCO/QDDSSRC) SRCMBR(ARTICLE)
CRTPF FILE(SAMCO1/CUSTOMER) SRCFILE(SAMCO/QDDSSRC) SRCMBR(CUSTOMER)
CRTPF FILE(SAMCO1/PROVIDER) SRCFILE(SAMCO/QDDSSRC) SRCMBR(PROVIDER)
CRTPF FILE(SAMCO1/FAMILLY) SRCFILE(SAMCO/QDDSSRC) SRCMBR(FAMILLY)
CRTPF FILE(SAMCO1/COUNTRY) SRCFILE(SAMCO/QDDSSRC) SRCMBR(COUNTRY)
CRTPF FILE(SAMCO1/PARAMETER) SRCFILE(SAMCO/QDDSSRC) SRCMBR(PARAMETER)
CRTPF FILE(SAMCO1/ORDER) SRCFILE(SAMCO/QDDSSRC) SRCMBR(ORDER)
CRTPF FILE(SAMCO1/DETORD) SRCFILE(SAMCO/QDDSSRC) SRCMBR(DETORD)
CRTPF FILE(SAMCO1/ARTIPROV) SRCFILE(SAMCO/QDDSSRC) SRCMBR(ARTIPROV)
```

### Step 0c: Populate Test Data

**Prompt for Bob:**
```
Populate SAMCO1 library with test data using the POPULATE_SAMCO_TABLES.sql script
```

**What Happens:**
Bob executes the SQL script (modified for SAMCO1):

```sql
-- Populate SAMCO1 with test data
INSERT INTO SAMCO1.COUNTRY (COID, COUNTR, COISO, COISO5, COISO1) VALUES
('FR', 'France', 'FRA', '250', '33'),
('US', 'United States', 'USA', '840', '1'),
('GB', 'United Kingdom', 'GBR', '826', '44');

INSERT INTO SAMCO1.FAMILLY (FAID, FADESC, FAVATCD, FACREA, FAMOD, FAMODID, FADEL) VALUES
('ELE', 'Electronics', '2', CURRENT DATE, CURRENT TIMESTAMP, 'ADMIN', ''),
('FUR', 'Furniture', '2', CURRENT DATE, CURRENT TIMESTAMP, 'ADMIN', ''),
('BOO', 'Books', '1', CURRENT DATE, CURRENT TIMESTAMP, 'ADMIN', '');

INSERT INTO SAMCO1.ARTICLE (ARID, ARDESC, ARSALEPR, ARWHSPR, ARTIFA, ARSTOCK,
                            ARMINQTY, ARCUSQTY, ARPURQTY, ARVATCD, ARCREA,
                            ARMOD, ARMODID, ARDEL) VALUES
('000001', 'Laptop Computer 15 inch', 899.99, 650.00, 'ELE', 25, 5, 0, 0, '2',
 CURRENT DATE, CURRENT TIMESTAMP, 'ADMIN', ''),
('000002', 'Wireless Mouse', 29.99, 15.00, 'ELE', 150, 20, 0, 0, '2',
 CURRENT DATE, CURRENT TIMESTAMP, 'ADMIN', ''),
('000003', 'Office Desk', 299.99, 180.00, 'FUR', 10, 2, 0, 0, '2',
 CURRENT DATE, CURRENT TIMESTAMP, 'ADMIN', '');

INSERT INTO SAMCO1.PARAMETER (PACODE, PASUBCODE, PARM1, PARM2, PARM3, PARM4, PARM5) VALUES
('VAT', '1', '10.00', 'Reduced VAT', '', 1, 10),
('VAT', '2', '20.00', 'Standard VAT', '', 2, 20);
```

**Alternative: Run Full Population Script**
```cl
RUNSQLSTM SRCFILE(SAMCO/QSQLSRC) SRCMBR(POPULATE_SAMCO_TABLES)
          COMMIT(*NONE) NAMING(*SQL)
```
(Note: You'll need to modify the script to use SAMCO1 instead of SAMCO)

### Step 0d: Copy Service Programs to SAMCO1

**Prompt for Bob:**
```
Copy the ART300 service program from SAMCO to SAMCO1
```

**What Happens:**
```cl
CRTDUPOBJ OBJ(ART300) FROMLIB(SAMCO) OBJTYPE(*SRVPGM) TOLIB(SAMCO1)
```

Or compile from source:
```cl
CRTSRVPGM SRVPGM(SAMCO1/ART300)
          MODULE(SAMCO1/ART300 SAMCO1/ART301 SAMCO1/ART302)
          EXPORT(*ALL)
          ACTGRP(*CALLER)
```

### Verification

**Prompt for Bob:**
```
Verify SAMCO1 library setup by listing all files and checking record counts
```

**Expected Output:**
```
SAMCO1 Library Contents:
- ARTICLE (*FILE) - 3 records
- CUSTOMER (*FILE) - 0 records
- PROVIDER (*FILE) - 0 records
- FAMILLY (*FILE) - 3 records
- COUNTRY (*FILE) - 3 records
- PARAMETER (*FILE) - 2 records
- ORDER (*FILE) - 0 records
- DETORD (*FILE) - 0 records
- ARTIPROV (*FILE) - 0 records
- ART300 (*SRVPGM) - Service program

✓ SAMCO1 library is ready for testing
```

---

## Step 1: Switch to IBM i Test Mode (1 minute)

**Action:**
1. Open Bob chat interface
2. Switch to **IBM i Test** mode (🎯)

**What This Mode Does:**
- Generates RPGUnit test stubs
- Creates test data setup
- Compiles and runs unit tests
- Analyzes test coverage

---

## Step 2: Identify Procedures to Test (3 minutes)

**Prompt for Bob:**
```
Parse the service program source SAMCO1/QRPGLESRC/ART300.RPGLE
and show me all exported procedures that need unit tests.
```

**What Happens:**
Bob uses `run_rpgle_parser` to identify:
- Exported procedures
- Parameters and return types
- Procedure purpose

**Expected Output:**
```
Exported Procedures in ART300:

1. GetArticle(articleId CHAR(6)) RETURNS article_t
   - Retrieves article by ID
   
2. ValidateArticle(article article_t) RETURNS BOOLEAN
   - Validates article data
   
3. CalculatePrice(basePrice PACKED(7,2), vatCode CHAR(1)) RETURNS PACKED(7,2)
   - Calculates price with VAT
```

---

## Step 3: Generate Test Stubs (5 minutes)

**Prompt for Bob:**
```
Generate RPGUnit test stubs for the ART300 service program.
Create tests for GetArticle, ValidateArticle, and CalculatePrice procedures.
```

**What Happens:**
Bob uses `generate_rpg_unit_test_stub` to create test file: `SAMCO1/QTESTSRC/ART300T.RPGLE`

**Generated Test Structure:**

```rpgle
**FREE

///
// Test Suite for ART300 - Article Functions
// Generated by Bob Premium Package
///

Ctl-Opt NoMain Option(*SrcStmt);

/include 'QRPGLESRC/ART300.RPGLE'
/include QRPGUNIT1,TESTCASE

//==================================================================
// Test: GetArticle - Valid Article ID
//==================================================================
Dcl-Proc test_GetArticle_ValidId Export;
  Dcl-Pi *N End-Pi;
  
  // Declarations
  Dcl-S articleId Char(6);
  Dcl-Ds article LikeDs(article_t);
  Dcl-Ds expected LikeDs(article_t);
  
  // Setup test data
  articleId = '000001';
  expected.ARID = '000001';
  expected.ARDESC = 'Laptop Computer 15 inch';
  expected.ARSALEPR = 899.99;
  
  // Execute
  article = GetArticle(articleId);
  
  // Assert
  aEqual(expected.ARID : article.ARID);
  aEqual(expected.ARDESC : article.ARDESC);
  aEqual(expected.ARSALEPR : article.ARSALEPR);
  
End-Proc;

//==================================================================
// Test: GetArticle - Invalid Article ID
//==================================================================
Dcl-Proc test_GetArticle_InvalidId Export;
  Dcl-Pi *N End-Pi;
  
  // Declarations
  Dcl-S articleId Char(6);
  Dcl-Ds article LikeDs(article_t);
  
  // Setup test data
  articleId = '999999';
  
  // Execute
  article = GetArticle(articleId);
  
  // Assert - Should return empty/null article
  aEqual(*Blank : article.ARID);
  
End-Proc;

//==================================================================
// Test: ValidateArticle - Valid Data
//==================================================================
Dcl-Proc test_ValidateArticle_Valid Export;
  Dcl-Pi *N End-Pi;
  
  // Declarations
  Dcl-Ds article LikeDs(article_t);
  Dcl-S result Ind;
  
  // Setup test data
  article.ARID = '000001';
  article.ARDESC = 'Test Article';
  article.ARSALEPR = 100.00;
  article.ARWHSPR = 50.00;
  article.ARTIFA = 'ELE';
  
  // Execute
  result = ValidateArticle(article);
  
  // Assert
  assert(result : 'Article should be valid');
  
End-Proc;

//==================================================================
// Test: ValidateArticle - Invalid Price
//==================================================================
Dcl-Proc test_ValidateArticle_InvalidPrice Export;
  Dcl-Pi *N End-Pi;
  
  // Declarations
  Dcl-Ds article LikeDs(article_t);
  Dcl-S result Ind;
  
  // Setup test data
  article.ARID = '000001';
  article.ARDESC = 'Test Article';
  article.ARSALEPR = -10.00;  // Invalid negative price
  article.ARWHSPR = 50.00;
  
  // Execute
  result = ValidateArticle(article);
  
  // Assert
  assert(Not result : 'Article with negative price should be invalid');
  
End-Proc;

//==================================================================
// Test: CalculatePrice - Standard VAT
//==================================================================
Dcl-Proc test_CalculatePrice_StandardVAT Export;
  Dcl-Pi *N End-Pi;
  
  // Declarations
  Dcl-S basePrice Packed(7:2);
  Dcl-S vatCode Char(1);
  Dcl-S result Packed(7:2);
  Dcl-S expected Packed(7:2);
  
  // Setup test data
  basePrice = 100.00;
  vatCode = '2';  // 20% VAT
  expected = 120.00;
  
  // Execute
  result = CalculatePrice(basePrice : vatCode);
  
  // Assert
  aEqual(expected : result);
  
End-Proc;

//==================================================================
// Test: CalculatePrice - Reduced VAT
//==================================================================
Dcl-Proc test_CalculatePrice_ReducedVAT Export;
  Dcl-Pi *N End-Pi;
  
  // Declarations
  Dcl-S basePrice Packed(7:2);
  Dcl-S vatCode Char(1);
  Dcl-S result Packed(7:2);
  Dcl-S expected Packed(7:2);
  
  // Setup test data
  basePrice = 100.00;
  vatCode = '1';  // 10% VAT
  expected = 110.00;
  
  // Execute
  result = CalculatePrice(basePrice : vatCode);
  
  // Assert
  aEqual(expected : result);
  
End-Proc;
```

---

## Step 4: Create Test Data Setup (2 minutes)

**Prompt for Bob:**
```
Create SQL statements to set up test data in SAMCO1
for running the article tests.
```

**What Happens:**
Bob generates test data setup:

```sql
-- Test Data Setup for ART300 Tests
DELETE FROM SAMCO1.ARTICLE WHERE ARID LIKE 'TEST%';

INSERT INTO SAMCO1.ARTICLE VALUES(
  '000001', 'Laptop Computer 15 inch', 899.99, 650.00,
  'ELE', 25, 5, 0, 0, '2',
  CURRENT DATE, CURRENT TIMESTAMP, 'ADMIN', ''
);

INSERT INTO SAMCO1.PARAMETER VALUES(
  'VAT', '2', '20.00', 'Standard VAT', '', 2, 20
);

INSERT INTO SAMCO1.PARAMETER VALUES(
  'VAT', '1', '10.00', 'Reduced VAT', '', 1, 10
);
```

---

## Step 5: Compile and Create Test Program (3 minutes)

**Prompt for Bob:**
```
Compile the test suite and create the ART300T test program.
```

**What Happens:**
Bob executes the compilation process in two steps:

### Step 5a: Create RPG Module

```cl
CRTRPGMOD MODULE(SAMCO1/ART300T) 
          SRCFILE(SAMCO1/QTESTSRC) 
          SRCMBR(ART300T)
          DBGVIEW(*SOURCE)
```

**This Creates:**
- Object: `SAMCO1/ART300T` 
- Type: `*MODULE`
- Purpose: Compiled test module ready for binding

### Step 5b: Create Test Program

```cl
CRTPGM PGM(SAMCO1/ART300T) 
       MODULE(SAMCO1/ART300T)
       BNDSRVPGM(RPGUNIT/RPGUNIT 
                 SAMCO1/ART300)
       ACTGRP(*NEW)
```

**This Creates:**
- Object: `SAMCO1/ART300T`
- Type: `*PGM`
- Purpose: Executable test program
- Binds to:
  - `RPGUNIT/RPGUNIT` - RPGUnit test framework
  - `SAMCO1/ART300` - Service program being tested

**Object Creation Summary:**

| Step | Command | Object Created | Type |
|------|---------|----------------|------|
| 1 | Bob generates | QTESTSRC/ART300T | Source member |
| 2 | CRTRPGMOD | SAMCO1/ART300T | *MODULE |
| 3 | CRTPGM | SAMCO1/ART300T | *PGM |

---

## Step 6: Run Unit Tests (2 minutes)

**Prompt for Bob:**
```
Run the RPGUnit tests and show me the results.
```

**What Happens:**
Bob executes:

```cl
RUCALLTST TSTPGM(SAMCO1/ART300T)
```

**How RUCALLTST Works:**
1. Loads the ART300T test program
2. Discovers all exported test procedures
3. Executes each test in sequence
4. Collects pass/fail results
5. Generates test report

**Expected Output:**
```
RPGUnit Test Results:

Test Suite: ART300T
Tests Run: 6
Passed: 6
Failed: 0
Errors: 0

✓ test_GetArticle_ValidId - PASSED
✓ test_GetArticle_InvalidId - PASSED
✓ test_ValidateArticle_Valid - PASSED
✓ test_ValidateArticle_InvalidPrice - PASSED
✓ test_CalculatePrice_StandardVAT - PASSED
✓ test_CalculatePrice_ReducedVAT - PASSED

All tests passed successfully!
```

---

## Step 7: Analyze Test Coverage (2 minutes)

**Prompt for Bob:**
```
Analyze the test coverage for ART300.
What procedures or code paths are not tested?
```

**What Happens:**
Bob analyzes and reports:

```
Test Coverage Analysis for ART300:

Tested Procedures (3/5):
✓ GetArticle - 2 test cases
✓ ValidateArticle - 2 test cases
✓ CalculatePrice - 2 test cases

Not Tested (2/5):
✗ UpdateArticle - No tests
✗ DeleteArticle - No tests

Recommendations:
1. Add tests for UpdateArticle procedure
2. Add tests for DeleteArticle procedure
3. Add edge case tests (null values, boundary conditions)
4. Add integration tests with database
```

---

## Understanding the ART300T Creation Process

### Complete Workflow

```
┌─────────────────────────────────────────────────────────────┐
│ 1. Source Analysis (Bob)                                    │
│    - Parse ART300.RPGLE                                     │
│    - Identify exported procedures                           │
└────────────────┬────────────────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────────────────┐
│ 2. Test Generation (Bob)                                    │
│    - Generate test stubs                                    │
│    - Create QTESTSRC/ART300T.RPGLE                         │
└────────────────┬────────────────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────────────────┐
│ 3. Module Compilation (CRTRPGMOD)                          │
│    - Compile test source                                    │
│    - Create SAMCO1/ART300T (*MODULE)                       │
└────────────────┬────────────────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────────────────┐
│ 4. Program Creation (CRTPGM)                               │
│    - Bind test module                                       │
│    - Link RPGUnit framework                                 │
│    - Link ART300 service program                           │
│    - Create SAMCO1/ART300T (*PGM)                          │
└────────────────┬────────────────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────────────────┐
│ 5. Test Execution (RUCALLTST)                              │
│    - Load test program                                      │
│    - Execute test procedures                                │
│    - Report results                                         │
└─────────────────────────────────────────────────────────────┘
```

### Key Components

**1. Test Source (ART300T.RPGLE):**
- Contains test procedures
- Marked with `Export` keyword
- Follows naming convention: `test_ProcedureName_Scenario`

**2. Test Module (ART300T *MODULE):**
- Compiled version of test source
- Not directly executable
- Ready for binding

**3. Test Program (ART300T *PGM):**
- Executable test program
- Binds test module + RPGUnit + ART300
- Runs in *NEW activation group for isolation

**4. RPGUnit Assertions:**
- `aEqual(expected : actual)` - Generic comparison (works for char, numeric)
- `iEqual(expected : actual)` - Integer/packed decimal comparison
- `assert(condition : message)` - Boolean condition with message

---

## Alternative Methods to Generate Tests

### Method 1: Using Slash Command (Fastest)
```
/ibmi-generate-rpgunit-test-suite SAMCO1/QRPGLESRC/ART300.RPGLE
```

### Method 2: Natural Language (Most Flexible)
```
Generate comprehensive RPGUnit test suite for ART300.RPGLE 
including setup, teardown, valid inputs, invalid inputs, 
edge cases, and integration tests
```

### Method 3: Step-by-Step (Most Control)
```
1. Analyze ART300.RPGLE and list exported procedures
2. Generate test stubs for GetArticle, ValidateArticle, CalculatePrice
3. Add setup/teardown procedures for test data
4. Add edge case tests for boundary values
5. Compile and run tests
```

---

## Running Tests Manually

After ART300T is created, you can run tests anytime:

**Basic Execution:**
```cl
RUCALLTST TSTPGM(SAMCO1/ART300T)
```

**With Options:**
```cl
RUCALLTST TSTPGM(SAMCO1/ART300T) 
          DETAIL(*BASIC)
          OUTPUT(*PRINT)
```

**With Code Coverage:**
```cl
RUCALLTST TSTPGM(SAMCO1/ART300T)
          COVRPT(*YES)
```

---

## ✅ Success Criteria

You've successfully completed this lab when:
- [x] Procedures identified for testing
- [x] RPGUnit test stubs generated
- [x] Test data setup created
- [x] Test module compiled (ART300T *MODULE)
- [x] Test program created (ART300T *PGM)
- [x] Tests executed successfully
- [x] Test coverage analyzed

---

## Key Takeaways

1. **Automate Test Generation**: Use Bob to create comprehensive test stubs
2. **Two-Step Compilation**: Module first (CRTRPGMOD), then program (CRTPGM)
3. **Proper Binding**: Link test module + RPGUnit + service program under test
4. **Test Isolation**: Use *NEW activation group for independent test execution
5. **Correct Assertions**: Use `aEqual()` for most comparisons (works for char and numeric)
6. **Test Early**: Write tests during development, not after
7. **Cover Edge Cases**: Test both valid and invalid inputs
8. **Setup Test Data**: Isolate tests with dedicated data
9. **Monitor Coverage**: Track what's tested and what's not

---

## Common Issues and Solutions

### Issue 1: Compilation Errors
**Problem:** Test module fails to compile  
**Solution:** Check include paths and prototype definitions

### Issue 2: Binding Errors
**Problem:** CRTPGM fails with unresolved references  
**Solution:** Verify BNDSRVPGM includes both RPGUNIT and service program under test

### Issue 3: Test Failures
**Problem:** Tests fail with assertion errors  
**Solution:** 
- Verify test data is set up correctly
- Check expected vs actual values
- Use correct assertion function (`aEqual` vs `iEqual` vs `assert`)

### Issue 4: Wrong Assertion Function
**Problem:** "Type of parameter 2 does not match prototype"  
**Solution:** Use `aEqual()` for most comparisons - it works for both character and numeric types

---

## Tools Used in This Lab

- `run_rpgle_parser` - Identify procedures to test
- `generate_rpg_unit_test_stub` - Create test stubs
- `run_cl_command` - Compile and run tests
- `run_sql_statement` - Setup test data
- `read_member` - Read source code

---

## RPGUnit Best Practices

1. **One Assert Per Test**: Keep tests focused on single behavior
2. **Descriptive Names**: Use clear test procedure names (test_Function_Scenario)
3. **Setup/Teardown**: Clean test data before/after each test
4. **Independent Tests**: Tests shouldn't depend on execution order
5. **Fast Tests**: Keep unit tests quick to run (< 1 second each)
6. **Use aEqual**: Generic assertion works for most data types
7. **Export All Tests**: Mark test procedures with `Export` keyword
8. **Document Tests**: Add comments explaining what's being tested

---

## Next Steps

- Generate tests for other service programs in SAMCO1
- Add integration tests that span multiple service programs
- Set up continuous testing in CI/CD pipeline
- Measure code coverage metrics with COVRPT(*YES)
- Create test data management procedures
- Return to Lab 106 for complete DevOps workflow

---

## Additional Resources

- RPGUnit Documentation: https://rpgunit.sourceforge.net/
- RPGUnit and vscode / Bob: https://codefori.github.io/docs/developing/testing/overview/
- IBM i Unit Testing Best Practices
- Bob IBM i Test Mode Documentation
- Code for IBM i Testing Guide 