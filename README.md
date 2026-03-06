# listing-plugin

Skills and MCP configuration for [listing.ai](https://listing.ai) — use with Claude Code to manage your profile, write articles, and search the marketplace.

## Install

### Plugin (MCP server + skills)

Installs the MCP server connection and all skills:

```bash
claude plugin add listing-ai/listing-plugin
```

### Skills only

Installs just the skills (configure the MCP server separately):

```bash
npx skills add listing-ai/listing-plugin
```

### MCP server only

If you only want the raw MCP tools without skills:

```bash
claude mcp add --transport http listing https://mcp.prod.listing.ai/mcp \
  --header "X-API-Key: ${LISTING_API_KEY}"
```

## Prerequisites

1. A [listing.ai](https://listing.ai) account
2. An API key (generate one at **listing.ai > Settings > API Keys**)

```bash
export LISTING_API_KEY="lst_your_key_here"
```

Search tools (`search_listings`, `get_search_filters`) work without authentication.

## Skills

| Skill | Invocable | Description |
|-------|-----------|-------------|
| `listing-api` | no | API reference — loaded as context for other skills |
| `manage-profile` | `/listing:manage-profile` | View and update your listing.ai profile |
| `write-article` | `/listing:write-article [topic]` | Research, draft, and publish an article |
| `search-listings` | `/listing:search-listings [query]` | Search the marketplace with filters and refinement |

## MCP Tools

### Profile

| Tool | Description |
|------|-------------|
| `get_profile` | Get your profile |
| `update_profile` | Update profile fields |

### Articles

| Tool | Description |
|------|-------------|
| `list_articles` | List articles, optionally filter by status |
| `get_article` | Get a single article by ID |
| `create_article` | Create a new article |
| `update_article` | Update an existing article |
| `delete_article` | Delete an article |

### Search (no auth required)

| Tool | Description |
|------|-------------|
| `search_listings` | Search with semantic query and/or structured filters |
| `get_search_filters` | Discover available filter fields and values |