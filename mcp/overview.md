# Model Context Protocol (MCP) - Complete Reference

> **Last Updated**: December 2025
> **Version**: 2.0 (Protocol Version: 2025-03-26)
> **Status**: Production Ready - Industry Standard

## Executive Summary

The **Model Context Protocol (MCP)** is the universal standard for connecting AI models to external tools, data sources, and systems. Open-sourced by Anthropic in November 2024, MCP has achieved unprecedented industry adoption with **OpenAI, Google DeepMind, and all major AI providers** now supporting the protocol.

MCP transforms Claude Code from a conversational AI into a **full-stack development powerhouse** capable of:
- Direct file system operations
- Database queries and management
- Browser automation and testing
- Version control operations
- Cloud infrastructure management
- Real-time documentation access
- Persistent memory across sessions

---

## The N+M Revolution

### Before MCP: The N×M Problem
```
N AI applications × M tools = N×M custom integrations
Example: 10 apps × 50 tools = 500 custom integrations
```

### With MCP: The N+M Solution
```
N AI applications + M tools = N+M total connections
Example: 10 apps + 50 tools = 60 standardized connections
```

**Result**: Linear scaling instead of exponential complexity.

---

## Core Architecture

### Three Pillars of MCP

```
┌─────────────────────────────────────────────────────────────┐
│                        MCP PROTOCOL                          │
├─────────────────┬───────────────────┬───────────────────────┤
│     TOOLS       │    RESOURCES      │       PROMPTS         │
│ (Model-Control) │  (App-Control)    │    (User-Control)     │
├─────────────────┼───────────────────┼───────────────────────┤
│ • Search DB     │ • Files           │ • Slash commands      │
│ • Write file    │ • Error logs      │ • Complex workflows   │
│ • Query API     │ • JSON objects    │ • Template triggers   │
│ • Execute code  │ • Structured data │ • Custom actions      │
└─────────────────┴───────────────────┴───────────────────────┘
```

### 1. Tools (Model-Controlled)
Functions that Claude can invoke autonomously to perform actions:
- Execute operations (create, read, update, delete)
- Query external systems
- Transform data
- Automate workflows

### 2. Resources (Application-Controlled)
Structured information fed into Claude's context:
- File contents and metadata
- Database schemas
- API documentation
- System state

### 3. Prompts (User-Controlled)
Predefined interaction patterns:
- Slash commands (`/deploy`, `/test`)
- Template triggers
- Workflow shortcuts

---

## Transport Mechanisms

### Streamable HTTP (Recommended - 2025)
The latest transport combining HTTP with Server-Sent Events (SSE):
```
Client ──HTTP POST──▶ Server
Client ◀──SSE Stream── Server
```
- Bidirectional communication
- Cloud-native and scalable
- Firewall-friendly

### stdio (Standard I/O)
Traditional process-based communication:
```
Client ──stdin──▶ Server Process
Client ◀──stdout── Server Process
```
- Local server execution
- Simple setup
- Process isolation

---

## Claude Code Integration

### Quick Start Commands

```bash
# Add an MCP server
claude mcp add <server-name> -- <command> [args...]

# Add with user scope (available globally)
claude mcp add <server-name> -s user -- <command> [args...]

# Add with project scope (project-specific)
claude mcp add <server-name> -s project -- <command> [args...]

# List configured servers
claude mcp list

# Remove a server
claude mcp remove <server-name>

# Check server status in Claude Code
/mcp
```

### Configuration Scopes

| Scope | Location | Use Case |
|-------|----------|----------|
| `user` | `~/.claude.json` | Personal tools available everywhere |
| `project` | `.claude/settings.local.json` | Project-specific integrations |

### Configuration File Format

**User-level** (`~/.claude.json`):
```json
{
  "mcpServers": {
    "memory": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-memory"]
    },
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "~/Projects"]
    }
  }
}
```

**Project-level** (`.claude/settings.local.json`):
```json
{
  "mcpServers": {
    "supabase": {
      "type": "http",
      "url": "https://mcp.supabase.com/v1/projects/<ref>",
      "headers": {
        "Authorization": "Bearer <token>"
      }
    }
  }
}
```

---

## Official Reference Servers

These are maintained by the MCP steering group and represent production-ready implementations:

| Server | Purpose | Installation |
|--------|---------|--------------|
| **Everything** | Reference/test server | `npx -y @modelcontextprotocol/server-everything` |
| **Fetch** | Web content retrieval | `npx -y @modelcontextprotocol/server-fetch` |
| **Filesystem** | Secure file operations | `npx -y @modelcontextprotocol/server-filesystem <paths>` |
| **Git** | Repository operations | `npx -y @modelcontextprotocol/server-git` |
| **Memory** | Knowledge graph persistence | `npx -y @modelcontextprotocol/server-memory` |
| **Sequential Thinking** | Structured problem-solving | `npx -y @modelcontextprotocol/server-sequential-thinking` |
| **Time** | Timezone operations | `npx -y @modelcontextprotocol/server-time` |

---

## Industry Adoption (2025)

| Organization | Status | Details |
|--------------|--------|---------|
| **Anthropic** | Creator | Native support in Claude Desktop, Claude Code |
| **OpenAI** | Adopted March 2025 | ChatGPT Desktop, Agents SDK, Responses API |
| **Google DeepMind** | Announced April 2025 | Gemini models and infrastructure |
| **Microsoft** | Active support | VS Code, Copilot integration |
| **JetBrains** | Official SDK | Kotlin SDK collaboration |
| **Shopify** | Official SDK | Ruby SDK collaboration |
| **Google** | Official SDK | Go SDK collaboration |

---

## Security Considerations

### Best Practices

1. **Verify Server Sources**: Only install MCP servers from trusted sources
2. **Principle of Least Privilege**: Grant minimum required permissions
3. **Audit Third-Party Servers**: Review code before installation
4. **Monitor Tool Usage**: Log and review MCP tool invocations
5. **Sandbox Sensitive Operations**: Use containers for risky operations

### Known Risks (April 2025 Security Analysis)

| Risk | Description | Mitigation |
|------|-------------|------------|
| **Prompt Injection** | Malicious content in fetched data | Input sanitization, content validation |
| **Tool Permission Escalation** | Combined tools enabling file exfiltration | Strict permission boundaries |
| **Lookalike Tools** | Malicious tools mimicking trusted ones | Server verification, code audit |

### Docker Isolation

For sensitive workloads, run MCP servers in containers:
```bash
docker run -v /path/to/data:/data mcp-server-name
```

---

## SDK Availability

| Language | Package | Maintainer |
|----------|---------|------------|
| TypeScript | `@modelcontextprotocol/sdk` | Anthropic |
| Python | `mcp` | Anthropic |
| C# | `ModelContextProtocol` | Anthropic |
| Kotlin | `mcp-kotlin-sdk` | JetBrains |
| Ruby | `mcp-ruby-sdk` | Shopify |
| Go | `mcp-go-sdk` | Google |

---

## Resources

### Official
- [MCP Specification](https://modelcontextprotocol.io)
- [GitHub: modelcontextprotocol](https://github.com/modelcontextprotocol)
- [Reference Servers](https://github.com/modelcontextprotocol/servers)

### Community
- [MCP Registry](https://mcp.so)
- [Smithery](https://smithery.ai)
- [MCP Cat](https://mcpcat.io)

### Related Documentation
- [Cutting-Edge MCP Servers](cutting-edge-servers.md)
- [Claude Code Integration Patterns](integration-patterns.md)
- [MCP Server Development Guide](development-guide.md)
