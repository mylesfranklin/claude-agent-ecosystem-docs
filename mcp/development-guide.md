# MCP Server Development Guide

> **Last Updated**: December 2025
> **Focus**: Building custom MCP servers for Claude Code

## Why Build Custom MCP Servers?

While 200+ pre-built servers exist, custom servers enable:

- **Proprietary System Integration**: Connect internal tools and databases
- **Workflow Optimization**: Tailor capabilities to specific processes
- **Security Control**: Implement custom authentication and auditing
- **Domain Expertise**: Encode specialized knowledge and procedures

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────┐
│                    CLAUDE CODE (Client)                  │
│   Discovers servers → Invokes tools → Receives results  │
└─────────────────────────┬───────────────────────────────┘
                          │ MCP Protocol (JSON-RPC 2.0)
                          │
┌─────────────────────────▼───────────────────────────────┐
│                    MCP SERVER                            │
│  ┌─────────────┬──────────────┬───────────────────────┐ │
│  │   Tools     │  Resources   │      Prompts          │ │
│  │ (actions)   │  (data)      │   (templates)         │ │
│  └─────────────┴──────────────┴───────────────────────┘ │
│                          │                               │
│  ┌───────────────────────▼──────────────────────────┐   │
│  │            Your Business Logic                    │   │
│  │  (APIs, databases, services, custom logic)       │   │
│  └──────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────┘
```

---

## Quick Start: TypeScript Server

### 1. Initialize Project

```bash
mkdir my-mcp-server
cd my-mcp-server
npm init -y
npm install @modelcontextprotocol/sdk zod
npm install -D typescript @types/node
```

### 2. Configure TypeScript

`tsconfig.json`:
```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "Node16",
    "moduleResolution": "Node16",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "declaration": true
  },
  "include": ["src/**/*"]
}
```

### 3. Create Server

`src/index.ts`:
```typescript
#!/usr/bin/env node
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import {
  CallToolRequestSchema,
  ListToolsRequestSchema,
} from "@modelcontextprotocol/sdk/types.js";
import { z } from "zod";

// Define tool input schemas
const GetWeatherSchema = z.object({
  city: z.string().describe("City name to get weather for"),
  units: z.enum(["celsius", "fahrenheit"]).default("celsius"),
});

// Create server instance
const server = new Server(
  {
    name: "weather-server",
    version: "1.0.0",
  },
  {
    capabilities: {
      tools: {},
    },
  }
);

// List available tools
server.setRequestHandler(ListToolsRequestSchema, async () => {
  return {
    tools: [
      {
        name: "get_weather",
        description: "Get current weather for a city",
        inputSchema: {
          type: "object",
          properties: {
            city: { type: "string", description: "City name" },
            units: {
              type: "string",
              enum: ["celsius", "fahrenheit"],
              default: "celsius",
            },
          },
          required: ["city"],
        },
      },
    ],
  };
});

// Handle tool execution
server.setRequestHandler(CallToolRequestSchema, async (request) => {
  const { name, arguments: args } = request.params;

  if (name === "get_weather") {
    const { city, units } = GetWeatherSchema.parse(args);

    // Your business logic here
    const weather = await fetchWeather(city, units);

    return {
      content: [
        {
          type: "text",
          text: JSON.stringify(weather, null, 2),
        },
      ],
    };
  }

  throw new Error(`Unknown tool: ${name}`);
});

// Business logic
async function fetchWeather(city: string, units: string) {
  // Replace with actual API call
  return {
    city,
    temperature: units === "celsius" ? 22 : 72,
    units,
    condition: "sunny",
  };
}

// Start server
async function main() {
  const transport = new StdioServerTransport();
  await server.connect(transport);
  console.error("Weather MCP server running on stdio");
}

main().catch(console.error);
```

### 4. Build and Test

```bash
# Build
npm run build

# Test locally
echo '{"jsonrpc":"2.0","method":"tools/list","id":1}' | node dist/index.js

# Add to Claude Code
claude mcp add weather -s user -- node /path/to/dist/index.js
```

---

## Quick Start: Python Server

### 1. Initialize Project

```bash
mkdir my-mcp-server
cd my-mcp-server
python -m venv venv
source venv/bin/activate
pip install mcp
```

### 2. Create Server

`server.py`:
```python
#!/usr/bin/env python3
import asyncio
import json
from mcp.server import Server
from mcp.server.stdio import stdio_server
from mcp.types import Tool, TextContent

# Create server
app = Server("weather-server")

@app.list_tools()
async def list_tools() -> list[Tool]:
    return [
        Tool(
            name="get_weather",
            description="Get current weather for a city",
            inputSchema={
                "type": "object",
                "properties": {
                    "city": {"type": "string", "description": "City name"},
                    "units": {
                        "type": "string",
                        "enum": ["celsius", "fahrenheit"],
                        "default": "celsius"
                    }
                },
                "required": ["city"]
            }
        )
    ]

@app.call_tool()
async def call_tool(name: str, arguments: dict) -> list[TextContent]:
    if name == "get_weather":
        city = arguments["city"]
        units = arguments.get("units", "celsius")

        # Your business logic
        weather = {
            "city": city,
            "temperature": 22 if units == "celsius" else 72,
            "units": units,
            "condition": "sunny"
        }

        return [TextContent(type="text", text=json.dumps(weather, indent=2))]

    raise ValueError(f"Unknown tool: {name}")

async def main():
    async with stdio_server() as (read_stream, write_stream):
        await app.run(read_stream, write_stream, app.create_initialization_options())

if __name__ == "__main__":
    asyncio.run(main())
```

### 3. Add to Claude Code

```bash
claude mcp add weather -s user -- python /path/to/server.py
```

---

## Adding Resources

Resources provide structured data to Claude:

```typescript
import { ListResourcesRequestSchema, ReadResourceRequestSchema } from "@modelcontextprotocol/sdk/types.js";

// List resources
server.setRequestHandler(ListResourcesRequestSchema, async () => {
  return {
    resources: [
      {
        uri: "config://settings",
        name: "Application Settings",
        description: "Current configuration values",
        mimeType: "application/json",
      },
    ],
  };
});

// Read resource
server.setRequestHandler(ReadResourceRequestSchema, async (request) => {
  const { uri } = request.params;

  if (uri === "config://settings") {
    return {
      contents: [
        {
          uri,
          mimeType: "application/json",
          text: JSON.stringify({
            debug: true,
            version: "1.0.0",
          }),
        },
      ],
    };
  }

  throw new Error(`Unknown resource: ${uri}`);
});
```

---

## Adding Prompts

Prompts are reusable templates:

```typescript
import { ListPromptsRequestSchema, GetPromptRequestSchema } from "@modelcontextprotocol/sdk/types.js";

server.setRequestHandler(ListPromptsRequestSchema, async () => {
  return {
    prompts: [
      {
        name: "analyze_code",
        description: "Analyze code for issues and improvements",
        arguments: [
          {
            name: "language",
            description: "Programming language",
            required: true,
          },
        ],
      },
    ],
  };
});

server.setRequestHandler(GetPromptRequestSchema, async (request) => {
  const { name, arguments: args } = request.params;

  if (name === "analyze_code") {
    return {
      messages: [
        {
          role: "user",
          content: {
            type: "text",
            text: `Analyze the following ${args?.language} code for:
1. Security vulnerabilities
2. Performance issues
3. Code style improvements
4. Best practices violations`,
          },
        },
      ],
    };
  }

  throw new Error(`Unknown prompt: ${name}`);
});
```

---

## HTTP Transport (Cloud Deployment)

For cloud-based servers using Streamable HTTP:

```typescript
import { SSEServerTransport } from "@modelcontextprotocol/sdk/server/sse.js";
import express from "express";

const app = express();
app.use(express.json());

// Handle SSE connections
app.get("/sse", async (req, res) => {
  const transport = new SSEServerTransport("/messages", res);
  await server.connect(transport);
});

// Handle messages
app.post("/messages", async (req, res) => {
  // Transport handles message routing
});

app.listen(3000);
```

Configure in Claude Code:
```bash
claude mcp add myserver --transport http https://myserver.com/sse
```

---

## Best Practices

### 1. Input Validation
Always validate inputs with schemas:
```typescript
const schema = z.object({
  query: z.string().min(1).max(1000),
  limit: z.number().int().positive().max(100).default(10),
});
```

### 2. Error Handling
Return meaningful errors:
```typescript
try {
  // Operation
} catch (error) {
  return {
    content: [
      {
        type: "text",
        text: `Error: ${error.message}`,
      },
    ],
    isError: true,
  };
}
```

### 3. Logging
Log to stderr (stdout is for protocol):
```typescript
console.error("Processing request:", request.params);
```

### 4. Timeouts
Implement operation timeouts:
```typescript
const result = await Promise.race([
  performOperation(),
  new Promise((_, reject) =>
    setTimeout(() => reject(new Error("Timeout")), 30000)
  ),
]);
```

### 5. Security
- Validate all inputs
- Sanitize outputs
- Use least privilege
- Audit sensitive operations

---

## Testing

### Unit Testing
```typescript
import { describe, it, expect } from "vitest";

describe("get_weather", () => {
  it("returns weather data", async () => {
    const result = await callTool("get_weather", { city: "London" });
    expect(result.content[0].text).toContain("London");
  });
});
```

### Integration Testing
```bash
# Test with MCP Inspector
npx @modelcontextprotocol/inspector node dist/index.js
```

---

## Packaging and Distribution

### npm Package
```json
{
  "name": "@yourorg/mcp-server-weather",
  "version": "1.0.0",
  "bin": {
    "mcp-server-weather": "./dist/index.js"
  },
  "files": ["dist"],
  "scripts": {
    "build": "tsc",
    "prepublishOnly": "npm run build"
  }
}
```

### Docker Image
```dockerfile
FROM node:20-slim
WORKDIR /app
COPY package*.json ./
RUN npm ci --production
COPY dist ./dist
ENTRYPOINT ["node", "dist/index.js"]
```

---

## Resources

- [MCP TypeScript SDK](https://github.com/modelcontextprotocol/typescript-sdk)
- [MCP Python SDK](https://github.com/modelcontextprotocol/python-sdk)
- [MCP Specification](https://modelcontextprotocol.io/specification)
- [Reference Server Implementations](https://github.com/modelcontextprotocol/servers)
