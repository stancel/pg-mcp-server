.. _pg-mcp-server-changelog:

######################################################################
Changelog
######################################################################

.. _pg-mcp-server-changelog-2026-03-22b:

2026-03-22 -- Documentation Sync and Port Update
======================================================================

- Updated ``docker-compose.yml``: removed deprecated ``version: '3.8'``,
  changed host port from 8000 to 8500 to match running container
- Updated ``example-clients/claude_cli.py`` default URL to port 8500
- Updated ``test.py`` default URL to port 8500
- Updated ``docs/architecture.rst`` MCP interface to include all tools
  (added ``pg_metadata``), all resources (added schema details,
  materialized views, sample data, rowcount, extensions), and all
  prompts (natural_language, data_visualization)
- Updated CLAUDE.md source structure to include viz tools
- Fixed ``.python-version`` to ``3.13.0``


.. _pg-mcp-server-changelog-2026-03-22:

2026-03-22 -- Project Scaffolding
======================================================================

- Initial project scaffolding with CLAUDE.md, README.rst, CHANGELOG.rst,
  TODO.rst, and docs/architecture.rst
- Documented current running state: single container ``pg-mcp`` on port
  8500, built from local Dockerfile with Python 3.13-slim and uv
- App version 0.1.0, upstream https://github.com/stuzero/pg-mcp-server
- Container running for 7+ months, currently healthy
