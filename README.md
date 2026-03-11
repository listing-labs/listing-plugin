# listing-plugin

Skills and MCP configuration for [listing.ai](https://listing.ai) — use with Claude Code or Claude Desktop to manage your profile, write articles, message other users, and search the marketplace.

## Install

### Claude Code

Installs the MCP server connection and all skills:

```
/plugin marketplace add listing-labs/listing-plugin
/plugin install listing@listing
```

### Skills only

Installs just the skills using [vercel-labs/skills](https://github.com/vercel-labs/skills) (configure the MCP server separately):

```bash
npx skills add listing-labs/listing-plugin
```

### MCP server only

If you only want the raw MCP tools without skills:

```bash
claude mcp add --transport http listing https://mcp.prod.listing.ai/mcp \
  --header "X-API-Key: ${LISTING_API_KEY}"
```

### Claude Desktop / claude.ai

#### Plugin (MCP server + skills)

1. Go to **Settings > Plugins > Browse plugins > Personal**
2. Click **+** → **Add marketplace from GitHub**
3. Enter `listing-labs/listing-plugin`

This installs the MCP connector and all skills. You'll be prompted to sign in via OAuth on first use — no API key needed.

#### Connector only (MCP server)

1. Go to **Settings > Connectors > Add custom connector**
2. Enter the server URL: `https://mcp.prod.listing.ai/mcp`
3. Click **Add**

## Prerequisites

A [listing.ai](https://listing.ai) account is required for profile, article, conversation, and intent tools. Search tools (`search_listings`, `get_search_filters`) work without authentication.

**Claude Code** uses an API key for authentication. Generate one at **listing.ai > Settings > API Keys**:

```bash
export LISTING_API_KEY="lst_your_key_here"
```

**Claude Desktop** authenticates via OAuth automatically — no API key needed.

## Skills

| Skill | Invocable | Description |
|-------|-----------|-------------|
| `listing-api` | no | API reference — loaded as context for other skills |
| `manage-profile` | `/listing:manage-profile` | View and update your listing.ai profile |
| `write-article` | `/listing:write-article [topic]` | Research, draft, and publish an article |
| `search-listings` | `/listing:search-listings [query]` | Search the marketplace with filters and refinement |
| `manage-conversations` | `/listing:manage-conversations` | View and manage conversations and messages |
| `manage-intents` | `/listing:manage-intents [query]` | Create and manage persistent saved searches with notifications |

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
| `publish_article` | Publish a draft article and create a linked post |
| `unpublish_article` | Unpublish an article and hide the linked post |

### Conversations

| Tool | Description |
|------|-------------|
| `list_conversations` | List conversations, optionally filter by status |
| `get_conversation` | Get a conversation with members and last message |
| `create_conversation` | Start a new conversation (or return existing) |
| `update_conversation` | Mark as read or archive a conversation |
| `delete_conversation` | Archive a conversation (soft delete) |
| `list_messages` | Get paginated messages in a conversation |
| `send_message` | Send a message in a conversation |

### Intents (saved searches)

| Tool | Description |
|------|-------------|
| `list_intents` | List saved search intents, optionally filter by status |
| `get_intent` | Get a single intent by ID |
| `create_intent` | Create a persistent saved search with automatic query classification |
| `update_intent` | Update an intent (re-extracts filters if query changes) |
| `delete_intent` | Delete an intent (soft delete) |
| `get_intent_results` | Get paginated match results for an intent |
| `pause_intent` | Pause an intent so it stops matching |
| `resume_intent` | Resume a paused intent |

### Search (no auth required)

| Tool | Description |
|------|-------------|
| `search_listings` | Search with semantic query and/or structured filters |
| `get_search_filters` | Discover available filter fields and values |