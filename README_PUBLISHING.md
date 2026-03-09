# How to use this folder

This folder contains **only** what should go in a **separate, public GitHub repo** for the Bankruptcy Observer API/MCP. It does not contain any BKO application code.

## Steps

1. **Create a new public repo** on GitHub (e.g. `bankruptcy-observer-api-docs` or `bko-mcp-docs`). Do not put it inside your main BKO monorepo.

2. **Copy the contents** of `public-repo-contents/` into the new repo:
   - `README.md`
   - `server.json`
   - (Optional) this file if you want to keep the instructions there; or delete it after copying.)

3. **Namespace for the MCP Registry:**  
   The `server.json` uses `com.bankruptcyobserver/mcp`. To publish to the [Official MCP Registry](https://modelcontextprotocol.io/registry/about), you must verify ownership of that namespace:
   - **Option A — Domain verification:** Prove you control `bankruptcyobserver.com` (e.g. DNS TXT or HTTP challenge). Then you can use `com.bankruptcyobserver/*`.
   - **Option B — GitHub namespace:** If you prefer, change `name` in `server.json` to `io.github.YOUR_GITHUB_USERNAME/bankruptcy-observer` and verify with GitHub OAuth when publishing.

4. **Publish to the MCP Registry** (when ready):
   - Install the Registry CLI (e.g. `brew install mcp-publisher`).
   - In the new public repo (or a local clone), run `mcp-publisher init` if you need to regenerate `server.json`, or use the existing one.
   - Authenticate (GitHub or domain) and publish. The registry will point to this public repo for metadata; your live endpoints stay at api.bankruptcyobserver.com and mcp.bankruptcyobserver.com.

5. **Keep docs in sync:**  
   The canonical documentation lives on your servers (`/docs`). This repo’s README only links to those URLs. When you update the spec in the main BKO project (e.g. `bko-mcp/docs/API_QUERIES_AND_DATA.md`), redeploy so the live `/docs` stays current. No need to copy the full spec into this public repo unless you want a static copy for GitHub-only readers.

## What stays private

- Your main BKO codebase (Laravel, Node/bko2025, Python bko-mcp app code).
- Any repo that contains this folder as part of a larger private project.

Only the **new** public repo you create from these contents is visible; it contains no source code, only README, `server.json`, and this guide.
