# Bankruptcy Observer — MCP

Public documentation and registry metadata for the **Bankruptcy Observer MCP server**. This repository contains no application source code.

The former REST host `api.bankruptcyobserver.com` redirects to the MCP docs. Programmatic access is the MCP server at `https://mcp.bankruptcyobserver.com/mcp`.

## What this is

- **Bankruptcy MCP server** — US business bankruptcy filings, dockets, court documents, case summaries, and monitoring via the [Model Context Protocol](https://modelcontextprotocol.io/) for AI assistants and LLM workflows.

Data covers **US business bankruptcies** (federal courts) only. Consumer-only filings and non-US jurisdictions are not included.

**Live docs (canonical):** [https://mcp.bankruptcyobserver.com/docs](https://mcp.bankruptcyobserver.com/docs)

---

## Free lookups (no token, no API key, no payment)

AI assistants and users can look up bankruptcy cases with **no account, no API key, and no API token**:

| Free query | MCP tool | Example |
|------------|----------|---------|
| **Debtor name** | `search_bankruptcy_cases_tool` | `search_term: "JOY-CPW, INC."` — no court required |
| **Case number** | `get_case_by_case_number_tool` | `short_case_number: "26-10543"` — no court required |

- Call these tools **without an API token**. Only case number or name is needed; **court is not required**. If more than one case matches (e.g. same case number in multiple courts), the server returns **all** matching cases. Each result includes limited case info; full case details, dockets, documents, and broad search require a paid plan.
- Wildcard name search (`*term`), EIN, industry, NAICS, state, date range, docket, documents, summaries, and monitoring require a subscription (or an Ala Carte request) and authentication.

Account tools `list_plans_tool` and `purchase_plan_tool` also work with no auth.

---

## Sign-up and access

Website subscribers at [bankruptcyobserver.com](https://www.bankruptcyobserver.com) get MCP access included. After payment, the token is in the subscriber dashboard at [https://www.bankruptcyobserver.com/subscriber/mcp-setup](https://www.bankruptcyobserver.com/subscriber/mcp-setup). **No token is emailed.**

You can also **subscribe through the MCP server**: call `list_plans_tool` for the live catalog, then `purchase_plan_tool` with that numeric `plan_id` (or its Stripe price id) to get a Stripe Checkout link. Browser alternative: [https://mcp.bankruptcyobserver.com/subscribe](https://mcp.bankruptcyobserver.com/subscribe) or [https://www.bankruptcyobserver.com/pricing](https://www.bankruptcyobserver.com/pricing).

`plan_id` is the numeric id from `list_plans_tool` (for example `57`, `60`, `58`, `62`) or the `stripe_price_id`. There are no plans named Professional, Business, or Enterprise for MCP checkout.

For questions about access, billing, data coverage, or custom enterprise feeds, use **only** the contact form at [https://www.bankruptcyobserver.com/contact](https://www.bankruptcyobserver.com/contact). Do not publish or use direct email addresses for contact.

### Current plans

Prices, plan ids, and Stripe price ids come from `list_plans_tool` (always current). Snapshot of the live catalog:

| Plan | Billing | MCP access |
|------|---------|------------|
| **Ala Carte Request** (`plan_id` `62`) | $5 one-time | One full-data request of any tool. Purchased requests never expire. Document downloads are extra. |
| **Full Access Light** (`plan_id` `57`) | $39 / month | Full search across US business filings, docket/documents/summaries/monitoring. 5 monitored cases. 100 agent questions / month. |
| **Full Access Standard** (`plan_id` `60`) | $49 / month (7-day free trial) | Same tools. 20 monitored cases. 300 agent questions / month. |
| **Full Access Heavy** (`plan_id` `58`) | $109 / month (7-day free trial) | Same tools. 50 monitored cases. 1,000 agent questions / month. |

Full Access plans include library PDFs at no charge; documents not yet in the library are 10¢/page (shown before download).

**Single Case** ($29 / month) is sold from a case page on the website, not from `list_plans_tool`. It includes docket, document, summary, and monitoring tools **only for cases on your monitoring list**. Broad search (wildcard name, EIN, industry, NAICS, state, date range) is Full Access.

**Enterprise** is a custom feed (EIN/address matching into your systems or agents), not a Stripe `plan_id`. Use the [contact form](https://www.bankruptcyobserver.com/contact) or [enterprise page](https://www.bankruptcyobserver.com/enterprise).

`check_subscription_tool` returns remaining quota for an authenticated token.

**Authenticate using either an API token or an OAuth access token, sent in one of these ways:**

- `Authorization: Bearer <your-token>`
- `X-API-Key: <your-token>`
- `Api-Key: <your-token>`
- Header-less clients (e.g. Grok web connectors): `https://mcp.bankruptcyobserver.com/mcp?api_key=YOUR_TOKEN` or `/mcp/t/YOUR_TOKEN`. Treat the full URL as a secret.

---

## Connecting via OpenAI

Use OpenAI's [Responses API](https://platform.openai.com/docs/guides/tools-remote-mcp) to call this MCP server directly.

**Free tier (no token):**

```python
from openai import OpenAI

client = OpenAI()

response = client.responses.create(
    model="gpt-4.1",
    tools=[{
        "type": "mcp",
        "server_label": "bankruptcy_observer",
        "server_url": "https://mcp.bankruptcyobserver.com/mcp",
        "require_approval": "never",
    }],
    input="Search for bankruptcy cases for Acme Corp",
)
print(response.output_text)
```

**Paid tier (with token):**

```python
response = client.responses.create(
    model="gpt-4.1",
    tools=[{
        "type": "mcp",
        "server_label": "bankruptcy_observer",
        "server_url": "https://mcp.bankruptcyobserver.com/mcp",
        "headers": {"Authorization": "Bearer YOUR_TOKEN"},
        "require_approval": "never",
    }],
    input="Get docket entries for case 24-10543",
)
```

> **Note:** Do not set `Content-Type` or `Accept` headers manually — the OpenAI client sets these correctly for the MCP protocol. Overriding them will cause the server to return an HTML error page instead of a JSON response.

> **OAuth option:** Connectors such as ChatGPT can also authenticate with MCP OAuth and send Bearer access tokens automatically after login. API token headers are still supported.

---

## Connecting in ChatGPT (OAuth)

To connect this MCP server in ChatGPT with OAuth, create a **new app/connector** and enter the OAuth endpoints below exactly.

If the **Registration URL** is omitted, Dynamic Client Registration will fail and ChatGPT will connect without usable tool authorization.

**OAuth endpoints**

- **Auth URL:** `https://mcp.bankruptcyobserver.com/oauth/authorize`
- **Token URL:** `https://mcp.bankruptcyobserver.com/oauth/token`
- **Registration URL:** `https://mcp.bankruptcyobserver.com/oauth/register`
- **Authorization server base:** `https://mcp.bankruptcyobserver.com/`
- **Resource:** `https://mcp.bankruptcyobserver.com/mcp`

Paths `/authorize`, `/token`, and `/register` (without `/oauth`) are not valid.

After connecting, ChatGPT should complete OAuth in-browser and then call tools using OAuth Bearer tokens. Legacy API-token headers remain supported for clients that use header auth.

---

## Endpoints

| Service | Endpoint | Auth |
|--------|----------|------|
| MCP | `https://mcp.bankruptcyobserver.com/mcp` | Free lookups: no auth. Restricted queries: API token or OAuth Bearer token |
| Docs | `https://mcp.bankruptcyobserver.com/docs` | None |
| Subscribe | `https://mcp.bankruptcyobserver.com/subscribe` or [bankruptcyobserver.com/pricing](https://www.bankruptcyobserver.com/pricing) | None |
| Website MCP overview | [https://www.bankruptcyobserver.com/mcp](https://www.bankruptcyobserver.com/mcp) | None |

The `/mcp` path is the MCP protocol endpoint (POST). For human-readable documentation, use the Docs URL (GET, no auth).

---

## Tools

Anonymous `tools/list` includes the full catalog. Paid tools return a payment/upgrade payload until you authenticate.

### Free / account

| Tool | Notes |
|------|--------|
| `search_bankruptcy_cases_tool` | **Free:** exact/prefix name, limited fields. **Paid:** `*term` contains search and full fields. |
| `get_case_by_case_number_tool` | **Free:** 7-digit number, all matching courts, limited fields. **Paid:** full data, court filters, `live_update`. |
| `list_plans_tool` | Live plan catalog. No auth. |
| `purchase_plan_tool` | Stripe Checkout URL. Pass `plan_id` from `list_plans_tool`. No auth. |
| `check_subscription_tool` | Status and remaining quota. |

### Full Access search

| Tool | Notes |
|------|--------|
| `get_case_by_ein_tool` | Debtor EIN. |
| `get_cases_by_industry_tool` | Industry label or key; optional date range. |
| `get_cases_by_naics_tool` | NAICS 2–4 digits; optional date range. |
| `get_cases_by_state_tool` | Two-letter state; optional date range. |
| `get_cases_by_date_range_tool` | Filed-date range (`dateFiled`). |

Ala Carte can call these tools using a purchased request. Single Case cannot (scoped to monitored cases).

### Case intelligence and documents (subscribers)

| Tool | Notes |
|------|--------|
| `get_docket_entries_tool` | Docket entries (`limit` max 50; `skip` / `offset` / `page` for the rest). Each entry includes `docket_id`. |
| `get_case_summary_tool` | Plain-English structured summary. |
| `get_document_tool` | Call with `docket_id` only for cost preview; `accept_charge: true` after the user confirms to get a signed PDF URL. |
| `get_document_cost_tool` | Optional same cost preview. |
| `list_monitored_cases_tool` | Cases on your monitoring list. |
| `add_monitored_case_tool` | Add a case to monitoring. |
| `refresh_docket_tool` | Immediate docket refresh from court sources. |
| `get_recent_developments_tool` | Docket activity since a date (default last 7 days). |

Document flow: `get_docket_entries_tool` → `get_document_tool` with `docket_id` only (cost) → user confirms → `get_document_tool` with `accept_charge: true`.

---

## Standard fields (case-level)

Returned for queries that return case rows (full fields on paid requests):

| Field | Description |
|-------|-------------|
| name | Debtor/case name |
| court | Court name |
| shortCaseNumber | Short case number (e.g. 22-12345) |
| dateDocketUpdated | When docket/case data was last updated in our system |
| dateFiled | Filing date |

**Optional** (included when relevant): `courtState`, `chapter`, `NAICS`, `industry`, `dateClosed`, `dateDismissed`, `isOpen` / `isClosed` / `isDismissed`, `assetAmount`, `liabAmount`.

Free lookups return a limited subset (name, chapter, court, case number, filing date, and similar).

---

## Full documentation (canonical)

The live spec is served with no auth at:

- **MCP docs:** [https://mcp.bankruptcyobserver.com/docs](https://mcp.bankruptcyobserver.com/docs)
- **Website MCP overview:** [https://www.bankruptcyobserver.com/mcp](https://www.bankruptcyobserver.com/mcp)
- **Pricing:** [https://www.bankruptcyobserver.com/pricing](https://www.bankruptcyobserver.com/pricing)

---

## Registry

This repo includes a `server.json` describing the remote MCP server (URL + optional API token header). The listing in the [Official MCP Registry](https://modelcontextprotocol.io/registry/about) may also point at the consolidated docs repo [`jmtroller/mcp-documentation`](https://github.com/jmtroller/mcp-documentation/tree/main/bankruptcy-observer). Search: `https://registry.modelcontextprotocol.io/v0.1/servers?search=com.bankruptcyobserver`

---

## License

Documentation and metadata in this repository are provided for discovery and integration. The Bankruptcy Observer product and MCP service are offered under separate terms; see the product website.
