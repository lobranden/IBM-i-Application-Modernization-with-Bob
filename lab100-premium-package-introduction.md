# IBM Bob Premium Package for i - Accelerating IBM i Development 

**Internal use only**

This guide introduces each IBM i specialized mode in Bob Premium Package with hands-on labs to master the tools and workflows.

The **IBM Bob Premium Package for i** provides seven specialized modes with built-in commands and tools for IBM i development. Six modes (DevOps, Developer, Modernization, Impact Analysis, Database Engineer, and Test) use built-in VSCode extension tools for local file operations, code analysis, and IBM i system commands. The seventh mode, IBM i Knowledge Assistant, leverages RAG (Retrieval-Augmented Generation) through an MCP (Model Context Protocol) server to search comprehensive IBM i documentation.

These labs complement the foundational [IBM Bob Core labs](../IBM-i-Application-Modernization-with-Bob/README.md) by demonstrating how the Premium Package delivers enhanced productivity through specialized modes, purpose-built commands, and optimized tools. The Premium Package provides superior accuracy for IBM i-specific tasks and can reduce token consumption by 10% or more compared to general-purpose modes.

## Labs Summary

| Lab | Title | Main Topic | Duration |
|-----|-------|------------|----------|
| [Lab 101](lab101-premium-devops-buildsamco.md) | IBM i DevOps - Build SAMCO Application | Build automation, deployment, CI/CD pipelines | 20 min | 
| [Lab 102](lab102-premium-developer-compile.md) | IBM i Developer - Code Explanation and Compilation | Code generation, compilation, documentation | 15 min | 
| [Lab 103](lab103-premium-modernization-dds-to-sql.md) | IBM i Modernization - Convert DDS to SQL | Convert legacy code, refactor, modernize | 15 min | 
| [Lab 104](lab104-premium-impact-analysis.md) | IBM i Impact Analysis - Analyze Object Dependencies | Analyze dependencies, assess change impact | 15 min | 
| [Lab 105](lab105-premium-database-engineer.md) | IBM i Database Engineer - SQL Development | Database design, SQL development, optimization | 15 min | 
| [Lab 106](lab106-premium-test-rpgunit.md) | IBM i Test - Generate RPGUnit Test Cases | Unit testing, test case generation | 15 min | 
| [Lab 107](lab107-premium-knowledge-assistant.md) | IBM i Knowledge Assistant Mode | Research documentation, MCP and RAG | 10 min | 
| | | **Total Duration** | **1 h 45 min** | |

---

## IBM i DevOps Mode (♾️)

**Purpose**: Build automation, deployment, CI/CD pipelines, system operations

**Lab**: [Lab 101 - Build SAMCO Application with DevOps Mode](lab101-premium-devops-buildsamco.md)

### Library & Object Management
- `create_library` - Create libraries on IBM i
- `create_source_file` - Create source physical files
- `create_member` - Create source members in QSYS

### Build & Deployment
- `run_pase_command` - Execute PASE commands (makei, Git, bash, etc.)
- `run_cl_command` - Execute CL commands (CRTPGM, CRTSRVPGM, etc.)
- `run_cl_syntax_checker` - Validate CL commands before execution
- `get_cl_command_doc` - Get official IBM i command documentation

### Database Operations
- `run_sql_statement` - Execute SQL DDL/DML statements
- `run_sql_syntax_checker` - Validate SQL before execution
- `get_database_objects` - List schemas, tables, views, procedures
- `generate_sql` - Generate DDL from existing objects

### File Operations (QSYS)
- `read_member` - Read source member content
- `write_member` - Write to source members
- `search_qsys` - Search libraries, files, members with patterns

### File Operations (IFS)
- `create_stream_file` - Create IFS files
- `create_directory` - Create IFS directories
- `read_stream_file` - Read IFS file content
- `write_stream_file` - Write to IFS files
- `search_ifs` - Search IFS directories and files

### System Configuration
- `get_connection_settings` - Get IBM i connection details
- `set_connection_settings` - Modify library list, current library
- `get_sql_job` - Get SQL job information
- `set_sql_job_jdbc_options` - Configure SQL job settings

**Quick Start**: Switch to DevOps mode and ask Bob to "Build the SAMCO application in library SAMCO1"

---

## IBM i Developer Mode (ℹ️)

**Purpose**: Code generation, compilation, documentation, database development

**Lab**: [Lab 102 - Compile and Document RPG Program](lab102-premium-developer-compile.md)

### Code Generation & Analysis
- `read_member` - Read RPG, SQL, CL, COBOL, DDS source
- `write_member` - Write source code to members
- `run_rpgle_parser` - Parse RPGLE for procedures, variables, files
- `run_cl_parser` - Parse CL for commands, variables, subroutines
- `run_dds_parser` - Parse DDS for record formats, fields
- `get_display_file_preview` - Generate text preview of DDS display files

### Compilation & Build
- `run_cl_command` - Compile programs (CRTBNDRPG, CRTSQLRPGI, CRTBNDCL, etc.)
- `run_cl_syntax_checker` - Validate CL commands
- `get_cl_command_doc` - Get command documentation with parameters

### Database Development
- `run_sql_statement` - Execute SQL DDL/DML
- `run_sql_syntax_checker` - Validate SQL syntax
- `get_database_objects` - List tables, views, indexes, procedures, triggers
- `generate_sql` - Generate DDL from physical/logical files
- `get_related_objects` - Find object dependencies
- `get_sql_examples` - Access SQL reference examples (IBM i Services, etc.)
- `get_sql_job` - Get current SQL job settings
- `set_sql_job_jdbc_options` - Configure naming, libraries, date format
- `get_active_schemas` - Get Schema Browser schemas

### Testing
- `generate_rpg_unit_test_stub` - Create RPGUnit test stubs for procedures

### Search & Discovery
- `search_qsys` - Search source code with regex patterns
- `search_ifs` - Search IFS files
- `list_code_definition_names` - List functions, classes, procedures

**Quick Start**: Switch to Developer mode and ask Bob to "Compile program ART300 in SAMCO1 with validation"

---

## IBM i Modernization Mode (💡)

**Purpose**: Convert legacy code, refactor, modernize applications

**Lab**: [Lab 103 - Convert DDS to SQL](lab103-premium-modernization-dds-to-sql.md)

### Code Analysis
- `read_member` - Read legacy source (Fixed RPG, OPM CL, DDS)
- `read_stream_file` - Read IFS source files
- `run_rpgle_parser` - Analyze RPG structure (Fixed/Free)
- `run_cl_parser` - Analyze CL structure (OPM/ILE)
- `run_dds_parser` - Analyze DDS file definitions
- `get_display_file_preview` - Understand DDS screen layouts

### Code Transformation
- `write_member` - Write modernized code
- `write_stream_file` - Write to IFS
- `apply_diff` - Apply surgical code changes
- `insert_content` - Add new code sections

### Conversion Tools
- `generate_sql` - Convert DDS to DDL (SQL tables/views)
- `run_cl_syntax_checker` - Validate modernized CL
- `run_sql_syntax_checker` - Validate converted SQL

### Search & Refactoring
- `search_qsys` - Find code patterns to modernize
- `search_ifs` - Search modernized code
- `search_files` - Search local workspace files

### Database Modernization
- `get_database_objects` - Analyze existing database structure
- `get_related_objects` - Understand dependencies before changes
- `run_sql_statement` - Create modern SQL objects

**Quick Start**: Switch to Modernization mode and ask Bob to "Convert ARTICLE file from DDS to SQL DDL"

---

## IBM i Impact Analysis Mode (🔗)

**Purpose**: Analyze dependencies, assess change impact, understand relationships

**Lab**: [Lab 104 - Analyze Object Dependencies](lab104-premium-impact-analysis.md)

### Dependency Analysis
- `get_related_objects` - Find all objects dependent on a file/table
- `get_database_objects` - List all database objects in schemas
- `generate_sql` - Analyze object definitions

### Code Analysis
- `run_rpgle_parser` - Identify procedure calls, file usage, variables
- `run_cl_parser` - Identify command usage, program calls
- `run_dds_parser` - Identify field usage, key fields
- `read_member` - Analyze source code for references
- `read_stream_file` - Analyze IFS source

### Search & Discovery
- `search_qsys` - Search for references across libraries
- `search_ifs` - Search for references in IFS
- `search_files` - Search local workspace

### System Catalog Queries
- `run_sql_statement` - Query system catalogs:
  - `QSYS2.SYSTABLES` - Table information
  - `QSYS2.SYSCOLUMNS` - Column definitions
  - `QSYS2.SYSDEP` - Object dependencies
  - `QSYS2.SYSROUTINEDEP` - Procedure dependencies
  - `QSYS2.SYSPROGRAMSTAT` - Program statistics
  - `QSYS2.SYSPARTITIONSTAT` - Table statistics

### Object Information
- `run_cl_command` - Display object details:
  - `DSPOBJD` - Display object description
  - `DSPDBR` - Display database relations
  - `DSPFFD` - Display file field description
  - `DSPPGMREF` - Display program references

**Quick Start**: Switch to Impact Analysis mode and ask Bob to "Find all objects dependent on ARTICLE file"

---

## IBM i Database Engineer Mode (🛢️)

**Purpose**: Database design, SQL development, performance optimization

**Lab**: [Lab 105 - SQL Development and Optimization](lab105-premium-database-engineer.md)

### Database Development
- `run_sql_statement` - Execute DDL/DML statements
- `run_sql_syntax_checker` - Validate SQL syntax
- `get_sql_examples` - Access SQL reference library
- `generate_sql` - Generate DDL from existing objects

### Database Analysis
- `get_database_objects` - List all database objects (tables, views, indexes, etc.)
- `get_related_objects` - Analyze dependencies
- `get_active_schemas` - Get working schemas

### SQL Job Management
- `get_sql_job` - Get current SQL job configuration
- `set_sql_job_jdbc_options` - Configure naming, libraries, formats
- `get_self_codes` - Get SQL error log entries
- `reset_self_codes` - Reset SQL error logs

### DDS to SQL Conversion
- `run_dds_parser` - Parse DDS definitions
- `generate_sql` - Convert DDS to DDL
- `get_display_file_preview` - Understand DDS layouts

**Quick Start**: Switch to Database Engineer mode and ask Bob to "Create a SQL view for article reporting"

---

## IBM i Test Mode (🎯)

**Purpose**: Unit testing, test case generation, code coverage

**Lab**: [Lab 106 - Generate RPGUnit Test Cases](lab106-premium-test-rpgunit.md)

### Test Generation
- `generate_rpg_unit_test_stub` - Create RPGUnit test stubs
- `read_member` - Read source to test
- `write_member` - Write test cases
- `run_rpgle_parser` - Identify procedures to test

### Test Execution
- `run_cl_command` - Run RPGUnit tests (RUCALLTST)
- `run_sql_statement` - Setup test data
- `get_database_objects` - Verify test database state

**Quick Start**: Switch to Test mode and ask Bob to "Generate RPGUnit tests for ART300 service program"

---

## IBM i Knowledge Assistant Mode (📖)

**Purpose**: Research IBM i documentation, learn concepts, get technical guidance

**Lab**: [Lab 107 - IBM i Knowledge Assistant with MCP and RAG](lab107-premium-knowledge-assistant.md)

### Knowledge Retrieval (MCP + RAG)
This mode uses **Model Context Protocol (MCP)** to connect to an external documentation server with **RAG (Retrieval-Augmented Generation)** technology. It searches a vector database of IBM i documentation to provide accurate, contextual answers.

### Documentation Sources
- IBM Knowledge Center (official IBM i documentation)
- IBM Redbooks (technical guides and best practices)
- IBM i Services documentation
- Community resources and technical articles

---

## IBM Bob : Common Tools (All Modes)

### Local File Operations
- `read_file` - Read local workspace files
- `write_to_file` - Write local files
- `list_files` - List directory contents
- `search_files` - Search with regex patterns
- `list_code_definition_names` - List code definitions

### General Operations
- `execute_command` - Run local terminal commands
- `create_temporary_file` - Create temp files for editing
- `ask_followup_question` - Request user input
- `attempt_completion` - Complete task

### Version Control
- `obtain_git_diff` - Get git differences
- `fetch_github_issue` - Fetch GitHub issues
- `generate_description_from_diff` - Generate PR descriptions
- `create_pull_request` - Create pull requests

---

*Bob Premium Package for IBM i - Accelerating IBM i Development*