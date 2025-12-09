# MCP Integration Patterns for Claude Code

> **Last Updated**: December 2025
> **Focus**: Real-world patterns for combining MCP servers effectively

## Pattern 1: The Research Assistant Stack

**Use Case**: Deep research, analysis, and documentation tasks

```bash
# Search and discovery
claude mcp add brave -s user -- npx -y @anthropic/mcp-server-brave-search
claude mcp add fetch -s user -- npx -y @modelcontextprotocol/server-fetch

# Reasoning and structure
claude mcp add sequential-thinking -s user -- npx -y @modelcontextprotocol/server-sequential-thinking

# Knowledge persistence
claude mcp add memory -s user -- npx -y @modelcontextprotocol/server-memory

# Document storage
claude mcp add filesystem -s user -- npx -y @modelcontextprotocol/server-filesystem ~/Research ~/Documents
```

**Workflow**:
```
1. User: "Research the latest trends in serverless architecture"
2. Claude:
   - Uses brave_search to find current articles
   - Uses fetch to retrieve full content
   - Uses sequential_thinking for structured analysis
   - Stores key findings in memory for future reference
   - Writes summary to filesystem
```

---

## Pattern 2: The Full-Stack Developer Stack

**Use Case**: End-to-end application development

```bash
# Documentation on demand
claude mcp add context7 -s user -- npx -y @upstash/context7-mcp@latest

# Version control
claude mcp add git -s user -- npx -y @modelcontextprotocol/server-git
claude mcp add github -s user -- npx -y @modelcontextprotocol/server-github

# Database
claude mcp add supabase -s project -- npx -y supabase-mcp-server

# Testing
claude mcp add playwright -s user -- npx -y @executeautomation/playwright-mcp-server

# Codebase search
claude mcp add claude-context -s project -- npx -y @zilliztech/claude-context
```

**Workflow**:
```
1. User: "Add user authentication to our Next.js app"
2. Claude:
   - Uses context7 to get latest Next.js auth patterns
   - Uses claude-context to understand existing codebase
   - Implements feature with accurate, current APIs
   - Creates database tables via supabase
   - Writes e2e tests via playwright
   - Commits changes via git
   - Creates PR via github
```

---

## Pattern 3: The DevOps Automation Stack

**Use Case**: Infrastructure management and deployment

```bash
# Container orchestration
claude mcp add k8s -s user -- npx -y @containers/kubernetes-mcp-server
claude mcp add docker -s user -- npx -y @docker/mcp-server

# CI/CD
claude mcp add github -s user -- npx -y @modelcontextprotocol/server-github

# Monitoring and logs
claude mcp add filesystem -s user -- npx -y @modelcontextprotocol/server-filesystem /var/log

# Reasoning for complex decisions
claude mcp add sequential-thinking -s user -- npx -y @modelcontextprotocol/server-sequential-thinking
```

**Workflow**:
```
1. User: "Deploy the new API version with zero downtime"
2. Claude:
   - Uses sequential_thinking to plan deployment steps
   - Creates new deployment via k8s
   - Monitors rollout status
   - Checks logs for errors
   - Rolls back if issues detected
   - Updates GitHub status
```

---

## Pattern 4: The Knowledge Worker Stack

**Use Case**: Writing, documentation, and content management

```bash
# Knowledge management
claude mcp add notion -s user --transport http https://mcp.notion.com/mcp
claude mcp add obsidian -s user -- npx -y @iansinnott/obsidian-claude-code-mcp

# Research
claude mcp add brave -s user -- npx -y @anthropic/mcp-server-brave-search
claude mcp add fetch -s user -- npx -y @modelcontextprotocol/server-fetch

# Memory
claude mcp add memory -s user -- npx -y @modelcontextprotocol/server-memory

# File system
claude mcp add filesystem -s user -- npx -y @modelcontextprotocol/server-filesystem ~/Documents ~/Writing
```

**Workflow**:
```
1. User: "Help me write a technical blog post about MCP"
2. Claude:
   - Searches memory for prior research on topic
   - Queries Obsidian vault for related notes
   - Uses brave_search for current information
   - Fetches relevant documentation
   - Drafts post to filesystem
   - Syncs to Notion for publishing workflow
```

---

## Pattern 5: The Testing Automation Stack

**Use Case**: Comprehensive test automation

```bash
# Browser testing
claude mcp add playwright -s user -- npx -y @executeautomation/playwright-mcp-server

# API testing (via fetch)
claude mcp add fetch -s user -- npx -y @modelcontextprotocol/server-fetch

# Database verification
claude mcp add supabase -s project -- npx -y supabase-mcp-server

# Documentation for test patterns
claude mcp add context7 -s user -- npx -y @upstash/context7-mcp@latest

# Structured test planning
claude mcp add sequential-thinking -s user -- npx -y @modelcontextprotocol/server-sequential-thinking
```

**Workflow**:
```
1. User: "Create comprehensive tests for our checkout flow"
2. Claude:
   - Uses sequential_thinking to plan test scenarios
   - Gets testing framework docs via context7
   - Creates e2e tests with playwright
   - Verifies database state with supabase
   - Tests API endpoints with fetch
   - Generates test report
```

---

## Pattern 6: The Project Management Stack

**Use Case**: Issue tracking and team coordination

```bash
# Issue tracking
claude mcp add linear -s user -- npx -y @composio/mcp-server-linear
claude mcp add github -s user -- npx -y @modelcontextprotocol/server-github

# Communication
claude mcp add slack -s user -- npx -y @anthropic/mcp-server-slack

# Documentation
claude mcp add notion -s user --transport http https://mcp.notion.com/mcp

# Memory for project context
claude mcp add memory -s user -- npx -y @modelcontextprotocol/server-memory
```

**Workflow**:
```
1. User: "Create a sprint planning summary"
2. Claude:
   - Fetches open issues from Linear
   - Reviews recent PRs from GitHub
   - Queries memory for team preferences
   - Creates summary document in Notion
   - Posts update to Slack
```

---

## Advanced Patterns

### Auto-Invocation Rules

Configure servers to automatically activate based on context:

**For Context7** (documentation):
```
"Always use context7 when I need code generation, setup steps, or library documentation"
```

**For Memory** (knowledge persistence):
```
"At conversation start, query memory for relevant context. At conversation end, store key learnings."
```

### Chained Operations

Combine servers for complex workflows:

```
User: "Find all TODO comments in our codebase, create Linear issues for each, and post a summary to Slack"

Claude Chain:
1. claude-context → search_code("TODO|FIXME|HACK")
2. linear → create_issue() [for each found]
3. memory → store_entities() [track created issues]
4. slack → post_message() [summary with issue links]
```

### Fallback Configurations

Set up redundant servers:

```json
{
  "mcpServers": {
    "search-primary": {
      "command": "npx",
      "args": ["-y", "@anthropic/mcp-server-brave-search"]
    },
    "search-fallback": {
      "command": "npx",
      "args": ["-y", "@anthropic/mcp-server-tavily"]
    }
  }
}
```

---

## Security-First Patterns

### Isolated Database Access

Keep database servers project-scoped with read-only defaults:

```bash
# Project-scoped, explicit connection
claude mcp add db -s project -- npx -y @bytebase/dbhub --dsn "postgresql://readonly:pass@host/db"
```

### Containerized Untrusted Servers

Run third-party servers in Docker:

```json
{
  "mcpServers": {
    "untrusted-server": {
      "command": "docker",
      "args": ["run", "--rm", "-i", "--network=none", "mcp-server-image"]
    }
  }
}
```

### Audit Logging

Enable verbose logging for sensitive operations:

```bash
claude mcp add filesystem -s user -- npx -y @modelcontextprotocol/server-filesystem ~/Projects --log-level=debug
```

---

## Optimization Tips

### 1. Minimize Server Count
Each server adds startup time. Consolidate where possible:
- Use omnisearch instead of multiple search servers
- Use Docker MCP Toolkit for efficient container management

### 2. Scope Appropriately
- **User scope**: Tools you always want (memory, filesystem, search)
- **Project scope**: Database connections, project-specific APIs

### 3. Lazy Loading
Configure servers to start on-demand rather than at session start.

### 4. Token Efficiency
- Use claude-context for large codebases (40% token reduction)
- Configure context7 to fetch only relevant sections

---

## Troubleshooting

### Server Won't Connect

```bash
# Check server status
/mcp

# Verify installation
npx -y @modelcontextprotocol/server-memory --version

# Check configuration
claude mcp list
```

### Windows-Specific Issues

Use full paths for commands:
```json
{
  "command": "C:\\Program Files\\nodejs\\npx.cmd",
  "args": ["-y", "@modelcontextprotocol/server-memory"]
}
```

### Permission Errors

Ensure directories are accessible:
```bash
claude mcp add filesystem -s user -- npx -y @modelcontextprotocol/server-filesystem "$HOME/Projects"
```
