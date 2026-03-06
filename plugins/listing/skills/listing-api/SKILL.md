---
name: listing-api
description: Core knowledge for interacting with the listing.ai API — profile, article, and search tools via MCP.
user-invocable: false
---

# listing.ai API Reference

All API calls are made through MCP tools provided by the `listing` MCP server. Tools are prefixed `mcp__listing__`.

## Setup

Add the listing MCP server to your Claude Code configuration:

```json
{
  "mcpServers": {
    "listing": {
      "type": "http",
      "url": "https://mcp.prod.listing.ai/mcp",
      "headers": {
        "X-API-Key": "${LISTING_API_KEY}"
      }
    }
  }
}
```

- **`LISTING_API_KEY`** environment variable must be set. If it is not set, tell the user to run: `export LISTING_API_KEY="lst_your_key_here"`
- **`LISTING_MCP_URL`** is optional. Override the URL above for local development: `export LISTING_MCP_URL="http://localhost:3001/mcp"`

---

## Tools

### get_profile

Get the user's listing.ai profile.

- Tool: `mcp__listing__get_profile`
- Parameters: none

Returns: `{ first_name, last_name, bio, title, office, specialty, phone_number, links, is_published }`

### update_profile

Partial update — only send the fields you want to change.

- Tool: `mcp__listing__update_profile`
- Parameters (all optional):

| Field | Type | Constraints |
|-------|------|-------------|
| `first_name` | string | max 100 chars |
| `last_name` | string | max 100 chars |
| `bio` | string | max 5000 chars |
| `title` | string | max 200 chars |
| `office` | string | max 200 chars |
| `specialty` | string[] | array of strings |
| `phone_number` | string | max 30 chars |
| `links` | object | key-value pairs (e.g. `{"website":"https://..."}`) |
| `is_published` | boolean | whether profile is publicly visible |

### list_articles

List articles, optionally filtered by status.

- Tool: `mcp__listing__list_articles`
- Parameters:

| Field | Type | Required |
|-------|------|----------|
| `status` | `"draft"` or `"published"` | no |

Returns: array of article objects.

### get_article

Get a single article by ID.

- Tool: `mcp__listing__get_article`
- Parameters:

| Field | Type | Required |
|-------|------|----------|
| `id` | string (UUID) | yes |

Returns: single article object.

### create_article

Create a new article.

- Tool: `mcp__listing__create_article`
- Parameters:

| Field | Type | Required | Constraints |
|-------|------|----------|-------------|
| `title` | string | yes | 1-500 chars |
| `content` | string | no | article body (HTML) |
| `status` | `"draft"` or `"published"` | no | defaults to draft |
| `article_type` | `"article"` or `"link"` | no | |
| `link_url` | string (URL) | no | for link-type articles |
| `cover_image_url` | string (URL) | no | |
| `og_title` | string | no | max 200 chars |
| `og_description` | string | no | max 500 chars |
| `og_image_url` | string (URL) | no | |
| `og_site_name` | string | no | max 200 chars |

### update_article

Partial update — only send the fields you want to change.

- Tool: `mcp__listing__update_article`
- Parameters: same as `create_article` plus required `id` (UUID). All fields except `id` are optional. Nullable fields (`cover_image_url`, `link_url`, `og_title`, `og_description`, `og_image_url`, `og_site_name`) can be set to `null` to clear them.

### delete_article

Delete an article.

- Tool: `mcp__listing__delete_article`
- Parameters:

| Field | Type | Required |
|-------|------|----------|
| `id` | string (UUID) | yes |

Returns: success confirmation text.

### search_listings

Search for listings in the marketplace. Does not require authentication.

- Tool: `mcp__listing__search_listings`
- Parameters:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `semantic_query` | string (max 1000) | one of `semantic_query` or `filters` | Natural language search terms (price/filter terms removed) |
| `filters` | FilterClause[] | one of `semantic_query` or `filters` | Structured filters extracted from query |
| `limit` | integer (1-100) | no, default 20 | Max results |
| `offset` | integer (>= 0) | no, default 0 | Pagination offset |

**FilterClause format:**

```json
{ "field": "price", "op": "lt", "value": 100 }
```

| Field | Operators | Values |
|-------|-----------|--------|
| `price` | `lt`, `lte`, `gt`, `gte` | any number |
| `listing_type_id` | `eq`, `in` | `product`, `service`, `event`, `property`, `other` |
| `category` | `eq`, `in` | `good`, `service`, `digital`, `freeform` |
| `condition` | `eq`, `in` | `new`, `used`, `not_applicable` |

The response includes `available_filters` — use these to suggest refinements.

### get_search_filters

Discover available search filters, including dynamic filters from product metadata. Does not require authentication.

- Tool: `mcp__listing__get_search_filters`
- Parameters:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `mode` | `"static"` or `"all"` | no, default `"static"` | `all` includes dynamic filters from product metadata |

---

## Error Handling

Common errors returned by MCP tools:

- **401**: Invalid or missing API key. Ask the user to check their `LISTING_API_KEY`.
- **404**: Resource not found. Verify the resource ID exists.
- **422**: Validation error. Check field constraints above.
- **500**: Server error. Suggest the user try again or check the listing.ai status page.
