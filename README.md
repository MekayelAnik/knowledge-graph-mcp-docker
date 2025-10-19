# Knowledge Graph MCP Server
### Multi-Architecture Docker Image for AI Memory & Persistent Knowledge

<div align="left">

<img alt="knowledge-graph-mcp" src="https://img.shields.io/badge/Knowledge%20Graph-MCP-4A90E2?style=for-the-badge&logo=graphql&logoColor=white" width="400">

[![Docker Pulls](https://img.shields.io/docker/pulls/mekayelanik/knowledge-graph-mcp.svg?style=flat-square)](https://hub.docker.com/r/mekayelanik/knowledge-graph-mcp)
[![Docker Stars](https://img.shields.io/docker/stars/mekayelanik/knowledge-graph-mcp.svg?style=flat-square)](https://hub.docker.com/r/mekayelanik/knowledge-graph-mcp)
[![License](https://img.shields.io/badge/license-GPL-blue.svg?style=flat-square)](LICENSE)

**[NPM Package](https://www.npmjs.com/package/mcp-knowledge-graph)** • **[GitHub Repository](https://github.com/mekayelanik/knowledge-graph-mcp)** • **[Docker Hub](https://hub.docker.com/r/mekayelanik/knowledge-graph-mcp)**

</div>

---

## 📋 Table of Contents

- [Overview](#overview)
- [Quick Start](#quick-start)
- [Understanding AIM](#understanding-aim)
- [Configuration](#configuration)
- [MCP Client Setup](#mcp-client-setup)
- [Available Tools](#available-tools)
- [Database Management](#database-management)
- [Advanced Usage](#advanced-usage)
- [Troubleshooting](#troubleshooting)
- [Resources & Support](#resources--support)

---

## Overview

Knowledge Graph MCP Server provides persistent memory for AI models through a local knowledge graph. Store entities, relations, and observations that persist across conversations, enabling AI assistants to remember context, build understanding over time, and maintain continuity in your interactions.

### Key Features

🧠 **Persistent AI Memory** - Store and retrieve information across all conversations  
🔗 **Entity Relationships** - Connect people, projects, events, and concepts  
📊 **Observation Tracking** - Add facts and notes to any entity  
🗂️ **Multiple Databases** - Organize memories by context (work, personal, projects)  
📁 **Project-Local Storage** - Automatic `.aim` directory detection for project-specific memory  
🌍 **Global Memory** - Shared knowledge base accessible from anywhere  
🔍 **Powerful Search** - Find information by keyword across your entire knowledge graph  
🛡️ **Safe Operations** - Built-in protection against accidental data overwrites  
🚀 **Multiple Protocols** - HTTP, SSE, and WebSocket transport support  
⚡ **Zero Configuration** - Works out of the box with sensible defaults

### Supported Architectures

| Architecture | Status | Notes |
|:-------------|:------:|:------|
| **x86-64** | ✅ Stable | Intel/AMD processors |
| **ARM64** | ✅ Stable | Raspberry Pi, Apple Silicon |

### Available Tags

| Tag | Stability | Use Case |
|:----|:---------:|:---------|
| `stable` | ⭐⭐⭐ | **Production (recommended)** |
| `latest` | ⭐⭐⭐ | Latest stable features |
| `1.x.x` | ⭐⭐⭐ | Version pinning |
| `beta` | ⚠️ | Testing only |

---

## Quick Start

### Prerequisites

- Docker Engine 23.0+
- Persistent storage for memory files (volume or bind mount)

### Docker Compose (Recommended)

```yaml
services:
  knowledge-graph-mcp:
    image: mekayelanik/knowledge-graph-mcp:stable
    container_name: knowledge-graph-mcp
    restart: unless-stopped
    ports:
      - "8025:8025"
    volumes:
      - ./aim-memory:/data/.aim
    environment:
      - PORT=8025
      - PUID=1000
      - PGID=1000
      - TZ=Asia/Dhaka
      - PROTOCOL=SHTTP
      - CORS=*
      - MEMORY_PATH=/data/.aim
```

**Deploy:**

```bash
docker compose up -d
docker compose logs -f knowledge-graph-mcp
```

### Docker CLI

```bash
docker run -d \
  --name=knowledge-graph-mcp \
  --restart=unless-stopped \
  -p 8025:8025 \
  -v $(pwd)/aim-memory:/data/.aim \
  -e PORT=8025 \
  -e PUID=1000 \
  -e PGID=1000 \
  -e PROTOCOL=SHTTP \
  -e CORS=* \
  -e MEMORY_PATH=/data/.aim \
  mekayelanik/knowledge-graph-mcp:stable
```

### Access Endpoints

| Protocol | Endpoint | Use Case |
|:---------|:---------|:---------|
| **HTTP** | `http://host-ip:8025/mcp` | **Recommended** |
| **SSE** | `http://host-ip:8025/sse` | Real-time streaming |
| **WebSocket** | `ws://host-ip:8025/message` | Bidirectional |
| **Health** | `http://host-ip:8025/healthz` | Monitoring |

> ⏱️ Server ready in 5-10 seconds after container start

---

## Understanding AIM

**AIM** stands for **AI Memory** - the core concept of this knowledge graph system. The AIM naming convention provides clear organization and safety across all components.

### The Three AIM Elements

#### 1. `.aim` Directories
Keep AI memory files organized and easily identifiable in your file system.

```bash
# Project-local memory
my-project/.aim/
├── memory.jsonl           # Master database
├── memory-work.jsonl      # Work context
└── memory-notes.jsonl     # Notes context

# Global memory
~/.aim/
├── memory.jsonl           # Master database
├── memory-personal.jsonl  # Personal context
└── memory-health.jsonl    # Health context
```

#### 2. `aim_` Tool Prefixes
Group related memory functions together in multi-tool setups, making them easy to discover and use.

- `aim_create_entities` - Add new entities
- `aim_create_relations` - Link entities
- `aim_add_observations` - Add facts
- `aim_search_nodes` - Find information
- `aim_read_graph` - View entire memory
- `aim_open_nodes` - Retrieve specific entities
- `aim_list_databases` - Show all databases
- `aim_delete_entities` - Remove entities
- `aim_delete_observations` - Remove facts
- `aim_delete_relations` - Remove connections

#### 3. `_aim` Safety Markers
Every memory file starts with `{"type":"_aim","source":"mcp-knowledge-graph"}` to prevent accidental overwrites of unrelated JSONL files.

```json
{"type":"_aim","source":"mcp-knowledge-graph"}
{"type":"entity","name":"John_Doe","entityType":"person","observations":["Met at conference"]}
```

---

## Configuration

### Environment Variables

#### Core Settings

| Variable | Default | Description |
|:---------|:-------:|:------------|
| `PORT` | `8025` | Server port (1-65535) |
| `PUID` | `1000` | User ID for file permissions |
| `PGID` | `1000` | Group ID for file permissions |
| `TZ` | `Asia/Dhaka` | Container timezone |
| `PROTOCOL` | `SHTTP` | Transport protocol (SHTTP/SSE/WS) |
| `CORS` | _(none)_ | Cross-Origin configuration |
| `MEMORY_PATH` | `/data/.aim` | Path to memory storage directory |
| `API_KEY` | _(none)_ | Optional authentication key |

#### Advanced Settings

| Variable | Default | Description |
|:---------|:-------:|:------------|
| `DEBUG_MODE` | `false` | Enable debug mode (`true`, `false`, `verbose`) |

### Protocol Configuration

```yaml
# HTTP/Streamable HTTP (Recommended)
environment:
  - PROTOCOL=SHTTP

# Server-Sent Events
environment:
  - PROTOCOL=SSE

# WebSocket
environment:
  - PROTOCOL=WS
```

### CORS Configuration

```yaml
# Development - Allow all origins
environment:
  - CORS=*

# Production - Specific domains
environment:
  - CORS=https://example.com,https://app.example.com

# Mixed domains and IPs
environment:
  - CORS=https://example.com,192.168.1.100:3000

# Regex patterns
environment:
  - CORS=/^https:\/\/.*\.example\.com$/
```

> ⚠️ **Security:** Never use `CORS=*` in production environments

### API Key Authentication

```yaml
# Enable API key authentication
environment:
  - API_KEY=your-secure-api-key-here

# API key requirements:
# - 5-128 characters
# - Alphanumeric plus: _ : . @ + = - (space)
# - Avoid common values: password, secret, admin, token, key, test, demo
```

### Storage Configuration

```yaml
# Default container path
environment:
  - MEMORY_PATH=/data/.aim

# Custom container path
environment:
  - MEMORY_PATH=/app/memory

# Always mount to host for persistence
volumes:
  - ./aim-memory:/data/.aim
  # or
  - /path/to/storage:/data/.aim
```

---

## MCP Client Setup

### Transport Compatibility

| Client | HTTP | SSE | WebSocket | Recommended |
|:-------|:----:|:---:|:---------:|:------------|
| **VS Code (Cline/Roo-Cline)** | ✅ | ✅ | ❌ | HTTP |
| **JetBrains IDEs** | ✅ | ✅ | ❌ | HTTP |
| **Claude Desktop** | ✅ | ✅ | ⚠️* | HTTP |
| **Cursor** | ✅ | ✅ | ⚠️* | HTTP |
| **Windsurf** | ✅ | ✅ | ⚠️* | HTTP |

> ⚠️ *WebSocket support is experimental

### VS Code (Cline/Roo-Cline)

Add to `.vscode/settings.json`:

```json
{
  "mcp.servers": {
    "memory": {
      "url": "http://host-ip:8025/mcp",
      "transport": "http",
      "autoApprove": [
           "aim_create_entities",
           "aim_create_relations",
           "aim_add_observations",
           "aim_delete_entities",
           "aim_delete_observations",
           "aim_delete_relations",
           "aim_search_nodes",
           "aim_read_graph",
           "aim_open_nodes",
           "aim_list_databases"
      ]
    }
  }
}
```

### JetBrains IDEs (PyCharm, IntelliJ IDEA, WebStorm, etc.)

JetBrains IDEs support MCP through the AI Assistant plugin. Configuration varies by IDE version:

#### Method 1: Settings UI (Recommended)

1. Open **Settings/Preferences** → **Tools** → **AI Assistant** → **MCP Servers**
2. Click **Add Server**
3. Configure the server:
   - **Name:** `memory`
   - **Transport:** `HTTP`
   - **URL:** `http://host-ip:8025/mcp`
4. Enable auto-approve for memory tools (optional)
5. Click **Apply** and restart IDE

#### Method 2: Configuration File

Create or edit `~/.config/JetBrains/<IDE>/mcp_settings.json`:

**PyCharm:**
```json
{
  "mcpServers": {
    "memory": {
      "transport": "http",
      "url": "http://localhost:8025/mcp",
      "autoApprove": [
        "aim_create_entities",
        "aim_create_relations",
        "aim_add_observations",
        "aim_search_nodes",
        "aim_read_graph",
        "aim_open_nodes",
        "aim_list_databases"
      ]
    }
  }
}
```

**IntelliJ IDEA:**
Location: `~/.config/JetBrains/IntelliJIdea<VERSION>/mcp_settings.json`

**WebStorm:**
Location: `~/.config/JetBrains/WebStorm<VERSION>/mcp_settings.json`

**Common locations:**
- **Linux:** `~/.config/JetBrains/<IDE><VERSION>/mcp_settings.json`
- **macOS:** `~/Library/Application Support/JetBrains/<IDE><VERSION>/mcp_settings.json`
- **Windows:** `%APPDATA%\JetBrains\<IDE><VERSION>\mcp_settings.json`

#### Method 3: Project-Level Configuration

Create `.idea/mcp_settings.json` in your project root:

```json
{
  "mcpServers": {
    "memory": {
      "transport": "http",
      "url": "http://localhost:8025/mcp"
    }
  }
}
```

**Verification:**
1. Open AI Assistant in your IDE
2. Check if "memory" server appears in available tools
3. Test with: "List all my databases"

### Claude Desktop

**Config Locations:**
- **Linux:** `~/.config/Claude/claude_desktop_config.json`
- **macOS:** `~/Library/Application Support/Claude/claude_desktop_config.json`
- **Windows:** `%APPDATA%\Claude\claude_desktop_config.json`

```json
{
  "mcpServers": {
    "memory": {
      "transport": "http",
      "url": "http://localhost:8025/mcp"
    }
  }
}
```

### Cursor

Add to `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "memory": {
      "transport": "http",
      "url": "http://host-ip:8025/mcp"
    }
  }
}
```

### Windsurf (Codeium)

Add to `.codeium/mcp_settings.json`:

```json
{
  "mcpServers": {
    "memory": {
      "transport": "http",
      "url": "http://host-ip:8025/mcp"
    }
  }
}
```

### Claude Code

Add to `~/.config/claude-code/mcp_config.json`:

```json
{
  "mcpServers": {
    "memory": {
      "transport": "http",
      "url": "http://localhost:8025/mcp"
    }
  }
}
```

Or configure via CLI:

```bash
claude-code config mcp add memory \
  --transport http \
  --url http://localhost:8025/mcp
```

---

## Available Tools

### 🆕 aim_create_entities
Create new entities in the knowledge graph (people, projects, events, concepts).

**Parameters:**
- `entities` (array, required): List of entities to create
  - `name` (string): Entity name (use underscores, e.g., "John_Doe")
  - `entityType` (string): Type (person, project, event, concept, etc.)
  - `observations` (array): Initial facts about the entity
- `context` (string, optional): Database name (defaults to master database)
- `location` (string, optional): Force "project" or "global" storage

**Example:**
```json
{
  "entities": [
    {
      "name": "John_Doe",
      "entityType": "person",
      "observations": [
        "Met at AI conference 2024",
        "Works on machine learning projects",
        "Based in San Francisco"
      ]
    }
  ]
}
```

---

### 🔗 aim_create_relations
Create relationships between entities to build connections in your knowledge graph.

**Parameters:**
- `relations` (array, required): List of relations to create
  - `from` (string): Source entity name
  - `to` (string): Target entity name
  - `relationType` (string): Type of relationship
- `context` (string, optional): Database name
- `location` (string, optional): Storage location

**Example:**
```json
{
  "relations": [
    {
      "from": "John_Doe",
      "to": "AI_Conference_2024",
      "relationType": "attended"
    },
    {
      "from": "John_Doe",
      "to": "ML_Project",
      "relationType": "works_on"
    }
  ]
}
```

---

### 📝 aim_add_observations
Add new facts and notes to existing entities.

**Parameters:**
- `observations` (array, required): List of observations to add
  - `entityName` (string): Target entity name
  - `contents` (array): New facts to add
- `context` (string, optional): Database name
- `location` (string, optional): Storage location

**Example:**
```json
{
  "observations": [
    {
      "entityName": "John_Doe",
      "contents": [
        "Presented paper on neural networks",
        "Planning to attend next conference"
      ]
    }
  ]
}
```

---

### 🔍 aim_search_nodes
Search for entities by keyword across your entire knowledge graph.

**Parameters:**
- `query` (string, required): Search keyword
- `context` (string, optional): Database name
- `location` (string, optional): Storage location

**Example:**
```json
{
  "query": "conference"
}
```

**Returns:** All entities and their observations containing the search term.

---

### 📖 aim_read_graph
Retrieve the entire knowledge graph or specific database.

**Parameters:**
- `context` (string, optional): Database name (defaults to master)
- `location` (string, optional): Storage location

**Use Cases:**
- View all stored knowledge
- Export memory for backup
- Analyze relationship patterns
- Generate reports

---

### 🎯 aim_open_nodes
Retrieve specific entities by name.

**Parameters:**
- `names` (array, required): List of entity names to retrieve
- `context` (string, optional): Database name
- `location` (string, optional): Storage location

**Example:**
```json
{
  "names": ["John_Doe", "AI_Conference_2024"]
}
```

---

### 📊 aim_list_databases
Show all available databases in both project and global locations.

**Parameters:**
- `location` (string, optional): Force "project" or "global" location

**Returns:**
```json
{
  "project_databases": ["default", "project-work"],
  "global_databases": ["default", "work", "personal", "health"],
  "current_location": "project (.aim directory detected)"
}
```

---

### 🗑️ aim_delete_entities
Remove entities from the knowledge graph.

**Parameters:**
- `entityNames` (array, required): List of entity names to delete
- `context` (string, optional): Database name
- `location` (string, optional): Storage location

**Example:**
```json
{
  "entityNames": ["Old_Project", "Deprecated_Concept"]
}
```

---

### ✂️ aim_delete_observations
Remove specific observations from entities.

**Parameters:**
- `deletions` (array, required): List of observations to delete
  - `entityName` (string): Target entity
  - `observations` (array): Specific observations to remove
- `context` (string, optional): Database name
- `location` (string, optional): Storage location

---

### 🔌 aim_delete_relations
Remove relationships between entities.

**Parameters:**
- `relations` (array, required): List of relations to delete
  - `from` (string): Source entity
  - `to` (string): Target entity
  - `relationType` (string): Type of relationship
- `context` (string, optional): Database name
- `location` (string, optional): Storage location

---

## Database Management

### Master Database Concept

The **master database** is your primary memory store, used by default when no specific database is requested.

- **Always named "default"** in database listings
- **Stored as `memory.jsonl`** in the filesystem
- **Always available** in both project-local and global locations
- **Used by default** for all operations unless you specify a different database

### Multiple Databases

Organize memories by topic using named databases:

```json
// Master database (default - no context needed)
{
  "entities": [{"name": "Important_Info", "entityType": "reference"}]
}

// Work database
{
  "context": "work",
  "entities": [{"name": "Q4_Project", "entityType": "project"}]
}

// Personal database
{
  "context": "personal",
  "entities": [{"name": "Mom", "entityType": "person"}]
}
```

### Storage Logic

**File Location Priority:**

1. **Project with `.aim`** → Uses `.aim/memory.jsonl` (project-local)
2. **No project/no .aim** → Uses configured global directory
3. **Named contexts** → Adds suffix: `memory-work.jsonl`, `memory-personal.jsonl`

**File Organization:**

```
# Global Setup
/data/.aim/
├── memory.jsonl           # Master database (default)
├── memory-work.jsonl      # Work database
├── memory-personal.jsonl  # Personal database
└── memory-health.jsonl    # Health database

# Project Setup
/data/.aim/
├── memory.jsonl           # Project master database (default)
└── memory-work.jsonl      # Project work database
```

### Safety System

Every memory file starts with a safety marker:

```json
{"type":"_aim","source":"mcp-knowledge-graph"}
```

- System **refuses to write** to files without this marker
- Prevents accidental overwrite of unrelated JSONL files
- Manual JSONL files need the marker added as the first line

---

## Advanced Usage

### Production Configuration

```yaml
services:
  knowledge-graph-mcp:
    image: mekayelanik/knowledge-graph-mcp:stable
    container_name: knowledge-graph-mcp
    restart: unless-stopped
    ports:
      - "8025:8025"
    volumes:
      - /mnt/persistent-storage/.aim:/data/.aim
    environment:
      # Core settings
      - PORT=8025
      - PUID=1000
      - PGID=1000
      - TZ=UTC
      - PROTOCOL=SHTTP
      
      # Security
      - CORS=https://app.example.com,https://admin.example.com
      - API_KEY=your-secure-production-key-2024
      
      # Storage
      - MEMORY_PATH=/data/.aim
    
    # Resource limits
    deploy:
      resources:
        limits:
          cpus: '1.0'
          memory: 512M
        reservations:
          cpus: '0.5'
          memory: 256M
    
    # Health check
    healthcheck:
      test: ["CMD", "nc", "-z", "localhost", "8025"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 10s
```

### Backup Strategy

```bash
# Backup all memory files
docker exec knowledge-graph-mcp tar -czf /tmp/aim-backup.tar.gz /data/.aim
docker cp knowledge-graph-mcp:/tmp/aim-backup.tar.gz ./backups/

# Or backup host volume directly
tar -czf aim-backup-$(date +%Y%m%d).tar.gz ./aim-memory/

# Automated backup script
#!/bin/bash
BACKUP_DIR="/path/to/backups"
MEMORY_DIR="./aim-memory"
DATE=$(date +%Y%m%d-%H%M%S)
tar -czf "$BACKUP_DIR/aim-backup-$DATE.tar.gz" "$MEMORY_DIR"
find "$BACKUP_DIR" -name "aim-backup-*.tar.gz" -mtime +30 -delete
```

### Reverse Proxy Setup

#### Nginx

```nginx
server {
    listen 80;
    server_name memory.example.com;
    
    location / {
        proxy_pass http://localhost:8025;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

#### Traefik

```yaml
services:
  knowledge-graph-mcp:
    image: mekayelanik/knowledge-graph-mcp:stable
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.memory.rule=Host(`memory.example.com`)"
      - "traefik.http.routers.memory.entrypoints=websecure"
      - "traefik.http.routers.memory.tls.certresolver=myresolver"
      - "traefik.http.services.memory.loadbalancer.server.port=8025"
```

### Multiple Instances for Different Projects

```yaml
services:
  # Global memory instance
  memory-global:
    image: mekayelanik/knowledge-graph-mcp:stable
    container_name: memory-global
    ports:
      - "8025:8025"
    volumes:
      - ./global-memory:/data/.aim
    environment:
      - PORT=8025
  
  # Project A memory
  memory-project-a:
    image: mekayelanik/knowledge-graph-mcp:stable
    container_name: memory-project-a
    ports:
      - "8026:8025"
    volumes:
      - ./project-a-memory:/data/.aim
    environment:
      - PORT=8025
```

---

## Troubleshooting

### Common Issues

**Container Won't Start**
```bash
# Check logs
docker logs knowledge-graph-mcp

# Pull latest image
docker pull mekayelanik/knowledge-graph-mcp:stable

# Restart container
docker restart knowledge-graph-mcp
```

**Memory Files Not Persisting**
```bash
# Verify volume mount
docker inspect knowledge-graph-mcp | grep -A 5 Mounts

# Check file permissions
ls -la ./aim-memory/

# Fix permissions
sudo chown -R 1000:1000 ./aim-memory/
```

**"File does not contain required _aim safety marker" Error**
```bash
# Delete and let system recreate
rm ./aim-memory/memory.jsonl

# Or add safety marker manually
echo '{"type":"_aim","source":"mcp-knowledge-graph"}' | cat - ./aim-memory/memory.jsonl > temp && mv temp ./aim-memory/memory.jsonl
```

**Permission Denied Errors**
```bash
# Check your user ID
id $USER

# Update PUID/PGID in docker-compose.yml
environment:
  - PUID=1001  # Your actual UID
  - PGID=1001  # Your actual GID
```

**Connection Refused**
```bash
# Verify container is running
docker ps | grep knowledge-graph-mcp

# Test health endpoint
curl http://localhost:8025/healthz
```

---

## Use Cases & Examples

### Personal Knowledge Management

```json
// Store personal contacts
{
  "entities": [{
    "name": "Alice_Smith",
    "entityType": "person",
    "observations": [
      "Best friend from college",
      "Birthday: March 15",
      "Lives in Seattle"
    ]
  }]
}
```

### Project Management

```json
// Track project details
{
  "context": "work",
  "entities": [{
    "name": "Website_Redesign_Q4",
    "entityType": "project",
    "observations": [
      "Started: October 1, 2024",
      "Deadline: December 31, 2024",
      "Status: In Progress"
    ]
  }]
}
```

---

## Best Practices

### Naming Conventions

```json
// Use underscores for entity names
"John_Doe"           // ✅ Good
"John Doe"           // ❌ Avoid spaces

// Use descriptive entity types
"person"             // ✅ Good
"project"            // ✅ Good
"thing"              // ❌ Too vague
```

### Security Recommendations

1. **Use persistent volumes** for production
2. **Enable API key authentication** when exposing publicly
3. **Never use `CORS=*`** in production
4. **Regular backups** of memory files
5. **Run as non-root** (default PUID/PGID)
6. **Use reverse proxy** with rate limiting
7. **Monitor logs** for suspicious activity
8. **Keep Docker image updated**

---

## Resources & Support

### Documentation
- 📦 [NPM Package](https://www.npmjs.com/package/mcp-knowledge-graph)
- 🔧 [GitHub Repository](https://github.com/mekayelanik/knowledge-graph-mcp)
- 🐳 [Docker Hub](https://hub.docker.com/r/mekayelanik/knowledge-graph-mcp)

### MCP Resources
- 📘 [MCP Protocol Specification](https://modelcontextprotocol.io)
- 🎓 [MCP Documentation](https://modelcontextprotocol.io/docs)
- 💬 [MCP Community](https://discord.gg/mcp)

### Getting Help
- Check logs: `docker logs knowledge-graph-mcp`
- Test health: `curl http://localhost:8025/healthz`
- [GitHub Issues](https://github.com/mekayelanik/knowledge-graph-mcp-docker/issues)

---

## License

GPL License - See [LICENSE](LICENSE) for details.

**Disclaimer:** Unofficial Docker image for [mcp-knowledge-graph](https://www.npmjs.com/package/mcp-knowledge-graph). This containerized version is maintained independently.

---

<div align="center">

**🧠 Build Persistent AI Memory • 🔗 Connect Knowledge • 📊 Organize Information**

[Report Image Related Issue](https://github.com/mekayelanik/knowledge-graph-mcp-docker/issues) • [Request Image Related Feature](https://github.com/mekayelanik/knowledge-graph-mcp-docker/issues) • [Contribute](https://github.com/mekayelanik/knowledge-graph-mcp/pulls)

</div>