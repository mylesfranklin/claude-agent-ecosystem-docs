# Cutting-Edge MCP Servers for Claude Code

> **Last Updated**: December 2025
> **Focus**: Production-ready servers that add significant capabilities to Claude Code
> **Philosophy**: Quality over quantity - only servers that provide substantial "firepower"

## Tier 1: Essential Power-Ups

These servers transform Claude Code into a full-stack development platform.

---

### Sequential Thinking Server
**The Brain Boost** - 54% improvement in complex problem-solving

```bash
claude mcp add sequential-thinking -s user -- npx -y @modelcontextprotocol/server-sequential-thinking
```

**What It Does**: Enables structured, reflective problem-solving that mirrors human cognitive patterns. Instead of jumping directly to solutions, Claude methodically works through problems.

**Capabilities**:
- Break complex problems into manageable steps
- Revise and refine thoughts as understanding deepens
- Branch into alternative reasoning paths
- Dynamically adjust the number of thinking steps
- Generate and verify solution hypotheses

**Best For**:
- Complex architectural decisions
- Multi-step debugging sessions
- Requirements analysis
- Algorithm design
- Any task with initially unclear scope

**Tools Provided**:
| Tool | Parameters | Purpose |
|------|------------|---------|
| `sequential_thinking` | `thought`, `nextThoughtNeeded`, `thoughtNumber`, `totalThoughts`, `isRevision`, `branchFromThought` | Step-by-step analysis |

---

### Memory Server (Knowledge Graph)
**Persistent Brain** - Cross-session context retention

```bash
claude mcp add memory -s user -- npx -y @modelcontextprotocol/server-memory
```

**What It Does**: Creates a local knowledge graph that persists user information, preferences, project context, and relationships across all chat sessions.

**Architecture**:
```
┌─────────────────────────────────────────────┐
│              KNOWLEDGE GRAPH                 │
├─────────────┬───────────────┬───────────────┤
│  ENTITIES   │   RELATIONS   │  OBSERVATIONS │
│ (nodes)     │   (edges)     │   (facts)     │
├─────────────┼───────────────┼───────────────┤
│ John_Smith  │   works_at    │ "Speaks       │
│ (person)    │   knows       │  Spanish"     │
│ Anthropic   │   manages     │ "Prefers      │
│ (org)       │   created     │  TypeScript"  │
└─────────────┴───────────────┴───────────────┘
```

**Tools Provided**:
| Tool | Purpose |
|------|---------|
| `create_entities` | Add new nodes to the graph |
| `create_relations` | Connect entities with relationships |
| `add_observations` | Attach facts to entities |
| `delete_entities` | Remove outdated entities |
| `read_graph` | Export complete knowledge structure |
| `search_nodes` | Query by content |
| `open_nodes` | Access specific entities by name |

**Workflow**:
1. Retrieve relevant memories at conversation start
2. Capture new information during interaction
3. Update graph with discoveries about identity, preferences, goals

---

### Context7 Server
**Live Documentation** - 15,000+ library docs on demand

```bash
claude mcp add context7 -s user -- npx -y @upstash/context7-mcp@latest
```

**What It Does**: Eliminates hallucinated APIs by fetching real-time, version-specific documentation directly from source repositories.

**The Problem It Solves**:
- ❌ Outdated code examples from training data
- ❌ Hallucinated APIs that don't exist
- ❌ Generic answers for old package versions

**The Solution**:
- ✅ Current official documentation
- ✅ Version-specific accuracy
- ✅ Real code examples from source

**Usage**:
```
// Add to your prompt:
"use context7"

// Or configure auto-invocation for code generation tasks
```

**Tools Provided**:
| Tool | Purpose |
|------|---------|
| `resolve-library-id` | Find Context7 library ID from name |
| `get-library-docs` | Fetch documentation for specific library |

**Supported Libraries**: Next.js, React, Vue, Svelte, Cloudflare Workers, AWS SDK, and thousands more.

---

## Tier 2: Browser & Web Automation

### Playwright MCP Server (Recommended)
**Browser Powerhouse** - Full browser automation

```bash
claude mcp add playwright -s user -- npx -y @executeautomation/playwright-mcp-server
```

**Note**: The official Puppeteer server has been **deprecated**. Playwright is the recommended replacement with superior cross-browser support.

**Capabilities**:
- Navigate and interact with web pages
- Take screenshots (full-page or viewport)
- Generate test code automatically
- Web scraping and data extraction
- Execute JavaScript in browser context
- Handle multiple tabs and windows
- Wait for elements with configurable timeouts

**Use Cases**:
- E2E testing automation
- Visual regression testing
- Web scraping for research
- Form filling and submission
- Screenshot documentation
- Interactive debugging

**Example**:
```
"Navigate to our staging site, fill in the login form, take a screenshot of the dashboard, and verify the new feature is visible"
```

---

### Fetch Server
**Web Content Retrieval** - Efficient URL fetching

```bash
claude mcp add fetch -s user -- npx -y @modelcontextprotocol/server-fetch
```

**What It Does**: Fetches web content and converts it to formats optimized for LLM consumption.

**Best For**:
- Retrieving API documentation
- Fetching reference material
- Downloading specifications
- Content analysis

---

## Tier 3: Data & Database

### Supabase MCP Server
**Full Database Control** - PostgreSQL + Auth + Storage

```bash
# HTTP transport (recommended for cloud)
claude mcp add --transport http supabase https://mcp.supabase.com/v1/projects/<project-ref>

# Or local installation
claude mcp add supabase -s user -- npx -y supabase-mcp-server
```

**Capabilities**:
- Create and manage Supabase projects
- Design tables and generate migrations
- Execute SQL queries (read/write)
- Manage database schema
- Create branches
- Generate TypeScript types
- Retrieve logs for debugging
- Row Level Security management

**Safety Features** (v0.3.8+):
- Automatic migration script creation for write operations
- Improved safety mode for query execution
- Confirmation prompts for destructive operations

**Example**:
```
"Create a new users table with email, name, and created_at columns. Add RLS policies so users can only see their own data."
```

---

### PostgreSQL MCP Server
**Direct Database Access** - Any PostgreSQL database

```bash
claude mcp add db -s user -- npx -y @bytebase/dbhub --dsn "postgresql://user:pass@host:5432/db"
```

**Natural Language Queries**:
- "What's our total revenue this month?"
- "Show me the schema for the orders table"
- "Find all users who signed up in the last week"

---

### Vector Database Integration

For RAG and semantic search applications:

**Pinecone MCP**:
```bash
claude mcp add pinecone -s user -- npx -y @sirmews/mcp-pinecone
```

**Use Cases**:
- Semantic code search
- Document similarity
- Recommendation systems
- Knowledge base queries

---

## Tier 4: DevOps & Infrastructure

### Docker MCP Toolkit
**Container Control** - 200+ pre-built servers

```bash
# Via Docker Desktop
# Install Docker MCP Toolkit extension
```

**Capabilities**:
- Launch containerized MCP servers with one click
- Automatic credential handling
- Secure, isolated execution environment
- Cross-platform consistency (Mac, Windows, Linux)

**Benefits**:
- No dependency conflicts
- Automatic cleanup
- Security isolation
- Consistent environments

---

### Kubernetes MCP Server
**Cluster Management** - Full K8s/OpenShift control

```bash
claude mcp add k8s -s user -- npx -y @containers/kubernetes-mcp-server
```

**Capabilities**:
- CRUD operations on any K8s resource
- Pod management (list, get, delete, logs, exec)
- Resource metrics and usage
- Helm chart deployment
- Service and deployment management

**Example**:
```
"Deploy the new version of our API to the staging namespace, scale it to 3 replicas, and show me the logs"
```

---

### Git Server
**Version Control** - Full repository operations

```bash
claude mcp add git -s user -- npx -y @modelcontextprotocol/server-git
```

**Capabilities**:
- Read and search repository history
- Branch operations
- Diff analysis
- Commit inspection
- Repository statistics

---

### GitHub Server
**Platform Integration** - Issues, PRs, Actions

```bash
claude mcp add github -s user -- npx -y @modelcontextprotocol/server-github
```

**Capabilities**:
- Repository management
- Issue and PR operations
- CI/CD workflow inspection
- Code review automation

---

## Tier 5: Search & Research

### Omnisearch Server
**Unified Search** - All search engines in one

```bash
claude mcp add omnisearch -s user -- npx -y @spences10/mcp-omnisearch
```

**Includes**:
- **Search**: Tavily, Brave, Kagi
- **AI Responses**: Perplexity, FastGPT
- **Content Processing**: Jina AI Reader, Kagi Summarizer

**Benefits**:
- Single configuration for all search tools
- Use whichever APIs you have keys for
- Unified interface

---

### Individual Search Servers

**Brave Search**:
```bash
claude mcp add brave -s user -- npx -y @anthropic/mcp-server-brave-search
```

**Perplexity**:
```bash
claude mcp add perplexity -s user -- npx -y @anthropic/mcp-server-perplexity
```

**Tavily**:
```bash
claude mcp add tavily -s user -- npx -y @anthropic/mcp-server-tavily
```

---

## Tier 6: Productivity & Collaboration

### Notion MCP
**Knowledge Management** - Full workspace access

```bash
claude mcp add --transport http notion https://mcp.notion.com/mcp
```

**Capabilities**:
- Read from and write to Notion pages
- Search across workspace
- Create and update databases
- Manage page properties

---

### Linear MCP
**Issue Tracking** - Terminal-based project management

```bash
claude mcp add linear -s user -- npx -y @composio/mcp-server-linear
```

**Capabilities**:
- Create and update issues
- Manage projects and cycles
- Assign tasks
- Update statuses

---

### Obsidian MCP
**Personal Knowledge** - Direct vault access

```bash
claude mcp add obsidian -s user -- npx -y @iansinnott/obsidian-claude-code-mcp
```

**Capabilities**:
- Search across all notes
- Read and modify files
- Create new notes
- Query relationships

---

### Slack MCP
**Team Communication** - Message and channel access

```bash
claude mcp add slack -s user -- npx -y @anthropic/mcp-server-slack
```

---

## Tier 7: Semantic Code Intelligence

### Claude Context (Zilliz)
**Codebase-Wide Search** - Semantic understanding at scale

```bash
claude mcp add claude-context -s user -- npx -y @zilliztech/claude-context
```

**What It Does**: Creates a searchable semantic index of your entire codebase using hybrid search (BM25 + dense vector).

**Benefits**:
- ~40% token reduction
- Find relevant code without loading everything
- Natural language code search
- Works across millions of lines

**Tools**:
| Tool | Purpose |
|------|---------|
| `index_codebase` | Create searchable index |
| `search_code` | Natural language code search |
| `get_indexing_status` | Monitor index progress |
| `clear_index` | Reset index |

**Requirements**: Node.js 20+, OpenAI API (embeddings), Zilliz Cloud

---

## Recommended Stack for Maximum Firepower

### Developer Power Stack
```bash
# Core reasoning
claude mcp add sequential-thinking -s user -- npx -y @modelcontextprotocol/server-sequential-thinking

# Persistent memory
claude mcp add memory -s user -- npx -y @modelcontextprotocol/server-memory

# Live documentation
claude mcp add context7 -s user -- npx -y @upstash/context7-mcp@latest

# File operations
claude mcp add filesystem -s user -- npx -y @modelcontextprotocol/server-filesystem ~/Projects ~/Documents

# Browser automation
claude mcp add playwright -s user -- npx -y @executeautomation/playwright-mcp-server

# Version control
claude mcp add git -s user -- npx -y @modelcontextprotocol/server-git

# Web search
claude mcp add brave -s user -- npx -y @anthropic/mcp-server-brave-search
```

### Full-Stack Development Stack
Add to the above:
```bash
# Database
claude mcp add supabase -s user -- npx -y supabase-mcp-server

# GitHub integration
claude mcp add github -s user -- npx -y @modelcontextprotocol/server-github

# Container management
claude mcp add docker -s user -- npx -y @docker/mcp-server
```

---

## Performance Optimization Tips

1. **Use user scope for global tools**: `-s user` makes servers available everywhere
2. **Project scope for project-specific**: Keep database connections project-scoped
3. **Enable dangerouslySkipPermissions for automation**: Only in trusted environments
4. **Configure auto-invocation rules**: Set up Context7 to auto-trigger for code tasks
5. **Use Docker for isolation**: Run untrusted servers in containers

---

## Sources

- [MCP Official Repository](https://github.com/modelcontextprotocol/servers)
- [Docker MCP Toolkit](https://www.docker.com/blog/add-mcp-servers-to-claude-code-with-mcp-toolkit/)
- [Context7 GitHub](https://github.com/upstash/context7)
- [Claude Context (Zilliz)](https://github.com/zilliztech/claude-context)
- [Kubernetes MCP Server](https://github.com/containers/kubernetes-mcp-server)
- [Playwright MCP](https://github.com/executeautomation/mcp-playwright)
- [MCP Omnisearch](https://github.com/spences10/mcp-omnisearch)
- [Supabase MCP](https://supabase.com/docs/guides/getting-started/mcp)
