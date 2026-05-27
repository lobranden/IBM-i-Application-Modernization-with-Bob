# Lab 101: IBM i DevOps - Build SAMCO Application with Bob Premium

**Internal use only**

## Overview
Learn how to use Bob's IBM i DevOps mode to build and deploy the SAMCO application using modern build automation tools on IBM i.

**Duration**: 20 minutes  
**Difficulty**: Intermediate  
**What You'll Build**: Complete SAMCO application in a new library using makei build automation

---

## Prerequisites
- Access to IBM i system via Code for IBM i extension
- Bob Premium Package for i installed
- SAMCO project files in workspace
- IBM i connection configured in VS Code

---

## Use Case: Automated Build and Database Setup

We'll use Bob's DevOps mode to:
1. Configure deployment directory
2. Create a new library (SAMCO1) and set as current library
3. Add recommended .gitignore file
4. Build the complete SAMCO application using Bob's automated build
5. Populate the database with sample data

---

## Step 1: Configure Deploy Directory (2 minutes)

**Action:**
1. In VS Code, open the **Code for IBM i** view
2. Connect to your IBM i system
3. Set your deploy directory following the [Project Explorer documentation](https://ibm.github.io/vscode-ibmi-projectexplorer/#/pages/projectExplorer/source-and-deployment?id=set-the-deploy-location)
4. Choose target IFS directory: `/home/<USERNAME>/builds/SAMCO/`

**What This Does:**
- Configures automatic deployment from local workspace to IBM i IFS
- Enables Bob to deploy and build in one seamless operation
- Makes source files accessible for build automation

**Why This Matters:**
Setting the deploy directory allows Bob to handle both deployment and building automatically, eliminating manual sync steps.

---

## Step 2: Switch to IBM i DevOps Mode (2 minutes)

**Action:**
1. Open Bob chat interface
2. Switch to **IBM i DevOps** mode (♾️)
3. Verify connection to IBM i system

**What This Mode Does:**
- Executes CL commands on IBM i
- Runs PASE commands (makei, Git, etc.)
- Manages build automation
- Handles deployment tasks

---

## Step 3: Create Library and Set as Current Library (3 minutes)

**Prompt for Bob:**
```
Create a new library called SAMCO1 on IBM i for the SAMCO application build and set this as my current library.
```

**What Happens:**
1. Bob uses `get_i_project` tool to understand your project configuration
2. Bob creates the library: `CRTLIB LIB(SAMCO1) TEXT('SAMCO Application Build')`
3. Bob uses `set_i_project` tool to set the `lib1` variable in your local `.env` file
4. Library SAMCO1 is now your current library for builds

**Behind the Scenes:**
Bob intelligently manages both the IBM i system (creating the library) and your local project configuration (updating .env), ensuring everything is properly configured for the build process.

---

## Step 4: Add Recommended .gitignore (2 minutes)

**Prompt for Bob:**
```
Create a .gitignore file in the SAMCO project root with the following entries:
.evfevent
.logs
.itest
.env
```

**What This Does:**
- Excludes build artifacts and logs from version control
- Protects local environment configuration (.env)
- Follows IBM i Project best practices

**Why This Matters:**
These files are generated during builds and contain local-specific information that shouldn't be committed to your repository.

---

## Step 5: Build the Application (8 minutes)

**Prompt for Bob:**
```
Build my application.
```

**What Happens:**
1. Bob uses the `run_build_or_compile` tool to automatically:
   - Deploy your source files to the IFS
   - Invoke the TOBi build system
   - Execute: `makei build` with your configured library (SAMCO1)
2. The makei tool processes all Rules.mk files
3. Creates 121 objects in SAMCO1:
   - Database files (PF, LF, DSPF, PRTF)
   - Programs (RPG, COBOL, CL, C/C++)
   - Service programs
   - Commands, message files, menus

**Note:** If the project structure is complex, you can specify the IFS path  of the application to build. For example: `/home/<USERNAME>/builds/SAMCO/`

**Build Process:**
You'll see the build happening in real-time, just like a manual build:
```
> Deploying source files...
> Running makei build in SAMCO1...
=== Creating RPG module [XML001.RPGLE]
✓ XML001.MODULE was created successfully!

=== Creating service program [TXT]
✓ TXT.SRVPGM was created successfully!

=== Creating PF [ARTICLE.PF]
✓ ARTICLE.FILE was created successfully!

... (121 objects total)

Objects: 0 failed 121 succeed 121 total
Build Completed!
```

**Key Build Tools:**
- **makei**: Modern build automation for IBM i
- **Rules.mk**: Makefile-style build rules
- **iproj.json**: Project configuration
- **run_build_or_compile**: Bob's tool that handles deployment + build in one operation

**Build Artifacts:**
- `.evfevent` files: Event logs from the build process
- `.logs` directory: Detailed job logs for troubleshooting
- These artifacts are automatically generated and help with debugging build failures

---

## Step 6: Understand the Build Output (3 minutes)

**Prompt for Bob:**
```
Explain what objects were created in SAMCO1 and their purpose.
```

**Expected Response:**
Bob will categorize the 121 objects:

**Database Layer:**
- Physical Files: ARTICLE, CUSTOMER, PROVIDER, ORDER, DETORD
- Logical Files: ARTICLE1, CUSTOME1, ORDER2 (indexed access)
- Display Files: ART200D, CUS200D, ORD200D (green screen UI)

**Business Logic:**
- Service Programs: FARTICLE, FCUSTOMER, FPROVIDER (reusable functions)
- Programs: ART200, CUS200, ORD200 (application logic)

**Supporting Objects:**
- Commands: CRTORD (create order)
- Message Files: SAMMSGF (error messages)
- Data Areas: LASTORDNO (order counter)

---

## Step 7: Populate the Database (4 minutes)

**Prompt for Bob:**
```
Populate the SAMCO1 database tables with sample data using:
SAMCO/POPULATE_SAMCO_TABLES.sql
```

**What Happens:**
Bob executes the SQL script to insert sample data:
- 10 countries, 10 families, 33 articles
- 10 providers, 10 customers, 10 orders
- Article-provider relationships and parameters

**Verification:**
```
Query the data to verify:
SELECT COUNT(*) FROM SAMCO1.ARTICLE;
SELECT * FROM SAMCO1.CUSTOMER;
```

---

## ✅ Success Criteria

You've successfully completed this lab when:
- [ ] Library SAMCO1 created on IBM i
- [ ] 121 objects built successfully (0 failures)
- [ ] Database tables populated with sample data
- [ ] You understand the build automation process

---

## Key Takeaways

1. **DevOps Mode**: Bob can execute commands directly on IBM i
2. **Build Automation**: makei provides modern build workflows
3. **Complete Deployment**: One command builds entire application
4. **Database Setup**: SQL scripts automate data population
5. **Zero Failures**: Modern tools ensure reliable builds

---

## Behind the Scenes: Tools Used

**Code for IBM i Extension:**
- Provides IBM i connection from VS Code
- Executes CL and PASE commands
- Manages library lists and current library

**makei Build Tool:**
- Reads Rules.mk files in each directory
- Determines build order based on dependencies
- Creates objects using native IBM i commands
- Provides colored output and error reporting

**Bob DevOps Mode:**
- Orchestrates build commands
- Monitors build progress
- Reports success/failure
- Handles error recovery

---

## Bob's DevOps Troubleshooting Capabilities

Bob can help diagnose and fix build issues using these tools:

**System Commands:**
```
Check system status: WRKSYSSTS
Display objects: DSPOBJD OBJ(SAMCO1/*ALL) OBJTYPE(*PGM)
Work with jobs: WRKACTJOB
Display library: DSPLIB LIB(SAMCO1)
```

**PASE Commands:**
```
Check environment: uname -a
List files: ls -la /home/BENOIT/builds/
View build logs: cat build.log
Check permissions: ls -l /QSYS.LIB/SAMCO1.LIB/
```

**SQL Queries:**
```
Verify tables: SELECT * FROM QSYS2.SYSTABLES WHERE TABLE_SCHEMA='SAMCO1'
Check data: SELECT COUNT(*) FROM SAMCO1.ARTICLE
View dependencies: SELECT * FROM QSYS2.SYSDEP WHERE BNAME='SAMCO1'
```

**Build Diagnostics:**
- Bob can read build output and identify specific compilation errors
- Analyzes `.evfevent` files and `.logs` directory for detailed error information
- Suggests fixes for missing dependencies or authority issues
- Can re-run failed object creation individually
- Verifies object creation with DSPOBJD commands

**Example Troubleshooting Session:**
```
User: "The build failed for program ART200"
Bob: Analyzes .evfevent files and job logs
Bob: Identifies missing service program FARTICLE
Bob: Verifies FARTICLE exists: DSPOBJD OBJ(SAMCO1/FARTICLE) OBJTYPE(*SRVPGM)
Bob: Rebuilds ART200 with correct binding directory
```

**Using @Problems for Quick Fixes:**
When build failures occur, you can use VS Code's Problems panel:
```
User: "Fix @Problems"
Bob: Analyzes all compilation errors from the Problems panel
Bob: Suggests and applies fixes for each issue
Bob: Re-runs the build to verify fixes
```

This approach is especially powerful because Bob can see all errors at once and address them systematically.

---

## Next Steps

- Explore the built objects: `DSPOBJD OBJ(SAMCO1/*ALL)`
- Run the application: `CALL SAMCO1/ART200`
- Query the data: `SELECT * FROM SAMCO1.ARTICLE`
- Try Lab 102 IBM i Developer mode
 