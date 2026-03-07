---
name: manage-intents
description: Create and manage persistent saved searches (intents) that notify you when new listings match your criteria.
user-invocable: true
allowed-tools:
  - mcp__listing__list_intents
  - mcp__listing__get_intent
  - mcp__listing__create_intent
  - mcp__listing__update_intent
  - mcp__listing__delete_intent
  - mcp__listing__get_intent_results
  - mcp__listing__pause_intent
  - mcp__listing__resume_intent
  - mcp__listing__search_listings
  - mcp__listing__get_search_filters
---

# Manage Intents Workflow

Help the user create and manage persistent saved searches (intents). Intents continuously match against new listings and can notify via webhook when matches are found.

## Input

The user may provide a search description (e.g. `/listing:manage-intents vintage leather jacket under $200`) or a management action (e.g. `/listing:manage-intents check my intents`). If no input is provided, ask what they'd like to do.

## Workflow

### Creating a New Intent

#### Step 1: Understand the Search

Parse the user's natural language query. Intents use automatic classification — the system extracts semantic search terms and structured filters from the `query` field. You do NOT need to extract filters manually like with `search_listings`.

Simply pass the user's full natural language query as `query` to `mcp__listing__create_intent`.

#### Step 2: Configure Options

Ask the user about optional settings if relevant:

- **Listing type**: restrict to `product`, `service`, `event`, `property`, or `other`
- **Webhook URL**: if they want push notifications when new matches appear
- **Expiration**: if the intent should auto-expire (e.g. "only look for the next 30 days")
- **Max results**: how many matches per cycle (default is fine for most users)

For most users, just the query is enough — don't overwhelm with options unless they ask.

#### Step 3: Create the Intent

Call `mcp__listing__create_intent` with the query and any options.

Show the user:
- The intent ID
- The extracted query breakdown (semantic query + filters) from the `extracted` field
- Current status (`active`)

#### Step 4: Check Initial Results

Call `mcp__listing__get_intent_results` to see if there are any immediate matches. Present results the same way as search results (title, price, condition, description).

If no results yet, reassure the user that the intent is active and will match against new listings as they're posted.

### Viewing Existing Intents

Call `mcp__listing__list_intents` to show all intents. Display each intent with:
- ID, query, status (active/paused)
- Extracted filters
- When it was created and last matched
- Webhook URL if set

Offer actions: view results, update, pause/resume, or delete.

### Viewing Intent Results

Call `mcp__listing__get_intent_results` with the intent ID. Display matches with:
- Listing title, price, condition
- Match score
- When the match was found
- Whether the webhook was notified

Use pagination (`limit`/`offset`) for large result sets.

### Updating an Intent

Use `mcp__listing__update_intent` with only the changed fields. If the user changes the query, the system automatically re-extracts filters and updates the embedding.

Show the updated intent to confirm.

### Pausing and Resuming

- `mcp__listing__pause_intent` — stops matching without deleting. Good for temporary holds.
- `mcp__listing__resume_intent` — reactivates a paused intent.

### Deleting an Intent

Confirm with the user before calling `mcp__listing__delete_intent`. This is a soft delete.

## Notes

- All intent tools require authentication.
- The `query` field is automatically classified — no need to manually extract filters.
- Intents are persistent by default. They keep matching until paused, deleted, or expired.
- The `extracted` field in the response shows how the system interpreted the query. Use this to verify the intent captures what the user wants.
- If a tool call fails, show the error and suggest next steps.
