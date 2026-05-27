# Lab 102: IBM i Developer - Code Explanation and Compilation

## Overview
Learn how to use Bob's IBM i Developer mode to copy source code to IBM i, explain code, validate compilation commands, and understand compilation errors.

**Duration**: 15 minutes  
**Difficulty**: Beginner  
**What You'll Learn**: Copy sources to IBM i, explain code, validate and compile programs

---

## Prerequisites
- Access to IBM i system via Code for IBM i extension
- Bob Premium Package for i installed
- SAMCO application in local workspace

---

## Exercise 1: Prepare Source Members on IBM i (3 minutes)

**Objective**: Copy local source files to IBM i source members in library SAMSRC

**Prompt for Bob:**
```
Copy these local workspace files to IBM i:
1. SAMCO/QRPGLESRC/ART300-Function_Article.RPGLE → SAMSRC/QRPGLESRC/ART300
2. SAMCO/QRPGLESRC/CUS200.PGM.SQLRPGLE → SAMSRC/QRPGLESRC/CUS200

Create library SAMSRC and source file QRPGLESRC with CCSID 37 if they don't exist.
```

**What Happens:**
1. Bob creates library SAMSRC
2. Creates source physical file QRPGLESRC with CCSID 37
3. Creates members ART300 and CUS200
4. Copies source code to members

**Expected Output:**
```
✓ Library SAMSRC created
✓ Source file QRPGLESRC created with CCSID 37
✓ Member ART300 created (135 records)
✓ Member CUS200 created (XXX records)
✓ Source files copied successfully
```

**Tools Used:**
- `create_library` - Create SAMSRC library
- `create_source_file` - Create QRPGLESRC with CCSID 37
- `create_member` - Create source members
- `write_member` - Copy source content

---

## Exercise 2: Explain Code Using /ibmi-explain (4 minutes)

**Objective**: Get concise explanations of the two source members

### 2a. Explain CUS200 Program

**Prompt for Bob:**
```
/ibmi-explain /SAMSRC/QRPGLESRC/CUS200.SQLRPGLE

Provide a concise explanation of this customer maintenance program.
```

**Expected Output:**
```
Overview:
CUS200 is an interactive customer maintenance program that allows users to 
view, add, update, and delete customer records using a subfile interface.

Key Components:
- Display file: CUS200D with subfile for customer list
- Database files: CUSTOMER1 (update), CUSTOMER2 (read)
- Main functionality: Customer CRUD operations
- Navigation: Subfile with selection options

Program Flow:
1. Load customer records into subfile
2. Display subfile for user selection
3. Process user actions (add/change/delete)
4. Update database accordingly
```

**Note:**: 

Bob analyzes IBM i programs by reading the source code and automatically retrieving any dependencies (such as copybooks or external artifacts) needed for complete context. When a dependency cannot be accessed from the IBM i system, Bob seamlessly falls back to reading the corresponding file from the local workspace, ensuring comprehensive code analysis regardless of source location.

**Optional:**: 

Ask Bob to explain ART300 program, GetArtDesc procedure. 

---

## Exercise 3: Validate CUS200 Compilation (2 minutes)

**Objective**: Validate compilation command syntax before executing

**Prompt for Bob:**
```
Validate this compilation command for CUS200:
CRTBNDRPG PGM(SAMSRC/CUS200) SRCFILE(SAMSRC/QRPGLESRC) SRCMBR(CUS200) DBGVIEW(*ALL)
```

Bob gives some recommendations. Then try :
```
Validate this compilation command for CUS200:
CRTSQLRPGI OBJ(SAMSRC/CUS200) SRCFILE(SAMSRC/QRPGLESRC) SRCMBR(CUS200) DBGVIEW(*ALL) COMMIT(*NONE)
```

**What Happens:**
Bob uses `run_cl_syntax_checker` to verify:
- Command syntax is correct
- All required parameters present
- No conflicting options

**Expected Output:**
```
✓ Command syntax is valid, or Bob provides some recommendations
✓ All required parameters present
✓ Fix the command, iterate, to get a command that is ready to execute
```

**Tools Used:**
- `run_cl_syntax_checker` - Validate CL command syntax

---

## Exercise 4: Validate ART300 Compilation and Understand Error (3 minutes)

**Objective**: Attempt to compile ART300 and understand why it fails

**Prompt for Bob:**
```
Please analyze source structure of ART300 , and validate this compilation command for ART300:
CRTBNDRPG PGM(SAMSRC/ART300) SRCFILE(SAMSRC/QRPGLESRC) SRCMBR(ART300) DBGVIEW(*ALL)
```

**What Happens:**
1. Bob validates the command syntax (passes)
2. Explains that ART300 cannot be compiled as a program

**Expected Output:**
```
✓ Command syntax is valid

⚠️ Compilation Issue Detected:
ART300 contains NOMAIN keyword - this is a service program MODULE, not a program.

Correct Approach:
1. Use CRTRPGMOD to create module:
   CRTRPGMOD MODULE(SAMSRC/ART300) SRCFILE(SAMSRC/QRPGLESRC) SRCMBR(ART300)

2. Then bind into service program:
   CRTSRVPGM SRVPGM(SAMSRC/FARTICLE) MODULE(SAMSRC/ART300) EXPORT(*ALL)

Key Difference:
- CRTBNDRPG: Creates standalone programs (with main procedure)
- CRTRPGMOD + CRTSRVPGM: Creates reusable service programs (NOMAIN modules)
```

**Tools Used:**
- `run_cl_syntax_checker` - Validate command syntax
- `run_rpgle_parser` - Analyze source structure
- `get_cl_command_doc` - Provide command documentation

---
## ✅ Success Criteria

You've successfully completed this lab when:
- [ ] Source files copied to SAMSRC library on IBM i
- [ ] CUS200 program explained concisely
- [ ] ART300 first procedure explained
- [ ] CUS200 compilation command validated
- [ ] ART300 compilation issue understood

---

## Key Takeaways

1. **Prepare Sources**: Copy local files to IBM i for compilation
2. **Explain Code**: Use `/ibmi-explain` for quick code understanding
3. **Validate First**: Always check command syntax before execution
4. **Understand Errors**: Service program modules need different compilation approach

---

## Tools Reference

| Tool | Purpose | Exercise |
|------|---------|----------|
| `create_library` | Create IBM i library | 1 |
| `create_source_file` | Create source physical file | 1 |
| `create_member` | Create source member | 1 |
| `write_member` | Write content to member | 1 |
| `read_member` | Read member content | 2 |
| `/ibmi-explain` | Explain code | 2 |
| `run_cl_syntax_checker` | Validate CL commands | 3, 4 |
| `run_rpgle_parser` | Analyze RPG structure | 4 |
| `get_cl_command_doc` | Get command documentation | 4, 5 |

---

## Next Steps

- Compile CUS200 successfully using validated command
- Create ART300 module and service program
- Explore other SAMCO programs
- Move to Lab 103 for code modernization