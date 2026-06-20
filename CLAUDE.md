# CLAUDE.md - pg-mcp-server

## Project Overview

**pg-mcp-server** is a Model Context Protocol (MCP) server for PostgreSQL databases with enhanced capabilities for AI agents. It provides schema discovery, query execution, and extension context (PostGIS, pgvector) over SSE transport.

- **App location**: `~/docker/pg-mcp-server/`
- **Local URL**: http://10.0.1.220:8500 (SSE endpoint at `/sse`)
- **Public URL**: None (local only)
- **Fork (origin)**: https://github.com/stancel/pg-mcp-server
- **Upstream**: https://github.com/stuzero/pg-mcp-server
- **Docs**: https://stuzero.github.io/pg-mcp/
- **Current version**: 0.1.0 (custom build from source)

## Architecture

### Containers

| Container | Image | Ports | Description |
|---|---|---|---|
| pg-mcp | pg-mcp-server-pg-mcp (built from local Dockerfile) | 8500:8000 | MCP server with SSE transport |

### Key Details

- Built from a local `Dockerfile` using Python 3.13-slim + uv
- Source code mounted at `./server:/app/server` for hot-reloading
- No persistent volumes (stateless server, connects to external PostgreSQL databases)
- Uses `pg-mcp-network` bridge network
- Dependencies managed via `uv` with `uv.lock` lockfile

### Source Structure

```
server/
  app.py          - FastMCP application entry point
  config.py       - Configuration settings
  database.py     - asyncpg connection management
  logging_config.py - Logging setup
  prompts/
    natural_language.py   - Natural language to SQL prompt registration
    data_visualization.py - Data visualization prompt registration
    templates/            - Jinja2 templates (generate_sql, generate_vega,
                            justify_sql, validate_nl)
  resources/
    schema.py       - Schema discovery resources (schemas, tables, columns)
    data.py         - Data resources (sample data, rowcount)
    extensions.py   - Extension context resources
    extensions/     - YAML extension definitions (postgis, pgvector)
    sql/            - Raw SQL files for schema queries
  tools/
    connection.py   - connect/disconnect tools
    query.py        - pg_query and pg_explain tools
    viz.py          - pg_metadata visualization tool
example-clients/
  claude_cli.py         - Claude-powered natural language SQL client
  gemini-agent-cli.py   - Gemini-powered agent client
get_schema.py     - Utility: dump full schema to JSON via MCP
test.py           - Integration test script for MCP server
```

## Compose Files

- `docker-compose.yml` - Single service definition (pg-mcp)
- `Dockerfile` - Python 3.13-slim with uv, syncs dependencies from lockfile

## Key Commands

```bash
# Status
docker compose ps

# Logs
docker compose logs --tail=50
docker compose logs -f  # follow

# Restart
docker compose restart

# Rebuild after code changes (non-mounted files)
docker compose up -d --build

# Stop / Start
docker compose down
docker compose up -d

# Run tests
python test.py "postgresql://user:pass@host:port/db"

# Example client
python example-clients/claude_cli.py "your natural language query"
```

## Troubleshooting

```bash
# Check container health
docker compose ps

# View recent logs
docker compose logs --tail=50

# Check SSE endpoint
curl -s http://10.0.1.220:8500/sse

# Check disk usage
docker system df -v 2>&1 | grep pg-mcp

# Inspect container
docker inspect pg-mcp

# Rebuild from scratch
docker compose down && docker compose up -d --build
```

## Notes

- The server runs in **read-only mode** by default (enforced via transaction settings)
- Connection credentials are only sent once during initial connection; opaque connection IDs are used thereafter
- The `.env` file contains API keys and database credentials for example clients (git-ignored)
- Shared conventions (git workflow, documentation standards, Docker conventions) are inherited from `~/docker/CLAUDE.md`
