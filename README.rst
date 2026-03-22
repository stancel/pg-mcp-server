.. _pg-mcp-server-readme:

######################################################################
pg-mcp-server
######################################################################

**PostgreSQL Model Context Protocol (MCP) Server**

A Model Context Protocol server for PostgreSQL databases with enhanced
capabilities for AI agents, providing schema discovery, query execution,
and extension context over SSE transport.

.. contents:: Table of Contents
   :local:
   :depth: 2


.. _pg-mcp-server-overview:

Overview
======================================================================

pg-mcp-server provides a comprehensive API for AI agents to discover,
connect to, query, and understand PostgreSQL databases through MCP's
resource-oriented architecture.

Key features:

- **Multi-database support** -- connect to multiple PostgreSQL databases
  simultaneously
- **Rich catalog information** -- table/column descriptions from the
  database catalog
- **Extension context** -- YAML-based knowledge for PostGIS and pgvector
- **Query explanation** -- dedicated tool for analyzing execution plans
- **SSE transport** -- production-ready Server-Sent Events transport
- **Read-only by default** -- enforced via transaction settings


.. _pg-mcp-server-access:

Access
======================================================================

.. list-table::
   :header-rows: 1
   :widths: 20 50

   * - Detail
     - Value
   * - Local URL
     - http://10.0.1.220:8500
   * - SSE Endpoint
     - http://10.0.1.220:8500/sse
   * - Public URL
     - None (local only)
   * - Host
     - Cybertron (10.0.1.220)


.. _pg-mcp-server-architecture:

Architecture
======================================================================

Single-container application built from a local Dockerfile:

- **Runtime**: Python 3.13-slim with uv package manager
- **Framework**: FastMCP (Python MCP library)
- **Database client**: asyncpg (async PostgreSQL)
- **Port mapping**: 8500 (host) -> 8000 (container)
- **Network**: ``pg-mcp-network`` (bridge)

The server source is volume-mounted for development hot-reloading.

See ``docs/architecture.rst`` for detailed architecture documentation.


.. _pg-mcp-server-key-commands:

Key Commands
======================================================================

.. code-block:: bash

   # Check status
   docker compose ps

   # View logs
   docker compose logs --tail=50

   # Restart
   docker compose restart

   # Rebuild after changes
   docker compose up -d --build

   # Stop / Start
   docker compose down
   docker compose up -d


.. _pg-mcp-server-references:

References
======================================================================

- Upstream repository: https://github.com/stuzero/pg-mcp-server
- Project documentation: https://stuzero.github.io/pg-mcp/
- Model Context Protocol: https://modelcontextprotocol.io
