---
name: listing-api
description: Core knowledge for interacting with the listing.ai API — profile, article, conversation, intent, and search tools via MCP.
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
| `content` | string | no | article body (Markdown — raw HTML will not be rendered) |
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

### publish_article

Publish a draft article — makes it publicly visible and creates a linked post.

- Tool: `mcp__listing__publish_article`
- Parameters:

| Field | Type | Required |
|-------|------|----------|
| `id` | string (UUID) | yes |

Returns: updated article object.

### unpublish_article

Unpublish an article — reverts it to draft status and hides the linked post.

- Tool: `mcp__listing__unpublish_article`
- Parameters:

| Field | Type | Required |
|-------|------|----------|
| `id` | string (UUID) | yes |

Returns: updated article object.

### list_conversations

List your conversations, optionally filtered by status.

- Tool: `mcp__listing__list_conversations`
- Parameters:

| Field | Type | Required |
|-------|------|----------|
| `status` | `"active"` or `"archived"` | no (default: active) |

Returns: array of conversation objects.

### get_conversation

Get a single conversation by ID, including members and last message.

- Tool: `mcp__listing__get_conversation`
- Parameters:

| Field | Type | Required |
|-------|------|----------|
| `id` | string (UUID) | yes |

Returns: single conversation object with members and last message.

### create_conversation

Start a new conversation with another user, or return an existing one if a conversation already exists with the same recipient and context.

- Tool: `mcp__listing__create_conversation`
- Parameters:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `recipient_id` | string (UUID) | yes | User ID of the recipient |
| `message` | string | no | Optional initial message to send |
| `context_type` | `"direct"`, `"profile"`, `"listing"`, `"post"` | no | Conversation context type (default: profile) |
| `context_id` | string | no | ID of the context object (e.g. listing ID, post ID) |

Returns: conversation object.

### update_conversation

Update a conversation — mark as read or archive it.

- Tool: `mcp__listing__update_conversation`
- Parameters:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string (UUID) | yes | Conversation ID |
| `action` | `"mark_read"` or `"archive"` | yes | Action to perform |

Returns: updated conversation object.

### delete_conversation

Archive (soft delete) a conversation.

- Tool: `mcp__listing__delete_conversation`
- Parameters:

| Field | Type | Required |
|-------|------|----------|
| `id` | string (UUID) | yes |

Returns: success confirmation text.

### list_messages

Get paginated messages in a conversation, with optional cursor-based pagination.

- Tool: `mcp__listing__list_messages`
- Parameters:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string (UUID) | yes | Conversation ID |
| `limit` | integer (1-100) | no, default 50 | Max messages to return |
| `offset` | integer (>= 0) | no, default 0 | Pagination offset |
| `before` | string (ISO-8601) | no | Get messages before this timestamp |
| `after` | string (ISO-8601) | no | Get messages after this timestamp |

Returns: `{ messages: [...], total: number }` with paginated message objects.

### send_message

Send a message in a conversation.

- Tool: `mcp__listing__send_message`
- Parameters:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string (UUID) | yes | Conversation ID |
| `content` | string | yes | Message content |
| `message_type` | `"text"`, `"system"`, `"ai_response"` | no, default `"text"` | Message type |

Returns: created message object.

### list_intents

List saved search intents, optionally filtered by status.

- Tool: `mcp__listing__list_intents`
- Parameters:

| Field | Type | Required |
|-------|------|----------|
| `status` | `"active"` or `"paused"` | no |

Returns: array of intent objects.

### get_intent

Get a single search intent by ID.

- Tool: `mcp__listing__get_intent`
- Parameters:

| Field | Type | Required |
|-------|------|----------|
| `id` | string (UUID) | yes |

Returns: single intent object.

### create_intent

Create a persistent saved search. The query is automatically classified into semantic search terms and structured filters.

- Tool: `mcp__listing__create_intent`
- Parameters:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `query` | string | yes | Natural language search query (max 1000 chars) |
| `listing_type_id` | string | no | Restrict to: `product`, `service`, `event`, `property`, `other` |
| `context_budget` | `"low"`, `"medium"`, `"high"` | no | How much context to include in results |
| `result_format` | `"full"`, `"summary"`, `"ids"` | no | Result detail level |
| `max_results` | integer (1-100) | no | Maximum results per match cycle |
| `webhook_url` | string (URL) | no | URL to POST notifications when new matches are found |
| `expires_at` | string (ISO-8601) | no | Expiration timestamp |

Returns: intent object with `extracted` field showing parsed semantic query and filters.

### update_intent

Partial update — only send fields you want to change. If the query changes, filters and embedding are automatically re-extracted.

- Tool: `mcp__listing__update_intent`
- Parameters: same as `create_intent` plus required `id` (UUID). All fields except `id` are optional. Nullable fields (`listing_type_id`, `webhook_url`, `expires_at`) can be set to `null` to clear them.

### delete_intent

Delete a search intent (soft delete).

- Tool: `mcp__listing__delete_intent`
- Parameters:

| Field | Type | Required |
|-------|------|----------|
| `id` | string (UUID) | yes |

Returns: success confirmation text.

### get_intent_results

Get paginated match results for a search intent.

- Tool: `mcp__listing__get_intent_results`
- Parameters:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string (UUID) | yes | Intent ID |
| `limit` | integer (1-100) | no, default 20 | Max results |
| `offset` | integer (>= 0) | no, default 0 | Pagination offset |

Returns: `{ results: [...], total: number }` where each result has `listing_id`, `score`, `notified`, and `matched_at`.

### pause_intent

Pause a search intent so it stops matching new listings.

- Tool: `mcp__listing__pause_intent`
- Parameters:

| Field | Type | Required |
|-------|------|----------|
| `id` | string (UUID) | yes |

Returns: updated intent object with `status: "paused"`.

### resume_intent

Resume a paused search intent.

- Tool: `mcp__listing__resume_intent`
- Parameters:

| Field | Type | Required |
|-------|------|----------|
| `id` | string (UUID) | yes |

Returns: updated intent object with `status: "active"`.

### Webhook Postback

When an intent has a `webhook_url` set, the system POSTs to that URL when new listings match. This is server-initiated, not triggered by any MCP tool call.

- **Method**: POST, Content-Type: `application/json`
- **Timeout**: 10 seconds, no retries
- **Success**: any 2xx marks matched results as `notified: true`

**Payload:**

```json
{
  "event": "intent.match",
  "intent_id": "uuid",
  "listings": [
    { "id": "listing-uuid", "score": 0.92 }
  ],
  "timestamp": "ISO-8601"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `event` | string | Always `"intent.match"` |
| `intent_id` | string (UUID) | The intent that was matched |
| `listings[].id` | string (UUID) | Listing ID — fetch full details via search tools |
| `listings[].score` | number (0–1) | Semantic similarity score (higher = better match) |
| `timestamp` | string (ISO-8601) | When the match was detected |

On failed delivery, `intent_result` rows remain `notified: false` but are still visible via `get_intent_results`.

---

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
