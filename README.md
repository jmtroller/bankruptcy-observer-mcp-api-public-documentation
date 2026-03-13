# Bankruptcy Observer — API & MCP

Public documentation and registry metadata for the **Bankruptcy Observer API** and **MCP server**. This repository contains no application source code.

## What this is

- **Bankruptcy REST API** — Programmatic access to US business bankruptcy data (search, case by EIN, docket items, cases by NAICS/state, and more).
- **Bankruptcy MCP server** — The same data and queries via the [Model Context Protocol](https://modelcontextprotocol.io/) for AI assistants and LLM workflows.

Data covers **US business bankruptcies** (federal courts) only. Consumer-only filings and non-US jurisdictions are not included.

---

## Sign-up and access

Access requires an account and an API token. Sign-up and subscription plans are on the main product site: [Bankruptcy Observer](https://bankruptcyobserver.com). After sign-up you receive an API token to use with both the REST API and the MCP server.

For questions about access, billing, or data coverage, see the contact page: https://www.bankruptcyobserver.com/contact.

**Send the token in one of these ways:**

- `Authorization: Bearer <your-token>`
- `X-API-Key: <your-token>`
- `Api-Key: <your-token>`

---

## Endpoints

| Service | Endpoint | Auth |
|--------|----------|------|
| REST API | `https://api.bankruptcyobserver.com/api/v1/...` | API token required |
| MCP      | `https://mcp.bankruptcyobserver.com/mcp`        | API token required |
| Docs     | `https://api.bankruptcyobserver.com/docs` or `https://mcp.bankruptcyobserver.com/docs` | None |

The `/mcp` path is the MCP protocol endpoint (POST only). For human-readable documentation, use the **Docs** URLs above (GET, no auth).

---

## Available queries and responses

The same queries are available on both the REST API and the MCP server. Each query returns JSON; case-level responses include the standard fields below unless noted.

### Query list

| # | Query | Inputs | What you get | Notes |
|---|-------|--------|--------------|--------|
| 1 | **Search** | `search_term`, `limit`, `skip`, optional `court_id` or `court_state` | List of cases with standard fields | Default: prefix (starts-with) on name. Use `*term` for contains (e.g. `*Hotel`). For case number lookup use Case by case number. |
| 2 | **Case by EIN** | `ein` | One case (or none) **+** `einInDatabase`: `"Yes"` or `"No"` | Single match; flag says whether we have this EIN. |
| 3 | **Docket items** | `shortCaseNumber`, optional `court_id` or `court_state`, `limit` | Case identifier, `dateDocketUpdated`, list of entries (itemNumber, itemText, itemDate, …) | Case resolved by short case number + optional court. |
| 4 | **Case by case number** | `shortCaseNumber`, optional `court_id` or `court_state` | One case with standard fields | Use when same number exists in multiple courts. |
| 5 | **Cases by NAICS** | `naics` (2–4 digits), `limit`, optional `start_date`, `end_date` | List of cases with standard fields | NAICS must be 2–4 digits; more than 4 is rejected. |
| 6 | **Cases by state** | `state` (2-letter), `limit`, optional `start_date`, `end_date` | List of cases with standard fields | Optional date range on dateFiled. |
| 7 | **Case status / dates** | — | Returned as part of case data | dateFiled, dateClosed, dateDismissed; isOpen / isClosed / isDismissed. |

**Pagination:** List responses support `limit` and `skip` where applicable; responses include `total` or similar where useful.

**Date range:** For NAICS and state queries, optional `start_date` and `end_date` filter on `dateFiled`. Search does not support date filters.

### Standard fields (case-level)

Returned for any query that returns case rows:

| Field | Description |
|-------|-------------|
| name | Debtor/case name |
| court | Court name |
| shortCaseNumber | Short case number (e.g. 22-12345) |
| dateDocketUpdated | When docket/case data was last updated in our system |
| dateFiled | Filing date |

**Optional** (included when relevant): `courtState`, `chapter`, `NAICS`, `industry`, `dateClosed`, `dateDismissed`, `isOpen` / `isClosed` / `isDismissed`, `assetAmount`, `liabAmount`.

### EIN query: `einInDatabase`

For **Case by EIN** the response always includes:

- **`einInDatabase`** — `"Yes"` if we have at least one case with this EIN (and the case payload); `"No"` if we have no case with this EIN (case payload null or omitted).

### Docket response shape

For **Docket items** you get:

- Case identifier (e.g. shortCaseNumber, court).
- **dateDocketUpdated** — when this case’s docket was last updated in our system.
- **entries** — array of objects with at least `itemNumber`, `itemText`, `itemDate` (and optionally pageCount, pacerDocId).

---

## API vs MCP mapping

| Query | REST API (POST) | MCP tool |
|-------|------------------|----------|
| Search | `/api/v1/search` | `search_bankruptcy_cases_tool` |
| Case by EIN | `/api/v1/case/ein` | `get_case_by_ein_tool` |
| Docket items | `/api/v1/docket` | `get_docket_entries_tool` |
| Case by case number | `/api/v1/case/by-number` | `get_case_by_case_number_tool` |
| Cases by NAICS | `/api/v1/cases/by-naics` | `get_cases_by_naics_tool` |
| Cases by state | `/api/v1/cases/by-state` | `get_cases_by_state_tool` |

Same backend for both; response shape is identical.

---

## Full documentation (canonical)

The full spec (same content as above, plus any updates) is also served with no auth at:

- **API docs:** [https://api.bankruptcyobserver.com/docs](https://api.bankruptcyobserver.com/docs)
- **MCP docs:** [https://mcp.bankruptcyobserver.com/docs](https://mcp.bankruptcyobserver.com/docs)

Both URLs return the same Markdown. Use them for the latest version or for AI/bot discovery.

---

## Registry

This repo includes a `server.json` for the [Official MCP Registry](https://modelcontextprotocol.io/registry/about). It describes the remote MCP server and how to connect (URL + API token header). Use it when publishing to the registry via the Registry CLI; the registry points to this GitHub repo for metadata only.

---

## License

Documentation and metadata in this repository are provided for discovery and integration. The Bankruptcy Observer product, API, and MCP service are offered under separate terms; see the product website.
