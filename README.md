# MCP Registry

A Firebase-hosted registry of MCP (Model Context Protocol) servers. Used by the
[skill-loader-agent](../skill-loader-agent/) to dynamically acquire capabilities at runtime.

## Live

**Catalog:** `https://biome-registry.web.app/catalog.json`  
**Browser:** `https://biome-registry.web.app`

## Catalog

14 verified MCP servers across 8 categories:

| Category | Servers |
|---|---|
| Search | Brave Search |
| Web | Fetch |
| Development | GitHub, GitLab |
| Location | Google Maps |
| Memory | Memory (Knowledge Graph) |
| Database | PostgreSQL, SQLite |
| Browser | Puppeteer |
| Reasoning | Sequential Thinking |
| Communication | Slack |
| Utilities | Time |
| Weather | OpenWeather |
| Testing | Everything |

## Adding a server

1. Edit `public/catalog.json` — add an entry to the `servers` array
2. Push to `main` — GitHub Actions auto-deploys to Firebase Hosting
3. Optionally run `cd scripts && npm install && node seed.js` to sync Firestore

### Entry format

```json
{
  "id": "my-server",
  "name": "My Server",
  "description": "What it does.",
  "capabilities": ["snake_case_tool_name"],
  "category": "utilities",
  "tags": ["keyword1", "keyword2"],
  "install": {
    "type": "npx",
    "package": "@scope/mcp-server-name",
    "args": [],
    "env_required": [
      { "key": "API_KEY", "description": "Where to get it" }
    ],
    "env_optional": []
  },
  "source_url": "https://github.com/...",
  "version": "latest",
  "verified": false
}
```

`install.type` is one of `npx`, `uvx`, or `pip`.

## Setup

### 1. Create Firebase project

```bash
firebase projects:create biome-registry --display-name "MCP Registry"
firebase use biome-registry
```

### 2. Enable Firestore

```bash
firebase firestore:databases:create --location=us-central
```

### 3. Deploy

```bash
firebase deploy
```

### 4. Seed Firestore (optional — catalog.json is the primary source)

```bash
cd scripts && npm install && node seed.js
```

### 5. Add GitHub secret for CI/CD

In Firebase Console → Project Settings → Service Accounts → Generate new private key.  
Add the JSON content as `FIREBASE_SERVICE_ACCOUNT` in GitHub → Settings → Secrets.

## API

| Endpoint | Description |
|---|---|
| `GET /catalog.json` | Full catalog — primary API used by skill-loader-agent |
| `GET /` | Visual catalog browser |
