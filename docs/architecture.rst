.. _pg-mcp-server-architecture:

######################################################################
Architecture
######################################################################

.. contents:: Table of Contents
   :local:
   :depth: 2


.. _pg-mcp-server-arch-overview:

Overview
======================================================================

pg-mcp-server is a single-container application that implements the
Model Context Protocol (MCP) over Server-Sent Events (SSE) transport.
It connects to external PostgreSQL databases on demand and provides
schema discovery, query execution, and extension context to AI agents.


.. _pg-mcp-server-arch-containers:

Container Layout
======================================================================

.. list-table::
   :header-rows: 1
   :widths: 15 25 15 45

   * - Container
     - Image
     - Restart
     - Description
   * - pg-mcp
     - pg-mcp-server-pg-mcp (local build)
     - unless-stopped
     - MCP server with SSE transport


.. _pg-mcp-server-arch-ports:

Port Mappings
======================================================================

.. list-table::
   :header-rows: 1
   :widths: 15 15 15 55

   * - Service
     - Host Port
     - Container Port
     - Purpose
   * - pg-mcp
     - 8500
     - 8000
     - MCP SSE endpoint


.. _pg-mcp-server-arch-volumes:

Data Volumes
======================================================================

.. list-table::
   :header-rows: 1
   :widths: 20 30 50

   * - Volume
     - Mount Path
     - Purpose
   * - ./server
     - /app/server
     - Development hot-reload mount (source code)

The application is stateless -- it does not persist data locally.
All database connections are to external PostgreSQL instances.


.. _pg-mcp-server-arch-network:

Network Configuration
======================================================================

- **Network**: ``pg-mcp-network`` (bridge driver)
- The server connects outbound to PostgreSQL databases; no inbound
  database connections are needed


.. _pg-mcp-server-arch-environment:

Environment Configuration
======================================================================

.. list-table::
   :header-rows: 1
   :widths: 25 20 55

   * - Variable
     - Value
     - Description
   * - LOG_LEVEL
     - DEBUG
     - Application logging verbosity
   * - PYTHONUNBUFFERED
     - 1
     - Disable Python output buffering for real-time logs

Environment variables are set directly in ``docker-compose.yml``.
The ``.env`` file contains credentials for example clients only
(not used by the server container).


.. _pg-mcp-server-arch-build:

Build Process
======================================================================

The Dockerfile performs:

1. Starts from ``python:3.13-slim``
2. Installs ``curl`` and ``ca-certificates``
3. Installs ``uv`` package manager via official installer
4. Copies project files into ``/app``
5. Runs ``uv sync --frozen`` to install dependencies from lockfile
6. Runs ``uv run -m server.app`` as the entrypoint

Key dependencies (from ``pyproject.toml``):

- ``mcp[cli]>=1.5.0`` -- Model Context Protocol framework
- ``asyncpg>=0.30.0`` -- Async PostgreSQL client
- ``anthropic>=0.49.0`` -- Anthropic API client
- ``jinja2>=3.1.6`` -- Template engine for prompts
- ``sqlglot>=26.16.2`` -- SQL parser and transpiler
- ``pydantic-ai>=0.0.46`` -- AI agent framework
- ``tabulate>=0.9.0`` -- Table formatting


.. _pg-mcp-server-arch-mcp-interface:

MCP Interface
======================================================================

Tools
----------------------------------------------------------------------

- **connect** -- Register a PostgreSQL connection string, returns a
  secure connection ID
- **disconnect** -- Close a database connection
- **pg_query** -- Execute read-only SQL queries using a connection ID
- **pg_explain** -- Analyze query execution plans in JSON format

Resources
----------------------------------------------------------------------

Schema discovery via URI patterns:

- ``pgmcp://{conn_id}/`` -- Comprehensive database description
- ``pgmcp://{conn_id}/schemas`` -- List schemas
- ``pgmcp://{conn_id}/schemas/{schema}/tables`` -- List tables
- ``pgmcp://{conn_id}/schemas/{schema}/tables/{table}/columns`` --
  Column details

Prompts
----------------------------------------------------------------------

Jinja2-based prompt templates in ``server/prompts/`` for guiding
AI agent interactions with database context.
