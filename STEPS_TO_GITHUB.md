# Steps to get Bankruptcy Observer API/MCP on GitHub

Follow these in order. No BKO app code is published—only docs and registry metadata.

---

## 1. Create a new repository on GitHub

1. Go to [github.com/new](https://github.com/new) (or your organization’s “New repository”).
2. **Repository name:** e.g. `bankruptcy-observer-api-docs` or `bko-mcp-docs`.
3. **Description (optional):** e.g. “Documentation and registry metadata for the Bankruptcy Observer API and MCP server.”
4. Choose **Public**.
5. **Do not** initialize with a README, .gitignore, or license (you’re adding your own files).
6. Click **Create repository**.

---

## 2. Copy the public-repo files into the new repo

From your machine (with the BKO repo at hand):

**Option A — Clone the new repo, then copy files**

```bash
# Clone the empty repo you just created (replace with your URL)
git clone https://github.com/YOUR_USERNAME_OR_ORG/bankruptcy-observer-api-docs.git
cd bankruptcy-observer-api-docs

# Copy the contents from your BKO project (adjust path if needed)
cp /home/ubuntu/web/Python/bko-mcp/public-repo-contents/README.md .
cp /home/ubuntu/web/Python/bko-mcp/public-repo-contents/server.json .

# Optional: keep the publishing guide
cp /home/ubuntu/web/Python/bko-mcp/public-repo-contents/README_PUBLISHING.md .

# Commit and push
git add README.md server.json
git add README_PUBLISHING.md   # if you copied it
git commit -m "Add API/MCP docs and MCP registry server.json"
git push origin main
```

**Option B — Add remote to an existing folder**

If you already have the `public-repo-contents` folder as its own directory:

```bash
cd /home/ubuntu/web/Python/bko-mcp/public-repo-contents
git init
git remote add origin https://github.com/YOUR_USERNAME_OR_ORG/bankruptcy-observer-api-docs.git
git add README.md server.json README_PUBLISHING.md
git commit -m "Add API/MCP docs and MCP registry server.json"
git branch -M main
git push -u origin main
```

---

## 3. Confirm it’s public and correct

- On GitHub, open the new repo and confirm:
  - **Settings → General → Danger zone:** visibility is **Public**.
  - The repo shows `README.md` and `server.json` (and any other files you added).
- Open the README on GitHub to confirm links (API docs, MCP docs, sign-up) work.

---

## 4. (Optional) Publish to the Official MCP Registry

When you want the server to appear in the MCP Registry:

1. **Namespace in `server.json`:**  
   - `com.bankruptcyobserver/mcp` → you must verify ownership of the domain `bankruptcyobserver.com`, or  
   - `io.github.YOUR_USERNAME/bankruptcy-observer` → you verify with GitHub.

2. **Install the Registry CLI:**  
   `brew install mcp-publisher` (or see [MCP Registry quickstart](https://modelcontextprotocol.io/registry/quickstart)).

3. **In a clone of the new public repo**, run the publisher (e.g. `mcp-publisher publish` or the command from the docs), authenticate (GitHub or domain), and submit. The registry will use this GitHub repo for metadata and point users to your live MCP URL.

---

## Summary checklist

- [ ] New public GitHub repo created (no README/gitignore/license).
- [ ] `README.md` and `server.json` (and optionally `README_PUBLISHING.md`) copied in and pushed.
- [ ] Repo is Public and README links look correct.
- [ ] (Optional) Namespace chosen and MCP Registry CLI used to publish.

After this, the “public face” of the API/MCP is on GitHub; your main BKO codebase stays private.
