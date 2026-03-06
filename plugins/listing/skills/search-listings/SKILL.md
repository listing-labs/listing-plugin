---
name: search-listings
description: Search the listing.ai marketplace for products, services, and more.
user-invocable: true
allowed-tools:
  - mcp__listing__search_listings
  - mcp__listing__get_search_filters
---

# Search Listings Workflow

Help the user search for listings in the marketplace.

## Input

The user provides a natural language search query (e.g. `/listing:search-listings vintage leather jacket under $200`). If no query is provided, ask what they're looking for.

## Workflow

### Step 1: Extract Filters from the Query

Parse the user's natural language query into `semantic_query` + `filters`:

- **Price**: "under $X" / "less than $X" → `{ field: "price", op: "lt", value: X }`. "over $X" / "more than $X" → `{ field: "price", op: "gt", value: X }`. "$X to $Y" → two filters: `price gte X` + `price lte Y`.
- **Condition**: "new" / "used" → `{ field: "condition", op: "eq", value: "new"|"used" }`
- **Type**: "products" / "services" / "events" → `{ field: "listing_type_id", op: "eq", value: "..." }`

Put the remaining descriptive text (with price/filter terms removed) in `semantic_query`. If only filters remain, omit `semantic_query`.

### Step 2: Search

Call `mcp__listing__search_listings` with the extracted parameters.

### Step 3: Present Results

Display results in a clear, scannable format:
- Title, price, condition
- Brief description
- Link if available

If no results are found, suggest broadening the search (fewer filters, different terms).

### Step 4: Refine (Optional)

The search response includes `available_filters` showing what filter values exist in the result set. Use these to offer refinement options:

- "I can narrow these down by condition (new/used) or category. Want me to filter further?"

If the user asks to refine, add the new filters to the previous search and call `mcp__listing__search_listings` again.

### Dynamic Filters

If the user mentions product-specific attributes (brand, size, color), call `mcp__listing__get_search_filters` with `mode: "all"` to discover available dynamic filter fields before searching.

## Notes

- Search does not require authentication — it works for all users.
- Always extract structured filters from the query rather than putting everything in `semantic_query`.
- Use pagination (`limit`/`offset`) for large result sets.
- If a tool call fails, show the error and suggest next steps.
