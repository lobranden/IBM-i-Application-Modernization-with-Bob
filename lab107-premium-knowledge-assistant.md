# Lab 107 - IBM i Knowledge Assistant Mode

**Duration**: 10 minutes  
**Mode**: 📖 IBM i Knowledge Assistant  
**Objective**: Learn to use the IBM i Knowledge Assistant mode powered by MCP and RAG to search IBM i documentation and get intelligent answers to technical questions.

---

## Overview

The **IBM i Knowledge Assistant** mode is unique among the Premium Package modes because it leverages **MCP (Model Context Protocol)** and **RAG (Retrieval-Augmented Generation)** technology to search a comprehensive vector database of IBM i documentation. Unlike the other six modes that use built-in VSCode extension tools for local operations, this mode connects to an external knowledge base to provide accurate, context-aware answers to IBM i technical questions.

### What Makes This Mode Special?

- **MCP Server Integration**: Connects to an external documentation server via Model Context Protocol
- **Vector Database Search**: Uses semantic search across IBM i documentation, IBM Redbooks, Knowledge Center articles, and technical resources
- **RAG Technology**: Retrieves relevant documentation chunks and generates accurate, contextual answers
- **No Local File Editing**: Read-only mode focused on knowledge retrieval and technical guidance

---

## Prerequisites

- IBM Bob Premium Package installed
- MCP server configured for IBM i documentation (see configuration section)
- Active internet connection for documentation queries

---

## Lab Exercises

### Exercise 1: Understanding IBM i Concepts (3 minutes)

**Scenario**: You need to understand the difference between physical files and SQL tables on IBM i.

**Steps**:

1. Switch to **IBM i Knowledge Assistant** mode (📖)
2. Ask Bob: "What is the difference between DDS physical files and SQL tables on IBM i?"
3. Review the comprehensive answer with references to IBM documentation
4. Follow up with: "When should I use SQL tables instead of physical files?"

**Expected Outcome**: Detailed explanation with best practices and migration considerations.

---

### Exercise 2: Command Syntax and Parameters (3 minutes)

**Scenario**: You need to understand the CRTSQLRPGI command parameters for compiling SQL RPG programs.

**Steps**:

1. Ask Bob: "Explain the CRTSQLRPGI command parameters, especially COMMIT, CLOSQLCSR, and DBGVIEW"
2. Review the parameter descriptions and valid values
3. Ask: "What are the best practices for CRTSQLRPGI compilation options?"

**Expected Outcome**: Detailed parameter explanations with recommended settings for different scenarios.

---

### Exercise 3: Performance and Optimization (2 minutes)

**Scenario**: You want to optimize SQL query performance on IBM i.

**Steps**:

1. Ask Bob: "How do I analyze and optimize SQL query performance on IBM i?"
2. Review the suggested IBM i Services and tools
3. Ask: "What are the key indexes and statistics I should maintain?"

**Expected Outcome**: Guidance on Visual Explain, index advisors, and IBM i Services for performance monitoring.

---

### Exercise 4: Modernization Guidance (2 minutes)

**Scenario**: You're planning to modernize a legacy RPG application.

**Steps**:

1. Ask Bob: "What are the recommended steps for modernizing fixed-format RPG to free-format?"
2. Review the modernization roadmap
3. Ask: "What tools are available for automated RPG conversion?"

**Expected Outcome**: Step-by-step modernization approach with tool recommendations and best practices.

---

## MCP Server Configuration

The IBM i Knowledge Assistant mode requires an MCP server configured with IBM i documentation. The server provides:

- **IBM Knowledge Center**: Official IBM i documentation
- **IBM Redbooks**: Technical guides and best practices
- **IBM i Services**: SQL services documentation
- **Community Resources**: Forums, blogs, and technical articles

### Configuration File Example

```json
{
  "mcpServers": {
    "ibmi-docs": {
      "command": "node",
      "args": ["/path/to/ibmi-mcp-server/index.js"],
      "env": {
        "VECTOR_DB_PATH": "/path/to/ibmi-docs.db"
      }
    }
  }
}
```

---

## Key Differences from Other Modes

| Aspect | IBM i Knowledge Assistant | Other Premium Modes |
|--------|--------------------------|---------------------|
| **Architecture** | MCP + RAG + Vector DB | Built-in VSCode tools |
| **Data Source** | External documentation | Local files + IBM i system |
| **Primary Use** | Research & learning | Development & operations |
| **File Operations** | Read-only | Read/write capabilities |
| **Network Required** | Yes (MCP server) | No (local tools) |

---

## When to Use This Mode

✅ **Use IBM i Knowledge Assistant when you need to**:
- Research IBM i concepts and features
- Understand command syntax and parameters
- Learn best practices and design patterns
- Find IBM Redbook recommendations
- Get guidance on modernization approaches
- Understand error messages and troubleshooting

❌ **Switch to other modes when you need to**:
- Write or modify code (use Developer or Modernization mode)
- Compile programs (use Developer mode)
- Build applications (use DevOps mode)
- Analyze dependencies (use Impact Analysis mode)
- Design databases (use Database Engineer mode)
- Create tests (use Test mode)

---

## Best Practices

1. **Ask Specific Questions**: The more specific your question, the more accurate the answer
2. **Include Context**: Mention your IBM i version, environment, or specific scenario
3. **Follow Up**: Ask clarifying questions to dive deeper into topics
4. **Verify with Official Docs**: Use the mode to find documentation, then verify on IBM Knowledge Center
5. **Combine with Other Modes**: Use Knowledge Assistant for research, then switch to appropriate mode for implementation

---

## Common Use Cases

### Use Case 1: Learning New Features
```
Question: "What are the new SQL features in IBM i 7.5?"
Result: Comprehensive list with examples and migration considerations
```

### Use Case 2: Troubleshooting
```
Question: "What does SQL error code -204 mean and how do I fix it?"
Result: Error explanation, common causes, and resolution steps
```

### Use Case 3: Architecture Decisions
```
Question: "Should I use stored procedures or service programs for business logic?"
Result: Comparison with pros/cons and architectural guidance
```

### Use Case 4: Migration Planning
```
Question: "What are the steps to migrate from DDS to DDL?"
Result: Step-by-step migration guide with tools and best practices
```

---

## Tips for Effective Queries

1. **Be Specific**: Instead of "How do I use SQL?", ask "How do I create a stored procedure with parameters on IBM i?"
2. **Provide Context**: Mention your environment, version, or constraints
3. **Ask Follow-ups**: Drill down into specific aspects of the answer
4. **Request Examples**: Ask for code examples or command syntax
5. **Seek Best Practices**: Ask about recommended approaches and patterns

---

## Summary

The IBM i Knowledge Assistant mode is your AI-powered research assistant for IBM i development. By leveraging MCP and RAG technology, it provides instant access to comprehensive IBM i documentation, enabling you to:

- Learn IBM i concepts quickly
- Find accurate command syntax
- Discover best practices
- Plan modernization projects
- Troubleshoot issues effectively

Remember: This mode is for **knowledge retrieval and guidance**. For actual development work, switch to the appropriate specialized mode (Developer, DevOps, Modernization, etc.).

---

## Next Steps

- Explore other Premium Package modes: [Lab 100 - Premium Package Introduction](lab100-premium-package-introduction.md)
- Practice with real scenarios in your IBM i environment
- Combine Knowledge Assistant with other modes for complete workflows
- Build your IBM i expertise with AI-powered assistance

---

*IBM Bob Premium Package - Your AI-Powered IBM i Knowledge Base*