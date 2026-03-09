# Bankruptcy Observer — API & MCP

Public documentation and registry metadata for the **Bankruptcy Observer API** and **MCP server**. This repository contains no application source code.

## What this is

- **Bankruptcy REST API** — Programmatic access to US business bankruptcy data (search, case by EIN, docket items, cases by NAICS/state, and more).
- **Bankruptcy MCP server** — The same data and queries via the [Model Context Protocol](https://modelcontextprotocol.io/) for AI assistants and LLM workflows.

Data covers **US business bankruptcies** (federal courts) only.

## Documentation (canonical)

Full specification — queries, response fields, sign-up, and pricing — is hosted on our servers. No auth required to read:

- **API docs:** [https://api.bankruptcyobserver.com/docs](https://api.bankruptcyobserver.com/docs)
- **MCP docs:** [https://mcp.bankruptcyobserver.com/docs](https://mcp.bankruptcyobserver.com/docs)

Both URLs return the same Markdown spec.

## Sign-up and access

Access requires an account and an API token. Sign-up and subscription plans are on the main product site: [Bankruptcy Observer](https://bankruptcyobserver.com). After sign-up you receive an API token to use with both the REST API and the MCP server (e.g. `Authorization: Bearer <token>` or `X-API-Key: <token>`).

## Endpoints

| Service | Endpoint | Auth |
|--------|----------|------|
| REST API | `https://api.bankruptcyobserver.com/api/v1/...` | API token required |
| MCP      | `https://mcp.bankruptcyobserver.com/mcp`        | API token required |
| Docs     | `https://api.bankruptcyobserver.com/docs` or `https://mcp.bankruptcyobserver.com/docs` | None |

## Registry

This repo includes a `server.json` for the [Official MCP Registry](https://modelcontextprotocol.io/registry/about). It describes the remote MCP server and how to connect (URL + API token header). Use it when publishing to the registry via the Registry CLI; the registry points to this GitHub repo for metadata only.

## License

Documentation and metadata in this repository are provided for discovery and integration. The Bankruptcy Observer product, API, and MCP service are offered under separate terms; see the product website.
