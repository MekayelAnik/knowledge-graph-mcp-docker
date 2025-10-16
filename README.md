# Knowledge Graph MCP Server
### Multi-Architecture Docker Image for AI Memory & Persistent Knowledge

<div align="left">

<img alt="knowledge-graph-mcp" src="https://img.shields.io/badge/Knowledge%20Graph-MCP-4A90E2?style=for-the-badge&logo=graphql&logoColor=white" width="400">

[![Docker Pulls](https://img.shields.io/docker/pulls/mekayelanik/knowledge-graph-mcp.svg?style=flat-square)](https://hub.docker.com/r/mekayelanik/knowledge-graph-mcp)
[![Docker Stars](https://img.shields.io/docker/stars/mekayelanik/knowledge-graph-mcp.svg?style=flat-square)](https://hub.docker.com/r/mekayelanik/knowledge-graph-mcp)
[![License](https://img.shields.io/badge/license-GPL-blue.svg?style=flat-square)](https://raw.githubusercontent.com/MekayelAnik/knowledge-graph-mcp-docker/refs/heads/main/LICENSE)

**[NPM Package](https://www.npmjs.com/package/mcp-knowledge-graph)** • **[GitHub Repository](https://github.com/mekayelanik/knowledge-graph-mcp-docker)** • **[Docker Hub](https://hub.docker.com/r/mekayelanik/knowledge-graph-mcp)**

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
        "aim_search_nodes",
        "aim_read_graph",
        "aim_open_nodes",
        "aim_list_databases"
      ]
    }
  }
}
```

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
        
        # Optional: Add API key header
        # proxy_set_header X-API-Key "your-api-key";
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

### Docker Network Setup

```yaml
services:
  knowledge-graph-mcp:
    image: mekayelanik/knowledge-graph-mcp:stable
    container_name: knowledge-graph-mcp
    networks:
      - ai-network
    volumes:
      - ./aim-memory:/data/.aim
    environment:
      - PORT=8025
      - PROTOCOL=SHTTP
    
  ai-assistant:
    image: ai-assistant:latest
    networks:
      - ai-network
    environment:
      - MEMORY_URL=http://knowledge-graph-mcp:8025/mcp

networks:
  ai-network:
    driver: bridge
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
  
  # Project B memory
  memory-project-b:
    image: mekayelanik/knowledge-graph-mcp:stable
    container_name: memory-project-b
    ports:
      - "8027:8025"
    volumes:
      - ./project-b-memory:/data/.aim
    environment:
      - PORT=8025
```

---

## Troubleshooting

### Pre-Flight Checklist

- ✅ Docker 23.0+
- ✅ Port 8025 available
- ✅ Persistent volume mounted
- ✅ Correct file permissions (PUID/PGID)
- ✅ Latest stable image

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
# The file may not belong to this system
# Option 1: Delete and let system recreate
rm ./aim-memory/memory.jsonl

# Option 2: Add safety marker manually
echo '{"type":"_aim","source":"mcp-knowledge-graph"}' | cat - ./aim-memory/memory.jsonl > temp && mv temp ./aim-memory/memory.jsonl
```

**Memories Going to Unexpected Locations**
```bash
# List all databases
# Use aim_list_databases tool in your MCP client

# Check memory directory contents
docker exec knowledge-graph-mcp ls -la /data/.aim/

# Verify MEMORY_PATH setting
docker exec knowledge-graph-mcp env | grep MEMORY_PATH
```

**Permission Denied Errors**
```bash
# Check your user ID
id $USER

# Update PUID/PGID in docker-compose.yml
environment:
  - PUID=1001  # Your actual UID
  - PGID=1001  # Your actual GID

# Recreate container
docker compose down
docker compose up -d
```

**Connection Refused**
```bash
# Verify container is running
docker ps | grep knowledge-graph-mcp

# Check port binding
docker port knowledge-graph-mcp

# Test health endpoint
curl http://localhost:8025/healthz
```

**CORS Errors**
```yaml
# Development - allow all
environment:
  - CORS=*

# Production - specific origins
environment:
  - CORS=https://yourdomain.com,https://app.yourdomain.com
```

**API Key Authentication Issues**
```bash
# Verify API key is set
docker exec knowledge-graph-mcp env | grep API_KEY

# Check if MCP client is sending key
# Most clients don't support API key headers yet
# Consider using CORS restrictions instead
```

**Too Many Similar Databases**
```bash
# List all database files
docker exec knowledge-graph-mcp ls -la /data/.aim/

# Delete unwanted databases
docker exec knowledge-graph-mcp rm /data/.aim/memory-unwanted.jsonl

# Or delete from host
rm ./aim-memory/memory-unwanted.jsonl
```

### Debug Mode

```yaml
# Enable verbose debugging
environment:
  - DEBUG_MODE=verbose

# Container will pause for inspection
docker exec -it knowledge-graph-mcp /bin/bash
```

### Health Check Testing

```bash
# Basic health check
curl http://localhost:8025/healthz

# Test MCP endpoint
curl http://localhost:8025/mcp

# Test with tool invocation
curl -X POST http://localhost:8025/mcp \
  -H "Content-Type: application/json" \
  -d '{"method":"tools/list"}'
```

---

## Resources & Support

### Documentation
- 📦 [NPM Package](https://www.npmjs.com/package/mcp-knowledge-graph)
- 🔧 [GitHub Repository](https://github.com/mekayelanik/knowledge-graph-mcp-docker)
- 🐳 [Docker Hub](https://hub.docker.com/r/mekayelanik/knowledge-graph-mcp)

### MCP Resources
- 📘 [MCP Protocol Specification](https://modelcontextprotocol.io)
- 🎓 [MCP Documentation](https://modelcontextprotocol.io/docs)
- 💬 [MCP Community](https://discord.gg/mcp)

### Getting Help

**Docker Image Issues:**
- [GitHub Issues](https://github.com/mekayelanik/knowledge-graph-mcp-docker/issues)
- [Discussions](https://github.com/mekayelanik/knowledge-graph-mcp-docker/discussions)

**General Questions:**
- Check logs: `docker logs knowledge-graph-mcp`
- Test health: `curl http://localhost:8025/healthz`
- Verify memory files: `ls -la ./aim-memory/`

### Updating

```bash
# Docker Compose
docker compose pull
docker compose up -d

# Docker CLI
docker pull mekayelanik/knowledge-graph-mcp:stable
docker stop knowledge-graph-mcp
docker rm knowledge-graph-mcp
# Re-run your docker run command
```

### Version Pinning

```yaml
# Use specific version
services:
  knowledge-graph-mcp:
    image: mekayelanik/knowledge-graph-mcp:1.0.0

# Or use stable tag (recommended)
services:
  knowledge-graph-mcp:
    image: mekayelanik/knowledge-graph-mcp:stable
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

// Use consistent relation types
"works_on"           // ✅ Good
"attended"           // ✅ Good
"related_to"         // ❌ Too generic
```

### Database Organization

```yaml
# Work-related memories
context: "work"
- Projects, meetings, colleagues, tasks

# Personal memories
context: "personal"
- Family, friends, personal events, hobbies

# Health information
context: "health"
- Medical history, appointments, medications

# Research and learning
context: "research"
- Papers, concepts, experiments, findings
```

### Backup Schedule

```bash
# Daily backups (keep 7 days)
0 2 * * * /path/to/backup-script.sh daily 7

# Weekly backups (keep 4 weeks)
0 3 * * 0 /path/to/backup-script.sh weekly 4

# Monthly backups (keep 12 months)
0 4 1 * * /path/to/backup-script.sh monthly 12
```

### Security Recommendations

1. **Use persistent volumes** for production
2. **Enable API key authentication** when exposing publicly
3. **Never use `CORS=*`** in production
4. **Regular backups** of memory files
5. **Run as non-root** (default PUID/PGID)
6. **Use reverse proxy** with rate limiting for public access
7. **Monitor logs** for suspicious activity
8. **Keep Docker image updated**
9. **Restrict network access** to trusted clients only
10. **Use specific version tags** for production stability

---

## Performance Tips

### Optimize Storage

```yaml
# Use SSD for better I/O performance
volumes:
  - /mnt/ssd/aim-memory:/data/.aim

# Regular maintenance
# Compact large memory files periodically
# Remove unused databases
```

### Resource Allocation

```yaml
# Light usage (personal projects)
deploy:
  resources:
    limits:
      cpus: '0.5'
      memory: 256M

# Medium usage (small teams)
deploy:
  resources:
    limits:
      cpus: '1.0'
      memory: 512M

# Heavy usage (large knowledge graphs)
deploy:
  resources:
    limits:
      cpus: '2.0'
      memory: 1G
```

### Monitoring

```yaml
# Add logging driver
services:
  knowledge-graph-mcp:
    image: mekayelanik/knowledge-graph-mcp:stable
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"
    
    # Prometheus metrics (if available)
    labels:
      - "prometheus.scrape=true"
      - "prometheus.port=8025"
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
      "Lives in Seattle",
      "Works at Tech Corp"
    ]
  }]
}

// Track important dates
{
  "entities": [{
    "name": "Anniversary_2024",
    "entityType": "event",
    "observations": [
      "Date: June 20, 2024",
      "10th wedding anniversary",
      "Reservation at Italian restaurant"
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
      "Budget: $50,000",
      "Status: In Progress"
    ]
  }],
  "relations": [{
    "from": "Website_Redesign_Q4",
    "to": "Alice_Smith",
    "relationType": "project_manager"
  }]
}
```

### Research & Learning

```json
// Store research findings
{
  "context": "research",
  "entities": [{
    "name": "Neural_Networks_Paper_2024",
    "entityType": "research",
    "observations": [
      "Authors: Smith et al.",
      "Published: Nature, 2024",
      "Key finding: New activation function improves accuracy by 15%",
      "Relevant for my thesis work"
    ]
  }]
}
```

### Health Tracking

```json
// Medical information
{
  "context": "health",
  "entities": [{
    "name": "Dr_Johnson",
    "entityType": "doctor",
    "observations": [
      "Specialty: Cardiology",
      "Clinic: Heart Health Center",
      "Phone: (555) 123-4567",
      "Last visit: September 15, 2024"
    ]
  }]
}
```

---

## Migration & Integration

### Migrating from Local NPM Installation

If you're currently using the NPM package locally:

```bash
# Export your existing memory
# Assuming you have memory files in ~/.aim/
cp -r ~/.aim/ ./aim-memory/

# Update your docker-compose.yml to use this directory
volumes:
  - ./aim-memory:/data/.aim

# Start the container
docker compose up -d

# Your AI assistant will now access the same memory files
```

### Integrating with CI/CD

```yaml
# GitHub Actions example
name: Backup AI Memory

on:
  schedule:
    - cron: '0 0 * * *'  # Daily at midnight

jobs:
  backup:
    runs-on: ubuntu-latest
    steps:
      - name: Backup memory files
        run: |
          docker exec knowledge-graph-mcp tar -czf /tmp/backup.tar.gz /data/.aim
          docker cp knowledge-graph-mcp:/tmp/backup.tar.gz ./backup.tar.gz
      
      - name: Upload to storage
        uses: actions/upload-artifact@v3
        with:
          name: memory-backup-${{ github.run_number }}
          path: backup.tar.gz
```

### Using with Multiple AI Assistants

```yaml
# Share memory across different AI tools
services:
  knowledge-graph-mcp:
    image: mekayelanik/knowledge-graph-mcp:stable
    ports:
      - "8025:8025"
    volumes:
      - shared-memory:/data/.aim
    networks:
      - ai-network

  # All your AI assistants connect to the same memory server
  # via http://knowledge-graph-mcp:8025/mcp

volumes:
  shared-memory:
    driver: local

networks:
  ai-network:
    driver: bridge
```

---

## Frequently Asked Questions

### What is the difference between master database and named databases?

The **master database** (always named "default") is your primary memory store used by default. **Named databases** are optional additions for organizing specific topics like work, personal, or health. Think of the master as your main notebook and named databases as specialized notebooks for different subjects.

### How do I choose between project-local and global storage?

Use **project-local** (`.aim` directory) when:
- Working on specific projects with isolated memory
- Need project-specific context that shouldn't mix with other projects
- Want memory to stay with the project files

Use **global** storage when:
- Need access to the same memory from multiple projects
- Working on general tasks not tied to a specific project
- Want centralized memory management

### Can I use both project-local and global storage simultaneously?

Yes! The system automatically detects `.aim` directories and uses project-local storage when present. You can force global storage by using the `location: "global"` parameter in tool calls.

### How do I migrate memory between locations?

```bash
# Copy from global to project
cp ~/.aim/memory.jsonl ./my-project/.aim/memory.jsonl

# Copy from project to global
cp ./my-project/.aim/memory.jsonl ~/.aim/memory-project-backup.jsonl
```

### What happens if I lose the memory files?

Memory files contain all your stored knowledge. **Always maintain backups!** If lost:
- Container will create new empty memory files
- All previous entities, relations, and observations are lost
- No automatic recovery without backups

### Can multiple containers share the same memory files?

**Not recommended!** Multiple containers writing to the same files can cause:
- Data corruption
- Race conditions
- Inconsistent state

Instead, run one container and have all clients connect to it.

### How much storage do memory files use?

Memory files are text-based JSONL format:
- Empty database: ~100 bytes (safety marker)
- Small personal memory (100 entities): ~50-100 KB
- Medium knowledge base (1000 entities): ~500 KB - 1 MB
- Large enterprise knowledge (10000+ entities): ~5-10 MB+

Very efficient storage for long-term knowledge retention.

### Can I edit memory files manually?

Yes, but carefully:
1. Stop the container first: `docker stop knowledge-graph-mcp`
2. Edit the JSONL file (each line is valid JSON)
3. Ensure the first line is the safety marker
4. Restart the container

Better approach: Use the provided tools through your AI assistant.

### How do I search for specific information?

Use the `aim_search_nodes` tool with keywords:
```json
{
  "query": "conference"
}
```

This searches all entity names and observations, returning matches across your entire knowledge graph.

### What's the best way to organize large amounts of knowledge?

1. **Use multiple databases** for different topics (work, personal, research)
2. **Create clear entity types** (person, project, event, concept, location)
3. **Use descriptive names** with underscores (Conference_2024, not just Event)
4. **Add detailed observations** with dates and context
5. **Build relationships** to connect related entities
6. **Regular cleanup** of outdated or duplicate information

---

## Changelog

### Version 1.0.0
- Initial release with full MCP support
- Master database implementation
- Multiple database support
- Project-local and global storage
- Safety marker system
- HTTP, SSE, and WebSocket protocols
- CORS configuration
- API key authentication
- Health check endpoint
- Auto-approve compatible with all major MCP clients

---

## Contributing

We welcome contributions! Here's how you can help:

### Reporting Issues
- Check existing issues first
- Provide Docker version and environment details
- Include relevant logs (`docker logs knowledge-graph-mcp`)
- Describe expected vs actual behavior

### Feature Requests
- Describe the use case
- Explain why it would be useful
- Suggest implementation approach if possible

### Pull Requests
- Fork the repository
- Create a feature branch
- Follow existing code style
- Test thoroughly
- Update documentation
- Submit PR with clear description

---

## License

GPL License - See [LICENSE](https://raw.githubusercontent.com/MekayelAnik/knowledge-graph-mcp-docker/refs/heads/main/LICENSE) for details.

**Disclaimer:** Unofficial Docker image for [mcp-knowledge-graph](https://www.npmjs.com/package/mcp-knowledge-graph). This containerized version is maintained independently. Please report Docker-specific issues to this repository and NPM package issues to the original package maintainers.

---

## Acknowledgments

- Built on top of the excellent [mcp-knowledge-graph](https://www.npmjs.com/package/mcp-knowledge-graph) NPM package
- Uses [Supergateway](https://www.npmjs.com/package/supergateway) for MCP protocol transport
- Inspired by the [Model Context Protocol](https://modelcontextprotocol.io) specification
- Thanks to the MCP community for feedback and testing

---

<div align="center">

**🧠 Build Persistent AI Memory • 🔗 Connect Knowledge • 📊 Organize Information**

[Report Docker Issue](https://github.com/mekayelanik/knowledge-graph-mcp-docker/issues) • [Request Feature](https://github.com/mekayelanik/knowledge-graph-mcp-docker/issues) • [Contribute](https://github.com/mekayelanik/knowledge-graph-mcp-docker/pulls)

</div>